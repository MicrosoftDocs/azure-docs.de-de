---
title: Azure CLI-Skript – Ändern von Serverkonfigurationen
description: Dieses CLI-Beispielskript listet alle verfügbaren Serverkonfigurationsoptionen auf und aktualisiert den Wert von einer der Optionen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 6b5a855c8db5cb87f313e14c42396ae70b407e61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66122089"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Auflisten und Aktualisieren der Konfigurationen eines Azure Database for PostgreSQL-Servers mithilfe der Azure CLI
Mit diesem CLI-Beispielskript werden alle verfügbaren Konfigurationsparameter sowie ihre zulässigen Werte für den Azure Database for PostgreSQL-Server aufgeführt. Zudem wird für *log_retention_days* ein anderer Wert als der Standardwert festgelegt.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittelle lokal ausführen, ist für diesen Artikel Azure CLI Version 2.0 oder höher erforderlich. Überprüfen Sie die Version, indem Sie `az --version` ausführen. Informationen zum Installieren oder Aktualisieren Ihrer Version der Azure CLI finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az postgres server create](/cli/azure/postgres/server) | Erstellt einen PostgreSQL-Server, der die Datenbanken hostet. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Listet die Konfigurationen eines Azure Database for PostgreSQL-Servers auf. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Aktualisiert die Konfiguration eines Azure Database for PostgreSQL-Servers. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Zeigt die Konfiguration eines Azure Database for PostgreSQL-Servers an. |
| [az group delete](/cli/azure/group) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure)
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure-Datenbank für PostgreSQL](../sample-scripts-azure-cli.md)
- Weitere Informationen zu Serverparametern finden Sie unter [Konfigurieren von Serverparametern in Azure Database for MySQL mit dem Azure-Portal](../howto-configure-server-parameters-using-portal.md).
