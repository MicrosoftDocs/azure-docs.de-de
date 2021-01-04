---
title: 'Azure Cosmos DB: SQL Java-API, SDK und Ressourcen'
description: Wichtige Informationen zur SQL Java-API und zum SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Java-SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0b48b8d954b132caab96e5978c90687899ea04c2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549224"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK für die SQL-API: Versionshinweise und Ressourcen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

Dies ist das ursprüngliche Sync Java SDK v2 von Azure Cosmos DB für die SQL-API, das synchrone Vorgänge unterstützt.

> [!IMPORTANT]  
> Dies ist *nicht* das neueste Java SDK für Azure Cosmos DB! Erwägen Sie die Verwendung des [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) für Ihr Projekt. Befolgen Sie für ein Upgrade die Anweisungen in den Anleitungen [Migrieren zum Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) und [Gegenüberstellung von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
>

| |  |
|---|---|
|**SDK-Download**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-Dokumentation**|[Java-API-Referenzdokumentation](/java/api/com.microsoft.azure.documentdb)|
|**Am SDK mitwirken**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Erste Schritte**|[Erste Schritte mit dem Java SDK](./create-sql-api-java.md)|
|**Web-App-Tutorial**|[Entwicklung von Webanwendungen mit Azure Cosmos DB](sql-api-java-application.md)|
|**Unterstützte Mindestlaufzeit**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)|

