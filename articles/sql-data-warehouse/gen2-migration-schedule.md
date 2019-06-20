---
title: Migrieren eines vorhandenen Azure SQL Data Warehouse zu Gen2 | Microsoft-Dokumentation
description: Anweisungen zum Migrieren eines vorhandenen Data Warehouse zu Gen2 und der Migrationsplan nach Region.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 999c75d07ef7e24d4d75587b6b42a4ab1b2192cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596114"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Aktualisieren des Data Warehouse zu Gen2

Microsoft hilft beim Senken der Einstiegskosten für die Ausführung eines Data Warehouses.  Niedrigere Computeebenen, die anspruchsvolle Abfragen verarbeiten können, sind jetzt für Azure SQL Data Warehouse verfügbar. Lesen Sie die vollständige Ankündigung unter [Azure SQL Data Warehouse Gen2 now supports lower compute tiers](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/) (Azure SQL Data Warehouse Gen2 unterstützt jetzt niedrigere Computeebenen). Das neue Angebot steht in den Regionen in der folgenden Tabelle zur Verfügung. In den unterstützten Regionen kann das vorhandene Gen1-Data Warehouse mit einer der folgenden Methoden auf Gen2 aktualisiert werden:

- **Der automatische Upgradevorgang:** Automatische Upgrades beginnen, sobald der Dienst in einer Region verfügbar ist.  Wenn automatische Upgrades in einer bestimmten Region beginnen, finden Upgrades einzelner DWs während des von Ihnen ausgewählten Wartungszeitplans statt.
- [**Selbstständiges Upgrade auf Gen2:** ](#self-upgrade-to-gen2) Sie können steuern, wann das Upgrade erfolgt, indem Sie ein selbstständiges Upgrade auf Gen2 ausführen. Wenn Ihre Region noch nicht unterstützt wird, können Sie die Wiederherstellung von einem Wiederherstellungspunkt direkt in einer Gen2-Instanz in einer unterstützten Region ausführen.

## <a name="automated-schedule-and-region-availability-table"></a>Automatisierter Zeitplan und die Tabelle der verfügbaren Regionen

In der folgenden Tabelle sehen Sie, wann der niedrigere Gen2-Computetarif in welchen Regionen zur Verfügung stehen wird und wann automatische Upgrades beginnen. Änderungen der Datumsangaben bleiben vorbehalten. Informieren Sie sich, wann Ihre Region verfügbar ist.

\* gibt an, dass ein bestimmter Zeitplan für die Region zurzeit nicht verfügbar ist.

| **Region** | **Niedrigerer Gen2-Computetarif verfügbar** | **Beginn automatischer Upgrades** |
|:--- |:--- |:--- |
| Australien (Osten) |Verfügbar |1\. Juni 2019 |
| Australien, Südosten |Verfügbar |1\. Mai 2019 |
| Brasilien Süd |Verfügbar |1\. Juni 2019 |
| Kanada, Mitte |Verfügbar |1\. Juni 2019 |
| Kanada, Osten |\* |\* |
| USA (Mitte) |Verfügbar |1\. Juni 2019 |
| China, Osten |\* |\* |
| China, Osten 2 |\* |Nur Gen2 |
| China, Norden |\* |\* |
| China, Norden 2 |Verfügbar |Nur Gen2 |
| Asien, Osten |Verfügbar |1\. Juni 2019 |
| USA (Ost) |Verfügbar |1\. Juni 2019 |
| USA (Ost) 2 |Verfügbar |1\. Juni 2019 |
| Frankreich, Mitte |\* |1\. Juni 2019 |
| Deutschland, Mitte |\* |\* |
| Deutschland, Westen-Mitte |1\. September 2019|2\. Januar 2020 |
| Indien, Mitte |Verfügbar |1\. Juni 2019 |
| Indien, Süden |Verfügbar |1\. Juni 2019 |
| Japan, Osten |Verfügbar |1\. Juni 2019 |
| Japan, Westen |Verfügbar |1\. Mai 2019 |
| Korea, Mitte |Verfügbar |1\. Juni 2019 |
| Korea, Süden |Verfügbar |1\. Mai 2019 |
| USA Nord Mitte |Verfügbar |1\. Mai 2019 |
| Nordeuropa |Verfügbar |1\. Juni 2019 |
| USA Süd Mitte |Verfügbar |1\. Juni 2019 |
| Südostasien |Verfügbar |1\. Juni 2019 |
| UK, Süden |Verfügbar, 2019 |1\. Juni 2019 |
| UK, Westen |\*|\* |
| USA, Westen-Mitte |2\. September 2019 |2\. Januar 2020|
| Europa, Westen |Verfügbar |1\. Juni 2019 |
| USA (Westen) |Verfügbar |1\. Juni 2019 |
| USA, Westen 2 |Verfügbar |1\. Juni 2019 |

## <a name="automatic-upgrade-process"></a>Automatischer Upgradevorgang

Wir planen automatische Upgrades für Ihre Gen 1-Instanzen nach dem oben angezeigten Verfügbarkeitsdiagramm. Um unerwartete Unterbrechungen der Verfügbarkeit des Data Warehouse zu vermeiden, werden die automatischen Upgrades während Ihres Wartungszeitplans geplant. Die Möglichkeit zum Erstellen einer neuen Gen1-Instanz wird in Regionen, in denen gerade ein automatisches Upgrade auf Gen2 durchgeführt wird, deaktiviert. Weitere Informationen zu Zeitplänen finden Sie unter [Anzeigen eines Wartungszeitplans](viewing-maintenance-schedule.md).

Der Upgradevorgang ist mit einem kurzen Abfall der Konnektivität (ca. 5 Minuten) verbunden, wenn wir Ihr Data Warehouse neu starten.  Sobald Ihr Data Warehouse neu gestartet ist, ist es vollständig zur Verwendung verfügbar. Zeitweise kann jedoch eine Beeinträchtigung der Leistung auftreten, während der Upgradevorgang weiterhin im Hintergrund die Datendateien aktualisiert. Die Gesamtzeit für die Leistungsminderung variiert abhängig von der Größe der Datendateien.

Sie können den Prozess der Datenaktualisierung auch mit Ausführung von [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) für alle primären Columnstore-Tabellen mit größerem SLO-Wert und größerer Ressourcenklasse nach dem Neustart beschleunigen.

> [!NOTE]
> ALTER INDEX REBUILD ist ein Offlinevorgang, und die Tabellen stehen erst dann zur Verfügung, wenn die Wiederherstellung abgeschlossen ist.

## <a name="self-upgrade-to-gen2"></a>Selbstupgrade auf Gen2

Sie können optional ein selbstständiges Upgrade ausführen, indem Sie diese Schritte für ein vorhandenes Gen1-Data Warehouse ausführen. Wenn Sie sich für ein selbstständiges Upgrade entscheiden, müssen Sie dieses vor Beginn des automatischen Upgradevorgangs in Ihrer Region abschließen. Dadurch wird sichergestellt, dass Sie jedes Risiko vermeiden, dass das automatische Upgrade einen Konflikt verursacht.

Für das Selbstupgrade können Sie zwischen zwei Optionen wählen.  Sie können entweder Ihr aktuelles Data Warehouse direkt aktualisieren oder ein Gen1-Data Warehouse in einer Gen2-Instanz wiederherstellen.

- [Direktes Upgrade](upgrade-to-latest-generation.md): Mit dieser Option wird Ihr vorhandenes Gen1-Data Warehouse auf Gen2 aktualisiert. Der Upgradevorgang ist mit einem kurzen Abfall der Konnektivität (ca. 5 Minuten) verbunden, wenn wir Ihr Data Warehouse neu starten.  Sobald Ihr Data Warehouse neu gestartet ist, ist es vollständig zur Verwendung verfügbar. Wenn während des Upgrades Probleme auftreten, können Sie eine [Supportanfrage](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) erstellen und als mögliche Ursache „Gen2-Upgrade“ angeben.
- [Upgrade vom Wiederherstellungspunkt](sql-data-warehouse-restore.md): Erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt auf Ihrem aktuellen Gen1-Data Warehouse, und führen Sie die direkte Wiederherstellung in eine Gen2-Instanz durch. Das vorhandene Gen1-Data Warehouse bleibt bestehen. Sobald die Wiederherstellung abgeschlossen ist, ist Ihr Gen2-Data Warehouse vollständig verwendbar.  Nachdem Sie alle Test- und Überprüfungsprozesse auf der wiederhergestellten Gen2-Instanz ausgeführt haben, kann die ursprüngliche Gen1-Instanz gelöscht werden.

   - Schritt 1: [Erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal) im Azure-Portal.
   - Schritt 2: Legen Sie bei der Wiederherstellung von einem benutzerdefinierten Wiederherstellungspunkt aus die „Leistungsebene“ auf Ihren bevorzugten Gen2-Tarif fest.

Zeitweise kann eine Beeinträchtigung der Leistung auftreten, während der Upgradevorgang weiterhin im Hintergrund die Datendateien aktualisiert. Die Gesamtzeit für die Leistungsminderung variiert abhängig von der Größe der Datendateien.

Um den Hintergrundprozess der Datenmigration zu beschleunigen, können Sie die Datenverschiebung sofort erzwingen, indem Sie [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) in allen primären Columnstore-Tabellen ausführen, die Sie mit einem größeren SLO-Wert und einer größeren Ressourcenklasse abrufen würden.

> [!NOTE]
> ALTER INDEX REBUILD ist ein Offlinevorgang, und die Tabellen stehen erst dann zur Verfügung, wenn die Wiederherstellung abgeschlossen ist.

Wenn Probleme mit Ihrem Data Warehouse auftreten, können Sie eine [Supportanfrage](sql-data-warehouse-get-started-create-support-ticket.md) erstellen und als möglichen Grund „Migration zu Gen2“ angeben.

Weitere Informationen finden Sie unter [Optimieren der Leistung durch ein Upgrade von SQL Data Warehouse](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Häufig gestellte Fragen zur Migration

**F: Kostet Gen2 dasselbe wie Gen1?**

- A: Ja.

**F: Wie wirken sich die Upgrades auf meine automatisierten Skripte aus?**

- A: Jedes Automatisierungsskript, das auf ein Servicelevelziel verweist, sollte so geändert werden, dass es dem Gen2-Äquivalent entspricht.  Ausführlichere Informationen finden Sie [hier](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**F: Wie lange dauert ein Selbstupgrade normalerweise?**

- A: Sie können ein direktes Upgrade oder ein Upgrade von einem Wiederherstellungspunkt aus ausführen.  
   - Ein direktes Upgrade bewirkt, dass Ihr Data Warehouse vorübergehend angehalten und dann fortgesetzt wird.  Ein Hintergrundprozess wird fortgesetzt, während das Data Warehouse online ist.  
   - Es dauert länger, wenn Sie über einen Wiederherstellungspunkt aktualisieren, da das Upgrade den vollständigen Wiederherstellungsprozess durchläuft.

**F: Wie lange dauert das automatische Upgrade?**

- A: Die tatsächliche Downtime für das Upgrade ist nur die Zeit zum Anhalten und Fortsetzen des Diensts, ca. 5 bis 10 Minuten. Nach der kurzen Downtime führt ein Hintergrundprozess die Speichermigration durch. Die Dauer des Hintergrundprozesses ist abhängig von der Größe Ihres Data Warehouse.

**F: Wann wird das automatische Upgrade durchgeführt?**

- A: Während Ihres Wartungszeitplans. Durch Nutzen Ihres ausgewählten Wartungszeitplans werden Unterbrechungen für Ihr Unternehmen minimiert.

**F: Was muss ich tun, wenn mein Hintergrund-Upgradeprozess nicht mehr zu reagieren scheint?**

 - A: Starten Sie eine Neuindizierung der Columnstore-Tabellen. Beachten Sie, dass die Neuindizierung der Tabelle offline während dieses Vorgangs durchgeführt wird.

**F: Was geschieht, wenn Gen2 nicht dasselbe Servicelevelziel hat wie ich auf Gen1?**
- A: Wenn Sie ein DW600 oder DW1200 auf Gen1 ausführen, sollten Sie DW500c bzw. DW1000c verwenden, da Gen2 mehr Arbeitsspeicher, Ressourcen und höhere Leistung als Gen1 bereitstellt.

**F: Kann ich die Geosicherung deaktivieren?**
- A:  Nein. Die Geosicherung ist ein Enterprisefeature, um die Verfügbarkeit Ihres Data Warehouse aufrechtzuerhalten, falls eine Region nicht mehr verfügbar ist. Öffnen Sie eine [Supportanfrage](sql-data-warehouse-get-started-create-support-ticket.md), wenn Sie weitere Fragen haben.

**F: Gibt es einen Unterschied in der T-SQL-Syntax zwischen Gen1 und Gen2?**

- A: Es gibt keine Änderung in der T-SQL-Sprachsyntax von Gen1 zu Gen2.

**F: Unterstützt Gen2 Wartungsfenster?**

- A: Ja.

**F: Kann ich nach dem Upgrade meiner Region eine neue Gen1-Instanz erstellen?**

- A:  Nein. Nach dem Upgrade einer Region ist das Erstellen neuer Gen 1-Instanzen nicht mehr möglich.

## <a name="next-steps"></a>Nächste Schritte

- [Upgradeschritte](upgrade-to-latest-generation.md)
- [Wartungsfenster](maintenance-scheduling.md)
- [Überwachung der Ressourcenintegrität](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Voraussetzungen](upgrade-to-latest-generation.md#before-you-begin)
- [Direktes Upgrade und Upgrade von einem Wiederherstellungspunkt aus](upgrade-to-latest-generation.md)
- [Erstellen eines benutzerdefinierten Wiederherstellungspunkts](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Wiederherstellen einer aktiven oder angehaltenen Datenbank im Azure-Portal](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Gewusst wie: Erstellen eines Supporttickets für SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
