---
title: 'Schnellstart: Hinzufügen eines Gastbenutzers mit PowerShell – Azure Active Directory | Microsoft-Dokumentation'
description: In diesem Schnellstart erfahren Sie, wie Sie mit PowerShell eine Einladung an einen externen Benutzer für die Azure AD B2B-Zusammenarbeit senden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5325529acb66735db0c27c0f5bdfca819e8ec4d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812303"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Schnellstart: Hinzufügen von Gastbenutzern mit PowerShell

Es gibt viele Möglichkeiten, externe Partner zu Ihren Apps und Diensten für die Azure Active Directory B2B-Zusammenarbeit einzuladen. Im vorherigen Schnellstart haben Sie erfahren, wie Sie Gastbenutzer direkt im Azure Active Directory-Verwaltungsportal hinzufügen. Sie können auch PowerShell verwenden, um Gastbenutzer einzeln oder gruppenweise hinzuzufügen. In diesem Schnellstart fügen Sie mit dem Befehl „New-AzureADMSInvitation“ Ihrem Azure-Mandanten einen Gastbenutzer hinzu.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

### <a name="install-the-latest-azureadpreview-module"></a>Installieren des aktuellen AzureADPreview-Moduls
Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls (AzureADPreview). 

Überprüfen Sie zunächst, welche Module Sie installiert haben. Öffnen Sie dafür Windows PowerShell als Benutzer mit erhöhten Rechten („Als Administrator ausführen“), und führen Sie den folgenden Befehl aus:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Wenn das AzureADPreview-Modul keine Meldung anzeigt, dass eine neuere Version verfügbar ist, können Sie loslegen. Andernfalls müssen Sie je nach Ausgabe einen der folgenden Schritte ausführen:

- Wenn keine Ergebnisse zurückgegeben werden, führen Sie den folgenden Befehl aus, um das AzureADPreview-Modul zu installieren:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Wenn nur das AzureADPreview-Modul in den Ergebnissen angezeigt wird, führen Sie die folgenden Befehle aus, um das AzureADPreview-Modul zu installieren: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Wenn nur das AzureADPreview-Modul in den Ergebnissen angezeigt wird, eine Meldung Sie jedoch darauf hinweist, dass eine neuere Version verfügbar ist, führen Sie die folgenden Befehle aus, um das Modul zu aktualisieren: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Möglicherweise erscheint ein Hinweis, dass Sie das Modul aus einem nicht vertrauenswürdigen Repository installieren. Das passiert, wenn Sie das PSGallery-Repository nicht zuvor als vertrauenswürdiges Repository festgelegt haben. Drücken Sie **Y**, um das Modul zu installieren.

### <a name="get-a-test-email-account"></a>Einrichten eines Test-E-Mail-Kontos

Sie benötigen ein Test-E-Mail-Konto, an das Sie die Einladung senden können. Das Konto muss sich außerhalb Ihrer Organisation befinden. Sie können jede Art von Konto verwenden, auch ein Konto bei sozialen Netzwerken wie die „gmail.com“- oder „outlook.com“-Adresse.

## <a name="sign-in-to-your-tenant"></a>Anmelden im Mandanten

Führen Sie den folgenden Befehl aus, um eine Verbindung mit der Mandantendomäne herzustellen:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Beispiel: `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Geben Sie bei Aufforderung Ihre Anmeldeinformationen ein.

## <a name="send-an-invitation"></a>Senden einer Einladung

1. Um eine Einladung an Ihr Test-E-Mail-Konto zu senden, führen Sie den folgenden PowerShell-Befehl aus, und ersetzen Sie dabei **Sanda** und **sanda\@fabrikam.com** durch den Namen Ihres Test-E-Mail-Kontos und Ihre E-Mail-Adresse: 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. Der Befehl sendet eine Einladung an die angegebene E-Mail-Adresse. Die Ausgabe sollte etwa so aussehen:

   ![PowerShell-Ausgabe: ausstehende Annahme durch Benutzer](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Überprüfen, ob der Benutzer im Verzeichnis vorhanden ist

1. Um zu überprüfen, ob der eingeladene Benutzer Azure AD hinzugefügt wurde, führen Sie den folgenden Befehl aus:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Überprüfen Sie, ob der von Ihnen eingeladene Benutzer mit einem Benutzerprinzipalnamen (UPN) im Format *E-Mail-Adresse*#EXT#\@*Domäne* in der Ausgabe angezeigt wird. Beispiel: *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, wobei „contoso.onmicrosoft.com“ der Organisation entspricht, für die Sie die Einladungen gesendet haben.

   ![PowerShell-Ausgabe: Gastbenutzer wurde hinzugefügt](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Testbenutzerkonto nicht mehr benötigen, können Sie es aus dem Verzeichnis löschen. Führen Sie den folgenden Befehl aus, um ein Benutzerkonto zu löschen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Beispiel: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Nächste Schritte
In diesem Schnellstart haben Sie mit PowerShell einen einzelnen Gastbenutzer zu Ihrem Verzeichnis eingeladen und hinzugefügt. Als Nächstes erfahren Sie, wie Sie mit PowerShell Masseneinladungen an Gastbenutzer senden.

> [!div class="nextstepaction"]
> [Tutorial: Masseneinladen von Benutzern für die Azure AD B2B-Zusammenarbeit](tutorial-bulk-invite.md)
