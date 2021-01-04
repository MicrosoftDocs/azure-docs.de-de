---
title: 'Tutorial: Azure Active Directory-Integration mit Kantega SSO for JIRA | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kantega SSO for JIRA konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 9643d0e63e85a9b500021a415e3cdaf3edc756c5
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608730"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Tutorial: Azure Active Directory-Integration mit Kantega SSO for JIRA

In diesem Tutorial erfahren Sie, wie Sie Kantega SSO for JIRA in Azure Active Directory (Azure AD) integrieren.
Die Integration von Kantega SSO for JIRA in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Kantega SSO for JIRA hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Kantega SSO for JIRA anzumelden (einmaliges Anmelden, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Kantega SSO for JIRA konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Kantega SSO for JIRA-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Kantega SSO for JIRA unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Hinzufügen von Kantega SSO for JIRA aus dem Katalog

Zum Konfigurieren der Integration von Kantega SSO for JIRA in Azure AD müssen Sie Kantega SSO for JIRA aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Kantega SSO for JIRA aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Kantega SSO for JIRA** ein, wählen Sie im Ergebnisbereich den Eintrag **Kantega SSO for JIRA** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Kantega SSO for JIRA in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Kantega SSO for JIRA basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kantega SSO for JIRA eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Kantega SSO for JIRA müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Kantega SSO for JIRA](#configure-kantega-sso-for-jira-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Kantega SSO for JIRA-Testbenutzers](#create-kantega-sso-for-jira-test-user)** , um eine Entsprechung von Britta Simon in Kantega SSO for JIRA zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Kantega SSO for JIRA die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Kantega SSO for JIRA** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![Screenshot: „Grundlegende SAML-Konfiguration“ mit den hervorgehobenen Textfeldern „Bezeichner“ und „Antwort-URL“ und der ausgewählten Schaltfläche „Speichern“](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Kantega SSO for JIRA](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte werden während der Konfiguration des JIRA-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Kantega SSO for JIRA einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Kantega SSO for JIRA

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem lokalen JIRA-Server als Administrator an.

1. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons**.

    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Dropdownmenü die Option „Add-Ons“ hervorgehoben ist](./media/kantegassoforjira-tutorial/addon1.png)

1. Klicken Sie im Registerkartenabschnitt „Add-Ons“ auf **Nach neuen Add-Ons suchen**. Suchen Sie nach **Kantega SSO for JIRA (SAML & Kerberos)** , und klicken Sie auf die Schaltfläche **Installieren**, um das neue SAML-Plug-In zu installieren.

    ![Screenshot: Abschnitt „Find new Add-ons“ (Nach neuen Add-Ons suchen) mit der Eingabe „Kantega SSO for JIRA (SAML & Kerberos)“ im Suchfeld und der ausgewählten Schaltfläche „Install“ (Installieren)](./media/kantegassoforjira-tutorial/addon2.png)

1. Die Installation des Plug-Ins wird gestartet.

    ![Screenshot: Dialogfeld „Installing“ (Wird installiert...) des Plug-Ins](./media/kantegassoforjira-tutorial/addon3.png)

1. Gehen Sie nach Abschluss der Installation wie folgt vor: Klicken Sie auf **Schließen**.

    ![Screenshot: Dialogfeld „Installed and ready to go!“ (Installiert und einsatzbereit!) mit der ausgewählten Aktion „Close“ (Schließen)](./media/kantegassoforjira-tutorial/addon33.png)

1.  Klicken Sie auf **Manage**.

    ![Screenshot: App-Seite „Kantega SSO“ mit der ausgewählten Schaltfläche „Manage“ (Verwalten)](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Das neue Plug-In wird unter **INTEGRATIONEN** aufgeführt. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Screenshot, auf dem die Option „INTEGRATIONS“ (INTEGRATIONEN) im Navigationsmenü auf der linken Seite hervorgehoben und die Schaltfläche „Configure“ (Konfigurieren) im Abschnitt „Manage add-ons“ (Add-Ons verwalten) ausgewählt ist](./media/kantegassoforjira-tutorial/addon35.png)

1. Im Abschnitt **SAML**: Wählen Sie in der Dropdownliste **Identitätsanbieter hinzufügen** die Option **Azure Active Directory (Azure AD)**.

    ![Screenshot: Dropdownmenü „Add identity provider“ (Identitätsanbieter hinzufügen) mit der ausgewählten Option „Azure Active Directory (Azure AD)“](./media/kantegassoforjira-tutorial/addon4.png)

1. Wählen Sie als Abonnementebene die Option **Basic**.

    ![Screenshot: Abschnitt „Preparing Azure AD“ (Azure AD wird vorbereitet...) mit Auswahl der Option „Basic“](./media/kantegassoforjira-tutorial/addon5.png)

