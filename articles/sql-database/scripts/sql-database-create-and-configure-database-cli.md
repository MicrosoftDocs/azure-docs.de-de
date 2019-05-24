---
title: 'CLI-Beispiel: Erstellen einer Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Verwenden Sie dieses Azure CLI-Beispielskript zum Erstellen einer SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
manager: craigg
ms.author: sstein
ms.reviewer: ''
ms.date: 02/08/2019
ms.openlocfilehash: 4c02ab45cd6e10b86681aae41cbb4bd99e6e0d6d
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832568"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Verwenden der CLI zum Erstellen einer einzelnen Azure SQL-Datenbank und Konfigurieren einer Firewallregel

In diesem CLI-Skriptbeispiel wird eine einzelne Azure SQL-Datenbank erstellt und eine Firewallregel auf Serverebene konfiguriert. Nach erfolgreicher Ausführung des Skripts können alle Azure-Dienste und die konfigurierte IP-Adresse auf die SQL-Datenbank zugreifen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Erstellt einen SQL-Datenbank-Server, der eine Einzeldatenbank oder einen Pool für elastische Datenbanken hostet. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Erstellt eine Firewallregel, die den Zugriff auf alle Einzeldatenbanken und Pools für elastische Datenbanken auf dem SQL-Datenbank-Server über den eingegebenen IP-Adressbereich zulässt. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create) | Erstellt eine Einzeldatenbank oder einen Pool für elastische Datenbanken. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../sql-database-cli-samples.md).