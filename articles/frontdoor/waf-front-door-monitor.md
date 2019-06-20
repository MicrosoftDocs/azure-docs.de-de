---
title: Überwachung und Protokollierung von Azure Web Application Firewall
description: Informationen zu Web Application Firewall (WAF) mit Front Door-Überwachung und -Protokollierung
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478974"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Überwachung und Protokollierung von Azure Web Application Firewall 

Die Überwachung und Protokollierung von Azure Web Application Firewall (WAF) werden mittels Protokollierung und Integration in Azure Monitor und Azure Monitor-Protokolle ermöglicht.

## <a name="azure-monitor"></a>Azure Monitor

WAF mit Front Door-Protokoll ist in [Azure Monitor](../azure-monitor/overview.md) integriert. Über Azure Monitor können Sie Diagnoseinformationen einschließlich WAF-Warnungen und -Protokolle nachverfolgen. Sie können die WAF-Überwachung in der Front Door-Ressource im Portal auf der Registerkarte **Diagnose** oder direkt über den Azure Monitor-Dienst konfigurieren.

Navigieren Sie im Azure-Portal zum Front Door-Ressourcentyp. Auf der Registerkarte **Metriken** unter **Überwachung** können Sie **WebApplicationFirewallRequestCount** hinzufügen, um die Anzahl der mit WAF-Regeln übereinstimmenden Anforderungen nachzuverfolgen. Benutzerdefinierte Filter können basierend auf Aktionstypen und Regelnamen erstellt werden.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Protokolle und Diagnose

WAF mit Front Door bietet detaillierte Berichte zu jeder erkannten Bedrohung. Die Protokollierung ist in Azure Diagnostics-Protokolle integriert, und Warnungen werden in einem JSON-Format erfasst. Diese Protokolle können in [Azure Monitor-Protokolle](../azure-monitor/insights/azure-networking-analytics.md) integriert werden.

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog protokolliert alle Anforderungen, die an Back-Ends von Kunden weitergeleitet werden. FrontdoorWebApplicationFirewallLog protokolliert jede Anforderung, die mit einer WAF-Regel übereinstimmt.

Mit der folgenden Beispielabfrage werden WAF-Protokolle für blockierte Anforderungen abgerufen:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Mit der folgenden Beispielabfrage werden AccessLogs-Einträge abgerufen:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Front Door](front-door-overview.md)

