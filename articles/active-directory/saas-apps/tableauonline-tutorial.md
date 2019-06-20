---
title: 'Tutorial: Azure Active Directory-Integration von Tableau Online | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Tableau Online konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 352ad9473a1c1a9360ddceb720ff968f4e97e012
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65889241"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Azure Active Directory-Integration von Tableau Online

In diesem Tutorial erfahren Sie, wie Sie Tableau Online in Azure Active Directory (Azure AD) integrieren.
Die Integration von Tableau Online in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Tableau Online hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Tableau Online anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Tableau Online konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Tableau Online-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Tableau Online unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-tableau-online-from-the-gallery"></a>Hinzufügen von Tableau Online aus dem Katalog

Zum Konfigurieren der Integration von Tableau Online in Azure AD müssen Sie Tableau Online aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Tableau Online aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Tableau Online** ein, wählen Sie im Ergebnisfenster **Tableau Online** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Tableau Online in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Tableau Online basierend auf einem Testbenutzer namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Tableau Online eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Tableau Online müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Tableau Online](#configure-tableau-online-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Tableau Online-Testbenutzers](#create-tableau-online-test-user)** , um eine Entsprechung von Britta Simon in Tableau Online zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Tableau Online die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Tableau Online** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Tableau Online](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`.

    > [!NOTE]
    > Den Wert für `<entityid>` erhalten Sie in diesem Tutorial im Abschnitt **Tableau Online einrichten**. Der Wert der Entitäts-ID entspricht dem **Azure AD-Bezeichner** im Abschnitt **Tableau Online einrichten**.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Tableau Online einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-tableau-online-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Tableau Online

1. Melden Sie sich in einem anderen Browserfenster in Ihrer Tableau Online-Anwendung als Administrator an. Wechseln Sie zu **Einstellungen** und dann zu **Authentifizierung**.

    ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Gehen Sie im Abschnitt **Authentifizierungstypen** zum Aktivieren von SAML wie folgt vor. Aktivieren Sie die Option **Enable an additional authentication method** (Zusätzliche Authentifizierungsmethode aktivieren) und anschließend das Kontrollkästchen **SAML**.

    ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Scrollen Sie nach unten bis zum Abschnitt **Import metadata file into Tableau Online** (Metadatendatei in Tableau Online importieren).  Klicken Sie auf „Durchsuchen“, und importieren Sie die Metadatendatei, die Sie aus Azure AD heruntergeladen haben. Klicken Sie anschließend auf **Übernehmen**.

   ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Fügen Sie im Abschnitt **Match assertions** (Assertionen abgleichen) den entsprechenden Identitätsanbieter-Assertionsnamen für **E-Mail-Adresse**, **Vorname** und **Nachname** ein. Gehen Sie wie folgt vor, um diese Informationen aus Azure AD abzurufen: 
  
    a. Wechseln Sie im Azure-Portal zur Anwendungsintegrationsseite von **Tableau Online**.

    b. Klicken Sie im Abschnitt **Benutzerattribute und Ansprüche** auf das Bearbeitungssymbol.

   ![Configure single sign-on](./media/tableauonline-tutorial/attributesection.png)

    c. Kopieren Sie den Namespacewert für die Attribute „givenname“, „email“ und „surname“ mithilfe der folgenden Schritte:

   ![Azure AD – einmaliges Anmelden](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klicken Sie auf den Wert **user.givenname**.

    e. Kopieren Sie den Wert aus dem Textfeld **Namespace**.

    ![Configure single sign-on](./media/tableauonline-tutorial/attributesection2.png)

    f. Befolgen Sie die vorherigen Anweisungen, um die Namespacewerte für „email“ und „surname“ zu kopieren.

    g. Wechseln Sie zur Tableau Online-Anwendung, und legen Sie den Abschnitt **User Attributes & Claim** (Benutzerattribute und Ansprüche) dann wie folgt fest:

    * E-Mail: **mail** oder **userprincipalname**

    * Vorname: **givenname**

    * Nachname: **surname**

    ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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
    Beispiel: BrittaSimon\@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Tableau Online gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Tableau Online** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Tableau Online**aus.

    ![Tableau Online-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-tableau-online-test-user"></a>Erstellen eines Tableau Online-Testbenutzers

In diesem Abschnitt erstellen Sie in Tableau Online einen Benutzer namens Britta Simon.

1. Klicken Sie in **Tableau Online** auf **Settings** (Einstellungen) und dann auf den Abschnitt **Authentication** (Authentifizierung). Scrollen Sie nach unten zum Abschnitt **Manage Users** (Benutzer verwalten). Klicken Sie auf **Add Users** (Benutzer hinzufügen) und dann auf **Enter Email Addresses** (E-Mail-Adressen eingeben).
  
    ![Erstellen eines Azure AD-Testbenutzers](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Wählen Sie **Add users for (SAML) authentication** (Benutzer für (SAML-)Authentifizierung auswählen). Fügen Sie im Textfeld **Enter Email Addresses** (E-Mail-Adressen eingeben) den Eintrag „britta.simon\@contoso.com“ hinzu.
  
    ![Erstellen eines Azure AD-Testbenutzers](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klicken Sie auf **Benutzer hinzufügen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Tableau Online“ klicken, sollten Sie automatisch bei der Tableau Online-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
