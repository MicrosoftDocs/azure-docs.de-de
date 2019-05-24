---
title: 'Tutorial: Azure Active Directory-Integration mit SkyDesk Email | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SkyDesk Email konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: e0c2dc6c370e697f896e24e7d56c6eb8900601a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867115"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Azure Active Directory-Integration mit SkyDesk Email

In diesem Tutorial erfahren Sie, wie Sie SkyDesk Email in Azure Active Directory (Azure AD) integrieren.
Die Integration von SkyDesk Email in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf SkyDesk Email zugreifen kann.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SkyDesk Email anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SkyDesk Email konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein SkyDesk Email-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SkyDesk Email unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-skydesk-email-from-the-gallery"></a>Hinzufügen von SkyDesk Email aus dem Katalog

Zum Konfigurieren der Integration von SkyDesk Email in Azure AD müssen Sie SkyDesk Email aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Um SkyDesk Email aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SkyDesk Email** ein, wählen Sie im Ergebnisbereich **SkyDesk Email** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![SkyDesk Email in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SkyDesk Email mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SkyDesk Email eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit SkyDesk Email sind die folgenden Bausteine erforderlich:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAP SkyDesk Email](#configure-skydesk-email-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SkyDesk Email-Testbenutzers](#create-skydesk-email-test-user)**, um ein Pendant zu Britta Simon in SkyDesk Email zu erhalten, das mit ihrer Repräsentation in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei SkyDesk Email die folgenden Schritte aus:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SkyDesk Email** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SkyDesk Email](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den SkyDesk Email-Client](https://www.skydesk.jp/apps/support/), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **SkyDesk Email einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SkyDesk Email

1. Melden Sie sich in einem anderen Webbrowser als Administrator bei Ihrem SkyDesk Email-Konto an.

1. Klicken Sie im oberen Menü auf **Setup**, und wählen Sie **Org** aus.

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klicken Sie im linken Bereich auf **Domains** (Domänen).

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klicken Sie auf **Add Domain** (Domäne hinzufügen).

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Geben Sie den Namen Ihrer Domäne ein, und überprüfen Sie die Domäne dann.

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klicken Sie im linken Bereich auf **SAML Authentication** .

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Führen Sie auf der Dialogseite **SAML Authentication** die folgenden Schritte aus:

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Um die SAML-basierte Authentifizierung zu verwenden, sollten Sie entweder eine **überprüfte Domäne** oder eine **Portal-URL** eingerichtet haben. Sie können die Portal-URL mit dem eindeutigen Namen einrichten.

    ![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Fügen Sie in das Textfeld **Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Abmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. **Change Password URL** ist optional, lassen Sie dieses Feld leer.

    d. Klicken Sie auf **Schlüssel aus Datei abrufen**, um das heruntergeladene Zertifikat aus dem Azure-Portal auszuwählen, und klicken Sie dann auf **Öffnen**, um das Zertifikat hochzuladen.

    e. Wählen Sie als **Algorithmus** den Wert **RSA** aus.

    f. Klicken Sie zum Speichern der Änderungen auf **OK** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SkyDesk Email gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **SkyDesk Email** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SkyDesk Email** aus.

    ![Der SkyDesk Email-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-skydesk-email-test-user"></a>Erstellen eines SkyDesk Email-Testbenutzers

In diesem Abschnitt erstellen Sie in SkyDesk Email einen Benutzer mit dem Namen Britta Simon.

Klicken Sie in SkyDesk Email im linken Bereich auf **User Access**, und geben Sie Ihren Benutzernamen ein.

![Configure single sign-on](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Wenn Sie mehrere Benutzer erstellen müssen, setzen Sie sich mit dem [Supportteam für den SkyDesk Email-Client](https://www.skydesk.jp/apps/support/) in Verbindung.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SkyDesk Email“ klicken, sollten Sie automatisch bei Ihrer SkyDesk Email-Anwendung angemeldet werden, für die Sie einmaliges Anmelden einrichten. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

