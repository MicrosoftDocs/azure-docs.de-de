---
title: Richtlinien für die Self-Service-Kennwortzurücksetzung von Azure AD – Azure Active Directory
description: Konfigurieren der Richtlinienoptionen zur Self-Service-Kennwortzurücksetzung in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f32952dff8f09db5b790818a5f98c527a04c2ef5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65823405"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Kennwortrichtlinien und -einschränkungen in Azure Active Directory

In diesem Artikel sind die Kennwortrichtlinien und Komplexitätsanforderungen beschrieben, die Benutzerkonten in Ihrem Azure Active Directory-Mandanten (Azure AD-Mandanten) zugeordnet sind.

## <a name="administrator-reset-policy-differences"></a>Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern

**Microsoft erzwingt standardmäßig eine starke *Zwei-Gate*-Richtlinie zur Kennwortzurücksetzung für alle Azure-Administratorrollen**. Diese Richtlinie kann von der Richtlinie abweichen, die Sie für Ihre Benutzer definiert haben, und nicht geändert werden. Sie sollten die Funktion zum Zurücksetzen des Kennworts immer als Benutzer ohne zugewiesene Azure-Administratorrollen testen.

Mit einer Zwei-Gate-Richtlinie haben **Administratoren nicht die Möglichkeit, Sicherheitsfragen zu verwenden**.

Eine Zwei-Gate-Richtlinie erfordert Authentifizierungsdaten, die aus zwei Elementen bestehen, z.B. **E-Mail-Adresse**, **Authentifikator-App** oder **Telefonnummer**. Eine Zwei-Gate-Richtlinie gilt in folgenden Situationen:

* Alle folgenden Administratorrollen sind betroffen:
  * Helpdesk-Administrator
  * Dienstunterstützungsadministrator
  * Rechnungsadministrator
  * Partnersupport der Ebene 1
  * Partnersupport der Ebene 2
  * Exchange-Administrator
  * Skype for Business-Administrator
  * Benutzeradministrator
  * Verzeichnis schreiben
  * Globaler Administrator oder Unternehmensadministrator
  * SharePoint-Administrator
  * Complianceadministrator
  * Anwendungsadministrator
  * Sicherheitsadministrator
  * Administrator für privilegierte Rollen
  * Intune-Administrator
  * Anwendungsproxy-Dienstadministrator
  * Dynamics 365-Administrator
  * Power BI-Dienstadministrator
  * Authentifizierungsadministrator
  * Privilegierter Authentifizierungsadministrator

* Wenn 30 Tage in einem Testabonnement abgelaufen sind, oder
* Es ist eine benutzerdefinierte Domäne vorhanden, z.B. contoso.com, oder
* Azure AD Connect synchronisiert Identitäten aus Ihrem lokalen Verzeichnis.

### <a name="exceptions"></a>Ausnahmen

Eine Ein-Gate-Richtlinie erfordert Authentifizierungsdaten, die aus einem Element bestehen, z.B. eine E-Mail-Adresse *oder* eine Telefonnummer. Eine Ein-Gate-Richtlinie gilt in folgenden Situationen:

* Für ein Testabonnement sind noch keine 30 Tage vergangen, oder
* Es ist keine benutzerdefinierte Domäne vorhanden (*.onmicrosoft.com), und
* Azure AD Connect synchronisiert keine Identitäten.

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-Richtlinien, die für alle Benutzerkonten gelten

Jedes Benutzerkonto, das sich bei Azure AD anmeldet, muss über ein eindeutiges, diesem Konto zugeordnetes Benutzerprinzipalnamen-Attribut (UPN, User Principal Name) verfügen. In der folgenden Tabelle sind die Richtlinien aufgeführt, die sowohl für lokale Active Directory-Benutzerkonten, die mit der Cloud synchronisiert werden, als auch für Benutzerkonten, die ausschließlich in der Cloud verwendet werden, gelten:

