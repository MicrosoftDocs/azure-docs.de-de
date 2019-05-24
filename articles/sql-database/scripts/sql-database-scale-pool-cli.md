---
title: CLI-Beispielskript zum Skalieren eines Pools für elastische SQL-Datenbanken in Azure SQL-Datenbank | Microsoft-Dokumentation
description: Azure CLI-Beispielskript zum Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 7e4c24c538bd72df6c3324ca7f8837ce481e6ea2
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833953"
---
# <a name="use-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Verwenden der Befehlszeilenschnittstelle zum Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank

Dieses Azure CLI-Skriptbeispiel erstellt Pools für elastische Datenbanken, verschiebt Pooldatenbanken und ändert die Computegrößen für Pools für elastische Datenbanken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um eine Ressourcengruppe, einen SQL-Datenbank-Server, eine Einzeldatenbank und SQL-Datenbank-Firewallregeln zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Erstellt einen SQL-Datenbank-Server, der Einzeldatenbanken und Pools für elastische Datenbanken hostet. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | Erstellt einen Pool für elastische Datenbanken. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | Erstellt eine Einzel- oder Pooldatenbank. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) | Aktualisiert einen Pool für elastische Datenbanken. In diesem Beispiel wird die zugewiesene eDTU (Transaktionseinheit in elastischer Datenbank) geändert. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche SQL-Datenbank-CLI-Skriptbeispiele finden Sie in der [Azure SQL-Datenbank-Dokumentation](../sql-database-cli-samples.md).
