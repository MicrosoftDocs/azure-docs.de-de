---
title: 'Tutorial: Azure Active Directory-Integration in lokales SharePoint | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und die lokale SharePoint-Instanz konfigurieren.
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
ms.openlocfilehash: a693b22c609829f3bf6e76637eac5793d73703e6
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862308"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit SharePoint (lokal)

In diesem Tutorial erfahren Sie, wie Sie die lokale SharePoint-Instanz in Azure Active Directory (Azure AD) integrieren. Die Integration von SharePoint (lokal) in Azure AD ermöglicht Folgendes:

* Steuern Sie, wer Zugriff auf SharePoint (lokal) hat, in Azure AD.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei SharePoint (lokal) anzumelden.
* Verwalten Sie Ihre Konten im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in die lokale SharePoint-Instanz konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Eine SharePoint 2013-Farm (oder neuer).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden (SSO) von Azure AD in einer Testumgebung. Benutzer in Azure AD können auf Ihre lokale SharePoint-Instanz zugreifen.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Erstellen von Unternehmensanwendungen im Azure-Portal

Zum Konfigurieren der Integration der lokalen SharePoint-Instanz in Azure AD müssen Sie die lokale Instanz aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

So fügen Sie die lokale SharePoint-Instanz aus dem Katalog hinzu

1. Wählen Sie im ganz linken Bereich des Azure-Portals die Option **Azure Active Directory** aus.

   > [!NOTE]
   > Wenn das Element nicht verfügbar ist, können Sie es auch über den Link **Alle Dienste** oben im ganz linken Bereich öffnen. In der folgenden Übersicht befindet sich der Link **Azure Active Directory** im Abschnitt **Identität**. Er kann auch über das Filterfeld gesucht werden.

1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen**.

1. Wählen Sie oben im Dialogfeld **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

