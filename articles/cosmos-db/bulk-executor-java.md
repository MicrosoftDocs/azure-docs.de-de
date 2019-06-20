---
title: Verwenden der BulkExecutor-Java-Bibliothek zum Ausführen von Massenimport- und -aktualisierungsvorgängen in Azure Cosmos DB
description: Massenimport und -aktualisierung von Azure Cosmos DB-Dokumenten mithilfe der BulkExecutor-Java-Bibliothek.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 68c83809cba0585d99751760c0e4f51893806170
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257195"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Verwenden der BulkExecutor-Java-Bibliothek zum Ausführen von Massenvorgängen in Azure Cosmos DB

Dieses Tutorial bietet Anleitungen zum Verwenden der BulkExecutor-Java-Bibliothek von Azure Cosmos DB zum Importieren und Aktualisieren von Azure Cosmos DB-Dokumenten. Informationen zur BulkExecutor-Bibliothek und dazu, wie Sie damit massiven Durchsatz und riesige Speichermengen nutzen können, finden Sie im Artikel [BulkExecutor-Bibliothek – Übersicht](bulk-executor-overview.md). In diesem Tutorial erstellen Sie eine Java-Anwendung, die zufällige Dokumente generiert. Diese werden per Massenvorgang in einen Azure Cosmos DB-Container importiert. Nach dem Importieren aktualisieren Sie per Massenvorgang einige Eigenschaften eines Dokuments. 

