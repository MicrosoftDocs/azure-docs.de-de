---
title: Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MariaDB mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle VNET-Dienstendpunkte und -Regeln für Azure Database for MariaDB erstellen und verwalten.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: a86b755770dc59f196c57f1d86e7f29200ce25e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171471"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln für Azure Database for MariaDB mithilfe der Azure-Befehlszeilenschnittstelle

Über Dienstendpunkte und Regeln für ein virtuelles Netzwerk (VNET) wird der private Adressraum eines virtuellen Netzwerks auf Ihren Azure Database for MariaDB-Server ausgeweitet. Mit den praktischen Befehlen der Azure-Befehlszeilenschnittstelle (Azure CLI) können Sie VNET-Dienstpunkte und -Regeln erstellen, aktualisieren, löschen, auflisten und anzeigen, um Ihren Server zu verwalten. Eine Übersicht über VNET-Dienstendpunkte für Azure Database for MariaDB – einschließlich der Einschränkungen – finden Sie unter [VNET-Dienstendpunkte für Azure Database for MariaDB-Server](concepts-data-access-security-vnet.md). VNET-Dienstendpunkte sind in allen unterstützten Regionen für Azure Database for MariaDB verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli) oder verwenden Sie Azure Cloud Shell im Browser.
- [Azure Database for MariaDB-Server und eine Datenbank](quickstart-create-mariadb-server-database-using-azure-cli.md)

> [!NOTE]
> VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.

## <a name="configure-vnet-service-endpoints"></a>Konfigurieren von VNET-Dienstendpunkten
Die [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)-Befehle werden zum Konfigurieren von virtuellen Netzwerken verwendet.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

Wenn Sie die CLI lokal ausführen, müssen Sie sich mit dem Befehl [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) an Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.
```azurecli-interactive
az login
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die Eigenschaft **id** aus der Ausgabe von **az login** für Ihr Abonnement.

- Das Konto muss über die erforderlichen Berechtigungen zum Erstellen eines virtuellen Netzwerks und eines Dienstendpunkts verfügen.

Dienstendpunkte können unabhängig für virtuelle Netzwerke konfiguriert werden, der Benutzer benötigt hierzu Schreibzugriff auf das virtuelle Netzwerk.

Um Azure-Dienstressourcen in einem VNET zu sichern, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung für „Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/“ verfügen. Diese Berechtigung ist standardmäßig in die integrierten Dienstadministratorrollen integriert und kann durch die Erstellung von benutzerdefinierten Rollen geändert werden.

Erfahren Sie mehr über [integrierte Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) und das Zuweisen bestimmter Berechtigungen zu [benutzerdefinierten Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

VNETs und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Wenn das VNET und die Ressourcen von Azure-Diensten in unterschiedlichen Abonnements enthalten sind, sollten sich die Ressourcen unter demselben Active Directory-Mandanten (AD) befinden.

> [!IMPORTANT]
> Es wird dringend empfohlen, diesen Artikel zu Dienstendpunktkonfigurationen und Überlegungen zu lesen, bevor Sie Dienstendpunkte konfigurieren. **VNET-Dienstendpunkt:** Ein [VNET-Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. VNET-Dienstendpunkte verwenden den Diensttypnamen **Microsoft.Sql**, der auf den Azure-Dienst „SQL-Datenbank“ verweist. Dieses Diensttag gilt auch für die Dienste Azure SQL-Datenbank, Azure Database for MariaDB, Azure Database for PostgreSQL und Azure Database for MySQL. Beim Anwenden des Diensttags **Microsoft.Sql** auf einen VNET-Dienstendpunkt muss beachtet werden, dass auf diese Weise der Dienstendpunkt-Datenverkehr für alle Azure-Datenbankdienste konfiguriert wird. Dies schließt Azure SQL-Datenbank-, Azure Database for PostgreSQL-, Azure Database for MariaDB- und Azure Database for MySQL-Server im Subnetz ein.

### <a name="sample-script"></a>Beispielskript

Dieses Beispielskript wird zum Erstellen eines Azure Database for MariaDB-Servers, eines virtuellen Netzwerks und eines VNET-Dienstendpunkts sowie zum Schützen des Servers im Subnetz mit einer VNET-Regel verwendet. Ändern Sie in diesem Beispielskript den Benutzernamen und das Kennwort des Administrators. Ersetzen Sie die SubscriptionID im Befehl `az account set --subscription` durch Ihre eigene Abonnement-ID.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->
