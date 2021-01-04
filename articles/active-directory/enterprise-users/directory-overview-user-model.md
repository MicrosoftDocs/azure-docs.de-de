---
title: Benutzer, Gruppen, Lizenzierung und Rollen in Azure Active Directory
description: Enthält eine Beschreibung der Beziehung zwischen zugewiesenen Benutzern und Lizenzen, Administratorrollen und Gruppenmitgliedschaft in Azure Active Directory.
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: krbain
ms.date: 12/02/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce4330b72ef7e1440689e0ec4af9009f551553a3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547269"
---
# <a name="what-is-enterprise-user-management"></a>Was beinhaltet die Verwaltung von Unternehmensbenutzern?

Dieser Artikel enthält für Azure AD-Administratoren eine Einführung in die Beziehung zwischen den wichtigsten Aufgaben der [Identitätsverwaltung](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) für Benutzer in Bezug auf deren Gruppen, Lizenzen, bereitgestellte Unternehmens-Apps und Administratorrollen. Wenn Ihre Organisation wächst, können Sie Azure AD-Gruppen und -Administratorrollen für folgende Zwecke nutzen:

* Zuweisen von Lizenzen zu Gruppen anstelle von einzelnen Benutzern
* Delegieren von Berechtigungen zum Verteilen der Azure AD-Verwaltungsarbeit auf Rollen mit weniger Rechten
* Zuweisen des Zugriffs auf Unternehmens-Apps zu Gruppen

## <a name="assign-users-to-groups"></a>Zuweisen von Benutzern zu Gruppen

Sie können Gruppen in Azure AD verwenden, um Lizenzen einer großen Zahl von Benutzern zuzuweisen oder den Benutzerzugriff auf bereitgestellte Unternehmens-Apps zuzuweisen. Sie können Gruppen verwenden, um alle Administratorrollen mit Ausnahme der Rolle „Globaler Administrator“ in Azure AD zuzuweisen, oder Sie können den Zugriff auf externe Ressourcen gewähren, z. B. SaaS-Anwendungen oder SharePoint-Websites.

Wenn Sie für zusätzliche Flexibilität sorgen und den Aufwand für die Verwaltung der Gruppenmitgliedschaft reduzieren möchten, können Sie in Azure AD [dynamische Gruppen](groups-create-rule.md) nutzen, um die Gruppenmitgliedschaft automatisch zu verlängern und zu begrenzen. Sie benötigen für jeden eindeutigen Benutzer, der Mitglied mindestens einer dynamischen Gruppe ist, eine Azure AD Premium P1-Lizenz.

## <a name="assign-licenses-to-groups"></a>Zuweisen von Lizenzen zu Gruppen

Das einzelne Zuweisen und Entfernen von Lizenzen für Benutzer kann relativ viel Zeit und Aufwand erfordern. Wenn Sie stattdessen [Gruppen Lizenzen zuweisen](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), können Sie Ihre umfangreiche Lizenzverwaltung vereinfachen.

Wenn Benutzer in Azure AD einer lizenzierten Gruppe beitreten, werden ihnen automatisch die richtigen Lizenzen zugewiesen. Wenn Benutzer die Gruppe verlassen, entfernt Azure AD ihre Lizenzzuweisungen. Ohne Azure AD-Gruppen müssen Sie ein PowerShell-Skript schreiben oder eine Graph-API verwenden, um Lizenzen für Benutzer, die der Organisation beitreten oder diese verlassen, per Massenvorgang hinzuzufügen oder zu entfernen.

Wenn nicht genügend verfügbare Lizenzen vorhanden sind oder ein Problem auftritt (z.B. Dienstpläne, die nicht gleichzeitig zugewiesen werden können), können Sie den Status der Lizenzprobleme für die Gruppe im Azure-Portal anzeigen.

>[!NOTE]
>Das Feature für die gruppenbasierte Lizenzierung befindet sich derzeit in der öffentlichen Vorschauphase. Während der Vorschauphase ist die Funktion in jedem Azure Active Directory-Lizenzplan (Azure AD) bzw. jeder Testversion verfügbar.

