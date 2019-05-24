---
title: PowerShell-Beispiel – Failovergruppe bei der Georeplikation – einzelne Azure SQL-Datenbank | Microsoft-Dokumentation
description: Azure PowerShell-Beispielskript zum Einrichten einer Failovergruppe für die aktive Georeplikation für eine Einzeldatenbank in Azure SQL-Datenbank und Ausführen eines Failovers.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: f049b61df4ad86fedea81c2e36c1690b8b7eb884
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115959"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-database-in-azure-sql-database"></a>Verwenden von PowerShell zum Konfigurieren einer Failovergruppe für die aktive Georeplikation für eine Einzeldatenbank in Azure SQL-Datenbank

Dieses PowerShell-Beispielskript konfiguriert eine Failovergruppe für die aktive Georeplikation für eine Einzeldatenbank und führt ein Failover zu einem sekundären Replikat der Datenbank aus.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-scripts"></a>Beispielskripts

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=18-21 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen SQL-Datenbank-Server, der Einzeldatenbanken und Pools für elastische Datenbanken hostet. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Erstellt einen Pool für elastische Datenbanken. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aktualisiert Datenbankeigenschaften oder verschiebt eine Datenbank in Pools für elastische Datenbanken, daraus hinaus oder zwischen ihnen. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Erstellt eine sekundäre Datenbank für eine vorhandene Datenbank und startet die Datenreplikation. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Ruft mindestens eine Datenbank ab. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Erklärt die sekundäre Datenbank zur primären und wechselt zu ihr – dadurch wird das Failover gestartet.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Ruft die Georeplikationsverknüpfungen zwischen einer Azure SQL-Datenbank und einer Ressourcengruppe oder einer SQL Server-Instanz ab. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Beendet die Datenreplikation zwischen einer SQL-Datenbank und der angegebenen sekundären Datenbank. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Erstellt eine neue Azure SQL-Datenbank-Failovergruppe für die angegebenen Server. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Vertauscht die Rollen der Server in der Failovergruppe und legt alle sekundären Datenbanken auf die primäre Rolle fest. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft eine bestimmte Azure SQL-Datenbank-Failovergruppe ab oder listet die Failovergruppen auf einem Server auf. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