## <a name="release-notes"></a>Versionshinweise

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Behebt ein Cacheproblem der primären Partition in einer documentCollection-Abfrage.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Unterstützung für die benutzerdefinierte Konfiguration von 449-Wiederholungsmeldungen.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Korrektur des Timeoutproblem für den Verbindungspool
* Korrektur der Aktualisierung des Authentifizierungstokens bei internen Wiederholungen

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Korrektes clientseitiges Replikatrichtlinientag für „databaseAccount“ aktualisiert und Lesevorgänge für „databaseAccount“-Konfiguration aus dem Cache durchgeführt.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Vermeiden eines Wiederholungsversuchs für einen Fehler aufgrund eines ungültigen Partitionsschlüsselbereichs, wenn der Benutzer „pkRangeId“ bereitstellt.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Optimierter Partitionsschlüssel-Bereichscache aktualisiert.
* Behebt das Szenario, bei dem das SDK keinen Hinweis auf die Partitionsaufteilung vom Server enthält und zu einer falschen Aktualisierung der clientseitigen Routingcaches führt.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Optimierter Sammlungscache aktualisiert.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Unterstützung hinzugefügt, um innere Ausnahmemeldung aus Anforderungsdiagnose-Zeichenfolge abzurufen.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Version-API auf PartitionKeyDefinition eingeführt.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Separate Timeoutunterstützung für den direkten Modus hinzugefügt.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Verbrauch von Null-Fehlermeldungen aus dem Dienst und Erzeugung von Dokumentclientausnahmen.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Socketverbindung verbessert, SoKeepAlive standardmäßig „true“ hinzugefügt.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Unterstützung für Anforderungsdiagnose-Zeichenfolge hinzugefügt.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Fehler in PartitionKey für Hash V2 behoben.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Unterstützung für zusammengesetzte Indizes hinzugefügt.
* Fehler im globalen Endpunkt-Manager beim Erzwingen von Aktualisierungen behoben.
* Fehler bei upsert-Vorgängen mit Vorbedingungen im direkten Modus behoben.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Fehler im Gatewayadresscache behoben.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Unterstützung von Schreibvorgängen in mehreren Regionen für den Direktmodus hinzugefügt.
* Unterstützung der Behandlung von E/A-Ausnahmen (IOExceptions) hinzugefügt, die von einem Proxy als Ausnahmen vom Typ „ServiceUnavailable“ ausgelöst werden.
* Fehler in der Wiederholungsrichtlinie für die Endpunktermittlung behoben.
* Fehler behoben, um sicherzustellen, dass in „BaseDatabaseAccountConfigurationProvider“ keine NULL-Zeiger-Ausnahmen ausgelöst werden.
* Fehler behoben, um sicherzustellen, dass QueryIterator keine NULL-Werte zurückgibt.
* Fehler behoben, um große PartitionKey-Werte zuzulassen.

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Unterstützung von Schreibvorgängen in mehreren Regionen für den Gatewaymodus hinzugefügt.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Fehler in „Lesen der Partitionsschlüsselbereiche“ für eine Abfrage behoben.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Fehler beim Festlegen der Fortsetzungstoken-Headergröße im Modus „DirectHttps“ behoben.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Streaming-Failoverunterstützung hinzugefügt.
* Unterstützung von benutzerdefinierten Metadaten hinzugefügt.
* Verbesserte Sitzungsbehandlungslogik.
* Einen Fehler im Partitionsschlüssel-Bereichscache behoben.
* Einen NPE-Fehler im direkten Modus behoben.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Unterstützung für den eindeutigen Index wurde hinzugefügt.
* Unterstützung für die Begrenzung der Größe von Fortsetzungstoken in Feedoptionen wurde hinzugefügt.
* Fehler in der JSON-Serialisierung (Zeitstempel) wurde behoben.
* Fehler in der JSON-Serialisierung (Enumeration) wurde behoben.
* Abhängigkeit von com.fasterxml.jackson.core:jackson-databind wurde auf 2.9.5 aktualisiert.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Verbindungspooling für direkten Modus wurde verbessert.
* Prefetch-Verbesserung für partitionsübergreifende Nicht-Orderby-Abfrage wurde verbessert.
* UUID-Generierung wurde verbessert.
* Sitzungskonsistenzlogik wurde verbessert.
* Unterstützung für Multipolygon wurde hinzugefügt.
* Unterstützung für die Statistik des Partitionsschlüsselbereichs für die Sammlung wurde hinzugefügt.
* Fehler bei der Unterstützung für mehrere Regionen wurde behoben.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Leistung bei der JSON-Serialisierung wurde verbessert.
* Für dieses SDK muss die neueste Version des [Azure Cosmos DB-Emulators](https://aka.ms/cosmosdb-emulator) vorhanden sein.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Interne Änderungen für Friend-Bibliotheken von Microsoft.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Ein Problem beim Lesen der Schlüsselbereiche einer einzelnen Partition wurde behoben.
* Ein Problem bei der „ResourceID“-Analyse, das sich auf Datenbank mit Kurznamen auswirkt, wurde behoben.
* Ein Problem, das durch die Codierung des Partitionsschlüssels verursacht wurde, wurde beseitigt.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Wichtige Fehlerbehebungen für die Anforderungsverarbeitung während Partitionsaufteilungen
* Es wurde ein Problem mit den Konsistenzebenen „Strong“ und „BoundedStaleness“ behoben.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Es wurde Unterstützung für eine neue Konsistenzebene mit dem Namen „ConsistentPrefix“ hinzugefügt.
* Ein Fehler beim Lesen von Sammlungen im Sitzungsmodus wurde behoben.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Es wurde eine Unterstützung für partitionierte Sammlungen mit wenigen Anforderungseinheiten (2.500 RU/s) und für die Skalierung in Schritten von 100 RU/s hinzugefügt.
* Es wurde ein Fehler in der nativen Assembly behoben, der bei einigen Abfragen die NullRef-Ausnahme ausgelöst hat.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* Ein Fehler in der Abfrage-Engine-Konfiguration wurde behoben, der Ausnahmen bei Abfragen im Gatewaymodus verursachen konnte.
* Eine Anzahl von Fehlern im Sitzungscontainer wurde behoben, die eine Ausnahme „Besitzerressource wurde nicht gefunden“ für Anforderungen direkt nach dem Erstellen der Sammlung verursachen konnten.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt. Siehe [Aggregationsunterstützung](sql-query-aggregate-functions.md).
* Unterstützung für Änderungsfeeds wurde hinzugefügt.
* Unterstützung für Sammlungskontingentinformationen über „RequestOptions.setPopulateQuotaInfo“ wurde hinzugefügt.
* Unterstützung für die Skriptprotokollierung gespeicherter Prozeduren über „RequestOptions.setScriptLoggingEnabled“ wurde hinzugefügt.
* Es wurde ein Fehler behoben, bei dem eine Abfrage im DirectHttps-Modus bei Drosselungsfehlern unter Umständen nicht mehr reagierte.
* Korrektur eines Fehlers im Sitzungskonsistenzmodus.
* Es wurde ein Fehler behoben, der bei hoher Anforderungsrate unter Umständen „NullReferenceException“ in „HttpContext“ auslöste.
* Verbesserte Leistung des DirectHttps-Modus.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Einfache, clientinstanzbasierte Proxyunterstützung mit ConnectionPolicy.setProxy()-API hinzugefügt.
* DocumentClient.close()-API zum ordnungsgemäßen Herunterfahren der DocumentClient-Instanz hinzugefügt.
* Abfrageleistung im direkten Konnektivitätsmodus verbessert, indem der Abfrageplan von der nativen Assembly (und nicht vom Gateway) abgeleitet wird.
* „FAIL_ON_UNKNOWN_PROPERTIES“ auf „false“ festgelegt, damit Benutzer in ihrem POJO „JsonIgnoreProperties“ nicht definieren müssen.
* Protokollierung für die Verwendung von SLF4J umgestaltet.
* Einige andere Fehler im Konsistenzleser behoben.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Fehler in der Verbindungsverwaltung behoben, um Verbindungsverluste im mit direkter Konnektivitätsmodus zu verhindern.
* Fehler in der TOP-Abfrage behoben, der unter Umständen zu einer NullReferenece-Ausnahme führt.
* Leistung durch Verringern der Anzahl von Netzwerkaufrufen für die internen Caches verbessert.
* Problembehandlung für „DocumentClientException“ durch Hinzufügen von Statuscode, Aktivitäts-ID und Anforderungs-URI verbessert.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Problem in der Verbindungsverwaltung behoben, um die Stabilität zu verbessern.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Unterstützung für BoundedStaleness-Konsistenzebene hinzugefügt.
* Unterstützung direkter Verbindungen für CRUD-Vorgänge für partitionierte Sammlungen hinzugefügt.
* Korrektur eines Fehlers beim Abfragen einer Datenbank mit SQL.
* Korrektur eines Fehlers im Sitzungscache, durch den unter Umständen das Sitzungstoken falsch festgelegt wird.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Unterstützung für parallele partitionsübergreifende Abfragen hinzugefügt.
* Unterstützung für TOP-/ORDER BY-Abfragen für partitionierte Sammlungen hinzugefügt.
* Unterstützung für starke Konsistenz hinzugefügt.
* Unterstützung für namensbasierte Anforderungen bei Verwendung von Direktverbindungen.
* Korrektur, durch die bei allen Anforderungswiederholungen der gleiche ActivityId-Wert verwendet wird.
* Korrektur eines Fehlers in Verbindung mit dem Sitzungscache bei der Neuerstellung einer Sammlung gleichen Namens.
* Polygon- und LineString-Datentypen beim Angeben der Sammlungsindizierungsrichtlinie für räumliche Geofencing-Abfragen hinzugefügt.
* Behobene Probleme mit Java Doc für Java 1.8.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* Korrektur eines Fehlers in „PartitionKeyDefinitionMap“ zum Zwischenspeichern einzelner Partitionssammlungen, ohne dass zusätzliche Anforderungen zum Abrufen von Partitionsschlüsseln erfolgen.
* Korrektur eines Fehlers, sodass keine Wiederholung erfolgt, wenn ein falscher Partitionsschlüsselwert bereitgestellt wird.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Unterstützung für Datenbankkonten in mehreren Regionen hinzugefügt.
* Unterstützung für automatische Wiederholungsversuche für gedrosselte Anforderungen hinzugefügt, mit Optionen zum Anpassen der maximalen Anzahl von Wiederholungsversuchen und der maximalen Wartezeit.  Weitere Informationen unter „RetryOptions“ und „ConnectionPolicy.getRetryOptions()“.
* Auf IPartitionResolver basierender benutzerdefinierter Partitionierungscode als „veraltet“ markiert. Verwenden Sie partitionierte Sammlungen, um mehr Speicher und höheren Durchsatz zu erzielen.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Unterstützung für Wiederholungsrichtlinie für Ratenbegrenzung hinzugefügt.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Unterstützung einer Gültigkeitsdauer (TTL) für Dokumente hinzugefügt.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Partitionierte Sammlungen](partitioning-overview.md) und [benutzerdefinierte Leistungsstufen](performance-levels.md) implementiert.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Fehler in „HashPartitionResolver“ behoben, um Hashwerte in Little-Endian zu generieren und Konsistenz mit den anderen SDKs herzustellen.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Hinzufügen von Hash- und Bereichspartitionen-Konfliktlösern, um die Freigabe von Anwendungen über mehrere Partitionen zu unterstützen.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert implementiert. Neue UpsertXXX-Methoden hinzugefügt, um das „Upsert“-Feature zu unterstützen.
* ID-basiertes Routing implementiert. Keine öffentliche API-Änderungen, alle Änderungen sind intern.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* „GeoSpatial“-Index unterstützt.
* „ID“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht mit einem Leerzeichen enden und keins der folgenden Zeichen enthalten: ?, /, #, \,
* Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* V2-Indizierungsrichtlinie implementiert.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK

