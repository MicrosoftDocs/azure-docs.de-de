---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure AD mit F5 | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden (Single Sign-On, SSO) zwischen Azure Active Directory und F5 konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 161348f7566ff64858d563f34ad8f3f4c7511adf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009159"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit F5

In diesem Tutorial erfahren Sie, wie Sie F5 in Azure Active Directory (Azure AD) integrieren. Die Integration von F5 in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf F5 hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei F5 anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

* F5-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

* Für die Bereitstellung der gemeinsamen Lösung wird folgende Lizenz benötigt:
    * F5 BIG-IP&reg;-Paket „Best“ (oder)

    * Eigenständige Lizenz für F5 BIG-IP Access Policy Manager&trade; (APM)

    * Add-On-Lizenz für F5 BIG-IP Access Policy Manager&trade; (APM) für eine bereits vorhandene Instanz von F5 BIG-IP&reg; Local Traffic Manager&trade; (LTM)

    * Zusätzlich zur obigen Lizenz kann das F5-System auch mit Folgendem lizenziert werden:

        * Abonnement der URL-Filterung zur Verwendung der URL-Kategoriedatenbank

        * F5 IP Intelligence-Abonnement zur Erkennung und Blockierung von bekannten Angreifern und schädlichem Datenverkehr

        * Netzwerk-HSM (Hardwaresicherheitsmodul) zum Schutz und zur Verwaltung digitaler Schlüssel für eine sichere Authentifizierung

* Das F5 BIG-IP-System wird mit APM-Modulen bereitgestellt. (LTM ist optional.)

