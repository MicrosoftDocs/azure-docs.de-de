---
title: Einrichten von Azure Attestation mithilfe von Azure PowerShell
description: Hier erfahren Sie, wie Sie einen Nachweisanbieter mithilfe von Azure PowerShell einrichten und konfigurieren.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3c908fffe93d657662310d4e91ed25ae28ca6908
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429091"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Schnellstart: Einrichten von Azure Attestation mithilfe von Azure PowerShell

Führen Sie die folgenden Schritte aus, um einen Nachweisanbieter mithilfe von Azure PowerShell einzurichten und zu konfigurieren. Weitere Informationen zum Installieren und Ausführen von Azure PowerShell finden Sie unter [Dokumentation zu Azure PowerShell](/powershell/azure/?view=azps-2.8.0&viewFallbackFrom=azps-2.4.0).

Beachten Sie, dass im PowerShell-Katalog die TLS-Versionen (Transport Layer Security) 1.0 und 1.1 als veraltet markiert sind. Empfohlen wird TLS 1.2 oder eine höhere Version. Daher werden unter Umständen die folgenden Fehler angezeigt:

- WARNUNG: Unable to resolve package source "https://www.powershellgallery.com/api/v2" (Die Paketquelle "https://www.powershellgallery.com/api/v2" kann nicht aufgelöst werden.)
- PackageManagement\Install-Package: No match was found for the specified search criteria and module name (Für die angegebenen Suchkriterien und den angegebenen Modulnamen wurde keine Übereinstimmung gefunden.) 

Führen Sie den folgenden Befehl vor den Befehlen vom Typ „Install-Module“ aus, um weiterhin mit dem PowerShell-Katalog zu interagieren.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Installieren des PowerShell-Moduls „Az.Attestation“

Installieren Sie auf dem Computer mit Azure PowerShell das PowerShell-Modul „Az.Attestation“, das Cmdlets für Azure Attestation enthält.  

### <a name="initial-installation"></a>Erstinstallation

Schließen Sie alle vorhandenen PowerShell-Fenster.

Starten Sie zum Installieren für den aktuellen Benutzer (current user) ein PowerShell-Fenster ohne erhöhte Rechte, und führen Sie Folgendes aus:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Starten Sie zum Installieren für alle Benutzer (all users) ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie Folgendes aus:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Schließen Sie die PowerShell-Konsole mit erhöhten Rechten.

### <a name="update-the-installation"></a>Aktualisieren der Installation

Schließen Sie alle vorhandenen PowerShell-Fenster.

Starten Sie zum Aktualisieren für den aktuellen Benutzer (current user) ein PowerShell-Fenster ohne erhöhte Rechte, und führen Sie Folgendes aus:

```powershell
Update-Module -Name Az.Attestation
```

Starten Sie zum Aktualisieren für alle Benutzer (all users) ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie Folgendes aus:

```powershell
Update-Module -Name Az.Attestation
```

Schließen Sie die PowerShell-Konsole mit erhöhten Rechten.

### <a name="get-installed-modules"></a>Abrufen installierter Module

Mindestens erforderliche Version der Az-Module zur Unterstützung von Nachweisvorgängen:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Führen Sie den folgenden Befehl aus, um die installierte Version aller Az-Module zu überprüfen: 

```powershell
Get-InstalledModule
```
Wenn die Versionen nicht die Mindestanforderungen erfüllen, führen Sie Befehle vom Typ „Update-Module“ aus.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich in der PowerShell-Konsole bei Azure an (ohne erhöhte Zugriffsrechte).

```powershell
Connect-AzAccount
```

Wechseln Sie bei Bedarf zu dem Abonnement, das für Azure Attestation verwendet werden soll.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.Attestation“

Registrieren Sie den Ressourcenanbieter „Microsoft.Attestation“ im Abonnement. Weitere Informationen zu Azure-Ressourcenanbietern sowie zum Konfigurieren und Verwalten von Ressourcenanbietern finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md). Beachten Sie, dass ein Ressourcenanbieter nur einmal für ein Abonnement registriert werden muss.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Regionale Verfügbarkeit von Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

Erstellen Sie eine Ressourcengruppe für den Nachweisanbieter. Beachten Sie, dass andere Azure-Ressourcen (einschließlich eines virtuellen Computers mit einer Clientanwendungsinstanz) in derselben Ressourcengruppe platziert werden können.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Erstellen und Verwalten eines Nachweisanbieters

Mit „New-AzAttestation“ wird ein Nachweisanbieter erstellt.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

„PolicySignerCertificateFile“ ist eine Datei, die mehrere vertrauenswürdige Signaturschlüssel angibt. Wenn für den Parameter „PolicySignerCertificateFile“ ein Dateiname angegeben wird, kann der Nachweisanbieter nur mit Richtlinien im signierten JWT-Format konfiguriert werden. Andernfalls kann die Richtlinie als Text oder nicht signiertes JWT konfiguriert werden.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Ein Beispiel für „PolicySignersCertificateFile“ finden Sie unter [Beispiele für ein Signaturgeberzertifikat einer Nachweisrichtlinie](policy-signer-examples.md).

