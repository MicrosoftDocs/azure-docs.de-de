---
title: 'Tutorial: Azure Active Directory-Integration in Deskradar | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Deskradar konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea17794a5ca57ae0d38d9a90857706cf380cab36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65862239"
---
# <a name="tutorial-azure-active-directory-integration-with-deskradar"></a>Tutorial: Azure Active Directory-Integration in Deskradar

In diesem Tutorial erfahren Sie, wie Sie Deskradar in Azure Active Directory (Azure AD) integrieren.
Die Integration von Deskradar in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Deskradar hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Deskradar anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration in Deskradar benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Deskradar-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Deskradar unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="add-deskradar-from-the-gallery"></a>Hinzufügen von Deskradar aus dem Katalog

Zum Konfigurieren der Integration von Deskradar in Azure AD müssen Sie Deskradar aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Deskradar aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Deskradar** in das Suchfeld ein, wählen Sie im Ergebnisbereich **Deskradar** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Deskradar in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei [Anwendungsname] mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in [Anwendungsname] eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei [Anwendungsname] müssen Sie die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Deskradar](#configure-deskradar-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Deskradar-Testbenutzers](#create-deskradar-test-user)**, um eine Entsprechung von Britta Simon in Deskradar zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit [Anwendungsname] zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Deskradar** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Deskradar](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://YOURDOMAIN.deskradar.cloud`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login`

    ![SSO-Informationen zur Domäne und zu den URLs für Deskradar](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Ersetzen Sie **YOURDOMAIN** durch die Domäne Ihrer Deskradar-Instanz. Wenden Sie sich an das [Supportteam von Deskradar](mailto:support@deskradar.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Die Deskradar-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

7. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    | NAME | Quellattribut|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | Nachname | user.surname |
    | E-Mail | user.userprincipalname |
    | | |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

9. Kopieren Sie im Abschnitt **Deskradar einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-deskradar-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens bei Deskradar

1. Melden Sie sich mit einem Administratorkonto bei Ihrer Deskradar-Instanz an. Verwenden Sie dabei die E-Mail-Adresse und das Kennwort, die Sie bei der Registrierung über einen Einladungslink festgelegt haben.

2. Öffnen Sie den Bereich **Team**, indem Sie auf das Symbol in der Randleiste klicken.

3. Wechseln Sie zur Registerkarte **Authentication** (Authentifizierung).

4. Führen Sie auf der Registerkarte **SAML 2.0** die folgenden Schritte aus:

    ![Deskradar-Konfiguration](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Aktivieren Sie die Authentifizierungsmethode **SAML**.

    b. Geben Sie im Textfeld **SAML SSO URL** (SAML-SSO-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Textfeld **Identity Provider Issuer** (Aussteller des Identitätsanbieters) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

5. Öffnen Sie die heruntergeladene Datei mit dem **Zertifikat (Base64)** in einem Text-Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Feld **Public Certificate** (Öffentliches Zertifikat) in Deskradar ein.

    ![Deskradar-Konfiguration](./media/deskradar-tutorial/15-paste-certificate.jpg)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Deskradar gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Deskradar** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Deskradar** aus.

    ![Deskradar-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-deskradar-test-user"></a>Erstellen eines Deskradar-Testbenutzers

In diesem Abschnitt erstellen Sie in Deskradar einen Benutzer namens Britta Simon. Wenden Sie sich an das  [Supportteam von Deskradar](mailto:support@deskradar.com) , um die Benutzer in der Deskradar-Plattform hinzufügen zu lassen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

**Um die Anwendung im SP-initiierten Modus zu verwenden, gehen Sie wie folgt vor:**

1. Starten Sie Ihre Deskradar-Instanz, indem Sie die URL im Browser öffnen: `https://YOURDOMAIN.deskradar.cloud` (ersetzen Sie `YOURDOMAIN` durch die Domäne Ihrer Deskradar-Instanz). 
1. Wählen Sie **Enterprise Single Sign-On** (Einmaliges Anmelden für Unternehmen) aus.

    ![Deskradar](./media/deskradar-tutorial/16-sso-button.jpg)

- Sie werden direkt bei Deskradar angemeldet, wenn Sie bereits in Active Directory angemeldet sind.
- Wenn Sie noch nicht angemeldet sind, werden Sie zum Active Directory-Anmeldeformular umgeleitet. In diesem Formular können Sie die Anmeldeinformationen Ihres Unternehmenskontos eingeben, um sich anzumelden.
- Nach der erfolgreichen Anmeldung mit den Unternehmensanmeldeinformationen gelangen Sie wieder zu Deskradar und werden dort angemeldet.

**Um die Anwendung im IDP-initiierten Modus zu verwenden, gehen Sie wie folgt vor:**

Wenn Sie im Zugriffsbereich auf die Kachel „Deskradar“ klicken, sollten Sie automatisch bei der Deskradar-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
