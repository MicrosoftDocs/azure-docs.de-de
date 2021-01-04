---
title: 'PowerShell-Skripts für Durchsatzvorgänge (RU/s): Azure Cosmos DB-Tabellen-API'
description: 'PowerShell-Skripts für Durchsatzvorgänge (RU/s): Azure Cosmos DB-Tabellen-API'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 60d55745f22b2babe29eb3fc8b8840810b9bcddb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094760"
---
# <a name="throughput-rus-operations-with-powershell-for-a-table-for-azure-cosmos-db---table-api"></a>Durchsatzvorgänge (RU/s) mit PowerShell für eine Tabelle für Azure Cosmos DB: Tabellen-API
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Durchsatzberechnung

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-get.ps1 "Get throughput on a table for Table API")]

## <a name="update-throughput"></a>Aktualisieren des Durchsatzes

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-update.ps1 "Update throughput on a table for Table API")]

## <a name="migrate-throughput"></a>Migrieren des Durchsatzes

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a table for Table API")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbtablethroughput) | Ruft den Durchsatzwert der angegebenen Tabellen-API-Tabelle ab. |
| [Update-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbsqldatabasethroughput) | Aktualisiert den Durchsatzwert der Tabellen-API-Tabelle. |
| [Invoke-AzCosmosDBTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbtablethroughputmigration) | Migriert den Durchsatz einer Tabelle der Tabellen-API. |
|**Azure-Ressourcengruppen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).