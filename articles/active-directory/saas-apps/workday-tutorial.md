---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workday | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Workday konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181372"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workday

In diesem Tutorial erfahren Sie, wie Sie Workday in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Workday in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Workday hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Workday anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Workday-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Workday unterstützt **SP-initiiertes** einmaliges Anmelden.

* Workday Mobile Application kann nun mit Azure AD konfiguriert werden, um SSO zu ermöglichen. Weitere Informationen zum Konfigurieren finden Sie unter [diesem Link](workday-mobile-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-workday-from-the-gallery"></a>Hinzufügen von Workday aus dem Katalog

Zum Konfigurieren der Integration von Workday in Azure AD müssen Sie Workday aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Workday** in das Suchfeld ein.
1. Wählen Sie **Workday** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Workday

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workday mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workday eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workday die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren von Workday](#configure-workday)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Workday-Testbenutzers](#create-workday-test-user)**, um ein Pendant von B. Simon in Workday zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Workday** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. Geben Sie im Textfeld **Abmelde-URL** eine URL im folgenden Format ein: `https://impl.workday.com/<tenant>/login-saml.htmld`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für die Anmelde-URL, die Antwort-URL und die Abmelde-URL. Ihre Antwort-URL muss eine Unterdomäne aufweisen (z.B. www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Eine URL wie `http://www.myworkday.com` funktioniert, `http://myworkday.com` hingegen nicht. Wenden Sie sich an das [Supportteam für den Workday-Client](https://www.workday.com/en-us/partners-services/services/support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Workday-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die Workday-Anwendung erwartet, dass **nameidentifier** die Werte für **user.mail**, **UPN** usw. zugeordnet sind. Sie müssen die Attributzuordnung daher bearbeiten, indem Sie auf das Symbol **Bearbeiten** klicken und die Attributzuordnung ändern.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

    > [!NOTE]
    > Hier ist die Namens-ID mit Benutzerprinzipalname (user.userprincipalname) als Standard zugeordnet. Sie müssen die Namens-ID der tatsächlichen Benutzer-ID in Ihrem Workday-Konto (E-Mail-Adresse, UPN usw.) zuordnen, damit einmaliges Anmelden funktioniert.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Klicken Sie zum Ändern der Optionen unter **Signieren** gemäß Ihren Anforderungen auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Zertifikat](common/edit-certificate.png) 

    ![SAML-Signaturzertifikat](./media/workday-tutorial/signing-option.png)

    a. Wählen Sie **SAML-Antwort und -Assertion signieren** für **Signaturoption** aus.

    b. Klicken Sie unten auf der Seite auf **Speichern**.

1. Kopieren Sie im Abschnitt **Workday einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Workday gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Workday** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-workday"></a>Konfigurieren von Workday

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Workday-Unternehmenswebsite als Administrator an.

1. Geben Sie oben links auf der Startseite im **Suchfeld** den Namen **Mandanten-Setup bearbeiten – Sicherheit** ein.

    ![Mandanten-Sicherheit bearbeiten](./media/workday-tutorial/IC782925.png "Mandanten-Sicherheit bearbeiten")


1. Führen Sie im Abschnitt **SAML Setup** die folgenden Schritte aus:

    ![SAML-Setup](./media/workday-tutorial/IC782926.png "SAML-Setup")

    a.  Wählen Sie **SAML-Authentifizierung aktivieen** aus.

    b.  Klicken Sie auf **Zeile hinzufügen**.

