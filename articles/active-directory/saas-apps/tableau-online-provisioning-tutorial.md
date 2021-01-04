---
title: 'Tutorial: Konfigurieren von Tableau Online für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Tableau Online konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359203"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Tableau Online für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die Sie in Tableau Online und Azure Active Directory (Azure AD) ausführen müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und Gruppen in Tableau Online zu konfigurieren.

> [!NOTE]
> Das Tutorial enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie über folgende Elemente verfügen:

*   Einen Azure AD-Mandanten.
*   Einen [Tableau Online-Mandanten](https://www.tableau.com/).
*   Ein Benutzerkonto in Tableau Online mit Administratorberechtigungen.

> [!NOTE]
> Die Azure AD-Bereitstellungsintegration basiert auf der [Tableau Online-REST-API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Diese API steht Tableau Online-Entwicklern zur Verfügung.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Hinzufügen von Tableau Online aus dem Azure Marketplace
Bevor Sie Tableau Online für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, fügen Sie Tableau Online aus dem Azure Marketplace der Liste mit den verwalteten SaaS-Anwendungen hinzu.

Führen Sie zum Hinzufügen von Tableau Online aus dem Azure Marketplace die folgenden Schritte aus.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory** aus.

    ![Azure Active Directory-Symbol](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Tableau Online** ein, und wählen Sie **Tableau Online** aus dem Ergebnisbereich aus. Wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Tableau Online in der Ergebnisliste](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Zuweisen von Benutzern zu Tableau Online

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Zusammenhang mit der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Tableau Online benötigen. Zum Zuweisen dieser Benutzer oder Gruppen zu Tableau Online befolgen Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Tableau Online

*   Wir empfehlen, zuerst nur einen einzelnen Azure AD-Benutzer zu Tableau Online zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können Sie dann weitere Benutzer oder Gruppen zuweisen.

*   Beim Zuweisen eines Benutzers zu Tableau Online müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurieren der automatischen Benutzerbereitstellung in Tableau Online

Dieser Abschnitt führt Sie durch die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts. Verwenden Sie diese zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Tableau Online anhand von Benutzer- oder Gruppenzuweisungen in Azure AD.

> [!TIP]
> Sie können auch SAML-basiertes einmaliges Anmelden für Tableau Online aktivieren. Befolgen Sie hierfür die Anleitung im [Tutorial für einmaliges Anmelden mit Tableau Online](tableauonline-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Tableau Online in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** > **Tableau Online** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Tableau Online** aus.

    ![Tableau Online-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Bereitstellung von Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Tableau Online-Bereitstellungsmodus](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Geben Sie unter dem Abschnitt **Administratoranmeldeinformationen** die Domäne, den Administratorbenutzernamen, das Administratorkennwort und die Inhalts-URL Ihres Tableau Online-Kontos ein:

   * Geben Sie im Feld **Domäne** die Unterdomäne basierend auf Schritt 6 ein.

   * Geben Sie im Feld **Administratorbenutzername** den Benutzernamen des Administratorkontos in Ihrem Tableau Online-Mandanten ein. z. B. admin@contoso.com.

   * Geben Sie im Feld **Administratorkennwort** das Kennwort des Administratorkontos für den Administratorbenutzernamen ein.

   * Geben Sie im Feld **Inhalts-URL** die Unterdomäne basierend auf Schritt 6 ein.

6. Nachdem Sie sich bei Ihrem Administratorkonto für Tableau Online angemeldet haben, können Sie die Werte für **Domäne** und **Inhalts-URL** aus der URL der Administratorseite abrufen.

    * Die **Domäne** für Ihr Tableau Online-Konto kann aus diesem Teil der URL kopiert werden:

        ![Tableau Online-Domäne](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Die **Inhalts-URL** für Ihr Tableau Online-Konto kann aus diesem Abschnitt kopiert werden. Es handelt sich um einen Wert, der beim Kontosetup definiert wird. In diesem Beispiel ist der Wert „contoso“:

        ![Tableau Online-Inhalts-URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Ihre **Domäne** kann sich von der hier dargestellten Domäne unterscheiden.

7. Nachdem Sie die in Schritt 5 gezeigten Felder ausgefüllt haben, wählen Sie **Testverbindung** aus, um sicherzustellen, dass Azure AD eine Verbindung mit Tableau Online herstellen kann. Wenn die Verbindung nicht möglich ist, sollten Sie sicherstellen, dass Ihr Tableau Online-Konto über Administratorberechtigungen verfügt, und den Vorgang wiederholen.

    ![Verbindung testen in Tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Tableau Online-Benachrichtigungs-E-Mail](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Tableau Online synchronisieren** aus.

    ![Tableau Online-Benutzersynchronisierung](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Tableau Online synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Tableau Online für Updatevorgänge verwendet werden. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Übereinstimmende Benutzerattribute in Tableau Online](./media/tableau-online-provisioning-tutorial/attribute.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Tableau** (Azure Active Directory-Gruppen mit Tableau synchronisieren) aus.

    ![Tableau Online-Gruppensynchronisierung](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Tableau Online synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Tableau Online für Updatevorgänge verwendet werden. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Übereinstimmende Gruppenattribute in Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, befolgen Sie die Anleitung unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für Tableau Online One zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Tableau Online-Bereitstellungsstatus](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definieren Sie die Benutzer oder Gruppen, die in Tableau Online bereitgestellt werden sollen. Wählen Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Tableau Online-Bereich](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern in Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Dadurch wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die folgenden Synchronisierungen. Diese erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und über Links zum Bereitstellungsaktivitätsbericht navigieren. Der Bericht beschreibt alle vom Azure AD-Bereitstellungsdienst in Tableau Online ausgeführten Aktionen.

Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Änderungsprotokoll
* 30.09.2020: Unterstützung für das Attribut „authSetting“ für Benutzer hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png