---
title: Erstellen einer Identität für die Azure-App mit PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Azure PowerShell eine Azure Active Directory-Anwendung und einen Dienstprinzipal erstellen sowie mittels rollenbasierter Zugriffssteuerung Zugriff auf Ressourcen gewähren. Es wird gezeigt, wie eine Anwendung mit einem Zertifikat authentifiziert wird.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: tomfitz
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d6de5186b1906d56b5a43317d9c36ad1cc6aad
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540396"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Gewusst wie: Verwenden von Azure PowerShell zum Erstellen eines Dienstprinzipals mit einem Zertifikat

Wenn eine App oder ein Skript Zugriff auf Ressourcen benötigt, können Sie eine Identität für die App einrichten und sie mit ihren eigenen Anmeldeinformationen authentifizieren. Diese Identität wird als Dienstprinzipal bezeichnet. Dieser Ansatz ermöglicht Ihnen Folgendes:

* Sie können der App-Identität Berechtigungen zuweisen, die sich von Ihren eigenen Berechtigungen unterscheiden. In der Regel sind diese Berechtigungen genau auf die Aufgaben der App beschränkt.
* Sie können ein Zertifikat für die Authentifizierung beim Ausführen eines unbeaufsichtigten Skripts verwenden.

> [!IMPORTANT]
> Anstatt einen Dienstprinzipal zu erstellen, sollten Sie die Verwendung verwalteter Identitäten für Azure-Ressourcen für Ihre Anwendungsidentität erwägen. Wenn Ihr Code unter einem Dienst ausgeführt wird, der verwaltete Identitäten unterstützt und auf Ressourcen zugreift, die die Azure AD-Authentifizierung (Active Directory) unterstützen, sind verwaltete Identitäten für Sie die besser geeignete Option. Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen (z.B. auch zu den Diensten, die diese zurzeit unterstützen) finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md).

In diesem Artikel wird veranschaulicht, wie Sie einen Dienstprinzipal erstellen, der mit einem Zertifikat authentifiziert wird. Informationen zur Einrichtung eines Dienstprinzipals mit einem Kennwort finden Sie unter [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Für diesen Artikel benötigen Sie die [aktuelle Version](/powershell/azure/install-az-ps) von PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Zum Abschließen dieses Artikels benötigen Sie sowohl in Ihrer Azure AD-Instanz als auch im Azure-Abonnement ausreichende Berechtigungen. Insbesondere müssen Sie eine App in Azure AD erstellen und dem Dienstprinzipal eine Rolle zuweisen können.

Die einfachste Möglichkeit zum Überprüfen, ob Ihr Konto über die erforderlichen Berechtigungen verfügt, ist über das Portal. Siehe [Überprüfen der erforderlichen Berechtigung](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Erstellen eines Dienstprinzipals mit selbstsigniertem Zertifikat

Im folgenden Beispiel geht es um ein einfaches Szenario. [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) wird zum Erstellen eines Dienstprinzipals mit einem selbstsignierten Zertifikat verwendet, und [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) wird verwendet, um dem Dienstprinzipal die Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) zuzuweisen. Die Rollenzuweisung ist auf Ihr derzeit ausgewähltes Azure-Abonnement beschränkt. Verwenden Sie [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext), um ein anderes Abonnement auszuwählen.

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Das Beispiel befindet sich 20 Sekunden lang im Ruhezustand und schafft damit Zeit für die Verteilung des neuen Dienstprinzipals in Azure AD. Wenn Ihr Skript nicht lange genug wartet, wird eine Fehlermeldung mit folgendem Wortlaut angezeigt: „Der Prinzipal {ID} ist im Verzeichnis {Verzeichnis-ID} nicht enthalten.“ Sie können diesen Fehler beheben, indem Sie einen Moment warten und dann den Befehl **New-AzRoleAssignment** erneut ausführen.

Sie können den Geltungsbereich für diese Rollenzuweisung mit dem Parameter **ResourceGroupName** auf eine bestimmte Ressourcengruppe festlegen. Sie können den Geltungsbereich durch die Verwendung der Parameter **ResourceType** und **ResourceName** auch auf eine bestimmte Ressource festlegen. 

Wenn Sie **nicht mit Windows 10 oder Windows Server 2016 arbeiten**, müssen Sie den [Generator für selbstsignierte Zertifikate](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) aus dem Skriptcenter von Microsoft herunterladen. Extrahieren Sie den Inhalt, und importieren Sie das benötigte Cmdlet.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Ersetzen Sie im Skript die folgenden zwei Zeilen, um das Zertifikat zu generieren.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Bereitstellen eines Zertifikats über automatisiertes PowerShell-Skript

Bei jeder Anmeldung als Dienstprinzipal müssen Sie die Mandanten-ID des Verzeichnisses für Ihre AD-App angeben. Ein Mandant ist eine Instanz von Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Erstellen eines Dienstprinzipals mit Zertifikat von der Zertifizierungsstelle

Im folgenden Beispiel wird ein von einer Zertifizierungsstelle ausgestelltes Zertifikat verwendet, um einen Dienstprinzipal zu erstellen. Die Zuweisung ist auf das angegebene Azure-Abonnement beschränkt. Der Dienstprinzipal wird der Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) hinzugefügt. Wenn während der Rollenzuweisung ein Fehler auftritt, wird versucht, die Zuweisung erneut durchzuführen.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Bereitstellen eines Zertifikats über automatisiertes PowerShell-Skript
Bei jeder Anmeldung als Dienstprinzipal müssen Sie die Mandanten-ID des Verzeichnisses für Ihre AD-App angeben. Ein Mandant ist eine Instanz von Azure AD.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Die Anwendungs-ID und die Mandanten-ID sind keine sensiblen Daten, sodass Sie sie direkt in Ihr Skript einbetten können. Wenn Sie die Mandanten-ID abrufen möchten, verwenden Sie Folgendes:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Wenn Sie die Anwendungs-ID abrufen möchten, verwenden Sie Folgendes:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Ändern von Anmeldeinformationen

