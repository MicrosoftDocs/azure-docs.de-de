---
title: Upgrade auf die neueste Generation des dedizierten SQL-Pools (früher SQL DW)
description: Führen Sie ein Upgrade des dedizierten Azure Synapse Analytics-SQL-Pools (früher SQL DW) auf die Azure-Hardware und -Speicherarchitektur der neuesten Generation durch.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459252"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Optimieren der Leistung durch ein Upgrade des dedizierten SQL-Pools (früher SQL DW) in Azure Synapse Analytics

Führen Sie ein Upgrade Ihres dedizierten SQL-Pools (früher SQL DW) auf die Azure-Hardware und -Speicherarchitektur der neuesten Generation durch.

## <a name="why-upgrade"></a>Gründe für ein Upgrade

In [unterstützten Regionen](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) können Sie jetzt im Azure-Portal ein Upgrade auf die Stufe „Optimiert für Compute Gen2“ für dedizierte SQL-Pools (früher SQL DW) nahtlos durchführen. Wenn Ihre Region kein Selbstupgrade unterstützt, können Sie ein Upgrade auf eine unterstützte Region durchführen oder warten, bis das Selbstupgrade in Ihrer Region verfügbar wird. Aktualisieren Sie jetzt, um die neueste Generation der Azure-Hardware sowie eine erweiterte Speicherarchitektur mit schnellerer Leistung, höherer Skalierbarkeit und unbegrenztem spaltenorientiertem Speicher zu nutzen.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Dieses Upgrade gilt in [unterstützten Regionen](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) für dedizierte SQL-Pools (früher SQL DW) der Stufe „Optimiert für Compute Gen1“.

## <a name="before-you-begin"></a>Voraussetzungen

