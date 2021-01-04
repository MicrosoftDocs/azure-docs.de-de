---
title: 'Tutorial: Konfigurieren von Zendesk für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zendesk konfigurieren.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359713"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zendesk für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die Sie in Zendesk und Azure Active Directory (Azure AD) ausführen müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und Gruppen in Zendesk zu konfigurieren.

> [!NOTE]
> Das Tutorial enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie über folgende Elemente verfügen:

* Einen Azure AD-Mandanten.
* Einen Zendesk-Mandanten, für den mindestens der Professional-Tarif aktiviert ist
* Ein Benutzerkonto in Zendesk mit Administratorberechtigungen.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Hinzufügen von Zendesk aus dem Azure Marketplace

Bevor Sie Zendesk für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, fügen Sie Zendesk aus dem Azure Marketplace der Liste mit den verwalteten SaaS-Anwendungen hinzu.

Führen Sie zum Hinzufügen von Zendesk aus dem Azure Marketplace die folgenden Schritte aus.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory** aus.

    ![Azure Active Directory-Symbol](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Zendesk** ein, und wählen Sie **Zendesk** im Ergebnisbereich aus. Wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Zendesk in der Ergebnisliste](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Zuweisen von Benutzern zu Zendesk

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Zusammenhang mit der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Zendesk benötigen. Zum Zuweisen dieser Benutzer oder Gruppen zu Zendesk befolgen Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zendesk

* Zendesk-Rollen werden derzeit automatisch und dynamisch auf der Benutzeroberfläche des Azure-Portals aufgefüllt. Stellen Sie vor dem Zuweisen von Zendesk-Rollen zu Benutzern sicher, dass eine erste Synchronisierung mit Zendesk ausgeführt wurde, um die aktuellen Rollen Ihres Zendesk-Mandanten abzurufen.

* Wir empfehlen, zuerst nur einen einzelnen Azure AD-Benutzer zu Zendesk zuzuweisen, um die Ausgangskonfiguration der automatischen Benutzerbereitstellung zu testen. Später können Sie dann weitere Benutzer oder Gruppen zuweisen, nachdem die Tests erfolgreich waren.

* Beim Zuweisen eines Benutzers zu Zendesk müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle (sofern verfügbar) auswählen. Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Konfigurieren der automatischen Benutzerbereitstellung in Zendesk 

Dieser Abschnitt führt Sie durch die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts. Verwenden Sie diese zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Zendesk anhand von Benutzer- oder Gruppenzuweisungen in Azure AD.

> [!TIP]
> Sie können auch SAML-basiertes einmaliges Anmelden für Zendesk aktivieren. Befolgen Sie hierfür die Anleitung im [Tutorial für einmaliges Anmelden mit Zendesk](zendesk-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Zendesk in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** > **Zendesk** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Zendesk** aus.

    ![Zendesk-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Zendesk-Bereitstellung](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Zendesk-Bereitstellungsmodus](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** den Administratorbenutzernamen, das geheime Token und die Domäne Ihres Zendesk-Kontos ein. Beispiele für diese Werte:

   * Geben Sie im Feld **Administratorbenutzername** den Benutzernamen des Administratorkontos in Ihrem Zendesk-Mandanten ein. z. B. admin@contoso.com.

   * Geben Sie im Feld **Geheimes Token** das geheime Token wie in Schritt 6 beschrieben ein.

   * Geben Sie im Feld **Domäne** die Unterdomäne Ihres Zendesk-Mandanten ein. Beispiel: Für ein Konto mit der Mandanten-URL `https://my-tenant.zendesk.com` lautet Ihre Unterdomäne **my-tenant**.

6. Das geheime Token für Ihr Zendesk-Konto finden Sie unter **Administrator** > **API** > **Einstellungen**. Vergewissern Sie sich, dass **Tokenzugriff** auf **Aktiviert** festgelegt ist.

    ![Zendesk-Administratoreinstellungen](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Geheimes Token für Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Nachdem Sie die in Schritt 5 gezeigten Felder ausgefüllt haben, wählen Sie **Verbindung testen** aus, um sicherzustellen, dass Azure AD eine Verbindung mit Zendesk herstellen kann. Falls der Verbindungsaufbau fehlschlägt, stellen Sie sicher, dass Ihr Zendesk-Konto über Administratorberechtigungen verfügt, und versuchen Sie es noch einmal.

    ![Verbindung testen in Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Zendesk-Benachrichtigungs-E-Mail](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zendesk** (Azure Active Directory-Benutzer mit Zendesk synchronisieren) aus.

    ![Zendesk-Benutzersynchronisierung](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Zendesk synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Zendesk für Updatevorgänge verwendet werden. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Übereinstimmende Benutzerattribute in Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Zendesk synchronisieren** aus.

    ![Zendesk-Gruppensynchronisierung](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Zendesk synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Gruppen in Zendesk für Updatevorgänge verwendet. Um alle Änderungen zu speichern, wählen Sie **Speichern** aus.

    ![Übereinstimmende Gruppenattribute in Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, befolgen Sie die Anleitung unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Um den Azure AD-Bereitstellungsdienst für Zendesk zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Zendesk-Bereitstellungsstatus](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definieren Sie die Benutzer oder Gruppen, die in Zendesk bereitgestellt werden sollen. Wählen Sie im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Zendesk-Bereich](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern in Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Dadurch wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die folgenden Synchronisierungen. Diese erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. 

Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und über Links zum Bereitstellungsaktivitätsbericht navigieren. Der Bericht beschreibt alle vom Azure AD-Bereitstellungsdienst in Zendesk ausgeführten Aktionen.

Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

* Zendesk unterstützt die Verwendung von Gruppen für Benutzer mit ausschließlichen **Agent** -Rollen. Weitere Informationen finden Sie in der [Zendesk-Dokumentation](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Wenn einem Benutzer oder einer Gruppe eine benutzerdefinierte Rolle zugewiesen wird, weist der Azure AD-Dienst für die automatische Benutzerbereitstellung auch die Standardrolle **Agent** zu. Nur Agents kann eine benutzerdefinierte Rolle zugewiesen werden. Weitere Informationen finden Sie in der [Dokumentation zur Zendesk-API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
