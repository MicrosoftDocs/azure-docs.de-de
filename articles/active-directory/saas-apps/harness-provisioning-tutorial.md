---
title: 'Tutorial: Konfigurieren von Harness für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Harness konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353712"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Harness für die automatische Benutzerbereitstellung

In diesem Artikel wird beschrieben, wie Sie Azure Active Directory (Azure AD) zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern oder Gruppen in Harness konfigurieren.

> [!NOTE]
> Dieser Artikel enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Informationen zu diesem Dienst und Antworten auf häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das diesem Artikel zugrunde liegende Szenario setzt voraus, dass Sie bereits über Folgendes verfügen:

* Einen Azure AD-Mandanten
* [Einen Harness-Mandanten](https://harness.io/pricing/)
* Ein Benutzerkonto in Harness mit *Administratorberechtigungen*

## <a name="assign-users-to-harness"></a>Zuweisen von Benutzern zu Harness

Azure Active Directory ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer oder Gruppen in Azure AD Zugriff auf Harness benötigen. Sie können diese Benutzer oder Gruppen anschließend Harness zuweisen, indem Sie die Anleitung unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) befolgen.

## <a name="important-tips-for-assigning-users-to-harness"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Harness

* Es empfiehlt sich, Harness zuerst nur einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Harness müssen Sie im Dialogfeld **Zuweisung** eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle *Standardzugriff* werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-harness-for-provisioning"></a>Einrichten von Harness für die Bereitstellung

1. Melden Sie sich bei der [Harness-Verwaltungskonsole](https://app.harness.io/#/login) an, und navigieren Sie zu **Continuous Security** > **Access Management** (Kontinuierliche Sicherheit > Zugriffsverwaltung).

    ![Harness-Verwaltungskonsole](media/harness-provisioning-tutorial/admin.png)

1. Wählen Sie **API Keys** (API-Schlüssel) aus.

    ![Link zu API-Schlüsseln in Harness](media/harness-provisioning-tutorial/apikeys.png)

1. Wählen Sie **Add API Key** (API-Schlüssel hinzufügen) aus. 

    ![Link zum Hinzufügen von API-Schlüsseln in Harness](media/harness-provisioning-tutorial/addkey.png)

1. Gehen Sie im Bereich **Add Api Key** (API-Schlüssel hinzufügen) wie folgt vor:

    ![Bereich zum Hinzufügen von API-Schlüsseln in Harness](media/harness-provisioning-tutorial/title.png)
   
   a. Geben Sie im Feld **Name** einen Namen für den Schlüssel ein.  
   b. Wählen Sie in der Dropdownliste **Permissions Inherited from** (Geerbte Berechtigungen von) eine Option aus. 
   
1. Klicken Sie auf **Submit** (Senden).

1. Kopieren Sie den Wert unter **Key** (Schlüssel) zur späteren Verwendung in diesem Tutorial.

    ![Harness: Token erstellen](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Hinzufügen von Harness aus dem Katalog

Vor dem Konfigurieren von Harness für die automatische Benutzerbereitstellung mit Azure AD müssen Sie Harness aus dem Azure AD-Anwendungskatalog der Liste der verwalteten SaaS-Anwendungen hinzufügen.

1. Wählen Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) die Option **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Der Link „Alle Anwendungen“](common/enterprise-applications.png)

1. Klicken Sie oben im Bereich auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **Harness** ein, und wählen Sie in der Ergebnisliste **Harness** und dann die Schaltfläche **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Harness in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurieren der automatischen Benutzerbereitstellung in Harness 

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Harness auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Harness aktivieren. Befolgen Sie dazu die Anweisungen unter [Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Harness](./harness-tutorial.md). Sie können einmaliges Anmelden unabhängig von der automatischen Benutzerbereitstellung konfigurieren, obwohl diese beiden Features einander ergänzen.

