---
title: Leistungstipps für Azure Cosmos DB und .NET SDK v2
description: Informationen zu den Clientkonfigurationsoptionen zur Verbesserung der Leistung von Azure Cosmos DB und NET SDK v2.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 47e20e89c8eaef59b9acd6cf7e31244afd4bcf60
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359046"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Leistungstipps für Azure Cosmos DB und .NET SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB ist eine schnelle und flexible verteilte Datenbank mit nahtloser Skalierung, garantierter Latenz und garantiertem Durchsatz. Die Skalierung Ihrer Datenbank mit Azure Cosmos DB erfordert weder aufwendige Änderungen an der Architektur noch das Schreiben von komplexem Code. Zentrales Hoch- und Herunterskalieren ist ebenso problemlos möglich wie ein einzelner API-Aufruf. Weitere Informationen finden Sie unter [Bereitstellen von Containerdurchsatz](how-to-provision-container-throughput.md) und [Bereitstellen von Datenbankdurchsatz](how-to-provision-database-throughput.md). Da der Zugriff auf Azure Cosmos DB jedoch über Netzwerkaufrufe erfolgt, können Sie clientseitige Optimierungen vornehmen, um bei Verwendung des [SQL .NET SDK](sql-api-sdk-dotnet-standard.md) eine optimale Leistung zu erzielen.

Wenn Sie versuchen möchten, die Datenbankleistung zu verbessern, sollten Sie die folgenden Optionen beachten:

## <a name="upgrade-to-the-net-v3-sdk"></a>Upgrade auf das .NET v3 SDK

Das [.NET v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) wurde veröffentlicht. Wenn Sie das .NET v3 SDK verwenden, finden Sie im [Leitfaden zur Leistung von .NET v3](performance-tips-dotnet-sdk-v3-sql.md) die folgenden Informationen:

- Standardwerte für den direkten TCP-Modus
- Stream-API-Unterstützung
- Unterstützung eines benutzerdefinierten Serialisierungsmoduls zur Verwendung von System.Text.JSON
- Integrierte Unterstützung von Batch- und Massenvorgängen

## <a name="hosting-recommendations"></a>Hostingempfehlungen

**Verwenden Sie für abfrageintensive Workloads die Windows-64-Bit- anstelle der Linux- oder Windows-32-Bit-Hostverarbeitung.**

Für eine bessere Leistung wird die Windows-64-Bit-Hostverarbeitung empfohlen. Das SQL SDK enthält die native Datei „ServiceInterop.dll“, um Abfragen lokal zu analysieren und zu optimieren. „ServiceInterop.dll“ wird nur auf der Windows x64-Plattform unterstützt. Bei Linux und anderen nicht unterstützten Plattformen, bei denen die Datei „ServiceInterop.dll“ nicht verfügbar ist, erfolgt ein zusätzlicher Netzwerkaufruf an das Gateway, um die optimierte Abfrage zu erhalten. Bei den folgenden Anwendungstypen wird standardmäßig die 32-Bit-Hostverarbeitung verwendet. Um die Hostverarbeitung auf die 64-Bit-Verarbeitung umzustellen, führen Sie je nach Typ Ihrer Anwendung die folgenden Schritte aus:

- Bei ausführbaren Anwendungen legen Sie zum Ändern der Hostverarbeitung im Fenster **Projekteigenschaften** auf der Registerkarte **Build** die Option [Zielplattform](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) auf **x64** fest.

- Bei VSTest-basierten Testprojekten können Sie die Hostverarbeitung ändern, indem Sie in der Menüoption **Visual Studio Test** die Option **Test** > **Testeinstellungen** > **Default Processor Architecture as X64** (Standardprozessorarchitektur als x64) auswählen.

- Bei lokal bereitgestellten ASP.NET-Webanwendungen ändern Sie die Hostverarbeitung, indem Sie unter **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webprojekte** die Option **64-Bit-Version von IIS Express für Websites und Projekte verwenden** aktivieren.

- Für ASP.NET-Webanwendungen, die in Azure bereitgestellt werden, können Sie die Hostverarbeitung ändern, indem Sie im Azure-Portal unter **Anwendungseinstellungen** die **64-Bit**-Plattform auswählen.

