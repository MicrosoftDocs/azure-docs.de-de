---
title: 'Azure CLI-Skript: Skalieren und Überwachen einer Azure Database for PostgreSQL-Instanz'
description: 'Azure CLI-Skriptbeispiel: Skalieren eines Azure-Datenbank für PostgreSQL-Servers auf eine andere Leistungsstufe nach Abfragen der Metriken.'
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 6bbf5f3a0a7d32425f80687de10444ee0819b9df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660456"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Überwachen und Skalieren eines einzelnen PostgreSQL-Servers mit der Azure CLI
Dieses CLI-Beispielskript skaliert Compute- und Speicherressourcen für einen einzelnen Azure Database for PostgreSQL-Server nach Abfragen der Metriken. Computeressourcen können hoch- oder herunterskaliert werden. Speicherressourcen können nur hochskaliert werden. 

> [!IMPORTANT] 
> Der Speicher kann nur zentral hochskaliert und nicht herunterskaliert werden.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript
Aktualisieren Sie das Skript mit Ihrer Abonnement-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Erstellt einen PostgreSQL-Server, der die Datenbanken hostet. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Aktualisiert die Eigenschaften des PostgreSQL-Servers. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Listet den Metrikwert für die Ressourcen auf. |
| [az group delete](/cli/azure/group) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich ausführlicher über [Compute- und Speicherressourcen in Azure Database for PostgreSQL](../concepts-pricing-tiers.md).
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure-Datenbank für PostgreSQL](../sample-scripts-azure-cli.md)
- Lesen Sie weitere Informationen zur [Azure-Befehlszeilenschnittstelle](/cli/azure).