> [!NOTE]
> Weitere Informationen zum Harness-SCIM-Endpunkt finden Sie im Harness-Artikel zu [API-Schlüsseln](https://docs.harness.io/article/smloyragsm-api-keys).

Führen Sie zum Konfigurieren der automatischen Benutzerbereitstellung für Harness in Azure AD die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Unternehmensanwendungen** > **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Harness** aus.

    ![Harness-Link in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie **Bereitstellung** aus.

    ![Schaltfläche „Bereitstellung“](common/provisioning.png)

1. Wählen Sie in der Dropdownliste **Bereitstellungsmodus** den Eintrag **Automatisch** aus.

    ![Dropdownliste „Bereitstellungsmodus“](common/provisioning-automatic.png)

1. Führen Sie unter **Administratoranmeldeinformationen** die folgenden Schritte aus:

    ![Mandanten-URL + Token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Geben Sie im Feld **Mandanten-URL** die URL **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** ein. Wenn Sie in Harness angemeldet sind, können Sie Ihre Harness-Konto-ID über die URL in Ihrem Browser abrufen.
   b. Geben Sie im Feld **Geheimes Token** den Wert des SCIM-Authentifizierungstokens ein, den Sie in Schritt 6 des Abschnitts „Einrichten von Harness für die Bereitstellung“ gespeichert haben.  
   c. Um sicherzustellen, dass Azure AD eine Verbindung mit Harness herstellen kann, wählen Sie **Verbindung testen** aus. Vergewissern Sie sich im Fall eines Verbindungsfehlers, dass Ihr Harness-Konto über *Administratorberechtigungen* verfügt, und versuchen Sie es noch mal.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Feld „Benachrichtigungs-E-Mail“](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie unter **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Harness synchronisieren** aus.

    ![Harness: Link „Azure Active Directory-Benutzer mit Harness synchronisieren“](media/harness-provisioning-tutorial/usermappings.png)

1. Überprüfen Sie unter **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Harness synchronisiert werden. Beachten Sie, dass die als *übereinstimmend* ausgewählten Attribute für den Abgleich der Benutzerkonten in Harness für Updatevorgänge verwendet werden. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Harness: Bereich für Benutzerattributzuordnungen](media/harness-provisioning-tutorial/userattributes.png)

1. Wählen Sie unter **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Harness synchronisieren** aus.

    ![Harness: Link „Azure Active Directory-Gruppen mit Harness synchronisieren“](media/harness-provisioning-tutorial/groupmappings.png)

1. Überprüfen Sie unter **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Harness synchronisiert werden. Die als *übereinstimmende* Eigenschaften ausgewählten Attribute werden verwendet, um die Gruppen in Harness für Updatevorgänge abzugleichen. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Harness: Bereich für Gruppenattributzuordnungen](media/harness-provisioning-tutorial/groupattributes.png)

1. Informationen zum Konfigurieren von Bereichsfiltern finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Ändern Sie zum Aktivieren des Azure AD-Bereitstellungsdiensts unter **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Schalter „Bereitstellungsstatus“ auf „Ein“](common/provisioning-toggle-on.png)

1. Wählen Sie unter **Einstellungen** in der Dropdownliste **Bereich** aus, wie die Benutzer oder Gruppen synchronisiert werden sollen, die Sie für Harness bereitstellen.

    ![Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Schaltfläche „Speichern“ für die Bereitstellung](common/provisioning-configuration-save.png)

Mit diesem Vorgang wird die erste Synchronisierung der von Ihnen bereitgestellten Benutzer oder Gruppen gestartet. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die folgenden Synchronisierungen. Die Synchronisierungen erfolgen etwa alle 40 Minuten, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Den Fortschritt können Sie im Abschnitt **Synchronisierungsdetails** überwachen. Außerdem können Sie die Links zu einem Bericht zu den Bereitstellungsaktivitäten verwenden. Darin werden alle Aktionen beschrieben, die vom Azure AD-Bereitstellungsdienst für Harness durchgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)