---
title: Leistungstipps für das Sync Java SDK v2 von Azure Cosmos DB
description: Erfahren Sie, wie Sie mit Clientkonfigurationsoptionen Verbesserungen an der Leistung von Azure Cosmos-Datenbanken für das Sync Java SDK v2 vornehmen.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6e7b01ae88645d8b16c3a43e21e40b53d242fdde
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549241"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Leistungstipps für das Sync Java SDK v2 von Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK V4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Dies ist *nicht* das neueste Java SDK für Azure Cosmos DB! Sie sollten ein Upgrade Ihres Projekts auf das [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) durchführen und dann die [Leistungstipps](performance-tips-java-sdk-v4-sql.md) für das Azure Cosmos DB Java SDK v4 lesen. Befolgen Sie für ein Upgrade die Anweisungen in den Anleitungen [Migrieren zum Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) und [Reactor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
> 
> Die folgenden Leistungstipps gelten ausschließlich für das Sync Java SDK v2 von Azure Cosmos DB. Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-java.md) zum Sync Java SDK v2 von Azure Cosmos DB und im [Maven-Repository](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).
>

Azure Cosmos DB ist eine schnelle und flexible verteilte Datenbank mit nahtloser Skalierung, garantierter Latenz und garantiertem Durchsatz. Die Skalierung Ihrer Datenbank mit Azure Cosmos DB erfordert weder aufwendige Änderungen an der Architektur noch das Schreiben von komplexem Code. Zentrales Hoch- und Herunterskalieren ist ebenso problemlos möglich wie ein einzelner API-Aufruf. Weitere Informationen finden Sie unter [Bereitstellen von Containerdurchsatz](how-to-provision-container-throughput.md) und [Bereitstellen von Datenbankdurchsatz](how-to-provision-database-throughput.md). Da der Zugriff auf Azure Cosmos DB jedoch über Netzwerkaufrufe erfolgt, können Sie bei der Verwendung des [Sync Java SDK v2 von Azure Cosmos DB](./sql-api-sdk-java.md) clientseitige Optimierungen vornehmen, um eine optimale Leistung zu erzielen.

Im Anschluss finden Sie einige Optionen zur Optimierung der Datenbankleistung:

## <a name="networking"></a>Netzwerk
<a id="direct-connection"></a>

1. **Verbindungsmodus: Verwenden von DirectHttps**

    Die Art der Verbindungsherstellung zwischen einem Client und Azure Cosmos DB hat erhebliche Auswirkungen auf die Leistung, insbesondere im Hinblick auf die clientseitige Latenz. Für die Konfiguration des Clients [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy) ist eine wichtige Konfigurationseinstellung verfügbar: [ConnectionMode](/java/api/com.microsoft.azure.documentdb.connectionmode).  Die zwei verfügbaren ConnectionModes lauten wie folgt:

   1. [Gateway (Standard)](/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](/java/api/com.microsoft.azure.documentdb.connectionmode)

      Der Gatewaymodus wird auf allen SDK-Plattformen unterstützt und ist als Standardoption konfiguriert.  Wenn Ihre Anwendung in einem Unternehmensnetzwerk mit strengen Firewalleinschränkungen ausgeführt wird, ist das Gateway die beste Wahl, da er den HTTPS-Standardport und einen einzelnen Endpunkt verwendet. Im Gatewaymodus ist jedoch jeweils ein zusätzlicher Netzwerkhop erforderlich, wenn Daten in Azure Cosmos DB geschrieben oder daraus gelesen werden, was sich negativ auf die Leistung auswirkt. Aus diesem Grund bietet der DirectHttps-Modus die bessere Leistung, da weniger Netzwerkhops erforderlich sind. 

      Das Sync Java SDK v2 von Azure Cosmos DB verwendet HTTPS als Transportprotokoll. HTTPS nutzt TLS für die erste Authentifizierung und Verschlüsselung des Datenverkehrs. Wenn Sie das Sync Java SDK v2 von Azure Cosmos DB verwenden, muss nur HTTPS-Port 443 geöffnet sein. 

      ConnectionMode wird im Zuge der Erstellung der DocumentClient-Instanz mit dem ConnectionPolicy-Parameter konfiguriert. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Sync Java SDK v2 (Maven com.microsoft.azure::azure-documentdb)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Diagramm der Azure Cosmos DB-Verbindungsrichtlinie" border="false":::

   <a id="same-region"></a>
