---
title: Wiederherstellen einer Datenbank aus einer Sicherung
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Hier erfahren Sie mehr über die Point-in-Time-Wiederherstellung, mit der Sie ein Rollback von bis zu 35 Tagen für eine Datenbank in Azure SQL-Datenbank oder eine Instanz in Azure SQL Managed Instance durchführen können.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 138cd03989008aa4fbd7d6ca3e06aff94fa76e33
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989444"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Azure SQL-Datenbank und SQL Managed Instance: Wiederherstellen automatisierter Datenbanksicherungen
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Die folgenden Optionen stehen für die Datenbankwiederherstellung mithilfe [automatisierter Datenbanksicherungen](automated-backups-overview.md) zur Verfügung. Ihre Möglichkeiten:

- Sie können eine neue Datenbank auf demselben Server erstellen, die zu einem bestimmten Zeitpunkt innerhalb des Aufbewahrungszeitraums wiederhergestellt werden kann.
- Sie können eine Datenbank auf demselben Server erstellen, die zum Löschzeitpunkt einer gelöschten Datenbank wiederhergestellt werden kann.
- Sie können eine neue Datenbank auf einem beliebigen Server in derselben Region erstellen, die zum Zeitpunkt der aktuellsten Sicherungen wiederhergestellt werden kann.
- Sie können eine neue Datenbank auf einem beliebigen Server in einer beliebigen anderen Region erstellen, die zum Zeitpunkt der aktuellsten replizierten Sicherungen wiederhergestellt werden kann.

Wenn Sie die [langfristige Aufbewahrung von Sicherungen](long-term-retention-overview.md) konfiguriert haben, können Sie auch eine neue Datenbank aus jeder Langzeitaufbewahrungssicherung auf einem beliebigen Server erstellen.

> [!IMPORTANT]
> Sie können eine vorhandene Datenbank während der Wiederherstellung nicht überschreiben.

Wenn Sie die Dienstebene „Standard“ oder „Premium“ verwenden, fallen bei der Wiederherstellung Ihrer Datenbanken möglicherweise zusätzliche Speicherkosten an. Die zusätzlichen Kosten entstehen, wenn die maximale Größe der wiederhergestellten Datenbank die mit der Dienstebene und Leistungsstufe der Zieldatenbank verbundene Menge des Speicherplatzes überschreitet. Ausführliche Informationen zu den Preisen für zusätzlichen Speicherplatz siehe [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/). Wenn die tatsächlich verwendete Speichermenge kleiner als die enthaltene Speichermenge ist, können Sie diese zusätzlichen Kosten durch Festlegen der maximalen Datenbankgröße auf die enthaltene Menge vermeiden.

## <a name="recovery-time"></a>Wiederherstellungszeit

Die Zeit, die zum Wiederherstellen einer Datenbank mit automatisierten Sicherungen benötigt wird, hängt von mehreren Faktoren ab:

- Größe der Datenbank
- Computegröße der Datenbank
- Anzahl der beteiligten Transaktionsprotokolle
- Menge der erneut auszuführenden Aktivitäten, um den Wiederherstellungspunkt wiederherzustellen
- Netzwerkbandbreite, sofern die Wiederherstellung in einer anderen Region erfolgt
- Anzahl der gleichzeitigen Wiederherstellungsanforderungen, die aktuell in der Zielregion verarbeitet werden

Bei einer großen oder sehr aktiven Datenbank kann die Wiederherstellung mehrere Stunden dauern. Bei einem längeren Ausfall in einer Region ist es möglich, dass eine große Anzahl von Geowiederherstellungsanforderungen für die Notfallwiederherstellung initiiert wird. Wenn es viele Anforderungen gibt, kann sich die Wiederherstellungszeit bei einzelnen Datenbanken erhöhen. Der Großteil der Datenbankwiederherstellungen erfolgt in weniger als 12 Stunden.

Für ein einzelnes Abonnement gibt es Einschränkungen hinsichtlich der Anzahl gleichzeitiger Wiederherstellungsanforderungen. Diese Einschränkungen gelten für eine beliebige Kombination von Point-in-Time-Wiederherstellungen, Geowiederherstellungen und Wiederherstellungen aus einer langfristig aufbewahrten Sicherung.

| **Bereitstellungsoption** | **Max. Anzahl gleichzeitiger Anforderungen, die verarbeitet werden** | **Max. Anzahl gleichzeitiger Anforderungen, die übermittelt werden** |
| :--- | --: | --: |
|**Einzeldatenbank (pro Abonnement)**|10|60|
|**Pool für elastische Datenbanken (pro Pool)**|4|200|


