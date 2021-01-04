---
title: 'Tutorial: Azure Active Directory-Integration in Mitel Connect | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Mitel Connect konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015178"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Tutorial: Azure Active Directory-Integration mit Mitel MiCloud Connect oder CloudLink Platform

In diesem Tutorial erfahren Sie, wie Sie die Mitel Connect-App verwenden, um Azure Active Directory (Azure AD) mit Mitel MiCloud Connect oder CloudLink Platform zu integrieren. Die Mitel Connect-App ist im Azure-Katalog verfügbar. Die Integration von Azure AD mit MiCloud Connect oder CloudLink Platform bietet Ihnen folgende Vorteile:

* Sie können den Benutzerzugriff auf MiCloud Connect- und CloudLink-Apps in Azure AD unter Verwendung ihrer Anmeldeinformationen steuern.
* Sie können es Benutzern in Ihrem Konto ermöglichen, sich mit ihren Azure AD-Konten automatisch bei MiCloud Connect oder CloudLink anzumelden (einmaliges Anmelden; Single Sign-On, SSO).

Ausführliche Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie mit der Integration von Azure AD mit Mitel MiCloud Connect oder CloudLink Platform beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in MiCloud Connect zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Mitel MiCloud Connect- oder ein Mitel CloudLink-Konto, abhängig von der zu konfigurierenden Anwendung

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden (SSO) von Azure AD.

* Mitel Connect unterstützt **SP**-initiiertes einmaliges Anmelden.
* Nach dem Konfigurieren von Mitel Connect können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="add-mitel-connect-from-the-gallery"></a>Hinzufügen von Mitel Connect aus dem Katalog

