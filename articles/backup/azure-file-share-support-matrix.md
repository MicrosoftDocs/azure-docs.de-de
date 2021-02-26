---
title: Unterstützungsmatrix für Sicherungen von Azure-Dateifreigaben
description: Enthält eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen bei der Sicherung von Azure-Dateifreigaben.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 999cb4c764e9960c509d19faa61016b2522259ac
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819859"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Unterstützungsmatrix für Sicherungen von Azure-Dateifreigaben

Sie können den [Azure Backup-Dienst](./backup-overview.md) verwenden, um Azure-Dateifreigaben zu sichern. Dieser Artikel enthält eine Zusammenfassung der Unterstützungseinstellungen bei der Sicherung von Azure-Dateifreigaben mit Azure Backup.

## <a name="supported-regions"></a>Unterstützte Regionen

### <a name="ga-regions-for-azure-file-shares-backup"></a>Regionen mit allgemeiner Verfügbarkeit für die Sicherung von Azure-Dateifreigaben

Die Sicherung von Azure-Dateifreigaben ist in allen Regionen verfügbar **mit Ausnahme von**: „Deutschland, Mitte (Sovereign)“, „Deutschland, Nordosten (Sovereign)“, „China, Osten“, „China, Osten 2“, „China, Norden“, „China, Norden 2“, „US Gov Iowa“

## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

| Speicherkontodetails | Support                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Kontoart            | Azure Backup unterstützt Azure-Dateifreigaben unter den Speicherkonten Universell V1, Universell V2 und im Dateispeicher-Kontotyp. |
| Leistung              | Azure Backup unterstützt Dateifreigaben sowohl unter Standard- als auch unter Storage Premium-Konten. |
| Replikation              | Unterstützt werden Azure-Dateifreigaben in Speicherkonten mit allen Replikationstypen |
| Firewall aktiviert         | Unterstützt werden Azure-Dateifreigaben in Speicherkonten mit Firewallregeln, die Microsoft Azure Services den Zugriff auf das Speicherkonto erlauben|

## <a name="supported-file-shares"></a>Ausgewählte Dateifreigaben

| Dateifreigabetyp                                   | Support   |
| -------------------------------------------------- | --------- |
| Standard                                           | Unterstützt |
| Groß                                              | Unterstützt |
| Premium                                            | Unterstützt |
| Mit dem Azure-Dateisynchronisierungsdienst verbundene Dateifreigaben | Unterstützt |

## <a name="protection-limits"></a>Schutzlimits

| Einstellung                                                      | Begrenzung |
| ------------------------------------------------------------ | ----- |
| Maximale Anzahl von Dateifreigaben, die pro Tag und pro Tresor geschützt werden können| 200   |
| Maximale Anzahl von Speicherkonten, die pro Tresor pro Tag registriert werden können | 50    |
| Maximale Anzahl von Dateifreigaben, die pro Tresor geschützt werden können | 2000   |
| Maximale Anzahl von Speicherkonten, die pro Tresor registriert werden können | 200   |

## <a name="backup-limits"></a>Backup-Grenzwerte

| Einstellung                                      | Begrenzung |
| -------------------------------------------- | ----- |
| Maximale Anzahl bedarfsgesteuerter Sicherungen pro Tag | 10   |
| Maximale Anzahl geplanter Sicherungen pro Tag | 1     |

## <a name="restore-limits"></a>Wiederherstellungslimits

| Einstellung                                                      | Begrenzung   |
| ------------------------------------------------------------ | ------- |
| Maximale Anzahl der Wiederherstellungen pro Tag                           | 10      |
| Maximale Anzahl der Dateien pro Wiederherstellung                         | 99      |
| Maximale empfohlene Wiederherstellungsgröße pro Wiederherstellung für große Dateifreigaben | 15 TiB |

## <a name="retention-limits"></a>Grenzwerte für die Aufbewahrung

| Einstellung                                                      | Begrenzung    |
| ------------------------------------------------------------ | -------- |
| Maximale Gesamtzahl der Wiederherstellungspunkte pro Dateifreigabe zu jedem beliebigen Zeitpunkt | 200      |
| Maximale Aufbewahrung von Wiederherstellungspunkten, die durch bedarfsgesteuerte Sicherung erstellt wurden | 10 Jahre |
| Maximale Aufbewahrung täglicher Wiederherstellungspunkte (Momentaufnahmen) pro Dateifreigabe| 200 Tage |
| Maximale Aufbewahrung wöchentlicher Wiederherstellungspunkte (Momentaufnahmen) pro Dateifreigabe | 200 Wochen |
| Maximale Aufbewahrung monatlicher Wiederherstellungspunkte (Momentaufnahmen) pro Dateifreigabe | 120 Monate |
| Maximale Aufbewahrung jährlicher Wiederherstellungspunkte (Momentaufnahmen) pro Dateifreigabe | 10 Jahre |

## <a name="supported-restore-methods"></a>Unterstützte Wiederherstellungsmethoden

| Wiederherstellungsmethode     | Details                                                      |
| ------------------ | ------------------------------------------------------------ |
| Wiederherstellung der vollständigen Freigabe | Sie können die gesamte Dateifreigabe am ursprünglichen oder an einem alternativen Speicherort wiederherstellen. |
| Wiederherstellung auf Elementebene | Sie können einzelne Dateien und Ordner am ursprünglichen oder an einem alternativen Speicherort wiederherstellen. |

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Sichern von Azure-Dateifreigaben](backup-afs.md).
* [Wiederherstellen von Azure-Dateifreigaben](restore-afs.md)
* [Verwalten der Sicherungen von Azure-Dateifreigaben](manage-afs-backup.md)
