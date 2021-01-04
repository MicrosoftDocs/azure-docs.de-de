---
title: 'Tutorial: Konfigurieren von GoToMeeting für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und GoToMeeting konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358898"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von GoToMeeting für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in GoToMeeting und Azure AD ausführen müssen, um Benutzerkonten von Azure AD an GoToMeeting automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein GoToMeeting-Abonnement, für das einmaliges Anmelden aktiviert ist
*   Ein Benutzerkonto in GoToMeeting mit Teamadministratorberechtigung

## <a name="assigning-users-to-gotomeeting"></a>Zuweisen von Benutzern zu GoToMeeting

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre GoToMeeting-App benötigen. Anschließend können Sie diese Benutzer Ihrer GoToMeeting-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Wichtige Tipps zum Zuweisen von Benutzern zu GoToMeeting

*   Es wird empfohlen, GoToMeeting einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie GoToMeeting einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

In diesem Abschnitt werden das Verbinden von Azure AD mit der GoToMeeting-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in GoToMeeting basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für GoToMeeting aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-account-provisioning"></a>So konfigurieren Sie die automatische Bereitstellung von Benutzerkonten:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie GoToMeeting bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer GoToMeeting-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **GoToMeeting**. Wählen Sie „GoToMeeting“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre GoToMeeting-Instanz und dann die Registerkarte **Bereitstellung** aus.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Screenshot der Registerkarte „Bereitstellung“ für GoToMeeting im Azure-Portal. Der Bereitstellungsmodus ist auf „Automatisch“ festgelegt, und „Administratorbenutzername“, „Kennwort“ und „Verbindung testen“ sind hervorgehoben.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Führen Sie im Abschnitt „Administratoranmeldeinformationen“ die folgenden Schritte aus:
   
    a. Geben Sie im Textfeld **GoToMeeting-Administratorbenutzername** den Benutzernamen eines Administrators ein.

    b. Geben Sie im Textfeld **GoToMeeting-Administratorkennwort** das Kennwort des Administrators ein.

1. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer GoToMeeting-App herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr GoToMeeting-Konto über Teamadministratorberechtigungen verfügt, und versuchen Sie den Schritt **Administratoranmeldeinformationen** erneut.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit GoToMeeting synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit GoToMeeting synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in GoToMeeting für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein** , um den Azure AD-Bereitstellungsdienst für GoToMeeting zu aktivieren.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und Gruppen gestartet, die GoToMeeting im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer GoToMeeting-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](./citrix-gotomeeting-tutorial.md)