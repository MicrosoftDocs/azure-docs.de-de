---
title: Erstellen einer vollständigen Linux-Umgebung mit der klassischen Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Speicher, einen virtuellen Linux-Computer, ein virtuelles Netzwerk mitsamt Subnetz, einen Lastenausgleich, einen Netzwerkadapter, eine öffentliche IP-Adresse und eine Netzwerksicherheitsgruppe über die klassische Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) von Grund auf neu erstellen.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4ba4060b-ce95-4747-a735-1d7c68597a1a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 04c1d69fc46b9a918038e93c4fc56681f225d365
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328713"
---
# <a name="create-a-complete-linux-environment-with-the-azure-classic-cli"></a>Erstellen einer vollständigen Linux-Umgebung mit der klassischen Azure-Befehlszeilenschnittstelle
In diesem Artikel erstellen wir ein einfaches Netzwerk mit einem Load Balancer und zwei virtuellen Computern, die zu Entwicklungszwecken und für einfache Berechnungen nützlich sind. Wir führen Sie Befehl für Befehl durch den Prozess, bis Sie über zwei funktionierende, sichere virtuelle Linux-Computer verfügen, mit denen Sie über das Internet von jedem Ort aus eine Verbindung herstellen können. Anschließend können Sie sich mit komplexeren Netzwerken und Umgebungen beschäftigen.

Im Laufe dieses Vorgangs lernen Sie die Abhängigkeitshierarchie kennen, die Sie mit dem Resource Manager-Bereitstellungsmodell erhalten, sowie die damit verbundene hohe Leistungsfähigkeit. Wenn Sie wissen, wie das System aufgebaut ist, können Sie es mithilfe von [Azure Resource Manager-Vorlagen](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)deutlich schneller neu erstellen. Und wenn Sie wissen, wie die einzelnen Komponenten Ihrer Umgebung zusammenwirken, ist die Erstellung von Vorlagen für deren Automatisierung viel einfacher.

Die Umgebung enthält:

* Zwei virtuelle Computer in einer Verfügbarkeitsgruppe
* Load Balancer mit Lastenausgleichsregel an Port 80
* Regeln für Netzwerksicherheitsgruppen (NSG) zum Schutz der virtuellen Computer vor unerwünschtem Datenverkehr

