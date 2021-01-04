---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Oracle PeopleSoft – Protected by F5 BIG-IP APM | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Oracle PeopleSoft – Protected by F5 BIG-IP APM konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: jeedes
ms.openlocfilehash: 3b7c8e024ac8361c08cc41195531a114bb12fcb4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522290"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Oracle PeopleSoft – Protected by F5 BIG-IP APM

In diesem Tutorial wird beschrieben, wie Sie Oracle PeopleSoft – Protected by F5 BIG-IP APM mit Azure Active Directory (Azure AD) integrieren. Beim Integrieren von Oracle PeopleSoft – Protected by F5 BIG-IP APM mit Azure AD haben Sie die folgenden Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Oracle PeopleSoft – Protected by F5 BIG-IP APM hat.
* Ermöglichen Sie für Ihre Benutzer das automatische Anmelden bei Oracle PeopleSoft – Protected by F5 BIG-IP APM mit ihren Azure AD-Konten.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.
* In diesem Tutorial wird die Anleitung für Oracle PeopleSoft ELM beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

1. Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
1. Abonnement von Oracle PeopleSoft – Protected by F5 BIG-IP APM, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

1. Für die Bereitstellung der gemeinsamen Lösung wird folgende Lizenz benötigt:

    1. F5 BIG-IP®-Paket „Best“ (oder) 
    2. Eigenständige Lizenz für F5 BIG-IP Access Policy Manager™ (APM) 
    3. Add-On-Lizenz für F5 BIG-IP Access Policy Manager™ (APM) für eine bereits vorhandene Instanz von F5 BIG-IP® Local Traffic Manager™ (LTM)
    4. Zusätzlich zur obigen Lizenz kann das F5-System auch mit Folgendem lizenziert werden: 
        * Abonnement der URL-Filterung zur Verwendung der URL-Kategoriedatenbank 
        * F5 IP Intelligence-Abonnement zur Erkennung und Blockierung von bekannten Angreifern und schädlichem Datenverkehr 
        * Netzwerk-HSM (Hardwaresicherheitsmodul) zum Schutz und zur Verwaltung digitaler Schlüssel für eine sichere Authentifizierung
1. Das F5 BIG-IP-System wird mit APM-Modulen bereitgestellt. (LTM ist optional.) 
1. Es wird zwar dringend empfohlen, die F5-Systeme in einer [Synchronisierungs-/Failovergerätegruppe](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/big-ip-device-service-clustering-administration-14-1-0.html) (Sync/Failover Device Group, S/F DG), die das aktive Standbypaar enthält, mit einer Floating IP-Adresse für Hochverfügbarkeit (High Availability, HA) bereitzustellen, dies ist jedoch optional. Eine noch höhere Schnittstellenredundanz kann durch Verwendung des Link Aggregation Control-Protokolls (LACP) erzielt werden. LACP verwaltet die verbundenen physischen Schnittstellen als einzelne virtuelle Schnittstelle (Aggregatgruppe) und erkennt alle Schnittstellenfehler innerhalb der Gruppe.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Für Oracle PeopleSoft – Protected by F5 BIG-IP APM wird **SP- und IDP** -initiiertes einmaliges Anmelden (SSO) unterstützt.

## <a name="adding-oracle-peoplesoft---protected-by-f5-big-ip-apm-from-the-gallery"></a>Hinzufügen von Oracle PeopleSoft – Protected by F5 BIG-IP APM aus dem Katalog

