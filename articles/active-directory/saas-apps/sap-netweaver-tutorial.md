---
title: 'Tutorial: Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP NetWeaver | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP NetWeaver konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 688c496239becad7d1462f41afee9c6f88af9f95
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676688"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP NetWeaver

In diesem Tutorial erfahren Sie, wie Sie SAP NetWeaver in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP NetWeaver in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf SAP NetWeaver haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei SAP NetWeaver anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SSO-fähiges SAP NetWeaver-Abonnement
* Mindestens SAP NetWeaver V7.20

## <a name="scenario-description"></a>Beschreibung des Szenarios

* SAP NetWeaver unterstützt sowohl **SAML** ( **SP-initiiertes SSO** ) als auch **OAuth** . In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

> [!NOTE]
> Konfigurieren Sie die Anwendung je nach Ihren Organisationsanforderungen entweder in SAML oder in OAuth. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Hinzufügen von SAP NetWeaver aus dem Katalog

Zum Konfigurieren der Integration von SAP NetWeaver in Azure AD müssen Sie SAP NetWeaver aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP NetWeaver** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **SAP NetWeaver** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sap-netweaver"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP NetWeaver

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SAP NetWeaver mithilfe eines Testbenutzers namens **B.Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP NetWeaver eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP NetWeaver die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren von SAP NetWeaver unter Verwendung von SAML](#configure-sap-netweaver-using-saml)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP NetWeaver-Testbenutzers](#create-sap-netweaver-test-user)** , um in SAP NetWeaver eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert
1. **[Konfigurieren von SAP NetWeaver für OAuth](#configure-sap-netweaver-for-oauth)** , um die OAuth-Einstellungen auf der Anwendungsseite zu konfigurieren

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit SAP NetWeaver die folgenden Schritte aus:

1. Melden Sie sich in einem neuen Webbrowserfenster bei der SAP NetWeaver-Unternehmenswebsite als Administrator an.

1. Stellen Sie sicher, dass **http** - und **https** -Dienste aktiv sind und die entsprechenden Ports im T-Code **SMICM** zugewiesen wurden.

1. Melden Sie sich beim Unternehmensclient des SAP-Systems (T01) an, für den das einmalige Anmelden benötigt wird, und aktivieren Sie die Verwaltung der HTTP-Sicherheitssitzung.

    a. Navigieren Sie zum Transaktionscode **SICF_SESSIONS** . Hier werden alle relevanten Profilparameter mit ihren aktuellen Werten angezeigt. Sie sehen wie folgt aus:
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Passen Sie die obigen Parameter an die Anforderungen Ihrer Organisation an. Die gezeigten Parameter dienen lediglich als Beispiele.

    b. Passen Sie ggf. die Parameter in der Instanz/im Standardprofil des SAP-Systems an, und starten Sie das SAP-System neu.

    c. Doppelklicken Sie auf den entsprechenden Client, um die HTTP-Sicherheitssitzung zu aktivieren.

    ![Die HTTP-Sicherheitssitzung ](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivieren Sie die folgenden SICF-Dienste:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Wechseln Sie im Unternehmensclient des SAP-Systems [T01/122] zum Transaktionscode **SAML2** . Es wird eine Benutzeroberfläche in einem Browser geöffnet. In diesem Beispiel wird von 122 als SAP-Unternehmensclient ausgegangen.

    ![Transaktionscode](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, um die Benutzeroberfläche zu starten, und klicken Sie auf **Edit** (Bearbeiten).

    ![Benutzername und Kennwort](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Ersetzen Sie die Angabe „T01122“ im Feld **Provider Name** (Anbietername) durch `http://T01122`, und klicken Sie auf **Save** (Speichern).

    > [!NOTE]
    > In der Standardeinstellung hat der Anbietername das Format `<sid><client>`, Azure AD erwartet den Namen jedoch im Format `<protocol>://<name>`. Es wird empfohlen, den Anbieternamen als `https://<sid><client>` anzugeben, damit mehrere SAP NetWeaver ABAP-Engines in Azure AD konfiguriert werden können.

    ![Mehrere SAP NetWeaver ABAP-Engines](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generieren von Metadaten des Dienstanbieters:** Sobald Sie mit dem Konfigurieren der Einstellungen **Local Provider** (Lokaler Anbieter) und **Trusted Providers** (Vertrauenswürdige Anbieter) auf der SAML 2.0-Benutzeroberfläche fertig sind, generieren Sie im nächsten Schritt die Metadatendatei des Dienstanbieters (die alle Einstellungen, die Authentifizierungskontexte und andere Konfigurationen in SAP enthält). Nachdem diese Datei generiert wurde, müssen Sie sie in Azure AD hochladen.

    ![Generieren der Dienstanbietermetadaten](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Wechseln Sie zu **Local Provider** (Lokaler Anbieter).

    b. Klicken Sie auf **Metadata** (Metadaten).

    c. Speichern Sie die generierte **Metadaten-XML-Datei** auf Ihrem Computer, und laden Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** hoch, damit die Werte **Bezeichner** und **Antwort-URL** automatisch aufgefüllt werden.

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP NetWeaver** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Klicken Sie auf **Metadatendatei hochladen** , um die zuvor abgerufene **Metadatendatei des Dienstanbieters** hochzuladen.

    b. Klicken Sie auf das **Ordnerlogo** , wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen** .

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte **Bezeichner** und **Antwort-URL** im Abschnitt **Grundlegende SAML-Konfiguration** in den entsprechenden Textfeldern automatisch ausgefüllt (wie unten dargestellt):

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Einige Kunden haben einen Fehler im Zusammenhang mit einer falsch konfigurierten Antwort-URL für ihre Instanz gemeldet. Wenn Sie einen derartigen Fehler erhalten, können Sie als Problemumgehung das folgende PowerShell-Skript verwenden, um die richtige Antwort-URL für Ihre Instanz festzulegen:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > Sie müssen die Objekt-ID des Dienstprinzipals zuerst selbst festlegen, Sie können sie aber auch hier übergeben.

1. Die SAP NetWeaver-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten** , um das Dialogfeld „Benutzerattribute“ zu öffnen.

    ![Bearbeiten der Attribute](common/edit-attribute.png)

1. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    a. Klicken Sie auf das Symbol **Bearbeiten** , um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Symbol „Bearbeiten“](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Wählen Sie in der Liste **Transformation** die Option **ExtractMailPrefix()** aus.

    c. Wählen Sie in der Liste **Parameter 1** die Option **user.userprincipalname** aus.

    d. Klicken Sie auf **Speichern** .

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **SAP NetWeaver einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf SAP NetWeaver gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **SAP NetWeaver** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurieren von SAP NetWeaver unter Verwendung von SAML

1. Melden Sie sich beim SAP-System an, und navigieren Sie zum Transaktionscode „SAML2“. Damit öffnen Sie ein neues Browserfenster mit dem SAML-Konfigurationsbildschirm.

2. Wechseln Sie zum Konfigurieren von Endpunkten für vertrauenswürdige Identitätsanbieter Provider (Azure AD) zur Registerkarte **Trusted Providers** (Vertrauenswürdige Anbieter).

    ![Konfigurieren von vertrauenswürdigen Anbietern für einmaliges Anmelden](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Wählen Sie **Hinzufügen** und dann im Kontextmenü **Metadatendatei hochladen** aus.

    ![Konfigurieren von einmaligem Anmelden 2](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Laden Sie die Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    ![Konfigurieren von einmaligem Anmelden 3](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Geben Sie im nächsten Bildschirm den Aliasnamen ein. Geben Sie z. B. „aadsts“ ein, und wählen Sie **Next** (Weiter) aus, um den Vorgang fortzusetzen.

    ![Konfigurieren von einmaligem Anmelden 4](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Stellen Sie sicher, dass Ihr **Digest Algorithm** (Digest-Algorithmus) **SHA-256** lautet und keine Änderungen erfordert. Wählen Sie dann **Weiter** aus.

    ![Konfigurieren von einmaligem Anmelden 5](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Verwenden Sie für **Single Sign-On Endpoints** (Endpunkte für einmaliges Anmelden) die Option **HTTP POST** , und klicken Sie zum Fortfahren auf **Weiter** .

    ![Konfigurieren von einmaligem Anmelden 6](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Verwenden Sie für **Single Logout Endpoints** (Endpunkte für einmaliges Abmelden) die Option **HTTPRedirect** , und klicken Sie zum Fortfahren auf **Weiter** .

    ![Konfigurieren von einmaligem Anmelden 7](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Wählen Sie unter **Artifact Endpoints** (Artefaktendpunkte) zum Fortfahren **Weiter** aus.

    ![Konfigurieren von einmaligem Anmelden 8](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Klicken Sie unter **Authentication Requirements** (Authentifizierungsanforderungen) auf **Fertig stellen** .

    ![Konfigurieren von einmaligem Anmelden 9](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Wechseln Sie zur Registerkarte **Trusted Provider** > **Identity Federation** (Vertrauenswürdige Anbieter > Identitätsverbund) (unten auf dem Bildschirm). Klicken Sie auf **Bearbeiten** .

    ![Konfigurieren von einmaligem Anmelden 10](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klicken Sie auf der Registerkarte **Identity Federation** (Identitätsverbund) (im Fenster unten) auf **Add** (Hinzufügen).

    ![Konfigurieren von einmaligem Anmelden 11](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Wählen Sie im Popupfenster unter **Supported NameID formats** (Unterstützte Formate für die Namens-ID) die Option **Unspecified** (Nicht angegeben) aus, und klicken Sie auf „OK“.

    ![Konfigurieren von einmaligem Anmelden 12](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Beachten Sie, dass die Werte **user ID Source** (Benutzer-ID-Quelle) und **user ID mapping mode** (Zuordnungsmodus für Benutzer-ID) die Verbindung zwischen SAP-Benutzern und Azure AD-Ansprüchen festlegen.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Szenario: Zuordnung zwischen SAP-Benutzer und Azure AD-Benutzer.

    a. Screenshot von „NameID details“ (Details zur Namens-ID) in SAP

    ![Konfigurieren von einmaligem Anmelden 13](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Screenshot der erforderlichen Ansprüche aus Azure AD

    ![Konfigurieren von einmaligem Anmelden 14](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Szenario: Auswählen der SAP-Benutzer-ID basierend auf der konfigurierten E-Mail-Adresse in SU01. In diesem Fall sollte die E-Mail-ID in SU01 für jeden Benutzer konfiguriert werden, der SSO benötigt.

    a.  Screenshot von „NameID details“ (Details zur Namens-ID) in SAP

    ![Konfigurieren von einmaligem Anmelden 15](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Screenshot der erforderlichen Ansprüche aus Azure AD

    ![Konfigurieren von einmaligem Anmelden 16](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klicken Sie auf **Save** (Speichern) und dann auf **Enable** (Aktivieren), um den Identitätsanbieter zu aktivieren.

    ![Konfigurieren von einmaligem Anmelden 17](./media/sapnetweaver-tutorial/configuration1.png)

16. Klicken Sie auf **OK** , wenn Sie dazu aufgefordert werden.

    ![Konfigurieren von einmaligem Anmelden 18](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Erstellen eines SAP NetWeaver-Testbenutzers

    In diesem Abschnitt erstellen Sie in SAP NetWeaver einen Benutzer namens B.simon. Arbeiten Sie mit Ihrem SAP-Expertenteam vor Ort oder mit dem SAP-Partner Ihres Unternehmens zusammen, um die Benutzer auf der SAP NetWeaver-Plattform hinzuzufügen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

1. Nachdem der Identitätsanbieter Azure AD aktiviert wurde, versuchen Sie, auf die URL unten zuzugreifen, um das einmalige Anmelden zu testen (es wird kein Benutzername und kein Kennwort angefordert).

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (Oder:) Verwenden Sie die unten aufgeführte URL.

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Ersetzen Sie „sapurl“ durch den tatsächlichen SAP-Hostnamen.

2. Über die obige URL sollten Sie zum unten gezeigten Bildschirm gelangen. Wenn Sie die unten abgebildete Seite erreichen können, wurde die Einrichtung des einmaligen Anmeldens von Azure AD erfolgreich abgeschlossen.

    ![Testen des einmaligen Anmeldens](./media/sapnetweaver-tutorial/testingsso.png)

3. Wenn Sie zur Eingabe von Benutzername und Kennwort aufgefordert werden, führen Sie eine Diagnose des Problems durch. Aktivieren Sie dazu die Ablaufverfolgung mithilfe der folgenden URL.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurieren von SAP NetWeaver für OAuth

1. Den dokumentierten SAP-Prozess finden Sie hier: [NetWeaver Gateway Service Enabling and OAuth 2.0 Scope Creation](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation) (NetWeaver-Gatewaydienst: Aktivierung und OAuth 2.0-Bereichserstellung)

2. Navigieren Sie zu SPRO, und suchen Sie nach **Activate and Maintain services** (Dienste aktivieren und verwalten).

    ![Aktivieren und Verwalten von Diensten](./media/sapnetweaver-tutorial/oauth01.png)

3. In diesem Beispiel möchten wir mit OAuth eine Verbindung zwischen dem OData-Dienst (`DAAG_MNGGRP`) und dem einmaligen Anmelden von Azure AD herstellen. Verwenden Sie die technische Dienstnamensuche für den Dienst `DAAG_MNGGRP`, und aktivieren Sie ihn, falls er noch nicht aktiv ist (grüner Status (`green`) auf der Registerkarte mit den ICF-Knoten). Vergewissern Sie sich, dass der Systemalias (das verbundene Back-End-System, auf dem der Dienst tatsächlich ausgeführt wird) korrekt ist.

    ![OData-Dienst](./media/sapnetweaver-tutorial/oauth02.png)

    * Klicken Sie als Nächstes auf der oberen Symbolleiste auf **OAuth** , und weisen Sie `scope` zu. Behalten Sie dabei den angegebenen Standardnamen bei.

4. In unserem Beispiel wird der Bereich `DAAG_MNGGRP_001` verwendet. Er wird auf der Grundlage des Dienstnamens generiert, indem automatisch eine Zahl hinzugefügt wird. Der Bericht `/IWFND/R_OAUTH_SCOPES` kann verwendet werden, um den Namen des Bereichs zu ändern oder manuell zu erstellen.

    ![Konfigurieren von OAuth](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Die Meldung `soft state status is not supported` kann ignoriert werden, da dies kein Problem darstellt. Ausführlichere Informationen finden Sie [hier](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true).

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Erstellen eines Dienstbenutzers für den OAuth 2.0-Client

1. OAuth2 verwendet eine Dienst-ID (`service ID`), um das Zugriffstoken für den Endbenutzer in dessen Namen abzurufen. Wichtige OAuth-bedingte Einschränkung: Die OAuth 2.0-Client-ID (`OAuth 2.0 Client ID`) muss exakt dem Benutzernamen (`username`) entsprechen, den der OAuth 2.0-Client beim Anfordern eines Zugriffstokens für die Anmeldung verwendet. In unserem Beispiel registrieren wir einen OAuth 2.0-Client namens „CLIENT1“. Im SAP-System muss somit ein Benutzer gleichen Namens (CLIENT1) vorhanden sein, und diesen Benutzer konfigurieren wir für die Verwendung durch die referenzierte Anwendung. 

2. Bei der Registrierung eines OAuth-Clients verwenden wir den Gewährungstyp „SAML-Bearer“ (`SAML Bearer Grant type`).

    >[!NOTE]
    >Ausführlichere Informationen finden Sie [hier](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type).

3. T-Code: „SU01“: Erstellen Sie den Benutzer „CLIENT1“ als Systemtyp (`System type`), und weisen Sie ein Kennwort zu. Speichern Sie das Kennwort nach Bedarf, um die Anmeldeinformation an den API-Programmierer weiterzugeben, der sie mit dem Benutzernamen im aufrufenden Code zusammenführen muss. Es sollte weder ein Profil noch eine Rolle zugewiesen werden.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrieren der neuen OAuth 2.0-Client-ID mit dem Erstellungs-Assistenten

1. Starten Sie zum Registrieren eines neuen **OAuth 2.0-Clients** die Transaktion **SOAUTH2** . Die Transaktion zeigt eine Übersicht über die bereits registrierten OAuth 2.0-Clients an. Wählen Sie **Create** (Erstellen) aus, um den Assistenten für den neuen OAuth-Client mit namens „CLIENT1“ (in diesem Beispiel) zu starten.

2. Navigieren Sie zum T-Code **SOAUTH2** , geben Sie die Beschreibung ein, und klicken Sie auf **Next** (Weiter).

    ![SOAUTH2](./media/sapnetweaver-tutorial/oauth04.png)

    ![OAuth 2.0-Client-ID](./media/sapnetweaver-tutorial/oauth05.png)

3. Wählen Sie in der Dropdownliste das bereits hinzugefügte Element **SAML2 IdP – Azure AD** aus, und speichern Sie.

    ![SAML2 IdP – Azure AD 1](./media/sapnetweaver-tutorial/oauth06.png)

    ![SAML2 IdP – Azure AD 2](./media/sapnetweaver-tutorial/oauth07.png)

    ![SAML2 IdP – Azure AD 3](./media/sapnetweaver-tutorial/oauth08.png)

4. Klicken Sie in der Bereichszuweisung auf **Add** (Hinzufügen), um den zuvor erstellten Bereich hinzuzufügen: `DAAG_MNGGRP_001`

    ![`Scope`](./media/sapnetweaver-tutorial/oauth09.png)

    ![Bereichszuweisung](./media/sapnetweaver-tutorial/oauth10.png)

5. Klicken Sie auf **Finish** (Fertig stellen).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Azure AD SAP NetWeaver können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.