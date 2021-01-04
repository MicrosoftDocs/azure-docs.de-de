---
title: Was ist ein Server in Azure SQL-Datenbank und Azure Synapse Analytics?
titleSuffix: ''
description: Hier erfahren Sie mehr über logische SQL-Server, die von Azure SQL-Datenbank und Azure Synapse Analytics verwendet werden. Außerdem lernen Sie, wie Sie diese verwalten.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: e0736e58983d4c28b8f2f4a20241cd36ac84fdfa
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445378"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Was ist ein logischer SQL-Server in Azure SQL-Datenbank und Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In Azure SQL-Datenbank und Azure Synapse Analytics ist ein Server ein logisches Konstrukt, das als zentraler Verwaltungspunkt für eine Sammlung von Datenbanken fungiert. Auf der Serverebene können Sie [Anmeldungen](logins-create-manage.md), [Firewallregeln](firewall-configure.md), [Überwachungsregeln](../../azure-sql/database/auditing-overview.md), [Bedrohungserkennungsrichtlinien](threat-detection-configure.md) und [Autofailover-Gruppen](auto-failover-group-overview.md) verwalten. Ein Server kann sich in einer anderen Region als seine Ressourcengruppe befinden. Bevor Sie eine Datenbank in Azure SQL-Datenbank oder eine Data Warehouse-Datenbank in Azure Synapse Analytics erstellen können, muss der Server vorhanden sein. Alle Datenbanken, die von einem einzelnen Server verwaltet werden, müssen in derselben Region wie der Server erstellt werden.

Dieser Server unterscheidet sich von einer SQL Server-Instanz, mit der Sie möglicherweise in der lokalen Umgebung vertraut sind. Es gibt keine Garantien für den Standort der Datenbanken oder Data Warehouse-Datenbanken im Zusammenhang mit dem Server, der diese verwaltet. Darüber hinaus stellen weder Azure SQL-Datenbank noch Azure Synapse Zugriff oder Features auf Instanzebene zur Verfügung. Im Gegensatz dazu werden die Instanzdatenbanken in einer verwalteten Instanz alle an einem gemeinsamen physischen Standort eingerichtet. Dies erfolgt auf dieselbe Weise wie bei SQL Server in lokalen Umgebungen oder bei virtuellen Computern.

Beim Erstellen eines Servers geben Sie ein Serveranmeldekonto und ein dazugehöriges Kennwort an, das über Administratorrechte für die Masterdatenbank auf dem Server und für alle Datenbanken verfügt, die auf diesem Server erstellt werden. Bei diesem anfänglichen Konto handelt es sich um ein SQL-Anmeldekonto. Azure SQL-Datenbank und Azure Synapse Analytics unterstützen die SQL-Authentifizierung und die Azure Active Directory-Authentifizierung. Weitere Informationen zu Anmeldungen und Authentifizierungen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](logins-create-manage.md). Windows-Authentifizierung wird nicht unterstützt.

Ein Server in SQL-Datenbank und Azure Synapse:

