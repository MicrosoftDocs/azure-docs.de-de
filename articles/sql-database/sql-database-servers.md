---
title: Azure SQL-Datenbank-Server | Microsoft-Dokumentation
description: Erfahren Sie etwas über Azure SQL-Datenbank-Server und deren Verwaltung.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 21d4e7bd3bf7453d2c770034728aedfdaa5ab85e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790172"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Azure SQL-Datenbank-Server und ihre Verwaltung

## <a name="what-is-an-azure-sql-database-server"></a>Was ist ein Azure SQL-Datenbank-Server?

Ein SQL-Datenbank-Server ist ein logisches Konstrukt, das als zentraler Verwaltungspunkt für mehrere Singletons oder [in einem Pool zusammengefasste](sql-database-elastic-pool.md) Datenbanken, [Anmeldungen](sql-database-manage-logins.md), [Firewallregeln](sql-database-firewall-configure.md), [Überwachungsregeln](sql-database-auditing.md), [Richtlinien zur Bedrohungserkennung](sql-database-threat-detection.md) und [Failovergruppen](sql-database-auto-failover-group.md) fungiert. Ein SQL-Datenbank-Server kann sich in einer anderen Region als seine Ressourcengruppe befinden. Der SQL-Datenbank-Server muss vorhanden sein, bevor Sie die Azure SQL-Datenbank erstellen können. Alle Datenbanken, die von einem SQL-Datenbank-Server verwaltet werden, müssen in derselben Region wie der SQL-Datenbank-Server erstellt werden.

Ein SQL-Datenbank-Server unterscheidet sich von einer SQL Server-Instanz, mit der Sie möglicherweise in Ihrer lokalen Umgebung vertraut sind. Insbesondere gibt der SQL-Datenbank-Dienst keine Garantie bezüglich des Speicherorts der Datenbanken in Bezug auf den SQL-Datenbank-Server, von dem sie verwaltet werden, und er bietet keinen Zugriff und keine Funktionen auf Instanzebene. Im Gegensatz dazu sind die Instanzdatenbanken in einer verwalteten Instanz alle gemeinsam angeordnet – und zwar auf dieselbe Weise wie bei SQL Server in lokalen Umgebungen.

Wenn Sie einen SQL-Datenbank-Server erstellen, geben Sie ein Serveranmeldekonto und -kennwort an, mit dem Sie Administratorrechte für die Masterdatenbank auf diesem Server und alle auf diesem Server erstellten Datenbanken haben. Bei diesem anfänglichen Konto handelt es sich um ein SQL-Anmeldekonto. Azure SQL-Datenbank unterstützt für die Authentifizierung die SQL-Authentifizierung und die Azure Active Directory-Authentifizierung. Weitere Informationen zu Anmeldungen und Authentifizierungen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](sql-database-manage-logins.md). Windows-Authentifizierung wird nicht unterstützt.

Ein SQL-Datenbank-Server:

- wird in einem Azure-Abonnement erstellt, kann aber mit seinen enthaltenen Ressourcen in ein anderes Abonnement verschoben werden
- ist die übergeordnete Ressource für Datenbanken, Pools für elastische Datenbanken und Data Warehouses
- bietet einen Namespace für Datenbanken, für Pools für elastische Datenbanken und Data Warehouses
- ist ein logischer Container mit Semantik von hoher Lebensdauer – beim Löschen eines Servers werden die enthaltenen Datenbanken, Pools für elastische Datenbanken und Data Warehouses gelöscht
- beteiligt sich an der [rollenbasierten Zugriffssteuerung (role-based access control, RBAC) in Azure](/azure/role-based-access-control/overview) – Datenbanken, Pools für elastische Datenbanken und Data Warehouses innerhalb eines Servers erben dessen Zugriffsrechte
- ist ein oberes Element der Identität von Datenbanken, Pools für elastische Datenbanken und Data Warehouses für Zwecke der Azure-Ressourcenverwaltung (siehe das URL-Schema für Datenbanken und Pools)
- stellt in einer Region Ressourcen zusammen
- bietet eine Verbindungsendpunkt für den Datenbankzugriff (`<serverName>`.database.windows.net)
- bietet Zugriff auf Metadaten bezüglich der darin enthaltenen Ressourcen über DMVs (dynamic management views), indem eine Verbindung mit einer Masterdatenbank hergestellt wird
- stellt den Bereich für Verwaltungsrichtlinien bereit, die für seine Datenbanken gelten: Anmeldungen, Firewall, Überwachung, Bedrohungserkennung usw.
- ist durch ein Kontingent innerhalb des übergeordneten Abonnements eingeschränkt (standardmäßig sechs Server pro Abonnement – [siehe Grenzwerte für Abonnements hier](../azure-subscription-service-limits.md))
- stellt den Bereich für Datenbank- und DTU- bzw. V-Kern-Kontingente für die darin enthaltenen Ressourcen (z.B. 45.000 DTU) bereit
- ist der Versionsverwaltungsbereich für Funktionen, die in enthaltenen Ressourcen aktiviert wurden
- Serverebenenprinzipal-Anmeldungen können alle Datenbanken auf einem Server verwalten
- kann Anmeldungen enthalten, die denen in SQL-Instanzen vor Ort ähnlich sind und die über Zugriff auf eine oder mehrere Datenbanken auf dem Server verfügen und denen beschränkte Administratorrechte zugewiesen werden können. Weitere Informationen finden Sie unter [Logins (Anmeldungen)](sql-database-manage-logins.md).
- Die Standardsortierung für alle auf einem SQL-Datenbank-Server erstellten Datenbanken ist `SQL_LATIN1_GENERAL_CP1_CI_AS`, wobei `LATIN1_GENERAL` für Englisch (USA), `CP1` für Codepage 1252, `CI` für keine Unterscheidung von Groß-/Kleinschreibung und `AS` für die Unterscheidung nach Akzent steht.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe des Azure-Portals

