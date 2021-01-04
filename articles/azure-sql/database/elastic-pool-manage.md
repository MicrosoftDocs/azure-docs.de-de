---
title: Verwalten von Pools für elastische Datenbanken
description: Sie können Pools für elastische Datenbanken in Azure SQL-Datenbank mit dem Azure-Portal, mit PowerShell, mit der Azure CLI, mit Transact-SQL (T-SQL) und mit der REST-API erstellen und verwalten.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 9c9af6e3bc3dfd798f4b3f0cad9319aa573c425d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455997"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Verwalten von Pools für elastische Datenbanken in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Mit einem Pool für elastische Datenbanken legen Sie die Menge der Ressourcen fest, die für den Pool für elastische Datenbanken zur Verarbeitung der Workload der zugehörigen Datenbanken erforderlich sind, sowie die Menge der Ressourcen für die einzelnen Pooldatenbanken.

## <a name="azure-portal"></a>Azure-Portal

Alle Pooleinstellungen befinden sich an einem Ort: dem Blatt **Pool konfigurieren**. Zum Aufrufen dieses Blatts suchen Sie im Azure-Portal einen Pool für elastische Datenbanken und klicken entweder oben auf dem Blatt oder im Ressourcenmenü auf der linken Seite auf **Pool konfigurieren**.

Hier können Sie eine beliebige Kombination aus den folgenden Änderungen vornehmen und alle in einem Batch speichern:

1. Ändern der Dienstebene des Pools
2. Herauf- oder Herunterskalieren der Leistung (DTU oder V-Kerne)
3. Hinzufügen oder Entfernen von Datenbanken im Pool
4. Festlegen einer minimalen (garantierten) und maximalen Leistungsgrenze für die Datenbanken in den Pools
5. Überprüfen der Kostenzusammenfassung, um alle Änderungen an Ihrer Rechnung anzuzeigen, die sich durch die neue Auswahl ergeben

