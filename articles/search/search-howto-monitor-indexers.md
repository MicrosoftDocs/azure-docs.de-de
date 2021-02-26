---
title: Überwachen des Status und der Ergebnisse von Indexern
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie den Status, den Fortschritt und die Ergebnisse von Indexern der kognitiven Azure-Suche über das Azure-Portal, mithilfe der REST-API oder per .NET SDK überwachen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063248"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Überwachen des Status und der Ergebnisse von Indexern der kognitiven Azure-Suche

Sie können die Indexerverarbeitung im Azure-Portal oder programmatisch über REST-Aufrufe oder ein Azure-SDK überwachen. Zusätzlich zum Status über den Indexer selbst können Sie Start- und Endzeiten sowie detaillierte Fehler und Warnungen einer bestimmten Ausführung anzeigen.

## <a name="monitor-using-azure-portal"></a>Überwachen mit dem Azure-Portal

Der aktuelle Status all Ihrer Indexer wird auf der Übersichtsseite Ihres Suchdiensts angezeigt. Die Portalseiten werden alle paar Minuten aktualisiert, sodass nicht sofort ein Hinweis auf einen neuen Indizierungslauf erkennbar ist.

   ![Liste der Indexer](media/search-monitor-indexers/indexers-list.png "Liste der Indexer")

| Status | BESCHREIBUNG |
|--------|-------------|
| **In Bearbeitung** | Gibt die aktive Ausführung an. Im Portal werden partielle Informationen angezeigt. Beim Fortschreiten der Indizierung erkennen Sie, dass der Wert **Dokumente erfolgreich** entsprechend ansteigt. Die Ausführung von Indexern, die große Datenmengen verarbeiten, kann sehr lange dauern. So können beispielsweise Indexer, die Millionen von Quelldokumenten verarbeiten, eine Laufzeit von 24 Stunden haben und nahezu umgehend neu gestartet werden. Indexer mit hohem Datenvolumen haben im Portal unter Umständen durchgehend den Status **In Bearbeitung**. Details zum Fortschritt sowie zu vergangenen Ausführungen sind allerdings auch während der Ausführung eines Indexers verfügbar. |
| **Erfolgreich** | Gibt an, dass die Ausführung erfolgreich war. Eine Indexerausführung kann auch erfolgreich sein, wenn bei einzelnen Dokumenten Fehler aufgetreten sind – vorausgesetzt, die Fehleranzahl liegt unter der Indexereinstellung **Max. Fehlerelemente**. |
| **Fehler** | Die Anzahl der Fehler hat **Max. Fehlerelemente** überschritten, und die Indizierung wurde beendet. |
| **Zurücksetzen** | Der interne Änderungsnachverfolgungs-Zustand des Indexers wurde zurückgesetzt. Der Indexer wird vollständig ausgeführt, und es werden alle Dokumente aktualisiert, nicht nur diejenigen mit jüngeren Zeitstempeln. |

Sie können auf einen Indexer in der Liste klicken, um weitere Informationen zur aktuellen Ausführung sowie zu vergangen Ausführungen des Indexers anzuzeigen.

   ![Indexerzusammenfassung und Ausführungsverlauf](media/search-monitor-indexers/indexer-summary.png "Indexerzusammenfassung und Ausführungsverlauf")

Im Diagramm **Indexerzusammenfassung** wird die Anzahl verarbeiteter Dokumente der letzten Ausführungen grafisch dargestellt.

Die Liste **Ausführungsdetails** enthält bis zu 50 der letzten Ausführungsergebnisse. Klicken Sie in der Liste auf ein Ausführungsergebnis, um Einzelheiten zur entsprechenden Ausführung anzuzeigen. Hierzu zählen unter anderem die Start- und Endzeit sowie möglicherweise aufgetretene Fehler und Warnungen.

   ![Ausführungsdetails eines Indexers](media/search-monitor-indexers/indexer-execution.png "Ausführungsdetails eines Indexers")

Sollten bei der Ausführung dokumentspezifische Probleme aufgetreten sein, werden diese in den Feldern „Fehler“ und „Warnungen“ aufgeführt.

   ![Indexerdetails mit Fehlern](media/search-monitor-indexers/indexer-execution-error.png "Indexerdetails mit Fehlern")

Warnungen sind bei bestimmten Arten von Indexern keine Seltenheit und deuten nicht immer auf ein Problem hin. So werden beispielsweise von Indexern, die Cognitive Services verwenden, unter Umständen Warnungen ausgegeben, wenn Bild- oder PDF-Dateien keinen zu verarbeitenden Text enthalten. 

Weitere Informationen zur Untersuchung von Indexerfehlern und -warnungen finden Sie unter [Beheben von häufigen Problemen bei Suchindexern in der kognitiven Azure-Suche](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Überwachen mithilfe des Befehls zum Abrufen des Indexerstatus (Rest-API)

Status und Ausführungsverlauf eines Indexers können mithilfe des [Befehls zum Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status) abgerufen werden:

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Die Antwort enthält den Gesamtstatus des Indexers, den letzten (oder laufenden) Aufruf des Indexers sowie den Verlauf der letzten Indexeraufrufe.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Der Ausführungsverlauf enthält bis zu 50 der letzten Ausführungen und ist in umgekehrter chronologischer Reihenfolge sortiert, sodass die neuesten Ausführungen zuerst aufgeführt werden.

Es gibt zwei verschiedene Statuswerte. Der übergeordnete Status gilt für den Indexer an sich. Der Indexerstatus **running** bedeutet, dass der Indexer ordnungsgemäß eingerichtet ist und ausgeführt werden kann, aber nicht, dass er momentan ausgeführt wird.

Jede Ausführung des Indexers hat auch einen eigenen Status, der angibt, ob die jeweilige Ausführung gerade stattfindet (**running**) oder bereits abgeschlossen wurde (**success**, **transientFailure** oder **persistentFailure**). 

Wenn ein Indexer zurückgesetzt wird, um den Zustand seiner Änderungsnachverfolgung zu aktualisieren, wird ein separater Ausführungsverlaufseintrag mit dem Status **Reset** hinzugefügt.

Ausführlichere Informationen zu Statuscodes und zu Indexerüberwachungsdaten finden Sie unter [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Überwachung mit .NET

Im folgenden C#-Beispiel wird das Azure Cognitive Search .NET SDK verwendet, um Informationen zum Status eines Indexers sowie die Ergebnisse der letzten (oder laufenden) Ausführung in der Konsole auszugeben.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Die Konsolenausgabe sieht in etwa wie folgt aus:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Es gibt zwei verschiedene Statuswerte. Der übergeordnete Status gilt für den Indexer an sich. Der Indexerstatus **Running** bedeutet, dass der Indexer ordnungsgemäß eingerichtet ist und ausgeführt werden kann, aber nicht, dass er momentan ausgeführt wird.

Jede Ausführung des Indexers hat auch einen eigenen Status, der angibt, ob die jeweilige Ausführung gerade stattfindet (**Running**) oder bereits abgeschlossen wurde (**Success** oder **TransientError**). 

Wenn ein Indexer zurückgesetzt wird, um den Zustand seiner Änderungsnachverfolgung zu aktualisieren, wird ein separater Verlaufseintrag mit dem Status **Reset** hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Details zu Statuscodes und sowie Informationen zur Indexerüberwachung finden Sie in den folgenden API-Referenzen:

* [GetIndexerStatus (REST-API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)