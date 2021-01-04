---
title: 'Azure CLI: Hinzufügen einer Datenbank zu einer Failovergruppe'
description: Verwenden Sie das Azure CLI-Beispielskript, um eine Datenbank in Azure SQL-Datenbank zu erstellen, sie zu einer Autofailover-Gruppe hinzuzufügen und das Failover zu testen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: f3a314f55d45a888dde08ddc275953e7f9bbf3b2
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594146"
---
# <a name="use-the-azure-cli-to-add-a-database-to-a-failover-group"></a>Verwenden der Azure CLI zum Hinzufügen einer Datenbank zu einer Failovergruppe

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

In diesem Azure CLI-Skriptbeispiel wird eine Datenbank in Azure SQL-Datenbank erstellt, eine Failovergruppe erstellt, die Datenbank zur Failovergruppe hinzugefügt und das Failover getestet.

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Führen Sie das Skript aus.

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add Azure SQL Database to failover group")]

### <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle zugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Beispielreferenz

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | BESCHREIBUNG |
|---|---|
| [az sql db](/cli/azure/sql/db) | Datenbankbefehle |
| [az sql failover-group](/cli/azure/sql/failover-group) | Befehle für Failovergruppen |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../az-cli-script-samples-content-guide.md).
