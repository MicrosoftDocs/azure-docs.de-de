---
title: 'Azure CLI-Skript: Skalieren eines Azure Database for MariaDB-Servers'
description: Dieses CLI-Beispielskript skaliert einen Azure Database for MariaDB-Server nach Abfragen der Metriken auf eine andere Leistungsstufe.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: ef01c38dddee0a74477ef331f935bda2787866df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171326"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Überwachen und Skalieren eines Azure Database for MariaDB-Servers mithilfe der Azure CLI
Dieses CLI-Beispielskript skaliert einen einzelnen Azure Database for MariaDB-Server nach Abfragen der Metriken auf eine andere Leistungsstufe.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittelle lokal ausführen, ist für diesen Artikel Azure CLI Version 2.0 oder höher erforderlich. Überprüfen Sie die Version, indem Sie `az --version` ausführen. Informationen zum Installieren oder Aktualisieren Ihrer Version der Azure CLI finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen. Ersetzen Sie die Abonnement-ID, die in den `az monitor`-Befehlen verwendet wird, durch Ihre eigene Abonnement-ID. [!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Erstellt einen MariaDB-Server, der die Datenbanken hostet. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Listet den Metrikwert für die Ressourcen auf. |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure)
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Weitere Informationen zur Skalierung finden Sie unter [Tarife](../concepts-pricing-tiers.md.
