---
title: Vorausfüllen von Kontaktinformationen für die Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Erfahren Sie, wie Sie Kontaktinformationen für Benutzer der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory vorab ausfüllen, damit diese das Feature verwenden können, ohne einen Registrierungsvorgang zu durchlaufen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed93944c2a94370250fb75a679c1ac37a2a8418c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861152"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Vorausfüllen von Kontaktinformationen zur Benutzerauthentifizierung für die Self-Service-Kennwortzurücksetzung in Azure Active Directory

Damit die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) bereitgestellt werden kann, müssen Kontaktinformationen zur Authentifizierung eines Benutzers vorhanden sein. In einigen Organisationen registrieren die Benutzer ihre Authentifizierungsdaten selbst. Andere Organisationen bevorzugen ein Synchronisieren mit Authentifizierungsdaten, die in Active Directory Domain Services (AD DS) bereits vorhanden sind. Die synchronisierten Daten werden für Azure AD und SSPR verfügbar gemacht, ohne dass ein Eingreifen des Benutzers erforderlich ist. Wenn Benutzer ihr Kennwort ändern oder zurücksetzen müssen, ist dies auch dann möglich, wenn sie ihre Kontaktinformationen zuvor nicht registriert haben.

Sie können die Kontaktinformationen für die Authentifizierung vorab ausfüllen, wenn Sie die folgenden Anforderungen erfüllen:

* Sie verfügen über ordnungsgemäß formatierte Daten in Ihrem lokalen Verzeichnis.
* Sie haben [Azure AD Connect](../hybrid/how-to-connect-install-express.md) für Ihren Azure AD-Mandanten konfiguriert.

Telefonnummern müssen im Format *+Landesvorwahl Telefonnummer* eingegeben werden, z. B. *+1 4251234567*.

> [!NOTE]
> Zwischen Landesvorwahl und Telefonnummer muss sich ein Leerzeichen befinden.
>
> Bei der Kennwortzurücksetzung werden Nebenstellen nicht unterstützt. Selbst bei der Angabe im Format *+1 4251234567X12345* werden Nebenstellen vor dem Anruf entfernt.

## <a name="fields-populated"></a>Ausgefüllte Felder

Wenn Sie die Standardeinstellungen in Azure AD Connect verwenden, werden die folgenden Zuordnungen zum Ausfüllen von Kontaktinformationen zur Authentifizierung für SSPR vorgenommen:

| Lokales Active Directory | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Bürotelefon |
| mobile                       | Mobiltelefon |

Nachdem ein Benutzer die Mobiltelefonnummer verifiziert hat, wird diese Nummer in Azure AD unter den **Kontaktinformationen für Authentifizierung** in das Feld *Telefon* eingefügt.

## <a name="authentication-contact-info"></a>Kontaktinformationen für Authentifizierung

Auf der Seite **Authentifizierungsmethoden** für einen Azure AD-Benutzer im Azure-Portal kann ein globaler Administrator die Kontaktinformationen für die Authentifizierung manuell festlegen. Vorhandene Methoden können im Abschnitt *Verwendbare Authentifizierungsmethoden* oder **+Add authentication methods** (+Authentifizierungsmethoden hinzufügen) überprüft werden, wie im folgenden Screenshot gezeigt:

:::image type="content" source="media/howto-sspr-authenticationdata/user-authentication-contact-info.png" alt-text="Verwalten von Authentifizierungsmethoden im Azure-Portal":::

Die folgenden Überlegungen gelten für diese Kontaktinformationen zur Authentifizierung:

* Wenn das Feld *Telefon* ausgefüllt und *Mobiltelefon* in der SSPR-Richtlinie aktiviert ist, wird dem Benutzer diese Nummer auf der Registrierungsseite für die Kennwortzurücksetzung und während des Workflows für die Kennwortzurücksetzung angezeigt.
* Wenn das Feld *E-Mail* ausgefüllt und *E-Mail* in der SSPR-Richtlinie aktiviert ist, wird dem Benutzer diese E-Mail-Adresse auf der Registrierungsseite für die Kennwortzurücksetzung und während des Workflows für die Kennwortzurücksetzung angezeigt.

