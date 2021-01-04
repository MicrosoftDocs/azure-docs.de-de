---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Confluence by resolution GmbH | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Confluence by resolution GmbH konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: c8f85c6dd42f1f4505474e03e378c0fe48d70005
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896492"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Confluence by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Confluence by resolution GmbH in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAML SSO for Confluence by resolution GmbH in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SAML SSO for Confluence by resolution GmbH hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SAML SSO for Confluence by resolution GmbH anzumelden (einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAML SSO for Confluence by resolution GmbH konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Abonnement, das für das einmalige Anmelden bei SAML SSO for Confluence by resolution GmbH aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAML SSO for Confluence by resolution GmbH unterstützt **SP** - und **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Hinzufügen von SAML SSO for Confluence by resolution GmbH aus dem Katalog

Um die Integration von SAML SSO for Confluence by resolution GmbH in Azure AD zu konfigurieren, müssen Sie SAML SSO for Confluence by resolution GmbH aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

**Um SAML SSO for Confluence by resolution GmbH aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte durch:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAML SSO for Confluence by resolution GmbH** ein, wählen Sie im Ergebnisbereich **SAML SSO for Confluence by resolution GmbH** aus, und klicken Sie dann auf **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![SAML SSO for Confluence by resolution GmbH in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei SAML SSO for Confluence by resolution GmbH mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit das einmalige Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Confluence by resolution GmbH eine Linkbeziehung eingerichtet werden.

Führen Sie die folgenden Bausteine aus, um das einmalige Anmelden mit Azure AD bei SAML SSO for Confluence by resolution GmbH zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAML SSO for Confluence by resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für SAML SSO for Confluence by resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** , um ein Gegenstück für Britta Simon in SAML SSO for Confluence by resolution GmbH bereitzustellen, das mit der Azure AD-Darstellung eines Benutzers verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte durch, um das einmalige Anmelden mit Azure AD bei SAML SSO for Confluence by resolution GmbH zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAML SSO for Confluence by resolution GmbH** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    ![Screenshot: „Grundlegende SAML-Konfiguration“ mit den hervorgehobenen Textfeldern „Bezeichner“ und „Antwort-URL“ und der ausgewählten Aktion „Speichern“](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAML SSO for Confluence by resolution GmbH](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den SAML SSO for Confluence by resolution GmbH-Client](https://www.resolution.de/go/support). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAML SSO for Confluence by resolution GmbH

1. Melden Sie sich in einem anderen Webbrowserfenster beim **SAML SSO for Confluence by resolution GmbH-Verwaltungsportal** als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons** .
    
    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Dropdownmenü die Option „Add-Ons“ hervorgehoben ist.](./media/samlssoconfluence-tutorial/addon1.png)

3. Sie werden zur Seite „Administratorzugriff“ umgeleitet. Geben Sie das Kennwort ein, und klicken Sie auf die Schaltfläche **Bestätigen** .

    ![Der Screenshot zeigt die Seite „Administratorzugriff“ mit ausgewählter Schaltfläche „Bestätigen“.](./media/samlssoconfluence-tutorial/addon2.png)

4. Klicken Sie auf der Registerkarte **ATLASSIAN MARKETPLACE** auf **Nach neuen Add-Ons suchen** . 

    ![Der Screenshot zeigt die Registerkarte „ATTLASSIAN MARKETPLACE“ mit ausgewähltem „Nach neuen Add-Ons suchen“.](./media/samlssoconfluence-tutorial/addon.png)

5. Suchen Sie nach **Einmalige SAML-Anmeldung (SSO) für Confluence** , und klicken Sie auf die Schaltfläche **Installieren** , um das neue SAML-Plug-In zu installieren.

    ![Der Screenshot zeigt die Seite „Nach neuen Add-Ons suchen“ mit dem Suchbegriff „Einmalige SAML-Anmeldung (SSO) für Confluence“ im Suchfeld und der ausgewählten Schaltfläche „Installieren“.](./media/samlssoconfluence-tutorial/addon7.png)

6. Die Installation des Plug-Ins wird gestartet. Klicken Sie auf **Schließen** .

    ![Der Screenshot zeigt das Dialogfeld „Installing“ (Wird installiert...).](./media/samlssoconfluence-tutorial/addon8.png)

    ![Screenshot: Dialogfeld „Installed and ready to go!“ (Installiert und einsatzbereit!) mit der ausgewählten Aktion „Close“ (Schließen)](./media/samlssoconfluence-tutorial/addon9.png)

7.  Klicken Sie auf **Manage** .

    ![Der Screenshot zeigt die App-Seite „Einmalige SAML-Anmeldung (SSO) für Confluence“ mit ausgewählter Schaltfläche „Manage“ (Verwalten).](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Klicken Sie auf **Konfigurieren** , um das neue Plug-In zu konfigurieren.

    ![Der Screenshot zeigt die Seite „Manage“ (Verwalten) mit ausgewählter Schaltfläche „Configure“ (Konfigurieren).](./media/samlssoconfluence-tutorial/addon11.png)

9. Dieses neue Plug-In wird auch auf der Registerkarte **BENUTZER & SICHERHEIT** angezeigt.

    ![Der Screenshot zeigt die Seite „Benutzer & Sicherheit“ mit ausgewähltem „Einmalige SAML-Anmeldung“.](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Klicken Sie auf der Seite **Konfiguration des SAML-SSO-Plug-Ins** auf die Schaltfläche **Add new IdP** (Neuen IdP hinzufügen), um die Einstellungen des Identitätsanbieters zu konfigurieren.

    ![Der Screenshot zeigt die Seite „Konfiguration des SAML-SSO-Plug-Ins“ mit ausgewählter Schaltfläche „Add new IdP“.](./media/samlssoconfluence-tutorial/addon4.png)

11. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Der Screenshot zeigt die Seite „Choose your SAML Identity Provider“ mit den hervorgehobenen Textfeldern „IdP Type“ (IdP-Typ), „Name“ und „Description“ (Beschreibung).](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Legen Sie als IdP-Typ die Option **Azure AD** fest.
    
    b. Fügen Sie den **Namen** des Identitätsanbieters (z.B. Azure AD) hinzu.
    
    c. Fügen Sie eine **Beschreibung** des Identitätsanbieters (z.B. Azure AD) hinzu.
    
    d. Klicken Sie auf **Weiter** .
    
12. Klicken Sie auf der Seite **Identitätsanbieterkonfiguration** auf die Schaltfläche **Weiter** .

    ![Der Screenshot zeigt die Seite „Identitätsanbieterkonfiguration“ mit ausgewählter Schaltfläche „Weiter“.](./media/samlssoconfluence-tutorial/addon5b.png)

13. Führen Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) die folgenden Schritte aus:

    ![Der Screenshot zeigt die Seite „Import SAML IdP Metadata“ mit den ausgewählten Schaltflächen „Importieren“, „Datei laden“ und „Weiter“.](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Klicken Sie auf die Schaltfläche **Datei laden** , und wählen Sie die in Schritt 5 heruntergeladene Metadaten-XML-Datei aus.

    b. Klicken Sie auf die Schaltfläche **Importieren** .
    
    c. Warten Sie kurz, bis der Import erfolgreich ausgeführt wurde.
    
    d. Klicken Sie auf die Schaltfläche **Weiter** .
    
14. Klicken Sie auf der Seite **User ID attribute and transformation** (Benutzer-ID-Attribut und Transformation) auf die Schaltfläche **Weiter** .

    ![Der Screenshot zeigt die Seite „User ID attribute and transformation“ mit ausgewählter Schaltfläche „Weiter“.](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. Klicken Sie auf der Seite **User creation and update** (Benutzererstellung und -aktualisierung) auf **Save & Next** (Speichern und weiter), um die Einstellungen zu speichern.   
    
    ![Der Screenshot zeigt die Seite „User creation and update“ mit ausgewählter Option „Save and Next“.](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. Klicken Sie auf der Seite **Testen Ihrer Einstellungen** auf **Skip test & configure manually** (Test überspringen und manuell konfigurieren), um den Benutzertest vorerst zu überspringen. Dieser wird im nächsten Abschnitt durchgeführt und erfordert einige Einstellungen im Azure-Portal. 
    
    ![Der Screenshot zeigt die Seite „Testen Ihrer Einstellungen“ mit ausgewählter Schaltfläche „Skip test & configure manually“.](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. Klicken Sie im angezeigten Dialogfeld **Das Überspringen des Tests bedeutet...** auf **OK** .
    
    ![Einmaliges Anmelden konfigurieren](./media/samlssoconfluence-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung** .  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAML SSO for Confluence by resolution GmbH gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **SAML SSO for Confluence by resolution GmbH** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **SAML SSO for Confluence by resolution GmbH** ein, und wählen Sie den Eintrag aus.

    ![Der SAML SSO for Confluence by resolution GmbH-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Erstellen eines Testbenutzers für SAML SSO for Confluence by resolution GmbH

Um Azure AD-Benutzern die Anmeldung bei SAML SSO for Confluence by resolution GmbH zu ermöglichen, müssen diese in SAML SSO for Confluence by resolution GmbH bereitgestellt werden.  
Die Bereitstellung in SAML SSO for Confluence by resolution GmbH ist ein manueller Vorgang.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Unternehmenswebsite von SAML SSO for Confluence by resolution GmbH als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung** .

    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Menü die Option „Benutzerverwaltung“ hervorgehoben ist.](./media/samlssoconfluence-tutorial/user1.png) 

3. Klicken Sie im Abschnitt „Benutzer“ auf die Registerkarte **Benutzer hinzufügen** . Führen Sie auf der Dialogfeldseite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Mitarbeiter hinzufügen](./media/samlssoconfluence-tutorial/user2.png) 

    a. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. von Britta Simon, ein.

    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort von Britta Simon ein.

    e. Klicken Sie auf **Kennwort bestätigen** , um das Kennwort erneut einzugeben.
    
    f. Klicken Sie auf die Schaltfläche **Hinzufügen** .

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAML SSO for Confluence by resolution GmbH“ klicken, sollten Sie automatisch bei Ihrer SAML SSO for Confluence by resolution GmbH-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)