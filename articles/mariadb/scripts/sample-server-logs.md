---
title: 'Azure CLI-Skript: Herunterladen von Serverprotokollen in Azure Database for MariaDB'
description: Dieses Beispielskript für die Azure-Befehlszeilenschnittstelle veranschaulicht das Aktivieren und Herunterladen der Serverprotokolle eines Azure Database for MariaDB-Servers.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: da2a8ed610873d957866117a9ef7aab424edd452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171343"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Aktivieren und Herunterladen der Serverprotokolle zu langsamen Abfragen von einem Azure Database for MariaDB-Server mithilfe der Azure-Befehlszeilenschnittstelle
Dieses CLI-Beispielskript aktiviert die Serverprotokolle zu langsamen Abfragen auf einem Azure Database for MariaDB-Server und lädt sie herunter.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittelle lokal ausführen, ist für diesen Artikel Azure CLI Version 2.0 oder höher erforderlich. Überprüfen Sie die Version, indem Sie `az --version` ausführen. Informationen zum Installieren oder Aktualisieren Ihrer Version der Azure CLI finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen. Ersetzen Sie &lt;log_file_name&gt; in den `az monitor`-Befehlen durch den Dateinamen Ihres eigenen Serverprotokolls.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Erstellt einen MariaDB-Server, der die Datenbanken hostet. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Listet die Konfigurationswerte für einen Server auf |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aktualisiert die Konfiguration eines Servers |
| [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Listet die Protokolldateien für einen Server auf. |
| [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Lädt Protokolldateien herunter |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure)
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MariaDB](../sample-scripts-azure-cli.md)
