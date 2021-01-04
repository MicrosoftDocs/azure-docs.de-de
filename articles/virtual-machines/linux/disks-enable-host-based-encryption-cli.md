---
title: Aktivieren der End-to-End-Verschlüsselung mit Verschlüsselung auf dem Host – Azure CLI – verwaltete Datenträger
description: Verwenden Sie Verschlüsselung auf dem Host, um die End-to-End-Verschlüsselung auf Ihren von Azure verwalteten Datenträgern zu aktivieren.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5c0336b80bee1cd5eb76d0ce3d5f99c7296a8467
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499763"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Verwenden der Azure CLI zum Aktivieren der End-to-End-Verschlüsselung mit Verschlüsselung auf dem Host

Wenn Sie die Verschlüsselung auf dem Host aktivieren, werden die auf dem VM-Host gespeicherten Daten ruhend verschlüsselt und verschlüsselt an den Speicherdienst übermittelt. Informationen zum Konzept der Verschlüsselung auf dem Host sowie zu anderen Verschlüsselungstypen für verwaltete Datenträger finden Sie unter [Verschlüsselung auf dem Host: End-to-End-Verschlüsselung für Ihre VM-Daten](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Unterstützte Regionen

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Unterstützte VM-Größen

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Sie können die VM-Größen auch programmgesteuert ermitteln. Informationen dazu, wie Sie sie programmgesteuert abrufen können, finden Sie im Abschnitt [Ermitteln der unterstützten VM-Größen](#finding-supported-vm-sizes).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen das Feature für Ihr Abonnement aktivieren lassen, um die Verschlüsselung auf dem Host für Ihre virtuellen Computer oder VM-Skalierungsgruppen verwenden zu können. Senden Sie eine E-Mail mit Ihren Abonnement-IDs an encryptionAtHost@microsoft.com, um das Feature für Ihre Abonnements aktivieren zu lassen.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Erstellen einer Azure Key Vault-Instanz und einer DiskEncryptionSet-Klasse

Nach dem Aktivieren des Features müssen Sie eine Azure Key Vault-Instanz und eine DiskEncryptionSet-Klasse einrichten, sofern noch nicht geschehen.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Beispiele

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Erstellen einer VM mit aktivierter Verschlüsselung auf dem Host mit kundenseitig verwalteten Schlüsseln 

Erstellen Sie eine VM mit verwalteten Datenträgern mithilfe des Ressourcen-URI der zuvor erstellten DiskEncryptionSet-Klasse, um einen Zwischenspeicher des Betriebssystems und Datenträger mit kundenseitig verwalteten Schlüsseln zu verschlüsseln. Temporäre Datenträger werden mit plattformseitig verwalteten Schlüsseln verschlüsselt. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Erstellen einer VM mit aktivierter Verschlüsselung auf dem Host mit plattformseitig verwalteten Schlüsseln 

Erstellen Sie eine VM mit aktivierter Verschlüsselung auf dem Host, um einen Zwischenspeicher des Betriebssystems/Datenträger und temporäre Datenträger mit plattformseitig verwalteten Schlüsseln zu verschlüsseln. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Aktualisieren einer VM zur Aktivierung der Verschlüsselung auf dem Host 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Überprüfen des Status einer Verschlüsselung auf dem Host für eine VM

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Erstellen einer VM-Skalierungsgruppe mit aktivierter Verschlüsselung auf dem Host mit kundenseitig verwalteten Schlüsseln 

Erstellen Sie eine VM-Skalierungsgruppe mit verwalteten Datenträgern mithilfe des Ressourcen-URIs der zuvor erstellten DiskEncryptionSet-Klasse, um einen Zwischenspeicher des Betriebssystems und Datenträger mit kundenseitig verwalteten Schlüsseln zu verschlüsseln. Temporäre Datenträger werden mit plattformseitig verwalteten Schlüsseln verschlüsselt. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Erstellen einer VM-Skalierungsgruppe mit aktivierter Verschlüsselung auf dem Host mit plattformseitig verwalteten Schlüsseln 

Erstellen Sie eine VM-Skalierungsgruppe mit aktivierter Verschlüsselung auf dem Host, um einen Zwischenspeicher des Betriebssystems/Datenträger und temporäre Datenträger mit plattformseitig verwalteten Schlüsseln zu verschlüsseln. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Aktualisieren einer VM-Skalierungsgruppe zur Aktivierung der Verschlüsselung auf dem Host 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Überprüfen des Status einer Verschlüsselung auf dem Host für eine VM-Skalierungsgruppe

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Ermitteln der unterstützten VM-Größen

Ältere VM-Größen werden nicht unterstützt. Die Liste der unterstützten VM-Größen finden Sie folgendermaßen:

Rufen Sie die [Resource Skus-API](/rest/api/compute/resourceskus/list) auf, und stellen Sie sicher, dass die `EncryptionAtHostSupported`-Funktion auf **True** festgelegt ist.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Alternativ können Sie auch das PowerShell-Cmdlet [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) aufrufen.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie diese Ressourcen erstellt und konfiguriert haben, können Sie diese zum Sichern Ihrer verwalteten Datenträger verwenden. Der folgende Link enthält Beispielskripts mit jeweils einem entsprechenden Szenario, die Sie zum Sichern Ihrer verwalteten Datenträger verwenden können.

[Beispiele für Azure Resource Manager-Vorlagen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)