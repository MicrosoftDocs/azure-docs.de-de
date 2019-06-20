---
title: 'Azure CLI-Skript: Erstellen einer Azure Database for MariaDB-Instanz'
description: Dieses CLI-Beispielskript erstellt einen Azure Database for MariaDB-Server und konfiguriert eine Firewallregel auf Serverebene.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: a7e8a4c8f8572cfdd236b1752e89b3a1b4e7b758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171368"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Erstellen eines MariaDB-Servers und Konfigurieren einer Firewallregel mit der Azure CLI
Dieses CLI-Beispielskript erstellt einen Azure Database for MariaDB-Server und konfiguriert eine Firewallregel auf Serverebene. Nach der erfolgreichen Ausführung des Skripts ist der MariaDB-Server für alle Azure-Dienste und die konfigurierte IP-Adresse erreichbar.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittelle lokal ausführen, ist für diesen Artikel Azure CLI Version 2.0 oder höher erforderlich. Überprüfen Sie die Version, indem Sie `az --version` ausführen. Informationen zum Installieren oder Aktualisieren Ihrer Version der Azure CLI finden Sie unter [Installieren von Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript
Bearbeiten Sie in diesem Beispielskript die hervorgehobenen Zeilen, um den Benutzernamen und das Kennwort des Administrators zu aktualisieren und auf Ihre eigenen Daten festzulegen.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung
Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen, nachdem das Skript ausgeführt wurde.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Erläuterung des Skripts
In diesem Skript werden die Befehle verwendet, die in der folgenden Tabelle aufgeführt sind:

| **Befehl** | **Hinweise** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Erstellt einen MariaDB-Server, der die Datenbanken hostet. |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | Erstellt eine Firewallregel, die vom eingegebenen IP-Adressbereich aus den Zugriff auf alle Server und darauf befindlichen Datenbanken ermöglicht. |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure CLI: [Azure CLI-Dokumentation](/cli/azure)
- Probieren Sie weitere Skripts aus: [Azure CLI-Beispiele für Azure Database for MariaDB](../sample-scripts-azure-cli.md)
