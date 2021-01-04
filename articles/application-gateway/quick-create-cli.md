---
title: 'Schnellstart: Weiterleiten von Webdatenverkehr mit der CLI'
titleSuffix: Azure Application Gateway
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie die Azure CLI zum Erstellen einer Azure Application Gateway-Instanz verwenden, mit der Webdatenverkehr an virtuelle Computer in einem Back-End-Pool geleitet wird.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 08/27/2020
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: d4b6fc296ae41667d271e243e9aca9b594e4a5b6
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886701"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway: Azure CLI

In dieser Schnellstartanleitung verwenden Sie die Azure CLI zum Erstellen eines Anwendungsgateways. Anschließend testen Sie es, um sicherzustellen, dass es ordnungsgemäß funktioniert. 

Das Anwendungsgateway leitet den Webdatenverkehr Ihrer Anwendungen an bestimmte Ressourcen in einem Back-End-Pool weiter. Sie weisen den Ports Listener zu, erstellen Regeln und fügen Ressourcen zu einem Back-End-Pool hinzu. Der Einfachheit halber wird in diesem Artikel ein einfaches Setup mit einer öffentlichen Front-End-IP-Adresse, einem grundlegenden Listener zum Hosten einer einzelnen Website auf diesem Anwendungsgateway, einer Routingregel für grundlegende Anforderungen und zwei virtuellen Computern im Back-End-Pool verwendet.

Sie können für diesen Schnellstart auch [Azure PowerShell](quick-create-powershell.md) oder das [Azure-Portal](quick-create-portal.md) verwenden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Erstellen Sie mithilfe von `az group create` eine Ressourcengruppe. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupAG* am Standort *eastus* erstellt.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen 

Für die Kommunikation in Azure zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich.  Das Subnetz für das Anwendungsgateway kann nur Anwendungsgateways enthalten. Andere Ressourcen sind nicht zulässig.  Sie können entweder ein neues Subnetz für Application Gateway erstellen oder ein bereits vorhandenes verwenden. In diesem Beispiel erstellen Sie zwei Subnetze: eins für das Anwendungsgateway und eins für die Back-End-Server. Je nach Anwendungsfall können Sie die Front-End-IP-Adresse von Application Gateway als öffentliche oder als private IP-Adresse konfigurieren. In diesem Beispiel verwenden Sie eine öffentliche Front-End-IP-Adresse.

Verwenden Sie `az network vnet create`, um das virtuelle Netzwerk und das Subnetz zu erstellen. Führen Sie zum Erstellen der öffentlichen IP-Adresse `az network public-ip create` aus.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>Erstellen der Back-End-Server

Ein Back-End kann Netzwerkkarten, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen, vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDN) und Back-Ends mit mehreren Mandanten wie Azure App Service umfassen. In diesem Beispiel erstellen Sie zwei virtuelle Computer, die als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um das Anwendungsgateway zu testen.

#### <a name="create-two-virtual-machines"></a>Erstellen von zwei virtuellen Computern

Installieren Sie den NGINX-Webserver auf den virtuellen Computern, um zu überprüfen, ob das Anwendungsgateway erfolgreich erstellt wurde. Sie können eine cloud-init-Konfigurationsdatei verwenden, um NGINX zu installieren und eine Node.js-App „Hallo Welt“ auf einer Linux-VM auszuführen. Weitere Informationen zu cloud-init finden Sie unter [cloud-init-Unterstützung für VMs in Azure](../virtual-machines/linux/using-cloud-init.md).

Kopieren Sie in Ihrer Azure Cloud Shell die folgende Konfiguration, und fügen Sie sie in eine Datei mit dem Namen *cloud-init.txt* ein. Geben Sie *editor cloud-init.txt* ein, um die Datei zu erstellen.

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

Erstellen Sie mithilfe von `az network nic create` die Netzwerkschnittstellen. Zum Erstellen der virtuellen Computer verwenden Sie `az vm create`.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Erstellen Sie mithilfe von `az network application-gateway create` ein Anwendungsgateway. Wenn Sie über die Azure CLI ein Anwendungsgateway erstellen, geben Sie Konfigurationsinformationen wie Kapazität, SKU und HTTP-Einstellungen an. Azure fügt dann die privaten IP-Adressen der Netzwerkschnittstellen als Server im Back-End-Pool des Anwendungsgateways hinzu.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Die Erstellung des Anwendungsgateways in Azure kann bis zu 30 Minuten in Anspruch nehmen. Nachdem das Anwendungsgateway erstellt wurde, können Sie die folgenden Einstellungen im Abschnitt **Einstellungen** der Seite **Anwendungsgateway** anzeigen:

- **appGatewayBackendPool**: Diese Einstellung finden Sie auf der Seite **Back-End-Pools**. Sie gibt den erforderlichen Back-End-Pool an.
- **appGatewayBackendHttpSettings**: Diese Einstellung finden Sie auf der Seite **HTTP-Einstellungen**. Sie gibt an, dass das Anwendungsgateway den Port 80 und das HTTP-Protokoll für die Kommunikation verwendet.
- **appGatewayHttpListener**: Diese Einstellung finden Sie auf der Seite **Listener**. Sie gibt den Standardlistener an, der **appGatewayBackendPool** zugeordnet ist.
- **appGatewayFrontendIP**: Diese Einstellung finden Sie auf der Seite **Front-End-IP-Konfigurationen**. Sie weist *myAGPublicIPAddress* zu **appGatewayHttpListener** zu.
- **rule1**: Diese Einstellung finden Sie auf der Seite **Regeln**. Sie gibt die Standardroutingregel an, die **appGatewayHttpListener** zugeordnet ist.

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Für die Erstellung des Anwendungsgateways in Azure ist kein NGINX-Webserver erforderlich. Sie haben ihn in diesem Schnellstart installiert, um zu überprüfen, ob Azure das Anwendungsgateway erfolgreich erstellt hat. Verwenden Sie `az network public-ip show`, um die öffentliche IP-Adresse des neuen Anwendungsgateways abzurufen. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.
    
![Testen des Anwendungsgateways](./media/quick-create-cli/application-gateway-nginxtest.png)

Wenn Sie den Browser aktualisieren, sollte der Name der zweiten VM angezeigt werden. Dies weist auf eine erfolgreiche Erstellung des Anwendungsgateways hin, das eine Verbindung mit dem Back-End herstellen kann.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit dem Anwendungsgateway erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe mit dem Befehl `az group delete`. Wenn Sie die Ressourcengruppe löschen, werden auch das Anwendungsgateway und alle zugehörigen Ressourcen entfernt.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Webdatenverkehr mit einem Anwendungsgateway per Azure CLI](./tutorial-manage-web-traffic-cli.md)