Wenn Sie die Anmeldeinformationen für eine AD-App entweder aufgrund einer Gefährdung der Sicherheit oder aufgrund des Ablaufs der Anmeldeinformationen ändern müssen, verwenden Sie die Cmdlets [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) und [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential).

Zum Entfernen aller Anmeldeinformationen für eine Anwendung verwenden Sie Folgendes:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Zum Hinzufügen eines Zertifikatwerts erstellen Sie ein selbstsigniertes Zertifikat, wie in diesem Artikel gezeigt. Verwenden Sie anschließend Folgendes:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debuggen

Wenn Sie einen Dienstprinzipal erstellen, können folgende Fehler auftreten:

* **„Authentication_Unauthorized“** oder **„Kein Abonnement in diesem Kontext gefunden.“** – Dieser Fehler wird angezeigt, wenn Ihr Konto nicht über die [erforderlichen Berechtigungen](#required-permissions) verfügt, um eine App in Azure AD zu registrieren. In der Regel wird dieser Fehler angezeigt, wenn in Ihrem Azure Active Directory nur Administratorbenutzer Apps registrieren können und Ihr Konto kein Administratorkonto ist. Bitten Sie Ihren Administrator, Sie entweder einer Administratorrolle zuzuweisen, oder Benutzern zu ermöglichen, Apps zu registrieren.

* Ihr Konto **„hat keine Berechtigung zum Ausführen der Aktion 'Microsoft.Authorization/roleAssignments/write' über Bereich '/subscriptions/{guid}'.“**  – Dieser Fehler wird angezeigt, wenn Ihr Konto nicht über ausreichende Berechtigungen verfügt, um eine Rolle einer Identität zuzuweisen. Bitten Sie Ihren Abonnementadministrator, Sie der Rolle „Benutzerzugriffsadministrator“ hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Einrichtung eines Dienstprinzipals mit einem Kennwort finden Sie unter [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Ausführliche Schritte zum Integrieren einer Anwendung in Azure zur Verwaltung von Ressourcen finden Sie im [Entwicklerhandbuch für die Autorisierung mit der Azure Resource Manager-API](../../azure-resource-manager/resource-manager-api-authentication.md).
* Eine ausführlichere Erläuterung zu Anwendungen und Dienstprinzipalen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](app-objects-and-service-principals.md).
* Weitere Informationen zur Azure AD-Authentifizierung finden Sie unter [Was ist eine Authentifizierung?](authentication-scenarios.md).
