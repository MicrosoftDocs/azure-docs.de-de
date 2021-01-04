---
title: Spiegeln von Apache Kafka-Themen – Azure HDInsight
description: Es wird beschrieben, wie Sie die Spiegelungsfunktion von Apache Kafka verwenden, um ein Replikat von Kafka in einem HDInsight-Cluster aufzubewahren, indem Sie die Themen in einem sekundären Cluster spiegeln.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: d4a2be6719fdaaa9dc859df21cc030478e474210
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428236"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Verwenden von MirrorMaker zum Replizieren von Apache Kafka-Themen mit Kafka in HDInsight

Erfahren Sie, wie Sie die Spiegelungsfunktion von Apache Kafka verwenden, um Themen in einen sekundären Cluster zu replizieren. Die Spiegelung kann als fortlaufender Prozess ausgeführt oder zu bestimmten Zeitpunkten als Methode zum Migrieren von Daten aus einem Cluster in einen anderen verwendet werden.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Whitelist* , einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

In diesem Beispiel wird das Spiegelung zum Replizieren von Themen zwischen zwei HDInsight-Clustern verwendet. Die beiden Cluster befinden sich in verschiedenen virtuellen Netzwerken in verschiedenen Rechenzentren.

> [!WARNING]  
> Die Spiegelung sollte nicht als Mittel zum Erzielen von Fehlertoleranz angesehen werden. Der Versatz von Elementen in einem Thema unterscheidet sich im primären und sekundären Cluster, sodass die Cluster für Clients nicht austauschbar sind.
>
> Falls Sie Bedenken wegen der Fehlertoleranz haben, sollten Sie die Replikation für die Themen in Ihrem Cluster festlegen. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Apache Kafka-Clusters in HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Funktionsweise der Apache Kafka-Spiegelung

