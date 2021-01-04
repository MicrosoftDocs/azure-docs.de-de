---
title: 'Azure Cosmos DB: SQL Node.js-API, SDK und Ressourcen'
description: Erhalten Sie alle Informationen zur SQL Node.js-API und zum SDK einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 7f101097945e5f345bae351ef5ca2a23a3e14be6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091156"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js-SDK für SQL-API: Versionshinweise und Ressourcen
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
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-Ressourcenanbieter](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

|Resource  |Link  |
|---------|---------|
|Herunterladen des SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-Dokumentation  |  [Referenzdokumentation zum JavaScript SDK](/javascript/api/%40azure/cosmos/?preserve-view=true&view=azure-node-latest)
|SDK-Installationsanweisungen  |  [Installationsanweisungen](https://github.com/Azure/azure-sdk-for-js)
|Mitwirkung am SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Beispiele | [Node.js-Codebeispiele](sql-api-nodejs-samples.md)
| Tutorial mit ersten Schritten | [Erste Schritte mit dem JavaScript SDK](sql-api-nodejs-get-started.md)
| Tutorial zu Web-Apps | [Erstellen einer Node.js-Webanwendung mithilfe von Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuell unterstützte Plattform | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/): SDK-Version 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/): SDK-Version 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/): SDK-Version 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/): SDK-Version 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/): SDK-Version 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/): SDK-Version 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/): SDK-Version 1.x.x

