---
title: 'Tutorial: Azure Active Directory-Integration mit BlueJeans | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und BlueJeans konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2830e95f042d5c83d91d6c026d2fa2e70eb3e3b2
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901101"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Azure Active Directory-Integration mit BlueJeans

In diesem Tutorial erfahren Sie, wie Sie BlueJeans in Azure Active Directory (Azure AD) integrieren.
Die Integration von BlueJeans in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf BlueJeans hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei BlueJeans anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit BlueJeans konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* BlueJeans-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* BlueJeans unterstützt **SP**-initiiertes einmaliges Anmelden.

* BlueJeans unterstützt die [**automatisierte** Benutzerbereitstellung](bluejeans-provisioning-tutorial.md).

## <a name="adding-bluejeans-from-the-gallery"></a>Hinzufügen von BlueJeans aus dem Katalog

Zum Konfigurieren der Integration von BlueJeans in Azure AD müssen Sie BlueJeans aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um BlueJeans aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **BlueJeans** ein, wählen Sie im Ergebnisbereich **BlueJeans** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![BlueJeans in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei BlueJeans basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in BlueJeans eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei BlueJeans müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für BlueJeans](#configure-bluejeans-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines BlueJeans-Testbenutzers](#create-bluejeans-test-user)**, um ein Pendant von Britta Simon in BlueJeans zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei BlueJeans die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **BlueJeans** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. Geben Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Werte ein:

    ![SSO-Informationen zur Domäne und zu den URLs für BlueJeans](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - Geben Sie im Textfeld **Bezeichner** Folgendes ein: `http://samlsp.bluejeans.com`
    
   - Geben Sie im Textfeld **Anmelde-URL** die URL der Landing Page ein, die Sie von BlueJeans erhalten haben. (Sie erhalten diesen Wert vom [Supportteam für den BlueJeans-Client](https://support.bluejeans.com/contact)): `https://<companyname>.bluejeans.com`.
    
   - Klicken Sie auf **Speichern**.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **BlueJeans einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-bluejeans-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für BlueJeans

1. Melden Sie sich in einem anderen Webbrowserfenster bei der **BlueJeans**-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **ADMIN \> GROUP SETTINGS \> SECURITY** (ADMINISTRATOR > GRUPPENEINSTELLUNGEN > SICHERHEIT).

    ![Admin](./media/bluejeans-tutorial/ic785868.png "Admin")

3. Führen Sie im Abschnitt **SECURITY** (SICHERHEIT) die folgenden Schritte aus:

    ![Einmaliges Anmelden für SAML](./media/bluejeans-tutorial/ic785869.png "Einmaliges Anmelden für SAML")

    a. Wählen Sie **SAML Single Sign On**aus.

    b. Wählen Sie **Enable automatic provisioning**aus.

4. Führen Sie die folgenden Schritte aus:

    ![Zertifikatpfad](./media/bluejeans-tutorial/ic785870.png "Zertifikatpfad")

    a. Klicken Sie auf **Choose File** (Datei auswählen), um das Base64-codierte Zertifikat hochzuladen, das Sie über das Azure-Portal heruntergeladen haben.

    b. Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Password Change URL** (URL für Kennwortänderung) den Wert der **URL für Kennwortänderung** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Logout URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

5. Führen Sie die folgenden Schritte aus:

    ![Änderungen speichern](./media/bluejeans-tutorial/ic785874.png "Änderungen speichern")

    a. Geben Sie in das Textfeld **Benutzer-ID** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.

    b. Geben Sie in das Textfeld **E-Mail** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.

    c. Klicken Sie auf **ÄNDERUNGEN SPEICHERN**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon\@yourcompanydomain.extension` ein. Beispiel: BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf BlueJeans gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **BlueJeans** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **BlueJeans** aus.

    ![BlueJeans-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-bluejeans-test-user"></a>Erstellen eines BlueJeans-Testbenutzers

In diesem Abschnitt wird in BlueJeans eine Benutzerin namens Britta Simon erstellt. BlueJeans unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](bluejeans-provisioning-tutorial.md).

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der **BlueJeans**-Unternehmenswebsite als Administrator an.

2. Navigieren Sie zu **ADMIN \> MANAGE USERS \> ADD USER** (ADMINISTRATOR > BENUTZER VERWALTEN > BENUTZER HINZUFÜGEN).

    ![Admin](./media/bluejeans-tutorial/ic785877.png "Admin")

    > [!IMPORTANT]
    > Die Registerkarte **ADD USER** (BENUTZER HINZUFÜGEN) ist nur verfügbar, wenn auf der Registerkarte **SECURITY** (SICHERHEIT) die Option **Enable automatic provisioning** (Automatische Bereitstellung aktivieren) deaktiviert ist.

3. Führen Sie im Abschnitt **ADD USER** (BENUTZER HINZUFÜGEN) die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/bluejeans-tutorial/ic785886.png "Benutzer hinzufügen")

    a. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein, z.B. **Britta**.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon**.

    c. Geben Sie im Textfeld **Pick a BlueJeans Username** (BlueJeans-Benutzernamen auswählen) den Namen eines Benutzers ein, etwa **Brittasimon**

    d. Geben Sie in das Textfeld **Create a Password** (Kennwort erstellen) Ihr Kennwort ein.

    e. Geben Sie im Textfeld **Company** (Unternehmen) Ihr Unternehmen ein.

    f. Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. `brittasimon\@contoso.com`.

    g. Geben Sie im Textfeld **Create a BlueJeans Meeting I.D** (BlueJeans-Meeting-ID erstellen) Ihre Meeting-ID ein.

    h. Geben Sie im Textfeld **Pick a Moderator Passcode** (Moderatorenkennung auswählen) Ihre Kennung ein.

    i. Klicken Sie auf **CONTINUE** (WEITER).

    ![Benutzer hinzufügen](./media/bluejeans-tutorial/ic785887.png "Benutzer hinzufügen")

    J. Klicken Sie auf **BENUTZER HINZUFÜGEN**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von BlueJeans-Benutzerkonten oder mithilfe von APIs bereitstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „BlueJeans“ klicken, sollten Sie automatisch bei Ihrer BlueJeans-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
