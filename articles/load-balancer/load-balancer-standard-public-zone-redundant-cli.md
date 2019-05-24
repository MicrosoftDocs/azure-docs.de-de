---
title: Lastenausgleich für zonenredundante VMs mithilfe der Azure CLI
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie mithilfe der Azure CLI einen öffentlichen Load Balancer Standard mit einem zonenredundanten Front-End erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: 8f1bf9b9070f2db2376de9cb0a0602eaea98b47e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66147681"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Lastenausgleich für VMs über alle Verfügbarkeitszonen hinweg mithilfe der Azure CLI

In diesem Artikel werden die Schritte zum Erstellen eines öffentlichen [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) mit einem zonenredundanten Front-End erläutert, um Zonenredundanz ohne Abhängigkeit von mehreren DNS-Einträgen zu erzielen. Eine einzelne Front-End-IP-Adresse ist automatisch zonenredundant.  Durch die Verwendung eines zonenredundanten Front-Ends für Ihren Load Balancer können Sie mit einer einzelnen IP-Adresse jetzt eine beliebige VM in einem virtuellen Netzwerk innerhalb einer Region über alle Verfügbarkeitszonen hinweg erreichen. Verwenden Sie Verfügbarkeitszonen, um Ihre Apps und Daten vor einem unwahrscheinlichen Fehler oder Ausfall eines gesamten Rechenzentrums zu schützen.

Informationen zur Verwendung von Verfügbarkeitszonen mit Load Balancer Standard finden Sie unter [Load Balancer Standard und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.17 oder höher ausführen.  Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Unterstützung für Verfügbarkeitszonen ist für ausgewählte Azure-Ressourcen und -Regionen sowie VM-Größenkategorien verfügbar. Weitere Informationen zu den ersten Schritten sowie zu den Azure-Ressourcen, -Regionen und VM-Größenkategorien, die mit Verfügbarkeitszonen verwendet werden können, finden Sie unter [Overview of Availability Zones in Azure (Preview) (Übersicht über Verfügbarkeitszonen in Azure (Vorschauversion))](https://docs.microsoft.com/azure/availability-zones/az-overview). Wenn Sie Unterstützung benötigen, können Sie über [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) Kontakt aufnehmen oder [ein Azure-Supportticket erstellen](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupSLB* am Standort *Europa, Westen* erstellt:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Erstellen einer zonenredundanten öffentlichen Standard-IP-Adresse
Um über das Internet auf Ihre App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Load Balancer. Ein zonenredundantes Front-End wird von allen Verfügbarkeitszonen in einer Region gleichzeitig versorgt. Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) eine zonenredundante öffentliche IP-Adresse. Wenn Sie eine öffentliche Standard-IP-Adresse erstellen, ist diese standardmäßig zonenredundant.

Im folgenden Beispiel wird in der Ressourcengruppe *myResourceGroupLoadBalancer* eine zonenredundante öffentliche IP-Adresse mit dem Namen *myPublicIP* erstellt.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-standard-load-balancer"></a>Erstellen eines Azure Load Balancer Standard
In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:
- Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Load Balancer empfängt
- Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
- Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
- Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-the-load-balancer"></a>Erstellen des Lastenausgleichs
Erstellen Sie mit [az network lb create](/cli/azure/network/lb#az-network-lb-create) einen Standard-Lastenausgleich. Im folgenden Beispiel wird der Lastenausgleich *myLoadBalancer* erstellt und die Adresse *myPublicIP* der Front-End-IP-Konfiguration zugewiesen.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Erstellen eines Integritätstests für Port 80

Ein Integritätstest überprüft alle VM-Instanzen, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. VM-Instanzen mit Fehlern beim Test werden aus dem Load Balancer entfernt, bis sie wieder online geschaltet werden und beim Test überprüft wurde, dass sie fehlerfrei sind. Erstellen Sie mit „az network lb probe create“ einen Integritätstest zur Überwachung der Integrität der virtuellen Computer. Zum Erstellen eines TCP-Integritätstests verwenden Sie [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Im folgenden Beispiel wird ein Integritätstest mit dem Namen *myHealthProbe* erstellt:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Erstellen einer Lastenausgleichsregel für Port 80
Mit einer Lastenausgleichsregel wird die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport definiert. Erstellen Sie mit [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) eine Lastenausgleichsregel mit dem Namen *myLoadBalancerRuleWeb*, die an Port 80 des Front-End-Pools *myFrontEndPool* lauscht und den Netzwerkdatenverkehr nach erfolgtem Lastenausgleich an den Back-End-Adresspool *myBackEndPool* sendet, wobei ebenfalls der Port 80 verwendet wird.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
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

Erstellen Sie ein virtuelles Netzwerk mit dem Namen *myVnet* und dem Subnetz *mySubnet* in myResourceGroup, indem Sie den Befehl [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) verwenden.


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) eine Netzwerksicherheitsgruppe mit dem Namen *myNetworkSecurityGroup*, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Erstellen Sie mit [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) eine Netzwerksicherheitsgruppenregel namens *myNetworkSecurityGroupRule* für Port 80.

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) drei virtuelle Netzwerkkarten, und ordnen Sie sie der öffentlichen IP-Adresse und der Netzwerksicherheitsgruppe zu. Im folgenden Beispiel werden sechs virtuelle Netzwerkkarten erstellt. (jeweils eine virtuelle NIC pro virtuellem Computer, den Sie in den folgenden Schritten für Ihre App erstellen). Sie können jederzeit weitere virtuelle NICs und virtuelle Computer erstellen und dem Load Balancer hinzufügen:

```azurecli-interactive
for i in `seq 1 3`; do
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
In diesem Beispiel erstellen Sie drei virtuelle Computer in Zone 1, Zone 2 und Zone 3, die als Back-End-Server für den Lastenausgleich verwendet werden sollen. Außerdem installieren Sie NGINX auf den virtuellen Computern, um zu überprüfen, ob der Lastenausgleich erfolgreich erstellt wurde.

### <a name="create-cloud-init-config"></a>Erstellen der cloud-init-Konfiguration

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

### <a name="create-the-zonal-virtual-machines"></a>Erstellen von virtuellen Computern in einer Zone
Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) die VMs in Zone 1, Zone 2 und Zone 3. Im folgenden Beispiel wird in jeder Zone eine VM erstellt und es werden SSH-Schlüssel generiert, sofern sie noch nicht vorhanden sind:

Erstellen Sie eine VM in jeder Zone (Zone 1, Zone 2 und Zone 3) am Standort *Europa, Westen*.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des Lastenausgleichs ab. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Geben Sie die öffentliche IP-Adresse in einem Webbrowser ein. Nicht vergessen: Es dauert einige Minuten, bis die virtuellen Computer bereit sind und der Load Balancer Datenverkehr an sie verteilen kann. Die App wird wie im folgenden Beispiel mit dem Hostnamen des virtuellen Computers angezeigt, an den der Load Balancer den Datenverkehr verteilt hat:

![Ausgeführte Node.js-App](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Damit der Datenverkehr auf die VMs in allen drei Verfügbarkeitszonen, in denen Ihre App ausgeführt wird, durch den Lastenausgleich verteilt wird, können Sie eine VM in einer bestimmten Zone beenden und Ihren Browser aktualisieren.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum [Load Balancer Standard](./load-balancer-standard-overview.md).



