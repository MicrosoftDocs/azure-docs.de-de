---
title: 'Azure CLI: Überwachen und Skalieren einer Einzeldatenbank in Azure SQL­-Datenbank'
description: Verwenden Sie ein Azure CLI-Beispielskript zum Überwachen und Skalieren einer einzelnen Datenbank in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: 0a72bea08052e5cd17502a21a98b41cb5582a3b9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493626"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Verwenden von Azure CLI zum Überwachen und Skalieren einer einzelnen Datenbank in Azure SQL-Datenbank

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Dieses Azure CLI-Skriptbeispiel skaliert eine einzelne Datenbank in Azure SQL-Datenbank nach Abfrage der Größeninformationen für die Datenbank auf eine andere Computegröße.

Wenn Sie die Azure-Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Führen Sie das Skript aus.

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Verwenden Sie [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list), um eine Liste von in der Datenbank ausgeführten Vorgängen zu erhalten, und verwenden Sie [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel), um einen Aktualisierungsvorgang in der Datenbank abzubrechen.

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle zugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Beispielreferenz

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Skript | BESCHREIBUNG |
|---|---|
| [az sql server](/cli/azure/sql/server) | Serverbefehle |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Zeigt die Größennutzungsinformationen für eine Datenbank an. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere CLI-Skriptbeispiele finden Sie unter [Azure CLI Beispielskripts](../az-cli-script-samples-content-guide.md).
