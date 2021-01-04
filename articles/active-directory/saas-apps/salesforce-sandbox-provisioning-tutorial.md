---
title: 'Tutorial: Konfigurieren von Salesforce Sandbox für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, welche Schritte in Salesforce Sandbox und Azure AD ausgeführt werden müssen, um Benutzerkonten von Azure AD automatisch in Salesforce Sandbox bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94352607"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Salesforce Sandbox für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Salesforce Sandbox und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Salesforce Sandbox automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Einen gültigen Mandanten für Salesforce Sandbox for Work oder Salesforce Sandbox for Education. Sie können ein kostenloses Testkonto für einen der Dienste verwenden.
*   Ein Benutzerkonto in Salesforce Sandbox mit Teamadministratorberechtigung.

## <a name="assigning-users-to-salesforce-sandbox"></a>Zuweisen von Benutzern zu Salesforce Sandbox

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer oder Gruppen in Azure AD Zugriff auf Ihre Salesforce Sandbox-App benötigen. Nach dieser Entscheidung können Sie die entsprechenden Benutzer und Gruppen gemäß der Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) Ihrer Salesforce Sandbox-App zuweisen.

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Salesforce Sandbox

* Es wird empfohlen, Salesforce Sandbox einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Wenn Sie einen Benutzer zu Salesforce Sandbox zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

> [!NOTE]
> Diese App importiert als Teil des Bereitstellungsprozesses benutzerdefinierte Rollen aus Salesforce Sandbox, die der Kunde beim Zuweisen von Benutzern auswählen kann.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der Salesforce Sandbox-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Salesforce Sandbox basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

>[!Tip]
>Sie können auch das SAML-basierte einmalige Anmelden für Salesforce Sandbox aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Salesforce Sandbox aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie Salesforce Sandbox bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Salesforce Sandbox-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **Salesforce Sandbox**. Wählen Sie „Salesforce Sandbox“ in den Suchergebnissen aus, und fügen Sie die Anwendung Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre Salesforce Sandbox-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Salesforce-Seite für die Sandboxbereitstellung mit dem Bereitstellungsmodus „Automatisch“ und weiteren Werten, die Sie festlegen können](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:
   
    a. Geben Sie in das Textfeld **Administratorbenutzername** den Namen eines Salesforce Sandbox-Kontos ein, dem das Profil **Systemadministrator** in „Salesforce.com“ zugewiesen ist.
   
    b. Geben Sie im Textfeld **Administratorkennwort** das Kennwort für dieses Konto ein.

1. Um Ihr Salesforce Sandbox-Sicherheitstoken abzurufen, öffnen Sie eine neue Registerkarte, und melden Sie sich mit dem gleichen Salesforce Sandbox-Administratorkonto an. Klicken Sie in der rechten oberen Ecke der Seite auf Ihren Namen und dann auf **Meine Einstellungen**.

     ![Screenshot: Ausgewählter Link „Einstellungen“](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Automatische Benutzerbereitstellung aktivieren")

1. Klicken Sie im linken Navigationsbereich auf **Meine persönlichen Informationen** , um den entsprechenden Abschnitt zu erweitern, und dann auf **Mein Sicherheitstoken zurücksetzen**.
  
    ![Screenshot: Ausgewählte Option „Reset My Security Token “ (Mein Sicherheitstoken zurücksetzen) unter „My Personal Information“ (Meine persönlichen Informationen)](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Automatische Benutzerbereitstellung aktivieren")

1. Klicken Sie auf der Seite **Sicherheitstoken zurücksetzen** auf die Schaltfläche **Sicherheitstoken zurücksetzen**.

    ![Screenshot: Seite „Rest Security Token“ (Sicherheitstoken zurücksetzen) mit erklärendem Text und der Option „Rest Security Token“ (Sicherheitstoken zurücksetzen)](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Automatische Benutzerbereitstellung aktivieren")

1. Überprüfen Sie den E-Mail-Posteingang dieses Administratorkontos. Achten Sie auf eine E-Mail von „Salesforce Sandbox.com“, die das neue Sicherheitstoken enthält.

1. Kopieren Sie das Token, wechseln Sie zu Ihrem Azure AD-Fenster, und fügen Sie es in das Feld **Geheimes Token** ein.

1. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Salesforce Sandbox-App herstellen kann.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

1. Klicken Sie auf **Speichern**.  
    
1.  Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Salesforce Sandbox synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Salesforce Sandbox synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Salesforce Sandbox für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für Salesforce Sandbox zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Salesforce Sandbox im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in der Salesforce Sandbox-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](./salesforce-sandbox-tutorial.md)