---
title: 'CLI-Skript: Skalieren eines Servers – Azure Database for MySQL'
description: Dieses CLI-Beispielskript skaliert einen Azure-Datenbank für MySQL-Server nach Abfragen der Metriken auf eine andere Leistungsstufe.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 31cf6d1f3bfcc44887ec939cb2f27df77f12cea3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539259"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Überwachen und Skalieren eines Azure-Datenbank für MySQL-Servers mithilfe der Azure CLI
Dieses CLI-Beispielskript skaliert Compute- und Speicherressourcen für einen einzelnen Azure Database for MySQL-Server nach Abfragen der Metriken. Computeressourcen können hoch- oder herunterskaliert werden. Speicherressourcen können nur hochskaliert werden.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript
Aktualisieren Sie das Skript mit Ihrer Abonnement-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Erstellt einen MySQL-Server, der die Datenbanken hostet. |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | Aktualisiert die Eigenschaften des MySQL-Servers. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listet den Metrikwert für die Ressourcen auf. |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich ausführlicher über [Compute- und Speicherressourcen in Azure Database for MySQL](../concepts-pricing-tiers.md).
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure-Datenbank für MySQL](../sample-scripts-azure-cli.md)
- Lesen Sie weitere Informationen zur [Azure-Befehlszeilenschnittstelle](/cli/azure).
