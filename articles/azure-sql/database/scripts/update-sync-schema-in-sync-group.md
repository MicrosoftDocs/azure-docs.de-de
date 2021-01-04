---
title: 'Mit PowerShell: Aktualisieren des Synchronisierungsschemas von SQL-Datensynchronisierung'
description: Azure PowerShell-Beispielskript zum Aktualisieren des Synchronisierungsschemas für die SQL-Datensynchronisierung
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792717"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

In diesem Azure PowerShell-Beispiel wird das Synchronisierungsschema in einer vorhandenen Synchronisierungsgruppe der SQL-Datensynchronisierung aktualisiert. Wenn Sie mehrere Tabellen synchronisieren, hilft Ihnen dieses Skript beim effizienten Aktualisieren des Synchronisierungsschemas. Dieses Beispiel veranschaulicht die Verwendung des Skripts **UpdateSyncSchema** , das auf GitHub als [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) verfügbar ist.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Eine Übersicht über die SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL-Datensynchronisierung unterstützt derzeit keine verwalteten Azure SQL-Instanzen.

## <a name="examples"></a>Beispiele

### <a name="add-all-tables-to-the-sync-schema"></a>Hinzufügen aller Tabellen zum Synchronisierungsschema

Im folgenden Beispiel wird das Datenbankschema aktualisiert, und dem Synchronisierungsschema werden alle gültigen Tabellen in der Hub-Datenbank hinzugefügt.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Hinzufügen und Entfernen von Tabellen und Spalten

Im folgenden Beispiel werden dem Synchronisierungsschema `[dbo].[Table1]` und `[dbo].[Table2].[Column1]` hinzugefügt und `[dbo].[Table3]` entfernt.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Skriptparameter

Das Skript **UpdateSyncSchema** weist die folgenden Parameter auf:

| Parameter | Notizen |
|---|---|
| $subscriptionId | Das Abonnement, in dem die Synchronisierungsgruppe erstellt wird |
| $resourceGroupName | Die Ressourcengruppe, in der die Synchronisierungsgruppe erstellt wird|
| $serverName | Der Servername der Hub-Datenbank|
| $databaseName | Der Name der Hub-Datenbank |
| $syncGroupName | Der Name der Synchronisierungsgruppe |
| $memberName | Geben Sie den Namen des Elements an, wenn Sie das Datenbankschema aus dem Synchronisierungselement und nicht aus der Hub-Datenbank laden möchten. Wenn Sie das Datenbankschema vom Hub laden möchten, lassen Sie diesen Parameter leer. |
| $timeoutInSeconds | Das Timeout, nach dem das Skript das Datenbankschema aktualisiert. Der Standardwert ist 900 Sekunden. |
| $refreshDatabaseSchema | Geben Sie an, ob das Skript das Datenbankschema aktualisieren muss. Wenn das Datenbankschema gegenüber der früheren Konfiguration geändert wurde (z. B. wenn Sie eine neue Tabelle oder Spalte hinzugefügt haben), müssen Sie das Schema aktualisieren, bevor Sie es neu konfigurieren. Der Standardwert ist "false". |
| $addAllTables | Wenn dieser Wert TRUE ist, werden dem Synchronisierungsschema alle gültigen Tabellen und Spalten hinzugefügt. Die Werte von $TablesAndColumnsToAdd und $TablesAndColumnsToRemove werden ignoriert. |
| $tablesAndColumnsToAdd | Geben Sie Tabellen oder Spalten an, die dem Synchronisierungsschema hinzugefügt werden sollen. Jeder Name einer Tabelle oder Spalte muss vollständig vom Schemanamen getrennt werden. Beispiel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Bei Angabe von mehreren Tabellen- oder Spaltennamen können diese durch ein Komma (,) voneinander getrennt werden. |
| $tablesAndColumnsToRemove | Geben Sie Tabellen oder Spalten an, die aus dem Synchronisierungsschema entfernt werden sollen. Jeder Name einer Tabelle oder Spalte muss vollständig vom Schemanamen getrennt werden. Beispiel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Bei Angabe von mehreren Tabellen- oder Spaltennamen können diese durch ein Komma (,) voneinander getrennt werden. |

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript **UpdateSyncSchema** verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | Gibt Informationen zu einer Synchronisierungsgruppe zurück. |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualisiert eine Synchronisierungsgruppe |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Gibt Informationen zu einem Synchronisierungselement zurück. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Gibt Informationen zu einem Synchronisierungsschema zurück. |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Aktualisiert ein Synchronisierungsschema |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie unter [Azure PowerShell-Beispiele für Azure SQL-Datenbank](../powershell-script-content-guide.md).

Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

- Übersicht: [Synchronisieren von Daten zwischen Azure SQL-Datenbank und SQL Server mit der SQL-Datensynchronisierung in Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Einrichten der Datensynchronisierung
    - Verwenden des Azure-Portals: [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und SQL Server](../sql-data-sync-sql-server-configure.md)
    - Verwenden von PowerShell
        -  [Verwenden von PowerShell zum Synchronisieren von Daten zwischen mehreren Datenbanken in Azure SQL-Datenbank](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Verwenden von PowerShell zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die SQL-Datensynchronisierung in Azure](../sql-data-sync-agent-overview.md)
- Bewährte Methoden: [Bewährte Methoden für die SQL-Datensynchronisierung in Azure](../sql-data-sync-best-practices.md)
- Überwachung: [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](../monitor-tune-overview.md)
- Problembehandlung: [Behandeln von Problemen mit der SQL-Datensynchronisierung in Azure](../sql-data-sync-troubleshoot.md)
- Aktualisieren des Synchronisierungsschemas
    - Verwenden von Transact-SQL: [Automatisieren der Replikation von Schemaänderungen in der SQL-Datensynchronisierung in Azure](../sql-data-sync-update-sync-schema.md)

Weitere Informationen zu SQL-Datenbank finden Sie hier:

- [Übersicht über die SQL-Datenbank](../sql-database-paas-overview.md)
- [Datenbank-Lebenszyklusverwaltung](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))