Um diese benutzerdefinierte Umgebung zu erstellen, benötigen Sie die aktuelle [klassische Azure-Befehlszeilenschnittstelle](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) im Resource Manager-Modus (`azure config mode arm`). Sie benötigen außerdem ein JSON-Analysetool. In diesem Beispiel wird [jq](https://stedolan.github.io/jq/)verwendet.


## <a name="cli-versions-to-complete-the-task"></a>CLI-Versionen zum Durchführen dieser Aufgabe
Führen Sie die Aufgabe mit einer der folgenden CLI-Versionen durch:

- [Klassische Azure-Befehlszeilenschnittstelle](#quick-commands): Unsere CLI für das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell (in diesem Artikel)
- [Azure-Befehlszeilenschnittstelle](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Unsere CLI der nächsten Generation für das Resource Manager-Bereitstellungsmodell


## <a name="quick-commands"></a>Schnellbefehle
Im folgenden Abschnitt werden die grundlegenden Befehlen zum Hochladen eines virtuellen Computers in Azure beschrieben, falls Sie die Aufgabe schnell durchführen müssen. Ausführlichere Informationen und Kontext für die einzelnen Schritte finden Sie im übrigen Dokument ([ab hier](#detailed-walkthrough)).

Vergewissern Sie sich, dass die [klassische Azure-Befehlszeilenschnittstelle](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) angemeldet ist und den Resource Manager-Modus nutzt:

```azurecli
azure config mode arm
```

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Als Beispielparameternamen werden `myResourceGroup`, `mystorageaccount` und `myVM` verwendet.

Erstellen Sie die Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westeurope`:

```azurecli
azure group create -n myResourceGroup -l westeurope
```

Überprüfen Sie die Ressourcengruppe mit dem JSON-Parser:

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Erstellen Sie das Speicherkonto. Das folgende Beispiel erstellt ein Speicherkonto namens `mystorageaccount`. (Der Name des Speicherkontos muss eindeutig sein, geben Sie daher einen eigenen eindeutigen Namen an.)

```azurecli
azure storage account create -g myResourceGroup -l westeurope \
  --kind Storage --sku-name GRS mystorageaccount
```

Überprüfen Sie das Speicherkonto mithilfe des JSON-Parsers:

```azurecli
azure storage account show -g myResourceGroup mystorageaccount --json | jq '.'
```

Erstellen des virtuellen Netzwerks Im folgenden Beispiel wird ein virtuelles Netzwerk namens `myVnet` erstellt:

```azurecli
azure network vnet create -g myResourceGroup -l westeurope\
  -n myVnet -a 192.168.0.0/16
```

Erstellen Sie ein Subnetz. Im folgenden Beispiel wird ein Subnetz namens `mySubnet` erstellt:

```azurecli
azure network vnet subnet create -g myResourceGroup \
  -e myVnet -n mySubnet -a 192.168.1.0/24
```

Überprüfen Sie das virtuelle Netzwerk und Subnetz mit der JSON-Parser:

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Erstellen Sie eine öffentliche IP-Adresse. Im folgenden Beispiel wird eine öffentliche IP-Adresse namens `myPublicIP` mit dem DNS-Namen `mypublicdns` erstellt. (Der DNS-Name muss eindeutig sein. Geben Sie daher einen eigenen eindeutigen Namen an.)

```azurecli
azure network public-ip create -g myResourceGroup -l westeurope \
  -n myPublicIP  -d mypublicdns -a static -i 4
```

Erstellen Sie den Load Balancer. Im folgenden Beispiel wird ein Load Balancer namens `myLoadBalancer` erstellt:

```azurecli
azure network lb create -g myResourceGroup -l westeurope -n myLoadBalancer
```

Erstellen Sie einen Front-End-IP-Pool für den Load Balancer, und ordnen Sie die öffentliche IP-Adresse zu. Im folgenden Beispiel wird ein Front-End-IP-Pool namens `mySubnetPool` erstellt:

```azurecli
azure network lb frontend-ip create -g myResourceGroup -l myLoadBalancer \
  -i myPublicIP -n myFrontEndPool
```

Erstellen Sie den Back-End-IP-Pool für den Load Balancer. Im folgenden Beispiel wird ein Back-End-IP-Pool namens `myBackEndPool` erstellt:

```azurecli
azure network lb address-pool create -g myResourceGroup -l myLoadBalancer \
  -n myBackEndPool
```

Erstellen Sie für den Load Balancer eingehende SSH-Regeln für die Netzwerkadressenübersetzung (Network Address Translation, NAT). Im folgenden Beispiel werden zwei Load Balancer-Regeln (`myLoadBalancerRuleSSH1` und `myLoadBalancerRuleSSH2`) erstellt:

```azurecli
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH1 -p tcp -f 4222 -b 22
azure network lb inbound-nat-rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleSSH2 -p tcp -f 4223 -b 22
```

Erstellen Sie die eingehenden NAT-Regeln (Web) für den Load Balancer. Im folgenden Beispiel wird eine Load Balancer-Regel namens `myLoadBalancerRuleWeb` erstellt:

```azurecli
azure network lb rule create -g myResourceGroup -l myLoadBalancer \
  -n myLoadBalancerRuleWeb -p tcp -f 80 -b 80 \
  -t myFrontEndPool -o myBackEndPool
```

Erstellen Sie den Integritätstest für den Load Balancer. Im folgenden Beispiel wird ein TCP-Test namens `myHealthProbe` erstellt:

```azurecli
azure network lb probe create -g myResourceGroup -l myLoadBalancer \
  -n myHealthProbe -p "tcp" -i 15 -c 4
```

Überprüfen Sie Load Balancer, IP-Pools und NAT-Regeln mithilfe des JSON-Parsers:

```azurecli
azure network lb show -g myResourceGroup -n myLoadBalancer --json | jq '.'
```

Erstellen Sie die erste Netzwerkschnittstellenkarte (NIC, Network Interface Card). Ersetzen Sie die `#####-###-###`-Abschnitte durch Ihre eigene Azure-Abonnement-ID. Ihre Abonnement-ID finden Sie bei der Untersuchung der zu erstellenden Ressourcen in der Ausgabe von **jq**. Die Abonnement-ID kann aber auch mit `azure account list` angezeigt werden.

Im folgenden Beispiel wird eine NIC namens `myNic1` erstellt:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic1 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
```

Erstellen Sie die zweite NIC. Im folgenden Beispiel wird eine NIC namens `myNic2` erstellt:

```azurecli
azure network nic create -g myResourceGroup -l westeurope \
  -n myNic2 -m myVnet -k mySubnet \
  -d "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool" \
  -e "/subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
```

Überprüfen Sie die beiden NICs mithilfe des JSON-Parsers:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
azure network nic show myResourceGroup myNic2 --json | jq '.'
```

Erstellen Sie die Netzwerksicherheitsgruppe. Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens `myNetworkSecurityGroup` erstellt:

```azurecli
azure network nsg create -g myResourceGroup -l westeurope \
  -n myNetworkSecurityGroup
```

Fügen Sie zwei eingehende Regeln für die Netzwerksicherheitsgruppe hinzu. Im folgenden Beispiel werden zwei Regeln (`myNetworkSecurityGroupRuleSSH` und `myNetworkSecurityGroupRuleHTTP`) erstellt:

```azurecli
azure network nsg rule create -p tcp -r inbound -y 1000 -u 22 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleSSH
azure network nsg rule create -p tcp -r inbound -y 1001 -u 80 -c allow \
  -g myResourceGroup -a myNetworkSecurityGroup -n myNetworkSecurityGroupRuleHTTP
```

Überprüfen Sie die Netzwerksicherheitsgruppe und die eingehenden Regeln mithilfe des JSON-Parsers:

```azurecli
azure network nsg show -g myResourceGroup -n myNetworkSecurityGroup --json | jq '.'
```

Binden Sie die Netzwerksicherheitsgruppe an die beiden NICs:

```azurecli
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic1
azure network nic set -g myResourceGroup -o myNetworkSecurityGroup -n myNic2
```

Erstellen Sie die Verfügbarkeitsgruppe. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens `myAvailabilitySet` erstellt:

```azurecli
azure availset create -g myResourceGroup -l westeurope -n myAvailabilitySet
```

Erstellen Sie den ersten virtuellen Linux-Computer. Im folgenden Beispiel wird ein virtueller Computer namens `myVM1` erstellt:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM1 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic1 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Erstellen Sie den zweiten virtuellen Linux-Computer. Im folgenden Beispiel wird ein virtueller Computer namens `myVM2` erstellt:

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM2 \
    --location westeurope \
    --os-type linux \
    --availset-name myAvailabilitySet \
    --nic-name myNic2 \
    --vnet-name myVnet \
    --vnet-subnet-name mySubnet \
    --storage-account-name mystorageaccount \
    --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username azureuser
```

Stellen Sie mit dem JSON-Parser sicher, dass alle Elemente erstellt wurden:

```azurecli
azure vm show -g myResourceGroup -n myVM1 --json | jq '.'
azure vm show -g myResourceGroup -n myVM2 --json | jq '.'
```

Exportieren Sie die neue Umgebung in eine Vorlage, um schnell neue Instanzen erstellen zu können:

```azurecli
azure group export myResourceGroup
```

## <a name="detailed-walkthrough"></a>Ausführliche exemplarische Vorgehensweise
In den folgenden ausführlichen Schritten wird erklärt, was jeder Befehl macht, wenn Sie Ihre Umgebung erstellen. Diese Konzepte helfen Ihnen beim Erstellen Ihrer eigenen benutzerdefinierten Umgebungen für Entwicklung oder Produktion.

Vergewissern Sie sich, dass die [klassische Azure-Befehlszeilenschnittstelle](../../cli-install-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) angemeldet ist und den Resource Manager-Modus nutzt:

```azurecli
azure config mode arm
```

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Als Beispielparameternamen werden `myResourceGroup`, `mystorageaccount` und `myVM` verwendet.

## <a name="create-resource-groups-and-choose-deployment-locations"></a>Erstellen einer Ressourcengruppe und Auswählen von Standorten für die Bereitstellung
Azure-Ressourcengruppen sind logische Bereitstellungsentitäten, die Konfigurationsdaten und Metadaten enthalten, um die logische Verwaltung von Ressourcenbereitstellungen zu ermöglichen. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westeurope`:

```azurecli
azure group create --name myResourceGroup --location westeurope
```

Ausgabe:

```azurecli                        
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
data:    Id:                  /subscriptions/guid/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westeurope
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen
Sie benötigen unter anderem Speicherkonten für Ihre VM-Datenträger und für alle zusätzlichen Datenträger, die Sie hinzufügen möchten. Sie erstellen Speicherkonten normalerweise immer direkt nach der Erstellung von Ressourcengruppen.

Hier verwenden wir den Befehl `azure storage account create` und übergeben den Speicherort des Kontos, die Ressourcengruppe, mit der es gesteuert wird, und den Typ der gewünschten Speicherunterstützung. Das folgende Beispiel erstellt ein Speicherkonto namens `mystorageaccount`:

```azurecli
azure storage account create \  
  --location westeurope \
  --resource-group myResourceGroup \
  --kind Storage --sku-name GRS \
  mystorageaccount
```

Ausgabe:

```azurecli
info:    Executing command storage account create
+ Creating storage account
info:    storage account create command OK
```

Um unsere Ressourcengruppe mit dem Befehl `azure group show` zu überprüfen, verwenden wir das Tool [jq](https://stedolan.github.io/jq/) mit der Azure-CLI-Option `--json`. (Zur Analyse von JSON können Sie **jsawk** oder eine andere Sprachbibliothek Ihrer Wahl verwenden.)

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Ausgabe:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Um das Speicherkonto mithilfe der Befehlszeilenschnittstelle zu untersuchen, müssen Sie zunächst die Kontonamen und Schlüssel festlegen. Ersetzen Sie den Namen des Speicherkontos im folgenden Beispiel mit einem Namen Ihrer Wahl:

```bash
export AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show mystorageaccount --resource-group myResourceGroup --json | jq -r '.string')"
```

Sie können Ihre Speicherinformationen dann leicht anzeigen:

```azurecli
azure storage container list
```

Ausgabe:

```azurecli
info:    Executing command storage container list
+ Getting storage containers
data:    Name  Public-Access  Last-Modified
data:    ----  -------------  -----------------------------
data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
info:    storage container list command OK
```

## <a name="create-a-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und des Subnetzes
Als Nächstes müssen Sie ein virtuelles Netzwerk erstellen, das in Azure ausgeführt wird, und ein Subnetz, in dem Sie Ihre virtuellen Computer erstellen können. Im folgenden Beispiel wird ein virtuelles Netzwerk namens `myVnet` mit dem Adresspräfix `192.168.0.0/16` erstellt:

```azurecli
azure network vnet create --resource-group myResourceGroup --location westeurope \
  --name myVnet --address-prefixes 192.168.0.0/16
```

Ausgabe:

```azurecli
info:    Executing command network vnet create
+ Looking up virtual network "myVnet"
+ Creating virtual network "myVnet"
+ Loading virtual network state
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet
data:    Name                            : myVnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westeurope
data:    ProvisioningState               : Succeeded
data:    Address prefixes:
data:      192.168.0.0/16
info:    network vnet create command OK
```

Auch hier verwenden wir wieder die Option „--json“ von `azure group show` und `jq`, um zu sehen, wie wir unsere Ressourcen erstellen. Wir verfügen nun über eine `storageAccounts`-Ressource und eine `virtualNetworks`-Ressource.  

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Ausgabe:

```json
{
  "tags": {},
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "name": "myResourceGroup",
  "provisioningState": "Succeeded",
  "location": "westeurope",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "resources": [
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
      "name": "myVnet",
      "type": "virtualNetworks",
      "location": "westeurope",
      "tags": null
    },
    {
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
      "name": "mystorageaccount",
      "type": "storageAccounts",
      "location": "westeurope",
      "tags": null
    }
  ],
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": []
    }
  ]
}
```

Jetzt erstellen wir ein Subnetz im virtuellen Netzwerk `myVnet`, in dem die virtuellen Computer bereitgestellt werden. Wir verwenden den Befehl `azure network vnet subnet create` zusammen mit den Ressourcen, die wir bereits erstellt haben: die Ressourcengruppe `myResourceGroup` und das virtuelle Netzwerk `myVnet`. Im folgenden Beispiel fügen wir das Subnetz namens `mySubnet` mit dem Subnetzadresspräfix `192.168.1.0/24` hinzu:

```azurecli
azure network vnet subnet create --resource-group myResourceGroup \
  --vnet-name myVnet --name mySubnet --address-prefix 192.168.1.0/24
```

Ausgabe:

```azurecli
info:    Executing command network vnet subnet create
+ Looking up the subnet "mySubnet"
+ Creating subnet "mySubnet"
+ Looking up the subnet "mySubnet"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:    Type                            : Microsoft.Network/virtualNetworks/subnets
data:    ProvisioningState               : Succeeded
data:    Name                            : mySubnet
data:    Address prefix                  : 192.168.1.0/24
data:
info:    network vnet subnet create command OK
```

Da das Subnetz logisch im virtuellen Netzwerk angeordnet ist, ermitteln wir die Subnetzinformationen mit einem etwas anderen Befehl. Wir verwenden den Befehl `azure network vnet show`, doch wir untersuchen die JSON-Ausgabe weiterhin mit `jq`.

```azurecli
azure network vnet show myResourceGroup myVnet --json | jq '.'
```

Ausgabe:

```json
{
  "subnets": [
    {
      "ipConfigurations": [],
      "addressPrefix": "192.168.1.0/24",
      "provisioningState": "Succeeded",
      "name": "mySubnet",
      "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    }
  ],
  "tags": {},
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "provisioningState": "Succeeded",
  "etag": "W/\"974f3e2c-028e-4b35-832b-a4b16ad25eb6\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "name": "myVnet",
  "location": "westeurope"
}
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse
Jetzt erstellen wir die öffentliche IP-Adresse (PIP), die wir Ihrem Load Balancer zuweisen werden. Damit können Sie mithilfe des Befehls `azure network public-ip create` über das Internet eine Verbindung mit Ihren virtuellen Computern herstellen. Da die Standardadresse dynamisch ist, erstellen wir in der Domäne **cloudapp.azure.com** mit der Option `--domain-name-label` einen benannten DNS-Eintrag. Im folgenden Beispiel wird eine öffentliche IP-Adresse namens `myPublicIP` mit dem DNS-Namen `mypublicdns` erstellt. Der DNS-Name muss eindeutig sein. Geben Sie daher einen eigenen eindeutigen Namen an:

```azurecli
azure network public-ip create --resource-group myResourceGroup \
  --location westeurope --name myPublicIP --domain-name-label mypublicdns
```

Ausgabe:

```azurecli
info:    Executing command network public-ip create
+ Looking up the public ip "myPublicIP"
+ Creating public ip address "myPublicIP"
+ Looking up the public ip "myPublicIP"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
data:    Name                            : myPublicIP
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    Idle timeout                    : 4
data:    Domain name label               : mypublicdns
data:    FQDN                            : mypublicdns.westeurope.cloudapp.azure.com
info:    network public-ip create command OK
```

Die öffentliche IP-Adresse ist außerdem eine Ressource der obersten Ebene, die Sie mit `azure group show`anzeigen können.

```azurecli
azure group show myResourceGroup --json | jq '.'
```

Ausgabe:

```json
{
"tags": {},
"id": "/subscriptions/guid/resourceGroups/myResourceGroup",
"name": "myResourceGroup",
"provisioningState": "Succeeded",
"location": "westeurope",
"properties": {
    "provisioningState": "Succeeded"
},
"resources": [
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "name": "myPublicIP",
    "type": "publicIPAddresses",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
    "name": "myVnet",
    "type": "virtualNetworks",
    "location": "westeurope",
    "tags": null
    },
    {
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "name": "mystorageaccount",
    "type": "storageAccounts",
    "location": "westeurope",
    "tags": null
    }
],
"permissions": [
    {
    "actions": [
        "*"
    ],
    "notActions": []
    }
]
}
```

Sie können mit dem umfassenden Befehl `azure network public-ip show` auf weitere Ressourcendetails zugreifen, z.B. auf den vollqualifizierten Domänennamen (FQDN) der Unterdomäne. Die öffentliche IP-Adressressource wurde logisch zugeordnet, aber es wurde noch keine spezielle Adresse zugewiesen. Um eine IP-Adresse zu erhalten, benötigen Sie einen Load Balancer, den wir noch nicht erstellt haben.

```azurecli
azure network public-ip show myResourceGroup myPublicIP --json | jq '.'
```

Ausgabe:

```json
{
"tags": {},
"publicIpAllocationMethod": "Dynamic",
"dnsSettings": {
    "domainNameLabel": "mypublicdns",
    "fqdn": "mypublicdns.westeurope.cloudapp.azure.com"
},
"idleTimeoutInMinutes": 4,
"provisioningState": "Succeeded",
"etag": "W/\"c63154b3-1130-49b9-a887-877d74d5ebc5\"",
"id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
"name": "myPublicIP",
"location": "westeurope"
}
```

## <a name="create-a-load-balancer-and-ip-pools"></a>Erstellen eines Load Balancers und IP-Pools
Wenn Sie einen Load Balancer erstellen, können sie den Datenverkehr auf mehrere virtuelle Computer verteilen. Der Load Balancer gewährleistet zudem Redundanz für Ihre Anwendung, indem mehrere virtuelle Computer ausgeführt werden, die bei der Wartung oder bei hoher Auslastung auf Anforderungen von Benutzern reagieren. Im folgenden Beispiel wird ein Load Balancer namens `myLoadBalancer` erstellt:

```azurecli
azure network lb create --resource-group myResourceGroup --location westeurope \
  --name myLoadBalancer
```

Ausgabe:

```azurecli
info:    Executing command network lb create
+ Looking up the load balancer "myLoadBalancer"
+ Creating load balancer "myLoadBalancer"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer
data:    Name                            : myLoadBalancer
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Da der Load Balancer ziemlich leer ist, erstellen wir einige IP-Pools. Wir möchten zwei IP-Pools für den Load Balancer erstellen: einen für das Front-End und einen für das Back-End. Der Front-End-IP-Adresspool ist öffentlich sichtbar. Es ist auch der Speicherort, den wir der PIP zuweisen, die wir zuvor erstellt haben. Dann verwenden wir den Back-End-Pool als Speicherort für die virtuellen Computer, mit denen wir eine Verbindung herstellen. Auf diese Weise kann der Datenverkehr über den Load Balancer zu den virtuellen Computern weitergeleitet werden.

Zunächst erstellen wir den Front-End-IP-Pool. Im folgenden Beispiel wird ein Front-End-Pool namens `myFrontEndPool` erstellt:

```azurecli
azure network lb frontend-ip create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --public-ip-name myPublicIP \
  --name myFrontEndPool
```

Ausgabe:

```azurecli
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "myLoadBalancer"
+ Looking up the public ip "myPublicIP"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myFrontEndPool
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP
info:    network lb mySubnet-ip create command OK
```

Beachten Sie die Verwendung der Option `--public-ip-name` zum Übergeben der zuvor erstellten `myPublicIP`-Adresse. Die Zuweisung der öffentlichen IP-Adresse zum Load Balancer ermöglicht Ihnen, die virtuellen Computer über das Internet zu erreichen.

Als Nächstes erstellen wir den zweiten IP-Pool für den Back-End-Datenverkehr. Im folgenden Beispiel wird ein Back-End-Pool namens `myBackEndPool` erstellt:

```azurecli
azure network lb address-pool create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myBackEndPool
```

Ausgabe:

```azurecli
info:    Executing command network lb address-pool create
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myBackEndPool
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

Wir können den Load Balancer mit `azure network lb show` überprüfen und die JSON-Ausgabe untersuchen:

```azurecli
azure network lb show myResourceGroup myLoadBalancer --json | jq '.'
```

Ausgabe:

```json
{
  "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"29c38649-77d6-43ff-ab8f-977536b0047c\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [],
  "probes": []
}
```

## <a name="create-load-balancer-nat-rules"></a>Erstellen der NAT-Regeln für den Load Balancer
Damit der Datenverkehr über den Load Balancer abgewickelt wird, müssen wir Regeln für die Netzwerkadressenübersetzung (Network Address Translation, NAT) erstellen, die eingehende oder ausgehende Aktionen angeben. Sie können das verwendete Protokoll angeben und dann wie gewünscht externe Ports internen Ports zuordnen. Wir erstellen für unsere Umgebung einige Regeln, die SSH-Datenverkehr durch den Load Balancer an die virtuellen Computer ermöglichen. Wir richten die TCP-Ports 4222 und 4223 so ein, dass der Datenverkehr auf den virtuellen Computern (die wir später erstellen) an den TCP-Port 22 geleitet wird. Im folgenden Beispiel wird eine Regel namens `myLoadBalancerRuleSSH1` erstellt, um den TCP-Port 4222 dem Port 22 zuzuordnen:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH1 \
  --protocol tcp --frontend-port 4222 --backend-port 22
```

Ausgabe:

```azurecli
info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default enable floating ip: false
warn:    Using default idle timeout: 4
warn:    Using default mySubnet IP configuration "myFrontEndPool"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleSSH1
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 4222
data:    Backend port                    : 22
data:    Enable floating IP              : false
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
info:    network lb inbound-nat-rule create command OK
```

Wiederholen Sie dieses Verfahren für Ihre zweite NAT-Regel für SSH. Im folgenden Beispiel wird eine Regel namens `myLoadBalancerRuleSSH2` erstellt, um den TCP-Port 4223 dem Port 22 zuzuordnen:

```azurecli
azure network lb inbound-nat-rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleSSH2 --protocol tcp \
  --frontend-port 4223 --backend-port 22
```

Wir erstellen auch eine NAT-Regel für den TCP-Port 80 (Webdatenverkehr) und binden die Regel in die IP-Pools ein. Wenn wir die Regel in einen IP-Pool einbinden, anstatt die Regel einzeln auf den virtuellen Computern einzubinden, können wir virtuelle Computer dem IP-Pool hinzufügen oder sie daraus entfernen. Der Load Balancer passt dann automatisch den Datenverkehrsfluss an. Im folgenden Beispiel wird eine Regel namens `myLoadBalancerRuleWeb` erstellt, um den TCP-Port 80 dem Port 80 zuzuordnen:

```azurecli
azure network lb rule create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myLoadBalancerRuleWeb --protocol tcp \
  --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-address-pool-name myBackEndPool
```

Ausgabe:

```azurecli
info:    Executing command network lb rule create
+ Looking up the load balancer "myLoadBalancer"
warn:    Using default idle timeout: 4
warn:    Using default enable floating ip: false
warn:    Using default load distribution: Default
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myLoadBalancerRuleWeb
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    mySubnet port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    mySubnet IP configuration id    : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool
data:    Backend address pool id         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
info:    network lb rule create command OK
```

## <a name="create-a-load-balancer-health-probe"></a>Erstellen des Integritätstests für den Load Balancer
Mit einem Integritätstest werden die virtuellen Computer hinter dem Load Balancer regelmäßig überprüft, um sicherzustellen, dass sie wie definiert ausgeführt werden und auf Anforderungen reagieren. Wenn dies nicht der Fall ist, werden sie außer Betrieb gesetzt, sodass Benutzer nicht an sie weitergeleitet werden. Sie können benutzerdefinierte Überprüfungen sowie Intervalle und Timeoutwerte für den Integritätstest definieren. Weitere Informationen zu Integritätstests finden Sie unter [Load Balancer-Integritätstest](../../load-balancer/load-balancer-custom-probe-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Im folgenden Beispiel wird ein TCP-Integritätstest namens `myHealthProbe` erstellt:

```azurecli
azure network lb probe create --resource-group myResourceGroup \
  --lb-name myLoadBalancer --name myHealthProbe --protocol "tcp" \
  --interval 15 --count 4
```

Ausgabe:

```azurecli
info:    Executing command network lb probe create
warn:    Using default probe port: 80
+ Looking up the load balancer "myLoadBalancer"
+ Updating load balancer "myLoadBalancer"
data:    Name                            : myHealthProbe
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 4
info:    network lb probe create command OK
```

Hier haben wir ein Intervall von 15 Sekunden für unsere Integritätstests angegeben. Bei maximal vier Tests (in 1 Minute) können Fehler auftreten, bevor der Load Balancer feststellt, dass der Host nicht mehr funktionsfähig ist.

## <a name="verify-the-load-balancer"></a>Überprüfen des Load Balancers
Jetzt ist die Load Balancer-Konfiguration abgeschlossen. Folgende Schritte haben Sie ausgeführt:

1. Sie haben einen Load Balancer erstellt.
2. Dann haben Sie einen Front-End-IP-Adresspool erstellt und ihm eine öffentliche IP-Adresse zugewiesen.
3. Anschließend haben Sie einen Back-End-IP-Adresspool erstellt, mit dem virtuelle Computer eine Verbindung herstellen können.
4. Sie haben NAT-Regeln für den SSH-Datenverkehr für die virtuellen Computer zu Verwaltungszwecken sowie eine Regel für den TCP-Port 80 für die Web-App erstellt.
5. Sie haben einen Integritätstest zur regelmäßigen Überprüfung der virtuellen Computer hinzugefügt. Dieser Integritätstest stellt sicher, dass Benutzer nicht versuchen, auf einen virtuellen Computer zuzugreifen, der nicht mehr funktioniert bzw. keine Inhalte mehr liefert.

So sieht Ihr Load Balancer nun aus:

```azurecli
azure network lb show --resource-group myResourceGroup \
  --name myLoadBalancer --json | jq '.'
```

Ausgabe:

```json
{
  "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
  "provisioningState": "Succeeded",
  "resourceGuid": "f1446acb-09ba-44d9-b8b6-849d9983dc09",
  "outboundNatRules": [],
  "inboundNatPools": [],
  "inboundNatRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH1",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4222,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    },
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleSSH2",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "protocol": "Tcp",
      "mySubnetPort": 4223,
      "backendPort": 22,
      "idleTimeoutInMinutes": 4,
      "enableFloatingIP": false,
      "provisioningState": "Succeeded"
    }
  ],
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer",
  "name": "myLoadBalancer",
  "type": "Microsoft.Network/loadBalancers",
  "location": "westeurope",
  "mySubnetIPConfigurations": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myFrontEndPool",
      "provisioningState": "Succeeded",
      "publicIPAddress": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP"
      },
      "privateIPAllocationMethod": "Dynamic",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "inboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        },
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
    }
  ],
  "backendAddressPools": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myBackEndPool",
      "provisioningState": "Succeeded",
      "loadBalancingRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb"
        }
      ],
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
    }
  ],
  "loadBalancingRules": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myLoadBalancerRuleWeb",
      "provisioningState": "Succeeded",
      "enableFloatingIP": false,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/loadBalancingRules/myLoadBalancerRuleWeb",
      "mySubnetIPConfiguration": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/mySubnetIPConfigurations/myFrontEndPool"
      },
      "backendAddressPool": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
      },
      "protocol": "Tcp",
      "loadDistribution": "Default",
      "mySubnetPort": 80,
      "backendPort": 80,
      "idleTimeoutInMinutes": 4
    }
  ],
  "probes": [
    {
      "etag": "W/\"62a7c8e7-859c-48d3-8e76-5e078c5e4a02\"",
      "name": "myHealthProbe",
      "provisioningState": "Succeeded",
      "numberOfProbes": 4,
      "intervalInSeconds": 15,
      "port": 80,
      "protocol": "Tcp",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/probes/myHealthProbe"
    }
  ]
}
```

## <a name="create-an-nic-to-use-with-the-linux-vm"></a>Erstellen einer Netzwerkschnittstellenkarte zur Verwendung mit der Linux-VM
NICs sind programmgesteuert verfügbar, da Sie Regeln zu ihrer Verwendung anwenden können. Sie können auch mehrere Regeln anwenden. Mit dem folgenden Befehl `azure network nic create` wird die Netzwerkkarte in den Last-Back-End-IP-Pool eingebunden und der NAT-Regel für das Zulassen von SSH-Datenverkehr zugeordnet.

Ersetzen Sie die `#####-###-###`-Abschnitte durch Ihre eigene Azure-Abonnement-ID. Ihre Abonnement-ID finden Sie bei der Untersuchung der zu erstellenden Ressourcen in der Ausgabe von `jq`. Die Abonnement-ID kann aber auch mit `azure account list` angezeigt werden.

