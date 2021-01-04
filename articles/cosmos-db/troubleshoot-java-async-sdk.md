---
title: Diagnose und Problembehandlung beim Azure Cosmos DB Async Java SDK v2
description: Verwenden Sie Features wie clientseitige Protokollierung und andere Tools von Drittanbietern, um Azure Cosmos DB-Probleme in Async Java SDK v2 zu erkennen, zu diagnostizieren und zu beheben.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: b39a74bd06f942cf21d201c8cef48bc6dfc57d46
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548085"
---
# <a name="troubleshoot-issues-when-you-use-the-azure-cosmos-db-async-java-sdk-v2-with-sql-api-accounts"></a>Behandeln von Problemen bei der Verwendung des Azure Cosmos DB Async Java SDK v2 mit SQL-API-Konten
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Dies ist *nicht* das neueste Java SDK für Azure Cosmos DB! Sie sollten ein Upgrade Ihres Projekts auf das [Azure Cosmos DB Java SDKv4](sql-api-sdk-java-v4.md) durchführen und dann den [Leitfaden zur Problembehandlung](troubleshoot-java-sdk-v4-sql.md) für das Azure Cosmos DB Java SDK v4 lesen. Befolgen Sie für ein Upgrade die Anweisungen in den Anleitungen [Migrieren zum Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) und [Gegenüberstellung von Reactor und RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md). 
>
> In diesem Artikel wird nur die Problembehandlung für das Azure Cosmos DB Async Java SDK v2 behandelt. Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-async-java.md) zum Azure Cosmos DB Async Java SDK v2, im [Maven-Repository](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) und in den [Leistungstipps](performance-tips-async-java.md).
>

Dieser Artikel behandelt allgemeine Probleme, Problemumgehungen, Diagnoseschritte und Tools bei der Verwendung des [Java Async SDK](sql-api-sdk-async-java.md) mit Azure Cosmos DB-SQL-API-Konten.
Das Java Async SDK bietet eine clientseitige logische Darstellung für den Zugriff auf die Azure Cosmos DB-SQL-API. Dieser Artikel beschreibt hilfreiche Tools und Vorgehensweisen für Problemfälle.

Beginnen Sie mit dieser Liste:

* Sehen Sie sich den Abschnitt [Häufig auftretende Probleme und Problemumgehungen] in diesem Artikel an.
* Sehen Sie sich das SDK an (verfügbar als [Open-Source-Lösung auf GitHub](https://github.com/Azure/azure-cosmosdb-java)). Es verfügt über einen aktiv überwachten [Problemabschnitt](https://github.com/Azure/azure-cosmosdb-java/issues). Überprüfen Sie, ob Sie ein ähnliches Problem finden, für das bereits eine Problemumgehung dokumentiert wurde.
* Überprüfen Sie die [Leistungstipps](performance-tips-async-java.md), und implementieren Sie die Empfehlungen.
* Lesen Sie den Rest dieses Artikels, falls Sie keine Lösung gefunden haben. Reichen Sie anschließend ein [GitHub-Problem](https://github.com/Azure/azure-cosmosdb-java/issues) ein.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Häufig auftretende Probleme und Problemumgehungen

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Netzwerkprobleme, Netty-Lesetimeoutfehler, niedriger Durchsatz, hohe Latenz

#### <a name="general-suggestions"></a>Allgemeine Empfehlungen
* Vergewissern Sie sich, dass die App in der gleichen Region ausgeführt wird, in der sich auch Ihr Azure Cosmos DB-Konto befindet. 
* Überprüfen Sie die CPU-Auslastung auf dem Host, auf dem die App ausgeführt wird. Wenn die CPU-Auslastung 90 Prozent oder mehr beträgt, führen Sie Ihre App auf einem Host mit einer höheren Konfiguration aus. Alternativ können Sie die Last auf mehrere Computer verteilen.

#### <a name="connection-throttling"></a>Verbindungsdrosselung
Eine Verbindungsdrosselung kann entweder aufgrund eines [Verbindungslimit auf einem Hostcomputer] oder aufgrund von [Azure SNAT-Portauslastung (PAT)] auftreten.

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Verbindungslimit auf einem Hostcomputer
Bei einigen Linux-Systemen (beispielsweise Red Hat) gilt eine Obergrenze für die Gesamtzahl geöffneter Dateien. Da Sockets in Linux als Dateien implementiert werden, schränkt dies auch die Gesamtanzahl von Verbindungen ein.
Führen Sie den folgenden Befehl aus.

```bash
ulimit -a
```
Die maximal zulässige Anzahl geöffneter Dateien (nofile) muss mindestens doppelt so hoch sein wie die Größe Ihres Verbindungspools. Weitere Informationen finden Sie unter [Leistungstipps für Azure Cosmos DB und Async Java](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT-Portauslastung (PAT)

Wenn Ihre App auf einem virtuellen Azure-Computer ohne öffentliche IP-Adresse bereitgestellt wird, werden standardmäßig [Azure SNAT-Ports](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) verwendet, um Verbindungen mit beliebigen Endpunkten außerhalb Ihres virtuellen Computers herzustellen. Die Anzahl zulässiger Verbindungen des virtuellen Computers mit dem Azure Cosmos DB-Endpunkt wird durch die [Azure SNAT-Konfiguration](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) eingeschränkt.

 Azure SNAT-Ports werden nur verwendet, wenn Ihr virtueller Computer eine private IP-Adresse besitzt und ein Prozess auf dem virtuellen Computer versucht, eine Verbindung mit einer öffentlichen IP-Adresse herzustellen. Es gibt zwei Problemumgehungen, um die Einschränkung durch Azure SNAT zu vermeiden:

* Fügen Sie Ihren Azure Cosmos DB-Dienstendpunkt dem Subnetz Ihres virtuellen Netzwerks von Azure Virtual Machines hinzu. Weitere Informationen finden Sie unter [Azure Virtual Network-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Wenn der Dienstendpunkt aktiviert ist, werden die Anforderungen nicht mehr von einer öffentlichen IP-Adresse an Azure Cosmos DB gesendet. Stattdessen wird die Identität des virtuellen Netzwerks und des Subnetzes gesendet. Diese Änderung kann zu Firewallproblemen führen, wenn nur öffentliche IP-Adressen zulässig sind. Wenn Sie eine Firewall verwenden und den Dienstendpunkt aktivieren, fügen Sie der Firewall mithilfe von [VNET-ACLs](/previous-versions/azure/virtual-network/virtual-networks-acl) ein Subnetz hinzu.
* Weisen Sie Ihrem virtuellen Azure-Computer eine öffentliche IP-Adresse zu.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Dienst nicht erreichbar – Firewall
``ConnectTimeoutException`` gibt an, dass das SDK den Dienst nicht erreichen kann.
Im direkten Modus erhalten Sie möglicherweise einen Fehler ähnlich dem folgenden:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Wenn Sie auf dem Computer mit der App eine Firewall ausführen, öffnen Sie den Portbereich 10.000 bis 20.000, der im direkten Modus verwendet wird.
Halten Sie auch das [Verbindungslimit auf einem Hostcomputer](#connection-limit-on-host) ein.

#### <a name="http-proxy"></a>HTTP-Proxy

Wenn Sie einen HTTP-Proxy verwenden, vergewissern Sie sich, dass er die Anzahl von Verbindungen unterstützt, die in `ConnectionPolicy` des SDK konfiguriert ist.
Andernfalls treten Verbindungsprobleme auf.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ungültiges Codierungsmuster: Blockieren eines Netty E/A-Threads

Das SDK verwendet für die Kommunikation mit Azure Cosmos DB die [Netty](https://netty.io/)-E/A-Bibliothek. Das SDK verfügt über asynchrone APIs und verwendet nicht blockierende E/A-APIs von Netty. Die E/A-Aufgaben des SDK werden in Netty-E/A-Threads ausgeführt. Die Anzahl von Netty-E/A-Threads ist so konfiguriert, dass sie mit der Anzahl von CPU-Kernen des App-Computers übereinstimmt. 

Die Netty-E/A-Threads sind nur für nicht blockierende Netty-E/A-Aufgaben vorgesehen. Das SDK gibt das API-Aufrufergebnis für einen der Netty-E/A-Threads an den Code der App zurück. Wenn die App nach dem Empfang von Ergebnissen im Netty-Thread einen länger dauernden Vorgang ausführt, stehen dem SDK möglicherweise nicht genügend E/A-Threads für interne E/A-Aufgaben zur Verfügung. Eine solche App-Programmierung kann zu niedrigem Durchsatz, hoher Wartezeit und zu `io.netty.handler.timeout.ReadTimeoutException`-Fehlern führen. Wechseln Sie zur Problemumgehung den Thread, wenn Sie wissen, dass der Vorgang länger dauert.

Sehen Sie sich beispielsweise den folgenden Codeausschnitt an. Möglicherweise wird im Netty-Thread eine Aufgabe ausgeführt, die mehr als nur ein paar Millisekunden dauert. In diesem Fall kann es vorkommen, dass für die Verarbeitung von E/A-Aufgaben kein Netty-E/A-Thread mehr vorhanden ist. Dies führt dann zu einem ReadTimeoutException-Fehler.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-readtimeout"></a>Async Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
Ändern Sie zur Problemumgehung den Thread für die Ausführung zeitaufwendiger Aufgaben. Definieren Sie eine Singletoninstanz des Schedulers für Ihre App.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
```
Unter Umständen müssen zeitaufwendige Aufgaben (etwa rechenintensive Aufgaben oder blockierende E/A-Vorgänge) ausgeführt werden. Verlagern Sie den Thread in diesem Fall mithilfe der API `.observeOn(customScheduler)` auf einen durch `customScheduler` bereitgestellten Worker.

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-applycustomscheduler"></a>Async Java SDK v2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Durch die Verwendung von `observeOn(customScheduler)` geben Sie den Netty-E/A-Thread frei und wechseln zu Ihrem eigenen benutzerdefinierten Thread, der vom benutzerdefinierten Scheduler bereitgestellt wird. Diese Änderung löst das Problem. Es treten keine Fehler vom Typ `io.netty.handler.timeout.ReadTimeoutException` mehr auf.

### <a name="connection-pool-exhausted-issue"></a>Problem „Verbindungspool erschöpft“

`PoolExhaustedException` ist ein clientseitiger Fehler. Dieser Fehler deutet darauf hin, dass Ihre App-Workload die Kapazitäten des SDK-Verbindungspools übersteigt. Erhöhen Sie die Größe des Verbindungspools, oder verteilen Sie die Last auf mehrere Apps.

### <a name="request-rate-too-large"></a>Anforderungsrate zu groß
Hierbei handelt es sich um einen serverseitigen Fehler. Er gibt an, dass Sie den bereitgestellten Durchsatz verbraucht haben. Wiederholen Sie den Vorgang zu einem späteren Zeitpunkt. Sollte dieser Fehler häufiger auftreten, empfiehlt sich unter Umständen eine Erhöhung des Sammlungsdurchsatzes.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Fehler beim Herstellen einer Verbindung mit dem Azure Cosmos DB-Emulator

Das HTTPS-Zertifikat des Azure Cosmos DB-Emulators ist selbstsigniert. Importieren Sie das Emulatorzertifikat in eine Java TrustStore-Instanz, damit das SDK mit dem Emulator verwendet werden kann. Weitere Informationen finden Sie unter [Exportieren der Azure Cosmos DB-Emulatorzertifikate](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Probleme durch Abhängigkeitskonflikte

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Die oben genannten Ausnahme legt nahe, dass bei Ihnen eine Abhängigkeit von einer älteren Version der RxJava-Bibliothek (z. B. 1.2.2) vorhanden ist. Unser SDK verwendet RxJava 1.3.8, die APIs enthält, die in früheren Version von RxJava nicht verfügbar sind. 

Die Problemumgehung für solche Probleme besteht darin, zu identifizieren, in welchen anderen Abhängigkeiten RxJava-1.2.2 vorhanden ist, die transitive Abhängigkeit von RxJava-1.2.2 auszuschließen und dem CosmosDB SDK zu gestatten, die neuere Version einzubringen.

Um zu identifizieren, welche Bibliothek RxJava-1.2.2 einbringt, führen Sie den folgenden Befehl neben Ihrer Projektdatei „pom.xml“ aus:
```bash
mvn dependency:tree
```
Weitere Informationen finden Sie in der [Anleitung zur Maven-Abhängigkeitsstruktur](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Nachdem Sie identifiziert haben, dass RxJava-1.2.2 eine transitive Abhängigkeit einer anderen Abhängigkeit Ihres Projekts ist, können Sie die Abhängigkeit von dieser Bibliothek in Ihrer pom-Datei ändern und die transitive RxJava-Abhängigkeit ausschließen:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Weitere Informationen finden Sie in der [Anleitung zum Ausschließen transitiver Abhängigkeiten](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Aktivieren von Client-SDK-Protokollierung

Das Java Async SDK verwendet SLF4j als Protokollierungsfassade, die die Anmeldung bei gängigen Protokollierungsframeworks wie log4j und logback unterstützt.

Wenn Sie beispielsweise log4j als Protokollierungsframework verwenden möchten, fügen Sie Ihrem Java-Klassenpfad die folgenden Bibliotheken hinzu:

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Fügen Sie außerdem eine log4j-Konfiguration hinzu:
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Weitere Informationen finden Sie im [Leitfaden zur sfl4j-Protokollierung](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Netzwerkstatistiken des Betriebssystems
Führen Sie den Befehl „netstat“ aus, um einen Überblick darüber zu erhalten, wie viele Verbindungen sich im Zustand `ESTABLISHED`, `CLOSE_WAIT` usw. befinden.

Unter Linux können Sie den folgenden Befehl ausführen:
```bash
netstat -nap
```
Filtern Sie das Ergebnis so, dass nur Verbindungen mit dem Azure Cosmos DB-Endpunkt angezeigt werden.

Die Anzahl von Verbindungen mit dem Azure Cosmos DB-Endpunkt im Zustand `ESTABLISHED` kann nicht höher sein als die Größe des konfigurierten Verbindungspools.

Viele Verbindungen mit dem Azure Cosmos DB-Endpunkt befinden sich möglicherweise im Zustand `CLOSE_WAIT`. Vielleicht sind es sogar mehr als 1.000. Eine so hohe Zahl deutet darauf hin, dass Verbindungen sehr schnell hergestellt und wieder getrennt werden. Das führt unter Umständen zu Problemen. Weitere Informationen finden Sie im Abschnitt [Häufig auftretende Probleme und Problemumgehungen].

 <!--Anchors-->
[Häufig auftretende Probleme und Problemumgehungen]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Verbindungslimit auf einem Hostcomputer]: #connection-limit-on-host
[Azure SNAT-Portauslastung (PAT)]: #snat
