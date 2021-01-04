---
title: Leistungstipps für das Azure Cosmos DB Async Java SDK v2
description: Erfahren Sie, wie Sie mit Clientkonfigurationsoptionen Verbesserungen an der Leistung von Azure Cosmos-Datenbanken für das Async Java SDK v2 vornehmen.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: bd009ae4909c8cb016a31323294df3a359eb7c51
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033662"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Leistungstipps für das Azure Cosmos DB Async Java SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)


> [!IMPORTANT]  
> Dies ist *nicht* das neueste Java SDK für Azure Cosmos DB! Sie sollten ein Upgrade Ihres Projekts auf das [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) durchführen und dann die [Leistungstipps](performance-tips-java-sdk-v4-sql.md) für das Azure Cosmos DB Java SDK v4 lesen. Befolgen Sie für ein Upgrade die Anweisungen in den Anleitungen [Migrieren zum Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) und [Gegenüberstellung von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
> 
> Die Leistungstipps in diesem Artikel gelten ausschließlich für das Azure Cosmos DB Async Java SDK v2. Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-async-java.md) zum Azure Cosmos DB Async Java SDK v2, im [Maven-Repository](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) und im [Leitfaden zur Problembehandlung](troubleshoot-java-async-sdk.md) für das Azure Cosmos DB Async Java SDK v2.
>

Azure Cosmos DB ist eine schnelle und flexible verteilte Datenbank mit nahtloser Skalierung, garantierter Latenz und garantiertem Durchsatz. Die Skalierung Ihrer Datenbank mit Azure Cosmos DB erfordert weder aufwendige Änderungen an der Architektur noch das Schreiben von komplexem Code. Zentrales Hoch- und Herunterskalieren ist ebenso problemlos möglich wie das Aufrufen einer einzelnen API oder SDK-Methode. Da der Zugriff auf Azure Cosmos DB jedoch über Netzwerkaufrufe erfolgt, können Sie bei der Verwendung des [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) clientseitige Optimierungen vornehmen, um eine optimale Leistung zu erzielen.

Im Anschluss finden Sie einige Optionen zur Optimierung der Datenbankleistung:

## <a name="networking"></a>Netzwerk

