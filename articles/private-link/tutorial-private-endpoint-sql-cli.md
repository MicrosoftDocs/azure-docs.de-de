---
title: 'Tutorial: Herstellen einer Verbindung mit einem Azure SQL-Server über einen privaten Azure-Endpunkt – Azure CLI'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe der Azure CLI einen Azure SQL-Server mit einem privaten Endpunkt erstellen.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.openlocfilehash: 8cfe44b9433ee1daac028253aa45c97804c88ae5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544104"
---
# <a name="tutorial---connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Tutorial: Herstellen einer Verbindung mit einem Azure SQL-Server über einen privaten Azure-Endpunkt – Azure CLI

Der private Azure-Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks und eines Bastion-Hosts.
> * Erstellen Sie eine VM.
> * Erstellen eines Azure SQL-Servers und eines privaten Endpunkts
> * Testen der Konnektivität mit dem privaten Endpunkt für SQL Server

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Melden Sie sich beim Azure-Portal an, und führen Sie `az login` aus, um sich zu vergewissern, dass Ihr Abonnement aktiv ist.
* Überprüfen Sie Ihre Version der Azure-Befehlszeilenschnittstelle, indem Sie in einem Terminal oder Befehlsfenster `az --version`ausführen. Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Sollten Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe:

* Name: **CreateSQLEndpointTutorial-rg** 
* Standort: **eastus**

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
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
* Ressourcengruppe: **CreateSQLEndpointTutorial-rg**
* Standort: **eastus**

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
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
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Verwenden Sie [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Erstellen einer standardmäßigen zonenredundanten öffentlichen IP-Adresse namens **myBastionIP**
* Ort: **CreateSQLEndpointTutorial-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Erstellen Sie mit [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) ein Bastionsubnetz:

* Name: **AzureBastionSubnet**
* Adresspräfix: **10.0.1.0/24**
* Virtuelles Netzwerk: **myVNet**
* Ressourcengruppe: **CreateSQLEndpointTutorial-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Erstellen Sie mit [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) einen Bastionhost:

* Name: **myBastionHost**
* Ort: **CreateSQLEndpointTutorial-rg**
* Der öffentlichen IP-Adresse **myBastionIP** zugeordnet
* Dem virtuellen Netzwerk **myVNet** zugeordnet
* Standort: **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

## <a name="create-test-virtual-machine"></a>Erstellen eines virtuellen Testcomputers

In diesem Abschnitt erstellen Sie einen virtuellen Computer zum Testen des privaten Endpunkts.

Erstellen Sie mit  [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer. Geben Sie bei entsprechender Aufforderung ein Kennwort als Anmeldeinformation für den virtuellen Computer ein:

* Name: **myVM**
* Ort: **CreateSQLEndpointTutorial-rg**
* Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Serverimage: **Win2019Datacenter**

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>Erstellen eines Azure SQL-Servers

In diesem Abschnitt erstellen Sie einen SQL-Server und eine Datenbank.

Verwenden Sie [az sql server create](/cli/azure/sql/server#az_sql_server_create) zum Erstellen eines SQL-Servers:

* Ersetzen Sie **\<sql-server-name>** durch einen eindeutigen Servernamen.
* Ersetzen Sie **\<your-password>** durch Ihr Kennwort.
* Ort: **CreateSQLEndpointTutorial-rg**
* Region: **eastus**

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Verwenden Sie [az sql db create](/cli/azure/sql/db#az_sql_db_create) zum Erstellen einer Datenbank:

* Name: **mydatabase**
* Ort: **CreateSQLEndpointTutorial-rg**
* Ersetzen Sie **\<sql-server-name>** durch einen eindeutigen Servernamen.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

In diesem Abschnitt erstellen Sie den privaten Endpunkt.

Platzieren Sie mithilfe von [az sql server list](/cli/azure/sql/server#az_sql_server_list) die Ressourcen-ID des SQL-Servers in einer Shellvariablen.

Erstellen Sie mithilfe von [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) den Endpunkt und die Verbindung:

* Name: **myPrivateEndpoint**
* Ressourcengruppe: **CreateSQLEndpointTutorial-rg**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Verbindung mit dem Namen **myConnection**

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone

In diesem Abschnitt erstellen und konfigurieren Sie die private DNS-Zone mithilfe von [az network private-dns zone create](/cli/azure/ext/privatedns/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create).  

Verwenden Sie [az network private-dns link vnet create](/cli/azure/ext/privatedns/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) zum Erstellen der virtuellen Netzwerkverknüpfung mit der DNS-Zone.

Sie erstellen eine DNS-Zonengruppe mit [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create).

* Zone mit dem Namen **privatelink.database.windows.net**
* Virtuelles Netzwerk: **myVNet**
* Ressourcengruppe: **CreateSQLEndpointTutorial-rg**
* DNS-Link mit dem Namen **myDNSLink**
* **myPrivateEndpoint** zugeordnet
* Zonengruppe mit dem Namen **MyZoneGroup**

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Testen der Verbindung mit dem privaten Endpunkt

In diesem Abschnitt verwenden Sie den virtuellen Computer, den Sie im vorherigen Schritt erstellt haben, um über den privaten Endpunkt eine Verbindung mit dem SQL-Server herzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) 
 
2. Wählen Sie **Ressourcengruppen** im linken Navigationsbereich aus.

3. Wählen Sie **CreateSQLEndpointTutorial-rg** aus.

4. Wählen Sie **myVM** aus.

5. Wählen Sie auf der Seite „Übersicht“ für **myVM** die Option **Verbinden** und dann **Bastion** aus.

6. Wählen Sie die blaue Schaltfläche **Bastion verwenden** aus.

7. Geben Sie Benutzernamen und Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben.

8. Öffnen Sie Windows PowerShell auf dem Server, nachdem Sie eine Verbindung hergestellt haben.

9. Geben Sie `nslookup <sqlserver-name>.database.windows.net` ein. Ersetzen Sie **\<sqlserver-name>** durch den Namen des SQL-Servers, den Sie in den vorherigen Schritten erstellt haben.  Sie erhalten eine Meldung ähnlich der folgenden:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Als Name für den SQL-Server wird die private IP-Adresse **10.0.0.5** zurückgegeben.  Diese Adresse befindet sich in dem Subnetz des virtuellen Netzwerks, das Sie zuvor erstellt haben.


10. Installieren Sie [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) auf **myVM**.

11. Öffnen Sie **SQL Server Management Studio**.

12. Geben Sie unter **Mit Server verbinden** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Servertyp | Wählen Sie **Datenbank-Engine** aus.|
    | Servername | Geben Sie **\<sql-server-name>.database.windows.net** ein. |
    | Authentifizierung | Wählen Sie **SQL Server-Authentifizierung** aus. |
    | Benutzername | Geben Sie den Benutzernamen ein, den Sie bei der Erstellung des Servers eingegeben haben. |
    | Kennwort | Geben Sie das Kennwort ein, das Sie bei der Erstellung des Servers eingegeben haben. |
    | Kennwort speichern | Wählen Sie **Ja** aus. |

13. Wählen Sie **Verbinden**.

14. Durchsuchen Sie Datenbanken im linken Menü.

15. (Optional) Erstellen Sie die Informationen unter **mysqldatabase**, bzw. rufen Sie sie ab.

16. Trennen Sie die Bastionhostverbindung mit **myVM**. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Wenn Sie Ihre Arbeit mit dem privaten Endpunkt, dem SQL-Server und dem virtuellen Computer abgeschlossen haben, löschen Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes erstellt:

* virtuelles Netzwerk und Bastionhost
* Virtuellen Computer
* Azure SQL-Server mit privatem Endpunkt

Sie haben den virtuellen Computer verwendet, um die Konnektivität mit dem SQL-Server über den privaten Endpunkt auf sichere Weise zu testen.

Erfahren Sie mehr über den Private Link-Dienst:
> [!div class="nextstepaction"]
> [Erstellen eines Private Link-Diensts](create-private-link-service-portal.md)