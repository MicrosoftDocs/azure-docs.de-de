---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 64290aad2d9f98006a715b480be8cb96965abbaf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161708"
---
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer. 

Das folgende Beispiel erstellt einen virtuellen Computer mit dem Namen *myVM* und SSH-Schlüssel, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`. Der Befehl legt zudem *azureuser* als Administratorbenutzernamen fest. Sie verwenden diesen Namen später, um eine Verbindung mit dem virtuellen Computer herzustellen. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an. Notieren Sie sich den Wert von `publicIpAddress`. Diese Adresse wird später verwendet, um auf den virtuellen Computer zuzugreifen.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Öffnen von Port 80 für Webdatenverkehr 

Standardmäßig sind für in Azure bereitgestellte virtuelle Linux-Computer nur eingehende SSH-Verbindungen zulässig. Da dieser virtuelle Computer als Webserver fungieren soll, muss Port 80 für Datenverkehr aus dem Internet geöffnet werden. Verwenden Sie zum Öffnen des gewünschten Ports den Befehl [az vm open-port](/cli/azure/vm).  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Computer


Wenn Sie die öffentliche IP-Adresse des virtuellen Computers nicht bereits kennen, führen Sie den Befehl [az network public-ip list](/cli/azure/network/public-ip) aus. Sie benötigen diese IP-Adresse für mehrere nachfolgende Schritte.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die korrekte öffentliche IP-Adresse Ihres virtuellen Computers. In diesem Beispiel lautet die IP-Adresse *40.68.254.142*. *azureuser* ist der Administratorbenutzername, der beim Erstellen des virtuellen Computers festgelegt wurde.

```bash
ssh azureuser@40.68.254.142
```

