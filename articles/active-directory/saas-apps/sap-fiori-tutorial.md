---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Fiori | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Fiori konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903945"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Tutorial: Azure Active Directory-Integration mit SAP Fiori

In diesem Tutorial erfahren Sie, wie Sie SAP Fiori in Azure Active Directory (Azure AD) integrieren.

Die Integration von SAP Fiori in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SAP Fiori hat.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei SAP Fiori anmelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in SAP Fiori konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein SAP Fiori-Abonnement, für das einmaliges Anmelden aktiviert ist.
* Dafür ist mindestens SAP Fiori 7.20 erforderlich.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren SAP Fiori in Azure AD.

SAP Fiori unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Hinzufügen von SAP Fiori im Azure-Portal

Damit Sie SAP Fiori in Azure AD integrieren können, müssen Sie SAP Fiori zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **SAP Fiori** ein. Wählen Sie in den Suchergebnissen **SAP Fiori** und dann **Hinzufügen** aus.

    ![SAP Fiori in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SAP Fiori mithilfe einer Testbenutzerin namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Fiori eingerichtet werden.

Sie müssen die folgenden Aufgaben ausführen, um das einmalige Anmelden von Azure AD bei SAP Fiori zu konfigurieren und zu testen:

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** | Ermöglicht Ihren Benutzern die Verwendung dieses Features. |
| **[Konfigurieren des einmaligen Anmeldens für SAP Fiori](#configure-sap-fiori-single-sign-on)** | Konfiguriert die Einstellungen für einmaliges Anmelden in der Anwendung. |
| **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** | Testet das einmalige Anmelden von Azure AD für eine Benutzerin namens Britta Simon. |
| **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** | Ermöglicht Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD. |
| **[Erstellen eines SAP Fiori-Testbenutzers](#create-an-sap-fiori-test-user)** | Erstellt eine Entsprechung von Britta Simon in SAP Fiori, die mit ihrer Darstellung in Azure AD verknüpft ist. |
| **[Testen des einmaligen Anmeldens](#test-single-sign-on)** | Überprüft, ob die Konfiguration funktioniert. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden von Azure AD bei SAP Fiori im Azure-Portal.

1. Melden Sie sich in einem neuen Webbrowserfenster bei der SAP Fiori-Unternehmenswebsite als Administrator an.

1. Stellen Sie sicher, dass **http**- und **https**-Dienste aktiv sind und dass die entsprechenden Ports der Transaktionscode-**SMICM** zugewiesen wurden.

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

1. Wechseln Sie im Business Client for SAP-System [**T01/122**] zum Transaktionscode **SAML2**. Die Benutzeroberfläche für die Konfiguration wird in einem neuen Browserfenster geöffnet. In diesem Beispiel verwenden wir das Business Client for SAP-System 122.

    ![Anmeldeseite für SAP Fiori Business Client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Geben Sie Ihren Benutzernamen und Ihr Kennwort ein, und wählen Sie dann **Log on** (Anmelden) aus.

    ![Seite mit der SAML 2.0-Konfiguration des ABAP-Systems T01/122 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. Ersetzen Sie im Feld **Provider Name** (Anbietername) **T01122** durch **http:\//T01122**, und wählen Sie dann **Save** (Speichern) aus.

    > [!NOTE]
    > Der Anbietername verfügt standardmäßig über das Format „\<SID>\<Client>“. Azure AD erwartet einen Namen im Format „\<Protokoll>://\<Name>“. Es wird empfohlen, den Anbieternamen im Format „https\://\<SID>\<Client>“ beizubehalten, damit Sie mehrere SAP Fiori ABAP-Engines in Azure AD konfigurieren können.

    ![Seite mit dem aktualisierten Anbieternamen in der SAML 2.0-Konfiguration des ABAP-Systems T01/122 in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Wählen Sie die Registerkarte **Local Provider** > **Metadata** (Lokaler Anbieter > Metadaten) aus.

1. Laden Sie im Dialogfeld **SAML 2.0 Metadata** (SAML 2.0-Metadaten) die generierte XML-Metadatendatei herunter, und speichern Sie diese auf dem Computer.

    ![Link zum Herunterladen von Metadaten im Dialogfeld „SAML 2.0 Metadata“ (SAML 2.0-Metadaten) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Fiori** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML** oder **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol) aus, um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Wählen Sie **Metadatendatei hochladen** aus.

        ![Option „Metadatendatei hochladen“](common/upload-metadata.png)

   1. Wählen Sie zum Auswählen der Metadatendatei das Ordnersymbol und dann **Hochladen** aus.

       ![Auswählen der Metadatendatei und der Schaltfläche „Hochladen“](common/browse-upload-metadata.png)

1. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Bereich **Grundlegende SAML-Konfiguration** automatisch eingefügt. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: https:\//\<Ihre Unternehmensinstanz von SAP Fiori\>

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Fiori](common/sp-identifier-reply.png)

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

    1. Wählen Sie in der Liste **Parameter 1** den Eintrag **user.userprinicipalname** aus.

    1. Wählen Sie **Speichern** aus.

       ![Bereich „Benutzeransprüche verwalten“](./media/sapfiori-tutorial/nameidattribute.png)

       ![Abschnitt „Transformation“ im Bereich „Benutzeransprüche verwalten“](./media/sapfiori-tutorial/nameidattribute1.png)


1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** neben **Verbundmetadaten-XML** aus. Wählen Sie eine Option zum Herunterladen entsprechend Ihren Anforderungen aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadoption für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **SAP Fiori einrichten** die folgenden URLs entsprechend Ihren Anforderungen:

    * Anmelde-URL
    * Azure AD-Bezeichner
    * Abmelde-URL

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAP Fiori

1. Melden Sie sich beim SAP-System an, und wechseln Sie zum Transaktionscode **SAML2**. Ein neues Browserfenster mit der SAML-Konfigurationsseite wird geöffnet.

1. Wählen Sie zum Konfigurieren von Endpunkten für einen vertrauenswürdigen Identitätsanbieter (Azure AD) die Registerkarte **Trusted Providers** (Vertrauenswürdige Anbieter) aus.

    ![Registerkarte „Trusted Providers“ (Vertrauenswürdige Anbieter) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Wählen Sie **Add** (Hinzufügen) und dann im Kontextmenü **Upload Metadata File** (Metadatendatei hochladen) aus.

    ![Optionen „Add and Upload Metadata File“ (Metadatendatei hinzufügen und Metadatendatei hochladen) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Laden Sie die Metadatendatei hoch, die Sie im Azure-Portal heruntergeladen haben. Klicken Sie auf **Weiter**.

    ![Auswählen der Metadatendatei, die in SAP hochgeladen werden soll](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Geben Sie auf der nächsten Seite im Feld **Alias** den Aliasnamen ein, beispielsweise **aadsts**. Klicken Sie auf **Weiter**.

    ![Feld „Alias“ in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Stellen Sie sicher, dass der Wert im Feld **Digest Algorithm** (Digestalgorithmus) **SHA-256** lautet. Klicken Sie auf **Weiter**.

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

    **Szenario 1**: Zuordnung zwischen SAP-Benutzer und Azure AD-Benutzer

    1. Notieren Sie sich in SAP die Details unter **Details of NameID Format „Unspecified“** (Details des NameID-Formats „Nicht angegeben“):

        ![Dialogfeld „Details of NameID Format ‚Unspecified‘“ (Details des NameID-Formats „Nicht angegeben“) in SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Notieren Sie sich im Azure-Portal unter **Benutzerattribute und Ansprüche** die erforderlichen Ansprüche in Azure AD.

        ![Dialogfeld „Benutzerattribute und Ansprüche“ im Azure-Portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Szenario 2**: Auswählen der SAP-Benutzer-ID basierend auf der konfigurierten E-Mail-Adresse in SU01 In diesem Fall sollte die E-Mail-ID in SU01 für jeden Benutzer konfiguriert werden, der SSO benötigt.

    1.  Notieren Sie sich in SAP die Details unter **Details of NameID Format „Unspecified“** (Details des NameID-Formats „Nicht angegeben“):

        ![Dialogfeld „Details of NameID Format ‚Unspecified‘“ (Details des NameID-Formats „Nicht angegeben“) in SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Notieren Sie sich im Azure-Portal unter **Benutzerattribute und Ansprüche** die erforderlichen Ansprüche in Azure AD.

       ![Dialogfeld „Benutzerattribute und Ansprüche“ im Azure-Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Wählen Sie **Save** (Speichern) und dann **Enable** (Aktivieren) aus, um den Identitätsanbieter zu aktivieren.

    ![Optionen „Save“ (Speichern) und „Enable“ (Aktivieren) in SAP](./media/sapfiori-tutorial/configuration1.png)

1. Wählen Sie **OK** aus, wenn Sie dazu aufgefordert werden.

    ![Option „OK“ im Dialogfeld „SAML 2.0 Configuration“ (SAML 2.0-Konfiguration) in SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie **Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge **brittasimon\@\<IhreUnternehmensdomäne>.\<Erweiterung** ein. Beispiel: **brittasimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

    ![Der Bereich „Benutzer“](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf SAP Fiori, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **SAP Fiori** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste den Eintrag **SAP Fiori** aus.

    ![SAP Fiori in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Wählen Sie **Auswählen**.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-an-sap-fiori-test-user"></a>Erstellen eines SAP Fiori-Testbenutzers

In diesem Abschnitt erstellen Sie in SAP Fiori eine Benutzerin namens Britta Simon. Arbeiten Sie mit Ihrem SAP-Expertenteam vor Ort oder mit dem SAP-Partner Ihres Unternehmens zusammen, um die Benutzerin auf der SAP Fiori-Plattform hinzuzufügen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

1. Nachdem der Identitätsanbieter Azure AD in SAP Fiori aktiviert wurde, versuchen Sie, auf eine der folgenden URLs zuzugreifen, um das einmalige Anmelden zu testen (es sollte kein Benutzername und Kennwort angefordert werden):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Ersetzen Sie *sapurl* durch den tatsächlichen SAP-Hostnamen.

1. Über die Test-URL sollte die folgende Test-Anwendungsseite in SAP aufgerufen werden. Wenn die Seite geöffnet wird, wurde das einmalige Anmelden von Azure AD erfolgreich eingerichtet.

    ![Standardmäßige Test-Anwendungsseite in SAP](./media/sapfiori-tutorial/testingsso.png)

1. Wenn ein Benutzername und ein Kennwort angefordert werden, aktivieren Sie die Ablaufverfolgung, um die Problemdiagnose zu unterstützen. Verwenden Sie für die Ablaufverfolgung folgende URL: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
