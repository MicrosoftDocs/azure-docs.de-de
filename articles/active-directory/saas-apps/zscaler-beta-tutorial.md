---
title: 'Tutorial: Azure Active Directory-Integration in Zscaler Beta | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Beta konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 1fec471e4047707f2b7cbaa55fdc7f2256bebe93
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519865"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Azure Active Directory-Integration in Zscaler Beta

In diesem Tutorial erfahren Sie, wie Sie Zscaler Beta in Azure Active Directory (Azure AD) integrieren.
Bei der Integration von Zscaler Beta in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Zscaler Beta hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zscaler Beta anzumelden (einmaliges Anmelden; Single Sign-On, SSO). Diese Zugriffssteuerung wird als einmaliges Anmelden (Single Sign-On, SSO) bezeichnet.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zscaler Beta konfigurieren zu können, ist Folgendes erforderlich:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Zscaler Beta-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler Beta unterstützt SP-initiiertes einmaliges Anmelden.
* Zscaler Beta unterstützt die Just-in-Time-Benutzerbereitstellung.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Hinzufügen von Zscaler Beta aus dem Azure Marketplace

Fügen Sie zum Konfigurieren der Integration von Zscaler Beta in Azure AD Zscaler Beta aus dem Azure Marketplace zur Liste mit den verwalteten SaaS-Apps hinzu.

