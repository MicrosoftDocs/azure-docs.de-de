---
title: 'Azure CLI-Skripts für Durchsatzvorgänge (RU/s): Ressourcen der Tabellen-API für Azure Cosmos DB'
description: 'Azure CLI-Skripts für Durchsatzvorgänge (RU/s): Ressourcen der Tabellen-API für Azure Cosmos DB'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 6e76da71cb14122817090e64354babf5a618db8b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562660"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Durchsatzvorgänge (RU/s) mit der Azure CLI für eine Tabelle der Azure Cosmos DB-Tabellen-API
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.12.1 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

Dieses Skript erstellt eine Tabellen-API-Tabelle und aktualisiert dann den Durchsatz der Tabelle. Anschließend führt das Skript die Migration vom standardmäßigen zum automatisch skalierten Durchsatz durch, und nach Abschluss der Migration wird dann der Wert des automatisch skalierten Durchsatzes gelesen.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Throughput operations for Table API.")]

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
| [az cosmosdb table create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Erstellt eine Azure Cosmos-Tabellen-API-Tabelle. |
| [az cosmosdb table throughput update](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Aktualisiert den Durchsatz für eine Tabelle der Azure Cosmos-Tabellen-API. |
| [az cosmosdb table throughput migrate](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-migrate) | Migriert den Durchsatz für eine Tabelle der Azure Cosmos-Tabellen-API. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Cosmos DB-CLI finden Sie in der [Dokumentation zur Azure Cosmos DB-CLI](/cli/azure/cosmosdb).

Alle CLI-Skriptbeispiele für Azure Cosmos DB finden Sie im [GitHub-Repository zur Azure Cosmos DB-CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