2. **Platzieren der Clients in der gleichen Azure-Region**

    Platzieren Sie nach Möglichkeit sämtliche Anwendungen, die Azure Cosmos DB aufrufen, in der gleichen Region wie die Azure Cosmos-Datenbank. Damit Sie einen ungefähren Vergleich haben: Azure Cosmos DB-Aufrufe aus derselben Region werden normalerweise innerhalb von ca. 1 bis 2 ms abgeschlossen, während die Latenz zwischen West- und Ostküste der USA mehr als 50 ms beträgt. Diese Latenz variiert ggf. von Anforderung zu Anforderung und ist abhängig von der Route, die die Anforderung zwischen dem Client und der Grenze des Azure-Datencenters nimmt. Die geringste Latenz erzielen Sie, wenn sich die aufrufende Anwendung in der gleichen Azure-Region wie der bereitgestellte Azure Cosmos DB-Endpunkt befindet. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Diagramm der Anforderungen und Antworten in zwei Regionen, in denen Computer über Mid-Tier-Dienste eine Verbindung mit einem Cosmos DB-Konto herstellen" border="false":::
   
## <a name="sdk-usage"></a>SDK-Verwendung
1. **Installieren des neuesten SDKs**

    Azure Cosmos DB-SDKs werden ständig verbessert, um eine optimale Leistung zu ermöglichen. Informationen zum neuesten SDK und zu den Verbesserungen finden Sie auf den Seiten unter [Azure Cosmos DB SDK](./sql-api-sdk-java.md).
2. **Verwenden eines Singleton-Azure Cosmos DB-Clients für die Lebensdauer der Anwendung**

    Bei Verwendung des direkten Modus ist jede [DocumentClient](/java/api/com.microsoft.azure.documentdb.documentclient)-Instanz threadsicher und verfügt über eine effiziente Verbindungsverwaltung und Adressenzwischenspeicherung. Zur Ermöglichung einer effizienten Verbindungsverwaltung und einer besseren DocumentClient-Leistung empfiehlt es sich, für die Lebensdauer der Anwendung pro Anwendungsdomäne eine einzelne DocumentClient-Instanz zu verwenden.

   <a id="max-connection"></a>
3. **Erhöhen von MaxPoolSize pro Host im Gatewaymodus**

    Anforderungen an Azure Cosmos DB erfolgen bei Verwendung des Gatewaymodus über HTTPS/REST und unterliegen dem Standardverbindungslimit pro Hostname oder IP-Adresse. Unter Umständen müssen Sie einen höheren MaxPoolSize-Wert festlegen (200 – 1000), damit die Clientbibliothek mehrere Verbindungen mit Azure Cosmos DB gleichzeitig nutzen kann. Im Sync Java SDK v2 von Azure Cosmos DB hat [ConnectionPolicy.getMaxPoolSize](/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) den Standardwert „100“. Verwenden Sie [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize), um den Wert zu ändern.

4. **Optimieren von parallelen Abfragen für partitionierte Sammlungen**

    Ab Version 1.9.0 des Sync Java SDK von Azure Cosmos DB werden parallele Abfragen unterstützt, mit denen Sie eine partitionierte Sammlung parallel abfragen können. Weitere Informationen finden Sie in den [Codebeispielen](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) für die Arbeit mit den SDKs. Parallele Abfragen sind darauf ausgelegt, Latenz und Durchsatz im Vergleich mit seriellen Abfragen zu verbessern.

    (a) **_Optimieren von setMaxDegreeOfParallelism\:_* _ Bei parallelen Abfragen werden mehrere Partitionen parallel abgefragt. Die Daten einer individuell partitionierten Sammlung werden in Bezug auf die Abfrage aber seriell abgerufen. Legen Sie also [setMaxDegreeOfParallelism](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) auf die Anzahl von Partitionen fest, bei der die Wahrscheinlichkeit, dass die bestmögliche Leistung für die Abfrage erzielt wird, am höchsten ist (vorausgesetzt, alle anderen Systembedingungen bleiben unverändert). Falls Ihnen die Anzahl von Partitionen nicht bekannt ist, können Sie setMaxDegreeOfParallelism auf einen hohen Wert festlegen. Das System wählt für den maximalen Grad an Parallelität dann den minimalen Wert aus (Anzahl von Partitionen, Benutzereingabe). 

    Es ist wichtig zu beachten, dass sich für parallele Abfragen die größten Vorteile ergeben, wenn die Daten in Bezug auf die Abfrage gleichmäßig auf alle Partitionen verteilt werden. Wenn die partitionierte Auflistung so partitioniert ist, dass sich alle Daten bzw. die meisten Daten, die von einer Abfrage zurückgegeben werden, auf einigen wenigen Partitionen befinden (schlimmstenfalls nur auf einer Partition), können aufgrund dieser Partitionierung Engpässe bei der Leistung auftreten.

    (b) _*_Optimieren von setMaxBufferedItemCount\:_*_ Die parallele Abfrage ist so konzipiert, dass Ergebnisse vorab abgerufen werden, während der Client den aktuellen Batch mit Ergebnissen verarbeitet. Diese Art des Abrufs führt zu einer Verbesserung der Latenz einer Abfrage. setMaxBufferedItemCount begrenzt die Anzahl von vorab abgerufenen Ergebnissen. Wenn Sie [setMaxBufferedItemCount](/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) auf die erwartete Anzahl von zurückgegebenen Ergebnissen (oder eine höhere Anzahl) festlegen, ist der Vorteil durch das vorherige Abrufen für die Abfrage am größten.

    Das vorherige Abrufen funktioniert unabhängig von MaxDegreeOfParallelism, und es ist nur ein Puffer für die Daten aller Partitionen vorhanden.  