Es gibt keine integrierte Methode zum Wiederherstellen des gesamten Servers. Ein Beispiel zum Ausführen dieser Aufgabe finden Sie unter [Azure SQL Database: Full server recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) (Azure SQL-Datenbank: Wiederherstellen des gesamten Servers).

> [!IMPORTANT]
> Zum Durchführen einer Wiederherstellung mit automatisierten Sicherungen müssen Sie Mitglied der Rolle „SQL Server-Mitwirkender“ oder „Mitwirkender für verwaltete SQL-Instanzen“ (je nach Wiederherstellungsziel) im Abonnement, oder Sie müssen der Besitzer des Abonnements sein. Weitere Informationen finden Sie unter [Azure RBAC: Integrierte Rollen](../../role-based-access-control/built-in-roles.md). Sie können das Azure-Portal, PowerShell oder die REST-API zur Wiederherstellung verwenden. Die Nutzung von Transact-SQL ist nicht möglich.

## <a name="point-in-time-restore"></a>Wiederherstellung bis zu einem bestimmten Zeitpunkt

Sie können den Zustand einer eigenständigen, Pool- oder Instanzdatenbank zu einem früheren Zeitpunkt mithilfe von Azure-Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase) oder [REST-API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.) wiederherstellen. Die Anforderung kann jede Dienstebene oder Computegröße für die wiederhergestellte Datenbank angeben. Stellen Sie sicher, dass auf dem Server, auf dem Sie die Datenbank wiederherstellen, ausreichende Ressourcen vorhanden sind. 

Nach Abschluss der Wiederherstellung wird eine neue Datenbank auf demselben Server wie die ursprüngliche Datenbank erstellt. Die wiederhergestellte Datenbank wird zu normalen Preisen basierend auf der Dienstebene und Computegröße in Rechnung gestellt. Kosten entstehen erst, wenn die Datenbankwiederherstellung abgeschlossen ist.

Im Allgemeinen wird beim Wiederherstellen der Datenbank der Zustand zu einem früheren Zeitpunkt wiederhergestellt. Sie können die wiederhergestellte Datenbank als Ersatz für die ursprüngliche Datenbank behandeln oder als Datenquelle zum Aktualisieren der ursprünglichen Datenbank verwenden.

- **Ersetzung der Datenbank**

  Wenn die wiederhergestellte Datenbank als Ersatz für die ursprüngliche Datenbank vorgesehen ist, sollten Sie die ursprüngliche Computegröße und Dienstebene der Datenbank angeben. Sie können die ursprüngliche Datenbank dann umbenennen und der wiederhergestellten Datenbank mit dem T-SQL-Befehl [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) den ursprünglichen Namen geben.

- **Wiederherstellung der Daten**

  Wenn Sie planen, zum Wiederherstellen nach einem Benutzer- oder Anwendungsfehler Daten aus der wiederhergestellten Datenbank abzurufen, müssen Sie ein Datenwiederherstellungsskript schreiben und ausführen, das Daten aus der wiederhergestellten Datenbank extrahiert und auf die ursprüngliche Datenbank anwendet. Der Wiederherstellungsvorgang kann zwar eine ganze Weile dauern, die wiederherstellende Datenbank wird jedoch während des Wiederherstellungsvorgangs in der Datenbankliste angezeigt. Wenn Sie die Datenbank während der Wiederherstellung löschen, wird der Wiederherstellungsvorgang abgebrochen, und es fallen keine Kosten für die Datenbank an, deren Wiederherstellung nicht abgeschlossen wurde.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Point-in-Time-Wiederherstellung mithilfe des Azure-Portals

Sie können die Wiederherstellung einer einzelnen Instanzdatenbank zu einem bestimmten Zeitpunkt auf dem Blatt „Übersicht“ der Datenbank ausführen, die im Azure-Portal wiederhergestellt werden soll.

#### <a name="sql-database"></a>SQL-Datenbank

