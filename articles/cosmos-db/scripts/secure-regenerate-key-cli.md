---
title: 'Azure CLI-Skript: Erneutes Generieren von Azure Cosmos DB-Kontoschlüsseln'
description: 'Azure CLI-Skriptbeispiel: erneutes Generieren von Azure Cosmos DB-Kontoschlüsseln'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/26/2018
ms.reviewer: sngun
ms.openlocfilehash: a8af5b7b035b67b5f09b1f16f26394f28941ec8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154645"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Erneutes Generieren von Azure Cosmos DB-Kontoschlüsseln mithilfe der Azure CLI

Dieses Beispiel generiert alle Arten von Azure Cosmos DB-Kontoschlüsseln erneut mithilfe der Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh "Regenerate Azure Cosmos DB account keys")]

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
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Aktualisiert ein Azure Cosmos DB-Konto. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) | Listet die Zugriffsschlüssel für ein Cosmos DB-Konto auf. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Generiert Azure Cosmos DB-Kontoschlüssel erneut. |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche CLI-Skriptbeispiele für Azure Cosmos DB finden Sie in der [Dokumentation zur Azure Cosmos DB-CLI](../cli-samples.md).
