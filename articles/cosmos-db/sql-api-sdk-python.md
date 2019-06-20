---
title: 'Azure Cosmos DB: SQL Python-API, SDK und Ressourcen'
description: Wichtige Informationen zur SQL Python-API und zum SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 9903339cbf0958893fb0d11a8c1b6ab7d156aae7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626790"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK für die SQL-API: Versionshinweise und Ressourcen
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
|**SDK herunterladen**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-Dokumentation**|[Python-API-Referenzdokumentation](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK-Installationsanweisungen**|[Python-SDK-Installationsanweisungen](https://github.com/Azure/azure-cosmos-python)|
|**Am SDK mitwirken**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Erste Schritte**|[Erste Schritte mit dem Python-SDK](sql-api-python-application.md)|
|**Aktuell unterstützte Plattform**|[Python 2.7](https://www.python.org/downloads/) und [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Versionshinweise

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Unterstützung für den MultiPolygon-Datentyp wurde hinzugefügt
* Fehlerbehebung für die Wiederholungsrichtlinie für den Lesevorgang in der Sitzung
* Fehlerbehebung für die falsche Auffüllung beim Decodieren von Base 64-Zeichenfolgen

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Fehlerbehebung in LocationCache
* Fehlerbehebung für die Endpunktwiederholungslogik
* Fehlerbehebung für die Dokumentation

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Unterstützung für Schreibvorgänge in mehreren Regionen.
* Der Namespace wurde in „azure.cosmos“ geändert.
* Sammlungs- und Dokumentkonzepte wurden in „Container“ und „Element“ umbenannt, „document_client“ wurde in „cosmos_client“ umbenannt. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Unterstützung für einen Proxy wurde hinzugefügt
* Unterstützung für das Lesen von Änderungsfeeds wurde hinzugefügt
* Unterstützung für Header des Sammlungskontingents wurde hinzugefügt
* Fehlerbehebung für Probleme bei großen Sitzungstoken
* Fehlerbehebung für die ReadMedia-API
* Fehlerbehebung im Partitionsschlüssel-Bereichscache

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Unterstützung für Standardwiederholungen bei Verbindungsproblemen hinzugefügt.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Die Dokumentation wurde aktualisiert, damit anstelle von Azure DocumentDB auf Azure Cosmos DB verwiesen wird.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Für dieses SDK muss die neueste Version des Azure Cosmos DB-Emulators vorhanden sein. Diese können Sie unter https://aka.ms/cosmosdb-emulator herunterladen.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Fehlerbehebung für aggregiertes Wörterbuch.
* Fehlerbehebung für das Abschneiden von Schrägstrichen im Ressourcenlink.
* Tests für Unicode-Codierung wurden hinzugefügt.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Es wurde Unterstützung für eine neue Konsistenzebene mit dem Namen „ConsistentPrefix“ hinzugefügt.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt.
* Eine Option zum Deaktivieren der SSL-Überprüfung bei der Ausführung für den Cosmos DB-Emulator wurde hinzugefügt.
* Die Einschränkung wurde entfernt, dass das Modul für abhängige Anforderungen genau 2.10.0 sein muss.
* Minimaler Durchsatz für partitionierte Sammlungen wurde von 10.100 RU/s auf 2.500 RU/s gesenkt.
* Unterstützung für das Aktivieren der Skriptprotokollierung während der Ausführung einer gespeicherten Prozedur wurde hinzugefügt.
* REST-API-Version wurde mit dieser Version auf „2017-01-19“ (19.01.2017) festgelegt.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* An Dokumentationskommentaren wurden redaktionelle Änderungen vorgenommen.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Unterstützung für Python 3.5 wurde hinzugefügt.
* Unterstützung für Verbindungspooling mithilfe eines Anforderungenmoduls wurde hinzugefügt.
* Unterstützung für Sitzungskonsistenz wurde hinzugefügt.
* Unterstützung für TOP-/ORDER BY-Abfragen für partitionierte Sammlungen wurde hinzugefügt.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Unterstützung für Wiederholungsrichtlinie für gedrosselte Anforderungen hinzugefügt. (Bei gedrosselten Anforderungen wird die Ausnahme „Anforderungsrate zu groß“, Fehlercode 429, angezeigt.) Standardmäßig führt Azure Cosmos DB für jede Anforderung neun Wiederholungen durch, wenn der Fehlercode 429 auftritt, und berücksichtigt dabei die „retryAfter“-Zeit im Antwortheader. Eine feste Wiederholungsintervalldauer kann jetzt als Teil der „RetryOptions“-Eigenschaft für das „ConnectionPolicy“-Objekt festgelegt werden, wenn Sie die „retryAfter“-Zeit ignorieren möchten, die vom Server zwischen den Wiederholungen zurückgegeben wird. Azure Cosmos DB wartet jetzt bei jeder gedrosselten Anforderung (unabhängig von der Anzahl der Wiederholungen) maximal 30 Sekunden und gibt die Antwort mit dem Fehlercode 429 zurück. Diese Dauer kann auch in der RetryOptions-Eigenschaft im ConnectionPolicy-Objekt überschrieben werden.
* Cosmos DB gibt nun „x-ms-throttle-retry-count“ und „x-ms-throttle-retry-wait-time-ms“ als Antwortheader in jeder Anforderung zurück, um die Anzahl der Wiederholungen bei einer Drosselung und die kumulative Zeit, die die Anforderung zwischen den Wiederholungen gewartet hat, anzugeben.
* Die „RetryPolicy“-Klasse und die dazugehörige Eigenschaft (retry_policy), die für die „document_client“-Klasse verfügbar gemacht wurden, wurden entfernt. Stattdessen wurde die „RetryOptions“-Klasse hinzugefügt, die die „RetryOptions“-Eigenschaft in der „ConnectionPolicy“-Klasse verfügbar macht, welche zum Überschreiben einiger der Standardwiederholungsoptionen verwendet werden kann.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Unterstützung für Datenbankkonten in mehreren Regionen hinzugefügt.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Unterstützung des TTL-Features (Time To Live) für Dokumente hinzugefügt.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Fehlerbehebungen im Zusammenhang mit serverseitiger Partitionierung, um Sonderzeichen im Pfad zum Partitionsschlüssel zuzulassen.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [Partitionierte Sammlungen](partition-data.md) und [benutzerdefinierte Leistungsstufen](performance-levels.md) implementiert. 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Hinzufügen von Hash- und Bereichspartitionen-Konfliktlösern, um die Freigabe von Anwendungen über mehrere Partitionen zu unterstützen.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert implementiert. Neue UpsertXXX-Methoden hinzugefügt, um das „Upsert“-Feature zu unterstützen.
* ID-basiertes Routing implementiert. Keine öffentliche API-Änderungen, alle Änderungen sind intern.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* GeoSpatial-Index unterstützt.
* „Id“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht mit einem Leerzeichen enden und keins der folgenden Zeichen enthalten: ?, /, #, \,
* Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2-Indizierungsrichtlinie implementiert.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Proxyverbindung unterstützt.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher empfiehlt es sich, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren. 

Anforderungen an Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

> [!WARNING]
> Alle Versionen des Azure SQL SDK für Python vor Version **1.0.0** werden am **29. Februar 2016** deaktiviert. 
> 
> 

<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15\. November 2018 |--- |
| [3.0.1](#3.0.1) |4\. Oktober 2018 |--- |
| [2.3.3](#2.3.3) |8\. September 2018 |--- |
| [2.3.2](#2.3.2) |8\. Mai 2018 |--- |
| [2.3.1](#2.3.1) |21\. Dezember 2017 |--- |
| [2.3.0](#2.3.0) |10\. November 2017 |--- |
| [2.2.1](#2.2.1) |29\. September 2017 |--- |
| [2.2.0](#2.2.0) |10\. Mai 2017 |--- |
| [2.1.0](#2.1.0) |01\. Mai 2017 |--- |
| [2.0.1](#2.0.1) |30\. Oktober 2016 |--- |
| [2.0.0](#2.0.0) |29\. September 2016 |--- |
| [1.9.0](#1.9.0) |7\. Juli 2016 |--- |
| [1.8.0](#1.8.0) |14\. Juni 2016 |--- |
| [1.7.0](#1.7.0) |26\. April 2016 |--- |
| [1.6.1](#1.6.1) |8\. April 2016 |--- |
| [1.6.0](#1.6.0) |29\. März 2016 |--- |
| [1.5.0](#1.5.0) |03\. Januar 2016 |--- |
| [1.4.2](#1.4.2) |06\. Oktober 2015 |--- |
| 1.4.1 |06\. Oktober 2015 |--- |
| [1.2.0](#1.2.0) |06\. August 2015 |--- |
| [1.1.0](#1.1.0) |9\. Juli 2015 |--- |
| [1.0.1](#1.0.1) |25\. Mai 2015 |--- |
| [1.0.0](#1.0.0) |7\. April 2015 |--- |
| 0.9.4-prelease |14\. Januar 2015 |29\. Februar 2016 |
| 0.9.3-prelease |09\. Dezember 2014 |29\. Februar 2016 |
| 0.9.2-prelease |25\. November 2014 |29\. Februar 2016 |
| 0.9.1-prelease |23\. September 2014 |29\. Februar 2016 |
| 0.9.0-prelease |21\.08.14 |29\. Februar 2016 |

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

