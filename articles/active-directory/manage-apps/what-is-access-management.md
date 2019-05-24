---
title: Verwalten des Zugriffs auf Apps mit Azure AD | Microsoft Docs
description: Erläutert, wie Azure Active Directory es Organisationen ermöglicht, die Apps festzulegen, auf die der jeweilige Benutzer Zugriff hat.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 851b9ab3604e851bcaf4f22d031c51558b15e0e6
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826000"
---
# <a name="managing-access-to-apps"></a>Verwalten des Zugriffs auf Apps
Die fortwährende Zugriffsverwaltung sowie die Nutzungsauswertung und Berichterstellung bleiben auch nach Integration einer Anwendung in das Identitätssystem der Organisation eine Herausforderung. In vielen Fällen müssen IT-Administratoren oder das Helpdesk eine fortlaufende aktive Rolle in der Verwaltung des Zugriffs auf Ihre Apps ausüben. Manchmal erfolgt die Zuweisung durch das allgemeine oder abteilungsinterne IT-Team. Oft soll die Zuweisungsentscheidung an einen kommerziellen Entscheidungsträger delegiert werden, da die IT-Abteilung ohnehin dessen Genehmigung einholen muss.  Andere Organisationen investieren in die Integration in ein vorhandenes automatisiertes Identitäts- und Zugriffsverwaltungssystem wie rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) oder attributbasierte Zugriffssteuerung (Attribute-Based Access Control, ABAC). Sowohl die Integration als auch die Entwicklung der Regeln sind häufig spezifisch und kostspielig. Auch die Überwachung oder Berichterstattung stellt bei beiden Verwaltungsansätzen eine eigene Investition dar, die komplex und teuer ist.

## <a name="how-does-azure-active-directory-help"></a>In welcher Weise hilft Azure Active Directory?
 Azure AD unterstützt die weitreichende Zugriffsverwaltung für konfigurierte Anwendungen, sodass Organisationen mühelos geeignete Zugriffsrichtlinien einrichten können, die von Anwendungsfällen mit automatischer, attributbasierter Zuweisung (ABAC- oder RBAC-Szenarios) über Delegierung bis hin zur Verwaltung durch den Administrator reichen. Mit Azure AD lassen sich ohne Aufwand komplexe Richtlinien einrichten, bei denen für eine gegebene Anwendung mehrere Verwaltungsmodelle kombiniert werden, und einmal definierte Verwaltungsregeln lassen sich sogar auf weitere Anwendungen mit demselben Benutzerkreis ausweiten.

* [Hinzufügen von neuen oder vorhandenen Anwendungen](configure-single-sign-on-portal.md)

  Die Anwendungszuweisung von Azure AD konzentriert sich auf zwei primäre Zuordnungsmodi:

* **Einzelne Zuweisung** : Ein IT-Administrator mit den Verzeichnisberechtigungen eines globalen Administrators kann einzelne Benutzerkonten auswählen und ihnen Zugriff auf die Anwendung gewähren.
* **Gruppenbasierte Zuweisung (nur kostenpflichtige Azure AD-Instanz)**: Ein IT-Administrator mit den Verzeichnisberechtigungen eines globalen Administrators kann der Anwendung eine Gruppe zuweisen. Die Zugriffsmöglichkeit bestimmter Benutzer ergibt sich daraus, ob diese zum Zeitpunkt des Zugriffs auf die Anwendung Mitglied der festgelegten Gruppe sind. Mit anderen Worten: Ein Administrator kann im Grunde folgende Zuweisungsregel erstellen: „Jedes derzeitige Mitglied der zugewiesenen Gruppe hat Zugriff auf die Anwendung“. Mit dieser Zuweisungsoption können Administratoren alle zur Verfügung stehenden Optionen zur Azure AD-Gruppenverwaltung nutzen, einschließlich [attributbasierter dynamischer Gruppen](../fundamentals/active-directory-groups-create-azure-portal.md), externer Systemgruppen (z.B. lokale Active Directory- oder Workday-Instanz) oder Gruppen mit Administrator- bzw. mit Self-Service-Verwaltung. Eine einzelne Gruppe kann problemlos mehreren Apps zugewiesen werden, sodass sichergestellt wird, dass Anwendungen mit Zuweisungsaffinität dieselben Zuweisungsregeln verwenden, um die Verwaltungskomplexität insgesamt zu verringern. Beachten Sie, dass geschachtelte Gruppenmitgliedschaften für die gruppenbasierte Zuordnung zu Anwendungen derzeit nicht unterstützt werden.

