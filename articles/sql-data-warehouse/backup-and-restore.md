---
title: Azure SQL Data Warehouse-Sicherung und -Wiederherstellung – Momentaufnahmen (georedundant) | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sicherungen und Wiederherstellungen in Azure SQL Data Warehouse funktionieren. Verwenden Sie Data Warehouse-Sicherungen, um Ihr Data Warehouse mithilfe eines Wiederherstellungspunkts in der primären Region wiederherzustellen. Mithilfe von georedundanten Sicherungen können Sie eine Wiederherstellung in einer anderen geografischen Region ausführen.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 914513bc19cc81da29efef12d50a6485233d169f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236580"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Sicherung und Wiederherstellung in Azure SQL Data Warehouse

Erfahren Sie, wie Sie Sicherungen und Wiederherstellungen in Azure SQL Data Warehouse verwenden. Verwenden Sie Data Warehouse-Wiederherstellungspunkte, um einen vorherigen Zustand Ihres Data Warehouse in der primären Region wiederherzustellen oder zu kopieren. Mithilfe von georedundanten Data Warehouse-Sicherungen können Sie eine Wiederherstellung in einer anderen geografischen Region ausführen.

## <a name="what-is-a-data-warehouse-snapshot"></a>Was ist eine Data Warehouse-Momentaufnahme?

Mit einer *Data Warehouse-Momentaufnahme* wird ein Wiederherstellungspunkt erstellt, mit dessen Hilfe Sie einen vorherigen Zustand Ihres Data Warehouse wiederherstellen oder kopieren können.  Da SQL Data Warehouse ein verteiltes System ist, besteht eine Data Warehouse-Momentaufnahme aus zahlreichen Dateien, die in Azure Storage gespeichert sind. Momentaufnahmen erfassen inkrementelle Änderungen der Daten, die in Ihrem Data Warehouse gespeichert sind.

Eine *Data Warehouse-Wiederherstellung* ist ein neues Data Warehouse, das auf der Grundlage eines Wiederherstellungspunkts eines vorhandenen oder gelöschten Data Warehouse erstellt wird. Die Wiederherstellung Ihrer Data Warehouse-Instanz ist ein wesentlicher Bestandteil jeder Strategie für Geschäftskontinuität und Notfallwiederherstellung, da Ihre Daten nach versehentlichen Beschädigungen oder Löschungen neu erstellt werden. Data Warehouse ist darüber hinaus ein leistungsstarker Mechanismus, mit dem Sie zu Test- und Entwicklungszwecken Kopien Ihres Data Warehouse erstellen können.  SQL Data Warehouse-Wiederherstellungsraten können je nach der Datenbankgröße und dem Speicherort des Quell- und Ziel-Data Warehouse variieren. Innerhalb derselben Region betragen die durchschnittlichen Wiederherstellungsraten in der Regel ca. 20 Minuten. 

## <a name="automatic-restore-points"></a>Automatische Wiederherstellungspunkte

Bei Momentaufnahmen handelt es sich um ein integriertes Feature des Diensts zum Erstellen von Wiederherstellungspunkten. Sie müssen das Feature nicht aktivieren. Automatische Wiederherstellungspunkte können derzeit nicht von Benutzern gelöscht werden, wenn der Dienst mithilfe dieser Wiederherstellungspunkte SLAs für die Wiederherstellung verwaltet.

SQL Data Warehouse erstellt im Laufe des Tages Momentaufnahmen Ihres Data Warehouse und generiert Wiederherstellungspunkte, die sieben Tage lang verfügbar sind. Dieser Aufbewahrungszeitraum kann nicht geändert werden. SQL Data Warehouse unterstützt eine RPO (Recovery Point Objective) von acht Stunden. Sie können das Data Warehouse in der primären Region anhand einer beliebigen Momentaufnahme wiederherstellen, die in den vergangenen sieben Tagen erstellt wurde.

Führen Sie die folgende Abfrage in Ihrer SQL Data Warehouse-Online-Instanz aus, um zu ermitteln, wann die letzte Momentaufnahme gestartet wurde.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Benutzerdefinierte Wiederherstellungspunkte

