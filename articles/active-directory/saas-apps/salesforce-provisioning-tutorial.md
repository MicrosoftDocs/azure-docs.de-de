---
title: 'Tutorial: Konfigurieren von Salesforce für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Salesforce konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 712cc5ce62225987f8cc3ea13b5e4fd10a7d5eaf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515788"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Salesforce für die automatische Benutzerbereitstellung

Dieses Tutorial zeigt Ihnen die Schritte, die Sie in Salesforce und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in Salesforce automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure Active Directory-Mandanten
* Einen Salesforce.com-Mandanten

> [!IMPORTANT]
> Bei Verwendung eines Salesforce.com-Testkontos können Sie die automatisierte Benutzerbereitstellung nicht konfigurieren. Bei Testkonten ist der erforderliche API-Zugriff erst nach dem Erwerb aktiviert. Sie können diese Einschränkung umgehen, indem Sie für dieses Tutorial ein [kostenloses Entwicklerkonto](https://developer.salesforce.com/signup) verwenden.

Wenn Sie eine Salesforce Sandbox-Umgebung verwenden, rufen Sie das [Tutorial: Azure Active Directory-Integration in Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879)auf.

## <a name="assigning-users-to-salesforce"></a>Zuweisen von Benutzern zu Salesforce

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer oder Gruppen in Azure AD Zugriff auf Ihre Salesforce-App benötigen. Nach dieser Entscheidung können Sie die entsprechenden Benutzer und Gruppen gemäß der Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) Ihrer Salesforce-App zuweisen.

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Salesforce

* Es wird empfohlen, Salesforce einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Wenn Sie einen Benutzer zu Salesforce zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

    > [!NOTE]
    > Diese App importiert als Teil des Bereitstellungsprozesses Profile aus Salesforce, die der Kunde beim Zuweisen von Benutzern in Azure AD auswählen kann. Beachten Sie, dass die aus Salesforce importierten Profile in Azure AD als Rollen angezeigt werden.

## <a name="enable-automated-user-provisioning"></a>Aktivieren der automatisierten Benutzerbereitstellung

Dieser Abschnitt führt Sie durch das Herstellen einer Verbindung von Azure AD mit der Salesforce-API zur Bereitstellung von Benutzerkonten sowie durch das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in Salesforce basierend auf der Benutzer- und Gruppenzuweisung in Azure AD.

> [!Tip]
> Sie können auch das SAML-basierte einmalige Anmelden für Salesforce aktivieren. Befolgen Sie dazu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für Salesforce aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Wenn Sie Salesforce bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer Salesforce-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **Salesforce**. Wählen Sie „Salesforce“ in den Suchergebnissen aus, und fügen Sie die Anwendung Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre Salesforce-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Bereitstellung](./media/salesforce-provisioning-tutorial/provisioning.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:

    a. Geben Sie im Textfeld **Administratorbenutzername** den Namen eines Salesforce-Kontos ein, dem das Profil **Systemadministrator** in „Salesforce.com“ zugewiesen ist.

    b. Geben Sie im Feld **Administratorkennwort** das Kennwort für das Konto ein.

6. Um Ihr Salesforce-Sicherheitstoken abzurufen, öffnen Sie eine neue Registerkarte, und melden Sie sich mit dem gleichen Salesforce-Administratorkonto an. Klicken Sie in der rechten oberen Ecke der Seite auf Ihren Namen und dann auf **Meine Einstellungen**.

    ![Automatische Benutzerbereitstellung aktivieren](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Automatische Benutzerbereitstellung aktivieren")

7. Klicken Sie im linken Navigationsbereich auf **Meine persönlichen Informationen**, um den entsprechenden Abschnitt zu erweitern, und dann auf **Mein Sicherheitstoken zurücksetzen**.
  
    ![Automatische Benutzerbereitstellung aktivieren](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Automatische Benutzerbereitstellung aktivieren")

8. Klicken Sie auf der Seite **Sicherheitstoken zurücksetzen** auf die Schaltfläche **Sicherheitstoken zurücksetzen**.

    ![Automatische Benutzerbereitstellung aktivieren](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Automatische Benutzerbereitstellung aktivieren")

9. Überprüfen Sie den E-Mail-Posteingang dieses Administratorkontos. Achten Sie auf eine E-Mail von Salesforce.com, die das neue Sicherheitstoken enthält.

10. Kopieren Sie das Token, wechseln Sie zu Ihrem Azure AD-Fenster, und fügen Sie es in das Feld **Geheimes Token** ein.

11. Die **Mandanten-URL** muss eingegeben werden, wenn sich die Instanz von Salesforce in der Salesforce Government Cloud befindet. Andernfalls ist die Angabe optional. Geben Sie die Mandanten-URL im Format „https://\<Ihre-Instanz\>.my.salesforce.com“ ein. Ersetzen Sie dabei \<Ihre-Instanz\> durch den Namen Ihrer Salesforce-Instanz.

12. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer Salesforce-App herstellen kann.

13. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das unten gezeigte Kontrollkästchen.

14. Klicken Sie auf **Speichern**.  

15. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit Salesforce synchronisieren** aus.

16. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Salesforce synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Salesforce für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

17. Um den Azure AD-Bereitstellungsdienst für Salesforce zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

18. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die Salesforce im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Beachten Sie, dass die Erstsynchronisierung länger dauert als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer Salesforce-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
