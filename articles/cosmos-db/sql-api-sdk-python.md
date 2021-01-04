---
title: Azure Cosmos DB SQL Python-API, SDK und Ressourcen
description: Wichtige Informationen zur SQL Python-API und zum SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB Python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: b69b3a3888b2d6e19b8eaa92ba9b2fedf0e15545
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091117"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK für die SQL-API: Versionshinweise und Ressourcen
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
> * [Bulk Executor – .NET  v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK herunterladen**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-Dokumentation**|[Python-API-Referenzdokumentation](/python/api/azure-cosmos/?preserve-view=true&view=azure-python)|
|**SDK-Installationsanweisungen**|[Python-SDK-Installationsanweisungen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Erste Schritte**|[Erste Schritte mit dem Python-SDK](create-sql-api-python.md)|
|**Aktuell unterstützte Plattform**|[Python 2.7](https://www.python.org/downloads/) und [Python 3.5.3+](https://www.python.org/downloads/)|

## <a name="release-history"></a>Releaseverlauf

### <a name="410-2020-08-10"></a>4.1.0 (10.08.2020)

- Warnung bezüglich der Einstellung des Modus der verzögerten Indizierung hinzugefügt. Das Back-End lässt das Erstellen von Containern mit diesem Modus nicht mehr zu und legt sie stattdessen auf konsistent fest.

**Neue Features**
- Die Möglichkeit zum Festlegen der Gültigkeitsdauer des analytischen Speichers beim Erstellen eines neuen Containers wurde hinzugefügt.

**Fehlerbehebungen**
- Die Unterstützung von Wörterbüchern als Eingaben für get_client-APIs wurde korrigiert.
- Die Kompatibilität mit Python 2/3 in Abfrageiteratoren wurde korrigiert.
- Typhinweisfehler wurde behoben (Problem Nr. 12570).
- Der Fehler wurde behoben, aufgrund dessen der upsert_item-Funktion keine Optionsheader hinzugefügt wurden. Problem Nr. 11791 – vielen Dank an @aalapatirvbd.
- Der Fehler wurde behoben, der bei Verwendung einer Nicht-Zeichenfolgen-ID in einem Element ausgelöst wurde. Jetzt wird ein TypeError anstelle eines AttributeError ausgelöst (Problem Nr. 11793).

### <a name="400"></a>4.0.0

* Stabiles Release.
* Der Pipeline wurde „HttpLoggingPolicy“ hinzugefügt, um die Übergabe in einer benutzerdefinierten Protokollierung für Anforderungs- und Antwortheader zu ermöglichen.

### <a name="400b6"></a>4.0.0b6

* Fehler in „synchronized_request“ für Medien-APIs behoben.
* „MediaReadMode“ und „MediaRequestTimeout“ wurden aus „ConnectionPolicy“ entfernt, da Medienanforderungen nicht unterstützt werden.

### <a name="400b5"></a>4.0.0b5

* Das Modul „azure.cosmos.errors“ ist veraltet und wurde durch „azure.cosmos.exceptions“ ersetzt.
* Die Zugriffsbedingungsparameter (`access_condition`, `if_match`, `if_none_match`) wurden zugunsten der separaten `match_condition`- und `etag`-Parameter als veraltet markiert.
* Fehler beim Routingzuordnungsanbieter wurde behoben.
* Unterstützung von LIMIT, OFFSET und DISTINCT für Abfragen wurde hinzugefügt.
* Der Standardausführungskontext für Dokumentabfragen wird jetzt für folgende Abfragen verwendet:

  * ChangeFeed-Abfragen
  * Abfragen in einer einzelnen Partition („partitionkey“, „partitionKeyRangeId“ ist in den Optionen vorhanden)
  * Andere Abfragen als Dokumentabfragen

* Beendigung für Aggregate in mehreren Partitionen, wenn das Aktivieren partitionsübergreifender Abfragen auf „true“ festgelegt ist, das value-Schlüsselwort jedoch fehlt
* Erreichen des Abfrageplan-Endpunkts für andere Szenarien zum Abrufen des Abfrageplans
* Unterstützung von `__repr__` für Cosmos-Entitätsobjekte wurde hinzugefügt.
* Dokumentation wurde aktualisiert.

### <a name="400b4"></a>4.0.0b4

* Allen Vorgängen wurde Unterstützung für ein `timeout`-Schlüsselwortargument hinzugefügt, um ein absolutes Timeout in Sekunden anzugeben, bis zu dem der Vorgang abgeschlossen werden muss. Beim Überschreiten des Timeoutwerts wird ein `azure.cosmos.errors.CosmosClientTimeoutError` ausgelöst.

* Eine neue `ConnectionRetryPolicy` zum Verwalten des Wiederholungsverhaltens bei HTTP-Verbindungsfehlern wurde hinzugefügt.

* Neue Konstruktorargumente und Konfigurationsschlüsselwortargumente pro Vorgang wurden hinzugefügt:

  * `retry_total`: Die maximale Anzahl der Wiederholungsversuche
  * `retry_backoff_max`: Die maximale Wiederholungswartezeit in Sekunden
  * `retry_fixed_interval`: Festes Wiederholungsintervall in Millisekunden
  * `retry_read`: Die maximale Anzahl der Socket-Lesewiederholungsversuche
  * `retry_connect`: Die maximale Anzahl der Wiederholungsversuche bei Verbindungsfehlern
  * `retry_status`: Die maximale Anzahl der Wiederholungsversuche bei Fehlerstatuscodes.
  * `retry_on_status_codes`: Eine Liste der speziellen Statuscodes, bei denen eine Wiederholung erfolgen soll
  * `retry_backoff_factor`: Der Faktor zum Berechnen der Wartezeit zwischen Wiederholungsversuchen

### <a name="400b3"></a>4.0.0b3

* `create_database_if_not_exists()` und `create_container_if_not_exists` wurden zu „CosmosClient“ bzw. „Database“ hinzugefügt.

### <a name="400b2"></a>4.0.0b2

* Version 4.0.0b2 ist die zweite Iteration der Entwicklung einer Clientbibliothek, die den Best Practices von Python entspricht.

**Wichtige Änderungen**

* Die Clientverbindung wurde so angepasst, dass die in `azure.core.pipeline` definierte HTTP-Pipeline verwendet wird.

* Interaktive Objekte wurden nun als Proxys umbenannt. Dies schließt Folgendes ein:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Der Konstruktor von `CosmosClient` wurde aktualisiert:

  * Der `auth`-Parameter wurde in `credential` umbenannt und akzeptiert nun direkt einen Authentifizierungstyp. Dies bedeutet, dass der Primärschlüsselwert, ein Wörterbuch mit Ressourcentoken oder eine Liste von Berechtigungen übergeben werden kann. Das alte Wörterbuchformat wird jedoch weiterhin unterstützt.

  * Der `connection_policy`-Parameter wurde in einen Schlüsselwortparameter geändert. Er wird zwar weiterhin unterstützt, jedoch kann jetzt jedes der einzelnen Attribute der Richtlinie als explizites Schlüsselwortargument übergeben werden:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* `CosmosClient` wurde ein neuer Konstruktor hinzugefügt, um die Erstellung über eine vom Azure-Portal abgerufene Verbindungszeichenfolge zu ermöglichen.

* Einige `read_all`-Vorgänge wurden in `list`-Vorgänge umbenannt:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Alle Vorgänge, die `request_options`- oder `feed_options`-Parameter akzeptieren. Diese wurden in Schlüsselwortparameter verschoben. Die Optionswörterbücher werden weiterhin unterstützt, jedoch werden jetzt außerdem die einzelnen Optionen im Wörterbuch als explizite Schlüsselwortargumente unterstützt.

* Die Fehlerhierarchie wird nun von `azure.core.AzureError` geerbt:

  * `HTTPFailure` wurde in `CosmosHttpResponseError` umbenannt.
  * `JSONParseFailure` wurde entfernt und durch `azure.core.DecodeError` ersetzt.
  * Es wurden zusätzliche Fehler für bestimmte Antwortcodes hinzugefügt:
    * `CosmosResourceNotFoundError` für Status 404
    * `CosmosResourceExistsError` für Status 409
    * `CosmosAccessConditionFailedError` für Status 412

* `CosmosClient` kann jetzt in einem Kontext-Manager ausgeführt werden, um das Schließen der Clientverbindung zu behandeln.

* Iterierbare Antworten (z. B. Abfrageantworten und Listenantworten) sind nun vom Typ `azure.core.paging.ItemPaged`. Die Methode `fetch_next_block` wurde durch einen sekundären Iterator ersetzt, auf den von der `by_page`-Methode zugegriffen wird.

### <a name="400b1"></a>4.0.0b1

Version 4.0.0b1 ist die erste Vorschau auf eine benutzerfreundliche Clientbibliothek, die den Best Practices von Python entspricht. Weitere Informationen zu dieser Vorschauversion und Vorschauversionen anderer Azure SDK-Bibliotheken finden Sie unter https://aka.ms/azure-sdk-preview1-python.

**Wichtige Änderungen: Neues API-Design**

* Vorgänge werden nun auf einen bestimmten Client beschränkt:

  * `CosmosClient`: Dieser Client behandelt Vorgänge auf Kontoebene. Dies umfasst das Verwalten von Diensteigenschaften und das Auflisten der Datenbanken innerhalb eines Kontos.
  * `Database`: Dieser Client behandelt Vorgänge auf Datenbankebene. Dies umfasst das Erstellen und Löschen von Containern, Benutzern und gespeicherten Prozeduren. Der Zugriff ist über eine cosmosClient-Instanz nach Name möglich.
  * `Container`: Dieser Client behandelt Vorgänge für einen bestimmten Container. Hierzu zählen das Abfragen und Einfügen von Elementen und das Verwalten von Eigenschaften.
  * `User`: Dieser Client behandelt Vorgänge für einen bestimmten Benutzer. Dies umfasst das Hinzufügen und Löschen von Berechtigungen und das Verwalten von Benutzereigenschaften.

    Der Zugriff auf diese Clients kann durch Abwärtsnavigation in der Clienthierarchie mit der `get_<child>_client`-Methode erfolgen. Ausführliche Informationen zur neuen API finden Sie in der [Referenzdokumentation](https://aka.ms/azsdk-python-cosmos-ref).

* Der Zugriff auf Clients erfolgt über den Namen und nicht über die ID. Es ist nicht erforderlich, Zeichenfolgen zu verketten, um Links zu erstellen.

* Es ist nicht mehr erforderlich, Typen und Methoden aus einzelnen Modulen zu importieren. Die öffentliche API-Oberfläche ist direkt im `azure.cosmos`-Paket verfügbar.

* Einzelne Anforderungseigenschaften können als Schlüsselwortargumente angegeben werden, anstatt eine eigene `RequestOptions`-Instanz zu erstellen.

### <a name="302"></a>3.0.2

* Unterstützung für den MultiPolygon-Datentyp wurde hinzugefügt
* Fehlerbehebung für die Wiederholungsrichtlinie für den Lesevorgang in der Sitzung
* Fehlerbehebung für die falsche Auffüllung beim Decodieren von Base 64-Zeichenfolgen

### <a name="301"></a>3.0.1

* Fehlerbehebung in LocationCache
* Fehlerbehebung für die Endpunktwiederholungslogik
* Fehlerbehebung für die Dokumentation

### <a name="300"></a>3.0.0

* Unterstützung für Schreibvorgänge in mehreren Regionen hinzugefügt
* Benennungsänderungen
  * „DocumentClient“ in „CosmosClient“
  * „Collection“ in „Container“
  * „Document“ in „Item“
  * Der Paketname wurde auf „azure-cosmos“ aktualisiert.
  * Der Namespace wurde auf „azure.cosmos“ aktualisiert.

### <a name="233"></a>2.3.3

* Unterstützung für einen Proxy wurde hinzugefügt
* Unterstützung für das Lesen von Änderungsfeeds wurde hinzugefügt
* Unterstützung für Header des Sammlungskontingents wurde hinzugefügt
* Fehlerbehebung für Probleme bei großen Sitzungstoken
* Fehlerbehebung für die ReadMedia-API
* Fehlerbehebung im Partitionsschlüssel-Bereichscache

### <a name="232"></a>2.3.2

* Unterstützung für Standardwiederholungen bei Verbindungsproblemen hinzugefügt.

### <a name="231"></a>2.3.1

* Die Dokumentation wurde aktualisiert, damit anstelle von Azure DocumentDB auf Azure Cosmos DB verwiesen wird.

### <a name="230"></a>2.3.0

* Für dieses SDK muss die neueste Version des Azure Cosmos DB-Emulators vorhanden sein. Diese können Sie unter https://aka.ms/cosmosdb-emulator herunterladen.

### <a name="221"></a>2.2.1

* Fehlerbehebung für aggregiertes Wörterbuch
* Fehlerbehebung für das Abschneiden von Schrägstrichen im Ressourcenlink
* Tests für Unicode-Codierung

### <a name="220"></a>2.2.0

* Es wurde Unterstützung für das Feature „Anforderungseinheiten pro Minute“ (RU/m) hinzugefügt.
* Es wurde Unterstützung für eine neue Konsistenzebene mit dem Namen „ConsistentPrefix“ hinzugefügt.

### <a name="210"></a>2.1.0

* Unterstützung für Aggregationsabfragen (COUNT, MIN, MAX, SUM und AVG) wurde hinzugefügt.
* Eine Option zum Deaktivieren der SSL-Überprüfung bei der Ausführung für den DocumentDB-Emulator wurde hinzugefügt.
* Die Einschränkung wurde entfernt, dass das Modul für abhängige Anforderungen genau 2.10.0 sein muss.
* Minimaler Durchsatz für partitionierte Sammlungen wurde von 10.100 RU/s auf 2.500 RU/s gesenkt.
* Unterstützung für das Aktivieren der Skriptprotokollierung während der Ausführung einer gespeicherten Prozedur wurde hinzugefügt.
* REST-API-Version wurde mit dieser Version auf „2017-01-19“ (19.01.2017) festgelegt.

### <a name="201"></a>2.0.1

* An Dokumentationskommentaren wurden redaktionelle Änderungen vorgenommen.

### <a name="200"></a>2.0.0

* Unterstützung für Python 3.5 wurde hinzugefügt.
* Unterstützung für Verbindungspooling mithilfe des Anforderungenmoduls wurde hinzugefügt.
* Unterstützung für Sitzungskonsistenz wurde hinzugefügt.
* Unterstützung für TOP-/ORDER BY-Abfragen für partitionierte Sammlungen wurde hinzugefügt.

### <a name="190"></a>1.9.0

* Unterstützung für Wiederholungsrichtlinie für gedrosselte Anforderungen hinzugefügt. (Bei gedrosselten Anforderungen wird die Ausnahme „Anforderungsrate zu groß“, Fehlercode 429, angezeigt.) Standardmäßig führt DocumentDB für jede Anforderung neun Wiederholungen durch, wenn der Fehlercode 429 auftritt, und berücksichtigt dabei die „retryAfter“-Zeit im Antwortheader.
  Eine feste Wiederholungsintervalldauer kann jetzt als Teil der „RetryOptions“-Eigenschaft für das „ConnectionPolicy“-Objekt festgelegt werden, wenn Sie die „retryAfter“-Zeit ignorieren möchten, die vom Server zwischen den Wiederholungen zurückgegeben wird.
  DocumentDB wartet jetzt bei jeder gedrosselten Anforderung (unabhängig von der Anzahl der Wiederholungen) maximal 30 Sekunden und gibt die Antwort mit dem Fehlercode 429 zurück.
  Diese Dauer kann auch in der RetryOptions-Eigenschaft im ConnectionPolicy-Objekt überschrieben werden.

* DocumentDB gibt nun „x-ms-throttle-retry-count“ und „x-ms-throttle-retry-wait-time-ms“ als Antwortheader in jeder Anforderung zurück, um die Anzahl der Wiederholungen bei einer Drosselung und die kumulative Zeit anzugeben, die die Anforderung zwischen den Wiederholungen gewartet hat.

* Die „RetryPolicy“-Klasse und die dazugehörige Eigenschaft (retry_policy), die für die „document_client“-Klasse verfügbar gemacht wurden, wurden entfernt. Stattdessen wurde die „RetryOptions“-Klasse hinzugefügt, die die „RetryOptions“-Eigenschaft in der „ConnectionPolicy“-Klasse verfügbar macht, welche zum Überschreiben einiger der Standardwiederholungsoptionen verwendet werden kann.

### <a name="180"></a>1.8.0

* Unterstützung für georeplizierte Datenbankkonten hinzugefügt
* Testfehlerbehebungen zum Verschieben des globalen Hosts und Hauptschlüssels in die einzelnen Testklassen

### <a name="170"></a>1.7.0

* Unterstützung des TTL-Features (Time To Live) für Dokumente hinzugefügt.

### <a name="161"></a>1.6.1

* Fehlerbehebungen im Zusammenhang mit serverseitiger Partitionierung, um Sonderzeichen im Pfad zum Partitionsschlüssel zuzulassen.

### <a name="160"></a>1.6.0

* Unterstützung für serverseitig partitionierte Sammlungen wurde hinzugefügt.

### <a name="150"></a>1.5.0

* Dem SDK wurde das Framework für clientseitiges Sharding hinzugefügt. Die Klassen „HashPartionResolver“ und „RangePartitionResolver“ wurden implementiert.

### <a name="142"></a>1.4.2

* Upsert implementiert. Neue UpsertXXX-Methoden hinzugefügt, um das „Upsert“-Feature zu unterstützen.
* ID-basiertes Routing implementiert. Keine öffentliche API-Änderungen, alle Änderungen sind intern.

### <a name="130"></a>1.3.0

* Version ausgelassen, um die Versionsnummer in Einklang mit den anderen SDKs zu bringen.

### <a name="120"></a>1.2.0

* GeoSpatial-Index unterstützt.
* „ID“-Eigenschaft wird für alle Ressourcen überprüft. IDs für Ressourcen dürfen nicht mit einem Leerzeichen enden und keines der folgenden Zeichen enthalten: `?, /, #, \\`
* Neue Überschrift „Fortschritt der Indextransformation“ zu „ResourceResponse“ hinzugefügt.

### <a name="110"></a>1.1.0

* V2-Indizierungsrichtlinie implementiert.

### <a name="101"></a>1.0.1

* Proxyverbindung unterstützt

## <a name="release--retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine

Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten. Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

> [!WARNING]
> Nach dem 31. August 2022 werden von Azure Cosmos DB für die Versionen 1.x oder 2.x des Azure Cosmos DB Python SDK für die SQL-API weder Fehlerbehebungen durchgeführt noch neue Funktionen hinzugefügt, und es wird keine Unterstützung bereitgestellt. Wenn Sie kein Upgrade durchführen möchten, werden Anforderungen, die von Version 1.x und 2.x des SDK gesendet werden, weiterhin vom Azure Cosmos DB-Dienst bedient.

| Version | Veröffentlichungsdatum | Deaktivierungstermine |
| --- | --- | --- |
| [4.0.0](#400) |20. Mai 2020 |--- |
| [3.0.2](#302) |15. November 2018 |--- |
| [3.0.1](#301) |4\. Oktober 2018 |--- |
| [2.3.3](#233) |8\. September 2018 |30. August 2020 |
| [2.3.2](#232) |8\. Mai 2018 |30. August 2020 |
| [2.3.1](#231) |21. Dezember 2017 |30. August 2020 |
| [2.3.0](#230) |10. November 2017 |30. August 2020 |
| [2.2.1](#221) |29. September 2017 |30. August 2020 |
| [2.2.0](#220) |10. Mai 2017 |30. August 2020 |
| [2.1.0](#210) |01. Mai 2017 |30. August 2020 |
| [2.0.1](#201) |30. Oktober 2016 |30. August 2020 |
| [2.0.0](#200) |29. September 2016 |30. August 2020 |
| [1.9.0](#190) |7\. Juli 2016 |30. August 2020 |
| [1.8.0](#180) |14. Juni 2016 |30. August 2020 |
| [1.7.0](#170) |26. April 2016 |30. August 2020 |
| [1.6.1](#161) |8\. April 2016 |30. August 2020 |
| [1.6.0](#160) |29. März 2016 |30. August 2020 |
| [1.5.0](#150) |03. Januar 2016 |30. August 2020 |
| [1.4.2](#142) |06. Oktober 2015 |30. August 2020 |
| 1.4.1 |06. Oktober 2015 |30. August 2020 |
| [1.2.0](#120) |06. August 2015 |30. August 2020 |
| [1.1.0](#110) |9\. Juli 2015 |30. August 2020 |
| [1.0.1](#101) |25. Mai 2015 |30. August 2020 |
| 1.0.0 |7\. April 2015 |30. August 2020 |
| 0.9.4-prelease |14. Januar 2015 |29. Februar 2016 |
| 0.9.3-prelease |09. Dezember 2014 |29. Februar 2016 |
| 0.9.2-prelease |25. November 2014 |29. Februar 2016 |
| 0.9.1-prelease |23. September 2014 |29. Februar 2016 |
| 0.9.0-prelease |21.08.14 |29. Februar 2016 |

## <a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).