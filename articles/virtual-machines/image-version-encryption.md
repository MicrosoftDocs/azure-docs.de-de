---
title: 'Vorschau: Erstellen einer mit eigenen Schlüsseln verschlüsselten Imageversion'
description: Erstellen Sie eine Imageversion in einem Katalog mit freigegebenen Images, und verwenden Sie dabei kundenseitig verwaltete Verschlüsselungsschlüssel.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 0f5eb6a2964cdb679c2a83bd9d9b6296e3e962ed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499287"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Vorschau: Verwenden von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von Images

Images in einer Shared Image Gallery werden als Momentaufnahmen gespeichert, sodass sie automatisch durch serverseitige Verschlüsselung verschlüsselt werden. Die serverseitige Verschlüsselung verwendet die [AES-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) mit 256 Bit – eine der stärksten verfügbaren Blockchiffren. Die serverseitige Verschlüsselung ist ebenfalls mit FIPS 140-2 konform. Weitere Informationen zu den kryptografischen Modulen, die verwalteten Azure-Datenträgern zugrunde liegen, finden Sie unter [Kryptografie-API: Die nächste Generation](/windows/desktop/seccng/cng-portal).

Sie können von der Plattform verwaltete Schlüssel oder eigene Schlüssel für die Verschlüsselung Ihrer Images verwenden. Sie können auch beide Methoden zusammen verwenden, um eine doppelte Verschlüsselung zu erreichen. Wenn Sie die Verschlüsselung mit eigenen Schlüsseln verwalten möchten, können Sie einen *kundenseitig verwalteten Schlüssel* angeben, der zum Verschlüsseln und Entschlüsseln aller Datenträger in Ihren Images verwendet werden soll. 

Die serverseitige Verschlüsselung über kundenseitig verwaltete Schlüssel verwendet Azure Key Vault. Sie können entweder [Ihre RSA-Schlüssel](../key-vault/keys/hsm-protected-keys.md) in den Schlüsseltresor importieren oder neue RSA-Schlüssel in Azure Key Vault generieren.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie bereits über einen Datenträgerverschlüsselungssatz in den einzelnen Regionen verfügen, in denen Sie Ihr Image replizieren möchten:

