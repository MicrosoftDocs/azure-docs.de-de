---
title: Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Linux-Cluster | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Aktivieren der Datenträgerverschlüsselung für Service Fabric-Clusterskalierungsgruppen in Azure mit Azure Resource Manager, Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161787"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Aktivieren der Datenträgerverschlüsselung für Service Fabric--Linux-Clusterknoten 
> [!div class="op_single_selector"]
> * [Datenträgerverschlüsselung für Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Datenträgerverschlüsselung für Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Gehen Sie folgendermaßen vor, um die Datenträgerverschlüsselung auf Service Fabric-Linux-Clusterknoten zu aktivieren. Sie müssen diese Schritte für alle Knotentypen/VM-Skalierungsgruppen ausführen. Für die Verschlüsselung der Knoten wird die Azure Disk Encryption-Funktion auf VM-Skalierungsgruppen eingesetzt.

In diesem Leitfaden werden folgende Verfahren behandelt:

* Schlüsselkonzepte, die Sie zum Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Linux-Cluster berücksichtigen müssen.
* Vorbereitende Schritte, die Sie vor dem Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Linux-Cluster ausführen müssen.
* Schritte, die Sie zum Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Linux-Cluster ausführen müssen.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

* **Selbstregistrierung**: Zur Verwendung der Vorschau der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe ist eine Selbstregistrierung erforderlich.
* Sie können Ihr Abonnement mit folgenden Schritten selbst registrieren: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Warten Sie ca. 10 Minuten, bis der Status „Registriert“ angezeigt wird. Sie können den Status überprüfen, indem Sie den folgenden Befehl ausführen: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault**: Erstellen Sie einen Schlüsseltresor im selben Abonnement und derselben Region wie die VM-Skalierungsgruppe, und legen Sie als Zugriffsrichtlinie auf dem Schlüsseltresor mit seinem PS-Cmdlet „EnabledForDiskEncryption“ fest. Sie können die Richtlinie auch unter Verwendung der KeyVault-Benutzeroberfläche im Azure-Portal festlegen: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Installieren Sie die aktuellste [Azure CLI](/cli/azure/install-azure-cli) mit den neuen Verschlüsselungsbefehlen.
* Installieren Sie die aktuelle Version des [Azure SDK aus dem Azure PowerShell-Release](https://github.com/Azure/azure-powershell/releases). Es folgen die ADE-Cmdlets der VM-Skalierungsgruppe zum Aktivieren ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) der Verschlüsselung, Abrufen ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) des Verschlüsselungsstatus und Entfernen ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) der Verschlüsselung von der Skalierungsgruppeninstanz. 

| Get-Help | Version |  `Source`  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 oder höher | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 oder höher | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Unterstützte Szenarien für die Datenträgerverschlüsselung
* VM-Skalierungsgruppenverschlüsselung wird nur für Skalierungsgruppen unterstützt, die mit verwalteten Datenträgern erstellt wurden, nicht für native oder (nicht verwaltete) Datenträger-Skalierungsgruppen.
* VM-Skalierungsgruppenverschlüsselung wird bei Datenvolumes für Linux-VM-Skalierungsgruppen unterstützt. Die Betriebssystem-Datenträgerverschlüsselung wird in der aktuellen Vorschau für Linux NICHT unterstützt.
* Reimaging- und Upgradevorgänge von VMs aus VM-Skalierungsgruppen werden in der aktuellen Vorschauversion nicht unterstützt.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Erstellen neuer Linux-Cluster und Aktivieren der Datenträgerverschlüsselung

Verwenden Sie die folgenden Befehle, um Cluster zu erstellen und die Datenträgerverschlüsselung mit Azure Resource Manager-Vorlage und selbstsigniertem Zertifikat zu aktivieren.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Verwenden einer bereits vorhandenen benutzerdefinierten Vorlage 

Wenn Sie eine benutzerdefinierte Vorlage für Ihre speziellen Anforderungen erstellen müssen, wird dringend empfohlen, dass Sie mit einer der Vorlagen beginnen, die unter den [Azure Service Fabric-Vorlagenbeispielen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) für Linux-Cluster verfügbar sind. 

Wenn Sie bereits über eine benutzerdefinierte Vorlage verfügen, überprüfen Sie, ob alle drei zertifikatbezogenen Parameter in der Vorlage und der Parameterdatei wie folgt benannt wurden und die Werte wie folgt NULL sind.

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Da für die Linux-VM-Skalierungsgruppe nur die Verschlüsselung des Datenträgers mit den zu verwaltenden Daten, nicht des Betriebssystem-Datenträgers, unterstützt wird, müssen wir diesen Datenträger mit der Azure Resource Manager-Vorlage hinzufügen. Aktualisieren Sie die Vorlage für die Bereitstellung des Datenträgers wie folgt:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Im Folgenden sehen Sie den entsprechenden CLI-Befehl hierfür. Ändern Sie die Werte in den declare-Anweisungen in die entsprechenden Werte. Die CLI unterstützt alle anderen Parameter, die vom oben genannten Powershell-Befehl unterstützt werden.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="linux-data-disk-mounting"></a>Einbinden des Linux-Datenträgers
Bevor wir mit der Verschlüsselung der Linux-VM-Skalierungsgruppe fortfahren, müssen wir sicherstellen, dass der hinzugefügte Datenträger ordnungsgemäß eingebunden ist. Melden Sie sich bei der Linux-Cluster-VM an, und führen Sie den LSBLK-Befehl aus. Die Ausgabe sollte diesen hinzugefügten Datenträger in der Bereitstellungspunktspalte anzeigen.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Bereitstellen einer Anwendung in einem Service Fabric-Linux-Cluster
Führen Sie die Schritte sowie Anleitungen zum [Bereitstellen einer Anwendung in Ihrem Cluster](service-fabric-quickstart-containers-linux.md) aus.


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Aktivieren der Datenträgerverschlüsselung für die oben erstellte Service Fabric-Linux-Cluster-VM-Skalierungsgruppe
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Vergewissern Sie sich, dass die Datenträgerverschlüsselung für Linux-VM-Skalierungsgruppen aktiviert ist.
Rufen Sie den Status einer gesamten VM-Skalierungsgruppe bzw. einer beliebigen Instanz-VM in der Skalierungsgruppe ab. Siehe nachstehende Befehle.
Zusätzlich kann sich der Benutzer bei der Linux-Cluster-VM anmelden und den LSBLK-Befehl ausführen. Die Ausgabe sollte diesen hinzugefügten Datenträger in der Bereitstellungspunktspalte anzeigen, und in der Spalte „Typ“ sollte für den hinzugefügten Datenträger „Crypt“ angezeigt werden.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Deaktivieren der Datenträgerverschlüsselung für die Service Fabric-Cluster-VM-Skalierungsgruppe 
Das Deaktivieren der Datenträgerverschlüsselung bezieht sich auf die gesamte VM-Skalierungsgruppe und nicht einzelne Instanzen. 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nächste Schritte
An diesem Punkt haben Sie einen sicheren Cluster und wissen, wie Sie die Datenträgerverschlüsselung für die Service Fabric-Linux-Cluster-VM-Skalierungsgruppe aktivieren/deaktivieren. Als Nächstes folgt die [Datenträgerverschlüsselung für Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
