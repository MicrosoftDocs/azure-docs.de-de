---
title: 'Erstellen und Testen eines NAT-Gateways: Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: In diesem Tutorial wird veranschaulicht, wie Sie mit der Azure CLI ein NAT-Gateway erstellen und den NAT-Dienst testen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0ec054d55432ad2680314b4ff91a067d37b629d4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734326"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Tutorial: Erstellen eines NAT-Gateways mit der Azure CLI und Testen des NAT-Diensts

In diesem Tutorial erstellen Sie ein NAT-Gateway, um ausgehende Konnektivität für virtuelle Computer in Azure bereitzustellen. Sie stellen einen virtuellen Quell- und Zielcomputer bereit, um das NAT-Gateway zu testen. Sie testen das NAT-Gateway, indem Sie ausgehende Verbindungen mit einer öffentlichen IP-Adresse herstellen. Diese Verbindungen werden vom virtuellen Quell- zum Zielcomputer hergestellt. In diesem Tutorial werden die Quelle und das Ziel der Einfachheit halber in zwei unterschiedlichen virtuellen Netzwerken jeweils in derselben Ressourcengruppe bereitgestellt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.71 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen **myResourceGroupNAT** am Standort **eastus2** erstellt:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Erstellen des NAT-Gateways

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Für den Zugriff auf das öffentliche Internet benötigen Sie mindestens eine öffentliche IP-Adresse für das NAT-Gateway. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), um in **myResourceGroupNAT** eine öffentliche IP-Ressource namens **myPublicIPsource** zu erstellen.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Erstellen des Präfixes für öffentliche IP-Adressen

Sie können für ein NAT-Gateway mehrere Ressourcen bzw. Präfixressourcen für öffentliche IP-Adressen verwenden. In diesem Szenario wird zur Veranschaulichung eine Präfixressource für öffentliche IP-Adressen hinzugefügt.   Verwenden Sie [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create), um eine Präfixressource für öffentliche IP-Adressen mit dem Namen **myPublicIPprefixsource** in **myResourceGroupNAT** zu erstellen.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Erstellen einer NAT-Gatewayressource

In diesem Abschnitt erfahren Sie, wie Sie mit der NAT-Gatewayressource die folgenden Komponenten des NAT-Diensts erstellen und konfigurieren:
  - Öffentlicher IP-Adresspool und Präfix für öffentliche IP-Adressen für ausgehende Datenflüsse, die von der NAT-Gatewayressource übersetzt werden
  - Ändern des Leerlauftimeouts von vier Minuten (Standardwert) in zehn Minuten

Erstellen Sie mit [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) ein globales Azure NAT-Gateway namens **myNATgateway**. Der Befehl verwendet die öffentliche IP-Adresse **myPublicIP** und das Präfix für öffentliche IP-Adressen **myPublicIPprefix**. Mit dem Befehl wird auch das Leerlauftimeout in „10 Minuten“ geändert.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

Das NAT-Gateway ist nun verwendungsbereit, und Sie müssen nur noch konfigurieren, welche Subnetze eines virtuellen Netzwerks das Gateway verwenden sollen.

## <a name="prepare-the-source-for-outbound-traffic"></a>Vorbereiten der Quelle für ausgehenden Datenverkehr

Hier wird die Einrichtung einer vollständigen Testumgebung Schritt für Schritt beschrieben. Sie richten einen Test mit Open-Source-Tools ein, um das NAT-Gateway zu überprüfen. Wir beginnen mit der Quelle, für die das zuvor erstellte NAT-Gateway verwendet wird.

### <a name="configure-virtual-network-for-source"></a>Konfigurieren eines virtuellen Netzwerks für die Quelle

Sie müssen zunächst das virtuelle Netzwerk erstellen, damit Sie einen virtuellen Computer bereitstellen und das NAT-Gateway testen können.

Erstellen Sie das virtuelle Netzwerk **myVnetsource** mit einem Subnetz namens **mySubnetsource** in **myResourceGroupNAT**, indem Sie den Befehl [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet) verwenden.  Der IP-Adressraum für das virtuelle Netzwerk ist **192.168.0.0/16**. Das Subnetz im virtuellen Netzwerk ist **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurieren des NAT-Diensts für das Quellsubnetz

