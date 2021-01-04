---
title: 'Tutorial: Azure Active Directory-Integration mit Origami | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Origami konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 02c79e8385c7a7e9d60a3dcbed603ca94cb1dc43
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522264"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Azure Active Directory-Integration mit Origami

In diesem Tutorial erfahren Sie, wie Sie Origami in Azure Active Directory (Azure AD) integrieren.
Die Integration von Origami in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Origami hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Origami anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Origami konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Origami-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Origami unterstützt **SP-initiiertes** einmaliges Anmelden

## <a name="adding-origami-from-the-gallery"></a>Hinzufügen von Origami aus dem Katalog

Zum Konfigurieren der Integration von Origami in Azure AD müssen Sie Origami aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Origami aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Origami** ein, wählen Sie im Ergebnisbereich **Origami** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Origami in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Origami basierend auf einem Testbenutzer mit dem Namen **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Origami eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Origami müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Origami](#configure-origami-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Origami-Testbenutzers](#create-origami-test-user)** , um in Origami einen Gegenpart von Britta Simon zu erhalten, der mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Origami die folgenden Schritte aus:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Origami** auf **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Origami](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den Origami-Client](https://wordpress.org/support/theme/origami), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Origami einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-origami-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Origami

1. Melden Sie sich mit Administratorrechten beim Origami-Konto an.

2. Klicken Sie oben im Menü auf **Administrator** .
   
    ![Screenshot: Origami-Homepage mit Auswahl von „Administrator“](./media/origami-tutorial/tutorial_origami_51.png)

3. Führen Sie auf der Dialogfeldseite „Single Sign On Setup“ die folgenden Schritte aus:
   
    ![Screenshot: Seite „Single Sign On Setup“ (Setup für einmaliges Anmelden) mit Auswahl von „Enable Single Sign On“ (Einmaliges Anmelden aktivieren) und hervorgehobenen Textfeldern](./media/origami-tutorial/tutorial_origami_531.png)

    a. Wählen Sie **Enable Single Sign On** aus.

    b. Fügen Sie im Textfeld **Identity Provider‘s Sign-in Page URL** (URL der Anmeldeseite des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie im Textfeld **Identity Provider‘s Sign-out Page URL** (URL der Abmeldeseite des Identitätsanbieters) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Durchsuchen** , um das Zertifikat hochzuladen, das Sie zuvor aus dem Azure-Portal heruntergeladen haben.

    e. Klicken Sie auf **Änderungen speichern** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf Origami gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **Origami** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Origami** aus.

    ![Origami-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-origami-test-user"></a>Erstellen eines Origami-Testbenutzers

In diesem Abschnitt erstellen Sie in Origami einen Benutzer mit dem Namen Britta Simon. 

1. Melden Sie sich mit Administratorrechten beim Origami-Konto an.

2. Klicken Sie oben im Menü auf **Administrator** .
   
    ![Screenshot: Startseite des Origami-Kontos mit Auswahl von „Administrator“](./media/origami-tutorial/tutorial_origami_51.png)

3. Klicken Sie im Dialogfeld **Users and Security** (Benutzer und Sicherheit) auf **Users** (Benutzer).
   
    ![Screenshot: Dialogfeld „Users and Security“ (Benutzer und Sicherheit) mit Auswahl von „Users“ (Benutzer)](./media/origami-tutorial/tutorial_origami_54.png)

4. Klicken Sie auf **Add New User** .
   
    ![Screenshot: Auswahl der Schaltfläche „Add New User“ (Neuen Benutzer hinzufügen)](./media/origami-tutorial/tutorial_origami_55.png)

5. Führen Sie im Dialogfeld „Add New User“ folgende Schritte aus:
   
    ![Screenshot: Dialogfeld „Add New User“ (Neuen Benutzer hinzufügen) mit hervorgehobenen Textfeldern „User Name“ (Benutzername), „First Name“ (Vorname) und „Last Name“ (Nachname)](./media/origami-tutorial/tutorial_origami_56.png)

    a. Geben Sie im Textfeld **Username** (Benutzername) die E-Mail-Adresse des Benutzers ein, z. B. **BrittaSimon\@contoso.com** .

    b. Geben Sie im Textfeld **Kennwort** ein Kennwort ein.

    c. Geben Sie im Textfeld **Confirm Password** das Kennwort erneut ein.

    d. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (beispielsweise **Britta** ).

    e. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein (beispielsweise **Simon** ).

    f. Klicken Sie auf **Speichern** .
   
    ![Screenshot: Auswahl der Schaltfläche „Speichern“](./media/origami-tutorial/tutorial_origami_57.png)

6. Weisen Sie dem Benutzer unter **User Roles** (Benutzerrollen) entsprechende Benutzerrollen und unter **Client Access** (Clientzugriff) einen Benutzerzugriff zu. 
   
    ![Einmaliges Anmelden konfigurieren](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Origami“ klicken, sollten Sie automatisch bei Ihrer Origami-Anwendung angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)