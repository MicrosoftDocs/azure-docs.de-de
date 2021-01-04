---
title: 'Tutorial: Azure Active Directory-Integration mit einer GitHub Enterprise Cloud-Organisation | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und einer GitHub Enterprise Cloud-Organisation konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 67a17aaa647d9aa6943b37d54fc0e3308ad8955f
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-a-github-enterprise-cloud-organization"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit einer GitHub Enterprise Cloud-Organisation

In diesem Tutorial erfahren Sie, wie Sie eine GitHub Enterprise Cloud-**Organisation** in Azure Active Directory (Azure AD) integrieren. Die Integration einer GitHub Enterprise Cloud-Organisation mit Azure AD ermöglicht Folgendes:

* Steuern in Azure AD, wer Zugriff auf Ihre GitHub Enterprise Cloud-Organisation hat
* Verwalten des Zugriffs auf Ihre GitHub Enterprise Cloud-Organisation an einem zentralen Ort: im Azure-Portal

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit einer GitHub Enterprise Cloud-Organisation konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Eine in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) erstellte GitHub-Organisation (erfordert den [Abrechnungsplan für GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* GitHub unterstützt **SP-initiiertes** einmaliges Anmelden.

* GitHub unterstützt die [**automatisierte** Benutzerbereitstellung (Organisationseinladungen)](github-provisioning-tutorial.md).
* Nach dem Konfigurieren von GitHub können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-github-from-the-gallery"></a>Hinzufügen von GitHub aus dem Katalog

Zum Konfigurieren der Integration von GitHub in Azure AD müssen Sie GitHub aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **GitHub** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **GitHub Enterprise Cloud - Organization** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für GitHub

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit GitHub mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in GitHub eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit GitHub die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für GitHub](#configure-github-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines GitHub-Testbenutzers](#create-github-test-user)** , um eine Entsprechung von B. Simon in GitHub zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **GitHub** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://github.com/orgs/<Organization ID>/sso`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://github.com/orgs/<Organization ID>`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://github.com/orgs/<Organization ID>/saml/consume`


    > [!NOTE]
    > Hinweis: Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL ersetzen. Hier empfehlen wir Ihnen, den eindeutigen Wert der Zeichenfolge im Bezeichner zu verwenden. Navigieren Sie zum Abschnitt „GitHub Admin“ (GitHub-Verwaltung), um diese Werte abzurufen.

5. Die GitHub-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute, wobei **Eindeutiger Benutzerbezeichner (Namens-ID)** hier **user.userprincipalname** zugeordnet wird. Die GitHub-Anwendung erwartet, dass **Eindeutiger Benutzerbezeichner (Namens-ID)** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher bearbeiten, indem Sie auf das Symbol **Bearbeiten** klicken und die Zuordnung entsprechend ändern.

    ![Screenshot: Abschnitt „User Attributes“ (Benutzerattribute) mit dem Symbol „Bearbeiten“ ausgewählt](common/edit-attribute.png)

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **GitHub einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf GitHub gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **GitHub** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.

    ![Benutzerrolle](./media/github-tutorial/user-role.png)

    > [!NOTE]
    > Die Option **Rolle auswählen** ist deaktiviert, und die Standardrolle für den ausgewählten Benutzer lautet „BENUTZER“.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-github-sso"></a>Konfigurieren des einmaligen Anmeldens für GitHub

1. Melden Sie sich in einem anderen Webbrowserfenster bei der GitHub-Organisationswebsite als Administrator an.

2. Navigieren Sie zu **Einstellungen**, und klicken Sie auf **Sicherheit**.

    ![Screenshot: GitHub-Menü „Organization settings“ (Organisationseinstellungen) mit ausgewählter Option „Security“ (Sicherheit)](./media/github-tutorial/security.png)

3. Aktivieren Sie das Kontrollkästchen **SAML-Authentifizierung aktivieren**, um die Felder für die Konfiguration des einmaligen Anmeldens anzuzeigen. Führen Sie die folgenden Schritte aus:

    ![Screenshot: Abschnitt „SAML Single Sign-On“ (Einmalige SAML-Anmeldung) mit aktiviertem Kontrollkästchen „Enable SAML authentication“ (SAML-Authentifizierung aktivieren) und hervorgehobenen URL-Textfeldern](./media/github-tutorial/saml-sso.png)

    a. Kopieren Sie den Wert für **URL für einmaliges Anmelden**, und fügen Sie ihn im Azure-Portal unter **Grundlegende SAML-Konfiguration** im Textfeld **Anmelde-URL** ein.
    
    b. Kopieren Sie den Wert für **Assertionsverbraucherdienst-URL**, und fügen Sie ihn im Azure-Portal unter **Grundlegende SAML-Konfiguration** im Textfeld **Antwort-URL** ein.

4. Konfigurieren Sie die folgenden Felder:

    ![Screenshot: Textfelder „Anmelde-URL“, „Aussteller“ und „Öffentliches Zertifikat“](./media/github-tutorial/configure.png)

    a. Fügen Sie im Textfeld **Sign-On URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Issuer** (Aussteller) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das aus dem Azure-Portal heruntergeladene Zertifikat im Editor, und fügen Sie es in das Textfeld **Public Certificate** (Öffentliches Zertifikat) ein.

    d. Klicken Sie auf das Symbol **Bearbeiten**, um die **Signaturmethode** und die **Digestmethode** von **RSA-SHA1** und **SHA1** in **RSA-SHA256** und **SHA256** wie unten gezeigt zu ändern.
    
    e. Aktualisieren Sie die standardmäßige **Assertionsverbraucherdienst-URL (Antwort-URL)** , sodass die URL auf GitHub mit der URL in der Azure-App-Registrierung übereinstimmt.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klicken Sie auf **Test SAML configuration** (SAML-Konfiguration testen), um sich zu vergewissern, dass beim einmaligen Anmelden keine Validierungsfehler oder anderen Fehler auftreten.

    ![Einstellungen](./media/github-tutorial/test.png)

6. Klicken Sie unten auf der Seite auf **Speichern**.

> [!NOTE]
> Einmaliges Anmelden auf GitHub führt die Authentifizierung für eine bestimmte Organisation in GitHub aus und ersetzt nicht die Authentifizierung von GitHub selbst. Wenn die github.com-Sitzung des Benutzers abgelaufen ist, werden Sie während des SSO-Prozesses möglicherweise aufgefordert, sich mit der ID und dem Kennwort für GitHub zu authentifizieren.

### <a name="create-github-test-user"></a>Erstellen eines GitHub-Testbenutzers

In diesem Abschnitt wird in GitHub eine Benutzerin namens Britta Simon erstellt. GitHub unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](github-provisioning-tutorial.md).

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der GitHub-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **People**.

    ![Screenshot: GitHub-Website mit ausgewählter Option „People“ (Personen)](./media/github-tutorial/people.png "Personen")

3. Klicken Sie auf **Invite member** (Mitglied einladen).

    ![Invite Users (Benutzer einladen)](./media/github-tutorial/invite-member.png "Invite Users")

4. Führen Sie auf der Dialogfeldseite **Invite member** (Mitglied einladen) die folgenden Schritte aus:

    a. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Kontos von Britta Simon ein.

    ![Invite People (Personen einladen)](./media/github-tutorial/email-box.png "Invite People")

    b. Klicken Sie auf **Einladung senden**.

    ![Screenshot: Dialogfeldseite „Invite Member“ (Mitglied einladen) mit dem Kontrollkästchen „Member“ (Mitglied) aktiviert und hervorgehobener Schaltfläche „Send invitation“ (Einladung senden)](./media/github-tutorial/send-invitation.png "Invite People")

    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „GitHub“ klicken, sollten Sie automatisch bei der GitHub-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [GitHub mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