Zum Konfigurieren der Integration von Oracle PeopleSoft – Protected by F5 BIG-IP APM in Azure AD müssen Sie Oracle PeopleSoft – Protected by F5 BIG-IP APM aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Oracle PeopleSoft – Protected by F5 BIG-IP APM** ein.
1. Wählen Sie im Ergebnisbereich die Option **Oracle PeopleSoft – Protected by F5 BIG-IP APM** aus, und fügen Sie anschließend die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-oracle-peoplesoft---protected-by-f5-big-ip-apm"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Oracle PeopleSoft – Protected by F5 BIG-IP APM

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Oracle PeopleSoft – Protected by F5 BIG-IP APM, indem Sie den Testbenutzer **B. Simon** verwenden. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Oracle PeopleSoft – Protected by F5 BIG-IP APM eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Oracle PeopleSoft – Protected by F5 BIG-IP APM zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Oracle PeopleSoft – Protected by F5 BIG-IP APM](#configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Oracle PeopleSoft – Protected by F5 BIG-IP APM-Testbenutzers](#create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user)** , um in Oracle PeopleSoft – Protected by F5 BIG-IP APM eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Oracle PeopleSoft – Protected by F5 BIG-IP APM** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<FQDN>.peoplesoft.f5.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/post/acs`

    c. Geben Sie im Textfeld **Abmelde-URL** eine URL im folgenden Format ein: `https://<FQDN>.peoplesoft.f5.com/saml/sp/profile/redirect/slr`.

1. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<FQDN>.peoplesoft.f5.com/`

    > [!NOTE]
    >Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit den tatsächlichen Werten für Anmelde-URL, Bezeichner, Antwort-URL und Abmelde-URL. Wenden Sie sich an das [Kundensupportteam von Oracle PeopleSoft – Protected by F5 BIG-IP APM](https://support.f5.com), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre Oracle PeopleSoft – Protected by F5 BIG-IP APM-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Oracle PeopleSoft – Protected by F5 BIG-IP APM-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name |  Quellattribut|
    | ------------------ | --------- |
    | EMPLID | user.employeeid |

1. Laden Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die **Verbundmetadaten-XML** -Datei und das **Zertifikat (Base64)** herunter, und speichern Sie diese Elemente auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/both-certificate.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Oracle PeopleSoft – Protected by F5 BIG-IP APM gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste mit den Anwendungen die Option **Oracle PeopleSoft – Protected by F5 BIG-IP APM** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-oracle-peoplesoft-protected-by-f5-big-ip-apm-sso"></a>Konfigurieren des einmaligen Anmeldens für Oracle PeopleSoft – Protected by F5 BIG-IP APM

### <a name="f5-saml-sp-configuration"></a>SAML-SP-Konfiguration für F5

Importieren Sie das Metadatenzertifikat in F5. Es wird später bei der Einrichtung benötigt. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Wählen Sie auf der rechten Seite **Import** (Importieren) aus.

![SAML-SP-Konfiguration für F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sp-configuration.png)

#### <a name="setup-the-saml-idp-connector"></a>Einrichten des SAML-IDP-Connectors 

1. Navigieren Sie zu **Access > Federation > SAML: Service Provider > External Idp Connectors** (Zugriff > Verbund > SAML-Dienstanbieter > Externe IdP-Connectors), und klicken Sie auf **Create > From Metadata** (Erstellen > Auf der Grundlage von Metadaten).

    ![SAML-IDP-Connector für F5](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/saml-idp-connector.png)

1. Klicken Sie auf der folgenden Seite auf **Browse** (Durchsuchen), um die XML-Datei hochzuladen.

1. Geben Sie im Textfeld **Identity Provider Name** (Identitätsanbietername) einen gültigen Namen ein, und klicken Sie anschließend auf **OK** .

    ![Neuer SAML-IDP-Connector](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-idp.png)

1. Führen Sie die erforderlichen Schritte auf der Registerkarte **Sicherheitseinstellungen** aus, und klicken Sie anschließend auf **OK** .

    ![Bearbeiten des SAML-IDP-Connectors](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp.png)

#### <a name="setup-the-saml-sp"></a>Einrichten des SAML-Dienstanbieters

1. Navigieren Sie zu **Access > Federation > SAML Service Provider > Local SP Services** (Zugriff > Verbund > SAML-Dienstanbieter > Lokale SP-Dienste), und klicken Sie auf **Create** (Erstellen). Geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **OK** :

    * Name: `<Name>`
    * Entity ID (Entitäts-ID): `https://<FQDN>.peoplesoft.f5.com`
    * SP Name Settings (SP-Namenseinstellungen)
        * Scheme (Schema): `https`
        * Host: `<FQDN>.peoplesoft.f5.com`
        * Description (Beschreibung): `<Description>`

    ![Neue SAML-SP-Dienste](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/new-saml-sp-service.png)

1. Wählen Sie die SP-Konfiguration „PeopleSoftAppSSO“ aus, und klicken Sie auf **Bind/Unbind IdP Connectors** (IdP-Connectors binden/Bindung der IdP-Connectors aufheben).
Klicken Sie auf **Add New Row** (Neue Zeile hinzufügen), wählen Sie den im vorherigen Schritt erstellten **externen IdP-Connector** aus, und klicken Sie auf **Update** (Aktualisieren) und anschließend auf **OK** .

    ![Erstellen von SAML-SP-Diensten](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/edit-saml-idp-use-sp.png)

## <a name="configuring-application"></a>Konfigurieren der Anwendung

### <a name="create-a-new-pool"></a>Erstellen eines neuen Pools
1. Navigieren Sie zu **Local Traffic > Pools > Pool List** (Lokaler Datenverkehr > Pools > Poolliste), klicken Sie auf **Create** (Erstellen), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig gestellt).

    * Name: `<Name>`
    * Description (Beschreibung): `<Description>`
    * Health Monitors (Systemüberwachungen): `http`
    * Address (Adresse): `<Address>`
    * Service Port (Dienstport): `<Service Port>`

    ![Erstellung eines neuen Pools](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/create-pool.png)


