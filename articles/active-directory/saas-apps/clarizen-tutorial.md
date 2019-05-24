---
title: 'Tutorial: Azure Active Directory-Integration mit Clarizen | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Clarizen konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48955354baf86d84c0ddc6618a99f7e806c26c2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65862374"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: Azure Active Directory-Integration mit Clarizen

In diesem Tutorial erfahren Sie, wie Sie Clarizen in Azure Active Directory (Azure AD) integrieren.
Die Integration von Clarizen in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf Clarizen zugreifen kann.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Clarizen anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Clarizen konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Clarizen-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Clarizen unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-clarizen-from-the-gallery"></a>Hinzufügen von Clarizen aus dem Katalog

Zum Konfigurieren der Integration von Clarizen in Azure AD müssen Sie Clarizen über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Clarizen aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Clarizen** ein, wählen Sie im Ergebnisbereich **Clarizen** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Clarizen in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Clarizen mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Clarizen eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Clarizen müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Clarizen](#configure-clarizen-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Clarizen-Testbenutzers](#create-clarizen-test-user)**, um eine Entsprechung von Britta Simon in Clarizen zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Clarizen die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Clarizen** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Clarizen](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** einen Wert ein: `Clarizen`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen die tatsächlichen Werte für Bezeichner und Antwort-URL verwenden. Hier empfehlen wir Ihnen, den eindeutigen Wert einer Zeichenfolge als Bezeichner zu verwenden. Um die tatsächlichen Werte zu erhalten, wenden Sie sich an das [Clarizen-Supportteam](https://success.clarizen.com/hc/en-us/requests/new).

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Clarizen einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-clarizen-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Clarizen

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Clarizen-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf Ihren Benutzernamen und dann auf **Settings**.

    ![Klicken auf „Settings“ unter Ihrem Benutzernamen](./media/clarizen-tutorial/tutorial_clarizen_001.png "Settings")

1. Klicken Sie auf die Registerkarte **Global Settings**. Klicken Sie anschließend neben **Federated Authentication** auf **edit**.

    ![Registerkarte „Global Settings“](./media/clarizen-tutorial/tutorial_clarizen_002.png "Global Settings")

1. Führen Sie im Dialogfeld **Federated Authentication** die folgenden Schritte aus:

    ![Dialogfeld „Federated Authentication“](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federated Authentication")

    a. Wählen Sie **Enable Federated Authentication** (Verbundauthentifizierung aktivieren).

    b. Klicken Sie auf **Upload** , um das heruntergeladene Zertifikat hochzuladen.

    c. Geben Sie im Feld **Sign-in URL** (Anmelde-URL) den Wert für die **Anmelde-URL** aus dem Konfigurationsfenster der Azure AD-Anwendung ein.

    d. Geben Sie im Feld **Sign-out URL** (Abmelde-URL) den Wert für die **Anmelde-URL** aus dem Konfigurationsfenster der Azure AD-Anwendung ein.

    e. Aktivieren Sie **Use POST**.

    f. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Clarizen gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Clarizen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Clarizen** aus.

    ![Clarizen-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-clarizen-test-user"></a>Erstellen eines Clarizen-Testbenutzers

In diesem Abschnitt wird in Clarizen eine Benutzerin namens Britta Simon erstellt.

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

Um für Azure AD-Benutzer das Anmelden bei Clarizen zu aktivieren, müssen Sie Benutzerkonten bereitstellen. Im Fall von Clarizen ist die Bereitstellung eine manuelle Aufgabe.

1. Melden Sie sich bei der Clarizen-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **People**.

    ![Klicken Sie auf „People“](./media/clarizen-tutorial/create_aaduser_001.png "Personen")

3. Klicken Sie auf **Invite User**.

    ![Schaltfläche „Invite User“](./media/clarizen-tutorial/create_aaduser_002.png "Benutzer einladen")

1. Führen Sie auf der Dialogfeldseite **Invite People** die folgenden Schritte aus:

    ![Dialogfeld „Invite People“](./media/clarizen-tutorial/create_aaduser_003.png "Personen einladen")

    a. Geben Sie im Feld **E-Mail** die E-Mail-Adresse des Kontos von Britta Simon ein.

    b. Klicken Sie auf **Einladen**.

    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.


### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Clarizen“ klicken, sollten Sie automatisch bei der Clarizen-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
