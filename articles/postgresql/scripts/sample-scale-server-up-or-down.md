---
title: Azure CLI-Skript – Skalieren einer Azure Database for PostgreSQL-Instanz
description: 'Azure CLI-Skriptbeispiel: Skalieren eines Azure-Datenbank für PostgreSQL-Servers auf eine andere Leistungsstufe nach Abfragen der Metriken.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: 65958eb1e2d7cc4492e0437b24db77f00ebc7eba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154525"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Überwachen und Skalieren eines einzelnen PostgreSQL-Servers mit der Azure CLI
Dieses CLI-Beispielskript skaliert einen einzelnen Azure-Datenbank für PostgreSQL-Server nach Abfragen der Metriken auf eine andere Leistungsstufe. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittelle lokal ausführen, ist für diesen Artikel Azure CLI Version 2.0 oder höher erforderlich. Überprüfen Sie die Version, indem Sie `az --version` ausführen. Informationen zum Installieren oder Aktualisieren Ihrer Version der Azure CLI finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen. Ersetzen Sie die Abonnement-ID, die in den `az monitor`-Befehlen verwendet wird, durch Ihre eigene Abonnement-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az postgres server create](/cli/azure/postgres/server) | Erstellt einen PostgreSQL-Server, der die Datenbanken hostet. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Listet den Metrikwert für die Ressourcen auf. |
| [az group delete](/cli/azure/group) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure)
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure-Datenbank für PostgreSQL](../sample-scripts-azure-cli.md)
- Weitere Informationen zur Skalierung: [Tarife](../concepts-service-tiers.md) und [Compute-Einheiten und Speichereinheiten](../concepts-compute-unit-and-storage.md)
