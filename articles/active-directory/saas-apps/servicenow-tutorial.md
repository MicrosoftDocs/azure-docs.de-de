---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ServiceNow | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ServiceNow konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7a15248300150c9b79c4678701f81fac923d56a3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353606"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ServiceNow

In diesem Tutorial erfahren Sie, wie Sie ServiceNow in Azure Active Directory (Azure AD) integrieren. Die Integration von ServiceNow in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf ServiceNow hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ServiceNow anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Jao6]

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SSO-fähiges ServiceNow-Abonnement
* Für ServiceNow unterstützt eine Instanz oder ein Mandant von ServiceNow die Versionen Calgary, Kingston, London, Madrid, New York, Orlando und Paris oder höher.
* Für ServiceNow Express: Eine Instanz von ServiceNow Express (Helsinki-Version oder höher)
* Für den ServiceNow-Mandanten muss das [SSO-Plug-In für mehrere Anbieter](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) aktiviert sein.
* Aktivieren Sie für die automatische Konfiguration das Multi-Provider-Plug-In für ServiceNow.
* Navigieren Sie zum Installieren der (mobilen) ServiceNow Classic-Anwendung zum entsprechenden Store, und suchen Sie nach der Anwendung „ServiceNow Classic“. Laden Sie sie herunter.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 

* ServiceNow unterstützt **SP-initiiertes** einmaliges Anmelden.

* ServiceNow unterstützt die [automatisierte Benutzerbereitstellung](servicenow-provisioning-tutorial.md).

* Die (mobile) ServiceNow Classic-Anwendung kann zur Aktivierung von SSO mit Azure AD konfiguriert werden. Sowohl Android-Benutzer als auch iOS-Benutzer werden unterstützt. In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

## <a name="add-servicenow-from-the-gallery"></a>Hinzufügen von ServiceNow über den Katalog

