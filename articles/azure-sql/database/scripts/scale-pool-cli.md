---
title: 'Azure CLI: Skalieren eines Pools für elastische Datenbanken'
description: Verwenden Sie ein Azure CLI-Beispielskript zum Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 30d462dec8c65252afbde0c4d2eee171891a64ed
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747313"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Verwenden der Azure CLI zum Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

In diesem Azure CLI-Skriptbeispiel werden Pools für elastische Datenbanken in Azure SQL-Datenbank erstellt, Pooldatenbanken verschoben und die Computegrößen für Pools für elastische Datenbanken geändert.

Wenn Sie die Azure CLI lokal installieren und verwenden möchten, ist es für dieses Thema erforderlich, die Azure CLI-Version 2.0 oder höher auszuführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Führen Sie das Skript aus.

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Beispielreferenz

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | BESCHREIBUNG |
|---|---|
| [az sql server](/cli/azure/sql/server) | Serverbefehle |
| [az sql db](/cli/azure/sql/db) | Datenbankbefehle |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | Befehle für Pools für elastische Datenbanken |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../az-cli-script-samples-content-guide.md).
