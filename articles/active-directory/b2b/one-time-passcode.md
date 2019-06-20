---
title: Authentifizierung mit Einmalkennung für B2B-Gastbenutzer – Azure Active Directory | Microsoft-Dokumentation
description: Verwenden der Einmalkennung per E-Mail zum Authentifizieren von B2B-Gastbenutzern ohne Einrichten eines Microsoft-Kontos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13808871d67bb47dce82c5a3493fd89b0dfe1dcd
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952860"
---
# <a name="email-one-time-passcode-authentication-preview"></a>Authentifizierung mit Einmalkennung per E-Mail (Vorschauversion)

|     |
| --- |
| Die Einmalkennung (One-time passcode, OTP) per E-Mail ist eine öffentliche Previewfunktion für Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

In diesem Artikel wird das Aktivieren der Authentifizierung mit Einmalkennung per E-Mail für B2B-Gastbenutzer beschrieben. Mit dem Feature „Einmalkennung per E-Mail“ werden B2B-Gastbenutzer authentifiziert, wenn sie über andere Wege (z. B. über Azure AD, ein Microsoft-Konto (MSA) oder den Verbund mit Google) nicht authentifiziert werden können. Bei der Authentifizierung mit Einmalkennung ist es nicht erforderlich, ein Microsoft-Konto zu erstellen. Wenn der Gastbenutzer eine Einladung einlöst oder auf eine freigegebene Ressource zugreift, kann er einen temporären Code anfordern, der an seine E-Mail-Adresse gesendet wird. Anschließend gibt er diesen Code ein, um den Anmeldevorgang fortzusetzen.

