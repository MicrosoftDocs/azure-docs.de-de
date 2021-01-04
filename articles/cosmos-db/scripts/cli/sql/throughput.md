---
title: 'Azure CLI-Skripts für Durchsatzvorgänge (RU/s): Ressourcen der Core-API (SQL) von Azure Cosmos DB'
description: 'Azure CLI-Skripts für Durchsatzvorgänge (RU/s): Ressourcen der Core-API (SQL) von Azure Cosmos DB'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 8310de5ce8fd3f90e422555a5111569fadcca982
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566389"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Durchsatzvorgänge (RU/s) mit der Azure CLI für eine Datenbank oder einen Container für die Core-API (SQL) von Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.12.1 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

Mit diesem Skript werden eine Datenbank mit freigegebenem Durchsatz sowie ein Container mit dediziertem Durchsatz für die Core-API (SQL) erstellt, und dann wird der Durchsatz für die Datenbank und die Sammlung aktualisiert. Anschließend führt das Skript die Migration vom standardmäßigen zum automatisch skalierten Durchsatz durch, und nach Abschluss der Migration wird dann der Wert des automatisch skalierten Durchsatzes gelesen.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Erstellt ein Konto für Azure Cosmos DB. |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Erstellt eine Azure Cosmos-Core-Datenbank (SQL). |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Erstellt einen Azure Cosmos-Core-Container (SQL). |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Aktualisiert den Durchsatz für eine Azure Cosmos-Core-Datenbank (SQL). |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Aktualisiert den Durchsatz für einen Azure Cosmos-Core-Container (SQL). |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Migriert den Durchsatz für eine Azure Cosmos-Core-Datenbank (SQL). |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Migriert den Durchsatz für einen Azure Cosmos-Core-Container (SQL). |
| [az group delete](/cli/azure/resource#az-resource-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Cosmos DB-CLI finden Sie in der [Dokumentation zur Azure Cosmos DB-CLI](/cli/azure/cosmosdb).

Alle CLI-Skriptbeispiele für Azure Cosmos DB finden Sie im [GitHub-Repository zur Azure Cosmos DB-CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