Zum Konfigurieren der Integration von ServiceNow in Azure AD müssen Sie ServiceNow aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ServiceNow** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **ServiceNow** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ServiceNow

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei ServiceNow mithilfe eines Testbenutzers namens **B.Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ServiceNow eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ServiceNow die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit B.Simon zu testen
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
    1. [Konfigurieren des einmaligen Anmeldens von Azure AD für ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. [Konfigurieren von ServiceNow](#configure-servicenow), um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren
    1. [Erstellen eines ServiceNow-Testbenutzers](#create-servicenow-test-user), um in ServiceNow eine Entsprechung von B.Simon zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist
    1. [Konfigurieren des einmaligen Anmeldens für ServiceNow Express](#configure-servicenow-express-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren  
3. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert
4. [Testen des einmaligen Anmeldens für ServiceNow Classic (mobil)](#test-sso-for-servicenow-classic-mobile), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ServiceNow** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie unter **Anmelde-URL** eine URL mit dem folgenden Muster ein: `https://<instancename>.service-now.com/navpage.do`

    b. Geben Sie unter **Bezeichner (Entitäts-ID)** eine URL mit dem folgenden Muster ein: `https://<instance-name>.service-now.com`

    c. Geben Sie unter **Antwort-URL** die URL in einem der folgenden Formate ein:

    | Antwort-URL|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. Geben Sie unter **Abmelde-URL** eine URL mit dem folgenden Muster ein: `https://<instancename>.service-now.com/navpage.do`

    > [!NOTE]
    > Sollte im Bezeichnerwert „/ “ hinzugefügt werden, entfernen Sie dies manuell.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch die tatsächliche Anmelde-URL, Antwort-URL, Abmelde-URL und den tatsächlichen Bezeichner ersetzen. Dies wird im weiteren Verlauf des Tutorials erläutert. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** nach **Zertifikat (Base64)** . 

   ![Screenshot: Abschnitt „SAML-Signaturzertifikat“ mit hervorgehobener Downloadoption](common/certificatebase64.png)

   a. Wählen Sie die Kopierschaltfläche aus, um die **Verbundmetadaten-URL der App** zu kopieren, und fügen Sie sie in Editor ein. Diese URL wird später im Tutorial benötigt.

    b. Wählen Sie **Herunterladen** aus, um **Zertifikat (Base64)** herunterzuladen, und speichern Sie die Zertifikatsdatei auf Ihrem Computer.

1. Kopieren Sie im Abschnitt **ServiceNow einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Screenshot: Abschnitt „ServiceNow einrichten“ mit hervorgehobenen URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie unter **Name**`B.Simon` ein.  
   1. Geben Sie unter **Benutzername** einen Benutzernamen im Format username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ServiceNow gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **ServiceNow** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **B.Simon** und anschließend **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurieren des einmaligen Anmeldens von Azure AD für ServiceNow Express

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ServiceNow** die Option **Einmaliges Anmelden** aus.

    ![Screenshot: ServiceNow-Anwendungsintegrationsseite mit Hervorhebung von „Einmaliges Anmelden“](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Screenshot: „SSO-Methode auswählen“ mit Hervorhebung von SAML](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie unter **Anmelde-URL** eine URL mit dem folgenden Muster ein: `https://<instancename>.service-now.com/navpage.do`

    b. Geben Sie unter **Bezeichner (Entitäts-ID)** eine URL mit dem folgenden Muster ein: `https://<instance-name>.service-now.com`

    c. Geben Sie unter **Antwort-URL** eine der folgenden URLs ein:

    | Antwort-URL |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. Geben Sie unter **Abmelde-URL** eine URL mit dem folgenden Muster ein: `https://<instancename>.service-now.com/navpage.do`
    
    > [!NOTE]
    > Sollte im Bezeichnerwert „/ “ hinzugefügt werden, entfernen Sie dies manuell.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch die tatsächliche Anmelde-URL, Antwort-URL, Abmelde-URL und den tatsächlichen Bezeichner ersetzen. Dies wird im weiteren Verlauf des Tutorials erläutert. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen (gemäß Ihrer Anforderung). Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Screenshot: Abschnitt „SAML-Signaturzertifikat“ mit hervorgehobener Downloadoption](common/certificatebase64.png)

6. ServiceNow kann von Azure AD automatisch für die SAML-basierte Authentifizierung konfiguriert werden. Wählen Sie im Abschnitt **ServiceNow einrichten** die Option **Schrittanleitung anzeigen** aus, um das Fenster **Anmeldung konfigurieren** zu öffnen.

    ![Screenshot: Abschnitt „ServiceNow einrichten“ mit hervorgehobener Option „Schrittanleitung anzeigen“](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. Geben Sie im Formular **Anmeldung konfigurieren** Ihren ServiceNow-Instanznamen, Administratorbenutzernamen und Ihr Administratorkennwort ein. Wählen Sie **Jetzt konfigurieren** aus. Dem angegebenen Administratorbenutzernamen muss in ServiceNow die Rolle **security_admin** zugewiesen sein. Andernfalls müssen Sie ServiceNow manuell für die Verwendung von Azure AD als SAML-Identitätsanbieter konfigurieren, indem Sie **Einmaliges Anmelden manuell konfigurieren** auswählen. Kopieren Sie **die Abmelde-URL, den Azure AD-Bezeichner und die Anmelde-URL** aus der Kurzübersicht.

    ![Screenshot: Formular zum Konfigurieren der Anmeldung mit hervorgehobener Option „Configure Now“ (Jetzt konfigurieren)](./media/servicenow-tutorial/configure.png "App-URL konfigurieren")

## <a name="configure-servicenow"></a>Konfigurieren von ServiceNow

1. Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

1. Aktivieren Sie das Plug-In **Integration - Multiple Provider Single Sign-On Installer** (Integration – SSO-Installationsprogramm für mehrere Anbieter):

    a. Suchen Sie im linken Bereich mithilfe des Suchfelds nach dem Abschnitt **System Definition** (Systemdefinition), und wählen Sie **Plugins** (Plug-Ins) aus.

    ![Screenshot: Abschnitt „System Definition“ (Systemdefinition) mit Hervorhebung von „System Definition“ (Systemdefinition) und „Plugins“ (Plug-Ins)](./media/servicenow-tutorial/tutorial-servicenow-03.png "Aktivieren des Plug-Ins")

    b. Suchen Sie nach **Integration – Multiple Provider Single Sign-On Installer** (Integration – SSO-Installationsprogramm für mehrere Anbieter).

     ![Screenshot: Seite für System-Plug-Ins mit Hervorhebung von „Integration - Multiple Provider Single Sign-On Installer“ (Integration – SSO-Installationsprogramm für mehrere Anbieter)](./media/servicenow-tutorial/tutorial-servicenow-04.png "Aktivieren des Plug-Ins")

    c. Wählen Sie das Plug-In aus. Klicken Sie mit der rechten Maustaste, und wählen Sie **Activate/Upgrade** (Aktivieren/Upgraden) aus.

     ![Screenshot: Plug-In-Kontextmenü mit hervorgehobener Option „Activate/Upgrade“ (Aktivieren/Upgraden)](./media/servicenow-tutorial/tutorial-activate.png "Aktivieren des Plug-Ins")

    d. Wählen Sie **Aktivieren** aus.

     ![Screenshot: Dialogfeld zum Aktivieren des Plug-Ins mit hervorgehobener Option „Activate“ (Aktivieren)](./media/servicenow-tutorial/tutorial-activate-1.png "Aktivieren des Plug-Ins")

1. Suchen Sie im linken Bereich über die Suchleiste nach dem Abschnitt **Multi-Provider SSO** (Multi-Provider-SSO), und wählen Sie **Properties** (Eigenschaften) aus.

    ![Screenshot: Abschnitt „Multi-Provider SSO“ (Multi-Provider-SSO) mit Hervorhebung von „Multi-Provider SSO“ (Multi-Provider-SSO) und „Properties“ (Eigenschaften)](./media/servicenow-tutorial/tutorial-servicenow-06.png "App-URL konfigurieren")

1. Gehen Sie im Dialogfeld **Multiple Provider SSO Properties** (Eigenschaften von SSO für mehrere Anbieter) wie folgt vor:

    ![Screenshot: Dialogfeld mit den Eigenschaften von SSO für mehrere Anbieter](./media/servicenow-tutorial/ic7694981.png "App-URL konfigurieren")

    * Wählen Sie unter **Enable multiple provider SSO** (SSO für mehrere Anbieter aktivieren) die Option **Yes** (Ja) aus.
  
    * Wählen Sie unter **Enable Auto Importing of users from all identity providers into the user table** (Automatisches Importieren von Benutzern aller Identitätsanbieter in die Benutzertabelle) die Option **Yes** (Ja) aus.

    * Wählen Sie unter **Enable debug logging for the multiple provider SSO integration** (Debugprotokollierung für die Multi-Provider SSO-Integration) die Option **Yes** (Ja) aus.

    * Geben Sie unter **The field on the user table that...** (Das Feld in der Benutzertabelle, das...) die Zeichenfolge **email** ein.
  
    * Wählen Sie **Speichern** aus.

1. ServiceNow kann automatisch oder manuell konfiguriert werden. Führen Sie für die automatische Konfiguration von ServiceNow die folgenden Schritte aus:

    1. Kehren Sie im Azure-Portal zur SSO-Seite für **ServiceNow** zurück.

    1. ServiceNow kann mit nur einem Klick konfiguriert werden. Navigieren Sie zum Aktivieren des entsprechenden Diensts zum Abschnitt **ServiceNow-Konfiguration**, und wählen Sie **ServiceNow konfigurieren** aus, um das Fenster **Anmeldung konfigurieren** zu öffnen.

        ![Screenshot: „ServiceNow einrichten“ mit hervorgehobener Option „Schrittanleitung anzeigen“](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. Geben Sie im Formular **Anmeldung konfigurieren** Ihren ServiceNow-Instanznamen, Administratorbenutzernamen und Ihr Administratorkennwort ein. Wählen Sie **Jetzt konfigurieren** aus. Dem angegebenen Administratorbenutzernamen muss in ServiceNow die Rolle **security_admin** zugewiesen sein, damit dies funktioniert. Andernfalls müssen Sie ServiceNow manuell für die Verwendung von Azure AD als SAML-Identitätsanbieter konfigurieren, indem Sie **Einmaliges Anmelden manuell konfigurieren** auswählen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus der Kurzübersicht.

        ![Screenshot: Formular zum Konfigurieren der Anmeldung mit hervorgehobener Option „Configure Now“ (Jetzt konfigurieren)](./media/servicenow-tutorial/configure.png "App-URL konfigurieren")

    1. Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

       * Bei der automatischen Konfiguration werden alle erforderlichen Einstellungen auf der Seite **ServiceNow** konfiguriert, aber das **X.509 Certificate** (X.509-Zertifikat) ist standardmäßig nicht aktiviert, und bei **Single Sign-On Script** (Skript für einmaliges Anmelden) fehlt der Wert **MultiSSOv2_SAML2_custom**. Sie müssen es Ihrem Identitätsanbieter in ServiceNow manuell zuordnen. Folgen Sie diesen Schritten:

         1. Suchen Sie im linken Bereich über das Suchfeld nach dem Abschnitt **Multi-Provider SSO** (Multi-Provider-SSO), und wählen Sie **Identity Providers** (Identitätsanbieter) aus.

            ![Screenshot: Abschnitt „Multi-Provider SSO“ (Multi-Provider-SSO) mit Hervorhebung von „Identity Providers“ (Identitätsanbieter)](./media/servicenow-tutorial/tutorial-servicenow-07.png "Einmaliges Anmelden konfigurieren")

         1. Wählen Sie den automatisch generierten Identitätsanbieter aus.

            ![Screenshot: Identitätsanbieter mit Hervorhebung des automatisch generierten Identitätsanbieters](./media/servicenow-tutorial/tutorial-servicenow-08.png "Einmaliges Anmelden konfigurieren")

         1.  Führen Sie im Abschnitt **Identitätsanbieter** die folgenden Schritte aus:

             ![Screenshot: Abschnitt „Identity Provider“ (Identitätsanbieter)](./media/servicenow-tutorial/automatic-config.png "Einmaliges Anmelden konfigurieren")

               a. Geben Sie unter **Name** einen Namen für Ihre Konfiguration ein (beispielsweise **Microsoft Azure Federated single sign-on**).

               b. Kopieren Sie den Wert für **ServiceNow-Homepage** (ServiceNow-Homepage), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration für ServiceNow** in das Textfeld **Anmelde-URL** ein.

                > [!NOTE]
                > Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** mit **/navpage.do** (beispielsweise `https://fabrikam.service-now.com/navpage.do`).

              c. Kopieren Sie den Wert für **Entity ID / Issuer** (Entitäts-ID/Aussteller), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration für ServiceNow** unter **Bezeichner** ein.

              d. Vergewissern Sie sich, dass **NameID Policy** (NameID-Richtlinie) auf den Wert `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` festgelegt ist. 

              e. Klicken Sie auf **Advanced** (Erweitert), und weisen Sie **Single Sign-On Script** (Skript für einmaliges Anmelden) den Wert **MultiSSOv2_SAML2_custom** zu.

         1. Scrollen Sie nach unten zum Abschnitt **X.509 Certificate** (X.509-Zertifikat), und wählen Sie **Edit** (Bearbeiten) aus.

             ![Screenshot: Abschnitt „X.509 Certificate“ (X.509-Zertifikat) mit hervorgehobener Option „Edit“ (Bearbeiten)](./media/servicenow-tutorial/tutorial-servicenow-09.png "Einmaliges Anmelden konfigurieren")

         1. Wählen Sie das Zertifikat und anschließend den Pfeil nach rechts aus, um das Zertifikat hinzuzufügen.

            ![Screenshot: Sammlung mit Hervorhebung des Zertifikats und des Rechtspfeils](./media/servicenow-tutorial/tutorial-servicenow-11.png "Einmaliges Anmelden konfigurieren")

          1. Wählen Sie **Speichern** aus.

          1. Wählen Sie rechts oben auf der Seite die Option **Test Connection** (Verbindung testen) aus.

             ![Screenshot: Seite mit hervorgehobener Option zum Testen der Verbindung](./media/servicenow-tutorial/tutorial-activate-2.png "Aktivieren des Plug-Ins")

             > [!NOTE]
             > Wenn bei der Testverbindung ein Fehler auftritt und Sie diese Verbindung nicht aktivieren können, können Sie den Außerkraftsetzungsschalter von ServiceNow nutzen. Geben Sie **Sys_properties.LIST** in der **Suchnavigation** ein. Daraufhin wird die neue Seite der Systemeigenschaften geöffnet. Hier müssen Sie eine neue Eigenschaft mit dem Namen **glide.authenticate.multisso.test.connection.mandatory** und dem Wert **True/False** für **datatype** erstellen. Legen Sie dann für **Wert** die Option **False** fest.

             > ![Screenshot der Seite „Verbindung testen“](./media/servicenow-tutorial/test-connection-fail.png "Einmaliges Anmelden konfigurieren")
        
          1. Geben Sie Ihre Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden. Daraufhin wird die folgende Seite angezeigt. Der Fehler unter **SSO Logout Test Results** (Testergebnisse der SSO-Abmeldung) wird erwartet. Ignorieren Sie den Fehler, und wählen Sie **Activate** (Aktivieren) aus.

             ![Screenshot der Seite „Anmeldeinformationen“](./media/servicenow-tutorial/servicenow-activate.png "Einmaliges Anmelden konfigurieren")
  
1. Führen Sie für die manuelle Konfiguration von **ServiceNow** die folgenden Schritte aus:

    1. Melden Sie sich bei Ihrer ServiceNow-Anwendung als Administrator an.

    1. Wählen Sie im linken Bereich **Identity Providers** (Identitätsanbieter) aus.

        ![Screenshot: „Multi-Provider SSO“ (Multi-Provider-SSO) mit Hervorhebung von „Identity Providers“ (Identitätsanbieter)](./media/servicenow-tutorial/tutorial-servicenow-07.png "Einmaliges Anmelden konfigurieren")

    1. Wählen Sie im Dialogfeld **Identity Providers** (Identitätsanbieter) die Option **New** (Neu) aus.

        ![Screenshot: Dialogfeld „Identity Providers“ (Identitätsanbieter) mit hervorgehobener Option „New“ (Neu)](./media/servicenow-tutorial/ic7694977.png "Einmaliges Anmelden konfigurieren")

    1. Wählen Sie im Dialogfeld **Identity Providers** (Identitätsanbieter) die Option **SAML** aus.

        ![Screenshot: Dialogfeld „Identity Providers“ (Identitätsanbieter) mit hervorgehobener Option „SAML“](./media/servicenow-tutorial/ic7694978.png "Einmaliges Anmelden konfigurieren")

    1. Führen Sie unter **Import Identity Provider Metadata** (Metadaten des Identitätsanbieters importieren) die folgenden Schritte aus:

        ![Screenshot: Dialogfeld „Import Identity Provider Metadata“ (Metadaten des Identitätsanbieters importieren) mit Hervorhebung der URL und der Option „Import“ (Importieren)](./media/servicenow-tutorial/idp.png "Einmaliges Anmelden konfigurieren")

        1. Geben Sie die **Verbundmetadaten-URL der App** ein, die Sie aus dem Azure-Portal kopiert haben.

        1. Wählen Sie **Importieren** aus.

    1. Die URL für die Metadaten des Identitätsanbieters wird gelesen, und alle Felder werden aufgefüllt.

        ![Screenshot: „Identity Provider“ (Identitätsanbieter)](./media/servicenow-tutorial/ic7694982.png "Einmaliges Anmelden konfigurieren")

        a. Geben Sie unter **Name** einen Namen für Ihre Konfiguration ein (beispielsweise **Microsoft Azure Federated single sign-on**).

        b. Kopieren Sie den Wert für **ServiceNow Homepage** (ServiceNow-Homepage). Fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration für ServiceNow** in das Textfeld **Anmelde-URL** ein.

        > [!NOTE]
        > Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** mit **/navpage.do** (beispielsweise `https://fabrikam.service-now.com/navpage.do`).

        c. Kopieren Sie den Wert für **Entity ID / Issuer** (Entitäts-ID/Aussteller). Fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration für ServiceNow** unter **Bezeichner** ein.

        d. Vergewissern Sie sich, dass **NameID Policy** (NameID-Richtlinie) auf den Wert `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` festgelegt ist.

        e. Wählen Sie **Erweitert** aus. Geben Sie unter **User Field** (Benutzerfeld) den Wert **email** ein.

        > [!NOTE]
        > Sie können Azure AD so konfigurieren, dass entweder die Azure AD-Benutzer-ID (Benutzerprinzipalname) oder die E-Mail-Adresse als eindeutiger Bezeichner im SAML-Token ausgegeben wird. Wechseln Sie dazu im Azure-Portal zu **ServiceNow** > **Attribute** > **Einmaliges Anmelden**, und weisen Sie das gewünschte Feld dem Attribut **nameidentifier** zu. Der gespeicherte Wert für das ausgewählte Attribut in Azure AD (beispielsweise „Benutzerprinzipalname“) muss dem in ServiceNow gespeicherten Wert für das eingegebene Feld (beispielsweise „user_name“) entsprechen.

        g. Wählen Sie rechts oben auf der Seite die Option **Test Connection** (Verbindung testen) aus.

        > [!NOTE]
        > Wenn bei der Testverbindung ein Fehler auftritt und Sie diese Verbindung nicht aktivieren können, können Sie den Außerkraftsetzungsschalter von ServiceNow nutzen. Geben Sie **Sys_properties.LIST** in der **Suchnavigation** ein. Daraufhin wird die neue Seite der Systemeigenschaften geöffnet. Hier müssen Sie eine neue Eigenschaft mit dem Namen **glide.authenticate.multisso.test.connection.mandatory** und dem Wert **True/False** für **datatype** erstellen. Legen Sie dann für **Wert** die Option **False** fest.

          > ![Screenshot von „Verbindung testen“](./media/servicenow-tutorial/test-connection-fail.png "Einmaliges Anmelden konfigurieren")

        h. Geben Sie Ihre Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden. Daraufhin wird die folgende Seite angezeigt. Der Fehler unter **SSO Logout Test Results** (Testergebnisse der SSO-Abmeldung) wird erwartet. Ignorieren Sie den Fehler, und wählen Sie **Activate** (Aktivieren) aus.

          ![credentials](./media/servicenow-tutorial/servicenow-activate.png "Einmaliges Anmelden konfigurieren")

### <a name="create-servicenow-test-user"></a>Erstellen eines ServiceNow-Testbenutzers

In diesem Abschnitt wird in ServiceNow ein Benutzer namens B.Simon erstellt. ServiceNow unterstützt die automatische Benutzerbereitstellung (standardmäßig aktiviert).

> [!NOTE]
> Wenn Sie einen Benutzer manuell erstellen möchten, wenden Sie sich an das [ServiceNow-Clientsupportteam](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Konfigurieren des einmaligen Anmeldens in ServiceNow Express

1. Melden Sie sich bei Ihrer ServiceNow Express-Anwendung als Administrator an.

2. Wählen Sie im linken Bereich **Single Sign-On** (Einmaliges Anmelden) aus.

    ![Screenshot: ServiceNow Express-Anwendung mit hervorgehobener Option für einmaliges Anmelden](./media/servicenow-tutorial/ic7694980ex.png "App-URL konfigurieren")

3. Wählen Sie rechts oben im Dialogfeld **Single Sign-On** (Einmaliges Anmelden) das Konfigurationssymbol aus, und legen Sie die folgenden Eigenschaften fest:

    ![Screenshot des Dialogfelds „Single Sign-On“ (Einmaliges Anmelden)](./media/servicenow-tutorial/ic7694981ex.png "App-URL konfigurieren")

    a. Schieben Sie den Umschalter der Option **Enable multiple provider SSO** (SSO für mehrere Anbieter aktivieren) nach rechts.

    b. Schieben Sie den Umschalter für **Enable debug logging for the multiple provider SSO integration** (Debugprotokollierung für die SSO-Integration für mehrere Anbieter aktivieren) nach rechts.

    c. Geben Sie unter **The field on the user table that...** (Das Feld in der Benutzertabelle, das...) die Zeichenfolge **user_name** ein.

4. Klicken Sie im Dialogfeld **Single Sign-On** (Einmaliges Anmelden) auf **Add New Certificate** (Neues Zertifikat hinzufügen).

    ![Screenshot: Dialogfeld „Single Sign-On“ (Einmaliges Anmelden) mit hervorgehobener Option „Add New Certificate“ (Neues Zertifikat hinzufügen)](./media/servicenow-tutorial/ic7694973ex.png "Einmaliges Anmelden konfigurieren")

5. Führen Sie im Dialogfeld **X.509 Certificates** (X.509-Zertifikate) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „X.509 Certificates“ (X.509-Zertifikate)](./media/servicenow-tutorial/ic7694975.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie unter **Name** einen Namen für Ihre Konfiguration ein (beispielsweise **TestSAML2.0**).

    b. Wählen Sie **Aktiv**.

    c. Wählen Sie unter **Format** das Format **PEM** aus.

    d. Wählen Sie unter **Type** (Typ) den Typ **Trust Store Cert** (Vertrauensspeicherzertifikat) aus.

    e. Öffnen Sie Ihr aus dem Azure-Portal heruntergeladenes Base64-codiertes Zertifikat in Editor. Kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn in das Textfeld **PEM Certificate** (PEM-Zertifikat) ein.

    f. Wählen Sie **Update** (Aktualisieren) aus.

6. Wählen Sie im Dialogfeld **Single Sign-On** (Einmaliges Anmelden) die Option **Add New IdP** (Neuen Identitätsanbieter hinzufügen) aus.

    ![Screenshot: Dialogfeld „Single Sign-On“ (Einmaliges Anmelden) mit hervorgehobener Option „Add New IdP“ (Neuen Identitätsanbieter hinzufügen)](./media/servicenow-tutorial/ic7694976ex.png "Einmaliges Anmelden konfigurieren")

7. Führen Sie im Dialogfeld **Add New Identity Provider** (Neuen Identitätsanbieter hinzufügen) unter **Configure Identity Provider** (Identitätsanbieter konfigurieren) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Add New Identity Provider“ (Neuen Identitätsanbieter hinzufügen)](./media/servicenow-tutorial/ic7694982ex.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie unter **Name** einen Namen für Ihre Konfiguration ein (beispielsweise **SAML 2.0**).

    b. Fügen Sie unter **Identity Provider URL** (Identitätsanbieter-URL) den Wert der Identitätsanbieter-ID ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie unter **Identity Provider's AuthnRequest** (AuthnRequest des Identitätsanbieters) den Wert der Authentifizierungsanforderungs-URL ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie unter **Identity Provider's AuthnRequest** („SingleLogoutRequest“ des Identitätsanbieters) den Wert der Abmelde-URL ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Wählen Sie unter **Identity Provider Certificate** (Identitätsanbieterzertifikat) das Zertifikat aus, das Sie im vorherigen Schritt erstellt haben.

8. Wählen Sie **Advanced Settings** (Erweiterte Einstellungen) aus. Führen Sie unter **Additional Identity Provider Properties** (Weitere Identitätsanbietereigenschaften) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Add New Identity Provider“ (Neuen Identitätsanbieter hinzufügen) mit hervorgehobener Option „Advanced Settings“ (Erweiterte Einstellungen)](./media/servicenow-tutorial/ic7694983ex.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie unter **Protocol Binding for the IDP's SingleLogoutRequest** (Protokollbindung für „SingleLogoutRequest“ des Identitätsanbieters) die Zeichenfolge **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect** ein.

    b. Geben Sie unter **NameID Policy** (NameID-Richtlinie) die Zeichenfolge **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified** ein.

    c. Geben Sie unter **AuthnContextClassRef Method** (AuthnContextClassRef-Methode) Folgendes ein: `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Deaktivieren Sie die Option **Create an AuthnContextClass** („AuthnContextClass“ erstellen).

9. Führen Sie unter **Additional Service Provider Properties** (Weitere Service Provider-Eigenschaften) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Add New Identity Provider“ (Neuen Identitätsanbieter hinzufügen) mit verschiedenen hervorgehobenen Eigenschaften](./media/servicenow-tutorial/ic7694984ex.png "Einmaliges Anmelden konfigurieren")

    a. Geben Sie unter **ServiceNow Homepage** (ServiceNow-Homepage) die URL der Homepage Ihrer ServiceNow-Instanz ein.

    > [!NOTE]
    > Die URL zur Homepage der ServiceNow-Instanz ist eine Verkettung Ihrer **ServiceNow-Mandanten-URL** mit **/navpage.do** (beispielsweise `https://fabrikam.service-now.com/navpage.do`).

    b. Geben Sie unter **Entity ID / Issuer** (Entitäts-ID/Aussteller) die URL Ihres ServiceNow-Mandaten ein.

    c. Geben Sie unter **Audience URI** (Zielgruppen-URI) die URL Ihres ServiceNow-Mandaten ein.

    d. Geben Sie unter **Clock Skew** (Uhrabweichungen) den Wert **60** ein.

    e. Geben Sie unter **User Field** (Benutzerfeld) den Wert **email** ein.

    > [!NOTE]
    > Sie können Azure AD so konfigurieren, dass entweder die Azure AD-Benutzer-ID (Benutzerprinzipalname) oder die E-Mail-Adresse als eindeutiger Bezeichner im SAML-Token ausgegeben wird. Wechseln Sie dazu im Azure-Portal zu **ServiceNow** > **Attribute** > **Einmaliges Anmelden**, und weisen Sie das gewünschte Feld dem Attribut **nameidentifier** zu. Der gespeicherte Wert für das ausgewählte Attribut in Azure AD (beispielsweise „Benutzerprinzipalname“) muss dem in ServiceNow gespeicherten Wert für das eingegebene Feld (beispielsweise „user_name“) entsprechen.

    f. Wählen Sie **Speichern** aus.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich auf die Kachel „ServiceNow“ klicken, sollten Sie automatisch bei Ihrer ServiceNow-Anwendung angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testen des einmaligen Anmeldens für ServiceNow Classic (mobil)

1. Öffnen Sie die Anwendung **ServiceNow Classic** (mobil), und führen Sie die folgenden Schritte aus:

    a. Wählen Sie rechts unten das Pluszeichen aus.

    ![Screenshot: ServiceNow Classic-Anwendung mit hervorgehobenem Pluszeichen](./media/servicenow-tutorial/test-03.png)

    b. Geben Sie den Namen Ihrer ServiceNow-Instanz ein, und wählen Sie **Continue** (Weiter) aus.

    ![Screenshot: Seite „Add Instance“ (Instanz hinzufügen) mit hervorgehobener Option „Continue“ (Weiter)](./media/servicenow-tutorial/test-04.png)

    c. Führen Sie auf der Seite **Log in** (Anmelden) die folgenden Schritte aus:

    ![Screenshot: Anmeldeseite mit hervorgehobener Option „Use external login“ (Externe Anmeldung verwenden)](./media/servicenow-tutorial/test-01.png)

    *  Geben Sie unter **Username** (Benutzername) einen Benutzernamen ein (beispielsweise B.simon@contoso.com).

    *  Wählen Sie **USE EXTERNAL LOGIN** (EXTERNE ANMELDUNG VERWENDEN) aus. Sie werden zur Azure AD-Seite für die Anmeldung weitergeleitet.

    *  Geben Sie Ihre Anmeldeinformationen ein. Falls eine Drittanbieterauthentifizierung vorhanden oder eine andere Sicherheitsfunktion aktiviert ist, muss der Benutzer entsprechend reagieren. Die **Startseite** der Anwendung wird angezeigt.

        ![Screenshot: Startseite der Anwendung](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ServiceNow können Sie Sitzungssteuerungen erzwingen. Dies schützt in Echtzeit vor der Exfiltration und Infiltration Ihrer vertraulichen Unternehmensdaten. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