### <a name="create-a-new-client-ssl-profile"></a>Erstellen eines neuen Client-SSL-Profils

Navigieren Sie zu **Local Traffic > Profiles > SSL > Client > +** (Lokaler Datenverkehr > Profile > SSL > Client > +), geben Sie die folgenden Informationen ein, und klicken Sie auf **Finished** (Fertig gestellt).

* Name: `<Name>`
* Certificate (Zertifikat): `<Certificate>`
* Schlüssel: `<Key>`

![Neues Client-SSL-Profil](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/client-ssl-profile.png)

### <a name="create-a-new-virtual-server"></a>Erstellen eines neuen virtuellen Servers

1. Navigieren Sie zu **Local Traffic > Virtual Servers > Virtual Server List > +** (Lokaler Datenverkehr > Virtuelle Server > Liste der virtuellen Server > +), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig gestellt).
    * Name: `<Name>`
    * Destination Address/Mask (Zieladresse/Maske): `<Address>`
    * Service Port (Dienstport): Port 443 HTTPS
    * HTTP Profile (Client) (HTTP-Profil (Client)): http

    ![Erstellen eines neuen virtuellen Servers](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-list.png)

1. Geben Sie auf dieser Seite die folgenden Werte ein:

    * SSL Profile (Client) (SSL-Profil (Client)): `<SSL Profile>`
    * Source Address Translation (Quelladressenübersetzung): Auto Map (Automatische Zuordnung)
    * Access Profile (Zugriffsprofil): `<Access Profile>`
    * Default Pool (Standardpool): `<Pool>`


    ![Erstellen eines neuen PeopleSoft-Pools für virtuelle Server](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/virtual-server-people-soft.png)

## <a name="setting-up-peoplesoft-application-to-support-f5-big-ip-apm-as-the-single-sign-on-solution"></a>Einrichten der PeopleSoft-Anwendung zur Unterstützung von F5 Big-IP APM als Lösung für einmaliges Anmelden

>[!Note]
> Referenz: https://docs.oracle.com/cd/E12530_01/oam.1014/e10356/people.htm

1. Melden Sie sich bei der PeopleSoft-Konsole `https://<FQDN>.peoplesoft.f5.com/:8000/psp/ps/?cmd=start` mit den Administratoranmeldeinformationen an (Beispiel: PS/PS).

    ![Manager-Self-Service](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-console.png)

