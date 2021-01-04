---
title: Verwenden von Kontoschlüsseln und Verbindungszeichenfolgen für ein Azure Cosmos-Konto
description: Verwenden von Kontoschlüsseln und Verbindungszeichenfolgen für ein Azure Cosmos-Konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: af5f92168e6bcc23345e3a11727eefe76615422c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563255"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Verwenden von Kontoschlüsseln und Verbindungszeichenfolgen für ein Azure Cosmos-Konto mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.9.1 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

Dieses Skript veranschaulicht vier Vorgänge.

- Auflisten aller Kontoschlüssel
- Auflisten der schreibgeschützten Kontoschlüssel
- Auflisten von Verbindungszeichenfolgen
- Erneutes Generieren von Kontoschlüsseln

> [!NOTE]
> In diesem Beispiel wird die Verwendung eines SQL-API-Kontos (Core-API) veranschaulicht, die Vorgänge für Kontoschlüssel und Verbindungszeichenfolgen sind jedoch bei allen Datenbank-APIs in Cosmos DB gleich.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

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
| [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) | Listet die Schlüssel für ein Azure Cosmos DB-Konto auf. |
| [az cosmosdb list-read-only-keys](/cli/azure/cosmosdb#az-cosmosdb-list-read-only-keys) | Listet die schreibgeschützten Schlüssel für ein Azure Cosmos DB-Konto auf. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Listet die Verbindungszeichenfolgen für ein Azure Cosmos DB-Konto auf. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Generiert die Schlüssel für ein Azure Cosmos DB-Konto erneut. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure Cosmos DB-CLI finden Sie in der [Dokumentation zur Azure Cosmos DB-CLI](/cli/azure/cosmosdb).

Alle CLI-Skriptbeispiele für Azure Cosmos DB finden Sie im [GitHub-Repository zur Azure Cosmos DB-CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
