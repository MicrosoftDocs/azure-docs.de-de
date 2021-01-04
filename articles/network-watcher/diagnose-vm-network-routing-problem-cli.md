---
title: Diagnostizieren von Problemen mit dem Netzwerkrouting einer VM – Azure-Befehlszeilenschnittstelle
titleSuffix: Azure Network Watcher
description: In diesem Artikel erfahren Sie, wie Sie die Azure CLI verwenden, um mithilfe der Funktion „Nächster Hop“ von Azure Network Watcher ein Netzwerkroutingproblem eines virtuellen Computers zu diagnostizieren.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 2d5f6f9cfaff722245f6105b5e86390b8aeb769f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539718"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnostizieren eines Netzwerkroutingproblems eines virtuellen Computers – Azure CLI

In diesem Artikel stellen Sie einen virtuellen Computer (Virtual Machine, VM) bereit und überprüfen dann die Kommunikation mit einer IP-Adresse und URL. Sie ermitteln die Ursache eines Kommunikationsfehlers und wie Sie ihn beheben können.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

- Die Azure CLI-Befehle in diesem Artikel sind für die Ausführung in einer Bash-Shell formatiert.

## <a name="create-a-vm"></a>Erstellen einer VM

Bevor Sie einen virtuellen Computer erstellen können, müssen Sie eine Ressourcengruppe erstellen, die den virtuellen Computer enthalten soll. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer. Wenn SSH-Schlüssel nicht bereits an einem Standardschlüsselspeicherort vorhanden sind, werden sie durch den Befehl erstellt. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`. Im folgenden Beispiel wird ein virtueller Computer namens *myVm* erstellt:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Fahren Sie erst mit den übrigen Schritten fort, wenn der virtuelle Computer erstellt und von Azure CLI eine Ausgabe zurückgegeben wurde.

## <a name="test-network-communication"></a>Testen der Netzwerkkommunikation

Wenn Sie die Netzwerkkommunikation mit Network Watcher testen möchten, müssen Sie zuerst eine Komponente zur Netzwerküberwachung in der Region aktivieren, in der sich der zu testende virtuelle Computer befindet. Danach können Sie die Kommunikation mit der Network Watcher-Funktion „Nächster Hop“ testen.

### <a name="enable-network-watcher"></a>Aktivieren von Network Watcher

Wenn Sie bereits über eine aktivierte Network Watcher-Instanz in der Region „USA, Osten“ verfügen, fahren Sie mit [Verwenden der Funktion „Nächster Hop“](#use-next-hop) fort. Erstellen Sie mithilfe des Befehls [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) eine Network Watcher-Instanz in der Region „USA, Osten“:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Verwenden der Funktion „Nächster Hop“

Azure erstellt automatisch Routen zu Standardzielen. Sie können benutzerdefinierte Routen erstellen, mit denen die Standardrouten außer Kraft gesetzt werden. In manchen Fällen können benutzerdefinierte Routen zu Fehlern bei der Kommunikation führen. Verwenden Sie zum Testen des Routings von einem virtuellen Computer den Befehl [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop), um den nächsten Routinghop zu ermitteln, wenn der Datenverkehr für eine bestimmte Adresse bestimmt ist.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit einer der IP-Adressen für www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Nach wenigen Sekunden werden Sie in der Ausgabe darüber informiert, dass der **nextHopType** **Internet** und die **routeTableId** **Systemroute** lautet. Durch dieses Ergebnis wissen Sie, dass eine gültige Route zum Ziel besteht.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

In der zurückgegebenen Ausgabe werden Sie darüber informiert, dass **Keiner** der **nextHopType** ist und die **routeTableId** ebenfalls **Systemroute** lautet. Durch dieses Ergebnis wissen Sie, dass zwar eine gültige Systemroute zum Ziel besteht, aber kein nächster Hop zum Weiterleiten des Datenverkehrs an das Ziel vorhanden ist.

## <a name="view-details-of-a-route"></a>Anzeigen von Details einer Route

Zur weiteren Analyse des Routings überprüfen Sie die effektiven Routen für die Netzwerkschnittstelle mit dem Befehl [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table):

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Die zurückgegebene Ausgabe enthält den folgenden Text:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Wenn Sie unter [Verwenden der Funktion „Nächster Hop“](#use-next-hop) den Befehl `az network watcher show-next-hop` zum Testen der ausgehenden Kommunikation mit 13.107.21.200 verwendet haben, wurde die Route mit dem **Adresspräfix** 0.0.0.0/0** zum Weiterleiten des Datenverkehrs an die Adresse verwendet, weil keine andere Route in der Ausgabe die Adresse enthält. Standardmäßig werden alle Adressen, die nicht im Adresspräfix einer anderen Route angegeben sind, an das Internet weitergeleitet.

Wenn Sie jedoch den Befehl `az network watcher show-next-hop` zum Testen der ausgehenden Kommunikation mit 172.31.0.100 verwendet haben, wurden Sie im Ergebnis darüber informiert, dass kein Typ des nächsten Hops vorhanden war. In der zurückgegebenen Ausgabe finden Sie auch den folgenden Text:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Wie Sie in der Ausgabe des Befehls `az network watcher nic show-effective-route-table` sehen können, ist zwar eine Standardroute zum Präfix 172.16.0.0/12 mit der Adresse 172.31.0.100 vorhanden, aber der **nextHopType** lautet **Keiner**. Azure erstellt eine Standardroute zu 172.16.0.0/12, gibt aber einen Typ des nächsten Hops erst dann an, wenn es einen Grund dafür gibt. Wenn Sie z. B. den Adressbereich 172.16.0.0/12 zum Adressraum des virtuellen Netzwerks hinzufügen, ändert Azure den **nextHopType** für die Route in **Virtuelles Netzwerk**. Bei einer Überprüfung würde dann **Virtuelles Netzwerk** als **nextHopType** angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group#az-group-delete) entfernen:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen virtuellen Computer erstellt und das Netzwerkrouting vom virtuellen Computer diagnostiziert. Sie haben erfahren, dass Azure mehrere Standardrouten erstellt, und Sie haben das Routing an zwei verschiedene Ziele getestet. Erfahren Sie mehr über das [Routing in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und das [Erstellen benutzerdefinierter Routen](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Für ausgehende Verbindungen virtueller Computer können Sie auch die Latenz sowie den zulässigen und verweigerten Netzwerkdatenverkehr zwischen dem virtuellen Computer und einem Endpunkt mithilfe der Funktion [Problembehandlung für Verbindung](network-watcher-connectivity-cli.md) von Network Watcher bestimmen. Sie können die Kommunikation zwischen einem virtuellen Computer und einem Endpunkt, z. B. einer IP-Adresse oder URL, mithilfe der Verbindungsmonitorfunktion von Network Watcher über einen Zeitraum überwachen. Informationen hierzu finden Sie unter [Überwachen einer Netzwerkverbindung](connection-monitor.md).