Mit „Get-AzAttestation“ werden die Nachweisanbietereigenschaften wie „status“ und „AttestURI“ abgerufen. Notieren Sie sich den Wert für „AttestURI“. Sie benötigen ihn später.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Der obige Befehl sollte eine Ausgabe wie die folgende ergeben: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Nachweisanbieter können mithilfe des Cmdlets „Remove-AzAttestation“ gelöscht werden.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Richtlinienverwaltung

Für die Verwaltung von Richtlinien benötigt ein Azure AD-Benutzer die folgenden Berechtigungen für „Aktionen“:
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

Diese Berechtigungen können einem AD-Benutzer über eine Rolle wie „Besitzer“ (Platzhalterberechtigungen), „Mitwirkender“ (Platzhalterberechtigungen) oder „Mitwirkender an Nachweis“ (spezifische Berechtigungen nur für Azure Attestation) zugewiesen werden.  

Für das Lesen von Richtlinien benötigt ein Azure AD-Benutzer die folgenden Berechtigungen für „Aktionen“:
- Microsoft.Attestation/attestationProviders/attestation/read

Diese Berechtigung kann einem AD-Benutzer über eine Rolle wie „Leser“ (Platzhalterberechtigungen) oder „Nachweisleser“ (spezifische Berechtigungen nur für Azure Attestation) zugewiesen werden.

Die folgenden PowerShell-Cmdlets ermöglichen die Richtlinienverwaltung für einen Nachweisanbieter (jeweils eine TEE):

„Get-AzAttestationPolicy“ gibt die aktuelle Richtlinie für die angegebene TEE zurück. Das Cmdlet zeigt die Richtlinie sowohl im Text- als auch im JWT-Format der Richtlinie an.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Unterstützte TEE-Typen sind „SgxEnclave“, „OpenEnclave“ und „VbsEnclave“.

Mit „Set-AttestationPolicy“ wird eine neue Richtlinie für die angegebene TEE festgelegt. Das Cmdlet akzeptiert Richtlinien im Text- oder JWT-Format und wird durch den Parameter „PolicyFormat“ gesteuert. Der Standardwert für „PolicyFormat“ lautet „Text“. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Wird während der Erstellung eines Nachweisanbieters „PolicySignerCertificateFile“ angegeben, können Richtlinien nur im signierten JWT-Format konfiguriert werden. Andernfalls kann die Richtlinie als Text oder nicht signiertes JWT konfiguriert werden.

Die Nachweisrichtlinie im JWT-Format muss einen Anspruch namens „AttestationPolicy“ enthalten. Bei einer signierten Richtlinie muss das JWT mit einem privaten Schlüssel signiert werden, der einem der vorhandenen Signaturgeberzertifikate für eine Richtlinie entspricht.

Richtlinienbeispiele finden Sie unter [Beispiele für eine Nachweisrichtlinie](policy-examples.md).

Mit „Reset-AzAttestationPolicy“ wird die Richtlinie auf den Standardwert für die angegebene TEE zurückgesetzt.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Verwaltung der Signaturgeberzertifikate einer Richtlinie

Die folgenden PowerShell-Cmdlets ermöglichen die Verwaltung der Signaturgeberzertifikate einer Richtlinie für einen Nachweisanbieter:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Das Signaturgeberzertifikat einer Richtlinie ist ein signiertes JWT mit einem Anspruch namens „maa-policyCertificate“. Der Wert des Anspruchs ist ein JWK, der den vertrauenswürdigen Signaturschlüssel enthält, der hinzugefügt werden soll. Das JWT muss mit einem privaten Schlüssel signiert werden, der einem der vorhandenen Signaturgeberzertifikate für eine Richtlinie entspricht.

Beachten Sie, dass die gesamte semantische Bearbeitung des Signaturgeberzertifikats der Richtlinie außerhalb von PowerShell erfolgen muss. In PowerShell handelt es sich um eine einfache Zeichenfolge.

Ein Beispiel für ein Signaturgeberzertifikat der Richtlinie finden Sie unter [Beispiele für ein Signaturgeberzertifikat einer Nachweisrichtlinie](policy-signer-examples.md).

Weitere Informationen zu den Cmdlets und den zugehörigen Parametern finden Sie im Artikel mit den [PowerShell-Cmdlets für Azure Attestation](/powershell/module/az.attestation/?view=azps-4.3.0#attestation). 

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Erbringen eines Nachweises für eine SGX-Enclave mithilfe von Codebeispielen](/samples/browse/?expanded=azure&terms=attestation)
