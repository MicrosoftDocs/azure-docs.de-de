---
title: 'Tutorial: Azure Active Directory-Integration mit PageDNA | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und PageDNA konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: e1ccd337851f8242526362675a11280e33584082
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993923"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Tutorial: Azure Active Directory-Integration mit PageDNA

In diesem Tutorial erfahren Sie, wie Sie PageDNA in Azure Active Directory (Azure AD) integrieren.

Die Integration von PageDNA in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf PageDNA hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei PageDNA anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Details zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit PageDNA konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein PageDNA-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren PageDNA in Azure AD.

PageDNA unterstützt die folgenden Features:

* SP-initiiertes einmaliges Anmelden (SSO).

* Just-In-Time-Benutzerbereitstellung.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Hinzufügen von PageDNA aus dem Azure Marketplace

Zum Konfigurieren der Integration von PageDNA in Azure AD müssen Sie PageDNA aus dem Azure Marketplace zur Liste der verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com?azure-portal=true) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie oben im Fenster **+Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **PageDNA** ein. Wählen Sie in den Suchergebnissen **PageDNA** aus, und wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![PageDNA in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei PageDNA mithilfe einer Testbenutzerin namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in PageDNA eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit PageDNA müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für PageDNA](#configure-pagedna-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Erstellen eines PageDNA-Testbenutzers](#create-a-pagedna-test-user)** , damit in PageDNA ein Benutzer namens „Britta Simon“ enthalten ist, der mit dem Azure AD-Benutzer namens „Britta Simon“ verknüpft ist.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei PageDNA die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **PageDNA** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol), um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für PageDNA](common/sp-identifier.png)

    1. Geben Sie im Feld **Anmelde-URL** eine URL nach einem der folgenden Muster ein:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL nach einem der folgenden Muster ein:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Diese Werte erhalten Sie vom [PageDNA-Supportteam](mailto:success@pagedna.com). Sie können sich auch die Muster im Bereich **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um ein **Zertifikat (Rohdaten)** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Option zum Herunterladen des Zertifikats (Rohdaten)](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **PageDNA einrichten** die URL(s), die Sie benötigen:

   * **Anmelde-URL**
   * **Azure AD-Bezeichner**
   * **Abmelde-URL**

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für PageDNA

Um das einmalige Anmelden auf der Seite von PageDNA zu konfigurieren, senden Sie das heruntergeladene Zertifikat (Rohdaten) und die entsprechenden aus dem Azure-Portal kopierten URLs an das [PageDNA-Supportteam](mailto:success@pagedna.com). Das PageDNA-Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens Britta Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory**   > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie oben im Bildschirm die Option **+ Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** folgende Schritte aus:

    ![Der Bereich „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BrittaSimon@\@\<yourcompanydomain>.\<extension>** ein. Beispiel: **BrittaSimon\@contoso.com**.

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie der Benutzerin Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf PageDNA erteilen.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **PageDNA** aus.

    ![Bereich für Unternehmensanwendungen](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **PageDNA** aus.

    ![PageDNA in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im linken Bereich unter **VERWALTEN** die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **+ Benutzer hinzufügen** aus, und wählen Sie anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** und anschließend unten im Bereich **Auswählen** aus.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie unten im Bereich die Option **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-pagedna-test-user"></a>Erstellen eines PageDNA-Testbenutzers

Eine Benutzerin namens „Britta Simon“ wird jetzt in PageDNA erstellt. Sie müssen nichts tun, um diesen Benutzer erstellen. PageDNA unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Ist noch keine Benutzerin namens „Britta Simon“ in PageDNA vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Bei der Auswahl von **PageDNA** im Portal „Meine Apps“ sollten Sie automatisch beim PageDNA-Abonnement angemeldet werden, für das Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

* [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

* [Was ist bedingter Zugriff?](../conditional-access/overview.md)