- wird in einem Azure-Abonnement erstellt, kann aber mit seinen enthaltenen Ressourcen in ein anderes Abonnement verschoben werden
- ist die übergeordnete Ressource für Datenbanken, Pools für elastische Datenbanken und Data Warehouses
- stellt einen Namespace für Datenbanken, Pools für elastische Datenbanken und Data Warehouse-Datenbanken bereit
- ist ein Container mit Semantik von hoher Lebensdauer – beim Löschen eines Servers werden die enthaltenen Datenbanken, Pools für elastische Datenbanken und SQK-Pools gelöscht
- beteiligt sich an der [rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) – Datenbanken, Pools für elastische Datenbanken und Data Warehouse-Datenbanken innerhalb eines Servers erben dessen Zugriffsrechte
- ist ein übergeordnetes Element der Identität von Datenbanken, Pools für elastische Datenbanken und Data Warehouse-Datenbanken für Zwecke der Azure-Ressourcenverwaltung (siehe das URL-Schema für Datenbanken und Pools)
- stellt in einer Region Ressourcen zusammen
- bietet eine Verbindungsendpunkt für den Datenbankzugriff (`<serverName>`.database.windows.net)
- bietet Zugriff auf Metadaten bezüglich der darin enthaltenen Ressourcen über DMVs (dynamic management views), indem eine Verbindung mit einer Masterdatenbank hergestellt wird
- stellt den Bereich für Verwaltungsrichtlinien bereit, die für seine Datenbanken gelten: Anmeldungen, Firewall, Überwachung, Bedrohungserkennung usw.
- ist durch ein Kontingent innerhalb des übergeordneten Abonnements eingeschränkt (standardmäßig sechs Server pro Abonnement – [siehe Grenzwerte für Abonnements hier](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- stellt den Bereich für Datenbank- und DTU- bzw. V-Kern-Kontingente für die darin enthaltenen Ressourcen (z.B. 45.000 DTU) bereit
- ist der Versionsverwaltungsbereich für Funktionen, die in enthaltenen Ressourcen aktiviert wurden
- Serverebenenprinzipal-Anmeldungen können alle Datenbanken auf einem Server verwalten
- kann Anmeldungen enthalten, die denen in SQL Server-Instanzen in lokalen Umgebungen ähnlich sind und die über Zugriff auf eine oder mehrere Datenbanken auf dem Server verfügen und denen beschränkte Administratorrechte zugewiesen werden können. Weitere Informationen finden Sie unter [Logins (Anmeldungen)](logins-create-manage.md).
- Die Standardsortierung für alle auf einem Server erstellten Datenbanken ist `SQL_LATIN1_GENERAL_CP1_CI_AS`, wobei `LATIN1_GENERAL` für Englisch (USA), `CP1` für Codepage 1252, `CI` für keine Unterscheidung von Groß-/Kleinschreibung und `AS` für die Unterscheidung nach Akzent steht.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Verwalten von Servern, Datenbanken und Firewalls mithilfe des Azure-Portals

Sie können die Ressourcengruppe für einen Server im Voraus oder beim Erstellen des Servers selbst erstellen. Es existieren mehrere Methoden zum Erhalten eines Formulars für einen neuen SQL-Server. Sie erhalten ein solches entweder durch das Erstellen eines neuen SQL-Servers oder als Teil der Erstellung einer neuen Datenbank.

### <a name="create-a-blank-server"></a>Erstellen eines leeren Servers

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu einem leeren SQL-Server-Formular (logischer SQL-Server), um einen Server (ohne eine Datenbank, einen Pool für elastische Datenbanken oder eine Data Warehouse-Datenbank) zu erstellen.

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Erstellen einer leeren Datenbank oder Beispieldatenbank in Azure SQL-Datenbank

Zum Erstellen einer SQL-Datenbank mithilfe des [Azure-Portals](https://portal.azure.com) navigieren Sie zu einem leeren SQL-Datenbank-Formular, und stellen Sie die erforderlichen Informationen bereit. Sie können die Ressourcengruppe und den Server im Voraus oder beim Erstellen der Datenbank selbst erstellen. Sie können eine leere Datenbank oder eine Beispieldatenbank basierend auf Adventure Works LT erstellen.

  ![Datenbankerstellung 1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Informationen zum Auswählen des Tarifs für Ihre Datenbank finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md).

Informationen zum Erstellen einer verwalteten Instanz finden Sie unter [Erstellen einer verwalteten Instanz](../managed-instance/instance-create-quickstart.md).

### <a name="manage-an-existing-server"></a>Verwalten eines vorhandenen Servers

Zum Verwalten eines vorhandenen Servers navigieren Sie mithilfe einer Reihe von Methoden zum Server, z. B. über eine bestimmte Datenbankseite, die Seite für die **SQL-Server** oder die Seite **Alle Ressourcen**.

Um eine vorhandene Datenbank zu verwalten, navigieren Sie zu der Seite **SQL-Datenbanken**, und klicken Sie auf die Datenbank, die Sie verwalten möchten. Der folgende Screenshot zeigt, wie Sie beginnen, über die Seite **Übersicht** für eine Datenbank eine Firewall auf Serverebene für eine Datenbank festzulegen.

   ![Serverfirewallregel](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Informationen zum Konfigurieren der Leistungseigenschaften für eine Datenbank finden Sie unter [DTU-basiertes Kaufmodell](service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](service-tiers-vcore.md).
> [!TIP]
> Eine Schnellstartanleitung zum Azure-Portal finden Sie unter [Erstellen einer Datenbank in SQL-Datenbank im Azure-Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Verwalten von Servern, Datenbanken und Firewalls mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Verwenden Sie zum Erstellen und Verwalten von Servern, Datenbanken und Firewalls mithilfe von Azure PowerShell die folgenden PowerShell-Cmdlets. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weiter. Informationen zum Erstellen und Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](elastic-pool-overview.md).

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

> [!TIP]
> Eine Schnellstartanleitung zu PowerShell finden Sie unter [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](single-database-create-quickstart.md). PowerShell-Beispielskripts finden Sie unter [Verwenden von PowerShell zum Erstellen einer einzelnen Datenbank und zum Konfigurieren einer Firewallregel auf Serverebene](scripts/create-and-configure-database-powershell.md) und [Überwachen und Skalieren einer Einzeldatenbank in Azure SQL­-Datenbank mit PowerShell](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Verwalten von Servern, Datenbanken und Firewalls mithilfe der Azure CLI

Verwenden Sie zum Erstellen und Verwalten von Servern, Datenbanken und Firewalls mithilfe der [Azure CLI](/cli/azure) die folgenden [Azure CLI-Befehle für SQL-Datenbank](/cli/azure/sql/db). Führen Sie die CLI mithilfe von [Cloud Shell](../../cloud-shell/overview.md) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows. Informationen zum Erstellen und Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](elastic-pool-overview.md).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Erstellt eine Datenbank|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Mit diesem Cmdlet werden alle Datenbanken, die von einem Server verwaltet werden, oder alle Datenbanken eines Pools für elastische Datenbanken aufgeführt.|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Listet verfügbare Dienstziele und Speicherlimits auf|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Gibt Informationen zur Datenbankverwendung zurück|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Dieses Cmdlet ruft eine Datenbank ab.
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

> [!TIP]
> Eine Schnellstartanleitung zur Azure CLI finden Sie unter [Azure CLI-Beispiele für Azure SQL-Datenbank und verwaltete SQL-Instanzen](az-cli-script-samples-content-guide.md). Azure CLI-Beispielskripts finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle zum Erstellen einer einzelnen Datenbank und Konfigurieren einer Firewallregel](scripts/create-and-configure-database-cli.md) und [Verwenden der Azure CLI zum Überwachen und Skalieren einer einzelnen Datenbank in Azure SQL-Datenbank](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Verwalten von Servern, Datenbanken und Firewalls mithilfe von Transact-SQL

Verwenden Sie zum Erstellen der Server, Datenbanken und Firewalls mithilfe von Transact-SQL die folgenden T-SQL-Befehle. Sie können diese Befehle mit dem Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) oder einem beliebigen anderen Programm ausführen, mit dem eine Verbindung mit einem Server hergestellt und Transact-SQL-Befehle übergeben werden können. Informationen zum Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](elastic-pool-overview.md).

> [!IMPORTANT]
> Sie können einen Server mithilfe von Transact-SQL nicht erstellen oder löschen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | Mit diesem Befehl wird eine neue Datenbank in Azure SQL-Datenbank erstellt. Es muss eine Verbindung mit der master-Datenbank bestehen, um eine neue Datenbank zu erstellen.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | Mit diesem Befehl wird eine neue Data Warehouse-Datenbank in Azure Synapse erstellt. Es muss eine Verbindung mit der master-Datenbank bestehen, um eine neue Datenbank zu erstellen.|
| [ALTER DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Mit diesem Befehl kann eine Datenbank oder ein Pool für elastische Datenbanken geändert werden. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|Mit diesem Befehl kann eine Data Warehouse-Datenbank in Azure Synapse geändert werden.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Löscht eine Datenbank.|
|[sys.database_service_objectives (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Dieser Befehl gibt die Edition (Dienstebene), das Dienstziel (Tarif) und den Namen des Pools für elastische Datenbanken für eine Datenbank zurück, sofern diese vorhanden sind. Wenn eine Anmeldung bei der Masterdatenbank für einen Server besteht, werden Informationen zu allen Datenbanken zurückgegeben. Für Azure Synapse müssen Sie mit der Masterdatenbank verbunden sein, um eine Anmeldung erstellen zu können.|
|[sys.dm_db_resource_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Mit diesem Befehl werden die CPU-, E/A- und Arbeitsspeichernutzung für eine Datenbank in Azure SQL-Datenbank zurückgegeben. Jede Zeile wird für 15 Sekunden beibehalten, auch wenn keine Aktivität in der Datenbank vorhanden ist.|
|[sys.resource_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Dieser Befehl gibt die CPU-Nutzung und Speicherdaten für eine Datenbank in Azure SQL-Datenbank zurück. Die Daten werden in Intervallen von fünf Minuten gesammelt und aggregiert.|
|[sys.database_connection_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Mit diesem Befehl können Sie Statistiken für Datenbankverbindungsereignisse für Azure SQL-Datenbank abrufen, die Ihnen eine Übersicht über erfolgreiche und nicht erfolgreiche Datenbankverbindungen bieten. |
|[sys.event_log (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Mit diesem Befehl werden erfolgreiche und nicht erfolgreiche Datenbankverbindungen sowie Deadlocks von Azure SQL-Datenbank zurückgegeben. Anhand dieser Informationen können Sie Ihre Datenbankaktivität nachverfolgen und Fehler beheben.|
|[sp_set_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Erstellt oder aktualisiert die Firewalleinstellungen auf Serverebene für den Server. Diese gespeicherte Prozedur ist nur in der master-Datenbank für den Prinzipalanmeldenamen auf Serverebene verfügbar. Eine Firewallregel auf Serverebene kann erst mithilfe von Transact-SQL erstellt werden, nachdem die erste Firewallregel auf Serverebene von einem Benutzer mit Azure-Berechtigungen erstellt wurde.|
|[sys.firewall_rules (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Dieser Befehl gibt Informationen über die Firewalleinstellungen auf Serverebene zurück, die einem Server zugeordnet sind.|
|[sp_delete_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Mit diesem Befehl werden Firewalleinstellungen auf Serverebene von einem Server entfernt. Diese gespeicherte Prozedur ist nur in der master-Datenbank für den Prinzipalanmeldenamen auf Serverebene verfügbar.|
|[sp_set_database_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Mit diesem Befehl werden Firewallregeln auf Datenbankebene für eine Datenbank in Azure SQL-Datenbank erstellt oder geändert. Datenbank-Firewallregeln können für die Masterdatenbank und für Benutzerdatenbanken in SQL-Datenbank konfiguriert werden. Datenbank-Firewallregeln sind bei der Verwendung von Benutzern eigenständiger Datenbanken nützlich. Datenbankfirewallregeln werden in Azure Synapse nicht unterstützt.|
|[sys.database_firewall_rules (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Mit diesem Befehl werden Informationen über die Firewalleinstellungen auf Datenbankebene für eine Datenbank in Azure SQL-Datenbank zurückgegeben. |
|[sp_delete_database_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Mit diesem Befehl werden Firewalleinstellungen auf Datenbankebene für eine Datenbank in Azure SQL-Datenbank entfernt. |

> [!TIP]
> Einen Schnellstart mit SQL Server Management Studio unter Microsoft Windows finden Sie unter [Azure SQL-Datenbank: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](connect-query-ssms.md). Einen Schnellstart mit Visual Studio Code unter macOS, Linux oder Windows finden Sie unter [Azure SQL-Datenbank: Verwenden von Visual Studio Code zum Herstellen einer Verbindung mit und Abfragen von Daten](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Verwalten von Servern, Datenbanken und Firewalls mithilfe der REST-API

Verwenden Sie zum Erstellen und Verwalten der Server, Datenbanken und Firewalls die folgenden REST-API-Anforderungen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Servers - Create oder Update](/rest/api/sql/servers/createorupdate)|Erstellt oder aktualisiert einen neuen Server.|
|[Servers - Delete](/rest/api/sql/servers/delete)|Mit diesem Befehl wird ein Server gelöscht.|
|[Servers - Get](/rest/api/sql/servers/get)|Ruft einen Server ab.|
|[Servers - List](/rest/api/sql/servers/list)|Gibt eine Liste mit Servern zurück.|
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

- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank finden Sie unter [Migrieren zu Azure SQL-Datenbank](migrate-to-database-from-sql-server.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](features-comparison.md).