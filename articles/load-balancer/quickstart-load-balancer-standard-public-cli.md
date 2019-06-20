---
title: 'Schnellstart: Erstellen eines öffentlichen Lastenausgleichs im Standard-Tarif: Azure CLI'
titlesuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie über die Azure-Befehlszeilenschnittstelle einen öffentlichen Lastenausgleich erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: ebeb7e109584726dcd2505fbd9d4c1d7bfde7aba
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387676"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer mit der Azure CLI

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie einen Load Balancer im Tarif „Standard“ erstellen. Zum Testen des Lastenausgleichs stellen Sie zwei virtuelle Computer (VMs) mit Ubuntu Server bereit und führen für eine Web-App zwischen den beiden VMs einen Lastenausgleich durch.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Azure CLI-Version 2.0.28 ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupSLB* am Standort *eastus* erstellt:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-standard-ip-address"></a>Erstellen einer öffentlichen Standard-IP-Adresse

Um über das Internet auf Ihre Web-App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Load Balancer. Ein Load Balancer im Standard-Tarif unterstützt nur öffentliche Standard-IP-Adressen. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) zum Erstellen einer öffentlichen Standard-IP-Adresse mit dem Namen *myPublicIP* in *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

## <a name="create-azure-load-balancer"></a>Erstellen eines Azure Load Balancers

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:
  - Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Load Balancer empfängt
  - Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  - Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  - Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer"></a>Erstellen des Lastenausgleichs

Erstellen Sie mit [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) einen öffentlichen Azure Load Balancer mit dem Namen **myLoadBalancer**, der den Front-End-Pool **myFrontEnd** und den Back-End-Pool **myBackEndPool** enthält (mit Zuordnung zur öffentlichen IP-Adresse **myPublicIP**, die Sie im vorherigen Schritt erstellt haben).

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Ein Integritätstest überprüft alle VM-Instanzen, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. VM-Instanzen mit Fehlern beim Test werden aus dem Load Balancer entfernt, bis sie wieder online geschaltet werden und beim Test überprüft wurde, dass sie fehlerfrei sind. Erstellen Sie mit [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) einen Integritätstest zur Überwachung der Integrität von virtuellen Computern. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Mit einer Lastenausgleichsregel wird die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport definiert. Erstellen Sie mit [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) eine Lastenausgleichsregel mit dem Namen *myLoadBalancerRuleWeb*, die an Port 80 des Front-End-Pools *myFrontEnd* lauscht und den Netzwerkdatenverkehr nach erfolgtem Lastenausgleich an den Back-End-Adresspool *myBackEndPool* sendet, wobei ebenfalls der Port 80 verwendet wird. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Vor der Bereitstellung mehrerer virtueller Computer und dem Testen des Lastenausgleichs müssen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen erstellen.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie ein virtuelles Netzwerk mit dem Namen *myVnet* und dem Subnetz *mySubnet* in *myResourceGroup*, indem Sie den Befehl [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) verwenden.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Für einen Load Balancer im Standard-Tarif müssen die VMs unter der Back-End-Adresse über NICs verfügen, die einer Netzwerksicherheitsgruppe angehören. Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Erstellen Sie eine Netzwerksicherheitsgruppen-Regel, um eingehende Verbindungen über Port 80 zuzulassen.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
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

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) drei Netzwerkschnittstellen, und ordnen Sie sie der öffentlichen IP-Adresse und der Netzwerksicherheitsgruppe zu. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```


## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Beispiel erstellen Sie drei virtuelle Computer, die als Back-End-Server für den Lastenausgleich verwendet werden. Außerdem installieren Sie NGINX auf den virtuellen Computern, um zu überprüfen, ob der Load Balancer erfolgreich erstellt wurde.

### <a name="create-an-availability-set"></a>Erstellen einer Verfügbarkeitsgruppe

Erstellen Sie mit [az vm availabilityset create](/cli/azure/network/nic) eine Verfügbarkeitsgruppe.

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupSLB \
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
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
done
```
Es kann einige Minuten dauern, bis die virtuellen Computer bereitgestellt wurden.

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des Lastenausgleichs ab. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![Testen des Load Balancers](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) können Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-step"></a>Nächster Schritt
In dieser Schnellstartanleitung haben Sie eine Load Balancer Standard-Instanz erstellt, virtuelle Computer angefügt, die Datenverkehrsregel für den Lastenausgleich sowie einen Integritätstest konfiguriert und den Lastenausgleich getestet. Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials zu Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

