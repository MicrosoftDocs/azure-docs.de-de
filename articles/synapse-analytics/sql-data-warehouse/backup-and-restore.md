---
title: Sicherung und Wiederherstellung – Momentaufnahmen (georedundant)
description: Hier erfahren Sie, wie Sicherung und Wiederherstellung in einem dedizierten Azure Synapse Analytics-SQL-Pool funktionieren. Verwenden Sie Sicherungen, um Ihr Data Warehouse mithilfe eines Wiederherstellungspunkts in der primären Region wiederherzustellen. Mithilfe von georedundanten Sicherungen können Sie eine Wiederherstellung in einer anderen geografischen Region ausführen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 254e07d5a3266927c9677107772b4ac2dbfd2ce0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454398"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Sichern und Wiederherstellen in einem dedizierten Azure Synapse SQL-Pool

Hier erfahren Sie, wie Sie Sicherungen und Wiederherstellungen in einem dedizierten Azure Synapse-SQL-Pool verwenden. Mithilfe von Wiederherstellungspunkten des dedizierten SQL-Pools können Sie einen vorherigen Zustand Ihres Data Warehouse in der primären Region wiederherstellen oder kopieren. Mithilfe von georedundanten Data Warehouse-Sicherungen können Sie eine Wiederherstellung in einer anderen geografischen Region ausführen.

## <a name="what-is-a-data-warehouse-snapshot"></a>Was ist eine Data Warehouse-Momentaufnahme?

Mit einer *Data Warehouse-Momentaufnahme* wird ein Wiederherstellungspunkt erstellt, mit dessen Hilfe Sie einen vorherigen Zustand Ihres Data Warehouse wiederherstellen oder kopieren können.  Da ein dedizierter SQL-Pool ein verteiltes System ist, besteht eine Data Warehouse-Momentaufnahme aus vielen Dateien, die in Azure Storage gespeichert sind. Momentaufnahmen erfassen inkrementelle Änderungen der Daten, die in Ihrem Data Warehouse gespeichert sind.

Eine *Data Warehouse-Wiederherstellung* ist ein neues Data Warehouse, das auf der Grundlage eines Wiederherstellungspunkts eines vorhandenen oder gelöschten Data Warehouse erstellt wird. Die Wiederherstellung Ihrer Data Warehouse-Instanz ist ein wesentlicher Bestandteil jeder Strategie für Geschäftskontinuität und Notfallwiederherstellung, da Ihre Daten nach versehentlichen Beschädigungen oder Löschungen neu erstellt werden. Data Warehouse ist darüber hinaus ein leistungsstarker Mechanismus, mit dem Sie zu Test- und Entwicklungszwecken Kopien Ihres Data Warehouse erstellen können. Die Wiederherstellungsraten von dedizierten SQL-Pools können je nach der Datenbankgröße und dem Speicherort des Quell- und Ziel-Data Warehouse variieren.

## <a name="automatic-restore-points"></a>Automatische Wiederherstellungspunkte

Bei Momentaufnahmen handelt es sich um ein integriertes Feature zum Erstellen von Wiederherstellungspunkten. Sie müssen das Feature nicht aktivieren. Der dedizierte SQL-Pool sollte jedoch für die Erstellung des Wiederherstellungspunkts einen aktiven Zustand aufweisen. Wenn er häufig angehalten wird, werden möglicherweise keine automatischen Wiederherstellungspunkte erstellt. Sie sollten daher einen benutzerdefinierten Wiederherstellungspunkt erstellen, bevor Sie den dedizierten SQL-Pool anhalten. Automatische Wiederherstellungspunkte können derzeit nicht von Benutzern gelöscht werden, da der Dienst mithilfe dieser Wiederherstellungspunkte SLAs für die Wiederherstellung verwaltet.

Die Momentaufnahmen Ihres Data Warehouse werden im Verlauf des Tages erstellt, und die generierten Wiederherstellungspunkte sind für sieben Tage verfügbar. Dieser Aufbewahrungszeitraum kann nicht geändert werden. Ein dedizierter SQL-Pool unterstützt eine RPO (Recovery Point Objective) von acht Stunden. Sie können das Data Warehouse in der primären Region anhand einer beliebigen Momentaufnahme wiederherstellen, die in den vergangenen sieben Tagen erstellt wurde.

Wenn Sie sehen möchten, wann die letzte Momentaufnahme gestartet wurde, führen Sie die folgende Abfrage in Ihrem dedizierten Online-SQL-Pool aus.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Benutzerdefinierte Wiederherstellungspunkte

