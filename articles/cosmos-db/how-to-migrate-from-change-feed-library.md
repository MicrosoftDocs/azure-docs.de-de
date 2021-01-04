---
title: Migrieren von der Änderungsfeed-Verarbeitungsbibliothek zum Azure Cosmos DB .NET V3 SDK
description: Hier erfahren Sie, wie Sie Ihre Anwendung von der Verwendung der Änderungsfeed-Verarbeitungsbibliothek zum Azure Cosmos DB SDK V3 migrieren.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: ce2d4d3ad3ae349718f01584ec077b18e11e4f8d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341262"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrieren von der Änderungsfeed-Verarbeitungsbibliothek zum Azure Cosmos DB .NET V3 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden die erforderlichen Schritte zum Migrieren eines vorhandenen Anwendungscodes, der die [Änderungsfeed-Verarbeitungsbibliothek](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) verwendet, zum [Änderungsfeed](change-feed.md)-Feature in der neuesten Version des .NET SDK (auch als .NET V3 SDK bezeichnet) beschrieben.

## <a name="required-code-changes"></a>Erforderliche Codeänderungen

Das .NET V3 SDK weist mehrere wichtige Änderungen auf. Nachfolgend sind die wichtigsten Schritte zum Migrieren Ihrer Anwendung aufgeführt:

1. Wandeln Sie die `DocumentCollectionInfo`-Instanzen in `Container`-Verweise für die überwachten Container und Leasescontainer um.
1. Anpassungen, die `WithProcessorOptions` verwenden, sollten für die Verwendung von `WithLeaseConfiguration` und `WithPollInterval` für Intervalle, `WithStartTime` [für die Startzeit](./change-feed-processor.md#starting-time) und `WithMaxItems` zum Definieren der maximalen Anzahl von Elementen aktualisiert werden.
1. Legen Sie `processorName` für `GetChangeFeedProcessorBuilder` entsprechend dem für `ChangeFeedProcessorOptions.LeasePrefix` konfigurierten Wert fest, oder verwenden Sie andernfalls `string.Empty`.
1. Die Änderungen werden nicht mehr als `IReadOnlyList<Document>` übermittelt, sondern es handelt sich um eine `IReadOnlyCollection<T>`, wobei `T` für einen von Ihnen zu definierenden Typ steht. Es gibt keine Basiselementklasse mehr.
1. Zum Handhaben der Änderungen ist keine Implementierung mehr erforderlich, sondern Sie müssen einen [Delegaten definieren](change-feed-processor.md#implementing-the-change-feed-processor). Der Delegat kann eine statische Funktion sein, oder Sie können eine eigene Klasse erstellen und eine Instanzmethode als Delegat übergeben, wenn Sie den Status über mehrere Ausführungen hinweg beibehalten müssen.

Wenn der ursprüngliche Code zum Erstellen des Änderungsfeedprozessors beispielsweise wie folgt aussieht:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Sieht der migrierte Code folgendermaßen aus:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

Der Delegat kann eine statische Methode sein:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Status und Leasecontainer

Ähnlich wie bei der Änderungsfeed-Verarbeitungsbibliothek verwendet das Feature für Änderungsfeeds im .NET V3 SDK einen [Leasecontainer](change-feed-processor.md#components-of-the-change-feed-processor) zum Speichern des Status. Allerdings ist das Schema unterschiedlich.

Der SDK V3-Änderungsfeedprozessor erkennt jeden alten Bibliotheksstatus und migriert ihn bei der ersten Ausführung des migrierten Anwendungscodes automatisch in das neue Schema. 

Sie können die Anwendung mithilfe des alten Codes gefahrlos beenden, den Code zur neuen Version migrieren und die migrierte Anwendung starten. Alle Änderungen, die während des Beendens der Anwendung auftraten, werden von der neuen Version übernommen und verarbeitet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Verwendungsbeispiele auf GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Zusätzliche Beispiele auf GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über den Änderungsfeedprozessor:

* [Änderungsfeedprozessor in Azure Cosmos DB](change-feed-processor.md)
* [Use the change feed estimator](how-to-use-change-feed-estimator.md) (Verwenden des Änderungsfeed-Estimators)
* [Konfigurieren der Startzeit des Änderungsfeedprozessors](./change-feed-processor.md#starting-time)