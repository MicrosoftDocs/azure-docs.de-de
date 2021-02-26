---
title: 'Bereitstellen eines Clouddiensts (erweiterter Support): PowerShell'
description: Bereitstellen eines Clouddiensts (erweiterter Support) mithilfe von PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 08a8dde815a6dea5d69e5e2a385cbaa03fba681a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832693"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Bereitstellen eines Clouddiensts (erweiterter Support) mithilfe von Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie das PowerShell-Modul `Az.CloudService` verwenden, um Cloud Services (erweiterter Support) mit mehreren Rollen („WebRole“ und „WorkerRole“) und mit Remotedesktoperweiterung in Azure bereitzustellen. 

> [!IMPORTANT]
> Cloud Services (erweiterter Support) befindet sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Vorbereitung

Informieren Sie sich über die [Bereitstellungsvoraussetzungen](deploy-prerequisite.md) für Cloud Services (erweiterter Support), und erstellen Sie die entsprechenden Ressourcen. 

## <a name="deploy-a-cloud-services-extended-support"></a>Bereitstellen einer Instanz von Cloud Services (erweiterter Support)
1. Installieren Sie das PowerShell-Modul „Az.CloudService“  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Erstellen einer neuen Ressourcengruppe Bei Verwendung einer vorhandenen Ressourcengruppe ist dieser Schritt optional.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Erstellen Sie ein Speicherkonto und einen Container zum Speichern der Clouddienstpaket-Datei (.cspkg) und der Dienstkonfigurationsdatei (.cscfg). Für das Speicherkonto muss ein eindeutiger Name verwendet werden. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Laden Sie Ihr Clouddienstpaket (.cspkg) in das Speicherkonto hoch.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Laden Sie Ihre Clouddienstkonfiguration (.cscfg) in das Speicherkonto hoch. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Erstellen Sie ein virtuelles Netzwerk und ein Subnetz. Bei Verwendung eines vorhandenen Netzwerks und Subnetzes ist dieser Schritt optional. In diesem Beispiel werden für beide Clouddienstrollen („WebRole“ und „WorkerRole“) ein einzelnes virtuelles Netzwerk und ein einzelnes Subnetz verwendet. 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Erstellen Sie eine öffentliche IP-Adresse, und legen Sie (optional) die DNS-Bezeichnungseigenschaft der öffentlichen IP-Adresse fest. Wenn Sie eine statische IP-Adresse verwenden, muss darauf in der Dienstkonfigurationsdatei als reservierte IP verwiesen werden.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Erstellen Sie das Netzwerkprofilobjekt, und ordnen Sie die öffentliche IP-Adresse dem Front-End des durch die Plattform erstellten Lastenausgleichs zu.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Erstellen einer Key Vault-Instanz Diese Key Vault-Instanz wird zum Speichern von Zertifikaten verwendet, die den Rollen des Clouddiensts (erweiterter Support) zugeordnet sind. Vergewissern Sie sich, dass Sie „Zugriffsrichtlinien“ (im Portal) für den Zugriff auf „Azure Virtual Machines für Bereitstellung“ und „Azure Resource Manager für Vorlagenbereitstellung“ aktiviert haben. Die Key Vault-Instanz muss in der gleichen Region und im gleichen Abonnement erstellt werden wie der Clouddienst und einen eindeutigen Namen besitzen. Weitere Informationen finden Sie unter [Verwenden von Zertifikaten mit Azure Cloud Services (erweiterter Support)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Aktualisieren Sie die Key Vault-Zugriffsrichtlinie, und erteilen Sie Ihrem Benutzerkonto Zertifikatberechtigungen. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Alternativ können Sie die Zugriffsrichtlinie per Objekt-ID festlegen. (Diese ID kann durch Ausführen von `Get-AzADUser` abgerufen werden.) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Im Rahmen dieses Beispiels fügen wir einer Key Vault-Instanz ein selbstsigniertes Zertifikat hinzu. Der Zertifikatfingerabdruck muss in der Clouddienst-Konfigurationsdatei (.cscfg) für die Bereitstellung in Clouddienstrollen hinzugefügt werden. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Erstellen Sie ein In-Memory-Objekt vom Typ „Betriebssystemprofil“. Das Betriebssystemprofil gibt die Zertifikate an, die Clouddienstrollen zugeordnet sind. Hierbei handelt es sich um das Zertifikat, das im vorherigen Schritt erstellt wurde. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Erstellen Sie ein In-Memory-Objekt vom Typ „Rollenprofil“. Das Rollenprofil definiert SKU-spezifische Eigenschaften einer Rolle wie Name, Kapazität und Ebene. In diesem Beispiel wurden zwei Rollen definiert: „frontendRole“ und „backendRole“. Die Rollenprofilinformationen müssen der Rollenkonfiguration entsprechen, die in der Konfigurationsdatei (.cscfg) und in der Dienstdefinitionsdatei (.csdef) definiert ist. 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. Optional: Erstellen Sie ein In-Memory-Objekt vom Typ „Erweiterungsprofil“, das Sie Ihrem Clouddienst hinzufügen möchten. In diesem Beispiel wird die RDP-Erweiterung hinzugefügt. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Beachten Sie, dass „configFile“ nur über Tags vom Typ „PublicConfig“ verfügen und etwa folgenden Namespace enthalten sollte:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. Optional: Definieren Sie Tags als PowerShell-Hashtabelle, die Sie Ihrem Clouddienst hinzufügen möchten. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Erstellen Sie eine Clouddienstbereitstellung mithilfe von Profilobjekten und SAS-URLs.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Nächste Schritte 
- Sehen Sie sich die [häufig gestellten Fragen](faq.md) zu Cloud Services (erweiterter Support) an.
- Stellen Sie einen Clouddienst (erweiterter Support) über das [Azure-Portal](deploy-portal.md), per [PowerShell](deploy-powershell.md), per [Vorlage](deploy-template.md) oder mithilfe von [Visual Studio](deploy-visual-studio.md) bereit.
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