1. Erstellen Sie in der PeopleSoft-Anwendung **OAMPSFT** als neues Benutzerprofil, und ordnen Sie eine niedrige Sicherheitsrolle zu, z. B. **PeopleSoft User** (PeopleSoft-Benutzer).
Navigieren Sie zu **PeopleTools > Security > User Profiles > User Profiles** (PeopleTools > Sicherheit > Benutzerprofile > Benutzerprofile), um ein neues Benutzerprofil zu erstellen, z. B.: **OAMPSFT** . Fügen Sie anschließend **PeopleSoft User** (PeopleSoft-Benutzer) hinzu.

    ![PeopleSoft-Benutzer](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/user-profile.png)

1. Greifen Sie auf das Webprofil zu, und geben Sie **OAMPSFT** als **Benutzer-ID** für den öffentlichen Zugriff ein.

    ![Benutzerprofile](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/web-profile-configuration.png)

1. Öffnen Sie im **PeopleTools Application Designer** den Datensatz **FUNCLIB_LDAP** .

    ![Webprofilkonfiguration](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/funclib.png)

1. Aktualisieren Sie den Benutzerheader mit **PS_SSO_UID** für die Funktion **OAMSSO_AUTHENTICATION** .
Ersetzen Sie in der Funktion **getWWWAuthConfig()** den Wert, der der **&defaultUserId** zugewiesen ist, durch den **OAMPSFT** -Wert, den wir im Webprofil definiert haben. Speichern Sie die Definition des Datensatzes.

    ![OAMSSO_AUTHENTICATION](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/record.png)

1. Greifen Sie unter „PeopleTools > Security > Security Objects“ (PeopleTools > Sicherheit > Sicherheitsobjekte) auf die Seite **Signon PeopleCode** (PeopleCode-Anmeldung) zu, und aktivieren Sie die Funktion **OAMSSO_AUTHENTICATION** . Dies ist der PeopleCode für die Anmeldung, um einmaliges Anmelden mit Oracle Access Manager zu ermöglichen.

    ![OAMSSO_AUTHENTICATION ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-on-people-soft.png)

## <a name="setting-up-f5-big-ip-apm-to-populate-ps_sso_uid-http-header-with-the-peoplesoft-user-id"></a>Einrichten von F5 Big-IP APM zum Einfügen der PeopleSoft-Benutzer-ID in den HTTP-Header „PS_SSO_UID“

### <a name="configuring-per-request-policy"></a>Konfigurieren von Richtlinien pro Anforderung
1. Navigieren Sie zu **Access > Profile/Policies > Per-Request Policies** (Zugriff > Profil/Richtlinien > Richtlinien pro Anforderung), klicken Sie auf **Create** (Erstellen), geben Sie die folgenden Informationen ein, und klicken Sie auf **Finished** (Fertig gestellt).

    * Name: `<Name>`
    * Profile Type (Profiltyp): All
    * Languages (Sprachen): `<Language>`

    ![Konfigurieren von Richtlinien pro Anforderung ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/per-request.png)

1. Klicken Sie auf **Edit** Per-Request Policy `<Name>` (Bearbeiten > Richtlinie pro Anforderung).![SSO für PeopleSoft: Bearbeiten > Richtlinie pro Anforderung](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso.png)

    `Header Name: <Header Name>`   
    `Header Value: <Header Value>`

### <a name="assign-per-request-policy-to-the-virtual-server"></a>Zuweisen einer Richtlinie pro Anforderung zum virtuellen Server

Navigieren Sie zu **Local Traffic > Virtual Servers > Virtual Server List > PeopleSoftApp** (Lokaler Datenverkehr > Virtuelle Server > Liste der virtuellen Server > PeopleSoftApp). Geben Sie `<Name>` als Richtlinie pro Anforderung an.

![PeopleSoftSSO als Richtlinie pro Anforderung ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/people-soft-sso-1.png)

## <a name="setting-up-f5-big-ip-apm-to-support-single-logout-from-peoplesoft-application"></a>Einrichten von F5 Big-IP APM für die Unterstützung des einmaligen Abmeldens für die PeopleSoft-Anwendung

