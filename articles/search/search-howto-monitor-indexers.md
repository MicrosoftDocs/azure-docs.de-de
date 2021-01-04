---
title: Überwachen des Status und der Ergebnisse von Indexern
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie den Status, den Fortschritt und die Ergebnisse von Indexern der kognitiven Azure-Suche über das Azure-Portal, mithilfe der REST-API oder per .NET SDK überwachen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0107dfb24ddad2a5b0f9f0ab12d2fe701466e385
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372828"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Überwachen des Status und der Ergebnisse von Indexern der kognitiven Azure-Suche

Die kognitive Azure-Suche stellt Status- und Überwachungsinformationen zu aktuellen und vergangenen Ausführungen jedes Indexers bereit.

Die Indexerüberwachung ist in folgenden Fällen hilfreich:

* Nachverfolgen des Fortschritts eines Indexers während einer laufenden Ausführung
* Überprüfen der Ergebnisse einer laufenden oder vergangenen Indexerausführung
* Ermitteln von allgemeinen Indexerfehlern sowie von Fehlern oder Warnungen im Zusammenhang mit einzelnen indizierten Dokumenten

## <a name="get-status-and-history"></a>Abrufen von Status und Verlauf

Auf die Überwachungsinformationen für Indexer kann auf unterschiedliche Weise zugegriffen werden:

* Gehen Sie im [Azure-Portal](#portal) wie folgt vor:
* Mithilfe der [REST API](#restapi)
* Per [.NET SDK](#dotnetsdk)

Zur Überwachung von Indexern stehen alle der folgenden Informationen zur Verfügung. Die Datenformate unterscheiden sich allerdings je nach verwendeter Zugriffsmethode:

* Statusinformationen zum Indexer an sich
* Informationen zur letzten Ausführung des Indexers – einschließlich Status, Start- und Endzeit sowie ausführliche Fehler und Warnungen
* Eine Liste mit vergangenen Indexerausführungen – einschließlich Status, Ergebnissen, Fehlern und Warnungen

Die Ausführung von Indexern, die große Datenmengen verarbeiten, kann sehr lange dauern. So können beispielsweise Indexer, die Millionen von Quelldokumenten verarbeiten, eine Laufzeit von 24 Stunden haben und nahezu umgehend neu gestartet werden. Indexer mit hohem Datenvolumen haben im Portal unter Umständen durchgehend den Status **In Bearbeitung**. Details zum Fortschritt sowie zu vergangenen Ausführungen sind allerdings auch während der Ausführung eines Indexers verfügbar.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Überwachung über das Portal

Der aktuelle Status Ihrer Indexer wird auf der Übersichtsseite Ihres Suchdiensts in der Liste **Indexer** angezeigt.

   ![Liste der Indexer](media/search-monitor-indexers/indexers-list.png "Liste der Indexer")

Wenn ein Indexer ausgeführt wird, hat er in der Liste den Status **In Bearbeitung**, und der Wert **Dokumente erfolgreich**  gibt an, wie viele Dokumente bereits verarbeitet wurden. Die Aktualisierung der Statuswerte und der Dokumentanzahl für die Indexer kann im Portal ein paar Minuten dauern.

Ein Indexer, dessen letzte Ausführung erfolgreich war, hat den Status **Erfolgreich**. Eine Indexerausführung kann auch erfolgreich sein, wenn bei einzelnen Dokumenten Fehler aufgetreten sind – vorausgesetzt, die Fehleranzahl liegt unter der Indexereinstellung **Max. Fehlerelemente**.

Wenn die letzte Ausführung mit einem Fehler beendet wurde, lautet der Status **Fehler**. Der Status **Zurückgesetzt** bedeutet, dass der Änderungsnachverfolgungsstatus des Indexers zurückgesetzt wurde.

Klicken Sie auf einen Indexer in der Liste, um weitere Informationen zur aktuellen Ausführung sowie zu vergangen Ausführungen des Indexers anzuzeigen.

   ![Indexerzusammenfassung und Ausführungsverlauf](media/search-monitor-indexers/indexer-summary.png "Indexerzusammenfassung und Ausführungsverlauf")

Im Diagramm **Indexerzusammenfassung** wird die Anzahl verarbeiteter Dokumente der letzten Ausführungen grafisch dargestellt.

Die Liste **Ausführungsdetails** enthält bis zu 50 der letzten Ausführungsergebnisse.

Klicken Sie in der Liste auf ein Ausführungsergebnis, um Einzelheiten zur entsprechenden Ausführung anzuzeigen. Hierzu zählen unter anderem die Start- und Endzeit sowie möglicherweise aufgetretene Fehler und Warnungen.

   ![Ausführungsdetails eines Indexers](media/search-monitor-indexers/indexer-execution.png "Ausführungsdetails eines Indexers")

Sollten bei der Ausführung dokumentspezifische Probleme aufgetreten sein, werden diese in den Feldern „Fehler“ und „Warnungen“ aufgeführt.

   ![Indexerdetails mit Fehlern](media/search-monitor-indexers/indexer-execution-error.png "Indexerdetails mit Fehlern")

Warnungen sind bei bestimmten Arten von Indexern keine Seltenheit und deuten nicht immer auf ein Problem hin. So werden beispielsweise von Indexern, die Cognitive Services verwenden, unter Umständen Warnungen ausgegeben, wenn Bild- oder PDF-Dateien keinen zu verarbeitenden Text enthalten.

Weitere Informationen zur Untersuchung von Indexerfehlern und -warnungen finden Sie unter [Beheben von häufigen Problemen bei Suchindexern in der kognitiven Azure-Suche](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Überwachung mithilfe von REST-APIs

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

Ausführlichere Informationen zu Statuscodes und zu Indexerüberwachungsdaten finden Sie unter [GetIndexerStatus](/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Überwachung per .NET SDK

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