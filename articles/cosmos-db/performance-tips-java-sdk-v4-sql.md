---
title: Leistungstipps für das Azure Cosmos DB Java SDK v4
description: Informieren Sie sich, wie Sie mit Clientkonfigurationsoptionen die Leistung von Azure Cosmos-Datenbanken für das Java SDK v4 verbessern.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperf-fy21q2
ms.openlocfilehash: 79f8c868b68cba1cff3e99e88e989fcc4d2a3df2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029038"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Leistungstipps für das Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Die Leistungstipps in diesem Artikel gelten ausschließlich für das Azure Cosmos DB Java SDK v4. Weitere Informationen finden Sie in den [Versionshinweisen zum Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md), im [Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-cosmos) und im [Leitfaden zur Problembehandlung für das Azure Cosmos DB Java SDK v4](troubleshoot-java-sdk-v4-sql.md). Wenn Sie aktuell eine ältere Version als v4 verwenden, lesen Sie den Leitfaden [Migrieren zum Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md), um Hilfe beim Upgrade auf v4 zu erhalten.
>

Azure Cosmos DB ist eine schnelle und flexible verteilte Datenbank mit nahtloser Skalierung, garantierter Latenz und garantiertem Durchsatz. Die Skalierung Ihrer Datenbank mit Azure Cosmos DB erfordert weder aufwendige Änderungen an der Architektur noch das Schreiben von komplexem Code. Zentrales Hoch- und Herunterskalieren ist ebenso problemlos möglich wie das Aufrufen einer einzelnen API oder SDK-Methode. Da der Zugriff auf Azure Cosmos DB jedoch über Netzwerkaufrufe erfolgt, können Sie bei der Verwendung des Azure Cosmos DB Java SDK v4 clientseitige Optimierungen vornehmen, um eine optimale Leistung zu erzielen.

Im Anschluss finden Sie einige Optionen zur Optimierung der Datenbankleistung:

## <a name="networking"></a>Netzwerk

