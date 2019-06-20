---
title: Threat Intelligence-gestütztes Filtern für Azure Firewall
description: Erfahren Sie mehr über das Threat Intelligence-gestützte Filtern für Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194046"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Threat Intelligence-gestütztes Filtern für Azure Firewall – Öffentliche Vorschauversion

Das Filtern auf Basis von Threat Intelligence kann für Ihre Firewall aktiviert werden, damit diese Sie bei Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnt und diesen verweigert. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. Microsoft Threat Intelligence basiert auf [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) und wird von mehreren Diensten einschließlich Azure Security Center verwendet.

![Threat Intelligence für die Firewall](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Das Filtern auf Basis von Threat Intelligence befindet sich derzeit in der öffentlichen Vorschau und wird mit einer Vorschau-SLA (Vereinbarung zum Servicelevel) bereitgestellt. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.  Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn das Threat Intelligence-gestützte Filtern aktiviert ist, werden die zugehörigen Regeln vor allen anderen Regeln (NAT-Regeln, Netzwerkregeln oder Anwendungsregeln) verarbeitet. Während der Vorschauversion sind nur die Datensätze von höchster Zuverlässigkeit enthalten.

Beim Auslösen einer Regel können Sie wahlweise nur eine Warnung protokollieren oder den Modus „Warnen und Verweigern“ verwenden.

Für das Threat Intelligence-gestützte Filtern ist standardmäßig der Warnmodus aktiviert. Bis die Portalschnittstelle in Ihrer Region verfügbar wird, können Sie diese Funktion nicht deaktivieren oder den Modus ändern.

![Portalschnittstelle für Threat Intelligence-gestütztes Filtern](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Protokolle

Der folgende Protokollausschnitt zeigt eine ausgelöste Regel:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testen

- **Testen bei ausgehendem Datenverkehr**: Warnungen bei ausgehendem Datenverkehr sollten selten auftreten, da dies bedeutet, dass Ihre Umgebung kompromittiert ist. Um zu testen, dass Warnungen bei ausgehendem Datenverkehr funktionieren, wurde ein FQDN erstellt, der eine Warnung auslöst. Verwenden Sie **testmaliciousdomain.eastus.cloudapp.azure.com** für die Tests bei ausgehendem Datenverkehr.

- **Testen bei eingehendem Datenverkehr**: Sie können davon ausgehen, das Warnungen bei eingehendem Datenverkehr angezeigt werden, wenn DNAT-Regeln in der Firewall konfiguriert sind. Dies gilt selbst dann, wenn nur bestimmte Quellen in der DNAT-Regel zulässig sind und der sonstige Datenverkehr verweigert wird. Azure Firewall gibt keine Warnungen für alle bekannten Portscanner aus, sondern nur für Scanner, die für ihre gelegentliche Beteiligung an schädlichen Aktivitäten bekannt sind.

## <a name="next-steps"></a>Nächste Schritte

- Siehe [Log Analytics-Beispiele für Azure Firewall](log-analytics-samples.md)
- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).
- Lesen Sie den [Microsoft Security Intelligence Report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report).