---
title: Häufig gestellte Fragen zu Azure-Datenverkehrsanalysen | Microsoft-Dokumentation
description: Hier finden Sie Antworten auf einige der am häufigsten gestellten Fragen zu Datenverkehrsanalysen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: f27f2a6319bf2648546a62c2566d938db657738f
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518463"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Häufig gestellte Fragen zu Traffic Analytics

In diesem Artikel werden an zentraler Stelle viele der am häufigsten gestellten Fragen zu Traffic Analytics in Azure Network Watcher beantwortet.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Welche Voraussetzungen gelten für die Verwendung von Traffic Analytics?

Für Traffic Analytics gelten folgende Voraussetzungen:

- Ein Abonnement mit aktiviertem Network Watcher
- Für die zu überwachenden Netzwerksicherheitsgruppen (NSG) aktivierte NSG-Datenflussprotokolle
- Ein Azure Storage-Konto zum Speichern unformatierter Datenflussprotokolle
- Ein Azure Log Analytics-Arbeitsbereich mit Lese- und Schreibzugriff

Ihr Konto muss eine der folgenden Voraussetzungen erfüllen, um Traffic Analytics aktivieren zu können:

- Ihr Konto muss eine der folgenden Azure-Rollen im Abonnementumfang haben: Besitzer, Mitwirkender, Leser oder Netzwerkmitwirkender.
- Wenn Ihr Konto nicht einer der zuvor genannten Rollen zugewiesen ist, müssen Sie es einer benutzerdefinierten Rolle zuweisen, der auf Abonnementebene die folgenden Aktionen zugewiesen sind.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
So überprüfen Sie die einem Benutzer für ein Abonnement zugewiesenen Rollen

1. Melden Sie sich mit **Login-AzAccount** bei Azure an. 

2. Wählen Sie mit **Select-AzSubscription** das gewünschte Abonnement aus. 

3. Listen Sie mit **Get-AzRoleAssignment -SignInName [E-Mail-Adresse des Benutzers] -IncludeClassicAdministrators** alle Rollen auf, die einem bestimmten Benutzer zugewiesen sind. 

Falls keine Ausgabe erfolgt, wenden Sie sich an den zuständigen Abonnementadministrator, um den zur Ausführung der Befehle erforderlichen Zugriff zu erhalten. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>In welchen Azure-Regionen ist Traffic Analytics verfügbar?

Datenverkehranalysen für Netzwerksicherheitsgruppen werden in den folgenden Regionen unterstützt:
- Kanada, Mitte
- USA, Westen-Mitte
- East US
- USA (Ost) 2
- USA Nord Mitte
- USA Süd Mitte
- USA (Mitte)
- USA (Westen)
- USA, Westen 2
- Frankreich, Mitte
- Europa, Westen
- Nordeuropa
- Brasilien Süd
- UK, Westen
- UK, Süden
- Australien (Osten)
- Australien, Südosten 
- Asien, Osten
- Asien, Südosten
- Korea, Mitte
- Indien, Mitte
- Indien (Süden)
- Japan, Osten
- Japan, Westen
- US Government, Virginia
- China, Osten 2

Der Log Analytics-Arbeitsbereich muss in den folgenden Regionen vorhanden sein:
- Kanada, Mitte
- USA, Westen-Mitte
- East US
- USA (Ost) 2
- USA Nord Mitte
- USA Süd Mitte
- USA (Mitte)
- USA (Westen)
- USA, Westen 2
- Frankreich, Mitte
- Europa, Westen
- Nordeuropa
- UK, Westen
- UK, Süden
- Australien (Osten)
- Australien, Südosten
- Asien, Osten
- Asien, Südosten 
- Korea, Mitte
- Indien, Mitte
- Japan, Osten
- US Government, Virginia
- China, Osten 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Können sich die Netzwerksicherheitsgruppen, für die ich Datenflussprotokolle aktiviere, in anderen Regionen befinden als mein Arbeitsbereich?

Ja, diese Netzwerksicherheitsgruppen können sich in anderen Regionen als Ihr Log Analytics-Arbeitsbereich befinden.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Können mehrere Netzwerksicherheitsgruppen in einem einzigen Arbeitsbereich konfiguriert werden?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kann ich einen bestehenden Arbeitsbereich verwenden?

