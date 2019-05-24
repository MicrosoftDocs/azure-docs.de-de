---
title: 'CLI-Beispiel: Verschieben von Azure SQL-Datenbank – Pool für elastische SQL-Datenbanken | Microsoft-Dokumentation'
description: Azure CLI-Beispielskript zum Verschieben einer SQL-Datenbank in einem Pool für elastische SQL-Datenbanken
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 640212b0ae3c9d5658b79652f394a1ad8b92e218
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834515"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Verwenden Sie zum Verschieben einer Azure SQL-Datenbank in einem Pool für elastische SQL-Datenbanken eine Befehlszeilenschnittstelle.

Dieses Azure CLI-Beispielskript erstellt zwei Pools für elastische Datenbanken, verschiebt eine Azure SQL-Datenbank aus einem Pool für elastische SQL-Datenbanken in einen anderen und verschiebt die Datenbank dann aus dem Pool für elastische Datenbanken in eine Einzeldatenbank-Computegröße.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

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
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | Erstellt einen Pool für elastische Datenbanken. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | Erstellt eine Einzeldatenbank oder eine Datenbank in einem Pool für elastische Datenbanken. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Aktualisiert Datenbankeigenschaften oder verschiebt eine Datenbank in Pools für elastische Datenbanken, daraus hinaus oder zwischen ihnen. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../sql-database-cli-samples.md).