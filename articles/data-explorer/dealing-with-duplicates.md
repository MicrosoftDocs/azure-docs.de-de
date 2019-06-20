---
title: Behandeln von doppelten Daten in Azure Data Explorer
description: In diesem Artikel werden verschiedene Ansätze für die Behandlung doppelter Daten mit Azure Data Explorer beschrieben.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445769"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Behandeln von doppelten Daten in Azure Data Explorer

Geräte, die Daten an die Cloud senden, speichern die Daten in einem lokalen Cache zwischen. Je nach Größe der Daten werden diese unter Umständen über Tage oder sogar Monate im lokalen Cache gespeichert. Es empfiehlt sich, Ihre Analysedatenbanken vor fehlerhaften Geräten zu schützen, die die zwischengespeicherten Daten erneut senden und so zu duplizierten Daten in der Analysedatenbank führen. In diesem Thema werden bewährte Methoden für den Umgang mit doppelten Daten in Szenarien dieser Art beschrieben.

Im günstigsten Fall wird eine Datenduplizierung von vornherein verhindert. Beheben Sie das Problem nach Möglichkeit bereits frühzeitig in der Datenpipeline. Das spart Kosten für die Verschiebung von Daten entlang der Datenpipeline und vermeidet den Einsatz von Ressourcen für die Behandlung doppelter Daten, die im System erfasst wurden. In Situationen, in denen das Quellsystem nicht geändert werden kann, gibt es verschiedene andere Möglichkeiten für den Umgang mit diesem Szenario.

## <a name="understand-the-impact-of-duplicate-data"></a>Grundlegendes zu den Auswirkungen doppelter Daten

Überwachen Sie den prozentualen Anteil doppelter Daten. Nach Ermittlung des prozentualen Anteils doppelter Daten können Sie den Umfang und die geschäftlichen Auswirkungen des Problems analysieren und geeignete Maßnahmen ergreifen.

Beispielabfrage zur Ermittlung des prozentualen Anteils doppelter Datensätze:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Lösungen für den Umgang mit doppelten Daten

### <a name="solution-1-dont-remove-duplicate-data"></a>Lösung 1: Beibehalten doppelter Daten

Untersuchen Sie Ihre geschäftlichen Anforderungen und die Toleranz gegenüber doppelten Daten. Bei manchen Datasets ist ein gewisser prozentualer Anteil doppelter Daten kein Problem. Wenn die duplizierten Daten keine größeren Auswirkungen haben, können Sie sie einfach ignorieren. Vorteil: Es entsteht kein Zusatzaufwand bei der Datenerfassung, und die Abfrageleistung wird nicht beeinträchtigt.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Lösung 2: Behandeln doppelter Zeilen im Rahmen von Abfragen

Die doppelten Datenzeilen können auch beim Abfragen herausgefiltert werden. Mit der Aggregatfunktion [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) können Sie die doppelten Datensätze herausfiltern und den letzten Datensatz auf der Grundlage des Zeitstempels (oder einer anderen Spalte) zurückgeben. Vorteil: Die Datenerfassung wird beschleunigt, da die Deduplizierung bereits bei der Abfrage erfolgt. Darüber hinaus stehen alle Datensätze (einschließlich Duplikate) für die Überwachung und Problembehandlung zur Verfügung. Nachteil: Durch die Verwendung der Funktion `arg_max` erhöhen sich bei jeder Datenabfrage die Abfragezeit und die CPU-Last. Je nach abgefragter Datenmenge kann es vorkommen, dass die Lösung nicht mehr funktioniert oder zu viel Arbeitsspeicher benötigt und auf eine andere Option ausgewichen werden muss.

Im folgenden Beispiel wird der zuletzt erfasste Datensatz für eine Gruppe von Spalten abgefragt, die die eindeutigen Datensätze bestimmen:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Diese Abfrage kann auch in einer Funktion platziert werden, anstatt die Tabelle direkt abzufragen:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Lösung 3: Filtern nach Duplikaten während der Datenerfassung

Eine weitere Möglichkeit besteht darin, während der Datenerfassung nach Duplikaten zu filtern. Die doppelten Daten werden daraufhin bei der Datenerfassung in Kusto-Tabellen ignoriert. Daten werden in einer Stagingtabelle erfasst und nach dem Entfernen doppelter Zeilen in eine andere Tabelle kopiert. Vorteil: Im Vergleich zur vorherigen Lösung verbessert sich die Abfrageleistung erheblich. Nachteil: Erfassungsdauer und Datenspeicherkosten erhöhen sich.

Diese Methode wird im folgenden Beispiel veranschaulicht:

1. Erstellen Sie eine weitere Tabelle mit dem gleichen Schema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Erstellen Sie eine Funktion, um die doppelten Datensätze herauszufiltern, indem Sie die Verknüpfung zwischen den neuen Datensätzen und den zuvor erfassten Datensätzen aufheben.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Verknüpfungsvorgänge sind CPU-gebundene Vorgänge und erhöhen die Systemlast.

1. Legen Sie die [Updaterichtlinie](/azure/kusto/management/update-policy) für die Tabelle `DeviceEventsUnique` fest. Die Updaterichtlinie wird aktiviert, wenn neue Daten bei der Tabelle `DeviceEventsAll` eingehen. Die Kusto-Engine führt die Funktion automatisch aus, wenn neue [Erweiterungen](/azure/kusto/management/extents-overview) erstellt werden. Die Verarbeitung ist auf die neu erstellten Daten ausgerichtet. Der folgende Befehl fügt die Quelltabelle (`DeviceEventsAll`), die Zieltabelle (`DeviceEventsUnique`) und die Funktion `RemoveDuplicatesDeviceEvents` zusammen, um die Updaterichtlinie zu erstellen.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Durch die Updaterichtlinie erhöht sich die Datenerfassungsdauer, da die Daten während der Erfassung gefiltert und anschließend zweimal erfasst werden: in der Tabelle `DeviceEventsAll` und in der Tabelle `DeviceEventsUnique`.

1. Optional: Legen Sie für die Tabelle `DeviceEventsAll` eine kürzere Datenaufbewahrung fest, um zu vermeiden, dass Kopien der Daten gespeichert werden. Wählen Sie die Anzahl von Tagen in Abhängigkeit vom Datenvolumen und dem Zeitraum, für den Sie Daten zu Problembehandlungszwecken aufbewahren möchten. Sie können die Datenaufbewahrung auf `0d` Tage festlegen, um Kosten zu sparen und die Leistung zu verbessern, da die Daten dann nicht in den Speicher hochgeladen werden.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Zusammenfassung

Datenduplizierung kann auf verschiedene Arten behandelt werden. Sehen Sie sich die Optionen sorgfältig an, um unter Berücksichtigung von Preis- und Leistungsaspekten die passende Methode für Ihr Unternehmen zu ermitteln.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)