* **Verbindungsmodus: Verwenden des direkten Modus**
<a id="direct-connection"></a>
    
    Als Standardverbindungsmodus des Java SDK wird der direkte Modus verwendet. Sie können den Verbindungsmodus im Client-Generator mithilfe der Methode *directMode()* oder *gatewayMode()* wie unten gezeigt konfigurieren. Um einen der beiden Modi mit den Standardeinstellungen zu konfigurieren, rufen Sie beide Methoden ohne Argumente auf. Andernfalls übergeben Sie eine Konfigurationseinstellungs-Klasseninstanz als Argument (*DirectConnectionConfig* für *directMode()* und *GatewayConnectionConfig* für *gatewayMode()* ). Weitere Informationen zu verschiedenen Konnektivitätsoptionen finden Sie im Artikel zu den [Konnektivitätsmodi](sql-sdk-connection-modes.md).
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK v4 (Maven com.azure::azure-cosmos): Sync-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    Die Methode *directMode()* verfügt aus dem folgenden Grund über eine zusätzliche Außerkraftsetzung: Vorgänge auf Steuerungsebene, z. B. Datenbank- und Container-CRUD, verwenden *immer* den Gatewaymodus. Wenn der Benutzer den direkten Modus für Vorgänge auf Datenebene konfiguriert hat, werden die Standardeinstellungen für den Gatewaymodus verwendet. Dies ist für die meisten Benutzer geeignet. Benutzer, die den direkten Modus für Datenebenenvorgänge verwenden und Gatewaymodusparameter anpassen möchten, können jedoch die folgenden *directMode()* -Außerkraftsetzung verwenden:

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Asynchron](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK v4 (Maven com.azure::azure-cosmos): Sync-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **Platzieren Sie Clients in derselben Azure-Region, um die Leistung zu verbessern.** <a id="same-region"></a>

    Platzieren Sie nach Möglichkeit sämtliche Anwendungen, die Azure Cosmos DB aufrufen, in der gleichen Region wie die Azure Cosmos-Datenbank. Damit Sie einen ungefähren Vergleich haben: Azure Cosmos DB-Aufrufe aus derselben Region werden normalerweise innerhalb von ca. 1 bis 2 ms abgeschlossen, während die Latenz zwischen West- und Ostküste der USA mehr als 50 ms beträgt. Diese Latenz variiert ggf. von Anforderung zu Anforderung und ist abhängig von der Route, die die Anforderung zwischen dem Client und der Grenze des Azure-Datencenters nimmt. Die geringste Latenz erzielen Sie, wenn sich die aufrufende Anwendung in der gleichen Azure-Region wie der bereitgestellte Azure Cosmos DB-Endpunkt befindet. Eine Liste mit den verfügbaren Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Abbildung der Azure Cosmos DB-Verbindungsrichtlinie" border="false":::

    Eine App, die mit einem Azure Cosmos DB-Konto in mehreren Regionen interagiert, muss [bevorzugte Regionen](tutorial-global-distribution-sql-api.md#preferred-locations) konfigurieren, um sicherzustellen, dass Anforderungen in eine angegebene Region weitergeleitet werden.

* **Aktivieren des beschleunigten Netzwerkbetriebs auf Ihrer Azure-VM für geringere Wartezeit**

Es wird empfohlen, dass Sie die Anweisungen zum Aktivieren des beschleunigten Netzwerkbetriebs für Ihre [Windows-](../virtual-network/create-vm-accelerated-networking-powershell.md) oder [Linux-VM](../virtual-network/create-vm-accelerated-networking-cli.md) befolgen (für Anweisungen jeweils klicken), um die Leistung zu maximieren.

Ohne den beschleunigten Netzwerkbetrieb können E/A-Vorgänge zwischen Ihrer Azure-VM und anderen Azure-Ressourcen unnötigerweise über einen Host und einen virtuellen Switch zwischen der VM und dessen Netzwerkkarte weitergeleitet werden. Wenn sich der Host und der virtuelle Switch inline auf dem Datenpfad befinden, sorgt dies beim Kommunikationskanal nicht nur für eine längere Wartezeit und Jitter, sondern auch die CPU-Zyklen von der VM werden reduziert. Bei beschleunigtem Netzwerkbetrieb erstellt die VM ohne Vermittler direkt Schnittstellen mit der Netzwerkkarte. Alle Netzwerkrichtliniendetails, die zuvor vom Host und dem virtuellen Switch verarbeitet wurden, werden jetzt über die Hardware der Netzwerkkarte verarbeitet, und der Host und der virtuelle Switch werden umgangen. Im Allgemeinen sind eine kürzere und *konsistentere* Wartezeit, ein höherer Durchsatz und eine geringere CPU-Auslastung zu erwarten, wenn Sie den beschleunigten Netzwerkbetrieb aktivieren.

Einschränkungen: Der beschleunigte Netzwerkbetrieb muss vom Betriebssystem der VM unterstützt werden und kann nur aktiviert werden, wenn die VM beendet und ihre Zuordnung aufgehoben wurde. Die VM kann nicht mit dem Azure Resource Manager bereitgestellt werden.

Weitere Informationen finden Sie in den Anweisungen für [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) bzw. [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>SDK-Verwendung
* **Installieren des neuesten SDKs**

    Azure Cosmos DB-SDKs werden ständig verbessert, um eine optimale Leistung zu ermöglichen. Informationen zum neuesten SDK und zu den Verbesserungen finden Sie auf den Seiten unter [Azure Cosmos DB SDK](sql-api-sdk-async-java.md).

* **Verwenden eines Singleton-Azure Cosmos DB-Clients für die Lebensdauer der Anwendung**

    Jede Azure Cosmos DB-Clientinstanz ist threadsicher und verfügt über eine effiziente Verbindungsverwaltung und Adressenzwischenspeicherung. Es empfiehlt sich, für die gesamte Lebensdauer der Anwendung pro Anwendungsdomäne nur eine einzelne Azure Cosmos DB-Clientinstanz zu verwenden, um eine effiziente Verbindungsverwaltung und bessere Leistung des Azure Cosmos DB-Clients zu ermöglichen.

   <a id="max-connection"></a>

* **Verwenden der niedrigsten für die Anwendung erforderlichen Konsistenzebene**

    Wenn Sie eine *CosmosClient*-Klasse erstellen, wird *Sitzung* als Standardkonsistenz festgelegt, falls keine andere explizite Angabe gemacht wird. Wenn die Konsistenz *Sitzung* für Ihre Anwendungslogik nicht erforderlich ist, legen Sie die *Konsistenz* auf *Letztlich* fest. Hinweis: Es wird empfohlen, mindestens *Sitzung* für die Konsistenz bei Anwendungen zu verwenden, die den Azure Cosmos DB-Änderungsfeedprozessor verwenden.

* **Verwenden der Async-API zum Ausschöpfen des bereitgestellten Durchsatzes**

    Das Azure Cosmos DB Java SDK v4 bündelt die APIs „Sync“ und „Async“. Grob gesagt implementiert die Async-API SDK-Funktionen, während die Sync-API ein einfacher Wrapper ist, der blockierende Aufrufe der Async-API durchführt. Dies steht im Kontrast zum älteren Azure Cosmos DB Async Java SDK v2, das nur die Async-API enthielt, und dem älteren Azure Cosmos DB Sync Java SDK v2, das lediglich die Sync-API enthielt und eine vollständig unterschiedliche Implementierung erforderte. 
    
    Die Auswahl der API wird während der Clientinitialisierung getroffen. Eine *CosmosAsyncClient*-Klasse unterstützt die Async-API, während die *CosmosClient*-Klasse die Sync-API unterstützt. 
    
    Die Async-API implementiert nicht blockierende E/A-Vorgänge und ist die beste Wahl, wenn Sie den Durchsatz beim Ausgeben von Anforderungen an Azure Cosmos DB voll ausschöpfen möchten. 
    
    Die Verwendung der Sync-API eignet sich, wenn Sie eine API benötigen, die die Antwort auf jede Anforderung blockiert, oder wenn im Zusammenhang mit Ihrer Anwendung hauptsächlich der synchrone Vorgang verwendet wird. Sie verwenden beispielsweise die Sync-API, wenn Sie Daten für Azure Cosmos DB in einer Microserviceanwendung beibehalten und der Durchsatz nicht entscheidend ist.  
    
    Beachten Sie, dass der Durchsatz der Sync-API mit zunehmender Anforderungsantwortzeit geringer wird, während mit der Async-API die vollständige Bandbreitenkapazität Ihrer Hardware genutzt werden kann. 
    
    Bei Verwendung der Sync-API ermöglicht eine geografische Kollokation einen höheren und konsistenteren Durchsatz (siehe [Bereitstellen von Clients in derselben Azure-Region für eine bessere Leistung](#collocate-clients)), der Durchsatz der Async-API wird jedoch nicht erreicht.

    Einige Benutzer sind möglicherweise auch nicht mit [Project Reactor](https://projectreactor.io/) vertraut. Dabei handelt es sich um das Reactive Streams-Framework, das zum Implementieren der Async-API des Azure Cosmos DB Java SDK v4 verwendet wird. Wenn dies ein Problem darstellt, empfiehlt es sich, den [Einführungsleitfaden zu Reactor-Mustern](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) durchzulesen und sich mithilfe dieser [Einführung in das reaktive Programmieren](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) damit vertraut zu machen. Wenn Sie Azure Cosmos DB bereits mit einer Async-Schnittstelle verwendet haben und es sich bei dem verwendeten SDK um das Azure Cosmos DB Async Java SDK v2 gehandelt hat, sind Sie möglicherweise mit [ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava) vertraut, sind sich aber nicht sicher, was sich bei Project Reactor geändert hat. Sehen Sie sich in diesem Fall unseren [Leitfaden mit dem Vergleich von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) an.

    Die folgenden Codeausschnitte zeigen, wie Sie Ihren Azure Cosmos DB-Client für Async- oder Sync-API-Vorgänge initialisieren:

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK v4 (Maven com.azure::azure-cosmos): Sync-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **Optimieren von ConnectionPolicy**

    Bei Verwendung des Azure Cosmos DB Java SDK v4 erfolgen Cosmos DB-Anforderungen im direkten Modus standardmäßig über TCP. Intern verwendet der direkte Modus eine spezielle Architektur, um Netzwerkressourcen dynamisch zu verwalten und die beste Leistung zu erzielen.

    Im Azure Cosmos DB Java SDK v4 ist der direkte Modus die beste Wahl, um die Datenbankleistung bei den meisten Workloads zu verbessern. 

    * ***Übersicht über den direkten Modus** _

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Darstellung der Architektur im direkten Modus" border="false":::

        Die clientseitige Architektur, die im direkten Modus eingesetzt wird, ermöglicht vorhersagbare Netzwerkauslastungen und Multiplexzugriff auf Azure Cosmos DB-Replikate. Das obige Diagramm zeigt, wie Clientanforderungen im direkten Modus an Replikate im Cosmos DB-Back-End weitergeleitet werden. Bei der Architektur für den direkten Modus werden auf Clientseite bis zu 10 *Kanäle* pro DB-Replikat zugeordnet. Ein Kanal ist eine TCP-Verbindung mit einem vorgeschalteten Anforderungspuffer, der 30 Anforderungen aufnehmen kann. Die zu einem Replikat gehörenden Kanäle werden nach Bedarf dynamisch vom **Dienstendpunkt** des Replikats zugeordnet. Wenn der Benutzer eine Anforderung im direkten Modus übermittelt, leitet der **TransportClient** die Anforderung basierend auf dem Partitionsschlüssel an den richtigen Dienstendpunkt weiter. In der **Anforderungswarteschlange** werden die Anforderungen vor dem Dienstendpunkt gepuffert.

    * ***Konfigurationsoptionen für den direkten Modus** _

        Wenn ein nicht standardmäßiges Verhalten beim direkten Modus gewünscht ist, erstellen Sie eine DirectConnectionConfig-Instanz. Passen Sie deren Eigenschaften an, und übergeben Sie die angepasste Eigenschafteninstanz dann an die Methode *directMode()* im Azure Cosmos DB-Client-Generator.

        Diese Konfigurationseinstellungen steuern das Verhalten der zugrunde liegenden Architektur des weiter oben behandelten direkten Modus.

        Verwenden Sie als ersten Schritt die folgenden empfohlenen Konfigurationseinstellungen. Diese *DirectConnectionConfig*-Optionen sind erweiterte Konfigurationseinstellungen, die sich auf unerwartete Weise auf die SDK-Leistung auswirken können. Es wird empfohlen, dass Benutzer diese nicht ändern, es sei denn, sie kennen die möglichen Auswirkungen und dies ist absolut notwendig. Wenden Sie sich an das [Azure Cosmos DB-Team](mailto:CosmosDBPerformanceSupport@service.microsoft.com), wenn Sie mit diesem speziellen Thema Probleme haben.

        | Konfigurationsoption       | Standard   |
        | :------------------:       | :-----:   |
        | idleConnectionTimeout      | „PT0“     |
        | maxConnectionsPerEndpoint  | „130“     |
        | connectTimeout             | „PT5S“    |
        | idleEndpointTimeout        | „PT1H“    |
        | maxRequestsPerConnection   | „30“      |

* **Optimieren von parallelen Abfragen für partitionierte Sammlungen**

    Das Azure Cosmos DB Java SDK v4 unterstützt parallele Abfragen, mit denen Sie eine partitionierte Sammlung parallel abfragen können. Weitere Informationen finden Sie in den [Codebeispielen](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) für die Arbeit mit dem Azure Cosmos DB Java SDK v4. Parallele Abfragen sind darauf ausgelegt, Latenz und Durchsatz im Vergleich mit seriellen Abfragen zu verbessern.

    * ***Optimieren von setMaxDegreeOfParallelism\:** _
    
        Bei parallelen Abfragen werden mehrere Partitionen parallel abgefragt. Die Daten einer individuell partitionierten Sammlung werden in Bezug auf die Abfrage aber seriell abgerufen. Legen Sie also „setMaxDegreeOfParallelism“ auf die Anzahl von Partitionen fest, bei der die Wahrscheinlichkeit, dass die bestmögliche Leistung für die Abfrage erzielt wird, am höchsten ist (vorausgesetzt, alle anderen Systembedingungen bleiben unverändert). Falls Ihnen die Anzahl von Partitionen nicht bekannt ist, können Sie setMaxDegreeOfParallelism auf einen hohen Wert festlegen. Das System wählt für den maximalen Grad an Parallelität dann den minimalen Wert aus (Anzahl von Partitionen, Benutzereingabe).

        Es ist wichtig zu beachten, dass sich für parallele Abfragen die größten Vorteile ergeben, wenn die Daten in Bezug auf die Abfrage gleichmäßig auf alle Partitionen verteilt werden. Wenn die partitionierte Auflistung so partitioniert ist, dass sich alle Daten bzw. die meisten Daten, die von einer Abfrage zurückgegeben werden, auf einigen wenigen Partitionen befinden (schlimmstenfalls nur auf einer Partition), können aufgrund dieser Partitionierung Engpässe bei der Leistung auftreten.

    **Optimieren von setMaxBufferedItemCount\:** _
    
        Parallel query is designed to pre-fetch results while the current batch of results is being processed by the client. The pre-fetching helps in overall latency improvement of a query. setMaxBufferedItemCount limits the number of pre-fetched results. Setting setMaxBufferedItemCount to the expected number of results returned (or a higher number) enables the query to receive maximum benefit from pre-fetching.

        Pre-fetching works the same way irrespective of the MaxDegreeOfParallelism, and there is a single buffer for the data from all partitions.

**Aufskalieren Ihrer Clientworkload**

    If you are testing at high throughput levels, the client application may become the bottleneck due to the machine capping out on CPU or network utilization. If you reach this point, you can continue to push the Azure Cosmos DB account further by scaling out your client applications across multiple servers.

    A good rule of thumb is not to exceed >50% CPU utilization on any given server, to keep latency low.

   <a id="tune-page-size"></a>

* **Optimieren der Seitengröße für Abfragen/Lesefeeds, um die Leistung zu verbessern**

    Wenn mehrere Dokumente mithilfe der Lesefeedfunktion (z. B. *readItems*) gleichzeitig gelesen werden oder eine SQL-Abfrage (*queryItems*) ausgegeben wird, werden die Ergebnisse bei der Rückgabe segmentiert, falls das Resultset zu groß ist. Ergebnisse werden standardmäßig in Blöcken mit je 100 Elementen oder 1 MB zurückgegeben (je nachdem, welcher Grenzwert zuerst erreicht wird).

    Angenommen, die Anwendung gibt eine Abfrage an Azure Cosmos DB aus, und die Anwendung erfordert alle Abfrageergebnisse, um die Aufgabe abzuschließen zu können. Durch Anpassen des Anforderungsheaderfelds [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) können Sie die Seitengröße erhöhen, um die Anzahl von Netzwerkroundtrips zu verringern, die zum Abrufen aller entsprechenden Ergebnisse erforderlich sind. 

    * Wenn Sie den [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)-Feldwert bei Abfragen mit nur einer Partition auf „-1“ (keine Begrenzung für Seitengröße) festlegen, wird die Wartezeit maximiert, indem die Anzahl der Abfrageantwortseiten reduziert wird: Entweder wird das vollständige Resultset auf einer einzelnen Seite zurückgegeben, oder das Resultset wird auf der möglichen Mindestanzahl von Seiten zurückgegeben, wenn die Abfrage länger als der Timeoutintervall dauert. Dadurch wird ein Vielfaches der Zeit für Roundtripanforderungen eingespart.
    
    * Bei partitionsübergreifenden Abfragen kann das Festlegen des [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)-Felds auf „-1“ und das Entfernen der Seitengrößenbeschränkung zu einer Überlastung des SDK mit nicht verwaltbaren Seitengrößen führen. In diesem Fall empfiehlt es sich, die Seitengrößenbeschränkung auf einen hohen, aber dennoch begrenzten Wert (z. B. 1.000) festzulegen, um die Wartezeit zu verringern. 
    
    In einigen Anwendungen benötigen Sie möglicherweise nicht alle Abfrageergebnisse. Falls nur einige wenige Ergebnisse angezeigt werden müssen (etwa, wenn von der Benutzeroberfläche oder Anwendungs-API gleichzeitig nur zehn Ergebnisse zurückgegeben werden), können Sie die Seitengröße auch auf „10“ verringern, um den durch Lese- und Abfragevorgänge beanspruchten Durchsatz zu reduzieren.

    Sie können auch das Argument für die bevorzugte Seitengröße der *byPage*-Methode festlegen, anstatt das REST-Headerfeld direkt zu ändern. Beachten Sie, dass mit [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) oder dem Argument der *byPage*-Methode für die bevorzugte Seitengröße nur eine Obergrenze für die Seitengröße und keine absolute Anforderung festgelegt wird. Aus mehreren Gründen gibt Azure Cosmos DB möglicherweise Seiten zurück, die kleiner als die bevorzugte Seitengröße sind. 

* **Verwenden des geeigneten Planers (Vermeiden des Diebstahls von Eventloop-E/A-Threads in Netty)**

    Die asynchrone Funktionalität des Azure Cosmos DB Java SDK basiert auf nicht blockierenden E/A-Vorgängen in [Netty](https://netty.io/). Das SDK verwendet eine feste Anzahl (die Anzahl von CPU-Kernen Ihres Computers) von E/A-Eventloop-Threads in Netty zum Ausführen von E/A-Vorgängen. Das von der API zurückgegebene Flux-Objekt gibt das Ergebnis auf einem der freigegebenen E/A-Eventloop-Threads in Netty aus. Daher ist es wichtig, die freigegebenen E/A-Eventloop-Threads in Netty nicht zu blockieren. CPU-intensives Arbeiten oder das Blockieren von Vorgängen auf dem E/A-Eventloop-Thread in Netty kann einen Deadlock verursachen oder den SDK-Durchsatz deutlich reduzieren.

    Beispiel: Der folgende Code führt eine CPU-intensive Arbeit auf dem E/A-Eventloop-Thread in Netty aus:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK v4 (Maven com.azure::azure-cosmos): Async-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    Wenn Sie nach dem Empfang des Ergebnisses CPU-intensive Arbeit am Ergebnis durchführen möchten, sollte diese nicht auf dem E/A-Eventloop-Thread in Netty erfolgen. Stattdessen können Sie einen eigenen Planer bereitstellen, um wie unten gezeigt einen eigenen Thread für die Ausführung Ihrer Arbeit zur Verfügung zu stellen (erfordert `import reactor.core.scheduler.Schedulers`).

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    Je nach Art der Arbeit müssen Sie den entsprechenden vorhandenen Reactor-Planer verwenden. Lesen Sie hier nach: [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html).

    Weitere Informationen zum Azure Cosmos DB Java SDK v4 finden Sie im [Azure Cosmos DB-Verzeichnis des Azure SDK für Java-Monorepos auf GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Optimieren der Protokollierungseinstellungen in Ihrer Anwendung**

    Aus verschiedenen Gründen kann es erforderlich sein, die Protokollierung in einem Thread hinzuzufügen, der einen hohen Anforderungsdurchsatz verursacht. Wenn das Ziel darin besteht, den bereitgestellten Durchsatz eines Containers mit den von diesem Thread generierten Anforderungen vollständig auszuschöpfen, kann die Leistung mithilfe von Protokollierungsoptimierungen erheblich verbessert werden.

    * ***Konfigurieren einer Async-Protokollierung** _

        Die Wartezeit einer synchronen Protokollierung ist notwendigerweise ein Faktor der Berechnung der Gesamtwartezeit des Threads, der Anforderungen generiert. Eine asynchrone Protokollierung wie [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) wird empfohlen, um den Protokollierungsaufwand von Hochleistungsanwendungsthreads zu entkoppeln.

    _ ***Deaktivieren der Netty-Protokollierung** _

        Netty library logging is chatty and needs to be turned off (suppressing sign in the configuration may not be enough) to avoid additional CPU costs. If you are not in debugging mode, disable netty's logging altogether. So if you are using log4j to remove the additional CPU costs incurred by ``org.apache.log4j.Category.callAppenders()`` from netty add the following line to your codebase:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 _ **Ressourcengrenzwert des Betriebssystems für geöffnete Dateien**
 
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

* **Angeben von Partitionsschlüsseln in Punktschreibvorgängen**

    Geben Sie beim API-Aufruf mit Punktschreibvorgängen wie unten gezeigt Elementpartitionsschlüssel an, um die Leistung zu verbessern:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK v4 (Maven com.azure::azure-cosmos): Sync-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    Geben Sie also nicht wie unten gezeigt nur die Elementinstanz an:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK v4 (Maven com.azure::azure-cosmos): Sync-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    Die letztere Option wird unterstützt, erhöht aber die Wartezeit Ihrer Anwendung. Das SDK muss das Element analysieren und den Partitionsschlüssel extrahieren.

## <a name="indexing-policy"></a>Indizierungsrichtlinie
 
* **Beschleunigen von Schreibvorgängen durch Ausschließen nicht verwendeter Pfade von der Indizierung**

    Die Indizierungsrichtlinie von Azure Cosmos DB ermöglicht auch die Verwendung von Indizierungspfaden („setIncludedPaths“ und „setExcludedPaths“), um anzugeben, welche Dokumentpfade in die Indizierung ein- bzw. von der Indizierung ausgeschlossen werden sollen. Der folgende Code zeigt beispielsweise, wie Sie ganze Abschnitte der Dokumente (auch als Unterstruktur bezeichnet) mit dem Platzhalter „*“ bei der Indizierung ein- und ausschließen.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    Weitere Informationen finden Sie unter [Indizierungsrichtlinien für Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

* **Messen und Optimieren (Senken) der Anzahl von Anforderungseinheiten pro Sekunde**

    Azure Cosmos DB bietet vielfältige Datenbankvorgänge (einschließlich relationaler und hierarchischer Abfragen mit UDFs, gespeicherter Prozeduren und Trigger), die alle in den Dokumenten innerhalb einer Datenbanksammlung ausgeführt werden. Die Kosten im Zusammenhang mit diesen Vorgängen variieren basierend auf dem CPU-, E/A- und Speicheraufwand, der für den jeweiligen Vorgang erforderlich ist. Anstelle sich Gedanken über Hardwareressourcen und deren Verwaltung zu machen, können Sie sich eine Anforderungseinheit (RU) als alleinige Maßeinheit für die Ressourcen vorstellen, die für das Durchführen der verschiedenen Datenbankvorgänge und das Ausführen einer Anwendungsanforderung erforderlich sind.

    Der Durchsatz wird basierend auf der für jeden Container festgelegten Anzahl von [Anforderungseinheiten](request-units.md) bereitgestellt. Der Verbrauch von Anforderungseinheiten wird als Rate pro Sekunde bemessen. Anwendungen, die die bereitgestellte Anforderungseinheitenrate für ihre Container überschreiten, werden begrenzt, bis die Rate wieder unter das bereitgestellte Niveau für den Container fällt. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, können Sie ihn durch Bereitstellung zusätzlicher Anforderungseinheiten erhöhen.

    Die Komplexität einer Abfrage wirkt sich darauf aus, wie viele Anforderungseinheiten für einen Vorgang verbraucht werden. Die Anzahl von Prädikaten, die Art der Prädikate, die Anzahl von UDFs und die Größe des Quelldatasets beeinflussen die Kosten von Abfragevorgängen.

    Untersuchen Sie zum Ermitteln des Indizierungsaufwands für einen beliebigen Vorgang (Erstellen, Aktualisieren oder Löschen) den Header [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers). So ermitteln Sie die Anzahl von Anforderungseinheiten, die von diesen Vorgängen genutzt werden. Sie können sich außerdem die entsprechende RequestCharge-Eigenschaft in ResourceResponse\<T> oder FeedResponse\<T> ansehen.

    # <a name="async"></a>[Asynchron](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK v4 (Maven com.azure::azure-cosmos): Sync-API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    Bei der in diesem Header zurückgegebenen Anforderungsbelastung handelt es sich um einen Bruchteil Ihres bereitgestellten Durchsatzes. Falls Sie beispielsweise 2000 RU/s bereitgestellt und die obige Abfrage 1000 Dokumente mit einer Größe von 1 KB zurückgibt, fallen für den Vorgang Kosten in Höhe von 1000 an. Somit werden vom Server innerhalb einer Sekunde nur zwei solcher Anforderungen berücksichtigt, und für weitere Anforderungen wird die Rate begrenzt. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md) sowie unter dem [Rechner für Anforderungseinheiten](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Behandeln von Ratenbeschränkungen/zu hohen Anforderungsraten**

    Wenn ein Client versucht, den für ein Konto reservierten Durchsatz zu überschreiten, wird die Serverleistung nicht beeinträchtigt, und es wird kein über die reservierte Kapazität hinausgehender Durchsatz in Anspruch genommen. Der Server beendet die Anforderung präemptiv mit „RequestRateTooLarge“ (HTTP-Statuscode 429) und gibt den Header [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) zurück. Darin ist die Zeitspanne (in Millisekunden) angegeben, die der Benutzer warten muss, bis ein neuer Anforderungsversuch unternommen werden kann.

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    Alle SDKs fangen diese Antwort implizit ab, berücksichtigen den vom Server angegebenen Header vom Typ „retry-after“ und wiederholen die Anforderung. Wenn nicht mehrere Clients gleichzeitig auf Ihr Konto zugreifen, wird die nächste Wiederholung erfolgreich ausgeführt.

    Falls mehrere Clients kumulativ und kontinuierlich die Anforderungsrate überschreiten, reicht die intern vom Client festgelegte Standardanzahl von neun Wiederholungen unter Umständen nicht aus. In diesem Fall löst der Client für die Anwendung eine *CosmosClientException*-Klasse mit dem Statuscode 429 aus. Die standardmäßige Wiederholungsanzahl kann durch Verwendung von „setRetryOptions“ für die ConnectionPolicy-Instanz geändert werden. Die *CosmosClientException*-Klasse mit dem Statuscode 429 wird standardmäßig nach einer kumulierten Wartezeit von 30 Sekunden zurückgegeben, wenn die Anforderung weiterhin die Anforderungsrate übersteigt. Dies gilt auch, wenn die aktuelle Wiederholungsanzahl unter der maximalen Wiederholungsanzahl liegt – ganz gleich, ob es sich dabei um den Standardwert (9) oder um einen benutzerdefinierten Wert handelt.

    Das automatisierte Wiederholungsverhalten trägt zwar bei den meisten Anwendungen zur Verbesserung der Resilienz und Nutzbarkeit bei, kann bei Leistungsbenchmarks aber auch hinderlich sein (insbesondere beim Ermitteln der Latenz). Die Wartezeit für den Client nimmt stark zu, wenn das Experiment die Serverdrosselung erreicht und damit die automatische Wiederholung durch das Client-SDK auslöst. Ermitteln Sie zur Vermeidung von Latenzspitzenwerten bei Leistungsexperimenten die von den einzelnen Vorgängen zurückgegebene Belastung, und stellen Sie sicher, dass die Anforderungen die reservierte Anforderungsrate nicht überschreiten. Weitere Informationen finden Sie unter [Anforderungseinheiten in DocumentDB](request-units.md).

* **Konzipieren für kleinere Dokumente und höheren Durchsatz**

    Die Anforderungsbelastung (die Kosten für die Anforderungsverarbeitung) eines Vorgangs hängt direkt mit der Größe des Dokuments zusammen. Vorgänge für große Dokumente sind teurer als Vorgänge für kleine Dateien. Im Idealfall sollten Sie Ihre Anwendung und Workflows so entwerfen, dass die Größe der Elemente etwa ein Kilobyte beträgt oder diese eine ähnliche Größe aufweisen. Bei Anwendungen, die hinsichtlich der Wartezeit empfindlich sind, sollten große Elemente vermieden werden. Dokumente mit mehreren Megabyte verlangsamen Ihre Anwendung.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwerfen einer auf Skalierung und hohe Leistung ausgelegten Anwendung finden Sie unter [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md).
