---
title: 'Tutorial: Konfigurieren von NetSuite OneWorld für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und NetSuite OneWorld konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: a5b995fd273fcfa231e101bc77b11d268be728fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359134"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von NetSuite für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in NetSuite OneWorld und Azure AD ausführen müssen, um Benutzerkonten von Azure AD automatisch in NetSuite bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

> [!NOTE]
> Bei dieser Integration erfolgt die Authentifizierung derzeit mithilfe der Standardauthentifizierung (Benutzername und Kennwort). NetSuite implementiert eine Anforderung für die mehrstufige Authentifizierung, die Kunden daran hindert, diese Integration zu verwenden, sofern sie nicht durch eine Ausnahme von dieser Anforderung befreit sind. Wir arbeiten mit NetSuite zusammen, um diese Integration auf eine neuere Authentifizierungsmethode zu aktualisieren, damit Kunden ohne eine Ausnahme sie wieder verwenden können. Wir aktualisieren dieses Dokument mit einer ETA, sobald eine verfügbar ist.

Empfohlene Maßnahme: Warten Sie, bis wir ein Update des Authentifizierungsverhaltens für diese Integration veröffentlichen, oder wenden Sie sich an den NetSuite-Support, um eine Ausnahme von der Anforderung für die mehrstufige Authentifizierung zu erhalten.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein NetSuite OneWorld-Abonnement. Beachten Sie, dass die automatische Benutzerbereitstellung derzeit nur mit NetSuite OneWorld unterstützt wird.
*   Ein Benutzerkonto in NetSuite mit Administratorberechtigungen
*   Die Integration in Azure AD erfordert eine Ausnahme für die zweistufige Authentifizierung. Wenden Sie sich an das NetSuite-Supportteam, um diese Ausnahme anzufordern.

## <a name="assigning-users-to-netsuite-oneworld"></a>Zuweisen von Benutzern zu NetSuite OneWorld

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre NetSuite-App benötigen. Anschließend können Sie Ihrer NetSuite-App diese Benutzer zuweisen, indem Sie den folgenden Anweisungen folgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Wichtige Tipps für das Zuweisen von Benutzern zu NetSuite OneWorld

*   Es wird empfohlen, NetSuite einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

*   Wenn Sie NetSuite einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

## <a name="enable-user-provisioning"></a>Aktivieren der Benutzerbereitstellung

In diesem Abschnitt werden die Schritte zum Herstellen einer Verbindung von Azure AD mit der NetSuite-API zur Bereitstellung von Benutzerkonten und die Schritte zum Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in NetSuite basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!TIP] 
> Sie können auch das SAML-basierte einmalige Anmelden für NetSuite aktivieren. Folgen Sie dazu den Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-user-account-provisioning"></a>Gehen Sie folgt vor, um die Bereitstellung von Benutzerkonten zu konfigurieren:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für NetSuite aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie NetSuite bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer NetSuite-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **NetSuite**. Wählen Sie in den Suchergebnissen den Eintrag „NetSuite“ aus, und fügen Sie Ihrer Anwendungsliste die Anwendung hinzu.

1. Wählen Sie Ihre NetSuite-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Screenshot der Seite „NetSuite –Bereitstellung“ mit dem Bereitstellungsmodus „Automatisch“ und weiteren Werten, die Sie festlegen können.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:
   
    a. Geben Sie im Textfeld **Administratorbenutzername** den Namen eines NetSuite-Kontos ein, dem das Profil **Systemadministrator** auf „NetSuite.com“ zugewiesen ist.
   
    b. Geben Sie im Textfeld **Administratorkennwort** das Kennwort für dieses Konto ein.
      
1. Klicken Sie im Azure-Portal auf **Verbindung testen** , um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer NetSuite-App herstellen kann.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit NetSuite synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit NetSuite synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in NetSuite für Aktualisierungsvorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für NetSuite zu aktivieren, ändern Sie im Abschnitt „Einstellungen“ den **Bereitstellungsstatus** in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die NetSuite im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Beachten Sie, dass die Erstsynchronisierung länger dauert als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Bereitstellungsaktivitätsprotokollen folgen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst für Ihre NetSuite-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](netsuite-tutorial.md)
