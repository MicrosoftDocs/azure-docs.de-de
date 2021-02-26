---
title: Azure CLI-Beispiel für die Verwaltung von VM-Skalierungsgruppen
description: In diesem Beispiel wird gezeigt, wie einer VM-Skalierungsgruppe Datenträger hinzugefügt werden. Sie können Datenträger aktualisieren und virtuelle Computer zur Azure AD-Authentifizierung hinzufügen.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 493f479a09fc7b21bb857ebd98c35824c548b5d0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539584"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Erstellen und Verwalten einer VM-Skalierungsgruppe

Verwenden Sie diese Beispielbefehle, um mithilfe der Azure CLI einen Prototyp für eine VM-Skalierungsgruppe zu erstellen.

Mit diesen Beispielbefehlen werden die folgenden Vorgänge veranschaulicht:

* Erstellen einer VM-Skalierungsgruppe.
* Hinzufügen neuer oder vorhandener Datenträger zu einer Skalierungsgruppe oder zu einer Instanz der Gruppe sowie Upgraden neuer oder vorhandener Datenträger
* Hinzufügen einer Skalierungsgruppe zur Azure AD-Authentifizierung (Azure Active Directory)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Beispielbefehle

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Nach dem Hinzufügen eines neuen Datenträgers müssen Sie den Datenträger formatieren und einbinden. Informationen zur Vorgehensweise bei virtuellen Windows-Computern finden Sie unter [Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer im Azure-Portal](../../virtual-machines/windows/attach-managed-disk-portal.md). Informationen zur Vorgehensweise bei virtuellen Linux-Computern finden Sie unter [Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Um den erweiterten Datenträger zu verwenden, erweitern Sie die zugrunde liegende Partition. Weitere Informationen finden Sie unter [Erweitern einer Datenträgerpartition und des Dateisystems](/azure/virtual-machines/linux/expand-disks#expand-a-disk-partition-and-filesystem).

In diesem Beispiel wurde die Größe eines Datenträgers geändert. Sie können das gleiche Verfahren verwenden, um einen Betriebssystemdatenträger zu aktualisieren. Weitere Informationen zur Vorgehensweise bei einem virtuellen Windows-Computer finden Sie unter [Erweitern des Betriebssystemlaufwerks eines virtuellen Computers](../../virtual-machines/windows/expand-os-disk.md). Weitere Informationen zur Vorgehensweise bei virtuellen Linux-Computern finden Sie unter [Erweitern von virtuellen Festplatten auf virtuellen Linux-Computern mit der Azure CLI](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

Führen Sie nach diesen Befehlen den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>In diesem Artikel verwendete Azure CLI-Referenzen

* [az disk delete](/cli/azure/disk#az_disk_delete)
* [az disk list](/cli/azure/disk#az_disk_list)
* [az disk update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtual machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)