| Eigenschaft | UserPrincipalName-Richtlinien |
| --- | --- |
| Zulässige Zeichen |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Unzulässige Zeichen |<ul> <li>Jedes\@\"-Zeichen, das nicht den Benutzernamen und die Domäne trennt.</li> <li>Darf keinen Punkt (.) unmittelbar vor dem \@\"-Symbol enthalten.</li></ul> |
| Längenbeschränkungen |<ul> <li>Die Gesamtlänge darf 113 Zeichen nicht überschreiten.</li><li>Vor dem \@\"-Symbol sind bis zu 64 Zeichen zulässig.</li><li>Nach dem \@\"-Symbol sind bis zu 48 Zeichen zulässig.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Kennwortrichtlinien, die nur für Cloudbenutzerkonten gelten

Die folgende Tabelle beschreibt die Kennwortrichtlinieneinstellungen, die auf die in Azure AD erstellten und verwalteten Benutzerkonten angewendet werden können:

| Eigenschaft | Requirements (Anforderungen) |
| --- | --- |
| Zulässige Zeichen |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li> <li>Leerraum</li></ul> |
| Unzulässige Zeichen |<ul><li>Unicode-Zeichen</li><li> Darf keinen Punkt (.) unmittelbar vor dem \@\"-Symbol enthalten.</li></ul> |
| Kennworteinschränkungen |<ul><li>Mindestens 8 Zeichen und höchstens 256 Zeichen.</li><li>Muss drei der folgenden vier Elemente enthalten:<ul><li>Kleinbuchstaben</li><li>Großbuchstaben</li><li>Zahlen (0 bis 9)</li><li>Symbole (siehe die vorherigen Kennworteinschränkungen)</li></ul></li></ul> |
| Zeitraum bis zum Ablauf des Kennworts |<ul><li>Standardwert: **90** Tage</li><li>Der Wert kann im Azure Active Directory-Modul für Windows PowerShell mit dem Cmdlet `Set-MsolPasswordPolicy` konfiguriert werden.</li></ul> |
| Benachrichtigung bei Ablauf des Kennworts |<ul><li>Standardwert: **14** Tage (bevor das Kennwort abläuft)</li><li>Der Wert kann mit dem Cmdlet `Set-MsolPasswordPolicy` konfiguriert werden.</li></ul> |
| Ablauf des Kennworts |<ul><li>Standardwert: **false** Tage (gibt an, dass Kennwortablauf aktiviert ist)</li><li>Der Wert kann mit dem Cmdlet `Set-MsolUser` für einzelne Benutzerkonten konfiguriert werden.</li></ul> |
| Verlauf der Kennwortänderungen |Das letzte Kennwort *kann nicht* erneut verwendet werden, wenn der Benutzer ein Kennwort ändert. |
| Verlauf der Kennwortzurücksetzungen | Das letzte Kennwort *kann* erneut verwendet werden, wenn der Benutzer ein vergessenes Kennwort zurücksetzt. |
| Kontosperrung |Nach 10 nicht erfolgreichen Anmeldeversuchen mit einem falschen Kennwort wird der Benutzer für eine Minute gesperrt. Mit jeder weiteren fehlerhaften Anmeldeversuch verlängert sich die Dauer, die der Benutzer gesperrt ist. [Smart Lockout](howto-password-smart-lockout.md) verfolgt die letzten drei fehlerhaften Kennworthashes, um zu vermeiden, dass der Sperrungszähler für dasselbe Kennwort erhöht wird. Wenn eine Person mehrere Male das falsche Kennwort eingibt, wird dadurch keine Kontosperre verursacht. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Festlegen von Kennwortablaufrichtlinien in Azure AD

Ein globaler Administrator oder Benutzeradministrator für einen Microsoft-Clouddienst kann mit dem Microsoft Azure Active Directory-Modul für Windows PowerShell festlegen, dass Benutzerkennwörter nicht ablaufen. Sie können auch Windows PowerShell-Cmdlets verwenden, um die Konfiguration zu entfernen, die angibt, dass Kennwörter nie ablaufen, oder um Benutzerkennwörter anzuzeigen, für die festgelegt ist, dass sie nie ablaufen. 

