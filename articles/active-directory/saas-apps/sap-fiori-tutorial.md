---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Fiori | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Fiori konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 35bb05eb019a1284a8a418df0197960130e8d013
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897295"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Fiori

In diesem Tutorial erfahren Sie, wie Sie SAP Fiori in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Fiori in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Fiori hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Fiori anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SAP Fiori-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Fiori unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Für die durch SAP Fiori initiierte iFrame-Authentifizierung wird die Verwendung des Parameters **IsPassive** in der SAML-Authentifizierungsanforderung (AuthnRequest) zur Authentifizierung im Hintergrund empfohlen. Weitere Informationen zum Parameter **IsPassive** finden Sie unter [SAML-Protokoll für einmaliges Anmelden](../develop/single-sign-on-saml-protocol.md).

## <a name="adding-sap-fiori-from-the-gallery"></a>Hinzufügen von SAP Fiori aus dem Katalog

Zum Konfigurieren der Integration von SAP Fiori in Azure AD müssen Sie SAP Fiori aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Fiori** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Fiori** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP Fiori

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Fiori mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Fiori eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP Fiori die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAP Fiori](#configure-sap-fiori-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP Fiori-Testbenutzers](#create-sap-fiori-test-user)** , um ein Pendant von B. Simon in SAP Fiori zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Melden Sie sich in einem neuen Webbrowserfenster bei der SAP Fiori-Unternehmenswebsite als Administrator an.

1. Stellen Sie sicher, dass **http** - und **https** -Dienste aktiv sind und dass die entsprechenden Ports der Transaktionscode- **SMICM** zugewiesen wurden.

1. Melden Sie sich beim SAP Business Client für SAP-System **T01** an, auf dem einmaliges Anmelden erforderlich ist. Aktivieren Sie dann HTTP Security Session Management.

    1. Wechseln Sie zum Transaktionscode **SICF_SESSIONS**. Es werden alle relevanten Profilparameter mit ihren aktuellen Werten angezeigt. Sie sehen wie im folgenden Beispiel aus:

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
        > Passen Sie die Parameter an Ihre Unternehmensanforderungen an. Die vorherigen Parameter dienen lediglich als Beispiel.

    1. Passen Sie ggf. die Parameter im (standardmäßigen) Instanzprofil des SAP-Systems an, und starten Sie das SAP-System neu.

    1. Doppelklicken Sie auf den entsprechenden Client, um eine HTTP-Sicherheitssitzung zu aktivieren.

        ![Seite mit aktuellen Werten der relevanten Profilparameter in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktivieren Sie die folgenden SICF-Dienste:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Wechseln Sie im Business Client for SAP-System [ **T01/122** ] zum Transaktionscode **SAML2**. Die Benutzeroberfläche für die Konfiguration wird in einem neuen Browserfenster geöffnet. In diesem Beispiel verwenden wir das Business Client for SAP-System 122.

    ![Anmeldeseite für SAP Fiori Business Client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, und wählen Sie dann **Log on** (Anmelden) aus.

    ![Seite mit der SAML 2.0-Konfiguration des ABAP-Systems T01/122 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Ersetzen Sie im Feld **Provider Name** (Anbietername) **T01122** durch **http:\//T01122** , und wählen Sie dann **Save** (Speichern) aus.

    > [!NOTE]
    > Der Anbietername ist standardmäßig im Format „\<sid>\<client>“ angegeben. Azure AD erwartet einen Namen im Format „\<protocol>://\<name>“. Es wird empfohlen, den Anbieternamen im Format „https\://\<sid>\<client>“ beizubehalten, damit Sie mehrere SAP Fiori ABAP-Engines in Azure AD konfigurieren können.

    ![Seite mit dem aktualisierten Anbieternamen in der SAML 2.0-Konfiguration des ABAP-Systems T01/122 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Wählen Sie die Registerkarte **Local Provider** > **Metadata** (Lokaler Anbieter > Metadaten) aus.

1. Laden Sie im Dialogfeld **SAML 2.0 Metadata** (SAML 2.0-Metadaten) die generierte XML-Metadatendatei herunter, und speichern Sie diese auf dem Computer.

    ![Link zum Herunterladen von Metadaten im Dialogfeld „SAML 2.0 Metadata“ (SAML 2.0-Metadaten) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Fiori** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo** , wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Bereich **Grundlegende SAML-Konfiguration** automatisch eingefügt. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: `https:\//\<your company instance of SAP Fiori\>`.

    > [!NOTE]
    > Einige Kunden berichten von Fehlern aufgrund falsch konfigurierter Werte unter **Antwort-URL**. Wenn Sie diesen Fehler erhalten, können Sie das folgende PowerShell-Skript verwenden, um die richtige Antwort-URL für Ihre Instanz festzulegen:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Sie können die `ServicePrincipal`-Objekt-ID selbst festlegen, bevor Sie das Skript ausführen, oder Sie können diese hier übergeben.

1. Die SAP Fiori-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Wählen Sie zum Verwalten dieser Attributwerte im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** aus.

    ![Bereich „Benutzerattribute“](common/edit-attribute.png)

1. Konfigurieren Sie im Bereich **Benutzerattribute und Ansprüche** die SAML-Tokenattribute, wie in der vorherigen Abbildung dargestellt. Führen Sie anschließend die folgenden Schritte aus:

    1. Wählen Sie **Bearbeiten** aus, um den Bereich **Benutzeransprüche verwalten** zu öffnen.

    1. Wählen Sie in der Liste **Transformation** den Eintrag **ExtractMailPrefix()** aus.

    1. Wählen Sie in der Liste **Parameter 1** die Option **user.userprincipalname** aus.

    1. Wählen Sie **Speichern** aus.

       ![Bereich „Benutzeransprüche verwalten“](./media/sapfiori-tutorial/nameidattribute.png)

       ![Abschnitt „Transformation“ im Bereich „Benutzeransprüche verwalten“](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **SAP Fiori einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP Fiori gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **SAP Fiori** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-fiori-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Fiori

1. Melden Sie sich beim SAP-System an, und wechseln Sie zum Transaktionscode **SAML2**. Ein neues Browserfenster mit der SAML-Konfigurationsseite wird geöffnet.

1. Wählen Sie zum Konfigurieren von Endpunkten für einen vertrauenswürdigen Identitätsanbieter (Azure AD) die Registerkarte **Trusted Providers** (Vertrauenswürdige Anbieter) aus.

    ![Registerkarte „Trusted Providers“ (Vertrauenswürdige Anbieter) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Wählen Sie **Add** (Hinzufügen) und dann im Kontextmenü **Upload Metadata File** (Metadatendatei hochladen) aus.

    ![Optionen „Add and Upload Metadata File“ (Metadatendatei hinzufügen und Metadatendatei hochladen) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Laden Sie die Metadatendatei hoch, die Sie im Azure-Portal heruntergeladen haben. Wählen Sie **Weiter** aus.

    ![Auswählen der Metadatendatei, die in SAP hochgeladen werden soll](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Geben Sie auf der nächsten Seite im Feld **Alias** den Aliasnamen ein, beispielsweise **aadsts**. Wählen Sie **Weiter** aus.

    ![Feld „Alias“ in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Stellen Sie sicher, dass der Wert im Feld **Digest Algorithm** (Digestalgorithmus) **SHA-256** lautet. Wählen Sie **Weiter** aus.

    ![Überprüfen des Werts für Digestalgorithmus in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Wählen Sie unter **Single Sign-On Endpoints** (Endpunkte für einmaliges Anmelden) **HTTP POST** und dann **Weiter** aus.

    ![Optionen für Endpunkte für einmaliges Anmelden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Wählen Sie unter **Single Logout Endpoints** (Endpunkte für einmaliges Abmelden) **HTTP Redirect** und dann **Weiter** aus.

    ![Optionen für Endpunkte für einmaliges Abmelden in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Wählen Sie unter **Artifact Endpoints** (Artefaktendpunkte) zum Fortfahren **Weiter** aus.

    ![Optionen für Artefaktendpunkte in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Wählen Sie unter **Authentication Requirements** (Authentifizierungsanforderungen) die Option **Fertig stellen** aus.

    ![Optionen „Authentication Requirements“ (Authentifizierungsanforderungen) und „Finish“ (Fertig stellen) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Wählen Sie (am unteren Seitenrand) **Trusted Provider** > **Identity Federation** (Vertrauenswürdiger Anbieter > Identitätsverbund) aus. Wählen Sie **Bearbeiten** aus.

    ![Registerkarten „Trusted Provider“ (Vertrauenswürdiger Anbieter) und „Identity Federation“ (Identitätsverbund) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Wählen Sie **Hinzufügen**.

    ![Option „Add“ (Hinzufügen) auf der Registerkarte „Identity Federation“ (Identitätsverbund)](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. Wählen Sie im Dialogfeld **Supported NameID Formats** (Unterstützte NameID-Formate) **Unspecified** (Nicht angegeben) aus. Klicken Sie auf **OK**.

    ![Dialogfeld „Supported NameID Formats“ (Unterstützte NameID-Formate) und Optionen in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Die Werte für **User ID Source** (Benutzer-ID-Quelle) und **User ID Mapping Mode** (Zuordnungsmodus für Benutzer-ID) bestimmen die Verknüpfung zwischen dem SAP-Benutzer und dem Azure AD-Anspruch.  

    **Szenario 1** : Zuordnung zwischen SAP-Benutzer und Azure AD-Benutzer

    1. Notieren Sie sich in SAP die Details unter **Details of NameID Format „Unspecified“** (Details des NameID-Formats „Nicht angegeben“):

        ![Screenshot: Dialogfeld „Details of NameID Format 'Unspecified'“ (Details des NameID-Formats 'Nicht angegeben“) in SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Notieren Sie sich im Azure-Portal unter **Benutzerattribute und Ansprüche** die erforderlichen Ansprüche in Azure AD.

        ![Screenshot: Dialogfeld „Benutzerattribute und Ansprüche“](./media/sapfiori-tutorial/claimsaad1.png)

    **Szenario 2** : Auswählen der SAP-Benutzer-ID basierend auf der konfigurierten E-Mail-Adresse in SU01 In diesem Fall sollte die E-Mail-ID in SU01 für jeden Benutzer konfiguriert werden, der SSO benötigt.

    1.  Notieren Sie sich in SAP die Details unter **Details of NameID Format „Unspecified“** (Details des NameID-Formats „Nicht angegeben“):

        ![Dialogfeld „Details of NameID Format ‚Unspecified‘“ (Details des NameID-Formats „Nicht angegeben“) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Notieren Sie sich im Azure-Portal unter **Benutzerattribute und Ansprüche** die erforderlichen Ansprüche in Azure AD.

       ![Dialogfeld „Benutzerattribute und Ansprüche“ im Azure-Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Wählen Sie **Save** (Speichern) und dann **Enable** (Aktivieren) aus, um den Identitätsanbieter zu aktivieren.

    ![Optionen „Save“ (Speichern) und „Enable“ (Aktivieren) in SAP](./media/sapfiori-tutorial/configuration1.png)

1. Wählen Sie **OK** aus, wenn Sie dazu aufgefordert werden.

    ![Option „OK“ im Dialogfeld „SAML 2.0 Configuration“ (SAML 2.0-Konfiguration) in SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Erstellen eines SAP Fiori-Testbenutzers

In diesem Abschnitt erstellen Sie in SAP Fiori eine Benutzerin namens Britta Simon. Arbeiten Sie mit Ihrem SAP-Expertenteam vor Ort oder mit dem SAP-Partner Ihres Unternehmens zusammen, um die Benutzerin auf der SAP Fiori-Plattform hinzuzufügen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

1. Nachdem der Identitätsanbieter Azure AD in SAP Fiori aktiviert wurde, versuchen Sie, auf eine der folgenden URLs zuzugreifen, um das einmalige Anmelden zu testen (es sollte kein Benutzername und Kennwort angefordert werden):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Ersetzen Sie *sapurl* durch den tatsächlichen SAP-Hostnamen.

1. Über die Test-URL sollte die folgende Test-Anwendungsseite in SAP aufgerufen werden. Wenn die Seite geöffnet wird, wurde das einmalige Anmelden von Azure AD erfolgreich eingerichtet.

    ![Standardmäßige Test-Anwendungsseite in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Wenn ein Benutzername und ein Kennwort angefordert werden, aktivieren Sie die Ablaufverfolgung, um die Problemdiagnose zu unterstützen. Verwenden Sie für die Ablaufverfolgung folgende URL: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [SAP Fiori mit Azure AD ausprobieren](https://aad.portal.azure.com/)