1. Führen Sie im Abschnitt **App-Eigenschaften** die folgenden Schritte aus: 

    ![Screenshot: Abschnitt „App properties“ (App-Eigenschaften) mit dem hervorgehobenen Textfeld „App ID URL“ (App-ID-URL), der hervorgehobenen Schaltfläche zum Kopieren und der ausgewählten Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/addon6.png)

    1. Kopieren Sie den Wert für den **App-ID-URI**, und verwenden Sie ihn als **Bezeichner, Antwort-URL und Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** des Azure-Portals.

    1. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Metadata import** (Metadatenimport) die folgenden Schritte aus: 

    ![Screenshot: Abschnitt „Metadata import“ (Metadatenimport) mit Auswahl der Option „Metadata file on my computer“ (Metadatendatei auf meinem Computer)](./media/kantegassoforjira-tutorial/addon7.png)

    1. Wählen Sie **Metadata file on my computer** (Metadatendatei auf meinem Computer), und laden Sie die Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    1. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Name and SSO location** (Name und SSO-Standort) die folgenden Schritte aus:

    ![Screenshot: „Name and SSO location“ (Name und SSO-Standort) mit hervorgehobenem Textfeld „Identity provider name“ (Name des Identitätsanbieters) und Auswahl der Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/addon8.png)

    1. Fügen Sie im Textfeld **Name des Identitätsanbieters** den Namen des Identitätsanbieters hinzu (z.B. Azure AD).

    1. Klicken Sie auf **Weiter**.

1. Überprüfen Sie das Signaturzertifikat, und klicken Sie auf **Weiter**.

    ![Screenshot: Abschnitt „Signature verification“ (Signaturüberprüfung) mit hervorgehobener Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/addon9.png)

1. Führen Sie im Abschnitt **JIRA user accounts** (JIRA-Benutzerkonten) die folgenden Schritte aus:

    ![Screenshot: „JIRA user accounts“ (JIRA-Benutzerkonten) mit hervorgehobener Option „Create users in JIRA's Internal Directory if needed“ (Benutzer im internen JIRA-Verzeichnis erstellen, falls erforderlich) und ausgewählter Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/addon10.png)

    1. Wählen Sie **Create users in JIRA's internal Directory if needed** (Benutzer im internen JIRA-Verzeichnis erstellen, falls erforderlich), und geben Sie den entsprechenden Namen der Gruppe für Benutzer ein (können mehrere durch Kommas getrennte Gruppen sein).

    1. Klicken Sie auf **Weiter**.

1. Klicken Sie auf **Fertig stellen**.

    ![Screenshot: Abschnitt „Summary“ (Zusammenfassung) mit ausgewählter Schaltfläche „Finish“ (Fertig stellen)](./media/kantegassoforjira-tutorial/addon11.png)

1. Führen Sie im Abschnitt **Known domains for Azure AD** (Bekannte Domänen für Azure AD) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/kantegassoforjira-tutorial/addon12.png)

    1. Wählen Sie im linken Bereich der Seite die Option **Known domains** (Bekannte Domänen).

    2. Geben Sie den Domänennamen im Textfeld **Known domains** (Bekannte Domänen) ein.

    3. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.

    1. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Kantega SSO for JIRA gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Kantega SSO for JIRA**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Kantega SSO for JIRA** aus.

    ![Kantega SSO for JIRA-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-kantega-sso-for-jira-test-user"></a>Erstellen eines Kantega SSO for JIRA-Testbenutzers

Damit sich Azure AD-Benutzer bei JIRA anmelden können, müssen sie in JIRA bereitgestellt werden. In Kantega SSO for JIRA ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem lokalen JIRA-Server als Administrator an.

1. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Dropdownmenü die Option „User management“ (Benutzerverwaltung) hervorgehoben ist](./media/kantegassoforjira-tutorial/user1.png) 

1. Klicken Sie im Registerkartenabschnitt **Benutzerverwaltung** auf **Benutzer erstellen**.

    ![Screenshot: Abschnitt „User management“ (Benutzerverwaltung), in dem die Schaltfläche „Create user“ (Benutzer erstellen) ausgewählt ist](./media/kantegassoforjira-tutorial/user2.png) 

1. Führen Sie auf der Dialogfeldseite **Neuen Benutzer erstellen** die folgenden Schritte durch:

    ![Mitarbeiter hinzufügen](./media/kantegassoforjira-tutorial/user3.png) 

    1. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    2. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    3. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    4. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.

    5. Klicken Sie auf **Benutzer erstellen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Kantega SSO for JIRA“ klicken, sollten Sie automatisch bei Ihrer Kantega SSO for JIRA-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)