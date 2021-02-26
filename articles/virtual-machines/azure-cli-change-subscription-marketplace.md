---
title: Azure CLI-Beispiel für das Verschieben eines virtuellen Azure-Computers aus dem Marketplace in ein anderes Abonnement
description: Hier finden Sie ein Azure CLI-Beispiel für das Verschieben eines virtuellen Azure Marketplace-Computers in ein anderes Abonnement.
author: cynthn
ms.author: cynthn
manager: ''
ms.date: 01/29/2021
ms.topic: sample
ms.service: virtual-machines
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: e70284a3a89c30d0f1d1363a15652449a6a1d4f5
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558102"
---
# <a name="move-a-marketplace-azure-virtual-machine-to-another-subscription"></a>Verschieben eines virtuellen Azure Marketplace-Computers in ein anderes Abonnement

Zum Verschieben eines virtuellen Marketplace-Computers in ein anderes Abonnement müssen Sie den Betriebssystemdatenträger in dieses Abonnement verschieben und dann den virtuellen Computer neu erstellen.

Sie benötigen dieses Verfahren nicht, um einen Datenträger in ein neues Abonnement zu verschieben. Erstellen Sie stattdessen einen neuen virtuellen Computer im neuen Abonnement aus dem Marketplace, verschieben Sie dann den Datenträger, und fügen Sie ihn an.

Dieses Skript veranschaulicht drei Vorgänge:

- Erstellen einer Momentaufnahme eines Betriebssystemdatenträgers
- Verschieben der Momentaufnahme in ein anderes Abonnement
- Erstellen eines virtuellen Computers auf der Grundlage dieser Momentaufnahme

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-script"></a>Beispielskript

Wenn Sie einen virtuellen Marketplace-Computer in ein anderes Abonnement verschieben möchten, müssen Sie auf der Grundlage des verschobenen Betriebssystemdatenträgers einen neuen virtuellen Computer für das gleiche Marketplace-Angebot erstellen.

> [!NOTE]
> Ist der VM-Plan nicht mehr im Marketplace verfügbar, können Sie dieses Verfahren nicht verwenden.

```azurecli
#!/bin/bash
# Set variable values before proceeding. 

# Variables
sourceResourceGroup= Resource group for the current virtual machine
sourceSubscription= Subscription for the current virtual machine
vmName= Name of the current virtual machine

destinationResourceGroup= Resource group for the new virtual machine, create if necessary
destinationSubscription= Subscription for the new virtual machine

# Set your current subscription for the source virtual machine
az account set --subscription $sourceSubscription

# Load variables about your virtual machine
# osType = windows or linux
osType=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --subscription $sourceSubscription \
    --query 'storageProfile.osDisk.osType' --output tsv)

# offer = Your offer in Marketplace
offer=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.offer' --output tsv)

# plan = Your plan in Marketplace
plan=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'plan' --output tsv)

# publisher = Your publisher in Marketplace
publisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --name $vmName --query 'storageProfile.imageReference.publisher' --output tsv)

# Get information to create new virtual machine
planName=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.name' --name $vmName)
planProduct=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.product' --name $vmName)
planPublisher=$(az vm get-instance-view --resource-group $sourceResourceGroup \
    --subscription $sourceSubscription --query 'plan.publisher' --name $vmName)

# Get the name of the OS disk
osDiskName=$(az vm show --resource-group $sourceResourceGroup --name $vmName \
    --query 'storageProfile.osDisk.name' --output tsv)

# Verify the terms for your market virtual machine
az vm image terms show --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $sourceSubscription

# Deallocate the virtual machine
az vm deallocate --resource-group $sourceResourceGroup --name $vmName

# Create a snapshot of the OS disk
az snapshot create --resource-group $sourceResourceGroup --name MigrationSnapshot \
    --source "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/disks/$osDiskName"

# Move the snapshot to your destination resource group
az resource move --destination-group $destinationResourceGroup \
    --destination-subscription-id $destinationSubscription \
    --ids "/subscriptions/$sourceSubscription/resourceGroups/$sourceResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot"

# Set your subscription to the destination value
az account set --subscription $destinationSubscription

# Accept the terms from the Marketplace
az vm image terms accept --offer $offer --plan '$plan' --publisher $publisher \
    --subscription $destinationSubscription

# Create disk from the snapshot 
az disk create --resource-group $destinationResourceGroup --name DestinationDisk \
    --source "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/snapshots/MigrationSnapshot" \
    --os-type $osType

# Create virtual machine from disk
az vm create --resource-group $destinationResourceGroup --name $vmName \
    --plan-name $planName --plan-product $planProduct  --plan-publisher $planPublisher \
    --attach-os-disk "/subscriptions/$destinationSubscription/resourceGroups/$destinationResourceGroup/providers/Microsoft.Compute/disks/DestinationDisk" \
    --os-type $osType
```

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

Entfernen Sie nach Ausführung des Beispiels mit den folgenden Befehlen die Ressourcengruppe und alle zugehörigen Ressourcen:

```azurecli
az group delete --name $sourceResourceGroup --subscription $sourceSubscription
az group delete --name $destinationResourceGroup --subscription $destinationSubscription
```

## <a name="azure-cli-references-used-in-this-article"></a>In diesem Artikel verwendete Azure CLI-Referenzen

- [az account set](/cli/azure/account#az_account_set)
- [az disk create](/cli/azure/disk#az_disk_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az resource move](/cli/azure/resource#az_resource_move)
- [az snapshot create](/cli/azure/snapshot#az_snapshot_create)
- [az vm create](/cli/azure/vm#az_vm_create)
- [az vm deallocate](/cli/azure/vm#az_vm_deallocate)
- [az vm delete](/cli/azure/vm#az_vm_delete)
- [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)
- [az vm image terms accept](/cli/azure/vm/image/terms#az_vm_image_terms_accept)
- [az vm image terms show](/cli/azure/vm/image/terms#az_vm_image_terms_show)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>Nächste Schritte

- [Verschieben von VMs in eine andere Azure-Region](../site-recovery/azure-to-azure-tutorial-migrate.md)
- [Verschieben eines virtuellen Computers in ein anderes Abonnement oder eine andere Ressourcengruppe](./linux/move-vm.md)