Ja. Wenn Sie einen bestehenden Arbeitsbereich auswählen, vergewissern Sie sich, dass dieser in die neue Abfragesprache geändert wurde. Wenn Sie kein Upgrade des Arbeitsbereichs ausführen möchten, müssen Sie einen neuen Arbeitsbereich anlegen. Weitere Informationen über die neue Abfragesprache finden Sie unter [Upgrade von Azure Monitor-Protokollen auf die neue Protokollsuche](../azure-monitor/log-query/log-query-overview.md).

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kann mein Azure Storage-Konto zu einem Abonnement und mein Log Analytics-Arbeitsbereich zu einem anderen Abonnement gehören?

Ja, Ihr Azure Storage-Konto kann zu einem Abonnement und Ihr Log Analytics-Arbeitsbereich zu einem anderen Abonnement gehören.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kann ich unformatierte Protokolle in einem anderen Abonnement speichern?

Ja. Sie können NSG-Flowprotokolle so konfigurieren, dass sie an ein Speicherkonto in einem anderen Abonnement gesendet werden, sofern Sie über die entsprechenden Berechtigungen verfügen und sich das Speicherkonto in derselben Region wie die NSG befindet. Die NSG und das Zielspeicherkonto müssen außerdem demselben Azure Active Directory-Mandanten angehören.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Was ist, wenn ich eine NSG aufgrund der Fehlermeldung „Nicht gefunden“ nicht für Traffic Analytics konfigurieren kann?

Wählen Sie eine unterstützte Region aus. Wenn Sie eine nicht unterstützte Region auswählen, erhalten Sie die Fehlermeldung „Nicht gefunden“. Die unterstützten Regionen sind weiter oben in diesem Artikel aufgeführt.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Was ist, wenn ich den Status „Fehler beim Laden“ unter der Seite mit den NSG-Datenflussprotokollen erhalte?

Der Microsoft.Insights-Anbieter muss registriert sein, damit die Datenflussprotokollierung ordnungsgemäß funktioniert. Wenn Sie sich nicht sicher sind, ob der Anbieter „Microsoft.Insights“ für Ihr Abonnement registriert ist, ersetzen Sie *xxxxx-xxxxx-xxxxxx-xxxx* im folgenden Befehl, und führen Sie dann die folgenden Befehle über PowerShell aus:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Ich habe die Lösung konfiguriert. Warum sehe ich davon nichts im Dashboard?

Es kann bis zu 30 Minuten dauern, bis es zum ersten Mal angezeigt wird. Die Lösung muss zunächst genügend Daten aggregieren, um aussagekräftige Erkenntnisse zu gewinnen. Erst dann werden Berichte generiert. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Was ist zu tun, wenn diese Meldung angezeigt wird: „Für den ausgewählten Zeitraum wurden in diesem Arbeitsbereich keine Daten gefunden. Ändern Sie das Zeitintervall, oder wählen Sie einen anderen Arbeitsbereich aus.“?

Versuchen Sie Folgendes:
- Ändern Sie auf der oberen Leiste den Zeitraum.
- Wählen Sie auf der oberen Leiste einen anderen Log Analytics-Arbeitsbereich aus.
- Versuchen Sie, nach 30 Minuten auf Traffic Analytics zuzugreifen, wenn es vor Kurzem aktiviert wurde.
    
Wenn das Problem bestehen bleibt, wenden Sie sich an das [User Voice-Forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Was ist zu tun, wenn diese Meldung angezeigt wird: „Ihre NSG-Datenflussprotokolle werden zum ersten Mal analysiert. Dieser Vorgang kann 20 bis 30 Minuten in Anspruch nehmen. Schauen Sie später wieder vorbei. 2) Wenn der oben genannte Schritt nicht funktioniert und sich Ihr Arbeitsbereich unter der SKU „Free“ befindet, überprüfen Sie hier die Nutzung Ihres Arbeitsbereichs, um das Kontingent zu prüfen, oder suchen Sie in den häufig gestellten Fragen nach weiteren Informationen.“?

Ggf. wird diese Meldung auf folgenden Gründen angezeigt:
- Traffic Analytics wurde vor Kurzem aktiviert, und es wurde ggf. noch keine ausreichende Datenmenge aggregiert, um aussagekräftige Erkenntnisse zu gewinnen.
- Sie verwenden die kostenlose Version des Log Analytics-Arbeitsbereichs, dessen Kontingentgrenzwerte überschritten wurden. Sie benötigen möglicherweise einen Arbeitsbereich mit größerer Kapazität.
    
