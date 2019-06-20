---
title: Identity Governance – Azure Active Directory | Microsoft-Dokumentation
description: Azure Active Directory Identity Governance ermöglicht Ihnen, den Sicherheitsbedarf Ihrer Organisation und die Produktivität von Mitarbeitern mit den richtigen Prozessen sowie Transparenz in Einklang zu bringen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/29/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1be6fc2b7d6da85778524cb8986f399c341370c1
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307048"
---
# <a name="what-is-azure-ad-identity-governance"></a>Was ist Azure AD Identity Governance?

Azure Active Directory (Azure AD) Identity Governance ermöglicht Ihnen, den Sicherheitsbedarf Ihrer Organisation und die Produktivität von Mitarbeitern mit den richtigen Prozessen sowie Transparenz in Einklang zu bringen. Der Dienst bietet Funktionen, mit denen Sie sicherstellen können, dass die richtigen Benutzer geeigneten Zugriff auf die richtigen Ressourcen haben, und die Möglichkeit, den Zugriff auf kritische Ressourcen zu schützen, zu überwachen und nachzuverfolgen, während Sie gleichzeitig die Produktivität Ihrer Mitarbeiter gewährleisten.  

Mit Identity Governance können Organisationen die folgenden Aufgaben sowohl für Mitarbeiter, Geschäftspartner und Anbieter als auch für Dienste und Anwendungen ausführen:

- Steuern des Identitätslebenszyklus
- Steuern des Zugriffslebenszyklus
- Sichere Verwaltung

Insbesondere soll Identity Governance Organisationen bei der Beantwortung der folgenden vier Schlüsselfragen unterstützen:

- Welche Benutzer sollen Zugriff auf welche Ressourcen haben?
- Wozu nutzen diese Benutzer den Zugriff?
- Gibt es effektive organisatorische Kontrollen zum Verwalten des Zugriffs?
- Können Prüfer feststellen, ob die Kontrollen funktionieren?

## <a name="identity-lifecycle"></a>Identitätslebenszyklus

Identity Governance hilft Organisationen, ein Gleichgewicht herzustellen zwischen der *Produktivität* – wie schnell eine Person auf die benötigten Ressourcen zugreifen kann (beispielsweise, wenn sie der Organisation beitritt) – und der *Sicherheit* – wie sich der Zugriff im Laufe der Zeit ändern sollte (beispielsweise aufgrund von Änderungen des Beschäftigungsstatus einer Person).  Identity Lifecycle Management ist die Grundlage für Identity Governance, und eine effektive Governance in großem Maßstab erfordert eine Modernisierung der Identity Lifecycle Management-Infrastruktur für Anwendungen.

![Identitätslebenszyklus](./media/identity-governance-overview/identity-lifecycle.png)

Bei vielen Organisationen ist der Identitätslebenszyklus für Mitarbeiter mit der Darstellung des Benutzers in einem Personalverwaltungssystem (Human Capital Management, HCM) verknüpft.  Wie im [Tutorial zur eingehenden Bereitstellung von Workday (Vorschauversion)](../saas-apps/workday-inbound-tutorial.md) beschrieben, verwaltet Azure AD Premium Benutzeridentitäten für die in Workday dargestellten Benutzer automatisch in Active Directory und Azure Active Directory.  Azure AD Premium umfasst auch [Microsoft Identity Manager](/microsoft-identity-manager/), eine Lösung, mit der Datensätze aus lokalen Personalverwaltungssystemen wie SAP, Oracle eBusiness und Oracle PeopleSoft importiert werden können.

Immer mehr Szenarien erfordern heute die Kollaboration mit Personen außerhalb Ihrer Organisation. Mithilfe der [Azure AD B2B](/azure/active-directory/b2b/)-Kollaboration (Business-to-Business) können Sie die Anwendungen und Dienste Ihrer Organisation für Gastbenutzer und externe Partner von beliebigen Organisationen sicher freigeben und dabei die Kontrolle über Ihre eigenen Unternehmensdaten behalten.

## <a name="access-lifecycle"></a>Zugriffslebenszyklus

Organisationen benötigen einen Prozess, um den Zugriff über die anfänglich beim Erstellen der Identität eines Benutzers zugewiesenen Rechte hinaus zu verwalten.  Zudem müssen Unternehmensorganisationen in der Lage sein, ihre Systeme effizient zu skalieren, damit sie Zugriffsrichtlinien und -kontrollen kontinuierlich entwickeln und erzwingen können.

![Zugriffslebenszyklus](./media/identity-governance-overview/access-lifecycle.png)

