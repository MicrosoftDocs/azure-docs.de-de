---
title: Was ist Azure Lighthouse?
description: Mit Azure Lighthouse können Dienstanbieter verwaltete Dienste für Ihre Kunden bereitstellen, bei denen bedarfsorientiert eine höhere Automatisierung und Effizienz erzielt werden können.
ms.date: 11/12/2020
ms.topic: overview
ms.openlocfilehash: a8fd796218804a79c82a5723c0e5851af359cdea
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95528821"
---
# <a name="what-is-azure-lighthouse"></a>Was ist Azure Lighthouse?

Azure Lighthouse ermöglicht die mandantenübergreifende Verwaltung sowie die Verwaltung mehrerer Mandanten mit stärkerer Automatisierung, besserer Skalierbarkeit und verbesserter Governance für alle Ressourcen und Mandanten.

Mit Azure Lighthouse können Dienstanbieter verwaltete Dienste mithilfe umfassender und stabiler Verwaltungstools bereitstellen, die in die Azure-Plattform integriert sind. Kunden behalten die Kontrolle darüber, wer auf ihren Mandanten zugreifen kann, auf welche Ressourcen sie Zugriff haben und welche Aktionen durchgeführt werden können. Von diesem Angebot können auch [IT-Abteilungen von Unternehmen](concepts/enterprise.md) profitieren, die Ressourcen auf mehreren Mandanten verwalten.

![Übersichtsdiagramm von Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Vorteile

Azure Lighthouse dient Dienstanbietern beim effizienten Erstellen und Bereitstellen verwalteter Dienste als Hilfe. Dies hat unter anderem folgende Vorteile:

- **Bedarfsorientierte Verwaltung**: Kundenbindungs- und Lebenszyklusvorgänge zur Verwaltung von Kundenressourcen sind einfacher und besser skalierbar. Vorhandene APIs, Verwaltungstools und Workflows können mit delegierten Ressourcen, z. B. auch außerhalb von Azure gehosteten Computern, verwendet werden. Dies gilt unabhängig von den Regionen, in denen sie sich befinden.
- **Bessere Transparenz und Kontrolle für Kunden**: Kunden können präzise steuern, welche Bereiche sie für die Verwaltung delegieren und welche Berechtigungen zulässig sind. Sie können Aktionen von Dienstanbietern überwachen und die Zugriffsmöglichkeit bei Bedarf vollständig entfernen.
- **Umfassende und einheitliche Plattformtools**: Unsere Toolerfahrung ist auf wichtige Dienstanbieterszenarien ausgelegt, einschließlich mehrerer Lizenzierungsmodelle wie EA (Enterprise Agreement), CSP (Cloud-Lösungsanbieter) und nutzungsbasierte Bezahlung. Für Azure Lighthouse können vorhandene Tools und APIs, Lizenzierungsmodelle, [von Azure verwaltete Anwendungen](concepts/managed-applications.md) und Partnerprogramme, z. B. das [Cloud Solution Provider-Programm (CSP)](/partner-center/csp-overview), genutzt werden. Sie können Azure Lighthouse in Ihre vorhandenen Workflows und Anwendungen integrieren und nachverfolgen, welchen Einfluss Sie auf die Kundenbindung haben, indem Sie [Ihre Partner-ID verknüpfen](./how-to/partner-earned-credit.md).

Es fallen keine zusätzlichen Kosten für die Verwendung von Azure Lighthouse zur Verwaltung von Azure-Ressourcen an. Jeder Azure-Kunde oder -Partner kann Azure Lighthouse verwenden.

## <a name="capabilities"></a>Funktionen

Azure Lighthouse umfasst mehrere Möglichkeiten zur Optimierung von Kundenbindung und Verwaltung:

- **Delegierte Azure-Ressourcenverwaltung**: [Verwalten Sie die Azure-Ressourcen Ihrer Kunden sicher von Ihrem eigenen Mandanten aus](concepts/azure-delegated-resource-management.md), ohne den Kontext oder Steuerungsebenen wechseln zu müssen. Kundenabonnements und Ressourcengruppen können an bestimmte Benutzer und Rollen im Verwaltungsmandanten delegiert werden, und es besteht die Möglichkeit, den Zugriff bei Bedarf zu entfernen.
- **Neue Azure-Portalerfahrung**: Zeigen Sie mandantenübergreifende Informationen auf der [Seite **Meine Kunden**](how-to/view-manage-customers.md) im Azure-Portal an. Auf einer entsprechenden [Seite **Dienstanbieter**](how-to/view-manage-service-providers.md) können Kunden den Dienstanbieterzugriff anzeigen und verwalten.
- **Azure Resource Manager-Vorlagen**: Verwenden Sie ARM-Vorlagen zum [Durchführen des Onboardings für delegierte Kundenressourcen](how-to/onboard-customer.md) und [Durchführen von mandantenübergreifenden Verwaltungsaufgaben](samples/index.md).
- **Angebote für verwaltete Dienste im Azure Marketplace:** [Bieten Sie Ihre Dienste Kunden als private oder öffentliche Angebote an](concepts/managed-services-offers.md), und nutzen Sie das automatische Onboarding für Azure Lighthouse.

> [!TIP]
> Ein ähnliches Angebot, [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181), hilft IT-Partnern dabei, Ihre Microsoft 365 Kunden skaliert zu integrieren, zu überwachen und zu verwalten. Microsoft 365 Lighthouse befindet sich derzeit in der privaten Vorschau.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur [delegierten Azure-Ressourcenverwaltung](concepts/azure-delegated-resource-management.md)
- Erfahren Sie mehr über [mandantenübergreifende Verwaltungsmöglichkeiten](concepts/cross-tenant-management-experience.md).
- Informieren Sie sich über die [Verwendung von Azure Lighthouse in einem Unternehmen](concepts/enterprise.md).
