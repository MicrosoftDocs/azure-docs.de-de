---
title: Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Linux-Cluster | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten unter Linux mit Azure Resource Manager und Azure Key Vault.
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
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258760"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten unter Linux 
> [!div class="op_single_selector"]
> * [Datenträgerverschlüsselung für Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Datenträgerverschlüsselung für Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

In diesem Tutorial erfahren Sie, wie Sie die Datenträgerverschlüsselung auf Azure Service Fabric-Clusterknoten unter Linux aktivieren. Sie müssen diese Schritte für alle Knotentypen und VM-Skalierungsgruppen befolgen. Für die Verschlüsselung der Knoten verwenden wir die Azure Disk Encryption-Funktion für VM-Skalierungsgruppen.

In dem Leitfaden werden die folgenden Themen behandelt:

* Schlüsselkonzepte, die Sie zum Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Cluster unter Linux berücksichtigen müssen.
* Schritte, die Sie vor dem Aktivieren der Datenträgerverschlüsselung auf Service Fabric-Clusterknoten unter Linux ausführen müssen.
* Schritte, die Sie zum Aktivieren der Datenträgerverschlüsselung auf Service Fabric-Clusterknoten unter Linux ausführen müssen.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

 **Selbstregistrierung**

Für die Vorschau auf die Datenträgerverschlüsselung für die VM-Skalierungsgruppe ist eine Selbstregistrierung erforderlich. Führen Sie die folgenden Schritte aus:

1. Führen Sie den folgenden Befehl aus: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Warten Sie etwa 10 Minuten, bis der Status *Registriert* anzeigt. Sie können den Status überprüfen, indem Sie den folgenden Befehl ausführen:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Erstellen Sie einen Schlüsseltresor im selben Abonnement und in derselben Region wie die Skalierungsgruppe. Wählen Sie dann die Zugriffsrichtlinie **EnabledForDiskEncryption** für den Schlüsseltresor mit seinem PowerShell-Cmdlet aus. Sie können die Richtlinie auch unter Verwendung der Key Vault-Benutzeroberfläche im Azure-Portal mit folgendem Befehl festlegen:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installieren Sie die aktuellste Version der [Azure CLI](/cli/azure/install-azure-cli), die über die neuen Verschlüsselungsbefehle verfügt.

3. Installieren Sie die aktuelle Version des [Azure SDK aus dem Azure PowerShell-Release](https://github.com/Azure/azure-powershell/releases). Es folgen die Azure Disk Encryption-Cmdlets (ADE) der VM-Skalierungsgruppe zum Aktivieren ([set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) der Verschlüsselung, Abrufen ([get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) des Verschlüsselungsstatus und Entfernen ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) der Verschlüsselung von der Skalierungsgruppeninstanz.


| Get-Help | Version |  `Source`  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 oder höher | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 oder höher | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Unterstützte Szenarien für die Datenträgerverschlüsselung
* Die Verschlüsselung von VM-Skalierungsgruppen wird nur für Skalierungsgruppen mit verwalteten Datenträgern unterstützt. Sie wird nicht für native (oder nicht verwaltete) Datenträger-Skalierungsgruppen unterstützt.
* Die Verschlüsselung und Deaktivierung der Verschlüsselung wird bei Betriebssystem- und Datenvolumes für VM-Skalierungsgruppen unter Linux unterstützt. 
* Reimaging- und Upgradevorgänge von virtuellen Computern (VMs) für VM-Skalierungsgruppen werden in der aktuellen Vorschauversion nicht unterstützt.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Erstellen eines neuen Clusters und Aktivieren der Datenträgerverschlüsselung

Verwenden Sie die folgenden Befehle, um Cluster zu erstellen und die Datenträgerverschlüsselung mit Azure Resource Manager-Vorlage und selbstsigniertem Zertifikat zu aktivieren.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure  

Melden Sie sich mit den folgenden Befehlen an:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Verwenden einer bereits vorhandenen benutzerdefinierten Vorlage 

Wenn Sie eine benutzerdefinierte Vorlage erstellen müssen, empfehlen wir Ihnen dringend, eine der Vorlagen auf der Seite [Beispiele für Azure Service Fabric-Clustererstellungsvorlagen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) zu verwenden. 

Wenn Sie bereits über eine benutzerdefinierte Vorlage verfügen, überprüfen Sie nochmals, dass alle drei zertifikatbezogenen Parameter in der Vorlage und der Parameterdatei wie folgt benannt werden. Stellen Sie außerdem sicher, dass die Werte wie folgt NULL sind.

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

Da nur die Datenträgerverschlüsselung für VM-Skalierungsgruppen unter Linux unterstützt wird, müssen Sie einen Datenträger mithilfe einer Resource Manager-Vorlage hinzufügen. Aktualisieren Sie die Vorlage für die Bereitstellung des Datenträgers wie folgt:

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

Im Folgenden sehen Sie den entsprechenden CLI-Befehl. Ändern Sie die Werte in den declare-Anweisungen in die entsprechenden Werte. Die CLI unterstützt alle anderen Parameter, die vom zuvor genannten PowerShell-Befehl unterstützt werden.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Einbinden eines Datenträgers in eine Linux-Instanz
Bevor Sie mit der Verschlüsselung für eine VM-Skalierungsgruppe fortfahren, vergewissern Sie sich, dass der hinzugefügte Datenträger ordnungsgemäß eingebunden ist. Melden Sie sich bei der Linux-Cluster-VM an, und führen Sie den **LSBLK**-Befehl aus. Die Ausgabe sollte diesen hinzugefügten Datenträger in der Spalte für den **Bereitstellungspunkt** anzeigen.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Bereitstellen einer Anwendung in einem Service Fabric-Cluster unter Linux
Befolgen Sie zum Bereitstellen einer Anwendung in Ihrem Cluster die Schritte und Anleitungen unter [Schnellstart: Bereitstellen von Linux-Containern in Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Aktivieren der Datenträgerverschlüsselung für die zuvor erstellten VM-Skalierungsgruppen
Führen Sie die folgenden Befehle aus, um die Datenträgerverschlüsselung für die VM-Skalierungsgruppen zu aktivieren, die Sie in den vorherigen Schritten erstellt haben:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Vergewissern Sie sich, dass die Datenträgerverschlüsselung für eine VM-Skalierungsgruppe unter Linux aktiviert ist.
Rufen Sie mit den folgenden Befehlen den Status einer gesamten VM-Skalierungsgruppe bzw. einer beliebigen Instanz in einer Skalierungsgruppe ab.
Sie können sich auch bei der Linux-Cluster-VM anmelden und den **LSBLK**-Befehl ausführen. Die Ausgabe sollte den hinzugefügten Datenträger in der Spalte für den **Bereitstellungspunkt** und die Spalte für den **Typ** als *Crypt* anzeigen.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Deaktivieren der Datenträgerverschlüsselung für eine VM-Skalierungsgruppe in einem Service Fabric-Cluster
Deaktivieren Sie die Datenträgerverschlüsselung für eine VM-Skalierungsgruppe, indem Sie die folgenden Befehle ausführen. Beachten Sie, dass sich das Deaktivieren der Datenträgerverschlüsselung auf die gesamte VM-Skalierungsgruppe und nicht auf eine einzelne Instanz bezieht.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nächste Schritte
An diesem Punkt sollten Sie über einen sicheren Cluster verfügen und wissen, wie Sie die Datenträgerverschlüsselung für Service Fabric-Clusterknoten und VM-Skalierungsgruppen aktivieren und deaktivieren. Eine ähnliche Anleitung zu Service Fabric-Clusterknoten unter Linux finden Sie unter [Datenträgerverschlüsselung für Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