1. Geben Sie in das Suchfeld **SharePoint lokal** ein. Wählen Sie im Ergebnisbereich **SharePoint (lokal)** aus.

    <kbd>![SharePoint (lokal) in der Ergebnisliste](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Geben Sie einen Namen für Ihre lokale SharePoint-Instanz an, und wählen Sie **Hinzufügen** aus, um die Anwendung hinzuzufügen.

1. Wählen Sie in der neuen Unternehmensanwendung **Eigenschaften** aus, und überprüfen Sie den Wert für **Benutzerzuweisung erforderlich?** .

   <kbd>![Umschaltfläche „Benutzerzuweisung erforderlich?“](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   In diesem Szenario ist der Wert auf **Nein** festgelegt.

## <a name="configure-and-test-azure-ad"></a>Konfigurieren und Testen von Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden (SSO) von Azure AD mit SharePoint (lokal). Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SharePoint (lokal) eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SharePoint (lokal) die folgenden Schritte aus:

- [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
- [Konfigurieren von SharePoint (lokal)](#configure-sharepoint-on-premises), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
- [Erstellen eines Azure AD-Testbenutzers im Azure-Portal](#create-an-azure-ad-test-user-in-the-azure-portal), um in Azure AD einen neuen Benutzer für einmaliges Anmelden zu erstellen.
- [Erstellen einer Azure AD-Sicherheitsgruppe im Azure-Portal](#create-an-azure-ad-security-group-in-the-azure-portal), um in Azure AD eine neue Sicherheitsgruppe für einmaliges Anmelden zu erstellen.
- [Erteilen von Berechtigungen für ein Azure AD-Konto in SharePoint (lokal)](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises), um einem Azure AD-Benutzer Berechtigungen zu erteilen.
- [Erteilen von Berechtigungen für eine Azure AD-Gruppe in SharePoint (lokal)](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises), um einer Azure AD-Gruppe Berechtigungen zu erteilen.
- [Gewähren des Zugriffs auf ein Gastkonto für SharePoint (lokal) im Azure-Portal](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal), um SharePoint (lokal) Berechtigungen für ein Gastkonto in Azure AD zu erteilen.
- [Konfigurieren des vertrauenswürdigen Identitätsanbieters für mehrere Webanwendungen](#configure-the-trusted-identity-provider-for-multiple-web-applications), um denselben vertrauenswürdigen Identitätsanbieter für mehrere Webanwendungen zu verwenden.

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

So konfigurieren Sie das einmalige Anmelden (SSO) mit SharePoint (lokal)

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Unternehmens-Apps** aus. Wählen Sie den Namen der zuvor erstellten Unternehmensanwendung aus und dann **Einmaliges Anmelden**.

1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML** aus, um einmaliges Anmelden zu aktivieren.
 
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für die lokale SharePoint-Instanz](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `urn:<sharepointFarmName>:<federationName>`.

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<YourSharePointSiteURL>/_trust/`.

    1. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: `https://<YourSharePointSiteURL>/`.
    1. Wählen Sie **Speichern** aus.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um das entsprechende **Zertifikat (Base64)** auf Basis Ihrer Anforderungen aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. Kopieren Sie im Abschnitt **SharePoint (lokal) einrichten** die entsprechenden URLs auf Basis Ihrer Anforderung:
    
    - **Anmelde-URL**
    
        Kopieren Sie die Anmelde-URL, und ersetzen Sie **/saml2** am Ende durch **/wsfed**, damit es wie https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed aussieht. (Diese URL ist nicht genau.)

    - **Azure AD-Bezeichner**
    - **Abmelde-URL**

    > [!NOTE]
    > Diese URL kann nicht ohne Änderungen in SharePoint verwendet werden. Sie müssen **/saml2** durch **/wsfed** ersetzen. Die lokale SharePoint-Anwendung verwendet SAML 1.1-Token, sodass Azure AD eine WS-Fed-Anforderung vom SharePoint-Server erwartet. Nach der Authentifizierung gibt er das SAML 1.1-Token aus.

### <a name="configure-sharepoint-on-premises"></a>Konfigurieren von SharePoint (lokal)

1. Erstellen Sie einen neuen vertrauenswürdigen Identitätsanbieter in SharePoint Server 2016.

    Melden Sie sich beim SharePoint-Server an, und öffnen Sie die SharePoint-Verwaltungsshell. Geben Sie die folgenden Werte ein:
    - **$realm** ist der Bezeichnerwert aus dem Abschnitt mit der Domäne und den URLs für SharePoint (lokal) im Azure-Portal.
    - **$wsfedurl** ist die SSO-Dienst-URL.
    - **$filepath** ist der Dateipfad, unter dem Sie die Zertifikatdatei aus dem Azure-Portal heruntergeladen haben.

    Führen Sie die folgenden Befehle aus, um einen neuen vertrauenswürdigen Identitätsanbieter zu konfigurieren.

    > [!TIP]
    > Unter [SharePoint PowerShell](/powershell/sharepoint/overview) finden Sie eine Übersicht über PowerShell und die Funktionen.


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. Aktivieren Sie den vertrauenswürdigen Identitätsanbieter für Ihre Anwendung.

    1. Wechseln Sie in der **Zentraladministration** zu **Webanwendung verwalten**, und wählen Sie die Webanwendung aus, die Sie mit Azure AD schützen möchten.

    1. Wählen Sie im Menüband **Authentifizierungsanbieter** aus, und wählen Sie die Zone aus, die Sie verwenden möchten.

    1. Wählen Sie **Vertrauenswürdiger Identitätsanbieter** und den Identitätsanbieter aus, den Sie mit dem Namen *AzureAD* registriert haben.

    1. Klicken Sie auf **OK**.

    ![Konfigurieren des Authentifizierungsanbieters](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Erstellen eines Azure AD-Testbenutzers im Azure-Portal

In diesem Abschnitt wird ein Testbenutzer im Azure-Portal erstellt.

1. Wählen Sie im ganz linken Bereich des Azure-Portals die Option **Azure Active Directory** aus. Wählen Sie im Bereich **Verwalten** die Option **Benutzer** aus.

1. Wählen Sie oben im Bildschirm **Alle Benutzer** > **Neuer Benutzer** aus.

1. Wählen Sie **Benutzer erstellen** aus, und führen Sie in den Eigenschaften für Benutzer die folgenden Schritte aus. Unter Umständen können Sie Benutzer in Ihrer Azure AD-Instanz unter Verwendung des Mandantensuffixes oder einer überprüften Domäne erstellen. 

    1. Geben Sie im Feld **Name** den Benutzernamen ein. Wir haben **TestUser** verwendet.
  
    1. Geben Sie im Feld **Benutzername** Folgendes ein: `TestUser@yourcompanydomain.extension`. In diesem Beispiel wird `TestUser@contoso.com` angezeigt.

       ![Dialogfeld „Benutzer“](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

    1. Sie können die Website jetzt für TestUser@contoso.com freigeben und diesem Benutzer den Zugriff darauf erlauben.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Erstellen einer Azure AD-Sicherheitsgruppe im Azure-Portal

1. Klicken Sie auf **Azure Active Directory** > **Gruppen**.

1. Wählen Sie **Neue Gruppe** aus.

1. Füllen Sie die Felder **Gruppentyp**, **Gruppenname**, **Gruppenbeschreibung** und **Mitgliedschaftstyp** aus. Wählen Sie die Pfeile für die Mitgliederauswahl aus, und suchen Sie dann nach den Mitgliedern, die Sie der Gruppe hinzufügen möchten, oder wählen Sie sie aus. Wählen Sie **Auswählen** aus, um die ausgewählten Mitglieder hinzuzufügen, und wählen Sie dann **Erstellen** aus.

![Erstellen einer Azure AD-Sicherheitsgruppe](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Erteilen von Berechtigungen für ein Azure AD-Konto in SharePoint (lokal)

Um einem Azure AD-Benutzer in SharePoint (lokal) Zugriff zu gewähren, geben Sie die Websitesammlung frei, oder fügen Sie den Azure AD-Benutzer zu einer der Gruppen der Websitesammlung hinzu. Benutzer können sich jetzt mit Azure AD-Identitäten bei SharePoint 201x anmelden. Allerdings kann die Benutzerfreundlichkeit noch weiter verbessert werden. Beispielsweise liefert die Suche nach einem Benutzer mehrere Suchergebnisse in der Personenauswahl. Es gibt ein Suchergebnis für jeden der Anspruchstypen, die in der Anspruchszuordnung erstellt wurden. Um einen Benutzer mithilfe der Personenauswahl auszuwählen, geben Sie den genauen Benutzernamen ein, und wählen Sie das Anspruchsergebnis **Name** aus.

![Suchergebnisse für Ansprüche](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Die gesuchten Werte werden nicht überprüft, sodass möglicherweise Rechtschreibfehler auftreten oder Benutzer versehentlich den falschen Anspruchstyp zum Zuweisen auswählen. Dies kann Benutzer daran hindern, erfolgreich auf Ressourcen zuzugreifen.

Um dieses Szenario mit der Personenauswahl zu korrigieren, bietet eine Open-Source-Lösung namens [AzureCP](https://yvand.github.io/AzureCP/) einen benutzerdefinierten Anspruchsanbieter für SharePoint 2013, 2016 und 2019. Sie verwendet die Microsoft Graph-API, um die Eingaben der Benutzer aufzulösen und zu überprüfen. Weitere Informationen finden Sie unter [AzureCP](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Ohne AzureCP können Sie Gruppen hinzufügen, indem Sie die ID der Azure AD-Gruppe hinzufügen, aber diese Methode ist weder benutzerfreundlich noch zuverlässig. So sieht es aus:
  > 
  >![Hinzufügen einer Azure AD-Gruppe zu einer SharePoint-Gruppe nach ID](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Erteilen von Berechtigungen für eine Azure AD-Gruppe in SharePoint (lokal)

Um SharePoint (lokal) Azure AD-Sicherheitsgruppen hinzuzufügen, müssen Sie einen benutzerdefinierten Anspruchsanbieter für SharePoint Server verwenden. In diesem Beispiel wird AzureCP verwendet.

 > [!NOTE]
 > AzureCP ist kein Produkt von Microsoft, und Microsoft bietet keinen Support für das Produkt. Informationen, um AzureCP herunterzuladen und in der SharePoint-Farm (lokal) zu installieren und konfigurieren, finden Sie auf der [AzureCP](https://yvand.github.io/AzureCP/)-Website. 

1. Konfigurieren Sie AzureCP in der lokalen SharePoint-Farm, oder konfigurieren Sie eine andere benutzerdefinierte Anspruchsanbieterlösung. Informationen zum Konfigurieren von AzureCP finden Sie auf dieser [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html)-Website.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Unternehmens-Apps** aus. Wählen Sie den Namen der zuvor erstellten Unternehmensanwendung aus und dann **Einmaliges Anmelden**.

1. Bearbeiten Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** den Abschnitt **Benutzerattribute und Ansprüche**.

1. Wählen Sie **Gruppenanspruch hinzufügen** aus.

1. Wählen Sie aus, welche dem Benutzer zugeordneten Gruppen im Anspruch zurückgegeben werden sollen. Wählen Sie in diesem Fall **Alle Gruppen** aus. Wählen Sie im Abschnitt **Quellattribut** die Option **Gruppen-ID** aus, und wählen Sie **Speichern** aus.

Um einer Azure AD-Sicherheitsgruppe in SharePoint (lokal) Zugriff zu gewähren, geben Sie die Websitesammlung frei, oder fügen Sie die Azure AD-Sicherheitsgruppe zu einer der Gruppen der Websitesammlung hinzu.

1. Wechseln Sie zur **SharePoint-Websitesammlung**. Wählen Sie unter **Websiteeinstellungen** für die SharePoint-Websitesammlung **Personen und Gruppen** aus. 

1. Wählen Sie die SharePoint-Gruppe aus, und wählen Sie dann **Neu** > **Benutzer zu dieser Gruppe hinzufügen** aus. Wenn Sie den Namen der Gruppe eingeben, zeigt die Personenauswahl die Azure AD-Sicherheitsgruppe an.

    ![Hinzufügen einer Azure AD-Gruppe zu einer SharePoint-Gruppe](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Gewähren des Zugriffs auf ein Gastkonto für SharePoint (lokal) im Azure-Portal

Sie können Ihrer SharePoint-Website auf konsistente Weise Zugriff auf ein Gastkonto gewähren, weil der UPN jetzt geändert wird. Beispielsweise wird der Benutzer `jdoe@outlook.com` als `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` dargestellt. Um Ihre Website für externe Benutzer freizugeben, müssen Sie im Azure-Portal in Ihrem Abschnitt **Benutzerattribute und Ansprüche** einige Änderungen vornehmen.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Unternehmens-Apps** aus. Wählen Sie den Namen der zuvor erstellten Unternehmensanwendung aus und dann **Einmaliges Anmelden**.

1. Bearbeiten Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** den Abschnitt **Benutzerattribute und Ansprüche**.

1. Wählen Sie in der Zone **Erforderlicher Anspruch** die Option **Eindeutiger Benutzerbezeichner (Namens-ID)** aus.

1. Ändern Sie die Eigenschaft **Quellattribut** in den Wert **user.localuserprincipalname**, und wählen Sie **Speichern** aus.

    ![Anzeige von „Benutzerattribute und Ansprüche“ vor der Bearbeitung, Quellattribut](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Wechseln Sie mithilfe des Menübands zurück zu **SAML-basierte Anmeldung**. Der Abschnitt **Benutzerattribute und Ansprüche** sieht nun wie folgt aus: 

    ![Anzeige von „Benutzerattribute und Ansprüche“ nach der Bearbeitung](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > Ein Nachname und Vorname sind in diesem Setup nicht erforderlich.

1. Wählen Sie im ganz linken Bereich des Azure-Portals die Option **Azure Active Directory** und dann **Benutzer** aus.

1. Wählen Sie **Neuer Gastbenutzer** aus.

1. Wählen Sie die Option **Benutzer einladen** aus. Tragen Sie die Benutzereigenschaften ein, und wählen Sie **Einladen** aus.

1. Sie können die Website jetzt für MyGuestAccount@outlook.com freigeben und diesem Benutzer den Zugriff darauf erlauben.

    ![Freigeben einer Website für ein Gastkonto](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurieren des vertrauenswürdigen Identitätsanbieters für mehrere Webanwendungen

Die Konfiguration funktioniert für eine einzelne Webanwendung, benötigt jedoch weitere Konfiguration, wenn Sie beabsichtigen, denselben vertrauenswürdigen Identitätsanbieter für mehrere Webanwendungen zu verwenden. Angenommen, Sie hätten eine Webanwendung erweitert, um die URL `https://sales.contoso.com` zu verwenden, und Sie möchten nun die Benutzer für `https://marketing.contoso.com` authentifizieren. Aktualisieren Sie hierfür den Identitätsanbieter, sodass dieser den WReply-Parameter berücksichtigt. Aktualisieren Sie ferner die Anwendungsregistrierung in Azure AD, um eine Antwort-URL hinzuzufügen.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Unternehmens-Apps** aus. Wählen Sie den Namen der zuvor erstellten Unternehmensanwendung aus und dann **Einmaliges Anmelden**.

1. Bearbeiten Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** den Abschnitt **Grundlegende SAML-Konfiguration**.

    ![Grundlegende SAML-Konfiguration](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. Fügen Sie für **Antwort-URL (Assertionsverbraucherdienst-URL)** die URL für die zusätzlichen Webanwendungen hinzu, und wählen Sie **Speichern** aus.

    ![Bearbeiten der grundlegenden SAML-Konfiguration](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. Öffnen Sie auf dem SharePoint-Server die SharePoint 201x-Verwaltungsshell, und führen Sie die folgenden Befehle aus. Verwenden Sie den Namen des Ausstellers vertrauenswürdiger Identitätstoken, den Sie zuvor verwendet haben.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. Wechseln Sie in der **Zentraladministration** zu der Web-Anwendung, und aktivieren Sie den vorhandenen vertrauenswürdigen Identitätsanbieter.

Möglicherweise gibt es auch andere Szenarien, in denen Sie Zugriff auf Ihre lokale SharePoint-Instanz für Ihre internen Benutzer gewähren möchten. In diesem Szenario müssen Sie Microsoft Azure Active Directory Connect bereitstellen, um die Synchronisierung Ihrer lokalen Benutzer mit Azure AD zu ermöglichen. Dieses Setup wird in einem anderen Artikel behandelt.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SharePoint (lokal) können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