1. Überprüfen Sie, ob Ihre [Region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) für die Migration von GEN1 zu GEN2 unterstützt wird. Beachten Sie die Datumsangaben für die automatische Migration. Um Konflikte mit dem automatisierten Vorgang zu vermeiden, planen Sie Ihre manuelle Migration vor dem Startdatum für den automatisierten Prozess.
2. Wenn Sie sich in einer Region befinden, die noch nicht unterstützt wird, prüfen Sie weiterhin, ob Ihre Region zwischenzeitlich hinzugefügt wurde, oder führen Sie das [Upgrade durch Wiederherstellung](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) in einer unterstützten Region durch.
3. Wenn Ihre Region unterstützt wird, führen Sie das [Upgrade über das Azure-Portal](#upgrade-in-a-supported-region-using-the-azure-portal) durch.
4. Wählen Sie die **vorgeschlagene Leistungsstufe** für den dedizierten SQL-Pool (früher SQL DW) basierend auf Ihrer aktuellen Leistungsstufe für „Optimiert für Compute Gen1“ aus. Verwenden Sie dafür die folgende Zuordnung:

   | Stufe „Optimiert für Compute Gen 1“ | Stufe „Optimiert für Compute Gen 2“ |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Die vorgeschlagenen Leistungsstufen sind kein direkter Wechsel. Beispielsweise empfehlen wir die Umstellung von DW600 auf DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Upgrade in einer unterstützten Region über das Azure-Portal

- Die Migration von Gen1 zu Gen2 über das Azure-Portal ist dauerhaft. Es gibt keinen Vorgang für die Rückkehr zu Gen1.
- Für die Migration zu Gen2 muss der dedizierte SQL-Pool (früher SQL DW) ausgeführt werden.

### <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
- Stellen Sie sicher, dass der dedizierte SQL-Pool (früher SQL DW) ausgeführt wird. Dies ist erforderlich, damit er zu Gen2 migriert werden kann.

### <a name="powershell-upgrade-commands"></a>PowerShell-Upgradebefehle

1. Wenn der zu aktualisierende dedizierte SQL-Pool (früher SQL DW) der Stufe „Optimiert für Compute Gen1“ angehalten wird, [setzen Sie den dedizierten SQL-Pool (früher SQL DW) fort](pause-and-resume-compute-portal.md).

2. Es tritt eine Ausfallzeit von einigen Minuten auf.

3. Identifizieren Sie alle Codeverweise auf die Leistungsebene „Optimiert für Compute Gen 1“, und ändern Sie sie in die entsprechende Leistungsebene „Optimiert für Compute Gen 2“. Nachstehend finden Sie zwei Beispiele, in denen Sie Codeverweise vor dem Upgrade aktualisieren sollten:

   Ursprünglicher PowerShell-Befehl für Gen 1:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Geändert in:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" wird geändert in: RequestedServiceObjectiveName "DW300 **c**"
   >

   Ursprünglicher T-SQL-Befehl für Gen 1:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Geändert in:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = 'DW300' wird geändert in: SERVICE_OBJECTIVE = 'DW300 **c**'

## <a name="start-the-upgrade"></a>Starten des Upgrades

1. Wechseln Sie im Azure-Portal zu Ihrem dedizierten SQL-Pool (früher SQL DW) der Stufe „Optimiert für Compute Gen1“. Wenn der zu aktualisierende dedizierte SQL-Pool (früher SQL DW) der Stufe „Optimiert für Compute Gen1“ angehalten wird, [setzen Sie den dedizierten SQL-Pool fort](pause-and-resume-compute-portal.md).
2. Wählen Sie auf der Registerkarte „Aufgaben“ die Karte **Upgrade auf Gen2** aus: ![Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Wenn die Karte **Upgrade auf Gen2** unter der Registerkarte „Aufgaben“ nicht angezeigt wird, ist Ihr Abonnementtyp in der aktuellen Region beschränkt.
   > [Senden Sie ein Supportticket](sql-data-warehouse-get-started-create-support-ticket.md), um Ihr Abonnement genehmigen zu lassen.

3. Stellen Sie sicher, dass die Ausführung Ihrer Workload abgeschlossen ist und die Workload stillgelegt wurde, bevor Sie das Upgrade durchführen. Bevor Ihr dedizierter SQL-Pool (früher SQL DW) wieder als dedizierter SQL-Pool (früher SQL DW) der Stufe „Optimiert für Compute Gen2“ online geschaltet wird, kommt es zu einer Downtime von einigen Minuten. Wählen Sie das **Upgrade** aus:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Überwachen Sie Ihr Upgrade**, indem Sie den Status im Azure-Portal überprüfen:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Der erste Schritt des Upgradeprozesses erfolgt im Skalierungsvorgang („Upgrade – Offline“), in dem alle Sitzungen gelöscht und Verbindungen getrennt werden.

   Der zweite Schritt des Upgradeprozesses ist die Datenmigration („Upgrade – Online“). Die Datenmigration ist ein nach und nach im Hintergrund ausgeführter Onlineprozess. Bei diesem Vorgang werden spaltenbasierte Daten langsam aus der alten Speicherarchitektur über einen lokalen SSD-Cache in die neue Speicherarchitektur verschoben. Während dieser Zeit ist Ihr dedizierter SQL-Pool (früher SQL DW) für Abfrage- und Ladevorgänge online. Ihre Daten stehen für Abfragen zur Verfügung, unabhängig davon, ob sie bereits migriert wurden oder nicht. Die Datenmigration erfolgt mit wechselnder Geschwindigkeit je nach Größe Ihrer Daten, Ihrer Leistungsstufe und der Anzahl Ihrer Columnstore-Segmente.

5. **Optionale Empfehlung:** Sobald der Skalierungsvorgang abgeschlossen ist, können Sie den Hintergrundvorgang der Datenmigration beschleunigen. Sie können die Datenverschiebung erzwingen, indem Sie [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) in allen primären Columnstore-Tabellen ausführen, die Sie mit einem größeren SLO-Wert und einer größeren Ressourcenklasse abrufen würden. Dieser Vorgang findet **offline** statt, im Gegensatz zum allmählich ausgeführten Hintergrundprozess, der je nach Anzahl und Größe Ihrer Tabellen Stunden dauern kann. Allerdings verläuft die Datenmigration nach Abschluss des Vorgangs möglicherweise viel schneller aufgrund der neuen erweiterten Speicherarchitektur mit hochwertigen Zeilengruppen.

> [!NOTE]
> ALTER INDEX REBUILD ist ein Offlinevorgang, und die Tabellen stehen erst dann zur Verfügung, wenn die Wiederherstellung abgeschlossen ist.

Die folgende Abfrage generiert die erforderlichen ALTER INDEX REBUILD-Befehle, um die Datenmigration zu beschleunigen:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Upgrade über eine geografische Azure-Region mit der Wiederherstellung über das Azure-Portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Erstellen eines benutzerdefinierten Wiederherstellungspunkts im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zu dem dedizierten SQL-Pool (früher SQL DW), für den Sie einen Wiederherstellungspunkt erstellen möchten.

3. Wählen Sie oben im Abschnitt „Übersicht“ die Option **+Neuer Wiederherstellungspunkt** aus.

    ![Neuer Wiederherstellungspunkt](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Geben Sie einen Namen für Ihren Wiederherstellungspunkt an.

    ![Name für den Wiederherstellungspunkt](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Wiederherstellen einer aktiven oder angehaltenen Datenbank im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zu dem dedizierten SQL-Pool (früher SQL DW), mit dem Sie die Wiederherstellung durchführen möchten.
3. Wählen Sie oben im Abschnitt „Übersicht“ die Option **Wiederherstellen** aus.

    ![ Wiederherstellung – Übersicht](./media/upgrade-to-latest-generation/restoring_0.png)

4. Wählen Sie eine der Optionen **Automatische Wiederherstellungspunkte** oder **Benutzerdefinierte Wiederherstellungspunkte** aus. Wenn Sie sich für benutzerdefinierte Wiederherstellungspunkte entschieden haben, **wählen Sie den gewünschten benutzerdefinierten Wiederherstellungspunkt aus**, oder **erstellen Sie einen neuen benutzerdefinierten Wiederherstellungspunkt**. Wählen Sie für den Server **Neu erstellen** und dann einen Server in einer unterstützten geografischen Gen2-Region aus.

    ![Automatische Wiederherstellungspunkte](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Wiederherstellen von einer geografischen Azure-Region aus mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Verwenden Sie das Cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um eine Datenbank wiederherzustellen.

> [!NOTE]
> Sie können eine Geowiederherstellung nach Gen2 durchführen! Geben Sie zu diesem Zweck als optionalen Parameter einen ServiceObjectiveName-Wert für Gen2 ein (z.B. DW1000 **c**).

1. Öffnen Sie Windows PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
4. Rufen Sie die Datenbank ab, die Sie wiederherstellen möchten.
5. Erstellen Sie die Wiederherstellungsanforderung für die Datenbank, indem Sie einen Gen2-ServiceObjectiveName angeben.
6. Überprüfen Sie den Status der mittels Geowiederherstellung wiederhergestellten Datenbank.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Informationen zum Konfigurieren der Datenbank nach Abschluss der Wiederherstellung finden Sie unter [Konfigurieren der Datenbank nach der Wiederherstellung](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

Für die wiederhergestellte Datenbank ist TDE aktiviert, wenn für die Quelldatenbank TDE aktiviert ist.

Wenn bei Ihrem dedizierten SQL-Pool Probleme auftreten, können Sie eine [Supportanfrage](sql-data-warehouse-get-started-create-support-ticket.md) erstellen und als möglichen Grund „Gen2-Upgrade“ angeben.

## <a name="next-steps"></a>Nächste Schritte

Ihr aktualisierter dedizierter SQL-Pool (früher SQL DW) ist online. Informationen zur optimalen Nutzung der erweiterten Architektur finden Sie unter [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md).