Dieses Feature ermöglicht das manuelle Auslösen von Momentaufnahmen, um vor und nach umfangreichen Änderungen Wiederherstellungspunkte Ihres Data Warehouse zu erstellen. Diese Funktion gewährleistet die logische Konsistenz von Wiederherstellungspunkten und sorgt somit für zusätzlichen Datenschutz und eine schnelle Wiederherstellung bei Workloadunterbrechungen oder Benutzerfehlern. Benutzerdefinierte Wiederherstellungspunkte sind sieben Tage lang verfügbar und werden automatisch für Sie gelöscht. Sie können die Aufbewahrungsdauer für benutzerdefinierte Wiederherstellungspunkte nicht ändern. **42 benutzerdefinierte Wiederherstellungspunkte** werden jeweils garantiert. Vor der Erstellung eines weiteren Wiederherstellungspunkts müssen also erst andere Wiederherstellungspunkte [gelöscht](https://go.microsoft.com/fwlink/?linkid=875299) werden. Sie können Momentaufnahmen zum Erstellen benutzerdefinierter Wiederherstellungspunkte über [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) oder das Azure-Portal auslösen.

> [!NOTE]
> Wenn Wiederherstellungspunkte nach sieben Tagen noch zur Verfügung stehen sollen, stimmen Sie [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) für diese Funktion ab. Sie können auch einen benutzerdefinierten Wiederherstellungspunkt erstellen und auf der Grundlage dieses neuen Wiederherstellungspunkts ein neues Data Warehouse wiederherstellen. Nach der Wiederherstellung ist der dedizierte SQL-Pool online, und Sie können ihn unbegrenzt anhalten, um Computekosten zu sparen. Für die angehaltene Datenbank fallen Speichergebühren nach dem Azure Storage Premium-Tarif an. Falls Sie eine aktive Kopie des wiederhergestellten Data Warehouse benötigen, können Sie die Datenbank fortsetzen. Dies sollte nur wenige Minuten dauern.

### <a name="restore-point-retention"></a>Aufbewahrung des Wiederherstellungspunkts

In Folgenden sind ausführliche Details zu den Aufbewahrungszeiträumen für Wiederherstellungspunkte aufgeführt:

1. Der dedizierte SQL-Pool löscht einen Wiederherstellungspunkt, wenn der Aufbewahrungszeitraum von sieben Tagen erreicht wird **und** insgesamt mindestens 42 (benutzerdefinierte und automatische) Wiederherstellungspunkte vorhanden sind.
2. Wenn ein dedizierter SQL-Pool angehalten wurde, werden keine Momentaufnahmen erstellt.
3. Das Alter eines Wiederherstellungspunkts basiert auf den absoluten Kalendertagen ab Erstellung des Wiederherstellungspunkts und umfasst auch Zeiten, in denen der SQL-Pool angehalten war.
4. Es wird garantiert, dass ein dedizierter SQL-Pool jeweils 42 benutzerdefinierte Wiederherstellungspunkte und 42 automatische Wiederherstellungspunkte speichern kann, solange diese Wiederherstellungspunkte den siebentägigen Aufbewahrungszeitraum nicht erreicht haben.
5. Wenn eine Momentaufnahme erstellt wurde, dann der dedizierte SQL-Pool länger als sieben Tage angehalten und anschließend fortgesetzt wird, wird der Wiederherstellungspunkt so lange beibehalten, bis insgesamt 42 (benutzerdefinierte und automatische) Wiederherstellungspunkte vorhanden sind.

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Aufbewahrung von Momentaufnahmen, wenn ein SQL-Pool gelöscht wird

Wenn Sie einen dedizierten SQL-Pool löschen, wird eine abschließende Momentaufnahme erstellt und sieben Tage lang gespeichert. Sie können den dedizierten SQL-Pool anhand des letzten Wiederherstellungspunkts wiederherstellen, der zum Zeitpunkt der Löschung erstellt wurde. Wenn der dedizierte SQL-Pool im angehaltenen Zustand gelöscht wird, wird keine Momentaufnahme erstellt. Erstellen Sie in diesem Fall einen benutzerdefinierten Wiederherstellungspunkt, bevor Sie den dedizierten SQL-Pool löschen.

> [!IMPORTANT]
> Wenn Sie den Server/Arbeitsbereich löschen, auf dem ein dedizierter SQL-Pool gehostet wird, werden auch alle zum Server/Arbeitsbereich gehörenden Datenbanken gelöscht und können nicht wiederhergestellt werden. Es ist nicht möglich, einen gelöschten Server wiederherzustellen.

## <a name="geo-backups-and-disaster-recovery"></a>Geosicherungen und Notfallwiederherstellung

Eine Geosicherung wird einmal täglich in einem [gekoppelten Rechenzentrum](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) erstellt. Die RPO für eine Geowiederherstellung beträgt 24 Stunden. Sie können die Geosicherung auf einem Server in einer beliebigen anderen Region wiederherstellen, in der dedizierte SQL-Pools unterstützt werden. Mit einer Geosicherung wird sichergestellt, dass Sie ein Data Warehouse wiederherstellen können, wenn Sie nicht auf die Wiederherstellungspunkte in Ihrer primären Region zugreifen können.

> [!NOTE]
> Wenn Sie eine kürzere RPO für Geosicherungen benötigen, stimmen Sie [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse) für diese Funktion ab. Sie können auch einen benutzerdefinierten Wiederherstellungspunkt erstellen und auf der Grundlage dieses neuen Wiederherstellungspunkts ein neues Data Warehouse in einer anderen Region wiederherstellen. Nach der Wiederherstellung ist das Data Warehouse online, und Sie können es unbegrenzt anhalten, um Computekosten zu sparen. Für die angehaltene Datenbank fallen Speichergebühren nach dem Azure Storage Premium-Tarif an. Falls Sie eine aktive Kopie des Data Warehouse benötigen, können Sie die Datenbank fortsetzen. Dies sollte nur wenige Minuten dauern.

## <a name="backup-and-restore-costs"></a>Kosten für Sicherung und Wiederherstellung

Beachten Sie, dass die Azure-Rechnung einen Eintrag für Storage und einen Eintrag für den Speicher für die Notfallwiederherstellung aufweist. Die Storage-Gebühren sind die Gesamtkosten für das Speichern Ihrer Daten in der primären Region und die von Momentaufnahmen erfassten inkrementellen Änderungen. Eine detailliertere Erläuterung, wie Momentaufnahmen in Rechnung gestellt werden, finden Sie unter [Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Die Gebühr für georedundanten Speicher umfasst die Kosten für das Speichern von Geosicherungen.  

Die Gesamtkosten für Ihr primäres Data Warehouse und die Speicherung von Momentaufnahmenänderungen für sieben Tage werden auf die nächsten TB aufgerundet. Wenn Ihr Data Warehouse beispielsweise 1,5 TB umfasst und die Momentaufnahmen 100 GB erfassen, werden Ihnen 2 TB Daten zum Azure Storage Premium-Tarif in Rechnung gestellt.

Bei Verwendung von georedundantem Speicher wird Ihnen dieser separat in Rechnung gestellt. Der georedundante Speicher wird zum Standardsatz für georedundanten Speicher mit Lesezugriff (Read-Access Geographically Redundant Storage, RA-GRS) berechnet.

Weitere Informationen zu den Preisen von Azure Synapse finden Sie unter [Azure Synapse – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Bei der Wiederherstellung überregionaler Daten werden Ihnen keine Kosten für ausgehenden Datenverkehr in Rechnung gestellt.

## <a name="restoring-from-restore-points"></a>Wiederherstellen anhand von Wiederherstellungspunkten

Jede Momentaufnahme erstellt einen Wiederherstellungspunkt, der den Zeitpunkt zu Beginn der Momentaufnahme darstellt. Um ein Data Warehouse wiederherzustellen, wählen Sie einen Wiederherstellungspunkt aus und geben einen Wiederherstellungsbefehl aus.  

Sie können entweder das wiederhergestellte Data Warehouse und das aktuelle beibehalten oder eines davon löschen. Wenn Sie das aktuelle Data Warehouse durch das wiederhergestellte Data Warehouse ersetzen möchten, können Sie es über [„ALTER DATABASE“](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) mit der Option „MODIFY NAME“ umbenennen.

Informationen zum Wiederherstellen eines Data Warehouse finden Sie unter [Wiederherstellen eines dedizierten SQL-Pools](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Zum Wiederherstellen einer gelöschten oder angehaltenen Data Warehouse-Instanz können Sie [ein Supportticket erstellen](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Abonnementübergreifende Wiederherstellung

Wenn Sie eine direkte Wiederherstellung über ein Abonnement hinweg durchführen müssen, stimmen Sie [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore) für diese Funktion ab. Führen Sie eine Wiederherstellung auf einen anderen Server aus, und [„verschieben“](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Sie den Server über Abonnements hinweg, um eine abonnementübergreifende Wiederherstellung durchzuführen.

## <a name="geo-redundant-restore"></a>Georedundante Wiederherstellung

Sie können [Ihren dedizierten SQL-Pool in jeder beliebigen Region wiederherstellen](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell), die dedizierte SQL-Pools auf Ihrer ausgewählten Leistungsebene unterstützt.

> [!NOTE]
> Zum Ausführen einer georedundanten Wiederherstellung müssen Sie dieses Feature nicht deaktiviert haben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Wiederherstellungspunkten finden Sie unter [Benutzerdefinierte Wiederherstellungspunkte](sql-data-warehouse-restore-points.md).
