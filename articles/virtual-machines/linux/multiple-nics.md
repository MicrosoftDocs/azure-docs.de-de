---
title: Erstellen eines virtuellen Linux-Computers in Azure mit mehreren Netzwerkkarten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle oder mithilfe von Resource Manager-Vorlagen einen virtuellen Linux-Computer mit mehreren angefügten Netzwerkkarten erstellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: 838afe38cc3b2b98ddad358ddb23ab6b6727e867
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236855"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Erstellen eines virtuellen Linux-Computers in Azure mit mehreren Netzwerkschnittstellenkarten


In diesem Artikel erfahren Sie, wie Sie eine VM mit mehreren Netzwerkkarten mithilfe der Azure CLI erstellen.

## <a name="create-supporting-resources"></a>Erstellen von unterstützenden Ressourcen
Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2), und melden Sie sich mit [az login](/cli/azure/reference-index) bei einem Azure-Konto an.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u.a. *myResourceGroup*, *mystorageaccount* und *myVM*.

Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Erstellen Sie das virtuelle Netzwerk mit [az network vnet create](/cli/azure/network/vnet). Im folgenden Beispiel werden das virtuelle Netzwerk *myVnet* und das Subnetz *mySubnetFrontEnd* erstellt:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet) ein Subnetz für den Back-End-Datenverkehr. Im folgenden Beispiel wird das Subnetz *mySubnetBackEnd* erstellt:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg) eine Netzwerksicherheitsgruppe. Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens *myNetworkSecurityGroup* erstellt:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Erstellen und Konfigurieren mehrerer Netzwerkkarten
Erstellen Sie mit [az network nic create](/cli/azure/network/nic) zwei Netzwerkkarten. Im folgenden Beispiel werden die beiden mit der Netzwerksicherheitsgruppe verknüpften Netzwerkkarten *myNic1* und *myNic2* erstellt. Jede dieser Netzwerkkarten ist mit allen Subnetzen verbunden:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Erstellen eines virtuellen Computers und Anfügen der Netzwerkkarten
Wenn Sie den virtuellen Computer erstellen, geben Sie mit `--nics` die Netzwerkkarten an, die Sie erstellt haben. Gehen Sie umsichtig vor, wenn Sie die Größe der virtuellen Computer auswählen. Sie können einem virtuellen Computer nur eine bestimmte Anzahl von Netzwerkkarten hinzufügen. Weitere Informationen finden Sie unter [Linux-VM-Größen](sizes.md).

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Fügen Sie dem Gastbetriebssystem Routingtabellen hinzu, indem Sie die Schritte unter [Konfigurieren des Gastbetriebssystems für mehrere Netzwerkadapter](#configure-guest-os-for-multiple-nics) ausführen.

## <a name="add-a-nic-to-a-vm"></a>Hinzufügen einer Netzwerkkarte zu einem virtuellen Computer
In den vorangegangenen Schritten wurde ein virtueller Computer mit mehreren Netzwerkkarten erstellt. Sie können mit der Azure CLI auch einer vorhandenen VM Netzwerkkarten hinzufügen. Verschiedene [VM-Größen](sizes.md) unterstützen eine unterschiedliche Anzahl von Netzwerkkarten, passen Sie die Größe Ihres virtuellen Computers daher entsprechend an. Bei Bedarf können Sie die [Größe eines virtuellen Computers ändern](change-vm-size.md).

Erstellen Sie mit [az network nic create](/cli/azure/network/nic) eine weitere Netzwerkkarte. Das folgende Beispiel erstellt die Netzwerkkarte *myNic3*, die mit dem Back-End-Subnetz und der Netzwerksicherheitsgruppe verbunden ist, die in den vorherigen Schritten erstellt wurden:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Um einem vorhandenen virtuellen Computer eine Netzwerkkarte hinzuzufügen, heben Sie zunächst die Zuordnung des virtuellen Computers mit [az vm deallocate](/cli/azure/vm) auf. Im folgenden Beispiel wird die Zuordnung für den virtuellen Computer *myVM* aufgehoben:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Fügen Sie die Netzwerkkarte mit [az vm nic add](/cli/azure/vm/nic) hinzu. Im folgenden Beispiel wird *myVM* die Netzwerkkarte *myNic3* hinzugefügt:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starten Sie den virtuellen Computer mit [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Fügen Sie dem Gastbetriebssystem Routingtabellen hinzu, indem Sie die Schritte unter [Konfigurieren des Gastbetriebssystems für mehrere Netzwerkadapter](#configure-guest-os-for-multiple-nics) ausführen.

## <a name="remove-a-nic-from-a-vm"></a>Entfernen einer Netzwerkkarte von einem virtuellen Computer
Um eine Netzwerkkarte von einem vorhandenen virtuellen Computer zu entfernen, heben Sie zunächst die Zuordnung des virtuellen Computers mit [az vm deallocate](/cli/azure/vm) auf. Im folgenden Beispiel wird die Zuordnung für den virtuellen Computer *myVM* aufgehoben:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Entfernen Sie die Netzwerkkarte mit [az vm nic remove](/cli/azure/vm/nic). Im folgenden Beispiel wird *myNic3* von *myVM* entfernt:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Starten Sie den virtuellen Computer mit [az vm start](/cli/azure/vm):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Erstellen von mehreren Netzwerkkarten mithilfe von Resource Manager-Vorlagen
Azure Resource Manager-Vorlagen verwenden deklarative JSON-Dateien zum Definieren Ihrer Umgebung. Lesen Sie eine [Übersicht über Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Resource Manager-Vorlagen bieten eine Möglichkeit, während der Bereitstellung mehrere Instanzen einer Ressource zu erstellen – z.B. mehrere Netzwerkkarten. Mit *copy* geben Sie die Anzahl der zu erstellenden Instanzen an:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Informieren Sie sich über das [Erstellen mehrerer Instanzen mithilfe von *copy*](../../resource-group-create-multiple.md). 

Sie können auch `copyIndex()` verwenden und eine Zahl an einen Ressourcennamen anfügen, sodass Sie `myNic1`, `myNic2` usw. erstellen können. Das folgende Beispiel veranschaulicht das Anfügen des Indexwerts:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Ein vollständiges Beispiel finden Sie unter [Erstellen von mehreren Netzwerkkarten mithilfe von Resource Manager-Vorlagen](../../virtual-network/template-samples.md).

Fügen Sie dem Gastbetriebssystem Routingtabellen hinzu, indem Sie die Schritte unter [Konfigurieren des Gastbetriebssystems für mehrere Netzwerkadapter](#configure-guest-os-for-multiple-nics) ausführen.

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurieren von Gastbetriebssystem für mehrere Netzwerkadapter

Im vorherigen Schritt wurden ein virtuelles Netzwerk und Subnetz erstellt, dann wurden Netzwerkadapter angefügt, und anschließend wurde ein virtueller Computer erstellt. Eine öffentliche IP-Adresse und Netzwerksicherheitsgruppen-Regeln, die SSH-Datenverkehr zulassen, wurden nicht erstellt. Um das Gastbetriebssystem für mehrere Netzwerkadapter zu konfigurieren, müssen Sie Remoteverbindungen zulassen und Befehle lokal auf dem virtuellen Computer ausführen.

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) wie folgt eine Netzwerksicherheitsgruppen-Regel, um SSH-Datenverkehr zuzulassen:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Erstellen Sie eine öffentliche IP-Adresse mit [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create), und weisen Sie sie dem ersten Netzwerkadapter mit [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) zu:

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

Zeigen Sie die öffentliche IP-Adresse des virtuellen Computers mithilfe von [az vm show](/cli/azure/vm#az-vm-show) wie folgt an:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Stellen Sie nun eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihres virtuellen Computers her. Der in einem vorherigen Schritt angegebene Standardbenutzername war *azureuser*. Geben Sie Ihren eigenen Benutzernamen und die öffentliche IP-Adresse an:

```bash
ssh azureuser@137.117.58.232
```

Zum Senden an oder von einer sekundären Netzwerkschnittstelle müssen Sie manuell für jede sekundäre Netzwerkschnittstelle beständige Routen zum Betriebssystem hinzufügen. In diesem Artikel ist *eth1* die sekundäre Schnittstelle. Anweisungen zum Hinzufügen beständiger Routen zum Betriebssystem variieren je nach Distribution. Anweisungen finden Sie in der Dokumentation für Ihre Distribution.

Wenn Sie die Route zum Betriebssystem hinzufügen, lautet die Gatewayadresse für jedes Subnetz, in dem sich die Netzwerkschnittstelle befindet, *.1*. Wenn die Netzwerkschnittstelle beispielsweise der Adresse *10.0.2.4* zugewiesen ist, legen Sie für die Route das Gateway *10.0.2.1* fest. Sie können ein bestimmtes Netzwerk für das Ziel der Route definieren oder das Ziel *0.0.0.0* angeben, wenn der gesamte Datenverkehr für die Schnittstelle das angegebene Gateway durchlaufen soll. Das Gateway für jedes Subnetz wird vom virtuellen Netzwerk verwaltet.

Nachdem Sie die Route für eine sekundäre Schnittstelle hinzugefügt haben, prüfen Sie, ob die Route in der Routingtabelle mit `route -n` enthalten ist. Die folgende Beispielausgabe gilt für die Routingtabelle, die die beiden Netzwerkschnittstellen enthält, die in diesem Artikel dem virtuellen Computer hinzugefügt wurden:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Vergewissern Sie sich, dass die hinzugefügte Route nach einem Neustart beibehalten wird, indem Sie die Routingtabelle nach einem Neustart erneut prüfen. Zum Testen der Konnektivität können Sie z.B. den folgenden Befehl eingeben, wobei *eth1* der Name einer sekundären Netzwerkschnittstelle ist:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die [Linux-VM-Größen](sizes.md), wenn Sie einen virtuellen Computer mit mehreren Netzwerkkarten erstellen. Achten Sie auf die maximale Anzahl von Netzwerkkarten, die von jeder VM-Größe unterstützt wird.

Schützen Sie Ihre virtuellen Computer zusätzlich mit Just-in-Time-VM-Zugriff. Dieses Feature öffnet bei Bedarf und für einen festgelegten Zeitraum Netzwerksicherheitsgruppen-Regeln für den SSH-Datenverkehr. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](../../security-center/security-center-just-in-time.md).