Für die Spiegelung wird das Tool [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (Teil von Apache Kafka) verwendet, um Datensätze aus Themen im primären Cluster zu nutzen und anschließend eine lokale Kopie im sekundären Cluster zu erstellen. MirrorMaker nutzt einen (oder mehrere) *Consumer* zum Lesen von Daten aus dem primären Cluster und einen *Producer* , der in den lokalen Cluster (sekundären Cluster) schreibt.

Am sinnvollsten ist es, die Spiegelung für die Notfallwiederherstellung mit Kafka-Clustern in verschiedenen Azure-Regionen einzurichten. Zu diesem Zweck werden die virtuellen Netzwerke, in denen sich die Cluster befinden, zu einem Peeringnetzwerk zusammengeschlossen.

Das folgende Diagramm veranschaulicht den Spiegelungsprozess und den Kommunikationsfluss zwischen den Clustern:

![Diagramm des Spiegelungsprozesses](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Der primäre und der sekundäre Cluster können sich in Bezug auf die Anzahl von Knoten und Partitionen unterscheiden, und auch der Versatz in den Themen ist unterschiedlich. Beim Spiegeln wird der Schlüsselwert beibehalten, der für die Partitionierung verwendet wird, sodass die Datensatzreihenfolge pro Schlüssel beibehalten wird.

### <a name="mirroring-across-network-boundaries"></a>Spiegelung über Netzwerkgrenzen hinweg

Wenn Sie eine Spiegelung zwischen Kafka-Clustern in unterschiedlichen Netzwerken durchführen müssen, sollten Sie außerdem Folgendes beachten:

* **Gateways** : Die Netzwerke müssen auf TCP/IP-Ebene kommunizieren können.

* **Serveradresse** : Sie können die IP-Adressen oder die vollqualifizierten Domänennamen verwenden, um Ihre Clusterknoten zu erreichen.

    * **IP-Adressen** : Wenn Sie Ihre Kafka-Cluster für die Ankündigung der IP-Adresse konfigurieren, können Sie die Spiegelung mit den IP-Adressen der Brokerknoten und der Zookeeperknoten einrichten.
    
    * **Domänennamen** : Wenn Sie Ihre Kafka-Cluster nicht für die Ankündigung der IP-Adresse konfigurieren, müssen die Cluster über ihre vollqualifizierten Domänennamen eine Verbindung miteinander herstellen können. Hierfür ist ein DNS-Server (Domain Name System) in jedem Netzwerk erforderlich, der dafür konfiguriert ist, Anforderungen an die anderen Netzwerke weiterzuleiten. Beim Erstellen eines virtuellen Azure-Netzwerks müssen Sie einen benutzerdefinierten DNS-Server und die IP-Adresse für den Server angeben, anstatt das automatisch bereitgestellte DNS des Netzwerks zu verwenden. Nach der Erstellung des virtuellen Netzwerks müssen Sie dann einen virtuellen Azure-Computer erstellen, für den diese IP-Adresse verwendet wird, und anschließend die DNS-Software darauf installieren und konfigurieren.

    > [!WARNING]  
    > Erstellen und konfigurieren Sie den benutzerdefinierten DNS-Server, bevor Sie HDInsight im virtuellen Netzwerk installieren. Es ist keine zusätzliche Konfiguration erforderlich, damit HDInsight den für das virtuelle Netzwerk konfigurierten DNS-Server verwenden kann.

Weitere Informationen zum Verbinden von zwei virtuellen Azure-Netzwerken finden Sie unter [Konfigurieren einer VNet-zu-VNet-Verbindung](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architektur der Spiegelung

In dieser Architektur befinden sich zwei Cluster in verschiedenen Ressourcengruppen und virtuellen Netzwerken: ein **primärer** und ein **sekundärer** Cluster.

### <a name="creation-steps"></a>Schritte zur Erstellung

1. Erstellen Sie zwei neue Ressourcengruppen:

    |Ressourcengruppe | Standort |
    |---|---|
    | kafka-primary-rg | USA, Mitte |
    | kafka-secondary-rg | USA Nord Mitte |

1. Erstellen Sie in **kafka-primary-rg** ein neues virtuelles Netzwerk namens **kafka-primary-vnet**. Übernehmen Sie die Standardeinstellungen.
1. Erstellen Sie – ebenfalls mit den Standardeinstellungen – in **kafka-secondary-rg** ein neues virtuelles Netzwerk namens **kafka-secondary-vnet**.

1. Erstellen Sie zwei neue Kafka-Cluster:

    | Clustername | Ressourcengruppe | Virtual Network | Speicherkonto |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Erstellen Sie virtuelle Netzwerkpeerings. Mit diesem Schritt werden zwei Peerings erstellt: eins von **kafka-primary-vnet** zu **kafka-secondary-vnet** und das andere von **kafka-secondary-vnet** zurück zu **kafka-primary-vnet**.
    1. Wählen Sie das virtuelle Netzwerk **kafka-primary-vnet** aus.
    1. Wählen Sie unter **Einstellungen** die Option **Peerings** aus.
    1. Wählen Sie **Hinzufügen**.
    1. Geben Sie auf dem Bildschirm **Peering hinzufügen** die Informationen wie im Screenshot unten gezeigt ein.

        ![HDInsight Kafka – Hinzufügen von VNET-Peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Konfigurieren der Ankündigung der IP-Adresse

Konfigurieren Sie die Ankündigung der IP-Adresse, um einem Client das Herstellen einer Verbindung mithilfe von Broker-IP-Adressen anstelle von Domänennamen zu ermöglichen.

1. Wechseln Sie zum Ambari-Dashboard für den primären Cluster: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
1. Wählen Sie **Dienste** > **Kafka** aus. Wählen Sie die Registerkarte **Configs** aus.
1. Fügen Sie dem unteren Abschnitt **kafka-env template** folgende Konfigurationszeilen hinzu. Wählen Sie **Speichern** aus.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Geben Sie auf dem Bildschirm **Save Configuration** (Konfiguration speichern) einen Hinweis ein, und klicken Sie auf **Save** (Speichern).
1. Wenn eine Konfigurationswarnung angezeigt wird, klicken Sie auf **Proceed Anyway** (Trotzdem fortfahren).
1. Wählen Sie für **Save Configuration Changes** (Konfigurationsänderungen speichern) die Option **OK** aus.
1. Wählen Sie in der Benachrichtigung **Restart Required** (Neustart erforderlich) die Option **Restart** > **Restart All Affected** (Neustart > Alle betroffenen neu starten) aus. Wählen Sie **Confirm Restart All**.

    ![Apache Ambari – Neustarten aller betroffenen Instanzen](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Konfigurieren Sie Kafka zum Lauschen auf allen Netzwerkschnittstellen.
    
1. Bleiben Sie auf der Registerkarte **Configs** (Konfigurationen) unter **Services** > **Kafka** (Dienste > Kafka). Legen Sie im Abschnitt **Kafka Broker** die Eigenschaft **listeners** auf `PLAINTEXT://0.0.0.0:9092` fest.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Restart** (Neustart) und **Confirm Restart All** (Bestätigen: Alle neu starten) aus.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Notieren Sie sich die IP-Adressen der Broker und Zookeeper für den primären Cluster.

1. Wählen Sie auf dem Ambari-Dashboard **Hosts** aus.
1. Notieren Sie sich die IP-Adressen für Broker und Zookeeper. Die ersten beiden Buchstaben des Hostnamens lauten **wn** für die Brokerknoten und **zk** für die Zookeeperknoten.

    ![Apache Ambari – Ansicht mit IP-Adressknoten](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Wiederholen Sie die vorangegangenen drei Schritte für den zweiten Cluster **kafka-secondary-cluster** : Konfigurieren der Ankündigung von IP-Adressen, Einrichten von Listenern und Notieren der IP-Adressen von Broker und Zookeeper.

## <a name="create-topics"></a>Erstellen von Themen

1. Stellen Sie über SSH eine Verbindung mit dem **primären** Cluster her:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersetzen Sie **sshuser** durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie **PRIMARYCLUSTER** durch den Basisnamen, den Sie beim Erstellen des Clusters verwendet haben.

    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Verwenden Sie den folgenden Befehl, um eine Variable mit den Apache-Zookeeperhosts für den primären Cluster zu erstellen. Zeichenfolgen wie `ZOOKEEPER_IP_ADDRESS1` müssen durch die tatsächlichen IP-Adressen ersetzt werden, die Sie zuvor notiert haben, z.B. `10.23.0.11` und `10.23.0.7`. Wenn Sie die Auflösung von vollqualifizierten Domänennamen mit einem benutzerdefinierten DNS-Server durchführen, führen Sie [diese Schritte](apache-kafka-get-started.md#getkafkainfo) aus, um die Namen der Broker und ZooKeeper abzurufen:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Um ein Thema mit Namen `testtopic` zu erstellen, nutzen Sie den folgenden Befehl:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Verwenden Sie den folgenden Befehl, um zu bestätigen, dass das Thema erstellt wurde:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    Die Antwort enthält `testtopic`.

1. Verwenden Sie Folgendes, um die Informationen zum Zookeeperhost für diesen Cluster (den **primären** Cluster) anzuzeigen:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Speichern Sie diese Informationen. Sie werden im nächsten Abschnitt verwendet.

## <a name="configure-mirroring"></a>Konfigurieren der Spiegelung

1. Stellen Sie in einer anderen SSH-Sitzung eine Verbindung mit dem **sekundären Cluster** her:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Ersetzen Sie **sshuser** durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie **SECONDARYCLUSTER** durch den Namen, den Sie beim Erstellen des Clusters verwendet haben.

    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Mit einer `consumer.properties`-Datei wird die Kommunikation mit dem **primären** Cluster konfiguriert. Verwenden Sie zum Erstellen der Datei den folgenden Befehl:

    ```bash
    nano consumer.properties
    ```

    Verwenden Sie als Inhalt der Datei `consumer.properties` den folgenden Text:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersetzen Sie **PRIMARY_ZKHOSTS** durch die IP-Adressen der Zookeeper aus dem **primären** Cluster.

    In dieser Datei werden die Consumerinformationen beschrieben, die beim Lesen aus dem primären Kafka-Cluster verwendet werden sollten. Weitere Informationen zur Consumerkonfiguration finden Sie unter [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Consumerkonfigurationen) bei „kafka.apache.org“.

    Drücken Sie zum Speichern der Datei **STRG+X** , **Y** und dann die **EINGABETASTE**.

1. Bevor Sie den Producer konfigurieren, der mit dem sekundären Cluster kommuniziert, richten Sie eine Variable für die IP-Adressen der Broker des **sekundären** Clusters ein. Verwenden Sie die folgenden Befehle, um diese Variable zu erstellen:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Der Befehl `echo $SECONDARY_BROKERHOSTS` gibt Informationen ähnlich dem folgenden Text zurück:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Eine `producer.properties`-Datei wird für die Kommunikation mit dem **sekundären** Cluster verwendet. Verwenden Sie zum Erstellen der Datei den folgenden Befehl:

    ```bash
    nano producer.properties
    ```

    Verwenden Sie als Inhalt der Datei `producer.properties` den folgenden Text:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Ersetzen Sie **SECONDARY_BROKERHOSTS** durch die IP-Adressen der Broker, die im vorherigen Schritt verwendet wurden.

    Weitere Informationen zur Producerkonfiguration finden Sie unter [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Producerkonfigurationen) bei „kafka.apache.org“.

1. Verwenden Sie die folgenden Befehle, um eine Umgebungsvariable mit den IP-Adressen der Zookeeperhosts für den sekundären Cluster zu erstellen:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Die Standardkonfiguration für Kafka auf HDInsight erlaubt keine automatische Erstellung von Themen. Bevor Sie mit der Spiegelung beginnen, müssen Sie sich für eine der folgenden Optionen entscheiden:

    * **Erstellen der Themen im sekundären Cluster** : Bei dieser Option haben Sie auch die Möglichkeit, die Anzahl von Partitionen und den Replikationsfaktor festzulegen.

        Mit folgendem Befehl können Sie Themen vorab erstellen:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Ersetzen Sie `testtopic` durch den Namen des zu erstellenden Themas.

    * **Konfigurieren des Clusters für die automatische Themaerstellung** : Bei dieser Option kann MirrorMaker zum automatischen Erstellen von Themen verwendet werden. Die Themen werden jedoch möglicherweise mit einer unterschiedlichen Anzahl von Partitionen bzw. einem anderen Replikationsfaktor als im primären Thema erstellt.

        Um den sekundären Cluster für das automatische Erstellen von Themen zu konfigurieren, führen Sie die folgenden Schritte aus:

        1. Wechseln Sie zum Ambari-Dashboard für den sekundären Cluster: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Klicken Sie auf **Services** > **Kafka** (Dienste > Kafka). Klicken Sie auf die Registerkarte **Configs** .
        1. Geben Sie in das Feld __Filter__ den Wert `auto.create` ein. Dies filtert die Liste der Eigenschaften und zeigt die Einstellung `auto.create.topics.enable`.
        1. Ändern Sie den Wert von `auto.create.topics.enable` in „true“, und wählen Sie dann __Speichern__. Fügen Sie einen Hinweis hinzu, und wählen Sie dann erneut __Speichern__.
        1. Wählen Sie den Dienst __Kafka__ , dann die Option __Neu starten__ und abschließend die Option __Neustart aller betroffenen__. Klicken Sie bei entsprechender Aufforderung auf __Neustart aller Dienste bestätigen__.

        ![Kafka – Aktivieren der automatischen Erstellung eines Themas](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Starten von MirrorMaker

1. Verwenden Sie den folgenden Befehl über die SSH-Verbindung mit dem **sekundären** Cluster, um den MirrorMaker-Prozess zu starten:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Die in diesem Beispiel verwendeten Parameter sind:

    |Parameter |Beschreibung |
    |---|---|
    |--consumer.config|Gibt die Datei an, in der die Consumereigenschaften enthalten sind. Diese Eigenschaften werden verwendet, um einen Consumer zu erstellen, mit dem aus dem *primären* Kafka-Cluster gelesen wird.|
    |--producer.config|Gibt die Datei an, in der die Producereigenschaften enthalten sind. Diese Eigenschaften werden verwendet, um einen Producer zu erstellen, mit dem in den *sekundären* Kafka-Cluster geschrieben wird.|
    |--whitelist|Eine Liste mit Themen, die von MirrorMaker aus dem primären in den sekundären Cluster repliziert werden.|
    |--num.streams|Die Anzahl von Consumerthreads, die erstellt werden sollen.|

    Der Consumer auf dem sekundären Knoten wartet jetzt auf Nachrichten.

2. Verwenden Sie den folgenden Befehl über die SSH-Verbindung mit dem **primären** Cluster, um einen Producer zu starten und Nachrichten an das Thema zu senden:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Sobald der Cursor an einer leeren Zeile steht, geben Sie einige Textnachrichten ein. Diese Nachrichten werden an das Thema im **primären** Cluster gesendet. Drücken Sie anschließend **STRG + C** , um den Producer-Prozess zu beenden.

3. Drücken Sie über die SSH-Verbindung mit dem **sekundären** Cluster die Tastenkombination **STRG + C** , um den MirrorMaker-Prozess zu beenden. Es kann einige Sekunden in Anspruch nehmen, um den Prozess zu beenden. Um sicherzustellen, dass die Nachrichten im sekundären Cluster repliziert wurden, verwenden Sie den folgenden Befehl:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    Die Themenliste enthält nun auch das Thema `testtopic`, das erstellt wurde, als MirrorMaker das Thema vom primären an den sekundären Cluster gespiegelt hat. Die aus dem Thema abgerufenen Nachrichten sind identisch mit den Nachrichten, die Sie im primären Cluster eingegeben haben.

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Mit den Schritten in diesem Dokument wurden Cluster in verschiedenen Azure-Ressourcengruppen erstellt. Um alle erstellten Ressourcen zu löschen, können Sie einfach die Ressourcengruppen löschen: **kafka-primary-rg** und **kafka-secondary_rg**. Durch Löschen der Ressourcengruppen werden alle beim Durcharbeiten dieses Dokuments erstellten Ressourcen gelöscht: Cluster, virtuelle Netzwerke und Speicherkonten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) zur Erstellung eines Replikats eines [Apache Kafka](https://kafka.apache.org/)-Clusters verwendet wird. Verwenden Sie die folgenden Links, um weitere Möglichkeiten zur Arbeit mit Kafka kennenzulernen:

* [Dokumentation zu Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) auf cwiki.apache.org.
* [Bewährte Methoden für Kafka MirrorMaker](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Erste Schritte mit Apache Kafka in HDInsight](apache-kafka-get-started.md)
* [Verwenden von Apache Spark mit Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Herstellen einer Verbindung mit Apache Kafka über ein virtuelles Azure-Netzwerk](apache-kafka-connect-vpn-gateway.md)
