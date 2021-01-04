---
title: Migrieren Ihres dedizierten SQL-Pools (früher SQL DW) zu Gen2
description: Anweisungen zum Migrieren eines dedizierten SQL-Pools (früher SQL DW) zu Gen2 und der Migrationsplan nach Region
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 512775369bd7787c6228c6d452be0e236ddf5cc2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456331"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Durchführen eines Upgrades für Ihren dedizierten SQL-Pool (früher SQL DW) auf Gen2

Microsoft hilft beim Senken der Einstiegskosten für die Ausführung eines dedizierten SQL-Pools (früher SQL DW).  Niedrigere Computeebenen, die anspruchsvolle Abfragen verarbeiten können, sind jetzt für den dedizierten SQL-Pool (früher SQL DW) verfügbar. Lesen Sie die vollständige Ankündigung unter [Azure SQL Data Warehouse Gen2 now supports lower compute tiers](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/) (Azure SQL Data Warehouse Gen2 unterstützt jetzt niedrigere Computeebenen). Das neue Angebot steht in den Regionen in der folgenden Tabelle zur Verfügung. Bei unterstützten Regionen sorgt der folgende Vorgang beim dedizierten Gen1-SQL-Pool (früher SQL DW) für ein Upgrade auf Gen2:

- **Der automatische Upgradevorgang:** Automatische Upgrades beginnen, sobald der Dienst in einer Region verfügbar ist.  Wenn automatische Upgrades in einer bestimmten Region beginnen, finden Upgrades einzelner Data Warehouses während des von Ihnen ausgewählten Wartungszeitplans statt.
- [**Selbstständiges Upgrade auf Gen2:**](#self-upgrade-to-gen2) Sie können steuern, wann das Upgrade erfolgt, indem Sie ein selbstständiges Upgrade auf Gen2 ausführen. Wenn Ihre Region noch nicht unterstützt wird, können Sie die Wiederherstellung von einem Wiederherstellungspunkt direkt in einer Gen2-Instanz in einer unterstützten Region ausführen.

## <a name="automated-schedule-and-region-availability-table"></a>Automatisierter Zeitplan und die Tabelle der verfügbaren Regionen

In der folgenden Tabelle sehen Sie, wann der niedrigere Gen2-Computetarif in welchen Regionen zur Verfügung stehen wird und wann automatische Upgrades beginnen. Änderungen der Datumsangaben bleiben vorbehalten. Informieren Sie sich, wann Ihre Region verfügbar ist.

\* gibt an, dass ein bestimmter Zeitplan für die Region zurzeit nicht verfügbar ist.

| **Region** | **Niedrigerer Gen2-Computetarif verfügbar** | **Beginn automatischer Upgrades** |
|:--- |:--- |:--- |
| Kanada, Osten |1\. Juni 2020 |1\. Juli 2020 |
| China, Osten |\* |\* |
| China, Norden |\* |\* |
| Deutschland, Mitte |\* |\* |
| Deutschland, Westen-Mitte |Verfügbar |1\. Mai 2020 |
| Indien, Westen |Verfügbar |1\. Mai 2020  |

## <a name="automatic-upgrade-process"></a>Automatischer Upgradevorgang

Wir planen automatische Upgrades für Ihre Gen 1-Instanzen nach dem oben angezeigten Verfügbarkeitsdiagramm. Um unerwartete Unterbrechungen bei der Verfügbarkeit des dedizierten SQL-Pools (früher SQL DW) zu vermeiden, werden die automatischen Upgrades während Ihres Wartungszeitplans geplant. Die Möglichkeit zum Erstellen einer neuen Gen1-Instanz wird in Regionen, in denen gerade ein automatisches Upgrade auf Gen2 durchgeführt wird, deaktiviert. Gen1 wird eingestellt, sobald die automatischen Upgrades abgeschlossen sind. Weitere Informationen zu Zeitplänen finden Sie unter [Anzeigen eines Wartungszeitplans](maintenance-scheduling.md#view-a-maintenance-schedule).

Der Upgradevorgang ist mit einem kurzen Abfall der Konnektivität (ca. 5 Minuten) verbunden, während wir Ihren dedizierten SQL-Pool (früher SQL DW) neu starten.  Nachdem Ihr dedizierter SQL-Pool (früher SQL DW) neu gestartet wurde, ist er zur Verwendung vollständig verfügbar. Zeitweise kann jedoch eine Beeinträchtigung der Leistung auftreten, während der Upgradevorgang weiterhin im Hintergrund die Datendateien aktualisiert. Die Gesamtzeit für die Leistungsminderung variiert abhängig von der Größe der Datendateien.

Sie können den Prozess der Datenaktualisierung auch mit Ausführung von [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) für alle primären Columnstore-Tabellen mit größerem SLO-Wert und größerer Ressourcenklasse nach dem Neustart beschleunigen.

> [!NOTE]
> ALTER INDEX REBUILD ist ein Offlinevorgang, und die Tabellen stehen erst dann zur Verfügung, wenn die Wiederherstellung abgeschlossen ist.

## <a name="self-upgrade-to-gen2"></a>Selbstupgrade auf Gen2

Sie können sich auch für ein Selbstupgrade (selbstständiges Upgrade) entscheiden, indem Sie bei einem dedizierten Gen1-SQL-Pool (früher SQL DW) diese Schritte ausführen. Wenn Sie sich für ein selbstständiges Upgrade entscheiden, müssen Sie dieses vor Beginn des automatischen Upgradevorgangs in Ihrer Region abschließen. Dadurch wird sichergestellt, dass Sie jedes Risiko vermeiden, dass das automatische Upgrade einen Konflikt verursacht.

Für das Selbstupgrade können Sie zwischen zwei Optionen wählen.  Sie können entweder ihren aktuellen dedizierten SQL-Pool (früher SQL DW) direkt aktualisieren, oder Sie können einen dedizierten Gen1-SQL-Pool (früher SQL DW) in eine Gen2-Instanz wiederherstellen.

- [Direktes Upgrade](upgrade-to-latest-generation.md) – Bei dieser Option wird Ihr dedizierter Gen1-SQL-Pool (früher SQL DW) auf Gen2 aktualisiert. Der Upgradevorgang ist mit einem kurzen Abfall der Konnektivität (ca. 5 Minuten) verbunden, während wir Ihren dedizierten SQL-Pool (früher SQL DW) neu starten.  Nach dem Neustart ist er zur Verwendung vollständig verfügbar. Wenn während des Upgrades Probleme auftreten, können Sie eine [Supportanfrage](sql-data-warehouse-get-started-create-support-ticket.md) erstellen und als mögliche Ursache „Gen2-Upgrade“ angeben.
- [Upgrade vom Wiederherstellungspunkt](sql-data-warehouse-restore-points.md) – Erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt für Ihren aktuellen dedizierten Gen1-SQL-Pool (früher SQL DW), und führen Sie dann die direkte Wiederherstellung in eine Gen2-Instanz durch. Der dedizierte Gen1-SQL-Pool (früher SQL DW) bleibt bestehen. Nachdem die Wiederherstellung abgeschlossen wurde, ist Ihr dedizierter Gen2-SQL-Pool (früher SQL DW) zur Verwendung vollständig verfügbar.  Nachdem Sie alle Test- und Überprüfungsprozesse auf der wiederhergestellten Gen2-Instanz ausgeführt haben, kann die ursprüngliche Gen1-Instanz gelöscht werden.

  - Schritt 1: [Erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt](sql-data-warehouse-restore-active-paused-dw.md) im Azure-Portal.
  - Schritt 2: Legen Sie bei der Wiederherstellung von einem benutzerdefinierten Wiederherstellungspunkt aus die „Leistungsebene“ auf Ihren bevorzugten Gen2-Tarif fest.

Zeitweise kann eine Beeinträchtigung der Leistung auftreten, während der Upgradevorgang weiterhin im Hintergrund die Datendateien aktualisiert. Die Gesamtzeit für die Leistungsminderung variiert abhängig von der Größe der Datendateien.

Um den Hintergrundprozess der Datenmigration zu beschleunigen, können Sie die Datenverschiebung sofort erzwingen, indem Sie [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) in allen primären Columnstore-Tabellen ausführen, die Sie mit einem größeren SLO-Wert und einer größeren Ressourcenklasse abrufen würden.

> [!NOTE]
> ALTER INDEX REBUILD ist ein Offlinevorgang, und die Tabellen stehen erst dann zur Verfügung, wenn die Wiederherstellung abgeschlossen ist.

Wenn bei Ihrem dedizierten SQL-Pool (früher SQL DW) Probleme auftreten, können Sie eine [Supportanfrage](sql-data-warehouse-get-started-create-support-ticket.md) erstellen und als möglichen Grund „Gen2-Upgrade“ angeben.

Weitere Informationen finden Sie unter [Optimieren der Leistung durch ein Upgrade von SQL Data Warehouse](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Häufig gestellte Fragen zur Migration

**F: Kostet Gen2 dasselbe wie Gen1?**

- A: Ja.

**F: Wie wirken sich die Upgrades auf meine automatisierten Skripte aus?**

- A: Jedes Automatisierungsskript, das auf ein Servicelevelziel verweist, sollte so geändert werden, dass es dem Gen2-Äquivalent entspricht.  Ausführlichere Informationen finden Sie [hier](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**F: Wie lange dauert ein Selbstupgrade normalerweise?**

- A: Sie können ein direktes Upgrade oder ein Upgrade von einem Wiederherstellungspunkt aus ausführen.

  - Ein direktes Upgrade bewirkt, dass Ihr dedizierter SQL-Pool (früher SQL DW) vorübergehend angehalten und dann fortgesetzt wird.  Ein Hintergrundprozess wird fortgesetzt, während der dedizierte SQL-Pool (früher SQL DW) online ist.  
  - Es dauert länger, wenn Sie über einen Wiederherstellungspunkt aktualisieren, da das Upgrade den vollständigen Wiederherstellungsprozess durchläuft.

**F: Wie lange dauert das automatische Upgrade?**

- A: Die tatsächliche Downtime für das Upgrade ist nur die Zeit zum Anhalten und Fortsetzen des Diensts, ca. 5 bis 10 Minuten. Nach der kurzen Downtime führt ein Hintergrundprozess die Speichermigration durch. Die Dauer des Hintergrundprozesses ist abhängig von der Größe Ihres dedizierten SQL-Pools (früher SQL DW).

**F: Wann wird das automatische Upgrade durchgeführt?**

- A: Während Ihres Wartungszeitplans. Durch Nutzen Ihres ausgewählten Wartungszeitplans werden Unterbrechungen für Ihr Unternehmen minimiert.

**F: Was muss ich tun, wenn mein Hintergrund-Upgradeprozess nicht mehr zu reagieren scheint?**

- A: Starten Sie eine Neuindizierung der Columnstore-Tabellen. Beachten Sie, dass die Neuindizierung der Tabelle offline während dieses Vorgangs durchgeführt wird.

**F: Was geschieht, wenn Gen2 nicht dasselbe Servicelevelziel hat wie ich auf Gen1?**

- A: Wenn Sie ein DW600 oder DW1200 auf Gen1 ausführen, sollten Sie DW500c bzw. DW1000c verwenden, da Gen2 mehr Arbeitsspeicher, Ressourcen und höhere Leistung als Gen1 bereitstellt.

**F: Kann ich die Geosicherung deaktivieren?**

- A: Nein. Die Geosicherung ist ein Enterprisefeature, um die Verfügbarkeit Ihres dedizierten SQL-Pools (früher SQL DW) aufrechtzuerhalten, falls eine Region nicht mehr verfügbar ist. Öffnen Sie eine [Supportanfrage](sql-data-warehouse-get-started-create-support-ticket.md), wenn Sie weitere Fragen haben.

**F: Gibt es einen Unterschied in der T-SQL-Syntax zwischen Gen1 und Gen2?**

- A: Es gibt keine Änderung in der T-SQL-Sprachsyntax von Gen1 zu Gen2.

**F: Unterstützt Gen2 Wartungsfenster?**

- A: Ja.

**F: Kann ich nach dem Upgrade meiner Region eine neue Gen1-Instanz erstellen?**

- A: Nein. Nach dem Upgrade einer Region ist das Erstellen neuer Gen 1-Instanzen nicht mehr möglich.

## <a name="next-steps"></a>Nächste Schritte

- [Upgradeschritte](upgrade-to-latest-generation.md)
- [Wartungsfenster](maintenance-scheduling.md)
- [Überwachung der Ressourcenintegrität](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Voraussetzungen](upgrade-to-latest-generation.md#before-you-begin)
- [Direktes Upgrade und Upgrade von einem Wiederherstellungspunkt aus](upgrade-to-latest-generation.md)
- [Erstellen eines benutzerdefinierten Wiederherstellungspunkts](sql-data-warehouse-restore-points.md)
- [Wiederherstellen einer aktiven oder angehaltenen Datenbank im Azure-Portal](sql-data-warehouse-restore-active-paused-dw.md)
- [Eröffnen einer Azure Synapse Analytics-Supportanfrage](https://go.microsoft.com/fwlink/?linkid=857950)