5. _ *Implementieren von Backoff in getRetryAfterInMilliseconds-Intervallen**

    Es empfiehlt sich, die Last während Leistungstests so lange erhöhen, bis eine geringe Menge von Anforderungen gedrosselt wird. Wenn es sich um eine gedrosselte Anwendung handelt, sollte die Clientanwendung diese Drosselung für das vom Server angegebene Wiederholungsintervall aussetzen. Durch das Aussetzen wird die geringstmögliche Wartezeit zwischen den Wiederholungsversuchen gewährleistet. Unterstützung für Wiederholungsrichtlinien wird ab Version 1.8.0 des [Sync Java SDK von Azure Cosmos DB](./sql-api-sdk-java.md) bereitgestellt. Weitere Informationen finden Sie unter [getRetryAfterInMilliseconds](/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Aufskalieren Ihrer Clientworkload**

    Wenn Sie auf einem hohen Durchsatzniveau testen (> 50.000 RU/s), kann sich die Clientanwendung als Engpass erweisen, da der Computer die CPU- oder Netzwerkauslastung deckelt. Wenn dieser Punkt erreicht wird, können Sie das Azure Cosmos DB-Konto weiter auslasten, indem Sie Ihre Clientanwendungen auf mehrere Server horizontal hochskalieren.

7. **Verwenden von namensbasierter Adressierung**

    Verwenden Sie anstelle von SelfLinks (`dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`self) mit dem Format \_ die namensbasierte Adressierung mit Links im Format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`, damit nicht die Ressourcen-IDs aller Ressourcen zum Erstellen der Verknüpfung abgerufen werden. Da diese Ressourcen zudem (möglicherweise mit demselben Namen) neu erstellt werden, ist ein Zwischenspeichern in diesem Fall eventuell wirkungslos.

   <a id="tune-page-size"></a>
8. **Optimieren der Seitengröße für Abfragen/Lesefeeds, um die Leistung zu verbessern**

    Wenn mehrere Dokumente mithilfe der Lesefeedfunktion (z.B. [readDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) gleichzeitig gelesen werden oder eine SQL-Abfrage ausgegeben wird, werden die Ergebnisse bei der Rückgabe segmentiert, falls das Resultset zu groß ist. Ergebnisse werden standardmäßig in Blöcken mit je 100 Elementen oder 1 MB zurückgegeben (je nachdem, welcher Grenzwert zuerst erreicht wird).

    Mithilfe des Anforderungsheaders [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) können Sie die Seitengröße auf bis zu 1000 erhöhen und so die Anzahl von Netzwerkroundtrips verringern, die zum Abrufen aller entsprechenden Ergebnisse erforderlich sind. Falls nur einige wenige Ergebnisse angezeigt werden müssen (etwa, wenn von der Benutzeroberfläche oder Anwendungs-API lediglich zehn Ergebnisse zurückgegeben werden), können Sie die Seitengröße auch auf 10 verringern, um den durch Lese- und Abfragevorgänge beanspruchten Durchsatz zu reduzieren.

    Die Seitengröße kann auch mithilfe der [setPageSize-Methode](/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize) festgelegt werden.

## <a name="indexing-policy"></a>Indizierungsrichtlinien
 
1. **Beschleunigen von Schreibvorgängen durch Ausschließen nicht verwendeter Pfade von der Indizierung**

    Die Indizierungsrichtlinie von Azure Cosmos DB ermöglicht auch die Verwendung von Indizierungspfaden ([setIncludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) und [setExcludedPaths](/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)), um anzugeben, welche Dokumentpfade in die Indizierung ein- bzw. von der Indizierung ausgeschlossen werden sollen.  Der folgenden Code zeigt beispielsweise, wie Sie einen gesamten Abschnitt der Dokumente (Unterstruktur) mit dem Platzhalter „*“ von der Indizierung ausschließen.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Sync Java SDK v2 (Maven com.microsoft.azure::azure-documentdb)

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

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

    Azure Cosmos DB bietet vielfältige Datenbankvorgänge (einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger), die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

    Der Durchsatz wird basierend auf der für jeden Container festgelegten Anzahl von [Anforderungseinheiten](request-units.md) bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitenrate für ihre Container überschreiten, werden begrenzt, bis die Rate wieder unter das bereitgestellte Niveau für den Container fällt. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, können Sie ihn durch Bereitstellung zusätzlicher Anforderungseinheiten erhöhen. 

    Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten für einen Vorgang verbraucht werden. Die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets beeinflussen die Kosten von Abfragevorgängen.

    Untersuchen Sie zum Ermitteln des Indizierungsaufwands für einen beliebigen Vorgang (Erstellen, Aktualisieren oder Löschen) den Header [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (oder die entsprechende RequestCharge-Eigenschaft in [ResourceResponse\<T>](/java/api/com.microsoft.azure.documentdb.resourceresponse) oder [FeedResponse\<T>](/java/api/com.microsoft.azure.documentdb.feedresponse)). So ermitteln Sie die Anzahl von Anforderungseinheiten, die von diesen Vorgängen genutzt werden.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Sync Java SDK v2 (Maven com.microsoft.azure::azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Bei der in diesem Header zurückgegebenen Anforderungsbelastung handelt es sich um einen Bruchteil Ihres bereitgestellten Durchsatzes. Falls Sie beispielsweise 2000 RU/s bereitgestellt und die obige Abfrage 1000 Dokumente mit einer Größe von 1 KB zurückgibt, fallen für den Vorgang Kosten in Höhe von 1000 an. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md) sowie unter dem [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Behandeln von Ratenbeschränkungen/zu hohen Anforderungsraten**

    Wenn ein Client versucht, den für ein Konto reservierten Durchsatz zu überschreiten, wird die Serverleistung nicht beeinträchtigt, und es wird kein über die reservierte Kapazität hinausgehender Durchsatz in Anspruch genommen. Der Server beendet die Anforderung präemptiv mit „RequestRateTooLarge“ (HTTP-Statuscode 429) und gibt den Header [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) zurück. Darin ist die Zeitspanne (in Millisekunden) angegeben, die der Benutzer warten muss, bis ein neuer Anforderungsversuch unternommen werden kann.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    Alle SDKs fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Wenn nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

    Falls mehrere Clients kumulativ und kontinuierlich die Anforderungsrate überschreiten, reicht die intern vom Client festgelegte Standardanzahl von neun Wiederholungen unter Umständen nicht aus. In diesem Fall löst der Client für die Anwendung eine [DocumentClientException](/java/api/com.microsoft.azure.documentdb.documentclientexception) mit dem Statuscode 429 aus. Die standardmäßige Wiederholungsanzahl kann durch Verwendung von [setRetryOptions](/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) für die [ConnectionPolicy](/java/api/com.microsoft.azure.documentdb.connectionpolicy)-Instanz geändert werden. Die DocumentClientException mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dies gilt auch, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt.

    Das automatisierte Wiederholungsverhalten trägt zwar bei den meisten Anwendungen zur Verbesserung der Resilienz und Nutzbarkeit bei, kann bei Leistungsbenchmarks aber auch hinderlich sein (insbesondere beim Ermitteln der Latenz).  Die Wartezeit für den Client nimmt stark zu, wenn das Experiment die Serverdrosselung erreicht und damit die automatische Wiederholung durch das Client-SDK auslöst. Ermitteln Sie zur Vermeidung von Latenzspitzenwerten bei Leistungsexperimenten die von den einzelnen Vorgängen zurückgegebene Belastung, und stellen Sie sicher, dass die Anforderungen die reservierte Anforderungsrate nicht überschreiten. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md).
3. **Konzipieren für kleinere Dokumente und höheren Durchsatz**

    Die Anforderungsbelastung (die Kosten für die Anforderungsverarbeitung) eines Vorgangs hängt direkt mit der Größe des Dokuments zusammen. Vorgänge für große Dokumente sind teurer als Vorgänge für kleine Dateien.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Entwerfen einer auf Skalierung und hohe Leistung ausgelegten Anwendung finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md).