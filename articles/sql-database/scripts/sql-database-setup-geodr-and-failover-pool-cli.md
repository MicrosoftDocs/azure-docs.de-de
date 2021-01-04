---
title: 'Azure CLI: Konfigurieren der aktiven Georeplikation für einen Pool für elastische Datenbanken'
description: Azure CLI-Beispielskript zum Einrichten der aktiven Georeplikation für eine Pooldatenbank in Azure SQL-Datenbank und Ausführen eines Failovers.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 7933273d02d3c1332d0d012ab6c22a59ed880640
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742656"
---
# <a name="use-cli-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>Verwenden der CLI zum Konfigurieren der aktiven Georeplikation für eine Pooldatenbank in Azure SQL-Datenbank

Dieses Azure CLI-Beispielskript konfiguriert die aktive Georeplikation für eine Pooldatenbank in Azure SQL-Datenbank und führt ein Failover zum sekundären Replikat der Datenbank aus.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Führen Sie das Skript aus.

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.sh "Set up active geo-replication for elastic pool")]

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle zugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name $resource
az group delete --name $secondaryResource
```

## <a name="sample-reference"></a>Beispielreferenz

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | BESCHREIBUNG |
|---|---|
| [az sql elastic-pool](/cli/azure/sql/elastic-pool) | Befehle für Pools für elastische Datenbanken |
| [az sql db replica](/cli/azure/sql/db/replica) | Befehle für die Datenbankreplikation |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../../azure-sql/database/az-cli-script-samples-content-guide.md).
