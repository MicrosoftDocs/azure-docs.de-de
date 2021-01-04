---
title: 'Tutorial: Konfigurieren von Elium für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Elium konfigurieren.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005502"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Elium für die automatische Benutzerbereitstellung

In diesem Tutorial wird gezeigt, wie Sie Elium und Azure Active Directory (Azure AD) für die automatische Bereitstellung und Aufhebung der Bereitstellung von Benutzern oder Gruppen in Elium konfigurieren.

> [!NOTE]
> Das Tutorial enthält die Beschreibung eines Connectors, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie Antworten bei häufig gestellten Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Dieser Connector befindet sich derzeit in der Vorschauversion. Informationen zu den allgemeinen Nutzungsbedingungen für Azure-Features in der Vorschau finden Sie unter [Zusätzliche Nutzungsbedingungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial setzt voraus, dass Sie bereits über die folgenden erforderlichen Komponenten verfügen:

* Einen Azure AD-Mandanten
* [Einen Elium-Mandanten](https://www.elium.com/pricing/)
* Ein Benutzerkonto in Elium, mit Administratorberechtigungen

## <a name="assigning-users-to-elium"></a>Zuweisen von Benutzern zu Elium

Azure AD ermittelt anhand von *Zuweisungen*, welche Benutzer Zugriff auf ausgewählte Apps erhalten. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Entscheiden Sie vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung, welche Benutzer und Gruppen in Azure AD Zugriff auf Elium benötigen. Weisen Sie anschließend diese Benutzer und Gruppen Elium zu, indem Sie die Schritte in [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md) ausführen.

## <a name="important-tips-for-assigning-users-to-elium"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Elium 

Wir empfehlen Ihnen, Elium zuerst nur einem einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und Gruppen zugewiesen werden.

Wenn Sie Elium einen Benutzer zuweisen, müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="set-up-elium-for-provisioning"></a>Einrichten von Elium für die Bereitstellung

Bevor Sie Elium für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie die SCIM-Bereitstellung (System for Cross-Domain Identity Management, System für domänenübergreifendes Identitätsmanagement) für Elium aktivieren. Folgen Sie diesen Schritten:

1. Melden Sie sich bei Elium an, und wechseln Sie zu **Mein Profil** > **Einstellungen**.

    ![Menüelement „Einstellungen“ in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Wählen Sie in der unteren linken Ecke unter **ERWEITERT** die Option **Sicherheit** aus.

    ![Link „Sicherheit“ in Elium](media/Elium-provisioning-tutorial/security.png)

1. Kopieren Sie die Werte für **Mandanten-URL** und **Geheimes Token**. Sie werden diese Werte später in den entsprechenden Feldern auf der Registerkarte **Bereitstellung** Ihrer Elium-Anwendung im Azure-Portal verwenden.

    ![Felder „Mandanten-URL“ und „Geheimes Token“ in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Hinzufügen von Elium aus dem Katalog

Um Elium für die automatische Benutzerbereitstellung mit Azure AD konfigurieren zu können, müssen Sie Ihrer Liste der verwalteten SaaS-Anwendungen (Software-as-a-Service) Elium aus dem Azure AD-Anwendungskatalog ebenfalls hinzufügen. Folgen Sie diesen Schritten:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich **Azure Active Directory** aus.

    ![Menüelement „Azure Active Directory“](common/select-azuread.png)

1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

     ![Blatt „Azure AD-Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie oben im Bereich **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Link „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **Elium** ein, wählen Sie in der Ergebnisliste **Elium** und dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![Suchfeld „Katalog“](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurieren der automatischen Benutzerbereitstellung in Elium

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren von Benutzern und Gruppen in Elium auf der Grundlage von Benutzer- und Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Wahlweise können Sie auch einmaliges Anmelden (Single Sign-on, SSO) für Elium auf der Grundlage von Security Assertion Markup Language (SAML) aktivieren. Befolgen Sie hierzu die Anweisungen im [Elium Single Sign-on Tutorial](Elium-tutorial.md). Sie können einmaliges Anmelden unabhängig von der automatischen Benutzerbereitstellung konfigurieren, obwohl die beiden Features einander ergänzen.

Führen Sie zum Konfigurieren der automatischen Benutzerbereitstellung für Elium in Azure AD die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.

    ![Blatt „Azure AD-Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste die Option **Elium** aus.

    ![Anwendungsliste auf dem Blatt „Unternehmensanwendungen“](common/all-applications.png)

1. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Registerkarte „Bereitstellung“ auf dem Blatt „Unternehmensanwendungen“](common/provisioning.png)

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Einstellung „Automatisch“ für „Bereitstellungsmodus“](common/provisioning-automatic.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** im Feld **Mandanten-URL** die Zeichenfolge **\<tenantURL\>/scim/v2** ein. (**tenantURL** ist der Wert, der zuvor aus der Elium-Administratorkonsole abgerufen wurde.) Geben Sie auch den Elium-Wert **Secret token** im Feld **Geheimes Token** ein. Wählen Sie schließlich **Verbindung testen** aus, um zu überprüfen, ob diese Azure AD-Instanz eine Verbindung mit Elium herstellen kann. Wenn die Verbindung nicht möglich ist, stellen Sie sicher, dass Ihr Elium-Konto über Administratorberechtigungen verfügt, und versuchen Sie es dann noch einmal.

    ![Felder „Mandanten-URL“ und „Geheimes Token“ in „Anmeldeinformationen“](common/provisioning-testconnection-tenanturltoken.png)

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten wird. Aktivieren Sie dann das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Benachrichtigungs-E-Mail](common/provisioning-notification-email.png)

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt **Zuordnungen** die Option **Azure Active Directory-Benutzer mit Elium synchronisieren** aus.

    ![Link „Synchronisieren“ zum Zuordnen von Azure AD-Benutzern zu Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Elium synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden bei Aktualisierungsvorgängen für den Abgleich der Benutzerkonten in Elium verwendet. Wählen Sie **Speichern** aus, um Ihre Änderungen zu committen.

    ![Attributzuordnungen zwischen Azure AD und Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Wenn Sie Bereichsfilter konfigurieren möchten, folgen Sie den Anleitungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Um den Azure AD-Bereitstellungsdienst für Elium zu aktivieren, ändern Sie im Abschnitt **Einstellungen** den **Bereitstellungsstatus** in **Ein**.

    ![Bereitstellungsstatus festgelegt auf „Ein“](common/provisioning-toggle-on.png)

1. Definieren Sie die Benutzer und Gruppen, die Sie für Elium bereitstellen möchten, indem Sie im Abschnitt **Einstellungen** die gewünschten Werte im Dropdown-Listenfeld **Bereich** auswählen.

    ![Listenfeld für den Bereitstellungsbereich](common/provisioning-scope.png)

1. Wählen Sie **Speichern** aus, wenn die Bereitstellung erfolgen kann.

    ![Schaltfläche „Speichern“ für die Bereitstellungskonfiguration](common/provisioning-configuration-save.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert wurden. Der Vorgang der Erstsynchronisierung dauert länger als spätere Synchronisierungen. Weitere Informationen zu der für die Bereitstellung benötigten Zeit finden Sie unter [Wie lange dauert die Bereitstellung von Benutzern?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Verwenden Sie den Abschnitt **Aktueller Status**, um den Fortschritt zu überwachen und den Links zu Ihrem Bereitstellungsaktivitätsbericht zu folgen. Im Bereitstellungsaktivitätsbericht werden alle Aktionen beschrieben, die vom Azure AD-Bereitstellungsdienst für Elium ausgeführt werden. Weitere Informationen finden Sie unter [Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Informationen zum Lesen der Azure AD-Bereitstellungsprotokolle finden Sie unter [Tutorial: Berichterstellung zur automatischen Benutzerkontobereitstellung](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../app-provisioning/check-status-user-account-provisioning.md)
