---
title: Richtlinien für die Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Erfahren Sie mehr über die verschiedenen Optionen für Self-Service-Kennwortzurücksetzungsrichtlinien in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 980e830716b66b63c8f2dc6e641a88af26d44871
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027063"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Kennwortrichtlinien und Kontoeinschränkungen in Azure Active Directory

In Azure Active Directory (Azure AD) definiert eine Kennwortrichtlinie Einstellungen wie die Kennwortkomplexität, die Länge oder das Alter. Außerdem definiert eine Richtlinie zulässige Zeichen und die Länge für Benutzernamen.

Wenn ein Kennwort in Azure AD mithilfe der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) geändert oder zurückgesetzt wird, erfolgt eine Überprüfung anhand der Kennwortrichtlinie. Wenn das Kennwort nicht die Richtlinienanforderungen erfüllt, wird der Benutzer aufgefordert, es erneut zu versuchen. Für Azure-Administratoren gelten einige Einschränkungen bei der Verwendung der SSPR, die für reguläre Benutzerkonten nicht gelten.

In diesem Artikel werden die Einstellungen und Komplexitätsanforderungen für Kennwortrichtlinien im Zusammenhang mit Benutzerkonten in Ihrem Azure AD-Mandanten sowie die Verwendung von PowerShell zum Überprüfen oder Festlegen von Kennwortablaufeinstellungen beschrieben.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Richtlinien für Benutzernamen

Jedes Konto, das sich bei Azure AD anmeldet, muss über ein eindeutiges, diesem Konto zugeordnetes Benutzerprinzipalnamen-Attribut (UPN, User Principal Name) verfügen. In Hybridumgebungen mit einer lokalen Active Directory Domain Services-Umgebung (AD DS), die über Azure AD Connect mit Azure AD synchronisiert wird, ist der Azure AD-UPN standardmäßig auf den lokalen UPN festgelegt.

In der folgenden Tabelle sind die Richtlinien für Benutzernamen aufgeführt, die sowohl für lokale AD DS-Konten, die mit Azure AD synchronisiert werden, als auch für Benutzerkonten gelten, die ausschließlich in der Cloud verwendet und direkt in Azure AD erstellt werden:

| Eigenschaft | UserPrincipalName-Richtlinien |
| --- | --- |
| Zulässige Zeichen |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Unzulässige Zeichen |<ul> <li>Jedes\@\"-Zeichen, das nicht den Benutzernamen und die Domäne trennt.</li> <li>Darf keinen Punkt (.) unmittelbar vor dem \@\"-Symbol enthalten.</li></ul> |
| Längenbeschränkungen |<ul> <li>Die Gesamtlänge darf 113 Zeichen nicht überschreiten.</li><li>Vor dem \@\"-Symbol sind bis zu 64 Zeichen zulässig.</li><li>Nach dem \@\"-Symbol sind bis zu 48 Zeichen zulässig.</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Azure AD-Kennwortrichtlinien

Eine Kennwortrichtlinie wird auf alle Benutzerkonten angewandt, die direkt in Azure AD erstellt und verwaltet werden. Einige dieser Kennwortrichtlinien können zwar nicht geändert werden, aber Sie können [benutzerdefinierte gesperrte Kennwörter für den Azure AD-Kennwortschutz](tutorial-configure-custom-password-protection.md) oder Parameter für Kontosperrungen konfigurieren.

Nach 10 nicht erfolgreichen Anmeldeversuchen mit einem falschen Kennwort wird das Konto standardmäßig gesperrt. Der Benutzer wird dann für eine Minute gesperrt. Mit jeder weiteren fehlerhaften Anmeldeversuch verlängert sich die Dauer, die der Benutzer gesperrt ist. [Smart Lockout](howto-password-smart-lockout.md) verfolgt die letzten drei fehlerhaften Kennworthashes, um zu vermeiden, dass der Sperrungszähler für dasselbe Kennwort erhöht wird. Wenn eine Person mehrmals das falsche Kennwort eingibt, wird dadurch keine Kontosperre verursacht. Sie können den Grenzwert und die Dauer von Smart Lockout-Maßnahmen festlegen.

Die Azure AD-Kennwortrichtlinie gilt nicht für Benutzerkonten, die über Azure AD Connect aus einer lokalen AD DS-Umgebung synchronisiert werden, sofern Sie nicht *EnforceCloudPasswordPolicyForPasswordSyncedUsers* aktivieren.

Die folgenden Optionen für Azure AD-Kennwortrichtlinien sind definiert: Sofern nichts anderes angegeben ist, können Sie die folgenden Einstellungen nicht ändern:

| Eigenschaft | Requirements (Anforderungen) |
| --- | --- |
| Zulässige Zeichen |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ;</li> <li>Leerraum</li></ul> |
| Unzulässige Zeichen | Unicode-Zeichen |
| Kennworteinschränkungen |<ul><li>Mindestens 8 Zeichen und höchstens 256 Zeichen.</li><li>Muss drei der folgenden vier Elemente enthalten:<ul><li>Kleinbuchstaben</li><li>Großbuchstaben</li><li>Zahlen (0 bis 9)</li><li>Symbole (siehe die vorherigen Kennworteinschränkungen)</li></ul></li></ul> |
| Zeitraum bis zum Ablauf des Kennworts (maximales Kennwortalter) |<ul><li>Standardwert: **90** Tage</li><li>Der Wert kann im Azure Active Directory-Modul für Windows PowerShell mit dem Cmdlet `Set-MsolPasswordPolicy` konfiguriert werden.</li></ul> |
| Benachrichtigung bei Ablauf des Kennworts (wenn Benutzer über den Ablauf des Kennworts benachrichtigt werden) |<ul><li>Standardwert: **14** Tage (bevor das Kennwort abläuft)</li><li>Der Wert kann mit dem Cmdlet `Set-MsolPasswordPolicy` konfiguriert werden.</li></ul> |
| Kennwortablauf (Kennwort läuft nie ab) |<ul><li>Standardwert: **false** (gibt an, dass Kennwörter ein Ablaufdatum aufweisen).</li><li>Der Wert kann mit dem Cmdlet `Set-MsolUser` für einzelne Benutzerkonten konfiguriert werden.</li></ul> |
| Verlauf der Kennwortänderungen | Das letzte Kennwort *kann nicht* erneut verwendet werden, wenn der Benutzer ein Kennwort ändert. |
| Verlauf der Kennwortzurücksetzungen | Das letzte Kennwort *kann* erneut verwendet werden, wenn der Benutzer ein vergessenes Kennwort zurücksetzt. |

## <a name="administrator-reset-policy-differences"></a>Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern

Standardmäßig ist die Self-Service-Kennwortzurücksetzung für Administratorkonten aktiviert, und es wird eine strenge Standardrichtlinie für die *zweistufige* Kennwortzurücksetzung erzwungen. Diese Richtlinie kann sich von der Richtlinie unterscheiden, die Sie für Ihre Benutzer definiert haben, und diese Richtlinie kann nicht geändert werden. Sie sollten die Funktion zum Zurücksetzen des Kennworts immer als Benutzer ohne zugewiesene Azure-Administratorrollen testen.

Mit einer Zwei-Gate-Richtlinie haben Administratoren nicht die Möglichkeit, Sicherheitsfragen zu verwenden.

Eine Zwei-Gate-Richtlinie erfordert Authentifizierungsdaten, die aus zwei Elementen bestehen, z. B. E-Mail-Adresse, Authentifikator-App oder Telefonnummer. Eine Zwei-Gate-Richtlinie gilt in folgenden Situationen:

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
* eine benutzerdefinierte Domäne für Ihren Azure AD-Mandanten konfiguriert wurde, z. B. *contoso.com*, oder
* Azure AD Connect synchronisiert Identitäten aus Ihrem lokalen Verzeichnis.

Sie können die Verwendung der Self-Service-Kennwortzurücksetzung für Administratorkonten über das PowerShell-Cmdlet[Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings) deaktivieren. Mithilfe des Parameters `-SelfServePasswordResetEnabled $False` wird die Self-Service-Kennwortzurücksetzung für Administratoren deaktiviert.

### <a name="exceptions"></a>Ausnahmen

Eine Ein-Gate-Richtlinie erfordert Authentifizierungsdaten, die aus einem Element bestehen, z. B. eine E-Mail-Adresse oder eine Telefonnummer. Eine Ein-Gate-Richtlinie gilt in folgenden Situationen:

* Für ein Testabonnement sind noch keine 30 Tage vergangen, oder
* für Ihren Azure AD-Mandanten wurde keine benutzerdefinierte Domäne konfiguriert, weshalb die Standarddomäne * *.onmicrosoft.com* verwendet wird. Die Standarddomäne * *.onmicrosoft.com* wird nicht für die Verwendung in der Produktion empfohlen.
* Azure AD Connect synchronisiert keine Identitäten.

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Richtlinien zum Kennwortablauf

Ein *globaler Administrator* oder *Benutzeradministrator* kann mit dem [Microsoft Azure AD-Modul für Windows PowerShell](/powershell/module/Azuread/) festlegen, dass Benutzerkennwörter nicht ablaufen.

Sie können auch PowerShell-Cmdlets verwenden, um die Konfiguration für niemals ablaufende Kennwörter zu entfernen oder um anzuzeigen, für welche Benutzerkennwörter festgelegt ist, dass sie nie ablaufen.

