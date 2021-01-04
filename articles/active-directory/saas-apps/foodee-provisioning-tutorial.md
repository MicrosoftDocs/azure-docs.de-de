---
title: 'Tutorial: Konfigurieren von Foodee für die automatische Benutzerbereitstellung mithilfe von Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Foodee konfigurieren.
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
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358676"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Foodee für die automatische Benutzerbereitstellung

In diesem Artikel wird gezeigt, wie Sie Azure Active Directory (Azure AD) in Foodee und Azure AD für die automatische Bereitstellung oder Aufhebung der Bereitstellung von Benutzern oder Gruppen in Foodee konfigurieren.

> [!NOTE]
> Der Artikel enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wenn Sie sich über den Zweck und die Funktionsweise dieses Diensts informieren sowie Antworten auf häufig gestellte Fragen erhalten möchten, lesen Sie [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Weitere Informationen zu den Nutzungsbedingungen von Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass Sie die folgenden Voraussetzungen erfüllt haben und verfügen über:

* Einen Azure AD-Mandanten
* [Einen Foodee-Mandanten](https://www.food.ee/about/)
* Ein Benutzerkonto in Foodee mit Administratorberechtigungen

## <a name="assign-users-to-foodee"></a>Zuweisen von Benutzern zu Foodee 

Azure AD ermittelt anhand von *Zuweisungen* , welche Benutzer Zugriff auf ausgewählte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer oder Gruppen in Azure AD Zugriff auf Foodee benötigen. Nachdem Sie Ihre Entscheidung getroffen haben, können Sie diese Benutzer oder Gruppen entsprechend den Anleitungen unter [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md) Foodee zuweisen.

## <a name="important-tips-for-assigning-users-to-foodee"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Foodee 

Beachten Sie beim Zuweisen von Benutzern die folgenden Tipps:

* Wir empfehlen Ihnen, Foodee zuerst nur einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können Sie dann weitere Benutzer oder Gruppen zuweisen.

* Wenn Sie Foodee einen Benutzer zuweisen, wählen Sie im Bereich **Zuweisung** eine gültige anwendungsspezifische Rolle aus (sofern verfügbar). Benutzer mit der Rolle *Standardzugriff* werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-foodee-for-provisioning"></a>Einrichten von Foodee für die Bereitstellung

Bevor Sie Foodee für die automatische Benutzerbereitstellung mithilfe von Azure AD konfigurieren, müssen Sie die SCIM-Bereitstellung (System for Cross-Domain Identity Management, System für domänenübergreifendes Identitätsmanagement) in Foodee aktivieren.

1. Melden Sie sich bei [Foodee](https://www.food.ee/login/) an, und wählen Sie dann Ihre Mandanten-ID aus.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Screenshot des Hauptmenüs des Foodee-Unternehmensportals. Im Menü wird ein Platzhalter für eine Mandanten-ID angezeigt." border="false":::

1. Wählen Sie unter **Enterprise Portal** (Unternehmensportal) die Option **Single Sign On** (Einmaliges Anmelden) aus.

    ![Das Foodee-Menü „Enterprise Portal“ im linken Bereich](media/Foodee-provisioning-tutorial/scim.png)

1. Kopieren Sie den Wert im Feld **API-Token** zur späteren Verwendung. Sie werden ihn im Azure-Portal auf der Registerkarte **Bereitstellung** Ihrer Foodee-Anwendung im Feld **Geheimes Token** eingeben.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Screenshot einer Seite des Foodee-Unternehmensportals. Ein API-Tokenwert ist hervorgehoben." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Hinzufügen von Foodee aus dem Katalog

Um Foodee für die automatische Benutzerbereitstellung mithilfe von Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen Foodee aus dem Azure AD-Anwendungskatalog hinzufügen.

Führen Sie die folgenden Schritte aus, um Foodee aus dem Azure AD-Anwendungskatalog hinzuzufügen:

1. Wählen Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) die Option **Azure Active Directory** aus.

    ![Der Befehl „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie oben im Bereich **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **Foodee** ein, wählen Sie im Ergebnisbereich **Foodee** und dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![„Foodee“ in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Konfigurieren der automatischen Benutzerbereitstellung in Foodee 

In diesem Abschnitt konfigurieren Sie den Azure AD-Bereitstellungsdienst für das Erstellen, Aktualisieren und Deaktivieren von Benutzern oder Gruppen in Foodee auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Foodee aktivieren. Folgen Sie dazu den Anleitungen unter [Tutorial: Integrieren von Foodee in Azure Active Directory](Foodee-tutorial.md). Sie können einmaliges Anmelden unabhängig von der automatischen Benutzerbereitstellung konfigurieren, obwohl diese beiden Features einander ergänzen.

Konfigurieren Sie die automatische Benutzerbereitstellung für Foodee in Azure AD, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com)**Unternehmensanwendungen** > **Alle Anwendungen** aus.

    ![Bereich für Unternehmensanwendungen](common/enterprise-applications.png)

1. Wählen Sie in der Liste **Anwendungen** den Eintrag **Foodee** aus.

    ![Der „Foodee“-Link in der Liste „Anwendungen“](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Screenshot der Optionen zum Verwalten mit aufgerufener Bereitstellungsoption](common/provisioning.png)

1. Wählen Sie in der Dropdownliste **Bereitstellungsmodus** den Eintrag **Automatisch** aus.

    ![Screenshot der Dropdownliste „Bereitstellungsmodus“ mit aufgerufener Option „Automatisch“](common/provisioning-automatic.png)

1. Führen Sie unter **Administratoranmeldeinformationen** die folgenden Schritte aus:

   a. Geben Sie im Feld **Mandanten-URL** den zuvor abgerufenen Wert **https:\//concierge.food.ee/scim/v2** ein.

   b. Geben Sie im Feld **Geheimes Token** den zuvor abgerufenen Wert für **API Token** ein.
   
   c. Um sicherzustellen, dass Azure AD eine Verbindung mit Foodee herstellen kann, wählen Sie **Verbindung testen** aus. Wenn die Verbindung nicht hergestellt werden kann, stellen Sie sicher, dass Ihr Foodee-Konto über Administratorberechtigungen verfügt, und versuchen Sie es dann noch einmal.

    ![Der Link „Verbindung testen“](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Das Textfeld „Benachrichtigungs-E-Mail“](common/provisioning-notification-email.png)

1. Wählen Sie **Speichern** aus.

1. Wählen Sie unter **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Foodee synchronisieren** aus.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Screenshot des Abschnitts „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Benutzer mit Foodee synchronisieren“ hervorgehoben." border="false":::

1. Überprüfen Sie unter **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Foodee synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Aktualisierungsvorgängen für den Abgleich der *Benutzerkonten* in Foodee verwendet. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Screenshot der Seite „Attributzuordnungen“. In einer Tabelle werden Attribute für Azure Active Directory und Foodee sowie die entsprechende Rangfolge aufgelistet." border="false":::

1. Wenn Sie Ihre Änderungen committen möchten, wählen Sie **Speichern** aus.
1. Wählen Sie unter **Zuordnungen** die Option **Azure Active Directory-Gruppen mit Foodee synchronisieren** aus.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Screenshot des Abschnitts „Zuordnungen“. Unter „Name“ ist die Option „Azure Active Directory-Gruppen mit Foodee synchronisieren“ hervorgehoben." border="false":::

1. Überprüfen Sie unter **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Foodee synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Aktualisierungsvorgängen für den Abgleich der *Gruppenkonten* in Foodee verwendet.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Screenshot der Seite „Attributzuordnungen“. In einer Tabelle werden Attribute für Azure Active Directory und Foodee sowie die entsprechende Rangfolge aufgelistet." border="false":::

1. Wenn Sie Ihre Änderungen committen möchten, wählen Sie **Speichern** aus.
1. Konfigurieren Sie die Bereichsfilter. Informationen zur Vorgehensweise finden Sie in den Anleitungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für Foodee zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Der Schalter „Bereitstellungsstatus“](common/provisioning-toggle-on.png)

1. Definieren Sie unter **Einstellungen** in der Dropdownliste **Bereich** die Benutzer oder Gruppen, die Sie für Foodee bereitstellen möchten.

    ![Die Dropdownliste für den Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Die Schaltfläche „Speichern“ für die Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Durch diesen Vorgang wird die Erstsynchronisierung der Benutzer oder Gruppen gestartet, die Sie in der Dropdownliste **Bereich** definiert haben. Die Erstsynchronisierung nimmt mehr Zeit in Anspruch als die nachfolgenden Synchronisierungen. Weitere Informationen finden Sie unter [Wie lange dauert die Bereitstellung von Benutzern?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Sie können den Abschnitt **Aktueller Status** verwenden, um den Fortschritt zu überwachen und den Links zu Ihrem Bereitstellungsaktivitätsbericht zu folgen. Der Bericht beschreibt alle vom Azure AD-Bereitstellungsdienst für Foodee ausgeführten Aktionen. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps im Azure-Portal](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
