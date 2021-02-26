---
title: Behandeln häufiger Fehler in der Cassandra-API von Azure Cosmos DB
description: In diesem Dokument erfahren Sie, wie Sie häufige Probleme in der Cassandra-API von Azure Cosmos DB behandeln.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 6d9a74729768a326379b5efddb864a4fee02fa59
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493214"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>Behandeln häufiger Probleme in der Cassandra-API von Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Die Cassandra-API in Azure Cosmos DB ist eine Kompatibilitätsebene, die [Wire Protocol-Unterstützung](cassandra-support.md) für die beliebte Open-Source-Datenbank Apache Cassandra bereitstellt und von [Azure Cosmos DB](./introduction.md) unterstützt wird. Als vollständig verwalteter cloudnativer Dienst bietet Azure Cosmos DB [Garantien für Verfügbarkeit, Durchsatz und Konsistenz](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) für die Cassandra-API. Diese Garantien sind für Legacyimplementierungen von Apache Cassandra nicht möglich. Außerdem ermöglicht die Cassandra-API die Ausführung von Plattformvorgängen ohne Wartung und das Patchen ohne Ausfallzeiten. Daher unterscheiden sich viele Back-End-Vorgänge von Apache Cassandra. Aus diesem Grund werden bestimmte Einstellungen und Vorgehensweisen empfohlen, um häufige Fehler zu vermeiden. 