Diese Anleitung gilt für andere Anbieter wie Intune und Microsoft 365, die ebenfalls auf Azure AD als Identitäts- und Verzeichnisdienste zurückgreifen. Kennwortablauf ist der einzige Teil der Richtlinie, der geändert werden kann.

> [!NOTE]
> Nur Kennwörter für Benutzerkonten, die nicht über Azure AD Connect synchronisiert werden, können so konfiguriert werden, dass sie nicht ablaufen. Weitere Informationen zur Verzeichnissynchronisierung finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Festlegen oder Überprüfen der Kennwortrichtlinien mithilfe von PowerShell

Um zu beginnen, [laden Sie das Azure AD PowerShell-Modul herunter und installieren es](/powershell/module/Azuread/). [Verbinden Sie es anschließend mit Ihrem Azure AD-Mandanten](/powershell/module/azuread/connect-azuread#examples).

Nachdem das Modul installiert wurde, können Sie jede Aufgabe nach Bedarf mit den folgenden Schritten abschließen.

### <a name="check-the-expiration-policy-for-a-password"></a>Überprüfen der Ablaufrichtlinie für ein Kennwort

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und stellen Sie mithilfe des Kontos eines *globalen Administrators* oder *Benutzeradministrators* eine [Verbindung mit Ihrem Azure AD-Mandanten](/powershell/module/azuread/connect-azuread#examples) her.
1. Führen Sie einen der folgenden Befehle für einen einzelnen Benutzer oder für alle Benutzer aus:

   * Führen Sie das folgende Cmdlet aus, um zu ermitteln, ob für das Kennwort eines einzelnen Benutzers festgelegt ist, dass es nie abläuft. Ersetzen Sie `<user ID>` durch die Benutzer-ID des Benutzers, den Sie überprüfen möchten, z. B. *driley\@contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Um die Einstellung **Kennwort läuft nie ab** für alle Benutzer anzuzeigen, führen Sie das folgende Cmdlet aus:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Festlegen, dass ein Kennwort abläuft

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und stellen Sie mithilfe des Kontos eines *globalen Administrators* oder *Benutzeradministrators* eine [Verbindung mit Ihrem Azure AD-Mandanten](/powershell/module/azuread/connect-azuread#examples) her.
1. Führen Sie einen der folgenden Befehle für einen einzelnen Benutzer oder für alle Benutzer aus:

   * Um für das Kennwort eines Benutzers festzulegen, dass es abläuft, führen Sie das folgende Cmdlet aus. Ersetzen Sie `<user ID>` durch die Benutzer-ID des Benutzers, den Sie überprüfen möchten, z. B. *driley\@contoso.onmicrosoft.com*.

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Um für die Kennwörter aller Benutzer in der Organisation festzulegen, dass sie ablaufen, verwenden Sie das folgende Cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Festlegen, dass ein Kennwort nicht abläuft

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und stellen Sie mithilfe des Kontos eines *globalen Administrators* oder *Benutzeradministrators* eine [Verbindung mit Ihrem Azure AD-Mandanten](/powershell/module/azuread/connect-azuread#examples) her.
1. Führen Sie einen der folgenden Befehle für einen einzelnen Benutzer oder für alle Benutzer aus:

   * Um für das Kennwort eines Benutzers festzulegen, dass es nie abläuft, führen Sie das folgende Cmdlet aus. Ersetzen Sie `<user ID>` durch die Benutzer-ID des Benutzers, den Sie überprüfen möchten, z. B. *driley\@contoso.onmicrosoft.com*.

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Um für die Kennwörter aller Benutzer in der Organisation festzulegen, dass sie nie ablaufen, verwenden Sie das folgende Cmdlet:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Kennwörter, die auf `-PasswordPolicies DisablePasswordExpiration` festgelegt sind, altern trotzdem entsprechend dem `pwdLastSet`-Attribut. Entsprechend dem `pwdLastSet`-Attribut ergibt sich, wenn Sie das Ablaufen in `-PasswordPolicies None` ändern, dass jedes Kennwort, dessen `pwdLastSet` älter als 90 Tage ist, vom Benutzer bei seiner nächster Anmeldung geändert werden muss. Diese Änderung kann eine große Anzahl von Benutzern betreffen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit SSPR finden Sie im [Tutorial: Ermöglichen der Kontoentsperrung oder Kennwortzurücksetzung für Benutzer mit der Self-Service-Kennwortzurücksetzung von Azure Active Directory](tutorial-enable-sspr.md).

Wenn Sie oder Ihre Benutzer Probleme mit SSPR haben, finden Sie weitere Informationen unter [Behandeln von Problemen mit der Self-Service-Kennwortzurücksetzung](./troubleshoot-sspr.md).
