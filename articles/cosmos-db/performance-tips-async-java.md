---
title: Leistungstipps für Azure Cosmos DB für Async Java
description: Machen Sie sich mit Clientkonfigurationsoptionen zur Verbesserung der Leistung von Azure Cosmos DB-Datenbanken vertraut.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: fa6ab58de09e26683cdd958ef77b0fa01d88e2e0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225586"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Leistungstipps für Azure Cosmos DB und Async Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB ist eine schnelle und flexible verteilte Datenbank mit nahtloser Skalierung, garantierter Latenz und garantiertem Durchsatz. Die Skalierung Ihrer Datenbank mit Azure Cosmos DB erfordert weder aufwendige Änderungen an der Architektur noch das Schreiben von komplexem Code. Zentrales Hoch- und Herunterskalieren ist ebenso problemlos möglich wie das Aufrufen einer einzelnen API oder SDK-Methode. Da der Zugriff auf Azure Cosmos DB jedoch über Netzwerkaufrufe erfolgt, können Sie bei der Verwendung des [SQL-Async Java-SDK](sql-api-sdk-async-java.md) clientseitige Optimierungen vornehmen, um eine optimale Leistung zu erzielen.

Im Anschluss finden Sie einige Optionen zur Optimierung der Datenbankleistung:

## <a name="networking"></a>Netzwerk
   <a id="same-region"></a>
