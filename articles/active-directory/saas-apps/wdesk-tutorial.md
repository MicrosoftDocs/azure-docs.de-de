---
title: 'Tutorial: Azure Active Directory-Integration mit Wdesk | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Wdesk konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 71feb455457fdf75fb19121bac1927b42fe38b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865462"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Tutorial: Azure Active Directory-Integration mit Wdesk

In diesem Tutorial erfahren Sie, wie Sie Wdesk in Azure Active Directory (Azure AD) integrieren.
Die Integration von Wdesk in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Wdesk hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Wdesk anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Wdesk konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Wdesk-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Wdesk unterstützt **SP-** und **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-wdesk-from-the-gallery"></a>Hinzufügen von Wdesk aus dem Katalog

Zum Konfigurieren der Integration von Wdesk in Azure AD müssen Sie Wdesk aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Wdesk aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im **Wdesk** ein, wählen Sie im Ergebnisbereich **Wdesk** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Wdesk in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Wdesk mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Wdesk eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Wdesk müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Wdesk](#configure-wdesk-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Wdesk-Testbenutzers](#create-wdesk-test-user)**, um eine Entsprechung von Britta Simon in Wdesk zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Wdesk die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Wdesk** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Wdesk](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Wdesk](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Sie erhalten diese Werte im WDesk-Portal bei der Konfiguration von SSO.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Wdesk einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-wdesk-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Wdesk

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Wdesk an.

2. Klicken Sie links unten auf **Admin**, und wählen Sie **Account Admin** (Kontoadministrator) aus:
 
     ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Navigieren Sie in Wdesk Admin zu **Security** (Sicherheit) und dann zu **SAML** > **SAML Settings** (SAML-Einstellungen):

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. Aktivieren Sie unter **General Settings** (Allgemeine Einstellungen) die Option **Enable SAML Single Sign On** (SAML Single Sign On aktivieren):

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Führen Sie unter **Service Provider Details** (Dienstanbieterdetails) die folgenden Schritte aus:

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopieren Sie die **Anmelde-URL**, und fügen Sie sie im Azure-Portal in das Textfeld **Anmelde-URL** ein.
   
      b. Kopieren Sie die **Metadaten-URL**, und fügen Sie sie im Azure-Portal in das Textfeld **Bezeichner** ein.
       
      c. Kopieren Sie die **Consumer-URL**, und fügen Sie sie im Azure-Portal in das Textfeld **Antwort-URL** ein.
   
      d. Klicken Sie im Azure-Portal auf **Speichern**, um die Änderungen zu speichern.      

6. Klicken Sie auf **Configure IdP Settings** (IdP-Einstellungen konfigurieren), um das Dialogfeld **Edit IdP Settings** (IdP-Einstellungen bearbeiten) zu öffnen. Klicken Sie auf **Choose File** (Datei auswählen), um die Datei **Metadata.xml**, die Sie im Azure-Portal gespeichert haben, zu suchen und hochzuladen.
    
    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Klicken Sie auf **Änderungen speichern**.

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Wdesk gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Wdesk** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Wdesk** aus.

    ![Wdesk-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-wdesk-test-user"></a>Erstellen eines Wdesk-Testbenutzers

Damit sich Azure AD-Benutzer bei Wdesk anmelden können, müssen sie in Wdesk bereitgestellt werden. Im Fall von Wdesk ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Wdesk als Sicherheitsadministrator an.

2. Navigieren Sie zu **Admin** > **Account Admin** (Kontoadministrator).

     ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klicken Sie unter **People** (Personen) auf **Members** (Mitglieder).

4. Klicken Sie nun auf **Add Member** (Mitglied hinzufügen), um das Dialogfeld **Add Member** (Mitglied hinzufügen) zu öffnen. 
   
    ![Erstellen eines Azure AD-Testbenutzers](./media/wdesk-tutorial/createuser1.png)  

5. Geben Sie im Textfeld **User** (Benutzer) den Benutzernamen des Benutzers (z. B. brittasimon@contoso.com) ein, und klicken Sie dann auf **Continue** (Weiter).

    ![Erstellen eines Azure AD-Testbenutzers](./media/wdesk-tutorial/createuser3.png)

6.  Geben Sie die Details wie folgt ein:
  
    ![Erstellen eines Azure AD-Testbenutzers](./media/wdesk-tutorial/createuser4.png)
 
    a. Geben Sie im Textfeld **E-mail** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. brittasimon@contoso.com.

    b. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein, z.B. **Britta**.

    c. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon**.

7. Klicken Sie auf die Schaltfläche **Save Member** (Mitglied speichern).  

    ![Erstellen eines Azure AD-Testbenutzers](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Wdesk“ klicken, sollten Sie automatisch bei der Wdesk-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

