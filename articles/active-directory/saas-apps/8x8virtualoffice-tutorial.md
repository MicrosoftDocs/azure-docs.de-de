---
title: 'Tutorial: Azure Active Directory-Integration mit 8x8 Virtual Office | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und 8x8 Virtual Office konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9385ec6a86c24e619ffafdae67bc66f66e099f3b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896979"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Azure Active Directory-Integration mit 8x8 Virtual Office

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) in 8x8 Virtual Office integrieren.
Diese Integration bietet folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf 8x8 Virtual Office haben soll.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihrem Azure AD-Konto automatisch bei 8x8 Virtual Office angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit 8x8 Virtual Office konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* 8x8 Virtual Office-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.


* 8x8 Virtual Office unterstützt **IDP**-initiiertes einmaliges Anmelden.

* 8x8 Virtual Office unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Hinzufügen von 8x8 Virtual Office über den Katalog

Zum Konfigurieren der Integration von 8x8 Virtual Office in Azure AD müssen Sie 8x8 Virtual Office über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um 8x8 Virtual Office über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **8x8 Virtual Office** ein, wählen Sie im Ergebnisbereich **8x8 Virtual Office** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![8 x 8 Virtual Office in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei 8x8 Virtual Office mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in 8x8 Virtual Office eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei 8x8 Virtual Office müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für 8x8 Virtual Office](#configure-8x8-virtual-office-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines 8x8 Virtual Office-Testbenutzers](#create-8x8-virtual-office-test-user)**, um in 8x8 Virtual Office eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens mit Azure AD bei 8x8 Virtual Office die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **8x8 Virtual Office** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für 8x8 Virtual Office](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://sso.8x8.com/saml2`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://sso.8x8.com/saml2`

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende Zertifikat (**Zertifikat (Rohdaten)**) aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificateraw.png)

6. Kopieren Sie im Abschnitt **8x8 Virtual Office einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für 8x8 Virtual Office

1. Melden Sie sich bei Ihrem 8x8 Virtual Office-Mandanten als Administrator an.

1. Wählen Sie im Anwendungsbereich die Option **Virtual Office Account Mgr** (Virtual Office-Konto-Manager) aus.

    ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Wählen Sie das Konto **Business** (Unternehmen) aus, und klicken Sie anschließend auf die Schaltfläche **Sign In** (Anmelden).

    ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klicken Sie auf der Menüleiste auf die Registerkarte **ACCOUNTS** (Konten).

   ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klicken Sie in der Liste unter „Accounts“ (Konten) auf **Single Sign On** (Einmaliges Anmelden).
  
   ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Wählen Sie als Authentifizierungsmethode die Option **Einmaliges Anmelden** aus, und klicken Sie anschließend auf **SAML**.

   ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Führen Sie im Abschnitt **SAML Single Sign-On** die folgenden Schritte aus:

   ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. Fügen Sie im Textfeld **Sign-In URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

   b. Fügen Sie im Textfeld **Sign-Out URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

   c. Fügen Sie in das Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

   d. Klicken Sie auf die Schaltfläche **Browse** (Durchsuchen), um das aus dem Azure-Portal heruntergeladene Zertifikat hochzuladen.

   e. Klicken Sie auf die Schaltfläche **Save** .

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf 8x8 Virtual Office gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **8x8 Virtual Office**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **8x8 Virtual Office** ein, und wählen Sie den Eintrag aus.

    ![8x8 Virtual Office-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-8x8-virtual-office-test-user"></a>Erstellen eines 8x8 Virtual Office-Testbenutzers

In diesem Abschnitt wird eine Benutzerin mit dem Namen „Britta Simon“ in 8x8 Virtual Office erstellt. 8x8 Virtual Office unterstützt die **Just-in-Time-Benutzerbereitstellung**, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn in 8x8 Virtual Office noch kein Benutzer vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem [Supportteam von 8x8 Virtual Office](https://www.8x8.com/about-us/contact-us) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „8x8 Virtual Office“ klicken, sollten Sie automatisch bei Ihrer 8x8 Virtual Office-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

