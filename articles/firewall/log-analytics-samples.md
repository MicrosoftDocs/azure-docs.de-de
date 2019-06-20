---
title: Azure Firewall-Protokollanalyse – Beispiele
description: Azure Firewall-Protokollanalyse – Beispiele
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/15/2019
ms.author: victorh
ms.openlocfilehash: 3f329d3dd4af1faef8f77d08db655cc7d6ef79fd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461533"
---
# <a name="azure-firewall-log-analytics-samples"></a>Azure Firewall-Protokollanalyse – Beispiele

Sie können die folgenden Azure Monitor-Protokollbeispiele verwenden, um Ihre Azure Firewall-Protokolle zu analysieren. Die Beispieldatei wird im Ansicht-Designer in Azure Monitor erstellt. Im Artikel zum [Ansicht-Designer in Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-view-designer) finden Sie weitere Informationen zum Konzept des Ansicht-Designers.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Azure Monitor-Protokollansicht

Hier wird beschrieben, wie Sie eine Beispielvisualisierung für Azure Monitor-Protokolle konfigurieren können. Die Beispielvisualisierung können Sie vom Repository [azure-docs-json-samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) herunterladen. Am einfachsten ist es, wenn Sie mit der rechten Maustaste auf den Hyperlink auf dieser Seite klicken, *Speichern unter* auswählen und einen Namen wie z.B. **AzureFirewall.omsview** eingeben. 

Führen Sie die folgenden Schritte aus, um die Ansicht zu Ihrem Log Analytics-Arbeitsbereich hinzuzufügen:

1. Öffnen Sie den Log Analytics-Arbeitsbereich im Azure-Portal.
2. Öffnen Sie **Ansicht-Designer** unter **Allgemein**.
3. Klicken Sie auf **Importieren**.
4. Navigieren Sie zu der zuvor heruntergeladenen Datei **AzureFirewall.omsview**, und wählen Sie sie aus.
5. Klicken Sie auf **Speichern**.

Die Ansicht der Anwendungsregelprotokolldaten sieht wie folgt aus:

![Anwendungsregelprotokolldaten](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

Die Netzwerkregelprotokolldaten werden wie folgt angezeigt:

![Netzwerkregelprotokolldaten]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Mit Azure Firewall werden Daten unter AzureDiagnostics mit der Kategorie **AzureFirewallApplicationRule** oder **AzureFirewallNetworkRule** protokolliert. Die Daten, die die Details enthalten, werden im Feld „msg_s“ gespeichert. Mit dem [parse](https://docs.microsoft.com/azure/kusto/query/parseoperator)-Operator können die verschiedenen Eigenschaften aus dem Feld „msg_s“ extrahiert werden. Mit den Abfragen unten werden die Informationen für beide Kategorien extrahiert.

## <a name="application-rules-log-data-query"></a>Abfrage der Anwendungsregelprotokolldaten

Mit der nachfolgenden Abfrage werden die Anwendungsregelprotokolldaten analysiert. In den verschiedenen Kommentarzeilen wird angegeben, wie die Abfrage erstellt wurde:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "denied","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

Die gleiche Abfrage in einem komprimierteren Format:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "denied","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Abfrage der Netzwerkregelprotokolldaten

Mit der folgenden Abfrage werden die Netzwerkregelprotokolldaten analysiert. In den verschiedenen Kommentarzeilen wird angegeben, wie die Abfrage erstellt wurde:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

Die gleiche Abfrage in einem komprimierteren Format:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Abfrage von Protokolldaten mit Threat Intelligence

Mit der folgenden Abfrage werden die Threat Intelligence-Regelprotokolldaten analysiert:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu Azure Firewall-Überwachung und -Diagnose finden Sie unter [Tutorial: Überwachen von Azure Firewall-Protokollen und -Metriken](tutorial-diagnostics.md).