Führen Sie die folgenden Schritte aus, um die Unterstützung des einmaligen Abmeldens für alle PeopleSoft-Benutzer hinzuzufügen:

1. Ermitteln der richtigen Abmelde-URL für das PeopleSoft-Portal
    * Zum Ermitteln der Adresse, die von der PeopleSoft-Anwendung zum Beenden einer Benutzersitzung verwendet wird, müssen Sie das Portal in einem Webbrowser öffnen und die Debugtools des Browsers aktivieren. Dies ist unten im Beispiel dargestellt:

        ![Abmelde-URL für PeopleSoft-Portal ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/sign-out.png)

    * Suchen Sie nach dem Element mit der ID `PT_LOGOUT_MENU`, und speichern Sie den URL-Pfad mit den Abfrageparametern. In unserem Beispiel haben wir den folgenden Wert erhalten: `/psp/ps/?cmd=logout`

1. Erstellen Sie eine LTM iRule, mit der der Benutzer an die APM-Abmelde-URL umgeleitet wird: `/my.logout.php3`

    * Navigieren Sie zu **Local Traffic > iRule** (Lokaler Datenverkehr > iRule), klicken Sie auf **Create** (Erstellen), geben Sie die folgenden Informationen an, und klicken Sie anschließend auf **Finished** (Fertig gestellt).

        Name: `<Name>`  
        Definition:  
                    _when HTTP_REQUEST { switch -glob -- [HTTP::uri] { `/psp/ps/?cmd=logout` { HTTP::redirect `/my.logout.php3` } } }_

1. Zuweisen der erstellten iRule zum virtuellen Server

    * Navigieren Sie zu **Local Traffic > Virtual Servers > Virtual Server List > PeopleSoftApp > Resources** (Lokaler Datenverkehr > Virtuelle Server > Liste der virtuellen Server > PeopleSoftApp > Ressourcen). Klicken Sie auf die Schaltfläche **Manage…** (Verwalten…) :   

    * Geben Sie `<Name>` als aktivierte iRule an, und klicken Sie auf **Finished** (Fertig gestellt).

        ![_iRule_PeopleSoftApp ](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/irule-people-soft.png)

    * Geben Sie im Textfeld **Name** den Namen `<Name>` an. 

        ![_iRule_PeopleSoftApp: Fertig gestellt](./media/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial/common-irule.png)


### <a name="create-oracle-peoplesoft-protected-by-f5-big-ip-apm-test-user"></a>Erstellen eines Testbenutzers für Oracle PeopleSoft – Protected by F5 BIG-IP APM

In diesem Abschnitt erstellen Sie in Oracle PeopleSoft – Protected by F5 BIG-IP APM einen Benutzer mit dem Namen „B. Simon“. Arbeiten Sie mit dem [Supportteam von Oracle PeopleSoft – Protected by F5 BIG-IP APM](https://support.f5.com) zusammen, um die Benutzer der Plattform von Oracle PeopleSoft – Protected by F5 BIG-IP APM hinzufügen zu lassen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen** . Es erfolgt eine Umleitung an die Anmelde-URL von Oracle PeopleSoft – Protected by F5 BIG-IP APM, über die Sie die Anmeldung initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Oracle PeopleSoft – Protected by F5 BIG-IP APM, um den Anmeldeablauf zu initiieren.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen** . Hierdurch sollten Sie automatisch bei der Instanz von Oracle PeopleSoft – Protected by F5 BIG-IP APM angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Zugriffsbereich verwenden, um die Anwendung in einem beliebigen Modus zu testen. Sobald Sie im Zugriffsbereich auf die Kachel „Oracle PeopleSoft – Protected by F5 BIG-IP APM“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeablaufs zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, werden Sie automatisch bei der Instanz von Oracle PeopleSoft – Protected by F5 BIG-IP APM angemeldet, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Oracle PeopleSoft – Protected by F5 BIG-IP APM können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.