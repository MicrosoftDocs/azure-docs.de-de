---
title: 'Azure Cosmos DB: SQL Node.js-API, SDK und Ressourcen'
description: Erhalten Sie alle Informationen zur SQL Node.js-API und zum SDK einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB Node.js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 1cb6889305e5f6bce5728039712a1834dc2e9353
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626739"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js-SDK für SQL-API: Versionshinweise und Ressourcen
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

|Resource  |Link  |
|---------|---------|
|Herunterladen des SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-Dokumentation  |  [Referenzdokumentation zum JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK-Installationsanweisungen  |  [Installationsanweisungen](https://github.com/Azure/azure-cosmos-js#installation)
|Mitwirkung am SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Beispiele | [Node.js-Codebeispiele](sql-api-nodejs-samples.md)
| Tutorial mit ersten Schritten | [Erste Schritte mit dem JavaScript SDK](sql-api-nodejs-get-started.md)
| Tutorial zu Web-Apps | [Erstellen einer Node.js-Webanwendung mithilfe von Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuell unterstützte Plattform | [Node.js Version 6.x](https://nodejs.org/en/blog/release/v6.10.3/): für SDK-Version 2.0.0 und höher erforderlich.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Versionshinweise

### <a name="2.0.5"/>2.0.5</a>
* Schnittstelle für Knoten-Agent-Typ hinzugefügt. Typescript-Benutzer müssen @types/node nicht mehr als Abhängigkeit installieren.
* Bevorzugte Standorte werden jetzt ordnungsgemäß berücksichtigt.
* Verbesserungen bei der Mitarbeit an der Entwicklerdokumentation
* Verschiedene Tippfehler wurden behoben.

### <a name="2.0.4"/>2.0.4</a>
* Das in 2.0.3 eingeführte Problem mit Typdefinitionen wurde behoben.

### <a name="2.0.3"/>2.0.3</a>
* Die Abhängigkeit `big-integer` wurde entfernt.
* Wechsel zu Verweisdirektiven für den AsyncIterable-Typ. Typescript-Benutzer müssen die Einstellung „lib“ nicht mehr anpassen.
* Tippfehler wurden korrigiert.

### <a name="2.0.2"/>2.0.2</a>
* Verknüpfungen der Readme-Datei wurden korrigiert.

### <a name="2.0.1"/>2.0.1</a>
* Fehler beim Wiederholen der Schnittstellenimplementierung wurden behoben.

### <a name="2.0.0"/>2.0.0</a>
* Allgemeine Verfügbarkeit von Version 2.0.0 des JavaScript SDK
* Unterstützung für Schreibvorgänge in mehreren Regionen hinzugefügt

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 von Version 2.0.0 des JavaScript SDK für die Public Preview.
* Neues Objektmodell, bei dem CosmosClient und Methoden der obersten Ebene auf die entsprechenden Datenbanken, Container und Elementklassen aufgeteilt werden. 
* Unterstützung für [Zusagen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* In TypeScript konvertiertes SDK.

### <a name="1.14.4"/>1.14.4</a>
* npm-Dokumentation wurde berichtigt.

### <a name="1.14.3"/>1.14.3</a>
* Unterstützung für Standardwiederholungen bei Verbindungsproblemen hinzugefügt.
* Unterstützung für das Lesen des Sammlungsänderungsfeeds hinzugefügt.
* Sitzungskonsistenzfehler behoben, der zeitweilig dazu geführt hat, dass die Lesesitzung nicht verfügbar war.
* Unterstützung für Abfragemetriken hinzugefügt.
* Maximale Anzahl von Verbindungen des HTTP-Agents geändert.

### <a name="1.14.2"/>1.14.2</a>
* Die Dokumentation wurde aktualisiert, damit anstelle von Azure DocumentDB auf Azure Cosmos DB verwiesen wird.
* Unterstützung für proxyUrl-Einstellung in ConnectionPolicy hinzugefügt.

### <a name="1.14.1"/>1.14.1</a>
* Kleinere Fehlerbehebung für Dateisysteme mit Berücksichtigung der Groß-/Kleinschreibung durchgeführt.

### <a name="1.14.0"/>1.14.0</a>
* Unterstützung für Sitzungskonsistenz hinzugefügt.
* Für dieses SDK muss die neueste Version des Azure Cosmos DB-Emulators vorhanden sein. Diese können Sie unter https://aka.ms/cosmosdb-emulator herunterladen.

### <a name="1.13.0"/>1.13.0</a>
* Partitionsübergreifende Abfragen sind jetzt aufteilungssicher.
* Unterstützung für Ressourcenlinks mit führenden und nachgestellten Schrägstrichen (und entsprechende Tests) hinzugefügt.

### <a name="1.12.2"/>1.12.2</a>
*   npm-Dokumentation wurde berichtigt.

### <a name="1.12.1"/>1.12.1</a>
* Ein Fehler in executeStoredProcedure, durch den beteiligte Dokumente Unicode-Sonderzeichen aufwiesen (LS, PS), wurde behoben.
* Ein Fehler bei der Verarbeitung von Dokumenten mit Unicode-Zeichen im Partitionsschlüssel wurde behoben.
* Ein Fehler bei der Unterstützung für das Erstellen von Sammlungen mit den Namensmedien wurde behoben. GitHub-Problem 114.
* Ein Fehler bei der Unterstützung für die Berechtigung des Autorisierungstokens wurde behoben. GitHub-Problem 178.

### <a name="1.12.0"/>1.12.0</a>
* Unterstützung für eine neue [Konsistenzebene](consistency-levels.md) mit dem Namen „Präfixkonsistenz“ hinzugefügt.
* Unterstützung für UriFactory hinzugefügt.
* Ein Fehler bei der Unicode-Unterstützung wurde behoben. GitHub-Problem 171.

### <a name="1.11.0"/>1.11.0</a>
* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt.
* Die Option zum Steuern des Parallelitätsgrads für partitionsübergreifende Abfragen wurde hinzugefügt.
* Die Option zum Deaktivieren der SSL-Überprüfung bei der Ausführung für den Azure Cosmos DB-Emulator wurde hinzugefügt.
* Minimaler Durchsatz für partitionierte Sammlungen wurde von 10.100 RU/s auf 2.500 RU/s gesenkt.
* Ein Fehler beim Fortsetzungstoken für Sammlung mit einer Partition wurde behoben. GitHub-Problem 107.
* Ein Fehler bei „executeStoredProcedure“ bei der Verarbeitung von 0 als einzelner Parameter wurde behoben. GitHub-Problem 155.

### <a name="1.10.2"/>1.10.2</a>
* Fehler bei Benutzer-Agent-Header zum Einbeziehen der SDK-Version wurde behoben.
* Kleinere Codebereinigung.

### <a name="1.10.1"/>1.10.1</a>
* Deaktivieren Sie die SSL-Überprüfung, wenn als Ziel des SDK „emulator(hostname=localhost)“ verwendet wird.
* Unterstützung für das Aktivieren der Skriptprotokollierung während der Ausführung einer gespeicherten Prozedur wurde hinzugefügt.

### <a name="1.10.0"/>1.10.0</a>
* Unterstützung für parallele partitionsübergreifende Abfragen hinzugefügt.
* Unterstützung für TOP-/ORDER BY-Abfragen für partitionierte Sammlungen hinzugefügt.

### <a name="1.9.0"/>1.9.0</a>
* Unterstützung für Wiederholungsrichtlinie für gedrosselte Anforderungen hinzugefügt. (Bei gedrosselten Anforderungen wird die Ausnahme „Anforderungsrate zu groß“, Fehlercode 429, angezeigt.) Standardmäßig führt Azure Cosmos DB für jede Anforderung neun Wiederholungen durch, wenn der Fehlercode 429 auftritt, und berücksichtigt dabei die „retryAfter“-Zeit im Antwortheader. Eine feste Wiederholungsintervalldauer kann jetzt als Teil der „RetryOptions“-Eigenschaft für das „ConnectionPolicy“-Objekt festgelegt werden, wenn Sie die „retryAfter“-Zeit ignorieren möchten, die vom Server zwischen den Wiederholungen zurückgegeben wird. Azure Cosmos DB wartet jetzt bei jeder gedrosselten Anforderung (unabhängig von der Anzahl der Wiederholungen) maximal 30 Sekunden und gibt die Antwort mit dem Fehlercode 429 zurück. Diese Dauer kann auch in der RetryOptions-Eigenschaft im ConnectionPolicy-Objekt überschrieben werden.
* Cosmos DB gibt nun „x-ms-throttle-retry-count“ und „x-ms-throttle-retry-wait-time-ms“ als Antwortheader in jeder Anforderung zurück, um die Anzahl der Wiederholungen bei einer Drosselung und die kumulative Zeit, die die Anforderung zwischen den Wiederholungen gewartet hat, anzugeben.
* Die „RetryOptions“-Klasse wurde hinzugefügt, die die „RetryOptions“-Eigenschaft in der „ConnectionPolicy“-Klasse verfügbar macht, welche zum Überschreiben einiger der Standardwiederholungsoptionen verwendet werden kann.

### <a name="1.8.0"/>1.8.0</a>
* Unterstützung für Datenbankkonten in mehreren Regionen hinzugefügt.

### <a name="1.7.0"/>1.7.0</a>
* Unterstützung des TTL-Features (Time To Live) für Dokumente hinzugefügt.

### <a name="1.6.0"/>1.6.0</a>
* [Partitionierte Sammlungen](partition-data.md) und [benutzerdefinierte Leistungsstufen](performance-levels.md) implementiert.

### <a name="1.5.6"/>1.5.6</a>
* Fehler in „RangePartitionResolver.resolveForRead“ behoben, durch den aufgrund einer ungültigen Verkettung der Ergebnisse keine Links zurückgegeben wurden.

### <a name="1.5.5"/>1.5.5</a>
* Problem mit „hashPartitionResolver resolveForRead()“ behoben: Bei einem nicht angegebenen Partitionsschlüssel wurde eine Ausnahme ausgelöst, anstatt eine Liste aller registrierten Links zurückzugeben.

### <a name="1.5.4"/>1.5.4</a>
* Behebt Problem [100](https://github.com/Azure/azure-documentdb-node/issues/100): Dedizierter HTTPS-Agent: Vermeidung des Änderns des globalen Agents für Azure Cosmos DB-Zwecke. Verwenden Sie einen dedizierten-Agent für alle Anforderungen der Bibliothek.

### <a name="1.5.3"/>1.5.3</a>
* Behebt Problem [Nr. 81](https://github.com/Azure/azure-documentdb-node/issues/81) : Ordnungsgemäße Behandlung von Bindestrichen in Medien-IDs.

### <a name="1.5.2"/>1.5.2</a>
* Behebt Problem [Nr. 95](https://github.com/Azure/azure-documentdb-node/issues/95) : Warnung des EventEmitter-Listeners bei Arbeitsspeicherverlusten.

### <a name="1.5.1"/>1.5.1</a>
* Behebt Problem [92](https://github.com/Azure/azure-documentdb-node/issues/90): Umbenennung des Ordners „Hash“ in „hash“ für Systeme, bei denen die Groß-/Kleinschreibung beachtet wird.

### <a name="1.5.0"/>1.5.0</a>
* Unterstützung für Sharding implementiert, indem Hash- und Bereichspartitionsresolver hinzugefügt wurden.

### <a name="1.4.0"/>1.4.0</a>
* Upsert implementiert. Neue „upsertXXX“-Methoden für „DocumentClient“.

### <a name="1.3.0"/>1.3.0</a>
* Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="1.2.2"/>1.2.2</a>
* Q Promises Wrapper auf neues Repository aufgeteilt.
* Paketdatei für Npm-Registrierung aktualisiert.

### <a name="1.2.1"/>1.2.1</a>
* ID-basiertes Routing implementiert.
* Behebt Problem [49](https://github.com/Azure/azure-documentdb-node/issues/49) : Konflikt zwischen current-Eigenschaft und current()-Methode.

### <a name="1.2.0"/>1.2.0</a>
* Unterstützung für „GeoSpatial“-Index hinzugefügt.
* „Id“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht die Zeichen ?, /, #, &#47;&#47;, enthalten und nicht mit einem Leerzeichen enden.
* Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="1.1.0"/>1.1.0</a>
* V2-Indizierungsrichtlinie implementiert.

### <a name="1.0.3"/>1.0.3</a>
* Problem [40](https://github.com/Azure/azure-documentdb-node/issues/40) : Implementierung der eslint- und grunt-Konfiguration im Core und Promise SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problem [45](https://github.com/Azure/azure-documentdb-node/issues/45) – Promise Wrapper enthält keinen fehlerhaften Header.

### <a name="1.0.1"/>1.0.1</a>
* Möglichkeit zum Abfragen von Konflikten durch Hinzufügen von „readConflicts“, „readConflictAsync“ und „queryConflicts“ implementiert.
* API-Dokumentation aktualisiert.
* Problem [41](https://github.com/Azure/azure-documentdb-node/issues/41) : client.createDocumentAsync-Fehler.

### <a name="1.0.0"/>1.0.0</a>
* Allgemeine Verfügbarkeit (GA) des SDK

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

Anforderungen an Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2\. August 2018 |--- |
| [1.14.4](#1.14.4) |3\.Mai 2018 |--- |
| [1.14.3](#1.14.3) |3\.Mai 2018 |--- |
| [1.14.2](#1.14.2) |21\. Dezember 2017 |--- |
| [1.14.1](#1.14.1) |10\. November 2017 |--- |
| [1.14.0](#1.14.0) |09\. November 2017 |--- |
| [1.13.0](#1.13.0) |11\. Oktober 2017 |--- |
| [1.12.2](#1.12.2) |10\. August 2017 |--- |
| [1.12.1](#1.12.1) |10\. August 2017 |--- |
| [1.12.0](#1.12.0) |10\. Mai 2017 |--- |
| [1.11.0](#1.11.0) |16\. März 2017 |--- |
| [1.10.2](#1.10.2) |27\. Januar 2017 |--- |
| [1.10.1](#1.10.1) |22\. Dezember 2016 |--- |
| [1.10.0](#1.10.0) |3\. Oktober 2016 |--- |
| [1.9.0](#1.9.0) |7\. Juli 2016 |--- |
| [1.8.0](#1.8.0) |14\. Juni 2016 |--- |
| [1.7.0](#1.7.0) |26\. April 2016 |--- |
| [1.6.0](#1.6.0) |29\. März 2016 |--- |
| [1.5.6](#1.5.6) |8\. März 2016 |--- |
| [1.5.5](#1.5.5) |2\. Februar 2016 |--- |
| [1.5.4](#1.5.4) |1\. Februar 2016 |--- |
| [1.5.2](#1.5.2) |26\. Januar 2016 |--- |
| [1.5.2](#1.5.2) |22\. Januar 2016 |--- |
| [1.5.1](#1.5.1) |4\. Januar 2016 |--- |
| [1.5.0](#1.5.0) |31\. Dezember 2015 |--- |
| [1.4.0](#1.4.0) |06\. Oktober 2015 |--- |
| [1.3.0](#1.3.0) |6\. Oktober 2015 |--- |
| [1.2.2](#1.2.2) |10\. September 2015 |--- |
| [1.2.1](#1.2.1) |15\. August 2015 |--- |
| [1.2.0](#1.2.0) |5\. August 2015 |--- |
| [1.1.0](#1.1.0) |9\. Juli 2015 |--- |
| [1.0.3](#1.0.3) |4\. Juni 2015 |--- |
| [1.0.2](#1.0.2) |23\. Mai 2015 |--- |
| [1.0.1](#1.0.1) |15\. Mai 2015 |--- |
| [1.0.0](#1.0.0) |8\. April 2015 |--- |

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

