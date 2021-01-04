---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit F5 | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und F5 konfigurieren.
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
ms.openlocfilehash: 8706ac588f2cc868805de7126140a1edd4b8c735
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92445527"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Tutorial: Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) zwischen Azure Active Directory und F5

In diesem Tutorial erfahren Sie, wie Sie F5 in Azure Active Directory (Azure AD) integrieren. Die Integration von F5 in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf F5 hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei F5 anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps mit einmaligem Anmelden in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

* F5-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

* Für die Bereitstellung der gemeinsamen Lösung wird folgende Lizenz benötigt:

    * F5 BIG-IP®-Paket „Best“ (oder) 

    * Eigenständige Lizenz für F5 BIG-IP Access Policy Manager™ (APM) 

    * Add-On-Lizenz für F5 BIG-IP Access Policy Manager™ (APM) für eine bereits vorhandene Instanz von F5 BIG-IP® Local Traffic Manager™ (LTM)

    * Zusätzlich zur obigen Lizenz kann das F5-System auch mit Folgendem lizenziert werden: 

        * Abonnement der URL-Filterung zur Verwendung der URL-Kategoriedatenbank

        * F5 IP Intelligence-Abonnement zur Erkennung und Blockierung von bekannten Angreifern und schädlichem Datenverkehr
     
        * Netzwerk-HSM (Hardwaresicherheitsmodul) zum Schutz und zur Verwaltung digitaler Schlüssel für eine sichere Authentifizierung

* Das F5 BIG-IP-System wird mit APM-Modulen bereitgestellt. (LTM ist optional.)

