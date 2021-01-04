---
title: Hinzufügen von Google als Identitätsanbieter für B2B – Azure AD
description: Erstellen eines Verbunds mit Google, um Gastbenutzern die Anmeldung bei Ihren Azure AD-Apps mit ihren eigenen Gmail-Konten zu ermöglichen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926234"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer

Durch die Einrichtung eines Verbunds mit Google können Sie eingeladenen Benutzern ermöglichen, sich bei Ihren freigegebenen Apps und Ressourcen mit ihren eigenen Gmail-Konten anzumelden, ohne dass sie Microsoft-Konten erstellen müssen. 

> [!NOTE]
> Der Google-Verbund wurde speziell für Gmail-Benutzer konzipiert. Um einen Verbund mit G Suite-Domänen einzurichten, verwenden Sie den [direkten Verbund](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Wie läuft der Vorgang für Google-Benutzer ab?
Wenn Sie eine Einladung an Google Gmail-Benutzer senden, sollten die Gastbenutzer auf Ihre freigegebenen Apps oder Ressourcen über einen Link zugreifen, der den Mandantenkontext enthält. Der Ablauf hängt davon ab, ob er bereits bei Google angemeldet ist:
  - Gastbenutzer, die nicht bei Google angemeldet sind, werden aufgefordert, sich anzumelden.
  - Gastbenutzer, die bereits bei Google angemeldet sind, werden aufgefordert, das zu verwendende Konto auszuwählen. Er muss das Konto auswählen, das Sie für die Einladung verwendet haben.

Gastbenutzer, bei denen ein Fehler aufgrund eines zu langen Headers angezeigt wird, können Cookies löschen oder ein privates oder Inkognito-Fenster öffnen und sich erneut anmelden.

![Screenshot der Google-Anmeldeseite](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Einschränkungen

Teams unterstützt Google-Gastbenutzer vollständig auf allen Geräten. Google-Benutzer können sich über einen allgemeinen Endpunkt wie `https://teams.microsoft.com` bei Teams anmelden.

Die allgemeinen Endpunkte anderer Anwendungen unterstützen Google-Benutzer möglicherweise nicht. Google-Gastbenutzer müssen sich über einen Link anmelden, der Mandanteninformationen enthält. Hier einige Beispiele:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Wenn Google-Gastbenutzer versuchen, einen Link wie `https://myapps.microsoft.com` oder `https://portal.azure.com` zu verwenden, erhalten sie eine Fehlermeldung.

Sie können Google-Gastbenutzern auch einen direkten Link zu einer Anwendung oder Ressource zur Verfügung stellen, sofern dieser Link Ihre Mandanteninformationen enthält. Beispiel: `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Schritt 1: Konfigurieren eines Google-Entwicklerprojekts
Erstellen Sie zunächst ein neues Projekt in der Google Developers Console, um eine Client-ID und einen geheimen Clientschlüssel abzurufen. Beide fügen Sie später in Azure Active Directory (Azure AD) hinzu. 
1. Wechseln Sie zur Google-APIs unter https://console.developers.google.com, und melden Sie sich mit Ihrem Google-Konto an. Es wird empfohlen, ein freigegebenes Google-Teamkonto zu verwenden.
2. Stimmen Sie den Vertragsbedingungen zu, wenn Sie dazu aufgefordert werden.
3. Erstellen eines neuen Projekts: Wählen Sie im Dashboard die Option **Projekt erstellen** aus, geben Sie dem Projekt einen Namen (z. B. **Azure AD B2B** ), und wählen Sie dann **Erstellen** aus: 
   
   ![Screenshot der Seite „Neues Projekt“](media/google-federation/google-new-project.png)

4. Wählen Sie auf der Seite **APIs & Dienste** unter dem neuen Projekt die Option **Anzeigen** aus.

5. Wählen Sie auf der Karte „APIs“die Option **Go to APIs overview** (Zur API-Übersicht wechseln) aus. Wählen Sie **OAuth-Zustimmungsbildschirm** aus.

6. Wählen Sie **Extern** und anschließend **Erstellen** aus. 

7. Geben Sie im **OAuth-Einwilligungsbildschirm** einen **Anwendungsnamen** ein:

   ![Screenshot des Google-OAuth-Einwilligungsbildschirms](media/google-federation/google-oauth-consent-screen.png)

8. Scrollen Sie zum Abschnitt **Authorized domains** (Autorisierte Domänen), und geben Sie **microsoftonline.com** ein:

   ![Screenshot des Abschnitts „Authorized domains“ (Autorisierte Domänen)](media/google-federation/google-oauth-authorized-domains.PNG)

9. Wählen Sie **Speichern** aus.

10. Wählen Sie **Credentials** aus. Wählen Sie im Menü **Anmeldedaten erstellen** die Option **OAuth-Client-ID** aus:

    ![Screenshot des Google APIs-Menüs „Anmeldedaten erstellen“](media/google-federation/google-api-credentials.png)

11. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus. Geben Sie der Anwendung einen geeigneten Namen, z. B. **Azure AD B2B**. Geben Sie unter **Autorisierte Weiterleitungs-URIs** die folgenden URIs ein:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(`<tenant ID>` ist Ihre Mandanten-ID.)
   
    > [!NOTE]
    > Ihre Mandanten-ID finden Sie im [Azure-Portal](https://portal.azure.com). Wählen Sie dort unter **Azure Active Directory** die Option **Eigenschaften** aus, und kopieren Sie die **Mandanten-ID**.

    ![Screenshot des Abschnitts „Autorisierte Umleitungs-URIs“](media/google-federation/google-create-oauth-client-id.png)

12. Klicken Sie auf **Erstellen**. Kopieren Sie die Client-ID und den geheimen Clientschlüssel. Sie verwenden diese Informationen, wenn Sie den Identitätsanbieter im Azure-Portal hinzufügen.

    ![Screenshot von OAuth-Client-ID und geheimem Clientschlüssel](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Schritt 2: Konfigurieren des Google-Verbunds in Azure AD 
Sie legen nun die Google-Client-ID und den geheimen Clientschlüssel fest. Dazu können Sie das Azure-Portal oder PowerShell verwenden. Testen Sie unbedingt die Konfiguration des Google-Verbunds, indem Sie sich selbst einladen. Verwenden Sie eine Gmail-Adresse, und versuchen Sie, die Einladung mit Ihrem eingeladenen Google-Konto einzulösen. 

**So konfigurieren Sie den Google-Verbund im Azure-Portal** 
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** und dann die Schaltfläche **Google** aus.
4. Geben Sie die Client-ID und den geheimen Clientschlüssel ein, die Sie zuvor erhalten haben. Wählen Sie **Speichern** aus: 

   ![Screenshot der Seite „Google als Identitätsanbieter hinzufügen“](media/google-federation/google-identity-provider.png)

**So konfigurieren Sie den Google-Verbund mithilfe von PowerShell**
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie den folgenden Befehl aus: `Connect-AzureAD`
3. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.  
4. Führen Sie den folgenden Befehl aus: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Verwenden Sie die Client-ID und den geheimen Clientschlüssel der App, die Sie hier erstellt haben: „Schritt 1: Konfigurieren eines Google-Entwicklerprojekts“. Weitere Informationen finden Sie unter [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Wie entferne ich einen Google Verbund?
Sie können Ihre Google-Verbundeinrichtung löschen. Wenn Sie dies tun, können sich Google-Gastbenutzer, die ihre Einladungen bereits eingelöst haben, nicht mehr anmelden. Sie können ihnen aber erneut Zugriff auf Ihre Ressourcen gewähren, indem Sie sie aus dem Verzeichnis löschen und erneut einladen. 
 
**So löschen Sie den Google-Verbund im Azure AD-Portal**
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** aus.
4. Wählen Sie in der Zeile **Google** die Schaltfläche mit den Auslassungspunkten ( **...** ) und dann **Löschen** aus. 
   
   ![Screenshot der Schaltfläche „Löschen“ für den Identitätsanbieter für soziale Netzwerke](media/google-federation/google-social-identity-providers.png)

1. Wählen Sie **Ja** aus, um den Löschvorgang zu bestätigen. 

**So löschen Sie den Google-Verbund mithilfe von PowerShell** 
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie `Connect-AzureAD` aus.  
4. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.  
5. Geben Sie den folgenden Befehl ein:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Weitere Informationen finden Sie unter [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
