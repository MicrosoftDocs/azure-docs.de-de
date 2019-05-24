---
title: 'Azure CLI-Skript: Erstellen eines Cassandra-API-Kontos, einer Datenbank und einer Tabelle für Azure Cosmos DB'
description: 'Azure CLI-Skriptbeispiel: Erstellen eines Cassandra-API-Kontos, einer Datenbank und einer Tabelle für Azure Cosmos DB'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 89d43d8fc956d747de885f69017ed79f49670858
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154753"
---
# <a name="azure-cosmos-db-create-a-cassandra-api-account-using-azure-cli"></a>Azure Cosmos DB: Erstellen eines Cassandra-API-Kontos mithilfe der Azure CLI

Mit diesem CLI-Beispielskript erstellen Sie ein Cassandra-API-Konto, eine Datenbank und eine Tabelle für Azure Cosmos DB.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-cassandra-account/create-cosmosdb-cassandra-account.sh "Create an Azure Cosmos DB Cassandra API account, database, and table.")]

> [!Note]
> Die Azure CLI unterstützt nicht direkt die Erstellung einer Cassandra-Tabelle. Verwenden Sie das Azure-Portal oder CQL Shell.

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Erstellt eine Azure Cosmos DB-Datenbank. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche CLI-Skriptbeispiele für Azure Cosmos DB finden Sie in der [Dokumentation zur Azure Cosmos DB-CLI](../cli-samples.md).