Konfigurieren Sie das Quellsubnetz **mySubnetsource** im virtuellen Netzwerk **myVnetsource** für die Verwendung der spezifischen NAT-Gatewayressource **myNATgateway**, indem Sie den Befehl [az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) verwenden. Mit diesem Befehl wird der NAT-Dienst im angegebenen Subnetz aktiviert.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

Der gesamte ausgehende Datenverkehr an Ziele im Internet verwendet jetzt den NAT-Dienst.  Eine UDR muss nicht konfiguriert werden.

Bevor Sie das NAT-Gateway testen können, müssen Sie einen virtuellen Quellcomputer erstellen.  Sie weisen eine Ressource für eine öffentliche IP-Adresse als öffentliche IP-Adresse auf Instanzebene zu, um den externen Zugriff auf diese VM zu ermöglichen. Diese Adresse wird nur für den Zugriff im Rahmen des Tests verwendet.  Wir veranschaulichen, wie der NAT-Dienst Vorrang vor anderen Ausgangsoptionen erhält.

Zu Übungszwecken können Sie diesen virtuellen Computer auch ohne öffentliche IP-Adresse erstellen und dann einen weiteren virtuellen Computer erstellen, der als Jumpbox ohne öffentliche IP-Adresse verwendet wird.

### <a name="create-public-ip-for-source-vm"></a>Erstellen einer öffentlichen IP-Adresse für den virtuellen Quellcomputer

Sie erstellen eine öffentliche IP-Adresse, die für den Zugriff auf den virtuellen Quellcomputer verwendet werden soll. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), um in **myResourceGroupNAT** eine öffentliche IP-Ressource namens **myPublicIPsourceVM** zu erstellen.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Erstellen einer NSG für den virtuellen Quellcomputer

Da öffentliche Standard-IP-Adressen standardmäßig sicher sind, müssen Sie eine NSG erstellen, um den SSH-Zugriff in eingehender Richtung zuzulassen.  Der NAT-Dienst von Azure kennt die Richtung des Datenflusses. Diese NSG wird nicht für ausgehenden Datenverkehr verwendet, nachdem das NAT-Gateway in demselben Subnetz konfiguriert wurde. Erstellen Sie mit [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) eine NSG-Ressource namens **myNSGsource** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Verfügbarmachen des SSH-Endpunkts auf einem virtuellen Quellcomputer

Sie erstellen eine Regel in der NSG für den SSH-Zugriff auf den virtuellen Quellcomputer. Verwenden Sie [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), um eine NSG-Regel mit dem Namen **ssh** zu erstellen. Diese Regel wird in der NSG mit dem Namen **myNSGsource** in der Ressourcengruppe **myResourceGroupNAT** erstellt.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Erstellen einer NIC für den virtuellen Quellcomputer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) eine Netzwerkschnittstelle, und ordnen Sie sie der öffentlichen IP-Adresse und der Netzwerksicherheitsgruppe zu. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Erstellen eines virtuellen Quellcomputers

