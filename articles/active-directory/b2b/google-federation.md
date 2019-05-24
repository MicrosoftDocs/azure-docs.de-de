---
title: Hinzufügen von Google als Identitätsanbieter für B2B – Azure Active Directory | Microsoft-Dokumentation
description: Erstellen eines Verbunds mit Google, um Gastbenutzern die Anmeldung bei Ihren Azure AD-Apps mit ihren eigenen Gmail-Konten zu ermöglichen
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94b35f0530dacfadb799f1d44d7a9eb666def6c9
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891938"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Hinzufügen von Google als Identitätsanbieter für B2B-Gastbenutzer

Durch die Einrichtung eines Verbunds mit Google können Sie eingeladenen Benutzern ermöglichen, sich bei Ihren freigegebenen Apps und Ressourcen mit ihren eigenen Google-Konten anzumelden, ohne dass sie Microsoft Accounts- (MSAs) oder Azure AD-Konten erstellen müssen.  
> [!NOTE]
> Ihre Google-Gastbenutzer müssen sich über einen Link anmelden, der den Mandantenkontext enthält (z.B. `https://myapps.microsoft.com/?tenantid=<tenant id>` oder `https://portal.azure.com/<tenant id>` bzw. `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` bei einer überprüften Domäne). Direkte Links zu Anwendungen und Ressourcen funktionieren ebenfalls, sofern sie den Mandantenkontext enthalten. Gastbenutzer können sich derzeit nicht über Endpunkte, die keinen Mandantenkontext aufweisen, anmelden. Die Verwendung beispielsweise von `https://myapps.microsoft.com`, `https://portal.azure.com` oder dem gemeinsamen Team-Endpunkt führt zu einem Fehler.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Wie läuft der Vorgang für Google-Benutzer ab?
Wenn Sie eine Einladung an einen Google Gmail-Benutzer senden, sollte der Gastbenutzer auf Ihre freigegebenen Apps oder Ressourcen über einen Link, der den Mandantenkontext enthält, zugreifen. Der Ablauf hängt davon ab, ob er bereits bei Google angemeldet ist:
  - Wenn der Gastbenutzer nicht bei Google angemeldet ist, wird er aufgefordert, sich bei Google anzumelden.
  - Wenn der Gastbenutzer bereits bei Google angemeldet ist, wird er aufgefordert, das Konto auszuwählen, das er verwenden möchte. Er muss das Konto auswählen, das Sie für die Einladung verwendet haben.

Wenn dem Gastbenutzer ein Fehler aufgrund eines zu langen Headers angezeigt wird, sollte er versuchen, seine Cookies zu löschen. Er kann auch ein privates oder Inkognitofenster öffnen und sich erneut anzumelden.