> [!NOTE] 
> Standardmäßig ist bei neuen Visual Studio-Projekten **Beliebige CPU** festgelegt. Es wird empfohlen, Ihr Projekt auf **x64** festzulegen, damit es nicht zu **x86** wechselt. Ein Projekt mit der Einstellung **Beliebige CPU** kann schnell zu **x86** wechseln, wenn eine Abhängigkeit hinzugefügt wird, die nur bei x86 verfügbar ist.<br/>
> Die Datei „ServiceInterop.dll“ muss sich im selben Ordner befinden, aus dem die SDK-DLL ausgeführt wird. Dies sollte nur dann von Bedeutung sein, wenn Sie DLLs manuell kopieren oder über benutzerdefinierte Build-/Bereitstellungssysteme verfügen.
    
**Aktivieren der serverseitigen Garbage Collection (GC)**

In einigen Fällen kann es hilfreich sein, die Häufigkeit zu verringern, mit der die Garbage Collection ausgeführt wird. Legen Sie [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) in .NET auf `true` fest.

**Erweitern Ihrer Clientworkload**

Wenn Sie auf einem hohen Durchsatzniveau testen (> 50.000 RU/s), kann sich die Clientanwendung als Engpass erweisen, da der Computer die CPU- oder Netzwerkauslastung deckelt. Wenn dieser Punkt erreicht wird, können Sie das Azure Cosmos DB-Konto weiter auslasten, indem Sie Ihre Clientanwendungen auf mehrere Server horizontal hochskalieren.

> [!NOTE] 
> Eine hohe CPU-Auslastung kann zu erhöhter Latenz und Ausnahmen des Typs „Anforderungstimeout“ führen.

## <a name="networking"></a><a id="networking"></a> Netzwerk

**Verbindungsrichtlinie: Verwenden des direkten Verbindungsmodus**

Als Standardverbindungsmodus des .NET V3 SDK wird der Gatewaymodus verwendet. Sie konfigurieren den Verbindungsmodus während der Erstellung der `DocumentClient`-Instanz mithilfe des Parameters `ConnectionPolicy`. Wenn Sie den direkten Modus verwenden, müssen Sie `Protocol` ebenfalls mithilfe des Parameters `ConnectionPolicy` festlegen. Weitere Informationen zu verschiedenen Konnektivitätsoptionen finden Sie im Artikel zu den [Konnektivitätsmodi](sql-sdk-connection-modes.md).

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Kurzfristige Portauslastung**

Wenn Sie ein hohes Verbindungsaufkommen oder eine hohe Portauslastung Ihrer Instanzen feststellen, überprüfen Sie zunächst, ob die Clientinstanzen Singletons sind. Anders ausgedrückt: Die Clientinstanzen müssen für die Lebensdauer der Anwendung eindeutig sein.

Bei der Ausführung über das TCP-Protokoll optimiert der Client die Latenz mithilfe langlebiger Verbindungen. Im Gegensatz dazu werden beim HTTPS-Protokoll Verbindungen nach 2 Minuten Inaktivität getrennt.

Wenn Sie in Szenarien mit geringem Zugriff eine im Vergleich zum Zugriff im Gatewaymodus höhere Anzahl von Verbindungen feststellen, haben Sie folgende Möglichkeiten:

* Legen Sie die Eigenschaft [ConnectionPolicy.PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) auf `PrivatePortPool` fest (gültig mit Frameworkversionen ab 4.6.1 und .NET Core-Versionen ab 2.0): Über diese Eigenschaft kann das SDK einen kleinen Pool von kurzlebigen Ports für verschiedene Azure Cosmos DB-Zielendpunkte verwenden.
* Konfigurieren Sie die Eigenschaft [ConnectionPolicy.IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) auf mindestens 10 Minuten. Die empfohlenen Werte liegen zwischen 20 Minuten und 24 Stunden.

**Aufrufen von OpenAsync, um die Startlatenz bei der ersten Anforderung zu vermeiden**

Aufgrund des erforderlichen Abrufs der Adressroutingtabelle tritt bei der ersten Anforderung standardmäßig eine höhere Latenz auf. Um diese Startlatenz bei der ersten Anforderung zu vermeiden, rufen Sie bei Verwendung des [SDK V2](sql-api-sdk-dotnet.md) während der Initialisierung einmal `OpenAsync()` auf. Der Aufruf sieht wie folgt aus: `await client.OpenAsync();`.

