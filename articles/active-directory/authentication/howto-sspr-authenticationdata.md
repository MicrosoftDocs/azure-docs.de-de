---
title: Datenanforderungen für Azure AD-SSPR – Azure Active Directory
description: Datenanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung und deren Bereitstellung
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a0d7edb6c7faafcad55e827c2d9e3d2eeea40f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60358027"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung

Damit die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) bereitgestellt werden kann, müssen Authentifizierungsdaten vorhanden sein. Einige Organisationen überlassen es ihren Benutzern, dass diese ihre Authentifizierungsdaten selbst eingeben. Aber viele Organisationen bevorzugen ein Synchronisieren mit Daten, die bereits in Active Directory vorhanden ist. Die synchronisierten Daten werden für Azure AD und SSPR verfügbar gemacht, ohne dass ein Eingreifen eines Benutzers erforderlich ist, wenn Sie:

* Die Daten ordnungsgemäß in Ihrem lokalen Verzeichnis formatieren.
* [Azure AD Connect durch Verwenden der Express-Einstellungen](../hybrid/how-to-connect-install-express.md) konfigurieren.

Für ein ordnungsgemäßes Funktionieren müssen Telefonnummern im Format *+Landesvorwahl Telefonnummer* vorliegen (Beispiel: +1 4255551234).

> [!NOTE]
> Zwischen Landesvorwahl und Telefonnummer muss sich ein Leerzeichen befinden.
>
> Für die Kennwortzurücksetzung werden Nebenstellen nicht unterstützt. Selbst bei der Angabe im Format +1 4255551234X12345 werden Nebenstellen vor dem Anruf entfernt.

## <a name="fields-populated"></a>Ausgefüllte Felder

Wenn Sie die Standardeinstellungen in Azure AD Connect verwenden, werden die folgenden Zuordnungen vorgenommen:

| Lokales Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Bürotelefon |
| mobile | Mobiltelefon |

Nachdem ein Benutzer die Mobiltelefonnummer verifiziert hat, wird diese Nummer in Azure AD unter den „Kontaktinformationen für Authentifizierung“ in das Feld „Telefon“ eingefügt.

## <a name="authentication-contact-info"></a>Kontaktinformationen für Authentifizierung

Ein globaler Administrator kann die Kontaktinformationen eines Benutzers für die Authentifizierung manuell festlegen, wie im folgenden Screenshot gezeigt.

![Kontaktinformationen für die Authentifizierung eines Benutzers in Azure AD][Contact]

Wenn das Feld „Telefon“ ausgefüllt und „Mobiltelefon“ in der SSPR-Richtlinie aktiviert ist, wird dem Benutzer diese Nummer auf der Registrierungsseite für die Kennwortzurücksetzung und während des Workflows für die Kennwortzurücksetzung angezeigt.

Das Feld „Alternatives Telefon“ wird nicht für die Kennwortzurücksetzung verwendet.

Wenn das Feld „E-Mail“ ausgefüllt und „E-Mail“ in der SSPR-Richtlinie aktiviert ist, wird dem Benutzer diese E-Mail-Adresse auf der Registrierungsseite für die Kennwortzurücksetzung und während des Workflows für die Kennwortzurücksetzung angezeigt.

Wenn das Feld „Alternative E-Mail-Adresse“ ausgefüllt und „E-Mail“ in der SSPR-Richtlinie aktiviert ist, wird dem Benutzer diese E-Mail-Adresse auf der Registrierungsseite für die Kennwortzurücksetzung **nicht** angezeigt – aber während des Workflows für die Kennwortzurücksetzung.

## <a name="security-questions-and-answers"></a>Sicherheitsfragen und -antworten

Die Sicherheitsfragen und -antworten werden sicher in Ihrem Azure AD-Mandanten gespeichert und sind für Benutzer nur über das [SSPR-Registrierungsportal](https://aka.ms/ssprsetup) zugänglich. Administratoren können den Inhalt der Fragen und Antworten anderer Benutzer weder anzeigen, festlegen noch ändern.

## <a name="what-happens-when-a-user-registers"></a>Was geschieht bei Registrierung eines Benutzers?

Wenn sich ein Benutzer registriert, zeigt die Registrierungsseite die folgenden Felder an:

* **Telefon für Authentifizierung**
* **E-Mail für Authentifizierung**
* **Sicherheitsfragen und Antworten**

Wenn Sie einen Wert für **Mobiltelefon** oder **Alternative E-Mail-Adresse** angegeben haben, können Benutzer diese Werte sofort verwenden, um ihre Kennwörter zurücksetzen, selbst wenn sie sich nicht für den Dienst registriert haben. Benutzer sehen diese Werte außerdem, wenn sie sich erstmalig registrieren, und sie können die Werte bei Bedarf ändern. Nachdem sich Benutzer erfolgreich registriert haben, werden diese Werte in den Feldern **Telefon für Authentifizierung** und **E-Mail für Authentifizierung** beibehalten.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Festlegen und Lesen der Authentifizierungsdaten über PowerShell

Die folgenden Felder können über PowerShell festgelegt werden:

* **Alternative E-Mail-Adresse**
* **Mobiltelefon**
* **Bürotelefon:** Kann nur festgelegt werden, wenn Sie keine Synchronisierung mit einem lokalen Verzeichnis vornehmen.

### <a name="use-powershell-version-1"></a>Verwenden von PowerShell Version 1

Zunächst müssen Sie [das Azure AD PowerShell-Modul herunterladen und installieren](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Nachdem Sie es installiert haben, können Sie anhand der folgenden Schritte die einzelnen Felder konfigurieren.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Festlegen der Authentifizierungsdaten mit PowerShell Version 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
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

Zunächst müssen Sie [das Azure AD Version 2 PowerShell-Modul herunterladen und installieren](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Nachdem Sie es installiert haben, können Sie anhand der folgenden Schritte die einzelnen Felder konfigurieren.

Um schnell aus neueren Versionen von PowerShell zu installieren, die „Install-Module“ unterstützen, führen Sie die folgenden Befehle aus. (In der ersten Zeile wird überprüft, ob das Modul bereits installiert ist.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Festlegen der Authentifizierungsdaten mit PowerShell Version 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
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

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Zurücksetzen oder Ändern des Kennworts](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globale Administratoren können die Kontaktinformationen eines Benutzer für die Authentifizierung bearbeiten"
