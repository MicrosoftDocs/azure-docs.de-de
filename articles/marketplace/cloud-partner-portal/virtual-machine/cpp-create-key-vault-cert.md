---
title: Erstellen eines Azure Key Vault-Zertifikats | Azure Marketplace
description: Dieser Artikel erläutert, wie Sie eine VM von einer in Azure bereitgestellten virtuellen Festplatte registrieren.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 5163aa0a9195aa712fa333667b3f7ccf227469be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938399"
---
# <a name="create-certificates-for-azure-key-vault"></a>Erstellen von Zertifikaten für Azure Key Vault

In diesem Artikel ist erläutert, wie die selbstsignierten Zertifikate bereitgestellt werden, die dazu benötigt werden, eine Windows-Remoteverwaltung-Verbindung (WinRM-Verbindung) mit einem unter Azure gehosteten virtuellen Computer herzustellen. Dieser Vorgang besteht aus drei Schritten:

1.  Erstellen Sie das Sicherheitszertifikat. 
2.  Erstellen Sie den Azure Key Vault, in dem dieses Zertifikat gespeichert werden soll. 
3.  Speichern Sie die Zertifikate in diesem Key Vault (Schlüsseltresor). 

Für diesen Vorgang können Sie entweder eine neue oder eine vorhandene Azure-Ressourcengruppe verwenden.  Der erstgenannte Ansatz wird in der folgenden Erläuterung verwendet.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Erstellen des Zertifikats

Bearbeiten Sie das folgende Azure PowerShell-Skript, und führen Sie es aus, um die Zertifikatsdatei (PFX) in einem lokalen Ordner zu erstellen.  Sie müssen die Werte der folgenden Parameter ersetzen:

|  **Parameter**        |   **Beschreibung**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Der lokale Ordner, in dem die PFX-Datei gespeichert werden soll  |
| `$location`    | Einer der geografischen Azure-Standardstandorte  |
| `$vmName`      | Der Name des geplanten virtuellen Azure-Computers   |
| `$certname`    | Der Name des Zertifikats; muss mit dem vollqualifizierten Domänennamen des geplanten virtuellen Computers übereinstimmen  |
| `$certpassword` | Das Kennwort für die Zertifikate; muss mit dem Kennwort für den geplanten virtuellen Computer übereinstimmen  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Verwenden Sie während dieser Schritte dieselbe PowerShell-Konsolensitzung, damit die Werte der verschiedenen Parameter erhalten bleiben.

> [!WARNING]
> Wenn Sie dieses Skript speichern, sollten Sie es nur in einem sicheren Ort speichern, da es Sicherheitsinformationen (ein Kennwort) enthält.


## <a name="create-the-key-vault"></a>Erstellen des Key Vaults (Schlüsseltresors)

Kopieren Sie den Inhalt der [Key Vault-Bereitstellungsvorlage](./cpp-key-vault-deploy-template.md) in eine Datei auf Ihrem lokalen Computer. (Im folgenden Beispielskript ist diese Ressource `C:\certLocation\keyvault.json`.)  Bearbeiten Sie das folgende Azure PowerShell-Skript, und führen Sie es aus, um eine Azure Key Vault-Instanz und die zugeordnete Ressourcengruppe zu erstellen.  Sie müssen die Werte der folgenden Parameter ersetzen:

|  **Parameter**        |   **Beschreibung**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Beliebige numerische Zeichenfolge, die an die Bereitstellungs-IDs angefügt wird                     |
| `$rgName`             | Der Name der zu erstellenden Azure-Ressourcengruppe (RG)                                        |
|  `$location`          | Einer der geografischen Azure-Standardstandorte                                  |
| `$kvTemplateJson`     | Der Pfad der Datei (keyvault.json), die die Resource Manager-Vorlage für den Key Vault enthält |
| `$kvname`             | Der Name des neuen Key Vaults (Schlüsseltresors)                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Speichern des Zertifikatspeichers

Sie können das in der PFX-Datei enthaltene Zertifikat jetzt im neuen Key Vault speichern, indem Sie das folgende Skript ausführen. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
    "@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Nächste Schritte

Als Nächstes [stellen Sie eine Azure-VM von einer Benutzer-VHD bereit](./cpp-deploy-vm-user-image.md).