> [!NOTE]
> Mit `OpenAsync` werden Anforderungen zum Abrufen der Adressroutingtabelle für alle Container im Konto generiert. Für Konten, die über viele Container verfügen, deren Anwendung jedoch auf eine Teilmenge der Container zugreift, würde `OpenAsync` eine unnötige Menge an Datenverkehr generieren, durch die die Initialisierung verlangsamt wird. Daher ist die Verwendung von `OpenAsync` in diesem Szenario möglicherweise nicht sinnvoll, da damit der Anwendungsstart verlangsamt wird.

**Platzieren von Clients in derselben Azure-Region, um die Leistung zu verbessern**

Platzieren Sie nach Möglichkeit sämtliche Anwendungen, die Azure Cosmos DB aufrufen, in der gleichen Region wie die Azure Cosmos DB-Datenbank. Damit Sie einen ungefähren Vergleich haben: Azure Cosmos DB-Aufrufe aus derselben Region werden normalerweise innerhalb von ca. 1 bis 2 ms abgeschlossen, während die Latenz zwischen West- und Ostküste der USA mehr als 50 ms beträgt. Diese Latenz variiert ggf. von Anforderung zu Anforderung und ist abhängig von der Route, die die Anforderung zwischen dem Client und der Grenze des Azure-Rechenzentrums nimmt. Die geringste Latenz erzielen Sie, wenn sich die aufrufende Anwendung in der gleichen Azure-Region wie der bereitgestellte Azure Cosmos DB-Endpunkt befindet. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB-Verbindungsrichtlinie" border="false":::

**Erhöhen der Anzahl von Threads/Aufgaben**
<a id="increase-threads"></a>

Da Azure Cosmos DB-Aufrufe über das Netzwerk erfolgen, müssen Sie eventuell den Parallelitätsgrad Ihrer Anforderungen variieren, um die Wartezeit für die Clientanwendung zwischen Anforderungen auf ein Minimum zu reduzieren. Erstellen Sie beispielsweise bei Verwendung der [Task Parallel Library](/dotnet/standard/parallel-programming/task-parallel-library-tpl) von .NET mehrere Hundert Aufgaben für Lese- und Schreibvorgänge in Azure Cosmos DB.

**Aktivieren des beschleunigten Netzwerkbetriebs**
 
