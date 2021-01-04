---
title: Erstellen und Verwalten von Servern und Einzeldatenbanken
description: Erfahren Sie mehr über das Erstellen und Verwalten von Servern und einzelnen Datenbanken in Azure SQL-Datenbank mit dem Azure-Portal, mit PowerShell, der Azure CLI, Transact-SQL (T-SQL) und der REST-API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: e50cce74f7291a6673e5d43f3485a1c63c81d827
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319289"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Erstellen und Verwalten von Servern und Einzeldatenbanken in Azure SQL-Datenbank

Sie können Server und einzelne Datenbanken in Azure SQL-Datenbank mit dem Azure-Portal, PowerShell, der Azure CLI, der REST-API oder Transact-SQL erstellen und verwalten.

## <a name="the-azure-portal"></a>Das Azure-Portal

Sie können die Ressourcengruppe für Azure SQL-Datenbank vorab oder beim Erstellen des Servers selbst erstellen.

### <a name="create-a-server"></a>Erstellen eines Servers

Um einen Server mithilfe des [Azure-Portals](https://portal.azure.com) zu erstellen, erstellen Sie eine neue [Serverressource](logical-servers.md) aus Azure Marketplace. Alternativ können Sie den Server erstellen, wenn Sie eine Azure SQL-Datenbank bereitstellen.

  ![Erstellen des Servers](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Erstellen einer leeren oder Beispieldatenbank

Um eine einzelne Azure SQL-Datenbank mithilfe des [Azure-Portals](https://portal.azure.com) zu erstellen, wählen Sie die Azure SQL-Datenbank-Ressource in Azure Marketplace aus. Sie können die Ressourcengruppe und den Server im Voraus oder beim Erstellen der Einzeldatenbank selbst erstellen. Sie können eine leere Datenbank oder eine Beispieldatenbank basierend auf Adventure Works LT erstellen.

  ![Datenbankerstellung 1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Informationen zum Auswählen des Tarifs für Ihre Datenbank finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Verwalten eines vorhandenen Servers

Zum Verwalten eines vorhandenen Servers navigieren Sie mithilfe einer Reihe von Methoden zum Server, z. B. über eine bestimmte Datenbankseite, die Seite für die **SQL-Server** oder die Seite **Alle Ressourcen**.

Um eine vorhandene Datenbank zu verwalten, navigieren Sie zu der Seite **SQL-Datenbanken** , und wählen Sie dann die Datenbank aus, die Sie verwalten möchten. Der folgende Screenshot zeigt, wie Sie beginnen, über die Seite **Übersicht** für eine Datenbank eine Firewall auf Serverebene für eine Datenbank festzulegen.

   ![Serverfirewallregel](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Informationen zum Konfigurieren der Leistungseigenschaften für eine Datenbank finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md).
> [!TIP]
> Eine Schnellstartanleitung zum Azure-Portal finden Sie unter [Erstellen einer Datenbank in SQL-Datenbank im Azure-Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Verwenden Sie zum Erstellen und Verwalten von Servern, Einzel- und Pooldatenbanken und Firewalls auf Serverebene mithilfe von Azure PowerShell die folgenden PowerShell-Cmdlets. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weiter.

> [!TIP]
> PowerShell-Beispielskripts finden Sie unter [Verwenden von PowerShell zum Erstellen einer Datenbank in SQL-Datenbank und Konfigurieren einer Firewallregel auf Serverebene](scripts/create-and-configure-database-powershell.md) und [Überwachen und Skalieren einer Datenbank in SQL-Datenbank mithilfe von PowerShell](scripts/monitor-and-scale-database-powershell.md).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Erstellt eine Datenbank |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Ruft mindestens eine Datenbank ab|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in einen Pool für elastische Datenbanken|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Entfernt eine Datenbank|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Erstellt eine Ressourcengruppe|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Erstellt einen Server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Gibt Informationen zu Servern zurück|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Ändert die Eigenschaften eines Servers|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Entfernt einen Server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Erstellt eine Firewallregel auf Serverebene |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Ruft Firewallregeln für einen Server ab|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändert eine Firewallregel auf einem Server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Löscht eine Firewallregel von einem Server|
| New-AzSqlServerVirtualNetworkRule | Erstellt eine [*VNET-Regel*](vnet-service-endpoint-rule-overview.md), die auf einem Subnetz basiert, das einen VNET-Dienstendpunkt darstellt. |

## <a name="the-azure-cli"></a>Die Azure-CLI

Verwenden Sie zum Erstellen und Verwalten der Server, Datenbanken und Firewalls mithilfe der [Azure CLI](/cli/azure) die folgenden [Azure CLI](/cli/azure/sql/db)-Befehle. Führen Sie die CLI mithilfe von [Cloud Shell](../../cloud-shell/overview.md) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows. Informationen zum Erstellen und Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](elastic-pool-overview.md).

> [!TIP]
> Eine Schnellstartanleitung zur Azure CLI finden Sie unter [Erstellen einer einzelnen Azure SQL-Datenbank mithilfe der Azure CLI](az-cli-script-samples-content-guide.md). Azure CLI-Beispielskripts finden Sie unter [Verwenden der CLI zum Erstellen einer Datenbank in Azure SQL-Datenbank und Konfigurieren einer SQL-Datenbank-Firewallregel](scripts/create-and-configure-database-cli.md) und [Verwenden der CLI zum Überwachen und Skalieren einer Datenbank in Azure SQL-Datenbank](scripts/monitor-and-scale-database-cli.md).
>

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Erstellt eine Datenbank|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Listet alle Datenbanken und Data Warehouses eines Servers oder alle Datenbanken eines Pools für elastische Datenbanken auf|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Listet verfügbare Dienstziele und Speicherlimits auf|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Gibt Informationen zur Datenbankverwendung zurück|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Ruft eine Datenbank oder ein Data Warehouse ab|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualisiert eine Datenbank|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Entfernt eine Datenbank|
|[az group create](/cli/azure/group#az-group-create)|Erstellt eine Ressourcengruppe|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Erstellt einen Server|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Listet Server auf|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Gibt Informationen zur Server-Verwendung zurück|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Ruft einen Server ab|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Aktualisiert einen Server|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Löscht einen Server|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Erstellt eine Serverfirewallregel|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Listet die Firewallregeln auf einem Server auf|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Zeigt die Details einer Firewallregel an|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualisiert eine Firewallregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Löscht eine Firewallregel|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Verwenden Sie zum Erstellen der Server, Datenbanken und Firewalls mithilfe von Transact-SQL die folgenden T-SQL-Befehle. Sie können diese Befehle mit dem Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) oder einem beliebigen anderen Programm ausführen, mit dem eine Verbindung mit einem Server in Azure SQL-Datenbank hergestellt und Transact-SQL-Befehle übergeben werden können. Informationen zum Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](elastic-pool-overview.md).

> [!TIP]
> Einen Schnellstart mit SQL Server Management Studio unter Microsoft Windows finden Sie unter [Azure SQL-Datenbank: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](connect-query-ssms.md). Einen Schnellstart mit Visual Studio Code unter macOS, Linux oder Windows finden Sie unter [Azure SQL-Datenbank: Verwenden von Visual Studio Code zum Herstellen einer Verbindung mit und Abfragen von Daten](connect-query-vscode.md).
> [!IMPORTANT]
> Sie können einen Server mithilfe von Transact-SQL nicht erstellen oder löschen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Erstellt eine neue einzelne Datenbank. Es muss eine Verbindung mit der master-Datenbank bestehen, um eine neue Datenbank zu erstellen.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Ändert eine Datenbank oder einen Pool für elastische Datenbanken. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Löscht eine Datenbank.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Gibt die Edition (Dienstebene), das Dienstziel (Tarif) und den Namen des Pools für elastische Datenbanken, falls vorhanden, für Azure SQL-Datenbank oder einen dedizierten SQL-Pool in Azure Synapse Analytics zurück. Wenn eine Anmeldung bei der Masterdatenbank auf einem Server in SQL-Datenbank besteht, werden Informationen zu allen Datenbanken zurückgegeben. Für Azure Synapse Analytics müssen Sie mit der Masterdatenbank verbunden sein, um eine Anmeldung erstellen zu können.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Mit diesem Befehl werden die CPU-, E/A- und Arbeitsspeichernutzung für eine Datenbank in Azure SQL-Datenbank zurückgegeben. Jede Zeile wird für 15 Sekunden beibehalten, auch wenn keine Aktivität in der Datenbank vorhanden ist.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Gibt die CPU-Nutzung und Speicherdaten für eine Datenbank in Azure SQL-Datenbank zurück. Die Daten werden in Intervallen von fünf Minuten gesammelt und aggregiert.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Enthält Statistiken zu Verbindungsereignissen für SQL-Datenbank und eine Übersicht über erfolgreiche und nicht erfolgreiche Datenbankverbindungen. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Gibt erfolgreiche Datenbankverbindungen, Verbindungsfehler und Deadlocks für Azure SQL-Datenbank zurück. Sie können diese Informationen nutzen, um die Datenbankaktivität mit SQL-Datenbank nachzuverfolgen oder um Probleme zu beheben.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Erstellt oder aktualisiert die Firewalleinstellungen auf Serverebene für den Server. Diese gespeicherte Prozedur ist nur in der master-Datenbank für den Prinzipalanmeldenamen auf Serverebene verfügbar. Eine Firewallregel auf Serverebene kann erst mithilfe von Transact-SQL erstellt werden, nachdem die erste Firewallregel auf Serverebene von einem Benutzer mit Azure-Berechtigungen erstellt wurde.|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Gibt Informationen zu den Firewalleinstellungen auf Serverebene im Zusammenhang mit Ihrer Datenbank in Azure SQL-Datenbank zurück.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Entfernt Firewalleinstellungen auf Serverebene vom Server. Diese gespeicherte Prozedur ist nur in der master-Datenbank für den Prinzipalanmeldenamen auf Serverebene verfügbar.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Mit diesem Befehl werden Firewallregeln auf Datenbankebene für Ihre Datenbank in Azure SQL-Datenbank erstellt oder geändert. Datenbank-Firewallregeln können für die Masterdatenbank und für Benutzerdatenbanken in SQL-Datenbank konfiguriert werden. Datenbank-Firewallregeln sind bei der Verwendung von Benutzern eigenständiger Datenbanken nützlich. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Gibt Informationen zu den Firewalleinstellungen auf Datenbankebene im Zusammenhang mit Ihrer Datenbank in Azure SQL-Datenbank zurück. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Entfernt eine Firewalleinstellung auf Datenbankebene aus einer Datenbank. |

## <a name="rest-api"></a>REST-API

Verwenden Sie zum Erstellen und Verwalten der Server, Datenbanken und Firewalls diese REST-API-Anforderungen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Servers - Create oder Update](/rest/api/sql/servers/createorupdate)|Erstellt oder aktualisiert einen neuen Server.|
|[Servers - Delete](/rest/api/sql/servers/delete)|Löscht eine SQL Server-Instanz.|
|[Servers - Get](/rest/api/sql/servers/get)|Ruft einen Server ab.|
|[Servers - List](/rest/api/sql/servers/list)|Gibt eine Liste aller Server in einem Abonnement zurück.|
|[Servers - List by resource group](/rest/api/sql/servers/listbyresourcegroup)|Gibt eine Liste aller Server in einer Ressourcengruppe zurück.|
|[Server - Update](/rest/api/sql/servers/update)|Aktualisiert einen vorhandenen Server.|
|[Databases - Create oder update](/rest/api/sql/databases/createorupdate)|Erstellt eine neue Datenbank oder aktualisiert eine bereits vorhandene Datenbank|
|[Datenbanken – Löschen](/rest/api/sql/databases/delete)|Löscht eine Datenbank.|
|[Datenbanken – Abrufen](/rest/api/sql/databases/get)|Ruft eine Datenbank ab|
|[Databases - List by elastic pool](/rest/api/sql/databases/listbyelasticpool)|Gibt eine Liste der Datenbanken in einem Pool für elastische Datenbanken zurück.|
|[Databases - List by server](/rest/api/sql/databases/listbyserver)|Gibt eine Liste der Datenbanken auf einem Server zurück|
|[Datenbanken – Aktualisieren](/rest/api/sql/databases/update)|Aktualisiert eine vorhandene Datenbank|
|[Firewall rules - Create oder update](/rest/api/sql/firewallrules/createorupdate)|Erstellt oder aktualisiert eine Firewallregel.|
|[Firewall rules - Delete](/rest/api/sql/firewallrules/delete)|Löscht eine Firewallregel.|
|[Firewall rules - Get](/rest/api/sql/firewallrules/get)|Ruft eine Firewallregel ab.|
|[Firewall rules - List by server](/rest/api/sql/firewallrules/listbyserver)|Gibt eine Liste von Firewallregeln zurück.|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure finden Sie unter [Migrieren zu Azure SQL-Datenbank](migrate-to-database-from-sql-server.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](features-comparison.md).
 
