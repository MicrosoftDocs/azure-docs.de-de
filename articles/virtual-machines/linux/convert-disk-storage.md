---
title: Konvertieren zwischen Storage Standard und Storage Premium für verwaltete Azure-Datenträger | Microsoft-Dokumentation
description: Konvertieren verwalteter Azure-Datenträger zwischen Storage Standard und Storage Premium mithilfe der Azure-Befehlszeilenschnittstelle
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc42bcbf7149f88eb895317a411c7acd5913d63d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417694"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konvertieren verwalteter Azure-Datenträger zwischen Storage Standard und Storage Premium

Es gibt vier Typen verwalteter Azure-Datenträger: Azure SSD Ultra (Vorschauversion), SSD Premium, SSD Standard und HDD Standard. Sie können basierend auf Ihren Leistungsanforderungen zwischen den drei allgemein verfügbaren Datenträgertypen (SSD Premium, SSD Standard und HDD Standard) wechseln. Derzeit ist noch kein Wechsel von oder zu SSD Ultra möglich. Sie müssen in diesem Fall einen neuen Datenträger bereitstellen.

Diese Funktion wird für nicht verwaltete Datenträger nicht unterstützt. Sie können jedoch problemlos [nicht verwaltete Datenträger in verwaltete Datenträger konvertieren](convert-unmanaged-to-managed-disks.md), um zwischen den Datenträgertypen wechseln zu können.

In diesem Artikel wird das Konvertieren verwalteter Datenträger zwischen Storage Standard und Storage Premium über die Azure-Befehlszeilenschnittstelle erläutert. Informationen zum Ausführen einer Installation oder eines Upgrades für das Tool finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Voraussetzungen

* Da für die Datenträgerkonvertierung ein Neustart des virtuellen Computers (VM) erforderlich ist, sollten Sie die Migration Ihres Datenträgerspeichers während eines bereits bestehenden Wartungsfensters durchführen.
* Konvertieren Sie nicht verwaltete Datenträger zuerst [in verwaltete Datenträger](convert-unmanaged-to-managed-disks.md), damit Sie zwischen den Speicheroptionen wechseln können.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Wechseln aller verwalteten Datenträger eines virtuellen Computers zwischen Premium und Standard

Dieses Beispiel zeigt, wie Sie alle Datenträger eines virtuellen Computers von Storage Standard in Storage Premium konvertieren (oder umgekehrt). Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel erfolgt auch ein Wechsel zu einer Größe, die Storage Premium unterstützt.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Wechseln einzelner verwalteter Datenträger zwischen Standard und Premium

Für Ihre Entwicklungs-/Testworkload sollten Sie eine Kombination aus Standard- und Premium-Datenträgern verwenden, um Ihre Kosten zu senken. Sie können auch nur die Datenträger aktualisieren, die eine höhere Leistung benötigen. Dieses Beispiel zeigt, wie Sie einen einzelnen VM-Datenträger von Storage Standard in Storage Premium konvertieren (oder umgekehrt). Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel erfolgt auch ein Wechsel zu einer Größe, die Storage Premium unterstützt.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Wechseln von verwalteten Datenträgern zwischen HDD Standard und SSD Standard

Dieses Beispiel zeigt, wie ein VM-Datenträger zwischen HDD Standard und SSD Standard konvertiert wird.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Wechseln verwalteter Datenträger zwischen Standard und Premium im Azure-Portal

Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie den virtuellen Computer in der Liste der **VMs** aus.
3. Wenn der virtuelle Computer nicht beendet ist, wählen Sie im oberen Bereich unter **Übersicht** für den virtuellen Computer **Beenden** aus, und warten Sie, bis der virtuellen Computer beendet wurde.
4. Wählen Sie im Bereich für die VM im Menü die Option **Datenträger** aus.
5. Wählen Sie den Datenträger aus, den Sie konvertieren möchten.
6. Wählen Sie im Menü **Konfiguration** aus.
7. Ändern Sie **Kontotyp** von **HDD Standard** in **SSD Premium** bzw. von **SSD Premium** in **HDD Standard**.
8. Wählen Sie **Speichern** aus, und schließen Sie den Datenträgerbereich.

Das Update des Datenträgertyps erfolgt sofort. Sie können den virtuellen Computer nach der Konvertierung neu starten.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine schreibgeschützte Kopie eines virtuellen Computers mit [Momentaufnahmen](snapshot-copy-managed-disk.md).