## <a name="release-notes"></a>Versionshinweise

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Legen Sie den Standardwert für ResponseContinuationTokenLimitInKB auf 1 KB fest. Standardmäßig begrenzen wir diesen Wert auf 1 KB, um lange Header zu vermeiden (für Node.js gilt eine globale Begrenzung der Headergröße). Ein Benutzer kann dieses Feld so festlegen, dass längere Header möglich sind, was dem Back-End helfen kann, die Ausführung von Abfragen zu optimieren.
* Entfernen Sie disableSSLVerification. Für diese Option gibt es neue Alternativen, die in [#388](https://github.com/Azure/azure-cosmos-js/pull/388) beschrieben sind.

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Zulassen, dass initialHeaders Header von Partitionsschlüsseln explizit festlegt
* package.json#files wird verwendet, um zu verhindern, dass externe Dateien veröffentlicht werden
* Sortierfehlers bei Routingzuordnungen in einer älteren Version von node+v8 korrigiert
* Fehler korrigiert, wenn der Benutzer teilweise Wiederholungsoptionen angibt

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Verhindern, dass Webpack Module auflöst, die mit „require“ aufgerufen werden

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Schon lange bestehenden Fehler behoben, bei dem RUs für Aggregatabfragen stets als 0 gemeldet wurden

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 Veröffentlichung von Version 3! 🎉 Viele neue Features, Fehlerbehebungen und einige wichtige Änderungen. Primäre Ziele dieser Version:

* Wichtige neue Features implementieren
  * DISTINCT-Abfragen
  * LIMIT/OFFSET-Abfragen
  * Vom Benutzer abbrechbare Anforderungen
* Aktualisierung auf die neueste Version der Cosmos-REST-API, in der alle Container unbegrenzte Skalierbarkeit haben
* Verwendung von Cosmos im Browser vereinfacht
* Bessere Abstimmung auf die neuen Richtlinien für das Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Migrationshandbuch für wichtige Änderungen
##### <a name="improved-client-constructor-options"></a>Clientkonstruktoroptionen verbessert

Konstruktoroptionen wurden vereinfacht:

* masterKey wurde in „key“ umbenannt und auf die oberste Ebene verschoben
* Eigenschaften, die sich zuvor unter „options.auth“ befanden, wurden auf die oberste Ebene verschoben

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>QueryIterator-API vereinfacht
In Version 2 gab es viele verschiedene Möglichkeiten, Ergebnisse einer Abfrage zu durchlaufen oder abzurufen. Wir haben versucht, die API der Version 3 zu vereinfachen und ähnliche oder doppelte APIs zu entfernen:

* iterator.next() und iterator.current() entfernt. Verwenden Sie fetchnext(), um Seiten mit Ergebnissen abzurufen.
* Entfernen Sie iterator.forEach(). Verwenden Sie stattdessen asynchrone Iteratoren.
* iterator.executeNext() in iterator.fetchNext() umbenannt
* iterator.toArray() in iterator.fetchAll() umbenannt
* Seiten sind nun ordnungsgemäße Antwortobjekte anstatt einfache JS-Objekte
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Feste Container sind jetzt partitioniert
Der Cosmos-Dienst unterstützt jetzt Partitionsschlüssel in allen Containern, einschließlich denjenigen, die zuvor als feste Container erstellt wurden. Das SDK der Version 3 wird auf die neueste API-Version aktualisiert, die diese Änderung implementiert, aber Sie ist nicht unterbrechend. Wenn Sie keinen Partitionsschlüssel für den Betrieb bereitstellen, verwenden wir standardmäßig einen Systemschlüssel, der mit allen Ihren vorhandenen Containern und Dokumenten funktioniert.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert für gespeicherte Prozeduren entfernt
Zuvor war Upsert für nicht partitionierte Sammlungen erlaubt, aber mit dem Update der API-Version sind alle Sammlungen partitioniert, sodass wir den Upsert vollständig entfernt haben.

##### <a name="item-reads-will-not-throw-on-404"></a>Bei Lesevorgängen von Elementen wird 404 nicht ausgelöst
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Standardmäßige Schreibvorgänge in mehreren Regionen
Das SDK schreibt jetzt standardmäßig in mehrere Regionen, wenn dies von ihrer Cosmos-Konfiguration unterstützt wird. Dieses Verhalten musste zuvor abonniert werden.

##### <a name="proper-error-objects"></a>Ordnungsgemäße Fehlerobjekte
Fehlerhafte Anforderungen lösen jetzt den ordnungsgemäßen Fehler oder dessen Unterklassen aus. Zuvor wurden einfache JS-Objekte ausgelöst.

#### <a name="new-features"></a>Neue Funktionen
##### <a name="user-cancelable-requests"></a>Vom Benutzer abbrechbare Anforderungen
Durch den Umstieg auf internes Abrufen können wir die AbortController-API des Browsers verwenden, um vom Benutzer abbrechbare Vorgänge zu unterstützen. Bei Vorgängen, bei denen möglicherweise mehrere Anforderungen bearbeitet werden (z. B. bei partitionsübergreifende Abfragen), werden alle Anforderungen für den Vorgang abgebrochen. Benutzer moderner Browser verfügen bereits über AbortController. Node.js-Benutzer müssen eine Polyfill-Bibliothek verwenden.

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Festlegen des Durchsatzes als Teil eines Erstellungsvorgangs für DB/Container
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Die Generierung von Headertoken wurde in die neue Bibliothek @azure/cosmos-sign verlagert. Aufrufer der Cosmos-REST-API können damit Header mit dem gleichen Code signieren, den wir innerhalb von @azure/cosmos aufrufen.

##### <a name="uuid-for-generated-ids"></a>UUID für generierte IDs
Version 2 enthielt benutzerdefinierten Code zum Generieren von Element-IDs. Wir haben auf die bekannte und gepflegte Communitybibliotheks-UUID umgestellt.

##### <a name="connection-strings"></a>Verbindungszeichenfolgen
Es ist nun möglich, eine im Azure-Portal kopierte Verbindungszeichenfolge zu übergeben:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Verbesserte Browsererfahrung
Obwohl es möglich war, das SDK von Version 2 im Browser zu verwenden, war dies keine ideale Lösung. Sie mussten mehrere in Node.js integrierte Polyfill-Bibliotheken und einen Bundler wie Webpack oder Parcel verwenden. Mithilfe des SDK von Version 3 ist die Standarderfahrung für Browserbenutzer wesentlich besser.

* Anforderungsinterna durch FETCH (#245) ersetzt
* Verwendung von BUFFER entfernt (#330)
* In Knoten integrierte Verwendung zugunsten universeller Pakete/APIs entfernt (#328)
* Auf node-abort-controller umgestiegen (#294)

#### <a name="bug-fixes"></a>Behebung von Programmfehlern
* Angebot zum Lesen korrigiert und erneutes Angebot von Tests (#224)
* EnableEndpointDiscovery korrigiert (#207)
* Fehlende RUs in paginierten Ergebnissen korrigiert (#360)
* SQL-Abfrageparametertyp erweitert (#346)
* ttl zu ItemDefinition hinzugefügt (#341)
* CP-Abfragemetriken korrigiert (#311)
* activityId zu FeedResponse hinzugefügt (#293)
* _ts-Typ von string in number geändert (#252)(#295)
* Aggregation der Belastungsanforderungen korrigiert (#289)
* Partitionsschlüssel mit leeren Zeichenfolgen zulässig (#277)
* Zeichenfolge zum Konfliktabfragetyp hinzugefügt (#237)
* uniqueKeyPolicy zu Container hinzugefügt (#234)

#### <a name="engineering-systems"></a>Entwicklungssysteme
Nicht immer die sichtbarsten Änderungen, aber sie helfen unserem Team, schneller besseren Code zu liefern.

* Rollup für Produktionsbuilds verwendet (#104)
* Auf TypeScript 3.5 aktualisiert (#327)
* Zu TS-Projektverweisen konvertiert. Testordner extrahiert (#270)
* noUnusedLocals und noUnusedParameters aktiviert (#275)
* Azure Pipelines YAML für CI-Builds (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Keine Änderungen am Code. Problem mit einigen zusätzliche Dateien im 2.1.4-Paket behoben.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Regionales Failover innerhalb einer Wiederholungsrichtlinie korrigiert
* hasMoreResults-Eigenschaft von ChangeFeed korrigiert
* Updates bei Entwicklungsabhängigkeiten
* PolicheckExclusions.txt hinzugefügt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* _ts-Typ von string in number geändert
* Standardindizierungstests korrigiert
* uniqueKeyPolicy zu v2 rückportiert
* Demo- und Demodebugkorrekturen

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Korrekturen beim Rückportierangebot in v3-Branch
* Fehler in der Typsignatur von executeNext() korrigiert
* Tippfehler korrigiert

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Umstrukturierung des Builds. Ermöglicht das Pullen der SDK-Version zum Buildzeitpunkt.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Neue Funktionen
* ChangeFeed-Unterstützung hinzugefügt (#196)
* MultiPolygon-Datentyp für Indizierung hinzugefügt (#191)
* „key“-Eigenschaft zum Konstruktor als Alias für masterKey (#202) hinzugefügt

#### <a name="fixes"></a>Fehlerbehebungen
* Fehler korrigiert, bei dem next() einen falschen Wert für den Iterator zurückgab

#### <a name="engineering-improvements"></a>Entwicklungsverbesserungen
* Integrationstest für die TypeScript-Nutzung hinzugefügt (#199)
* Direkte Installation über GitHub aktiviert (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Schnittstelle für Knoten-Agent-Typ hinzugefügt. Typescript-Benutzer müssen @types/node nicht mehr als Abhängigkeit installieren.
* Bevorzugte Standorte werden jetzt ordnungsgemäß berücksichtigt.
* Verbesserungen bei der Mitarbeit an der Entwicklerdokumentation
* Verschiedene Tippfehler wurden behoben.

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Das in 2.0.3 eingeführte Problem mit Typdefinitionen wurde behoben.

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Die Abhängigkeit `big-integer` wurde entfernt.
* Wechsel zu Verweisdirektiven für den AsyncIterable-Typ. Typescript-Benutzer müssen die Einstellung „lib“ nicht mehr anpassen.
* Tippfehler wurden korrigiert.

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Verknüpfungen der Readme-Datei wurden korrigiert.

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Fehler beim Wiederholen der Schnittstellenimplementierung wurden behoben.

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Allgemeine Verfügbarkeit von Version 2.0.0 des JavaScript SDK
* Unterstützung für Schreibvorgänge in mehreren Regionen hinzugefügt

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 von Version 2.0.0 des JavaScript SDK für die Public Preview.
* Neues Objektmodell, bei dem CosmosClient und Methoden der obersten Ebene auf die entsprechenden Datenbanken, Container und Elementklassen aufgeteilt werden. 
* Unterstützung für [Zusagen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* In TypeScript konvertiertes SDK.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* npm-Dokumentation wurde berichtigt.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Unterstützung für Standardwiederholungen bei Verbindungsproblemen hinzugefügt.
* Unterstützung für das Lesen des Sammlungsänderungsfeeds hinzugefügt.
* Sitzungskonsistenzfehler behoben, der zeitweilig dazu geführt hat, dass die Lesesitzung nicht verfügbar war.
* Unterstützung für Abfragemetriken hinzugefügt.
* Maximale Anzahl von Verbindungen des HTTP-Agents geändert.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Die Dokumentation wurde aktualisiert, damit anstelle von Azure DocumentDB auf Azure Cosmos DB verwiesen wird.
* Unterstützung für proxyUrl-Einstellung in ConnectionPolicy hinzugefügt.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Kleinere Fehlerbehebung für Dateisysteme mit Berücksichtigung der Groß-/Kleinschreibung durchgeführt.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Unterstützung für Sitzungskonsistenz hinzugefügt.
* Für dieses SDK muss die neueste Version des [Azure Cosmos DB-Emulators](https://aka.ms/cosmosdb-emulator) vorhanden sein.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Partitionsübergreifende Abfragen sind jetzt aufteilungssicher.
* Unterstützung für Ressourcenlinks mit führenden und nachgestellten Schrägstrichen (und entsprechende Tests) hinzugefügt.

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    npm-Dokumentation wurde berichtigt.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Ein Fehler in executeStoredProcedure, durch den beteiligte Dokumente Unicode-Sonderzeichen aufwiesen (LS, PS), wurde behoben.
* Ein Fehler bei der Verarbeitung von Dokumenten mit Unicode-Zeichen im Partitionsschlüssel wurde behoben.
* Ein Fehler bei der Unterstützung für das Erstellen von Sammlungen mit den Namensmedien wurde behoben. GitHub-Problem 114.
* Ein Fehler bei der Unterstützung für die Berechtigung des Autorisierungstokens wurde behoben. GitHub-Problem 178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Unterstützung für eine neue [Konsistenzebene](consistency-levels.md) mit dem Namen „Präfixkonsistenz“ hinzugefügt.
* Unterstützung für UriFactory hinzugefügt.
* Ein Fehler bei der Unicode-Unterstützung wurde behoben. GitHub-Problem 171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt.
* Die Option zum Steuern des Parallelitätsgrads für partitionsübergreifende Abfragen wurde hinzugefügt.
* Die Option zum Deaktivieren der TLS-Überprüfung bei der Ausführung für den Azure Cosmos DB-Emulator wurde hinzugefügt.
* Minimaler Durchsatz für partitionierte Sammlungen wurde von 10.100 RU/s auf 2.500 RU/s gesenkt.
* Ein Fehler beim Fortsetzungstoken für Sammlung mit einer Partition wurde behoben. GitHub-Problem 107.
* Ein Fehler bei „executeStoredProcedure“ bei der Verarbeitung von 0 als einzelner Parameter wurde behoben. GitHub-Problem 155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Fehler bei Benutzer-Agent-Header zum Einbeziehen der SDK-Version wurde behoben.
* Kleinere Codebereinigung.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Deaktivieren Sie die TLS-Überprüfung, wenn als Ziel des SDK „emulator(hostname=localhost)“ verwendet wird.
* Unterstützung für das Aktivieren der Skriptprotokollierung während der Ausführung einer gespeicherten Prozedur wurde hinzugefügt.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Unterstützung für parallele partitionsübergreifende Abfragen hinzugefügt.
* Unterstützung für TOP-/ORDER BY-Abfragen für partitionierte Sammlungen hinzugefügt.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Unterstützung für Wiederholungsrichtlinie für gedrosselte Anforderungen hinzugefügt. (Bei gedrosselten Anforderungen wird die Ausnahme „Anforderungsrate zu groß“, Fehlercode 429, angezeigt.) Standardmäßig führt Azure Cosmos DB für jede Anforderung neun Wiederholungen durch, wenn der Fehlercode 429 auftritt, und berücksichtigt dabei die „retryAfter“-Zeit im Antwortheader. Eine feste Wiederholungsintervalldauer kann jetzt als Teil der „RetryOptions“-Eigenschaft für das „ConnectionPolicy“-Objekt festgelegt werden, wenn Sie die „retryAfter“-Zeit ignorieren möchten, die vom Server zwischen den Wiederholungen zurückgegeben wird. Azure Cosmos DB wartet jetzt bei jeder gedrosselten Anforderung (unabhängig von der Anzahl der Wiederholungen) maximal 30 Sekunden und gibt die Antwort mit dem Fehlercode 429 zurück. Diese Dauer kann auch in der RetryOptions-Eigenschaft im ConnectionPolicy-Objekt überschrieben werden.
* Cosmos DB gibt nun „x-ms-throttle-retry-count“ und „x-ms-throttle-retry-wait-time-ms“ als Antwortheader in jeder Anforderung zurück, um die Anzahl der Wiederholungen bei einer Drosselung und die kumulative Zeit, die die Anforderung zwischen den Wiederholungen gewartet hat, anzugeben.
* Die „RetryOptions“-Klasse wurde hinzugefügt, die die „RetryOptions“-Eigenschaft in der „ConnectionPolicy“-Klasse verfügbar macht, welche zum Überschreiben einiger der Standardwiederholungsoptionen verwendet werden kann.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Unterstützung für Datenbankkonten in mehreren Regionen hinzugefügt.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Unterstützung des TTL-Features (Time To Live) für Dokumente hinzugefügt.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [Partitionierte Sammlungen](partitioning-overview.md) und [benutzerdefinierte Leistungsstufen](performance-levels.md) implementiert.

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* Fehler in „RangePartitionResolver.resolveForRead“ behoben, durch den aufgrund einer ungültigen Verkettung der Ergebnisse keine Links zurückgegeben wurden.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* Problem mit „hashPartitionResolver resolveForRead()“ behoben: Bei einem nicht angegebenen Partitionsschlüssel wurde eine Ausnahme ausgelöst, anstatt eine Liste aller registrierten Links zurückzugeben.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Behebt Problem [100](https://github.com/Azure/azure-documentdb-node/issues/100): Dedizierter HTTPS-Agent: Vermeidung des Änderns des globalen Agents für Azure Cosmos DB-Zwecke. Verwenden Sie für alle Anforderungen der Bibliothek einen dedizierten Agent.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Behebt Problem [Nr. 81](https://github.com/Azure/azure-documentdb-node/issues/81): ordnungsgemäße Verarbeitung von Bindestrichen in Medien-IDs

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Behebt Problem [Nr. 95](https://github.com/Azure/azure-documentdb-node/issues/95) : Warnung des EventEmitter-Listeners bei Arbeitsspeicherverlusten.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Behebt Problem [92](https://github.com/Azure/azure-documentdb-node/issues/90): Umbenennung des Ordners „Hash“ in „hash“ für Systeme, bei denen die Groß-/Kleinschreibung beachtet wird.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Unterstützung für Sharding implementiert, indem Hash- und Bereichspartitionsresolver hinzugefügt wurden.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert implementiert. Neue „upsertXXX“-Methoden für „DocumentClient“.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Q Promises Wrapper auf neues Repository aufgeteilt.
* Paketdatei für Npm-Registrierung aktualisiert.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* ID-basiertes Routing implementiert.
* Behebt Problem [49](https://github.com/Azure/azure-documentdb-node/issues/49) : Konflikt zwischen current-Eigenschaft und current()-Methode.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Unterstützung für „GeoSpatial“-Index hinzugefügt.
* „ID“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht die Zeichen ?, /, #, &#47;&#47;, enthalten und nicht mit einem Leerzeichen enden.
* Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* V2-Indizierungsrichtlinie implementiert.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Problem [40](https://github.com/Azure/azure-documentdb-node/issues/40) : Implementierung der eslint- und grunt-Konfiguration im Core und Promise SDK.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Problem [45](https://github.com/Azure/azure-documentdb-node/issues/45) – Promise Wrapper enthält keinen fehlerhaften Header.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Möglichkeit zum Abfragen von Konflikten durch Hinzufügen von „readConflicts“, „readConflictAsync“ und „queryConflicts“ implementiert.
* API-Dokumentation aktualisiert.
* Problem [41](https://github.com/Azure/azure-documentdb-node/issues/41) : client.createDocumentAsync-Fehler.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Allgemeine Verfügbarkeit (GA) des SDK

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten. Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

| Version | Veröffentlichungsdatum | Deaktivierungstermine |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26. Juli 2019 |--- |
| [3.0.4](#3.0.4) |22. Juli 2019 |--- |
| [3.0.3](#3.0.3) |17. Juli 2019 |--- |
| [3.0.2](#3.0.2) |9\. Juli 2019 |--- |
| [3.0.0](#3.0.0) |28. Juni 2019 |--- |
| [2.1.5](#2.1.5) |20. März 2019 |--- |
| [2.1.4](#2.1.4) |15. März 2019 |--- |
| [2.1.3](#2.1.3) |8\. März 2019 |--- |
| [2.1.2](#2.1.2) |28. Januar 2019 |--- |
| [2.1.1](#2.1.1) |5\. Dezember 2018 |--- |
| [2.1.0](#2.1.0) |4\. Dezember 2018 |--- |
| [2.0.5](#2.0.5) |7\. November 2018 |--- |
| [2.0.4](#2.0.4) |30. Oktober 2018 |--- |
| [2.0.3](#2.0.3) |30. Oktober 2018 |--- |
| [2.0.2](#2.0.2) |10. Oktober 2018 |--- |
| [2.0.1](#2.0.1) |25. September 2018 |--- |
| [2.0.0](#2.0.0) |24. September 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2\. August 2018 |--- |
| [1.14.4](#1.14.4) |3\.Mai 2018 |30. August 2020 |
| [1.14.3](#1.14.3) |3\.Mai 2018 |30. August 2020 |
| [1.14.2](#1.14.2) |21. Dezember 2017 |30. August 2020 |
| [1.14.1](#1.14.1) |10. November 2017 |30. August 2020 |
| [1.14.0](#1.14.0) |09. November 2017 |30. August 2020 |
| [1.13.0](#1.13.0) |11. Oktober 2017 |30. August 2020 |
| [1.12.2](#1.12.2) |10. August 2017 |30. August 2020 |
| [1.12.1](#1.12.1) |10. August 2017 |30. August 2020 |
| [1.12.0](#1.12.0) |10. Mai 2017 |30. August 2020 |
| [1.11.0](#1.11.0) |16. März 2017 |30. August 2020 |
| [1.10.2](#1.10.2) |27. Januar 2017 |30. August 2020 |
| [1.10.1](#1.10.1) |22. Dezember 2016 |30. August 2020 |
| [1.10.0](#1.10.0) |3\. Oktober 2016 |30. August 2020 |
| [1.9.0](#1.9.0) |7\. Juli 2016 |30. August 2020 |
| [1.8.0](#1.8.0) |14. Juni 2016 |30. August 2020 |
| [1.7.0](#1.7.0) |26. April 2016 |30. August 2020 |
| [1.6.0](#1.6.0) |29. März 2016 |30. August 2020 |
| [1.5.6](#1.5.6) |8\. März 2016 |30. August 2020 |
| [1.5.5](#1.5.5) |2\. Februar 2016 |30. August 2020 |
| [1.5.4](#1.5.4) |1\. Februar 2016 |30. August 2020 |
| [1.5.2](#1.5.2) |26. Januar 2016 |30. August 2020 |
| [1.5.2](#1.5.2) |22. Januar 2016 |30. August 2020 |
| [1.5.1](#1.5.1) |4\. Januar 2016 |30. August 2020 |
| [1.5.0](#1.5.0) |31. Dezember 2015 |30. August 2020 |
| [1.4.0](#1.4.0) |06. Oktober 2015 |30. August 2020 |
| [1.3.0](#1.3.0) |06. Oktober 2015 |30. August 2020 |
| [1.2.2](#1.2.2) |10. September 2015 |30. August 2020 |
| [1.2.1](#1.2.1) |15. August 2015 |30. August 2020 |
| [1.2.0](#1.2.0) |5\. August 2015 |30. August 2020 |
| [1.1.0](#1.1.0) |9\. Juli 2015 |30. August 2020 |
| [1.0.3](#1.0.3) |4\. Juni 2015 |30. August 2020 |
| [1.0.2](#1.0.2) |23. Mai 2015 |30. August 2020 |
| [1.0.1](#1.0.1) |15. Mai 2015 |30. August 2020 |
| [1.0.0](#1.0.0) |8\. April 2015 |30. August 2020 |

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).