---
title: 'Azure Cosmos DB: SQL Java-API, SDK und Ressourcen'
description: Wichtige Informationen zur SQL Java-API und zum SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Java-SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 03f7f050e158ba30d2f89e79ffcc9e8723eb5268
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298689"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK für die SQL-API: Versionshinweise und Ressourcen
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

Das SQL API Java SDK unterstützt synchrone Vorgänge. Verwenden Sie für die Unterstützung asynchroner Vorgänge das [SQL API Async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**SDK-Download**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-Dokumentation**|[Java-API-Referenzdokumentation](/java/api/com.microsoft.azure.documentdb)|
|**Am SDK mitwirken**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Erste Schritte**|[Erste Schritte mit dem Java SDK](sql-api-java-get-started.md)|
|**Web-App-Tutorial**|[Entwicklung von Webanwendungen mit Azure Cosmos DB](sql-api-java-application.md)|
|**Unterstützte Mindestlaufzeit**|[Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Versionshinweise

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Fehler in PartitionKey für Hash V2 behoben.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Unterstützung für zusammengesetzte Indizes hinzugefügt.
* Fehler im globalen Endpunkt-Manager beim Erzwingen von Aktualisierungen behoben.
* Fehler bei upsert-Vorgängen mit Vorbedingungen im direkten Modus behoben.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Fehler im Gatewayadresscache behoben.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Unterstützung von Schreibvorgängen in mehreren Regionen für den Direktmodus hinzugefügt.
* Unterstützung der Behandlung von E/A-Ausnahmen (IOExceptions) hinzugefügt, die von einem Proxy als Ausnahmen vom Typ „ServiceUnavailable“ ausgelöst werden.
* Fehler in der Wiederholungsrichtlinie für die Endpunktermittlung behoben.
* Fehler behoben, um sicherzustellen, dass in „BaseDatabaseAccountConfigurationProvider“ keine NULL-Zeiger-Ausnahmen ausgelöst werden.
* Fehler behoben, um sicherzustellen, dass QueryIterator keine NULL-Werte zurückgibt.
* Fehler behoben, um große PartitionKey-Werte zuzulassen.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Unterstützung von Schreibvorgängen in mehreren Regionen für den Gatewaymodus hinzugefügt.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Fehler in „Lesen der Partitionsschlüsselbereiche“ für eine Abfrage behoben.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Fehler beim Festlegen der Fortsetzungstoken-Headergröße im Modus „DirectHttps“ behoben.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Streaming-Failoverunterstützung hinzugefügt.
* Unterstützung von benutzerdefinierten Metadaten hinzugefügt.
* Verbesserte Sitzungsbehandlungslogik.
* Einen Fehler im Partitionsschlüssel-Bereichscache behoben.
* Einen NPE-Fehler im direkten Modus behoben.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Unterstützung für den eindeutigen Index wurde hinzugefügt.
* Unterstützung für die Begrenzung der Größe von Fortsetzungstoken in Feedoptionen wurde hinzugefügt.
* Fehler in der JSON-Serialisierung (Zeitstempel) wurde behoben.
* Fehler in der JSON-Serialisierung (Enumeration) wurde behoben.
* Abhängigkeit von com.fasterxml.jackson.core:jackson-databind wurde auf 2.9.5 aktualisiert.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Verbindungspooling für direkten Modus wurde verbessert.
* Prefetch-Verbesserung für partitionsübergreifende Nicht-Orderby-Abfrage wurde verbessert.
* UUID-Generierung wurde verbessert.
* Sitzungskonsistenzlogik wurde verbessert.
* Unterstützung für Multipolygon wurde hinzugefügt.
* Unterstützung für die Statistik des Partitionsschlüsselbereichs für die Sammlung wurde hinzugefügt.
* Fehler bei der Unterstützung für mehrere Regionen wurde behoben.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Leistung bei der JSON-Serialisierung wurde verbessert.
* Für dieses SDK muss die neueste Version des Azure Cosmos DB-Emulators vorhanden sein. Diese können Sie unter https://aka.ms/cosmosdb-emulator herunterladen.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Interne Änderungen für Friend-Bibliotheken von Microsoft.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Ein Problem beim Lesen der Schlüsselbereiche einer einzelnen Partition wurde behoben.
* Ein Problem bei der „ResourceID“-Analyse, das sich auf Datenbank mit Kurznamen auswirkt, wurde behoben.
* Ein Problem, das durch die Codierung des Partitionsschlüssels verursacht wurde, wurde beseitigt.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Wichtige Fehlerbehebungen für die Anforderungsverarbeitung während Partitionsaufteilungen
* Es wurde ein Problem mit den Konsistenzebenen „Strong“ und „BoundedStaleness“ behoben.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Es wurde Unterstützung für eine neue Konsistenzebene mit dem Namen „ConsistentPrefix“ hinzugefügt.
* Ein Fehler beim Lesen von Sammlungen im Sitzungsmodus wurde behoben.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Es wurde eine Unterstützung für partitionierte Sammlungen mit wenigen Anforderungseinheiten (2.500 RU/s) und für die Skalierung in Schritten von 100 RU/s hinzugefügt.
* Es wurde ein Fehler in der nativen Assembly behoben, der bei einigen Abfragen die NullRef-Ausnahme ausgelöst hat.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Ein Fehler in der Abfrage-Engine-Konfiguration wurde behoben, der Ausnahmen bei Abfragen im Gatewaymodus verursachen konnte.
* Eine Anzahl von Fehlern im Sitzungscontainer wurde behoben, die eine Ausnahme „Besitzerressource wurde nicht gefunden“ für Anforderungen direkt nach dem Erstellen der Sammlung verursachen konnten.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt. Siehe [Aggregationsunterstützung](how-to-sql-query.md#Aggregates).
* Unterstützung für Änderungsfeeds wurde hinzugefügt.
* Unterstützung für Sammlungskontingentinformationen über „RequestOptions.setPopulateQuotaInfo“ wurde hinzugefügt.
* Unterstützung für die Skriptprotokollierung gespeicherter Prozeduren über „RequestOptions.setScriptLoggingEnabled“ wurde hinzugefügt.
* Es wurde ein Fehler behoben, bei dem eine Abfrage im DirectHttps-Modus bei Drosselungsfehlern unter Umständen nicht mehr reagierte.
* Korrektur eines Fehlers im Sitzungskonsistenzmodus.
* Es wurde ein Fehler behoben, der bei hoher Anforderungsrate unter Umständen „NullReferenceException“ in „HttpContext“ auslöste.
* Verbesserte Leistung des DirectHttps-Modus.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Einfache, clientinstanzbasierte Proxyunterstützung mit ConnectionPolicy.setProxy()-API hinzugefügt.
* DocumentClient.close()-API zum ordnungsgemäßen Herunterfahren der DocumentClient-Instanz hinzugefügt.
* Abfrageleistung im direkten Konnektivitätsmodus verbessert, indem der Abfrageplan von der nativen Assembly (und nicht vom Gateway) abgeleitet wird.
* „FAIL_ON_UNKNOWN_PROPERTIES“ auf „false“ festgelegt, damit Benutzer in ihrem POJO „JsonIgnoreProperties“ nicht definieren müssen.
* Protokollierung für die Verwendung von SLF4J umgestaltet.
* Einige andere Fehler im Konsistenzleser behoben.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Fehler in der Verbindungsverwaltung behoben, um Verbindungsverluste im mit direkter Konnektivitätsmodus zu verhindern.
* Fehler in der TOP-Abfrage behoben, der unter Umständen zu einer NullReferenece-Ausnahme führt.
* Leistung durch Verringern der Anzahl von Netzwerkaufrufen für die internen Caches verbessert.
* Problembehandlung für „DocumentClientException“ durch Hinzufügen von Statuscode, Aktivitäts-ID und Anforderungs-URI verbessert.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Problem in der Verbindungsverwaltung behoben, um die Stabilität zu verbessern.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Unterstützung für BoundedStaleness-Konsistenzebene hinzugefügt.
* Unterstützung direkter Verbindungen für CRUD-Vorgänge für partitionierte Sammlungen hinzugefügt.
* Korrektur eines Fehlers beim Abfragen einer Datenbank mit SQL.
* Korrektur eines Fehlers im Sitzungscache, durch den unter Umständen das Sitzungstoken falsch festgelegt wird.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Unterstützung für parallele partitionsübergreifende Abfragen hinzugefügt.
* Unterstützung für TOP-/ORDER BY-Abfragen für partitionierte Sammlungen hinzugefügt.
* Unterstützung für starke Konsistenz hinzugefügt.
* Unterstützung für namensbasierte Anforderungen bei Verwendung von Direktverbindungen.
* Korrektur, durch die bei allen Anforderungswiederholungen der gleiche ActivityId-Wert verwendet wird.
* Korrektur eines Fehlers in Verbindung mit dem Sitzungscache bei der Neuerstellung einer Sammlung gleichen Namens.
* Polygon- und LineString-Datentypen beim Angeben der Sammlungsindizierungsrichtlinie für räumliche Geofencing-Abfragen hinzugefügt.
* Behobene Probleme mit Java Doc für Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Korrektur eines Fehlers in „PartitionKeyDefinitionMap“ zum Zwischenspeichern einzelner Partitionssammlungen, ohne dass zusätzliche Anforderungen zum Abrufen von Partitionsschlüsseln erfolgen.
* Korrektur eines Fehlers, sodass keine Wiederholung erfolgt, wenn ein falscher Partitionsschlüsselwert bereitgestellt wird.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Unterstützung für Datenbankkonten in mehreren Regionen hinzugefügt.
* Unterstützung für automatische Wiederholungsversuche für gedrosselte Anforderungen hinzugefügt, mit Optionen zum Anpassen der maximalen Anzahl von Wiederholungsversuchen und der maximalen Wartezeit.  Weitere Informationen unter „RetryOptions“ und „ConnectionPolicy.getRetryOptions()“.
* Auf IPartitionResolver basierender benutzerdefinierter Partitionierungscode als „veraltet“ markiert. Verwenden Sie partitionierte Sammlungen, um mehr Speicher und höheren Durchsatz zu erzielen.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Unterstützung für Wiederholungsrichtlinie für Ratenbegrenzung hinzugefügt.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Unterstützung einer Gültigkeitsdauer (TTL) für Dokumente hinzugefügt.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [Partitionierte Sammlungen](partition-data.md) und [benutzerdefinierte Leistungsstufen](performance-levels.md) implementiert.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Fehler in „HashPartitionResolver“ behoben, um Hashwerte in Little-Endian zu generieren und Konsistenz mit den anderen SDKs herzustellen.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Hinzufügen von Hash- und Bereichspartitionen-Konfliktlösern, um die Freigabe von Anwendungen über mehrere Partitionen zu unterstützen.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert implementiert. Neue UpsertXXX-Methoden hinzugefügt, um das „Upsert“-Feature zu unterstützen.
* ID-basiertes Routing implementiert. Keine öffentliche API-Änderungen, alle Änderungen sind intern.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* „GeoSpatial“-Index unterstützt.
* „ID“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht mit einem Leerzeichen enden und keins der folgenden Zeichen enthalten: ?, /, #, \,
* Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2-Indizierungsrichtlinie implementiert.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK

## <a name="release-and-retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher empfiehlt es sich, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

Anforderungen an Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

> [!WARNING]
> Alle Versionen **1.x** des SQL SDK für Java werdn am **30 Mai 2020** eingestellt.
> 
>

> [!WARNING]
> Alle Versionen des SQL SDK für Java vor Version **1.0.0** werden am **29. Februar 2016** deaktiviert.
> 
> 

<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13. März 2018 |--- |
| [2.1.2](#2.1.2) |09. März 2018 |--- |
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
| [1.9.0](#1.9.0) |3. Oktober 2016 |30. Mai 2020 |
| [1.8.1](#1.8.1) |30. Juni 2016 |30. Mai 2020 |
| [1.8.0](#1.8.0) |14. Juni 2016 |30. Mai 2020 |
| [1.7.1](#1.7.1) |30. April 2016 |30. Mai 2020 |
| [1.7.0](#1.7.0) |27. April 2016 |30. Mai 2020 |
| [1.6.0](#1.6.0) |29. März 2016 |30. Mai 2020 |
| [1.5.1](#1.5.1) |31. Dezember 2015 |30. Mai 2020 |
| [1.5.0](#1.5.0) |4. Dezember 2015 |30. Mai 2020 |
| [1.4.0](#1.4.0) |5. Oktober 2015 |30. Mai 2020 |
| [1.3.0](#1.3.0) |5. Oktober 2015 |30. Mai 2020 |
| [1.2.0](#1.2.0) |5. August 2015 |30. Mai 2020 |
| [1.1.0](#1.1.0) |9. Juli 2015 |30. Mai 2020 |
| 1.0.1 |12. Mai 2015 |30. Mai 2020 |
| [1.0.0](#1.0.0) |7. April 2015 |30. Mai 2020 |
| 0.9.5-prelease |9. März 2015 |29. Februar 2016 |
| 0.9.4-prelease |17. Februar 2015 |29. Februar 2016 |
| 0.9.3-prelease |13. Januar 2015 |29. Februar 2016 |
| 0.9.2-prelease |19. Dezember 2014 |29. Februar 2016 |
| 0.9.1-prelease |19. Dezember 2014 |29. Februar 2016 |
| 0.9.0-prelease |10. Dezember 2014 |29. Februar 2016 |

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