Im folgenden Beispiel wird eine NIC namens `myNic1` erstellt:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 \
  --lb-address-pool-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
```

Ausgabe:

```azurecli
info:    Executing command network nic create
+ Looking up the subnet "mySubnet"
+ Looking up the network interface "myNic1"
+ Creating network interface "myNic1"
data:    Id                              : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1
data:    Name                            : myNic1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westeurope
data:    Provisioning state              : Succeeded
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : Nic-IP-config
data:      Provisioning state            : Succeeded
data:      Private IP address            : 192.168.1.4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool
data:      Load balancer inbound NAT rules:
data:        Id                          : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1
data:
info:    network nic create command OK
```

Sie können die Details sehen, wenn Sie die Ressource direkt untersuchen. Untersuchen Sie die Ressource mithilfe des `azure network nic show` -Befehls:

```azurecli
azure network nic show myResourceGroup myNic1 --json | jq '.'
```

Ausgabe:

```json
{
  "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
  "provisioningState": "Succeeded",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1",
  "name": "myNic1",
  "type": "Microsoft.Network/networkInterfaces",
  "location": "westeurope",
  "ipConfigurations": [
    {
      "etag": "W/\"fc1eaaa1-ee55-45bd-b847-5a08c7f4264a\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/Nic-IP-config",
      "loadBalancerBackendAddressPools": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool"
        }
      ],
      "loadBalancerInboundNatRules": [
        {
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH1"
        }
      ],
      "privateIPAddress": "192.168.1.4",
      "privateIPAllocationMethod": "Dynamic",
      "subnet": {
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
      },
      "provisioningState": "Succeeded",
      "name": "Nic-IP-config"
    }
  ],
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": []
  },
  "enableIPForwarding": false,
  "resourceGuid": "a20258b8-6361-45f6-b1b4-27ffed28798c"
}
```

Jetzt erstellen wir die zweite NIC und binden sie erneut in unseren Back-End-IP-Adresspool ein. Dieses Mal lässt die zweite NAT-Regel SSH-Datenverkehr zu. Im folgenden Beispiel wird eine NIC namens `myNic2` erstellt:

```azurecli
azure network nic create --resource-group myResourceGroup --location westeurope \
  --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 \
  --lb-address-pool-ids  /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/backendAddressPools/myBackEndPool \
  --lb-inbound-nat-rule-ids /subscriptions/########-####-####-####-############/resourceGroups/myResourceGroup/providers/Microsoft.Network/loadBalancers/myLoadBalancer/inboundNatRules/myLoadBalancerRuleSSH2
