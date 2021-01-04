---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Jira by resolution GmbH | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Jira by resolution GmbH konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: fe241a3fd74e1421f1bd3d39087fe776ee7b61d9
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891515"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Jira by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Jira by resolution GmbH mit Azure Active Directory (Azure AD) einrichten.
Die Integration von SAML SSO for Jira by resolution GmbH in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer sich mit dem SAML SSO-Plug-In der resolution GmbH bei Jira anmelden kann.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch unter Verwendung von SAML SSO for Jira by resolution GmbH anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration und SAML SSO for Jira by resolution GmbH konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über keine Azure AD-Umgebung verfügen, erhalten Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion.
* Ein Abonnement, das für das einmalige Anmelden bei SAML SSO for Jira by resolution GmbH aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAML SSO for Jira by resolution GmbH unterstützt **SP** - und **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Hinzufügen einer Unternehmensanwendung für einmaliges Anmelden

Um das einmalige Anmelden in Azure AD einrichten zu können, müssen Sie eine neue Unternehmensanwendung hinzufügen. Im Katalog steht mit **SAML SSO for Jira by resolution GmbH** eine entsprechend vorkonfigurierte Anwendung zur Verfügung.

**Um SAML SSO for Jira by resolution GmbH aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und klicken Sie auf **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie zum Hinzufügen einer neuen Anwendung am oberen Rand des Dialogfelds auf die Schaltfläche **Neue Anwendung**.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAML SSO for Jira by resolution GmbH** ein, wählen Sie im Ergebnisbereich **SAML SSO for Jira by resolution GmbH** aus, und klicken Sie anschließend auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen. Der Name der Unternehmens-App kann auf Wunsch geändert werden.

     ![SAML SSO for Jira by resolution GmbH in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurieren und Testen des einmaligen Anmeldens mit dem SAML SSO-Plug-In und Azure AD

In diesem Abschnitt wird das einmalige Anmelden bei Jira für einen Azure AD-Benutzer konfiguriert und getestet. Hierzu wird ein Testbenutzer namens **Britta Simon** verwendet.
Damit das einmalige Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Jira by resolution GmbH eine Linkbeziehung eingerichtet werden.

Um das einmalige Anmelden zu konfigurieren und zu testen, sind folgende Schritte erforderlich:

1. **[Konfigurieren der Azure AD-Unternehmensanwendung für einmaliges Anmelden:](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** Konfigurieren Sie die Azure AD-Unternehmensanwendung für einmaliges Anmelden.
2. **[Konfigurieren des SAML SSO-Plug-Ins Ihrer Jira-Instanz:](#configure-the-saml-sso-plugin-of-your-jira-instance)** Konfigurieren Sie die SSO-Einstellungen auf der Anwendungsseite.
3. **[Erstellen eines Azure AD-Testbenutzers:](#create-an-azure-ad-test-user)** Erstellen Sie einen Testbenutzer in Azure AD.
1. **[Zuweisen des Azure AD-Testbenutzers:](#assign-the-azure-ad-test-user)** Ermöglichen Sie dem Testbenutzer die Verwendung des einmaligen Anmeldens aufseiten von Azure.
1. **[Erstellen des Testbenutzers in Jira:](#create-the-test-user-also-in-jira)** Erstellen Sie in Jira ein Pendant für den Azure AD-Testbenutzer.
1. **[Testen des einmaligen Anmeldens:](#test-single-sign-on)** Vergewissern Sie sich, dass die Konfiguration funktioniert.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurieren der Azure AD-Unternehmensanwendung für einmaliges Anmelden

In diesem Abschnitt richten Sie das einmalige Anmelden über das Azure-Portal ein.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden mit SAML SSO for Jira by resolution GmbH zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) unter der soeben erstellten Unternehmensanwendung **SAML SSO for Jira by resolution GmbH** im linken Bereich die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie unter **SSO-Methode auswählen** den Modus **SAML** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie anschließend auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“ mit den hervorgehobenen Textfeldern „Bezeichner“ und „Antwort-URL“ und der ausgewählten Schaltfläche „Speichern“](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAML SSO for Jira by resolution GmbH](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ersetzen Sie **\<server-base-url>** für den Bezeichner, die Antwort-URL und die Anmelde-URL durch die Basis-URL Ihrer Jira-Instanz. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen. Sollten Sie Probleme haben, wenden Sie sich an das [Supportteam für den SAML SSO for Jira by resolution GmbH-Client](https://www.resolution.de/go/support).

4. Laden Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die **Verbundmetadaten-XML** herunter, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurieren des SAML SSO-Plug-Ins Ihrer Jira-Instanz 

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer Jira-Instanz als Administrator an.

2. Zeigen Sie auf der rechten Seite auf das Zahnrad, und klicken Sie auf **Manage apps** (Apps verwalten).
    
    ![Screenshot: Pfeil, der auf das Zahnradsymbol zeigt, und ausgewählte Option „Manage apps“ (Apps verwalten) in der Dropdownliste](./media/samlssojira-tutorial/addon1.png)

3. Sollten Sie auf die Seite für den Administratorzugriff umgeleitet werden, geben Sie dort Ihr **Kennwort** ein, und klicken Sie auf die **Bestätigungsschaltfläche**.

    ![Screenshot: Seite für den Administratorzugriff](./media/samlssojira-tutorial/addon2.png)

4. Jira leitet Sie normalerweise zum Atlassian-Marketplace um. Sollte das nicht der Fall sein, klicken Sie im linken Bereich auf **Find new apps** (Nach neuen Apps suchen). Suchen Sie nach **SAML Single Sign On (SSO) for JIRA** , und klicken Sie auf die Schaltfläche **Install** (Installieren), um das SAML-Plug-In zu installieren.

    ![Screenshot: Seite „Atlassian Marketplace for JIRA“ (Atlassian-Marketplace für JIRA) mit einem Pfeil, der auf die Schaltfläche „Install“ (Installieren) für die App „SAML Single Sign On (SSO) Jira, SAML/SSO“ zeigt](./media/samlssojira-tutorial/store.png)

5. Die Installation des Plug-Ins wird gestartet. Klicken Sie nach Abschluss des Vorgangs auf die Schaltfläche **Close** (Schließen).

    ![Screenshot: Dialogfeld „Installing“ (Wird installiert...)](./media/samlssojira-tutorial/store-2.png)

    ![Screenshot: Dialogfeld „Installed and ready to go!“ (Installiert und einsatzbereit!) mit ausgewählter Schaltfläche „Close“ (Schließen)](./media/samlssojira-tutorial/store-3.png)

6. Klicken Sie anschließend auf **Manage** (Verwalten).

    ![Screenshot: App „SAML Single Sign On (SSO) Jira, SAML/SSO“ mit ausgewählter Schaltfläche „Manage“ (Verwalten)](./media/samlssojira-tutorial/store-4.png)
    
8. Klicken Sie auf **Configure** (Konfigurieren), um das soeben installierte Plug-In zu konfigurieren.

    ![Screenshot: Seite „Manage apps“ (Apps verwalten) mit ausgewählter Schaltfläche „Configure“ (Konfigurieren) für die App „SAML SingleSignOn for Jira“](./media/samlssojira-tutorial/store-5.png)

9. Klicken Sie im **Konfigurations-Assistenten für das SAML SSO-Plug-In** auf **Add new IdP** (Neuen IdP hinzufügen), um Azure AD als neuen Identitätsanbieter zu konfigurieren.

    ![Screenshot: Willkommensseite mit ausgewählter Schaltfläche „Add new IdP“ (Neuen IdP hinzufügen)](./media/samlssojira-tutorial/addon4.png) 

10. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Screenshot: Seite „Choose your SAML Identity Provider“ (SAML-Identitätsanbieter auswählen) mit hervorgehobenen Textfeldern „IdP Type“ (IdP-Typ) und „Name“ sowie mit ausgewählter Schaltfläche „Next“ (Weiter)](./media/samlssojira-tutorial/addon5a.png)
 
    a. Legen Sie als IdP-Typ die Option **Azure AD** fest.
    
    b. Fügen Sie den **Namen** des Identitätsanbieters hinzu (beispielsweise „Azure AD“).
    
    c. Fügen Sie optional eine **Beschreibung** des Identitätsanbieters hinzu (beispielsweise „Azure AD“).
    
    d. Klicken Sie auf **Weiter**.
    
11. Klicken Sie auf der Seite **Identity provider configuration** (Identitätsanbieterkonfiguration) auf die Schaltfläche **Next** (Weiter).
 
    ![Screenshot: Seite für die Identitätsanbieterkonfiguration](./media/samlssojira-tutorial/addon5b.png)

12. Führen Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) die folgenden Schritte aus:

    ![Screenshot: Seite „Import SAML IdP Metadata“ (SAML-IdP-Metadaten importieren) mit ausgewählter Aktion „Select Metadata XML File“ (Metadaten-XML-Datei auswählen)](./media/samlssojira-tutorial/addon5c.png)

    a. Klicken Sie auf die Schaltfläche **Select Metadata XML File** (Metadaten-XML-Datei auswählen), und wählen Sie die **Verbundmetadaten-XML-Datei** aus, die Sie zuvor heruntergeladen haben.

    b. Klicken Sie auf die Schaltfläche **Import** (Importieren).
     
    c. Warten Sie kurz, bis der Importvorgang abgeschlossen ist.  
     
    d. Klicken Sie auf die Schaltfläche **Weiter**.
    
13. Klicken Sie auf der Seite **User ID attribute and transformation** (Benutzer-ID-Attribut und Transformation) auf die Schaltfläche **Next** (Weiter).

    ![Screenshot: Seite „User ID attribute and transformation“ (Benutzer-ID-Attribut und Transformation) mit ausgewählter Schaltfläche „Next“ (Weiter)](./media/samlssojira-tutorial/addon5d.png)
    
14. Klicken Sie auf der Seite **User creation and update** (Benutzererstellung und -aktualisierung) auf **Save & Next** (Speichern und weiter), um die Einstellungen zu speichern.
    
    ![Screenshot: Seite „User creation and update“ (Benutzererstellung und -aktualisierung) mit ausgewählter Option „Save & Next“ (Speichern und weiter)](./media/samlssojira-tutorial/addon6a.png)
    
15. Klicken Sie auf der Seite **Test your settings** (Einstellungen testen) auf **Skip test & configure manually** (Test überspringen und manuell konfigurieren), um den Benutzertest vorerst zu überspringen. Dieser wird im nächsten Abschnitt durchgeführt und erfordert einige Einstellungen im Azure-Portal.
    
    ![Screenshot: Seite „Test your settings“ (Einstellungen testen) mit ausgewählter Schaltfläche „Skip test & configure manually“ (Test überspringen und manuell konfigurieren)](./media/samlssojira-tutorial/addon6b.png)
    
16. Klicken Sie auf **OK** , um die Warnung zu überspringen.
    
    ![Screenshot: Warnungsdialogfeld mit ausgewählter Schaltfläche „OK“](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal. Mit diesem Benutzer wird das einmalige Anmelden getestet.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie im oberen Bildschirmbereich die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **Britta Simon** ein.
  
    b. Geben Sie im Feld **Benutzername** die Zeichenfolge <b>BrittaSimon@contoso.com</b> ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Kennwortfeld.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt wird Britta Simon der Unternehmensanwendung hinzugefügt, um das einmalige Anmelden nutzen zu können.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus. 

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Suchen Sie in der Anwendungsliste nach der Unternehmensanwendung, die Sie am Anfang dieses Tutorials erstellt haben. Wenn Sie sich an die Schritte des Tutorials gehalten haben, lautet der Name **SAML SSO for Jira by resolution GmbH**. Falls Sie einen anderen Namen verwendet haben, suchen Sie nach diesem Namen.

    ![SAML SSO for Jira by resolution GmbH-Link in der Anwendungsliste](common/all-applications.png)

3. Klicken Sie im linken Bereich auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-the-test-user-also-in-jira"></a>Erstellen des Testbenutzers in Jira

Um Azure AD-Benutzern die Anmeldung bei SAML SSO for Jira by resolution GmbH zu ermöglichen, müssen sie in SAML SSO for Jira by resolution GmbH bereitgestellt werden. Im Rahmen dieses Tutorials muss die Bereitstellung manuell durchgeführt werden. Für das SAML SSO-Plug-In von resolution stehen allerdings auch andere Bereitstellungsmodelle zur Verfügung – beispielsweise für die **Just-In-Time-Bereitstellung**. Weitere Informationen finden Sie in der [Dokumentation für SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Sollten Sie Fragen haben, wenden Sie sich an den [Support von resolution](https://www.resolution.de/go/support).

**Führen Sie zum manuellen Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Jira-Instanz als Administrator an.

2. Zeigen Sie auf das Zahnrad, und wählen Sie **User management** (Benutzerverwaltung) aus.

   ![Screenshot: Pfeil, der auf das Zahnradsymbol zeigt, und ausgewählte Option „User management“ (Benutzerverwaltung) in der Dropdownliste](./media/samlssojira-tutorial/user1.png)

3. Sollten Sie auf die Seite für den Administratorzugriff umgeleitet werden, geben Sie dort Ihr **Kennwort** ein, und klicken Sie anschließend auf die **Bestätigungsschaltfläche**.

    ![Screenshot: Seite für den Administratorzugriff mit hervorgehobenem Textfeld „Password“ (Kennwort)](./media/samlssojira-tutorial/user2.png) 

4. Klicken Sie im Registerkartenabschnitt **User management** (Benutzerverwaltung) auf **create user** (Benutzer erstellen).

    ![Screenshot: Registerkarte „User management“ (Benutzerverwaltung) mit ausgewählter Schaltfläche „Create user“ (Benutzer erstellen)](./media/samlssojira-tutorial/user3-new.png) 

5. Führen Sie auf der Dialogfeldseite **Create new user** (Neuen Benutzer erstellen) die folgenden Schritte aus. Der erstellte Benutzer muss exakt dem Benutzer in Azure AD entsprechen:

    ![Mitarbeiter hinzufügen](./media/samlssojira-tutorial/user4-new.png) 

    a. Geben Sie im Textfeld **Email address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein: <b>BrittaSimon@contoso.com</b>.

    b. Geben Sie im Textfeld **Full Name** (Vollständiger Name) den vollständigen Namen des Benutzers ein: **Britta Simon**.

    c. Geben Sie im Textfeld **Username** (Benutzername) die E-Mail-Adresse des Benutzers ein: <b>BrittaSimon@contoso.com</b>. 

    d. Geben Sie im Textfeld **Password** (Kennwort) das Kennwort des Benutzers ein.

    e. Klicken Sie auf **Create user** (Benutzer erstellen), um die Benutzererstellung abzuschließen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAML SSO for Jira by resolution GmbH“ klicken, sollten Sie automatisch bei Ihrer SAML SSO for Jira by resolution GmbH-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

Sie können einmaliges Anmelden auch testen, wenn Sie zu `https://<server-base-url>/plugins/servlet/samlsso` navigieren. Ersetzen Sie **\<server-base-url>** durch die Basis-URL Ihrer Jira-Instanz.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Aktivieren der SSO-Umleitung für Jira

Wie im vorherigen Abschnitt bereits erwähnt kann einmaliges Anmelden derzeit auf zwei Arten ausgelöst werden: entweder über das **Azure-Portal** oder unter Verwendung eines **speziellen Links zu Ihrer Jira-Instanz**. Mit dem SAML SSO-Plug-In der resolution GmbH können Sie das einmalige Anmelden auslösen, indem Sie einfach **auf eine beliebige URL zugreifen, die auf Ihre Jira-Instanz verweist**.

Nach Aktivierung einer Option im Plug-In werden im Wesentlichen alle Benutzer, die auf Jira zugreifen, zum einmaligen Anmelden umgeleitet.

Gehen Sie in **Ihrer Jira-Instanz** wie folgt vor, um die SSO-Umleitung zu aktivieren:

1. Öffnen Sie die Konfigurationsseite des SAML SSO-Plug-Ins in Jira.
1. Klicken Sie im linken Bereich auf **Redirection** (Umleitung).

   ![Partieller Screenshot der Jira-Seite für die Konfiguration des SAML SingleSignOn-Plug-Ins, in dem im linken Navigationsbereich der Link „Redirection“ (Umleitung) hervorgehoben ist](./media/samlssojira-tutorial/ssore1.png)

1. Aktivieren Sie das Kontrollkästchen **Enable SSO Redirect** (SSO-Umleitung aktivieren).

   ![Partieller Screenshot der Jira-Seite für die Konfiguration des SAML SingleSignOn-Plug-Ins, in dem das aktivierte Kontrollkästchen „Enable SSO Redirect“ (SSO-Umleitung aktivieren) hervorgehoben ist](./media/samlssojira-tutorial/ssore2.png) 

1. Klicken Sie in der rechten oberen Ecke auf die Schaltfläche **Save Settings** (Einstellungen speichern).

Nach Aktivierung der Option ist die Eingabeaufforderung für Benutzername und Kennwort weiterhin unter `https://\<server-base-url>/login.jsp?nosso` erreichbar, wenn das Kontrollkästchen **Enable nosso** („nosso“ aktivieren) aktiviert ist. **\<server-base-url>** muss wie immer durch Ihre Basis-URL ersetzt werden.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)