---
title: 'Schnellstart: Diagnostizieren von Problemen mit dem Filter für VM-Netzwerkdatenverkehr – Azure-Befehlszeilenschnittstelle'
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie die Azure CLI verwenden, um mithilfe der IP-Flussüberprüfungsfunktion von Azure Network Watcher Probleme mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers diagnostizieren.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bd99fe0ea8a92ad05ad258dcf4d8da6e4685f263
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492521"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Schnellstart: Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers – Azure CLI

In dieser Schnellstartanleitung stellen Sie einen virtuellen Computer (Virtual Machine, VM) bereit und überprüfen dann die ausgehende Kommunikation für eine IP-Adresse und URL sowie die eingehende Kommunikation von einer IP-Adresse. Sie ermitteln die Ursache eines Kommunikationsfehlers und wie Sie ihn beheben können.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Schnellstart ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

- Die Azure CLI-Befehle in dieser Schnellstartanleitung sind für die Ausführung in einer Bash-Shell formatiert.

## <a name="create-a-vm"></a>Erstellen einer VM

Bevor Sie einen virtuellen Computer erstellen können, müssen Sie eine Ressourcengruppe erstellen, die den virtuellen Computer enthalten soll. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Wenn SSH-Schlüssel nicht bereits an einem Standardschlüsselspeicherort vorhanden sind, werden sie durch den Befehl erstellt. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`. Im folgenden Beispiel wird ein virtueller Computer namens *myVm* erstellt:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Fahren Sie erst mit den übrigen Schritten fort, wenn der virtuelle Computer erstellt und von Azure CLI eine Ausgabe zurückgegeben wurde.

## <a name="test-network-communication"></a>Testen der Netzwerkkommunikation

Wenn Sie die Netzwerkkommunikation mit Network Watcher testen möchten, müssen Sie zuerst eine Network Watcher-Instanz in der Region aktivieren, in der sich der zu testende virtuelle Computer befindet. Danach können Sie die Kommunikation mit der IP-Flussüberprüfungsfunktion von Network Watcher testen.

### <a name="enable-network-watcher"></a>Aktivieren von Network Watcher

Wenn Sie bereits über eine aktivierte Network Watcher-Instanz in der Region „USA, Osten“ verfügen, fahren Sie mit [Verwenden der IP-Flussüberprüfung](#use-ip-flow-verify) fort. Erstellen Sie mithilfe des Befehls [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) eine Network Watcher-Instanz in der Region „eastus“:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Verwenden der IP-Flussüberprüfung

Wenn Sie einen virtuellen Computer erstellen, wird der ein- und ausgehende Netzwerkdatenverkehr des virtuellen Computers von Azure standardmäßig zugelassen bzw. abgelehnt. Die Azure-Standardeinstellungen können später außer Kraft gesetzt werden, um zusätzliche Arten von Datenverkehr zuzulassen oder abzulehnen. Verwenden Sie den Befehl [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow), um zu testen, ob der Datenverkehr an verschiedene Ziele und von einer Quell-IP-Adresse zugelassen oder abgelehnt wird.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit einer der IP-Adressen für www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Nach einigen Sekunden werden Sie im zurückgegebenen Ergebnis darüber informiert, dass der Zugriff durch eine Sicherheitsregel mit dem Namen **AllowInternetOutbound** zugelassen wird.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Im zurückgegebenen Ergebnis werden Sie darüber informiert, dass der Zugriff durch eine Sicherheitsregel mit dem Namen **DefaultOutboundDenyAll** verweigert wird.

Testen Sie die eingehende Kommunikation des virtuellen Computers für 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Im zurückgegebenen Ergebnis werden Sie darüber informiert, dass der Zugriff aufgrund einer Sicherheitsregel mit dem Namen **DefaultInboundDenyAll** verweigert wird. Nachdem Sie nun wissen, welche Sicherheitsregeln den ein- und ausgehenden Datenverkehr eines virtuellen Computers zulassen oder ablehnen, können Sie Lösungen für die Probleme ermitteln.

## <a name="view-details-of-a-security-rule"></a>Anzeigen von Details einer Sicherheitsregel

Überprüfen Sie mithilfe des Befehls [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg) die geltenden Sicherheitsregeln für die Netzwerkschnittstelle, um zu ermitteln, warum die Regeln unter [Verwenden der IP-Flussüberprüfung](#use-ip-flow-verify) die Kommunikation zulassen oder verhindern:

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Die zurückgegebene Ausgabe enthält den folgenden Text für die Regel **AllowInternetOutbound**, die in einem vorherigen Schritt unter [Verwenden der IP-Flussüberprüfung](#use-ip-flow-verify) den ausgehenden Zugriff auf www.bing.com zugelassen hat:

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

In der obigen Ausgabe sehen Sie, dass **Internet** als **destinationAddressPrefix** verwendet wird. Der Zusammenhang zwischen 13.107.21.200 und **Internet** wird allerdings nicht klar. Unter **expandedDestinationAddressPrefix** sind mehrere Adresspräfixe aufgelistet. Eines der Präfixe in der Liste ist **12.0.0.0/6**, das den IP-Adressbereich 12.0.0.1 bis 15.255.255.254 umfasst. Da 13.107.21.200 in diesem Adressbereich liegt, lässt die Regel **AllowInternetOutBound** den ausgehenden Datenverkehr zu. Darüber hinaus enthält die obige Ausgabe keine Regeln mit einer höheren Priorität (niedrigere Zahl), die diese Regel außer Kraft setzen. Um die ausgehende Kommunikation mit einer IP-Adresse zu verweigern, können Sie eine Sicherheitsregel mit einer höheren Priorität hinzufügen, die ausgehenden Datenverkehr über den Port 80 für die IP-Adresse verweigert.

Beim Ausführen des Befehls `az network watcher test-ip-flow` zum Testen der ausgehenden Kommunikation mit 172.131.0.100 (im Abschnitt [Verwenden der IP-Flussüberprüfung](#use-ip-flow-verify)) wurden Sie in der Ausgabe darüber informiert, dass die Kommunikation durch die Regel **DefaultOutboundDenyAll** verweigert wurde. Die Regel **DefaultOutboundDenyAll** entspricht der Regel **DenyAllOutBound**, die in der folgenden Ausgabe des Befehls `az network nic list-effective-nsg` aufgeführt ist:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

Die Regel listet **0.0.0.0/0** als **destinationAddressPrefix** auf. Die Regel verweigert die ausgehende Kommunikation mit 172.131.0.100, da die Adresse nicht in **destinationAddressPrefix** einer der anderen ausgehenden Regeln in der Ausgabe des Befehls `az network nic list-effective-nsg` enthalten ist. Um die ausgehende Kommunikation zuzulassen, können Sie eine Sicherheitsregel mit einer höheren Priorität hinzufügen, die ausgehenden Datenverkehr über den Port 80 für 172.131.0.100 zulässt.

Beim Ausführen des Befehls `az network watcher test-ip-flow` zum Testen der eingehenden Kommunikation von 172.131.0.100 (im Abschnitt [Verwenden der IP-Flussüberprüfung](#use-ip-flow-verify)) wurden Sie in der Ausgabe darüber informiert, dass die Kommunikation durch die Regel **DefaultInboundDenyAll** verweigert wurde. Die Regel **DefaultInboundDenyAll** entspricht der Regel **DenyAllInBound**, die in der folgenden Ausgabe des Befehls `az network nic list-effective-nsg` aufgeführt ist:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Die Regel **DenyAllInBound** wird angewendet, da in der Ausgabe des Befehls `az network nic list-effective-nsg` keine andere Regel mit einer höheren Priorität vorhanden ist, die auf dem virtuellen Computer eingehenden Datenverkehr von 172.131.0.100 am Port 80 zulässt. Um die eingehende Kommunikation zuzulassen, können Sie eine Sicherheitsregel mit einer höheren Priorität hinzufügen, die eingehenden Datenverkehr von 172.131.0.100 am Port 80 zulässt.

Mit den Überprüfungen in dieser Schnellstartanleitung wurde die Azure-Konfiguration getestet. Wenn die Überprüfungen zwar die erwarteten Ergebnisse zurückgeben, aber weiterhin Netzwerkprobleme auftreten, stellen Sie sicher, dass zwischen Ihrem virtuellen Computer und dem Endpunkt, mit dem Sie kommunizieren, keine Firewall vorhanden ist und das Betriebssystem auf Ihrem virtuellen Computer nicht über eine Firewall verfügt, die die Kommunikation zulässt oder verweigert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit [az group delete](/cli/azure/group) entfernen:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen virtuellen Computer erstellt sowie Filter für ein- und ausgehenden Netzwerkdatenverkehr diagnostiziert. Sie haben gelernt, dass Netzwerksicherheitsgruppen-Regeln den ein- und ausgehenden Datenverkehr eines virtuellen Computers zulassen oder verweigern. Erfahren Sie mehr über [Sicherheitsregeln](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und das [Erstellen von Sicherheitsregeln](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Auch wenn für den Netzwerkdatenverkehr die richtigen Filter vorhanden sind, kann die Kommunikation mit einem virtuellen Computer aufgrund der Routingkonfiguration fehlschlagen. Informationen zum Diagnostizieren von Routingproblemen in VM-Netzwerken finden Sie unter [Diagnostizieren von VM-Routingproblemen](diagnose-vm-network-routing-problem-cli.md). Unter [Problembehandlung für Verbindungen](network-watcher-connectivity-cli.md) erfahren Sie außerdem, wie Sie mit nur einem Tool Probleme mit Ausgangsrouting und Wartezeiten sowie Probleme mit dem Filtern des Datenverkehrs diagnostizieren.