* Es wird zwar dringend empfohlen, die F5-Systeme in einer [Synchronisierungs-/Failovergerätegruppe](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (Sync/Failover Device Group, S/F DG), die das aktive Standbypaar enthält, mit einer Floating IP-Adresse für Hochverfügbarkeit (High Availability, HA) bereitzustellen, dies ist jedoch optional. Eine noch höhere Schnittstellenredundanz kann durch Verwendung des Link Aggregation Control-Protokolls (LACP) erzielt werden. LACP verwaltet die verbundenen physischen Schnittstellen als einzelne virtuelle Schnittstelle (Aggregatgruppe) und erkennt alle Schnittstellenfehler innerhalb der Gruppe.

* Für Kerberos-Anwendungen: Ein lokales AD-Dienstkonto für die eingeschränkte Delegierung.  Informationen zum Erstellen eines AD-Delegierungskontos finden Sie in der [F5-Dokumentation](https://support.f5.com/csp/article/K43063049).

## <a name="access-guided-configuration"></a>Zugriffsgesteuerte Konfiguration

* Die zugriffsgesteuerte Konfiguration wird ab der Version 13.1.0.8 von F5 TMOS unterstützt. Wenn in Ihrem BIG-IP-System eine Version vor 13.1.0.8 ausgeführt wird, lesen Sie den Abschnitt **Erweiterte Konfiguration**.

* Bei der zugriffsgesteuerten Konfiguration handelt es sich um eine völlig neue und optimierte Benutzererfahrung. Diese workflowbasierte Architektur bietet intuitive, eintrittsvariante Konfigurationsschritte, die auf die ausgewählte Topologie zugeschnitten sind.

* Upgraden Sie die interaktive Konfiguration, indem Sie das neueste Anwendungsfallpaket von [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748) herunterladen, bevor Sie mit der Konfiguration fortfahren. Gehen Sie zum Upgraden wie folgt vor:

    >[!NOTE]
    >Die bereitgestellten Screenshots stammen aus der neuesten veröffentlichten Version (BIG-IP 15.0 mit AGC-Version 5.0). Die Konfigurationsschritte gelten für diesen Anwendungsfall ab 13.1.0.8 bis zur neuesten BIG-IP-Version.

1. Klicken Sie auf der Webbenutzeroberfläche von F5 BIG-IP auf **Access > Guided Configuration** (Zugriff > Interaktive Konfiguration).

1. Klicken Sie links oben auf der Seite **Guided Configuration** (Interaktive Konfiguration) auf **Upgrade Guided Configuration** (Interaktive Konfiguration upgraden).

    ![Screenshot der Seite „Guided Configuration“ mit dem Link „Update Guided Configuration“](./media/headerf5-tutorial/configure14.png) 

1. Wählen Sie im daraufhin angezeigten Bildschirm zum Upgraden der interaktiven Konfiguration die Option **Choose File** (Datei auswählen) aus, um das heruntergeladene Anwendungsfallpaket hochzuladen, und klicken Sie auf die Schaltfläche **Upload and Install** (Hochladen und installieren).

    ![Screenshot des Dialogfelds „Upgrade Guided Configuration“ mit ausgewählter Schaltfläche „Choose File“.](./media/headerf5-tutorial/configure15.png) 

1. Klicken Sie nach Abschluss des Upgrades auf die Schaltfläche **Continue** (Weiter).

    ![Screenshot des Dialogfelds „Upgrade Guided Configuration“ mit einer Abschlussmeldung](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Einmaliges Anmelden von F5 kann auf drei unterschiedliche Arten konfiguriert werden:

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](kerbf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Wichtige Authentifizierungsszenarien

* Neben der nativen Azure Active Directory-Integrationsunterstützung für moderne Authentifizierungsprotokolle wie OpenID Connect, SAML und WS-Fed erweitert F5 den sicheren Zugriff für legacybasierte Authentifizierungs-Apps sowohl für den internen als auch für den externen Zugriff mit Azure AD, um für diese Anwendungen moderne Szenarien wie etwa Zugriff ohne Kennwort zu ermöglichen. Dies beinhaltet Folgendes:

* Apps mit headerbasierter Authentifizierung

* Apps mit Kerberos-Authentifizierung

* Apps mit anonymer Authentifizierung oder ohne integrierte Authentifizierung

* Apps mit NTLM-Authentifizierung (Schutz mit doppelten Eingabeaufforderungen für den Benutzer)

* Formularbasierte Anwendung (Schutz mit doppelten Eingabeaufforderungen für den Benutzer)

## <a name="adding-f5-from-the-gallery"></a>Hinzufügen von F5 aus dem Katalog

Zum Konfigurieren der Integration von F5 in Azure AD müssen Sie F5 aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
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

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **F5** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

1. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

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
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf F5 gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **F5** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

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

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](kerbf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung

### <a name="guided-configuration"></a>Interaktive Konfiguration

1. Melden Sie sich in einem neuen Webbrowserfenster bei der Unternehmenswebsite von F5 (headerbasiert) als Administrator an, und führen Sie die folgenden Schritte aus:

1. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus. Geben Sie unter **Certificate Name** (Zertifikatname) einen Zertifikatnamen an. (Auf diesen wird später in der Konfiguration verwiesen.) Wählen Sie unter **Certificate Source** (Zertifikatquelle) die Option „Upload File“ (Datei hochladen) aus, und geben Sie das Zertifikat an, das Sie beim Konfigurieren des einmaligen Anmeldens mit SAML heruntergeladen haben. Klicken Sie auf **Importieren**.

    ![Screenshot mit der SSL-Zertifikatliste, wo Sie den Namen (Certificate Name) und die Quelle des Zertifikats (Certificate Source) auswählen](./media/headerf5-tutorial/configure12.png)
 
1. Darüber hinaus benötigen Sie das SSL-Zertifikat für den Anwendungshostnamen. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus. Der **Importtyp** lautet **PKCS 12 (IIS)** . Geben Sie unter **Key Name** (Schlüsselname) einen Schlüsselnamen und anschließend die PFX-Datei an. (Auf den Schlüsselnamen wird später in der Konfiguration verwiesen.) Geben Sie das **Kennwort** für die PFX-Datei an. Klicken Sie auf **Importieren**.

    >[!NOTE]
    >Im vorliegenden Beispiel heißt unsere App `Headerapp.superdemo.live`, wir verwenden ein Platzhalterzertifikat, und der Schlüsselname lautet `WildCard-SuperDemo.live`.

    ![Screenshot der Seite „SSL Certificate/Key Source“](./media/headerf5-tutorial/configure13.png)

1. Wir verwenden die interaktive Umgebung, um den Azure AD-Verbund und den Anwendungszugriff einzurichten. Navigieren Sie in F5 BIG-IP unter **Main** (Hauptmenü) zu **Access > Guided Configuration > Federation > SAML Service Provider** (Zugriff > Interaktive Konfiguration > Verbund > SAML-Dienstanbieter). Klicken Sie auf **Next** (Weiter) und anschließend erneut auf **Next** (Weiter), um mit der Konfiguration zu beginnen.

    ![Screenshot der Seite „Guided Configuration“ mit ausgewählter Option „Federation“](./media/headerf5-tutorial/configure01.png)

    ![Screenshot der Seite „SAML Service Provider“](./media/headerf5-tutorial/configure02.png)
 
1. Geben Sie unter **Configuration Name** (Konfigurationsname) einen Namen für die Konfiguration an. Geben Sie unter **Entity ID** (Entitäts-ID) die Entitäts-ID an. Verwenden Sie dabei den Wert aus der Azure AD-Anwendungskonfiguration. Geben Sie unter **Host Name** (Hostname) den Hostnamen an. Fügen Sie unter **Description** (Beschreibung) eine Beschreibung als Referenz hinzu. Übernehmen Sie die restlichen Standardeinträge und -optionen, und klicken Sie anschließend auf **Save & Next** (Speichern und weiter).

    ![Screenshot der Seite „Service Provider Properties“](./media/headerf5-tutorial/configure03.png) 

1. In diesem Beispiel erstellen wir einen neuen virtuellen Server als 192.168.30.20 mit Port 443. Geben Sie unter **Destination Address** (Zieladresse) die IP-Adresse des virtuellen Servers an. Wählen Sie unter **Client SSL Profile** (Client-SSL-Profil) die Option „Create new“ (Neu erstellen) aus. Geben Sie das zuvor hochgeladene Anwendungszertifikat (in diesem Beispiel das Platzhalterzertifikat) sowie den zugehörigen Schlüssel an, und klicken Sie anschließend auf **Save & Next** (Speichern und weiter).

    >[!NOTE]
    >In diesem Beispiel wird unser interner Webserver am Port 888 ausgeführt, und wir möchten ihn mit dem Port 443 veröffentlichen.

    ![Screenshot der Seite „Virtual Server Properties“](./media/headerf5-tutorial/configure04.png) 

1. Geben Sie unter **Select method to configure your IdP connector** (Konfigurationsmethode für Ihren IdP-Connector auswählen) die Option „Metadata“ (Metadaten) an, klicken Sie auf „Choose File“ (Datei auswählen), und laden Sie die zuvor heruntergeladene Metadaten-XML-Datei aus Azure AD hoch. Geben Sie unter **Name** einen eindeutigen Namen für den SAML-IdP-Connector an. Wählen Sie unter **Metadata Signing Certificate** (Metadatensignaturzertifikat) das zuvor hochgeladene Metadatensignaturzertifikat aus. Klicken Sie auf **Save & Next** (Speichern und weiter).

    ![Screenshot der Seite „External Identity Provider Connector Settings“](./media/headerf5-tutorial/configure05.png)
 
1. Wählen Sie unter **Select a Pool** (Pool auswählen) entweder die Option **Create New** (Neu erstellen) oder einen bereits vorhandenen Pool aus. Lassen Sie den anderen Wert unverändert. Geben Sie unter **Pool Servers** (Poolserver) die IP-Adresse in das Feld für die IP-Adresse/den Knotennamen ein. Geben Sie den **Port** an. Klicken Sie auf **Save & Next** (Speichern und weiter).

    ![Screenshot der Seite „Pool Properties“](./media/headerf5-tutorial/configure06.png)

1. Aktivieren Sie im Bildschirm mit den Einstellungen für einmaliges Anmelden das Kontrollkästchen **Enable Single Sign-On** (Einmaliges Anmelden aktivieren). Wählen Sie unter „Selected Single Sign-On Type“ (Ausgewählter SSO-Typ) die Option **HTTP header-based** (HTTP-Header-basiert) aus. Ersetzen Sie **session.saml.last.Identity** unter „Username Source“ (Quelle des Benutzernamens) durch **session.saml.last.attr.name.Identity**. (Diese Variable wird unter Verwendung der Anspruchszuordnung in Azure AD festgelegt.) Geben Sie unter „SSO Headers“ (SSO-Header) Folgendes an:

    * **Header Name (Headername): MyAuthorization**

    * **Header Value (Headerwert): %{session.saml.last.attr.name.Identity}**

    * Klicken Sie auf **Save & Next** (Speichern und weiter).

    Eine vollständige Liste der Variablen und Werte finden Sie im Anhang. Bei Bedarf können weitere Header hinzugefügt werden.

    >[!NOTE]
    >Als Kontoname wird das erstellte F5-Delegierungskonto verwendet (siehe Dokumentation zu F5).

    ![Screenshot der Seite „Single Sign-On Settings“](./media/headerf5-tutorial/configure07.png) 

1. Der Einfachheit halber werden die Endpunktprüfungen in diesem Leitfaden übersprungen.  Details finden Sie in der F5-Dokumentation. Wählen Sie **Save & Next** (Speichern und weiter) aus.

    ![Screenshot der Seite „Endpoint Checks Properties“](./media/headerf5-tutorial/configure08.png)

1. Übernehmen Sie die Standardwerte, und klicken Sie auf **Save & Next** (Speichern und weiter). Ausführliche Informationen zu den Einstellungen für die SAML-Sitzungsverwaltung finden Sie in der Dokumentation zu F5.

    ![Screenshot der Seite „Timeout Settings“](./media/headerf5-tutorial/configure09.png)

1. Überprüfen Sie die Zusammenfassung, und wählen Sie **Deploy** (Bereitstellen) aus, um BIG-IP zu konfigurieren. Klicken Sie auf **Finish** (Fertig stellen).

    ![Screenshot der Seite „Your application is ready to be deployed“](./media/headerf5-tutorial/configure10.png)

    ![Screenshot der Seite „Your application is deployed“](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

Verwenden Sie diesen Abschnitt, wenn Sie die interaktive Konfiguration nicht verwenden können oder weitere Parameter hinzufügen/ändern möchten. Sie benötigen das TLS-/SSL-Zertifikat für den Anwendungshostnamen.

1. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus. Der **Importtyp** lautet **PKCS 12 (IIS)** . Geben Sie unter **Key Name** (Schlüsselname) einen Schlüsselnamen und anschließend die PFX-Datei an. (Auf den Schlüsselnamen wird später in der Konfiguration verwiesen.) Geben Sie das **Kennwort** für die PFX-Datei an. Klicken Sie auf **Importieren**.

    >[!NOTE]
    >Im vorliegenden Beispiel heißt unsere App `Headerapp.superdemo.live`, wir verwenden ein Platzhalterzertifikat, und der Schlüsselname lautet `WildCard-SuperDemo.live`.
  
    ![Screenshot der Seite „SSL Certificate/Key Source“ für eine erweiterte Konfiguration](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Hinzufügen eines neuen Webservers zu F5 BIG-IP

1. Klicken Sie auf **Main > IApps > Application Services > Application > Create** (Hauptmenü > IApps > Anwendungsdienste > Anwendung > Erstellen).

1. Geben Sie unter **Name** den Namen an, und wählen Sie unter **Template** (Vorlage) die Option **f5.http** aus.
 
    ![Screenshot der Seite „Application Services“ zeigt die Anwendungsdienste Seite mit Vorlagenauswahl (Template Selection)](./media/headerf5-tutorial/configure18.png)

1. Unsere App „HeaderApp2“ wird in diesem Fall extern als HTTPS veröffentlicht. Für **how should the BIG-IP system handle SSL Traffic?** (Wie soll SSL-Datenverkehr vom BIG-IP-System behandelt werden?) geben wir Folgendes an: **Terminate SSL from Client, Plaintext to servers (SSL Offload)** (SSL vom Client beenden, Klartext an Server (SSL-Abladung)). Geben Sie unter **Welches SSL-Zertifikat möchten Sie verwenden?** Ihr Zertifikat und unter **Welchen privaten SSL-Schlüssel möchten Sie verwenden?** Ihren Schlüssel an. Geben Sie unter **What IP Address do you want to use for the Virtual Server?** (Welche IP-Adresse möchten Sie für den virtuellen Server verwenden?) die IP-Adresse des virtuellen Servers an. 

    * **Geben Sie weitere Details an:**

        * FQDN  

        * Geben Sie einen vorhandenen App-Pool an, oder erstellen Sie einen neuen.

        * Geben Sie bei Erstellung eines neuen App-Servers die **interne IP-Adresse** und die **Portnummer** an.

        ![Screenshot des Bereichs, in dem Sie diese Details angeben können](./media/headerf5-tutorial/configure19.png) 

1. Klicken Sie auf **Finished** (Fertig).

    ![Screenshot der Seite nach Abschluss](./media/headerf5-tutorial/configure20.png) 

1. Vergewissern Sie sich, dass die App-Eigenschaften geändert werden können. Klicken Sie auf **Main > IApps > Application Services: Applications > HeaderApp2** (Hauptmenü > IApps > Anwendungsdienste: Anwendungen > HeaderApp2). Deaktivieren Sie das Kontrollkästchen **Strict Updates** (Strenge Aktualisierungen). (Wir ändern eine Einstellung außerhalb der grafischen Benutzeroberfläche.) Klicken Sie auf die Schaltfläche **Update** (Aktualisieren).

    ![Screenshot der Seite „Application Services“ mit ausgewählter Registerkarte „Properties“](./media/headerf5-tutorial/configure21.png) 

1. Nun sollten Sie den virtuellen Server durchsuchen können.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Konfigurieren von F5 als SP und Azure als IdP

1.  Klicken Sie auf **Access > Federation > SAML Service Provider > Local SP Service** (Zugriff > Verbund > SAML-Dienstanbieter > Lokaler SP-Dienst) und anschließend auf „Create“ (Erstellen) oder auf das Pluszeichen.

    ![Screenshot der Seite „About this BIG-IP“ ](./media/headerf5-tutorial/configure22.png)

1. Geben Sie Details für den Dienstanbieterdienst an. Geben Sie unter **Name** einen Namen für die F5-SP-Konfiguration an. Geben Sie unter **Entity ID** (Entitäts-ID) die Entitäts-ID an (entspricht in der Regel der Anwendungs-URL).

    ![Screenshot der Seite „SAML Service Provider“ mit dem Dialogfeld „Create New SAML SP Service“](./media/headerf5-tutorial/configure23.png)

    ![Screenshot des Dialogfelds „Create New SAML SP Service“ mit ausgewählter Option „Endpoint Settings“](./media/headerf5-tutorial/configure24.png)

    ![Screenshot des Dialogfelds „Create New SAML SP Service“ mit ausgewählter Option „Security Settings“](./media/headerf5-tutorial/configure25.png)

    ![Screenshot des Dialogfelds „Create New SAML SP Service“ mit ausgewählter Option „Authentication Context“](./media/headerf5-tutorial/configure26.png)

    ![Screenshot des Dialogfelds „Create New SAML SP Service“ mit ausgewählter Option „Requested Attributes“](./media/headerf5-tutorial/configure27.png)

    ![Screenshot des Dialogfelds „Edit SAML SP Service“ mit ausgewählter Option „Advanced Settings“](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Erstellen des IdP-Connectors

1. Klicken Sie auf die Schaltfläche **Bind/Unbind IdP Connectors** (IdP-Connectors binden/Bindung der IdP-Connectors aufheben), wählen Sie **Create New IdP Connector** (Neuen IdP-Connector erstellen) und **From Metadata** (Auf der Grundlage von Metadaten) aus, und gehen Sie anschließend wie folgt vor:
 
    ![Screenshot des Dialogfelds „Edit SAML IdPs that use this SP“ mit ausgewähltem Listenfeld „Create New IdP Connector“](./media/headerf5-tutorial/configure29.png)

    a. Navigieren Sie zur Datei **metadata.xml** , die aus Azure AD heruntergeladen wurde, und geben Sie einen Namen für den Identitätsanbieter an.

    b. Klicken Sie auf **OK**.

    c. Der Connector wird erstellt, und das Zertifikat wird automatisch auf der Grundlage der XML-Metadatendatei vorbereitet.
    
    ![Screenshot des Dialogfelds „Create New SAML IdP Connector“](./media/headerf5-tutorial/configure30.png)

    d. Konfigurieren Sie F5 BIG-IP so, dass alle Anforderungen an Azure AD gesendet werden.

    e. Klicken Sie auf **Add New Row** (Neue Zeile hinzufügen), wählen Sie **AzureIDP** (in den vorherigen Schritten erstellt) aus, und geben Sie Folgendes an: 

    f. **Matching Source (Abgleichsquelle): %{session.server.landinguri}** 

    g. **Matching Value (Abgleichswert): /** _

    h. Klicken Sie auf _ *Update** (Aktualisieren).

    i. Klicken Sie auf **OK**

    j. **Die SAML-IdP-Einrichtung ist abgeschlossen.**
    
    ![Screenshot des Dialogfelds „Edit SAML IdPs that use this SP“](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Konfigurieren der F5-Richtlinie, um Benutzer an den Azure-SAML-IdP umzuleiten

1. Gehen Sie wie folgt vor, um die F5-Richtlinie so zu konfigurieren, dass Benutzer an den Azure-SAML-IdP umgeleitet werden:

    a. Klicken Sie auf **Main > Access > Profile/Policies > Access Profiles** (Hauptmenü > Zugriff > Profil/Richtlinien > Zugriffsprofile).

    b. Klicken Sie auf die Schaltfläche **Erstellen**.

    ![Screenshot der Seite „Access Profiles“](./media/headerf5-tutorial/configure32.png)
 
    c. Geben Sie unter **Name** einen Namen an („HeaderAppAzureSAMLPolicy“ in diesem Beispiel).

    d. Bei Bedarf können weitere Einstellungen angepasst werden. Entsprechende Informationen finden Sie in der F5-Dokumentation.

    ![Screenshot der Seite „General Properties“](./media/headerf5-tutorial/configure33.png)

    ![Fortsetzung des Screenshots der Seite „General Properties“](./media/headerf5-tutorial/configure34.png) 

    e. Klicken Sie auf **Finished** (Fertig).

    f. Klicken Sie nach Abschluss der Richtlinienerstellung auf die Richtlinie, und navigieren Sie zur Registerkarte **Access Policy** (Zugriffsrichtlinie).

    ![Screenshot der Registerkarte „Access Policy“ mit „General Properties“](./media/headerf5-tutorial/configure35.png)
 
    g. Klicken Sie unter **Visual Policy Editor** (Visueller Richtlinien-Editor) auf den Link **Edit Access Policy for Profile** (Zugriffsrichtlinie für das Profil bearbeiten).

    h. Klicken Sie im visuellen Richtlinien-Editor auf das Pluszeichen, und wählen Sie **SAML Auth** (SAML-Authentifizierung) aus.

    ![Screenshot mit einer Zugriffsrichtlinie](./media/headerf5-tutorial/configure36.png)

    ![Screenshot eines Dialogfelds für Suchvorgänge mit ausgewählter Schaltfläche „SAML Auth“](./media/headerf5-tutorial/configure37.png)
 
    i. Klicken Sie auf **Add Item** (Element hinzufügen).

    j. Geben Sie unter **Properties** (Eigenschaften) unter **Name** einen Namen an, wählen Sie unter **AAA Server** (AAA-Server) den zuvor konfigurierten SP aus, und klicken Sie auf **Save** (Speichern).
 
    ![Screenshot mit den Eigenschaften des Elements, einschließlich dessen „AAA Server“](./media/headerf5-tutorial/configure38.png)

    k. Die grundlegende Richtlinie ist bereit. Sie können die Richtlinie anpassen, um zusätzliche Quellen/Attributspeicher zu integrieren.

    ![Screenshot mit der angepassten Richtlinie](./media/headerf5-tutorial/configure39.png)
 
    l. Klicken Sie oben auf den Link **Apply Access Policy** (Zugriffsrichtlinie anwenden).

### <a name="apply-access-profile-to-the-virtual-server"></a>Anwenden des Zugriffsprofils auf den virtuellen Server

1. Weisen Sie das Zugriffsprofil dem virtuellen Server zu, damit F5 BIG-IP APM die Profileinstellungen auf eingehenden Datenverkehr anwenden und die zuvor definierte Zugriffsrichtlinie ausführen kann.

    a. Klicken Sie auf **Main** > **Local Traffic** > **Virtual Servers** (Hauptmenü > Lokaler Datenverkehr > Virtuelle Server).

    ![Screenshot der Seite „Virtual Server List“](./media/headerf5-tutorial/configure40.png)
 
    b. Klicken Sie auf den virtuellen Server, scrollen Sie in der Dropdownliste **Access Profile** (Zugriffsprofil) zum Abschnitt **Access Policy** (Zugriffsrichtlinie), und wählen Sie die erstellte SAML-Richtlinie aus („HeaderAppAzureSAMLPolicy“ in diesem Beispiel).

    c. Klicken Sie auf **Update** (Aktualisieren).
 
    ![Screenshot des Bereichs „Access Policy“](./media/headerf5-tutorial/configure41.png)

    d. Erstellen Sie eine F5 BIG-IP iRule®, um die benutzerdefinierten SAML-Attribute aus der eingehenden Assertion zu extrahieren und als HTTP-Header an die Back-End-Testanwendung zu übergeben. Klicken Sie auf **Main > Local Traffic > iRules > iRule List > Create** (Hauptmenü > Lokaler Datenverkehr > iRules > iRule-Liste > Erstellen).

    ![Screenshot mit der iRule-Liste (iRule List) für den lokalen Datenverkehr (Local Traffic)](./media/headerf5-tutorial/configure42.png)
 
    e. Fügen Sie den folgenden F5 BIG-IP iRule-Text in das Definitionsfenster ein:

    ![Screenshot der Seite „New iRule“](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Beispielausgabe**

    ![Screenshot der Beispielausgabe](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Erstellen eines F5-Testbenutzers

In diesem Abschnitt erstellen Sie in F5 einen Benutzer namens B. Simon. Wenden Sie sich an das [Supportteam für den F5-Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45), um die Benutzer auf der F5-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „F5“ klicken, sollten Sie automatisch bei der F5-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [F5 mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](kerbf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](advance-kerbf5-tutorial.md)