1. **Platzieren der Clients in der gleichen Azure-Region**

    Platzieren Sie nach Möglichkeit sämtliche Anwendungen, die Azure Cosmos DB aufrufen, in der gleichen Region wie die Azure Cosmos DB-Datenbank. Damit Sie einen ungefähren Vergleich haben: Azure Cosmos DB-Aufrufe aus derselben Region werden normalerweise innerhalb von ca. 1 bis 2 ms abgeschlossen, während die Latenz zwischen West- und Ostküste der USA mehr als 50 ms beträgt. Diese Latenz variiert ggf. von Anforderung zu Anforderung und ist abhängig von der Route, die die Anforderung zwischen dem Client und der Grenze des Azure-Datencenters nimmt. Die geringste Latenz erzielen Sie, wenn sich die aufrufende Anwendung in der gleichen Azure-Region wie der bereitgestellte Azure Cosmos DB-Endpunkt befindet. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/#services).

    ![Abbildung der Azure Cosmos DB-Verbindungsrichtlinie](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK-Verwendung
1. **Installieren des neuesten SDKs**

    Azure Cosmos DB-SDKs werden ständig verbessert, um eine optimale Leistung zu ermöglichen. Informationen zum neuesten SDK und zu den Verbesserungen finden Sie auf den Seiten unter [Azure Cosmos DB SDK](sql-api-sdk-async-java.md).
2. **Verwenden eines Singleton-Azure Cosmos DB-Clients für die Lebensdauer der Anwendung**

    Jede AsyncDocumentClient-Instanz ist threadsicher und verfügt über eine effiziente Verbindungsverwaltung und Adressenzwischenspeicherung. Zur Ermöglichung einer effizienten Verbindungsverwaltung und einer besseren AsyncDocumentClient-Leistung empfiehlt es sich, für die Lebensdauer der Anwendung pro Anwendungsdomäne eine einzelne AsyncDocumentClient-Instanz zu verwenden.

   <a id="max-connection"></a>

3. **Optimieren von ConnectionPolicy**

    Anforderungen an Azure Cosmos DB erfolgen bei Verwendung des Async Java SDK über HTTPS/REST und unterliegen der standardmäßigen maximalen Verbindungspoolgröße (1000). Dieser Standardwert sollte für die meisten Anwendungsfälle ideal sein. Für den Fall, dass Sie über eine große Sammlung mit vielen Partitionen verfügen, können Sie die maximale Verbindungspoolgröße jedoch mithilfe von „setMaxPoolSize“ auf einen höheren Wert (z.B. 1500) festlegen.

4. **Optimieren von parallelen Abfragen für partitionierte Sammlungen**

    Das Azure Cosmos DB-SQL-Async Java-SDK unterstützt parallele Abfragen unterstützt, mit denen Sie eine partitionierte Sammlung parallel abfragen können. Weitere Informationen finden Sie in den [Codebeispielen](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) für die Arbeit mit den SDKs. Parallele Abfragen sind darauf ausgelegt, Latenz und Durchsatz im Vergleich mit seriellen Abfragen zu verbessern.

    (a) ***Optimieren von setMaxDegreeOfParallelism\:*** Bei parallelen Abfragen werden mehrere Partitionen parallel abgefragt. Die Daten einer individuell partitionierten Sammlung werden in Bezug auf die Abfrage aber seriell abgerufen. Legen Sie also „setMaxDegreeOfParallelism“ auf die Anzahl von Partitionen fest, bei der die Wahrscheinlichkeit, dass die bestmögliche Leistung für die Abfrage erzielt wird, am höchsten ist (vorausgesetzt, alle anderen Systembedingungen bleiben unverändert). Falls Ihnen die Anzahl von Partitionen nicht bekannt ist, können Sie setMaxDegreeOfParallelism auf einen hohen Wert festlegen. Das System wählt für den maximalen Grad an Parallelität dann den minimalen Wert aus (Anzahl von Partitionen, Benutzereingabe).

    Es ist wichtig zu beachten, dass sich für parallele Abfragen die größten Vorteile ergeben, wenn die Daten in Bezug auf die Abfrage gleichmäßig auf alle Partitionen verteilt werden. Wenn die partitionierte Auflistung so partitioniert ist, dass sich alle Daten bzw. die meisten Daten, die von einer Abfrage zurückgegeben werden, auf einigen wenigen Partitionen befinden (schlimmstenfalls nur auf einer Partition), können aufgrund dieser Partitionierung Engpässe bei der Leistung auftreten.

    (b) ***Optimieren von setMaxBufferedItemCount\:*** Die parallele Abfrage ist so konzipiert, dass Ergebnisse vorab abgerufen werden, während der Client den aktuellen Batch mit Ergebnissen verarbeitet. Diese Art des Abrufs führt zu einer Verbesserung der Latenz einer Abfrage. setMaxBufferedItemCount begrenzt die Anzahl von vorab abgerufenen Ergebnissen. Wenn Sie „setMaxBufferedItemCount“ auf die erwartete Anzahl von zurückgegebenen Ergebnissen (oder eine höhere Anzahl) festlegen, ist der Vorteil durch das vorherige Abrufen für die Abfrage am größten.

    Das vorherige Abrufen funktioniert unabhängig von MaxDegreeOfParallelism, und es ist nur ein Puffer für die Daten aller Partitionen vorhanden.

5. **Implementieren von Backoff in getRetryAfterInMilliseconds-Intervallen**

    Es empfiehlt sich, die Last während Leistungstests so lange erhöhen, bis eine geringe Menge von Anforderungen gedrosselt wird. Wenn es sich um eine gedrosselte Anwendung handelt, sollte die Clientanwendung für das vom Server angegebene Wiederholungsintervall aussetzen. Durch das Aussetzen wird die geringstmögliche Wartezeit zwischen den Wiederholungsversuchen gewährleistet.
6. **Horizontales Hochskalieren Ihrer Clientworkload**

    Wenn Sie auf einem hohen Durchsatzniveau testen (> 50.000 RU/s), kann sich die Clientanwendung als Engpass erweisen, da der Computer die CPU- oder Netzwerkauslastung deckelt. Wenn dieser Punkt erreicht wird, können Sie das Azure Cosmos DB-Konto weiter auslasten, indem Sie Ihre Clientanwendungen auf mehrere Server horizontal hochskalieren.

7. **Verwenden von namensbasierter Adressierung**

    Verwenden Sie anstelle von SelfLinks (`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`self) mit dem Format \_ die namensbasierte Adressierung mit Links im Format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`, damit nicht die Ressourcen-IDs aller Ressourcen zum Erstellen der Verknüpfung abgerufen werden. Da diese Ressourcen zudem (möglicherweise mit demselben Namen) neu erstellt werden, ist ein Zwischenspeichern in diesem Fall eventuell wirkungslos.

   <a id="tune-page-size"></a>
8. **Optimieren der Seitengröße für Abfragen/Lesefeeds, um die Leistung zu verbessern**

    Wenn mehrere Dokumente mithilfe der Lesefeedfunktion (z.B. „readDocuments“) gleichzeitig gelesen werden oder eine SQL-Abfrage ausgegeben wird, werden die Ergebnisse bei der Rückgabe segmentiert, falls das Resultset zu groß ist. Ergebnisse werden standardmäßig in Blöcken mit je 100 Elementen oder 1 MB zurückgegeben (je nachdem, welcher Grenzwert zuerst erreicht wird).

    Mithilfe des Anforderungsheaders [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) können Sie die Seitengröße auf bis zu 1000 erhöhen und so die Anzahl von Netzwerkroundtrips verringern, die zum Abrufen aller entsprechenden Ergebnisse erforderlich sind. Falls nur einige wenige Ergebnisse angezeigt werden müssen (etwa, wenn von der Benutzeroberfläche oder Anwendungs-API lediglich zehn Ergebnisse zurückgegeben werden), können Sie die Seitengröße auch auf 10 verringern, um den durch Lese- und Abfragevorgänge beanspruchten Durchsatz zu reduzieren.

    Die Seitengröße kann auch mithilfe der setMaxItemCount-Methode festgelegt werden.

9. **Verwenden des geeigneten Planers (Vermeiden des Diebstahls von Eventloop-E/A-Threads in Netty)**

    Das Async Java SDK verwendet [Netty](https://netty.io/) für nicht blockierende E/A-Vorgänge. Das SDK verwendet eine feste Anzahl (die Anzahl von CPU-Kernen Ihres Computers) von E/A-Eventloop-Threads in Netty zum Ausführen von E/A-Vorgängen. Das von der API zurückgegebene Observable-Objekt gibt das Ergebnis auf einem der freigegebenen E/A-Eventloop-Threads in Netty aus. Daher ist es wichtig, die freigegebenen E/A-Eventloop-Threads in Netty nicht zu blockieren. CPU-intensives Arbeiten oder das Blockieren von Vorgängen auf dem E/A-Eventloop-Thread in Netty kann einen Deadlock verursachen oder den SDK-Durchsatz deutlich reduzieren.

    Beispiel: Der folgende Code führt eine CPU-intensive Arbeit auf dem E/A-Eventloop-Thread in Netty aus:

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

    Weitere Informationen finden Sie auf der [GitHub-Seite](https://github.com/Azure/azure-cosmosdb-java) für Async Java SDK.

10. **Deaktivieren der Netty-Protokollierung** Die Netty-Bibliotheksprotokollierung führt zu übermäßiger Kommunikation und muss deaktiviert werden (das Unterdrücken der Anmeldung in der Konfiguration reicht möglicherweise nicht aus), um zusätzliche CPU-Kosten zu vermeiden. Wenn Sie sich nicht im Debuggingmodus befinden, deaktivieren Sie die Protokollierung von Netty vollständig. Wenn Sie also „log4j“ verwenden, um die zusätzlichen CPU-Kosten zu verhindern, die durch ``org.apache.log4j.Category.callAppenders()`` von Netty anfallen, fügen Sie Ihrer Codebasis die folgende Zeile hinzu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Ressourcengrenzwert des Betriebssystems für geöffnete Dateien** Einige Linux-Systeme (z.B. Red Hat) haben eine Obergrenze für die Anzahl von offenen Dateien und damit für die Gesamtzahl von Verbindungen. Führen Sie den folgenden Befehl aus, um die aktuellen Grenzwerte anzuzeigen:

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

12. **Verwenden der nativen SSL-Implementierung für Netty** Netty kann OpenSSL direkt für den SSL-Implementierungsstapel verwenden, um eine bessere Leistung zu erzielen. Wenn diese Konfiguration fehlt, greift Netty auf die standardmäßige SSL-Implementierung von Java zurück.

    unter Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    und fügen Sie Ihren Maven-Projektabhängigkeiten die folgende Abhängigkeit hinzu:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Lesen Sie für andere Plattformen (Red Hat, Windows, Mac usw.) die Anweisungen unter https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indizierungsrichtlinien
 
1. **Beschleunigen von Schreibvorgängen durch Ausschließen nicht verwendeter Pfade von der Indizierung**

    Die Indizierungsrichtlinie von Azure Cosmos DB ermöglicht auch die Verwendung von Indizierungspfaden („setIncludedPaths“ und „setExcludedPaths“), um anzugeben, welche Dokumentpfade in die Indizierung ein- bzw. von der Indizierung ausgeschlossen werden sollen. Die Verwendung von Indizierungspfaden kann in Szenarien, in denen die Abfragemuster im Voraus bekannt sind, die Leistung bei Schreibvorgängen verbessern und den Indexspeicher verringern, da die Indizierungskosten direkt damit zusammenhängen, wie viele individuelle Pfade indiziert werden. Der folgende Code zeigt beispielsweise, wie Sie mithilfe des Platzhalterzeichens „*“ einen gesamten Abschnitt von Dokumenten (eine Unterstruktur) von der Indizierung ausschließen.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Weitere Informationen finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

    Azure Cosmos DB bietet vielfältige Datenbankvorgänge (einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger), die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

    Der Durchsatz wird basierend auf der für jeden Container festgelegten Anzahl von [Anforderungseinheiten](request-units.md) bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitenrate für ihre Container überschreiten, werden begrenzt, bis die Rate wieder unter das bereitgestellte Niveau für den Container fällt. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, können Sie ihn durch Bereitstellung zusätzlicher Anforderungseinheiten erhöhen.

    Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten für einen Vorgang verbraucht werden. Die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets beeinflussen die Kosten von Abfragevorgängen.

    Untersuchen Sie zum Ermitteln des Indizierungsaufwands für einen beliebigen Vorgang (Erstellen, Aktualisieren oder Löschen) den Header [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). So ermitteln Sie die Anzahl von Anforderungseinheiten, die von diesen Vorgängen genutzt werden. Sie können sich außerdem die entsprechende RequestCharge-Eigenschaft in ResourceResponse<T> oder FeedResponse<T> ansehen.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Bei der in diesem Header zurückgegebenen Anforderungsbelastung handelt es sich um einen Bruchteil Ihres bereitgestellten Durchsatzes. Falls Sie beispielsweise 2000 RU/s bereitgestellt und die obige Abfrage 1000 Dokumente mit einer Größe von 1 KB zurückgibt, fallen für den Vorgang Kosten in Höhe von 1000 an. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md) sowie unter dem [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Behandeln von Ratenbeschränkungen/zu hohen Anforderungsraten**

    Wenn ein Client versucht, den für ein Konto reservierten Durchsatz zu überschreiten, wird die Serverleistung nicht beeinträchtigt, und es wird kein über die reservierte Kapazität hinausgehender Durchsatz in Anspruch genommen. Der Server beendet die Anforderung präemptiv mit „RequestRateTooLarge“ (HTTP-Statuscode 429) und gibt den Header [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) zurück. Darin ist die Zeitspanne (in Millisekunden) angegeben, die der Benutzer warten muss, bis ein neuer Anforderungsversuch unternommen werden kann.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Alle SDKs fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Wenn nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

    Falls mehrere Clients kumulativ und kontinuierlich die Anforderungsrate überschreiten, reicht die intern vom Client festgelegte Standardanzahl von neun Wiederholungen unter Umständen nicht aus. In diesem Fall löst der Client für die Anwendung eine DocumentClientException mit dem Statuscode 429 aus. Die standardmäßige Wiederholungsanzahl kann durch Verwendung von „setRetryOptions“ für die ConnectionPolicy-Instanz geändert werden. Die DocumentClientException mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dies gilt auch, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt.

    Das automatisierte Wiederholungsverhalten trägt zwar bei den meisten Anwendungen zur Verbesserung der Resilienz und Nutzbarkeit bei, kann bei Leistungsbenchmarks aber auch hinderlich sein (insbesondere beim Ermitteln der Latenz). Die Wartezeit für den Client nimmt stark zu, wenn das Experiment die Serverdrosselung erreicht und damit die automatische Wiederholung durch das Client-SDK auslöst. Ermitteln Sie zur Vermeidung von Latenzspitzenwerten bei Leistungsexperimenten die von den einzelnen Vorgängen zurückgegebene Belastung, und stellen Sie sicher, dass die Anforderungen die reservierte Anforderungsrate nicht überschreiten. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md).
3. **Konzipieren für kleinere Dokumente und höheren Durchsatz**

    Die Anforderungsbelastung (die Kosten für die Anforderungsverarbeitung) eines Vorgangs hängt direkt mit der Größe des Dokuments zusammen. Vorgänge für große Dokumente sind teurer als Vorgänge für kleine Dateien.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Entwerfen einer auf Skalierung und hohe Leistung ausgelegten Anwendung finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](partition-data.md).
