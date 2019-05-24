---
title: 'Tutorial: Azure Active Directory-Integration mit Palo Alto Networks - Aperture | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks - Aperture konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 375b58f47454a7f5904bcdbd132b8318091e9ebd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65870044"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Azure Active Directory-Integration mit Palo Alto Networks - Aperture

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks - Aperture in Azure Active Directory (Azure AD) integrieren.
Die Integration von Palo Alto Networks - Aperture in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Palo Alto Networks - Aperture hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Palo Alto Networks - Aperture anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Palo Alto Networks - Aperture konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Abonnement für Palo Alto Networks - Aperture, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Palo Alto Networks - Aperture unterstützt **SP-** und **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Hinzufügen von Palo Alto Networks - Aperture aus dem Katalog

Zum Konfigurieren der Integration von Palo Alto Networks - Aperture in Azure AD müssen Sie Palo Alto Networks - Aperture aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie zum Hinzufügen von Palo Alto Networks - Aperture aus dem Katalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Palo Alto Networks - Aperture** ein, wählen Sie im Ergebnisbereich **Palo Alto Networks - Aperture** aus, und klicken Sie auf **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Palo Alto Networks - Aperture in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Palo Alto Networks - Aperture mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks - Aperture eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Palo Alto Networks - Aperture müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks - Aperture](#configure-palo-alto-networks---aperture-single-sign-on)**, um die SSO-Einstellungen auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Palo Alto Networks - Aperture-Testbenutzers](#create-palo-alto-networks---aperture-test-user)**, um ein Pendant von Britta Simon in Palo Alto Networks - Aperture zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Palo Alto Networks - Aperture die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Palo Alto Networks - Aperture** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Palo Alto Networks - Aperture](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Palo Alto Networks - Aperture](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den Palo Alto Networks - Aperture-Client](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Palo Alto Networks - Aperture einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Palo Alto Networks - Aperture

1. Melden Sie sich in einem anderen Webbrowserfenster bei Palo Alto Networks - Aperture als Administrator an.

2. Klicken Sie auf der oberen Menüleiste auf **SETTINGS** (EINSTELLUNGEN).

    ![Registerkarte „Settings“ (Einstellungen)](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigieren Sie zum Abschnitt **APPLICATION** (ANWENDUNG), und klicken Sie links im Menü auf **Authentication** (Authentifizierung).

    ![Registerkarte „Authentication“ (Authentifizierung)](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Führen Sie auf der Seite **Authentication** (Authentifizierung) die folgenden Schritte aus:
    
    ![Registerkarte „Authentication“ (Authentifizierung)](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Aktivieren Sie im Feld **Single Sign-On** (Einmaliges Anmelden) die Option **Enable Single Sign-On (Supported SSP Providers are Okta, One login)** (Einmaliges Anmelden aktivieren (unterstützte SSP-Anbieter: Okta, Onelogin)).

    b. Fügen Sie in das Textfeld **Identity Provider ID** (ID des Identitätsanbieters) den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Choose File** (Datei auswählen), um das heruntergeladene Zertifikat aus Azure AD in das Feld **Identity Provider Certificate** (Identitätsanbieterzertifikat) hochzuladen.

    d. Fügen Sie in das Textfeld **Identity Provider SSO URL** (SSO-URL des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Überprüfen Sie die IdP-Informationen im Abschnitt **Aperture Info** (Infos zu Aperture), und laden Sie das Zertifikat aus dem Feld **Aperture Key** (Aperture-Schlüssel) herunter.

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
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Palo Alto Networks - Aperture gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Palo Alto Networks - Aperture** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Palo Alto Networks - Aperture** aus.

    ![Der Link für Palo Alto Networks - Aperture in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Erstellen eines Testbenutzers für Palo Alto Networks - Aperture

In diesem Abschnitt erstellen Sie in Palo Alto Networks - Aperture eine Benutzerin namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer auf der Palo Alto Networks - Aperture-Plattform ggf. vom [Supportteam für den Palo Alto Networks - Aperture-Client](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Palo Alto Networks - Aperture“ klicken, sollten Sie automatisch bei der Instanz für Palo Alto Networks - Aperture angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

