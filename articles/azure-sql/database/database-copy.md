---
title: Kopieren einer Datenbank
description: Erstellen Sie eine transaktionskonsistente Kopie einer vorhandenen Datenbank in Azure SQL-Datenbank auf demselben oder einem anderen Server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: ''
ms.date: 10/30/2020
ms.openlocfilehash: 53e62d790514bd3fb5bef93788fa78944db28c2c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127738"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopieren einer transaktionskonsistenten Kopie einer Datenbank in Azure SQL-Datenbank

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL-Datenbank bietet verschiedene Methoden, um eine Kopie einer vorhandenen [Datenbank](single-database-overview.md) auf demselben Server oder auf einem anderen Server zu erstellen. Sie können eine Datenbank über das Azure-Portal, PowerShell, die Azure CLI oder T-SQL kopieren.

## <a name="overview"></a>Übersicht

Bei einer Datenbankkopie handelt es sich um eine im Hinblick auf Transaktionen konsistente Momentaufnahme der Quelldatenbank zu einem Zeitpunkt, nachdem die Kopieranforderung initiiert wurde. Sie können denselben Server oder einen anderen Server für die Kopie auswählen. Außerdem können Sie die Sicherungsredundanz, die Dienstebene und die Computegröße der Quelldatenbank beibehalten oder eine andere Sicherungsspeicherredundanz und/oder Computegröße innerhalb derselben oder einer anderen Dienstebene verwenden. Nachdem der Kopiervorgang abgeschlossen ist, wird die Kopie zu einer voll funktionsfähigen, unabhängigen Datenbank. Die Anmeldungen, Benutzer und Berechtigungen in der kopierten Datenbank werden unabhängig von der Quelldatenbank verwaltet. Die Kopie wird mithilfe der Georeplikationstechnik erstellt. Sobald das Replikat-Seeding abgeschlossen ist, wird der Georeplikationslink automatisch beendet. Alle Anforderungen zur Nutzung der Georeplikation gelten für den Datenbank-Kopiervorgang. Weitere Informationen finden Sie unter [Aktive Georeplikation – Übersicht](active-geo-replication-overview.md).

> [!NOTE]
> Die konfigurierbare Sicherungsspeicherredundanz von Azure SQL-Datenbank ist zurzeit nur in der Azure-Region „Brasilien, Süden“ als Public Preview und in der Region „Asien, Südosten“ allgemein verfügbar. Wird die Quelldatenbank mit lokal redundanter oder zonenredundanter Sicherungsspeicherredundanz erstellt, wird in der Vorschauversion das Kopieren einer Datenbank auf einen Server in einer anderen Azure-Region nicht unterstützt. 

## <a name="logins-in-the-database-copy"></a>Anmeldungen in der Datenbankkopie

Wenn Sie eine Datenbank auf denselben Server kopieren, können für beide Datenbanken dieselben Anmeldedaten verwendet werden. Der Sicherheitsprinzipal, den Sie zum Kopieren der Datenbank verwenden, wird zum Datenbankbesitzer der neuen Datenbank.

Wenn Sie eine Datenbank auf einen anderen Server kopieren, wird der Sicherheitsprinzipal, der den Kopiervorgang auf den Zielserver initiiert hat, zum Besitzer der neuen Datenbank.

Ungeachtet des Zielservers werden alle Datenbankbenutzer, ihre Berechtigungen und ihre Sicherheits-IDs (SIDs) in die Datenbankkopie kopiert. Durch die Verwendung von [eigenständigen Datenbankbenutzern](logins-create-manage.md) für den Datenzugriff stellen Sie sicher, dass die kopierte Datenbank die gleichen Benutzeranmeldeinformationen verwendet, sodass sofort nach Abschluss des Kopiervorgang ein Zugriff mit denselben Anmeldeinformationen möglich ist.