- Wenn Sie nur einen kundenseitig verwalteten Schlüssel verwenden möchten, finden Sie weitere Informationen in den Artikeln zum Aktivieren kundenseitig verwalteter Schlüssel mit serverseitiger Verschlüsselung über das [Azure-Portal](./disks-enable-customer-managed-keys-portal.md) oder mithilfe von [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Wenn Sie sowohl plattformseitig als auch kundenseitig verwaltete Schlüssel (für die doppelte Verschlüsselung) verwenden möchten, lesen Sie die Artikel zum Aktivieren der doppelten Verschlüsselung für ruhende Daten über das [Azure-Portal](./disks-enable-double-encryption-at-rest-portal.md) oder mithilfe von [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > Für den Zugriff auf das Azure-Portal müssen Sie den Link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) verwenden. Die doppelte Verschlüsselung im Ruhezustand ist derzeit im öffentlichen Azure-Portal ohne Verwendung des Links nicht sichtbar.

## <a name="limitations"></a>Einschränkungen

Wenn Sie kundenseitig verwaltete Schlüssel zur Verschlüsselung von Images in einer Shared Image Gallery verwenden, gelten folgende Einschränkungen:   

- Verschlüsselungsschlüsselsätze müssen sich in demselben Abonnement wie Ihr Image befinden.

- Da Verschlüsselungsschlüsselsätze regionale Ressourcen sind, erfordert jede Region einen anderen Verschlüsselungsschlüsselsatz.

- Images, die kundenseitig verwaltete Schlüssel verwenden, können nicht kopiert oder freigegeben werden. 

- Sobald Sie eigene Schlüssel zum Verschlüsseln eines Datenträgers oder Images verwenden, können Sie nicht zur Verwendung von plattformseitig verwalteten Schlüsseln zum Verschlüsseln dieser Datenträger oder Images zurückkehren.


> [!IMPORTANT]
> Die Verschlüsselung mithilfe kundenseitig verwalteter Schlüssel befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Für die öffentliche Vorschau müssen Sie das Feature zuerst registrieren:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Es dauert einige Minuten, bis die Registrierung abgeschlossen ist. Verwenden Sie `Get-AzProviderFeature`, um den Status der Featureregistrierung zu überprüfen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Wenn `RegistrationState` den Wert `Registered` zurückgibt, können Sie mit dem nächsten Schritt fortfahren.

Überprüfen Sie Ihre Anbieterregistrierung. Stellen Sie sicher, dass `Registered` zurückgegeben wird.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Wenn nicht `Registered` zurückgegeben wird, registrieren Sie die Anbieter mithilfe des folgenden Codes:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Um einen Datenträgerverschlüsselungssatz für eine Imageversion anzugeben, verwenden Sie [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) mit dem Parameter `-TargetRegion`: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Erstellen einer VM

Sie können einen virtuellen Computer (VM) aus einem Katalog mit freigegebenen Images erstellen und kundenseitig verwaltete Schlüssel zum Verschlüsseln der Datenträger verwenden. Die Syntax ist dieselbe wie beim Erstellen einer [generalisierten](vm-generalized-image-version-powershell.md) oder [spezialisierten](vm-specialized-image-version-powershell.md) VM aus einem Image. Verwenden Sie den erweiterten Parametersatz, und fügen Sie `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` der VM-Konfiguration hinzu.

Bei Datenträgern für Daten fügen Sie den Parameter `-DiskEncryptionSetId $setID` hinzu, wenn Sie [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) verwenden.


## <a name="cli"></a>Befehlszeilenschnittstelle (CLI) 

In der Public Preview müssen Sie das Feature zuerst registrieren. Die Registrierung dauert ungefähr 30 Minuten.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Überprüfen Sie den Status der Funktionsregistrierung:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Wenn dieser Code den Wert `"state": "Registered"` zurückgibt, können Sie mit dem nächsten Schritt fortfahren.

Überprüfen Sie die Registrierung:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Wenn nicht „registered“ zurückgegeben wird, führen Sie den folgenden Befehl aus:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Um einen Datenträgerverschlüsselungssatz für eine Imageversion anzugeben, verwenden Sie [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) mit dem Parameter `--target-region-encryption`. Das Format für `--target-region-encryption` ist eine durch Komma getrennte Liste mit Schlüsseln für die Verschlüsselung der Datenträger für Betriebssystem und Daten. Diese sollte wie folgt aussehen: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Wenn die Quelle des Betriebssystemdatenträgers ein verwalteter Datenträger oder eine VM ist, verwenden Sie `--managed-image`, um die Quelle für die Imageversion anzugeben. In diesem Beispiel ist die Quelle ein verwaltetes Image, das sowohl über einen Datenträger für das Betriebssystem als auch über einen Datenträger für Daten bei LUN 0 verfügt. Der Betriebssystemdatenträger wird mit DiskEncryptionSet1 verschlüsselt, der Datenträger für Daten mit DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Wenn die Quelle für den Betriebssystemdatenträger eine Momentaufnahme ist, verwenden Sie `--os-snapshot`, um den Datenträger anzugeben. Wenn es Datenträgermomentaufnahmen gibt, die auch Teil der Imageversion sein sollten, fügen Sie diese hinzu. Verwenden Sie `--data-snapshot-luns` zur Angabe der LUN und `--data-snapshots` zur Angabe der Momentaufnahmen.

In diesem Beispiel sind die Quellen Datenträgermomentaufnahmen. Es gibt einen Betriebssystemdatenträger und einen Datenträger für Daten bei LUN 0. Der Betriebssystemdatenträger wird mit DiskEncryptionSet1 verschlüsselt, der Datenträger für Daten mit DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Sie können eine VM aus einem Katalog mit freigegebenen Images erstellen und kundenseitig verwaltete Schlüssel zum Verschlüsseln der Datenträger verwenden. Die Syntax ist dieselbe wie beim Erstellen einer [generalisierten](vm-generalized-image-version-cli.md) oder [spezialisierten](vm-specialized-image-version-cli.md) VM aus einem Image. Fügen Sie einfach den Parameter `--os-disk-encryption-set` mit der ID des Verschlüsselungssatzes hinzu. Bei Datenträgern für Daten fügen Sie `--data-disk-encryption-sets` mit einer durch Leerzeichen getrennten Liste der Datenträgerverschlüsselungssätze für die Datenträger hinzu.


## <a name="portal"></a>Portal

Wenn Sie Ihre Imageversion im Portal erstellen, können Sie die Registerkarte **Verschlüsselung** verwenden, um Ihre Speicherverschlüsselungssätze anzuwenden.

> [!IMPORTANT]
> Wenn Sie die doppelte Verschlüsselung verwenden möchten, müssen Sie für den Zugriff auf das Azure-Portal den Link [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) verwenden. Die doppelte Verschlüsselung im Ruhezustand ist derzeit im öffentlichen Azure-Portal ohne Verwendung des Links nicht sichtbar.


1. Wählen Sie auf der Seite **Imageversion erstellen** die Registerkarte **Verschlüsselung** aus.
2. Wählen Sie als **Verschlüsselungstyp** die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** oder **Doppelte Verschlüsselung mit plattformseitig und kundenseitig verwalteten Schlüsseln** aus. 
3. Wählen Sie für jeden Datenträger im Image einen Verschlüsselungssatz aus der Dropdownliste **Datenträgerverschlüsselungssatz** aus. 

### <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Sie können eine VM aus einer Imageversion erstellen und kundenseitig verwaltete Schlüssel zum Verschlüsseln der Datenträger verwenden. Wenn Sie die VM im Portal erstellen, wählen Sie auf der Registerkarte **Datenträger** als **Verschlüsselungstyp** die Option **Verschlüsselung ruhender Daten mit einem kundenseitig verwalteten Schlüssel** oder **Doppelte Verschlüsselung mit plattformseitig und kundenseitig verwalteten Schlüsseln** aus. Dann können Sie den Verschlüsselungssatz in der Dropdownliste auswählen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [serverseitige Datenträgerverschlüsselung](./disk-encryption.md).

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).