---
title: Herstellen von Verbindungen zwischen virtuellen Netzwerken mittels VNET-Peering – Azure CLI
description: In diesem Artikel erfahren Sie, wie Sie mit der Azure CLI durch Peering virtueller Netzwerke Verbindungen zwischen virtuellen Netzwerken herstellen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c6399e437fa314aa82e0b41cbf8a170ea3ab72e
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741772"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Herstellen von Verbindungen zwischen virtuellen Netzwerken durch Peerings für virtuelle Netzwerke mit der Azure CLI

Sie können durch Peering virtueller Netzwerke Verbindungen zwischen virtuellen Netzwerken herstellen. Sobald ein Peering zwischen virtuellen Netzwerken eingerichtet wurde, können Ressourcen in beiden virtuellen Netzwerken untereinander mit der gleichen Latenz und Bandbreite kommunizieren, als befänden sie sich im selben virtuellen Netzwerk. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen zweier virtueller Netzwerke
* Herstellen einer Verbindung zwischen zwei virtuellen Netzwerken mit einem Peering virtueller Netzwerke
* Bereitstellen eines virtuellen Computers (VM) in jedem virtuellen Netzwerk
* Kommunikation zwischen VMs

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-virtual-networks"></a>Erstellen virtueller Netzwerke

Vor der Erstellung eines virtuellen Netzwerks müssen Sie eine Ressourcengruppe für das virtuelle Netzwerk und alle anderen in diesem Artikel erstellten Ressourcen erstellen. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVirtualNetwork1* mit dem Adresspräfix *10.0.0.0/16* erstellt.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Erstellen Sie ein virtuelles Netzwerk namens *myVirtualNetwork2* mit dem Adresspräfix *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Einrichten eines Peerings von virtuellen Netzwerken

Peerings werden zwischen IDs virtueller Netzwerke erstellt. Daher müssen Sie mit [az network vnet show](/cli/azure/network/vnet) zuerst die ID jedes virtuellen Netzwerks abrufen und die ID in einer Variablen speichern.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Erstellen Sie mit [az network vnet peering create](/cli/azure/network/vnet/peering) ein Peering zwischen *myVirtualNetwork1* und *myVirtualNetwork2*. Wenn der `--allow-vnet-access`-Parameter nicht angegeben ist, wird ein Peering eingerichtet, es ist jedoch keine Kommunikation über diesen möglich.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

In der Ausgabe, die nach der Ausführung des vorherigen Befehl zurückgegeben wird, können Sie sehen, dass **peeringState** auf *Initiiert* steht. Das Peering verbleibt im Zustand *Initiiert*, bis Sie das Peering zwischen *myVirtualNetwork2* und *myVirtualNetwork1* einrichten. Richten Sie das Peering zwischen *myVirtualNetwork2* und *myVirtualNetwork1* ein. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

In der Ausgabe, die nach der Ausführung des vorherigen Befehl zurückgegeben wird, können Sie sehen, dass **peeringState** auf *Verbunden* steht. Azure hat zudem den Peeringstatus des Peerings *myVirtualNetwork1-myVirtualNetwork2* in *Verbunden* geändert. Überprüfen Sie mit [az network vnet peering show](/cli/azure/network/vnet/peering), ob der Peeringstatus für das Peering *myVirtualNetwork1-myVirtualNetwork2* in *Verbunden* geändert wurde.

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Ressourcen in einem virtuelles Netzwerk können erst dann mit Ressourcen im anderen virtuellen Netzwerk kommunizieren, wenn **peeringState** für die Peerings in beiden virtuellen Netzwerken auf *Verbunden* steht. 

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie eine VM in jedem virtuellen Netzwerk, damit in einem späteren Schritt die Kommunikation untereinander möglich ist.

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Im folgenden Beispiel wird eine VM namens *myVm1* im virtuellen Netzwerk *myVirtualNetwork1* erstellt. Wenn SSH-Schlüssel nicht bereits an einem Standardschlüsselspeicherort vorhanden sind, werden sie durch den Befehl erstellt. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`. Mit der Option `--no-wait` wird der virtuelle Computer im Hintergrund erstellt, sodass Sie mit dem nächsten Schritt fortfahren können.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

Erstellen Sie eine VM im virtuellen Netzwerk *myVirtualNetwork2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Notieren Sie sich **publicIpAddress**. Über diese Adresse wird in einem späteren Schritt über das Internet auf den virtuellen Computer zugegriffen.

## <a name="communicate-between-vms"></a>Kommunikation zwischen VMs

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit der *myVm2*-VM. Ersetzen Sie `<publicIpAddress>` durch die öffentliche IP-Adresse der VM. Im vorherigen Beispiel lautet die öffentliche IP-Adresse *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Pingen Sie die VM in *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Sie erhalten vier Antworten. 

Schließen Sie die SSH-Sitzung mit der *myVm2*-VM. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group) entfernen.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie zwei Netzwerke in derselben Azure-Region durch das Peering virtueller Netzwerke verbunden werden. Sie können auch virtuelle Netzwerke in unterschiedlichen [ unterstützten Regionen](virtual-network-manage-peering.md#cross-region) und [verschiedenen Azure-Abonnements](create-peering-different-subscriptions.md#cli) durch Peering verbinden und [Netzwerke vom Typ „Nabe und Speiche“](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) mit Peering erstellen. Weitere Informationen zum Peering in virtuellen Netzwerken finden Sie unter [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md) und [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md).

Sie können über ein VPN [eine Verbindung zwischen Ihrem eigenen Computer und einem virtuellen Netzwerk herstellen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und mit Ressourcen in einem virtuellen Netzwerk oder in durch Peering verbundenen virtuellen Netzwerken interagieren. Wiederverwendbare Skripts, um viele der in den Artikeln zu virtuellen Netzwerken behandelten Aufgaben durchzuführen, finden Sie unter [Azure CLI-Beispiele für Linux-VMs](cli-samples.md).
