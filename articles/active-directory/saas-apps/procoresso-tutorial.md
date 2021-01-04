---
title: 'Tutorial: Azure Active Directory-Integration mit Procore SSO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Procore SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515286"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Azure Active Directory-Integration mit Procore SSO

In diesem Tutorial erfahren Sie, wie Sie Procore SSO in Azure Active Directory (Azure AD) integrieren.
Die Integration von Procore SSO in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf Procore SSO zugreifen kann.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Procore SSO angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Procore SSO konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Procore SSO-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Procore SSO unterstützt **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-procore-sso-from-the-gallery"></a>Hinzufügen von Procore SSO aus dem Katalog

Zum Konfigurieren der Integration von Procore SSO in Azure AD müssen Sie Procore SSO aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Procore SSO aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Procore SSO** ein, wählen Sie im Ergebnisbereich **Procore SSO** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Procore SSO in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden über Azure AD bei Procore SSO mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Procore SSO eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens bei Procore SSO über Azure AD müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Procore SSO](#configure-procore-sso-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Procore SSO-Testbenutzers](#create-procore-sso-test-user)** , um eine Entsprechung von Britta Simon in Procore SSO zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens bei Procore SSO über Azure AD die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Procore SSO** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

    ![SSO-Informationen zur Domäne und zu den URLs für Procore SSO](common/preintegrated.png)

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Procore SSO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Procore SSO

1. Um das einmalige Anmelden aufseiten von **Procore SSO** zu konfigurieren, melden Sie sich als Administrator bei Ihrer Procore-Unternehmenswebsite an.

2. Klicken Sie auf der Dropdownliste der Toolbox auf **Admin** , um die Seite mit den Einstellungen für das einmalige Anmelden zu öffnen.

    ![Screenshot: Unternehmenswebsite von Procore mit Auswahl von „Directory“ (Verzeichnis)](./media/procoresso-tutorial/procore_tool_admin.png)

3. Fügen Sie die Werte wie unten beschrieben in die Felder ein.

    ![Screenshot: Dialogfeld „Add a Person“ (Person hinzufügen)](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. Fügen Sie im Textfeld **Single Sign On Issuer URL** (SSO-Aussteller-URL) den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie im Feld **SAML Sign On Target URL** (Ziel-URL für SAML-Anmeldung) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie jetzt die **Verbundmetadaten-XML-Datei** , die Sie aus dem Azure-Portal heruntergeladen haben, und kopieren Sie das Zertifikat in das Tag mit der Bezeichnung **X509Certificate** . Fügen Sie den kopierten Wert in das Feld **Single Sign On x509 Certificate** ein.

4. Klicken Sie auf **Save Changes** .

5. Nachdem Sie diese Einstellungen konfiguriert haben, müssen Sie den **Domänennamen** , mit dem Sie sich bei Procore anmelden (z.B. **contoso.com** ), an das [Procore-Supportteam](https://support.procore.com/) senden, damit das Team das einmalige Anmelden im Verbund für diese Domäne aktiviert.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Procore SSO gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **Procore SSO** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Procore SSO** aus.

    ![Link „Procore SSO“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-procore-sso-test-user"></a>Erstellen eines Procore SSO-Testbenutzers

Führen Sie die folgenden Schritte aus, um einen Procore-Testbenutzer aufseiten von Procore SSO zu erstellen.

1. Melden Sie sich als Administrator bei Ihrer Procore-Unternehmenswebsite an.    

2. Klicken Sie in der Dropdownliste der Toolbox auf **Directory** , um die Verzeichnisseite für Ihr Unternehmen zu öffnen.

    ![Screenshot: Unternehmenswebsite von Procore mit Auswahl von „Directory“ (Verzeichnis) in der Toolbox](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klicken Sie auf die Option **Add a Person** , um das Formular zu öffnen, und geben Sie die folgenden Optionen ein:

    ![Screenshot: Hinzufügen einer Person zu Boylan Construction und Eingabe von Benutzerinformationen](./media/procoresso-tutorial/Procore_user_add.png)

    a. Geben Sie im Textfeld **First Name** den Vornamen des Benutzers ein, z.B. **Britta** .

    b. Geben Sie im Textfeld **Last Name** den Nachnamen des Benutzers ein, z.B. **Simon** .

    c. Geben Sie im Textfeld **Email Address** die E-Mail-Adresse des Benutzers ein, z. B. BrittaSimon@contoso.com.

    d. Wählen Sie für **Permission Template** die Option **Apply Permission Template Later** aus.

    e. Klicken Sie auf **Erstellen** .

4. Prüfen Sie die Informationen für den neu hinzugefügten Kontakt, und aktualisieren Sie sie ggf.

    ![Screenshot: Bearbeitungsseite, auf der Sie die Benutzereinstellungen überprüfen können](./media/procoresso-tutorial/Procore_user_check.png)

5. Klicken Sie auf **Save and Send Invitiation** (wenn eine Einladung per E-Mail erforderlich ist) oder auf **Save** (zum direkten Speichern), um die Benutzerregistrierung abzuschließen.
    
    ![Screenshot: Aktuelle Projekteinstellungen mit Option zum Speichern und Senden einer Einladung](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Procore SSO“ klicken, sollten Sie automatisch bei der Procore SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)