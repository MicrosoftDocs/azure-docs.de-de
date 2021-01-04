---
title: 'Tutorial: Azure Active Directory-Integration mit StatusPage | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und StatusPage konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: b138ab5a67de747480806c19f0d58014c41a1a77
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522052"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Azure Active Directory-Integration mit StatusPage

In diesem Tutorial erfahren Sie, wie Sie StatusPage in Azure Active Directory (Azure AD) integrieren.
Die Integration von StatusPage in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf StatusPage Zugriff hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei StatusPage angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit StatusPage konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* StatusPage-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* StatusPage unterstützt **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-statuspage-from-the-gallery"></a>Hinzufügen von StatusPage aus dem Katalog

Zum Konfigurieren der Integration von StatusPage in Azure AD müssen Sie StatusPage aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um StatusPage aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **StatusPage** ein, wählen Sie im Ergebnisbereich **StatusPage** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![StatusPage in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei StatusPage mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in StatusPage eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei StatusPage müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für StatusPage](#configure-statuspage-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines StatusPage-Testbenutzers](#create-statuspage-test-user)** , um eine Entsprechung von Britta Simon in StatusPage zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in StatusPage die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **StatusPage** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für StatusPage](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:

    - `https://<subdomain>.statuspagestaging.com/`
    - `https://<subdomain>.statuspage.io/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    - `https://<subdomain>.statuspagestaging.com/sso/saml/consume`
    - `https://<subdomain>.statuspage.io/sso/saml/consume`

    > [!NOTE]
    > Wenden Sie sich unter [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)an das Supportteam von StatusPage, um die für das Konfigurieren der einmaligen Anmeldung erforderlichen Metadaten anzufordern. 
    >
    > a. Kopieren Sie in den Metadaten den Wert für „Aussteller“, und fügen Sie ihn in das Textfeld **Bezeichner** ein.
    >
    > b. Kopieren Sie in den Metadaten den Wert für „Antwort-URL“, und fügen Sie ihn in das Textfeld **Antwort-URL** ein.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **StatusPage einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-statuspage-single-sign-on"></a>Konfigurieren von StatusPage für einmaliges Anmelden

1. Melden Sie sich in einem anderen Webbrowserfenster bei der StatusPage-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Hauptsymbolleiste auf **Konto verwalten** .

    ![Screenshot: Auswahl der Option „Konto verwalten“ auf der StatusPage-Unternehmenswebsite](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicken Sie auf die Registerkarte **Einmaliges Anmelden** .

    ![Screenshot: Registerkarte „Einmaliges Anmelden“](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Führen Sie auf der Seite für die SSO-Einrichtung die folgenden Schritte aus:

    ![Screenshot: Seite für die SSO-Einrichtung, auf der Sie die beschriebenen Werte eingeben können](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Screenshot: Schaltfläche „Konfiguration speichern“](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Fügen Sie in das Textfeld **SSO Target URL** (SSO-Ziel-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.

    c. Klicken Sie auf **KONFIGURATION SPEICHERN** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf StatusPage gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **StatusPage** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **StatusPage** aus.

    ![StatusPage-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-statuspage-test-user"></a>Erstellen eines StatusPage-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in StatusPage.

StatusPage unterstützt die Just-in-Time-Bereitstellung. Sie haben sie bereits unter [Konfigurieren der einmaligen Anmeldung in Azure AD](#configure-azure-ad-single-sign-on) aktiviert.

**Um einen Benutzer namens Britta Simon in StatusPage zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der StatusPage-Unternehmenswebsite als Administrator an.

1. Klicken Sie im Menü oben auf **Konto verwalten** .

    ![Screenshot: Auswahl der Option „Konto verwalten“ auf der StatusPage-Unternehmenswebsite](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicken Sie auf die Registerkarte **Teammitglieder** .
  
    ![Screenshot: Registerkarte „Teammitglieder“](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klicken Sie auf **TEAMMITGLIEDER HINZUFÜGEN** .
  
    ![Screenshot: Schaltfläche „Teammitglied hinzufügen“](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Geben Sie in die Textfelder **E-Mail-Adresse** , **Vorname** und **Nachname** die entsprechenden Informationen eines gültigen Benutzers ein, den Sie bereitstellen möchten. 

    ![Screenshot: Dialogfeld „Benutzer hinzufügen“, in dem Sie die beschriebenen Werte eingeben können](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Wählen Sie als **Rolle** die Option **Clientadministrator** aus.

1. Klicken Sie auf **KONTO ERSTELLEN** .

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „StatusPage“ klicken, sollten Sie automatisch bei der StatusPage-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)