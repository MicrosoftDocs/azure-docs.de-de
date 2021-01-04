---
title: 'Tutorial: Konfigurieren von Visitly für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Visitly konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358608"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Visitly für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Visitly und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern oder Gruppen in Visitly zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Informationen zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen (Software as a Service) mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Vorschaufunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten
* [Einen Visitly-Mandanten](https://www.visitly.io/pricing/)
* Ein Benutzerkonto in Visitly mit Administratorberechtigungen

## <a name="assign-users-to-visitly"></a>Zuweisen von Benutzern zu Visitly 

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Zusammenhang mit der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Visitly benötigen. Anschließend können Sie diese Benutzer oder Gruppen Visitly wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Visitly 

* Wir empfehlen Ihnen, Visitly zuerst nur einem einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Visitly müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle „Standardzugriff“ werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-visitly-for-provisioning"></a>Einrichten von Visitly für die Bereitstellung

Bevor Sie Visitly für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie die SCIM-Bereitstellung (System for Cross-Domain Identity Management, System für domänenübergreifendes Identitätsmanagement) in Visitly aktivieren.

1. Melden Sie sich bei [Visitly](https://app.visitly.io/login) an. Wählen Sie **Integrations** (Integrationen) > **Host Synchronization** (Hostsynchronisierung) aus.

    ![Hostsynchronisierung](media/Visitly-provisioning-tutorial/login.png)

2. Wählen Sie den Abschnitt **Azure AD** aus.

    ![Azure AD-Abschnitt](media/Visitly-provisioning-tutorial/integration.png)

3. Kopieren Sie den **API-Schlüssel**. Diese Werte werden im Azure-Portal auf der Registerkarte **Bereitstellung** Ihrer Visitly-Anwendung in das Feld **Geheimes Token** eingegeben.

    ![API-Schlüssel](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Hinzufügen von Visitly aus dem Katalog

Bevor Sie Visitly für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, fügen Sie Visitly aus dem Azure AD-Anwendungskatalog zur Liste mit den verwalteten SaaS-Anwendungen hinzu.

Führen Sie die folgenden Schritte aus, um Visitly aus dem Azure AD-Anwendungskatalog hinzuzufügen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Visitly** ein, wählen Sie im Ergebnisbereich die Anwendung **Visitly** aus, und wählen Sie dann die Schaltfläche **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Visitly in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Konfigurieren der automatischen Benutzerbereitstellung in Visitly 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Visitly auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Um das SAML-basierte einmalige Anmelden für Visitly zu aktivieren, befolgen Sie die Anweisungen unter [Tutorial: Einmaliges Anmelden in Visitly](Visitly-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Konfigurieren der automatischen Benutzerbereitstellung für Visitly in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Alle Anwendungen](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Visitly** aus.

    ![Visitly-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Bereitstellungsmodus, festgelegt auf „Automatisch“](common/provisioning-automatic.png)

5. Geben Sie im Abschnitt „Administratoranmeldeinformationen“ die zuvor abgerufenen Werte von `https://api.visitly.io/v1/usersync/SCIM` und **API-Schlüssel** in das Feld **Mandanten-URL** bzw. **Geheimes Token** ein. Um sicherzustellen, dass Azure AD eine Verbindung mit Visitly herstellen kann, wählen Sie **Verbindung testen** aus. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Visitly-Konto über Administratorberechtigungen verfügt, und versuchen Sie es dann noch einmal.

    ![Mandanten-URL und -token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Wählen Sie **Speichern** aus.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Visitly synchronisieren** aus.

    ![Visitly-Benutzerzuordnungen](media/visitly-provisioning-tutorial/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Visitly synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in Visitly für Aktualisierungsvorgänge verwendet. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Visitly-Benutzerattribute](media/visitly-provisioning-tutorial/userattribute.png)

10. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Visitly zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer oder Gruppen fest, die in Visitly bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Unter [Überprüfen des Status der Benutzerbereitstellung](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) finden Sie weitere Informationen dazu, wie lange das Bereitstellen von Benutzern oder Gruppen dauert.

Im Abschnitt **Aktueller Status** können Sie den Fortschritt überwachen und Links zu Ihrem Bereitstellungsaktivitätsbericht folgen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst für Visitly ausgeführt werden. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Connector-Einschränkungen

Visitly unterstützt kein endgültiges Löschen. Es wird immer nur vorläufige Löschen durchgeführt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
