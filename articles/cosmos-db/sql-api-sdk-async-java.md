---
title: 'Azure Cosmos DB: SQL Async Java-API, SDK und Ressourcen'
description: Wichtige Informationen zur SQL Async Java-API und zum SDK, einschließlich Veröffentlichungsterminen, Deaktivierungsterminen und Änderungen an den einzelnen Versionen des Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 3/5/2019
ms.author: moderakh
ms.openlocfilehash: 356838f16f7f13506657326bae5dbe994d54bdd5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "57570095"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK für die SQL-API: Versionshinweise und Ressourcen
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

Das Async Java SDK für die SQL-API unterscheidet sich vom Java SDK für die SQL-API dahingehend, dass es asynchrone Vorgänge mit Unterstützung der [Netty-Bibliothek](https://netty.io/) bereitstellt. Das bereits vorhandene [Java SDK für die SQL-API](sql-api-sdk-java.md) unterstützt asynchrone Vorgänge nicht. 

| |  |
|---|---|
| **SDK-Download** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-Dokumentation** |[Java-API-Referenzdokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Am SDK mitwirken** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Erste Schritte** | [Erste Schritte mit dem Async Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Codebeispiel** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Leistungstipps**| [GitHub-Infodatei](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Unterstützte Mindestlaufzeit**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Versionshinweise

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Fehlerbehebung für Ressourcenverlust bei client#close()  ([Github 88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Fortsetzung der Tokenunterstützung für partitionsübergreifende Abfragen hinzugefügt.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Behebung einiger Fehler im direkten Modus.
* Verbesserte Protokollierung im direkten Modus.
* Verbesserte Verbindungsverwaltung.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Konnektivität des direkten Modus ist jetzt allgemein verfügbar (Generally Available, GA). Ein Beispiel, das Konnektivität des direkten Modus verwendet, finden Sie im GitHub-Repository unter [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java).
* Unterstützung für QueryMetrics hinzugefügt.
* Die APIs, die java.util.Collection akzeptieren, wurden geändert, deren Reihenfolge wichtig ist, um stattdessen java.util.List zu akzeptieren. Jetzt wird List von ConnectionPolicy#getPreferredLocations(), JsonSerialization und PartitionKey(.) akzeptiert.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Unterstützung für Konnektivität des direkten Modus hinzugefügt.
* Die APIs, die java.util.Collection akzeptieren, wurden geändert, deren Reihenfolge wichtig ist, um stattdessen java.util.List zu akzeptieren.
  Jetzt wird List von ConnectionPolicy#getPreferredLocations(), JsonSerialization und PartitionKey(.) akzeptiert.
* Für Dokumentabfragen im Gatewaymodus wurde ein Sitzungsfehler behoben.
* Die Abhängigkeiten (netty 0.4.20 [GitHub #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8) wurden aktualisiert.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Fehlerbehebung bei der Behandlung sehr großer Abfrageantworten.
* Fehlerbehebung bei der Ressourcetokenbehandlung bei der Instanziierung von Clients ([Github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Upgrade für anfällige jackson-databind-Abhängigkeit ([Github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Ein Ressourcenverlustfehler wurde behoben.
* MultiPolygon-Unterstützung wurde hinzugefügt.
* Unterstützung benutzerdefinierter Header in „RequestOptions“ wurde hinzugefügt.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Ein Fehler beim Packen wurde behoben.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Ein NPE-Fehler im Pfad für die Wiederholung des Schreibvorgangs wurde behoben.
* Ein NPE-Fehler bei der Endpunktverwaltung wurde behoben.
* Upgrade für anfällige Abhängigkeiten wurde durchgeführt ([GitHub 68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Unterstützung für Netty-Netzwerkprotokollierung für die Problembehandlung wurde hinzugefügt.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Unterstützung für Schreibvorgänge in mehreren Regionen wurde hinzugefügt.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Unterstützung für einen Proxy wurde hinzugefügt.
* Unterstützung für Ressourcentokenautorisierung wurde hinzugefügt.
* Ein Problem beim Verarbeiten großer Partitionsschlüssel ([GitHub 63](https://github.com/Azure/azure-cosmosdb-java/issues/63)) wurde behoben.
* Die Dokumentation wurde verbessert.
* Das SDK wurde in feiner abgestimmte Module umstrukturiert.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Ein Fehler für nicht englische Gebietsschemas wurde behoben ([GitHub 51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* In der Konfliktressource wurden Hilfsmethoden hinzugefügt.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Die Abhängigkeit „org.json“ wurde aus Leistungs- und Lizenzgründen durch „jackson“ ersetzt ([GitHub 29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Die veraltete OfferV2-Klasse wurde entfernt.
* Die Zugriffsmethode wurde für die Offer-Klasse für Durchsatzinhalte hinzugefügt.
* Alle Methoden in „Document/Resource“, die org.json-Typen zurückgeben, wurden geändert, um einen jackson-Objekttyp zurückzugeben.
* getObject(.)-Methode von Klassen, die „JsonSerializable“ erweitern, wurde geändert, um einen jackson-ObjectNode-Typ zurückzugeben.
* getCollection(.)-Methode wurde geändert, um eine Sammlung von „ObjectNode“ zurückzugeben.
* Konstruktoren für JsonSerializable-Unterklassen mit org.json.JSONObject-Argumenten wurden entfernt.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) verwendet nun zwei Leerzeichen für die Einrückung.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Unterstützung für die Richtlinie des eindeutigen Index wurde hinzugefügt.
* Unterstützung für die Begrenzung der Antwort bezüglich der Größe von Fortsetzungstoken in Feedoptionen wurde hinzugefügt.
* Unterstützung für die Partitionsaufteilung in partitionsübergreifenden Abfragen wurde hinzugefügt.
* Ein Fehler in der JSON-Zeitstempelserialisierung wurde behoben ([GitHub 32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Fehler in der JSON-Enumerationsserialisierung wurde behoben.
* Ein Fehler bei der Verwaltung von Dokumenten mit einer Größe von 2 MB wurde behoben ([GitHub 33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Die Abhängigkeit „com.fasterxml.jackson.core:jackson-databind“ wurde aufgrund eines Fehlers auf 2.9.5 aktualisiert ([jackson-databind: GitHub 1599](https://github.com/FasterXML/jackson-databind/issues/1599)).
* Die Abhängigkeit von „rxjava-extras“ wurde aufgrund eines Fehlers auf 0.8.0.17 aktualisiert ([rxjava-extras: GitHub 30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Die Metadatenbeschreibung in der Pom-Datei wurde aktualisiert, damit sie mit dem Rest der Dokumentation übereinstimmt.
* Syntaxoptimierung ([GitHub 41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub 40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Rückstauunterstützung in Abfrage hinzugefügt
* Unterstützung für die ID des Partitionsschlüsselbereichs in der Abfrage hinzugefügt
* Eine Korrektur wurde durchgeführt, um ein größeres Fortsetzungstoken im Anforderungsheader zu ermöglichen (Fehlerbehebung: GitHub 24).
* Netty-Abhängigkeit aktualisiert auf 4.1.22. Sicherstellung, dass JVM nach Abschluss des Hauptthreads herunterfährt.
* Korrektur, um beim Lesen der Masterressourcen die Übergabe des Sitzungstokens zu verhindern
* Weitere Beispiele hinzugefügt
* Weitere Benchmarktestszenarien hinzugefügt
* Java-Headerdateien zur ordnungsgemäßen Erstellung von Java-Dokumenten korrigiert

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allgemein verfügbares SDK mit End-to-End-Unterstützung für nicht blockierende EA-Vorgänge mit der [Netty-Bibliothek](https://netty.io/) im Gatewaymodus 

## <a name="release-and-retirement-dates"></a>Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur zum aktuellen SDK hinzugefügt. Daher wird empfohlen, stets so früh wie möglich ein Upgrade auf die aktuelle SDK-Version auszuführen.

Anforderungen an Cosmos DB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

<br/>

| Version | Herausgabedatum | Deaktivierungstermine |
| --- | --- | --- |
| [2.4.3](#2.4.3) |5\. März 2019|--- |
| [2.4.2](#2.4.2) |1\. März 2019|--- |
| [2.4.1](#2.4.1) |20\. Februar 2019|--- |
| [2.4.0](#2.4.0) |8\. Februar 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |04\. Februar 2019|--- |
| [2.3.1](#2.3.1) |15\. Januar 2019|--- |
| [2.3.0](#2.3.0) |29\. November 2018|--- |
| [2.2.2](#2.2.2) |8\. November 2018|--- |
| [2.2.1](#2.2.1) |2\. November 2018|--- |
| [2.2.0](#2.2.0) |22\. September 2018|--- |
| [2.1.0](#2.1.0) |5\. September 2018|--- |
| [2.0.1](#2.0.1) |16\. August 2018|--- |
| [2.0.0](#2.0.0) |20\. Juni 2018|--- |
| [1.0.2](#1.0.2) |18\. Mai 2018|--- |
| [1.0.1](#1.0.1) |20\. April 2018|--- |
| [1.0.0](#1.0.0) |27\. Februar 2018|--- |

## <a name="faq"></a>Häufig gestellte Fragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Weitere Informationen
Weitere Informationen zu Cosmos DB finden Sie auf der Seite zum Dienst [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

