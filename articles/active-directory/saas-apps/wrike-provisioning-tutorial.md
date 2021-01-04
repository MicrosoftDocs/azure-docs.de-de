---
title: 'Tutorial: Konfigurieren von Wrike für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Wrike konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95988092"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Wrike für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Wrike und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern oder Gruppen in Wrike zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Vorschaufunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Ein Wrike-Mandant](https://www.wrike.com/price/)
* Ein Benutzerkonto in Wrike mit Administratorrechten

## <a name="assign-users-to-wrike"></a>Zuweisen von Benutzern zu Wrike
Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Zusammenhang mit der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Wrike benötigen. Anschließend können Sie diese Benutzer oder Gruppen Wrike wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Wrike

* Wir empfehlen Ihnen, Wrike zuerst nur einem einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Wrike müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-wrike-for-provisioning"></a>Einrichten von Wrike für die Bereitstellung

Bevor Sie Wrike für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie die SCIM-Bereitstellung (System for Cross-Domain Identity Management, System für domänenübergreifendes Identitätsmanagement) in Wrike aktivieren.

1. Melden Sie sich bei Ihrer [Wrike-Verwaltungskonsole](https://www.Wrike.com/login/) an. Navigieren Sie zu Ihrer Mandanten-ID. Wählen Sie **Apps & Integrations** (Apps und Integrationen) aus.

    ![Apps und Integrationen](media/Wrike-provisioning-tutorial/admin.png)

2.  Wechseln Sie zu **Azure AD**, und wählen Sie es aus.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Wählen Sie SCIM aus. Kopieren Sie die **Basis-URL**.

    ![Basis-URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Wählen Sie **API** > **Azure SCIM** aus.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Ein Popup Fenster wird geöffnet. Geben Sie das gleiche Kennwort ein, das Sie zuvor erstellt haben, um ein Konto zu erstellen.

    ![Wrike – Token erstellen](media/Wrike-provisioning-tutorial/password.png)

6.  Kopieren Sie das **Secret Token** (Geheimes Token), und fügen Sie es in Azure AD ein. Wählen Sie **Save** (Speichern) aus, um die Bereitstellungseinrichtung unter Wrike abzuschließen.

    ![Dauerhaftes Zugriffstoken](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Hinzufügen von Wrike aus dem Katalog

Bevor Sie Wrike für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, fügen Sie Wrike aus dem Azure AD-Anwendungskatalog zur Liste mit den verwalteten SaaS-Anwendungen hinzu.

Führen Sie die folgenden Schritte aus, um Wrike aus dem Azure AD-Anwendungskatalog hinzuzufügen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Wrike**  ein, wählen Sie im Ergebnisbereich **Wrike**  aus, und wählen Sie dann die Schaltfläche **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Wrike in der Ergebnisliste](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Konfigurieren der automatischen Benutzerbereitstellung in Wrike 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Wrike auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Um das SAML-basierte einmalige Anmelden für Wrike zu aktivieren, befolgen Sie die Anweisungen unter [Tutorial: Einmaliges Anmelden in Wrike](wrike-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Wrike in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Alle Anwendungen](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Wrike** aus.

    ![Wrike-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Bereitstellungsmodus, festgelegt auf „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt „Administratoranmeldeinformationen“ die zuvor abgerufenen Werte für die **Basis-URL** und das **permanente Zugriffstoken** in die Felder **Mandanten-URL** und. **Geheimes Token** ein. Um sicherzustellen, dass Azure AD eine Verbindung mit Wrike herstellen kann, wählen Sie **Verbindung testen** aus. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Wrike-Konto über Administratorberechtigungen verfügt, und versuchen Sie es dann noch einmal.

    ![Mandanten-URL und -token](common/provisioning-testconnection-tenanturltoken.png)

7. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

8. Wählen Sie **Speichern** aus.

9. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Wrike synchronisieren**.

    ![Benutzerzuordnungen in Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Wrike synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Wrike für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Benutzerattribute in Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Um den Azure AD-Bereitstellungsdienst für Wrike zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

13. Legen Sie die Benutzer oder Gruppen fest, die in Wrike bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

14. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Unter [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) finden Sie weitere Informationen dazu, wie lange das Bereitstellen von Benutzern oder Gruppen dauert.

Im Abschnitt **Aktueller Status** können Sie den Fortschritt überwachen und Links zu Ihrem Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Wrike ausgeführt werden. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)