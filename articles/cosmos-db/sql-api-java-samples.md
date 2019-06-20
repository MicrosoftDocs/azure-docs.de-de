---
title: 'Azure Cosmos DB: Java-Beispiele für die SQL-API'
description: Java-Beispiele für häufige Aufgaben mit der SQL-API von Azure Cosmos DB, einschließlich CRUD-Vorgängen, finden Sie auf GitHub.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: sample
ms.date: 02/08/2019
ms.author: sngun
ms.openlocfilehash: 075ddf2df5c8bd5c6eed04911be1f4a20faf6921
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475762"
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB: Java-Beispiele für die SQL-API

> [!div class="op_single_selector"]
> * [.NET-Beispiele](sql-api-dotnet-samples.md)
> * [Java-Beispiele](sql-api-java-samples.md)
> * [Async Java-Beispiele](sql-api-async-java-samples.md)
> * [Node.js-Beispiele](sql-api-nodejs-samples.md)
> * [Python-Beispiele](sql-api-python-samples.md)
> * [Katalog mit Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Die neuesten Beispielanwendungen, in denen CRUD-Vorgänge und andere gängige Vorgänge für Azure Cosmos DB-Ressourcen ausgeführt werden, finden Sie im GitHub-Repository [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java). Dieser Artikel enthält Folgendes:

* Links zu den Aufgaben in den einzelnen Java-Beispielprojektdateien. 
* Links zum zugehörigen API-Referenzinhalt.

**Voraussetzungen**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Sie können [Visual Studio-Abonnementvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Ihr Visual Studio-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Zum Ausführen dieser Beispielanwendung benötigen Sie Folgendes:

* Java Development Kit 7
* Microsoft Azure DocumentDB Java SDK

Optional können Sie Maven verwenden, um die neuesten Binärdateien des Microsoft Azure DocumentDB Java SDK zur Verwendung in Ihrem Projekt abzurufen. Maven fügt alle erforderlichen Abhängigkeiten automatisch hinzu. Andernfalls können Sie die in der Datei „pom.xml“ aufgelisteten Abhängigkeiten direkt herunterladen und Ihrem Buildpfad hinzufügen.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Ausführen der Beispielanwendungen**

Klonen des Beispielrepositorys:
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Sie können die Beispiele entweder mit Eclipse oder über die Befehlszeile mit Maven ausführen.

So führen Sie die Beispiele über Eclipse aus:
* Laden Sie die Datei „pom.xml“ des übergeordneten Hauptprojekts in Eclipse. Die documentdb-Beispiele sollten automatisch geladen werden.
* Zum Ausführen der Beispiele benötigen Sie einen gültigen Azure Cosmos DB-Endpunkt. Die Endpunkte werden aus `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java` gelesen.
* Sie können Ihre Endpunktanmeldeinformationen als VM-Argumente in der Eclipse JUnit-Ausführungskonfiguration übergeben oder in „AccountCredentials.java“ einfügen.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Jetzt können Sie die Beispiele als JUnit-Tests in Eclipse ausführen.

So führen Sie die Beispiele über die Befehlszeile aus:
* Die zweite Möglichkeit zum Ausführen der Beispiele ist die Verwendung von Maven:
* Führen Sie Maven aus, und übergeben Sie Ihre Azure Cosmos DB-Endpunktanmeldeinformationen:
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Jedes Beispiel ist eigenständig mit eigener Einrichtung und Bereinigung. Die Beispiele richten mehrere Aufrufe an [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection). Dabei wird Ihrem Abonnement jedes Mal 1 Stunde Nutzung gemäß der Leistungsstufe der erstellten Sammlung berechnet. 
   > 
   > 

## <a name="database-examples"></a>Datenbankbeispiele
Die Datei [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Azure Cosmos-Datenbanken zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](databases-containers-items.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen und Lesen einer Datenbank](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |
| [Erstellen und Löschen einer Datenbank](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedatabase) |
| [Erstellen und Abfragen einer Datenbank](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydatabases) |

## <a name="collection-examples"></a>Sammlungsbeispiele
Die Datei [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Azure Cosmos-Sammlungen zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](databases-containers-items.md).

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung mit nur einer Partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Erstellen einer Sammlung mit mehreren Partitionen](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions) |
| [Löschen einer Sammlung](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletecollection) |

## <a name="document-examples"></a>Dokumentbeispiele
Die Datei [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Azure Cosmos-Dokumente zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Arbeiten mit Datenbanken, Containern und Elementen](databases-containers-items.md).

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen, Lesen und Löschen eines Dokuments](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument) |
| [Erstellen eines Dokuments mit einer programmierbaren Dokumentdefinition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Dokument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resource.setid) |

## <a name="indexing-examples"></a>Indizierungsbeispiele
Die Datei [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die Indizierung in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele die Konzeptartikel [Indizierungsrichtlinien](index-policy.md), [Indizierungstypen](index-types.md) und [Indizierungspfade](index-paths.md). 

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen eines Index und Festlegen der Indizierungsrichtlinie](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy) |

Weitere Informationen zur Indizierung finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Abfragebeispiele
Die Datei [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die SQL-Abfragereferenz in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [SQL-Abfragebeispiele](how-to-sql-query.md). 


| Aufgabe | API-Referenz |
| --- | --- |
| [Ausführen einer einfachen partitionsübergreifenden Dokumentabfrage](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setenablecrosspartitionquery) |
| [Sortieren nach Abfrage](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse.getqueryiterator) |

Weitere Informationen zum Schreiben von Abfragen finden Sie unter [SQL-Abfragen für Azure Cosmos DB-DocumentDB-API](how-to-sql-query.md).

## <a name="offer-examples"></a>Angebotsbeispiele
Die Datei [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden:

| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung und Festlegen des Durchsatzes](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.requestoptions.setofferthroughput) |
| [Lesen einer Sammlung, um das zugehörige Angebot zu finden](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.queryoffers) |

## <a name="partition-key-examples"></a>Partitionsschlüsselbeispiele
Die Datei [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über Partitionierung und Partitionsschlüssel in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel zur [Partitionierung](partitioning-overview.md). 


| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer Sammlung mit nur einer Partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) |
| [Ändern des Durchsatzangebots für eine Sammlung mit nur einer Partition](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Beispiele für gespeicherte Prozeduren
Die Datei [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) zeigt, wie die folgenden Aufgaben ausgeführt werden. Um mehr über die serverseitige Programmierung in Azure Cosmos DB zu erfahren, lesen Sie vor dem Ausführen der folgenden Beispiele den Konzeptartikel [Gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen](stored-procedures-triggers-udfs.md). 


| Aufgabe | API-Referenz |
| --- | --- |
| [Erstellen einer gespeicherten Prozedur](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.createstoredprocedure) |
| [Ausführen einer gespeicherten Prozedur mit Argumenten](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
| [Ausführen einer gespeicherten Prozedur mit einem Objektargument](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.executestoredprocedure) |
