---
title: Leistungstipps für Azure Cosmos DB und .NET
description: Machen Sie sich mit Clientkonfigurationsoptionen zur Verbesserung der Leistung von Azure Cosmos DB-Datenbanken vertraut.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.openlocfilehash: c8907f1b1c8069a3a3e92d01a5fa6341c06ec952
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688805"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Leistungstipps für Azure Cosmos DB und .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB ist eine schnelle und flexible verteilte Datenbank mit nahtloser Skalierung, garantierter Latenz und garantiertem Durchsatz. Die Skalierung Ihrer Datenbank mit Azure Cosmos DB erfordert weder aufwendige Änderungen an der Architektur noch das Schreiben von komplexem Code. Zentrales Hoch- und Herunterskalieren ist ebenso problemlos möglich wie ein einzelner API-Aufruf. Weitere Informationen finden Sie unter [Bereitstellen von Containerdurchsatz](how-to-provision-container-throughput.md) und [Bereitstellen von Datenbankdurchsatz](how-to-provision-database-throughput.md). Weil der Zugriff auf Azure Cosmos DB jedoch über Netzwerkaufrufe erfolgt, können Sie clientseitige Optimierungen vornehmen, um bei Verwendung des [SQL .NET SDK](documentdb-sdk-dotnet.md) eine optimale Leistung zu erzielen.

Im Anschluss finden Sie einige Optionen zur Optimierung der Datenbankleistung:

## <a name="networking"></a>Netzwerk
<a id="direct-connection"></a>

1. **Verbindungsrichtlinie: Verwenden des direkten Verbindungsmodus**

    Die Art der Verbindungsherstellung zwischen einem Client und Azure Cosmos DB hat erhebliche Auswirkungen auf die Leistung, insbesondere im Hinblick auf die clientseitige Latenz. Für die Konfiguration der Verbindungsrichtlinie stehen zwei wichtige Konfigurationseinstellungen zur Verfügung: der Verbindungs*modus* und das Verbindungs*protokoll*.  Folgende Modi sind verfügbar:

   * Gatewaymodus (Standard)
      
     Der Gatewaymodus wird auf allen SDK-Plattformen unterstützt und ist als Standardoption konfiguriert. Wenn Ihre Anwendung in einem Unternehmensnetzwerk mit strengen Firewalleinschränkungen ausgeführt wird, ist der Gatewaymodus die beste Wahl, da er den HTTPS-Standardport und einen einzelnen Endpunkt verwendet. Im Gatewaymodus ist jedoch jeweils ein zusätzlicher Netzwerkhop erforderlich, wenn Daten in Azure Cosmos DB geschrieben oder daraus gelesen werden, was sich negativ auf die Leistung auswirkt. Aus diesem Grund bietet der direkte Modus die bessere Leistung, da weniger Netzwerkhops erforderlich sind. Der Gatewayverbindungsmodus wird auch empfohlen, wenn Sie Anwendungen in Umgebungen mit einer begrenzten Anzahl von Socketverbindungen ausführen, z.B. bei Verwendung von Azure Functions oder wenn für Sie ein Verbrauchstarif gilt. 

   * Direkter Modus

     Der direkte Modus unterstützt die Verbindung über TCP- und HTTPS-Protokolle. Wenn Sie die neueste Version des .NET SDK verwenden, wird der direkte Verbindungsmodus in .NET Standard 2.0 und .NET Framework unterstützt. Bei Verwendung des direkten Modus stehen zwei Protokolloptionen zur Verfügung:

     * TCP
     * HTTPS

     Im Gatewaysmodus verwendet Cosmos DB Port 443 und die Ports 10250, 10255 und 10256, wenn Sie die API for MongoDB von Azure Cosmos DB verwenden. Der Port 10250 wird einer MongoDB-Standardinstanz ohne Georeplikation zugeordnet, und die Ports 10255/10256 werden der MongoDB-Instanz mit Georeplikationsfunktionen zugeordnet. Wenn Sie TCP im direkten Modus verwenden, müssen Sie zusätzlich zu den Gatewayports sicherstellen, dass der Portbereich zwischen 10000 und 20000 offen ist, da Azure Cosmos DB dynamische TCP-Ports verwendet. Wenn diese Ports nicht geöffnet sind und Sie versuchen, TCP zu verwenden, wird der Fehler „503 – Dienst nicht verfügbar“ angezeigt. Die folgende Tabelle zeigt die für andere APIs verfügbaren Konnektivitätsmodi und die Benutzer der Dienstports für jede API an:

     |Verbindungsmodus  |Unterstütztes Protokoll  |Unterstützte SDKs  |API/Dienstport  |
     |---------|---------|---------|---------|
     |Gateway  |   HTTPS    |  Alle SDKS    |   SQL(443), Mongo(10250, 10255, 10256), Table(443), Cassandra(10350), Graph(443)    |
     |Direkt    |    HTTPS     |  .NET- und Java-SDK    |   Ports im Bereich 10.000-20.000    |
     |Direkt    |     TCP    |  .NET SDK    | Ports im Bereich 10.000-20.000 |

     Azure Cosmos DB bietet ein einfaches und offenes RESTful-Programmiermodell über HTTPS. Darüber hinaus ist ein effizientes TCP-Protokoll vorhanden, das ebenfalls über ein RESTful-Kommunikationsmodell verfügt und über das .NET-Client-SDK verfügbar ist. Sowohl Direct TCP als auch HTTPS nutzen SSL für die erste Authentifizierung und Verschlüsselung des Datenverkehrs. Die beste Leistung erzielen Sie mit dem TCP-Protokoll.

     Der Verbindungsmodus wird im Zuge der Erstellung der DocumentClient-Instanz mit dem ConnectionPolicy-Parameter konfiguriert. Bei Verwendung des direkten Modus kann das Protokoll auch innerhalb des ConnectionPolicy-Parameters festgelegt werden.

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     Da TCP nur im direkten Modus unterstützt wird, wird bei Verwendung des Gatewaymodus immer das HTTPS-Protokoll für die Kommunikation mit dem Gateway verwendet, und der Protokollwert des ConnectionPolicy-Parameters wird ignoriert.

     ![Abbildung der Azure Cosmos DB-Verbindungsrichtlinie](./media/performance-tips/connection-policy.png)

