---
title: Erstellen eines virtuellen Linux-Computers in einer Zone mit der Azure CLI
description: Erstellen eines virtuellen Linux-Computers in einer Verfügbarkeitszone mit der Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: 964d2b5d89fd91aca68e9b47b0049529fe284848
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016402"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Erstellen eines virtuellen Linux-Computers in einer Verfügbarkeitszone mit der Azure CLI

In diesem Artikel werden die Schritte zum Erstellen eines virtuellen Linux-Computers in einer Azure-Verfügbarkeitszone mithilfe der Azure CLI erläutert. Eine [Verfügbarkeitszone](../../availability-zones/az-overview.md) ist eine physisch separate Zone in einer Azure-Region. Verwenden Sie Verfügbarkeitszonen, um Ihre Apps und Daten vor einem unwahrscheinlichen Fehler oder Ausfall eines gesamten Rechenzentrums zu schützen.

Um eine Verfügbarkeitszone verwenden zu können, muss der virtuelle Computer in einer [unterstützten Azure-Region](../../availability-zones/az-region.md) erstellt werden.

Stellen Sie sicher, dass Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und über [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.


## <a name="check-vm-sku-availability"></a>Überprüfen der VM-SKU-Verfügbarkeit
Die Verfügbarkeit von VM-Größen bzw. SKUs kann je nach Region und Zone variieren. Als Hilfe bei der Planung zur Verwendung von Verfügbarkeitszonen können Sie die verfügbaren VM-SKUs nach Azure-Region und -Zone auflisten. Hierdurch wird sichergestellt, dass Sie eine geeignete VM-Größe wählen und die gewünschte zonenübergreifende Resilienz erzielen. Weitere Informationen zu den verschiedenen VM-Typen und -Größen finden Sie unter [Übersicht über VM-Größen](../sizes.md).

Sie können die verfügbaren VM SKUs mit dem Befehl [az vm list-skus](/cli/azure/vm) auflisten. Im folgenden Beispiel werden die verfügbaren VM-SKUs in der Region *eastus2* aufgeführt:

```azurecli
az vm list-skus --location eastus2 --output table
```

Die Ausgabe ähnelt dem folgenden verkürzten Beispiel, in dem die Verfügbarkeitszonen aufgeführt werden, in denen die einzelnen VM-Größen verfügbar sind:

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe.  

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor dem virtuellen Computer muss eine Ressourcengruppe erstellt werden. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupVM* in der Region *eastus2* erstellt. „USA, Osten 2“ ist eine der Azure-Regionen, die Verfügbarkeitszonen unterstützen.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Die Ressourcengruppe wird beim Erstellen oder Ändern eines virtuellen Computers angegeben und ist im gesamten Artikel zu sehen.

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer. 

Beim Erstellen eines virtuellen Computers stehen mehrere Optionen zur Verfügung, z.B. Betriebssystemimage, Festlegen der Datenträgergröße und Administratoranmeldeinformationen. In diesem Beispiel wird ein virtueller Computer mit dem Namen *myVM* erstellt, auf dem Ubuntu Server ausgeführt wird. Der virtuelle Computer wird in der Verfügbarkeitszone *1* erstellt. Standardmäßig wird der virtuelle Computer mit der Größe *Standard_DS1_v2* erstellt.

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Die Erstellung der VM kann einige Minuten dauern. Nach der Erstellung des virtuellen Computers gibt die Azure-Befehlszeilenschnittstelle Informationen zu dem virtuellen Computer aus. Notieren Sie sich den Wert `zones`. Er gibt die Verfügbarkeitszone an, in der der virtuelle Computer ausgeführt wird. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bestätigen der Zone für verwalteten Datenträger und IP-Adresse

Bei der Bereitstellung des virtuellen Computers in einer Verfügbarkeitszone wird in der gleichen Verfügbarkeitszone ein verwalteter Datenträger bereitgestellt. Standardmäßig wird in dieser Zone ebenfalls eine öffentliche IP-Adresse erstellt. Die folgenden Beispiele rufen Informationen zu diesen Ressourcen ab.

Verwenden Sie den Befehl [az vm show](/cli/azure/vm) zum Zurückgeben der Datenträger-ID, um sicherzustellen, dass der verwaltete Datenträger der VM sich in der Verfügbarkeitszone befindet. In diesem Beispiel wird die Datenträger-ID in einer Variablen gespeichert, die in einem späteren Schritt verwendet wird. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Nun können Sie Informationen zum verwalteten Datenträger abrufen:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Die Ausgabe zeigt, dass sich der verwaltete Datenträger in der gleichen Verfügbarkeitszone befindet wie der virtuelle Computer:

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Verwenden Sie den Befehl [az vm list-ip-addresses](/cli/azure/vm), um den Namen der öffentlichen IP-Adresse in *myVM* zurückzugeben. In diesem Beispiel wird der Name in einer Variablen gespeichert, die in einem späteren Schritt verwendet wird.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Nun können Sie Informationen zur IP-Adresse abrufen:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Die Ausgabe zeigt, dass sich die IP-Adresse in derselben Verfügbarkeitszone wie der virtuelle Computer befindet:

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen virtuellen Computer in einer Verfügbarkeitszone erstellen. Erfahren Sie mehr über die [Verfügbarkeit](../availability.md) virtueller Azure-Computer.