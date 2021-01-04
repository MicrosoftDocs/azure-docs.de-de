---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Teamphoria | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Teamphoria konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: a8cf58f49cba1bcc77c96a0da2061a037bb62bf2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504744"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Teamphoria

In diesem Tutorial erfahren Sie, wie Sie Teamphoria in Azure Active Directory (Azure AD) integrieren. Die Integration von Teamphoria in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Teamphoria hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Teamphoria anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Teamphoria-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Teamphoria unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-teamphoria-from-the-gallery"></a>Hinzufügen von Teamphoria aus dem Katalog

Zum Konfigurieren der Integration von Teamphoria in Azure AD müssen Sie Teamphoria aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Teamphoria** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Teamphoria** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Teamphoria

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Teamphoria mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Teamphoria eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Teamphoria die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Teamphoria](#configure-teamphoria-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Teamphoria-Testbenutzers](#create-teamphoria-test-user)** , um in Teamphoria ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Teamphoria** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Teamphoria-Client](https://www.teamphoria.com/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Teamphoria einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Teamphoria gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Teamphoria** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-teamphoria-sso"></a>Konfigurieren des einmaligen Anmeldens für Teamphoria

1. Wenn Sie die Konfiguration in Teamphoria automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Teamphoria einrichten** , um zur Anwendung Teamphoria weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Teamphoria anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Teamphoria manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Teamphoria-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Wechseln Sie auf der linken Symbolleiste zu **ADMIN SETTINGS** (Administratoreinstellungen), und klicken Sie auf der Registerkarte „Configure“ (Konfigurieren) auf **SINGLE SIGN-ON** (Einmaliges Anmelden), um das SSO-Konfigurationsfenster zu öffnen.

    ![Screenshot der Administratoreinstellungen zum Auswählen des einmaligen Anmeldens](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Klicken Sie in der oberen rechten Ecke auf die Option **ADD NEW IDENTITY PROVIDER** (Neue Identitätsanbieter hinzufügen), um das Formular zum Hinzufügen der Einstellungen für SSO zu öffnen.

    ![Screenshot zum Auswählen der Option für das Hinzufügen eines neuen Identitätsanbieters](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Geben Sie die Details wie unten beschrieben in den Feldern ein.

    ![Screenshot: Seite, auf der Sie die beschriebenen Werte eingeben können](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME** (Anzeigename): Geben Sie den Anzeigenamen des Plug-Ins auf der Administratorseite ein.

    b. **BUTTON NAME** (Schaltflächenname): Der Name der Registerkarte, die auf der Anmeldeseite für die Anmeldung über SSO angezeigt wird.

    c. **CERTIFICATE** (Zertifikat): Öffnen Sie das zuvor aus dem Azure-Portal heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in dieses Feld ein.

    d. **ENTRY POINT** (Einstiegspunkt): Fügen Sie die aus dem Azure-Portal kopierte **Anmelde-URL** ein.

    e. Setzen Sie die Option auf **ON** (Ein), und klicken Sie auf **SAVE** (Speichern).

### <a name="create-teamphoria-test-user"></a>Erstellen eines Teamphoria-Testbenutzers

Damit sich Azure AD-Benutzer bei Teamphoria anmelden können, müssen sie in Teamphoria bereitgestellt werden. Im Fall von Teamphoria ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Teamphoria-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der linken Symbolleiste auf **ADMIN** -Einstellungen, und klicken Sie auf der Registerkarte **MANAGE** (Verwalten) auf **USERS** (Benutzer), um die Administratorseite für Benutzer zu öffnen.

    ![Mitarbeiter hinzufügen](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klicken Sie auf die Option **MANUAL INVITE** (Manuell einladen).

    ![Screenshot der Option für manuelles Einladen](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Führen Sie auf dieser Seite die folgende Aktion aus.

    ![Screenshot der Seite für manuelles Einladen von Benutzern zum Eingeben von Name und E-Mail-Adresse](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Geben Sie im Textfeld **EMAIL ADDRESS** (E-MAIL-ADRESSE) die **E-Mail-Adresse** des Benutzers (etwa B.Simon) ein.

    b. Geben Sie im Textfeld **FIRST NAME** (VORNAME) den Vornamen des Benutzers ein (z. B. **B** ).

    c. Geben Sie im Textfeld **LAST NAME** (NACHNAME) den Nachnamen des Benutzers ein (z. B. **Simon** ).

    d. Klicken Sie auf **INVITE 1 USER** (1 Benutzer einladen). Der Benutzer muss die Einladung annehmen, um im System erstellt zu werden.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Teamphoria“ klicken, sollten Sie automatisch bei der Teamphoria-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Teamphoria mit Azure AD ausprobieren](https://aad.portal.azure.com/)