Diese Anleitung gilt für andere Anbieter wie Intune und Office 365, die ebenfalls auf Azure AD als Identitäts- und Verzeichnisdienste zurückgreifen. Kennwortablauf ist der einzige Teil der Richtlinie, der geändert werden kann.

> [!NOTE]
> Nur Kennwörter für Benutzerkonten, die nicht über die Verzeichnissynchronisierung synchronisiert werden, können so konfiguriert werden, dass sie nicht ablaufen. Weitere Informationen zur Verzeichnissynchronisierung finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Festlegen oder Überprüfen der Kennwortrichtlinien mithilfe von PowerShell

Zunächst müssen Sie [das Azure AD PowerShell-Modul herunterladen und installieren](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Nachdem Sie das Modul installiert haben, können Sie jedes Feld mit den folgenden Schritten konfigurieren.

### <a name="check-the-expiration-policy-for-a-password"></a>Überprüfen der Ablaufrichtlinie für ein Kennwort

1. Stellen Sie mit den Anmeldeinformationen des Benutzeradministrators oder Unternehmensadministrators eine Verbindung mit Windows PowerShell her.
1. Führen Sie einen der folgenden Befehle aus:

   * Um festzustellen, ob für das Kennwort eines einzelnen Benutzers festgelegt ist, dass es nie abläuft, führen Sie das folgende Cmdlet mit dem UPN (z.B. *aprilr\@contoso.onmicrosoft.com*) oder der Benutzer-ID des Benutzers aus, den Sie überprüfen möchten:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Um die Einstellung **Kennwort läuft nie ab** für alle Benutzer anzuzeigen, führen Sie das folgende Cmdlet aus:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Festlegen, dass ein Kennwort abläuft

1. Stellen Sie mit den Anmeldeinformationen des Benutzeradministrators oder Unternehmensadministrators eine Verbindung mit Windows PowerShell her.
1. Führen Sie einen der folgenden Befehle aus:

   * Um für das Kennwort eines Benutzers festzulegen, dass es abläuft, führen Sie das folgende Cmdlet mit UPN oder Benutzer-ID des Benutzers aus:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Um für die Kennwörter aller Benutzer in der Organisation festzulegen, dass sie ablaufen, verwenden Sie das folgende Cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Festlegen, dass ein Kennwort nicht abläuft

1. Stellen Sie mit den Anmeldeinformationen des Benutzeradministrators oder Unternehmensadministrators eine Verbindung mit Windows PowerShell her.
1. Führen Sie einen der folgenden Befehle aus:

   * Um für das Kennwort eines Benutzers festzulegen, dass es nie abläuft, führen Sie das folgende Cmdlet mit UPN oder Benutzer-ID des Benutzers aus:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Um für die Kennwörter aller Benutzer in der Organisation festzulegen, dass sie nie ablaufen, verwenden Sie das folgende Cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Kennwörter, die auf `-PasswordPolicies DisablePasswordExpiration` festgelegt sind, altern trotzdem entsprechend dem `pwdLastSet`-Attribut. Wenn Sie für die Benutzerkennwörter festlegen, dass sie nie ablaufen, und dann 90+ Tage vergangen sind, laufen die Kennwörter ab. Entsprechend dem `pwdLastSet`-Attribut ergibt sich, wenn Sie das Ablaufen in `-PasswordPolicies None` ändern, dass jedes Kennwort, dessen `pwdLastSet` älter als 90 Tage ist, vom Benutzer bei seiner nächster Anmeldung geändert werden muss. Diese Änderung kann eine große Anzahl von Benutzern betreffen.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zur Kennwortzurücksetzung über Azure AD:

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Ich habe mein Azure AD-Kennwort vergessen. Was nun?](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Welche Authentifizierungsmethoden sind für Benutzer verfügbar?](concept-sspr-howitworks.md#authentication-methods)
* [Worum handelt es sich beim Rückschreiben von Kennwörtern, und warum sollte ich mir Gedanken darüber machen?](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)
