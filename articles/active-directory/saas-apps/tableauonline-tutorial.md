---
title: 'Tutorial: Azure Active Directory-Integration von Tableau Online | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Tableau Online konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 7841f09b113b4bdf7eacddbbfc5f054bf69b1750
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517819"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Tableau Online

In diesem Tutorial erfahren Sie, wie Sie Tableau Online in Azure Active Directory (Azure AD) integrieren. Die Integration von Tableau Online in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Tableau Online hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Tableau Online anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Tableau Online-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Tableau Online unterstützt **SP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren von Tableau Online können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-tableau-online-from-the-gallery"></a>Hinzufügen von Tableau Online aus dem Katalog

Zum Konfigurieren der Integration von Tableau Online in Azure AD müssen Sie Tableau Online aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Tableau Online** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Tableau Online** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Tableau Online basierend auf einem Testbenutzer namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Tableau Online eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Tableau Online die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Tableau Online](#configure-tableau-online-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Tableau Online-Testbenutzers](#create-tableau-online-test-user)** , um eine Entsprechung von B. Simon in Tableau Online zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Tableau Online die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Tableau Online** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Tableau Online](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`.

    > [!NOTE]
    > Den Wert für `<entityid>` erhalten Sie in diesem Tutorial im Abschnitt **Tableau Online einrichten**. Der Wert der Entitäts-ID entspricht dem **Azure AD-Bezeichner** im Abschnitt **Tableau Online einrichten**.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Tableau Online einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Beispiel: BrittaSimon\@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Tableau Online gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Tableau Online** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Tableau Online** aus.

    ![Tableau Online-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-tableau-online-sso"></a>Konfigurieren des einmaligen Anmeldens für Tableau Online

1. Melden Sie sich in einem anderen Browserfenster in Ihrer Tableau Online-Anwendung als Administrator an. Wechseln Sie zu **Einstellungen** und dann zu **Authentifizierung**.

    ![Screenshot, der die im Menü „Settings“ (Einstellungen) ausgewählte Option „Authentication“ (Authentifizierung) zeigt](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Gehen Sie im Abschnitt **Authentifizierungstypen** zum Aktivieren von SAML wie folgt vor. Aktivieren Sie die Option **Enable an additional authentication method** (Zusätzliche Authentifizierungsmethode aktivieren) und anschließend das Kontrollkästchen **SAML**.

    ![Screenshot: Abschnitt „Authentication types“ (Authentifizierungstypen), in dem Sie die beschriebenen Werte auswählen können](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Scrollen Sie nach unten bis zum Abschnitt **Import metadata file into Tableau Online** (Metadatendatei in Tableau Online importieren).  Klicken Sie auf „Durchsuchen“, und importieren Sie die Metadatendatei, die Sie aus Azure AD heruntergeladen haben. Klicken Sie anschließend auf **Übernehmen**.

   ![Screenshot: Abschnitt, in dem Sie die Metadatendatei importieren können](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Fügen Sie im Abschnitt **Match assertions** (Assertionen abgleichen) den entsprechenden Identitätsanbieter-Assertionsnamen für **E-Mail-Adresse** , **Vorname** und **Nachname** ein. Gehen Sie wie folgt vor, um diese Informationen aus Azure AD abzurufen: 
  
    a. Wechseln Sie im Azure-Portal zur Anwendungsintegrationsseite von **Tableau Online**.

    b. Klicken Sie im Abschnitt **Benutzerattribute und Ansprüche** auf das Bearbeitungssymbol.

   ![Screenshot: Abschnitt „Benutzerattribute und Ansprüche“, in dem Sie das Bearbeitungssymbol auswählen können](./media/tableauonline-tutorial/attributesection.png)

    c. Kopieren Sie den Namespacewert für die Attribute „givenname“, „email“ und „surname“ mithilfe der folgenden Schritte:

   ![Screenshot: Attribute „Givenname“, „Surname“ und „Emailaddress“](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klicken Sie auf den Wert **user.givenname**.

    e. Kopieren Sie den Wert aus dem Textfeld **Namespace**.

    ![Screenshot: Abschnitt „Benutzeransprüche verwalten“, in dem Sie den Namespace eingeben können](./media/tableauonline-tutorial/attributesection2.png)

    f. Befolgen Sie die vorherigen Anweisungen, um die Namespacewerte für „email“ und „surname“ zu kopieren.

    g. Wechseln Sie zur Tableau Online-Anwendung, und legen Sie den Abschnitt **User Attributes & Claim** (Benutzerattribute und Ansprüche) dann wie folgt fest:

    * E-Mail: **mail** oder **userprincipalname**

    * Vorname: **givenname**

    * Nachname: **surname**

    ![Screenshot: Abschnitt „Match attributes“ (Attribute abgleichen), in dem Sie die Werte eingeben können](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Erstellen eines Tableau Online-Testbenutzers

In diesem Abschnitt erstellen Sie in Tableau Online einen Benutzer namens Britta Simon.

1. Klicken Sie in **Tableau Online** auf **Settings** (Einstellungen) und dann auf den Abschnitt **Authentication** (Authentifizierung). Scrollen Sie nach unten zum Abschnitt **Manage Users** (Benutzer verwalten). Klicken Sie auf **Add Users** (Benutzer hinzufügen) und dann auf **Enter Email Addresses** (E-Mail-Adressen eingeben).
  
    ![Screenshot: Abschnitt „Manage users“ (Benutzer verwalten), in dem Sie „Add users“ (Benutzer hinzufügen) auswählen können](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Wählen Sie **Add users for (SAML) authentication** (Benutzer für (SAML-)Authentifizierung auswählen). Fügen Sie im Textfeld **Enter Email Addresses** (E-Mail-Adressen eingeben) den Eintrag „britta.simon\@contoso.com“ hinzu.
  
    ![Screenshot: Seite „Add Users“ (Benutzer hinzufügen), auf der Sie eine E-Mail-Adresse eingeben können](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klicken Sie auf **Benutzer hinzufügen**.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Tableau Online“ klicken, sollten Sie automatisch bei der Tableau Online-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)