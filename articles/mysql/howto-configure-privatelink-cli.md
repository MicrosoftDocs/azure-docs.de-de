---
title: Private Link – Azure-Befehlszeilenschnittstelle – Azure Database for MySQL
description: Hier erfahren Sie, wie Sie Private Link für Azure Database for MySQL über die Azure-Befehlszeilenschnittstelle konfigurieren.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: da6a2b97d2656d56fa2aa0e7259fba433bd7b81e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998582"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Erstellen und Verwalten von Private Link für Azure Database for MySQL über die Befehlszeilenschnittstelle (CLI)

Ein privater Endpunkt ist der grundlegende Baustein für Private Link in Azure. Mit ihm können Azure-Ressourcen wie virtuelle Computer (VMs) privat mit Private Link-Ressourcen kommunizieren. In diesem Artikel erfahren Sie, wie Sie einen virtuellen Computer in einem virtuellen Azure-Netzwerk und einen Azure Database for MySQL-Server mit einem privaten Azure-Endpunkt über die Azure-Befehlszeilenschnittstelle erstellen.

> [!NOTE]
> Das Feature „Private Link“ ist nur für Azure Database for MySQL-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Datenbankserver einer dieser Tarife festgelegt ist.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie eine Ressource erstellen können, müssen Sie eine Ressourcengruppe zum Hosten des virtuellen Netzwerks erstellen. Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *westeurope*:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Erstellen Sie mit [az network vnet create](/cli/azure/network/vnet) ein virtuelles Netzwerk. Bei diesem Beispiel wird ein virtuelles Standardnetzwerk mit dem Namen *myVirtualNetwork* und dem Subnetz *mySubnet* erstellt:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Deaktivieren von Richtlinien für den privaten Endpunkt im Subnetz 
Azure stellt Ressourcen für ein Subnetz innerhalb eines virtuellen Netzwerks bereit. Daher müssen Sie das Subnetz erstellen oder aktualisieren, um die [Netzwerkrichtlinien](../private-link/disable-private-endpoint-network-policy.md) für den privaten Endpunkt im Subnetz zu deaktivieren. Aktualisieren Sie eine Subnetzkonfiguration mit dem Namen *mySubnet* mit [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Erstellen des virtuellen Computers 
Erstellen Sie mit „az vm create“ einen virtuellen Computer. Geben Sie bei entsprechender Aufforderung ein Kennwort als Anmeldeinformation für den virtuellen Computer ein. In diesem Beispiel wird ein virtueller Computer mit dem Namen *myVm* erstellt: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Notieren Sie die öffentliche IP-Adresse der VM. Sie verwenden diese Adresse im nächsten Schritt zum Herstellen einer Verbindung mit dem virtuellen Computer über das Internet.

## <a name="create-an-azure-database-for-mysql-server"></a>Erstellen eines Servers für Azure-Datenbank für MySQL 
Erstellen Sie mit dem Befehl „az mysql server create“ eine Azure Database for MySQL-Instanz. Denken Sie daran, dass der Name Ihres MySQL-Servers innerhalb von Azure eindeutig sein muss. Ersetzen Sie daher den Platzhalterwert in Klammern durch Ihren eigenen eindeutigen Wert: 

```azurecli-interactive
# Create a server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> In einigen Fällen befinden sich Azure Database for MySQL und das VNET-Subnetz in unterschiedlichen Abonnements. In diesen Fällen müssen Sie folgende Konfigurationen sicherstellen:
> - Stellen Sie sicher, dass für beide Abonnements der Ressourcenanbieter **Microsoft.DBforMySQL** registriert ist. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen][resource-manager-portal].

## <a name="create-the-private-endpoint"></a>Erstellen des privaten Endpunkts 
Erstellen Sie einen privaten Endpunkt für den MySQL-Server in Ihrer Virtual Network-Instanz: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMySQL/servers" --query "id" -o tsv) \    
    --group-id mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurieren der privaten DNS-Zone 
Erstellen Sie eine private DNS-Zone für die MySQL-Server-Domäne, und erstellen Sie eine Zuordnungsverknüpfung mit der Virtual Network-Instanz. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Der FQDN in der DNS-Einstellung des Kunden wird nicht in die konfigurierte private IP-Adresse aufgelöst. Sie müssen eine DNS-Zone für den konfigurierten FQDN einrichten, wie [hier](../dns/dns-operations-recordsets-portal.md) gezeigt.

## <a name="connect-to-a-vm-from-the-internet"></a>Herstellen einer Verbindung mit einem virtuellen Computer über das Internet

Stellen Sie wie folgt eine Verbindung mit dem virtuellen Computer *myVm* aus dem Internet her:

1. Geben Sie in der Suchleiste des Portals *myVm* ein.

1. Wählen Sie die Schaltfläche **Verbinden** aus. Nach dem Auswählen der Schaltfläche **Verbinden** wird **Verbindung mit virtuellem Computer herstellen** geöffnet.

1. Wählen Sie **RDP-Datei herunterladen** aus. Azure erstellt eine Remotedesktopprotokoll-Datei (*RDP*) und lädt sie auf Ihren Computer herunter.

1. Öffnen Sie die Datei *downloaded.rdp*.

    1. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus.

    1. Geben Sie den Benutzernamen und das Kennwort ein, den/das Sie beim Erstellen des virtuellen Computers angegeben haben.

        > [!NOTE]
        > Unter Umständen müssen Sie **Weitere Optionen** > **Anderes Konto verwenden** auswählen, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.

1. Klicken Sie auf **OK**.

1. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn Sie eine Zertifikatwarnung erhalten, wählen Sie **Ja** oder **Weiter** aus.

1. Sobald der VM-Desktop angezeigt wird, minimieren Sie ihn, um zu Ihrem lokalen Desktop zurückzukehren.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Privates Zugreifen auf den MySQL-Server vom virtuellen Computer

1. Öffnen Sie PowerShell auf dem Remotedesktop von  *myVM*.

2. Geben Sie  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com` ein. 

    Sie erhalten eine Meldung wie die folgende:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Testen Sie die Verbindung über den privaten Link für den MySQL-Server mit einem beliebigen verfügbaren Client. Im folgenden Beispiel wird für diesen Vorgang [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) verwendet.


4. Geben Sie in **Neue Verbindung** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Verbindungsname| Wählen Sie den gewünschten Verbindungsnamen aus.|
    | Hostname | Select *mydemoserver.privatelink.mysql.database.azure.com* |
    | Username | Geben Sie einen Benutzernamen als *username@servername* ein, der während der Erstellung des MySQL-Servers angegeben wurde. |
    | Kennwort | Geben Sie ein während der Erstellung des MySQL-Servers angegebenes Kennwort ein. |
    ||

5. Wählen Sie „Verbinden“ aus.

6. Durchsuchen Sie Datenbanken im linken Menü.

7. (Optional:) Erstellen Sie die MySQL-Datenbank, oder fragen Sie sie ab.

8. Schließen Sie die Remotedesktopverbindung mit myVm.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 
Wenn die Ressourcengruppe und alle enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit „az group delete“ entfernen: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen finden Sie unter [Was ist privater Endpunkt in Azure?](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md