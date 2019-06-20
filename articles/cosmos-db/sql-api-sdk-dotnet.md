---
title: 'Azure Cosmos DB: SQL .NET-API, -SDK und -Ressourcen'
description: Erhalten Sie alle Informationen zu SQL .NET-API und -SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 3c420882b734883039ec95d609c155617359fa25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510720"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK für SQL-API: Download und Anmerkungen zum Release
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
|**SDK-Download**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API-Dokumentation**|[.NET API-Referenzdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Beispiele**|[.NET-Codebeispiele](sql-api-dotnet-samples.md)|
|**Erste Schritte**|[Erste Schritte mit dem Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Web-App-Tutorial**|[Entwicklung von Webanwendungen mit Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuelles unterstütztes Framework**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Versionshinweise

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

* Verbesserungen der Diagnoseablaufverfolgung

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

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* ConsistencyLevel-Eigenschaft zu „FeedOptions“ hinzugefügt.
* „JsonSerializerSettings“ zu „RequestOptions“ und „FeedOptions“ hinzugefügt.
* „EnableReadRequestsFallback“ zu „ConnectionPolicy“ hinzugefügt.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Korrektur von KeyNotFoundException für die partitionsübergreifende Reihenfolge von Abfragen in Ausnahmefällen.
* Korrektur eines Fehlers, bei dem ein JsonProperty-Attribut in der Select-Klausel für LINQ-Abfragen nicht berücksichtigt wurde.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Korrektur des Fehlers, der unter bestimmten Racebedingungen zu vorübergehenden Fehlern vom Typ „Microsoft.Azure.Documents.NotFoundException: Die Lesesitzung für das eingegebene Sitzungstoken ist nicht verfügbar“ geführt hat (bei Verwendung der Sitzungskonsistenzebene).

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Die Regression wurde korrigiert, bei der „FeedOptions.MaxItemCount = -1“ einen System.ArithmeticException-Fehler mit dem Hinweis ausgelöst hat, dass die Seitengröße negativ ist.
* Eine neue ToString()-Funktion wurde zu „QueryMetrics“ hinzugefügt.
* Partitionsstatistiken wurden beim Lesen von Sammlungen verfügbar gemacht.
* Die PartitionKey-Eigenschaft wurde zu „ChangeFeedOptions“ hinzugefügt.
* Kleinere Fehlerbehebungen

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Fügt die Möglichkeit hinzu, eindeutige Indizes für die Dokumente anzugeben, indem die UniqueKeyPolicy-Eigenschaft für die DocumentCollection-Klasse verwendet wird.
* Korrektur eines Fehlers, bei dem die benutzerdefinierten JsonSerializer-Einstellungen für einige Abfragen sowie die Ausführung einer gespeicherten Prozedur nicht berücksichtigt wurden.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Brandingänderung von Azure DocumentDB in Azure Cosmos DB in der API-Referenzdokumentation, in Metadateninformationen in Assemblys sowie im NuGet-Paket. 
* Verfügbarmachung von Diagnoseinformationen und Wartezeit aus der Antwort gesendeter Anforderungen mit dem direkten Konnektivitätsmodus. Die Eigenschaftsnamen für die Klasse „ResourceResponse“ lauten „RequestDiagnosticsString“ und „RequestLatency“.
* Für dieses SDK muss die neueste Version des Azure Cosmos DB-Emulators vorhanden sein. Diese können Sie unter https://aka.ms/cosmosdb-emulator herunterladen. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Interne Änderungen für Friend-Assemblys von Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Mehrere Korrekturen und Verbesserungen hinsichtlich der Zuverlässigkeit wurden hinzugefügt.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Unterstützung für PartitionKeyRangeId als FeedOption für die Bereichsabfrageergebnisse eines bestimmten Werts des Partitionsschlüsselbereichs wurde hinzugefügt. 
* Unterstützung für StartTime als ChangeFeedOption als Ausgangspunkt für die Änderungen nach diesem Zeitpunkt wurde hinzugefügt.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* In der JsonSerializable-Klasse wurde ein Problem behoben, das zu einer Ausnahme durch Stapelüberlauf führen konnte.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Ein Problem wurde behoben, das die erneute Kompilierung der Anwendung aufgrund der Einführung von JsonSerializerSettings als optionaler Parameter im DocumentClient-Konstruktor erforderte.
* Der DocumentClient-Konstruktor wurde als veraltet markiert. Bei diesem Konstruktor wurde JsonSerializerSettings als letzter Parameter vorausgesetzt, um beim Übergeben des JsonSerializerSettings-Parameters Standardwerte für den ConnectionPolicy- und den ConsistencyLevel-Parameter zuzulassen.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Unterstützung für das Angeben von benutzerdefinierten Einstellungen vom Typ „JsonSerializerSettings“ beim Instanziieren von [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) wurde hinzugefügt.

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Ein Problem mit x64-Computern, die die SSE4-Anweisung nicht unterstützen und beim Ausführen von Azure Cosmos DB-SQL-Abfragen eine SEHException-Ausnahme auslösen, wurde behoben.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Es wurde Unterstützung für eine neue Konsistenzebene mit dem Namen „ConsistentPrefix“ hinzugefügt.
*   Es wurde Unterstützung für Abfragemetriken für einzelne Partitionen hinzugefügt.
*   Es wurde Unterstützung für die Größenbeschränkung des Fortsetzungstoken für Abfragen hinzugefügt.
*   Es wurde Unterstützung für eine ausführlichere Ablaufverfolgung für Anforderungsfehler hinzugefügt.
*   Es wurden einige Leistungsverbesserungen im SDK vorgenommen.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funktionell identisch mit 1.13.3. Es wurden einige interne Änderungen vorgenommen.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funktionell identisch mit 1.13.2. Es wurden einige interne Änderungen vorgenommen.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Problem behoben, bei dem der in „FeedOptions“ für aggregierte Abfragen angegebene „PartitionKey“-Wert ignoriert wurde.
* Problem bei der transparenten Durchführung der Partitionsverwaltung im Verlauf einer partitionsübergreifenden Ausführung von ORDER BY-Abfragen behoben.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Es wurde ein Fehler behoben, der Deadlocks in einigen der asynchronen APIs verursacht hat, wenn diese im ASP.NET-Kontext verwendet wurden.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Es wurden Fehler behoben, um das SDK für automatische Failover unter bestimmten Bedingungen stabiler zu machen.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Korrektur eines Problems, das bisweilen eine WebException verursacht: Der Remotename konnte nicht aufgelöst werden.“ verursacht.
* Unterstützung zum direkten Lesen eines eingegebenen Dokuments durch Hinzufügen neuer Überladungen zur ReadDocumentAsync-API.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ-Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt.
* Behebung eines Problems mit einem Arbeitsspeicherverlust für das ConnectionPolicy-Objekt, das durch die Verwendung des Ereignishandlers verursacht wurde.
* Behebung eines Problems, bei dem UpsertAttachmentAsync nicht funktionierte, wenn ETag verwendet wurde.
* Behebung eines Problems, bei dem die partitionsübergreifende Fortsetzung einer order-by-Abfrage nicht funktionierte, wenn ein Zeichenfolgenfeld sortiert wurde.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt. Siehe [Aggregationsunterstützung](how-to-sql-query.md#Aggregates).
* Minimaler Durchsatz für partitionierte Sammlungen wurde von 10.100 RU/s auf 2.500 RU/s gesenkt.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Behebung eines Problems, bei dem einige der partitionsübergreifenden Abfragen im 32-Bit-Hostprozess fehlschlugen.
* Behebung eines Problems, bei dem der Sitzungscontainer nicht mit dem Token für fehlgeschlagene Anforderungen im Gateway-Modus aktualisiert wurde.
* Behebung eines Problems, bei dem eine Abfrage mit UDF-Aufrufen in der Projektion in einigen Fällen fehlschlug.
* Clientseitige Leistungskorrekturen zur Erhöhung des Lese- und Schreibdurchsatzes der Anforderungen.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Behebung eines Problems, bei dem der Sitzungscontainer nicht mit dem Token für fehlgeschlagene Anforderungen im Gateway-Modus aktualisiert wurde.
* Unterstützung für die Verwendung des SDK in einem 32-Bit-Hostprozess wurde hinzugefügt. Hinweis: Bei Verwendung von partitionsübergreifenden Abfragen wird die 64-Bit-Hostverarbeitung empfohlen, um eine bessere Leistung zu erzielen.
* Verbesserte Leistung für Szenarien im Zusammenhang mit Abfragen mit einer großen Anzahl von Partitionsschlüsselwerten in einem IN-Ausdruck.
* Verschiedene Ressourcenkontingentstatistiken in ResourceResponse für Leseanforderungen von Dokumentsammlungen wurden aufgefüllt, wenn die Anforderungsoption „PopulateQuotaInfo“ festgelegt ist.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* In 1.11.0 wurden kleinere Probleme mit der Leistung der CreateDocumentCollectionIfNotExistsAsync-API behoben.
* Behebung von Leistungsproblemen im SDK für Szenarien mit zahlreichen gleichzeitigen Anforderungen.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Unterstützung für neue Klassen und Methoden zum Verarbeiten des [Änderungsfeeds](change-feed.md) von Dokumenten in einer Sammlung.
* Unterstützung für die partitionsübergreifende Abfragefortsetzung und einige Leistungsverbesserungen für partitionsübergreifende Abfragen.
* Die Methoden CreateDatabaseIfNotExistsAsync und CreateDocumentCollectionIfNotExistsAsync wurden hinzugefügt.
* LINQ-Unterstützung für Systemfunktionen: IsDefined, IsNull und IsPrimitive.
* Behebung des Problems mit der automatischen Platzierung der Assemblys „Microsoft.Azure.Documents.ServiceInterop.dll“ und „DocumentDB.Spatial.Sql.dll“ im Ordner „bin“ der Anwendung bei Verwendung des NuGet-Pakets mit Projekten, die project.json-Tools enthalten.
* Unterstützung für das Ausgeben clientseitiger ETW-Ablaufverfolgungen, die in Debugszenarien hilfreich sein können.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Unterstützung direkter Verbindungen für partitionierte Sammlungen hinzugefügt.
* Verbesserte Leistung für die Konsistenzebene „Begrenzte Veraltung (Bounded Staleness)“.
* Polygon- und LineString-Datentypen beim Angeben der Sammlungsindizierungsrichtlinie für räumliche Geofencing-Abfragen hinzugefügt.
* LINQ-Unterstützung für StringEnumConverter, IsoDateTimeConverter und UnixDateTimeConverter beim Übersetzen von Prädikaten hinzugefügt.
* Verschiedene Fehlerbehebungen im SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Korrektur eines Problems, das die folgende Ausnahme vom Typ „Nicht gefunden“ („NotFoundException“) auslöste: Die Lesesitzung für das eingegebene Sitzungstoken ist nicht verfügbar. Diese Ausnahme trat in einigen Fällen bei Abfragen für die Leseregion eines geografisch verteilten Kontos auf.
* Es wurde die ResponseStream-Eigenschaft in der ResourceResponse-Klasse verfügbar gemacht, die einen direkten Zugriff auf den zugrunde liegenden Datenstrom aus einer Antwort ermöglicht.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Die Klassen „ResourceResponse“, „FeedResponse“, „StoredProcedureResponse“ und „MediaResponse“ wurden geändert, um die entsprechende öffentliche Schnittstelle zu implementieren, damit sie für die testgetriebene Bereitstellung (Test Driven Deployment, TDD) simuliert werden können.
* Ein Problem wurde behoben, das einen falsch formatierten Partitionsschlüssel-Header verursacht hat, wenn für die Datenserialisierung ein benutzerdefiniertes JsonSerializerSettings-Objekt verwendet wird.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Korrektur eines Fehlers, der dazu führte, dass Abfragen mit langer Ausführungszeit mit folgendem Fehler abgebrochen wurden: Autorisierungstoken ist zum aktuellen Zeitpunkt ungültig.
* Es wurde ein Fehler behoben, durch den die ursprüngliche SqlParameterCollection aus partitionsübergreifenden top/order-by-Abfragen entfernt wurde.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Unterstützung für parallele Abfragen für partitionierte Sammlungen hinzugefügt.
* Unterstützung für partitionsübergreifende ORDER BY-und TOP-Abfragen für partitionierte Sammlungen hinzugefügt.
* Fehlende Verweise auf „DocumentDB.Spatial.Sql.dll“ und „Microsoft.Azure.Documents.ServiceInterop.dll“ wurden behoben, die beim Referenzieren eines Azure Cosmos DB-Projekts mit einem Verweis auf das Azure Cosmos DB NuGet-Paket erforderlich sind.
* Ein Fehler in Bezug auf die Verwendung verschiedener Parametertypen in benutzerdefinierten Funktionen in LINQ wurde behoben. 
* Fehler bei global replizierten Konten behoben, bei dem Upsert-Aufrufe an Leseregionen anstatt Schreibregionen geleitet wurden.
* Der „IDocumentClient“-Schnittstelle fehlende Methoden hinzugefügt: 
  * „UpsertAttachmentAsync“-Methode, die „mediaStream“ und „options“ als Parameter akzeptiert
  * „CreateAttachmentAsync“-Methode, die „options“ als Parameter akzeptiert
  * „CreateOfferQuery“-Methode, die „querySpec“ als Parameter akzeptiert
* Nicht versiegelte öffentliche Klassen, die in der IDocumentClient-Schnittstelle verfügbar gemacht werden.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Unterstützung für Datenbankkonten in mehreren Regionen hinzugefügt.
* Unterstützung für Wiederholungsversuche für gedrosselte Anforderungen hinzugefügt.  Benutzer kann die Anzahl von Wiederholungsversuchen und die maximale Wartezeit durch Konfigurieren der ConnectionPolicy.RetryOptions-Eigenschaft anpassen.
* Es wurde eine neue IDocumentClient-Schnittstelle hinzugefügt, die die Signaturen aller DocumentClient-Eigenschaften und -Methoden definiert.  Im Rahmen dieser Änderung wurden auch die Erweiterungsmethoden zum Erstellen von IQueryable und IOrderedQueryable in Methoden der DocumentClient-Klasse selbst geändert.
* Eine Konfigurationsoption zum Festlegen des „ServicePoint.ConnectionLimit“-Werts für einen bestimmten Azure Cosmos DB-Endpunkt-URI wurde hinzugefügt.  Verwenden Sie ConnectionPolicy.MaxConnectionLimit zum Ändern des Standardwerts, der auf 50 festgelegt ist.
* IPartitionResolver und die zugehörige Implementierung wurden als „veraltet“ markiert.  Unterstützung für IPartitionResolver ist jetzt veraltet. Um mehr Speicher und höheren Durchsatz zu erzielen, empfiehlt sich die Verwendung von partitionierten Sammlungen.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* URI basierend auf der „ExecuteStoredProcedureAsync“-Methode eine Überladung hinzugefügt, die „RequestOptions“ als Parameter akzeptiert.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Unterstützung einer Gültigkeitsdauer (TTL) für Dokumente hinzugefügt.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Ein Fehler in der Nuget-Paketerstellung von .NET SDK wurde behoben (dient zum Verpacken von .NET SDK als Teil einer Azure Cloud Service-Lösung).

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* [Partitionierte Sammlungen](partition-data.md) und [benutzerdefinierte Leistungsstufen](performance-levels.md) implementiert. 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Korrigiert]** Die Abfrage des Azure Cosmos DB-Endpunkts löst Folgendes aus: „System.Net.Http.HttpRequestException: Fehler beim Kopieren von Inhalt in einen Stream“.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Erweiterte LINQ-Unterstützung, einschließlich neuer Operatoren für Paging, bedingte Ausdrücke und Bereichsvergleiche.
  * Take-Operator, um SELECT TOP-Verhalten in LINQ zu ermöglichen
  * CompareTo-Operator, um Vergleiche von Zeichenfolgenbereichen zu ermöglichen
  * Bedingte (?) und zusammengefügte (??) Operatoren
* **[Korrigiert]** ArgumentOutOfRangeException beim Kombinieren der Modellprojektion mit „Where-In“ in LINQ-Abfragen. [Nr. 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Korrigiert]** Wenn SELECT nicht der letzte Ausdruck ist, nahm der LINQ-Anbieter keine Projektion an und erzeugte SELECT * falsch.  [Nr. 58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Upsert implementiert, UpsertXXXAsync-Methoden hinzugefügt
* Leistungsverbesserungen für alle Anforderungen
* LINQ-Anbieterunterstützung für bedingte, „Coalesce“- und „CompareTo“-Methoden für Zeichenfolgen
* **[Korrigiert]** LINQ-Anbieter--&gt; „Contains“-Methode in „List“ implementiert, um für „IEnumerable“ und „Array“ dieselbe SQL zu generieren.
* **[Korrigiert]** „BackoffRetryUtility“ verwendet die gleiche „HttpRequestMessage“ erneut, anstatt bei einer Wiederholung eine neue zu erstellen.
* **[Veraltet]** „UriFactory.CreateCollection“ --&gt; „UriFactory.CreateDocumentCollection“ sollte stattdessen verwendet werden.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Korrigiert]** Lokalisierungsprobleme bei Verwenden nicht englischer Kulturinformationen wie beispielsweise nl-NL usw. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* ID-basiertes Routing hinzugefügt
  * Neues „UriFactory“-Hilfsprogramm zum Unterstützen des Erstellens ID-basierter Ressourcenlinks
  * Neue Überladungen für „DocumentClient“ zum Aufnehmen des URI.
* „IsValid()“ und „IsValidDetailed()“ in LINQ für Geodaten hinzugefügt.
* LINQ-Anbieterunterstützung verbessert:
  * **Math:** Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **String:** Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Array:** Concat, Contains, Count
  * **IN** -Operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Unterstützung für das Ändern der Indizierungsrichtlinie hinzugefügt.
  * Neue „ReplaceDocumentCollectionAsync“-Methode in „DocumentClient“
  * Neue „IndexTransformationProgress“-Eigenschaft in „ResourceResponse“<T> zum Nachverfolgen des prozentualen Fortschritts von Indexrichtlinienänderungen
  * „DocumentCollection.IndexingPolicy“ jetzt änderbar.
* Unterstützung für die räumliche Indizierung und Abfrage hinzugefügt.
  * Neuer „Microsoft.Azure.Documents.Spatial“-Namespace für die Serialisierung/Deserialisierung räumlicher Typen wie Punkt und Polygon.
  * Neue „SpatialIndex“-Klasse für die Indizierung von in Cosmos DB gespeicherten GeoJSON-Daten.
* **[Korrigiert]** Falsche SQL-Abfrage aus LINQ-Ausdruck generiert [Nr. 38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Eine Abhängigkeit von Newtonsoft.Json v5.0.7 wurde hinzugefügt.
* Änderungen zur Unterstützung von „Order By“ vorgenommen:
  
  * LINQ-Anbieterunterstützung für „OrderBy()“ oder „OrderByDescending()“
  * Änderung an „IndexingPolicy“ zur Unterstützung von „Order By“ 
    
    **Mögliche neue Änderung** 
    
    Wenn Sie über vorhandenen Code verfügen, der Sammlungen mit einer benutzerdefinierten Indizierungsrichtlinie bereitstellt, muss Ihr vorhandener Code so aktualisiert werden, dass er die neue IndexingPolicy-Klasse unterstützt. Wenn Sie keine benutzerdefinierte Indizierungsrichtlinie haben, betrifft Sie diese Änderung nicht.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Unterstützung für das Partitionieren von Daten mithilfe der neuen Klassen „HashPartitionResolver“ und „RangePartitionResolver“ und von „IPartitionResolver“ wurde hinzugefügt.
* „DataContract“-Serialisierung wurde hinzugefügt.
* GUID-Unterstützung in LINQ-Anbieter wurde hinzugefügt.
* UDF-Unterstützung in LINQ wurde hinzugefügt.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren. 

Anforderungen an Azure Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.4.0](#2.4.0) |5\. Mai 2019 |--- |
| [2.3.0](#2.3.0) |4\. April 2019 |--- |
| [2.2.3](#2.2.3) |11\. Februar 2019 |--- |
| [2.2.2](#2.2.2) |6\. Februar 2019 |--- |
| [2.2.1](#2.2.1) |24\. Dezember 2018 |--- |
| [2.2.0](#2.2.0) |07\. Dezember 2018 |--- |
| [2.1.3](#2.1.3) |15\. Oktober 2018 |--- |
| [2.1.2](#2.1.2) |4\. Oktober 2018 |--- |
| [2.1.1](#2.1.1) |27\. September 2018 |--- |
| [2.1.0](#2.1.0) |21\. September 2018 |--- |
| [2.0.0](#2.0.0) |07\. September 2018 |--- |
| [1.22.0](#1.22.0) |19\. April 2018 |--- |
| [1.21.1](#1.20.1) |09\. März 2018 |--- |
| [1.20.2](#1.20.1) |21\. Februar 2018 |--- |
| [1.20.1](#1.20.1) |05\. Februar 2018 |--- |
| [1.19.1](#1.19.1) |16\. November 2017 |--- |
| [1.19.0](#1.19.0) |10\. November 2017 |--- |
| [1.18.1](#1.18.1) |07\. November 2017 |--- |
| [1.18.0](#1.18.0) |17\. Oktober 2017 |--- |
| [1.17.0](#1.17.0) |10\. August 2017 |--- |
| [1.16.1](#1.16.1) |07\. August 2017 |--- |
| [1.16.0](#1.16.0) |02\. August 2017 |--- |
| [1.15.0](#1.15.0) |30\. Juni 2017 |--- |
| [1.14.1](#1.14.1) |23\. Mai 2017 |--- |
| [1.14.0](#1.14.0) |10\. Mai 2017 |--- |
| [1.13.4](#1.13.4) |09\. Mai 2017 |--- |
| [1.13.3](#1.13.3) |06\. Mai 2017 |--- |
| [1.13.2](#1.13.2) |19\. April 2017 |--- |
| [1.13.1](#1.13.1) |29\. März 2017 |--- |
| [1.13.0](#1.13.0) |24\. März 2017 |--- |
| [1.12.2](#1.12.2) |20\. März 2017 |--- |
| [1.12.1](#1.12.1) |14\. März 2017 |--- |
| [1.12.0](#1.12.0) |15\. Februar 2017 |--- |
| [1.11.4](#1.11.4) |6\. Februar 2017 |--- |
| [1.11.3](#1.11.3) |26\. Januar 2017 |--- |
| [1.11.1](#1.11.1) |21\. Dezember 2016 |--- |
| [1.11.0](#1.11.0) |8\. Dezember 2016 |--- |
| [1.10.0](#1.10.0) |27\. September 2016 |--- |
| [1.9.5](#1.9.5) |1\. September 2016 |--- |
| [1.9.4](#1.9.4) |24\. August 2016 |--- |
| [1.9.3](#1.9.3) |15\. August 2016 |--- |
| [1.9.2](#1.9.2) |23\. Juli 2016 |--- |
| [1.8.0](#1.8.0) |14\. Juni 2016 |--- |
| [1.7.1](#1.7.1) |6\. Mai 2016 |--- |
| [1.7.0](#1.7.0) |26\. April 2016 |--- |
| [1.6.3](#1.6.3) |8\. April 2016 |--- |
| [1.6.2](#1.6.2) |29\. März 2016 |--- |
| [1.5.3](#1.5.3) |19\. Februar 2016 |--- |
| [1.5.2](#1.5.2) |14\. Dezember 2015 |--- |
| [1.5.1](#1.5.1) |23\. November 2015 |--- |
| [1.5.0](#1.5.0) |5 Oktober 2015 |--- |
| [1.4.1](#1.4.1) |25\. August 2015 |--- |
| [1.4.0](#1.4.0) |13\. August 2015 |--- |
| [1.3.0](#1.3.0) |5\. August 2015 |--- |
| [1.2.0](#1.2.0) |6\. Juli 2015 |--- |
| [1.1.0](#1.1.0) |30\. April 2015 |--- |
| [1.0.0](#1.0.0) |8\. April 2015 |--- |


## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

