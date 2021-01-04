---
title: 'Tutorial: Azure Active Directory-Integration mit Workspot Control | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Workspot Control konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 65e424c6275f9990a5d5a42a54bc4131300a45bf
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895034"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Azure Active Directory-Integration mit Workspot Control

In diesem Tutorial erfahren Sie, wie Sie Workspot Control in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Workspot Control in Azure AD haben Sie folgende Möglichkeiten:

* Verwenden Sie Azure AD, um zu steuern, wer Zugriff auf Workspot Control hat.
* Ermöglichen Sie es Benutzern, sich mit ihren Azure AD-Konten automatisch bei Workspot Control anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Workspot Control konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.

* Workspot Control-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

> [!Note]
> Workspot Control unterstützt SP- und IDP-initiiertes einmaliges Anmelden.


## <a name="adding-workspot-control-from-the-gallery"></a>Hinzufügen des Workspot Control-Steuerelements aus dem Katalog

Zum Konfigurieren der Integration von Workspot Control in Azure AD müssen Sie Workspot Control aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Gehen Sie wie folgt vor, um Workspot Control aus dem Katalog hinzuzufügen:**

1. Wählen Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) den Dienst **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie **Alle Anwendungen** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Fenster die Option **Neue Anwendung** aus.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Workspot Control** ein, wählen Sie im Ergebnisbereich **Workspot Control** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Fenster „Aus Katalog hinzufügen“](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workspot Control für einen Testbenutzer namens Britta Simon.
Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workspot Control eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workspot Control müssen Sie die folgenden Schritte ausführen:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. [Konfigurieren des einmaligen Anmeldens für Workspot Control](#configure-workspot-control-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD für Britta Simon zu testen
4. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. [Erstellen eines Workspot Control-Testbenutzers](#create-a-workspot-control-test-user), um eine Entsprechung von Britta Simon in Workspot Control einzurichten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. [Testen des einmaligen Anmeldens](#test-single-sign-on), um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Workspot Control zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Workspot Control** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Fenster **SSO-Methode auswählen** den Modus **SAML** aus, um einmaliges Anmelden zu aktivieren.

    ![Fenster zum Auswählen einer SSO-Methode](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** (Stift) aus, um **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Hervorgehobenes Bearbeitungssymbol in „Grundlegende SAML-Konfiguration“](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten:

    ![Screenshot: Seite „Grundlegende SAML-Konfiguration“ zum Eingeben des Bezeichners und einer Antwort-URL sowie zum Klicken auf „Speichern“](common/idp-intiated.png)

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:<br/>
    **_https://<<i></i>INSTANZNAME>-saml.workspot.com/saml/metadata_* _

    1. Geben Sie im Textfeld _*Antwort-URL* * eine URL im folgenden Format ein:<br/>
    **_https://< <i></i>INSTANZNAME>-saml.workspot.com/saml/assertion_*_

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie „Zusätzliche URLs festlegen“ aus.

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/metadata-upload-additional-signon.png)

    Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein:<br/>
    **_https://< <i></i>INSTANZNAME>-saml.workspot.com/_* _

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte erhalten Sie vom [Supportteam für den Workspot Control-Client](mailto:support@workspot.com). Sie können sich auch im Azure-Portal die Muster im Abschnitt *Grundlegende SAML-Konfiguration* ansehen.

6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den verfügbaren Optionen herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Link zum Herunterladen des Zertifikats (Base64)](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Workspot Control einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    - **Anmelde-URL**

    - **Azure AD-Bezeichner**

    - **Abmelde-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Workspot Control

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Workspot Control an.

2. Wählen Sie auf der Symbolleiste am oberen Rand der Seite **Setup** und dann **SAML** aus.

    ![Setupoptionen](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Führen Sie auf der Seite **Security Assertion Markup Language Configuration** (Security Assertion Markup Language-Konfiguration) die folgenden Schritte aus:
 
    ![Fenster „Security Assertion Markup Language Configuration“ (Security Assertion Markup Language-Konfiguration)](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Fügen Sie im Feld **Entity ID** (Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **Signon Service URL** (URL des Anmeldediensts) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **Logout Service URL** (URL des Abmeldediensts) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Wählen Sie **Update File** (Datei aktualisieren) aus, um das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, in das X.509-Zertifikat hochzuladen.

    1. Wählen Sie **Speichern** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Fenster die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie unter den Eigenschaften für den Benutzer die folgenden Schritte aus:

    ![Fenster „Benutzereigenschaften“](common/user-properties.png)

    1. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** den Namen * *brittasimon@* yourcompanydomain.extension*** ein. Geben Sie beispielsweise **BrittaSimon@contoso. <i></i>com** ein.

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** . Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Workspot Control, um ihr die Verwendung des einmaligen Anmeldens von Azure zu ermöglichen.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** > **Workspot Control** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Workspot Control** aus.

    ![Workspot Control-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** . Wählen Sie dann im Fenster **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Fenster „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Fenster **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** aus. Klicken Sie dann auf **Auswählen** .

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Fenster **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie dann im unteren Bereich auf **Auswählen** .

7. Wählen Sie auf der Seite **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-workspot-control-test-user"></a>Erstellen eines Workspot Control-Testbenutzers

Damit sich Azure AD-Benutzer bei Workspot Control anmelden können, müssen sie in Workspot Control bereitgestellt werden. Die Bereitstellung ist eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Workspot Control als Sicherheitsadministrator an.

2. Wählen Sie auf der Symbolleiste am oberen Rand der Seite **Users** (Benutzer) und dann **Add User** (Benutzer hinzufügen) aus.

    ![Optionen „Users“ (Benutzer)](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Führen Sie im Fenster **Add a New User** (Neuen Benutzer hinzufügen) die folgenden Schritte aus:

    ![Fenster „Add a New User“ (Neuen Benutzer hinzufügen)](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen eines Benutzers ein, z. B. **Britta** .

    1. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen eines Benutzers ein, z. B. **Simon** .

    1. Geben Sie im Feld **Email** (E-Mail) die E-Mail-Adresse des Benutzers ein, z. B. **Brittasimon@contoso. <i></i>com** .

    1. Wählen Sie die geeignete Benutzerrolle aus der Dropdownliste **Role** (Rolle) aus.

    1. Wählen Sie die geeignete Benutzergruppe aus der Dropdownliste **Group** (Gruppe) aus.

    1. Wählen Sie **Add User** (Benutzer hinzufügen) aus.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den *Zugriffsbereich* .

Wenn Sie im Zugriffsbereich auf die Kachel **Workspot Control** klicken, sollten Sie automatisch bei der Workspot Control-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)