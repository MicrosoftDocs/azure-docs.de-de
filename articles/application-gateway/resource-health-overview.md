---
title: Übersicht über Resource Health für Azure Application Gateway
description: Dieser Artikel enthält eine Übersicht über das Resource Health-Feature für Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397171"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Übersicht über Resource Health für Azure Application Gateway

[Azure Resource Health](../service-health/resource-health-overview.md) unterstützt Sie bei der Diagnose und bei Supportanfragen, wenn ein Azure-Dienstproblem Auswirkungen auf Ihre Ressourcen hat. Der Dienst informiert Sie über die aktuelle und frühere Integrität Ihrer Ressourcen. Zudem bietet er technische Unterstützung beim Beheben von Problemen.

Bei Application Gateway ermittelt Resource Health anhand von Signalen, die vom Gateway ausgegeben werden, ob das Gateway fehlerfrei ist. Sollte das Gateway fehlerhaft sein, analysiert Resource Health zusätzliche Informationen, um die Ursache des Problems zu bestimmen. Darüber hinaus werden von Microsoft ausgeführte Aktionen oder Schritte angegeben, die Sie selbst ausführen können, um das Problem zu beheben.

Weitere Details zur Bewertung der Integrität entnehmen Sie der vollständigen Liste von Ressourcentypen und Integritätsprüfungen in [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Der Integritätsstatus für Application Gateway wird als eine der folgenden Statusoptionen angezeigt:

## <a name="available"></a>Verfügbar

Der Status **Verfügbar** bedeutet, dass der Dienst keine Ereignisse erkannt hat, die die Integrität der Ressource beeinflussen. In Fällen, in denen das Gateway innerhalb der letzten 24 Stunden nach einer ungeplanten Downtime wiederhergestellt wurde, wird die Benachrichtigung **Kürzlich behandelt** angezeigt.

![Integritätsstatus „Verfügbar“](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Nicht verfügbar

Der Status **Nicht verfügbar** bedeutet, dass der Dienst ein aktives plattformbezogenes oder nicht plattformbezogenes Ereignis erkannt hat, das Auswirkungen auf die Integrität des Gateways hat.

### <a name="platform-events"></a>Plattformereignisse

Plattformereignisse werden von mehreren Komponenten der Azure-Infrastruktur ausgelöst. Dazu zählen sowohl geplante Aktionen (z.B. geplante Wartung) als auch unerwartete Vorfälle (z.B. ein nicht geplanter Neustart eines Hosts).

Resource Health stellt weitere Details zum Ereignis und zum Wiederherstellungsprozess bereit. Darüber hinaus können Sie sich selbst dann an den Support wenden, wenn Sie nicht über eine aktive Microsoft-Supportvereinbarung verfügen.

![Status „Nicht verfügbar“](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unbekannt

Der Integritätsstatus **Unbekannt** gibt an, dass Resource Health seit mehr als zehn Minuten keine Informationen mehr zum Gateway empfangen hat. Dieser Status ist keine definitive Angabe des Gatewayzustands. Er ist jedoch ein wichtiger Datenpunkt für die Problembehandlung.

Wenn das Gateway wie erwartet funktioniert, ändert sich der Status nach wenigen Minuten in **Verfügbar**.

Sollten Probleme vorliegen, kann der Integritätsstatus **Unbekannt** darauf hindeuten, dass das Gateway durch ein Ereignis auf der Plattform beeinträchtigt wird.

![Status „Unbekannt“](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Heruntergestuft

Der Integritätsstatus **Heruntergestuft** gibt an, dass Leistungseinbußen für Ihr Gateway festgestellt wurden, es aber weiterhin verwendbar ist.

![Status „Heruntergestuft“](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Behandlung von Problemen mit der Web Application Firewall (WAF) für Application Gateway finden Sie unter [Problembehandlung für die Web Application Firewall (WAF) für Azure Application Gateway](../web-application-firewall/ag/web-application-firewall-troubleshoot.md).