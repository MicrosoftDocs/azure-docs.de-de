---
title: Referenz zur Verwaltungs-API für eine verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Erstellen und Verwalten von verwalteten Azure SQL-Datenbank-Instanzen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 6362084c11ce7aa9078823758700239694162765
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150764"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Referenz zur Verwaltungs-API für verwaltete Azure SQL-Datenbank-Instanzen

Sie können verwaltete Azure SQL-Datenbank-Instanzen mit dem Azure-Portal, mit PowerShell, der Azure CLI, der REST-API oder Transact-SQL erstellen und verwalten. Dieser Artikel gibt Ihnen eine Übersicht über Funktionen und API, die Sie zum Erstellen und Konfigurieren einer verwalteten Instanz verwenden können.

## <a name="azure-portal-create-a-managed-instance"></a>Azure-Portal: Erstellen einer verwalteten Instanz

Eine Schnellstartanleitung zum Erstellen einer verwalteten Azure SQL-Datenbank-Instanz finden Sie unter [Schnellstart: Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Erstellen und Verwalten verwalteter Instanzen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

Verwenden Sie zum Erstellen und Verwalten verwalteter Instanzen mithilfe von Azure PowerShell die folgenden PowerShell-Cmdlets. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weiter.

> [!TIP]
> PowerShell-Beispielskripts finden Sie unter [Schnellstartskript: Erstellen einer verwalteten Azure SQL-Instanz mithilfe der PowerShell-Bibliothek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Erstellt eine verwaltete Azure SQL-Datenbank-Instanz |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Gibt Informationen zur verwalteten Azure SQL-Instanz zurück|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Legt Eigenschaften für eine verwaltete Azure SQL-Datenbank-Instanz fest|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Entfernt eine verwaltete Azure SQL-Datenbank-Instanz|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Erstellt eine Datenbank der verwalteten Azure SQL-Datenbank-Instanz|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Gibt Informationen zur Datenbank der verwalteten Azure SQL-Instanz zurück|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Entfernt eine Datenbank der verwalteten Azure SQL-Datenbank-Instanz|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Stellt eine Datenbank der verwalteten Azure SQL-Datenbank-Instanz wieder her|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure-Befehlszeilenschnittstelle: Erstellen und Verwalten verwalteter Instanzen

Verwenden Sie zum Erstellen und Verwalten verwalteter Instanzen mithilfe der [Azure CLI](/cli/azure) die folgenden [Azure CLI-Befehle für eine verwaltete SQL-Instanz](/cli/azure/sql/mi). Führen Sie die CLI mithilfe von [Cloud Shell](/azure/cloud-shell/overview) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows.

> [!TIP]
> Eine Azure CLI-Schnellstartanleitung finden Sie unter [Arbeiten mit einer verwalteten SQL-Instanz mithilfe der Azure CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Erstellt eine verwaltete Instanz|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Listet verfügbare verwaltete Instanzen auf|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Ruft die Details für eine verwaltete Instanz ab|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualisiert eine verwaltete Instanz|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Entfernt eine verwaltete Instanz|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Erstellt eine verwaltete Datenbank|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Listet verfügbare verwaltete Datenbanken auf|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Stellt eine verwaltete Datenbank wieder her|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Entfernt eine verwaltete Datenbank|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Erstellen und Verwalten von Instanzdatenbanken

Verwenden Sie zum Erstellen und Verwalten einer Instanzdatenbank nach dem Erstellen der verwalteten Instanz die folgenden T-SQL-Befehle. Sie können diese Befehle über das Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) oder ein anderes Programm ausgeben, mit dem eine Verbindung mit einem Azure SQL-Datenbank-Server hergestellt und Transact-SQL-Befehle übergeben werden können.

> [!TIP]
> Schnellstartanleitungen, in denen gezeigt wird, wie Sie eine verwaltete Instanz mithilfe von SQL Server Management Studio unter Microsoft Windows konfigurieren und eine Verbindung herstellen, finden Sie unter [Schnellstart: Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-vm.md) und [Schnellstart: Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer mit einer verwalteten Azure SQL-Datenbank-Instanz](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Sie können eine verwaltete Instanz mithilfe von Transact-SQL nicht erstellen oder löschen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Erstellt eine neue Datenbank der verwalteten Instanz. Sie müssen über eine Verbindung mit der Masterdatenbank verfügen, um eine neue Datenbank erstellen zu können.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Ändert eine Datenbank der verwalteten Azure SQL-Instanz.|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST-API: Erstellen und Verwalten verwalteter Instanzen

Verwenden Sie zum Erstellen und Verwalten verwalteter Instanzen die folgenden REST-API-Anforderungen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Verwaltete Instanzen – Erstellen oder Aktualisieren](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Erstellt oder aktualisiert eine verwaltete Instanz.|
|[Verwaltete Instanzen – Löschen](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Löscht eine verwaltete Instanz.|
|[Verwaltete Instanzen – Abrufen](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Ruft eine verwaltete Instanz ab.|
|[Verwaltete Instanzen – Auflisten](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Gibt eine Liste der verwalteten Instanzen in einem Abonnement zurück.|
|[Verwaltet Instanzen – Auflisten nach Ressourcengruppe](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Gibt eine Liste der verwalteten Instanzen in einer Ressourcengruppe zurück.|
|[Verwaltete Instanzen – Aktualisieren](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualisiert eine verwaltete Instanz.|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure finden Sie unter [Migrieren zu Azure SQL-Datenbank](sql-database-single-database-migrate.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](sql-database-features.md).