Um Latenzen und CPU-Jitter zu reduzieren, wird empfohlen, auf den virtuellen Clientcomputern den beschleunigten Netzwerkbetrieb zu aktivieren. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-powershell.md) oder [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-Verwendung

**Installieren des neuesten SDKs**

Azure Cosmos DB-SDKs werden ständig verbessert, um eine optimale Leistung zu ermöglichen. Informationen zum neuesten SDK und zu den Verbesserungen finden Sie auf den Seiten unter [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md).

**Verwenden eines Singleton-Azure Cosmos DB-Clients für die Lebensdauer der Anwendung**

Bei Verwendung des direkten Modus ist jede Instanz von `DocumentClient` threadsicher und verfügt über eine effiziente Verbindungsverwaltung und Adressenzwischenspeicherung. Zur Ermöglichung einer effizienten Verbindungsverwaltung und einer besseren Leistung des SDK-Clients empfiehlt es sich, für die Lebensdauer der Anwendung pro `AppDomain` eine einzelne Instanz zu verwenden.

**Erhöhen von System.Net MaxConnections pro Host bei Verwendung des Gatewaymodus**

Azure Cosmos DB-Anforderungen erfolgen im Gatewaymodus über HTTPS/REST. Sie unterliegen dem Standardverbindungslimit pro Hostname oder IP-Adresse. Unter Umständen müssen Sie einen höheren `MaxConnections`-Wert festlegen (100 – 1.000), damit die Clientbibliothek mehrere Verbindungen mit Azure Cosmos DB gleichzeitig nutzen kann. In .NET SDK 1.8.0 und höher ist der Standardwert für [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) „50“. Um den Wert zu ändern, können Sie [Documents.Client.ConnectionPolicy.MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) auf einen höheren Wert festlegen.

**Optimieren von parallelen Abfragen für partitionierte Sammlungen**

Ab Version 1.9.0 des SQL .NET SDK werden parallele Abfragen unterstützt, mit denen Sie eine partitionierte Sammlung parallel abfragen können. Weitere Informationen finden Sie in den [Codebeispielen](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs) für die Arbeit mit den SDKs. Parallele Abfragen sind darauf ausgelegt, Latenz und Durchsatz im Vergleich zu seriellen Abfragen zu verbessern. Parallele Abfragen bieten zwei Parameter, die Sie an Ihre Anforderungen anpassen können: 
- `MaxDegreeOfParallelism` steuert die maximale Anzahl von Partitionen, die parallel abgefragt werden können. 
- `MaxBufferedItemCount` steuert die Anzahl der vorab abgerufenen Ergebnisse.

**_Optimieren des Grads der Parallelität_* _

Bei parallelen Abfragen werden mehrere Partitionen gleichzeitig abgefragt. Die Daten einer individuellen Partition werden in Bezug auf die Abfrage aber seriell abgerufen. Wenn Sie `MaxDegreeOfParallelism` in [SDK V2](sql-api-sdk-dotnet.md) auf die Anzahl von Partitionen festlegen, ist die Wahrscheinlichkeit am höchsten, dass die bestmögliche Leistung für die Abfrage erzielt wird (vorausgesetzt, alle anderen Systembedingungen bleiben unverändert). Wenn Sie die Anzahl der Partitionen nicht kennen, können Sie den Grad der Parallelität auf eine hohe Zahl festlegen. Das System wählt den Mindestwert (Anzahl der Partitionen, vom Benutzer bereitgestellte Eingabe) als Grad der Parallelität aus.

Für parallele Abfragen ergeben sich die größten Vorteile, wenn die Daten in Bezug auf die Abfrage gleichmäßig auf alle Partitionen verteilt sind. Wenn die partitionierte Sammlung so partitioniert ist, dass sich alle Daten bzw. die meisten Daten, die von einer Abfrage zurückgegeben werden, auf einigen wenigen Partitionen befinden (schlimmstenfalls nur auf einer Partition), können diese Partitionen Leistungsengpässe verursachen.

_*_Optimieren von MaxBufferedItemCount_*_
    
Parallele Abfragen sind so konzipiert, dass Ergebnisse vorab abgerufen werden, während der Client den aktuellen Batch der Ergebnisse verarbeitet. Dieser Vorababruf führt zu einer Verbesserung der allgemeinen Latenz einer Abfrage. Der Parameter `MaxBufferedItemCount` begrenzt die Anzahl von vorab abgerufenen Ergebnissen. Wenn Sie `MaxBufferedItemCount` auf die erwartete Anzahl von zurückgegebenen Ergebnissen (oder eine höhere Zahl) festlegen, ist der Vorteil durch das vorherige Abrufen für die Abfrage am größten.

Das vorherige Abrufen funktioniert unabhängig vom Parallelitätsgrad, und es ist nur ein Puffer für die Daten aller Partitionen vorhanden.  

_ *Implementieren eines Backoffs in RetryAfter-Intervallen**

Es empfiehlt sich, die Last während Leistungstests so lange erhöhen, bis eine geringe Menge von Anforderungen gedrosselt wird. Wenn die Anforderungen gedrosselt werden, sollte die Clientanwendung diese Drosselung für das vom Server angegebene Wiederholungsintervall aussetzen. Durch das Aussetzen wird die geringstmögliche Wartezeit zwischen den Wiederholungsversuchen gewährleistet. 

Die folgenden SDKs bieten Unterstützung für Wiederholungsrichtlinien:
- Version 1.8.0 und höher des [.NET SDK für SQL](sql-api-sdk-dotnet.md) und des [Java SDK für SQL](sql-api-sdk-java.md)
- Version 1.9.0 und höher des [Node.js SDK für SQL](sql-api-sdk-node.md) und des [Python SDK für SQL](sql-api-sdk-python.md)
- Alle unterstützten Versionen der [.NET Core SDKs](sql-api-sdk-dotnet-core.md) 

Weitere Informationen finden Sie unter [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter).
    
Das .NET SDK ab Version 1.19 verfügt über einen Mechanismus zum Protokollieren zusätzlicher Diagnoseinformationen und zum Beheben von Problemen mit der Latenz, wie im folgenden Beispiel gezeigt. Sie können die Diagnosezeichenfolge für Anforderungen protokollieren, die eine höhere Leselatenz aufweisen. Mithilfe der erfassten Diagnosezeichenfolge können Sie ermitteln, wie häufig bei einer bestimmten Anforderung der Fehler 429 aufgetreten ist.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Zwischenspeichern von Dokument-URIs zur Verringerung der Latenz bei Lesevorgängen**

Zur Erzielung einer optimalen Leseleistung sollten Dokument-URIs möglichst immer zwischengespeichert werden. Sie müssen die Logik definieren, um die Ressourcen-ID beim Erstellen einer Ressource zwischenzuspeichern. Suchvorgänge auf Basis von Ressourcen-IDs sind schneller als namensbasierte, sodass durch ein Zwischenspeichern dieser Werte die Leistung verbessert wird.

**Optimieren der Seitengröße für Abfragen/Lesefeeds, um die Leistung zu verbessern**

Wenn mehrere Dokumente mithilfe der Lesefeedfunktion (z. B. `ReadDocumentFeedAsync`) gleichzeitig gelesen werden oder eine SQL-Abfrage ausgegeben wird, werden die Ergebnisse bei der Rückgabe segmentiert, falls das Resultset zu groß ist. Ergebnisse werden standardmäßig in Blöcken mit je 100 Elementen oder 1 MB zurückgegeben (je nachdem, welcher Grenzwert zuerst erreicht wird).

Mithilfe von [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) können Sie die Seitengröße erhöhen, um bis zu 1.000 Header anzufordern, und so die Anzahl von Netzwerkroundtrips verringern, die zum Abrufen aller entsprechenden Ergebnisse erforderlich sind. Falls nur einige wenige Ergebnisse angezeigt werden müssen (etwa, wenn von der Benutzeroberfläche oder Anwendungs-API lediglich zehn Ergebnisse zurückgegeben werden), können Sie die Seitengröße auch auf 10 verringern, um den durch Lese- und Abfragevorgänge beanspruchten Durchsatz zu reduzieren.

> [!NOTE] 
> Die `maxItemCount`-Eigenschaft sollte nicht nur für die Paginierung verwendet werden. Ihr Hauptverwendungszweck ist die Verbesserung der Leistung von Abfragen durch Reduzierung der maximalen Anzahl von Elementen, die auf einer einzigen Seite zurückgegeben werden.  

Sie können die Seitengröße auch mithilfe der verfügbaren Azure Cosmos DB-SDKs festlegen. Mit der [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true)-Eigenschaft in `FeedOptions` können Sie die maximale Anzahl von Elementen festlegen, die beim Enumerationsvorgang zurückgegeben werden. Wenn `maxItemCount` auf „-1“ festgelegt ist, ermittelt das SDK automatisch den optimalen Wert abhängig von der Größe des Dokuments. Beispiel:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Beim Ausführen einer Abfrage werden die resultierenden Daten in einem TCP-Paket gesendet. Wenn Sie einen zu niedrigen Wert für `maxItemCount` angeben, ist die zum Senden der Daten im TCP-Paket erforderliche Anzahl der Roundtrips hoch, was sich auf die Leistung auswirkt. Wenn Sie nicht sicher sind, welchen Wert Sie für die `maxItemCount`-Eigenschaft festlegen sollen, empfiehlt es sich, den Wert auf „-1“ festzulegen und den Standardwert vom SDK auswählen zu lassen.

**Erhöhen der Anzahl von Threads/Aufgaben**

Siehe [Erhöhen der Anzahl von Threads/Aufgaben](#increase-threads) im Abschnitt „Netzwerk“ dieses Artikels.

## <a name="indexing-policy"></a>Indizierungsrichtlinie
 
**Beschleunigen von Schreibvorgängen durch Ausschließen nicht verwendeter Pfade von der Indizierung**

Die Indizierungsrichtlinie von Azure Cosmos DB ermöglicht auch die Verwendung von Indizierungspfaden („IndexingPolicy.IncludedPaths“ und „IndexingPolicy.ExcludedPaths“), um anzugeben, welche Dokumentpfade in die Indizierung ein- bzw. ausgeschlossen werden sollen. Indizierungspfade können bessere Schreibleistungen und geringeren Indexspeicherbedarf für Szenarien bieten, in denen die Abfragemuster im Voraus bekannt sind. Dies liegt daran, dass der Indizierungsaufwand direkt mit der Anzahl der indizierten eindeutigen Pfade zusammenhängt. Der folgende Code zeigt beispielsweise, wie Sie einen vollständigen Abschnitt der Dokumente (eine Unterstruktur) mit dem Platzhalter „*“ von der Indizierung ausschließen:

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

Weitere Informationen finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a><a id="measure-rus"></a> Durchsatz

**Messen und Optimieren zum Senken der erforderlichen Anforderungseinheiten pro Sekunde**

Azure Cosmos DB bietet einen umfangreichen Satz von Datenbankvorgängen. Diese Vorgänge umfassen relationale und hierarchische Abfragen mit UDFs, gespeicherte Prozeduren und Trigger, die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren in Abhängigkeit vom CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

Der Durchsatz wird basierend auf der für jeden Container festgelegten Anzahl von [Anforderungseinheiten](request-units.md) bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Rate von Anforderungseinheiten für ihre Container überschreiten, werden begrenzt, bis die Rate wieder unter das bereitgestellte Niveau für den Container fällt. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, können Sie ihn durch Bereitstellung zusätzlicher Anforderungseinheiten erhöhen.

Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten für einen Vorgang verbraucht werden. Die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets beeinflussen die Kosten von Abfragevorgängen.

Untersuchen Sie zum Ermitteln des Aufwands für einen Vorgang (Erstellen, Aktualisieren oder Löschen) den Header [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (oder die entsprechende `RequestCharge`-Eigenschaft in `ResourceResponse\<T>` oder `FeedResponse\<T>` im .NET SDK), um die Anzahl von Anforderungseinheiten zu ermitteln, die von diesen Vorgängen verbraucht werden:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

Bei den in diesem Header zurückgegebenen Anforderungskosten handelt es sich um einen Bruchteil Ihres bereitgestellten Durchsatzes (2.000 RU/s). Falls die obige Abfrage also beispielsweise 1.000 Dokumente mit einer Größe von 1 KB zurückgibt, fallen für den Vorgang Kosten in Höhe von 1.000 an. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie unter [Anforderungseinheiten](request-units.md) sowie unter dem [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Behandeln von Ratenbeschränkungen/zu hohen Anforderungsraten**

Wenn ein Client versucht, den für ein Konto reservierten Durchsatz zu überschreiten, wird die Serverleistung nicht beeinträchtigt, und es wird kein über die reservierte Kapazität hinausgehender Durchsatz in Anspruch genommen. Der Server beendet die Anforderung vorab mit RequestRateTooLarge (HTTP-Statuscode 429). Er gibt einen [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)-Header zurück, der die Zeitspanne in Millisekunden angibt, die der Benutzer warten muss, bevor er die Anforderung erneut versuchen kann.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

Alle SDKs fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Wenn nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

Wenn insgesamt mehrere Clients beständig die Anforderungsrate überschreiten, reicht die standardmäßige Wiederholungsanzahl (vom Client intern auf 9 festgelegt) möglicherweise nicht aus. In diesem Fall löst der Client eine DocumentClientException mit dem Statuscode 429 für die Anwendung aus. 

Sie können die Standardanzahl von Wiederholungsversuchen ändern, indem Sie die `RetryOptions` für die `ConnectionPolicy`-Instanz festlegen. Die DocumentClientException mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dieser Fehler wird auch dann zurückgegeben, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt.

Das automatisierte Wiederholungsverhalten trägt dazu bei, die Resilienz und Benutzerfreundlichkeit für die meisten Anwendungen zu verbessern. Dies stellt jedoch möglicherweise nicht das beste Verhalten dar, wenn Sie Leistungsbenchmarks durchführen, und insbesondere nicht beim Messen der Latenz. Die Wartezeit für den Client nimmt stark zu, wenn das Experiment die Serverdrosselung erreicht und damit die automatische Wiederholung durch das Client-SDK auslöst. Ermitteln Sie zur Vermeidung von Latenzspitzenwerten bei Leistungsexperimenten die von den einzelnen Vorgängen zurückgegebene Belastung, und stellen Sie sicher, dass die Anforderungen die reservierte Anforderungsrate nicht überschreiten. Weitere Informationen finden Sie unter [Anforderungseinheiten](request-units.md).

**Verwenden eines auf kleinere Dokumente ausgerichteten Designs für einen höheren Durchsatz**

Der Anforderungsaufwand (also die Kosten für die Anforderungsverarbeitung) eines Vorgangs hängt direkt mit der Größe des Dokuments zusammen. Vorgänge für große Dokumente sind teurer als Vorgänge für kleine Dokumente.

## <a name="next-steps"></a>Nächste Schritte

Eine Beispielanwendung zur Evaluierung von Azure Cosmos DB für Hochleistungsszenarien auf einigen Clientcomputern finden Sie unter [Leistungs- und Skalierungstests mit Azure Cosmos DB](performance-testing.md).

Weitere Informationen zum Entwerfen einer auf Skalierung und hohe Leistung ausgelegten Anwendung finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md).