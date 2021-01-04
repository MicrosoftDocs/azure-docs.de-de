---
title: Weiterleiten von Netzwerkdatenverkehr – Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Netzwerkdatenverkehr mithilfe der Azure-Befehlszeilenschnittstelle und einer Routingtabelle weitergeleitet wird.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 043d450a1b968174ad263579d39de06a296a98e4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741484"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Weiterleiten von Netzwerkdatenverkehr mithilfe der Azure-Befehlszeilenschnittstelle

Standardmäßig leitet Azure automatisch den Datenverkehr aller Subnetze des virtuellen Netzwerks weiter. Sie können eigene Routen erstellen, um das Azure-Standardrouting außer Kraft zu setzen. Die Möglichkeit zum Erstellen von benutzerdefinierten Routen ist beispielsweise hilfreich, wenn Sie über ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) Datenverkehr zwischen Subnetzen weiterleiten möchten. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen einer Routingtabelle
* Erstellen einer Route
* Erstellen eines virtuellen Netzwerks mit mehreren Subnetzen
* Zuordnen einer Routingtabelle zu einem Subnetz
* Erstellen eines virtuellen Netzwerkgeräts, das Datenverkehr weiterleitet
* Bereitstellen von VMs in unterschiedlichen Subnetzen
* Weiterleiten von Datenverkehr aus einem Subnetz zu einem anderen über ein virtuelles Netzwerkgerät

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-route-table"></a>Erstellen einer Routingtabelle

Bevor Sie eine Routingtabelle erstellen können, müssen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe für alle in diesem Artikel erstellten Ressourcen erstellen. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Erstellen Sie mit [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) eine Routingtabelle. Im folgenden Beispiel wird eine Routingtabelle mit dem Namen *myRouteTablePublic* erstellt. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Erstellen einer Route

Erstellen Sie mit [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) eine Route in der Routingtabelle. 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Zuordnen einer Routingtabelle zu einem Subnetz

Bevor Sie eine Routingtabelle einem Subnetz zuordnen können, müssen Sie ein virtuelles Netzwerk und ein Subnetz erstellen. Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk mit einem Subnetz.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) zwei weitere Subnetze.

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Ordnen Sie mit [az network vnet subnet update](/cli/azure/network/vnet/subnet) die Routingtabelle *myRouteTablePublic* dem Subnetz *Public* zu.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Erstellen eines virtuellen Netzwerkgeräts

Ein virtuelles Netzwerkgerät ist eine VM, die eine Netzwerkfunktion wie Routing, Firewall oder WAN-Optimierung ausführt.

Erstellen Sie eine NVA im *DMZ*-Subnetz mit [az vm create](/cli/azure/vm). Beim Erstellen einer VM erstellt Azure standardmäßig eine öffentliche IP-Adresse und weist diese der VM zu. Der Parameter `--public-ip-address ""` weist Azure an, keine öffentliche IP-Adresse zu erstellen und der VM zuzuweisen, da keine Verbindungen mit der VM über das Internet hergestellt werden müssen. Wenn SSH-Schlüssel nicht bereits an einem Standardschlüsselspeicherort vorhanden sind, werden sie durch den Befehl erstellt. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Fahren Sie erst mit dem nächsten Schritt fort, wenn Azure die VM erstellt und Ausgaben über die VM zurückgegeben hat. 

