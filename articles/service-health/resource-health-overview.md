---
title: Übersicht über Azure Resource Health | Microsoft-Dokumentation
description: Übersicht über Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 05/10/2019
ms.openlocfilehash: e79f2924448b69989cc563b7b3b30bca0540533f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067203"
---
# <a name="azure-resource-health-overview"></a>Übersicht über Azure Resource Health
 
Azure Resource Health unterstützt Sie bei der Diagnose und bei Supportanfragen, wenn ein Azure-Dienstproblem Auswirkungen auf Ihre Ressourcen hat. Der Dienst informiert Sie über die aktuelle und frühere Integrität Ihrer Ressourcen. Zudem bietet er technische Unterstützung beim Beheben von Problemen.

Während [Azure-Status](https://status.azure.com) Sie über Dienstprobleme informiert, die einen großen Azure-Kundenkreis betreffen, bietet Ihnen Resource Health ein personalisiertes Dashboard der Integrität Ihrer Ressourcen. Resource Health teilt Ihnen mit, wie oft Ihre Ressourcen in der Vergangenheit aufgrund von Azure-Dienstproblemen nicht verfügbar waren. So können Sie leicht erkennen, ob eine SLA verletzt wurde. 

## <a name="resource-definition-and-health-assessment"></a>Ressourcendefinition und Integritätsbewertung

Eine Ressource ist eine bestimmte Instanz eines Azure-Diensts, z. B. ein virtueller Computer, eine Web-App oder eine SQL-Datenbank.

Resource Health ermittelt anhand von Signalen, die von den verschiedenen Azure-Diensten ausgegeben werden, ob eine Ressource fehlerfrei ist. Wenn eine Ressource fehlerhaft ist, analysiert Resource Health zusätzliche Informationen, um die Quelle des Problems zu bestimmen. Resource Health identifiziert auch Aktionen, die Microsoft zum Beheben des Problems durchführt, oder welche Aktionen Sie durchführen können, um die Ursache des Problems zu beseitigen. 

Weitere Details zur Bewertung der Integrität entnehmen Sie der vollständigen Liste von Ressourcentypen und Integritätsprüfungen in [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Integritätsstatus

Die Integrität einer Ressource wird als einer der folgenden Status angezeigt.

### <a name="available"></a>Verfügbar

Der Status **Verfügbar** bedeutet, dass der Dienst noch keine Ereignisse erkannt hat, die die Integrität der Ressource beeinflussen. In Fällen, in denen die Ressource während der letzten 24 Stunden nach einer ungeplanten Ausfallzeit wiederhergestellt worden ist, wird die Benachrichtigung **Kürzlich behandelt** angezeigt.

![Status „Verfügbar“ bei einem virtuellen Computer mit der Benachrichtigung „Kürzlich behandelt“](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Nicht verfügbar

Der Status **Nicht verfügbar** bedeutet, dass der Dienst ein aktives Plattform- oder Nicht-Plattformereignis erkannt hat, das Auswirkungen auf die Integrität der Ressource hat.

#### <a name="platform-events"></a>Plattformereignisse

Plattformereignisse werden von mehreren Komponenten der Azure-Infrastruktur ausgelöst. Dazu zählen sowohl geplante Aktionen (z.B. geplante Wartung) als auch unerwartete Vorfälle (z.B. ein nicht geplanter Neustart eines Hosts).

Resource Health stellt weitere Details zum Ereignis und zum Wiederherstellungsprozess bereit. Darüber hinaus können Sie sich selbst dann an den Support wenden, wenn Sie nicht über eine aktive Microsoft-Supportvereinbarung verfügen.

![Status „Nicht verfügbar“ bei einem virtuellen Computer aufgrund eines Plattformereignisses](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Nicht-Plattformereignisse

Nicht-Plattformereignisse werden durch Aktionen von Benutzern ausgelöst. Beispiele dafür sind das Anhalten eines virtuellen Computers oder das Erreichen der maximalen Anzahl von Verbindungen mit einer Azure Cache for Redis-Instanz.

![Status „Nicht verfügbar“ bei einem virtuellen Computer aufgrund eines Nicht-Plattformereignisses](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

Der Integritätsstatus **Unbekannt** gibt an, dass Resource Health seit mehr als 10 Minuten keine Informationen mehr zu dieser Ressource empfangen hat. Dieser Status ist zwar keine definitive Angabe des Ressourcenzustands, aber ein wichtiger Datenpunkt im Problembehandlungsprozess.

Wenn die Ressource wie erwartet ausgeführt wird, wird der Status der Ressource nach wenigen Minuten in **Verfügbar** geändert.

Wenn Probleme bei der Ressource auftreten, kann der Integritätsstatus **Unbekannt** darauf hindeuten, dass die Ressource durch ein Ereignis auf der Plattform beeinträchtigt wird.

![Status „Unbekannt“ bei einem virtuellen Computer](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Heruntergestuft

Der Integritätsstatus **Heruntergestuft** gibt an, dass Ihre Ressource einen Leistungsverlust festgestellt hat, obwohl sie noch für die Nutzung verfügbar ist.
Verschiedene Ressourcen verwenden ihre eigenen Kriterien, anhand derer sie angeben, dass eine Ressource einen Leistungsverlust erleidet.

![Status „Heruntergestuft“ bei einem virtuellen Computer](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Melden eines falschen Status

Wenn Sie glauben, dass der aktuelle Integritätsstatus falsch ist, können Sie uns dies durch die Auswahl von **Falschen Integritätsstatus melden** mitteilen. In Fällen, in denen Sie von einem Azure-Problem betroffen sind, empfehlen wir Ihnen, sich in Resource Health an den Support zu wenden. 

![Feld zum Übermitteln von Informationen zu einem falschen Status](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Verlaufsinformationen

Sie können im Abschnitt **Integritätsverlauf** von Resource Health auf bis zu 14 Tage alte Integritätsverlaufsdaten zugreifen. 

![Liste der Resource Health-Ereignisse in den letzten zwei Wochen](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Erste Schritte

Öffnen von Resource Health für eine Ressource:

1. Melden Sie sich beim Azure-Portal an.
2. Navigieren Sie zu der Ressource.
3. Wählen Sie im linken Bereich im Ressourcenmenü die Option **Ressourcenintegrität** aus.

![Öffnen von Resource Health über die Ressourcenansicht](./media/resource-health-overview/from-resource-blade.png)

Sie können auch auf Resource Health zugreifen, indem Sie **Alle Dienste** auswählen und im Filtertextfeld **Resource Health** eingeben. Wählen Sie im Bereich **Hilfe und Support** die Option [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth) aus.

![Öffnen von Resource Health über „Alle Dienste“](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Resource Health finden Sie in den folgenden Dokumenten:
-  [Ressourcentypen und Integritätsprüfungen in Azure Resource Health](resource-health-checks-resource-types.md)
-  [Azure Resource Health – FAQ](resource-health-faq.md)




