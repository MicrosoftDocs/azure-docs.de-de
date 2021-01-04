---
title: 'Tutorial: Konfigurieren von DocuSign für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und DocuSign konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: dc3f307a21b746981a84b1c0747c4b22c448541f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349904"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von DocuSign für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in DocuSign und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in DocuSign automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein DocuSign-Abonnement, für das einmaliges Anmelden aktiviert ist
*   Ein Benutzerkonto in DocuSign mit Teamadministratorberechtigung

## <a name="assigning-users-to-docusign"></a>Zuweisen von Benutzern zu DocuSign

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre DocuSign-App benötigen. Anschließend können Sie diese Benutzer Ihrer DocuSign-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Wichtige Tipps zum Zuweisen von Benutzern zu DocuSign

*   Es wird empfohlen, DocuSign einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer zugewiesen werden.

*   Wenn Sie DocuSign einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

> [!NOTE]
> Azure AD unterstützt nicht die Gruppenbereitstellung mit der Docusign-Anwendung, es können stattdessen nur Benutzer bereitgestellt werden.

## <a name="enable-user-provisioning"></a>Aktivieren der Benutzerbereitstellung

In diesem Abschnitt wird das Herstellen einer Verbindung von Azure AD mit der DocuSign-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in DocuSign basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!Tip]
> Sie können auch das SAML-basierte einmalige Anmelden für DocuSign aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-user-account-provisioning"></a>Gehen Sie folgt vor, um die Bereitstellung von Benutzerkonten zu konfigurieren:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für DocuSign aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie DocuSign bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer DocuSign-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **DocuSign**. Wählen Sie „DocuSign“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre DocuSign-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Screenshot der Registerkarte „Bereitstellung“ für DocuSign im Azure-Portal. Der Bereitstellungsmodus ist auf „Automatisch“ festgelegt, und „Administratorbenutzername“, „Kennwort“ und „Verbindung testen“ sind hervorgehoben.](./media/docusign-provisioning-tutorial/provisioning.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:
   
    a. Geben Sie im Textfeld **Administratorbenutzername** den Namen eines DocuSign-Kontos ein, dem das Profil **Systemadministrator** in „DocuSign.com“ zugewiesen ist.
   
    b. Geben Sie im Textfeld **Administratorkennwort** das Kennwort für dieses Konto ein.

> [!NOTE]
> Wenn sowohl SSO als auch die Benutzerbereitstellung eingerichtet ist, müssen die für die Bereitstellung verwendeten Autorisierungsanmeldeinformationen so konfiguriert werden, dass sie sowohl mit SSO als auch mit Benutzername bzw. Kennwort funktionieren.

1. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer DocuSign-App herstellen kann.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit DocuSign synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit DocuSign synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in DocuSign für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für DocuSign zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer gestartet, die DocuSign im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer DocuSign-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung
* Rollen oder Berechtigungsprofile für Benutzer können in DocuSign mithilfe eines Ausdrucks in den Attributzuordnungen bereitgestellt werden. Verwenden Sie dafür die Funktionen [switch](../app-provisioning/functions-for-customizing-application-data.md#switch) und [singleAppRoleAssignment](../app-provisioning/functions-for-customizing-application-data.md#singleapproleassignment). Ein Beispiel: Der unten stehende Ausdruck stellt die ID „8032066“ bereit, wenn einem Benutzer in Azure AD die Rolle „DS-Administrator zugewiesen wurde. Ein Berechtigungsprofil wird nicht bereitgestellt, wenn dem Benutzer keine Rolle auf Azure AD-Seite zugewiesen wurde. Die ID kann über das [Portal](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles) von DocuSign abgerufen werden.

Switch(SingleAppRoleAssignment([appRoleAssignments])," ", "8032066", "DS Admin")


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](docusign-tutorial.md)