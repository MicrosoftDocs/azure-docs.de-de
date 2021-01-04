---
title: Verwalten benutzerdefinierter Richtlinien mit PowerShell
titleSuffix: Azure AD B2C
description: Verwenden Sie in Azure Active Directory (Azure AD) das PowerShell-Cmdlet für die programmgesteuerte Verwaltung Ihrer benutzerdefinierten Azure AD B2C-Richtlinien. Mit PowerShell können Sie benutzerdefinierte Richtlinien erstellen, lesen, aktualisieren und löschen.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928714"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Verwalten von benutzerdefinierten Azure AD B2C-Richtlinien mit Azure PowerShell

Azure PowerShell stellt mehrere Cmdlets für die befehlszeilen- und skriptgestützte Verwaltung benutzerdefinierter Richtlinien in Ihrem Azure AD B2C-Mandanten bereit. Erfahren Sie, wie Sie das Azure AD PowerShell-Modul für folgende Aufgaben verwenden:

* Auflisten der benutzerdefinierten Richtlinien in einem Azure AD B2C-Mandanten
* Herunterladen einer Richtlinie von einem Mandanten
* Aktualisieren einer vorhandenen Richtlinie durch Überschreiben des Inhalts
* Hochladen einer neuen Richtlinie in ihren Azure AD B2C-Mandanten
* Löschen einer benutzerdefinierten Richtlinie aus einem Mandanten

## <a name="prerequisites"></a>Voraussetzungen

* [Azure AD B2C-Mandant](tutorial-create-tenant.md) und Anmeldeinformationen für einen Benutzer im Verzeichnis mit der Rolle [B2C-IEF-Richtlinienadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Benutzerdefinierte Richtlinien](custom-policy-get-started.md), die in Ihren Mandanten hochgeladen wurden
* [Azure AD PowerShell-Modul für Graph (**Vorschauversion**](/powershell/azure/active-directory/install-adv2))

## <a name="connect-powershell-session-to-b2c-tenant"></a>Verbinden der PowerShell-Sitzung mit dem B2C-Mandanten

Um in Ihrem Azure AD B2C-Mandanten mit benutzerdefinierten Richtlinien arbeiten zu können, müssen Sie zuerst mit dem Befehl [Connect-AzureAD][Connect-AzureAD] Ihre PowerShell-Sitzung mit dem Mandanten verbinden.

Führen Sie den folgenden Befehl aus, und ersetzen Sie dabei `{b2c-tenant-name}` durch den Namen Ihres Azure AD B2C-Mandanten. Melden Sie sich mit einem Konto an, dem im Verzeichnis die Rolle [B2C-IEF-Richtlinienadministrator](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) zugewiesen ist.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Beispiel für die Befehlsausgabe bei erfolgreicher Anmeldung:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Auflisten aller benutzerdefinierten Richtlinien im Mandanten

Durch das Ermitteln der benutzerdefinierten Richtlinien kann ein Azure AD B2C-Administrator seinen Vorgängen Geschäftslogik hinzufügen, diese überprüfen und verwalten. Mit dem Befehl [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] wird eine Liste der IDs von benutzerdefinierten Richtlinien in einem Azure AD B2C-Mandanten zurückzugeben.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Beispiel für die Befehlsausgabe:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Herunterladen einer Richtlinie

Nachdem Sie die Liste der Richtlinien-IDs überprüft haben, können Sie mit dem Befehl [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] auf eine bestimmte Richtlinie abzielen, um deren Inhalt herunterzuladen.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

In diesem Beispiel wird die Richtlinie mit der ID *B2C_1A_signup_signin* heruntergeladen:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Um den Richtlinieninhalt lokal zu bearbeiten, übergeben Sie mit dem `-OutputFilePath`-Argument die Befehlsausgabe an eine Datei und öffnen dann die Datei in Ihrem bevorzugten Editor.

Beispielbefehl zum Senden der Ausgabe an eine Datei:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aktualisieren einer vorhandenen Richtlinie

Nachdem Sie die von Ihnen erstellte oder heruntergeladene Richtliniendatei bearbeitet haben, können Sie mit dem Befehl [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] die aktualisierte Richtlinie in Azure AD B2C veröffentlichen.

Wenn Sie den Befehl `Set-AzureADMSTrustFrameworkPolicy` mit der ID einer Richtlinie ausgeben, die bereits in Ihrem Azure AD B2C-Mandanten vorhanden ist, wird der Inhalt dieser Richtlinie überschrieben.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Beispielbefehl:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Weitere Beispiele finden Sie in der Referenz zum Befehl [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy].

## <a name="upload-a-new-policy"></a>Hochladen einer neuen Richtlinie

Wenn Sie eine Änderung an einer benutzerdefinierten Richtlinie vornehmen, die in der Produktionsumgebung ausgeführt wird, können Sie ggf. mehrere Versionen der Richtlinie für Fallback- oder A/B-Testszenarien veröffentlichen. Vielleicht möchten Sie aber auch eine Kopie einer vorhandenen Richtlinie erstellen, einige kleine Änderungen daran vornehmen und sie dann als neue Richtlinie für eine andere Anwendung hochladen.

Verwenden Sie zum Hochladen einer neuen Richtlinie den Befehl [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy]:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Beispielbefehl:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Löschen einer benutzerdefinierten Richtlinie

Unter dem Gesichtspunkt eines Lebenszyklus mit bereinigten Vorgängen empfiehlt es sich, regelmäßig nicht verwendete benutzerdefinierte Richtlinien zu entfernen. Beispielsweise können Sie alte Richtlinienversionen entfernen, nachdem Sie eine Migration zu einem neuen Satz von Richtlinien durchgeführt und die Funktionalität der neuen Richtlinien überprüft haben. Wenn Sie versuchen, eine Reihe von benutzerdefinierten Richtlinien zu veröffentlichen und eine Fehlermeldung erhalten, kann es außerdem sinnvoll sein, die Richtlinien zu entfernen, die im Rahmen der fehlgeschlagenen Freigabe erstellt wurden.

Verwenden Sie den Befehl [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy], um eine Richtlinie aus Ihrem Mandanten zu löschen.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Beispielbefehl:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Problembehandlung beim Hochladen von Richtlinien

Wenn Sie versuchen, eine neue benutzerdefinierte Richtlinie zu veröffentlichen oder eine vorhandene Richtlinie zu aktualisieren, können eine falsche XML-Formatierung und Fehler in der Vererbungskette der Richtliniendatei zu Fehlern bei der Überprüfung führen.

Hier ist zum Beispiel der Versuch, eine Richtlinie mit Inhalt zu aktualisieren, der falsch formatierten XML-Code enthält (die Ausgabe ist aus Gründen der Übersichtlichkeit gekürzt):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Informationen zur Problembehandlung bei benutzerdefinierten Richtlinien finden Sie unter [Beheben von Problemen mit benutzerdefinierten Azure AD B2C-Richtlinien und dem Identity Experience Framework](./troubleshoot-custom-policies.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung von PowerShell zur Bereitstellung benutzerdefinierter Richtlinien im Rahmen einer CI/CD-Pipeline (Continuous Integration/Continuous Delivery) finden Sie unter [Bereitstellen von benutzerdefinierten Richtlinien aus einer Azure DevOps-Pipeline](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
