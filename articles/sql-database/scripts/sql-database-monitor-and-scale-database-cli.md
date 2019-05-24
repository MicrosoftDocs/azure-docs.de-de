---
title: 'CLI-Beispiel: Überwachen und Skalieren einer einzelnen Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Azure CLI-Beispielskript zum Überwachen und Skalieren einer einzelnen Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: eeb932123e57dcb10fce3baf9670d8c1ae961fb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151669"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Überwachen und Skalieren einer einzelnen SQL­-Datenbank über die Befehlszeilenschnittstelle

Dieses Azure CLI-Skriptbeispiel skaliert eine einzelne Azure SQL-Datenbank nach Abfrage der Größeninformationen für die Datenbank auf eine andere Computegröße.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Artikel die Azure CLI-Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Verwenden Sie [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list), um eine Liste von in der Datenbank ausgeführten Vorgängen zu erhalten, und verwenden Sie [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel), um einen Aktualisierungsvorgang in der Datenbank abzubrechen.

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Erstellt einen SQL-Datenbank-Server, der Einzeldatenbanken und Pools für elastische Datenbanken hostet. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql#az-sql-show-usage) | Zeigt die Größennutzungsinformationen für eine Einzel- oder Pooldatenbank an. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Aktualisiert Datenbankeigenschaften (z.B. Dienstebene oder Computegröße) oder verschiebt eine Datenbank in, aus oder zwischen Pools für elastische Datenbanken. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../sql-database-cli-samples.md).
