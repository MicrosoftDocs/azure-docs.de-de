---
title: Referenz zur Verwaltungs-API für verwaltete Azure SQL-Instanzen
description: Informationen zum Erstellen und Konfigurieren verwalteter Instanzen von Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: cf100861705bf1654b7206445c884b5fe315b06d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792632"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Referenz zur Verwaltungs-API für verwaltete Azure SQL-Instanzen
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Sie können verwaltete Instanzen von Azure SQL Managed Instance mit dem Azure-Portal, mit PowerShell, der Azure CLI, der REST-API oder Transact-SQL erstellen und konfigurieren. Dieser Artikel gibt Ihnen eine Übersicht über die Funktionen und die API, die Sie zum Erstellen und Konfigurieren von verwalteten Instanzen verwenden können.

## <a name="azure-portal-create-a-managed-instance"></a>Azure-Portal: Erstellen einer verwalteten Instanz

Eine Schnellstartanleitung zum Erstellen einer verwalteten Instanz finden Sie unter [Schnellstart: Erstellen einer verwalteten Instanz](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>Mit PowerShell: Erstellen und Konfigurieren von verwalteten Instanzen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRM-Modulen sind im Wesentlichen identisch.

Verwenden Sie zum Erstellen und Verwalten verwalteter Instanzen mithilfe von Azure PowerShell die folgenden PowerShell-Cmdlets. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weiter.

> [!TIP]
> PowerShell-Beispielskripts finden Sie unter [Schnellstartskript: Erstellen einer verwalteten Instanz mithilfe der PowerShell-Bibliothek](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Erstellt eine verwaltete Instanz. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Gibt Informationen zu einer verwalteten Instanz zurück.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Legt Eigenschaften für eine verwaltete Instanz fest.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Entfernt eine verwaltete Instanz.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Ruft eine Liste der Verwaltungsvorgänge ab, die für die verwaltete Instanz oder einen bestimmten Vorgang ausgeführt wurden.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Bricht den Verwaltungsvorgang ab, der für die verwaltete Instanz ausgeführt wird.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Erstellt eine SQL Managed Instance-Datenbank.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Gibt Informationen zu einer SQL Managed Instance-Datenbank zurück.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Entfernt eine SQL Managed Instance-Datenbank.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Stellt eine SQL Managed Instance-Datenbank wieder her.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: Erstellen und Konfigurieren von verwalteten Instanzen

Verwenden Sie zum Erstellen und Konfigurieren verwalteter Instanzen mithilfe der [Azure CLI](/cli/azure) die folgenden [Azure CLI-Befehle für SQL Managed Instance](/cli/azure/sql/mi). Führen Sie die CLI mithilfe von [Azure Cloud Shell](../../cloud-shell/overview.md) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows.

> [!TIP]
> Eine Azure CLI-Schnellstartanleitung finden Sie unter [Arbeiten mit einer verwalteten SQL-Instanz mithilfe der Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az-sql-mi-create) |Erstellt eine verwaltete Instanz.|
|[az sql mi list](/cli/azure/sql/mi#az-sql-mi-list)|Listet verfügbare verwaltete Instanzen auf.|
|[az sql mi show](/cli/azure/sql/mi#az-sql-mi-show)|Ruft die Details für eine verwaltete Instanz ab.|
|[az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)|Aktualisiert eine verwaltete Instanz.|
|[az sql mi delete](/cli/azure/sql/mi#az-sql-mi-delete)|Entfernt eine verwaltete Instanz.|
|[az sql mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Ruft eine Liste der Verwaltungsvorgänge ab, die für die verwaltete Instanz ausgeführt wurden.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Ruft den Verwaltungsvorgang ab, der für die verwaltete Instanz ausgeführt wurde.|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Bricht den Verwaltungsvorgang ab, der für die verwaltete Instanz ausgeführt wird.|
|[az sql midb create](/cli/azure/sql/midb#az-sql-midb-create) |Erstellt eine verwaltete Datenbank.|
|[az sql midb list](/cli/azure/sql/midb#az-sql-midb-list)|Listet verfügbare verwaltete Datenbanken auf.|
|[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)|Stellt eine verwaltete Datenbank wieder her.|
|[az sql midb delete](/cli/azure/sql/midb#az-sql-midb-delete)|Entfernt eine verwaltete Datenbank.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Erstellen und Konfigurieren von Instanzdatenbanken

Verwenden Sie zum Erstellen und Konfigurieren einer Instanzdatenbank nach dem Erstellen der verwalteten Instanz die folgenden T-SQL-Befehle. Sie können diese Befehle mit dem Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) oder einem beliebigen anderen Programm ausführen, mit dem eine Verbindung mit einem Server hergestellt und Transact-SQL-Befehle übergeben werden können.

> [!TIP]
> Schnellstartanleitungen, in denen gezeigt wird, wie Sie eine verwaltete Instanz mithilfe von SQL Server Management Studio unter Microsoft Windows konfigurieren und eine Verbindung herstellen, finden Sie unter [Schnellstart: Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten Azure SQL-Instanz](connect-vm-instance-configure.md) und [Schnellstart: Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer zu einer verwalteten Azure SQL-Instanz](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Sie können eine verwaltete Instanz nicht mithilfe von Transact-SQL erstellen oder löschen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Erstellt eine neue verwaltete Instanz in SQL Managed Instance. Es muss eine Verbindung mit der master-Datenbank bestehen, um eine neue Datenbank zu erstellen.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Ändert eine verwaltete Instanz in SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST-API: Erstellen und Konfigurieren von verwalteten Instanzen

Verwenden Sie zum Erstellen und Konfigurieren verwalteter Instanzen die folgenden REST-API-Anforderungen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Verwaltete Instanzen – Erstellen oder Aktualisieren](/rest/api/sql/managedinstances/createorupdate)|Erstellt oder aktualisiert eine verwaltete Instanz.|
|[Verwaltete Instanzen – Löschen](/rest/api/sql/managedinstances/delete)|Löscht eine verwaltete Instanz.|
|[Verwaltete Instanzen – Abrufen](/rest/api/sql/managedinstances/get)|Ruft eine verwaltete Instanz ab.|
|[Verwaltete Instanzen – Auflisten](/rest/api/sql/managedinstances/list)|Gibt eine Liste der verwalteten Instanzen in einem Abonnement zurück.|
|[Verwaltet Instanzen – Auflisten nach Ressourcengruppe](/rest/api/sql/managedinstances/listbyresourcegroup)|Gibt eine Liste der verwalteten Instanzen in einer Ressourcengruppe zurück.|
|[Verwaltete Instanzen – Aktualisieren](/rest/api/sql/managedinstances/update)|Aktualisiert eine verwaltete Instanz.|
|[Vorgänge verwalteter Instanzen – Auflisten nach verwalteter Instanz](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Ruft eine Liste der Verwaltungsvorgänge ab, die für die verwaltete Instanz ausgeführt wurden.|
|[Vorgänge verwalteter Instanzen – Abrufen](/rest/api/sql/managedinstanceoperations/get)|Ruft den Verwaltungsvorgang ab, der für die verwaltete Instanz ausgeführt wurde.|
|[Vorgänge verwalteter Instanzen – Abbrechen](/rest/api/sql/managedinstanceoperations/cancel)|Bricht den Verwaltungsvorgang ab, der für die verwaltete Instanz ausgeführt wird.|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure finden Sie unter [Migrieren zu Azure SQL-Datenbank](../database/migrate-to-database-from-sql-server.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](../database/features-comparison.md).