---
title: 'Tutorial: Konfigurieren von Druva für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Druva konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2ef564ae61aab10e1d100ccaf2547248e4a6a8e8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352166"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Druva für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Druva und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Druva zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Tutorial zu Grunde liegende Szenario setzt voraus, dass Sie bereits über die folgenden Voraussetzungen verfügen:

* Einen Azure AD-Mandanten.
* Einen [Druva-Mandanten](https://www.druva.com/products/pricing-plans/)
* Ein Benutzerkonto in Druva mit Administratorberechtigungen

## <a name="assigning-users-to-druva"></a>Zuweisen von Benutzern zu Druva

Azure Active Directory ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Druva benötigen. Anschließend können Sie diese Benutzer und/oder Gruppen Druva wie folgt zuweisen:
* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Druva

* Es wird empfohlen, Druva einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Druva müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="setup-druva-for-provisioning"></a>Einrichten von Druva für die Bereitstellung

Bevor Sie Druva für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie in Druva die SCIM-Bereitstellung aktivieren.

1. Melden Sie sich bei Ihrer [Druva-Verwaltungskonsole](https://console.druva.com) an. Navigieren Sie zu **Druva > inSync**.

    ![Druva-Verwaltungskonsole](media/druva-provisioning-tutorial/menubar.png)

2. Navigieren Sie zu **Manage** > **Deployments** > **Users** (Verwalten > Bereitstellungen > Benutzer).

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Screenshot der Druva-Verwaltungskonsole. Die Option „Verwalten“ ist hervorgehoben, und das Menü „Verwalten“ wird angezeigt. In diesem Menü ist unter „Bereitstellungen“ die Option „Benutzer“ hervorgehoben." border="false":::

3.  Navigieren zu **Einstellungen**. Klicken Sie dann auf **Generate Token** (Token generieren).

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Screenshot einer Seite der Druva-Verwaltungskonsole. Die Option „Einstellungen“ ist hervorgehoben, und die Registerkarte „Einstellungen“ ist geöffnet. Die Schaltfläche „Token generieren“ ist hervorgehoben." border="false":::

4.  Kopieren Sie den Wert von **Auth token** (Authentifizierungstoken). Dieser Wert wird im Azure-Portal auf der Registerkarte „Bereitstellung“ Ihrer Druva-Anwendung in das Feld **Geheimes Token** eingegeben.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Screenshot der Seite „Token generieren“ in der Druva-Verwaltungskonsole. Der Link „Token kopieren“ ermöglicht das Kopieren des Werts des Authentifizierungstokens." border="false":::

## <a name="add-druva-from-the-gallery"></a>Hinzufügen von Druva aus dem Katalog

Bevor Sie Druva für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Druva aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Druva aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Druva** ein, wählen Sie im Ergebnisbereich **Druva** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Druva in der Ergebnisliste](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Konfigurieren der automatischen Benutzerbereitstellung in Druva 

In diesem Abschnitt werden die Schritte erläutert, mit denen Sie den Azure AD-Bereitstellungsdienst zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Druva auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD konfigurieren.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Druva aktivieren. Befolgen Sie hierzu die Anweisungen im [SSO-Tutorial für Druva](druva-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>So konfigurieren Sie die automatische Benutzerbereitstellung für Druva in Azure AD:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Druva** aus.

    ![Druva-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

5.  Geben Sie im Abschnitt „Administratoranmeldeinformationen“ in das Feld **Mandanten-URL**`https://apis.druva.com/insync/scim` ein. Geben Sie den Wert für das **Authentifizierungstoken** in das Feld **Geheimes Token** ein. Klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Druva herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Druva-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und wählen Sie **Bei Fehler E-Mail-Benachrichtigung senden** aus.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

7. Klicken Sie auf **Speichern**.

8. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Druva synchronisieren** aus.

    ![Benutzerzuordnungen in Druva](media/druva-provisioning-tutorial/usermapping.png)

9. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Druva synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in Druva für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Benutzerattribute in Druva](media/druva-provisioning-tutorial/userattribute.png)


10. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Um den Azure AD-Bereitstellungsdienst für Druva zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Aktivierter Bereitstellungsstatus](common/provisioning-toggle-on.png)

12. Legen Sie die Benutzer und/oder Gruppen fest, die in Druva bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

13. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Speichern der Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

    Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Druva ausgeführt werden.

    Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Connector-Einschränkungen

* Druva setzt **email** als erforderliches Attribut voraus. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen](../app-provisioning/check-status-user-account-provisioning.md).