## <a name="release-and-retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten. Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher empfiehlt es sich, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

> [!WARNING]
> Nach dem 30. Mai 2020 werden von Azure Cosmos DB für die Versionen 1.x des Azure Cosmos DB Java SDK für die SQL-API weder Fehlerbehebungen durchgeführt, noch neue Funktionen hinzugefügt, noch Unterstützung bereitgestellt. Wenn Sie kein Upgrade durchführen möchten, werden Anforderungen, die von Version 1.x des SDK gesendet werden, weiterhin vom Azure Cosmos DB-Dienst bedient.
>
> Nach dem 29. Februar 2016 werden von Azure Cosmos DB für die Versionen 0.x des Azure Cosmos DB Java SDK für die SQL-API weder Fehlerbehebungen durchgeführt, noch neue Funktionen hinzugefügt, noch Unterstützung bereitgestellt. Wenn Sie kein Upgrade durchführen möchten, werden Anforderungen, die von Version 0.x des SDK gesendet werden, weiterhin vom Azure Cosmos DB-Dienst bedient.


| Version | Veröffentlichungsdatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.5.1](#2.5.1) |03. Juni 2020 |--- |
| [2.5.0](#2.5.0) |12. Mai 2020 |--- |
| [2.4.7](#2.4.7) |20. Februar 2020 |--- |
| [2.4.6](#2.4.6) |24. Januar 2020 |--- |
| [2.4.5](#2.4.5) |10. November 2019 |--- |
| [2.4.4](#2.4.4) |24. Oktober 2019 |--- |
| [2.4.2](#2.4.2) |26. September 2019 |--- |
| [2.4.1](#2.4.1) |18 Juli 2019 |--- |
| [2.4.0](#2.4.0) |04. Mai 2019 |--- |
| [2.3.0](#2.3.0) |24. April 2019 |--- |
| [2.2.3](#2.2.3) |16. April 2019 |--- |
| [2.2.2](#2.2.2) |05. April 2019 |--- |
| [2.2.0](#2.2.0) |27. März 2019 |--- |
| [2.1.3](#2.1.3) |13. März 2019 |--- |
| [2.1.2](#2.1.2) |09. März 2019 |--- |
| [2.1.1](#2.1.1) |13. Dezember 2018 |--- |
| [2.1.0](#2.1.0) |20. November 2018 |--- |
| [2.0.0](#2.0.0) |21. September 2018 |--- |
| [1.16.4](#1.16.4) |10. September 2018 |30. Mai 2020 |
| [1.16.3](#1.16.3) |09. September 2018 |30. Mai 2020 |
| [1.16.2](#1.16.2) |29. Juni 2018 |30. Mai 2020 |
| [1.16.1](#1.16.1) |16. Mai 2018 |30. Mai 2020 |
| [1.16.0](#1.16.0) |15. März 2018 |30. Mai 2020 |
| [1.15.0](#1.15.0) |14. November 2017 |30. Mai 2020 |
| [1.14.0](#1.14.0) |28. Oktober 2017 |30. Mai 2020 |
| [1.13.0](#1.13.0) |25. August 2017 |30. Mai 2020 |
| [1.12.0](#1.12.0) |11. Juli 2017 |30. Mai 2020 |
| [1.11.0](#1.11.0) |10. Mai 2017 |30. Mai 2020 |
| [1.10.0](#1.10.0) |11. März 2017 |30. Mai 2020 |
| [1.9.6](#1.9.6) |21. Februar 2017 |30. Mai 2020 |
| [1.9.5](#1.9.5) |31. Januar 2017 |30. Mai 2020 |
| [1.9.4](#1.9.4) |24. November 2016 |30. Mai 2020 |
| [1.9.3](#1.9.3) |30. Oktober 2016 |30. Mai 2020 |
| [1.9.2](#1.9.2) |28. Oktober 2016 |30. Mai 2020 |
| [1.9.1](#1.9.1) |26. Oktober 2016 |30. Mai 2020 |
| [1.9.0](#1.9.0) |3\. Oktober 2016 |30. Mai 2020 |
| [1.8.1](#1.8.1) |30. Juni 2016 |30. Mai 2020 |
| [1.8.0](#1.8.0) |14. Juni 2016 |30. Mai 2020 |
| [1.7.1](#1.7.1) |30. April 2016 |30. Mai 2020 |
| [1.7.0](#1.7.0) |27. April 2016 |30. Mai 2020 |
| [1.6.0](#1.6.0) |29. März 2016 |30. Mai 2020 |
| [1.5.1](#1.5.1) |31. Dezember 2015 |30. Mai 2020 |
| [1.5.0](#1.5.0) |4\. Dezember 2015 |30. Mai 2020 |
| [1.4.0](#1.4.0) |5\. Oktober 2015 |30. Mai 2020 |
| [1.3.0](#1.3.0) |5\. Oktober 2015 |30. Mai 2020 |
| [1.2.0](#1.2.0) |5\. August 2015 |30. Mai 2020 |
| [1.1.0](#1.1.0) |9\. Juli 2015 |30. Mai 2020 |
| 1.0.1 |12. Mai 2015 |30. Mai 2020 |
| [1.0.0](#1.0.0) |7\. April 2015 |30. Mai 2020 |
| 0.9.5-prelease |9\. März 2015 |29. Februar 2016 |
| 0.9.4-prelease |17. Februar 2015 |29. Februar 2016 |
| 0.9.3-prelease |13. Januar 2015 |29. Februar 2016 |
| 0.9.2-prelease |19. Dezember 2014 |29. Februar 2016 |
| 0.9.1-prelease |19. Dezember 2014 |29. Februar 2016 |
| 0.9.0-prelease |10. Dezember 2014 |29. Februar 2016 |

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).