```

## <a name="create-a-network-security-group-and-rules"></a>Erstellen einer Netzwerksicherheitsgruppe und von Regeln
Als Nächstes erstellen wir eine Netzwerksicherheitsgruppe und die eingehenden Regeln, mit denen der Zugriff auf die Netzwerkschnittstellenkarte gesteuert wird. Eine Netzwerksicherheitsgruppe kann auf eine NIC oder auf ein Subnetz angewendet werden. Der ein- und ausgehende Datenverkehr Ihrer virtuellen Computer wird durch Definieren entsprechender Regeln gesteuert. Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe namens `myNetworkSecurityGroup` erstellt:

```azurecli
azure network nsg create --resource-group myResourceGroup --location westeurope \
  --name myNetworkSecurityGroup
```

Wir fügen die eingehende Regel für die NSG hinzu, um eingehende Verbindungen für Port 22 zuzulassen (SSH-Unterstützung). Im folgenden Beispiel wird eine Regel namens `myNetworkSecurityGroupRuleSSH` erstellt, um TCP auf Port 22 zuzulassen:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1000 --destination-port-range 22 --access allow \
  --name myNetworkSecurityGroupRuleSSH
```

Als Nächstes fügen wir die eingehende Regel für die NSG hinzu, um eingehende Verbindungen über Port 80 zuzulassen (Unterstützung von Webdatenverkehr). Im folgenden Beispiel wird eine Regel namens `myNetworkSecurityGroupRuleHTTP` erstellt, um TCP auf Port 80 zuzulassen:

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup --protocol tcp --direction inbound \
  --priority 1001 --destination-port-range 80 --access allow \
  --name myNetworkSecurityGroupRuleHTTP
