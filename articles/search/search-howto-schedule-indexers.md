---
title: Festlegen eines Zeitplans für Indexer – Azure Search
description: Legen Sie einen Zeitplan für Azure Search-Indexer fest, um Inhalte in regelmäßigen Abständen oder zu bestimmten Zeiten zu indizieren.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755478"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Festlegen eines Zeitplans für Indexer in Azure Search
Ein Indexer wird normal einmal ausgeführt, und zwar sofort nach der Erstellung. Sie können ihn über das Portal, die REST-API oder das .NET SDK nach Bedarf erneut ausführen. Sie können den Indexer auch so konfigurieren, dass er regelmäßig nach einem Zeitplan ausgeführt wird.

Situationen, in denen das Festlegen eines Zeitplans für den Indexer nützlich ist:

* Quelldaten ändern sich im Laufe der Zeit, und die Azure Search-Indexer sollen die geänderten Daten automatisch verarbeiten.
* Der Index wird aus mehreren Datenquellen aufgefüllt, und Sie möchten sicherstellen, dass die Indexer zu unterschiedlichen Zeiten ausgeführt werden, um Konflikte zu vermeiden.
* Die Quelldaten sind sehr groß, und die Indexerausführung soll auf verschiedene Zeiten verteilt werden. Weitere Informationen zur Indizierung großer Datenmengen finden Sie unter [Indizieren großer Datasets in Azure Search](search-howto-large-index.md).

Der Scheduler ist ein integriertes Feature von Azure Search. Sie können keinen externen Scheduler verwenden, um Azure Search-Indexer zu steuern.

## <a name="define-schedule-properties"></a>Definieren der Zeitplaneigenschaften

Ein Indexerzeitplan besitzt zwei Eigenschaften:
* **Intervall**: Definiert die Zeitspanne zwischen geplanten Indexerausführungen. Das kleinste zulässige Intervall beträgt 5 Minuten, und das größte 24 Stunden.
* **Startzeit (UTC)** : Gibt an, wann der Indexer zum ersten Mal ausgeführt werden soll.