Erstellen Sie den virtuellen Computer mit [az vm create](/cli/azure/vm#az-vm-create).  Sie generieren SSH-Schlüssel für diesen virtuellen Computer und speichern den privaten Schlüssel zur späteren Verwendung.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Die Rückgabe des Befehls erfolgt zwar sofort, aber es kann einige Minuten dauern, bis die VM bereitgestellt wird.

## <a name="prepare-destination-for-outbound-traffic"></a>Vorbereiten des Ziels für ausgehenden Datenverkehr

Wir erstellen jetzt ein Ziel für den ausgehenden Datenverkehr, der vom NAT-Dienst übersetzt wird, damit Sie einen Test durchführen können.

### <a name="configure-virtual-network-for-destination"></a>Konfigurieren des virtuellen Netzwerks für das Ziel

 Wir müssen ein virtuelles Netzwerk erstellen, in dem der virtuelle Zielcomputer angeordnet werden kann.  Diese Befehle umfassen die gleichen Schritte wie für den virtuellen Quellcomputer, aber es wurden geringfügige Änderungen zum Verfügbarmachen des Zielendpunkts vorgenommen.

Erstellen Sie das virtuelle Netzwerk **myVnetdestination** mit einem Subnetz namens **mySubnetdestination** in **myResourceGroupNAT**, indem Sie den Befehl [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet) verwenden.  Der IP-Adressraum für das virtuelle Netzwerk ist **192.168.0.0/16**. Das Subnetz im virtuellen Netzwerk ist **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Erstellen einer öffentlichen IP-Adresse für den virtuellen Zielcomputer

Sie erstellen eine öffentliche IP-Adresse, die für den Zugriff auf den virtuellen Quellcomputer verwendet werden soll. Verwenden Sie [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), um in **myResourceGroupNAT** eine öffentliche IP-Ressource namens **myPublicIPdestinationVM** zu erstellen. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Erstellen einer NSG für den virtuellen Zielcomputer

Da öffentliche Standard-IP-Adressen standardmäßig sicher sind, müssen Sie eine NSG erstellen, um eingehenden Zugriff für SSH zuzulassen. Der NAT-Dienst von Azure kennt die Richtung des Datenflusses. Diese NSG wird nicht für ausgehenden Datenverkehr verwendet, nachdem das NAT-Gateway in demselben Subnetz konfiguriert wurde. Erstellen Sie mit [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) eine NSG-Ressource namens **myNSGdestination** in **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Verfügbarmachen des SSH-Endpunkts auf dem virtuellen Zielcomputer

Sie erstellen eine Regel in der NSG für den SSH-Zugriff auf den virtuellen Zielcomputer. Verwenden Sie [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), um eine NSG-Regel mit dem Namen **ssh** zu erstellen. Diese Regel wird in der NSG mit dem Namen **myNSGdestination** in der Ressourcengruppe **myResourceGroupNAT** erstellt.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Verfügbarmachen des HTTP-Endpunkts auf dem virtuellen Zielcomputer

Sie erstellen in der NSG eine Regel für den HTTP-Zugriff auf den virtuellen Zielcomputer. Erstellen Sie mit [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) in **myResourceGroupNAT** eine NSG-Regel namens **http** in der NSG mit dem Namen **myNSGdestination**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Erstellen einer NIC für den virtuellen Zielcomputer

Erstellen Sie mit [az network nic create](/cli/azure/network/nic#az-network-nic-create) eine Netzwerkschnittstelle, und ordnen Sie sie der öffentlichen IP-Adresse **myPublicIPdestinationVM** und der Netzwerksicherheitsgruppe **myNSGdestination** zu. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Erstellen eines virtuellen Zielcomputers

Erstellen Sie den virtuellen Computer mit [az vm create](/cli/azure/vm#az-vm-create).  Sie generieren SSH-Schlüssel für diesen virtuellen Computer und speichern den privaten Schlüssel zur späteren Verwendung.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Die Rückgabe des Befehls erfolgt zwar sofort, aber es kann einige Minuten dauern, bis die VM bereitgestellt wird.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Vorbereiten eines Webservers und Testen der Nutzlast auf dem virtuellen Zielcomputer

Als Erstes müssen Sie die IP-Adresse des virtuellen Zielcomputers ermitteln.  Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des virtuellen Zielcomputers ab. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in einen Editor ein, damit Sie sie in den nachfolgenden Schritten verwenden können. Geben Sie an, dass es sich um den virtuellen Zielcomputer handelt.

### <a name="sign-in-to-destination-vm"></a>Anmelden beim virtuellen Zielcomputer

Die SSH-Anmeldeinformationen sollten in der Cloud Shell-Instanz aus dem vorherigen Vorgang gespeichert werden.  Öffnen Sie [Azure Cloud Shell](https://shell.azure.com) im Browser. Verwenden Sie die im vorherigen Schritt abgerufene IP-Adresse, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen. 

```bash
ssh <ip-address-destination>
```

Kopieren Sie die folgenden Befehle, und fügen Sie sie ein, nachdem Sie sich angemeldet haben.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Mit diesen Befehlen wird der virtuelle Computer aktualisiert, nginx installiert und eine Datei mit 100 KB erstellt. Diese Datei wird mit dem NAT-Dienst vom virtuellen Quellcomputer abgerufen.

Schließen Sie die SSH-Sitzung mit dem virtuellen Zielcomputer.

## <a name="prepare-test-on-source-vm"></a>Vorbereiten des Tests auf dem virtuellen Quellcomputer

Als Erstes müssen Sie die IP-Adresse des virtuellen Quellcomputers ermitteln.  Rufen Sie mithilfe von [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) die öffentliche IP-Adresse des virtuellen Quellcomputers ab. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in einen Editor ein, damit Sie sie in den nachfolgenden Schritten verwenden können. Geben Sie an, dass es sich um den virtuellen Quellcomputer handelt.

### <a name="sign-in-to-source-vm"></a>Anmelden beim virtuellen Quellcomputer

Die SSH-Anmeldeinformationen werden wiederum in Cloud Shell gespeichert. Öffnen Sie in Ihrem Browser eine neue Registerkarte für [Azure Cloud Shell](https://shell.azure.com).  Verwenden Sie die im vorherigen Schritt abgerufene IP-Adresse, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen. 

```bash
ssh <ip-address-source>
```

Kopieren Sie die folgenden Befehle, und fügen Sie sie ein, um den Test des NAT-Diensts vorzubereiten.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Mit diesem Befehl wird Ihr virtueller Computer aktualisiert, go installiert, [hey](https://github.com/rakyll/hey) von GitHub installiert und Ihre Shellumgebung aktualisiert.

Sie können den NAT-Dienst nun testen.

## <a name="validate-nat-service"></a>Überprüfen des NAT-Diensts

Während der Anmeldung beim virtuellen Quellcomputer können Sie **cURL** und **hey** verwenden, um Anforderungen für die IP-Zieladresse zu generieren.

Verwenden Sie cURL, um die 100-KB-Datei abzurufen.  Ersetzen Sie **\<ip-address-destination>** im Beispiel unten durch die IP-Zieladresse, die Sie weiter oben kopiert haben.  Der Parameter **--output** gibt an, dass die abgerufene Datei verworfen wird.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Sie können auch eine Reihe von Anforderungen generieren, indem Sie **hey** verwenden. Ersetzen Sie **\<ip-address-destination>** erneut durch die IP-Zieladresse, die Sie weiter oben kopiert haben.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Mit diesem Befehl werden 100 Anforderungen (jeweils zehn) mit einem Timeout von 30 Sekunden generiert. Die TCP-Verbindung wird nicht wiederverwendet.  Mit jeder Anforderung werden 100 KB abgerufen.  Am Ende der Ausführung meldet **hey** einige statistische Daten, um anzugeben, welches Ergebnis für den NAT-Dienst erzielt wurde.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) entfernen.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie ein NAT-Gateway und einen virtuellen Quell- und Zielcomputer erstellt und dann das NAT-Gateway getestet.

Sehen Sie sich die Metriken in Azure Monitor an, um Informationen zum Betrieb Ihres NAT-Diensts anzuzeigen. Diagnostizieren Sie Probleme wie die Ressourcenüberlastung von verfügbaren SNAT-Ports.  Die Ressourcenüberlastung von SNAT-Ports kann durch das Hinzufügen zusätzlicher Ressourcen bzw. Präfixressourcen für öffentliche IP-Adressen leicht behoben werden.

- Informieren Sie sich über [Virtual Network NAT](./nat-overview.md).
- Informieren Sie sich über [NAT-Gatewayressourcen](./nat-gateway-resource.md).
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe der Azure CLI](./quickstart-create-nat-gateway-cli.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe von Azure PowerShell](./quickstart-create-nat-gateway-powershell.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe des Azure-Portals](./quickstart-create-nat-gateway-portal.md)

> [!div class="nextstepaction"]