1. Führen Sie im Abschnitt **SAML Identity Providers** (SAML-Identitätsanbieter) die folgenden Aktionen für die neu erstellte Zeile aus:

    a. Führen Sie für die nachfolgend dargestellten Felder die unten beschriebenen Aktionen aus:

    ![SAML Identity Providers 1](./media/workday-tutorial/IC7829271.png "SAML-Identitätsanbieter") (SAML-Identitätsanbieter 1)

    * Geben Sie im Textfeld **Name des Identitätsanbieters** einen Anbieternamen ein (z.B: *SPInitiatedSSO*).

    * Kopieren Sie im Azure-Portal im Abschnitt **Workday einrichten** den **Azure AD-Bezeichner**, und fügen Sie sie in das Textfeld **Issuer** (Aussteller) ein.

    * Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat** im Editor, und fügen Sie den Inhalt im Textfeld **x.509 Certificate** (x.509-Zertifikat) ein.

    b. Führen Sie für die nachfolgend dargestellten Felder die unten beschriebenen Aktionen aus:

    ![SAML Identity Providers 2](./media/workday-tutorial/saml-identity-provider-2.png "SAML-Identitätsanbieter") (SAML-Identitätsanbieter 2)

    * Aktivieren Sie das Kontrollkästchen **Enable IdP Initiated Logout** (IdP-initiierte Abmeldung ermöglichen).

    * Geben Sie in das Textfeld **Logout Response URL** (Anforderungs-URL für Abmeldung) die URL **http://www.workday.com** ein.

    * Fügen Sie in das Textfeld **Logout Request URL** (Anforderungs-URL für Abmeldung) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    * Aktivieren Sie das Kontrollkästchen **SP Initiated** (SP-initiiert).

    * Geben Sie in das Textfeld **Dienstanbieter-ID** Folgendes ein: **http://www.workday.com**.


    * Wählen Sie **Für SP-initiierte Authentifizierungsanforderung keinen Deflate ausführen** aus.

    c. Führen Sie für die nachfolgend dargestellten Felder die unten beschriebenen Aktionen aus:

    ![SAML Identity Providers 3](./media/workday-tutorial/saml-identity-provider-3.png "SAML-Identitätsanbieter") (SAML-Identitätsanbieter 3)

    * Kopieren Sie im Azure-Portal im Abschnitt **Workday einrichten** die **Anmelde-URL**, und fügen Sie sie in das Textfeld **IdP SSO Service URL** (Dienst-URL des Identitätsanbieters für einmaliges Anmelden) ein.

    * Wählen Sie im Textfeld **Used for Environments** (Für Umgebungen verwendet) den entsprechenden Umgebungsnamen im Dropdownmenü aus.

1. Führen Sie die in der folgenden Abbildung gezeigten Schritte aus:

    ![Workday](./media/workday-tutorial/service-provider.png "SAML-Identitätsanbieter")

    a. Geben Sie in das Textfeld **Service Provider ID (Will be Deprecated)** (Dienstanbieter-ID (wird eingestellt)) Folgendes ein: **http://www.workday.com** .

    b. Geben Sie in das Textfeld **IDP SSO Service URL (Will be Deprecated)** (Dienst-URL des Identitätsanbieters für einmaliges Anmelden (wird eingestellt)) den Wert der **Anmelde-URL** ein.

    c. Wählen Sie **Do Not Deflate SP-initiated Authentication Request (Will be Deprecated)** (Für SP-initiierte Authentifizierungsanforderung keinen Deflate ausführen (wird eingestellt)) aus.

    d. Wählen Sie als **Authentication Request Signature Method** (Signaturmethode für Authentifizierungsanfragen) die Option **SHA256** aus.

    e. Klicken Sie auf **OK**.

    > [!NOTE]
    > Stellen Sie sicher, dass Sie das einmalige Anmelden ordnungsgemäß eingerichtet haben. Falls Sie das einmalige Anmelden mit der falschen Einrichtung aktivieren, können Sie die Anwendung möglicherweise nicht mit Ihren Anmeldeinformationen öffnen und werden gesperrt. In diesem Fall bietet Workday eine Sicherungs-URL für die Anmeldung, mit der Benutzer ihren normalen Benutzernamen mit Kennwort im folgenden Format verwenden können: [Ihre Workday-URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Erstellen eines Workday-Testbenutzers

1. Melden Sie sich bei der Workday-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der Ecke oben rechts auf **Profile** (Profil), wählen Sie **Home** aus, und klicken Sie auf der Registerkarte **Applications** (Anwendungen) auf **Directory** (Verzeichnis). 

1. Wählen Sie auf der Seite **Directory** (Verzeichnis) auf der Registerkarte „View“ (Ansicht) die Option **Find Workers** (Mitarbeiter suchen) aus.

    ![Find workers (Mitarbeiter suchen)](./media/workday-tutorial/user-directory.png)

1.  Wählen Sie auf der Seite **Find Workers** (Mitarbeiter suchen) den Benutzer in den Ergebnissen aus.

1. Wählen Sie auf der folgenden Seite **Job > Worker Security** (Position > Mitarbeitersicherheit) aus. Die Angabe für **Workday account** (Workday-Konto) muss mit dem Wert der **Namens-ID** für Azure Active Directory übereinstimmen.

    ![Worker Security (Mitarbeitersicherheit)](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Weitere Informationen zum Erstellen eines Workday-Testbenutzers erhalten Sie vom [Supportteam für den Workday-Client](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Workday weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die Workday-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Workday“ klicken, sollten Sie automatisch bei der Workday-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Workday können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.