![Screenshot der Google-Anmeldeseite](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Schritt 1: Konfigurieren eines Google-Entwicklerprojekts
Erstellen Sie zunächst ein neues Projekt in der Google Developers Console, um eine Client-ID und einen geheimen Clientschlüssel abzurufen. Beides fügen Sie später in Azure AD hinzu. 
1. Wechseln Sie zur Google-APIs unter https://console.developers.google.com, und melden Sie sich mit Ihrem Google-Konto an. Es wird empfohlen, ein freigegebenes Google-Teamkonto zu verwenden.
2. Erstellen eines neuen Projekts: Wählen Sie auf dem Dashboard **Projekt erstellen** und dann **Erstellen** aus. Geben Sie auf der Seite „Neues Projekt“ einen **Projektnamen** ein, und wählen Sie dann **Erstellen** aus.
   
   ![Screenshot mit einer Seite „Neues Projekt“ für Google](media/google-federation/google-new-project.png)

3. Stellen Sie sicher, dass das neue Projekt im Menü „Projekt“ ausgewählt ist. Öffnen Sie dann das Menü links oben, und wählen Sie **APIs & Dienste** > **Zugangsdaten** aus.

   ![Screenshot der Option „Google-API-Anmeldeinformationen“](media/google-federation/google-api.png)
 
4. Wählen Sie die Registerkarte **OAuth-Zustimmungsbildschirm** aus, und geben Sie einen **Anwendungsnamen** ein. (Behalten Sie die restlichen Einstellungen bei.)

   ![Screenshot der Option „Google-OAuth-Einwilligungsbildschirm“](media/google-federation/google-oauth-consent-screen.png)

5. Scrollen Sie zum Abschnitt **Authorized domains** (Autorisierte Domänen), und geben Sie „microsoftonline.com“ ein.

   ![Screenshot des Abschnitts „Autorisierte Domänen“](media/google-federation/google-oauth-authorized-domains.png)

6. Wählen Sie **Speichern** aus.

7. Wählen Sie die Registerkarte **Anmeldedaten** aus. Wählen Sie im Menü **Anmeldedaten erstellen** die Option **OAuth-Client-ID** aus.

   ![Screenshot der Google-API-Option zum Erstellen von Anmeldeinformationen](media/google-federation/google-api-credentials.png)

8. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus, und geben Sie dann unter **Autorisierte Weiterleitungs-URIs** die folgenden URIs ein:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(Dabei ist `<directory id>` Ihre Verzeichnis-ID.)
   
     > [!NOTE]
     > Sie finden Ihre Verzeichnis-ID unter https://portal.azure.com. Wählen Sie dort unter **Azure Active Directory** die Option **Eigenschaften** aus, und kopieren Sie die **Verzeichnis-ID**.

   ![Screenshot des Abschnitts „Autorisierte Umleitungs-URIs“](media/google-federation/google-create-oauth-client-id.png)

9. Klicken Sie auf **Erstellen**. Kopieren Sie die Client-ID und den geheimen Clientschlüssel, die Sie verwenden möchten, um den Identitätsanbieter im Azure AD-Portal hinzuzufügen.

   ![Screenshot der OAuth-Client-ID und des Clientgeheimnisses](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Schritt 2: Konfigurieren des Google-Verbunds in Azure AD 
Nun legen Sie die Google-Client-ID und den geheimen Clientschlüssel entweder durch Eingabe im Azure AD-Portal oder mithilfe von PowerShell fest. Testen Sie unbedingt die Konfiguration des Google-Verbunds, indem Sie sich selbst unter Verwendung einer Gmail-Adresse einladen und versuchen, die Einladung mit dem eingeladenen Google-Konto einzulösen. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>So konfigurieren Sie den Google-Verbund im Azure AD-Portal 
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Organisationsbeziehungen** aus.
3. Wählen Sie **Identitätsanbieter** aus, und klicken Sie dann auf die Schaltfläche **Google**.
4. Geben Sie einen Namen ein. Geben Sie dann die Client-ID und den geheimen Clientschlüssel ein, die Sie zuvor erhalten haben. Wählen Sie **Speichern** aus. 

   ![Screenshot der Seite „Google als Identitätsanbieter hinzufügen“](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>So konfigurieren Sie den Google-Verbund mithilfe von PowerShell
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie den folgenden Befehl aus: `Connect-AzureAD`.
3. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.  
4. Führen Sie den folgenden Befehl aus: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Verwenden Sie die Client-ID und den geheimen Clientschlüssel der App, die Sie hier erstellt haben: „Schritt 1: Konfigurieren eines Google-Entwicklerprojekts“. Weitere Informationen finden Sie im Artikel zu [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Wie entferne ich einen Google Verbund?
Sie können Ihre Google-Verbundeinrichtung löschen. Wenn Sie dies tun, können Google-Gastbenutzer, die bereits seine Einladung eingelöst haben, sich nicht mehr anmelden. Sie können ihnen aber erneut Zugriff auf Ihre Ressourcen erteilen, indem Sie sie aus dem Verzeichnis löschen und erneut einladen. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>So löschen Sie den Google-Verbund im Azure AD-Portal 
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Organisationsbeziehungen** aus.
3. Wählen Sie **Identitätsanbieter** aus.
4. Wählen Sie in der Zeile **Google** das Kontextmenü (**...**) aus, und wählen Sie dann **Löschen** aus. 
   
   ![Screenshot der Option „Löschen“ für den Identitätsanbieter für soziale Netzwerke](media/google-federation/google-social-identity-providers.png)

1. Wählen Sie **Ja** aus, um den Löschvorgang zu bestätigen. 

### <a name="to-delete-google-federation-by-using-powershell"></a>So löschen Sie den Google-Verbund mithilfe von PowerShell 
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie `Connect-AzureAD`aus.  
4. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an.  
5. Geben Sie den folgenden Befehl ein:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Weitere Informationen finden Sie unter [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
