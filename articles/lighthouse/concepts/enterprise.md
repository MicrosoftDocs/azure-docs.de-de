---
title: Azure Lighthouse in Unternehmensszenarien
description: Die Funktionen von Azure Lighthouse können zum Vereinfachen der mandantenübergreifenden Verwaltung in einem Unternehmen, in dem mehrere Azure AD-Mandanten vorhanden sind, verwendet werden.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 3f452e6810fa6809b5ba1b83b664f8b38d82a895
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004244"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in Unternehmensszenarien

Ein häufiges Szenario für [Azure Lighthouse](../overview.md) ist ein Dienstanbieter, der Ressourcen in den Azure AD-Mandanten (Azure Active Directory) seiner Kunden verwaltet. Die Funktionen von Azure Lighthouse können aber auch zum Vereinfachen der mandantenübergreifenden Verwaltung in einem Unternehmen, in dem mehrere Azure AD-Mandanten vorhanden sind, verwendet werden.

## <a name="single-vs-multiple-tenants"></a>Einzelner Mandant und mehrere Mandanten

Für die meisten Organisationen ist die Verwaltung mit nur einem Azure AD-Mandanten einfacher. Wenn sich alle Ressourcen unter einem Mandanten befinden, können die Verwaltungsaufgaben nach den jeweiligen Benutzern, Benutzergruppen oder Dienstprinzipalen des Mandanten zentralisiert werden. Wir empfehlen Ihnen, nach Möglichkeit nur einen Mandanten für Ihre Organisation zu verwenden. Einige Organisationen können allerdings über mehrere Azure AD-Mandanten verfügen. Manchmal ist dies nur vorübergehend erforderlich, wenn beispielsweise Übernahmen erfolgt sind und noch keine langfristige Konsolidierungsstrategie für Mandanten definiert wurde. Es kann auch sein, dass Organisationen aufgrund von unabhängigen Tochtergesellschaften, geografischen oder rechtlichen Anforderungen oder anderen Aspekten fortlaufend mehrere Mandanten verwalten müssen.

Falls eine Architektur mit mehreren Mandanten benötigt wird, kann Azure Lighthouse zur Zentralisierung und Optimierung von Verwaltungsvorgängen beitragen. Durch Verwendung der [delegierten Azure-Ressourcenverwaltung](azure-delegated-resource-management.md) können Benutzer in einem verwalteten Mandanten [mandantenübergreifende Verwaltungsfunktionen](cross-tenant-management-experience.md) zentral und skalierbar ausführen.

## <a name="tenant-management-architecture"></a>Architektur für die Mandantenverwaltung

Um Azure Lighthouse in einem Unternehmen zu verwenden, müssen Sie bestimmen, welcher Mandant die Benutzer umfasst, die Verwaltungsvorgänge für die anderen Mandanten durchführen. Sie müssen also ermitteln, welcher Mandant der verwaltende Mandant für die anderen Mandanten ist.

Angenommen, Ihre Organisation verfügt über einen einzelnen Mandanten mit dem Namen *Mandant A*. Ihre Organisation schafft dann *Mandant B* und *Mandant C* an, die aus geschäftlichen Gründen separat verwaltet werden müssen.

Ihre Organisation möchte auf allen Mandanten die gleichen Richtliniendefinitionen, Sicherungsmethoden und Sicherheitsprozesse verwenden. Da Mandant A bereits Benutzer enthält, die für diese Aufgaben zuständig sind, können Sie Abonnements in Mandant B und Mandant C integrieren, damit die gleichen Benutzer in Mandant A diese Aufgaben durchführen können.