```

> [!NOTE]
> Die eingehende Regel ist ein Filter für eingehende Netzwerkverbindungen. In diesem Beispiel binden wir die NSG an die virtuelle NIC der virtuellen Computer. Dies bedeutet, dass alle Anforderungen an Port 22 über die NIC auf dem virtuellen Computer übergeben werden. Dies ist eine Eingangsregel über eine Netzwerkverbindung und nicht über einen Endpunkt wie in klassischen Bereitstellungen. Zum Öffnen eines Ports muss `--source-port-range` auf „\*“ (Standardwert) festgelegt bleiben, damit eingehende Anforderungen von **jedem** anfordernden Port akzeptiert werden. Ports sind in der Regel dynamisch.
>
>

## <a name="bind-to-the-nic"></a>Binden an die Netzwerkkarte
Binden Sie die NSG an die NICs. Wir müssen unsere NICs mit unserer Netzwerksicherheitsgruppe verbinden. Führen Sie beide Befehle aus, um beide NICs einzubinden:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
  --network-security-group-name myNetworkSecurityGroup
```

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic2 \
  --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen
Mit Verfügbarkeitsgruppen können die virtuellen Computer auf Fehlerdomänen und Upgradedomänen verteilt werden. Als Nächstes erstellen wir daher eine Verfügbarkeitsgruppe für die virtuellen Computer. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens `myAvailabilitySet` erstellt:

```azurecli
azure availset create --resource-group myResourceGroup --location westeurope
  --name myAvailabilitySet
```

Durch Fehlerdomänen wird eine Gruppe virtueller Computer definiert, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. Die innerhalb der Verfügbarkeitsgruppe konfigurierten virtuellen Computer werden standardmäßig auf bis zu drei Fehlerdomänen verteilt. Dadurch wirkt sich ein Hardwareproblem in einer dieser Fehlerdomänen nicht auf jeden virtuellen Computer aus, auf dem Ihre Anwendung ausgeführt wird. Azure verteilt virtuelle Computer automatisch auf die Fehlerdomänen, wenn sie in einer Verfügbarkeitsgruppe platziert werden.

Upgradedomänen definieren Gruppen virtueller Computer und die zugrunde liegende physische Hardware, die gleichzeitig neu gestartet werden können. Während einer geplanten Wartung werden die Upgradedomänen möglicherweise nicht der Reihe nach neu gestartet. Es wird aber jeweils nur eine Upgradedomäne neu gestartet. Azure verteilt Ihre virtuellen Computer auch hier automatisch auf die Upgradedomänen, wenn sie in einer Verfügbarkeitsgruppe platziert werden.

Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="create-the-linux-vms"></a>Erstellen der Linux-VMs
Sie haben die Speicher- und Netzwerkressourcen zur Unterstützung von über das Internet erreichbaren virtuellen Computern erstellt. Wir erstellen jetzt diese virtuellen Computer und schützen sie mit einem SSH-Schlüssel ohne Kennwort. In diesem Fall erstellen wir eine Ubuntu-VM basierend auf dem aktuellen LTS-Stand. Wir ermitteln diese Imageinformationen mithilfe von `azure vm image list`. Dies wird unter [Suchen nach Azure VM-Images](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) beschrieben.