In diesem Artikel werden häufige Fehler und deren Lösungen für Anwendungen beschrieben, die die Cassandra-API von Azure Cosmos DB nutzen. Falls der bei Ihnen auftretende Fehler im Anschluss nicht aufgeführt ist und es sich um einen Fehler bei einem [unterstützten Vorgang in der Cassandra-API](cassandra-support.md) handelt, der bei der Verwendung von *nativem Apache Cassandra nicht auftritt*, [erstellen Sie eine Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
Hierbei handelt es sich um eine übergeordnete Wrapperausnahme mit einer Vielzahl möglicher Ursachen und innerer Ausnahmen, von denen viele mit dem Client zusammenhängen können. 
### <a name="solution"></a>Lösung
Im Anschluss folgen einige gängige Ursachen und Lösungen: 
- Leerlauftimeout von Azure Load Balancer-Instanzen: Dieses Problem kann auch in Form von `ClosedConnectionException` auftreten. Legen Sie zur Behebung die Keepalive-Einstellung im Treiber fest (wie [hier](#enable-keep-alive-for-java-driver) zu sehen), und erhöhen Sie die Keepalive-Einstellungen im Betriebssystem, oder [passen Sie das Leerlauftimeout in Azure Load Balancer an](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). 
- **Ressourcenauslastung der Clientanwendung:** Vergewissern Sie sich, dass die Clientcomputer über genügend Ressourcen verfügen, um die Anforderung abzuwickeln. 

## <a name="cannot-connect-to-host"></a>Verbindungsherstellung mit Host nicht möglich
Ihnen wird unter Umständen der folgende Fehler angezeigt: `Cannot connect to any host, scheduling retry in 600000 milliseconds` 

### <a name="solution"></a>Lösung
Hierbei kann es sich um eine clientseitige SNAT-Auslastung handeln. Gehen Sie wie unter [Verwenden von SNAT für ausgehende Verbindungen](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) beschrieben vor, um dieses Problem auszuschließen. Es kann sich aber auch um ein Leerlauftimeoutproblem handeln. (Das Leerlauftimeout von Azure Load Balancer ist standardmäßig auf vier Minuten festgelegt.) Weitere Informationen finden Sie in der Dokumentation unter [Konfigurieren der TCP-Zurücksetzung und des Leerlauftimeouts für Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). Aktivieren Sie TCP-Keep-Alive über die Treibereinstellungen (wie [hier](#enable-keep-alive-for-java-driver) zu sehen), und legen Sie das Intervall `keepAlive` für das Betriebssystem auf weniger als vier Minuten fest.

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
Die Gesamtanzahl der verbrauchten Anforderungseinheiten übersteigt die bereitgestellten Anforderungseinheiten für den Keyspace oder die Tabelle. Daher werden die Anforderungen gedrosselt.
### <a name="solution"></a>Lösung
Skalieren Sie den Durchsatz, der einem Keyspace oder einer Tabelle zugewiesen ist, im Azure-Portal ([hier](manage-scale-cassandra.md) finden Sie weitere Informationen zu Skalierungsvorgängen in der Cassandra-API), oder implementieren Sie eine Wiederholungsrichtlinie. Informationen zu Java finden Sie unter den Beispielen für Wiederholungslogik für den [v3.x-Treiber](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) und den [v4.x-Treiber](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Weitere Informationen finden Sie auch unter [Cassandra-Erweiterungen für Java in Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions).

### <a name="overloadedexception-even-with-sufficient-throughput"></a>„OverloadedException“ trotz ausreichendem Durchsatz 
Anforderungen werden vom System offenbar gedrosselt, obwohl ausreichend Durchsatz für das Anforderungsvolumen und/oder die verbrauchten Anforderungseinheiten bereitgestellt wurde. Eine unerwartete Ratenbegrenzung kann zwei Ursachen haben:
- **Vorgänge auf Schemaebene:** Die Cassandra-API implementiert ein Budget für den Systemdurchsatz bei Vorgängen auf Schemaebene (CREATE TABLE, ALTER TABLE, DROP TABLE). Dieses Budget sollte für Schemavorgänge in einem Produktionssystem ausreichen. Bei einer sehr großen Anzahl von Vorgängen auf Schemaebene ist es jedoch möglich, dass dieser Grenzwert überschritten wird. Da dieses Budget nicht vom Benutzer gesteuert wird, muss ggf. die Anzahl der ausgeführten Schemavorgänge verringert werden. Wenn das Problem dadurch nicht behoben werden kann oder diese Aktion für Ihre Workload nicht möglich ist, [erstellen Sie eine Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md).
- **Datenschiefe:** Wenn Durchsatz über die Cassandra-API bereitgestellt wird, wird er gleichmäßig auf physische Partitionen aufgeteilt, und jede physische Partition hat eine Obergrenze. Wenn bei Ihnen eine große Datenmenge von einer bestimmten Partition eingefügt oder abgefragt wird, kann es trotz Bereitstellung eines hohen Gesamtdurchsatzes (Anforderungseinheiten) für diese Tabelle zu einer Ratenbegrenzung kommen. Überprüfen Sie Ihr Datenmodell, und vergewissern Sie sich, dass keine übermäßige Schiefe vorhanden ist, die zu heißen Partitionen führen kann. 

## <a name="intermittent-connectivity-errors-java"></a>Zeitweilige Verbindungsfehler (Java) 
Die Verbindung wird unerwartet getrennt, oder es tritt ein Timeout auf.

### <a name="solution"></a>Lösung 
Die Apache Cassandra-Treiber für Java bieten zwei native Richtlinien für die erneute Verbindungsherstellung: `ExponentialReconnectionPolicy` und `ConstantReconnectionPolicy`. Der Standardwert ist `ExponentialReconnectionPolicy`. Für die Cassandra-API von Azure Cosmos DB wird jedoch `ConstantReconnectionPolicy` mit einer Verzögerung von 2 Sekunden empfohlen. Informationen zum Java v4.x-Treiber finden Sie in der [Treiberdokumentation](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/), Informationen zu Java 3.x finden Sie [hier](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/), und Beispiele zum Konfigurieren einer Richtlinie für Verbindungswiederholungen für Java-Treiber stehen [weiter unten](#configuring-reconnectionpolicy-for-java-driver) zur Verfügung.

## <a name="error-with-load-balancing-policy"></a>Fehler im Zusammenhang mit der Lastenausgleichsrichtlinie

Angenommen, Sie haben eine Lastenausgleichsrichtlinie in v3.x des Java-DataStax-Treibers implementiert und dabei Code wie den folgenden verwendet:

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

Wenn der Wert für `withLocalDc()` nicht dem Rechenzentrum am Kontaktpunkt entspricht, tritt möglicherweise ganz kurz der folgende Fehler auf: `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`. 

### <a name="solution"></a>Lösung 
Implementieren Sie [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java). (Unter Umständen ist auch ein Upgrade der Nebenversion von DataStax erforderlich, damit diese Lösung funktioniert.)

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>Zählvorgang bei großer Tabelle nicht erfolgreich
Wenn `select count(*) from table` oder ein ähnlicher Befehl für eine große Anzahl von Zeilen ausgeführt wird, tritt ein Servertimeout auf.

### <a name="solution"></a>Lösung 
Bei Verwendung eines lokalen CQLSH-Clients können Sie versuchen, die Einstellung `--connect-timeout` oder `--request-timeout` zu ändern. (Weitere Informationen finden Sie [hier](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)). Sollte trotzdem weiterhin ein Timeout für den Zählvorgang auftreten, können Sie die Anzahl von Datensätzen über die Telemetriedaten des Azure Cosmos DB-Back-Ends abrufen. Wählen Sie hierzu im Azure-Portal auf der Registerkarte „Metriken“ die Metrik `document count` aus, und fügen Sie einen Filter für die Datenbank oder Sammlung (Entsprechung der Tabelle in Azure Cosmos DB) hinzu. In dem resultierenden Graphen können Sie dann auf den Zeitpunkt zeigen, für den Sie die Anzahl von Datensätzen ermitteln möchten.

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="Metrikenansicht":::


## <a name="configuring-reconnectionpolicy-for-java-driver"></a>Konfigurieren einer Richtlinie für Verbindungswiederholungen für Java-Treiber

### <a name="version-3x"></a>Version 3.x

Bei Version 3.x des Java-Treibers konfigurieren Sie die Richtlinie für Verbindungswiederholungen bei der Erstellung eines Clusterobjekts:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>Version 4.x

Bei Version 4.x des Java-Treibers konfigurieren Sie die Richtlinie für Verbindungswiederholungen durch Überschreiben der Einstellungen in der Datei `reference.conf`:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>Aktivieren der Keepalive-Einstellung für Java-Treiber

### <a name="version-3x"></a>Version 3.x

Bei Version 3.x des Java-Treibers legen Sie die Keepalive-Einstellung beim Erstellen eines Clusterobjekts fest. Sie müssen darüber hinaus auch sicherstellen, dass Keepalive [im Betriebssystem aktiviert](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) ist:

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>Version 4.x

Bei Version 4.x des Java-Treibers legen Sie die Keepalive-Einstellung durch Überschreiben der Einstellungen in `reference.conf` fest. Sie müssen darüber hinaus auch sicherstellen, dass Keepalive [im Betriebssystem aktiviert](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089) ist:

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [unterstützten Features](cassandra-support.md) der Cassandra-API von Azure Cosmos DB.
- Erfahren Sie, wie Sie [vom nativen Apache Cassandra zur Cassandra-API von Azure Cosmos DB migrieren](cassandra-migrate-cosmos-db-databricks.md).