Damit Netzwerkdatenverkehr, der nicht für seine eigene IP-Adresse bestimmt ist, an eine Netzwerkschnittstelle gesendet werden kann, muss die IP-Weiterleitung für die Netzwerkschnittstelle aktiviert sein. Aktivieren Sie mit [az network nic update](/cli/azure/network/nic) die IP-Weiterleitung für die Netzwerkschnittstelle.

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Das Betriebssystem der VM oder eine Anwendung, die auf der VM ausgeführt wird, muss ebenfalls Netzwerkdatenverkehr weiterleiten können. Aktivieren Sie die IP-Weiterleitung innerhalb des Betriebssystems der VM mit [az vm extension set](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Die Ausführung des Befehls kann bis zu einer Minute dauern.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie zwei VMs im virtuellen Netzwerk, damit Sie in einem späteren Schritt diesen Datenverkehr aus dem Subnetz *Public* über die NVA an das Subnetz *Private* weiterleiten können. 

Erstellen Sie mit *az vm create* eine VM im Subnetz [Public](/cli/azure/vm). Der Parameter `--no-wait` ermöglicht Azure die Ausführung des Befehls im Hintergrund, sodass Sie mit dem nächsten Befehl fortfahren können. Zur Vereinfachung dieses Artikels wird ein Kennwort verwendet. Schlüssel werden in der Regel in Produktionsbereitstellungen verwendet. Bei der Verwendung von Schlüsseln müssen Sie außerdem die SSH-Agent-Weiterleitung konfigurieren. Weitere Informationen finden Sie in der Dokumentation Ihres SSH-Clients. Ersetzen Sie `<replace-with-your-password>` im folgenden Befehl durch ein Kennwort Ihrer Wahl.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Erstellen Sie eine VM im Subnetz *Private*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Notieren Sie sich **publicIpAddress**. Über diese Adresse wird in einem späteren Schritt über das Internet auf den virtuellen Computer zugegriffen.

## <a name="route-traffic-through-an-nva"></a>Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit der *myVmPrivate*-VM. Ersetzen Sie *\<publicIpAddress>* durch die öffentliche IP-Adresse der VM. Im Beispiel oben lautet die IP-Adresse *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Geben Sie bei entsprechender Aufforderung das Kennwort ein, das Sie unter [Erstellen von virtuellen Computern](#create-virtual-machines) ausgewählt haben.

Installieren Sie mit dem folgenden Befehl die Routenverfolgung auf der *myVmPrivate*-VM:

```bash
sudo apt-get install traceroute
```

Verwenden Sie den folgenden Befehl, um die Weiterleitung von Netzwerkdatenverkehr von der *myVmPrivate*-VM zur *myVmPublic*-VM zu testen.

```bash
traceroute myVmPublic
```

Die Antwort ähnelt dem folgenden Beispiel:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Wie Sie sehen, wird Datenverkehr direkt von der VM *myVmPrivate* zur VM *myVmPublic* weitergeleitet. Azure-Standardrouten leiten Datenverkehr direkt zwischen Subnetzen weiter. 

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung zwischen der *myVmPublic*-VM und der *myVmPrivate*-VM:

```bash
ssh azureuser@myVmPublic
```

Installieren Sie mit dem folgenden Befehl die Routenverfolgung auf der *myVmPublic*-VM:

```bash
sudo apt-get install traceroute
```

Verwenden Sie den folgenden Befehl, um die Weiterleitung von Netzwerkdatenverkehr von der *myVmPublic*-VM zur *myVmPrivate*-VM zu testen.

```bash
traceroute myVmPrivate
```

Die Antwort ähnelt dem folgenden Beispiel:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Wie Sie sehen können, ist der erste Hop 10.0.2.4, die private IP-Adresse des virtuellen Netzwerksgeräts. Der zweite Hop ist 10.0.1.4, die private IP-Adresse der VM *myVmPrivate*. Die Route, die der Routingtabelle *myRouteTablePublic* hinzugefügt und dem Subnetz *Public* zugeordnet wurde, hat bewirkt, dass Azure Datenverkehr über das virtuelle Netzwerkgerät weiterleitet und nicht direkt an das Subnetz *Private*.

Schließen Sie die SSH-Sitzungen zu *myVmPublic*- und *myVmPrivate*-VM.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group) entfernen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Routingtabelle erstellt und einem Subnetz zugeordnet. Sie haben ein einfaches virtuelles Netzwerkgerät erstellt, das Datenverkehr von einem öffentlichen Subnetz an ein privates Subnetz weiterleitet. Über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) können Sie eine Vielzahl von vorkonfigurierten virtuellen Netzwerkgeräten bereitstellen, die Netzwerkfunktionen wie Firewall und WAN-Optimierung ausführen. Weitere Informationen zum Routing finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md) und [Erstellen, Ändern oder Löschen einer Routingtabelle](manage-route-table.md).

Sie können zwar in einem virtuellen Netzwerk viele Azure-Ressourcen bereitstellen, für einige Azure-PaaS-Dienste können jedoch keine Ressourcen in einem virtuellen Netzwerk bereitgestellt werden. Allerdings können Sie den Zugriff auf die Ressourcen einiger Azure-PaaS-Dienste nach wie vor auf den Datenverkehr nur eines Subnetzes eines virtuellen Netzwerks beschränken. Weitere Informationen finden Sie unter [Einschränken des Netzwerkzugriffs auf PaaS-Ressourcen mit virtuellen Netzwerkdienstendpunkten mithilfe der Azure CLI](tutorial-restrict-network-access-to-resources-cli.md).