2. **Aufrufen von OpenAsync, um die Startlatenz bei der ersten Anforderung zu vermeiden**

    Aufgrund des erforderlichen Abrufs der Adressroutingtabelle tritt bei der ersten Anforderung standardmäßig eine höhere Latenz auf. Um diese Startlatenz bei der ersten Anforderung zu vermeiden, empfiehlt es sich, während der Initialisierung einmal „OpenAsync()“ aufzurufen:

        await client.OpenAsync();
   <a id="same-region"></a>
3. **Platzieren der Clients in der gleichen Azure-Region**

    Platzieren Sie nach Möglichkeit sämtliche Anwendungen, die Azure Cosmos DB aufrufen, in der gleichen Region wie die Azure Cosmos DB-Datenbank. Damit Sie einen ungefähren Vergleich haben: Azure Cosmos DB-Aufrufe aus derselben Region werden normalerweise innerhalb von ca. 1 bis 2 ms abgeschlossen, während die Latenz zwischen West- und Ostküste der USA mehr als 50 ms beträgt. Diese Latenz variiert ggf. von Anforderung zu Anforderung und ist abhängig von der Route, die die Anforderung zwischen dem Client und der Grenze des Azure-Datencenters nimmt. Die geringste Latenz erzielen Sie, wenn sich die aufrufende Anwendung in der gleichen Azure-Region wie der bereitgestellte Azure Cosmos DB-Endpunkt befindet. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/#services).

    ![Abbildung der Azure Cosmos DB-Verbindungsrichtlinie](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **Erhöhen der Anzahl von Threads/Aufgaben**

    Da Azure Cosmos DB-Aufrufe über das Netzwerk abgewickelt werden, müssen Sie ggf. den Parallelitätsgrad Ihrer Anforderungen variieren, um die Wartezeit für die Clientanwendung zwischen Anforderungen auf ein Minimum zu reduzieren. Erstellen Sie beispielsweise bei Verwendung der [Task Parallel Library](https://msdn.microsoft.com//library/dd460717.aspx) von .NET mehrere hundert Aufgaben für Lese- und Schreibvorgänge in Azure Cosmos DB.

5. **Aktivieren des beschleunigten Netzwerkbetriebs**

   Um Latenzzeiten und CPU-Jitter zu reduzieren, empfehlen wir, dass auf die virtuellen Clientcomputern der beschleunigte Netzwerkbetrieb aktiviert ist. Informationen zum Aktivieren des beschleunigten Netzwerkbetrieb finden Sie in den Artikeln [Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-powershell.md) oder [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md).


## <a name="sdk-usage"></a>SDK-Verwendung
1. **Installieren des neuesten SDKs**

    Azure Cosmos DB-SDKs werden ständig verbessert, um eine optimale Leistung zu ermöglichen. Informationen zum neuesten SDK und zu den Verbesserungen finden Sie auf den Seiten unter [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md).
2. **Verwenden eines Singleton-Azure Cosmos DB-Clients für die Lebensdauer der Anwendung**

    Bei Verwendung des direkten Modus ist jede DocumentClient-Instanz threadsicher und verfügt über eine effiziente Verbindungsverwaltung und Adressenzwischenspeicherung. Zur Ermöglichung einer effizienten Verbindungsverwaltung und einer besseren DocumentClient-Leistung empfiehlt es sich, für die Lebensdauer der Anwendung pro Anwendungsdomäne eine einzelne DocumentClient-Instanz zu verwenden.

   <a id="max-connection"></a>
3. **Erhöhen von „System.Net MaxConnections“ pro Host bei Verwendung des Gatewaymodus**

    Anforderungen an Azure Cosmos DB erfolgen bei Verwendung des Gatewaymodus über HTTPS/REST und unterliegen dem Standardverbindungslimit pro Hostname oder IP-Adresse. Unter Umständen müssen Sie einen höheren „MaxConnections“-Wert festlegen (100 bis 1.000), damit die Clientbibliothek mehrere Verbindungen mit Azure Cosmos DB gleichzeitig nutzen kann. Ab .NET SDK 1.8.0 ist der Standardwert für [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) auf 50 festgelegt. Zur Erhöhung dieses Werts können Sie [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) auf einen höheren Wert festlegen.   
4. **Optimieren von parallelen Abfragen für partitionierte Sammlungen**

     Ab Version 1.9.0 des SQL .NET SDK werden parallele Abfragen unterstützt, mit denen Sie eine partitionierte Sammlung parallel abfragen können. Weitere Informationen finden Sie in den [Codebeispielen](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) für die Arbeit mit den SDKs. Parallele Abfragen sind darauf ausgelegt, Latenz und Durchsatz im Vergleich mit seriellen Abfragen zu verbessern. Parallele Abfragen verfügen über zwei Parameter, die Benutzer optimieren können, um sie an ihre Anforderungen anzupassen: (a) MaxDegreeOfParallelism zum Steuern der maximalen Anzahl von Partitionen, die parallel abgefragt werden können, und (b) MaxBufferedItemCount zum Steuern der Anzahl von vorab abgerufenen Ergebnissen.

    (a) ***Optimieren von MaxDegreeOfParallelism\:*** Bei parallelen Abfragen werden mehrere Partitionen parallel abgefragt. Die Daten einer individuell partitionierten Sammlung werden in Bezug auf die Abfrage aber seriell abgerufen. Wenn Sie MaxDegreeOfParallelism also auf die Anzahl von Partitionen festlegen, ist die Wahrscheinlichkeit am höchsten, dass die bestmögliche Leistung für die Abfrage erzielt wird (vorausgesetzt, alle anderen Systembedingungen bleiben unverändert). Falls Ihnen die Anzahl von Partitionen nicht bekannt ist, können Sie MaxDegreeOfParallelism auf einen hohen Wert festlegen. Das System wählt für MaxDegreeOfParallelism dann den minimalen Wert aus (Anzahl von Partitionen, Benutzereingabe).

    Es ist wichtig zu beachten, dass sich für parallele Abfragen die größten Vorteile ergeben, wenn die Daten in Bezug auf die Abfrage gleichmäßig auf alle Partitionen verteilt werden. Wenn die partitionierte Auflistung so partitioniert ist, dass sich alle Daten bzw. die meisten Daten, die von einer Abfrage zurückgegeben werden, auf einigen wenigen Partitionen befinden (schlimmstenfalls nur auf einer Partition), können aufgrund dieser Partitionierung Engpässe bei der Leistung auftreten.

    (b) ***Optimieren von MaxBufferedItemCount\:*** Die parallele Abfrage ist so konzipiert, dass Ergebnisse vorab abgerufen werden, während der Client den aktuellen Batch mit Ergebnissen verarbeitet. Diese Art des Abrufs führt zu einer Verbesserung der Latenz einer Abfrage. MaxBufferedItemCount ist der Parameter zum Begrenzen der Anzahl von vorab abgerufenen Ergebnissen. Wenn Sie MaxBufferedItemCount auf die erwartete Anzahl von zurückgegebenen Ergebnissen (oder eine höhere Anzahl) festlegen, ist der Vorteil durch das vorherige Abrufen für die Abfrage am größten.

    Das vorherige Abrufen funktioniert unabhängig von MaxDegreeOfParallelism, und es ist nur ein Puffer für die Daten aller Partitionen vorhanden.  
5. **Aktivieren der serverseitigen Garbage Collection**

    In einigen Fällen kann es hilfreich sein, die Häufigkeit zu verringern, mit der die Garbage Collection ausgeführt wird. Legen Sie [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) in .NET auf „True“ fest.
6. **Implementieren eines Backoffs in RetryAfter-Intervallen**

    Es empfiehlt sich, die Last während Leistungstests so lange erhöhen, bis eine geringe Menge von Anforderungen gedrosselt wird. Wenn es sich um eine gedrosselte Anwendung handelt, sollte die Clientanwendung diese Drosselung für das vom Server angegebene Wiederholungsintervall aussetzen. Durch das Aussetzen wird die geringstmögliche Wartezeit zwischen den Wiederholungsversuchen gewährleistet. Wiederholungsrichtlinien werden ab Version 1.8.0 von SQL ([.NET](sql-api-sdk-dotnet.md) und [Java](sql-api-sdk-java.md)) bzw. ab Version 1.9.0 ([Node.js](sql-api-sdk-node.md) und [Python](sql-api-sdk-python.md)) und in allen unterstützten Versionen der [.NET Core](sql-api-sdk-dotnet-core.md)-SDKs unterstützt. Weitere Informationen finden Sie unter [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    .NET SDK ab Version 1.19 verfügt über einen Mechanismus zum Protokollieren zusätzlicher Diagnoseinformationen und zum Beheben von Problemen mit der Latenz, wie im folgenden Beispiel gezeigt. Sie können die Diagnosezeichenfolge für Anforderungen protokollieren, die eine höhere Leselatenz aufweisen. Mithilfe der erfassten Diagnosezeichenfolge können Sie ermitteln, wie häufig bei einer bestimmten Anforderung der Fehler 429 aufgetreten ist.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Horizontales Hochskalieren Ihrer Clientworkload**

    Wenn Sie auf einem hohen Durchsatzniveau testen (> 50.000 RU/s), kann sich die Clientanwendung als Engpass erweisen, da der Computer die CPU- oder Netzwerknutzung deckelt. Wenn dieser Punkt erreicht wird, können Sie das Azure Cosmos DB-Konto weiter auslasten, indem Sie Ihre Clientanwendungen auf mehrere Server horizontal hochskalieren.
8. **Zwischenspeichern von Dokument-URIs zur Verringerung der Latenz bei Lesevorgängen**

    Zur Erzielung einer optimalen Leseleistung sollten Dokument-URIs möglichst immer zwischengespeichert werden. Sie müssen die Logik definieren, um resourceid beim Erstellen der Ressource zwischenzuspeichern. Resourceid-basierte Suchen sind schneller als namensbasierte Suchen, sodass durch die Zwischenspeicherung dieser Werte die Leistung verbessert wird. 

   <a id="tune-page-size"></a>
1. **Optimieren der Seitengröße für Abfragen/Lesefeeds, um die Leistung zu verbessern**

   Wenn mehrere Dokumente mithilfe der Lesefeedfunktion (z. B. „ReadDocumentFeedAsync“) gleichzeitig gelesen werden oder eine SQL-Abfrage ausgegeben wird, werden die Ergebnisse bei der Rückgabe segmentiert, falls das Resultset zu groß ist. Ergebnisse werden standardmäßig in Blöcken mit je 100 Elementen oder 1 MB zurückgegeben (je nachdem, welcher Grenzwert zuerst erreicht wird).

   Mithilfe des Anforderungsheaders [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) können Sie die Seitengröße auf bis zu 1000 erhöhen und so die Anzahl von Netzwerkroundtrips verringern, die zum Abrufen aller entsprechenden Ergebnisse erforderlich sind. Falls nur einige wenige Ergebnisse angezeigt werden müssen (etwa, wenn von der Benutzeroberfläche oder Anwendungs-API lediglich zehn Ergebnisse zurückgegeben werden), können Sie die Seitengröße auch auf 10 verringern, um den durch Lese- und Abfragevorgänge beanspruchten Durchsatz zu reduzieren.

   > [!NOTE] 
   > Die „maxItemCount“-Eigenschaft sollte nicht nur für die Paginierung verwendet werden. Ihr Hauptverwendungszweck ist die Verbesserung der Leistung von Abfragen durch Reduzieren der maximalen Anzahl von Elementen, die auf einer einzigen Seite zurückgegeben werden.  

   Sie können die Seitengröße auch mithilfe der verfügbaren Azure Cosmos DB-SDKs festlegen. Mit der [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet)-Eigenschaft in „FeedOptions“ können Sie die maximale Anzahl von Elementen festlegen, die beim Enumerationsvorgang zurückgegeben werden. Wenn `maxItemCount` auf „-1“ festgelegt ist, ermittelt das SDK automatisch den optimalen Wert abhängig von der Größe des Dokuments. Beispiel:
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   Beim Ausführen einer Abfrage werden die resultierenden Daten in einem TCP-Paket gesendet. Wenn Sie einen zu niedrigen Wert für `maxItemCount` angeben, ist die zum Senden der Daten im TCP-Paket erforderliche Anzahl der Roundtrips hoch, was sich auf die Leistung auswirkt. Wenn Sie nicht sicher sind, welchen Wert Sie für die `maxItemCount`-Eigenschaft festlegen sollen, empfiehlt es sich, den Wert auf „-1“ festzulegen und den Standardwert vom SDK auswählen zu lassen. 

10. **Erhöhen der Anzahl von Threads/Aufgaben**

    Siehe [Erhöhen der Anzahl von Threads/Aufgaben](#increase-threads) im Abschnitt „Netzwerk“.

11. **Verwenden der 64-Bit-Hostverarbeitung.**

    Das SQL SDK funktioniert in einem 32-Bit-Hostprozess, wenn Sie SQL .NET SDK Version 1.11.4 und höher verwenden. Wenn Sie jedoch partitionsübergreifende Abfragen verwenden, wird die 64-Bit-Hostverarbeitung empfohlen, um eine bessere Leistung zu erzielen. Bei den folgenden Anwendungstypen ist der 32-Bit-Hostprozess der Standardprozess. Führen Sie je nach Typ Ihrer Anwendung die folgenden Schritte aus, um diesen zu 64-Bit zu ändern:

    - Bei ausführbaren Anwendungen deaktivieren Sie dazu auf der Registerkarte **Build** im Fenster **Projekteigenschaften** die Option **32-Bit bevorzugen**.

    - Bei VSTest-basierten Testprojekten ist dies möglich, indem Sie in der Menüoption **Visual Studio Test** die Optionen **Test**->**Testeinstellungen**->**Default Processor Architecture as X64** (Standardprozessorarchitektur als X64) auswählen.

    - Bei lokal bereitgestellten ASP.NET-Webanwendungen ist dies möglich, indem Sie unter **Extras**->**Optionen**->**Projekte und Projektmappen**->**Webprojekte** die Option **64-Bit-Version von IIS Express für Websites und Projekte verwenden** aktivieren.

    - Für auf Azure bereitgestellte ASP.NET-Webanwendungen wählen Sie im Azure-Portal unter **Anwendungseinstellungen** die Option **Platform as 64-bit** (Plattform als 64-Bit) aus.

## <a name="indexing-policy"></a>Indizierungsrichtlinien
 
1. **Beschleunigen von Schreibvorgängen durch Ausschließen nicht verwendeter Pfade von der Indizierung**

    Die Indizierungsrichtlinie von Cosmos DB ermöglicht auch die Verwendung von Indizierungspfaden („IndexingPolicy.IncludedPaths“ und „IndexingPolicy.ExcludedPaths“), um anzugeben, welche Dokumentpfade in die Indizierung ein- bzw. von der Indizierung ausgeschlossen werden sollen.  Der folgenden Code zeigt beispielsweise, wie Sie einen gesamten Abschnitt der Dokumente (eine Unterstruktur) mit dem Platzhalter „*“ von der Indizierung ausschließen.

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Weitere Informationen finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

    Azure Cosmos DB bietet vielfältige Datenbankvorgänge (einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger), die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

    Der Durchsatz wird basierend auf der für jeden Container festgelegten Anzahl von [Anforderungseinheiten](request-units.md) bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitenrate für ihre Container überschreiten, werden begrenzt, bis die Rate wieder unter das bereitgestellte Niveau für den Container fällt. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, können Sie ihn durch Bereitstellung zusätzlicher Anforderungseinheiten erhöhen. 

    Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten für einen Vorgang verbraucht werden. Die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets beeinflussen die Kosten von Abfragevorgängen.

    Untersuchen Sie zum Ermitteln des Aufwands für einen beliebigen Vorgang (Erstellen, Aktualisieren oder Löschen) den Header [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (oder die entsprechende „RequestCharge“-Eigenschaft in „ResourceResponse<T>“ oder „FeedResponse<T>“ im .NET SDK), um die Anzahl von Anforderungseinheiten zu ermitteln, die von diesen Vorgängen genutzt werden.

    ```csharp
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    Bei der in diesem Header zurückgegebenen Anforderungsbelastung handelt es sich um einen Bruchteil Ihres bereitgestellten Durchsatzes (2.000 RUs/Sekunde). Falls die obige Abfrage also beispielsweise 1.000 Dokumente mit einer Größe von 1 KB zurückgibt, fallen für den Vorgang Kosten in Höhe von 1.000 an. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md) sowie unter dem [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Behandeln von Ratenbeschränkungen/zu hohen Anforderungsraten**

    Wenn ein Client versucht, den für ein Konto reservierten Durchsatz zu überschreiten, wird die Serverleistung nicht beeinträchtigt, und es wird kein über die reservierte Kapazität hinausgehender Durchsatz in Anspruch genommen. Der Server beendet die Anforderung präemptiv mit „RequestRateTooLarge“ (HTTP-Statuscode 429) und gibt den Header [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) zurück. Darin ist die Zeitspanne (in Millisekunden) angegeben, die der Benutzer warten muss, bis ein neuer Anforderungsversuch unternommen werden kann.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Alle SDKs fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Wenn nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

    Falls mehrere Clients kumulativ und kontinuierlich die Anforderungsrate überschreiten, reicht die intern vom Client festgelegte Standardanzahl von neun Wiederholungen unter Umständen nicht aus. In diesem Fall löst der Client für die Anwendung eine DocumentClientException mit dem Statuscode 429 aus. Die standardmäßige Wiederholungsanzahl kann durch Festlegen der Wiederholungsoptionen (RetryOptions) für die ConnectionPolicy-Instanz geändert werden. Die DocumentClientException mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dies gilt auch, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt.

    Das automatisierte Wiederholungsverhalten trägt zwar bei den meisten Anwendungen zur Verbesserung der Resilienz und Nutzbarkeit bei, kann bei Leistungsbenchmarks aber auch hinderlich sein (insbesondere beim Ermitteln der Latenz).  Die Wartezeit für den Client nimmt stark zu, wenn das Experiment die Serverdrosselung erreicht und damit die automatische Wiederholung durch das Client-SDK auslöst. Ermitteln Sie zur Vermeidung von Latenzspitzenwerten bei Leistungsexperimenten die von den einzelnen Vorgängen zurückgegebene Belastung, und stellen Sie sicher, dass die Anforderungen die reservierte Anforderungsrate nicht überschreiten. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md).
3. **Konzipieren für kleinere Dokumente und höheren Durchsatz**

    Die Anforderungsbelastung (also die Kosten für die Anforderungsverarbeitung) eines Vorgangs hängt direkt mit der Größe des Dokuments zusammen. Vorgänge für große Dokumente sind teurer als Vorgänge für kleine Dateien.

## <a name="next-steps"></a>Nächste Schritte
Eine Beispielanwendung zur Evaluierung von Azure Cosmos DB für Hochleistungsszenarien auf einigen Clientcomputern finden Sie unter [Leistungs- und Skalierungstests mit Azure Cosmos DB](performance-testing.md).

Weitere Informationen zur Gestaltung einer auf Skalierung und hohe Leistung ausgelegten Anwendung finden Sie unter [Partitionieren und Skalieren von Daten in Cosmos DB](partition-data.md).