Dieses Feature ist derzeit als Vorschauversion verfügbar (siehe [Abonnieren der Vorschauversion](#opting-in-to-the-preview) weiter unten). Im Anschluss an die Vorschauversion wird dieses Feature standardmäßig für alle Mandanten aktiviert.

> [!NOTE]
> Benutzer mit Einmalkennung müssen sich über einen Link anmelden, der den Mandantenkontext enthält (z.B. `https://myapps.microsoft.com/?tenantid=<tenant id>` oder `https://portal.azure.com/<tenant id>` bzw. `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` bei einer überprüften Domäne). Direkte Links zu Anwendungen und Ressourcen funktionieren ebenfalls, sofern sie den Mandantenkontext enthalten. Gastbenutzer können sich derzeit nicht über Endpunkte, die keinen Mandantenkontext aufweisen, anmelden. Die Verwendung beispielsweise von `https://myapps.microsoft.com`, `https://portal.azure.com` oder dem gemeinsamen Team-Endpunkt führt zu einem Fehler. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Benutzeroberfläche für Gastbenutzer mit Einmalkennung
Bei der Authentifizierung mit Einmalkennung kann der Gastbenutzer seine Einladung über einen direkten Link oder mithilfe der Einladungs-E-Mail einlösen. In beiden Fällen gibt eine Nachricht im Browser an, dass ein Code an die E-Mail-Adresse des Gastbenutzers gesendet wird. Der Gastbenutzer klickt auf **Code senden**:
 
   ![Screenshot mit der Schaltfläche „Code senden“](media/one-time-passcode/otp-send-code.png)
 
Eine Kennung wird an die E-Mail-Adresse des Benutzers gesendet. Der Benutzer ruft die Kennung aus der E-Mail ab und gibt sie im Browserfenster ein:
 
   ![Screenshot mit der Schaltfläche „Code eingeben“](media/one-time-passcode/otp-enter-code.png)
 
Der Gastbenutzer wird jetzt authentifiziert und kann entweder die freigegebene Ressource anzeigen oder seinen Anmeldevorgang fortsetzen. 

> [!NOTE]
> Einmalkennungen sind 30 Minuten gültig. Nach 30 Minuten ist die jeweilige Einmalkennung nicht mehr gültig, und der Benutzer muss eine neue Kennung anfordern. Benutzersitzungen laufen nach 24 Stunden ab. Danach erhält der Gastbenutzer eine neue Kennung, wenn er auf die Ressource zugreift. Der Ablauf der Sitzung sorgt für zusätzliche Sicherheit, besonders wenn ein Gastbenutzer das Unternehmen verlässt oder den Zugriff nicht mehr benötigt.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wann erhält ein Gastbenutzer eine Einmalkennung?

Wenn ein Gastbenutzer eine Einladung einlöst oder einen Link zu einer Ressource verwendet, die für ihn freigegeben wurde, erhält er unter folgenden Bedingungen eine Einmalkennung:
- Er hat kein Azure AD-Konto 
- Er hat kein Microsoft-Konto 
- Der einladende Mandant hat keinen Verbund mit Google für @gmail.com- und @googlemail.com- Benutzer eingerichtet 

Zum Zeitpunkt der Einladung gibt es keinen Hinweis darauf, dass der eingeladene Benutzer die Authentifizierung mit Einmalkennung verwendet. Wenn sich der Gastbenutzer jedoch anmeldet, wird die Authentifizierung mit Einmalkennung als alternative Methode verwendet, wenn keine anderen Authentifizierungsmethoden eingesetzt werden können. 

Sie können die Gastbenutzer anzeigen, die sich im Azure-Portal mit Einmalkennungen authentifizieren. Wechseln Sie zu diesem Zweck zu **Azure Active Directory** > **Organisationsbeziehungen** > **Benutzer aus anderen Organisationen**.

![Screenshot mit einem Benutzer mit Einmalkennung mit dem Quellwert „OTP“](media/one-time-passcode/otp-users.png)

> [!NOTE]
> Wenn ein Benutzer eine Einmalkennung einlöst und später ein MSA, ein Azure AD-Konto oder ein anderes Verbundkonto erhält, wird er weiterhin mit einer Einmalkennung authentifiziert. Wenn Sie seine Authentifizierungsmethode aktualisieren möchten, können Sie sein Gastbenutzerkonto löschen und ihn erneut einladen.

### <a name="example"></a>Beispiel
Gastbenutzer alexdoe@gmail.com wird von Fabrikam eingeladen. Das Unternehmen hat keinen Verbund mit Google eingerichtet. Alex hat kein Microsoft-Konto. Er erhält für die Authentifizierung eine Einmalkennung.

## <a name="opting-in-to-the-preview"></a>Abonnieren der Vorschauversion 
Es dauert möglicherweise einige Minuten, bis die Abonnierungsaktion wirksam wird. Danach wird nur für neu eingeladene Benutzer, welche die oben genannten Bedingungen erfüllen, die Authentifizierung mit Einmalkennung verwendet. Gastbenutzer, die ihre Einladung bereits eingelöst haben, werden weiterhin mit der bisherigen Methode authentifiziert.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>So abonnieren Sie mithilfe des Azure AD-Portals
1.  Melden Sie sich als globaler Azure AD-Administrator im [Azure-Portal](https://portal.azure.com/) an.
2.  Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3.  Wählen Sie unter **Verwalten** die Option **Organisationsbeziehungen** aus.
4.  Wählen Sie **Settings**aus.
5.  Wählen Sie unter **Einmalkennung per E-Mail für Gastbenutzer aktivieren (Vorschauversion)** die Option **Ja** aus.
 
### <a name="to-opt-in-using-powershell"></a>So abonnieren Sie mithilfe von PowerShell

Zuerst müssen Sie die aktuelle Version von Azure AD PowerShell für das Graph-Modul (AzureADPreview) installieren. Anschließend ermitteln Sie, ob bereits B2B-Richtlinien vorhanden sind, und führen die entsprechenden Befehle aus.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Voraussetzung: Installieren des aktuellen AzureADPreview-Moduls
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Suchen nach vorhandenen Richtlinien und Abonnieren

Überprüfen Sie als Nächstes, ob derzeit eine Richtlinie „B2BManagementPolicy“ vorhanden ist. Führen Sie dazu die folgende Abfrage aus:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Wenn die Ausgabe „False“ lautet, ist die Richtlinie derzeit nicht vorhanden. Führen Sie den folgenden Befehl aus, um eine neue Richtlinie „B2BManagementPolicy“ zu erstellen und die Vorschauversion zu abonnieren:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Wenn die Ausgabe „True“ lautet, ist die Richtlinie „B2BManagementPolicy“ derzeit vorhanden. Führen Sie den folgenden Befehl aus, um die Richtlinie zu aktualisieren und die Vorschauversion zu abonnieren:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Kündigen des Abonnements der Vorschauversion
Es dauert möglicherweise einige Minuten, bis die Kündigungsaktion für das Abonnement wirksam wird. Wenn Sie die Vorschauversion deaktivieren, können sich alle Gastbenutzer, die eine Einmalkennung eingelöst haben, nicht mehr anmelden. Sie können die Gastbenutzer löschen und neu einladen, damit sich die Gastbenutzer mit einer anderen Authentifizierungsmethode anmelden können.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>So deaktivieren Sie die Vorschauversion mithilfe des Azure AD-Portals
1.  Melden Sie sich als globaler Azure AD-Administrator im [Azure-Portal](https://portal.azure.com/) an.
2.  Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3.  Wählen Sie unter **Verwalten** die Option **Organisationsbeziehungen** aus.
4.  Wählen Sie **Settings**aus.
5.  Wählen Sie unter **Einmalkennung per E-Mail für Gastbenutzer aktivieren (Vorschauversion)** die Option **Nein** aus.

### <a name="to-turn-off-the-preview-using-powershell"></a>So deaktivieren Sie die Vorschauversion mithilfe von PowerShell
Installieren Sie das aktuelle AzureADPreview-Modul, falls dies noch nicht geschehen ist (siehe [Voraussetzungen: Installieren des aktuellen AzureADPreview-Moduls](#prerequisite-install-the-latest-azureadpreview-module) weiter oben). Überprüfen Sie dann, ob derzeit die Richtlinie der Vorschauversion für die Authentifizierung mit Einmalkennung vorhanden ist, indem Sie die folgende Abfrage ausführen:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Wenn die Ausgabe „True“ lautet, kündigen Sie das Abonnement für die Vorschauversion, indem Sie den folgenden Befehl ausführen:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