Durch Einsatz dieser beiden Zuweisungsmodi können Administratoren jeden gewünschten Ansatz zur Zuweisungsverwaltung realisieren.

Bei Azure AD ist die Berichterstellung über Nutzung und Zuweisung vollständig integriert, sodass Administratoren Berichte über den Zuweisungszustand, Zuweisungsfehler und sogar die Nutzung leicht anfertigen können.

## <a name="complex-application-assignment-with-azure-ad"></a>Komplexe Anwendungszuweisungen mit Azure AD
Betrachten Sie eine Anwendung wie Salesforce. In vielen Organisationen wird Salesforce in erster Linie von den Marketing- und Vertriebsteams verwendet. Häufig haben Mitglieder des Marketingteams umfassende Berechtigungen für den Zugriff auf Salesforce, während Mitglieder des Vertriebsteams nur beschränkten Zugriff haben. In vielen Fällen hat ein großer Personenkreis von Information-Workern eingeschränkten Zugriff auf die Anwendung. Ausnahmen von dieser Regel kommen erschwerend hinzu. Häufig ist es Sache der Marketing- oder Vertriebsleitung, Benutzern Zugriff zu gewähren oder ihre Rollen unabhängig von allgemeinen Regeln zu ändern.

Mit Azure AD können Anwendungen wie Salesforce beispielsweise für einmaliges Anmelden (SSO) und automatisierte Bereitstellung vorkonfiguriert werden. Sobald die Anwendung konfiguriert ist, kann der Administrator die einmalige Aktion zum Erstellen und Zuweisen der entsprechenden Gruppen übernehmen. In diesem Beispiel kann ein Administrator die folgenden Zuweisungen vornehmen:

* [Dynamische Gruppen](../fundamentals/active-directory-groups-create-azure-portal.md) lassen sich durch Verwendung von Attributen wie Abteilung oder Rolle derart definieren, dass sie alle Mitglieder der Vertriebs- und Marketingteams abbilden:
  
  * Alle Mitglieder von Marketinggruppen würden in diesem Fall der Rolle „Marketing“ in Salesforce zugewiesen.
  * Alle Mitglieder von Vertriebsteamgruppen würden der Rolle „Vertrieb“ in Salesforce zugewiesen werden. In einem Verfeinerungsschritt könnten mehrere Gruppen verwendet werden, die regionale, anderen Salesforce-Gruppen zugewiesene Vertriebsteams darstellen.
* Zum Aktivieren des Ausnahmemechanismus könnte für jede Rolle eine Self-Service-Gruppe erstellt werden. Die Gruppe „Salesforce Marketingausnahme“ kann beispielsweise als Self-Service-Gruppe erstellt werden. Diese Gruppe kann der Salesforce-Marketingrolle zugewiesen und das Leadershipteam der Marketingabteilung als deren Besitzer festgelegt werden. Mitglieder des Leadershipteams der Marketingabteilung könnten somit Benutzer hinzufügen oder entfernen, eine Beitrittsrichtlinie einrichten oder sogar einzelne Beitrittsanfragen genehmigen oder verweigern. Dieser Mechanismus wird auch durch ein für Information-Worker angemessenes Verfahren begünstigt, bei dem keine spezielle Schulung für Besitzer oder Mitglieder erforderlich ist.

In diesem Fall würde allen zugewiesenen Benutzern automatisch Salesforce bereitgestellt, und bei einem Wechsel in andere Gruppen würde ihre Rollenzuweisung in Salesforce aktualisiert. Die Benutzer könnten Salesforce über den Microsoft Zugriffsbereich für Anwendungen, Office-Webclients oder sogar durch Aufrufen der Salesforce-Anmeldeseite innerhalb der Organisation ermitteln und darauf zugreifen. Administratoren könnten in diesem Fall den Nutzungs- und Zuweisungszustand mithilfe von Azure AD-Berichten leicht anzeigen.

Administratoren können den [bedingten Zugriff von Azure AD](../active-directory-conditional-access-azure-portal.md) einsetzen, um Zugriffsrichtlinien für bestimmte Rollen festzulegen. In diesen Richtlinien kann enthalten sein, ob der Zugriff von außerhalb der Unternehmensumgebung erlaubt ist, und sogar, ob Multi-Factor Authentication oder bestimmte Geräteanforderungen für verschiedene Anwendungsfälle zur Anwendung kommen.

## <a name="next-steps"></a>Nächste Schritte
* [Schützen von Apps durch bedingten Zugriff](../active-directory-conditional-access-azure-portal.md)
* [Self-Service-Gruppenverwaltung/SSAA](../users-groups-roles/groups-self-service-management.md)
