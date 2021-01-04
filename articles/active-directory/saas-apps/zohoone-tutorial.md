---
title: 'Tutorial: Azure Active Directory-Integration in Zoho One| Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Zoho One konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 98c9012969051e4370239a0f4098e0ea1d7a55a0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348312"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Azure Active Directory-Integration in Zoho One

In diesem Tutorial erfahren Sie, wie Sie Zoho One in Azure Active Directory (Azure AD) integrieren.
Die Integration von Zoho One in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Zoho One hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zoho One anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zoho One konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Zoho One-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zoho One unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-zoho-one-from-the-gallery"></a>Hinzufügen von Zoho One aus dem Katalog

Zum Konfigurieren der Integration von Zoho One in Azure AD müssen Sie Zoho One aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**So fügen Sie Zoho One aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Zoho One** ein, wählen Sie im Ergebnisbereich **Zoho One** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Zoho One in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Zoho One anhand einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zoho One eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Zoho One zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zoho One](#configure-zoho-one-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Zoho One-Testbenutzers](#create-zoho-one-test-user)** , um eine Entsprechung von Britta Simon in Zoho One zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Zoho One die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zoho One** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![Screenshot: Seite „Grundlegende SAML-Konfiguration“ zum Eingeben des Bezeichners und einer Antwort-URL sowie zum Klicken auf „Speichern“](common/idp-relay.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL ein: `one.zoho.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Der vorherige Wert für die **Antwort-URL** entspricht nicht dem tatsächlichen Wert. Den Wert `<saml-identifier>` erhalten Sie in Schritt 4 des Abschnitts **Konfigurieren des einmaligen Anmeldens für Zoho One**. Dies wird weiter unten in diesem Tutorial beschrieben.

    c. Klicken Sie auf **Zusätzliche URLs festlegen**.

    d. Geben Sie im Textfeld **Relayzustand** eine URL ein: `https://one.zoho.com`

5. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/both-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Der vorherige Wert für die **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Sie müssen den Wert durch die tatsächliche Anmelde-URL im Abschnitt **Konfigurieren des einmaligen Anmeldens für Zoho One** ersetzen. Dies wird weiter unten in diesem Tutorial beschrieben. 

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Zoho One einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zoho One

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Zoho One-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Registerkarte **Organisation** (Organisation) unter **SAML Authentication** (SAML-Authentifizierung) auf **Setup** (Einrichten).

    ![Organisation in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Führen Sie auf der Popupseite die folgenden Schritte aus:

    ![Anmelden und Abmelden in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Fügen Sie in das Textfeld **Sign-in URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Sign-out URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Browse** (Durchsuchen), um das **Zertifikat (Base64)** hochzuladen, das Sie zuvor aus dem Azure-Portal heruntergeladen haben.

    d. Klicken Sie auf **Speichern**.

4. Nachdem Sie die eingerichtete SAML-Authentifizierung gespeichert haben, kopieren Sie den **SAML-Identifier**-Wert und fügen die **Antwort-URL** anstelle von `<saml-identifier>` an (beispielsweise `https://accounts.zoho.com/samlresponse/one.zoho.com`). Anschließend fügen Sie den erzeugten Wert im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    ![SAML in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Öffnen Sie die Registerkarte **Domains** (Domänen), und klicken Sie auf **Add Domain** (Domäne hinzufügen).

    ![Domäne in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Führen Sie auf der Seite **Add Domain** (Domäne hinzufügen) die folgenden Schritte aus:

    ![Hinzufügen einer Domäne in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. Geben Sie im Textfeld **Domain Name** (Domänennamen) eine Domäne ein, z. B. „contoso.com“.

    b. Klicken Sie auf **Hinzufügen**.

    >[!Note]
    >Überprüfen Sie Ihre Domäne nach dem Hinzufügen mithilfe [dieser Schritte](https://www.zoho.com/one/help/admin-guide/domain-verification.html). Nachdem die Domäne überprüft wurde, geben Sie Ihren Domänennamen im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** unter **Anmelde-URL** ein.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Zoho One, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Zoho One** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Zoho One** aus.

    ![Zoho One-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-zoho-one-test-user"></a>Erstellen eines Zoho One-Testbenutzers

Damit sich Azure AD-Benutzer bei Zoho One anmelden können, müssen sie in Zoho One bereitgestellt werden. Im Fall von Zoho One muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Zoho One als Sicherheitsadministrator an.

2. Klicken Sie auf der Registerkarte **Users** (Benutzer) auf das **Benutzerlogo**.

    ![Benutzer in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Führen Sie auf der Seite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Hinzufügen eines Benutzers in Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Geben Sie im Textfeld **Name** (Name) den Namen des Benutzers ein, z.B. **Britta Simon**.
    
    b. Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. brittasimon@contoso.com.

    >[!Note]
    >Wählen Sie aus der Domänenliste Ihre überprüfte Domäne aus.

    c. Klicken Sie auf **Hinzufügen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zoho One“ klicken, sollten Sie automatisch bei Ihrer Zoho One-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)