---
title: 'Schnellstart: Erstellen eines privaten Azure-Endpunkts mit der Azure-Befehlszeilenschnittstelle'
description: In diesem Schnellstart erfahren Sie, wie Sie mithilfe der Azure CLI einen privaten Endpunkt erstellen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: bba912930a9dff0a79e0b0d81025b7524c238db0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368677"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Schnellstart: Erstellen eines privaten Endpunkts mit Azure CLI

Beginnen Sie mit Azure Private Link, indem Sie einen privaten Endpunkt verwenden, um eine Verbindung mit einer Azure-Web-App herzustellen.

In diesem Schnellstart erstellen Sie einen privaten Endpunkt für eine Azure-Web-App und stellen einen virtuellen Computer bereit, um die private Verbindung zu testen.  

Private Endpunkte können für verschiedene Arten von Azure-Diensten erstellt werden, z. B. Azure SQL und Azure Storage.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Eine Azure-Web-App mit einem App Service-Plan vom Typ **PremiumV2-Tarif** oder höher, die in Ihrem Azure-Abonnement bereitgestellt wird.  
    * Weitere Informationen und ein Beispiel finden Sie unter [Schnellstart: Erstellen von ASP.NET Core-Web-Apps in Azure](../app-service/quickstart-dotnetcore.md). 
    * Ein ausführliches Tutorial zum Erstellen einer Web-App und eines Endpunkts finden Sie unter [Tutorial: Herstellen einer Verbindung mit einer Web-App mithilfe eines privaten Azure-Endpunkts](tutorial-private-endpoint-webapp-portal.md).
* Melden Sie sich beim Azure-Portal an, und führen Sie `az login` aus, um sich zu vergewissern, dass Ihr Abonnement aktiv ist.
* Überprüfen Sie Ihre Version der Azure-Befehlszeilenschnittstelle, indem Sie in einem Terminal oder Befehlsfenster `az --version`ausführen. Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Sollten Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe:

* Name: **CreatePrivateEndpointQS-rg** 
* Standort: **eastus**

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk, ein Subnetz und einen Bastion-Host. 

Der Bastion-Host wird verwendet, um eine sichere Verbindung mit dem virtuellen Computer herzustellen, um den privaten Endpunkt zu testen.

Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) ein virtuelles Netzwerk:

* Name: **myVNet**
* Adresspräfix: **10.0.0.0/16**
* Subnetzname: **myBackendSubnet**
* Subnetzpräfix: **10.0.0.0/24**
* Ressourcengruppe: **CreatePrivateEndpointQS-rg**
* Standort: **eastus**

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Aktualisieren Sie das Subnetz mit [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update), um für den privaten Endpunkt Netzwerkrichtlinien für private Endpunkte zu deaktivieren.

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myBastionIP**
* In: **CreatePrivateEndpointQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein Bastionsubnetz:

* Name: **AzureBastionSubnet**
* Adresspräfix: **10.0.1.0/24**
* Virtuelles Netzwerk: **myVNet**
* Ressourcengruppe: **CreatePrivateEndpointQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Erstellen Sie mit [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) einen Bastionhost:

* Name: **myBastionHost**
* In: **CreatePrivateEndpointQS-rg**
* Der öffentlichen IP-Adresse **myBastionIP** zugeordnet
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Standort: **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

## <a name="create-test-virtual-machine"></a>Erstellen eines virtuellen Testcomputers

In diesem Abschnitt erstellen Sie einen virtuellen Computer zum Testen des privaten Endpunkts.

Erstellen Sie mit  [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer. Geben Sie bei entsprechender Aufforderung ein Kennwort als Anmeldeinformation für den virtuellen Computer ein:

* Name: **myVM**.
* In: **CreatePrivateEndpointQS-rg**
* Netzwerk: **myVNet**.
* Subnetz: **myBackendSubnet**
* Serverimage: **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie den privaten Endpunkt.

Verwenden Sie [az webapp list](/cli/azure/webapp#az_webapp_list), um die Ressourcen-ID der Web-App, die Sie zuvor erstellt haben, in einer Shellvariablen zu platzieren.

Erstellen Sie mithilfe von [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) den Endpunkt und die Verbindung:

* Name: **myPrivateEndpoint**.
* Ressourcengruppe: **CreatePrivateEndpointQS-rg**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Verbindungsname: **myConnection**
* Ihre Web-App: **\<webapp-resource-group-name>**

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

In diesem Abschnitt erstellen und konfigurieren Sie die private DNS-Zone mithilfe von [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Verwenden Sie [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) zum Erstellen der virtuellen Netzwerkverknüpfung mit der DNS-Zone.

Sie erstellen eine DNS-Zonengruppe mit [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zone: **privatelink.azurewebsites.net**
* Virtuelles Netzwerk: **myVNet**
* Ressourcengruppe: **CreatePrivateEndpointQS-rg**
* DNS-Link: **myDNSLink**
* Zugeordnet zu: **myPrivateEndpoint**
* Zonengruppe: **MyZoneGroup**

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem SQL-Server herzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) 
 
2. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

3. Wählen Sie **CreatePrivateEndpointQS-rg** aus.

4. Wählen Sie **myVM** aus.

5. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

6. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

7. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

8. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

9. Geben Sie `nslookup <your-webapp-name>.azurewebsites.net` ein. Ersetzen Sie **\<your-webapp-name>** durch den Namen der Web-App, die Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Als Name der Web-App wird die private IP-Adresse **10.0.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.

10. Öffnen Sie in der Bastionhostverbindung mit **myVM** den Internet Explorer.

11. Geben Sie die URL Ihrer Web-App ein: **https://\<your-webapp-name>.azurewebsites.net**.

12. Sie erhalten die Standardseite der Web-App, wenn Ihre Anwendung noch nicht bereitgestellt wurde:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standardseite der Web-App." border="true":::

13. Trennen Sie die Verbindung zu **myVM**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 
Wenn Sie mit der Nutzung des privaten Endpunkts und der VM fertig sind, verwenden Sie [az group delete](/cli/azure/group#az_group_delete), um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes erstellt:

* virtuelles Netzwerk und Bastionhost
* Virtuellen Computer
* Privater Endpunkt für eine Azure-Web-App.

Sie haben den virtuellen Computer verwendet, um die Konnektivität mit der Web-App über den privaten Endpunkt auf sichere Weise zu testen.

Weitere Informationen zu den Diensten, die einen privaten Endpunkt unterstützen, finden Sie unter:
> [!div class="nextstepaction"]
> [Private Link-Verfügbarkeit](private-link-overview.md#availability)
