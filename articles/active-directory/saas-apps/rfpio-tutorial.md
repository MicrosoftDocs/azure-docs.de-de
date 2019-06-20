---
title: 'Tutorial: Azure Active Directory-Integration mit RFPIO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RFPIO konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176e0c80b4b1c70c3f49a35ae04d6233bc080f43
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687807"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Azure Active Directory-Integration mit RFPIO

In diesem Tutorial erfahren Sie, wie Sie RFPIO in Azure Active Directory (Azure AD) integrieren.
Die Integration von RFPIO in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf RFPIO hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei RFPIO anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit RFPIO konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* RFPIO-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* RFPIO unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-rfpio-from-the-gallery"></a>Hinzufügen von RFPIO aus dem Katalog

Zum Konfigurieren der Integration von RFPIO in Azure AD müssen Sie RFPIO aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um RFPIO aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **RFPIO** in das Suchfeld ein, wählen Sie im Ergebnisbereich den Eintrag **RFPIO** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![RFPIO in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei RFPIO mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RFPIO eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei RFPIO müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für RFPIO](#configure-rfpio-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines RFPIO-Testbenutzers](#create-rfpio-test-user)** , um eine Entsprechung von Britta Simon in RFPIO zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in RFPIO die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **RFPIO** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für RFPIO](common/idp-identifier.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.rfpio.com`

    b. Klicken Sie auf **Zusätzliche URLs festlegen**.

    c. Geben Sie im Textfeld **Relayzustand** einen Zeichenfolgenwert ein. Wenden Sie sich an das [Supportteam von RFPIO](https://www.rfpio.com/contact/), um diesen Wert zu erhalten.

    ![SSO-Informationen zur Domäne und zu den URLs für RFPIO](common/idp-preintegrated-relay.png)

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![image](common/both-preintegrated-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.app.rfpio.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von RFPIO](https://www.rfpio.com/contact/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **RFPIO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-rfpio-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für RFPIO

1. Melden Sie sich in einem anderen Webbrowserfenster bei der **RFPIO**-Website als Administrator an.

1. Klicken Sie in der linken unteren Ecke auf die Dropdownliste.

    ![Configure single sign-on](./media/rfpio-tutorial/app1.png)

1. Klicken Sie auf **Organization Settings** (Organisationseinstellungen). 

    ![Configure single sign-on](./media/rfpio-tutorial/app2.png)

1. Klicken Sie auf **FEATURES & INTEGRATION**.

    ![Configure single sign-on](./media/rfpio-tutorial/app4.png)

1. Klicken Sie unter **SAML SSO Configuration** (SAML-SSO-Konfiguration) auf **Edit** (Bearbeiten).

    ![Configure single sign-on](./media/rfpio-tutorial/app3.png)

1. Führen Sie in diesem Abschnitt folgende Aktionen durch:

    ![Configure single sign-on](./media/rfpio-tutorial/app5.png)
    
    a. Kopieren Sie den Inhalt der heruntergeladenen **XML-Metadatendatei**, und fügen Sie ihn in das Feld **identity configuration** (Identitätskonfiguration) ein.

    > [!NOTE]
    >Verwenden Sie zum Kopieren des Inhalts der heruntergeladenen **Verbundmetadaten-XML** das Programm **Notepad++** oder einen geeigneten **XML-Editor**.

    b. Klicken Sie auf **Überprüfen**.

    c. Schalten Sie nach dem Klicken auf **Überprüfen** die Einstellung **SAML(Enabled)** (SAML – aktiviert) auf „Ein“.

    d. Klicken Sie auf **Submit**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf RFPIO gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, wählen Sie **Alle Anwendungen** aus, und wählen Sie dann **RFPIO** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **RFPIO** aus.

    ![RFPIO-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-rfpio-test-user"></a>Erstellen eines RFPIO-Testbenutzers

1. Melden Sie sich bei der RFPIO-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der linken unteren Ecke auf die Dropdownliste.

    ![Configure single sign-on](./media/rfpio-tutorial/app1.png)

1. Klicken Sie auf **Organization Settings** (Organisationseinstellungen). 

    ![Configure single sign-on](./media/rfpio-tutorial/app2.png)

1. Klicken Sie auf **TEAM MEMBERS** (TEAMMITGLIEDER).

    ![Configure single sign-on](./media/rfpio-tutorial/app6.png)

1. Klicken Sie auf **ADD MEMBERS** (MITGLIEDER HINZUFÜGEN).

    ![Configure single sign-on](./media/rfpio-tutorial/app7.png)

1. Im Abschnitt **Add New Members** (Neue Mitglieder hinzufügen) Führen Sie folgende Aktionen aus:

    ![Configure single sign-on](./media/rfpio-tutorial/app8.png)

    a. Geben Sie die **E-Mail-Adresse** in das Feld **Enter one email per line** (eine E-Mail-Adresse pro Zeile eingeben) ein.

    b. Wählen Sie die **Rolle** entsprechend Ihren Anforderungen aus.

    c. Klicken Sie auf **ADD MEMBERS** (MITGLIEDER HINZUFÜGEN).

    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „RFPIO“ klicken, sollten Sie automatisch bei Ihrer RFPIO-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

