---
title: 'Tutorial: Azure Active Directory-Integration in lokales SharePoint | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und die lokale SharePoint-Instanz konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 8e85f390ee5ff74f02cb95fa4dcf1dfc1a35dad1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699861"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Azure Active Directory-Integration in lokales SharePoint

In diesem Tutorial erfahren Sie, wie Sie die lokale SharePoint-Instanz in Azure Active Directory (Azure AD) integrieren.
Die Integration einer lokalen SharePoint-Instanz in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf die lokale SharePoint-Instanz hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SharePoint (lokal) anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in die lokale SharePoint-Instanz konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für SharePoint (lokal), für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SharePoint (lokal) unterstützt das **SP-initiierte** einmalige Anmelden.

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Hinzufügen der lokalen SharePoint-Instanz aus dem Katalog

Zum Konfigurieren der Integration der lokalen SharePoint-Instanz in Azure AD müssen Sie die lokale Instanz aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**So fügen Sie die lokale SharePoint-Instanz aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **lokales SharePoint** ein, wählen Sie im Ergebnisbereich **lokales SharePoint** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Lokales SharePoint in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei einer lokalen SharePoint-Instanz anhand einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SharePoint (lokal) eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit einer lokalen SharePoint-Instanz zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SharePoint (lokal)](#configure-sharepoint-on-premises-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen einer Azure AD-Sicherheitsgruppe im Azure-Portal](#create-an-azure-ad-security-group-in-the-azure-portal)** , um in Azure AD eine neue Sicherheitsgruppe für einmaliges Anmelden zu aktivieren.
5. **[Gewähren des Zugriffs auf die lokale SharePoint-Sicherheitsgruppe](#grant-access-to-sharepoint-on-premises-security-group)** , um einer bestimmten Gruppe Zugriff auf Azure AD zu gewähren.
6. **[Zuweisen der Azure AD-Sicherheitsgruppe im Azure-Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** , um die bestimmte Gruppe zu Authentifizierungszwecken Azure AD zuzuweisen.
7. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit SharePoint (lokal) die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SharePoint (lokal)** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für die lokale SharePoint-Instanz](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `urn:sharepoint:federation`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Supportteam der lokalen SharePoint-Instanz](https://support.office.com/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

    > [!Note]
    > Notieren Sie sich den Dateipfad, in den Sie die Zertifikatdatei heruntergeladen haben. Sie benötigen ihn später im PowerShell-Skript für die Konfiguration.

6. Kopieren Sie im Abschnitt **SharePoint (lokal) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen. Verwenden Sie für **Dienst-URL für einmalige Anmeldung** einen Wert im folgenden Format: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ ist die Mandanten-ID des Azure AD-Abonnements.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

    > [!NOTE]
    > Die lokale SharePoint-Anwendung verwendet SAML 1.1-Token, sodass Azure AD eine WS-Fed-Anforderung vom SharePoint-Server erwartet und nach der Authentifizierung SAML 1.1-Token ausstellt.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SharePoint (lokal)

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Unternehmenswebsite der lokalen SharePoint-Instanz als Administrator an.

2. **Konfigurieren Sie einen neuen vertrauenswürdigen Identitätsanbieter in SharePoint Server 2016.**

    Melden Sie sich in SharePoint Server 2016 an, und öffnen Sie die SharePoint 2016-Verwaltungsshell. Geben Sie die Werte für „$realm“ (Bezeichnerwert aus dem Abschnitt „Domäne und URLs für lokale SharePoint-Instanz“ im Azure-Portal), „$wsfedurl“ (Dienst-URL für einmaliges Anmelden) und „$filepath“ (Dateipfad, in den Sie die Zertifikatdatei heruntergeladen haben) aus dem Azure-Portal ein, und führen Sie die folgenden Befehle aus, um einen neuen vertrauenswürdigen Identitätsanbieter zu konfigurieren.

    > [!TIP]
    > Unter [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps) finden Sie eine Übersicht über PowerShell und die Funktionen.

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Führen Sie als Nächstes diese Schritte aus, um den vertrauenswürdigen Identitätsanbieter für Ihre Anwendung zu aktivieren:

    a. Navigieren Sie in der Zentraladministration zu **Webanwendung verwalten**, und wählen Sie die Webanwendung aus, die Sie mit Azure AD schützen möchten.

    b. Klicken Sie im Menüband auf **Authentifizierungsanbieter**, und wählen Sie die Zone aus, die Sie verwenden möchten.

    c. Wählen Sie **Vertrauenswürdiger Identitätsanbieter** und den Identitätsanbieter aus, den Sie mit dem Namen *AzureAD* registriert haben.

    d. Wählen Sie in der Anmeldeseite-URL-Einstellung **Benutzerdefinierte Anmeldeseite** aus, und geben Sie den Wert „/_trust/“ an.

    e. Klicken Sie auf **OK**.

    ![Konfigurieren des Authentifizierungsanbieters](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Einige externe Benutzer können diese SSO-Integration nicht nutzen, da ihr UPN einen beschädigten Wert wie `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com` enthält. Wir arbeiten bereits an der Möglichkeit, eine benutzertypabhängige Behandlung des UPN in der Kunden-App zu konfigurieren. Anschließend sollten alle Gastbenutzer SSO genauso problemlos wie Mitarbeiter der Organisation verwenden können.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Erstellen einer Azure AD-Sicherheitsgruppe im Azure-Portal

1. Klicken Sie auf **Azure Active Directory > Alle Gruppen**.

    ![Erstellen einer Azure AD-Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Klicken Sie auf **Neue Gruppe**:

    ![Erstellen einer Azure AD-Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Geben Sie **Gruppentyp**, **Gruppenname**, **Gruppenbeschreibung** und **Mitgliedschaftstyp** an. Klicken Sie auf den Pfeil, um Mitglieder auszuwählen, und suchen Sie nach dem Mitglied, das Sie der Gruppe hinzufügen möchten, oder klicken Sie auf das gewünschte Mitglied. Klicken Sie auf **Auswählen**, um die ausgewählten Mitglieder hinzuzufügen, und klicken Sie anschließend auf **Erstellen**.

    ![Erstellen einer Azure AD-Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Wenn Sie SharePoint lokal Azure Active Directory-Sicherheitsgruppen zuweisen möchten, müssen Sie [AzureCP](https://yvand.github.io/AzureCP/) in der lokalen SharePoint-Farm installieren und konfigurieren ODER einen alternativen benutzerdefinierten Anspruchsanbieter für SharePoint entwickeln und konfigurieren.  Für den Fall, dass Sie AzureCP nicht verwenden möchten, finden Sie am Ende dieses Dokuments weitere Informationen zum Erstellen eines eigenen benutzerdefinierten Anspruchsanbieters.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Gewähren des Zugriffs auf die lokale SharePoint-Sicherheitsgruppe

**Konfigurieren von Sicherheitsgruppen und Berechtigungen für die App-Registrierung**

1. Wählen Sie im Azure-Portal **Azure Active Directory** und anschließend **App-Registrierungen** aus.

    ![Blatt „Unternehmensanwendungen“](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Geben Sie **SharePoint lokal** in das Suchfeld ein, und wählen Sie den Eintrag aus.

    ![Lokales SharePoint in der Ergebnisliste](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Klicken Sie auf **Manifest**.

    ![Option „Manifest“](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Ändern Sie „`groupMembershipClaims`: `NULL`“ in „`groupMembershipClaims`: `SecurityGroup`“. Klicken Sie anschließend auf „Speichern“.

    ![Bearbeiten des Manifests](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Klicken Sie auf **Einstellungen** und anschließend auf **Erforderliche Berechtigungen**.

    ![Erforderliche Berechtigungen](./media/sharepoint-on-premises-tutorial/settings.png)

6. Klicken Sie auf **Hinzufügen** und anschließend auf **Hiermit wählen Sie eine API aus**.

    ![API-Zugriff](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Fügen Sie nacheinander **Windows Azure Active Directory** und **Microsoft Graph-API** hinzu.

    ![API-Auswahl](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Wählen Sie „Windows Azure Active Directory“ aus, aktivieren Sie das Kontrollkästchen „Verzeichnisdaten lesen“, und klicken Sie auf „Auswählen“. Navigieren Sie zurück, fügen Sie „Microsoft Graph“ hinzu, und aktivieren Sie erneut das Kontrollkästchen „Verzeichnisdaten lesen“.  Klicken Sie auf „Auswählen“ und anschließend auf „Fertig“.

    ![Aktivieren des Zugriffs](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Klicken Sie nun unter den erforderlichen Einstellungen auf **Berechtigungen erteilen** und anschließend auf „Ja“.

    ![Erteilen von Berechtigungen](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Überprüfen Sie anhand der Benachrichtigungen, ob die Berechtigungen erfolgreich erteilt wurden.  Falls nicht, funktioniert AzureCP nicht ordnungsgemäß, und SharePoint kann nicht lokal mit Azure Active Directory-Sicherheitsgruppen konfiguriert werden.

10. Konfigurieren Sie AzureCP in der lokalen SharePoint-Farm, oder konfigurieren Sie eine andere benutzerdefinierte Anspruchsanbieterlösung.  In diesem Beispiel wird AzureCP verwendet.

    > [!NOTE]
    > Hinweis: AzureCP ist kein Produkt von Microsoft, und Microsoft bietet keinen technischen Support für das Produkt. Laden Sie AzureCP unter https://yvand.github.io/AzureCP/ herunter, installieren Sie die Lösung in Ihrer lokalen SharePoint-Farm, und konfigurieren Sie sie entsprechend. 

11. **Gewähren des Zugriffs auf die Azure Active Directory-Sicherheitsgruppe in der lokalen SharePoint-Instanz:** Den Gruppen muss Zugriff auf die Anwendung in der lokalen SharePoint-Instanz gewährt werden.  Gehen Sie wie folgt vor, um die Berechtigungen für den Zugriff auf die Webanwendung festzulegen.

12. Klicken Sie in der Zentraladministration auf „Anwendungsverwaltung“ > „Webanwendungen verwalten“, wählen Sie die Webanwendung aus, um das Menüband zu aktivieren, und klicken Sie auf „Benutzerrichtlinie“.

    ![Zentraladministration](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Klicken Sie unter „Richtlinie für Webanwendung“ auf „Benutzer hinzufügen“, wählen Sie die Zone aus, und klicken Sie auf „Weiter“.  Klicken Sie auf das Adressbuch.

    ![Richtlinie für Webanwendung](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Suchen Sie nach der Azure Active Directory-Sicherheitsgruppe, fügen Sie sie hinzu, und klicken Sie auf „OK“.

    ![Hinzufügen der Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Wählen Sie die Berechtigungen aus, und klicken Sie auf „Fertig stellen“.

    ![Hinzufügen der Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Vergewissern Sie sich unter „Richtlinie für Webanwendung“, dass die Azure Active Directory-Gruppe hinzugefügt wurde.  Der Gruppenanspruch zeigt die ID des Azure Active Directory-Sicherheitsgruppenobjekts für den Benutzernamen an.

    ![Hinzufügen der Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Navigieren Sie zur SharePoint-Websitesammlung, und fügen Sie die Gruppe dort ebenfalls hinzu. Klicken Sie auf „Websiteeinstellungen“ > „Websiteberechtigungen“ > „Berechtigungen erteilen“.  Suchen Sie nach dem Gruppenrollenanspruch, weisen Sie die Berechtigungsstufe zu, und klicken Sie auf „Freigeben“.

    ![Hinzufügen der Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurieren eines vertrauenswürdigen Identitätsanbieters für mehrere Webanwendungen

Die Konfiguration funktioniert für eine einzelne Webanwendung, benötigt jedoch weitere Konfiguration, wenn Sie beabsichtigen, denselben vertrauenswürdigen Identitätsanbieter für mehrere Webanwendungen zu verwenden. Angenommen, eine Webanwendung wäre erweitert worden, um die URL `https://portal.contoso.local` zu verwenden. Nun möchten Sie die Benutzer für `https://sales.contoso.local` ebenfalls authentifizieren. Dazu müssen Sie den Identitätsanbieter aktualisieren, sodass dieser den WReply-Parameter berücksichtigt. Außerdem müssen Sie die Anwendungsregistrierung in Azure AD aktualisieren, um eine Antwort-URL hinzuzufügen.

1. Öffnen Sie im Azure-Portal das Azure AD-Verzeichnis. Klicken Sie auf **App-Registrierungen** und dann auf **Alle Anwendungen anzeigen**. Klicken Sie auf die Anwendung, die Sie zuvor erstellt haben (SharePoint-SAML-Integration).

2. Klicken Sie auf **Einstellungen**.

3. Klicken Sie auf dem Einstellungsblatt auf **Antwort-URLs**.

4. Fügen Sie die URL für die zusätzliche Webanwendung hinzu, die auf `/_trust/default.aspx` endet (z.B. `https://sales.contoso.local/_trust/default.aspx`), und klicken Sie auf **Speichern**.

5. Öffnen Sie auf dem SharePoint-Server die **SharePoint 2016-Verwaltungsshell**, und führen Sie die folgenden Befehle mithilfe des Namens des Tokenausstellers des vertrauenswürdige Identitätsanbieter aus, den Sie zuvor verwendet haben.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Navigieren Sie in der Zentraladministration zu der Web-Anwendung, und aktivieren Sie den vorhandenen vertrauenswürdigen Identitätsanbieter. Denken Sie daran, auch die URL der Anmeldeseite als eine benutzerdefinierte Anmeldeseite `/_trust/` zu konfigurieren.

7. Klicken Sie in der Zentraladministration auf die Webanwendung, und wählen Sie **User Policy** (Benutzerrichtlinie) aus. Fügen Sie einen Benutzer, wie zuvor in diesem Artikel gezeigt, mit den entsprechenden Berechtigungen hinzu.

### <a name="fixing-people-picker"></a>Verbesserte Personenauswahl

Benutzer können sich jetzt mit Azure AD-Identitäten bei SharePoint 2016 anmelden. Allerdings kann die Benutzerfreundlichkeit noch weiter verbessert werden. Beispielsweise liefert die Suche nach einem Benutzer mehrere Suchergebnisse in der Personenauswahl. Es gibt ein Suchergebnis für jeden der drei Anspruchstypen, der in der Anspruchszuordnung erstellt wurde. Um einen Benutzer mit der Personenauswahl auszuwählen, geben Sie seinen genauen Benutzernamen ein, und wählen Sie das Anspruchsergebnis **Name** aus.

![Suchergebnisse für Ansprüche](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Die gesuchten Werte werden nicht überprüft, sodass möglicherweise Rechtschreibfehler auftreten, oder Benutzer versehentlich den falschen Anspruchstyp zum Zuweisen auswählen, z.B. den Anspruch **SurName**. Folglich können Benutzer nicht auf Ressourcen zugreifen.

Dieses Problem lässt sich mit der Open-Source-Lösung [AzureCP](https://yvand.github.io/AzureCP/) beheben, die einen benutzerdefinierten Anspruchsanbieter für SharePoint 2016 bereitstellt. Azure AD Graph klärt und überprüft die Eingabe der Benutzer. Weitere Informationen finden Sie unter [ AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Zuweisen der Azure AD-Sicherheitsgruppe im Azure-Portal

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, **Alle Anwendungen** und dann **SharePoint (lokal)** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **SharePoint (lokal)** ein, und wählen Sie den Eintrag aus.

    ![Link für SharePoint (lokal) in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf **Benutzer hinzufügen**.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Suchen Sie nach der gewünschten Sicherheitsgruppe, und klicken Sie auf die Gruppe, um sie dem Abschnitt „Mitglieder auswählen“ hinzuzufügen. Klicken Sie auf **Auswählen** und anschließend auf **Zuweisen**.

    ![Suchen nach der Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Vergewissern Sie sich anhand der Benachrichtigungen auf der Menüleiste, dass die Gruppe erfolgreich der Enterprise-Anwendung im Azure-Portal zugewiesen wurde.

### <a name="create-sharepoint-on-premises-test-user"></a>Erstellen eines Testbenutzers für SharePoint (lokal)

In diesem Abschnitt erstellen Sie in SharePoint (lokal) einen Benutzer namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer auf der Plattform für SharePoint (lokal) ggf. vom  [Supportteam für SharePoint (lokal)](https://support.office.com/) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SharePoint (lokal)“ klicken, sollten Sie automatisch bei der Anwendung für SharePoint (lokal) angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
