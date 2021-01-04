---
title: 'CLI-Skript: Ändern von Serverparametern – Azure Database for MariaDB'
description: Dieses CLI-Beispielskript listet alle verfügbaren Serverkonfigurationen und -updates einer Azure Database for MariaDB-Instanz auf.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: c7a46f98f74648ccae9f9f9f94c218d42056decb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536805"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Auflisten und Aktualisieren der Konfigurationen eines Azure Database for MariaDB-Servers mithilfe der Azure CLI
Mit diesem CLI-Beispielskript werden alle verfügbaren Konfigurationsparameter sowie ihre zulässigen Werte für den Azure Database for MariaDB-Server aufgeführt. Zudem wird für *innodb_lock_wait_timeout* ein anderer Wert als der Standardwert festgelegt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Erstellt einen MariaDB-Server, der die Datenbanken hostet. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Listet die Konfigurationen eines Azure Database for MariaDB-Servers auf. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aktualisiert die Konfigurationen eines Azure Database for MariaDB-Servers. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Zeigt die Konfigurationen eines Azure Database for MariaDB-Servers an. |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure).
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Weitere Informationen zu Serverparametern finden Sie unter [Konfigurieren von Serverparametern in Azure Database for MariaDB über das Azure-Portal](../howto-server-parameters.md).
