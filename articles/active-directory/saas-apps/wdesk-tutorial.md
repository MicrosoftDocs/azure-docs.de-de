---
title: 'Tutorial: Azure Active Directory-Integration mit Wdesk | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Wdesk konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 11539e7d6e685703810537cf69e0e7ed3ec0be4c
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609087"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Wdesk

In diesem Tutorial erfahren Sie, wie Sie Wdesk in Azure Active Directory (Azure AD) integrieren. Die Integration von Wdesk in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Wdesk hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Wdesk anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Wdesk-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Wdesk unterstützt **SP-** und **IDP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren von Wdesk können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-wdesk-from-the-gallery"></a>Hinzufügen von Wdesk aus dem Katalog

Zum Konfigurieren der Integration von Wdesk in Azure AD müssen Sie Wdesk aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Wdesk** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Wdesk** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Wdesk mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Wdesk eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Wdesk die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Wdesk](#configure-wdesk-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Wdesk-Testbenutzers](#create-wdesk-test-user)** , um eine Entsprechung von B. Simon in Wdesk zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Wdesk die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Wdesk** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![Screenshot: Seite „Grundlegende SAML-Konfiguration“ zum Eingeben des Bezeichners und einer Antwort-URL sowie zum Klicken auf „Speichern“](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/metadata-upload-additional-signon.png)

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

    d. Klicken Sie auf **Erstellen**.

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

## <a name="configure-wdesk-sso"></a>Konfigurieren des einmaligen Anmeldens für Wdesk

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Wdesk an.

1. Klicken Sie links unten auf **Admin**, und wählen Sie **Account Admin** (Kontoadministrator) aus:
 
    ![Der Screenshot zeigt die im Menü „Admin“ ausgewählte Option „Account Admin“.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Navigieren Sie in Wdesk Admin zu **Security** (Sicherheit) und dann zu **SAML** > **SAML Settings** (SAML-Einstellungen):

    ![Der Screenshot zeigt die auf der Registerkarte „SAML“ ausgewählte Option „SAML Settings“.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Aktivieren Sie unter **SAML User ID Settings** (Einstellungen für die SAML-Benutzer-ID) das Kontrollkästchen **SAML User ID is Wdesk Username** (SAML-Benutzer-ID entspricht Wdesk-Benutzername).

    ![Der Screenshot zeigt „SAML User ID Settings“, unter denen Sie „SAML User ID is Wdesk Username“ aktivieren können.](./media/wdesk-tutorial/wdesk-username.png)

4. Aktivieren Sie unter **General Settings** (Allgemeine Einstellungen) die Option **Enable SAML Single Sign On** (SAML Single Sign On aktivieren):

    ![Der Screenshot zeigt „Edit SAML Settings“ (SAML-Einstellungen bearbeiten ), unter denen Sie „Enable SAML Single Sign On“ aktivieren können.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. Führen Sie unter **Service Provider Details** (Dienstanbieterdetails) die folgenden Schritte aus:

    ![Der Screenshot zeigt „Service Provider Details“, unter denen Sie die beschriebenen Werte eingeben können.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

    1. Kopieren Sie die **Anmelde-URL**, und fügen Sie sie im Azure-Portal in das Textfeld **Anmelde-URL** ein.

    1. Kopieren Sie die **Metadaten-URL**, und fügen Sie sie im Azure-Portal in das Textfeld **Bezeichner** ein.

    1. Kopieren Sie die **Consumer-URL**, und fügen Sie sie im Azure-Portal in das Textfeld **Antwort-URL** ein.

    1. Klicken Sie im Azure-Portal auf **Speichern**, um die Änderungen zu speichern.      

1. Klicken Sie auf **Configure IdP Settings** (IdP-Einstellungen konfigurieren), um das Dialogfeld **Edit IdP Settings** (IdP-Einstellungen bearbeiten) zu öffnen. Klicken Sie auf **Choose File** (Datei auswählen), um die Datei **Metadata.xml**, die Sie im Azure-Portal gespeichert haben, zu suchen und hochzuladen.
    
    ![Der Screenshot zeigt „Edit IdP Settings“, über das Sie Metadaten hochladen können.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Klicken Sie auf **Änderungen speichern**.

    ![Der Screenshot zeigt die Schaltfläche „Änderungen speichern“.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Erstellen eines Wdesk-Testbenutzers

Damit sich Azure AD-Benutzer bei Wdesk anmelden können, müssen sie in Wdesk bereitgestellt werden. Im Fall von Wdesk ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Wdesk als Sicherheitsadministrator an.

2. Navigieren Sie zu **Admin** > **Account Admin** (Kontoadministrator).

     ![Der Screenshot zeigt die im Menü „Admin“ ausgewählte Option „Account Admin“.](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klicken Sie unter **People** (Personen) auf **Members** (Mitglieder).

4. Klicken Sie nun auf **Add Member** (Mitglied hinzufügen), um das Dialogfeld **Add Member** (Mitglied hinzufügen) zu öffnen. 
   
    ![Der Screenshot zeigt die Registerkarte „Members“, auf der Sie „Add Member“ auswählen können.](./media/wdesk-tutorial/createuser1.png)  

5. Geben Sie im Textfeld **User** (Benutzer) den Benutzernamen des Benutzers (z. B. b.simon@contoso.com) ein, und klicken Sie dann auf **Continue** (Weiter).

    ![Der Screenshot zeigt das Dialogfeld „Add Member“, in dem Sie einen Benutzer eingeben können.](./media/wdesk-tutorial/createuser3.png)

6.  Geben Sie die Details wie folgt ein:
  
    ![Der Screenshot zeigt das Dialogfeld „Add Member“, in dem Sie grundlegende Informationen zu einem Benutzer eingeben können.](./media/wdesk-tutorial/createuser4.png)
 
    a. Geben Sie im Textfeld **E-mail** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. b.simon@contoso.com.

    b. Geben Sie im Textfeld **First name** (Vorname) den Vornamen des Benutzers ein, z. B. **B**.

    c. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon**.

7. Klicken Sie auf die Schaltfläche **Save Member** (Mitglied speichern).  

    ![Der Screenshot zeigt das Senden einer Begrüßungs-E-Mail über die Schaltfläche „Save Member“.](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Wdesk“ klicken, sollten Sie automatisch bei der Wdesk-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)