![Diagramm zu Benutzern in Mandant A, die Ressourcen in Mandant B und Mandant C verwalten](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Sicherheits- und Zugriffsaspekte

In den meisten Unternehmensszenarien empfiehlt es sich, ein vollständiges Abonnement an Azure Lighthouse zu delegieren. Sie können auch festlegen, dass nur bestimmte Ressourcengruppen innerhalb eines Abonnements delegiert werden.

Achten Sie aber jeweils darauf, dass Sie sich [beim Festlegen, welche Benutzer Zugriff auf delegierte Ressourcen haben, an das Prinzip der geringsten Rechte halten](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). So stellen Sie sicher, dass die Benutzer nur über die Berechtigungen verfügen, die sie zum Durchführen der erforderlichen Aufgaben benötigen, und das Risiko unbeabsichtigter Fehler wird reduziert.

Bei Azure Lighthouse werden nur Verknüpfungen zwischen einem verwaltenden Mandanten und den verwalteten Mandanten bereitgestellt, anstatt Daten oder Ressourcen physisch zu verschieben. Außerdem ist der Zugriff immer nur in einer Richtung möglich, und zwar vom verwaltenden Mandanten zu den verwalteten Mandanten.  Für Benutzer und Gruppen auf dem verwaltenden Mandanten sollte weiterhin die mehrstufige Authentifizierung genutzt werden, wenn Verwaltungsvorgänge auf verwalteten Mandantenressourcen durchgeführt werden.

Unternehmen mit Leitlinien für interne oder externe Governance und Compliance können [Azure-Aktivitätsprotokolle](../../azure-monitor/platform/platform-logs-overview.md) verwenden, um die jeweiligen Transparenzanforderungen zu erfüllen. Wenn für Unternehmensmandanten Beziehungen zwischen verwaltenden und verwalteten Mandanten eingerichtet wurden, können die Benutzer in den Mandanten protokollierte Aktivitäten anzeigen und so Aktionen sehen, die von Benutzern im verwaltenden Mandanten ausgeführt wurden.

## <a name="onboarding-considerations"></a>Überlegungen zum Onboarding

Für Abonnements (bzw. Ressourcengruppen unter einem Abonnement) kann das Onboarding in Azure Lighthouse durchgeführt werden. Hierzu werden entweder Azure Resource Manager-Vorlagen bereitgestellt oder im Azure Marketplace veröffentlichte Angebote für verwaltete Dienste genutzt.

Da Unternehmensbenutzer normalerweise Direktzugriff auf die Mandanten des Unternehmens haben und für ein Verwaltungsangebot keine Marketing- oder Förderungsmaßnahmen erforderlich sind, ist es in der Regel schneller und einfacher, Azure Resource Manager-Vorlagen bereitzustellen. Zwar wird in der [Anleitung für das Onboarding](../how-to/onboard-customer.md) auf Dienstanbieter und Kunden verwiesen, doch können Unternehmen die gleichen Prozesse für das Onboarding ihrer Mandanten verwenden.

Falls Sie diese Vorgehensweise bevorzugen, kann das Onboarding für Mandanten eines Unternehmens durchgeführt werden, indem ein [Angebot für verwaltete Dienste im Azure Marketplace veröffentlicht wird](../how-to/publish-managed-services-offers.md). Achten Sie darauf, dass Ihre Pläne als „Privat“ gekennzeichnet sind, um sicherzustellen, dass das Angebot nur für die richtigen Mandanten verfügbar ist. Bei einem privaten Plan geben Sie die Abonnement-IDs für jeden Mandanten an, für den das Onboarding durchgeführt werden soll. Es erhalten dann keine anderen Personen Ihr Angebot.

## <a name="terminology-notes"></a>Hinweise zur Terminologie

Für die mandantenübergreifende Verwaltung im Unternehmen können Verweise auf Dienstanbieter in der Azure Lighthouse-Dokumentation so interpretiert werden, dass sie für den verwaltenden Mandanten eines Unternehmens gelten. Dies ist der Mandant mit den Benutzern, die Ressourcen in anderen Mandanten über Azure Lighthouse verwalten. Verweise auf Kunden können entsprechend so interpretiert werden, dass sie für die Mandanten gelten, die Ressourcen für die Verwaltung durch Benutzer des verwaltenden Mandanten delegieren.

Im obigen Beispiel können Sie sich Mandant A als Dienstanbietermandanten (verwaltender Mandant) und Mandant B und Mandant C als Kundenmandanten vorstellen.

In diesem Beispiel können Benutzer von Mandant A, die über die richtigen Berechtigungen verfügen, [delegierte Ressourcen anzeigen und verwalten](../how-to/view-manage-customers.md), indem sie im Azure-Portal die Seite **Meine Kunden** verwenden. Ebenso können Benutzer von Mandant B und Mandant C mit den richtigen Berechtigungen [die Ressourcen anzeigen und verwalten](../how-to/view-manage-service-providers.md), die an Mandant A delegiert wurden, indem sie im Azure-Portal die Seite **Dienstanbieter** verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](cross-tenant-management-experience.md).
- Informationen zur [delegierten Azure-Ressourcenverwaltung](azure-delegated-resource-management.md)