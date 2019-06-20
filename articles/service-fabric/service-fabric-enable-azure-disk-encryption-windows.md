---
title: Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Windows-Cluster | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten mithilfe von Azure Key Vault in Azure Resource Manager.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471414"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten unter Windows 
> [!div class="op_single_selector"]
> * [Datenträgerverschlüsselung für Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Datenträgerverschlüsselung für Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

In diesem Tutorial erfahren Sie, wie Sie die Datenträgerverschlüsselung für Service Fabric-Clusterknoten unter Windows aktivieren. Diese Schritte müssen für alle Knotentypen und VM-Skalierungsgruppen ausgeführt werden. Für die Verschlüsselung der Knoten verwenden wir die Azure Disk Encryption-Funktion für VM-Skalierungsgruppen.

Der Leitfaden behandelt folgende Themen:

* Schlüsselkonzepte, mit denen Sie beim Aktivieren der Datenträgerverschlüsselung für VM-Skalierungsgruppen im Service Fabric-Cluster unter Windows vertraut sein müssen.
* Schritte, die Sie vor dem Aktivieren der Datenträgerverschlüsselung für Service Fabric-Clusterknoten unter Windows ausführen müssen.
* Schritte, die Sie zum Aktivieren der Datenträgerverschlüsselung für Service Fabric-Clusterknoten unter Windows ausführen müssen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

**Selbstregistrierung** 

Die Vorschauversion der Datenträgerverschlüsselung für die VM-Skalierungsgruppe erfordert eine Selbstregistrierung. Führen Sie die folgenden Schritte aus: 

1. Führen Sie zunächst den folgenden Befehl aus:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Warten Sie etwa zehn Minuten, bis der Status *Registriert* angezeigt wird. Sie können den Status überprüfen, indem Sie den folgenden Befehl ausführen: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Erstellen Sie einen Schlüsseltresor im gleichen Abonnement und in der gleichen Region wie die Skalierungsgruppe, und wählen Sie für den Schlüsseltresor mithilfe des zugehörigen PowerShell-Cmdlets die Zugriffsrichtlinie **EnabledForDiskEncryption** aus. Sie können die Richtlinie auch mit folgendem Befehl über die Key Vault-Benutzeroberfläche im Azure-Portal festlegen:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installieren Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), da diese über die neuen Verschlüsselungsbefehle verfügt.
3. Installieren Sie die neueste Version des [Azure SDK aus dem Azure PowerShell-Release](https://github.com/Azure/azure-powershell/releases). Es folgen die Azure Disk Encryption-Cmdlets (ADE) der VM-Skalierungsgruppe zum Aktivieren ([set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) der Verschlüsselung, Abrufen ([get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) des Verschlüsselungsstatus und Entfernen ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) der Verschlüsselung aus der Skalierungsgruppeninstanz.

| Get-Help | Version |  `Source`  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 oder höher | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 oder höher | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 oder höher | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Unterstützte Szenarien für die Datenträgerverschlüsselung
* Die Verschlüsselung für VM-Skalierungsgruppen wird nur für Skalierungsgruppen mit verwalteten Datenträgern unterstützt. Sie wird nicht für native (oder nicht verwaltete) Datenträger-Skalierungsgruppen unterstützt.
* Die Verschlüsselung wird für Betriebssystem- und Datenvolumes in VM-Skalierungsgruppen unter Windows unterstützt. Das Deaktivieren der Verschlüsselung wird ebenfalls für Betriebssystem- und Datenvolumes für VM-Skalierungsgruppen unter Windows unterstützt.
* Reimaging- und Upgradevorgänge von virtuellen Computern für VM-Skalierungsgruppen werden in der aktuellen Vorschauversion nicht unterstützt.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Erstellen eines neuen Clusters und Aktivieren der Datenträgerverschlüsselung

Verwenden Sie die folgenden Befehle, um Cluster zu erstellen und die Datenträgerverschlüsselung mit Azure Resource Manager-Vorlage und selbstsigniertem Zertifikat zu aktivieren.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure 
Melden Sie sich mit den folgenden Befehlen an:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Verwenden einer bereits vorhandenen benutzerdefinierten Vorlage 

Wenn Sie eine benutzerdefinierte Vorlage für Ihre speziellen Anforderungen erstellen müssen, wird dringend empfohlen, mit einer der Vorlagen zu beginnen, die auf der [Seite mit Beispielen für Azure Service Fabric-Clustererstellungsvorlagen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) zur Verfügung stehen. Eine Anleitung zum [Anpassen Ihrer Clustervorlage][customize-your-cluster-template] finden Sie im weiteren Verlauf.

Wenn Sie bereits über eine benutzerdefinierte Vorlage verfügen, überprüfen Sie, ob alle drei zertifikatbezogenen Parameter in der Vorlage und der Parameterdatei wie folgt benannt und die Werte NULL sind:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Bereitstellen einer Anwendung in einem Service Fabric-Cluster unter Windows
Gehen Sie wie unter [Bereitstellen und Entfernen von Anwendungen mit PowerShell](service-fabric-deploy-remove-applications.md) beschrieben vor, um eine Anwendung in Ihrem Cluster bereitzustellen.


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Vergewissern, dass die Datenträgerverschlüsselung für eine VM-Skalierungsgruppe unter Windows aktiviert ist
Rufen Sie mit den folgenden Befehlen den Status einer gesamten VM-Skalierungsgruppe oder einer beliebigen Instanz in einer Skalierungsgruppe ab.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Darüber hinaus können Sie sich bei der VM-Skalierungsgruppe anmelden und sich vergewissern, dass die Laufwerke verschlüsselt sind.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Deaktivieren der Datenträgerverschlüsselung für eine VM-Skalierungsgruppe in einem Service Fabric-Cluster 
Deaktivieren Sie die Datenträgerverschlüsselung für eine VM-Skalierungsgruppe, indem Sie die folgenden Befehle ausführen. Beachten Sie, dass sich das Deaktivieren der Datenträgerverschlüsselung auf die gesamte VM-Skalierungsgruppe und nicht auf eine einzelne Instanz bezieht.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nächste Schritte
An diesem Punkt sollten Sie über einen sicheren Cluster verfügen und wissen, wie Sie die Datenträgerverschlüsselung für Service Fabric-Clusterknoten und VM-Skalierungsgruppen aktivieren und deaktivieren. Eine ähnliche Anleitung für Service Fabric-Clusterknoten unter Linux finden Sie unter [Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Clusterknoten unter Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
