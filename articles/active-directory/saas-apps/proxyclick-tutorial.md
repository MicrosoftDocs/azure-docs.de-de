---
title: 'Tutorial: Azure Active Directory-Integration mit Proxyclick | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Proxyclick konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b797c7d49495aac90abb31a9214bf2928784a866
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65868646"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Azure Active Directory-Integration mit Proxyclick

In diesem Tutorial erfahren Sie, wie Sie Proxyclick in Azure Active Directory (Azure AD) integrieren.
Die Integration von Proxyclick in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Proxyclick hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Proxyclick anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Proxyclick konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Proxyclick-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Proxyclick unterstützt **SP-** und **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-proxyclick-from-the-gallery"></a>Hinzufügen von Proxyclick aus dem Katalog

Zum Konfigurieren der Integration von Proxyclick in Azure AD müssen Sie Proxyclick aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**So fügen Sie Proxyclick aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Proxyclick** ein, wählen Sie im Ergebnisbereich **Proxyclick** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Proxyclick in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Proxyclick anhand eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Proxyclick eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Proxyclick zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Proxyclick](#configure-proxyclick-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Proxyclick-Testbenutzers](#create-proxyclick-test-user)**, um eine Entsprechung von Britta Simon in Proxyclick zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligem Anmeldens von Azure AD mit Proxyclick die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Proxyclick** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Proxyclick](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://saml.proxyclick.com/init/<companyId>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://saml.proxyclick.com/consume/<companyId>`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Proxyclick](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL ersetzen. Darauf wird später im Tutorial eingegangen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Proxyclick einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Proxyclick

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Proxyclick-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Account & Settings** (Konto & Einstellungen) aus.

    ![Proxyclick-Konfiguration](./media/proxyclick-tutorial/configure1.png)

3. Scrollen Sie nach unten zu **INTEGRATIONS** (INTEGRATIONEN), und wählen Sie **SAML** (SAML) aus.

    ![Proxyclick-Konfiguration](./media/proxyclick-tutorial/configure2.png)

4. Führen Sie im Abschnitt **SAML** (SAML) die folgenden Schritte aus:

    ![Proxyclick-Konfiguration](./media/proxyclick-tutorial/configure3.png)

    a. Kopieren Sie den Wert aus **SAML Consumer URL** (SAML-Consumer-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Antwort-URL** ein.

    b. Kopieren Sie den Wert aus **SAML SSO Redirect URL** (Umleitungs-URL für SAML-SSO), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die Textfelder **Anmelde-URL** und **Bezeichner** ein.

    c. Wählen Sie als **SAML Request Method** (SAML-Anforderungsmethode) **HTTP Redirect** (HTTP-Umleitung) aus.

    d. Fügen Sie im Textfeld **Aussteller** den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie in das Textfeld **SAML 2.0 Endpoint URL** (SAML 2.0-Endpunkt-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Öffnen Sie im Editor das Zertifikat, das Sie im Azure-Portal heruntergeladen haben, und fügen Sie den Inhalt in das Textfeld **Zertifikat** ein.

    g. Klicken Sie auf **Änderungen speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Proxyclick, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Proxyclick** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Proxyclick** aus.

    ![Proxyclick-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-proxyclick-test-user"></a>Erstellen eines Proxyclick-Testbenutzers

Damit sich Azure AD-Benutzer bei Proxyclick anmelden können, müssen sie in Proxyclick bereitgestellt werden. Im Fall von Proxyclick muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich auf der Proxyclick-Unternehmenswebsite als Administrator an.

1. Klicken Sie oben in der Navigationsleiste auf **Colleagues** (Kollegen).

    ![Mitarbeiter hinzufügen](./media/proxyclick-tutorial/user1.png)

1. Klicken Sie auf **Add Colleague** (Kollegen hinzufügen).

    ![Mitarbeiter hinzufügen](./media/proxyclick-tutorial/user2.png)

1. Führen Sie im Abschnitt **Add a colleague** (Kollegen hinzufügen) die folgenden Schritte aus:

    ![Mitarbeiter hinzufügen](./media/proxyclick-tutorial/user3.png)

    a. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. brittasimon@contoso.com, ein.

    b. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein (beispielsweise „Britta“).

    c. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein (beispielsweise „Simon“).

    d. Klicken Sie auf **Benutzer hinzufügen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Proxyclick“ klicken, sollten Sie automatisch bei der Proxyclick-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

