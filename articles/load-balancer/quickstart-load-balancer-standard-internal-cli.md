---
title: 'Schnellstart: Erstellen eines internen Lastenausgleichs – Azure CLI'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen internen Lastenausgleich über die Azure-Befehlszeilenschnittstelle erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 834b5c3651a7fff085dc53096f66d5e3f4bf27b4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700408"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Schnellstart: Erstellen eines internen Load Balancers für den Lastenausgleich virtueller Computer mit der Azure CLI

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie mithilfe der Azure CLI einen öffentlichen Lastenausgleich und drei virtuelle Computer erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Für diesen Schnellstart ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe:

* Benannt als **CreateIntLBQS-rg**. 
* Standort: **eastus**

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer und Ihren Lastenausgleich bereitstellen.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) ein virtuelles Netzwerk:

* Name: **myVNet**
* Adresspräfix: **10.1.0.0/16**
* Subnetzname: **myBackendSubnet**
* Subnetzpräfix: **10.1.0.0/24**
* In der Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Für einen Standard-Lastenausgleich müssen die virtuellen Computer unter der Back-End-Adresse über Netzwerkschnittstellen verfügen, die einer Netzwerksicherheitsgruppe angehören. 

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) eine Netzwerksicherheitsgruppe:

* Name: **myNSG**
* In Ressourcengruppe **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) eine Netzwerksicherheitsgruppen-Regel:

* Name: **myNSGRuleHTTP**
* Netzwerksicherheitsgruppe: **myNSG** (aus dem vorherigen Schritt)
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Protokoll **(*)**
* Richtung: **Eingehend**
* Quelle: **(*)**
* Ziel: **(*)**
* Zielport: **Port 80**
* Zugriff: **Zulassen**
* Priorität: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Abschnitt wird Folgendes erstellt:

* Netzwerkschnittstellen für die Back-End-Server
* Eine Cloudkonfigurationsdatei namens **cloud-init.txt** für die Serverkonfiguration
* Zwei virtuelle Computer als Back-End-Server für den Lastenausgleich

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Erstellen von Netzwerkschnittstellen für die virtuellen Computer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) zwei Netzwerkschnittstellen:

#### <a name="vm1"></a>VM1

* Name: **myNicVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Name: **myNicVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

### <a name="create-cloud-init-configuration-file"></a>Erstellen der Konfigurationsdatei „cloud-init“

Verwenden Sie eine Konfigurationsdatei namens „cloud-init“, um NGINX zu installieren und eine Node.js-App namens „Hello World“ auf einem virtuellen Linux-Computer auszuführen. 

Erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen „cloud-init.txt“. Kopieren Sie die folgende Konfiguration, und fügen Sie sie in die Shell ein. Achten Sie darauf, die gesamte Datei „cloud-init“ ordnungsgemäß zu kopieren – insbesondere die erste Zeile:

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
### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie mit [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) die virtuellen Computer:

#### <a name="vm1"></a>VM1
* Name: **myVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM1**
* VM-Image: **UbuntuLTS**
* Konfigurationsdatei: **cloud-init.txt** (aus dem vorherigen Schritt)
* Zone: **Zone 1**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Name: **myVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM2**
* VM-Image: **UbuntuLTS**
* Konfigurationsdatei: **cloud-init.txt** (aus dem vorherigen Schritt)
* Zone: **Zone 2**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Die Bereitstellung der virtuellen Computer kann eine Weile dauern.

## <a name="create-standard-load-balancer"></a>Erstellen eines Standard-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

  * Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  * Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  * Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  * Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie mit [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) einen öffentlichen Lastenausgleich:

* Name: **myLoadBalancer**
* Front-End-Pool namens **myFrontEnd**
* Back-End-Pool namens **myBackEndPool**
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Dem Back-End-Subnetz **myBackendSubnet** zugeordnet

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Von einem Integritätstest werden alle VM-Instanzen überprüft, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. 

Ist der Test bei einem virtuellen Computer nicht erfolgreich, wird er aus dem Lastenausgleich entfernt. Nach Behebung des Fehlers wird der virtuelle Computer dem Lastenausgleich wieder hinzugefügt.

Erstellen Sie mit [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) einen Integritätstest:

* Überwacht die Integrität der virtuellen Computer
* Name: **myHealthProbe**
* Protokoll: **TCP**
* Überwachter Port: **80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) eine Lastenausgleichsregel:

* Name: **myHTTPRule**
* Lauscht am **Port 80** im Front-End-Pool **myFrontEnd**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** unter Verwendung von **Port 80** 
* Verwendet den Integritätstest **myHealthProbe**
* Protokoll: **TCP**
* Leerlaufzeitüberschreitung von **15 Minuten**.
* Aktivieren Sie die TCP-Zurücksetzung.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Die virtuellen Computer im Back-End-Pool verfügen mit dieser Konfiguration nicht über ausgehende Internetkonnektivität. </br> Weitere Informationen zu ausgehender Konnektivität finden Sie unter: </br> **[Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md)**</br> Optionen zum Bereitstellen von Konnektivität: </br> **[Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)](egress-only.md)** </br> **[Was ist Virtual Network NAT?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Hinzufügen virtueller Computer zum Back-End-Pool des Lastenausgleichs

Fügen Sie mit [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) die virtuellen Computer zum Back-End-Pool hinzu:


#### <a name="vm1"></a>VM1
* Back-End-Adresspool: **myBackEndPool**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Zugeordnet zur Netzwerkschnittstelle **myNicVM1** und zu **ipconfig1**
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Back-End-Adresspool: **myBackEndPool**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Zugeordnet zur Netzwerkschnittstelle **myNicVM2** und zu **ipconfig1**
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer und Ihren Lastenausgleich bereitstellen.

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt) ein virtuelles Netzwerk:

* Name: **myVNet**
* Adresspräfix: **10.1.0.0/16**
* Subnetzname: **myBackendSubnet**
* Subnetzpräfix: **10.1.0.0/24**
* In der Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Für einen Standard-Lastenausgleich müssen die virtuellen Computer unter der Back-End-Adresse über Netzwerkschnittstellen verfügen, die einer Netzwerksicherheitsgruppe angehören. 

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) eine Netzwerksicherheitsgruppe:

* Name: **myNSG**
* In Ressourcengruppe **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) eine Netzwerksicherheitsgruppen-Regel:

* Name: **myNSGRuleHTTP**
* Netzwerksicherheitsgruppe: **myNSG** (aus dem vorherigen Schritt)
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Protokoll **(*)**
* Richtung: **Eingehend**
* Quelle: **(*)**
* Ziel: **(*)**
* Zielport: **Port 80**
* Zugriff: **Zulassen**
* Priorität: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Erstellen von Netzwerkschnittstellen für die virtuellen Computer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) zwei Netzwerkschnittstellen:

#### <a name="vm1"></a>VM1

* Name: **myNicVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Name: **myNicVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Abschnitt wird Folgendes erstellt:

* Eine Cloudkonfigurationsdatei namens **cloud-init.txt** für die Serverkonfiguration 
* Verfügbarkeitsgruppe für die virtuellen Computer
* Zwei virtuelle Computer als Back-End-Server für den Lastenausgleich

Installieren Sie NGINX auf den virtuellen Computern, um zu überprüfen, ob der Lastenausgleich erfolgreich erstellt wurde.

### <a name="create-cloud-init-configuration-file"></a>Erstellen der Konfigurationsdatei „cloud-init“

Verwenden Sie eine Konfigurationsdatei namens „cloud-init“, um NGINX zu installieren und eine Node.js-App namens „Hello World“ auf einem virtuellen Linux-Computer auszuführen. 

Erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen „cloud-init.txt“. Kopieren Sie die folgende Konfiguration, und fügen Sie sie in die Shell ein. Achten Sie darauf, die gesamte Datei „cloud-init“ ordnungsgemäß zu kopieren – insbesondere die erste Zeile:

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

### <a name="create-availability-set-for-virtual-machines"></a>Erstellen einer Verfügbarkeitsgruppe für virtuelle Computer

Erstellen Sie mit [az vm availability-set create](/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) die Verfügbarkeitsgruppe:

* Name: **myAvSet**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie mit [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) die virtuellen Computer:

#### <a name="vm1"></a>VM1
* Name: **myVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM1**
* VM-Image: **UbuntuLTS**
* Konfigurationsdatei: **cloud-init.txt** (aus dem vorherigen Schritt)
* Verfügbarkeitsgruppe: **myAvSet**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Name: **myVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM2**
* VM-Image: **UbuntuLTS**
* Konfigurationsdatei: **cloud-init.txt** (aus dem vorherigen Schritt)
* Zone: **Zone 2**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Die Bereitstellung der virtuellen Computer kann eine Weile dauern.

## <a name="create-basic-load-balancer"></a>Erstellen eines Basic-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

  * Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  * Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  * Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  * Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie mit [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) einen öffentlichen Lastenausgleich:

* Name: **myLoadBalancer**
* Front-End-Pool namens **myFrontEnd**
* Back-End-Pool namens **myBackEndPool**
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Dem Back-End-Subnetz **myBackendSubnet** zugeordnet

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Von einem Integritätstest werden alle VM-Instanzen überprüft, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. 

Ist der Test bei einem virtuellen Computer nicht erfolgreich, wird er aus dem Lastenausgleich entfernt. Nach Behebung des Fehlers wird der virtuelle Computer dem Lastenausgleich wieder hinzugefügt.

Erstellen Sie mit [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create) einen Integritätstest:

* Überwacht die Integrität der virtuellen Computer
* Name: **myHealthProbe**
* Protokoll: **TCP**
* Überwachter Port: **80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) eine Lastenausgleichsregel:

* Name: **myHTTPRule**
* Lauscht am **Port 80** im Front-End-Pool **myFrontEnd**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** unter Verwendung von **Port 80** 
* Verwendet den Integritätstest **myHealthProbe**
* Protokoll: **TCP**
* Leerlaufzeitüberschreitung von **15 Minuten**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Hinzufügen virtueller Computer zum Back-End-Pool des Lastenausgleichs

Fügen Sie mit [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) die virtuellen Computer zum Back-End-Pool hinzu:


#### <a name="vm1"></a>VM1
* Back-End-Adresspool: **myBackEndPool**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Zugeordnet zur Netzwerkschnittstelle **myNicVM1** und zu **ipconfig1**
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Back-End-Adresspool: **myBackEndPool**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Zugeordnet zur Netzwerkschnittstelle **myNicVM2** und zu **ipconfig1**
* Zugeordnet zum Lastenausgleich **myLoadBalancer**

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

### <a name="create-azure-bastion-public-ip"></a>Erstellen einer öffentlichen IP-Adresse für Azure Bastion

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myBastionIP**
* In **CreateIntLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Erstellen eines Azure Bastion-Subnetzes

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) ein Subnetz:

* Name: **AzureBastionSubnet**
* Adresspräfix: **10.1.1.0/24**
* Virtuelles Netzwerk: **myVNet**
* In Ressourcengruppe **CreateIntLBQS-rg**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Erstellen des Azure Bastion-Hosts
Erstellen Sie mit [az network bastion create](/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) einen Bastionhost:

* Name: **myBastionHost**
* In **CreateIntLBQS-rg**
* Der öffentlichen IP-Adresse **myBastionIP** zugeordnet
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Standort: **eastus**

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Es dauert einige Minuten, bis der Bastionhost bereitgestellt wird.

### <a name="create-test-virtual-machine"></a>Erstellen eines virtuellen Testcomputers

Erstellen Sie die Netzwerkschnittstelle mit [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* Name: **myNicTestVM**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Erstellen Sie den virtuellen Computer mit [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* Name: **myTestVM**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicTestVM**
* Image des virtuellen Computers: **Win2019Datacenter**
* Wählen Sie Werte für **\<adminpass>** und **\<adminuser>** aus.
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Es kann einige Minuten dauern, bis der virtuelle Computer bereitgestellt ist.

### <a name="test"></a>Test

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Bildschirm **Übersicht** nach der privaten IP-Adresse für den Lastenausgleich. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend **myLoadBalancer** aus.

2. Notieren Sie sich in der **Übersicht** von **myLoadBalancer** die Adresse neben **Private IP-Adresse**, oder kopieren Sie sie.

3. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myTestVM** (in der Ressourcengruppe **CreateIntLBQS-rg**) aus.

4. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

7. Öffnen Sie **Internet Explorer** auf **myTestVM**.

8. Geben Sie die IP-Adresse aus dem vorherigen Schritt in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Erstellen eines internen Standard-Lastenausgleichs" border="true":::
   
Um zu sehen, wie Datenverkehr durch den Lastenausgleich auf alle drei virtuellen Computer verteilt wird, können Sie die Standardseite des IIS-Webservers der einzelnen virtuellen Computer anpassen und dann über den Clientcomputer eine Aktualisierung Ihres Webbrowsers erzwingen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie den Befehl [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete), um die Ressourcengruppe, den Lastenausgleich und alle zugehörigen Ressourcen zu entfernen, wenn Sie sie nicht mehr benötigen.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung wurde Folgendes vermittelt:

* Erstellen eines Standard-Lastenausgleichs oder eines öffentlichen Lastenausgleichs
* Anfügen von virtuellen Computern 
* Konfigurieren der Datenverkehrsregel für den Lastenausgleich sowie des Integritätstests
* Testen des Lastenausgleichs

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren: 
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)