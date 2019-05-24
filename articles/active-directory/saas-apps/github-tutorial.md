---
title: 'Tutorial: Azure Active Directory-Integration in GitHub | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und GitHub konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25540d1f26fa6021ef05108f9743e77a6184f3b3
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898567"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Tutorial: Azure Active Directory-Integration in GitHub

In diesem Tutorial erfahren Sie, wie Sie GitHub in Azure Active Directory (Azure AD) integrieren.
Die Integration von GitHub in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf GitHub hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei GitHub anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit GitHub konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Eine in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) erstellte GitHub-Organisation (erfordert den [Abrechnungsplan für GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* GitHub unterstützt **SP-initiiertes** einmaliges Anmelden.

* GitHub unterstützt die [**automatisierte** Benutzerbereitstellung](github-provisioning-tutorial.md).

## <a name="adding-github-from-the-gallery"></a>Hinzufügen von GitHub aus dem Katalog

Zum Konfigurieren der Integration von GitHub in Azure AD müssen Sie GitHub aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um GitHub aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **GitHub** ein, wählen Sie im Ergebnisbereich **GitHub.com** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![GitHub in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei GitHub mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in GitHub eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei GitHub müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für GitHub](#configure-github-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines GitHub-Testbenutzers](#create-github-test-user)**, um eine Entsprechung für Britta Simon in GitHub zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei GitHub die folgenden Schritte aus:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **GitHub** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und den URLs für GitHub](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://github.com/orgs/<entity-id>/sso`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://github.com/orgs/<entity-id>`.

    > [!NOTE]
    > Hinweis: Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner ersetzt werden. Hier empfehlen wir Ihnen, den eindeutigen Wert der Zeichenfolge im Bezeichner zu verwenden. Navigieren Sie zum Abschnitt „GitHub Admin“ (GitHub-Verwaltung), um diese Werte abzurufen.

5. Die GitHub-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die GitHub-Anwendung erwartet, dass **nameidentifier** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol **Bearbeiten** klicken.

    ![image](common/edit-attribute.png)

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **GitHub einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-github-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für GitHub

1. Melden Sie sich in einem anderen Webbrowserfenster bei der GitHub-Organisationswebsite als Administrator an.

2. Navigieren Sie zu **Einstellungen**, und klicken Sie auf **Sicherheit**.

    ![Einstellungen](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Aktivieren Sie das Kontrollkästchen **SAML-Authentifizierung aktivieren**, um die Felder für die Konfiguration des einmaligen Anmeldens anzuzeigen. Verwenden Sie anschließend den Wert für die SSO-URL, um die URL für das einmalige Anmelden in der Azure AD-Konfiguration zu aktualisieren.

    ![Einstellungen](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Konfigurieren Sie die folgenden Felder:

    ![Einstellungen](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Fügen Sie im Textfeld **Sign-On URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Issuer** (Aussteller) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das aus dem Azure-Portal heruntergeladene Zertifikat im Editor, und fügen Sie es in das Textfeld **Public Certificate** (Öffentliches Zertifikat) ein.

    d. Klicken Sie auf das Symbol **Bearbeiten**, um die **Signaturmethode** und die **Digestmethode** von **RSA-SHA1** und **SHA1** in **RSA-SHA256** und **SHA256** wie unten gezeigt zu ändern.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klicken Sie auf **Test SAML configuration** (SAML-Konfiguration testen), um sich zu vergewissern, dass beim einmaligen Anmelden keine Validierungsfehler oder anderen Fehler auftreten.

    ![Einstellungen](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klicken Sie unten auf der Seite auf **Speichern**.

> [!NOTE]
> Einmaliges Anmelden auf GitHub führt die Authentifizierung für eine bestimmte Organisation in GitHub aus und ersetzt nicht die Authentifizierung von GitHub selbst. Wenn die github.com-Sitzung des Benutzers abgelaufen ist, werden Sie während des SSO-Prozesses möglicherweise aufgefordert, sich mit der ID und dem Kennwort für GitHub zu authentifizieren.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf GitHub, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **GitHub** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **GitHub** aus.

    ![GitHub-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-github-test-user"></a>Erstellen eines GitHub-Testbenutzers

In diesem Abschnitt wird in GitHub eine Benutzerin namens Britta Simon erstellt. GitHub unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](github-provisioning-tutorial.md).

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der GitHub-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **People**.

    ![Personen](./media/github-tutorial/tutorial_github_config_github_08.png "Personen")

3. Klicken Sie auf **Invite member** (Mitglied einladen).

    ![Benutzer einladen](./media/github-tutorial/tutorial_github_config_github_09.png "Benutzer einladen")

4. Führen Sie auf der Dialogfeldseite **Invite member** (Mitglied einladen) die folgenden Schritte aus:

    a. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Kontos von Britta Simon ein.

    ![Personen einladen](./media/github-tutorial/tutorial_github_config_github_10.png "Personen einladen")

    b. Klicken Sie auf **Einladung senden**.

    ![Personen einladen](./media/github-tutorial/tutorial_github_config_github_11.png "Personen einladen")

    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „GitHub“ klicken, sollten Sie automatisch bei der GitHub-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
