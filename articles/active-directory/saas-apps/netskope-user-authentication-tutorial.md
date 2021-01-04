---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Netskope User Authentication | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Netskope User Authentication konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 1ae9e39f5a23725b17451d7144c8304194d2a1d0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Netskope User Authentication

In diesem Tutorial erfahren Sie, wie Sie Netskope User Authentication in Azure Active Directory (Azure AD) integrieren. Die Integration von Netskope User Authentication in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Netskope User Authentication haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Netskope User Authentication anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Netskope User Authentication-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Netskope User Authentication unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Hinzufügen von Netskope User Authentication aus dem Katalog

Um die Integration von Netskope User Authentication in Azure AD zu konfigurieren, müssen Sie Netskope User Authentication über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Netskope User Authentication** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Netskope User Authentication** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Netskope User Authentication

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Netskope User Authentication mithilfe eines Testbenutzers namens **B.Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Netskope User Authentication eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Netskope User Authentication die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Netskope User Authentication](#configure-netskope-user-authentication-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Netskope User Authentication-Testbenutzers](#create-netskope-user-authentication-test-user)** , um in Netskope User Authentication eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Netskope User Authentication** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<tenantname>.goskope.com/<customer entered string>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte werden später in diesem Tutorial erklärt.

1. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Die Anmelde-URL ist lediglich ein Beispielwert. Aktualisieren Sie ihn mit der tatsächlichen Anmelde-URL. Den Wert für die Anmelde-URL erhalten Sie vom [Clientsupportteam von Netskope User Authentication](mailto:support@netskope.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Netskope User Authentication einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf Netskope User Authentication gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Netskope User Authentication** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurieren des einmaligen Anmeldens für Netskope User Authentication

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Netskope User Authentication-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf die Registerkarte **Active Platform** (Aktive Plattform).

    ![Screenshot, auf dem „Active Platform“ (Aktive Plattform) in den Einstellungen ausgewählt ist](./media/netskope-user-authentication-tutorial/user1.png)

1. Scrollen Sie nach unten zu **FORWARD PROXY** (WEITERLEITUNGSPROXY), und wählen Sie **SAML** aus.

    ![Screenshot, auf dem unter „Active Platform“ (Aktive Plattform) die Option „SAML“ ausgewählt ist](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Führen Sie auf der Seite **SAML Settings** die folgenden Schritte aus:

    ![Screenshot: „SAML Settings“ (SAML-Einstellungen), wo Sie die beschriebenen Werte eingeben können](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Kopieren Sie die **SAML-Entitäts-ID** , und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    b. Kopieren Sie die **SAML-ACS-URL** , und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

1. Klicken Sie auf **ADD ACCOUNT** (KONTO HINZUFÜGEN).

    ![Screenshot, auf dem „ADD ACCOUNT“ (KONTO HINZUFÜGEN) im Bereich „SAML“ ausgewählt ist](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Führen Sie auf der Seite **Add SAML Account** (SAML-Konto hinzufügen) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Add SAML Account“ (SAML-Konto hinzufügen), in dem Sie die beschriebenen Werte eingeben können](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Geben Sie im Textfeld **NAME** den Namen an (beispielsweise „Azure AD“).

    b. Fügen Sie in das Textfeld **IDP URL** (IDP-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **IDP ENTITY ID** (IDP-ENTITÄTS-ID) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie die heruntergeladene Metadatendatei in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **IDP CERTIFICATE** (IDP-ZERTIFIKAT) ein.

    e. Klicken Sie auf **SPEICHERN** .

### <a name="create-netskope-user-authentication-test-user"></a>Erstellen eines Netskope User Authentication-Testbenutzers

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Netskope User Authentication-Unternehmenswebsite als Administrator an.

1. Klicken Sie im linken Navigationsbereich auf **Settings** (Einstellungen).

    ![Screenshot, auf dem „Settings“ (Einstellungen) ausgewählt ist](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Klicken Sie auf die Registerkarte **Active Platform** (Aktive Plattform).

    ![Screenshot, auf dem „Active Platform“ (Aktive Plattform) in den Einstellungen ausgewählt ist](./media/netskope-user-authentication-tutorial/user1.png)

1. Klicken Sie auf die Registerkarte **Benutzer** .

    ![Screenshot, auf dem unter „Active Platform“ (Aktive Plattform) die Option „Users“ (Benutzer) ausgewählt ist](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klicken Sie auf **ADD USERS** (BENUTZER HINZUFÜGEN).

    ![Screenshot: Dialogfeld „Users“ (Benutzer), in dem Sie die Option „ADD USERS“ (BENUTZER HINZUFÜGEN) auswählen können](./media/netskope-user-authentication-tutorial/user-add.png)

1. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie hinzufügen möchten, und klicken Sie anschließend auf **ADD** (HINZUFÜGEN).

    ![Screenshot: Abschnitt „Add Users“ (Benutzer hinzufügen), in dem Sie eine Liste von Benutzern eingeben können](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Netskope User Authentication“ klicken, sollten Sie automatisch bei der Netskope User Authentication-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Netskope User Authentication mit Azure AD ausprobieren](https://aad.portal.azure.com/)