## <a name="security-questions-and-answers"></a>Sicherheitsfragen und -antworten

Die Sicherheitsfragen und -antworten werden sicher in Ihrem Azure AD-Mandanten gespeichert und sind für Benutzer nur über das [SSPR-Registrierungsportal](https://aka.ms/ssprsetup) zugänglich. Administratoren können den Inhalt der Fragen und Antworten anderer Benutzer weder anzeigen, festlegen noch ändern.

## <a name="what-happens-when-a-user-registers"></a>Was geschieht bei Registrierung eines Benutzers?

Wenn sich ein Benutzer registriert, zeigt die Registrierungsseite die folgenden Felder an:

* **Telefon für Authentifizierung**
* **E-Mail für Authentifizierung**
* **Sicherheitsfragen und Antworten**

Wenn Sie einen Wert für *Mobiltelefon* oder *Alternative E-Mail-Adresse* angegeben haben, können Benutzer diese Werte sofort verwenden, um ihre Kennwörter zurücksetzen, selbst wenn sie sich nicht für den Dienst registriert haben.

Benutzer sehen diese Werte außerdem bei der ersten Registrierung und können die Werte bei Bedarf ändern. Nachdem sie sich erfolgreich registriert haben, werden diese Werte in den Feldern *Authentifizierungstelefon* und *E-Mail-Adresse für Authentifizierung* beibehalten.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Festlegen und Lesen der Authentifizierungsdaten über PowerShell

Die folgenden Felder können über PowerShell festgelegt werden:

* *Alternative E-Mail-Adresse*
* *Mobiltelefon*
* *Bürotelefon*
    * Kann nur festgelegt werden, wenn Sie keine Synchronisierung mit einem lokalen Verzeichnis vornehmen.

> [!IMPORTANT]
> Es ist bekannt, dass zwischen den Befehlsfeatures von PowerShell v1 und PowerShell v2 keine Parität vorliegt. Die [Microsoft Graph-REST-API (Beta) für Authentifizierungsmethoden](/graph/api/resources/authenticationmethods-overview) ist der aktuelle Entwicklungsschwerpunkt für eine moderne Interaktion.

### <a name="use-powershell-version-1"></a>Verwenden von PowerShell Version 1

Um zu beginnen, [laden Sie das Azure AD PowerShell-Modul herunter und installieren es](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). Nach der Installation können Sie jedes Feld mit den folgenden Schritten konfigurieren.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Festlegen der Authentifizierungsdaten mit PowerShell Version 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Lesen der Authentifizierungsdaten mit PowerShell Version 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Lesen der Optionen „Telefon für Authentifizierung“ und „E-Mail für Authentifizierung“

Wenn Sie PowerShell Version 1 verwenden und die Optionen **Telefon für Authentifizierung** und **E-Mail für Authentifizierung** lesen möchten, führen Sie die folgenden Befehle aus:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Verwenden von PowerShell Version 2

Zunächst [laden Sie das PowerShell-Modul für Azure AD Version 2 herunter und installieren es](/powershell/module/azuread/).

Für eine schnelle Installation aus neueren Versionen von PowerShell, die `Install-Module` unterstützen, führen Sie die folgenden Befehle aus. In der ersten Zeile wird überprüft, ob das Modul bereits installiert ist:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Nachdem das Modul installiert wurde, können Sie jedes Feld mit den folgenden Schritten konfigurieren.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Festlegen der Authentifizierungsdaten mit PowerShell Version 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Lesen der Authentifizierungsdaten mit PowerShell Version 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Kontaktinformationen zur Authentifizierung für die Benutzer ausgefüllt wurden, schließen Sie das folgende Tutorial ab, um die Self-Service-Kennwortzurücksetzung zu aktivieren:

> [!div class="nextstepaction"]
> [Aktivieren der Self-Service-Kennwortzurücksetzung in Azure AD](tutorial-enable-sspr.md)
