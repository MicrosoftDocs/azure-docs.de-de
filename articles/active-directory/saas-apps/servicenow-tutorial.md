---
title: 'Tutorial: Azure Active Directory-Integration mit ServiceNow | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ServiceNow konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0efc69aa7fb081303df6fc6327253fd5b8336999
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65902799"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Tutorial: Azure Active Directory-Integration mit ServiceNow

In diesem Tutorial erfahren Sie, wie Sie ServiceNow in Azure Active Directory (Azure AD) integrieren.
Die Integration von ServiceNow in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf ServiceNow hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Litmos angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ServiceNow konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* ServiceNow-Abonnement, für das einmaliges Anmelden aktiviert ist
* Für ServiceNow eine Instanz oder einen Mandanten von ServiceNow, Calgary-Version oder höher
* Für ServiceNow Express eine Instanz von ServiceNow Express, Helsinki-Version oder höher
* Für den ServiceNow-Mandanten muss das [SSO-Plug-In für mehrere Anbieter](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiviert sein. Dazu kann [eine Serviceanfrage übermittelt werden](https://hi.service-now.com).
* Aktivieren Sie für die automatische Konfiguration das Multi-Provider-Plug-In für ServiceNow.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ServiceNow unterstützt **SP**-initiiertes einmaliges Anmelden.

* ServiceNow unterstützt die [**automatisierte** Benutzerbereitstellung](servicenow-provisioning-tutorial.md).

## <a name="adding-servicenow-from-the-gallery"></a>Hinzufügen von ServiceNow aus dem Katalog

Zum Konfigurieren der Integration von ServiceNow in Azure AD müssen Sie ServiceNow aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um ServiceNow aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ServiceNow** ein, wählen Sie im Ergebnisbereich **ServiceNow** aus, und klicken Sie anschließend auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![ServiceNow in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei ServiceNow basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ServiceNow eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit ServiceNow zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens in Azure AD für ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ServiceNow](#configure-servicenow-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Konfigurieren des einmaligen Anmeldens in Azure AD für ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
4. **[Konfigurieren des einmaligen Anmeldens für ServiceNow Express](#configure-servicenow-express-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
5. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
6. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
7. **[Erstellen eines ServiceNow-Testbenutzers](#create-servicenow-test-user)**, um eine Entsprechung von Britta Simon in ServiceNow zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
8. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurieren des einmaligen Anmeldens von Azure AD für ServiceNow

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit ServiceNow zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ServiceNow** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für ServiceNow](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<instance-name>.service-now.com/navpage.do`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<instance-name>.service-now.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner ersetzen. Dies wird an späterer Stelle im Tutorial erläutert. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

    a. Klicken Sie auf die Schaltfläche „Kopieren“, um die **Verbundmetadaten-URL der App**  zu kopieren und in Editor einzufügen. Die Verbundmetadaten-URL der App wird später in diesem Tutorial verwendet.

    b. Klicken Sie auf **Herunterladen**, um das **Zertifikat (Base64)** herunterzuladen, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

6. Kopieren Sie im Abschnitt **ServiceNow einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-servicenow-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ServiceNow

1. Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

2. Aktivieren Sie das Plug-In **Integration – Multiple Provider Single Sign-On Installer** (Integration – SSO-Installationsprogramm für mehrere Anbieter), indem Sie die nächsten Schritte ausführen:

    a. Suchen Sie im Navigationsbereich auf der linken Seite den Abschnitt **Systemdefinition** auf der Suchleiste, und klicken Sie auf **Plug-Ins**.

    ![Plug-In aktivieren](./media/servicenow-tutorial/tutorial_servicenow_03.png "Plug-In aktivieren")

    b. Suchen Sie nach **Integration – Multiple Provider Single Sign-On Installer** (Integration – SSO-Installationsprogramm für mehrere Anbieter).

     ![Plug-In aktivieren](./media/servicenow-tutorial/tutorial_servicenow_04.png "Plug-In aktivieren")

    c. Wählen Sie das Plug-In aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Activate/Upgrade** (Aktivieren/Upgrade) aus.

     ![Plug-In aktivieren](./media/servicenow-tutorial/tutorial_activate.png "Plug-In aktivieren")

    d. Klicken Sie auf die Schaltfläche **Aktivieren**.

     ![Plug-In aktivieren](./media/servicenow-tutorial/tutorial_activate1.png "Plug-In aktivieren")

3. Suchen Sie im Navigationsbereich auf der linken Seite den Abschnitt **Multi-Provider SSO** auf der Suchleiste, und klicken Sie auf **Eigenschaften**.

    ![App-URL konfigurieren](./media/servicenow-tutorial/tutorial_servicenow_06.png "App-URL konfigurieren")

4. Führen Sie im Dialogfeld **Multiple Provider SSO Properties** die folgenden Schritte aus:

    ![App-URL konfigurieren](./media/servicenow-tutorial/ic7694981.png "App-URL konfigurieren")

    * Bei **Enable multiple provider SSO** wählen Sie **Yes** aus.
  
    * Wählen Sie bei **Enable Auto Importing of users from all identity providers into the user table** (Automatisches Importieren von Benutzern aller Identitätsanbieter in die Benutzertabelle) **Yes** (Ja) aus.

    * Wählen Sie bei **Enable debug logging for the multiple provider SSO integration** (Debugprotokollierung für die Multi-Provider SSO-Integration) **Yes** (Ja) aus.

    * Im Textfeld **The field on the user table that...** geben Sie **user_name** ein.
  
    * Klicken Sie auf **Speichern**.

5. Es gibt zwei Möglichkeiten für die Konfiguration von **ServiceNow**: automatisch und manuell.

6. Führen Sie für die automatische Konfiguration von **ServiceNow** die unten angegebenen Schritte aus:

    * Wechseln Sie zurück zur **ServiceNow**-SSO-Seite im Azure-Portal.

    * Für ServiceNow gibt es einen Ein-Klick-Konfigurationsdienst, d. h., Azure AD konfiguriert ServiceNow für die SAML-Authentifizierung automatisch. Um diesen Dienst zu aktivieren, wechseln Sie in den Abschnitt **ServiceNow-Konfiguration**, und klicken Sie auf **ServiceNow konfigurieren**, um das Fenster „Anmeldung konfigurieren“ zu öffnen.

        ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Geben Sie im Formular **Anmeldung konfigurieren** Ihren ServiceNow-Instanznamen, Administratorbenutzernamen und das Administratorkennwort ein, und klicken Sie auf **Jetzt konfigurieren**. Beachten Sie, dass dem angegebenen Administratorbenutzernamen in ServiceNow die Rolle **security_admin** zugewiesen sein muss, damit dies funktioniert. Wenn Sie hingegen ServiceNow für die Verwendung von Azure AD als SAML-Identitätsanbieter manuell konfigurieren möchten, klicken Sie auf **Einmaliges Anmelden manuell konfigurieren**, und kopieren Sie aus dem Abschnitt „Kurzübersicht“ **die Abmelde-URL, die SAML-Entitäts-ID und die SAML-Dienst-URL für einmaliges Anmelden**.

        ![App-URL konfigurieren](./media/servicenow-tutorial/configure.png "App-URL konfigurieren")

    * Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

    * Bei der automatischen Konfiguration werden alle erforderlichen Einstellungen aufseiten von **ServiceNow** konfiguriert, aber das **X.509-Zertifikat** ist standardmäßig nicht aktiviert. Sie müssen es Ihrem Identitätsanbieter in ServiceNow manuell zuordnen. Führen Sie hierzu die unten angegebenen Schritte aus:

    * Suchen Sie im Navigationsbereich auf der linken Seite den Abschnitt **Multi-Provider SSO** auf der Suchleiste, und klicken Sie auf **Identitätsanbieter**.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/tutorial_servicenow_07.png "Einmaliges Anmelden konfigurieren")

    * Klicken Sie auf den automatisch generierten Identitätsanbieter.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/tutorial_servicenow_08.png "Einmaliges Anmelden konfigurieren")

    *  Führen Sie im Abschnitt **Identitätsanbieter** die folgenden Schritte aus:

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/automatic_config.png "Einmaliges Anmelden konfigurieren")

        * Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein (z.B. **Einmaliges Anmelden für Microsoft Azure-Verbunddienste**).

        * Entfernen Sie den Wert für **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest des Identitätsanbieters) aus dem Textfeld.

        * Kopieren Sie den Wert für die **ServiceNow-Homepage**, und fügen Sie ihn im Azure-Portal im Abschnitt **Domäne und URLs für ServiceNow** im Textfeld **Anmelde-URL** ein.

            > [!NOTE]
            > Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** mit **/navpage.do** (beispielsweise `https://fabrikam.service-now.com/navpage.do`).

        * Kopieren Sie den Wert für **Entitäts-ID/Aussteller**, und fügen Sie ihn im Azure-Portal im Abschnitt **Domäne und URLs für ServiceNow** im Textfeld **Bezeichner** ein.

        * Stellen Sie sicher, dass **NameID-Richtlinie** auf den Wert `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` festgelegt ist. 

    * Scrollen Sie nach unten zum Abschnitt **X.509-Zertifikat**, und wählen Sie **Bearbeiten** aus.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/tutorial_servicenow_09.png "Einmaliges Anmelden konfigurieren")

    * Wählen Sie das Zertifikat aus, und klicken Sie auf den Pfeil nach rechts, um das Zertifikat hinzuzufügen.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/tutorial_servicenow_11.png "Einmaliges Anmelden konfigurieren")

    * Klicken Sie auf **Speichern**.

    * Klicken Sie rechts oben auf der Seite auf **Verbindung testen**.

        ![Plug-In aktivieren](./media/servicenow-tutorial/tutorial_activate2.png "Plug-In aktivieren")

    * Nach dem Klicken auf **Verbindung testen** wird ein Popupfenster angezeigt, in dem Sie Anmeldeinformationen eingeben müssen. Anschließend wird die unten abgebildete Seite mit Ergebnissen angezeigt. Der Fehler zu den **Testergebnissen der SSO-Abmeldung** wird erwartet. Sie können ihn ignorieren und auf die Schaltfläche **Aktivieren** klicken.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/servicenowactivate.png "Einmaliges Anmelden konfigurieren")
  
7. Führen Sie für die manuelle Konfiguration von **ServiceNow** die unten angegebenen Schritte aus.

    * Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

    * Klicken Sie im Navigationsbereich auf der linken Seite auf **Identity Providers**.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/tutorial_servicenow_07.png "Einmaliges Anmelden konfigurieren")

    * Klicken Sie im Dialogfeld **Identitätsanbieter** auf **Neu**.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694977.png "Einmaliges Anmelden konfigurieren")

    * Klicken Sie im Dialogfeld **Identitätsanbieter** auf **SAML**.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694978.png "Einmaliges Anmelden konfigurieren")

    * Führen Sie im Popupfenster **Import Identity Provider Metadata** (Metadaten des Identitätsanbieters importieren) die folgenden Schritte aus:

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/idp.png "Einmaliges Anmelden konfigurieren")

        * Geben Sie die **Verbundmetadaten-URL der App** ein, die Sie aus dem Azure-Portal kopiert haben.

        * Klicken Sie auf **Importieren**.

    * Die IdP-Metadaten-URL wird gelesen, und alle Felder werden aufgefüllt.

        ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694982.png "Einmaliges Anmelden konfigurieren")

        * Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein (z.B. **Einmaliges Anmelden für Microsoft Azure-Verbunddienste**).

        * Entfernen Sie den Wert für **Identity Provider's SingleLogoutRequest** (SingleLogoutRequest des Identitätsanbieters) aus dem Textfeld.

        * Kopieren Sie den Wert für die **ServiceNow-Homepage**, und fügen Sie ihn im Azure-Portal im Abschnitt **Domäne und URLs für ServiceNow** im Textfeld **Anmelde-URL** ein.

            > [!NOTE]
            > Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** mit **/navpage.do** (beispielsweise `https://fabrikam.service-now.com/navpage.do`).

        * Kopieren Sie den Wert für **Entitäts-ID/Aussteller**, und fügen Sie ihn im Azure-Portal im Abschnitt **Domäne und URLs für ServiceNow** im Textfeld **Bezeichner** ein.

        * Stellen Sie sicher, dass **NameID-Richtlinie** auf den Wert `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` festgelegt ist.

        * Klicken Sie auf **Erweitert**. Geben Sie im Textfeld **User Field** (Benutzerfeld) den Wert **email** oder **user_name** ein, je nachdem, welches Feld für die eindeutige Identifizierung von Benutzern in Ihrer ServiceNow-Bereitstellung verwendet wird.

            > [!NOTE]
            > Sie können Azure AD so konfigurieren, dass entweder die Azure AD-Benutzer-ID (Benutzerprinzipalname) oder die E-Mail-Adresse als eindeutiger Bezeichner im SAML-Token ausgegeben wird. Wechseln Sie dazu im Azure-Portal zum Abschnitt **ServiceNow > Attribute > Einmaliges Anmelden**, und weisen Sie dem **nameidentifier**-Attribut das gewünschte Feld zu. Der gespeicherte Wert für das ausgewählte Attribut in Azure AD (z.B. Benutzerprinzipalname) muss dem in ServiceNow gespeicherten Wert für das eingegebene Feld (z.B. user_name) entsprechen.

        * Klicken Sie rechts oben auf der Seite auf **Verbindung testen**.

        * Nach dem Klicken auf **Verbindung testen** wird ein Popupfenster angezeigt, in dem Sie Anmeldeinformationen eingeben müssen. Anschließend wird die unten abgebildete Seite mit Ergebnissen angezeigt. Der Fehler zu den **Testergebnissen der SSO-Abmeldung** wird erwartet. Sie können ihn ignorieren und auf die Schaltfläche **Aktivieren** klicken.

          ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/servicenowactivate.png "Einmaliges Anmelden konfigurieren")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurieren des einmaligen Anmeldens von Azure AD für ServiceNow Express

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ServiceNow** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für ServiceNow](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<instance-name>.service-now.com/navpage.do`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<instance-name>.service-now.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner ersetzen. Dies wird an späterer Stelle im Tutorial erläutert. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Für ServiceNow gibt es einen Ein-Klick-Konfigurationsdienst, d. h., Azure AD konfiguriert ServiceNow für die SAML-Authentifizierung automatisch. Klicken Sie im Abschnitt **ServiceNow einrichten** auf **Schrittanleitung anzeigen**, um das Fenster „Anmeldung konfigurieren“ zu öffnen.

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Geben Sie im Formular **Anmeldung konfigurieren** Ihren ServiceNow-Instanznamen, Administratorbenutzernamen und das Administratorkennwort ein, und klicken Sie auf **Jetzt konfigurieren**. Beachten Sie, dass dem angegebenen Administratorbenutzernamen in ServiceNow die Rolle **security_admin** zugewiesen sein muss, damit dies funktioniert. Wenn Sie hingegen ServiceNow für die Verwendung von Azure AD als SAML-Identitätsanbieter manuell konfigurieren möchten, klicken Sie auf **Einmaliges Anmelden manuell konfigurieren**, und kopieren Sie aus dem Abschnitt „Kurzübersicht“ **die Abmelde-URL, die SAML-Entitäts-ID und die SAML-Dienst-URL für einmaliges Anmelden**.

    ![App-URL konfigurieren](./media/servicenow-tutorial/configure.png "App-URL konfigurieren")

### <a name="configure-servicenow-express-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ServiceNow Express

1. Melden Sie sich bei Ihrer ServiceNow Express-Anwendung als Administrator an.

2. Klicken Sie links im Navigationsbereich auf **Einmaliges Anmelden**.

    ![App-URL konfigurieren](./media/servicenow-tutorial/ic7694980ex.png "App-URL konfigurieren")

3. Klicken Sie im Dialogfeld **Einmaliges Anmelden** oben rechts auf das Konfigurationssymbol, und legen Sie die folgenden Eigenschaften fest:

    ![App-URL konfigurieren](./media/servicenow-tutorial/ic7694981ex.png "App-URL konfigurieren")

    a. Schieben Sie den Umschalter der Option **Enable multiple provider SSO** (SSO für mehrere Anbieter aktivieren) nach rechts.

    b. Schieben Sie den Umschalter für **Enable debug logging for the multiple provider SSO integration** (Debugprotokollierung für die SSO-Integration für mehrere Anbieter aktivieren) nach rechts.

    c. Im Textfeld **The field on the user table that...** geben Sie **user_name** ein.

4. Klicken Sie im Dialogfeld **Einmaliges Anmelden** auf **Neues Zertifikat hinzufügen**.

    ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694973ex.png "Einmaliges Anmelden konfigurieren")

5. Führen Sie im Dialogfeld **X.509 Certificates** die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694975.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z.B.: **TestSAML2.0**).

    b. Wählen Sie **Aktiv**.

    c. Wählen Sie unter **Format** das Format **PEM** aus.

    d. Wählen Sie unter **Type** den Typ **Trust Store Cert** aus.

    e. Öffnen Sie das vom Azure-Portal heruntergeladene Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **PEM Certificate** (PEM-Zertifikat) ein.

    f. Klicken Sie auf **Aktualisieren**.

6. Klicken Sie im Dialogfeld **Einmaliges Anmelden** auf **Add New IdP** (Neuen IdP hinzufügen).

    ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694976ex.png "Einmaliges Anmelden konfigurieren")

7. Führen Sie im Dialogfeld **Add New Identity Provider** (Neuen Identitätsanbieter hinzufügen) unter **Configure Identity Provider** (Identitätsanbieter konfigurieren) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694982ex.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z.B.: **SAML 2.0**).

    b. Fügen Sie in das Feld **Identity Provider URL** den Wert für die **Identitätsanbieter-ID** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Feld **Identity Provider's AuthnRequest** den Wert für die **Authentifizierungsanforderungs-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Feld **Identity Provider's SingleLogoutRequest** den Wert für die **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Wählen Sie als **Identity Provider Certificate** (Identitätsanbieterzertifikat) das Zertifikat aus, das Sie im vorherigen Schritt erstellt haben.

8. Klicken Sie auf **Advanced Settings** (Erweiterte Einstellungen), und führen Sie unter **Additional Identity Provider Properties** (Weitere Identitätsanbietereigenschaften) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694983ex.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie im Textfeld **Protocol Binding for the IDP's SingleLogoutRequest** die Zeichenfolge **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect** ein.

    b. Geben Sie im Textfeld **NameID Policy** die Zeichenfolge **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified** ein.

    c. Geben Sie unter **AuthnContextClassRef Method** die URL `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` ein.

    d. Deaktivieren Sie **Create an AuthnContextClass**.

9. Führen Sie unter **Additional Service Provider Properties** (Weitere Service Provider-Eigenschaften) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/servicenow-tutorial/ic7694984ex.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie im Textfeld **ServiceNow Homepage** die URL zur Homepage Ihrer ServiceNow-Instanz ein.

    > [!NOTE]
    > Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** mit **/navpage.do** (beispielsweise `https://fabrikam.service-now.com/navpage.do`).

    b. Geben Sie im Textfeld **Entity ID / Issuer** die URL Ihres ServiceNow-Mandaten ein.

    c. Geben Sie im Textfeld **Audience URI** (Benutzergruppen-URI) die URL Ihres ServiceNow-Mandanten ein.

    d. Im Textfeld **Clock Skew** geben Sie **60** ein.

    e. Geben Sie im Textfeld **User Field** (Benutzerfeld) den Wert **email** oder **user_name** ein, je nachdem, welches Feld für die eindeutige Identifizierung von Benutzern in Ihrer ServiceNow-Bereitstellung verwendet wird.

    > [!NOTE]
    > Sie können Azure AD so konfigurieren, dass entweder die Azure AD-Benutzer-ID (Benutzerprinzipalname) oder die E-Mail-Adresse als eindeutiger Bezeichner im SAML-Token ausgegeben wird. Wechseln Sie dazu im Azure-Portal zum Abschnitt **ServiceNow > Attribute > Einmaliges Anmelden**, und weisen Sie dem **nameidentifier**-Attribut das gewünschte Feld zu. Der gespeicherte Wert für das ausgewählte Attribut in Azure AD (z.B. Benutzerprinzipalname) muss dem in ServiceNow gespeicherten Wert für das eingegebene Feld (z.B. user_name) entsprechen.

    f. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ServiceNow gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **ServiceNow**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **ServiceNow** aus.

    ![ServiceNow-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-servicenow-test-user"></a>Erstellen eines ServiceNow-Testbenutzers

In diesem Abschnitt wird in ServiceNow eine Benutzerin namens Britta Simon erstellt. ServiceNow unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](servicenow-provisioning-tutorial.md).

> [!NOTE]
> Wenn Sie einen Benutzer manuell erstellen möchten, wenden Sie sich an das [ServiceNow-Supportteam](https://www.servicenow.com/support/contact-support.html).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „ServiceNow“ klicken, sollten Sie automatisch bei Ihrer ServiceNow-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurieren der Benutzerbereitstellung](servicenow-provisioning-tutorial.md)
