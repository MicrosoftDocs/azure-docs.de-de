---
title: PowerShell-Beispiel zum Wiederherstellen einer Azure SQL-Datenbank aus einer Sicherung | Microsoft-Dokumentation
description: Azure PowerShell-Beispielskript zum Wiederherstellen einer Azure SQL-Einzeldatenbank in einem früheren Zustand auf der Grundlage automatischer Sicherungen
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/27/2019
ms.openlocfilehash: 0ec4aa6e4beb1455c1d9a284569057b44f9386fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66170223"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-to-an-earlier-point-in-time"></a>Wiederherstellen einer Azure SQL-Einzeldatenbank in einem früheren Zustand mithilfe von PowerShell

Dieses PowerShell-Beispielskript stellt einen bestimmten früheren Zustand einer Azure SQL-Datenbank wieder her.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen SQL-Datenbank-Server, der eine Einzeldatenbank oder einen Pool für elastische Datenbanken hostet. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine Datenbank auf einem SQL-Datenbankserver als eigenständige Datenbank oder Pooldatenbank. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Ruft eine georedundante Sicherung einer eigenständigen Datenbank oder Pooldatenbank ab. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Stellt eine eigenständige SQL-Datenbank oder eine SQL-Pooldatenbank wieder her. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Entfernt eine eigenständige Azure SQL-Datenbank oder eine Azure SQL-Pooldatenbank. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ruft eine gelöschte eigenständige Datenbank oder Pooldatenbank ab, die Sie wiederherstellen können. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
