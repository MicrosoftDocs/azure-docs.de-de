---
title: 'Tutorial: Azure Active Directory-Integration mit Image Relay | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Image Relay konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2d41af8fa04b03ab8d18277d377f3700575cd1
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898157"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Tutorial: Azure Active Directory-Integration mit Image Relay

In diesem Tutorial erfahren Sie, wie Sie Image Relay in Azure Active Directory (Azure AD) integrieren.
Diese Integration von Image Relay bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Image Relay hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Image Relay anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration von Image Relay konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Image Relay-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Image Relay unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-image-relay-from-the-gallery"></a>Hinzufügen von Image Relay aus dem Katalog

Zum Konfigurieren der Integration von Image Relay in Azure AD müssen Sie Image Relay aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Image Relay aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Image Relay** ein, wählen Sie im Ergebnisbereich **Image Relay** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Image Relay in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Image Relay basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Image Relay eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei Image Relay müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Image Relay](#configure-image-relay-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Image Relay-Testbenutzers](#create-image-relay-test-user)**, um ein Pendant von Britta Simon in Image Relay zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Image Relay die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Image Relay** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Image Relay](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.imagerelay.com/`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<companyname>.imagerelay.com/sso/metadata`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Image Relay](http://support.imagerelay.com/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Image Relay einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-image-relay-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Image Relay

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer Image Relay-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Symbolleiste oben auf die Workload **Users & Permissions** (Benutzer und Berechtigungen).

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

3. Klicken Sie auf **Create New Permission**(Neue Berechtigung erstellen).

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

4. Aktivieren Sie in der Workload **Single Sign On Settings** (Einstellungen für einmaliges Anmelden) das Kontrollkästchen **This Group can only sign-in via Single Sign On** (Diese Gruppe kann sich nur per einmaligem Anmelden anmelden), und klicken Sie dann auf **Speichern**.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

5. Wechseln Sie zu **Kontoeinstellungen**.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

6. Klicken Sie auf die Workload **Single Sign On Settings** (Einstellungen für einmaliges Anmelden).

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

7. Führen Sie im Dialogfeld **SAML Settings** (SAML-Einstellungen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)

    a. Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Logout URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Wählen Sie unter **Name Id Format** (Format der Namens-ID) die Option **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** aus.

    d. Wählen Sie unter **Binding Options for Requests from the Service Provider (Image Relay)** (Bindungsoptionen für Anforderungen vom Dienstanbieter (Image Relay)) die Option **POST Binding** (POST-Bindung) aus.

    e. Klicken Sie unter **x.509-Zertifikat** auf **Zertifikat aktualisieren**.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **x.509 Certificate** (x.509-Zertifikat) ein.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Aktivieren Sie im Abschnitt **Just-In-Time User Provisioning** (Just-In-Time-Benutzerbereitstellung) das Kontrollkästchen **Enable Just-In-Time User Provisioning** (Just-In-Time-Benutzerbereitstellung aktivieren).

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Wählen Sie die Berechtigungsgruppe aus (z.B. **SSO Basic** (SSO allgemein)), bei der die Anmeldung nur per einmaligem Anmelden erfolgen soll.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Image Relay gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Image Relay** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Image Relay** aus.

    ![Image Relay-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-image-relay-test-user"></a>Erstellen eines Image Relay-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in Image Relay.

**Um eine Benutzerin namens Britta Simon in Image Relay zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer Image Relay-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Users & Permissions** (Benutzer und Berechtigungen), und klicken Sie auf **Create SSO User** (SSO-Benutzer erstellen).

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Geben Sie die **E-Mail-Adresse**, den **Vornamen**, den **Nachnamen** und das **Unternehmen** des bereitzustellenden Benutzers an. Wählen Sie außerdem die Berechtigungsgruppe aus (z.B. „SSO Basic“). Dies ist die Gruppe, die sich nur per einmaligem Anmelden anmelden kann.

    ![Configure single sign-on](./media/imagerelay-tutorial/tutorial_imagerelay_22.png)

4. Klicken Sie auf **Create**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Image Relay“ klicken, sollten Sie automatisch bei der Image Relay-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)