Zum Konfigurieren der Integration von Mitel Connect in Azure AD müssen Sie Mitel Connect im Azure-Portal aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie **Neue Anwendung** aus.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Mitel Connect** in das Suchfeld ein, und wählen Sie im Ergebnisbereich **Mitel Connect** und dann **Hinzufügen** aus.

     ![Mitel Connect in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit MiCloud Connect oder CloudLink Platform mithilfe eines Testbenutzers namens **_Britta Simon_**. Damit einmaliges Anmelden funktioniert, muss zwischen dem Benutzer im Azure AD-Portal und dem entsprechenden Benutzer auf der Mitel-Plattform eine Verknüpfung eingerichtet werden. In den folgenden Abschnitten finden Sie Informationen zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit MiCloud Connect oder CloudLink Platform.
* Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit MiCloud Connect
* Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit CloudLink Platform

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit MiCloud Connect

So konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit MiCloud Connect:

1. **[Konfigurieren von MiCloud Connect für das einmalige Anmelden mit Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**, um Ihren Benutzern die Verwendung des Features zu ermöglichen und die SSO-Einstellungen auf der Anwendungsseite zu konfigurieren.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
4. **[Erstellen eines Mitel MiCloud Connect-Testbenutzers](#create-a-mitel-micloud-connect-test-user)**, um in Ihrem MiCloud Connect-Konto eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurieren von MiCloud Connect für das einmalige Anmelden mit Azure AD

In diesem Abschnitt aktivieren Sie im Azure-Portal das einmalige Anmelden von Azure AD für MiCloud Connect, und Sie konfigurieren Ihr MiCloud Connect-Konto zum Zulassen des einmaligen Anmeldens mit Azure AD.

Um MiCloud Connect mit einmaligem Anmelden für Azure AD zu konfigurieren, empfiehlt es sich, das Azure-Portal und das Mitel-Kontoportal nebeneinander zu öffnen. Sie müssen einige Informationen aus dem Azure-Portal in das Mitel-Kontoportal kopieren und umgekehrt.


1. So öffnen Sie die Konfigurationsseite im [Azure-Portal](https://portal.azure.com/):

    1. Wählen Sie auf der Anwendungsintegrationsseite für **Mitel Connect** die Option **Einmaliges Anmelden** aus.

       ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

    1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** die Methode **SAML** aus.
    
       ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)
    
       Die Seite „SAML-basierte Anmeldung“ wird angezeigt.

2. So öffnen Sie das Konfigurationsdialogfeld im Mitel-Kontoportal:

    1. Wählen Sie im Menü **Phone System** (Telefonsystem) die Option **Add-On Features** (Add-On-Features) aus.

    1. Wählen Sie rechts neben **Single Sign-On** (Einmaliges Anmelden) die Option **Activate** (Aktivieren) oder **Settings** (Einstellungen) aus.
    
    Das Dialogfeld „Connect Single Sign-On Settings“ (Connect-SSO-Einstellungen) wird angezeigt.
    
3. Aktivieren Sie das Kontrollkästchen **Enable Single Sign-On** (Einmaliges Anmelden aktivieren).
    
    ![Screenshot: Mitel Connect-Seite mit Einstellungen für einmaliges Anmelden, auf der das Kontrollkästchen „Enable Single Sign-On“ (Einmaliges Anmelden aktivieren) aktiviert ist](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. Wählen Sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** das Symbol **Bearbeiten** aus.
   
    ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit Auswahl des Symbols „Bearbeiten“](common/edit-urls.png)

    Das Dialogfeld „Grundlegende SAML-Konfiguration“ wird angezeigt.

5.  Kopieren Sie im Mitel-Kontoportal die URL im Feld **Mitel Identifier (Entity ID)** (Mitel-Bezeichner (Entitäts-ID)), und fügen Sie sie in das Feld **Bezeichner (Entitäts-ID)** im Azure-Portal ein.

6. Kopieren Sie im Mitel-Kontoportal die URL im Feld **Reply URL (Assertion Consumer Service URL)** (Antwort-URL (Assertionsverbraucherdienst-URL)), und fügen Sie sie in das Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** im Azure-Portal ein.

   ![Screenshot: „Grundlegende SAML-Konfiguration“ im Azure-Portal und Abschnitt „Set Up Identity Provider“ (Identitätsanbieter einrichten) im Mitel-Kontoportal mit Linien zur Verdeutlichung der Beziehungen](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. Geben Sie im Textfeld **Anmelde-URL** eine der folgenden URLs ein:

    1. **https://portal.shoretelsky.com** , um das Mitel-Kontoportal als Mitel-Standardanwendung zu verwenden
    1. **https://teamwork.shoretel.com** , um Teamwork als Mitel-Standardanwendung zu verwenden

    > [!NOTE]
    > Die Mitel-Standardanwendung ist die Anwendung, auf die zugegriffen wird, wenn ein Benutzer im Zugriffsbereich die Kachel „Mitel Connect“ auswählt. Auf diese Anwendung wird auch zugegriffen, wenn Sie eine Testeinrichtung in Azure AD vornehmen.

8. Wählen Sie im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** die Option **Speichern** aus.

9. Wählen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **SAML-Signaturzertifikat** neben **Zertifikat (Base64)** die Option **Herunterladen** aus, um das **Signaturzertifikat** herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Screenshot: Bereich „SAML-Signaturzertifikat“, in dem Sie ein Zertifikat herunterladen können](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Öffnen Sie das Signaturzertifikat in einem Text-Editor, kopieren Sie alle Daten in der Datei, und fügen Sie sie dann in das Feld **Signing Certificate** (Signaturzertifikat) im Mitel-Kontoportal ein. 

      ![Screenshot: Feld „Signaturzertifikat“](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Führen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **Mitel Connect einrichten** die folgenden Schritte aus:

     1. Kopieren Sie die URL im Feld **Anmelde-URL**, und fügen Sie sie in das Feld **Sign-in URL** (Anmelde-URL) im Mitel-Kontoportal ein.

     1. Kopieren Sie die URL im Feld **Azure AD-Bezeichner**, und fügen Sie sie in das Feld **Entity ID** (Entitäts-ID) im Mitel-Kontoportal ein.
         
         ![Screenshot: Beziehung zwischen der Seite „SAML-basierte Anmeldung“ im Azure-Portal und dem Mitel-Kontoportal](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Wählen Sie im Mitel-Kontoportal im Dialogfeld **Connect Single Sign-On Settings** (Connect-SSO-Einstellungen) die Option **Save** (Speichern) aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie im Dialogfeld „Benutzereigenschaften“ die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen „brittasimon@\<yourcompanydomain\>.\<extension\>“ ein.  Beispiel: BrittaSimon@contoso.com.

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert im Feld **Kennwort**.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mitel Connect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Mitel Connect** aus.

    ![Mitel Connect-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** und anschließend am unteren Bildschirmrand **Auswählen** aus.

6. Wenn Sie einen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Erstellen eines Mitel MiCloud Connect-Testbenutzers

In diesem Abschnitt erstellen Sie in Ihrem MiCloud Connect-Konto eine Benutzerin mit Namen Britta Simon. Benutzer müssen erstellt und aktiviert werden, um einmaliges Anmelden verwenden zu können.

Informationen zum Hinzufügen von Benutzern im Mitel-Kontoportal finden Sie im Artikel zum [Hinzufügen eines Benutzers](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) in der Mitel-Knowledge Base.

Erstellen Sie in Ihrem MiCloud Connect-Konto einen Benutzer mit den folgenden Details:

* **Name:** Britta Simon
* **Geschäftliche E-Mail-Adresse:** `brittasimon@<yourcompanydomain>.<extension>`   
  (Beispiel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **Benutzername:** `brittasimon@<yourcompanydomain>.<extension>`  
  (Beispiel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com). Der Benutzername ist in der Regel mit der geschäftlichen E-Mail-Adresse des Benutzers identisch.)

> [!NOTE]
> Der MiCloud Connect-Benutzername muss mit der E-Mail-Adresse des Benutzers in Azure identisch sein.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „Mitel Connect“ auswählen, sollten Sie automatisch zur Anmeldung bei der MiCloud Connect-Anwendung umgeleitet werden, die Sie im Feld **Anmelde-URL** als Standardanwendung konfiguriert haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit CloudLink Platform

In diesem Abschnitt erfahren Sie, wie Sie im Azure-Portal das einmalige Anmelden von Azure AD für CloudLink Platform aktivieren und das CloudLink Platform-Konto konfigurieren, um einmaliges Anmelden unter Verwendung von Azure AD zu ermöglichen.

Zum Konfigurieren von CloudLink Platform mit einmaligem Anmelden für Azure AD werden die folgenden Schritte empfohlen: Öffnen Sie das Azure-Portal und das CloudLink-Kontoportal nebeneinander, da Sie einige Informationen aus dem Azure-Portal in das CloudLink-Kontoportal (und umgekehrt) kopieren müssen.

1. So öffnen Sie die Konfigurationsseite im [Azure-Portal](https://portal.azure.com/):

    1. Wählen Sie auf der Anwendungsintegrationsseite für **Mitel Connect** die Option **Einmaliges Anmelden** aus.

       ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

    1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** die Methode **SAML** aus.

       ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)
    
       Die Seite **SAML-basierte Anmeldung** wird mit dem Abschnitt **Grundlegende SAML-Konfiguration** geöffnet.

       ![Screenshot: Seite „SAML-basierte Anmeldung“ mit dem Abschnitt „Grundlegende SAML-Konfiguration“](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. So greifen Sie im CloudLink-Kontoportal auf den Konfigurationsbereich **Azure AD Single Sign On** (Azure AD-SSO) zu:

    1. Navigieren Sie zur Seite **Account Information** (Kontoinformationen) des Kundenkontos, mit dem Sie die Integration aktivieren möchten.

    1. Wählen Sie im Abschnitt **Integrations** (Integrationen) die Option **+ Add new**  (+ Neu hinzufügen) aus. Auf einem Popupbildschirm wird der Bereich **Integrations** (Integrationen) angezeigt.

    1. Wählen Sie die Registerkarte **3rd party** (Drittanbieter) aus. Eine Liste der unterstützten Drittanbieteranwendungen wird angezeigt. Wählen Sie die Schaltfläche **Add** (Hinzufügen) für **Azure AD Single Sign On** (Azure AD-SSO) und dann **Done** (Fertig) aus.

       ![Screenshot: Seite „Integrationen“, auf der Sie einmaliges Anmelden für Azure AD hinzufügen können](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       **Azure AD Single Sign On** (Azure AD-SSO) wird für das Kundenkonto aktiviert und dem Abschnitt **Integrations** (Integrationen) auf der Seite **Account Information** (Kontoinformationen) hinzugefügt.   

   1. Wählen Sie **Complete Setup** (Setup abschließen) aus.
    
      ![Screenshot: Option „Complete Setup“ (Setup abschließen) für Azure AD-SSO](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Der Konfigurationsbereich **Azure AD Single Sign On** (Azure AD-SSO) wird geöffnet.
      
       ![Screenshot: Konfigurationsbereich „Azure AD Single Sign-On“ (Azure AD-SSO)](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel empfiehlt, das Kontrollkästchen **Enable Mitel Credentials (Optional)** (Mitel-Anmeldeinformationen aktivieren (optional)) im Abschnitt **Optional Mitel credentials** (Optionale Mitel-Anmeldeinformationen) nicht zu aktivieren. Aktivieren Sie dieses Kontrollkästchen nur, wenn Sie möchten, dass sich der Benutzer nicht nur mit der Option für einmaliges Anmelden, sondern auch mithilfe der Mitel-Anmeldeinformationen bei der CloudLink-Anwendung anmelden kann.

3. Wählen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **Grundlegende SAML-Konfiguration** das Symbol **Bearbeiten** aus. Der Bereich **Grundlegende SAML-Konfiguration** wird geöffnet.

    ![Screenshot: Bereich „Grundlegende SAML-Konfiguration“ mit Auswahl des Symbols „Bearbeiten“](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Kopieren Sie im CloudLink-Kontoportal die URL im Feld **Mitel Identifier (Entity ID)** (Mitel-Bezeichner (Entitäts-ID)), und fügen Sie sie im Azure-Portal in das Feld **Bezeichner (Entitäts-ID)** ein.

 5. Kopieren Sie im CloudLink-Kontoportal die URL im Feld **Reply URL (Assertion Consumer Service URL)** (Antwort-URL (Assertionsverbraucherdienst-URL)), und fügen Sie sie im Azure-Portal in das Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** ein.  
    
    ![Screenshot: Beziehung zwischen Seiten im CloudLink-Kontoportal und dem Azure-Portal](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. Geben Sie im Textfeld **Anmelde-URL** die URL `https://accounts.mitel.io` ein, um das CloudLink-Kontoportal als Mitel-Standardanwendung zu nutzen.
     
     ![Screenshot: Textfeld „Anmelde-URL“](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > Die Mitel-Standardanwendung ist die Anwendung, die geöffnet wird, wenn ein Benutzer im Zugriffsbereich die Kachel „Mitel Connect“ auswählt. Auf diese Anwendung wird auch zugegriffen, wenn der Benutzer eine Testeinrichtung in Azure AD konfiguriert.

7. Wählen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die Option **Speichern** aus.

8. Wählen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **SAML-Signaturzertifikat** neben **Zertifikat (Base64)** die Option **Herunterladen** aus, um das **Signaturzertifikat** herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.
  
    ![Screenshot: Abschnitt „SAML-Signaturzertifikat“, in dem Sie ein Base64-Zertifikat herunterladen können](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Öffnen Sie das Signaturzertifikat in einem Text-Editor, kopieren Sie alle Daten in der Datei, und fügen Sie sie dann im CloudLink-Kontoportal in das Feld **Signing Certificate** (Signaturzertifikat) ein.  

    > [!NOTE]
    > Wenn Sie über mehrere Zertifikate verfügen, empfiehlt es sich, sie nacheinander einzufügen. 
       
    ![Screenshot: Zweiter Schritt des Verfahrens, in dem Sie Werte aus Ihrer Azure AD-Integration einfügen](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Führen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **Mitel Connect einrichten** die folgenden Schritte aus:

     1. Kopieren Sie die URL im Feld **Anmelde-URL**, und fügen Sie sie im CloudLink-Kontoportal in das Feld **Sign-in URL** (Anmelde-URL) ein.

     1. Kopieren Sie die URL im Feld **Azure AD-Bezeichner**, und fügen Sie sie im CloudLink-Kontoportal in das Feld **IDP Identifier (Entity ID)** (IDP-Bezeichner (Entitäts-ID)) ein.
     
        ![Screenshot: Anzeige der Quelle für die hier beschriebenen Werte in Mitel Connect](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Wählen Sie im CloudLink-Kontoportal im Bereich **Azure AD Single Sign On** (Azure AD-SSO) die Option **Save** (Speichern) aus:

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie im Dialogfeld „Benutzereigenschaften“ die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen „brittasimon@\<yourcompanydomain\>.\<extension\>“ ein.  Beispiel: BrittaSimon@contoso.com.

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert im Feld **Kennwort**.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mitel Connect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Mitel Connect** aus.

    ![Mitel Connect-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** und anschließend am unteren Bildschirmrand **Auswählen** aus.

6. Wenn Sie einen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-cloudlink-test-user"></a>Erstellen eines CloudLink-Testbenutzers

In diesem Abschnitt wird beschrieben, wie Sie in CloudLink Platform einen Testbenutzer namens **_Britta Simon_** erstellen. Benutzer müssen erstellt und aktiviert werden, damit sie einmaliges Anmelden verwenden können.

Ausführliche Informationen zum Hinzufügen von Benutzern im CloudLink-Kontoportal finden Sie in der [Dokumentation zu CloudLink-Konten](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html) unter **_Verwalten von Benutzern_**.

Erstellen Sie in Ihrem CloudLink-Kontoportal einen Benutzer mit den folgenden Details:

* Name: Britta Simon
* Vorname: Britta
* Nachname: Simon
* E-Mail: BrittaSimon@contoso.com

> [!NOTE]
> Die CloudLink-E-Mail-Adresse des Benutzers muss mit dem Wert für **Benutzerprinzipalname** im Azure-Portal übereinstimmen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „Mitel Connect“ auswählen, werden Sie automatisch zur Anmeldung bei der CloudLink-Anwendung umgeleitet, die Sie im Feld **Anmelde-URL** als Standardanwendung konfiguriert haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)