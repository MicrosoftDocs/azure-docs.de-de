---
title: Migrieren von Couchbase zu Azure Cosmos DB-SQL-API
description: Schrittweise Anleitung zum Migrieren von Couchbase zu Azure Cosmos DB-SQL-API
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: e84b80233d87ac4ae5e2281b506e225c4ab1bd9d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357601"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrieren von Couchbase zu Azure Cosmos DB-SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ist eine skalierbare, global verteilte, vollständig verwaltete Datenbank. Sie bietet garantierten Zugriff mit geringer Wartezeit auf Ihre Daten. Weitere Informationen zu Azure Cosmos DB finden Sie im Artikel [Übersicht](introduction.md). Dieser Artikel enthält Anleitungen zum Migrieren von Java-Anwendungen, die mit Couchbase verbunden sind, zu einem SQL-API-Konto in Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Unterschiede in der Benennung

Im Folgenden sind die wichtigsten Features aufgeführt, die bei einem Vergleich zwischen Azure Cosmos DB und Couchbase unterschiedlich funktionieren:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase-Server| Konto       |
|Bucket           | Datenbank      |
|Bucket           | Container/Sammlung |
|JSON-Dokument    | Element/Dokument |

## <a name="key-differences"></a>Wesentliche Unterschiede

* Bei Azure Cosmos DB enthält das Dokument ein Feld „ID“, während „ID“ bei Couchbase ein Teil des Buckets ist. Das Feld „ID“ ist innerhalb der Partition eindeutig.

* Azure Cosmos DB wird mithilfe eines der Verfahren Partitionierung oder Sharding skaliert. Dies bedeutet, dass die Daten in mehrere Shards/Partitionen aufgeteilt werden. Diese Partitionen/Shards werden basierend auf der von Ihnen angegebenen Partitionsschlüsseleigenschaft erstellt. Sie können den Partitionsschlüssel auswählen, um Lese- und Schreibvorgänge oder auch optimiertes Lesen/Schreiben zu optimieren. Weitere Informationen finden Sie im Artikel [Partitionierung](./partitioning-overview.md).

* In Azure Cosmos DB muss die Sammlung für die Hierarchie der obersten Ebene nicht angegeben werden, weil der Sammlungsname bereits vorhanden ist. Dieses Feature vereinfacht die JSON-Struktur erheblich. Das folgende Beispiel zeigt Unterschiede im Datenmodell zwischen Couchbase und Azure Cosmos DB:

   **Couchbase**: Dokument-ID = „99FF4444“

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Im Dokument – wie unten gezeigt – auf „ID“ verweisen

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK-Unterstützung

Azure Cosmos DB enthält folgende SDKs zur Unterstützung verschiedener Java-Frameworks:

* Async SDK
* Spring Boot SDK

In den folgenden Abschnitten wird beschrieben, wann die einzelnen SDKs zu verwenden sind. Sehen Sie sich ein Beispiel an, in dem es drei Arten von Workloads gibt:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase als Dokumentrepository und Spring Data-basierte benutzerdefinierte Abfragen

Wenn die von Ihnen migrierte Workload auf dem Springboot-basierten SDK basiert, können Sie die folgenden Schritte ausführen:

1. Übergeordnetes Element zur Datei POM.xml hinzufügen:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Eigenschaften zur Datei POM.xml hinzufügen:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Abhängigkeiten zur Datei POM.xml hinzufügen:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Fügen Sie unter „Ressourcen“ Anwendungseigenschaften hinzu, und geben Sie Folgendes an. Sicherstellen, dass die Parameter „URL“, „Schlüssel“ und „Datenbankname“ ersetzt werden:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definieren Sie den Namen der Sammlung im Modell. Sie können auch weitere Anmerkungen festlegen. Beispiel: „ID“, „Partitionsschlüssel“, um sie explizit anzugeben:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Im Folgenden sind die Codeausschnitte für CRUD-Vorgänge aufgeführt:

### <a name="insert-and-update-operations"></a>Einfügungs- und Aktualisierungsvorgänge

Bei diesen Vorgängen ist *_repo* das Objekt des Repositorys und *doc* das Objekt der POJO-Klasse. Sie können `.save` zum Einfügen oder für Upsert verwenden (wenn das Dokument mit der angegebenen ID gefunden wurde). Der folgende Codeausschnitt zeigt, wie ein Dokumentobjekt eingefügt oder aktualisiert wird:

```_repo.save(doc);```

### <a name="delete-operation"></a>Löschvorgang