## <a name="delegate-administrator-roles"></a>Delegieren von Administratorrollen

Viele große Organisationen wünschen sich Optionen für ihre Benutzer, mit denen ausreichende Berechtigungen für ihre Arbeitsaufgaben gewährt werden können, ohne dass die umfassende Rolle „Globaler Administrator“ beispielsweise Benutzern zugewiesen werden muss, die Anwendungen registrieren müssen. Hier ist ein Beispiel für neue Azure AD-Administratorrollen angegeben, mit denen Sie die Aufgaben der Anwendungsverwaltung präziser verteilen können:

 Rollenname | Zusammenfassung der Berechtigungen
 --------- | -------------------
 **Anwendungsadministrator** | Kann Unternehmensanwendungen und Anwendungsregistrierungen hinzufügen und verwalten und Anwendungsproxyeinstellungen konfigurieren. Anwendungsadministratoren können Richtlinien und Geräte für bedingten Zugriff anzeigen, aber nicht verwalten.
 **Cloudanwendungsadministrator** | Kann Unternehmensanwendungen und Registrierungen für Unternehmens-Apps hinzufügen und verwalten. Diese Rolle verfügt über alle Berechtigungen des Anwendungsadministrators, mit Ausnahme der Verwaltung von Anwendungsproxyeinstellungen.
**Anwendungsentwickler** | Kann Anwendungsregistrierungen hinzufügen und aktualisieren, aber keine Unternehmensanwendungen verwalten oder einen Anwendungsproxy konfigurieren.

Neue Azure AD-Administratorrollen werden derzeit hinzugefügt. Im Azure-Portal oder in der [Referenz zu den Berechtigungen der Administratorrolle ](../roles/permissions-reference.md) können Sie sich die derzeit verfügbaren Rollen ansehen.

## <a name="assign-app-access"></a>Zuweisen des App-Zugriffs

Sie können Azure AD nutzen, um Gruppenzugriff auf die [Unternehmens-Apps zuzuweisen, die in Ihrer Azure AD-Organisation bereitgestellt werden](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context). Wenn Sie dynamische Gruppen mit der Gruppenzuweisung zu Apps kombinieren, können Sie Ihre Zuweisungen des Benutzerzugriffs auf Apps automatisieren, wenn Ihre Organisation wächst. Sie benötigen eine Lizenz vom Typ Azure Active Directory Premium P1 oder Premium P2, um den Zugriff auf Unternehmens-Apps zuweisen zu können.

Mit Azure AD können Sie auch die Daten präzise steuern, die zwischen der App und den Gruppen ausgetauscht werden, denen Sie die Zugriffsberechtigung zuweisen. Öffnen Sie in [Unternehmensanwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) eine App, und wählen Sie **Bereitstellung**, um Folgendes durchzuführen:

* Einrichten der automatischen Bereitstellung für Apps, die dies unterstützen
* Bereitstellen von Anmeldeinformationen zum Herstellen einer Verbindung mit der Benutzerverwaltungs-API der App
* Einrichten der Zuordnungen, mit denen gesteuert wird, welche Benutzerattribute zwischen Azure AD und der App fließen, wenn Benutzerkonten bereitgestellt oder aktualisiert werden
* Starten und Beenden des Azure AD-Bereitstellungsdiensts für eine App, Löschen des Bereitstellungscaches oder Neustarten des Diensts
* Anzeigen des **Bereitstellungsaktivitätsberichts** mit einem Protokoll aller Benutzer und Gruppen, die zwischen Azure AD und der App erstellt, aktualisiert und entfernt wurden, und des **Fehlerberichts zur Bereitstellung** mit ausführlicheren Fehlermeldungen

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie noch nicht lange als Azure AD-Administrator arbeiten, hilft Ihnen die [Dokumentation mit grundlegenden Informationen zu Azure Active Directory](../fundamentals/index.yml) weiter.

Sie können auch mit dem [Erstellen von Gruppen](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [Zuweisen von Lizenzen](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [Zuweisen des App-Zugriffs](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) oder [Zuweisen von Administratorrollen](../roles/permissions-reference.md) beginnen.