Sie können die Ressourcengruppe der Azure SQL-Datenbank im Voraus oder beim Erstellen des Servers selbst erstellen. Es existieren mehrere Methoden zum Erhalten eines Formulars für einen neuen SQL-Server. Sie erhalten ein solches entweder durch das Erstellen eines neuen SQL-Servers oder als Teil der Erstellung einer neuen Datenbank.

### <a name="create-a-blank-sql-database-server"></a>Erstellen eines leeren SQL-Datenbank-Servers

Um mithilfe des [Azure-Portals](https://portal.azure.com) einen Azure SQL-Datenbank-Server (ohne eine Datenbank) zu erstellen, navigieren Sie zu einem leeren SQL-Server-Formular (logischer Server).  

### <a name="create-a-blank-or-sample-sql-database"></a>Erstellen einer leeren oder einer Beispiel-SQL-Datenbank

Zum Erstellen einer Azure SQL-Datenbank mithilfe des [Azure-Portals](https://portal.azure.com) navigieren Sie zu einem leeren SQL-Datenbank-Formular, und stellen Sie die erforderlichen Informationen bereit. Sie können die Ressourcengruppe für Azure SQL-Datenbank und den SQL-Datenbank-Server im Voraus oder beim Erstellen der Datenbank selbst erstellen. Sie können eine leere Datenbank oder eine Beispieldatenbank basierend auf Adventure Works LT erstellen.

  ![Datenbankerstellung 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Informationen zum Auswählen des Tarifs für Ihre Datenbank finden Sie unter [DTU-basiertes Kaufmodell](sql-database-service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](sql-database-service-tiers-vcore.md).

Informationen zum Erstellen einer verwalteten Instanz finden Sie unter [Erstellen einer verwalteten Instanz](sql-database-managed-instance-get-started.md).

### <a name="manage-an-existing-sql-server"></a>Verwalten eines vorhandenen SQL-Servers

Zum Verwalten eines vorhandenen Servers navigieren Sie mithilfe einer Reihe von Methoden zum Server, z.B. über eine bestimmte Seite der SQL-Datenbank, die Seite der **SQL-Server** oder die Seite **All resources** (Alle Ressourcen).

Um eine vorhandene Datenbank zu verwalten, navigieren Sie zu der Seite **SQL-Datenbanken**, und klicken Sie auf die Datenbank, die Sie verwalten möchten. Der folgende Screenshot zeigt, wie Sie beginnen, über die Seite **Übersicht** für eine Datenbank eine Firewall auf Serverebene für eine Datenbank festzulegen.

   ![Serverfirewallregel](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Informationen zum Konfigurieren der Leistungseigenschaften für eine Datenbank finden Sie unter [DTU-basiertes Kaufmodell](sql-database-service-tiers-dtu.md) und [V-Kern-basiertes Kaufmodell](sql-database-service-tiers-vcore.md).
> [!TIP]
> Eine Schnellstartanleitung zum Azure-Portal finden Sie unter [Erstellen einer Azure SQL-Datenbank im Azure-Portal](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

Verwenden Sie zum Erstellen und Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe von Azure PowerShell die folgenden PowerShell-Cmdlets. Wenn Sie PowerShell installieren oder aktualisieren müssen, helfen Ihnen die Informationen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) weiter. Informationen zum Erstellen und Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](sql-database-elastic-pool.md).

| Cmdlet | BESCHREIBUNG |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Erstellt eine Datenbank |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Ruft mindestens eine Datenbank ab|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in einen Pool für elastische Datenbanken|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Entfernt eine Datenbank|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Erstellt eine Ressourcengruppe|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Erstellt einen Server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Gibt Informationen zu Servern zurück|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Ändert die Eigenschaften eines Servers|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Entfernt einen Server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Erstellt eine Firewallregel auf Serverebene |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Ruft Firewallregeln für einen Server ab|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändert eine Firewallregel auf einem Server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Löscht eine Firewallregel von einem Server|
| New-AzSqlServerVirtualNetworkRule | Erstellt eine [*VNET-Regel*](sql-database-vnet-service-endpoint-rule-overview.md), die auf einem Subnetz basiert, das einen VNET-Dienstendpunkt darstellt. |

> [!TIP]
> Einen Schnellstart zu PowerShell finden Sie unter [Erstellen einer Azure SQL-Einzeldatenbank mithilfe von PowerShell](sql-database-single-database-get-started.md). PowerShell-Beispielskripts finden Sie unter [Verwenden von PowerShell zum Erstellen einer Azure SQL-Einzeldatenbank und Konfigurieren einer Firewallregel](scripts/sql-database-create-and-configure-database-powershell.md) und [Überwachen und Skalieren einer Azure SQL-Einzeldatenbank mithilfe von PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe der Azure CLI

Verwenden Sie zum Erstellen und Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe der [Azure CLI](/cli/azure) die folgenden [Azure CLI-SQL-Datenbank](/cli/azure/sql/db)-Befehle. Führen Sie die CLI mithilfe von [Cloud Shell](/azure/cloud-shell/overview) in Ihrem Browser aus, oder [installieren](/cli/azure/install-azure-cli) Sie sie unter macOS, Linux oder Windows. Informationen zum Erstellen und Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](sql-database-elastic-pool.md).

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

> [!TIP]
> Einen Schnellstart zur Azure-Befehlszeilenschnittstelle finden Sie unter [Erstellen einer Azure SQL-Einzeldatenbank mithilfe der Azure-Befehlszeilenschnittstelle](sql-database-cli-samples.md). Beispielskripts für die Azure-Befehlszeilenschnittstelle finden Sie unter [Verwenden der Befehlszeilenschnittstelle zum Erstellen einer einzelnen Azure SQL-Einzeldatenbank und Konfigurieren einer Firewallregel](scripts/sql-database-create-and-configure-database-cli.md) und [Verwenden der Befehlszeilenschnittstelle zum Überwachen und Skalieren einer Azure SQL-Datenbank-Einzeldatenbank](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe von Transact-SQL

Verwenden Sie zum Erstellen und Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe von Transact-SQL die folgenden T-SQL-Befehle. Sie können diese Befehle mit dem Azure-Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) oder einem beliebigen anderen Programm ausführen, mit dem eine Verbindung mit einem Azure SQL-Datenbank-Server hergestellt und Transact-SQL-Befehle übergeben werden können. Informationen zum Verwalten von Pools für elastische Datenbanken finden Sie unter [Pools für elastische Datenbanken](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Sie können einen Server mithilfe von Transact-SQL nicht erstellen oder löschen.
>

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[CREATE DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/create-database-azure-sql-database)|Erstellt eine neue Datenbank. Sie müssen über eine Verbindung mit der Masterdatenbank verfügen, um eine neue Datenbank erstellen zu können.|
| [ALTER DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/alter-database-azure-sql-database) |Ändert eine Azure SQL-Datenbank. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Ändert ein Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Löscht eine Datenbank.|
|[sys.database_service_objectives (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Gibt die Edition (Dienstebene), das Dienstziel (Tarif) und den Namen des Pools für elastische Datenbanken, falls vorhanden, für eine Azure SQL-Datenbank oder ein Azure SQL Data Warehouse zurück. Wenn eine Anmeldung an der Masterdatenbank in einem Azure SQL-Datenbank-Server besteht, werden Informationen zu allen Datenbanken zurückgegeben. Für Azure SQL Data Warehouse müssen Sie über eine Verbindung mit der Masterdatenbank verfügen.|
|[sys.dm_db_resource_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Gibt die CPU-, E/A- und Arbeitsspeichernutzung für eine Azure SQL-Datenbank zurück. Für alle 15 Sekunden ist eine Zeile vorhanden, selbst wenn keine Aktivität in der Datenbank erfolgt ist.|
|[sys.resource_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Gibt CPU-Nutzungs- und Speicherdaten für eine Azure SQL-Datenbank zurück. Die Daten werden in Intervallen von fünf Minuten gesammelt und aggregiert.|
|[sys.database_connection_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Enthält Statistiken zu Verbindungsereignissen für SQL-Datenbank und eine Übersicht über erfolgreiche und nicht erfolgreiche Datenbankverbindungen. |
|[sys.event_log (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Gibt erfolgreiche Datenbankverbindungen, Verbindungsfehler und Deadlocks für Azure SQL-Datenbank zurück. Sie können diese Informationen nutzen, um die Datenbankaktivität mit SQL-Datenbank nachzuverfolgen oder um Probleme zu beheben.|
|[sp_set_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Erstellt oder aktualisiert die Firewalleinstellungen auf Serverebene für Ihre SQL-Datenbank-Server. Diese gespeicherte Prozedur ist nur in der Masterdatenbank für die Prinzipalanmeldung auf Serverebene verfügbar. Eine Firewallregel auf Serverebene kann erst mithilfe von Transact-SQL erstellt werden, nachdem die erste Firewallregel auf Serverebene von einem Benutzer mit Azure-Berechtigungen erstellt wurde.|
|[sys.firewall_rules (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Gibt Informationen zu den Firewalleinstellungen auf Serverebene im Zusammenhang mit Ihrer Microsoft Azure SQL-Datenbank zurück.|
|[sp_delete_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Entfernt Firewalleinstellungen auf Serverebene von Ihrem SQL-Datenbank-Server. Diese gespeicherte Prozedur ist nur in der Masterdatenbank für die Prinzipalanmeldung auf Serverebene verfügbar.|
|[sp_set_database_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Erstellt oder aktualisiert die Firewallregeln auf Datenbankebene für Ihre Azure SQL-Datenbank oder SQL Data Warehouse. Datenbank-Firewallregeln können für die Masterdatenbank und für Benutzerdatenbanken in SQL-Datenbank konfiguriert werden. Datenbank-Firewallregeln sind bei der Verwendung von Benutzern eigenständiger Datenbanken nützlich. |
|[sys.database_firewall_rules (Azure SQL-Datenbank)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Gibt Informationen zu den Firewalleinstellungen auf Datenbankebene im Zusammenhang mit Ihrer Microsoft Azure SQL-Datenbank zurück. |
|[sp_delete_database_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Entfernt die Firewalleinstellung auf Datenbankebene von Ihrer Azure SQL-Datenbank oder SQL Data Warehouse. |

> [!TIP]
> Eine Schnellstartanleitung mit SQL Server Management Studio unter Microsoft Windows finden Sie unter [Azure SQL-Datenbank: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](sql-database-connect-query-ssms.md). Einen Schnellstart mit Visual Studio Code unter macOS, Linux oder Windows finden Sie unter [Azure SQL-Datenbank: Verwenden von Visual Studio Code zum Herstellen einer Verbindung mit und Abfragen von Daten](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls mithilfe der REST-API

Verwenden Sie zum Erstellen und Verwalten von Azure SQL-Servern, -Datenbanken und -Firewalls diese REST-API-Anforderungen.

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Servers - Create oder Update](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Erstellt oder aktualisiert einen neuen Server.|
|[Servers - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|Löscht eine SQL Server-Instanz.|
|[Servers - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Ruft einen Server ab.|
|[Servers - List](https://docs.microsoft.com/rest/api/sql/servers/list)|Gibt eine Serverliste zurück.|
|[Servers - List by resource group](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Gibt eine Liste aller Server in einer Ressourcengruppe zurück.|
|[Server - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualisiert einen vorhandenen Server.|
|[Databases - Create oder update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Erstellt eine neue Datenbank oder aktualisiert eine bereits vorhandene Datenbank|
|[Datenbanken – Löschen](https://docs.microsoft.com/rest/api/sql/databases/delete)|Löscht eine Datenbank.|
|[Datenbanken – Abrufen](https://docs.microsoft.com/rest/api/sql/databases/get)|Ruft eine Datenbank ab|
|[Databases - List by elastic pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Gibt eine Liste der Datenbanken in einem Pool für elastische Datenbanken zurück.|
|[Databases - List by server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Gibt eine Liste der Datenbanken auf einem Server zurück|
|[Datenbanken – Aktualisieren](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualisiert eine vorhandene Datenbank|
|[Firewall rules - Create oder update](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Erstellt oder aktualisiert eine Firewallregel.|
|[Firewall rules - Delete](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Löscht eine Firewallregel.|
|[Firewall rules - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Ruft eine Firewallregel ab.|
|[Firewall rules - List by server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Gibt eine Liste von Firewallregeln zurück.|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure finden Sie unter [Migrieren zu Azure SQL-Datenbank](sql-database-single-database-migrate.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](sql-database-features.md).
