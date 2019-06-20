---
title: 'Tutorial: Integration von Azure Active Directory in LinkedIn Sales Navigator | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und LinkedIn Sales Navigator konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a216e720523767f428036290aea7151c2dca34
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708167"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Tutorial: Integration von Azure Active Directory in LinkedIn Sales Navigator

In diesem Tutorial erfahren Sie, wie Sie LinkedIn Sales Navigator in Azure Active Directory (Azure AD) integrieren.
Die Integration von LinkedIn Sales Navigator in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf LinkedIn Sales Navigator hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei LinkedIn Sales Navigator anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Integration von Azure AD in LinkedIn Sales Navigator konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* LinkedIn Sales Navigator-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LinkedIn Sales Navigator unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

* LinkedIn Sales Navigator unterstützt die **Just-in-Time**-Benutzerbereitstellung.

* LinkedIn Sales Navigator unterstützt die [**automatisierte** Benutzerbereitstellung](linkedinsalesnavigator-provisioning-tutorial.md).

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Hinzufügen von LinkedIn Sales Navigator aus dem Katalog

Zum Konfigurieren der Integration von LinkedIn Sales Navigator in Azure AD müssen Sie LinkedIn Sales Navigator aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Um LinkedIn Sales Navigator aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **LinkedIn Sales Navigator** ein, wählen Sie im Ergebnisbereich **LinkedIn Sales Navigator** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![LinkedIn Sales Navigator in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit LinkedIn Sales Navigator mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LinkedIn Sales Navigator eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens bei LinkedIn Sales Navigator über Azure AD müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für LinkedIn Sales Navigator](#configure-linkedin-sales-navigator-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines LinkedIn Sales Navigator-Testbenutzers](#create-linkedin-sales-navigator-test-user)** , um eine Entsprechung von Britta Simon in LinkedIn Sales Navigator zu erhalten, die ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit LinkedIn Sales Navigator die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **LinkedIn Sales Navigator** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für LinkedIn Sales Navigator](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** den Wert für **Entitäts-ID** ein, den Sie im Linkedin-Portal kopieren (weiter unten in diesem Tutorial erläutert).

    b. Geben Sie im Textfeld **Antwort-URL** den Wert für **Assertion Consumer Access (ACS)-URL** ein, den Sie im Linkedin-Portal kopieren (weiter unten in diesem Tutorial erläutert).

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für LinkedIn Sales Navigator](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

6. Die LinkedIn Sales Navigator-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die LinkedIn Sales Navigator-Anwendung erwartet, dass „nameidentifier“ der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol „Bearbeiten“ klicken.

    ![image](common/edit-attribute.png)

7. Darüber hinaus wird von der LinkedIn Sales Navigator-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt „Benutzeransprüche“ die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | NAME | Quellattribut|
    | --- | --- |
    | E-Mail| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **LinkedIn Sales Navigator einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für LinkedIn Sales Navigator

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer **LinkedIn Sales Navigator**-Website als Administrator an.

1. Klicken Sie im **Kontocenter** unter **Einstellungen** auf **Globale Einstellungen**. Wählen Sie außerdem **Sales Navigator** aus der Dropdownliste aus.

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Klicken Sie auf **OR Click Here to load and copy individual fields from the form** (ODER klicken Sie hier, um einzelne Felder aus dem Formular zu laden und zu kopieren), und führen Sie die folgenden Schritte aus:

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Kopieren Sie den Wert im Feld **Entity Id** (Entitäts-ID), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    b. Kopieren Sie den Wert im Feld **Assertion Consumer Access (ACS) Url** (Assertion Consumer Access-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

1. Wechseln Sie zum Abschnitt **LinkedIn-Administratoreinstellungen**. Klicken Sie auf **Upload XML file** (XML-Datei hochladen), um die XML-Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Klicken Sie auf **Ein**, um das einmalige Anmelden zu aktivieren. Der Status des einmaligen Anmeldens wechselt von **Nicht verbunden** zu **Verbunden**.

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

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

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf LinkedIn Sales Navigator gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **LinkedIn Sales Navigator** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **LinkedIn Sales Navigator** aus.

    ![LinkedIn Sales Navigator-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-linkedin-sales-navigator-test-user"></a>Erstellen eines LinkedIn Sales Navigator-Testbenutzers

Die LinkedIn Sales Navigator-Anwendung unterstützt Just-in-Time(JIT)-Benutzerbereitstellungen. Nach der Authentifizierung werden Benutzer in der Anwendung automatisch erstellt. Aktivieren Sie **Lizenzen automatisch zuweisen**, um dem Benutzer eine Lizenz zuzuweisen.

   ![Erstellen eines Azure AD-Testbenutzers](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „LinkedIn Sales Navigator“ klicken, sollten Sie automatisch bei der LinkedIn Sales Navigator-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