Zurzeit wird die BulkExecutor-Bibliothek nur von Azure Cosmos DB SQL-API- und Gremlin-API-Konten unterstützt. Dieser Artikel beschreibt, wie Sie die BulkExecutor-.NET-Bibliothek mit SQL-API-Konten verwenden. Weitere Informationen zum Verwenden der BulkExecutor-.NET-Bibliothek mit der Gremlin-API finden Sie unter [Ausführen von Massenvorgängen in der Azure Cosmos DB Gremlin-API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.  

* Sie können [Azure Cosmos DB ohne ein Azure-Abonnement testen](https://azure.microsoft.com/try/cosmosdb/) – kostenlos und ohne Verpflichtungen. Alternativ dazu können Sie den [Azure Cosmos DB-Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) mit dem Endpunkt `https://localhost:8081` verwenden. Den Primärschlüssel finden Sie unter [Authentifizieren von Anforderungen](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - Führen Sie unter Ubuntu `apt-get install default-jdk` aus, um das JDK zu installieren.  

  - Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.

* Ein binäres [Maven](https://maven.apache.org/)-Archiv ([Download](https://maven.apache.org/download.cgi)/[Installationsanleitung](https://maven.apache.org/install.html))  
  
  - Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.

* Erstellen Sie ein Azure Cosmos DB-SQL-API-Konto mithilfe der Schritte, die im Abschnitt [Erstellen eines Datenbankkontos](create-sql-api-java.md#create-a-database-account) des Java-Schnellstarttutorials beschrieben werden.

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen Sie jetzt mit der Arbeit mit Code, indem Sie eine Java-Beispielanwendung von GitHub herunterladen. Diese Anwendung führt Massenvorgänge für Azure Cosmos DB-Daten aus. Um die Anwendung zu klonen, öffnen Sie eine Eingabeaufforderung, navigieren Sie zu dem Verzeichnis, in das Sie die Anwendung kopieren möchten, und führen Sie den folgenden Befehl aus:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Das geklonte Repository enthält zwei Beispiele „bulkimport“ und „bulkupdate“ relativ zum Ordner „\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor“. Die Anwendung bulkimport generiert zufällig Dokumente und importiert sie in Azure Cosmos DB. Die Anwendung bulkupdate aktualisiert einige Dokumente in Azure Cosmos DB. In den nächsten Abschnitten sehen wir uns den Code in jeder dieser Beispiel-Apps an. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Massenimport von Daten in Azure Cosmos DB

1. Die Verbindungszeichenfolgen von Azure Cosmos DB werden als Argumente gelesen und Variablen zugewiesen, die in der Datei „CmdLineConfiguration.java“ definiert sind.  

2. Als Nächstes wird mit folgenden Anweisungen das DocumentClient-Objekt initialisiert:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Das DocumentBulkExecutor-Objekt wird mit hohen Wiederholungswerten für Wartezeit und gedrosselte Anforderungen initialisiert. Dann werden die Werte auf 0 festgelegt, um die Überlastungssteuerung während der Lebensdauer an DocumentBulkExecutor zu übergeben.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Rufen Sie die importAll-API auf, über die zufällige Dokumente generiert werden, die per Massenvorgang in einen Azure Cosmos DB-Container importiert werden sollen. Sie können die Befehlszeilenkonfigurationen in der Datei „CmdLineConfiguration.java“ einrichten.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Die Massenimport-API akzeptiert eine Sammlung von JSON-serialisierten Dokumenten und weist die folgende Syntax auf – weitere Informationen finden Sie in der [API-Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Die importAll-Methode akzeptiert die folgenden Parameter:
 
   |**Parameter**  |**Beschreibung**  |
   |---------|---------|
   |isUpsert    |   Ein Flag zum Aktivieren von Upsert-Vorgängen für die Dokumente. Wenn bereits ein Dokument mit der angegebenen ID vorhanden ist, wird es aktualisiert.  |
   |disableAutomaticIdGeneration     |   Ein Flag zum Deaktivieren der automatischen Generierung einer ID. Standardmäßig ist „true“ festgelegt.   |
   |maxConcurrencyPerPartitionRange    |  Der maximale Grad an Parallelität pro Partitionsschlüsselbereich. Der Standardwert lautet 20.  |

   **Definition von Massenimport-Antwortobjekten** – das Ergebnis des Massenimport-API-Aufrufs enthält die folgenden get-Methoden:

   |**Parameter**  |**Beschreibung**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Die Gesamtanzahl von Dokumenten, die von allen an den Massenimport-API-Aufruf gesendeten Dokumenten erfolgreich importiert wurden.      |
   |double getTotalRequestUnitsConsumed()   |  Die Gesamtanzahl von Anforderungseinheiten, die vom Massenimport-API-Aufruf verbraucht wurden.       |
   |Duration getTotalTimeTaken()   |    Die Gesamtzeit, die der Massenimport-API-Aufruf benötigt, um die Ausführung abzuschließen.     |
   |List\<Ausnahme> getErrors() |  Ruft die Liste der Fehler ab, wenn einige der im Batch an den Massenimport-API-Aufruf übermittelten Dokumente nicht eingefügt werden konnten.       |
   |List\<Objekt> getBadInputDocuments()  |    Die Liste der Dokumente mit ungültigem Format, die im Massenimport-API-Aufruf nicht erfolgreich importiert wurden. Benutzer sollten die zurückgegebenen Dokumente korrigieren und den Import wiederholen. Zu Dokumenten mit ungültigem Format gehören auch Dokumente, deren ID-Wert keine Zeichenfolge ist (NULL-Werte oder andere Datentypen werden als ungültig betrachtet).     |

5. Wenn die Massenimportanwendung fertig ist, erstellen Sie das Befehlszeilenprogramm aus der Quelle, indem Sie den Befehl „mvn clean package“ ausführen. Dieser Befehl generiert eine JAR-Datei im Zielordner:  

   ```java
   mvn clean package
   ```

6. Nachdem die Zielabhängigkeiten generiert wurden, können Sie die Anwendung für den Massenimport mithilfe des folgenden Befehls aufrufen:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Die Massenimportanwendung erstellt eine neue Datenbank und eine Sammlung mit dem Datenbanknamen, dem Sammlungsname und Durchsatzwerten, die in der App.config-Datei angegeben sind. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Massenaktualisierung von Daten in Azure Cosmos DB

Sie können vorhandene Dokumente mithilfe der BulkUpdateAsync-API aktualisieren. In diesem Beispiel legen Sie das Feld „Name“ auf einen neuen Wert fest und entfernen das Feld „Description“ aus den vorhandenen Dokumenten. Alle unterstützten Vorgänge zum Aktualisieren von Feldern finden Sie in der [API-Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definiert die Aktualisierungselemente zusammen mit den entsprechenden Vorgängen zum Aktualisieren von Feldern. In diesem Beispiel verwenden Sie SetUpdateOperation zum Aktualisieren des Felds „Name“ und UnsetUpdateOperation zum Entfernen des Felds „Description“ aus allen Dokumenten. Sie können auch andere Vorgänge ausführen, wie z.B. das inkrementelle Erhöhen eines Dokumentfelds um einen bestimmten Wert, das Verschieben bestimmter Werte in ein Arrayfeld oder das Entfernen eines bestimmten Werts aus einem Arrayfeld. Informationen zu den verschiedenen Methoden, die von der API für die Massenaktualisierung bereitgestellt werden, finden Sie in der [API-Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Rufen Sie die updateAll-API auf, über die zufällige Dokumente generiert werden, die per Massenvorgang in einen Azure Cosmos DB-Container importiert werden sollen. Sie können die Befehlszeilenkonfigurationen einrichten, die in der Datei übergeben werden.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Die Massenaktualisierungs-API akzeptiert eine Sammlung von Elementen, die aktualisiert werden sollen. Jedes Aktualisierungselement gibt die Liste der Feldaktualisierungsvorgänge an, die für ein durch eine ID und einen Partitionsschlüsselwert identifiziertes Dokument ausgeführt werden sollen – weitere Einzelheiten finden Sie in der [API-Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Die updateAll-Methode akzeptiert die folgenden Parameter:

   |**Parameter** |**Beschreibung** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Der maximale Grad an Parallelität pro Partitionsschlüsselbereich. Der Standardwert lautet 20.  |
 
   **Definition von Massenimport-Antwortobjekten** – das Ergebnis des Massenimport-API-Aufrufs enthält die folgenden get-Methoden:

   |**Parameter** |**Beschreibung**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Die Gesamtanzahl von Dokumenten, die von allen an den Massenaktualisierungs-API-Aufruf gesendeten Dokumenten erfolgreich aktualisiert wurden.      |
   |double getTotalRequestUnitsConsumed() |  Die Gesamtanzahl von Anforderungseinheiten, die vom Massenaktualisierungs-API-Aufruf verbraucht wurden.       |
   |Duration getTotalTimeTaken()  |   Die Gesamtzeit, die der Massenaktualisierungs-API-Aufruf benötigt, um die Ausführung abzuschließen.      |
   |List\<Ausnahme> getErrors()   |    Ruft die Liste der Fehler ab, wenn einige der im Batch an den Massenaktualisierungs-API-Aufruf übermittelten Dokumente nicht eingefügt werden konnten.      |

3. Wenn die Massenaktualisierungsanwendung fertig ist, erstellen Sie das Befehlszeilenprogramm aus der Quelle, indem Sie den Befehl „mvn clean package“ ausführen. Dieser Befehl generiert eine JAR-Datei im Zielordner:  

   ```
   mvn clean package
   ```

4. Nachdem die Zielabhängigkeiten generiert wurden, können Sie die Anwendung für die Massenaktualisierung mithilfe des folgenden Befehls aufrufen:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Leistungstipps 

Berücksichtigen Sie bei der Verwendung der BulkExecutor-Bibliothek die folgenden Punkte, um die Leistung zu verbessern:

* Um die beste Leistung zu erzielen, führen Sie Ihre Anwendung auf einem virtuellen Azure-Computer in der Region aus, die Sie für Ihre Cosmos DB-Kontoschreibvorgänge verwenden.  
* So erzielen Sie einen höheren Durchsatz:  

   * Legen Sie die JVM-Heapgröße auf einen Wert fest, der so hoch ist, dass Arbeitsspeicherprobleme bei der Verarbeitung einer großen Anzahl von Dokumenten vermieden werden. Empfohlene Heapgröße: max(3GB, 3 * sizeof(all documents passed to bulk import API in one batch)).  
   * Es muss eine gewisse Vorverarbeitungszeit eingerechnet werden, daher erhalten Sie einen höheren Durchsatz, wenn Sie Massenvorgänge mit einer großen Anzahl von Dokumenten ausführen. Wenn Sie z.B. 10.000.000 Dokumente importieren möchten, sollten Sie eher 10 Massenimportvorgänge für 10 Batches mit jeweils 1.000.000 Dokumenten ausführen als 100 Massenimportvorgänge für 100 Batches mit jeweils 100.000 Dokumenten.  

* Es empfiehlt sich, ein einzelnes DocumentBulkExecutor-Objekt für die gesamte Anwendung auf einem einzelnen virtuellen Computer zu instanziieren, das einem bestimmten Azure Cosmos DB-Container entspricht.  

* Eine einzelne Ausführung einer Massenvorgang-API verbraucht eine große Menge an CPU- und Netzwerk-E/A-Ressourcen des Clientcomputers. Dies wird erreicht, indem mehrere Tasks intern erzeugt werden. Vermeiden Sie das Erzeugen mehrerer gleichzeitiger Tasks in Ihrem Anwendungsprozess, von denen jeder Massenvorgang-API-Aufrufe ausführt. Wenn ein einzelner Massenvorgang-API-Aufruf, der auf einem einzelnen virtuellen Computer ausgeführt wird, nicht den gesamten Durchsatz Ihres Containers verbrauchen kann (wenn der Durchsatz mehr als 1 Million Anforderungseinheiten pro Sekunde beträgt), ist es besser, separate virtuelle Computer zu erstellen, um Massenvorgang-API-Aufrufe gleichzeitig auszuführen.

    
## <a name="next-steps"></a>Nächste Schritte
* Informationen zu Maven-Paketen und Versionshinweisen zur BulkExecutor-Java-Bibliothek finden Sie unter [Details zum BulkExecutor-SDK](sql-api-sdk-bulk-executor-java.md).


