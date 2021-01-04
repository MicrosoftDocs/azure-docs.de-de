---
title: 'Tutorial: Konfigurieren von Jive für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier werden die Schritte beschrieben, die Sie in Jive und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Jive automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ebee5d986007e07d497056620f0cfc437b2da4d1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356398"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Jive für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Jive und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Jive automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein Jive-Abonnement, für das einmaliges Anmelden aktiviert ist
*   Ein Benutzerkonto in Jive mit Teamadministratorberechtigungen

## <a name="assigning-users-to-jive"></a>Zuweisen von Benutzern zu Jive

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre Jive-App benötigen. Anschließend können Sie diese Benutzer Ihrer Jive-App zuweisen, indem Sie die folgenden Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-jive"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Jive

*   Es wird empfohlen, Jive einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie Jive einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-user-provisioning"></a>Aktivieren der Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der Jive-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Jive basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Jive aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-user-account-provisioning"></a>Gehen Sie folgt vor, um die Bereitstellung von Benutzerkonten zu konfigurieren:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Jive aktivieren.
Im Rahmen dieses Verfahrens werden Sie zur Angabe eines Benutzersicherheitstokens aufgefordert, das Sie von „Jive.com“ anfordern müssen.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie Jive bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Jive-Instanz. Wählen Sie andernfalls **Hinzufügen** , und suchen Sie im Anwendungskatalog nach **Jive**. Wählen Sie Jive in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre Jive-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Screenshot der Seite für die Jive-Bereitstellung mit dem Bereitstellungsmodus „Automatisch“ und weiteren Werten, die Sie festlegen können.](./media/jive-provisioning-tutorial/provisioning.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:
   
    a. Geben Sie im Textfeld für den **Jive-Admin-Benutzernamen** den Namen eines Jive-Kontos ein, dem das Profil **Systemadministrator** auf „Jive.com“ zugewiesen ist.
   
    b. Geben Sie im Feld **Jive-Administratorkennwort** das Kennwort für das Konto ein.
   
    c. Geben Sie im Textfeld **Jive-Mandanten-URL** die URL des Jive-Mandanten ein.
      
      > [!NOTE]
      > Die Jive-Mandanten-URL ist die URL, die von Ihrer Organisation zum Anmelden bei Jive verwendet wird.  
      > In der Regel weist die URL folgendes Format auf: **www.\<organization\>.jive.com**.          

1. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Jive-App herstellen kann.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das unten gezeigte Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Jive synchronisieren**.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Jive synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Jive für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für Jive zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Jive im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer Jive-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](jive-tutorial.md)