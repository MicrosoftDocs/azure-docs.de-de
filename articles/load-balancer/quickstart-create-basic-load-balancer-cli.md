---
title: 'Schnellstart: Erstellen eines Lastenausgleichs im Basic-Tarif – Azure CLI'
titlesuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie über die Azure-Befehlszeilenschnittstelle einen öffentlichen Lastenausgleich erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.custom: mvc
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.openlocfilehash: 698714990b9b34567d918d3b8c536bc3e39d66b8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257695"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-azure-cli"></a>Schnellstart: Erstellen eines Load Balancers für den Lastenausgleich virtueller Computer mit der Azure CLI

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Instanz von Azure Load Balancer für den Lastenausgleich im Internetdatenverkehr zwischen virtuellen Computern in Azure erstellen. Zum Testen des Lastenausgleichs stellen Sie zwei virtuelle Computer (Virtual Machines, VMs) mit Ubuntu-Server bereit und führen einen Lastenausgleich für eine Web-App durch.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.28 ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe namens *myResourceGroupLB* am Standort *eastus* erstellt:

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Um über das Internet auf Ihre Web-App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Lastenausgleich. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), um in *myResourceGroupLB* eine öffentliche IP-Adresse namens *myPublicIP* zu erstellen.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupLB --name myPublicIP
```

## <a name="create-azure-load-balancer"></a>Erstellen eines Azure Load Balancers

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:
  - Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Load Balancer empfängt
  - Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  - Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  - Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer"></a>Erstellen des Lastenausgleichs

Erstellen Sie mit [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) einen öffentlichen Azure Load Balancer mit dem Namen **myLoadBalancer**, der den Front-End-Pool **myFrontEndPool** und den Back-End-Pool **myBackEndPool** enthält (zugeordnet zur öffentlichen IP-Adresse **myPublicIP**, die Sie im vorherigen Schritt erstellt haben).

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Ein Integritätstest überprüft alle VM-Instanzen, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. VM-Instanzen mit Fehlern beim Test werden aus dem Load Balancer entfernt, bis sie wieder online geschaltet werden und beim Test überprüft wurde, dass sie fehlerfrei sind. Erstellen Sie mit [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) einen Integritätstest zur Überwachung der Integrität von virtuellen Computern. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Mit einer Lastenausgleichsregel wird die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport definiert. Erstellen Sie mit [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) eine Lastenausgleichsregel mit dem Namen *myLoadBalancerRuleWeb*, die an Port 80 des Front-End-Pools *myFrontEndPool* lauscht und den Netzwerkdatenverkehr nach erfolgtem Lastenausgleich an den Back-End-Adresspool *myBackEndPool* sendet, wobei ebenfalls der Port 80 verwendet wird. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Vor der Bereitstellung mehrerer virtueller Computer und dem Testen des Lastenausgleichs müssen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen erstellen.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie ein virtuelles Netzwerk mit dem Namen *myVnet* und dem Subnetz *mySubnet* in *myResourceGroup*, indem Sie den Befehl [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) verwenden.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Erstellen Sie eine Netzwerksicherheitsgruppen-Regel, um eingehende Verbindungen über Port 80 zuzulassen.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>Erstellen von NICs

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) zwei Netzwerkschnittstellen, und ordnen Sie sie der öffentlichen IP-Adresse und der Netzwerksicherheitsgruppe zu. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```


## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Beispiel erstellen Sie zwei virtuelle Computer, die als Back-End-Server für den Lastenausgleich verwendet werden. Außerdem installieren Sie NGINX auf den virtuellen Computern, um zu überprüfen, ob der Load Balancer erfolgreich erstellt wurde.

### <a name="create-an-availability-set"></a>Erstellen einer Verfügbarkeitsgruppe

Erstellen Sie mit [az vm availabilityset create](/cli/azure/network/nic) eine Verfügbarkeitsgruppe.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupLB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Erstellen von zwei virtuellen Computern

Mithilfe einer cloud-init-Konfigurationsdatei können Sie NGINX installieren und eine Node.js-App vom Typ „Hallo Welt“ auf einem virtuellen Linux-Computer ausführen. Erstellen Sie in der aktuellen Shell eine Datei namens „cloud-init.txt“, kopieren Sie die folgende Konfiguration, und fügen Sie sie in die Shell ein. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
Erstellen Sie die virtuellen Computer mit [az vm create](/cli/azure/vm#az-vm-create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    --no-wait
    done
```
Es kann einige Minuten dauern, bis die virtuellen Computer bereitgestellt wurden.

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des Lastenausgleichs ab. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
![Testen des Load Balancers](./media/load-balancer-get-started-internet-arm-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) können Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive 
  az group delete --name myResourceGroupLB
```


## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie einen Lastenausgleich im Tarif „Basic“ erstellt, virtuelle Computer angefügt, eine Datenverkehrsregel für den Lastenausgleich sowie einen Integritätstest konfiguriert und den Lastenausgleich getestet. Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials zu Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-basic-internal-portal.md)
