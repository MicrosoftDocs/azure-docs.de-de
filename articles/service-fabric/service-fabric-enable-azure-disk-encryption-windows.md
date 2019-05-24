---
title: Aktivieren der Datenträgerverschlüsselung für Azure Service Fabric-Windows-Cluster | Microsoft-Dokumentation
description: Dieser Artikel beschreibt das Aktivieren der Datenträgerverschlüsselung für Service Fabric-Clusterknoten in Azure mit Azure Resource Manager, Azure Key Vault.
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
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66119163"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Aktivieren der Datenträgerverschlüsselung für Service Fabric-Windows-Clusterknoten 
> [!div class="op_single_selector"]
> * [Datenträgerverschlüsselung für Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Datenträgerverschlüsselung für Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Gehen Sie folgendermaßen vor, um die Datenträgerverschlüsselung auf Service Fabric-Windows-Clusterknoten zu aktivieren. Sie müssen diese Schritte für alle Knotentypen/VM-Skalierungsgruppen ausführen. Für die Verschlüsselung der Knoten wird die Azure Disk Encryption-Funktion auf VM-Skalierungsgruppen eingesetzt.

In diesem Leitfaden werden folgende Verfahren behandelt:

* Schlüsselkonzepte, die Sie zum Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Windows-Cluster berücksichtigen müssen.
* Vorbereitende Schritte, die Sie vor dem Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Windows-Cluster ausführen müssen.
* Schritte, die Sie zum Aktivieren der Datenträgerverschlüsselung auf einer VM-Skalierungsgruppe im Service Fabric-Windows-Cluster ausführen müssen.

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
* **Azure Key Vault**: Erstellen Sie einen Schlüsseltresor im selben Abonnement und derselben Region wie die Skalierungsgruppe, und legen Sie als Zugriffsrichtlinie auf dem Schlüsseltresor mit seinem PS-Cmdlet „EnabledForDiskEncryption“ fest. Sie können die Richtlinie auch unter Verwendung der KeyVault-Benutzeroberfläche im Azure-Portal festlegen: 
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
* VM-Skalierungsgruppenverschlüsselung wird bei Betriebssystem- und Datenvolumes für eine Windows-VM-Skalierungsgruppe unterstützt. Das Deaktivieren der Verschlüsselung wird bei Betriebssystem- und Datenvolumes für die Windows-Skalierungsgruppe unterstützt.
* Reimaging- und Upgradevorgänge von VMs aus VM-Skalierungsgruppen werden in der aktuellen Vorschauversion nicht unterstützt.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Erstellen neuer Cluster und Aktivieren der Datenträgerverschlüsselung

Verwenden Sie die folgenden Befehle, um Cluster zu erstellen und die Datenträgerverschlüsselung mit Azure Resource Manager-Vorlage und selbstsigniertem Zertifikat zu aktivieren.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Verwenden einer bereits vorhandenen benutzerdefinierten Vorlage 

Wenn Sie eine benutzerdefinierte Vorlage für Ihre speziellen Anforderungen erstellen müssen, wird dringend empfohlen, dass Sie mit einer der Vorlagen beginnen, die unter den [Azure Service Fabric-Vorlagenbeispielen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) verfügbar sind. Beachten Sie die Anleitung und Erklärungen zum [Anpassen Ihrer Clustervorlage][customize-your-cluster-template] im folgenden Abschnitt.

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Bereitstellen einer Anwendung in einem Windows Service Fabric-Cluster
Führen Sie die Schritte sowie Anleitungen zum [Bereitstellen und Entfernen von Anwendungen mit PowerShell](service-fabric-deploy-remove-applications.md) aus.


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Aktivieren der Datenträgerverschlüsselung für die oben erstellte Service Fabric-Cluster-VM-Skalierungsgruppe
 
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


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Vergewissern Sie sich, dass die Datenträgerverschlüsselung für Windows-VM-Skalierungsgruppen aktiviert ist.
Rufen Sie den Status einer gesamten VM-Skalierungsgruppe bzw. einer beliebigen Instanz in der Skalierungsgruppe ab. Siehe nachstehende Befehle.
Ein weiterer Benutzer kann sich bei der VM in der Skalierungsgruppe anmelden und sicherstellen, dass die Laufwerke verschlüsselt sind.

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

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Nächste Schritte
An diesem Punkt haben Sie einen sicheren Cluster und wissen, wie Sie die Datenträgerverschlüsselung für die Service Fabric-Cluster-VM-Skalierungsgruppe aktivieren/deaktivieren. Das nächste Thema ist die [Datenträgerverschlüsselung für Linux](service-fabric-enable-azure-disk-encryption-linux.md). 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
