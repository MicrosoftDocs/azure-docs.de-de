---
title: Überwachung von Migrationsaktivität mithilfe von Azure Database Migration Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Migrationsaktivität mithilfe von Azure Database Migration Service überwacht wird.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 325bbee3f3d5ad5097f710cb56fe03baff97388a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532819"
---
# <a name="monitor-migration-activity"></a>Überwachen der Migrationsaktivität
In diesem Artikel erfahren Sie, wie der Fortschritt einer Migration auf Datenbankebene und Tabellenebene überwacht wird.

## <a name="monitor-at-the-database-level"></a>Überwachung auf Datenbankebene
Zur Überwachung der Aktivität auf Datenbankebene zeigen Sie das Blatt auf Datenbankebene an:

![Blatt auf Datenbankebene](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Wählen den Link für die Datenbank aus, um die Liste der Tabellen und ihren Migrationsfortschritt anzuzeigen.

In der folgenden Tabelle sind die Felder des Blatts auf Datenbankebene aufgelistet und die jeweils zugeordneten verschiedenen Statuswerte beschrieben.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Feldname</strong></th>
      <th><strong>Substatus des Felds</strong></th>
      <th><strong>Beschreibung</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Aktivitätsstatus</strong></td>
      <td>Wird ausgeführt</td>
      <td>Migrationsaktivität wird ausgeführt.</td>
    </tr>
    <tr>
      <td>Succeeded</td>
      <td>Migrationsaktivität erfolgreich ohne Probleme.</td>
    </tr>
    <tr>
      <td>Fehlerhaft</td>
      <td>Fehler bei der Migration. Dir vollständige Fehlermeldung finden Sie unter dem Link „Fehlerdetails anzeigen“ unter den Migrationsdetails.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Wird initialisiert...</td>
      <td>DMS richtet die Migrationspipeline ein.</td>
    </tr>
    <tr>
      <td>Wird ausgeführt</td>
      <td>DMS-Pipeline wird ausgeführt und führt die Migration durch.</td>
    </tr>
    <tr>
      <td>Abgeschlossen</td>
      <td>Migration abgeschlossen.</td>
    </tr>
    <tr>
      <td>Fehler</td>
      <td>Fehler bei der Migration. Klicken Sie auf „Details zur Migration“, um Fehler bei der Migration anzuzeigen.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Details zur Migration</strong></td>
      <td>Die Migrationspipeline wird initialisiert.</td>
      <td>DMS richtet die Migrationspipeline ein.</td>
    </tr>
    <tr>
      <td>Vollständiger Datenladevorgang in Bearbeitung</td>
      <td>DMS führt den ersten Ladevorgang durch.</td>
    </tr>
    <tr>
      <td>Bereit für die Umstellung</td>
      <td>Nach Abschluss des ersten Ladevorgangs markiert DMS die Datenbank als bereit für die Umstellung. Benutzer müssen überprüfen, ob die fortlaufende Synchronisierung für alle Daten erfolgt ist.</td>
    </tr>
    <tr>
      <td>Alle Änderungen angewendet</td>
      <td>Erster Ladevorgang und fortlaufende Synchronisierung sind abgeschlossen. Dieser Status wird auch nach der erfolgreichen Umstellung der Datenbank angezeigt.</td>
    </tr>
    <tr>
      <td>Fehlerdetails anzeigen</td>
      <td>Klicken Sie auf den Link, um Fehlerdetails anzuzeigen.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duration</strong></td>
      <td>–</td>
      <td>Gesamtzeit zwischen der Initialisierung und dem Abschluss bzw. dem Fehlschlagen der Migrationsaktivität.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Überwachung auf Tabellenebene: Kurze Zusammenfassung
Zur Überwachung der Aktivität auf Tabellenebene zeigen Sie das Blatt auf Tabellenebene an. Im oberen Bereich des Blatts werden die genaue Anzahl der migrierten Zeilen im vollständigen Ladevorgang und inkrementelle Updates angezeigt. 

Im unteren Bereich des Blatts sind die Tabellen aufgeführt, und eine kurze Zusammenfassung der Migrationsfortschritt wird angezeigt.

![Blatt auf Tabellenebene: Kurze Übersicht](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

In der folgenden Tabelle sind die in den Details auf Tabellenebene angezeigten Felder beschrieben.

| Feldname        | BESCHREIBUNG       |
| ------------- | ------------- |
| **Vollständiger Ladevorgang abgeschlossen**      | Anzahl von Tabellen, für die ein vollständiger Datenladevorgang abgeschlossen wurde. |
| **Vollständiger Ladevorgang in Warteschlange eingereiht**      | Anzahl von Tabellen in der Warteschlange für einen vollständigen Ladevorgang.      |
| **Vollständiger Ladevorgang wird durchgeführt** | Anzahl von fehlerhaften Tabellen.      |
| **Inkrementelle Updates**      | Anzahl von auf das Ziel angewendeten Change Data Capture-Updates (CDC) in Zeilen. |
| **Inkrementelle Einfügevorgänge**      | Anzahl von auf das Ziel angewendete CDC-Einfügungen in Zeilen.      |
| **Inkrementelle Löschvorgänge** | Anzahl von auf das Ziel angewendete CDC-Löschungen in Zeilen.      |
| **Ausstehende Änderungen**      | Anzahl von CDCs in Zeilen, die noch auf die Anwendung auf das Ziel warten. |
| **Angewendete Änderungen**      | Gesamtanzahl von auf das Ziel angewendeten CDC-Updates, -Einfügungen und -Löschungen in Zeilen.      |
| **Tabellen in fehlerhaftem Zustand** | Anzahl von Tabellen, die sich während der Migration im Fehlerzustand befinden. Tabellen wechseln beispielsweise in den Fehlerzustand, wenn im Ziel Duplikate identifiziert werden, oder wenn Daten nicht mit dem Ladevorgang in der Zieltabelle kompatibel sind.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Überwachung auf Tabellenebene: Detaillierte Zusammenfassung
Der Migrationsfortschritt wird auf zwei Registerkarten – einmal im vollständigen Ladevorgang und einmal in der inkrementellen Synchronisierung – angezeigt.
    
![Registerkarte „Vollständiger Ladevorgang“](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Registerkarte „Inkrementelle Datensynchronisierung“](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

In der folgenden Tabelle sind die im Migrationsfortschritt auf Tabellenebene angezeigten Felder beschrieben.

| Feldname        | BESCHREIBUNG       |
| ------------- | ------------- |
| **Status – Wird synchronisiert**      | Fortlaufende Synchronisierung wird ausgeführt. |
| **Einfügen**      | Anzahl von auf das Ziel angewendete CDC-Einfügungen in Zeilen.      |
| **Aktualisieren** | Anzahl von auf das Ziel angewendete CDC-Updates in Zeilen.      |
| **Löschen**      | Anzahl von auf das Ziel angewendete CDC-Löschungen in Zeilen. |
| **Angewendet gesamt**      | Gesamtanzahl von auf das Ziel angewendeten CDC-Updates, -Einfügungen und -Löschungen in Zeilen. |
| **Datenfehler** | Anzahl der in dieser Tabelle aufgetretenen Datenfehler. Einige Beispiele für Fehler sind *511: Eine Zeile der Größe %d kann nicht erstellt werden, da sie länger als die zulässige maximale Zeilengröße von %d wäre, 8114: Fehler beim Konvertieren des %ls-Datentyps in %ls.*  Kunden sollten eine Abfrage von Tabelle „dms_apply_exceptions“ im Azure-Ziel durchführen, um die Fehlerdetails anzuzeigen.    |

> [!NOTE]
> Die CDC-Werte „Einfügen“, „Aktualisieren“, „Löschen“ und „Angewendet gesamt“ verringern sich möglicherweise, wenn die Datenbank umgestellt oder die Migration neu gestartet wird.

## <a name="next-steps"></a>Nächste Schritte
- Überprüfen der Migrationsanleitung im [Leitfaden zur Datenbankmigration von Microsoft](https://datamigration.microsoft.com/)