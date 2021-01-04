---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit 8x8 | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und 8x8 konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 81a7efea268600e661981b35f79149fe814ef084
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180675"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit 8x8

In diesem Tutorial erfahren Sie, wie Sie 8x8 in Azure Active Directory (Azure AD) integrieren. Die Integration von 8x8 in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf 8x8 hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei 8x8 anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein 8x8-Abonnement

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* 8x8 unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-8x8-from-the-gallery"></a>Hinzufügen von 8x8 über den Katalog

Zum Konfigurieren der Integration von 8x8 in Azure AD müssen Sie 8x8 aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **8x8** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **8x8** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für 8x8

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit 8x8 mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in 8x8 eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit 8x8 die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für 8x8](#configure-8x8-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines 8x8-Testbenutzers](#create-8x8-test-user)** , um in 8x8 eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **8x8** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL ein: `https://sso.8x8.com/saml2`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL ein: `https://sso.8x8.com/saml2`.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer. Sie verwenden das Zertifikat später in diesem Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für 8x8**.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **8x8 einrichten** die URLs. Sie verwenden diese Werte später im Tutorial.

    ![Kopieren der Konfiguration-URLs](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf 8x8 gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **8x8** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-8x8-sso"></a>Konfigurieren des einmaligen Anmeldens für 8x8

Der nächste Teil des Tutorials hängt von der Art Ihres 8x8-Abonnements ab:

* Kunden mit 8x8-Editionen und X Series-Kunden, die Configuration Manager für die Verwaltung nutzen, finden weitere Informationen unter [Konfigurieren der 8x8-Verwaltungskonsole](#configure-8x8-admin-console).
* Virtual Office-Kunden, die Account Manager für die Verwaltung verwenden, finden weitere Informationen unter [Konfigurieren von 8x8 Account Manager](#configure-8x8-account-manager).

### <a name="configure-8x8-admin-console"></a>Konfigurieren der 8x8-Verwaltungskonsole

1. Wenn Sie die Konfiguration in 8x8 automatisieren möchten, müssen Sie die **Browsererweiterung zur sicheren Anmeldung bei „Meine Apps“** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **8x8 einrichten**, um zur 8x8-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei 8x8 anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie 8x8 manuell einrichten möchten, müssen Sie sich bei der [Verwaltungskonsole](https://admin.8x8.com/) von 8x8 als Administrator anmelden.

1. Klicken Sie auf der Startseite auf **Identity Management** (Identitätsverwaltung).

    ![Screenshot, auf dem die Kachel „Identity Management“ hervorgehoben ist](./media/8x8virtualoffice-tutorial/configure1.png)

1. Aktivieren Sie **Single Sign On (SSO)** (Einmaliges Anmelden (SSO)), und wählen Sie dann **Microsoft Azure AD** aus.

    ![Screenshot, auf dem die Optionen „Einmaliges Anmelden (S S O)“ und „Microsoft Azure A D“ hervorgehoben sind](./media/8x8virtualoffice-tutorial/configure2.png)

1. Kopieren Sie die drei URLs und das Signaturzertifikat von der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** in Azure AD in den Abschnitt **Microsoft Azure AD SAML Settings** (Microsoft Azure AD-SAML-Einstellungen) in der 8x8-Verwaltungskonsole.

    ![8x8-Verwaltungskonsole](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Kopieren Sie den Wert der **Anmelde-URL** in **IDP Login URL** (IDP-Anmelde-URL).

    b. Kopieren Sie den Wert unter **Azure AD-Bezeichner** in das Feld **IDP Issuer URL/URN** (IDP-Aussteller-URL/-URN).

    c. Kopieren Sie den Wert der **Abmelde-URL** in das Feld **IDP Logout URL** (IDP-Abmelde-URL).

    d. Laden Sie das **Zertifikat (Base64)** herunter, und laden Sie es unter **Certificate** (Zertifikat) hoch.

    e. Klicken Sie auf **Speichern**.

### <a name="configure-8x8-account-manager"></a>Konfigurieren von 8x8 Account Manager

1. Melden Sie sich bei Ihrem 8x8 Virtual Office-Mandanten als Administrator an.

1. Wählen Sie im Anwendungsbereich die Option **Virtual Office Account Mgr** (Virtual Office-Konto-Manager) aus.

    ![Screenshot, auf dem die Kachel „Virtual Office Account M G R“ hervorgehoben ist](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Wählen Sie das Konto **Business** (Unternehmen) aus, und klicken Sie anschließend auf die Schaltfläche **Sign In** (Anmelden).

    ![Screenshot, auf dem die Option „Business“ und die Schaltfläche „Sign In“ hervorgehoben sind](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klicken Sie auf der Menüleiste auf die Registerkarte **ACCOUNTS** (Konten).

    ![Screenshot, auf dem in der Menüliste die Registerkarte „Accounts“ hervorgehoben ist](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klicken Sie in der Liste unter „Accounts“ (Konten) auf **Single Sign On** (Einmaliges Anmelden).

    ![Screenshot, auf dem die Option „Einmaliges Anmelden“ hervorgehoben ist](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Wählen Sie als Authentifizierungsmethode die Option **Einmaliges Anmelden** aus, und klicken Sie anschließend auf **SAML**.

    ![Screenshot, auf dem „S A M L“ unter „Einmaliges Anmelden“ hervorgehoben ist](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Führen Sie im Abschnitt **SAML Single Sign-On** die folgenden Schritte aus:

    ![App-seitige Konfiguration](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Fügen Sie im Textfeld **Sign-In URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie im Textfeld **Sign-Out URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf die Schaltfläche **Browse** (Durchsuchen), um das aus dem Azure-Portal heruntergeladene Zertifikat hochzuladen.

    e. Klicken Sie auf die Schaltfläche **Save** .

### <a name="create-8x8-test-user"></a>Erstellen eines 8x8-Testbenutzers

In diesem Abschnitt erstellen Sie in 8x8 einen Benutzer namens Britta Simon. Wenden Sie sich an das [8x8-Supportteam](https://www.8x8.com/about-us/contact-us), um die Benutzer auf der 8x8-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für 8x8 weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die 8x8-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der 8x8-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Zugriffsbereich verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie im Zugriffsbereich auf die Kachel „8x8“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der 8x8-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von 8x8 können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Daten Ihrer Organisation schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.