Sehen Sie sich den folgenden Codeausschnitt an, in dem die ID und der Partitionsschlüssel für das Dokumentobjekt zum Suchen danach und zum Löschen obligatorisch ist:

```_repo.delete(doc);```

### <a name="read-operation"></a>Lesevorgang

Sie können das Dokument mit oder ohne Angabe des Partitionsschlüssels lesen. Wenn Sie den Partitionsschlüssel nicht angeben, wird das Dokument als partitionsübergreifende Abfrage behandelt. Sehen Sie sich die folgenden Codebeispiele an. Im ersten wird ein Vorgang mithilfe von ID und Partitionsschlüsselfeld ausgeführt. Im zweiten Beispiel wird ein reguläres Feld ohne Angabe des Partitionsschlüsselfelds verwendet.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Dies bedeutet: Sie können jetzt Ihre Anwendung bei Azure Cosmos DB verwenden. Der vollständige Code für das in diesem Dokument beschriebene Beispiel steht im GitHub-Repository [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) zur Verfügung.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase als Dokumentrepository und Verwendung von N1QL-Abfragen

N1QL-Abfragen sind die Möglichkeit zum Definieren von Abfragen in der Couchbase.

|N1QL-Abfrage | Azure CosmosDB Query|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Sie können die folgenden Änderungen in Ihren N1QL-Abfragen feststellen:

* Sie müssen nicht das META-Schlüsselwort verwenden oder auf das Dokument der ersten Ebene verweisen. Stattdessen können Sie Ihren eigenen Verweis auf den Container erstellen. In diesem Beispiel haben wir dafür „c“ gewählt (jeder beliebige Buchstabe ist aber möglich). Dieser Verweis wird als Präfix für alle Felder der ersten Ebene verwendet. Beispiel: „c.id“, „c.land“ usw.

* Statt „ANY“ können Sie ein Unterdokument jetzt auch verknüpfen und darauf mit einem dedizierten Alias wie „m“ verweisen. Sobald Sie einen Alias für ein Unterdokument erstellt haben, müssen Sie ihn auch verwenden. Beispiel: „m.Land“.

* Weil die Reihenfolge von „OFFSET“ in einer Azure Cosmos DB-Abfrage anders ist, müssen Sie zuerst „OFFSET“ und dann „LIMIT“ angeben. Es wird davon abgeraten, das Spring Data SDK bei einer maximalen Anzahl von benutzerdefinierten Abfragen zu verwenden, da dies bei der Übergabe der Abfrage an Azure Cosmos DB zu einem unnötigen Aufwand auf der Clientseite führen kann. Stattdessen gibt es ein direktes Async Java SDK, das in diesem Fall viel effizienter genutzt werden kann.

### <a name="read-operation"></a>Lesevorgang

Verwenden Sie das Async Java SDK mit den folgenden Schritten:

1. Konfigurieren Sie die folgende Abhängigkeit für die Datei POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Erstellen Sie mit der Methode `ConnectionBuilder` ein Verbindungsobjekt für Azure Cosmos DB, wie im folgenden Beispiel gezeigt wird. Sie müssen diese Deklaration unbedingt in das Bean einfügen, damit der folgende Code nur einmal ausgeführt wird:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(PrimaryKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Zur Ausführung der Abfrage müssen Sie den folgenden Codeausschnitt ausführen:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Jetzt können Sie mithilfe der vorstehenden Methode mehrere Abfragen übergeben und ganz unaufwändig ausführen. Falls Sie eine einzige große Abfrage ausführen müssen, die in mehrere Abfragen aufgeteilt werden kann, probieren Sie den folgenden Codeausschnitt statt des vorherigen Codes aus:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Mit dem vorherigen Code können Sie Abfragen parallel ausführen und die verteilten Ausführungen vergrößern, um sie zu optimieren. Außerdem können Sie auch die Einfügungs- und Aktualisierungsvorgänge ausführen:

### <a name="insert-operation"></a>Einfügungsvorgang

Führen Sie zum Einfügen des Dokuments den folgenden Code aus:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Abonnieren Sie dann Mono als:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Upsertvorgang

Beim Upsertvorgang müssen Sie das zu aktualisierende Dokument angeben. Zum Abrufen des gesamten Dokuments können Sie den unter der Überschrift „Lesevorgang“ erwähnten Codeausschnitt verwenden und dann das/die erforderliche(n) Feld(er) ändern. Mit dem folgenden Codeausschnitt wird ein Upsertvorgang für das Dokument ausgeführt:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Abonnieren Sie dann Mono. Verweisen Sie dafür im Einfügungsvorgang auf den Codeausschnitt zum Mono-Abonnement.

### <a name="delete-operation"></a>Löschvorgang

Mit dem folgenden Codeausschnitt wird ein Löschvorgang ausgeführt:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Abonnieren Sie dann Mono, und verweisen Sie dafür im Einfügungsvorgang auf den Codeausschnitt zum Mono-Abonnement. Das vollständige Codebeispiel steht im GitHub-Repository [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) zur Verfügung.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase als Schlüssel-Wert-Paar

Dies ist ein einfacher Workloadtyp, in dem Sie Suchen statt Abfragen ausführen können. Führen Sie die folgenden Schritte für Schlüssel-Wert-Paare aus:

1. Ziehen Sie die Verwendung von „ID“ als Primärschlüssel in Betracht. So stellen Sie sicher, dass Sie einen Suchvorgang direkt in der jeweiligen Partition ausführen können. Erstellen Sie eine Sammlung, und geben Sie „/ID“ als Partitionsschlüssel an.

1. Deaktivieren Sie die Indizierung vollständig. Weil Sie Suchvorgänge ausführen werden, ist es sinnlos, einen Indizierungsaufwand zu erzeugen. Melden Sie sich zum Deaktivieren der Indizierung beim Azure-Portal an, und wechseln Sie zu „Azure Cosmos DB-Konto“. Öffnen Sie den **Daten-Explorer**, wählen Sie Ihre **Datenbank** und den **Container** aus. Öffnen Sie die Registerkarte **Skalierung und Einstellungen**, und wählen Sie die **Indizierungsrichtlinie** aus. Die Indizierungsrichtlinie sieht derzeit folgendermaßen aus:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Ersetzen Sie die vorstehende Indizierungsrichtlinie durch folgende Richtlinie:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Verwenden Sie zum Erstellen des Verbindungsobjekts den folgenden Codeausschnitt: Verbindungsobjekt (einzufügen in @Bean oder als statisch festlegen):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(PrimaryKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Jetzt können Sie die CRUD-Vorgänge folgendermaßen ausführen:

### <a name="read-operation"></a>Lesevorgang

Verwenden Sie zum Lesen des Elements den folgenden Codeausschnitt:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Einfügungsvorgang

Zum Einfügen eines Elements können Sie den folgenden Code ausführen:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Abonnieren Sie dann Mono als:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsertvorgang

Wenn Sie den Wert eines Elements aktualisieren möchten, verweisen Sie auf den nachstehenden Codeausschnitt:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Abonnieren Sie dann Mono, und verweisen Sie dafür im Einfügungsvorgang auf den Codeausschnitt zum Mono-Abonnement.

### <a name="delete-operation"></a>Löschvorgang

Verwenden Sie zum Ausführen des Löschvorgangs den folgenden Codeausschnitt:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Abonnieren Sie dann Mono, und verweisen Sie dafür im Einfügungsvorgang auf den Codeausschnitt zum Mono-Abonnement. Das vollständige Codebeispiel steht im GitHub-Repository [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) zur Verfügung.

## <a name="data-migration"></a>Datenmigration

Es gibt zwei Möglichkeiten zum Migrieren von Daten.

* **Mit Azure Data Factory:** Dies ist die empfehlenswerteste Methode zum Migrieren der Daten. Konfigurieren Sie die Quelle als Couchbase und die Senke als Azure Cosmos DB-SQL-API. Die dafür erforderlichen Schritte finden Sie im Artikel zum [Azure Cosmos DB Data Factory-Connector](../data-factory/connector-azure-cosmos-db.md).

* **Mit dem Azure Cosmos DB-Datenimporttool:** Diese Option wird zum Migrieren einer geringeren Datenmenge mithilfe von VMs empfohlen. Detaillierte Schritte finden Sie im Artikel [Data importer](./import-data.md) (Datenimportprogramm).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Leistungstests finden Sie im Artikel [Leistungs- und Skalierungstests mit Azure Cosmos DB](./performance-testing.md).
* Informationen zum Optimieren des Codes finden Sie im Artikel [Leistungstipps für Azure Cosmos DB](./performance-tips-async-java.md).
* Informationen zum Java Async V3 SDK finden Sie im GitHub-Repository [SDK-Referenz](https://github.com/Azure/azure-cosmosdb-java/tree/v3).