* **Verbindungsmodus: Verwenden des direkten Modus**
    
  Die Art der Verbindung eines Clients mit Azure Cosmos DB hat erhebliche Auswirkungen auf die Leistung, insbesondere im Hinblick auf die clientseitige Latenz. Für die Konfiguration der *ConnectionPolicy* des Clients ist *ConnectionMode* eine wichtige Konfigurationseinstellung. Die zwei für das Azure Cosmos DB Async Java SDK v2 verfügbaren ConnectionMode-Konfigurationseinstellungen lauten wie folgt:  
      
  * [Gateway (Standard)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direkt](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  Der Gatewaymodus wird auf allen SDK-Plattformen unterstützt und ist als Standardoption konfiguriert. Wenn Ihre Anwendung in einem Unternehmensnetzwerk mit strengen Firewalleinschränkungen ausgeführt wird, ist der Gatewaymodus die beste Wahl, da er den HTTPS-Standardport und einen einzelnen Endpunkt verwendet.   Im Gatewaymodus ist jedoch jeweils ein zusätzlicher Netzwerkhop erforderlich, wenn Daten in Azure Cosmos DB geschrieben oder daraus gelesen werden, was sich negativ auf die Leistung auswirkt. Aus diesem Grund bietet der direkte Modus die bessere Leistung, da weniger Netzwerkhops erforderlich sind.
  
  *ConnectionMode* wird im Zuge der Erstellung der *DocumentClient*-Instanz mit dem *ConnectionPolicy*-Parameter konfiguriert.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Platzieren der Clients in der gleichen Azure-Region**

  Platzieren Sie nach Möglichkeit sämtliche Anwendungen, die Azure Cosmos DB aufrufen, in der gleichen Region wie die Azure Cosmos-Datenbank. Damit Sie einen ungefähren Vergleich haben: Azure Cosmos DB-Aufrufe aus derselben Region werden normalerweise innerhalb von ca. 1 bis 2 ms abgeschlossen, während die Latenz zwischen West- und Ostküste der USA mehr als 50 ms beträgt. Diese Latenz variiert ggf. von Anforderung zu Anforderung und ist abhängig von der Route, die die Anforderung zwischen dem Client und der Grenze des Azure-Datencenters nimmt. Die geringste Latenz erzielen Sie, wenn sich die aufrufende Anwendung in der gleichen Azure-Region wie der bereitgestellte Azure Cosmos DB-Endpunkt befindet. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/#services).

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Abbildung der Azure Cosmos DB-Verbindungsrichtlinie" border="false":::

## <a name="sdk-usage"></a>SDK-Verwendung

* **Installieren des neuesten SDKs**

  Azure Cosmos DB-SDKs werden ständig verbessert, um eine optimale Leistung zu ermöglichen. Informationen zum neuesten SDK und zu den Verbesserungen finden Sie in den [Versionshinweisen](sql-api-sdk-async-java.md) zum Azure Cosmos DB Async Java SDK v2.

* **Verwenden eines Singleton-Azure Cosmos DB-Clients für die Lebensdauer der Anwendung**

  Jede AsyncDocumentClient-Instanz ist threadsicher und verfügt über eine effiziente Verbindungsverwaltung und Adressenzwischenspeicherung. Zur Ermöglichung einer effizienten Verbindungsverwaltung und einer besseren AsyncDocumentClient-Leistung empfiehlt es sich, für die Lebensdauer der Anwendung pro Anwendungsdomäne eine einzelne AsyncDocumentClient-Instanz zu verwenden.

* **Optimieren von ConnectionPolicy**

  Bei Verwendung des Azure Cosmos DB Async Java SDK v2 erfolgen Cosmos DB-Anforderungen im direkten Modus standardmäßig über TCP. Intern verwendet das SDK eine spezielle Architektur für den direkten Modus, um Netzwerkressourcen dynamisch zu verwalten und die beste Leistung zu erzielen.

  Im Azure Cosmos DB Async Java SDK v2 ist der direkte Modus die beste Wahl, um die Datenbankleistung bei den meisten Workloads zu verbessern. 

  * ***Übersicht über den direkten Modus** _

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Darstellung der Architektur im direkten Modus" border="false":::
  
  Die clientseitige Architektur, die im direkten Modus eingesetzt wird, ermöglicht vorhersagbare Netzwerkauslastungen und Multiplexzugriff auf Azure Cosmos DB-Replikate. Das obige Diagramm zeigt, wie Clientanforderungen im direkten Modus an Replikate im Cosmos DB-Back-End weitergeleitet werden. Bei der Architektur für den direkten Modus werden auf der Clientseite bis zu 10 _ *Kanäle** pro Datenbankreplikat zugeordnet. Ein Kanal ist eine TCP-Verbindung mit einem vorgeschalteten Anforderungspuffer, der 30 Anforderungen aufnehmen kann. Die zu einem Replikat gehörenden Kanäle werden nach Bedarf dynamisch vom **Dienstendpunkt** des Replikats zugeordnet. Wenn der Benutzer eine Anforderung im direkten Modus übermittelt, leitet der **TransportClient** die Anforderung basierend auf dem Partitionsschlüssel an den richtigen Dienstendpunkt weiter. In der **Anforderungswarteschlange** werden die Anforderungen vor dem Dienstendpunkt gepuffert.

  * ***ConnectionPolicy-Konfigurationsoptionen für den direkten Modus** _

    Verwenden Sie als ersten Schritt die folgenden empfohlenen Konfigurationseinstellungen. Wenden Sie sich an das [Azure Cosmos DB-Team](mailto:CosmosDBPerformanceSupport@service.microsoft.com), wenn Sie mit diesem speziellen Thema Probleme haben.

    Wenn Sie Azure Cosmos DB als Verweisdatenbank verwenden (d. h., die Datenbank wird für viele Punktlesevorgänge und nur wenige Schreibvorgänge verwendet), ist es möglicherweise akzeptabel, _idleEndpointTimeout* auf 0 (null) festzulegen (d. h., es gibt keinen Timeout).


    | Konfigurationsoption       | Standard    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | connectionTimeout          | „PT1M“     |
    | idleChannelTimeout         | „PT0S“     |
    | idleEndpointTimeout        | „PT1M10S“  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | „PT1M5S“   |
    | requestExpiryInterval      | „PT5S“     |
    | requestTimeout             | „PT1M“     |
    | requestTimerResolution     | „PT0.5S“   |
    | sendHangDetectionTime      | „PT10S“    |
    | shutdownTimeout            | „PT15S“    |

* ***Programmiertipps für den direkten Modus** _

  Der Artikel zum [Behandeln von Problemen](troubleshoot-java-async-sdk.md) beim Azure Cosmos DB Async Java SDK v2 bietet Ihnen grundlegende Informationen zum Beheben von SDK-Problemen.
  
  Einige wichtige Programmiertipps bei der Verwendung des direkten Modus:
  
  _ **Verwenden Sie Multithreading in der Anwendung für eine effiziente TCP-Datenübertragung:** Nachdem Sie eine Anforderung übermittelt haben, sollte Ihre Anwendung den Empfang von Daten in einem anderen Thread abonnieren. Andernfalls wird ein unbeabsichtigter „Halbduplexvorgang“ erzwungen, und die nachfolgenden Anforderungen werden blockiert, um auf die Antwort der vorherigen Anforderung zu warten.
  
  * **Führen Sie rechenintensive Workloads in einem dedizierten Thread aus**: Aus ähnlichen Gründen wie beim vorherigen Tipp sollten Vorgänge wie komplexe Datenverarbeitungen am besten in einem separaten Thread ausgeführt werden. Wenn eine Anforderung Daten aus einem anderen Datenspeicher abruft (z. B. wenn der Thread Azure Cosmos DB und Spark-Datenspeicher gleichzeitig verwendet), kann dies zu einer längeren Wartezeit führen. Es empfiehlt sich daher, einen zusätzlichen Thread zu erzeugen, der auf eine Antwort vom anderen Datenspeicher wartet.
  
    * Die zugrunde liegenden Netzwerk-E/A im Azure Cosmos DB Async Java SDK v2 wird von Netty verwaltet. Lesen Sie daher auch diese [Tipps zur Vermeidung von Codierungsmustern, die Netty-E/A-Threads blockieren](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Datenmodellierung:** Bei der SLA für Azure Cosmos DB wird davon ausgegangen, dass die Dokumente kleiner als 1 KB sind. Wenn Sie Ihr Datenmodell und Ihre Programmierung für geringere Dokumentgrößen optimieren, führt dies im Allgemeinen zu einer niedrigeren Latenz. Wenn Sie die Speicherung und den Abruf von Dokumenten mit mehr als 1 KB benötigen, sollten die Dokumente möglichst mit Daten in Azure Blob Storage verknüpft werden.

* **Optimieren von parallelen Abfragen für partitionierte Sammlungen**

  Das Azure Cosmos DB Async Java SDK v2 unterstützt parallele Abfragen, sodass Sie eine partitionierte Sammlung parallel abfragen können. Weitere Informationen finden Sie in den [Codebeispielen](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) für die Arbeit mit den SDKs. Parallele Abfragen sind darauf ausgelegt, Latenz und Durchsatz im Vergleich mit seriellen Abfragen zu verbessern.

  * ***Optimieren von setMaxDegreeOfParallelism\:** _
    
    Bei parallelen Abfragen werden mehrere Partitionen parallel abgefragt. Die Daten einer individuell partitionierten Sammlung werden in Bezug auf die Abfrage aber seriell abgerufen. Legen Sie also „setMaxDegreeOfParallelism“ auf die Anzahl von Partitionen fest, bei der die Wahrscheinlichkeit, dass die bestmögliche Leistung für die Abfrage erzielt wird, am höchsten ist (vorausgesetzt, alle anderen Systembedingungen bleiben unverändert). Falls Ihnen die Anzahl von Partitionen nicht bekannt ist, können Sie setMaxDegreeOfParallelism auf einen hohen Wert festlegen. Das System wählt für den maximalen Grad an Parallelität dann den minimalen Wert aus (Anzahl von Partitionen, Benutzereingabe).

    Es ist wichtig zu beachten, dass sich für parallele Abfragen die größten Vorteile ergeben, wenn die Daten in Bezug auf die Abfrage gleichmäßig auf alle Partitionen verteilt werden. Wenn die partitionierte Auflistung so partitioniert ist, dass sich alle Daten bzw. die meisten Daten, die von einer Abfrage zurückgegeben werden, auf einigen wenigen Partitionen befinden (schlimmstenfalls nur auf einer Partition), können aufgrund dieser Partitionierung Engpässe bei der Leistung auftreten.

  _***Optimieren von setMaxBufferedItemCount\:** _
    
    Parallele Abfragen sind so konzipiert, dass Ergebnisse vorab abgerufen werden, während der Client den aktuellen Batch der Ergebnisse verarbeitet. Diese Art des Abrufs führt zu einer Verbesserung der Latenz einer Abfrage. setMaxBufferedItemCount begrenzt die Anzahl von vorab abgerufenen Ergebnissen. Wenn Sie „setMaxBufferedItemCount“ auf die erwartete Anzahl von zurückgegebenen Ergebnissen (oder eine höhere Anzahl) festlegen, ist der Vorteil durch das vorherige Abrufen für die Abfrage am größten.

    Das vorherige Abrufen funktioniert unabhängig von MaxDegreeOfParallelism, und es ist nur ein Puffer für die Daten aller Partitionen vorhanden.

_ **Implementieren von Backoff in getRetryAfterInMilliseconds-Intervallen**

  Es empfiehlt sich, die Last während Leistungstests so lange erhöhen, bis eine geringe Menge von Anforderungen gedrosselt wird. Wenn es sich um eine gedrosselte Anwendung handelt, sollte die Clientanwendung für das vom Server angegebene Wiederholungsintervall aussetzen. Durch das Aussetzen wird die geringstmögliche Wartezeit zwischen den Wiederholungsversuchen gewährleistet.

* **Aufskalieren Ihrer Clientworkload**

  Wenn Sie auf einem hohen Durchsatzniveau testen (> 50.000 RU/s), kann sich die Clientanwendung als Engpass erweisen, da der Computer die CPU- oder Netzwerkauslastung deckelt. Wenn dieser Punkt erreicht wird, können Sie das Azure Cosmos DB-Konto weiter auslasten, indem Sie Ihre Clientanwendungen auf mehrere Server horizontal hochskalieren.

* **Verwenden von namensbasierter Adressierung**

  Verwenden Sie anstelle von SelfLinks (`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`self) mit dem Format \_ die namensbasierte Adressierung mit Links im Format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`, damit nicht die Ressourcen-IDs aller Ressourcen zum Erstellen der Verknüpfung abgerufen werden. Da diese Ressourcen zudem (möglicherweise mit demselben Namen) neu erstellt werden, ist ein Zwischenspeichern in diesem Fall eventuell wirkungslos.

* **Optimieren der Seitengröße für Abfragen/Lesefeeds, um die Leistung zu verbessern**

  Wenn mehrere Dokumente mithilfe der Lesefeedfunktion (z.B. „readDocuments“) gleichzeitig gelesen werden oder eine SQL-Abfrage ausgegeben wird, werden die Ergebnisse bei der Rückgabe segmentiert, falls das Resultset zu groß ist. Ergebnisse werden standardmäßig in Blöcken mit je 100 Elementen oder 1 MB zurückgegeben (je nachdem, welcher Grenzwert zuerst erreicht wird).

  Mithilfe des Anforderungsheaders [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) können Sie die Seitengröße auf bis zu 1000 erhöhen und so die Anzahl von Netzwerkroundtrips verringern, die zum Abrufen aller entsprechenden Ergebnisse erforderlich sind. Falls nur einige wenige Ergebnisse angezeigt werden müssen (etwa, wenn von der Benutzeroberfläche oder Anwendungs-API lediglich zehn Ergebnisse zurückgegeben werden), können Sie die Seitengröße auch auf 10 verringern, um den durch Lese- und Abfragevorgänge beanspruchten Durchsatz zu reduzieren.

  Die Seitengröße kann auch mithilfe der setMaxItemCount-Methode festgelegt werden.

* **Verwenden des geeigneten Planers (Vermeiden des Diebstahls von Eventloop-E/A-Threads in Netty)**

  Das Azure Cosmos DB Async Java SDK v2 verwendet [Netty](https://netty.io/) für nicht blockierende E/A-Vorgänge. Das SDK verwendet eine feste Anzahl (die Anzahl von CPU-Kernen Ihres Computers) von E/A-Eventloop-Threads in Netty zum Ausführen von E/A-Vorgängen. Das von der API zurückgegebene Observable-Objekt gibt das Ergebnis auf einem der freigegebenen E/A-Eventloop-Threads in Netty aus. Daher ist es wichtig, die freigegebenen E/A-Eventloop-Threads in Netty nicht zu blockieren. CPU-intensives Arbeiten oder das Blockieren von Vorgängen auf dem E/A-Eventloop-Thread in Netty kann einen Deadlock verursachen oder den SDK-Durchsatz deutlich reduzieren.

  Beispiel: Der folgende Code führt eine CPU-intensive Arbeit auf dem E/A-Eventloop-Thread in Netty aus:

  **Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Wenn Sie nach dem Empfang des Ergebnisses CPU-intensive Arbeit am Ergebnis durchführen möchten, sollte diese nicht auf dem E/A-Eventloop-Thread in Netty erfolgen. Stattdessen können Sie einen eigenen Planer bereitstellen, um einen eigenen Thread für die Ausführung Ihrer Arbeit zur Verfügung zu stellen.

  **Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Je nach Art der Arbeit müssen Sie den entsprechenden vorhandenen RxJava-Planer für Ihre Arbeit verwenden. Lesen Sie hier nach: [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

  Weitere Informationen finden Sie auf der [GitHub-Seite](https://github.com/Azure/azure-cosmosdb-java) für das Azure Cosmos DB Async Java SDK v2.

* **Deaktivieren der Netty-Protokollierung**

    Die Netty-Bibliotheksprotokollierung führt zu übermäßiger Kommunikation und muss deaktiviert werden (das Unterdrücken der Anmeldung in der Konfiguration reicht möglicherweise nicht aus), um zusätzliche CPU-Kosten zu vermeiden. Wenn Sie sich nicht im Debuggingmodus befinden, deaktivieren Sie die Protokollierung von Netty vollständig. Wenn Sie also „log4j“ verwenden, um die zusätzlichen CPU-Kosten zu verhindern, die durch ``org.apache.log4j.Category.callAppenders()`` von Netty anfallen, fügen Sie Ihrer Codebasis die folgende Zeile hinzu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Ressourcengrenzwert des Betriebssystems für geöffnete Dateien**
 
    Einige Linux-Systeme (z. B. Red Hat) haben eine Obergrenze für die Anzahl von offenen Dateien und damit für die Gesamtanzahl von Verbindungen. Führen Sie den folgenden Befehl aus, um die aktuellen Grenzwerte anzuzeigen:

    ```bash
    ulimit -a
    ```

    Die Anzahl von geöffneten Dateien (nofile) muss groß sein, damit ausreichend Platz für Ihre konfigurierte Verbindungspoolgröße und andere offene Dateien des Betriebssystem vorhanden ist. Sie kann geändert werden, um einen größeren Verbindungspool zu ermöglichen.

    Öffnen Sie die Datei „limits.conf“:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Ändern Sie die folgenden Zeilen, bzw. fügen Sie sie hinzu:

    ```
    * - nofile 100000
    ```

## <a name="indexing-policy"></a>Indizierungsrichtlinien
 
* **Beschleunigen von Schreibvorgängen durch Ausschließen nicht verwendeter Pfade von der Indizierung**

    Die Indizierungsrichtlinie von Azure Cosmos DB ermöglicht auch die Verwendung von Indizierungspfaden („setIncludedPaths“ und „setExcludedPaths“), um anzugeben, welche Dokumentpfade in die Indizierung ein- bzw. von der Indizierung ausgeschlossen werden sollen. Der folgenden Code zeigt beispielsweise, wie Sie einen gesamten Abschnitt der Dokumente (auch als Unterstruktur bezeichnet) mit dem Platzhalter „*“ von der Indizierung ausschließen.

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Weitere Informationen finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](./index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a>Durchsatz

* **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

    Azure Cosmos DB bietet vielfältige Datenbankvorgänge (einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger), die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

    Der Durchsatz wird basierend auf der für jeden Container festgelegten Anzahl von [Anforderungseinheiten](request-units.md) bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitenrate für ihre Container überschreiten, werden begrenzt, bis die Rate wieder unter das bereitgestellte Niveau für den Container fällt. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, können Sie ihn durch Bereitstellung zusätzlicher Anforderungseinheiten erhöhen.

    Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten für einen Vorgang verbraucht werden. Die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets beeinflussen die Kosten von Abfragevorgängen.

    Untersuchen Sie zum Ermitteln des Indizierungsaufwands für einen beliebigen Vorgang (Erstellen, Aktualisieren oder Löschen) den Header [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). So ermitteln Sie die Anzahl von Anforderungseinheiten, die von diesen Vorgängen genutzt werden. Sie können sich außerdem die entsprechende RequestCharge-Eigenschaft in ResourceResponse\<T> oder FeedResponse\<T> ansehen.

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Bei der in diesem Header zurückgegebenen Anforderungsbelastung handelt es sich um einen Bruchteil Ihres bereitgestellten Durchsatzes. Falls Sie beispielsweise 2000 RU/s bereitgestellt und die obige Abfrage 1000 Dokumente mit einer Größe von 1 KB zurückgibt, fallen für den Vorgang Kosten in Höhe von 1000 an. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md) sowie unter dem [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).

* **Behandeln von Ratenbeschränkungen/zu hohen Anforderungsraten**

    Wenn ein Client versucht, den für ein Konto reservierten Durchsatz zu überschreiten, wird die Serverleistung nicht beeinträchtigt, und es wird kein über die reservierte Kapazität hinausgehender Durchsatz in Anspruch genommen. Der Server beendet die Anforderung präemptiv mit „RequestRateTooLarge“ (HTTP-Statuscode 429) und gibt den Header [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) zurück. Darin ist die Zeitspanne (in Millisekunden) angegeben, die der Benutzer warten muss, bis ein neuer Anforderungsversuch unternommen werden kann.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    Alle SDKs fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Wenn nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

    Falls mehrere Clients kumulativ und kontinuierlich die Anforderungsrate überschreiten, reicht die intern vom Client festgelegte Standardanzahl von neun Wiederholungen unter Umständen nicht aus. In diesem Fall löst der Client für die Anwendung eine DocumentClientException mit dem Statuscode 429 aus. Die standardmäßige Wiederholungsanzahl kann durch Verwendung von „setRetryOptions“ für die ConnectionPolicy-Instanz geändert werden. Die DocumentClientException mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dies gilt auch, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt.

    Das automatisierte Wiederholungsverhalten trägt zwar bei den meisten Anwendungen zur Verbesserung der Resilienz und Nutzbarkeit bei, kann bei Leistungsbenchmarks aber auch hinderlich sein (insbesondere beim Ermitteln der Latenz). Die Wartezeit für den Client nimmt stark zu, wenn das Experiment die Serverdrosselung erreicht und damit die automatische Wiederholung durch das Client-SDK auslöst. Ermitteln Sie zur Vermeidung von Latenzspitzenwerten bei Leistungsexperimenten die von den einzelnen Vorgängen zurückgegebene Belastung, und stellen Sie sicher, dass die Anforderungen die reservierte Anforderungsrate nicht überschreiten. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md).

* **Konzipieren für kleinere Dokumente und höheren Durchsatz**

    Die Anforderungsbelastung (die Kosten für die Anforderungsverarbeitung) eines Vorgangs hängt direkt mit der Größe des Dokuments zusammen. Vorgänge für große Dokumente sind teurer als Vorgänge für kleine Dateien.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwerfen einer auf Skalierung und hohe Leistung ausgelegten Anwendung finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md).