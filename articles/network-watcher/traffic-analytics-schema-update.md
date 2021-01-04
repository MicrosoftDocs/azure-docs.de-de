---
title: Aktualisierung des Azure Traffic Analytics-Schemas – März 2020 | Microsoft-Dokumentation
description: Hier finden Sie Beispielabfragen mit neuen Feldern im Traffic Analytics-Schema.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: be69e7fc0c836257b92aa8573826fe1e8a36d770
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948441"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Beispielabfragen mit neuen Feldern im Traffic Analytics-Schema (Schemaaktualisierung vom August 2019)

Das [Traffic Analytics-Protokollschema](./traffic-analytics-schema.md) enthält die folgenden neuen Felder: **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s**. Die neuen Felder enthalten Informationen zu Quell- und Ziel-IP-Adressen und sie vereinfachen Abfragen.

In den nächsten Monaten werden die folgenden veralteten Felder eingestellt: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

Die folgenden drei Beispiele zeigen, wie die alten Felder durch die neuen ersetzt werden können.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Beispiel 1: Felder für VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s und PublicIPs_s

Aus dem Feld **FlowDirection_s** müssen wir für AzurePublic- und ExternalPublic-Flows keine Quell- und Zielfälle ableiten. Es kann auch unangebracht sein, das Feld **FlowDirection_s** für ein virtuelles Netzwerkgerät zu verwenden.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Beispiel 2: Feld NSGRules_s

Das alte Feld hat das folgende Format verwendet:

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

Die Daten werden nicht mehr über eine Netzwerksicherheitsgruppe (NSG) aggregiert. Im aktualisierten Schema enthält **NSGList_s** nur eine Netzwerksicherheitsgruppe. Außerdem enthält **NSGRules** nur eine Regel. Wir haben hier und in anderen Feldern die komplizierte Formatierung entfernt, wie im Beispiel gezeigt.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Beispiel 3: Feld FlowCount_d

Da die Daten nicht übergreifend für Netzwerksicherheitsgruppen zusammengefasst werden, ist **FlowCount_d** einfach:

**AllowedInFlows_d** + **DeniedInFlows_d** + **AllowedOutFlows_d** + **DeniedOutFlows_d**

Nur eines der vier Felder wird nicht 0 (null) sein. Die anderen drei Felder weisen den Wert 0 (null) auf. Die Felder werden aufgefüllt, um den Status und die Anzahl an der NIC anzugeben, in der der Flow erfasst wurde.

Um diese Bedingungen zu veranschaulichen:

- Wenn der Flow zulässig war, wird eines der Felder mit dem Präfix „Allowed" aufgefüllt.
- Wenn der Flow abgelehnt wurde, wird eines der Felder mit dem Präfix „Denied“ aufgefüllt.
- Wenn der Flow eingehend war, wird eines der Felder mit dem Suffix „InFlows_d“ gefüllt.
- Wenn der Flow ausgehend war, wird eines der Felder mit dem Suffix „OutFlows_d“ gefüllt.

Je nach den Bedingungen wissen wir, welches der vier Felder gefüllt werden wird.

## <a name="next-steps"></a>Nächste Schritte

- Antworten auf häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zu Traffic Analytics](traffic-analytics-faq.md).
- Weitere Informationen zu Funktionen finden Sie in der [Traffic Analytics-Dokumentation](traffic-analytics.md).