Wir haben ein Image mithilfe des Befehls `azure vm image list westeurope canonical | grep LTS`ausgewählt. In diesem Fall verwenden wir `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`. Für das letzte Feld übergeben wir `latest` , sodass in Zukunft immer die letzten Builds abgerufen werden. (Die Zeichenfolge, die wir verwenden, ist `canonical:UbuntuServer:16.04.0-LTS:16.04.201608150`).

Der nächste Schritt ist allen Benutzern vertraut, die mit **ssh-keygen -t rsa -b 2048**bereits ein Paar aus einem öffentlichen und einem privaten SSH-RSA-Schlüssel unter Linux oder Mac erstellt haben. Wenn sich in Ihrem `~/.ssh` -Verzeichnis keine Zertifikatsschlüsselpaare befinden, können Sie sie erstellen:

* Automatisch mithilfe der Option `azure vm create --generate-ssh-keys` .
* Manuell mithilfe [der Anweisungen zur Selbsterstellung](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Alternativ können Sie Ihre SSH-Verbindungen nach der Erstellung des virtuellen Computers mithilfe der `--admin-password`-Methode authentifizieren. Diese Methode ist in der Regel weniger sicher.

Wir erstellen den virtuellen Computer, indem wir alle Ressourcen und Informationen mit dem Befehl `azure vm create` zusammenfassen:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM1 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic1 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Ausgabe:

```azurecli
info:    Executing command vm create
+ Looking up the VM "myVM1"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account mystorageaccount
+ Looking up the availability set "myAvailabilitySet"
info:    Found an Availability set "myAvailabilitySet"
+ Looking up the NIC "myNic1"
info:    Found an existing NIC "myNic1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet" in the NIC "myNic1"
info:    This is an NIC without publicIP configured
info:    The storage URI 'https://mystorageaccount.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
info:    vm create command OK
```

Anschließend können Sie mit Ihren standardmäßigen SSH-Schlüsseln eine Verbindung mit dem virtuellen Computer herstellen. Stellen Sie sicher, dass Sie den entsprechenden Port angeben, da die Weiterleitung über den Load Balancer erfolgt. (Für unseren ersten virtuellen Computer richten wir die NAT-Regel zum Weiterleiten von Port 4222 an den virtuellen Computer ein.)

```bash
ssh ops@mypublicdns.westeurope.cloudapp.azure.com -p 4222
```

Ausgabe:

```bash
The authenticity of host '[mypublicdns.westeurope.cloudapp.azure.com]:4222 ([xx.xx.xx.xx]:4222)' can't be established.
ECDSA key fingerprint is 94:2d:d0:ce:6b:fb:7f:ad:5b:3c:78:93:75:82:12:f9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[mypublicdns.westeurope.cloudapp.azure.com]:4222,[xx.xx.xx.xx]:4222' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-34-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

ops@myVM1:~$
```

Erstellen Sie nun den zweiten virtuellen Computer auf die gleiche Weise:

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM2 \
  --location westeurope \
  --os-type linux \
  --availset-name myAvailabilitySet \
  --nic-name myNic2 \
  --vnet-name myVnet \
  --vnet-subnet-name mySubnet \
  --storage-account-name mystorageaccount \
  --image-urn canonical:UbuntuServer:16.04.0-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username azureuser
```

Jetzt können Sie mit dem Befehl `azure vm show myResourceGroup myVM1` die erstellten Elemente untersuchen. Nun werden die virtuellen Ubuntu-Computer, bei denen Sie sich nur mit Ihrem SSH-Schlüsselpaar anmelden können (weil Kennwörter deaktiviert sind), hinter einem Load Balancer in Azure ausgeführt. Sie können „nginx“ oder „httpd“ installieren und eine Web-App bereitstellen. Zudem können Sie den Datenverkehrsfluss durch den Load Balancer an beide virtuellen Computer sehen.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM1
```

Ausgabe:

```azurecli
info:    Executing command vm show
+ Looking up the VM "TestVM1"
+ Looking up the NIC "myNic1"
data:    Id                              :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM1
data:    ProvisioningState               :Succeeded
data:    Name                            :myVM1
data:    Location                        :westeurope
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :16.04.0-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clib45a8b650f4428a1-os-1471973896525
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/clib45a8b650f4428a1-os-1471973896525.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM1
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-24-D4-AA
data:          Provisioning State        :Succeeded
data:          Name                      :LmyNic1
data:          Location                  :westeurope
data:
data:    AvailabilitySet:
data:      Id                            :/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://mystorageaccount.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm show command OK

```


## <a name="export-the-environment-as-a-template"></a>Exportieren der Umgebung als Vorlage
Nachdem Sie nun diese Umgebung erstellt haben, möchten Sie vielleicht eine weitere Entwicklungsumgebung mit den gleichen Parametern oder eine entsprechende Produktionsumgebung erstellen. Der Resource Manager verwendet die JSON-Vorlagen, die alle Parameter für Ihre Umgebung definieren. Sie erstellen ganze Umgebungen durch Verweisen auf diese JSON-Vorlage. Sie können [JSON-Vorlagen manuell erstellen](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder eine vorhandene Umgebung exportieren, um eine JSON-Vorlage zu erstellen:

```azurecli
azure group export --name myResourceGroup
```

Dieser Befehl erstellt die `myResourceGroup.json`-Datei in Ihrem aktuellen Arbeitsverzeichnis. Wenn Sie eine Umgebung aus dieser Vorlage erstellen, werden Sie aufgefordert, alle Ressourcennamen, beispielsweise die Namen für Load Balancer, Netzwerkschnittstellen oder virtuelle Computer einzugeben. Sie können diese Namen in Ihre Vorlagendatei eintragen, indem Sie die Parameter `-p` oder `--includeParameterDefaultValue` dem Befehl `azure group export` hinzufügen, der zuvor gezeigt wurde. Bearbeiten Sie die JSON-Vorlage zum Angeben der Ressourcennamen, oder [erstellen Sie eine Datei „parameters.json“](../../resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), die die Ressourcennamen angibt.

Erstellen Sie wie folgt eine Umgebung aus Ihrer Vorlage:

```azurecli
azure group deployment create --resource-group myNewResourceGroup \
  --template-file myResourceGroup.json
```

Möglicherweise möchten Sie [weitere Informationen zum Bereitstellen von Vorlagen](../../resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)lesen. Erfahren Sie, wie Sie Umgebungen unter Verwendung der Parameterdatei schrittweise aktualisieren und von einem zentralen Speicherort aus auf Vorlagen zugreifen.

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt beginnen, mit mehreren Netzwerkkomponenten und VMs zu arbeiten. Sie können diese Beispielumgebung nutzen, um Ihre Anwendung mithilfe der hier eingeführten zentralen Komponenten zu erstellen.