Sie können einen Zeitplan festlegen, wenn Sie den Indexer erstmalig erstellen, oder die Eigenschaften des Indexers später aktualisieren. Zeitpläne für Indexer können mit dem [Portal](#portal), der [REST-API](#restApi) oder dem [.NET SDK](#dotNetSdk) festgelegt werden.

Nur eine Ausführung eines Indexers kann zu einem gegebenen Zeitpunkt ausgeführt werden. Wird ein Indexer zu dem Zeitpunkt, für den seine nächste Ausführung geplant ist, bereits ausgeführt, wird diese Ausführung auf den nächsten geplanten Zeitpunkt verschoben.

Betrachten wir ein Beispiel, um dies zu konkreter veranschaulichen. Angenommen, Sie haben einen Indexerzeitplan mit dem **Intervall** „Stündlich“ und der **Startzeit** „1. Juni 2019 um 8:00:00 Uhr (UTC)“ konfiguriert. Folgendes könnte passieren, wenn eine Indexerausführung länger als eine Stunde dauert:

* Die erste Indexerausführung beginnt am 1. Juni 2019 um oder gegen 8:00 Uhr (UTC). Angenommen, diese Ausführung dauert 20 Minuten (oder hat eine andere Dauer unter 1 Stunde).
* Die zweite Indexerausführung beginnt am 1. Juni 2019 um oder gegen 9:00 Uhr (UTC). Angenommen, diese Ausführung dauert 70 Minuten – also mehr als einer Stunde – und wird erst gegen 10:10 Uhr (UTC) abgeschlossen.
* Der Start der dritten Ausführung ist für 10:00 Uhr (UTC) geplant, aber zu diesem Zeitpunkt ist die vorherige Ausführung noch nicht abgeschlossen. Diese geplante Ausführung wird dann übersprungen. Die nächste Ausführung des Indexers startet somit erst um 11:00 Uhr (UTC).

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definieren eines Zeitplans im Portal

Mit dem Datenimport-Assistenten im Portal können Sie den Zeitplan für einen Indexer zum Zeitpunkt der Erstellung definieren. Die Standardeinstellung für den Zeitplan ist **Stündlich**. Das bedeutet, dass der Indexer einmal nach der Erstellung ausgeführt und die Ausführung dann stündlich einmal wiederholt wird.

Sie können die Zeitplaneinstellung auf **Einmal** ändern, wenn der Indexer nicht automatisch erneut ausgeführt werden soll, oder auf **Täglich**, um ihn einmal pro Tag auszuführen. Legen Sie ihn auf **Benutzerdefiniert** fest, wenn Sie ein anderes Intervall oder eine bestimmte Startzeit in der Zukunft angeben möchten.

Wenn Sie den Zeitplan auf **Benutzerdefiniert** festlegen, werden weitere Felder angezeigt, in denen Sie das **Intervall** und die **Startzeit (UTC)** angeben können. Das kürzeste zulässige Zeitintervall ist 5 Minuten und das längste 1440 Minuten (24 Stunden).

   ![Festlegen des Indexerzeitplans im Datenimport-Assistenten](media/search-howto-schedule-indexers/schedule-import-data.png "Festlegen des Indexerzeitplans im Datenimport-Assistenten")

Nach der Erstellung eines Indexers können Sie die Zeitplaneinstellungen mithilfe des Bearbeitungsbereichs im Indexer ändern. Die Zeitplanfelder im Datenimport-Assistent sind identisch.

   ![Festlegen des Zeitplans im Bearbeitungsbereich des Indexers](media/search-howto-schedule-indexers/schedule-edit.png "Festlegen des Zeitplans im Bearbeitungsbereich des Indexers")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definieren eines Zeitplans mit der REST-API

Sie können den Zeitplan für einen Indexer mithilfe der REST-API definieren. Dazu fügen Sie die **schedule**-Eigenschaft beim Erstellen oder Aktualisieren des Indexers hinzu. Das folgende Beispiel zeigt eine PUT-Anforderung zum Aktualisieren eines vorhandenen Indexers:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Der Parameter **interval** ist erforderlich. Das Intervall bezieht sich auf den Zeitraum zwischen dem Start von zwei aufeinander folgenden Indexerausführungen. Das kleinste zulässige Intervall beträgt 5 Minuten. Das längste ist ein Tag. Es muss als XSD-Wert „dayTimeDuration“ formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitwerts](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Das Muster hierfür lautet wie folgt: `P(nD)(T(nH)(nM))`. Beispiele: `PT15M` = alle 15 Minuten, `PT2H` = alle 2 Stunden.

Der optionale Parameter **startTime** gibt an, wann die geplanten Ausführungen beginnen sollen. Wird er weggelassen, wird die aktuelle UTC-Zeit verwendet. Diese Zeitangabe kann in der Vergangenheit liegen. In diesem Fall wird die erste Ausführung so geplant, als wäre der Indexer seit der ursprünglich mit **startTime** angegebenen Startzeit kontinuierlich ausgeführt worden.

Sie können einen Indexer bei Bedarf auch jederzeit mit dem Aufruf „Indexer ausführen“ ausführen. Weitere Informationen zum Ausführen von Indexern und zum Festlegen von Indexerzeitplänen finden Sie unter [Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer) (Ausführen eines Indexers), [Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer) (Abrufen eines Indexers) und [Update Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) (Aktualisieren eines Indexers) in der REST-API-Referenz.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definieren eines Zeitplans mit dem .NET SDK

Sie können den Zeitplan für einen Indexer mithilfe des Azure Search .NET SDK definieren. Dazu fügen Sie die **schedule**-Eigenschaft beim Erstellen oder Aktualisieren eines Indexers hinzu.

Das folgende C#-Beispiel erstellt einen Indexer unter Verwendung einer vordefinierten Datenquelle und eines Index, und legt den Zeitplan so fest, dass der Indexer in 30 Minuten ab jetzt zum ersten Mal und dann einmal pro Tag ausgeführt wird:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Wenn der **schedule**-Parameter ausgelassen wird, wird der Indexer nur einmal ausgeführt, unmittelbar nachdem er erstellt wurde.

Der **startTime**-Parameter kann auf einen Zeitpunkt in der Vergangenheit festgelegt werden. In diesem Fall wird die erste Ausführung so geplant, als wäre der Indexer seit der mit **startTime** angegebenen Startzeit kontinuierlich ausgeführt worden.

Der Zeitplan wird mit der [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet)-Klasse definiert. Der **IndexingSchedule**-Konstruktor erfordert einen **interval**-Parameter, der mit einem **TimeSpan**-Objekt angegeben wird. Das kleinste zulässige Intervall beträgt 5 Minuten, und das größte 24 Stunden. Der zweite **startTime**-Parameter, der als ein **DateTimeOffset**-Objekt angegeben wird, ist optional.

Mit dem .NET SDK können Sie die Indexervorgänge mithilfe der [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)-Klasse und der zugehörigen [Indexers](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers)-Eigenschaft steuern, die die Methoden aus der **IIndexersOperations**-Schnittstelle implementiert. 

Sie können einen Indexer bei Bedarf jederzeit mit einem der [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync) oder [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)-Methoden ausführen.

Weitere Informationen zum Erstellen, Aktualisieren und Ausführen von Indexern finden Sie unter [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