* Es wird zwar dringend empfohlen, die F5-Systeme in einer [Synchronisierungs-/Failovergerätegruppe](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (Sync/Failover Device Group, S/F DG), die das aktive Standbypaar enthält, mit einer Floating IP-Adresse für Hochverfügbarkeit (High Availability, HA) bereitzustellen, dies ist jedoch optional. Eine noch höhere Schnittstellenredundanz kann durch Verwendung des Link Aggregation Control-Protokolls (LACP) erzielt werden. LACP verwaltet die verbundenen physischen Schnittstellen als einzelne virtuelle Schnittstelle (Aggregatgruppe) und erkennt alle Schnittstellenfehler innerhalb der Gruppe.

* Für Kerberos-Anwendungen: Ein lokales AD-Dienstkonto für die eingeschränkte Delegierung.  Informationen zum Erstellen eines AD-Delegierungskontos finden Sie in der [F5-Dokumentation](https://support.f5.com/csp/article/K43063049).

## <a name="access-guided-configuration"></a>Zugriffsgesteuerte Konfiguration

* Die zugriffsgesteuerte Konfiguration wird ab Version 13.1.0.8 von F5 TMOS unterstützt. Wenn in Ihrem BIG-IP-System eine Version vor 13.1.0.8 ausgeführt wird, lesen Sie den Abschnitt **Erweiterte Konfiguration**.

* Bei der zugriffsgesteuerten Konfiguration handelt es sich um eine völlig neue und optimierte Benutzererfahrung. Diese workflowbasierte Architektur bietet intuitive, eintrittsvariante Konfigurationsschritte, die auf die ausgewählte Topologie zugeschnitten sind.

* Upgraden Sie die interaktive Konfiguration, indem Sie das neueste Anwendungsfallpaket von [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748) herunterladen, bevor Sie mit der Konfiguration fortfahren. Gehen Sie zum Upgraden wie folgt vor:

    >[!NOTE]
    >Die bereitgestellten Screenshots stammen aus der neuesten veröffentlichten Version (BIG-IP 15.0 mit AGC-Version 5.0). Die Konfigurationsschritte gelten für diesen Anwendungsfall ab 13.1.0.8 bis zur neuesten BIG-IP-Version.

1. Klicken Sie auf der Webbenutzeroberfläche von F5 BIG-IP auf **Access > Guided Configuration** (Zugriff > Interaktive Konfiguration).

2. Klicken Sie links oben auf der Seite **Guided Configuration** (Interaktive Konfiguration) auf **Upgrade Guided Configuration** (Interaktive Konfiguration upgraden).

    ![Screenshot der Seite „Guided Configuration“ (Interaktive Konfiguration) mit der ausgewählten Aktion „Upgrade Guided Configuration“ (Interaktive Konfiguration upgraden)](./media/kerbf5-tutorial/configure14.png) 

3. Wählen Sie im daraufhin angezeigten Bildschirm zum Upgraden der interaktiven Konfiguration die Option **Choose File** (Datei auswählen) aus, um das heruntergeladene Anwendungsfallpaket hochzuladen, und klicken Sie auf die Schaltfläche **Upload and Install** (Hochladen und installieren).

    ![Screenshot des Popupbildschirms „Upgrade Guided Configuration“ (Interaktive Konfiguration upgraden) mit der Auswahl von „Choose File“ (Datei auswählen) und „Upload and Install“ (Hochladen und installieren)](./media/kerbf5-tutorial/configure15.png) 

4. Klicken Sie nach Abschluss des Upgrades auf die Schaltfläche **Continue** (Weiter).

    ![Screenshot des Dialogfelds „Guided Configuration update is complete“ (Aktualisieren der interaktiven Konfiguration abgeschlossen) und der ausgewählten Schaltfläche „Continue“ (Weiter)](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* F5 unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Einmaliges Anmelden von F5 kann auf drei unterschiedliche Arten konfiguriert werden:

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](#configure-f5-single-sign-on-for-kerberos-application)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](headerf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Wichtige Authentifizierungsszenarien

Neben der nativen Azure Active Directory-Integrationsunterstützung für moderne Authentifizierungsprotokolle wie OpenID Connect, SAML und WS-Fed erweitert F5 den sicheren Zugriff für legacybasierte Authentifizierungs-Apps sowohl für den internen als auch für den externen Zugriff mit Azure AD, um für diese Anwendungen moderne Szenarien wie etwa Zugriff ohne Kennwort zu ermöglichen. Dies beinhaltet Folgendes:

* Apps mit headerbasierter Authentifizierung

* Apps mit Kerberos-Authentifizierung

* Apps mit anonymer Authentifizierung oder ohne integrierte Authentifizierung

* Apps mit NTLM-Authentifizierung (Schutz mit doppelten Eingabeaufforderungen für den Benutzer)

* Formularbasierte Anwendung (Schutz mit doppelten Eingabeaufforderungen für den Benutzer)

## <a name="adding-f5-from-the-gallery"></a>Hinzufügen von F5 aus dem Katalog

Zum Konfigurieren der Integration von F5 in Azure AD müssen Sie F5 aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **F5** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **F5** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für F5

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit F5 mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in F5 eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit F5 die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für F5](#configure-f5-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines F5-Testbenutzers](#create-f5-test-user)** , um eine Entsprechung von B. Simon in F5 zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **F5** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den F5-Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** nach **Verbundmetadaten-XML** sowie nach **Zertifikat (Base64)** , und wählen Sie anschließend **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **F5 einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf F5 gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **F5** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.
1. Klicken Sie auf **Bedingter Zugriff**.
1. Klicken Sie auf **Neue Richtlinie**.
1. Daraufhin wird Ihre F5-App als Ressource für eine Richtlinie für bedingten Zugriff angezeigt, und Sie können einen beliebigen bedingten Zugriff anwenden (beispielsweise eine mehrstufige Authentifizierung, eine gerätebasierte Zugriffssteuerung oder eine Identity Protection-Richtlinie).

## <a name="configure-f5-sso"></a>Konfigurieren des einmaligen Anmeldens für F5

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](headerf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung

### <a name="guided-configuration"></a>Interaktive Konfiguration

1. Melden Sie sich in einem neuen Webbrowserfenster bei der F5-Unternehmenswebsite (Kerberos) als Administrator an, und führen Sie die folgenden Schritte aus:

1. Sie müssen das Metadatenzertifikat in F5 importieren. Es wird später bei der Einrichtung benötigt.

1. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus. Geben Sie unter **Certificate Name** (Zertifikatname) einen Zertifikatnamen an. (Auf diesen wird später in der Konfiguration verwiesen.) Wählen Sie unter **Certificate Source** (Zertifikatquelle) die Option „Upload File“ (Datei hochladen) aus, und geben Sie das Zertifikat an, das Sie beim Konfigurieren des einmaligen Anmeldens mit SAML heruntergeladen haben. Klicken Sie auf **Importieren**.

    ![Screenshot der Seite „S S L Certificate/Key Source“ (SSL-Zertifikat-/Schlüsselquelle) mit „Certificate Name“ (Zertifikatname) hervorgehoben und den ausgewählten Schaltflächen „Upload File“ (Datei hochladen) und „Import“ (Importieren)](./media/kerbf5-tutorial/configure01.png) 

1. Darüber hinaus benötigen Sie das SSL-Zertifikat für den Anwendungshostnamen. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus. Der **Importtyp** lautet **PKCS 12 (IIS)** . Geben Sie unter **Key Name** (Schlüsselname) einen Schlüsselnamen und anschließend die PFX-Datei an. (Auf den Schlüsselnamen wird später in der Konfiguration verwiesen.) Geben Sie das **Kennwort** für die PFX-Datei an. Klicken Sie auf **Importieren**.

    >[!NOTE]
    >Im vorliegenden Beispiel heißt unsere App `Kerbapp.superdemo.live`, wir verwenden ein Platzhalterzertifikat, und der Schlüsselname lautet `WildCard-SuperDemo.live`.

    ![Screenshot der Seite „S S L Certificate/Key Source“ (SSL-Zertifikat-/Schlüsselquelle) mit den eingegebenen Werten und der ausgewählten Schaltfläche „Import“ (Importieren)](./media/kerbf5-tutorial/configure02.png) 
 
1. Wir verwenden die interaktive Umgebung, um den Azure AD-Verbund und den Anwendungszugriff einzurichten. Navigieren Sie in F5 BIG-IP unter **Main** (Hauptmenü) zu **Access > Guided Configuration > Federation > SAML Service Provider** (Zugriff > Interaktive Konfiguration > Verbund > SAML-Dienstanbieter). Klicken Sie auf **Next** (Weiter) und anschließend erneut auf **Next** (Weiter), um mit der Konfiguration zu beginnen.

    ![Screenshot der Seite „Guided Configuration“ (Interaktive Konfiguration) mit hervorgehobenem Symbol „Federation“ (Verbund) und „S A M L Service Provider“ (SAML-Dienstanbieter)](./media/kerbf5-tutorial/configure03.png) 

    ![Screenshot der Seite „Guided Configuration - S A M L Service Provider“ (Interaktive Konfiguration – SAML-Dienstanbieter) mit der ausgewählten Schaltfläche „Next“ (Weiter)](./media/kerbf5-tutorial/configure04.png)

1. Geben Sie unter **Configuration Name** (Konfigurationsname) einen Namen für die Konfiguration an. Geben Sie unter **Entity ID** (Entitäts-ID) die Entitäts-ID an. Verwenden Sie dabei den Wert aus der Azure AD-Anwendungskonfiguration. Geben Sie unter **Host Name** (Hostname) den Hostnamen an. Fügen Sie unter **Description** (Beschreibung) eine Beschreibung als Referenz hinzu. Übernehmen Sie die restlichen Standardeinträge und -optionen, und klicken Sie anschließend auf **Save & Next** (Speichern und weiter).

    ![Screenshot von „Service Provider Properties“ (Dienstanbietereigenschaften) mit den hervorgehobenen Textfeldern „Host name“ (Hostname) und „Description“ (Beschreibung) und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure05.png) 

1. In diesem Beispiel erstellen wir einen neuen virtuellen Server als 192.168.30.200 mit Port 443. Geben Sie unter **Destination Address** (Zieladresse) die IP-Adresse des virtuellen Servers an. Wählen Sie unter **Client SSL Profile** (Client-SSL-Profil) die Option „Create new“ (Neu erstellen) aus. Geben Sie das zuvor hochgeladene Anwendungszertifikat (in diesem Beispiel das Platzhalterzertifikat) sowie den zugehörigen Schlüssel an, und klicken Sie anschließend auf **Save & Next** (Speichern und weiter).

    >[!NOTE]
    >In diesem Beispiel wird unser interner Webserver am Port 80 ausgeführt, und wir möchten ihn mit dem Port 443 veröffentlichen.

    ![Screenshot der Seite „Virtual Server Properties“ (Eigenschaften des virtuellen Servers) mit hervorgehobenem Textfeld „Destination Address“ (Zieladresse) und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure06.png)

1. Geben Sie unter **Select method to configure your IdP connector** (Konfigurationsmethode für Ihren IdP-Connector auswählen) die Option „Metadata“ (Metadaten) an, klicken Sie auf „Choose File“ (Datei auswählen), und laden Sie die zuvor heruntergeladene Metadaten-XML-Datei aus Azure AD hoch. Geben Sie unter **Name** einen eindeutigen Namen für den SAML-IdP-Connector an. Wählen Sie unter **Metadata Signing Certificate** (Metadatensignaturzertifikat) das zuvor hochgeladene Metadatensignaturzertifikat aus. Klicken Sie auf **Save & Next** (Speichern und weiter).

    ![Screenshot der Seite „External Identity Provider Connector Settings“ (Connectoreinstellungen für externe Identitätsanbieter) mit hervorgehobenem Textfeld „Name“ und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure07.png)  

1. Wählen Sie unter **Select a Pool** (Pool auswählen) entweder die Option **Create New** (Neu erstellen) oder einen bereits vorhandenen Pool aus. Lassen Sie den anderen Wert unverändert.    Geben Sie unter **Pool Servers** (Poolserver) die IP-Adresse in das Feld für die IP-Adresse/den Knotennamen ein. Geben Sie den **Port** an. Klicken Sie auf **Save & Next** (Speichern und weiter).
 
    ![Screenshot der Seite „Pool Properties“ (Pooleigenschaften) mit hervorgehobenem Textfeld „IP Address/Node Name“ (IP-Adresse/Knotenname) und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure08.png)

1. Aktivieren Sie im Bildschirm mit den Einstellungen für einmaliges Anmelden das Kontrollkästchen **Enable Single Sign-On** (Einmaliges Anmelden aktivieren). Wählen Sie unter **Selected Single Sign-On Type** (Ausgewählter SSO-Typ) die Option **Kerberos** aus. Ersetzen Sie **session.saml.last.Identity** unter **Username Source** (Quelle des Benutzernamens) durch **session.saml.last.attr.name.Identity**. (Diese Variable wird unter Verwendung der Anspruchszuordnung in Azure AD festgelegt.) Wählen Sie **Show Advanced Setting** (Erweiterte Einstellung anzeigen) aus. Geben Sie unter **Kerberos Realm** (Kerberos-Bereich) den Domänennamen ein. Geben Sie unter **Account Name / Account Password** (Kontoname/Kontokennwort) das APM-Delegierungskonto und das zugehörige Kennwort an. Geben Sie im Feld **KDC** die IP-Adresse des Domänencontrollers an. Klicken Sie auf **Save & Next** (Speichern und weiter).

    ![Screenshot von „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden) mit hervorgehobenen Textfeldern und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure09.png)   

1. Der Einfachheit halber werden die Endpunktprüfungen in diesem Leitfaden übersprungen.  Details finden Sie in der F5-Dokumentation.  Wählen Sie **Save & Next** (Speichern und weiter) aus.

    ![Screenshot der Seite „Endpoint Checks Properties“ (Eigenschaften der Endpunktprüfungen) und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure10.png) 

1. Übernehmen Sie die Standardwerte, und klicken Sie auf **Save & Next** (Speichern und weiter). Ausführliche Informationen zu den Einstellungen für die SAML-Sitzungsverwaltung finden Sie in der F5-Dokumentation.


    ![Screenshot der Seite „Timeout Settings“ (Timeouteigenschaften) und der ausgewählten Schaltfläche „Save & Next“ (Speichern und weiter)](./media/kerbf5-tutorial/configure11.png) 
 
1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Deploy** (Bereitstellen) aus, um BIG-IP zu konfigurieren.
 
    ![Screenshot der Seite „Your application is ready to be deployed“ (Ihre Anwendung kann jetzt bereitgestellt werden) mit dem hervorgehobenen Abschnitt „Summary“ (Zusammenfassung) und der ausgewählten Schaltfläche „Deploy“ (Bereitstellen)](./media/kerbf5-tutorial/configure12.png)

1. Klicken Sie nach Abschluss der Anwendungskonfiguration auf **Finish** (Fertig stellen).

    ![Screenshot der Seite „Your application is deployed“ (Ihre Anwendung wird bereitgestellt) mit der ausgewählten Schaltfläche „Finish“ (Fertig stellen)](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

>[!NOTE]
>Referenzinformationen finden Sie [hier](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html).

### <a name="configuring-an-active-directory-aaa-server"></a>Konfigurieren eines Active Directory-AAA-Servers

Sie konfigurieren einen Active Directory-AAA-Server in Access Policy Manager (APM), um Domänencontroller und Anmeldeinformationen anzugeben, die von APM zum Authentifizieren von Benutzern verwendet werden.

1. Klicken Sie auf der Registerkarte „Main“ (Hauptmenü) auf **Access Policy > AAA Servers > Active Directory** (Zugriffsrichtlinie > AAA-Server > Active Directory). Der Bildschirm mit der Active Directory-Serverliste wird geöffnet.

2. Klicken Sie auf **Erstellen**. Der Bildschirm mit den Eigenschaften für den neuen Server wird geöffnet.

3. Geben Sie im Feld **Name** einen eindeutigen Namen für den Authentifizierungsserver ein.

4. Geben Sie im Feld **Domain Name** (Domänenname) den Namen der Windows-Domäne ein.

5. Wählen Sie für die Einstellung **Server Connection** (Serververbindung) eine der folgenden Optionen aus:

   * Wählen Sie **Use Pool** (Pool verwenden) aus, um Hochverfügbarkeit für den AAA-Server einzurichten.

   * Wählen Sie **Direct** (Direkt) aus, um den AAA-Server als eigenständigen Server einzurichten.

6. Falls Sie die Option **Direct** (Direkt) ausgewählt haben, geben Sie einen Namen in das Feld **Domain Controller** (Domänencontroller) ein.

7. Falls Sie die Option **Use Pool** (Pool verwenden) ausgewählt haben, konfigurieren Sie den Pool:

   * Geben Sie im Feld **Domain Controller Pool Name** (Name des Domänencontroller-Pools) einen Namen ein.

   * Geben Sie die **Domänencontroller** im Pool an, indem Sie jeweils die IP-Adresse und den Hostnamen eingeben und anschließend auf die Schaltfläche **Add** (Hinzufügen) klicken.

   * Zur Überwachung der Integrität des AAA-Servers können Sie eine Integritätsüberwachung auswählen. In diesem Fall ist nur die Überwachung **gateway_icmp** geeignet. Diese Option kann in der Liste **Server Pool Monitor** (Serverpoolüberwachung) ausgewählt werden.

8. Geben Sie im Feld **Admin Name** (Administratorname) einen Namen für einen Administrator ein, der über Administratorberechtigungen für Active Directory verfügt. (Bei diesem Namen wird die Groß-/Kleinschreibung beachtet.) APM verwendet die Informationen aus den Feldern **Admin Name** (Administratorname) und **Admin Password** (Administratorkennwort) für AD-Abfragen. Ist Active Directory für anonyme Abfragen konfiguriert, muss kein Administratorname angegeben werden. Andernfalls benötigt APM ein Konto, das über ausreichende Berechtigungen für die Bindung an einen Active Directory-Server sowie für den Abruf von Benutzergruppeninformationen und Active Directory-Kennwortrichtlinien zur Unterstützung kennwortbezogener Funktionen verfügt. (APM muss Kennwortrichtlinien abrufen, wenn Sie beispielsweise in einer AD-Abfrageaktion auswählen, dass der Benutzer vor Ablauf des Kennworts zum Ändern des Kennworts aufgefordert werden soll.) Wenn Sie in dieser Konfiguration keine Administratorkontoinformationen angeben, verwendet APM das Benutzerkonto, um Informationen abzurufen. In diesem Fall muss das Benutzerkonto über ausreichende Berechtigungen verfügen.

9. Geben Sie im Feld **Admin Password** (Administratorkennwort) das Administratorkennwort ein, das dem Domänennamen zugeordnet ist.

10. Geben Sie im Feld **Verify Admin Password** (Administratorkennwort bestätigen) das Administratorkennwort ein, das der Einstellung **Domain Name** (Domänenname) zugeordnet ist.

11. Geben Sie im Feld **Group Cache Lifetime** (Cachelebensdauer für die Gruppe) die Anzahl von Tagen ein. Die Standardlebensdauer beträgt 30 Tage.

12. Geben Sie im Feld **Password Security Object Cache Lifetime** (Cachelebensdauer für das Kennwortsicherheitsobjekt) die Anzahl von Tagen ein. Die Standardlebensdauer beträgt 30 Tage.

13. Wählen Sie in der Liste **Kerberos Preauthentication Encryption Type** (Verschlüsselungstyp der Kerberos-Vorauthentifizierung) einen Verschlüsselungstyp aus. Der Standardwert ist **None** (Kein). Wenn Sie einen Verschlüsselungstyp angeben, schließt das BIG-IP-System Kerberos-Vorauthentifizierungsdaten in das erste AS-REQ-Paket (Authentication Service Request, Authentifizierungsdienstanforderung) ein.

14. Geben Sie im Feld **Timeout** ein Timeoutintervall (in Sekunden) für den AAA-Server ein. (Diese Einstellung ist optional.)

15. Klicken Sie auf **Finished** (Fertig). Der neue Server wird in der Liste angezeigt. Dadurch wird der neue Active Directory-Server der Active Directory-Serverliste hinzugefügt.

    ![Screenshot der Abschnitte „General Properties“ (Allgemeine Eigenschaften) und „Configuration“ (Konfiguration)](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML-Konfiguration

1. Sie müssen das Metadatenzertifikat in F5 importieren. Es wird später bei der Einrichtung benötigt. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus.

    ![Screenshot der Seite „Import S S L Certificate/Key Source“ (SSL-Zertifikat-/Schlüsselquelle importieren) mit der ausgewählten Schaltfläche „Import“ (Importieren)](./media/kerbf5-tutorial/configure18.png)

2. Navigieren Sie zum Einrichten des SAML-Identitätsanbieters zu **Access > Federation > SAML Service Provider > External Idp Connectors** (Zugriff > Verbund > SAML-Dienstanbieter > Externe IdP-Connectors), und klicken Sie auf **Create > From Metadata** (Erstellen > Auf der Grundlage von Metadaten).

    ![Screenshot der Seite „S A M L Service Provider“ (SAML-Dienstanbieter) mit der Auswahl „From Metadata“ (Auf der Grundlage von Metadaten) in der Dropdownliste „Create“ (Erstellen)](./media/kerbf5-tutorial/configure19.png)

    ![Screenshot des Dialogfelds „Create New S A M L I d P Connector“ (Neuen SAML-IdP-Connector erstellen)](./media/kerbf5-tutorial/configure20.png)

    ![Screenshot des Fensters „Edit S A M L I d P Connector“ (SAML-IdP-Connector bearbeiten) mit der Auswahl „General Settings“ (Allgemeine Einstellungen)](./media/kerbf5-tutorial/configure21.png)

    ![Screenshot des Fensters „Edit S A M L I d P Connector“ (SAML-IdP-Connector bearbeiten) mit der Auswahl „Single Sign On Service Settings“ (Diensteinstellung für einmaliges Anmelden)](./media/kerbf5-tutorial/configure22.png)

    ![Screenshot des Fensters „Edit S A M L I d P Connector“ (SAML-IdP-Connector bearbeiten) mit der Auswahl „Security Settings“ (Sicherheitseinstellungen)](./media/kerbf5-tutorial/configure23.png)

    ![Screenshot des Fensters „Edit S A M L I d P Connector“ (SAML-IdP-Connector bearbeiten) mit der Auswahl „S L O Service Settings“ (SLO-Diensteinstellungen)](./media/kerbf5-tutorial/configure24.png)

1. Navigieren Sie zum Einrichten des SAML-Dienstanbieters zu **Access > Federation > SAML Service Provider > Local SP Services** (Zugriff > Verbund > SAML-Dienstanbieter > Lokale SP-Dienste), und klicken Sie auf **Create** (Erstellen). Geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **OK**:

    * Name: KerbApp200SAML
    * Entity ID* (Entitäts-ID): https://kerb-app.com.cutestat.com
    * SP Name Settings (SP-Namenseinstellungen)
    * Scheme (Schema): https
    * Host: kerbapp200.superdemo.live
    * Description (Beschreibung): kerbapp200.superdemo.live

     ![Screenshot des Fensters „Edit S A M L S P Service“ (SAML-LSP-Dienst bearbeiten) mit der Auswahl „General Settings“ (Allgemeine Einstellungen)](./media/kerbf5-tutorial/configure25.png)

     b. Wählen Sie die SP-Konfiguration „KerbApp200SAML“ aus, und klicken Sie auf **Bind/Unbind IdP Connectors** (IdP-Connectors binden/Bindung der IdP-Connectors aufheben).

     ![Screenshot der Seite „S A M L Service Provider - Local S P Services“ (SAML-Dienstanbieter – lokale SP-Dienste) mit der Auswahl „KerbAPP200 S A M L“](./media/kerbf5-tutorial/configure26.png)

     ![Screenshot der ausgewählten Schaltfläche „Bind/Unbind I d P Connectors“ (IdP-Connectors binden/Bindung der IdP-Connectors aufheben)](./media/kerbf5-tutorial/configure27.png)

     c. Klicken Sie auf **Add New Row** (Neue Zeile hinzufügen), wählen Sie den im vorherigen Schritt erstellten **externen IdP-Connector** aus, und klicken Sie auf **Update** (Aktualisieren) und anschließend auf **OK**.

     ![Screenshot des Fensters „Edit S A M L I d Ps that use this S P“ (SAML-IdPs bearbeiten, die diesen SP nutzen) mit der ausgewählten Schaltfläche „Add New Row“ (Neue Zeile hinzufügen)](./media/kerbf5-tutorial/configure28.png)

1. Navigieren Sie zum Konfigurieren des einmaligen Anmeldens für Kerberos zu **Access > Single Sign-on > Kerberos** (Zugriff > Einmaliges Anmelden > Kerberos), geben Sie die Informationen an, und klicken Sie anschließend auf **Finished** (Fertig).

    >[!Note]
    > Das Kerberos-Delegierungskonto muss erstellt und angegeben werden. Informationen finden Sie im Abschnitt zu KCD. (Variablenverweise finden Sie im Anhang.)

    * **Username Source** (Quelle des Benutzernamens): session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **User Realm Source** (Quelle des Benutzerbereichs): session.logon.last.domain

        ![Screenshot der Seite „Single Sign-On - Properties“ (Einmaliges Anmelden – Eigenschaften) mit hervorgehobenen Textfeldern „Username Source“ (Quelle des Benutzernamens) und „User Realm Source“ (Quelle des Benutzerbereichs)](./media/kerbf5-tutorial/configure29.png)

1. Navigieren Sie zum Konfigurieren des Zugriffsprofils zu **Access > Profile/Policies > Access Profile (per session policies)** (Zugriff > Profil/Richtlinien > Zugriffsprofil (Richtlinien pro Sitzung)), klicken Sie auf **Create** (Erstellen) geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig):

    * Name: KerbApp200
    * Profile Type (Profiltyp): All
    * Profile Scope (Profilbereich): Profil
    * Sprachen: Englisch

        ![Screenshot der Seite „Profile/Policies - Properties“ (Profile/Richtlinien – Eigenschaften) mit hervorgehobenen Textfeldern „Name“, „Profile Type“ (Profiltyp) und „Languages“ (Sprachen)](./media/kerbf5-tutorial/configure30.png)

1. Klicken Sie auf den Namen (KerbApp200), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Update** (Aktualisieren):

    * Domain Cookie (Domänencookie): superdemo.live
    * SSO Configuration (SSO-Konfiguration): KerAppSSO_sso

        ![Screenshot der Seite „S S D/Auth Domains“ (SSD/Authentifizierungsdomänen) mit hervorgehobenem Textfeld „Domain Cookie“ (Domänencookie) und hervorgehobenem Dropdownfeld „S S O Configuration“ (SSO-Konfiguration) und ausgewählter Schaltfläche „Update“ (Aktualisieren)](./media/kerbf5-tutorial/configure31.png)

1. Klicken Sie auf **Zugriffsrichtlinie**, und klicken Sie dann für das Profil „KerbApp200“ auf **Zugriffsrichtlinie bearbeiten**.

    ![Screenshot der Seite „Access Policy“ (Zugriffsrichtlinie) mit der ausgewählten Aktion „Edit Access Policy for Profile KerbApp200“ (Zugriffsrichtlinie für das Profil KerbApp200 bearbeiten)](./media/kerbf5-tutorial/configure32.png)

    ![Screenshot der Seite „Access Policy“ (Zugriffsrichtlinie) und des Dialogfelds „S A M L Authentication S P“ (SAML-Authentifizierungs-SP)](./media/kerbf5-tutorial/configure33.png)

    ![Screenshot der Seite „Access Policy“ (Zugriffsrichtlinie) und des Dialogfelds „Variable Assign“ (Variablenzuweisung) mit hervorgehobenen Textfeldern „Assignment“ (Zuweisung)](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![Screenshot der Seite „Access Policy“ (Zugriffsrichtlinie) und des Dialogfelds „Active Directory“ mit hervorgehobenem Textfeld „SearchFilter“](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![Screenshot der Seite„Access Policy“ (Zugriffsrichtlinie) mit dem Dialogfeld „A D Query - Branch Rules“ (AD-Abfrage – Branchregeln)](./media/kerbf5-tutorial/configure36.png)

        ![Screenshot der hervorgehobenen Textfelder „Custom Variable“ (Benutzerdefinierte Variable) und „Custom Expression“ (Benutzerdefinierte Ausdrücke)](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![Screenshot des hervorgehobenen Textfelds „Username from Logon Page“ (Benutzername von Anmeldeseite)](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Klicken Sie zum Hinzufügen eines neuen Knotens auf **Local Traffic > Nodes > Node List > Create** (Lokaler Datenverkehr > Knoten > Knotenliste > Erstellen), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig):

    * Name: KerbApp200
    * Beschreibung: KerbApp200
    * Adresse: 192.168.20.200

        ![Screenshot der Seite „New Node“ (Neuer Knoten) mit hervorgehobenen Textfeldern „Name“, „Description“ (Beschreibung) und „Address“ (Adresse) und der ausgewählten Schaltfläche „Finished“ (Fertig)](./media/kerbf5-tutorial/configure39.png)

1. Klicken Sie zum Erstellen eines neuen Pools auf **Local Traffic > Pools > Pool List > Create** (Lokaler Datenverkehr > Pools > Poolliste > Erstellen), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig):

    * Name: KerbApp200-Pool
    * Beschreibung: KerbApp200-Pool
    * Health Monitors (Integritätsmonitore): http
    * Adresse: 192.168.20.200
    * Service Port (Dienstport): 81

        ![Screenshot der Seite „New Pool“ (Neuer Pool) mit eingegebenen Werten und der ausgewählten Schaltfläche „Finished“ (Fertig)](./media/kerbf5-tutorial/configure40.png)

1. Klicken Sie zum Erstellen eines neuen Pools auf **Local Traffic > Virtual Servers > Virtual Server List > +** (Lokaler Datenverkehr > Virtuelle Server > Liste virtueller Server > +), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig):

    * Name: KerbApp200
    * Destination Address/Mask (Zieladresse/Maske): Host 192.168.30.200
    * Service Port (Dienstport): Port 443 HTTPS
    * Access Profile (Zugriffsprofil): KerbApp200
    * Geben Sie das im vorherigen Schritt erstellte Zugriffsprofil an.

        ![Screenshot der Seite „Virtual Server List“ (Liste virtueller Server) mit hervorgehobenen Textfeldern „Name“, „Destination Address/Mask“ (Zieladresse/Maske) und „Service Port“ (Dienstport)](./media/kerbf5-tutorial/configure41.png)

        ![Screenshot der Seite „Virtual Server List“ (Liste virtueller Server) mit hervorgehobener Dropdownfeld „Access Profile“ (Zugriffsprofil)](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Einrichten der Kerberos-Delegierung 

>[!NOTE]
>Referenzinformationen finden Sie [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf).

*  **Schritt 1:** Erstellen eines Delegierungskontos

    **Beispiel:**
    * Domänenname: **superdemo.live**

    * SAM-Kontoname: **big-ipuser**

    * New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **Schritt 2:** Festlegen des SPN (für das APM-Delegierungskonto)

    **Beispiel:**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **Schritt 3:** SPN-Delegierung (für das App Service-Konto): Richten Sie die entsprechende Delegierung für das F5-Delegierungskonto ein.
    Im folgenden Beispiel wird das APM-Delegierungskonto für KCD für die App „FRP-App1.superdemo.live“ konfiguriert:

    ![F5-Konfiguration (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Geben Sie die Details wie in [diesem Referenzdokument](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) beschrieben ein.

### <a name="create-f5-test-user"></a>Erstellen eines F5-Testbenutzers

In diesem Abschnitt erstellen Sie in F5 einen Benutzer namens B. Simon. Zum Hinzufügen der Benutzer auf der F5-Plattform wenden Sie sich an das [Supportteam für den F5-Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „F5“ klicken, sollten Sie automatisch bei der F5-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [F5 mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](headerf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](advance-kerbf5-tutorial.md)