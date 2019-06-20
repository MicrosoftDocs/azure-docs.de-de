---
title: 'Azure Cosmos DB: SQL .NET Core-API, -SDK und -Ressourcen'
description: Hier finden Sie umfassende Informationen zur SQL .NET Core-API und zum entsprechenden SDK – einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen der einzelnen Versionen des Azure Cosmos DB .NET Core SDKs.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 4ec9d5e605a2319a04dac4cb52dbe49c77354479
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510639"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK für SQL-API: Versionshinweise und Ressourcen
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Änderungsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor: .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor: Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK-Download**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API-Dokumentation**|[.NET API-Referenzdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Beispiele**|[.NET-Codebeispiele](sql-api-dotnet-samples.md)|
|**Erste Schritte**|[Azure Cosmos DB: Erste Schritte mit der DocumentDB-API und .NET Core](sql-api-dotnet-core-get-started-preview.md)|
|**Web-App-Tutorial**|[Entwicklung von Webanwendungen mit Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuelles unterstütztes Framework**|[.NET Standard 1.6 und .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Versionsinformationen

Die Features des Azure Cosmos DB .NET Core SDKs sind mit den Features der neuesten Version des [Azure Cosmos DB .NET SDKs](sql-api-sdk-dotnet.md) identisch.

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-preview
* Preview 1 von [Version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) des .NET SDK als Public Preview.
* Ziel: .NET Standard, unterstützt .NET Framework 4.6.1 und höher sowie .NET Core 2.0 und höher
* Neues Objektmodell, bei dem CosmosClient und Methoden der obersten Ebene auf die entsprechenden Cosmos-Datenbanken, -Container und -Elementklassen aufgeteilt werden.
* Unterstützung von Streams.
* Aktualisierter CosmosResponseMessage-Statuscode für die Antwort vom Server. Eine Ausnahme wird nur ausgelöst, wenn keine Antwort zurückgegeben wird.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Die System.Net.Http-Version des SDK entspricht der Definition aus dem NuGet-Paket.
* Höhere Dezimalgenauigkeit für LINQ-Abfragen
* Neu hinzugefügte Klassen: CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType, PartitionKeyDefinitionVersion
* „TimeToLivePropertyPath“ zu „DocumentCollection“ hinzugefügt
* „CompositeIndexes“ und „SpatialIndexes“ zu „IndexPolicy“ hinzugefügt
* „Version“ zu „PartitionKeyDefinition“ hinzugefügt
* „None“ zu „PartitionKey“ hinzugefügt

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * „IdleTcpConnectionTimeout“, „OpenTcpConnectionTimeout“, „MaxRequestsPerTcpConnection“ und „MaxTcpConnectionsPerEndpoint“ zu „ConnectionPolicy“ hinzugefügt
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Verbesserungen bei der Diagnose

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Umgebungsvariableneinstellung „POCOSerializationOnly“ hinzugefügt.

* „DocumentDB.Spatial.Sql.dll“ wurde entfernt und ist jetzt in „Microsoft.Azure.Documents.ServiceInterop.dll“ enthalten.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Verbesserung der Wiederholungslogik während des Failovers für StoredProcedure-Ausführungsaufrufe.

* DocumentClientEventSource-Singleton wurde vorgenommen. 

* Behebung des Problems, dass GatewayAddressCache-Timeout ConnectionPolicy RequestTimeout nicht berücksichtigt.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Für die direkte/TCP-Transportdiagnose wurde eine Ausnahme vom Typ „TransportException“, ein interner Ausnahmetyp des SDKs, hinzugefügt. Wenn dieser Typ in Ausnahmemeldungen vorhanden ist, werden zusätzliche Informationen für die Behebung von Problemen mit der Clientkonnektivität ausgegeben.

* Es wurde eine neue Konstruktorüberladung hinzugefügt, die einen HTTP-Meldungshandler, ein HTTP-Handlerstack zum Senden von HttpClient-Anforderungen (z. B. HttpClientHandler), verwendet.

* Korrektur des Fehlers, durch den Header mit Null-Werten nicht ordnungsgemäß verarbeitet wurden.

* Verbesserte Validierung des Caches für die Sammlung.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* System.Net.Security in 4.3.2 aktualisiert.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Verbesserungen der Diagnoseablaufverfolgung.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Mehr Resilienz gegenüber vorübergehenden Anforderungsfehlern in mehreren Regionen hinzugefügt.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Unterstützung für Schreibvorgänge in mehreren Regionen hinzugefügt.
* Partitionsübergreifende Verbesserungen der Abfrageleistung bei TOP und MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Unterstützung für den Abbruch von Anforderungen hinzugefügt.
* SetCurrentLocation zu ConnectionPolicy hinzugefügt, sodass automatisch die bevorzugten Standorte basierend auf der Region automatisch aufgefüllt werden.
* Fehler in partitionsübergreifenden Abfragen mit Min./Max. und einem Filter behoben, durch den keine Dokumente auf einer einzelnen Partition gefunden werden konnten.
* DocumentClient-Methoden haben nun Parität mit IDocumentClient.
* Direkte TCP-Transportstapel aktualisiert, um die Anzahl der hergestellten Verbindungen zu verringern.
* Unterstützung von Direktmodus-TCP für Nicht-Windows-Clients hinzugefügt.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Unterstützung für den Abbruch von Anforderungen hinzugefügt.
* SetCurrentLocation zu ConnectionPolicy hinzugefügt, sodass automatisch die bevorzugten Standorte basierend auf der Region automatisch aufgefüllt werden.
* Fehler in partitionsübergreifenden Abfragen mit Min./Max. und einem Filter behoben, durch den keine Dokumente auf einer einzelnen Partition gefunden werden konnten.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* DocumentClient-Methoden haben nun Parität mit IDocumentClient.
* Direkte TCP-Transportstapel aktualisiert, um die Anzahl der hergestellten Verbindungen zu verringern.
* Unterstützung von Direktmodus-TCP für Nicht-Windows-Clients hinzugefügt.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* ConsistencyLevel-Eigenschaft zu „FeedOptions“ hinzugefügt.
* „JsonSerializerSettings“ zu „RequestOptions“ und „FeedOptions“ hinzugefügt.
* „EnableReadRequestsFallback“ zu „ConnectionPolicy“ hinzugefügt.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Korrektur von KeyNotFoundException für die partitionsübergreifende Reihenfolge von Abfragen in Ausnahmefällen.
* Korrektur eines Fehlers, bei dem ein JsonProperty-Attribut in der Select-Klausel für LINQ-Abfragen nicht berücksichtigt wurde.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Korrektur des Fehlers, der unter bestimmten Racebedingungen zu vorübergehenden Fehlern vom Typ „Microsoft.Azure.Documents.NotFoundException: Die Lesesitzung für das eingegebene Sitzungstoken ist nicht verfügbar“ geführt hat (bei Verwendung der Sitzungskonsistenzebene).

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Die Regression wurde korrigiert, bei der „FeedOptions.MaxItemCount = -1“ einen System.ArithmeticException-Fehler mit dem Hinweis ausgelöst hat, dass die Seitengröße negativ ist.
* Eine neue ToString()-Funktion wurde zu „QueryMetrics“ hinzugefügt.
* Partitionsstatistiken wurden beim Lesen von Sammlungen verfügbar gemacht.
* Die PartitionKey-Eigenschaft wurde zu „ChangeFeedOptions“ hinzugefügt.
* Kleinere Fehlerbehebungen

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Fügt die Möglichkeit hinzu, eindeutige Indizes für die Dokumente anzugeben, indem die UniqueKeyPolicy-Eigenschaft für die DocumentCollection-Klasse verwendet wird.
 * Korrektur eines Fehlers, bei dem die benutzerdefinierten JsonSerializer-Einstellungen für einige Abfragen sowie die Ausführung einer gespeicherten Prozedur nicht berücksichtigt wurden.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Brandingänderung von Azure DocumentDB in Azure Cosmos DB in der API-Referenzdokumentation, in Metadateninformationen in Assemblys sowie im NuGet-Paket.
 * Verfügbarmachung von Diagnoseinformationen und Wartezeit aus der Antwort gesendeter Anforderungen mit dem direkten Konnektivitätsmodus. Die Eigenschaftsnamen für die Klasse „ResourceResponse“ lauten „RequestDiagnosticsString“ und „RequestLatency“.
 * Für dieses SDK muss die neueste Version des Azure Cosmos DB-Emulators vorhanden sein. Diese können Sie unter https://aka.ms/cosmosdb-emulator herunterladen.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Mehrere Korrekturen und Verbesserungen hinsichtlich der Zuverlässigkeit wurden hinzugefügt.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Interne Änderungen im Zusammenhang mit der Unterstützung von [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Unterstützung für PartitionKeyRangeId als FeedOption für die Bereichsabfrageergebnisse eines bestimmten Werts des Partitionsschlüsselbereichs wurde hinzugefügt.
* Unterstützung für StartTime als ChangeFeedOption als Ausgangspunkt für die Änderungen nach diesem Zeitpunkt wurde hinzugefügt.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   In der JsonSerializable-Klasse wurde ein Problem behoben, das zu einer Ausnahme durch Stapelüberlauf führen konnte.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Unterstützung für das Angeben von benutzerdefinierten Einstellungen vom Typ JsonSerializerSettings beim Instanziieren einer [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet)-Instanz wurde hinzugefügt.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Unterstützung von .NET Standard 1.5 als eines der Ziel-Frameworks.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Ein Problem mit x64-Computern, die die SSE4-Anweisung nicht unterstützen und beim Ausführen von Azure Cosmos DB-Abfragen eine SEHException-Ausnahme auslösen, wurde behoben.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Es wurde Unterstützung für eine neue Konsistenzebene mit dem Namen „ConsistentPrefix“ hinzugefügt.
*   Es wurde Unterstützung für Abfragemetriken für einzelne Partitionen hinzugefügt.
*   Es wurde Unterstützung für die Größenbeschränkung des Fortsetzungstoken für Abfragen hinzugefügt.
*   Es wurde Unterstützung für eine ausführlichere Ablaufverfolgung für Anforderungsfehler hinzugefügt.
*   Es wurden einige Leistungsverbesserungen im SDK vorgenommen.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Problem behoben, bei dem der in „FeedOptions“ für aggregierte Abfragen angegebene „PartitionKey“-Wert ignoriert wurde.
* Problem bei der transparenten Durchführung der Partitionsverwaltung im Verlauf einer partitionsübergreifenden Ausführung von ORDER BY-Abfragen behoben.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Es wurde ein Fehler behoben, der Deadlocks in einigen der asynchronen APIs verursacht hat, wenn diese im ASP.NET-Kontext verwendet wurden.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Es wurden Fehler behoben, um das SDK für automatische Failover unter bestimmten Bedingungen stabiler zu machen.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Korrektur eines Problems, das bisweilen einen Fehler des Typs „WebException: Der Remotename konnte nicht aufgelöst werden.“ verursacht.
* Unterstützung zum direkten Lesen eines eingegebenen Dokuments durch Hinzufügen neuer Überladungen zur ReadDocumentAsync-API.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* LINQ-Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt.
* Behebung eines Problems mit einem Arbeitsspeicherverlust für das ConnectionPolicy-Objekt, das durch die Verwendung des Ereignishandlers verursacht wurde.
* Behebung eines Problems, bei dem UpsertAttachmentAsync nicht funktionierte, wenn ETag verwendet wurde.
* Behebung eines Problems, bei dem die partitionsübergreifende Fortsetzung einer order-by-Abfrage nicht funktionierte, wenn ein Zeichenfolgenfeld sortiert wurde.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt. Siehe [Aggregationsunterstützung](how-to-sql-query.md#Aggregates).
* Minimaler Durchsatz für partitionierte Sammlungen wurde von 10.100 RU/s auf 2.500 RU/s gesenkt.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Mit dem .NET Core SDK für Azure Cosmos DB können Sie schnelle, plattformübergreifende [ASP.NET Core](https://www.asp.net/core)- und [.NET Core](https://www.microsoft.com/net/core#windows)-Apps für Windows, Mac und Linux erstellen. Die neueste Version des .NET Core SDKs für Azure Cosmos DB ist vollständig mit [Xamarin](https://www.xamarin.com) kompatibel und kann zum Erstellen von Anwendungen für iOS, Android und Mono (Linux) verwendet werden.  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Mit der Vorschauversion des .NET Core SDKs für Azure Cosmos DB können Sie schnelle, plattformübergreifende [ASP.NET Core](https://www.asp.net/core)- und [.NET Core](https://www.microsoft.com/net/core#windows)-Apps für Windows, Mac und Linux erstellen.

Die Features der Vorschauversion des Azure Cosmos DB .NET Core SDKs sind mit den Features der neuesten Version des [Azure Cosmos DB .NET SDKs](sql-api-sdk-dotnet.md) identisch und unterstützen Folgendes:
* Alle [Verbindungsmodi](performance-tips.md#networking): Gatewaymodus, TCP direkt und HTTPs direkt.
* Alle [Konsistenzebenen](consistency-levels.md): Stark, Sitzung, Begrenzte Veraltung und Letztlich.
* [Partitionierte Sammlungen](partition-data.md).
* [Datenbankkonten in mehreren Regionen und Georeplikation](distribute-data-globally.md).

Wenn Sie Fragen zu diesem SDK haben, stellen Sie diese bei [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb), oder melden Sie im [GitHub-Repository](https://github.com/Azure/azure-documentdb-dotnet/issues) ein Problem.

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.4.0](#2.4.0) |5\. Mai 2019 |--- |
| [2.3.0](#2.3.0) |4\. April 2019 |--- |
| [2.2.3](#2.2.3) |11\. März 2019 |--- |
| [2.2.2](#2.2.2) |6\. Februar 2019 |--- |
| [2.2.1](#2.2.1) |24\. Dezember 2018 |--- |
| [2.2.0](#2.2.0) |07\. Dezember 2018 |--- |
| [2.1.3](#2.1.3) |15\. Oktober 2018 |--- |
| [2.1.2](#2.1.2) |4\. Oktober 2018 |--- |
| [2.1.1](#2.1.1) |27\. September 2018 |--- |
| [2.1.0](#2.1.0) |21\. September 2018 |--- |
| [2.0.0](#2.0.0) |07\. September 2018 |--- |
| [1.9.1](#1.9.1) |09\. März 2018 |--- |
| [1.8.2](#1.8.2) |21\. Februar 2018 |--- |
| [1.8.1](#1.8.1) |05\. Februar 2018 |--- |
| [1.7.1](#1.7.1) |16\. November 2017 |--- |
| [1.7.0](#1.7.0) |10\. November 2017 |--- |
| [1.6.0](#1.6.0) |17\. Oktober 2017 |--- |
| [1.5.1](#1.5.1) |02\. Oktober 2017 |--- |
| [1.5.0](#1.5.0) |10\. August 2017 |--- | 
| [1.4.1](#1.4.1) |07\. August 2017 |--- |
| [1.4.0](#1.4.0) |02\. August 2017 |--- |
| [1.3.2](#1.3.2) |12\. Juni 2017 |--- |
| [1.3.1](#1.3.1) |23\. Mai 2017 |--- |
| [1.3.0](#1.3.0) |10\. Mai 2017 |--- |
| [1.2.2](#1.2.2) |19\. April 2017 |--- |
| [1.2.1](#1.2.1) |29\. März 2017 |--- |
| [1.2.0](#1.2.0) |25\. März 2017 |--- |
| [1.1.2](#1.1.2) |20\. März 2017 |--- |
| [1.1.1](#1.1.1) |14\. März 2017 |--- |
| [1.1.0](#1.1.0) |16\. Februar 2017 |--- |
| [1.0.0](#1.0.0) |21\. Dezember 2016 |--- |
| [0.1.0-preview](#0.1.0-preview) |15\. November 2016 |31\. Dezember 2016 |

## <a name="see-also"></a>Siehe auch
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

