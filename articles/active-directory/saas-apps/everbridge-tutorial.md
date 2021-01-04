---
title: 'Tutorial: Azure Active Directory-Integration mit Everbridge | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Everbridge konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 732362ef7099e93697320d8e47180c1207e8cb32
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995895"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Azure Active Directory-Integration mit Everbridge

In diesem Tutorial erfahren Sie, wie Sie Everbridge in Azure Active Directory (Azure AD) integrieren.
Bei der Integration von Everbridge in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Everbridge hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Everbridge anzumelden (einmaliges Anmelden; Single Sign-On, SSO). Diese Zugriffssteuerung wird als einmaliges Anmelden (Single Sign-On, SSO) bezeichnet.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.
Weitere Informationen zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Everbridge konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Everbridge-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Everbridge unterstützt IDP-initiiertes einmaliges Anmelden.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Hinzufügen von Everbridge aus dem Azure Marketplace

Zum Konfigurieren der Integration von Everbridge in Azure AD müssen Sie Everbridge aus dem Azure Marketplace zur Liste mit den verwalteten SaaS-Apps hinzufügen.

Führen Sie zum Hinzufügen von Everbridge aus dem Azure Marketplace die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich die Option **Azure Active Directory**.

    ![Azure Active Directory-Schaltfläche](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Everbridge** ein. Wählen Sie im Ergebnisbereich **Everbridge** und dann **Hinzufügen** aus.

     ![Everbridge in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Everbridge mithilfe des Testbenutzers Britta Simon.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Everbridge eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Everbridge müssen die folgenden Schritte ausgeführt werden:

- [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
- [Konfigurieren von Everbridge als Everbridge-Manager-Portal mit einmaligem Anmelden](#configure-everbridge-as-everbridge-manager-portal-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
- [Konfigurieren von Everbridge als Everbridge-Mitgliederportal mit einmaligem Anmelden](#configure-everbridge-as-everbridge-member-portal-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
- [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
- [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
- [Erstellen eines Everbridge-Testbenutzers](#create-an-everbridge-test-user), um ein Pendant von Britta Simon in Everbridge zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
- [Testen der einmaligen Anmeldung](#test-single-sign-on), um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Everbridge zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Everbridge** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

    >[!NOTE]
    >Konfigurieren Sie die Anwendung sowohl im Azure-Portal als auch im Everbridge-Portal entweder als Manager-Portal *oder* als Mitgliederportal.

4. Um die Anwendung **Everbridge** als **Everbridge-Manager-Portal** zu konfigurieren, führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Everbridge](common/idp-intiated.png)

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://sso.everbridge.net/<API_Name>`.

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem tatsächlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Everbridge-Supportteam](mailto:support@everbridge.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Um die Anwendung **Everbridge** als **Everbridge-Mitgliederportal** zu konfigurieren, führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

  * Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, führen Sie die folgenden Schritte aus:

     ![SSO-Informationen zur Domäne und zu den URLs für Everbridge für den IDP-initiierten Modus](common/idp-intiated.png)

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`.

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`.

   * Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus:

     ![SSO-Informationen zur Domäne und zu den URLs für Everbridge für den SP-initiierten Modus](common/both-signonurl.png)

     a. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`.

     > [!NOTE]
     > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Diese Werte erhalten Sie vom [Everbridge-Supportteam](mailto:support@everbridge.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um die **Verbundmetadaten-XML** herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Everbridge einrichten** die URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    - Anmelde-URL
    - Azure AD-Bezeichner
    - Abmelde-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurieren von Everbridge als Everbridge-Manager Portal mit einmaligem Anmelden

Führen Sie die folgenden Schritte aus, um SSO in **Everbridge** als **Everbridge -Manager-Portalanwendung** zu konfigurieren.
 
1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei Everbridge an.

1. Wählen Sie im oberen Menü die Registerkarte **Settings** (Einstellungen) aus. Wählen Sie unter **Security** (Sicherheit) die Option **Single Sign-On** (Einmaliges Anmelden) aus.
   
     ![Einmaliges Anmelden konfigurieren](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Geben Sie im Feld **Name** den Namen des Identitätsanbieters ein. Ein Beispiel dafür ist der Name Ihres Unternehmens.
   
     b. Geben Sie im Feld **API Name** (API-Name) den Namen der API ein.
   
     c. Wählen Sie **Choose File** (Datei auswählen) aus, um die Metadatendatei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.
   
     d. Wählen Sie für **SAML Identity Location** (SAML-Identitätsstandort) die Option **Identity is in the NameIdentifier element of the Subject statement** (Identität befindet sich im NameIdentifier-Element der Ausstellererklärung).
   
     e. Fügen Sie in das Feld **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
     f. Wählen Sie für **Service Provider initiated Request Binding** (Vom Dienstanbieter initiierte Anforderungsbindung) die Einstellung **HTTP Redirect** (HTTP-Umleitung) aus.

     g. Wählen Sie **Speichern** aus.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurieren von Everbridge als Everbridge-Mitgliederportal mit einmaligem Anmelden

Um einmaliges Anmelden für **Everbridge** als **Everbridge-Mitgliederportal** zu konfigurieren, senden Sie die heruntergeladene **Verbundmetadaten-XML-Datei** an das [Everbridge-Supportteam](mailto:support@everbridge.com). Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Führen Sie zum Erstellen des Testbenutzers Britta Simon im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Links „Benutzer“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Gehen Sie im Dialogfeld **Benutzer** wie folgt vor:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: `brittasimon@yourcompanydomain.extension`. z. B. BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

Ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Everbridge gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** >**Everbridge** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Everbridge** aus.

    ![Everbridge-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Dialogfeld „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Wählen Sie am unteren Bildschirmrand **Auswählen** aus.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie am unteren Bildschirmrand **Auswählen** aus.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-an-everbridge-test-user"></a>Erstellen eines Everbridge-Testbenutzers

In diesem Abschnitt erstellen Sie in Everbridge den Testbenutzer Britta Simon. Wenn Sie Benutzer auf der Everbridge-Plattform hinzufügen möchten, wenden Sie sich an das [Supportteam von Everbridge](mailto:support@everbridge.com). Benutzer müssen in Everbridge erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

Testen Sie Ihre Azure AD-Konfiguration für das einmalige Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „Everbridge“ auswählen, sollten Sie automatisch bei der Everbridge-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)
- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Was ist bedingter Zugriff?](../conditional-access/overview.md)