![Konfigurationsblatt eines Pools für elastische Datenbanken](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Verwenden Sie zum Erstellen und Verwalten von Pools für elastische Datenbanken und Pooldatenbanken in SQL-Datenbank mithilfe von Azure PowerShell die folgenden PowerShell-Cmdlets. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weiter. Weitere Informationen zum Erstellen und Verwalten des Servers für Pools für elastische Datenbanken finden Sie unter [Was ist ein logischer SQL-Server in Azure SQL-Datenbank und Azure Synapse?](logical-servers.md). Weitere Informationen zum Erstellen und Verwalten von Firewallregeln finden Sie unter [Erstellen und Verwalten von Firewallregeln mit PowerShell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> PowerShell-Beispielskripts finden Sie unter [Erstellen von Pools für elastische Datenbanken und Verschieben von Datenbanken zwischen Pools und aus einem Pool heraus mit PowerShell](scripts/move-database-between-elastic-pools-powershell.md) und [Verwenden von PowerShell zum Überwachen und Skalieren eines Pools für elastische SQL-Datenbanken in der Azure SQL-Datenbank](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Erstellt einen Pool für elastische Datenbanken.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Ruft die Pools für elastische Datenbanken und ihre Eigenschaftswerte ab.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Ändert die Eigenschaften eines Pools für elastische Datenbanken. Verwenden Sie z.B. die **StorageMB**-Eigenschaft zum Ändern der maximalen Speicherkapazität eines Pools für elastische Datenbanken.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Löscht einen Pool für elastische Datenbanken.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Ruft den Status von Vorgängen für einen Pool für elastische Datenbanken ab.|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Erstellt eine neue Datenbank in einem vorhandenen Pool oder als einzelne Datenbank. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Ruft mindestens eine Datenbank ab.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in, aus oder zwischen Pools für elastische Datenbanken.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Entfernt eine Datenbank.|

> [!TIP]
> Das Erstellen zahlreicher Datenbanken in einem Pool für elastische Datenbanken kann eine Weile dauern, wenn dies über das Portal oder über PowerShell-Cmdlets erfolgt, die jeweils nur eine Einzeldatenbank erstellen. Informationen zum Automatisieren der Erstellung in einem Pool für elastische Datenbanken finden Sie unter [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Azure CLI

Verwenden Sie zum Erstellen und Verwalten von Pools für elastische SQL-Datenbank-Instanzen mithilfe der [Azure CLI](/cli/azure) die folgenden [Azure CLI-SQL-Datenbank](/cli/azure/sql/db)-Befehle. Führen Sie die CLI mithilfe von [Cloud Shell](../../cloud-shell/overview.md) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows.

> [!TIP]
> Azure CLI-Beispielskripts finden Sie unter [Verwenden der Azure CLI zum Verschieben einer Azure SQL-Datenbank in einen Pool für elastische SQL-Datenbanken ](scripts/move-database-between-elastic-pools-cli.md) und [Verwenden der Azure CLI zum Skalieren eines Pools für elastische SQL-Datenbanken in Azure SQL-Datenbank](scripts/scale-pool-cli.md).
>

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Erstellt einen Pool für elastische Datenbanken.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Gibt eine Liste der Pools für elastische Datenbanken auf einem Server zurück.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Gibt eine Liste der Datenbanken in einem Pool für elastische Datenbanken zurück.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Umfasst auch verfügbare DTU-Einstellungen, Speicherlimits und datenbankbezogene Einstellungen für den Pool. Zum Verringern der Ausführlichkeit sind zusätzliche Speicherlimits und datenbankbezogene Einstellungen standardmäßig ausgeblendet.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualisiert einen Pool für elastische Datenbanken.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Löscht den Pool für elastische Datenbanken.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Verwenden Sie die folgenden T-SQL-Befehle zum Erstellen und Verschieben von Datenbanken in vorhandenen Pools für elastische Datenbanken oder zum Zurückgeben von Informationen zu einem Pool für elastische SQL-Datenbank-Instanzen mit Transact-SQL. Sie können diese Befehle mit dem Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) oder einem beliebigen anderen Programm ausführen, mit dem eine Verbindung mit einem Server hergestellt und Transact-SQL-Befehle übergeben werden können. Weitere Informationen zum Erstellen und Verwalten von Firewallregeln mit T-SQL finden Sie unter [Verwalten von Firewallregeln mit Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Mit Transact-SQL können Sie einen Pool für elastische Azure SQL-Datenbank-Instanzen nicht erstellen, aktualisieren oder löschen. Sie können Datenbanken einem Pool für elastische Datenbanken hinzufügen oder daraus entfernen und mithilfe von DMVs Informationen zu vorhandenen Pools für elastische Datenbanken zurückgeben.
>

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/create-database-azure-sql-database)|Erstellt eine neue Datenbank in einem vorhandenen Pool oder als einzelne Datenbank. Es muss eine Verbindung mit der master-Datenbank bestehen, um eine neue Datenbank zu erstellen.|
| [ALTER DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/alter-database-azure-sql-database) |Dient zum Verschieben einer Datenbank in, aus oder zwischen Pools für elastische Datenbanken.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Löscht eine Datenbank.|
|[sys.elastic_pool_resource_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Dieser Befehl gibt Ressourcenverwendungsstatistiken für alle Pools für elastische Datenbanken auf einem Server zurück. Für jeden Pool für elastische Datenbanken ist eine Zeile pro 15-Sekunden-Berichtzeitfenster vorhanden (vier Zeilen pro Minute). Dies umfasst CPU-, E/A-, Protokoll-, Speicher- und gleichzeitige Anforderungs-/Sitzungsauslastung durch alle Datenbanken im Pool.|
|[sys.database_service_objectives (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Dieser Befehl gibt die Version (Dienstebene), das Dienstziel (Tarif) und den Namen des Pools für elastische Datenbanken, falls vorhanden, für eine Datenbank in SQL-Datenbank oder Azure Synapse Analytics zurück. Wenn eine Anmeldung an der Masterdatenbank in einem Server besteht, werden Informationen zu allen Datenbanken zurückgegeben. Für Azure Synapse Analytics müssen Sie mit der Masterdatenbank verbunden sein, um eine Anmeldung erstellen zu können.|

## <a name="rest-api"></a>REST-API

Verwenden Sie zum Erstellen und Verwalten von Pools für elastische Datenbanken und Pooldatenbanken in SQL-Datenbank diese REST-API-Anforderungen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Pools für elastische Datenbanken – Erstellen oder Aktualisieren](/rest/api/sql/elasticpools/createorupdate)|Erstellt einen neuen Pool für elastische Datenbanken oder aktualisiert einen vorhandenen elastischen Pool für elastische Datenbanken|
|[Pools für elastische Datenbanken – Löschen](/rest/api/sql/elasticpools/delete)|Löscht den Pool für elastische Datenbanken.|
|[Pools für elastische Datenbanken – Abrufen](/rest/api/sql/elasticpools/get)|Ruft einen Pool für elastische Datenbanken ab|
|[Pools für elastische Datenbanken – Auflisten nach Server](/rest/api/sql/elasticpools/listbyserver)|Gibt eine Liste der Pools für elastische Datenbanken auf einem Server zurück.|
|[Pools für elastische Datenbanken – Aktualisieren](/rest/api/sql/elasticpools/listbyserver)|Aktualisiert einen vorhandenen Pool für elastische Datenbanken|
|[Pool für elastische Datenbanken – Aktivitäten](/rest/api/sql/elasticpoolactivities)|Gibt Aktivitäten des Pools für elastische Datenbanken zurück|
|[Pool für elastische Datenbanken – Datenbankaktivitäten](/rest/api/sql/elasticpooldatabaseactivities)|Gibt Aktivitäten in Datenbanken in einem Pool für elastische Datenbanken zurück|
|[Databases - Create oder update](/rest/api/sql/databases/createorupdate)|Erstellt eine neue Datenbank oder aktualisiert eine bereits vorhandene Datenbank|
|[Datenbanken – Abrufen](/rest/api/sql/databases/get)|Ruft eine Datenbank ab|
|[Databases - List by elastic pool](/rest/api/sql/databases/listbyelasticpool)|Gibt eine Liste der Datenbanken in einem Pool für elastische Datenbanken zurück.|
|[Databases - List by server](/rest/api/sql/databases/listbyserver)|Gibt eine Liste der Datenbanken auf einem Server zurück|
|[Datenbanken – Aktualisieren](/rest/api/sql/databases/update)|Aktualisiert eine vorhandene Datenbank|

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Entwurfsmustern für SaaS-Anwendungen, für die Pools für elastische Datenbanken verwendet werden, finden Sie unter [Entwurfsmuster für SaaS-Anwendungen mit mehreren Mandanten und Azure SQL-Datenbank](saas-tenancy-app-design-patterns.md).
* Ein SaaS-Tutorial, in dem Pools für elastische Datenbanken verwendet werden, finden Sie in der [Einführung in die SaaS-Anwendung Wingtip](saas-dbpertenant-wingtip-app-overview.md).