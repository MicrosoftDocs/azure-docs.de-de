---
title: 'Tutorial: Azure Active Directory-Integration in Mitel Connect | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Mitel Connect konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 611867cf5606d5a1078706e1c0f67f673a7fa500
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254561"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Tutorial: Azure Active Directory-Integration in Mitel MiCloud Connect

In diesem Tutorial erfahren Sie, wie Sie Mitel MiCloud Connect in Azure Active Directory (Azure AD) integrieren. Die Integration von MiCloud Connect in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, welche Benutzer mit ihren Unternehmensanmeldeinformationen auf MiCloud Connect-Apps zugreifen können.
* Sie können Benutzern in Ihrem Konto ermöglichen, sich mit ihren Azure AD-Konten automatisch bei MiCloud Connect anzumelden (einmaliges Anmelden; Single Sign-On, SSO).


Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in MiCloud Connect zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement

  Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Mitel MiCloud Connect-Konto

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden (SSO) von Azure AD.

* Mitel Connect unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-mitel-connect-from-the-gallery"></a>Hinzufügen von Mitel Connect aus dem Katalog

Zum Konfigurieren der Integration von Mitel Connect in Azure AD müssen Sie Mitel Connect im Azure-Portal aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Mitel Connect aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie auf **Neue Anwendung**.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Mitel Connect** in das Suchfeld ein, klicken Sie im Ergebnisbereich auf **Mitel Connect** und dann auf **Hinzufügen**.

     ![Mitel Connect in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei MiCloud Connect mithilfe eines Testbenutzers namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in MiCloud Connect eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei MiCloud Connect zu konfigurieren und zu testen:

1. **[Konfigurieren von MiCloud Connect für das einmalige Anmelden mit Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** , um Ihren Benutzern die Verwendung des Features zu ermöglichen und die SSO-Einstellungen auf der Anwendungsseite zu konfigurieren.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
4. **[Erstellen eines Mitel MiCloud Connect-Testbenutzers](#create-a-mitel-micloud-connect-test-user)** , um in Ihrem MiCloud Connect-Konto eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurieren von MiCloud Connect für das einmalige Anmelden mit Azure AD

In diesem Abschnitt aktivieren Sie im Azure-Portal das einmalige Anmelden von Azure AD für MiCloud Connect, und Sie konfigurieren Ihr MiCloud Connect-Konto zum Zulassen des einmaligen Anmeldens mit Azure AD.

Um MiCloud Connect mit einmaligem Anmelden für Azure AD zu konfigurieren, empfiehlt es sich, das Azure-Portal und das Mitel-Kontoportal nebeneinander zu öffnen. Sie müssen einige Informationen aus dem Azure-Portal in das Mitel-Kontoportal kopieren und umgekehrt.


1. Gehen Sie wie folgt vor, um die Konfigurationsseite im [Azure-Portal](https://portal.azure.com/) zu öffnen:

    a. Klicken Sie auf der Anwendungsintegrationsseite für **Mitel Connect** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

    b. Klicken Sie im Dialogfeld **SSO-Methode auswählen** auf **SAML**.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)
    
    Die Seite „SAML-basierte Anmeldung“ wird angezeigt.

2. Gehen Sie wie folgt vor, um das Konfigurationsdialogfeld im Mitel-Kontoportal zu öffnen:

    a. Klicken Sie im Menü **Phone System** (Telefonsystem) auf **Add-On Features** (Add-On-Features).

    b. Klicken Sie rechts neben **Single Sign-On** (Einmaliges Anmelden) auf **Activate** (Aktivieren) oder **Settings** (Einstellungen).
    
    Das Dialogfeld „Connect Single Sign-On Settings“ (Connect-SSO-Einstellungen) wird angezeigt.
    
3. Aktivieren Sie das Kontrollkästchen **Enable Single Sign-On** (Einmaliges Anmelden aktivieren).
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Klicken Sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** auf das Symbol **Bearbeiten**.
    ![image](common/edit-urls.png)

    Das Dialogfeld „Grundlegende SAML-Konfiguration“ wird angezeigt.

5.  Kopieren Sie im Mitel-Kontoportal die URL im Feld **Mitel Identifier (Entity ID)** (Mitel-Bezeichner (Entitäts-ID)), und fügen Sie sie in das Feld **Bezeichner (Entitäts-ID)** im Azure-Portal ein.

6. Kopieren Sie im Mitel-Kontoportal die URL im Feld **Reply URL (Assertion Consumer Service URL)** (Antwort-URL (Assertionsverbraucherdienst-URL)), und fügen Sie sie in das Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** im Azure-Portal ein.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Geben Sie im Textfeld **Anmelde-URL** eine der folgenden URLs ein:

    * **https://portal.shoretelsky.com** , um das Mitel-Kontoportal als Mitel-Standardanwendung zu verwenden
    * **https://teamwork.shoretel.com** , um Teamwork als Mitel-Standardanwendung zu verwenden

    **HINWEIS**: Die Mitel-Standardanwendung ist die Anwendung, auf die zugegriffen wird, wenn ein Benutzer im Zugriffsbereich auf die Kachel „Mitel Connect“ klickt. Auf diese Anwendung wird auch zugegriffen, wenn Sie eine Testeinrichtung in Azure AD vornehmen.

8. Klicken Sie im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** auf **Speichern**.

9. Klicken Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **SAML-Signaturzertifikat** neben **Zertifikat (Base64)** auf **Herunterladen**, um das **Signaturzertifikat** herunterzuladen, und speichern Sie es auf Ihrem Computer.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Öffnen Sie das Signaturzertifikat in einem Text-Editor, kopieren Sie alle Daten in der Datei, und fügen Sie sie dann in das Feld **Signing Certificate** (Signaturzertifikat) im Mitel-Kontoportal ein. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Führen Sie im Azure-Portal die folgenden Schritte im Abschnitt **Mitel Connect einrichten** der Seite **SAML-basierte Anmeldung** aus:

    a. Kopieren Sie die URL im Feld **Anmelde-URL**, und fügen Sie sie in das Feld **Sign-in URL** (Anmelde-URL) im Mitel-Kontoportal ein.

    b. Kopieren Sie die URL im Feld **Azure AD-Bezeichner**, und fügen Sie sie in das Feld **Entity ID** (Entitäts-ID) im Mitel-Kontoportal ein.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klicken Sie im Mitel-Kontoportal im Dialogfeld **Connect Single Sign-On Settings** (Connect-SSO-Einstellungen) auf **Save** (Speichern).

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens Britta Simon.

1. Klicken Sie im Azure-Portal im linken Bereich auf **Azure Active Directory**, auf **Benutzer** und dann auf **Alle Benutzer**.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Klicken Sie oben auf dem Bildschirm auf **Neuer Benutzer**.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie im Dialogfeld „Benutzereigenschaften“ die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: brittasimon@\<ihreunternehmensdomäne\>.\<erweiterung\>.  
Beispiel: BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert im Feld **Kennwort**.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mitel Connect gewähren.

1. Klicken Sie im Azure-Portal auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Klicken Sie in der Anwendungsliste auf **Mitel Connect**.

    ![Mitel Connect-Link in der Anwendungsliste](common/all-applications.png)

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** auf **Benutzer und Gruppen**.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

6. Wenn Sie einen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Erstellen eines Mitel MiCloud Connect-Testbenutzers

In diesem Abschnitt erstellen Sie in Ihrem MiCloud Connect-Konto eine Benutzerin mit Namen Britta Simon. Benutzer müssen erstellt und aktiviert werden, um einmaliges Anmelden verwenden zu können.

Informationen zum Hinzufügen von Benutzern im Mitel-Kontoportal finden Sie im Artikel zum [Hinzufügen eines Benutzers](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) in der Mitel-Knowledge Base.

Erstellen Sie in Ihrem MiCloud Connect-Konto einen Benutzer mit den folgenden Details:

  * **Name:** Britta Simon

* **Geschäftliche E-Mail-Adresse:** `brittasimon@<yourcompanydomain>.<extension>`   
(Beispiel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Benutzername:** `brittasimon@<yourcompanydomain>.<extension> `  
(Beispiel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com). Der Benutzername ist in der Regel mit der geschäftlichen E-Mail-Adresse des Benutzers identisch.)

**HINWEIS:** Der MiCloud Connect-Benutzername muss mit der E-Mail-Adresse des Benutzers in Azure identisch sein.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Mitel Connect“ klicken, sollten Sie automatisch zur Anmeldung bei der MiCloud Connect-Anwendung umgeleitet werden, die Sie im Feld **Anmelde-URL** als Standardanwendung konfiguriert haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
