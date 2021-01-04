---
title: 'Schnellstart: Erstellen eines Synapse SQL-Pools mithilfe der Azure-Befehlszeilenschnittstelle'
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle schnell einen Synapse SQL-Pool mit einer Firewallregel auf Serverebene erstellen.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: Kevin
ms.custom: azure-synapse
ms.openlocfilehash: 59195bba69bb343e55cfcb7342400d93dcce60c0
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932957"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Schnellstart: Erstellen eines Synapse SQL-Pools mithilfe der Azure-Befehlszeilenschnittstelle

Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen Synapse SQL-Pool (Data Warehouse) in Azure Synapse Analytics erstellen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Erste Schritte

Verwenden Sie die folgenden Befehle, um sich bei Azure anzumelden und eine Ressourcengruppe einzurichten:

1. Führen Sie bei Verwendung einer lokalen Installation den Befehl [az login](/cli/azure/reference-index#az_login) aus, um sich bei Azure anzumelden:

   ```azurecli
   az login
   ```

1. Verwenden Sie bei Bedarf den Befehl [az account set](/cli/azure/account#az_account_set), um Ihr Abonnement auszuwählen:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Erstellen Sie mithilfe des Befehls [az sql server create](/cli/azure/sql/server#az_sql_server_create) einen [logischen SQL-Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json):

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Ein Server enthält eine Gruppe von Datenbanken, die als Gruppe verwaltet werden.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene

Erstellen Sie eine [Firewallregel auf Serverebene](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Eine Firewallregel auf Serverebene ermöglicht es einer externen Anwendung wie SQL Server Management Studio oder dem SQLCMD-Hilfsprogramm, über die Firewall des SQL-Pooldiensts eine Verbindung mit einem SQL-Pool herzustellen.

Führen Sie den Befehl [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) aus, um eine Firewallregel zu erstellen:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

In diesem Beispiel wird die Firewall nur für andere Azure-Ressourcen geöffnet. Ändern Sie die IP-Adresse in eine für Ihre Umgebung geeignete Adresse, um die externe Konnektivität zu ermöglichen. Verwenden Sie 0.0.0.0 als IP-Startadresse und 255.255.255.255 als Endadresse, wenn Sie alle IP-Adressen öffnen möchten.

> [!NOTE]
> SQL-Endpunkte kommunizieren über Port 1433. Wenn Sie versuchen, eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 1433 öffnet.
>

## <a name="create-and-manage-your-sql-pool"></a>Erstellen und Verwalten Ihres SQL-Pools

Erstellen Sie den SQL-Pool. In diesem Beispiel wird „DW100c“ als Dienstziel angegeben. Hierbei handelt es sich um einen kostengünstigeren Ausgangspunkt für Ihren SQL-Pool.

> [!NOTE]
> Sie benötigen einen zuvor erstellten Arbeitsbereich. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure Synapse-Arbeitsbereichs über die Azure CLI](../quickstart-create-workspace-cli.md).

Verwenden Sie den Befehl [az synapse sql pool create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create), um den SQL-Pool zu erstellen:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Weitere Informationen zu den Parameteroptionen finden Sie unter [az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool).

Ihre SQL-Pools können Sie mithilfe des Befehls [az synapse sql pool list](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list) anzeigen:

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Verwenden Sie den Befehl [az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update), um einen vorhandenen Pool zu aktualisieren:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Verwenden Sie den Befehl [az synapse sql pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause), um Ihren Pool anzuhalten:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Verwenden Sie den Befehl [az synapse sql pool resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume), um einen angehaltenen Pool zu starten:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Wenn Sie einen vorhandenen SQL-Pool entfernen möchten, verwenden Sie den Befehl [az synapse sql pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete):

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstarttutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf.

> [!TIP]
> Wenn Sie mit weiteren Schnellstart-Tutorials fortfahren möchten, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete), um alle in dieser Schnellstartanleitung erstellten Ressourcen zu löschen.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun einen SQL-Pool sowie eine Firewallregel erstellt und diese mit Ihrem SQL-Pool verbunden. Weitere Informationen finden Sie im Artikel zum [Laden von Daten in den SQL-Pool](load-data-from-azure-blob-storage-using-polybase.md).