Dieses Feature ermöglicht das manuelle Auslösen von Momentaufnahmen, um vor und nach umfangreichen Änderungen Wiederherstellungspunkte Ihres Data Warehouse zu erstellen. Diese Funktion gewährleistet die logische Konsistenz von Wiederherstellungspunkten und sorgt somit für zusätzlichen Datenschutz und eine schnelle Wiederherstellung bei Workloadunterbrechungen oder Benutzerfehlern. Benutzerdefinierte Wiederherstellungspunkte sind sieben Tage lang verfügbar und werden automatisch für Sie gelöscht. Sie können die Aufbewahrungsdauer für benutzerdefinierte Wiederherstellungspunkte nicht ändern. **42 benutzerdefinierte Wiederherstellungspunkte** werden jeweils garantiert. Vor der Erstellung eines weiteren Wiederherstellungspunkts müssen also erst andere Wiederherstellungspunkte [gelöscht](https://go.microsoft.com/fwlink/?linkid=875299) werden. Sie können Momentaufnahmen zum Erstellen benutzerdefinierter Wiederherstellungspunkte über [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) oder das Azure-Portal auslösen.

> [!NOTE]
> Wenn Wiederherstellungspunkte nach sieben Tagen noch zur Verfügung stehen sollen, stimmen Sie [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) für diese Funktion ab. Sie können auch einen benutzerdefinierten Wiederherstellungspunkt erstellen und auf der Grundlage dieses neuen Wiederherstellungspunkts ein neues Data Warehouse wiederherstellen. Nach der Wiederherstellung ist das Data Warehouse online, und Sie können es unbegrenzt anhalten, um Computekosten zu sparen. Für die angehaltene Datenbank fallen Speichergebühren nach dem Azure Storage Premium-Tarif an. Falls Sie eine aktive Kopie des wiederhergestellten Data Warehouse benötigen, können Sie die Datenbank fortsetzen. Dies sollte nur wenige Minuten dauern.

### <a name="restore-point-retention"></a>Aufbewahrung des Wiederherstellungspunkts

In Folgenden sind ausführliche Details zu den Aufbewahrungszeiträumen für Wiederherstellungspunkte aufgeführt:

1. SQL Data Warehouse löscht einen Wiederherstellungspunkt, wenn die Aufbewahrungsdauer von sieben Tage erreicht wird **und** insgesamt mindestens 42 (benutzerdefinierte und automatische) Wiederherstellungspunkte vorhanden sind.
2. Wenn ein Data Warehouse angehalten wird, werden keine Momentaufnahmen erstellt.
3. Das Alter eines Wiederherstellungspunkts basiert auf den absoluten Kalendertagen ab Erstellung des Wiederherstellungspunkts und beinhaltet auch Zeiten, in denen das Data Warehouse angehalten war.
4. Es wird garantiert, dass ein Data Warehouse jeweils 42 benutzerdefinierte Wiederherstellungspunkte und 42 automatische Wiederherstellungspunkte speichern kann, solange diese Wiederherstellungspunkte nicht die siebentägige Aufbewahrungsdauer erreicht haben.
5. Wenn eine Momentaufnahme erstellt wird, das Data Warehouse für mehr als sieben Tage angehalten und anschließend fortgesetzt wird, kann der Wiederherstellungspunkt beibehalten werden, bis insgesamt 42 (benutzerdefinierte und automatische) Wiederherstellungspunkte vorhanden sind.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Aufbewahrung von Momentaufnahmen, wenn ein Data Warehouse gelöscht wird

Wenn Sie ein Data Warehouse trennen, erstellt SQL Data Warehouse eine letzte Momentaufnahme und speichert diese sieben Tage lang. Sie können das Data Warehouse anhand des letzten Wiederherstellungspunkts wiederherstellen, der zum Zeitpunkt der Löschung erstellt wurde.

> [!IMPORTANT]
> Wenn Sie eine logische SQL Server-Instanz löschen, werden auch alle Datenbanken der Instanz gelöscht und können nicht wiederhergestellt werden. Es ist nicht möglich, einen gelöschten Server wiederherzustellen.

## <a name="geo-backups-and-disaster-recovery"></a>Geosicherungen und Notfallwiederherstellung

SQL Data Warehouse führt ein Mal pro Tag für ein [geografisch gepaartes Datencenter](../best-practices-availability-paired-regions.md) eine Geosicherung aus. Die RPO für eine Geowiederherstellung beträgt 24 Stunden. Sie können die Geosicherung auf einem Server in einer beliebigen anderen Region wiederherstellen, in der SQL Data Warehouse unterstützt wird. Mit einer Geosicherung wird sichergestellt, dass Sie ein Data Warehouse wiederherstellen können, wenn Sie nicht auf die Wiederherstellungspunkte in Ihrer primären Region zugreifen können.

Geosicherungen sind standardmäßig aktiviert. Wenn Ihr Data Warehouse Gen1 ist, können Sie diese Option [abwählen](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy), wenn Sie dies wünschen. Sie können Geosicherungen für Gen2 nicht abwählen, da Datenschutz eine integrierte Garantie ist.

> [!NOTE]
> Wenn Sie eine kürzere RPO für Geosicherungen benötigen, stimmen Sie [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse) für diese Funktion ab. Sie können auch einen benutzerdefinierten Wiederherstellungspunkt erstellen und auf der Grundlage dieses neuen Wiederherstellungspunkts ein neues Data Warehouse in einer anderen Region wiederherstellen. Nach der Wiederherstellung ist das Data Warehouse online, und Sie können es unbegrenzt anhalten, um Computekosten zu sparen. Für die angehaltene Datenbank fallen Speichergebühren nach dem Azure Storage Premium-Tarif an. Falls Sie eine aktive Kopie des Data Warehouse benötigen, können Sie die Datenbank fortsetzen. Dies sollte nur wenige Minuten dauern.

## <a name="backup-and-restore-costs"></a>Kosten für Sicherung und Wiederherstellung

Beachten Sie, dass die Azure-Rechnung einen Eintrag für Storage und einen Eintrag für den Speicher für die Notfallwiederherstellung aufweist. Die Storage-Gebühren sind die Gesamtkosten für das Speichern Ihrer Daten in der primären Region und die von Momentaufnahmen erfassten inkrementellen Änderungen. Eine detailliertere Erläuterung, wie Momentaufnahmen in Rechnung gestellt werden, finden Sie unter [Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Die Gebühr für georedundanten Speicher umfasst die Kosten für das Speichern von Geosicherungen.  

Die Gesamtkosten für Ihr primäres Data Warehouse und die Speicherung von Momentaufnahmenänderungen für sieben Tage werden auf die nächsten TB aufgerundet. Wenn Ihr Data Warehouse beispielsweise 1,5 TB umfasst und die Momentaufnahmen 100 GB erfassen, werden Ihnen 2 TB Daten zum Azure Storage Premium-Tarif in Rechnung gestellt.

Bei Verwendung von georedundantem Speicher wird Ihnen dieser separat in Rechnung gestellt. Der georedundante Speicher wird zum Standardsatz für georedundanten Speicher mit Lesezugriff (Read-Access Geographically Redundant Storage, RA-GRS) berechnet.

Weitere Informationen zu den Preisen von SQL Data Warehouse finden Sie unter [SQL Data Warehouse – Preise]. Bei der Wiederherstellung überregionaler Daten werden Ihnen keine Kosten für ausgehenden Datenverkehr in Rechnung gestellt.

## <a name="restoring-from-restore-points"></a>Wiederherstellen anhand von Wiederherstellungspunkten

Jede Momentaufnahme erstellt einen Wiederherstellungspunkt, der den Zeitpunkt zu Beginn der Momentaufnahme darstellt. Um ein Data Warehouse wiederherzustellen, wählen Sie einen Wiederherstellungspunkt aus und geben einen Wiederherstellungsbefehl aus.  

Sie können entweder das wiederhergestellte Data Warehouse und das aktuelle beibehalten oder eines davon löschen. Wenn Sie die aktuelle Data Warehouse-Instanz durch die wiederhergestellte Data Warehouse-Instanz ersetzen möchten, können Sie sie über [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) mit der Option MODIFY NAME umbenennen.

Informationen zur Wiederherstellung einer Data Warehouse-Instanz finden Sie unter [Wiederherstellen einer Azure SQL Data Warehouse-Instanz (Portal)](sql-data-warehouse-restore-database-portal.md), [Wiederherstellen einer Azure SQL Data Warehouse-Instanz (PowerShell)](sql-data-warehouse-restore-database-powershell.md) oder [Wiederherstellen einer Azure SQL Data Warehouse-Instanz mit REST-APIs](sql-data-warehouse-restore-database-rest-api.md).

Zum Wiederherstellen einer gelöschten oder angehaltenen Data Warehouse-Instanz können Sie [ein Supportticket erstellen](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Abonnementübergreifende Wiederherstellung

Wenn Sie eine direkte Wiederherstellung über ein Abonnement hinweg durchführen müssen, stimmen Sie [hier](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore) für diese Funktion ab. Wiederherstellung auf einen anderen logischen Server und [„Verschieben“](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) des Servers über Abonnements hinweg, um eine abonnementübergreifende Wiederherstellung durchzuführen. 

## <a name="geo-redundant-restore"></a>Georedundante Wiederherstellung

Sie können [Ihr Data Warehouse in einer beliebigen Region wiederherstellen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region), die SQL Data Warehouse auf der gewählten Leistungsebene unterstützt.

> [!NOTE]
> Zum Ausführen einer georedundanten Wiederherstellung müssen Sie dieses Feature nicht deaktiviert haben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Planen der Notfallwiederherstellung finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](../sql-database/sql-database-business-continuity.md).
