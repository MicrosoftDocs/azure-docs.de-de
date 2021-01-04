---
title: Diagnose und Problembehandlung des Azure Cosmos DB Java SDK v4
description: Verwenden Sie Features wie die clientseitige Protokollierung und andere Tools von Drittanbietern, um Azure Cosmos DB-Probleme im Java SDK v4 zu erkennen, zu diagnostizieren und zu beheben.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: 4753f7c0b8b5e515d33da3f9df48a2cdd9d921cc
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96017575"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Behandeln von Problemen bei der Verwendung des Azure Cosmos DB Java SDK v4 mit SQL-API-Konten
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Asynchrones Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> In diesem Artikel wird die Problembehandlung für das Azure Cosmos DB Java SDK v4 behandelt. Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-java-v4.md) zum Azure Cosmos DB Java SDK v4, im [Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-cosmos) und in den [Leistungstipps](performance-tips-java-sdk-v4-sql.md). Wenn Sie aktuell eine ältere Version als V4 verwenden, lesen Sie den Leitfaden [Migrieren zu Azure Cosmos DB Java SDK V4](migrate-java-v4-sdk.md), um Hilfe beim Aktualisieren auf V4 zu erhalten.
>

In diesem Artikel werden allgemeine Probleme, Problemumgehungen, Diagnoseschritte und Tools für die Verwendung des Azure Cosmos DB Java SDK v4 mit Azure Cosmos DB-SQL-API-Konten behandelt.
Das Azure Cosmos DB Java SDK v4 bietet eine clientseitige logische Darstellung für den Zugriff auf die Azure Cosmos DB-SQL-API. Dieser Artikel beschreibt hilfreiche Tools und Vorgehensweisen für Problemfälle.

Beginnen Sie mit dieser Liste:

