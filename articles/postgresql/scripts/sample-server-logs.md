---
title: Azure CLI-Skript – Herunterladen von Serverprotokollen in Azure Database for PostgreSQL
description: Dieses Beispielskript für die Azure-Befehlszeilenschnittstelle veranschaulicht das Aktivieren und Herunterladen der Serverprotokolle eines Azure Database for PostgreSQL-Servers.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: d6ba14a8838d71397a8c2348a03b1d760b3cd739
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660439"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Aktivieren und Herunterladen der Serverprotokolle zu langsamen Abfragen von einem Azure Database for PostgreSQL-Server mithilfe der Azure-Befehlszeilenschnittstelle
Dieses Beispielskript für die Befehlszeilenschnittstelle aktiviert die Serverprotokolle zu langsamen Abfragen auf einem Azure Database for PostgreSQL-Server und lädt sie herunter.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen. Ersetzen Sie &lt;log_file_name&gt; in den `az monitor`-Befehlen durch den Dateinamen Ihres eigenen Serverprotokolls.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az postgres server create](/cli/azure/postgres/server) | Erstellt einen PostgreSQL-Server, der die Datenbanken hostet. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Listet die Konfigurationswerte für einen Server auf |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Aktualisiert die Konfiguration eines Servers |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Listet die Protokolldateien für einen Server auf. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Lädt Protokolldateien herunter |
| [az group delete](/cli/azure/group) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure).
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure-Datenbank für PostgreSQL](../sample-scripts-azure-cli.md)
- [Konfigurieren der und Zugreifen auf die Serverprotokolle im Azure-Portal](../howto-configure-server-logs-in-portal.md)