Führen Sie zum Hinzufügen von Zscaler Beta aus dem Azure Marketplace die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory** .

    ![Azure Active Directory-Schaltfläche](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Zscaler Beta** ein. Wählen Sie im Ergebnisbereich **Zscaler Beta** und anschließend **Hinzufügen** aus.

     ![Zscaler Beta in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler Beta mithilfe des Testbenutzers Britta Simon.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Beta eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler Beta die folgenden Schritte aus:

- [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
- [Konfigurieren des einmaligen Anmeldens für Zscaler Beta](#configure-zscaler-beta-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
- [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
- [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
- [Erstellen eines Zscaler Beta-Testbenutzers](#create-a-zscaler-beta-test-user), um ein Pendant von Britta Simon in Zscaler Beta zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
- [Testen der einmaligen Anmeldung](#test-single-sign-on), um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Zscaler Beta zu konfigurieren:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zscaler Beta** auf **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** den folgenden Schritt aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Zscaler Beta](common/sp-intiated.png)

    - Geben Sie im Textfeld **Anmelde-URL** die von Ihren Benutzern für die Anmeldung bei Ihrer Zscaler Beta-Anwendung verwendete URL ein.

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der tatsächlichen Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Zscaler Beta-Client](https://www.zscaler.com/company/contact).

5. Die Zscaler Beta-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Sie müssen Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf **Bearbeiten** , um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Dialogfeld „Benutzerattribute“](common/edit-attribute.png)

6. Die Zscaler Beta-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:
    
    | Name | Quellattribut | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Wählen Sie **Neuen Anspruch hinzufügen** aus, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Dialogfeld „Benutzeransprüche“](common/new-save-attribute.png)

    ![Dialogfeld „Benutzeransprüche verwalten“](common/new-attribute-details.png)

    b. Geben Sie im Feld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie das Feld **Namespace** leer.

    d. Wählen Sie für **Quelle** die Option **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK** .

    g. Wählen Sie **Speichern** aus.

    > [!NOTE]
    > Informationen zum Konfigurieren von Rollen in Azure AD finden Sie unter [Gewusst wie: Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/active-directory-enterprise-app-role-management.md).

7. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um das **Zertifikat (Base64)** herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

8. Kopieren Sie im Abschnitt **Zscaler Beta einrichten** die URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    - Anmelde-URL
    - Azure AD-Bezeichner
    - Abmelde-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zscaler Beta

1. Wenn Sie die Konfiguration in Zscaler Beta automatisieren möchten, installieren Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** , indem Sie **Erweiterung installieren** auswählen.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Wenn Sie die Erweiterung zum Browser hinzugefügt haben, werden Sie durch die Auswahl von **Zscaler Beta einrichten** zur Ihrer Zscaler Beta-Anwendung weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zscaler Beta anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Öffnen Sie zum manuellen Einrichten von Zscaler Beta ein neues Webbrowserfenster. Melden Sie sich bei Ihrer Zscaler Beta-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Navigieren Sie zu **Administration** > **Authentication** > **Authentication Settings** (Verwaltung > Authentifizierung > Authentifizierungseinstellungen), und führen Sie die folgenden Schritte aus.
   
    ![Verwaltung](./media/zscaler-beta-tutorial/ic800206.png "Verwaltung")

    a. Wählen Sie unter **Authentifizierungstyp** die Option **SAML** aus.

    b. Wählen Sie **Configure SAML** (SAML konfigurieren) aus.

5. Gehen Sie im Fenster **Edit SAML** (SAML bearbeiten) wie folgt vor: 
            
    ![Benutzer & Authentifizierung verwalten](./media/zscaler-beta-tutorial/ic800208.png "Benutzer & Authentifizierung verwalten")
    
    a. Fügen Sie im Feld **SAML Portal URL** (SAML-Portal-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    b. Geben Sie im Feld **Login Name Attribute** (Anmeldenamensattribut) die Zeichenfolge **NameID** ein.

    c. Wählen Sie im Feld **Public SSL Certificate** (Öffentliches SSL-Zertifikat) die Option **Upload** (Hochladen) aus, um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal heruntergeladen haben.

    d. Schalten Sie **Enable SAML Auto-Provisioning** (Automatische SAML-Bereitstellung aktivieren) um.

    e. Geben Sie im Feld **User Display Name Attribute** (Benutzeranzeigenamens-Attribut) die Zeichenfolge **displayName** ein, wenn Sie die automatische SAML-Bereitstellung für displayName-Attribute aktivieren möchten.

    f. Geben Sie im Feld **Group Name Attribute** (Gruppennamensattribut) die Zeichenfolge **memberOf** ein, wenn Sie die automatische SAML-Bereitstellung für memberOf-Attribute aktivieren möchten.

    g. Geben Sie im Feld **Department Name Attribute** (Abteilungsnamensattribut) die Zeichenfolge **department** ein, wenn Sie die automatische SAML-Bereitstellung für Abteilungsattribute aktivieren möchten.

    h. Wählen Sie **Speichern** aus.

6. Führen Sie auf der Dialogseite **Configure User Authentication** (Benutzerauthentifizierung konfigurieren) die folgenden Schritte aus:

    ![Menü „Activation“ (Aktivierung) und Schaltfläche „Activate“ (Aktivieren)](./media/zscaler-beta-tutorial/ic800207.png)

    a. Bewegen Sie den Mauszeiger unten links auf das Menü **Activation** (Aktivierung).

    b. Wählen Sie **Aktivieren** aus.

## <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen
Führen Sie zum Konfigurieren der Proxyeinstellungen in Internet Explorer die folgenden Schritte aus:

1. Starten Sie **Internet Explorer** .

2. Wählen Sie im Menü **Extras** die Option **Internetoptionen** aus, um das Dialogfeld **Internetoptionen** zu öffnen. 
    
     ![Dialogfeld „Internetoptionen“](./media/zscaler-beta-tutorial/ic769492.png "Internetoptionen")

3. Wählen Sie die Registerkarte **Verbindungen** aus. 
  
     ![Registerkarte „Verbindungen“](./media/zscaler-beta-tutorial/ic769493.png "Verbindungen")

4. Wählen Sie **LAN-Einstellungen** aus, um das Dialogfeld **Einstellungen für lokales Netzwerk** zu öffnen.

5. Gehen Sie im Abschnitt **Proxyserver** wie folgt vor: 
   
    ![Abschnitt „Proxyserver“](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Aktivieren Sie das Kontrollkästchen **Proxyserver für LAN verwenden** .

    b. Geben Sie in das Feld **Adresse** die Adresse **gateway.Zscaler Beta.net** ein.

    c. Geben Sie im Feld **Port** den Port **80** ein.

    d. Aktivieren Sie das Kontrollkästchen **Proxyserver für lokale Adressen umgehen** .

    e. Wählen Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** die Option **OK** aus.

6. Wählen Sie zum Schließen des Dialogfelds **Internetoptionen** die Option **OK** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Links „Benutzer“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Gehen Sie im Dialogfeld **Benutzer** wie folgt vor:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: `brittasimon@yourcompanydomain.extension`. z. B. BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** . Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

Ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Beta gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Zscaler Beta** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Zscaler Beta** ein, und wählen Sie den Eintrag aus.

    ![Zscaler Beta-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf **Benutzer hinzufügen** . Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Schaltfläche „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste den Benutzer (etwa **Britta Simon** ) aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.

    ![Dialogfeld „Benutzer und Gruppen“](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Benutzerrolle in der Liste aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.

    ![Dialogfeld „Rolle auswählen“](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

    ![Dialogfeld „Zuweisung hinzufügen“](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Erstellen eines Zscaler Beta-Testbenutzers

In diesem Abschnitt wird in Zscaler Beta der Benutzer Britta Simon erstellt. Zscaler Beta unterstützt die **Just-in-Time-Benutzerbereitstellung** , die standardmäßig aktiviert ist. In diesem Abschnitt müssen Sie keine Schritte ausführen. Ist ein Benutzer noch nicht in Zscaler Beta vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!Note]
>Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam von Zscaler Beta](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

Testen Sie Ihre Azure AD-Konfiguration für das einmalige Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zscaler Beta“ klicken, sollten Sie automatisch bei der Zscaler Beta-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)
- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Was ist bedingter Zugriff?](../conditional-access/overview.md)