Um über das Azure-Portal den Zustand einer Datenbank zu einem bestimmten Zeitpunkt wiederherzustellen, öffnen Sie die Datenbank-Übersichtsseite, und wählen Sie auf der Symbolleiste die Option **Wiederherstellen** aus. Wählen Sie die Sicherungsquelle aus, und wählen Sie den Sicherungspunkt-Zeitpunkt aus, von dem aus eine neue Datenbank erstellt wird.

  ![Screenshot der Datenbankwiederherstellungsoptionen für SQL-Datenbank](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Um eine Datenbank der verwalteten Instanz mithilfe von Azure-Portal auf den Zustand zu einem bestimmten Zeitpunkt wiederherzustellen, öffnen Sie die Datenbank-Übersichtsseite, und wählen Sie auf der Symbolleiste die Option **Wiederherstellen** aus. Wählen Sie den Sicherungspunkt-Zeitpunkt aus, von dem aus eine neue Datenbank erstellt wird.

  ![Screenshot der Datenbankwiederherstellungsoptionen für eine verwaltete SQL-Instanz](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Informationen zum programmgesteuerten Wiederherstellen einer Datenbank aus einer Sicherung finden Sie unter [Programmgesteuerte Wiederherstellung mit automatisierten Sicherungen](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Wiederherstellen einer gelöschten Datenbank

Sie können eine gelöschte Datenbank auf demselben Server oder in der gleichen verwalteten Instanz auf den Zustand zum Zeitpunkt der Löschung oder früher wiederherstellen. Hierzu können Sie das Azure-Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase) oder [REST (createMode=Restore)](/rest/api/sql/databases/createorupdate) verwenden. Die Wiederherstellung einer gelöschten Datenbank erfolgt durch das Erstellen einer neuen Datenbank aus der Sicherung.

> [!IMPORTANT]
> Wenn Sie einen Server oder eine verwaltete Instanz löschen, werden alle enthaltenen Datenbanken ebenfalls gelöscht, und sie können nicht wiederhergestellt werden. Ein gelöschter Server oder eine gelöschte verwaltete Instanz kann nicht wiederhergestellt werden.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Wiederherstellen einer gelöschten Datenbank im Azure-Portal

Die Wiederherstellung gelöschter Datenbanken über das Azure-Portal erfolgt aus der Ressource für den Server oder die verwaltete Instanz.

> [!TIP]
> Es kann einige Minuten dauern, bis kürzlich gelöschte Datenbanken auf der Seite **Gelöschte Datenbanken** im Azure-Portal angezeigt werden, oder wenn gelöschte Datenbanken [programmgesteuert](#programmatic-recovery-using-automated-backups) angezeigt werden.

#### <a name="sql-database"></a>SQL-Datenbank

Um über das Azure-Portal den Zustand einer gelöschten Datenbank zum Löschzeitpunkt wiederherzustellen, öffnen Sie die Serverübersichtsseite, und wählen Sie **Gelöschte Datenbanken** aus. Wählen Sie eine gelöschte Datenbank aus, die Sie wiederherstellen möchten, und geben Sie den Namen für die neue Datenbank ein, die mit den aus der Sicherung wiederhergestellten Daten erstellt wird.

  ![Screenshot: Wiederherstellen einer gelöschten Datenbank](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Öffnen Sie zum Wiederherstellen einer verwalteten Datenbank mithilfe des Azure-Portals die Übersichtsseite der verwalteten Instanzen, und wählen Sie **Gelöschte Datenbanken** aus. Wählen Sie eine gelöschte Datenbank aus, die Sie wiederherstellen möchten, und geben Sie den Namen für die neue Datenbank ein, die mit den aus der Sicherung wiederhergestellten Daten erstellt wird.

  ![Screenshot: Wiederherstellen einer gelöschten Azure SQL Managed Instance-Datenbank](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Wiederherstellen einer gelöschten Datenbank mithilfe von PowerShell

Verwenden Sie die folgenden Beispielskripts mithilfe von PowerShell zum Wiederherstellen einer gelöschten Datenbank für SQL-Datenbank oder SQL Managed Instance.

#### <a name="sql-database"></a>SQL-Datenbank

Ein PowerShell-Beispielskript, das zeigt, wie eine gelöschte Datenbank in Azure SQL-Datenbank wiederhergestellt wird, finden Sie unter [Wiederherstellen einer gelöschten Datenbank mithilfe von PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Ein PowerShell-Beispielskript, das zeigt, wie eine gelöschte Instanzdatenbank wiederhergestellt wird, finden Sie unter [Wiederherstellen einer gelöschten Instanzdatenbank mithilfe von PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database).

> [!TIP]
> Informationen zum programmgesteuerten Wiederherstellen einer gelöschten Datenbank finden Sie unter [Programmgesteuerte Wiederherstellung mit automatisierten Sicherungen](recovery-using-backups.md).

## <a name="geo-restore"></a>Geowiederherstellung

> [!IMPORTANT]
> Geowiederherstellung ist nur für SQL-Datenbanken oder verwaltete Instanzen verfügbar, die mit einem georedundanten [Sicherungsspeicher](automated-backups-overview.md#backup-storage-redundancy) konfiguriert wurden.

Sie können eine Datenbank auf einem SQL-Datenbank-Server oder eine Instanzdatenbank einer beliebigen verwalteten Instanz in einer beliebigen Azure-Region aus den aktuellsten georeplizierten Sicherungen wiederherstellen. Geowiederherstellung verwendet eine georeplizierte Sicherung als Quelle. Sie können die Geowiederherstellung auch dann anfordern, wenn aufgrund eines Ausfalls kein Zugriff auf Datenbank oder Rechenzentrum möglich ist.

Die Geowiederherstellung ist die Standardoption für die Wiederherstellung, wenn Ihre Datenbank aufgrund eines Vorfalls in der Hostingregion nicht verfügbar ist. Sie können die Datenbank auf einem Server in einer beliebigen anderen Region wiederherstellen. Es gibt eine Verzögerung zwischen der Erstellung einer Sicherung und der Georeplikation in einem Azure-Blob in einer anderen Region. Daher kann die wiederhergestellte Datenbank bis zu einer Stunde hinter der ursprünglichen Datenbank zurückliegen. Die folgende Abbildung zeigt eine Datenbankwiederherstellung aus der letzten verfügbaren Sicherung in einer anderen Region.

![Darstellung der Geowiederherstellung](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Durchführen einer Geowiederherstellung über das Azure-Portal

Erstellen Sie im Azure-Portal eine neue Einzeldatenbank oder Datenbank der verwalteten Instanz, und wählen Sie eine verfügbare Sicherung für die Geowiederherstellung aus. Die neu erstellte Datenbank enthält die mittels Geowiederherstellung wiederhergestellten Sicherungsdaten.

#### <a name="sql-database"></a>SQL-Datenbank

Wenn Sie eine Einzeldatenbank mittels Geowiederherstellung aus dem Azure-Portal in der Region und auf dem Server Ihrer Wahl wiederherstellen möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie im **Dashboard** die Option **Hinzufügen** > **SQL-Datenbank erstellen** aus. Geben Sie auf der Registerkarte **Grundeinstellungen** die erforderlichen Informationen ein.
2. Wählen Sie **Zusätzliche Einstellungen** aus.
3. Wählen Sie unter **Vorhandene Daten verwenden** die Option **Sicherung** aus.
4. Wählen Sie unter **Sicherung** in der Liste der verfügbaren Geowiederherstellungssicherungen eine Sicherung aus.

    ![Screenshot der Optionen für „SQL-Datenbank erstellen“](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Schließen Sie den Vorgang des Erstellens einer neuen Datenbank aus der Sicherung ab. Wenn Sie eine Datenbank in Azure SQL-Datenbank erstellen, enthält diese die mittels Geowiederherstellung wiederhergestellte Sicherung.

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Wenn Sie eine Datenbank der verwalteten Instanz mittels Geowiederherstellung aus Azure-Portal auf einer vorhandenen verwalteten Instanz in einer Region Ihrer Wahl wiederherstellen möchten, wählen Sie die verwaltete Instanz aus, auf der die Datenbank wiederhergestellt werden soll. Folgen Sie diesen Schritten:

1. Wählen Sie **Neue Datenbank** aus.
2. Geben Sie den gewünschten Datenbanknamen ein.
3. Wählen Sie unter **Vorhandene Daten verwenden** die Option **Sicherung** aus.
4. Wählen Sie in der Liste der verfügbaren Geowiederherstellungssicherungen eine Sicherung aus.

    ![Screenshot der Optionen für „Neue Datenbank“](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Schließen Sie den Vorgang des Erstellens einer neuen Datenbank ab. Die erstellte Instanzdatenbank enthält die mittels Geowiederherstellung wiederhergestellte Sicherung.

### <a name="geo-restore-by-using-powershell"></a>Durchführen einer Geowiederherstellung mithilfe von PowerShell

#### <a name="sql-database"></a>SQL-Datenbank

Ein PowerShell-Skript, das zeigt, wie die Geowiederherstellung für eine Azure Einzeldatenbank durchgeführt wird, finden Sie unter [Wiederherstellen einer Einzeldatenbank auf den Zustand zu einem früheren Zeitpunkt mithilfe von PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Ein PowerShell-Skript, das zeigt, wie die Geowiederherstellung für eine Datenbank der verwalteten Instanz durchgeführt wird, finden Sie unter [Wiederherstellen einer Datenbank der verwalteten Instanz in einer anderen geografischen Region mithilfe von PowerShell](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Überlegungen zur Geowiederherstellung

Sie können keine Point-in-Time-Wiederherstellung für eine sekundäre Geodatenbank durchführen. Dies ist nur für eine primäre Datenbank möglich. Ausführliche Informationen zum Verwenden der Geowiederherstellung nach einem Ausfall finden Sie unter [Wiederherstellen nach einem Ausfall](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> Die Geowiederherstellung ist die einfachste in SQL-Datenbank und SQL Managed Instance verfügbare Lösung für die Notfallwiederherstellung. Sie beruht auf automatisch erstellten georeplizierten Sicherungen mit einem RPO (Recovery Point Objective) von bis zu 1 Stunde und einer geschätzten Wiederherstellungszeit von bis zu 12 Stunden. Sie garantiert nicht, dass die Zielregion über die Kapazität zum Wiederherstellen Ihrer Datenbanken nach einem regionalen Ausfall verfügt, da der Bedarf voraussichtlich stark ansteigen wird. Für nicht unternehmenskritische Anwendungen, die relativ kleine Datenbanken verwenden, ist die Geowiederherstellung eine geeignete Lösung für die Notfallwiederherstellung. 
>
> Verwenden Sie für unternehmenskritische Anwendungen, die große Datenbanken erfordern und die Geschäftskontinuität sicherstellen müssen, [Autofailover-Gruppen](auto-failover-group-overview.md). Die Lösung bietet erhebliche niedrigere RPO (Recovery Point Objective) und RTO (Recovery Time Objective), und die Kapazität ist immer garantiert. 
>
> Weitere Informationen zu Optionen für Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Programmgesteuerte Wiederherstellung mit automatisierten Sicherungen

Sie können auch Azure PowerShell oder die REST-API für die Wiederherstellung verwenden. Die folgenden Tabellen beschreiben den verfügbaren Satz von Befehlen.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von SQL-Datenbank und SQL Managed Instance weiterhin unterstützt, alle zukünftigen Entwicklungen erfolgen jedoch für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den Azure Resource Manager-Modulen sind in großen Teilen identisch.

#### <a name="sql-database"></a>SQL-Datenbank

Wie Sie eine eigenständige Datenbank oder in einem Pool zusammengefasste Datenbank wiederherstellen, erfahren Sie unter [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | BESCHREIBUNG |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ruft mindestens eine Datenbank ab. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ruft eine gelöschte Datenbank ab, die Sie wiederherstellen können. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Ruft eine georedundante Sicherung einer Datenbank ab. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Stellt eine Datenbank wieder her. |

  > [!TIP]
  > Ein PowerShell-Beispielskript, das zeigt, wie eine Point-in-Time-Wiederherstellung einer Datenbank durchgeführt wird, finden Sie unter [Wiederherstellen einer Datenbank mithilfe von PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Wie Sie die Datenbank einer verwalteten Instanz wiederherstellen, erfahren Sie unter [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | BESCHREIBUNG |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Ruft eine oder mehrere verwaltete Instanzen ab. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Ruft Instanzdatenbanken ab. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Stellt eine Instanzdatenbank wieder her. |

### <a name="rest-api"></a>REST-API

So stellen Sie eine Datenbank mithilfe der REST-API wieder her:

| API | BESCHREIBUNG |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Stellt eine Datenbank wieder her. |
| [Get Create or Update Database Status](/rest/api/sql/operations) |Ruft den Status während eines Wiederherstellungsvorgangs ab. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL-Datenbank

Eine Erläuterung zum Wiederherstellen einer Datenbank mithilfe der Azure CLI finden Sie unter [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>Verwaltete SQL-Instanz

Eine Erläuterung zum Wiederherstellen einer Datenbank der verwalteten Instanz mithilfe der Azure CLI finden Sie unter [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Zusammenfassung

Automatische Sicherungen schützen Ihre Datenbanken vor Benutzer- und Anwendungsfehlern, versehentlichen Datenbanklöschungen und längeren Ausfällen. Diese integrierte Funktion ist für alle Dienstebenen und Computegrößen verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Übersicht: Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md)
- [Langfristige Aufbewahrung](long-term-retention-overview.md)
- Informationen zu schnelleren Wiederherstellungsoptionen finden Sie unter [Aktive Georeplikation](active-geo-replication-overview.md) oder [Autofailover-Gruppen](auto-failover-group-overview.md).