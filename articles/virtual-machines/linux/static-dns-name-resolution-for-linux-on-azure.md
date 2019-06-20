---
title: Verwenden des internen DNS für die Auflösung von virtuellen Computernamen mit der Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie über die Azure CLI virtuelle Netzwerkkarten erstellen und das interne DNS für die Auflösung von virtuellen Computernamen in Azure verwenden.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 5e893d597c2193676cb350fc80d7baa694ad6fd1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60478258"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Erstellen von virtuellen Netzwerkkarten und Verwenden des internen DNS für die Auflösung von virtuellen Computernamen in Azure

In diesem Artikel wird gezeigt, wie Sie über die Azure CLI mithilfe von virtuellen Netzwerkkarten (vNICs) und DNS-Bezeichnungsnamen statische interne DNS-Namen für virtuelle Linux-Computer festlegen. Statische DNS-Namen werden für permanente Infrastrukturdienste wie einen Jenkins-Buildserver, der in diesem Dokument als Beispiel dient, oder einen Git-Server verwendet.

Folgende Anforderungen müssen erfüllt sein:

* [ein Azure-Konto](https://azure.microsoft.com/pricing/free-trial/)
* [Dateien mit den öffentlichen und privaten SSH-Schlüsseln](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Schnellbefehle
Falls Sie die Aufgabe schnell durchführen müssen, finden Sie im folgenden Abschnitt eine Erläuterung der erforderlichen Befehle. Ausführlichere Informationen und Kontext zu den einzelnen Schritten finden Sie im übrigen Dokument ([ab hier](#detailed-walkthrough)). Zum Ausführen dieser Schritte muss die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein.

Voraussetzungen: Ressourcengruppe, virtuelles Netzwerk und Subnetz, Netzwerksicherheitsgruppe mit eingehenden SSH-Verbindungen

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Erstellen einer virtuellen Netzwerkkarte mit einem statischen internen DNS-Namen
Erstellen Sie mit [az network nic create](/cli/azure/network/nic) die vNIC. Das Befehlszeilenschnittstellen-Flag `--internal-dns-name` dient zum Festlegen der DNS-Bezeichnung, die den statischen DNS-Namen für die vNIC bereitstellt. Das folgende Beispiel erstellt eine vNIC namens `myNic`, verbindet diese mit dem virtuellen Netzwerk `myVnet` und erstellt den internen DNS-Namenseintrag `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Bereitstellen eines virtuellen Computers und Herstellen der Verbindung mit der vNIC
Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Das Flag `--nics` verbindet die vNIC während der Bereitstellung in Azure mit dem virtuellen Computer. Das folgende Beispiel verwendet Azure Managed Disks, um einen virtuellen Computer namens `myVM` zu erstellen, und fügt dann die vNIC namens `myNic` aus dem vorherigen Schritt an diesen Computer an:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Ausführliche exemplarische Vorgehensweise

Für eine vollständige CiCd-Infrastruktur (Continuous Integration und Continuous Deployment) in Azure müssen bestimmte Server statisch oder langlebige Server sein. Azure-Ressourcen wie virtuelle Netzwerke und Netzwerksicherheitsgruppen sollten statische und langlebige Ressourcen sein, die nur selten bereitgestellt werden. Sobald ein virtuelles Netzwerk bereitgestellt wurde, kann es von neuen Bereitstellungen ohne Nebenwirkungen auf die Infrastruktur wiederverwendet werden. Sie können später einen Git-Repositoryserver oder einen Jenkins-Automatisierungsserver hinzufügen, um dieses virtuelle Netzwerk um CiCd für Ihre Entwicklungs- oder Testumgebungen zu ergänzen.  

Interne DNS-Namen können nur innerhalb eines virtuellen Azure-Netzwerks aufgelöst werden. Da die DNS-Namen intern sind, können sie nicht für das externe Internet aufgelöst werden, wodurch die Sicherheit der Infrastruktur zusätzlich erhöht wird.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Als Beispielparameternamen werden `myResourceGroup`, `myNic` und `myVM` verwendet.

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen
Erstellen Sie zunächst die Ressourcengruppe mithilfe von [az group create](/cli/azure/group). Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

Der nächste Schritt besteht darin, ein virtuelles Netzwerk zu erstellen, in dem die virtuellen Computer gestartet werden sollen. Das virtuelle Netzwerk enthält ein Subnetz für diese exemplarische Vorgehensweise. Weitere Informationen zu virtuellen Azure-Netzwerken finden Sie unter [Create, change, or delete a virtual network](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) (Erstellen, Ändern oder Löschen eines virtuellen Netzwerks). 

Erstellen Sie das virtuelle Netzwerk mit [az network vnet create](/cli/azure/network/vnet). Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen `myVnet` und ein Subnetz mit dem Namen `mySubnet` erstellt:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Erstellen der Netzwerksicherheitsgruppe
Azure-Netzwerksicherheitsgruppen sind gleichwertig mit einer Firewall auf Netzwerkebene. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Erstellen von NSGs in der Azure-Befehlszeilenschnittstelle](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Erstellen Sie die Netzwerksicherheitsgruppe mit [az network nsg create](/cli/azure/network/nsg). Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens `myNetworkSecurityGroup` erstellt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Hinzufügen einer eingehenden Regel zum Zulassen von SSH
Fügen Sie mithilfe von [az network nsg rule create](/cli/azure/network/nsg/rule) eine eingehende Regel für die Netzwerksicherheitsgruppe hinzu. Im folgenden Beispiel wird eine Regel namens `myRuleAllowSSH` erstellt:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Zuordnen des Subnetzes zur Netzwerksicherheitsgruppe
Um das Subnetz zur Netzwerksicherheitsgruppe zuzuordnen, verwenden Sie [az network vnet subnet update](/cli/azure/network/vnet/subnet). Im folgenden Beispiel wird das Subnetz mit dem Namen `mySubnet` zur Netzwerksicherheitsgruppe `myNetworkSecurityGroup` zugeordnet:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Erstellen der virtuellen Netzwerkkarte und der statischen DNS-Namen
Azure ist äußerst flexibel. Um jedoch DNS-Namen für die Namensauflösung von virtuellen Computern verwenden zu können, müssen Sie virtuelle Netzwerkkarten (vNICs) erstellen, die eine DNS-Bezeichnung enthalten. vNICs sind wichtig, da Sie sie durch Verbinden mit verschiedenen virtuellen Computern während des gesamten Lebenszyklus der Infrastruktur wiederverwenden können. Auf diese Weise können Sie die vNIC als statische Ressource beibehalten, während die virtuellen Computer temporär sein können. Durch die Verwendung von DNS-Bezeichnungen für die vNIC können Sie eine einfache Namensauflösung von anderen virtuellen Computern im virtuellen Netzwerk ermöglichen. Wenn auflösbare Namen verwendet werden, können andere VMs anhand des DNS-Namens `Jenkins` auf den Automatisierungsserver oder mit `gitrepo` auf den Git-Server zugreifen.  

Erstellen Sie mit [az network nic create](/cli/azure/network/nic) die vNIC. Das folgende Beispiel erstellt eine vNIC namens `myNic`, verbindet diese `myVnet` mit dem virtuellen Netzwerk `myVnet` und erstellt den internen DNS-Namenseintrag `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Bereitstellen des virtuellen Computers in der virtuellen Netzwerkinfrastruktur
Wir verfügen jetzt über ein virtuelles Netzwerk, ein Subnetz und eine Netzwerksicherheitsgruppe, die als Firewall fungiert und unser Subnetz schützt, indem sämtlicher eingehender Datenverkehr mit Ausnahme von Port 22 für SSH blockiert wird. Wir verfügen zudem über eine virtuelle Netzwerkkarte. Jetzt können Sie in dieser bestehenden Netzwerkinfrastruktur einen virtuellen Computer bereitstellen.

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Das folgende Beispiel verwendet Azure Managed Disks, um einen virtuellen Computer namens `myVM` zu erstellen, und fügt dann die vNIC namens `myNic` aus dem vorherigen Schritt an diesen Computer an:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Mithilfe der CLI-Flags zum Aufruf vorhandener Ressourcen weisen wir Azure an, die VM im vorhandenen Netzwerk bereitzustellen. In anderen Worten: Sobald ein VNET und ein Subnetz bereitgestellt wurden, können sie als statische oder permanente Ressourcen innerhalb Ihrer Azure-Region beibehalten werden.  

## <a name="next-steps"></a>Nächste Schritte
* [Direktes Erstellen einer benutzerdefinierten Umgebung für einen virtuellen Linux-Computer über Azure-CLI-Befehle](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Erstellen einer Linux-VM in Azure mithilfe von Vorlagen](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