* Sehen Sie sich den Abschnitt [Häufig auftretende Probleme und Problemumgehungen] in diesem Artikel an.
* Sehen Sie sich das Java SDK im zentralen Azure Cosmos DB-Repository an, das [Open-Source auf GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) verfügbar ist. Es verfügt über einen aktiv überwachten [Problemabschnitt](https://github.com/Azure/azure-sdk-for-java/issues). Überprüfen Sie, ob Sie ein ähnliches Problem finden, für das bereits eine Problemumgehung dokumentiert wurde. Ein hilfreicher Tipp ist das Filtern von Problemen nach dem Tag *cosmos:v4-item*.
* Sehen Sie sich die [Leistungstipps](performance-tips-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK v4 an, und implementieren Sie die empfohlenen Methoden.
* Lesen Sie den Rest dieses Artikels, falls Sie keine Lösung gefunden haben. Reichen Sie anschließend ein [GitHub-Problem](https://github.com/Azure/azure-sdk-for-java/issues) ein. Wenn es eine Option zum Hinzufügen von Tags zu Ihrem GitHub-Issue gibt, fügen Sie das Tag *cosmos:v4-item* hinzu.

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Häufig auftretende Probleme und Problemumgehungen

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Netzwerkprobleme, Netty-Lesetimeoutfehler, niedriger Durchsatz, hohe Latenz

#### <a name="general-suggestions"></a>Allgemeine Empfehlungen
Für eine optimale Leistung:
* Vergewissern Sie sich, dass die App in der gleichen Region ausgeführt wird, in der sich auch Ihr Azure Cosmos DB-Konto befindet. 
* Überprüfen Sie die CPU-Auslastung auf dem Host, auf dem die App ausgeführt wird. Wenn die CPU-Auslastung 50 Prozent oder mehr beträgt, führen Sie Ihre App auf einem Host mit einer höheren Konfiguration aus. Alternativ können Sie die Last auf mehrere Computer verteilen.
    * Wenn Sie Ihre Anwendung in Azure Kubernetes Service ausführen, können Sie [Azure Monitor zum Überwachen der CPU-Auslastung verwenden](../azure-monitor/insights/container-insights-analyze.md).

#### <a name="connection-throttling"></a>Verbindungsdrosselung
Eine Verbindungsdrosselung kann entweder aufgrund eines [Verbindungslimit auf einem Hostcomputer] oder aufgrund von [Azure SNAT-Portauslastung (PAT)] auftreten.

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Verbindungslimit auf einem Hostcomputer
Bei einigen Linux-Systemen (beispielsweise Red Hat) gilt eine Obergrenze für die Gesamtzahl geöffneter Dateien. Da Sockets in Linux als Dateien implementiert werden, schränkt dies auch die Gesamtanzahl von Verbindungen ein.
Führen Sie den folgenden Befehl aus.

```bash
ulimit -a
```
Die maximal zulässige Anzahl geöffneter Dateien (nofile) muss mindestens doppelt so hoch sein wie die Größe Ihres Verbindungspools. Weitere Informationen finden Sie im Artikel zu den [Leistungstipps](performance-tips-java-sdk-v4-sql.md) für das Azure Cosmos DB Java SDK v4.

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

Das SDK verwendet für die Kommunikation mit Azure Cosmos DB die [Netty](https://netty.io/)-E/A-Bibliothek. Das SDK verfügt über eine asynchrone API und verwendet nicht blockierende E/A-APIs von Netty. Die E/A-Aufgaben des SDK werden in Netty-E/A-Threads ausgeführt. Die Anzahl von Netty-E/A-Threads ist so konfiguriert, dass sie mit der Anzahl von CPU-Kernen des App-Computers übereinstimmt. 

Die Netty-E/A-Threads sind nur für nicht blockierende Netty-E/A-Aufgaben vorgesehen. Das SDK gibt das API-Aufrufergebnis für einen der Netty-E/A-Threads an den Code der App zurück. Wenn die App nach dem Empfang von Ergebnissen im Netty-Thread einen länger dauernden Vorgang ausführt, stehen dem SDK möglicherweise nicht genügend E/A-Threads für interne E/A-Aufgaben zur Verfügung. Eine solche App-Programmierung kann zu niedrigem Durchsatz, hoher Wartezeit und zu `io.netty.handler.timeout.ReadTimeoutException`-Fehlern führen. Wechseln Sie zur Problemumgehung den Thread, wenn Sie wissen, dass der Vorgang länger dauert.

Betrachten Sie beispielsweise das folgende Codeausschnitt, in dem Elemente zu einem Container hinzugefügt werden ([hier](create-sql-api-java.md) finden Sie eine Anleitung zum Einrichten der Datenbank und des Containers). Möglicherweise wird im Netty-Thread eine Aufgabe ausgeführt, die mehr als nur ein paar Millisekunden dauert. In diesem Fall kann es vorkommen, dass für die Verarbeitung von E/A-Aufgaben kein Netty-E/A-Thread mehr vorhanden ist. Dies führt dann zu einem ReadTimeoutException-Fehler.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

Ändern Sie zur Problemumgehung den Thread für die Ausführung zeitaufwendiger Aufgaben. Definieren Sie eine Singletoninstanz des Schedulers für Ihre App.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

Unter Umständen müssen zeitaufwendige Aufgaben (etwa rechenintensive Aufgaben oder blockierende E/A-Vorgänge) ausgeführt werden. Verlagern Sie den Thread in diesem Fall mithilfe der API `.publishOn(customScheduler)` auf einen durch `customScheduler` bereitgestellten Worker.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

Durch die Verwendung von `publishOn(customScheduler)` geben Sie den Netty-E/A-Thread frei und wechseln zu Ihrem eigenen benutzerdefinierten Thread, der vom benutzerdefinierten Scheduler bereitgestellt wird. Diese Änderung löst das Problem. Es treten keine Fehler vom Typ `io.netty.handler.timeout.ReadTimeoutException` mehr auf.

### <a name="request-rate-too-large"></a>Anforderungsrate zu groß
Hierbei handelt es sich um einen serverseitigen Fehler. Er gibt an, dass Sie den bereitgestellten Durchsatz verbraucht haben. Wiederholen Sie den Vorgang zu einem späteren Zeitpunkt. Sollte dieser Fehler häufiger auftreten, empfiehlt sich unter Umständen eine Erhöhung des Sammlungsdurchsatzes.

* **Implementieren von Backoff in getRetryAfterInMilliseconds-Intervallen**

    Es empfiehlt sich, die Last während Leistungstests so lange erhöhen, bis eine geringe Menge von Anforderungen gedrosselt wird. Wenn es sich um eine gedrosselte Anwendung handelt, sollte die Clientanwendung für das vom Server angegebene Wiederholungsintervall aussetzen. Durch das Aussetzen wird die geringstmögliche Wartezeit zwischen den Wiederholungsversuchen gewährleistet.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Fehler beim Herstellen einer Verbindung mit dem Azure Cosmos DB-Emulator

Das HTTPS-Zertifikat des Azure Cosmos DB-Emulators ist selbstsigniert. Importieren Sie das Emulatorzertifikat in eine Java TrustStore-Instanz, damit das SDK mit dem Emulator verwendet werden kann. Weitere Informationen finden Sie unter [Exportieren der Azure Cosmos DB-Emulatorzertifikate](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Probleme durch Abhängigkeitskonflikte

Das Azure Cosmos DB Java SDK umfasst eine Reihe von Abhängigkeiten. Allgemein gilt, wenn Ihre Projektabhängigkeitsstruktur eine ältere Version eine Artefakts enthält, von dem das Azure Cosmos DB Java SDK abhängig ist, kann dies zu unerwarteten Fehlern beim Ausführen Ihrer Anwendung führen. Wenn Sie debuggen, warum Ihre Anwendung unerwartete Ausnahmen auslöst, empfiehlt es sich, sicherzustellen, dass die Abhängigkeitsstruktur keine ältere Version einer oder mehrerer Abhängigkeiten des Azure Cosmos DB Java SDK miteinbezieht.

Zur Umgehung dieses Problems müssen Sie identifizieren, welche der Projektabhängigkeiten eine alte Version enthält, und die transitive Abhängigkeit für diese ältere Version ausschließen. Schließlich müssen Sie dem Azure Cosmos DB Java SDK erlauben, eine neuere Version zu verwenden.

Führen Sie den folgenden Befehl für Ihre Projektdatei „pom.xml“ aus, um zu identifizieren, welche Ihrer Projektabhängigkeiten eine ältere Version enthält:
```bash
mvn dependency:tree
```
Weitere Informationen finden Sie in der [Anleitung zur Maven-Abhängigkeitsstruktur](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Sobald Sie wissen, welche Abhängigkeit Ihres Projekts von einer älteren Version abhängig ist, können Sie die Abhängigkeit von der Bibliothek in Ihrer pom-Datei ändern und die transitive Abhängigkeit ausschließen, indem Sie dem folgenden Beispiel folgen (im Beispiel wird davon ausgegangen, dass es sich bei der veralteten Abhängigkeit um *reactor-core* handelt):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Weitere Informationen finden Sie in der [Anleitung zum Ausschließen transitiver Abhängigkeiten](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Aktivieren von Client-SDK-Protokollierung

Das Azure Cosmos DB Java SDK v4 verwendet SLF4j als Protokollierungsfassade, die die Anmeldung bei gängigen Protokollierungsframeworks wie log4j und logback unterstützt.

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

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
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

Unter Windows können Sie denselben Befehl mit anderen Argumentflags ausführen:
```bash
netstat -abn
```

Filtern Sie das Ergebnis so, dass nur Verbindungen mit dem Azure Cosmos DB-Endpunkt angezeigt werden.

Die Anzahl von Verbindungen mit dem Azure Cosmos DB-Endpunkt im Zustand `ESTABLISHED` kann nicht höher sein als die Größe des konfigurierten Verbindungspools.

Viele Verbindungen mit dem Azure Cosmos DB-Endpunkt befinden sich möglicherweise im Zustand `CLOSE_WAIT`. Vielleicht sind es sogar mehr als 1.000. Eine so hohe Zahl deutet darauf hin, dass Verbindungen sehr schnell hergestellt und wieder getrennt werden. Das führt unter Umständen zu Problemen. Weitere Informationen finden Sie im Abschnitt [Häufig auftretende Probleme und Problemumgehungen].

 <!--Anchors-->
[Häufig auftretende Probleme und Problemumgehungen]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Verbindungslimit auf einem Hostcomputer]: #connection-limit-on-host
[Azure SNAT-Portauslastung (PAT)]: #snat
