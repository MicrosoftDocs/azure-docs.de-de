---
title: Planen der Ausführung eines Indexers
titleSuffix: Azure Cognitive Search
description: Legen Sie einen Zeitplan für Indexer der kognitiven Azure-Suche fest, um Inhalte in regelmäßigen Abständen oder zu bestimmten Zeiten zu indizieren.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: dfd8526a035d4eef4d07539e541e37c88023b500
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063212"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche

Ein Indexer wird normal einmal ausgeführt, und zwar sofort nach der Erstellung. Anschließend können Sie ihn im Azure-Portal, über [Indexer ausführen (REST-API)](/rest/api/searchservice/run-indexer) oder über ein Azure SDK nach Bedarf erneut ausführen. Alternativ können Sie einen Indexer auch so konfigurieren, dass er nach einem Zeitplan ausgeführt wird. Fälle, in denen das Festlegen eines Zeitplans für einen Indexer nützlich ist:

* Die Quelldaten ändern sich im Lauf der Zeit, und der Suchindexer soll die Deltadaten automatisch verarbeiten.
* Die Quelldaten sind sehr groß, und die Indexerausführung soll auf verschiedene Zeiten verteilt werden. Weitere Informationen zur Indizierung großer Datenmengen finden Sie unter [Indizieren großer Datasets in der kognitiven Azure-Suche](search-howto-large-index.md).
* Ein Suchindex wird aus mehreren Datenquellen aufgefüllt, und die Indexer sollen zu unterschiedlichen Zeiten ausgeführt werden, um Konflikte zu vermeiden.

Ein Zeitplan kann beispielsweise wie folgt aussehen: Beginn am 1. Januar und Ausführung alle 50 Minuten.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> Der Scheduler ist eine integrierte Funktion der kognitiven Azure-Suche. Externe Scheduler werden nicht unterstützt.

## <a name="schedule-property"></a>schedule-Eigenschaft

Ein Zeitplan ist Teil der Indexerdefinition. Wenn die **schedule**-Eigenschaft ausgelassen wird, wird der Indexer nur einmal unmittelbar nach seiner Erstellung ausgeführt. Wenn Sie eine **schedule**-Eigenschaft hinzufügen, geben Sie zwei Teile an.

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
|**Intervall** | (erforderlich) Die Zeitspanne zwischen dem Start von zwei aufeinanderfolgenden Indexerausführungen. Das kürzeste zulässige Zeitintervall ist 5 Minuten und das längste 1.440 Minuten (24 Stunden). Es muss als XSD-Wert „dayTimeDuration“ formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitwerts](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Das Muster hierfür lautet wie folgt: `P(nD)(T(nH)(nM))`. <br/><br/>Beispiele: `PT15M` = alle 15 Minuten, `PT2H` = alle 2 Stunden.|
| **Startzeit (UTC)** | (optional) Gibt an, wann die geplanten Ausführungen beginnen sollen. Wenn kein Wert angegeben wird, wird die aktuelle UTC-Zeit verwendet. Diese Zeitangabe kann in der Vergangenheit liegen. In diesem Fall wird die erste Ausführung so geplant, als wäre der Indexer seit der ursprünglich mit **startTime** angegebenen Startzeit kontinuierlich ausgeführt worden.<br/><br/>Beispiele: `2021-01-01T00:00:00Z` ab Mitternacht am 1. Januar, `2021-01-05T22:28:00Z` ab 22:28 Uhr am 5. Januar.|

## <a name="scheduling-behavior"></a>Planungsverhalten

Nur eine Ausführung eines Indexers kann zu einem gegebenen Zeitpunkt ausgeführt werden. Wird ein Indexer zu dem Zeitpunkt, für den seine nächste Ausführung geplant ist, bereits ausgeführt, wird diese Ausführung auf den nächsten geplanten Zeitpunkt verschoben.

Betrachten wir ein Beispiel, um dies zu konkreter veranschaulichen. Angenommen, Sie haben einen Indexerzeitplan mit dem **Intervall** „Stündlich“ und der **Startzeit** „1. Juni 2019 um 8:00:00 Uhr (UTC)“ konfiguriert. Folgendes könnte passieren, wenn eine Indexerausführung länger als eine Stunde dauert:

* Die erste Indexerausführung beginnt am 1. Juni 2019 um oder gegen 8:00 Uhr (UTC). Angenommen, diese Ausführung dauert 20 Minuten (oder hat eine andere Dauer unter 1 Stunde).
* Die zweite Indexerausführung beginnt am 1. Juni 2019 um oder gegen 9:00 Uhr (UTC). Angenommen, diese Ausführung dauert 70 Minuten – also mehr als einer Stunde – und wird erst gegen 10:10 Uhr (UTC) abgeschlossen.
* Der Start der dritten Ausführung ist für 10:00 Uhr (UTC) geplant, aber zu diesem Zeitpunkt ist die vorherige Ausführung noch nicht abgeschlossen. Diese geplante Ausführung wird dann übersprungen. Die nächste Ausführung des Indexers startet somit erst um 11:00 Uhr (UTC).

> [!NOTE]
> Wenn ein Indexer auf einen bestimmten Zeitplan festgelegt ist, im gleichen Dokument aber wiederholt ein Fehler auftritt, wird der Indexer in selteneren Intervallen ausgeführt (bis hin zum maximalen Intervall von mindestens einmal in 24 Stunden), bis die Ausführung fehlerfrei verläuft. Wenn Sie denken, dass Sie das zugrunde liegende Problem behoben haben, können Sie den Indexer manuell ausführen. Wenn die Indizierung dann erfolgreich ausgeführt wird, wird der Indexer auf den regulären Zeitplan zurückgesetzt.

## <a name="schedule-using-rest"></a>Festlegen eines Zeitplans mit REST

Geben Sie die **schedule**-Eigenschaft beim Erstellen oder Aktualisieren des Indexers an.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>Festlegen eines Zeitplans mithilfe von .NET

Das folgende C#-Beispiel erstellt einen Indexer für Azure SQL-Datenbank unter Verwendung einer vordefinierten Datenquelle und eines Index und legt den Zeitplan so fest, dass der Indexer sofort und dann einmal pro Tag ausgeführt wird:

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Wenn Sie einen Indexer mit [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) erstellen oder aktualisieren, wird der Zeitplan mithilfe der [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule)-Klasse definiert. Der **IndexingSchedule**-Konstruktor erfordert einen **Interval**-Parameter, der mit einem **TimeSpan**-Objekt angegeben wird. Wie bereits erwähnt, beträgt das kleinste zulässige Intervall 5 Minuten und das größte 24 Stunden. Der zweite **StartTime**-Parameter, der als ein **DateTimeOffset**-Objekt angegeben wird, ist optional.

## <a name="next-steps"></a>Nächste Schritte

Für Indexer, die nach einem Zeitplan ausgeführt werden, können Sie Vorgänge überwachen, indem Sie den Status des Suchdiensts abrufen, oder detaillierte Informationen abfragen, indem Sie die Diagnoseprotokollierung aktivieren.

* [Überwachen des Status von Suchindexern](search-howto-monitor-indexers.md)
* [Sammeln und Analysieren von Protokolldaten](search-monitor-logs.md)