In der Regel delegiert die IT-Abteilung Entscheidungen bezüglich der Zugriffsgenehmigung an Entscheidungsträger im Unternehmen.  Darüber hinaus kann die IT die Benutzer selbst in den Prozess einbeziehen.  Beispielsweise müssen Benutzer, die in Europa auf vertrauliche Kundendaten in der Marketinganwendung eines Unternehmens zugreifen, die Richtlinien des Unternehmens kennen. Gastbenutzer kennen die Anforderungen für die Behandlung von Daten in einer Organisation, zu der sie eingeladen wurden, möglicherweise nicht.

Organisationen können den Zugriffslebenszyklus mit Technologien wie [dynamischen Gruppen](../users-groups-roles/groups-dynamic-membership.md) in Verbindung mit der Benutzerbereitstellung in [SaaS-Apps](../saas-apps/tutorial-list.md) oder [in SCIM integrierten Apps](../manage-apps/use-scim-to-provision-users-and-groups.md) automatisieren.  Außerdem können Organisationen steuern, welche [Gastbenutzer Zugriff auf lokale Anwendungen haben](../b2b/hybrid-cloud-to-on-premises.md).  Diese Zugriffsrechte können dann regelmäßig mithilfe von wiederkehrenden [Azure AD-Zugriffsüberprüfungen](access-reviews-overview.md) überprüft werden.

Wenn ein Benutzer auf Anwendungen zuzugreifen versucht, erzwingt Azure AD Richtlinien für [bedingten Zugriff](/azure/active-directory/conditional-access/). Richtlinien für bedingten Zugriff können beispielsweise beinhalten, dass [Nutzungsbedingungen](../conditional-access/terms-of-use.md) angezeigt werden und [der Benutzer diesen Bedingungen zustimmen muss](../conditional-access/require-tou.md), bevor er auf eine Anwendung zugreifen kann.

## <a name="privileged-access-lifecycle"></a>Privilegierter Zugriffslebenszyklus

In der Vergangenheit wurde der privilegierte Zugriff von anderen Anbietern als eine von Identity Governance getrennte Funktion dargestellt. Wir bei Microsoft denken jedoch, dass die Steuerung des privilegierten Zugriffs ein wichtiger Bestandteil von Identity Governance ist – insbesondere angesichts des mit diesen Administratorrechten einhergehenden möglichen Missbrauchs und Schadens für eine Organisation. Die Mitarbeiter, Anbieter und Auftragnehmer, denen Administratorrechte gewährt werden, müssen gesteuert werden.

![Privilegierter Zugriffslebenszyklus](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) bietet zusätzliche Kontrollen zum Sichern des Zugriffs und Verwalten der Zugriffsrechte für Ressourcen in Azure AD, Azure und anderen Microsoft Online Services.  Zusätzlich zur mehrstufigen Authentifizierung und zum bedingten Zugriff stehen Ihnen mit den Funktionen für Just-in-Time-Zugriff und Warnungen für Rollenänderungen von Azure AD PIM umfassende Governancekontrollen zum Schützen der Ressourcen Ihres Unternehmens (Verzeichnis-, Office 365- und Azure-Ressourcenrollen) zur Verfügung. Wie bei anderen Formen des Zugriffs können Organisationen mithilfe von Zugriffsüberprüfungen eine periodische erneute Zertifizierung für alle Benutzer mit Administratorrollen konfigurieren.

## <a name="getting-started"></a>Erste Schritte

Es gibt keine perfekte Lösung oder Empfehlung, die für alle Kunden geeignet ist. Die folgenden Konfigurationen können jedoch als Leitfaden für die Basisrichtlinien dienen, die Microsoft zum Verbessern der Sicherheit und Produktivität von Mitarbeitern empfiehlt.

- [Konfigurationen für den Identitäts- und Gerätezugriff](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Sichern des privilegierten Zugriffs](../users-groups-roles/directory-admin-roles-secure.md)

Sie können auch im Azure-Portal die Registerkarte „Erste Schritte“ von **Identity Governance** öffnen, um mit der Verwendung der Berechtigungsverwaltung, von Zugriffsüberprüfungen, Privileged Identity Management und Nutzungsbedingungen zu beginnen.

![Identity Governance, Registerkarte „Erste Schritte“](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD-Berechtigungsverwaltung? (Vorschauversion)](entitlement-management-overview.md)
- [Was sind Azure AD-Zugriffsüberprüfungen?](access-reviews-overview.md)
- [Was ist Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Welche Möglichkeiten bieten die Nutzungsbedingungen?](active-directory-tou.md)