Wenn das Problem bestehen bleibt, wenden Sie sich an das [User Voice-Forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Was ist zu tun, wenn diese Meldung angezeigt wird: „Es sieht so aus, als wären Ressourcendaten (Topologie), aber keine Datenflussinformationen verfügbar. Klicken Sie in der Zwischenzeit hier, um Ressourcendaten anzuzeigen, und lesen Sie die häufig gestellten Fragen, um weitere Informationen zu erhalten.“?

Sie sehen die Ressourceninformationen im Dashboard, aber es gibt keine datenflussbezogenen Statistiken. Es sind ggf. keine Daten vorhanden, da keine Kommunikation zwischen den Ressourcen erfolgt. Warten Sie 60 Minuten, und überprüfen Sie dann den Status erneut. Wenn das Problem bestehen bleibt und Sie sicher sind, dass es Kommunikationsdatenflüsse zwischen Ressourcen gibt, wenden Sie sich an das [User Voice-Forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kann ich Traffic Analytics mithilfe von PowerShell oder einer Azure Resource Manager-Vorlage bzw. einem ARM-Client konfigurieren?

Sie können Traffic Analytics mithilfe von Windows PowerShell ab Version 6.2.1 konfigurieren. Informationen zum Konfigurieren der Datenflussprotokollierung und von Traffic Analytics für eine bestimmte Netzwerksicherheitsgruppe (Network Security Group, NSG) mithilfe des Cmdlets „Set“ finden Sie unter [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Informationen zum Abrufen des Status der Datenflussprotokollierung und von Traffic Analytics für eine bestimmte NSG finden Sie unter [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus).

Derzeit können Sie keine Azure Resource Manager-Vorlage verwenden, um Traffic Analytics zu konfigurieren.

Informationen zur Konfiguration von Traffic Analytics mit einem Azure Resource Manager-Client finden Sie in den folgenden Beispielen.

**Beispiel für das Cmdlet „Set“:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Beispiel für das Cmdlet „Get“:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Was kostet Traffic Analytics?

Traffic Analytics ist getaktet. Die Taktung basiert auf der Verarbeitung von Flussprotokolldaten durch den Dienst und der Speicherung der resultierenden verbesserten Protokolle in einem Log Analytics-Arbeitsbereich. 

Beispiel für den [Tarif](https://azure.microsoft.com/pricing/details/network-watcher/) und die Region „USA, Westen-Mitte“: Wenn in einem von Traffic Analytics verarbeiteten Speicherkonto gespeicherte Datenfluss-Protokolldaten 10 GB und im Log Analytics-Arbeitsbereich erfasste erweiterte Protokolle 1 GB umfassen, fallen als Gebühren an: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Wie oft werden Daten von Traffic Analytics verarbeitet?

Informationen hierzu finden Sie im [Abschnitt zur Datenaggregation](./traffic-analytics-schema.md#data-aggregation) im Dokument „Schema und Datenaggregation in Traffic Analytics“.

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Wie entscheidet Traffic Analytics, ob eine IP-Adresse schädlich ist? 

Traffic Analytics nutzt interne Microsoft-Threat Intelligence-Systeme, um eine IP-Adresse als schädlich einzustufen. Diese Systeme nutzen verschiedenste Telemetriequellen, beispielsweise Microsoft-Produkte und -Dienste, Microsoft Digital Crimes Unit (DCU) und Microsoft Security Response Center (MSRC) sowie externe Feeds, und generieren umfassende Informationen, die weit über diese Quellen hinausgehen. Einige dieser Daten sind interne Microsoft-Daten. Falls eine bekannte IP-Adresse als „schädlich“ gekennzeichnet wird, erstellen Sie ein Supportticket, um uns die Details mitzuteilen.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Wie kann ich Benachrichtigungen für Traffic Analytics-Daten festlegen?

Traffic Analytics bietet keine integrierte Unterstützung für Benachrichtigungen. Da Traffic Analytics-Daten in Protokollanalyseprotokollen gespeichert werden, können Sie für diese Daten jedoch benutzerdefinierte Abfragen schreiben und Benachrichtigungen festlegen. Schritte:
- Sie können den Kurzlink für Log Analytics in Traffic Analytics verwenden. 
- Verwenden Sie das [hier dokumentierte Schema](traffic-analytics-schema.md) zum Schreiben Ihrer Abfragen. 
- Klicken Sie auf „Neue Warnungsregel“, um die Benachrichtigung zu erstellen.
- Informationen zum Erstellen der Benachrichtigung finden Sie in der [Dokumentation zu Protokollwarnungen ](../azure-monitor/platform/alerts-log.md).

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Wie kann ich überprüfen, von welchen virtuellen Computern der meiste lokale Datenverkehr empfangen wird?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  Für IP-Adressen:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

Verwenden Sie für die Zeit das Format: jjjj-mm-tt 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Wie kann ich die Standardabweichung beim Datenverkehr überprüfen, den meine virtuellen Computer von lokalen Computern empfangen?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

Für IP-Adressen:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Wie kann ich überprüfen, welche Ports zwischen IP-Paaren mit NSG-Regeln erreichbar (oder blockiert) sind?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Wie kann ich mit der Tastatur in der geografischen Kartenansicht navigieren?

Die Seite mit der geografischen Karte enthält zwei Hauptbereiche:
    
- **Banner:** Das Banner am oberen Rand der geografischen Karte enthält Schaltflächen zur Auswahl von Verteilungsfiltern für Datenverkehr (z. B. Bereitstellung, Datenverkehr aus Ländern/Regionen und schädliche Daten). Wenn Sie auf eine Schaltfläche klicken, wird der entsprechende Filter auf die Karte angewendet. Wenn Sie beispielsweise auf die Schaltfläche „Aktiv“ klicken, werden die aktiven Rechenzentren in Ihrer Bereitstellung auf der Karte hervorgehoben.
- **Karte:** Im Kartenbereich unter dem Banner wird die Verteilung des Datenverkehrs für Azure-Rechenzentren und Länder/Regionen gezeigt.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tastaturnavigation im Banner
    
- Standardmäßig ist auf der Seite mit der geografischen Karte für das Banner der Filter „Azure-Rechenzentren“ ausgewählt.
- Um zu einem anderen Filter zu wechseln, drücken Sie die Taste `Tab` oder `Right arrow`. Um wieder zum vorherigen Filter zu wechseln, drücken Sie `Shift+Tab` oder die Taste `Left arrow`. Die Navigationsrichtung ist von links nach rechts und dann von oben nach unten.
- Drücken Sie `Enter` oder die Pfeiltaste `Down`, um den ausgewählten Filter anzuwenden. Je nach Filterauswahl und Bereitstellung werden ein oder mehrere Knoten unter dem Kartenbereich hervorgehoben.
- Um zwischen Banner und Karte zu wechseln, drücken Sie `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-map"></a>Tastaturnavigation auf der Karte
    
- Nachdem Sie einen beliebigen Filter im Banner ausgewählt und `Ctrl+F6` gedrückt haben, bewegt sich der Fokus zu einem der hervorgehobenen Knoten (**Azure-Rechenzentrum** oder **Land/Region**) in der Kartenansicht.
- Um zu anderen hervorgehobenen Knoten auf der Karte zu gelangen, drücken Sie für die Vorwärtsbewegung entweder die Taste `Tab` oder `Right arrow`. Drücken Sie für die Rückwärtsbewegung `Shift+Tab` oder die Taste `Left arrow`.
- Zur Auswahl eines markierten Knotens in der Karte verwenden Sie `Enter` oder die Taste `Down arrow`.
- Bei Auswahl eines solchen Knotens wird der Fokus auf die **Infotoolbox** des Knotens verschoben. Standardmäßig wird der Fokus auf die geschlossene Schaltfläche in der **Infotoolbox** verschoben. Zur weiteren Navigation in der Ansicht **Box** drücken Sie die Taste `Right arrow` und `Left arrow`, um vorwärts bzw. rückwärts zu navigieren. Das Drücken von `Enter` hat die gleiche Wirkung wie die Auswahl der fokussierten Schaltfläche in der **Infotoolbox**.
- Wenn Sie `Tab` drücken, während der Fokus auf der **Infotoolbox** liegt, bewegt sich der Fokus zu den Endpunkten auf demselben Kontinent wie der ausgewählte Knoten. Mit den Tasten `Right arrow` und `Left arrow` können Sie durch diese Endpunkte navigieren.
- Um zu anderen Clustern von Datenflussendpunkten bzw. Kontinenten zu wechseln, drücken Sie `Tab` für die Vorwärtsbewegung und `Shift+Tab` für die Rückwärtsbewegung.
- Sobald der Fokus auf **Kontinentcluster** liegt, können Sie mit `Enter` oder der Pfeiltaste `Down` die Endpunkte innerhalb des Kontinentclusters markieren. Um im Informationsfeld des Kontinentclusters zu den Endpunkten und zur Schaltfläche zum Schließen zu navigieren, drücken Sie die Taste `Right arrow` oder `Left arrow` für die Vorwärts- bzw. Rückwärtsbewegung. An jedem beliebigen Endpunkt können Sie mit `Shift+L` zur Verbindungslinie vom ausgewählten Knoten zum Endpunkt wechseln. Sie können erneut `Shift+L` drücken, um zum ausgewählten Endpunkt zu navigieren.
        
### <a name="keyboard-navigation-at-any-stage"></a>Tastaturnavigation in beliebiger Phase
    
- `Esc` klappt die erweiterte Auswahl zu.
- Die Taste `Up-arrow` führt die gleiche Aktion wie `Esc` aus. Die Taste `Down arrow` führt die gleiche Aktion wie `Enter` aus.
- Verwenden Sie `Shift+Plus` zum Vergrößern und `Shift+Minus` zum Verkleinern.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Wie kann ich mit der Tastatur in der Ansicht der Topologie der virtuellen Netzwerke navigieren?

Die Seite mit der Topologie der virtuellen Netzwerke hat zwei Hauptabschnitte:
    
- **Banner:** Das Banner in der Topologie der virtuellen Netzwerke bietet Schaltflächen zum Auswählen von Filtern für die Datenverkehrsverteilung (z.B. verbundene virtuelle Netzwerke, getrennte virtuelle Netzwerke und öffentliche IP-Adressen). Wenn Sie auf eine Schaltfläche klicken, wird der entsprechende Filter auf die Topologie angewendet. Wenn Sie beispielsweise auf die Schaltfläche „Aktiv“ klicken, werden in der Topologie die aktiven virtuellen Netzwerke in Ihrer Bereitstellung hervorgehoben.
- **Topology**: Im Topologiebereich unter dem Banner wird die Verteilung des Datenverkehrs unter den virtuellen Netzwerken gezeigt.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tastaturnavigation im Banner
    
- Standardmäßig ist die Auswahl auf der Seite mit der Topologie der virtuellen Netzwerke für das Banner der Filter „Verbundene VNets“.
- Um zu einem anderen Filter zu wechseln, drücken Sie die Taste `Tab` für die Vorwärtsbewegung. Um wieder zum vorherigen Filter zu wechseln, drücken Sie `Shift+Tab`. Die Navigationsrichtung ist von links nach rechts und dann von oben nach unten.
- Drücken Sie `Enter`, um den ausgewählten Filter anzuwenden. Je nach Filterauswahl und Bereitstellung werden ein oder mehrere Knoten (virtuelle Netzwerke) unter dem Topologiebereich hervorgehoben.
- Um zwischen dem Banner und der Topologie zu wechseln, drücken Sie `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Tastaturnavigation in der Topologie
    
- Nachdem Sie einen beliebigen Filter im Banner ausgewählt und `Ctrl+F6` gedrückt haben, bewegt sich der Fokus zum hervorgehobenen Knoten (**VNet**) in der Topologieansicht.
- Um zu anderen hervorgehobenen Knoten in der Topologieansicht zu gelangen, drücken Sie für die Vorwärtsbewegung `Shift+Right arrow`. 
- Bei hervorgehobenen Knoten wird der Fokus zur **Infotoolbox** des Knotens verschoben. Standardmäßig wird der Fokus zur Schaltfläche **Weitere Details** in der **Infotoolbox** verschoben. Zur weiteren Navigation in der Ansicht **Box** drücken Sie die Taste `Right arrow` oder `Left arrow`, um vorwärts bzw. rückwärts zu navigieren. Das Drücken von `Enter` hat die gleiche Wirkung wie die Auswahl der fokussierten Schaltfläche in der **Infotoolbox**.
- Wenn Sie einen solchen Knoten auswählen, können Sie alle seine Verbindungen nacheinander aufrufen, indem Sie `Shift+Left arrow` drücken. Der Fokus wird zur **Infotoolbox** dieser Verbindung verschoben. Jederzeit kann der Fokus durch erneutes Drücken von `Shift+Right arrow` zurück zum Knoten verschoben werden.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Wie kann ich mit der Tastatur in der Ansicht der Topologie der Subnetze navigieren?

Die Seite mit der Topologie der Subnetze hat zwei Hauptabschnitte:
    
- **Banner:** Das Banner in der Topologie der Subnetze bietet Schaltflächen zum Auswählen von Filtern für die Datenverkehrsverteilung (z.B. „Aktiv“, „Mittel“ und „Gatewaysubnetze“). Wenn Sie auf eine Schaltfläche klicken, wird der entsprechende Filter auf die Topologie angewendet. Wenn Sie beispielsweise auf die Schaltfläche „Aktiv“ klicken, werden in der Topologie die aktiven Subnetze in Ihrer Bereitstellung hervorgehoben.
- **Topology**: Im Topologiebereich unter dem Banner wird die Verteilung des Datenverkehrs unter den virtuellen Subnetzen gezeigt.
    
### <a name="keyboard-navigation-on-the-banner"></a>Tastaturnavigation im Banner
    
- Standardmäßig ist die Auswahl auf der Seite mit der Topologie der virtuellen Subnetze für das Banner der Filter „Subnetze“.
- Um zu einem anderen Filter zu wechseln, drücken Sie die Taste `Tab` für die Vorwärtsbewegung. Um wieder zum vorherigen Filter zu wechseln, drücken Sie `Shift+Tab`. Die Navigationsrichtung ist von links nach rechts und dann von oben nach unten.
- Drücken Sie `Enter`, um den ausgewählten Filter anzuwenden. Je nach Filterauswahl und Bereitstellung werden ein oder mehrere Knoten (Subnetze) unter dem Topologiebereich hervorgehoben.
- Um zwischen dem Banner und der Topologie zu wechseln, drücken Sie `Ctrl+F6`.
        
### <a name="keyboard-navigation-on-the-topology"></a>Tastaturnavigation in der Topologie
    
- Nachdem Sie einen beliebigen Filter im Banner ausgewählt und `Ctrl+F6` gedrückt haben, bewegt sich der Fokus zu einem der hervorgehobenen Knoten (**Subnetz**) in der Topologieansicht.
- Um zu anderen hervorgehobenen Knoten in der Topologieansicht zu gelangen, drücken Sie für die Vorwärtsbewegung `Shift+Right arrow`. 
- Bei hervorgehobenen Knoten wird der Fokus zur **Infotoolbox** des Knotens verschoben. Standardmäßig wird der Fokus zur Schaltfläche **Weitere Details** in der **Infotoolbox** verschoben. Zur weiteren Navigation in der Ansicht **Box** drücken Sie die Taste `Right arrow` und `Left arrow`, um vorwärts bzw. rückwärts zu navigieren. Das Drücken von `Enter` hat die gleiche Wirkung wie die Auswahl der fokussierten Schaltfläche in der **Infotoolbox**.
- Wenn Sie einen solchen Knoten auswählen, können Sie alle seine Verbindungen nacheinander aufrufen, indem Sie `Shift+Left arrow` drücken. Der Fokus wird zur **Infotoolbox** dieser Verbindung verschoben. Jederzeit kann der Fokus durch erneutes Drücken von `Shift+Right arrow` zurück zum Knoten verschoben werden.

## <a name="are-classic-nsgs-supported"></a>Werden klassische Netzwerksicherheitsgruppen unterstützt?
Nein, Traffic Analytics unterstützt keine klassischen Netzwerksicherheitsgruppen (NSGs). Es wird empfohlen, IaaS-Ressourcen vom klassischen Schema zu Azure Resource Manager zu migrieren, da klassische Ressourcen als [veraltet](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation) markiert werden. [Lesen Sie diesen Artikel](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview), um zu erfahren, wie Sie die Migration ausführen.