Wenn Sie Anmeldungen auf Serverebene für den Datenzugriff verwenden und die Datenbank auf einen anderen Server kopieren, funktioniert der anmeldungsbasierte Zugriff möglicherweise nicht. Dies kann der Fall sein, wenn Anmeldungen auf dem Zielserver nicht vorhanden oder Kennwörter und Sicherheits-IDs (SIDs) unterschiedlich sind. Informationen zum Verwalten von Anmeldungen beim Kopieren einer Datenbank auf einen anderen Server finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md). Nach einem erfolgreichen Kopiervorgang auf einen anderen Server kann sich, bevor andere Benutzer neu zugeordnet werden, nur die dem Datenbankbesitzer zugeordnete Anmeldung oder der Serveradministrator bei der kopierten Datenbank anmelden. Informationen zum Auflösen von Anmeldungen nach Abschluss des Kopiervorgangs finden Sie unter [Auflösen von Anmeldungen](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Kopieren mithilfe des Azure-Portals

Um eine Datenbank über das Azure-Portal zu kopieren, öffnen Sie die Seite für Ihre Datenbank, und klicken Sie auf **Kopieren**.

   ![Datenbankkopie](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopieren mithilfe von PowerShell oder der Azure CLI

Verwenden Sie die folgenden Beispiele, wenn Sie eine Datenbank kopieren möchten.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie in PowerShell das Cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy).

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen.  Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Der Datenbank-Kopiervorgang ist ein asynchroner Vorgang, aber die Zieldatenbank wird sofort erstellt, nachdem die Anforderung akzeptiert wurde. Wenn Sie den Kopiervorgang abbrechen müssen, während er noch ausgeführt wird, sollten Sie die Zieldatenbank mit dem Cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) löschen.

Ein vollständiges PowerShell-Beispielskript finden Sie unter [Kopieren einer Datenbank auf einen neuen Server](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Der Datenbank-Kopiervorgang ist ein asynchroner Vorgang, aber die Zieldatenbank wird sofort erstellt, nachdem die Anforderung akzeptiert wurde. Wenn Sie den Kopiervorgang während der Ausführung abbrechen müssen, löschen Sie die Zieldatenbank mit dem Befehl [az sql db delete](/cli/azure/sql/db#az-sql-db-delete).

* * *

## <a name="copy-using-transact-sql"></a>Kopieren mithilfe von Transact-SQL

Melden Sie sich mit der Serveradministratoranmeldung oder der Anmeldung, mit der die zu kopierende Datenbank erstellt wurde, bei der Masterdatenbank an. Damit der Datenbankkopiervorgang funktioniert, müssen alle Anmeldungen, die kein Serveradministrator sind, Mitglieder der Rolle `dbmanager` sein. Weitere Informationen zu Anmeldungen und zum Herstellen einer Verbindung mit dem Server finden Sie unter [Verwalten von Anmeldungen](logins-create-manage.md).

Starten Sie das Kopieren der Quelldatenbank mit der Anweisung [CREATE DATABASE ... AS COPY OF](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database). Die T-SQL-Anweisung wird weiter ausgeführt, bis der Datenbankkopiervorgang beendet ist.

> [!NOTE]
> Durch das Beenden der T-SQL-Anweisung wird der Datenbankkopiervorgang nicht beendet. Um den Vorgang zu beenden, müssen Sie die Zieldatenbank löschen.
>

> [!IMPORTANT]
> Das Auswählen einer Sicherungsspeicherredundanz bei Verwendung des Befehls „T-SQL CREATE DATABASE ... AS COPY OF“ wird noch nicht unterstützt. 

### <a name="copy-to-the-same-server"></a>Kopieren auf denselben Server

Melden Sie sich mit der Serveradministratoranmeldung oder der Anmeldung, mit der die zu kopierende Datenbank erstellt wurde, bei der Masterdatenbank an. Damit der Datenbankkopiervorgang funktioniert, müssen alle Anmeldungen, die kein Serveradministrator sind, Mitglieder der `dbmanager`-Rolle sein.

Mit diesem Befehl wird Database1 in eine neue Datenbank mit dem Namen „Database2“ auf demselben Server kopiert. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Kopieren in einen Pool für elastische Datenbanken

Melden Sie sich mit der Serveradministratoranmeldung oder der Anmeldung, mit der die zu kopierende Datenbank erstellt wurde, bei der Masterdatenbank an. Damit der Datenbankkopiervorgang funktioniert, müssen alle Anmeldungen, die kein Serveradministrator sind, Mitglieder der `dbmanager`-Rolle sein.

Dieser Befehl kopiert „Database1“ in eine neue Datenbank namens „Database2“ in einen Pool für elastische Datenbanken namens „pool1“. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.

„Database1“ kann eine einzelne oder eine Pooldatenbank sein. Das Kopieren zwischen verschiedenen Ebenenpools wird zwar unterstützt, aber einige ebenenübergreifende Kopien werden nicht erfolgreich sein. Sie können z. B. eine einzelne oder eine Datenbank vom Typ „Elastisch Standard“ in einen universellen Pool kopieren, aber Sie können keine Datenbank vom Typ „Elastisch Standard“ in einen Pool vom Typ „Premium“ kopieren. 

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) ) ;
   ```

### <a name="copy-to-a-different-server"></a>Kopieren auf einen anderen Server

Melden Sie sich auf dem Zielserver, auf dem die neue Datenbank erstellt werden soll, bei der Masterdatenbank an. Verwenden Sie eine Anmeldung mit dem Namen und dem Kennwort des Datenbankbesitzers der Quelldatenbank auf dem Quellserver. Bei der Anmeldung auf dem Zielserver muss es sich zudem um ein Mitglied der `dbmanager`-Rolle oder die Serveradministratoranmeldung handeln.

Mit diesem Befehl wird Database1 auf server1 in eine neue Datenbank mit dem Namen „Database2“ auf server2 kopiert. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Die Firewalls beider Server müssen so konfiguriert werden, dass sie eingehende Verbindungen von der IP-Adresse des Clients zulassen, der den T-SQL-Befehl „CREATE DATABASE ... AS COPY OF“ ausgibt.

### <a name="copy-to-a-different-subscription"></a>Kopieren in ein anderes Abonnement

Sie können Ihre Datenbank mit den im Abschnitt [Kopieren einer SQL-Datenbank auf einen anderen Server](#copy-to-a-different-server) beschriebenen Schritten und mithilfe von T-SQL auf einen Server in einem anderen Abonnement kopieren. Stellen Sie sicher, dass Sie eine Anmeldung mit dem Namen und dem Kennwort des Datenbankbesitzers der Quelldatenbank auf dem Quellserver verwenden. Außerdem muss es sich bei der Anmeldung sowohl auf dem Quell- als auch dem Zielserver um ein Mitglied der `dbmanager`-Rolle oder einen Serveradministrator handeln.

> [!NOTE]
> Das [Azure-Portal](https://portal.azure.com), PowerShell und die Azure CLI bieten keine Unterstützung für das Kopieren von Datenbanken in ein anderes Abonnement.

> [!TIP]
> Die Datenbankkopie, die T-SQL verwendet, unterstützt das Kopieren einer Datenbank aus einem Abonnement in einem anderen Azure-Mandanten. Dies wird nur unterstützt, wenn für die Anmeldung am Zielserver eine SQL-Authentifizierungsanmeldung verwendet wird.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Überwachen des Fortschritts des Kopiervorgangs

Überwachen Sie den Kopiervorgang, indem Sie die Ansichten [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database) und [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) abfragen. Während des Kopiervorgangs wird die Spalte **state_desc** der Ansicht „sys.databases“ für die neue Datenbank auf **COPYING** gesetzt.

* Wenn beim Kopieren ein Fehler auftritt, wird die Spalte **state_desc** der Ansicht „sys.databases“ für die neue Datenbank auf **SUSPECT** gesetzt. Führen Sie die DROP-Anweisung in der neuen Datenbank aus, und wiederholen Sie den Vorgang später noch einmal.
* Wenn der Kopiervorgang erfolgreich ist, wird die Spalte **state_desc** der Ansicht „sys.databases“ für die neue Datenbank auf **ONLINE** gesetzt. Der Kopiervorgang ist abgeschlossen, und die neue Datenbank ist eine normale Datenbank, die unabhängig von der Quelldatenbank geändert werden kann.

> [!NOTE]
> Wenn Sie den Kopiervorgang während der Ausführung abbrechen möchten, führen Sie die Anweisung [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) für die neue Datenbank aus.

> [!IMPORTANT]
> Falls Sie eine Kopie mit einem deutlich niedrigeren Dienstziel als die Quelle erstellen müssen, verfügt die Zieldatenbank ggf. nicht über genügend Ressourcen für die Durchführung des Seedingprozesses. Möglicherweise schlägt dadurch der Kopiervorgang fehl. Verwenden Sie in diesem Szenario eine Anforderung einer Geowiederherstellung, um eine Kopie auf einem anderen Server bzw. in einer anderen Region zu erstellen. Weitere Informationen finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank mit Datenbanksicherungen](recovery-using-backups.md#geo-restore).

## <a name="azure-roles-to-manage-database-copy"></a>Azure-Rollen zum Verwalten der Datenbankkopie

Zum Erstellen einer Datenbankkopie müssen Sie über die folgenden Rollen verfügen:

* Besitzer des Abonnements
* Rolle „Mitwirkender von SQL Server“ oder
* Benutzerdefinierte Rolle für die Quell- und Zieldatenbank mit folgender Berechtigung:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Zum Abbrechen eines Datenbank-Kopiervorgangs müssen Sie über die folgenden Rollen verfügen:

* Besitzer des Abonnements
* Rolle „Mitwirkender von SQL Server“ oder
* Benutzerdefinierte Rolle für die Quell- und Zieldatenbank mit folgender Berechtigung:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Zum Verwalten der Datenbankkopie über das Azure-Portal benötigen Sie außerdem die folgenden Berechtigungen:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Wenn Sie die Vorgänge unter Bereitstellungen in der Ressourcengruppe im Portal anzeigen möchten, und zwar Vorgänge über mehrere Ressourcenanbieter hinweg einschließlich SQL-Vorgängen, benötigen Sie diese zusätzlichen Azure-Rollen:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Auflösen von Anmeldungen

Nachdem die neue Datenbank auf dem Zielserver online ist, können Sie die [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true)-Anweisung verwenden, um die Benutzer aus der neuen Datenbank den Anmeldungen auf dem Zielserver neu zuzuordnen. Informationen zum Auflösen von verwaisten Benutzern finden Sie unter [Problembehandlung bei verwaisten Benutzern](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Siehe auch [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).

Für alle Benutzer werden in der neuen Datenbank die Berechtigungen beibehalten, über die sie auch in der Quelldatenbank verfügt haben. Der Benutzer, der das Kopieren der Datenbank initiiert hat, wird zum Datenbankbesitzer der neuen Datenbank. Nachdem der Kopiervorgang erfolgreich abgeschlossen wurde und bevor andere Benutzer neu zugeordnet werden, kann sich nur der Datenbankbesitzer bei der neuen Datenbank anmelden.

Informationen zum Verwalten von Benutzern und Anmeldungen beim Kopieren einer Datenbank auf einen anderen Server finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Fehler beim Kopieren von Datenbanken

Die folgenden Fehler können beim Kopieren einer Datenbank in Azure SQL-Datenbank auftreten. Weitere Informationen finden Sie unter [Kopieren einer Azure SQL-Datenbank](database-copy.md).

| Fehlercode | severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| 40635 |16 |Der Client mit der IP-Adresse „%.&#x2a;ls“ ist vorübergehend deaktiviert. |
| 40637 |16 |Das Kopieren von Datenbanken ist derzeit deaktiviert. |
| 40561 |16 |Fehler beim Kopieren der Datenbank. Die Quell- oder die Zieldatenbank ist nicht vorhanden. |
| 40562 |16 |Fehler beim Kopieren der Datenbank. Die Quelldatenbank wurde gelöscht. |
| 40563 |16 |Fehler beim Kopieren der Datenbank. Die Zieldatenbank wurde gelöscht. |
| 40564 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40565 |16 |Fehler beim Kopieren der Datenbank. Es darf jeweils nur eine Datenbankkopie von derselben Quelle erstellt werden. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut. |
| 40566 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40567 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40568 |16 |Fehler beim Kopieren der Datenbank. Die Quelldatenbank ist nicht mehr verfügbar. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40569 |16 |Fehler beim Kopieren der Datenbank. Die Zieldatenbank ist nicht mehr verfügbar. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang. |
| 40570 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut. |
| 40571 |16 |Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut. |

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Anmeldungen finden Sie unter [Verwalten von Anmeldungen](logins-create-manage.md) und [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).
* Informationen zum Exportieren einer Datenbank finden Sie unter [Exportieren der Datenbank in eine BACPAC-Datei](database-export.md).
