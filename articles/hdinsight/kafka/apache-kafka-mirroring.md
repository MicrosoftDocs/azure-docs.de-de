---
title: Spiegeln von Apache Kafka-Themen – Azure HDInsight
description: Es wird beschrieben, wie Sie die Spiegelungsfunktion von Apache Kafka verwenden, um ein Replikat von Kafka in einem HDInsight-Cluster aufzubewahren, indem Sie die Themen in einem sekundären Cluster spiegeln.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: ba04ed7c95cbf00d5996ef237d3ac65053da0662
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727393"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Verwenden von MirrorMaker zum Replizieren von Apache Kafka-Themen mit Kafka in HDInsight

Erfahren Sie, wie Sie die Spiegelungsfunktion von Apache Kafka verwenden, um Themen in einen sekundären Cluster zu replizieren. Die Spiegelung kann als fortlaufender Prozess ausgeführt oder zu bestimmten Zeitpunkten als Methode zum Migrieren von Daten aus einem Cluster in einen anderen verwendet werden.

In diesem Beispiel wird das Spiegelung zum Replizieren von Themen zwischen zwei HDInsight-Clustern verwendet. Beide Cluster befinden sich in einem virtuellen Azure-Netzwerk in der gleichen Region.

> [!WARNING]  
> Die Spiegelung sollte nicht als Mittel zum Erzielen von Fehlertoleranz angesehen werden. Der Versatz von Elementen in einem Thema unterscheidet sich für Quell- und Zielcluster, sodass diese für Clients nicht austauschbar sind.
>
> Falls Sie Bedenken wegen der Fehlertoleranz haben, sollten Sie die Replikation für die Themen in Ihrem Cluster festlegen. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Apache Kafka-Clusters in HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Funktionsweise der Apache Kafka-Spiegelung

Für die Spiegelung wird das Tool [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (Teil von Apache Kafka) verwendet, um Datensätze aus Themen im Quellcluster zu nutzen und anschließend eine lokale Kopie im Zielcluster zu erstellen. MirrorMaker nutzt einen (oder mehrere) *Consumer*, mit denen aus dem Quellcluster gelesen wird, und einen *Producer*, der in den lokalen Cluster (Zielcluster) schreibt.

Im folgenden Diagramm ist der Spiegelungsprozess dargestellt:

![Diagramm des Spiegelungsprozesses](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka in HDInsight ermöglicht keinen Zugriff auf den Kafka-Dienst über das öffentliche Internet. Kafka-Producers oder -Consumer müssen sich jeweils in demselben virtuellen Azure-Netzwerk wie die Knoten im Kafka-Cluster befinden. Für dieses Beispiel sind die Kafka-Quell- und -Zielcluster in einem virtuellen Azure-Netzwerk angeordnet. Im folgenden Diagramm ist dargestellt, wie der Kommunikationsfluss zwischen den Clustern abläuft:

![Diagramm mit Kafka-Quell- und -Zielclustern in einem virtuellen Azure-Netzwerk](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

Die Quell- und Zielcluster können sich in Bezug auf die Anzahl von Knoten und Partitionen unterscheiden, und auch der Versatz in den Themen ist unterschiedlich. Beim Spiegeln wird der Schlüsselwert beibehalten, der für die Partitionierung verwendet wird, sodass die Datensatzreihenfolge pro Schlüssel beibehalten wird.

### <a name="mirroring-across-network-boundaries"></a>Spiegelung über Netzwerkgrenzen hinweg

Wenn Sie eine Spiegelung zwischen Kafka-Clustern in unterschiedlichen Netzwerken durchführen müssen, sollten Sie außerdem Folgendes beachten:

* **Gateways**: Die Netzwerke müssen auf TCP/IP-Ebene kommunizieren können.

* **Namensauflösung**: Die Kafka-Cluster in jedem Netzwerk müssen per Hostname eine Verbindung miteinander herstellen können. Hierfür ist unter Umständen ein DNS-Server (Domain Name System) in jedem Netzwerk erforderlich, der dafür konfiguriert ist, Anforderungen an die anderen Netzwerke weiterzuleiten.

    Beim Erstellen eines virtuellen Azure-Netzwerks müssen Sie einen benutzerdefinierten DNS-Server und die IP-Adresse für den Server angeben, anstatt das automatisch bereitgestellte DNS des Netzwerks zu verwenden. Nach der Erstellung des virtuellen Netzwerks müssen Sie dann einen virtuellen Azure-Computer erstellen, für den diese IP-Adresse verwendet wird, und anschließend die DNS-Software darauf installieren und konfigurieren.

    > [!WARNING]  
    > Erstellen und konfigurieren Sie den benutzerdefinierten DNS-Server, bevor Sie HDInsight im virtuellen Netzwerk installieren. Es ist keine zusätzliche Konfiguration erforderlich, damit HDInsight den für das virtuelle Netzwerk konfigurierten DNS-Server verwenden kann.

Weitere Informationen zum Verbinden von zwei virtuellen Azure-Netzwerken finden Sie unter [Konfigurieren einer VNet-zu-VNet-Verbindung](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-apache-kafka-clusters"></a>Erstellen von Apache Kafka-Clustern

Es ist zwar möglich, ein virtuelles Azure-Netzwerk und Kafka-Cluster manuell zu erstellen, aber mit einer Azure Resource Manager-Vorlage ist dies erheblich einfacher. Führen Sie die folgenden Schritte aus, um ein virtuelles Azure-Netzwerk und zwei Kafka-Cluster für Ihr Azure-Abonnement bereitzustellen.

1. Verwenden Sie die folgende Schaltfläche, um sich bei Azure anzumelden, und öffnen Sie die Vorlage im Azure-Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Die Azure Resource Manager-Vorlage finden Sie unter **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json** .

    > [!WARNING]  
    > Um die Verfügbarkeit von Kafka in HDInsight zu gewährleisten, muss der Cluster mindestens drei Workerknoten enthalten. Diese Vorlage erstellt einen Kafka-Cluster, der drei Workerknoten enthält.

2. Verwenden Sie die folgenden Informationen, um die Einträge auf dem Blatt **Benutzerdefinierte Bereitstellung** aufzufüllen:
    
    ![Benutzerdefinierte HDInsight-Bereitstellung](./media/apache-kafka-mirroring/parameters.png)
    
    * **Ressourcengruppe**: Erstellen Sie eine Gruppe, oder wählen Sie eine vorhandene Gruppe aus. Diese Gruppe enthält den HDInsight-Cluster.

    * **Standort**: Wählen Sie einen Standort in Ihrer Nähe aus.
     
    * **Basisclustername**: Dieser Wert wird als Basisname für den Kafka-Cluster verwendet. Beispiel: Mit der Eingabe von **hdi** werden Cluster mit den Namen **source-hdi** und **dest-hdi** erstellt.

    * **Benutzername für Clusteranmeldung**: Der Administratorbenutzername für die Kafka-Quell- und -Zielcluster.

    * **Kennwort für Clusteranmeldung**: Das Administratorbenutzerkennwort für die Kafka-Quell- und -Zielcluster.

    * **SSH-Benutzername**: Der SSH-Benutzer, der für die Kafka-Quell- und -Zielcluster erstellt werden soll.

    * **SSH-Kennwort**: Das Kennwort für den SSH-Benutzer für die Kafka-Quell- und -Zielcluster.

3. Lesen Sie die **Geschäftsbedingungen**, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu**.

4. Aktivieren Sie zum Schluss **An Dashboard anheften**, und wählen Sie dann **Kaufen** aus. Das Erstellen der Cluster dauert ca. 20 Minuten.

> [!IMPORTANT]  
> Die Namen der HDInsight-Cluster lauten **source-BASENAME** und **dest-BASENAME**, wobei BASENAME der Name ist, den Sie für die Vorlage angegeben haben. Sie verwenden diese Namen in späteren Schritten, wenn Sie eine Verbindung mit den Clustern herstellen.

## <a name="create-topics"></a>Erstellen von Themen

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem **Quell**cluster her:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Ersetzen Sie **sshuser** durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie **BASENAME** durch den Basisnamen, der beim Erstellen des Clusters verwendet wurde.

    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie die folgenden Befehle, um nach den Apache Zookeeper-Hosts für den Quellcluster zu suchen:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ersetzen Sie `$CLUSTERNAME` durch den Namen des Quellclusters. Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein.

3. Um ein Thema mit Namen `testtopic` zu erstellen, nutzen Sie den folgenden Befehl:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Verwenden Sie den folgenden Befehl, um zu bestätigen, dass das Thema erstellt wurde:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    Die Antwort enthält `testtopic`.

4. Verwenden Sie Folgendes, um die Informationen zum Zookeeper-Host für diesen Cluster (**Quell**cluster) anzuzeigen:

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Speichern Sie diese Informationen. Sie werden im nächsten Abschnitt verwendet.

## <a name="configure-mirroring"></a>Konfigurieren der Spiegelung

1. Stellen Sie in einer anderen SSH-Sitzung eine Verbindung mit dem **Zielcluster** her:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Ersetzen Sie **sshuser** durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie **BASENAME** durch den Basisnamen, der beim Erstellen des Clusters verwendet wurde.

    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Mit einer `consumer.properties`-Datei wird die Kommunikation mit dem **Quell**cluster konfiguriert. Verwenden Sie zum Erstellen der Datei den folgenden Befehl:

    ```bash
    nano consumer.properties
    ```

    Verwenden Sie als Inhalt der Datei `consumer.properties` den folgenden Text:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Ersetzen Sie **SOURCE_ZKHOSTS** durch die Informationen zu Zookeeper-Hosts aus dem **Quell**cluster.

    In dieser Datei werden die Consumerinformationen beschrieben, die beim Lesen vom Kafka-Quellcluster verwendet werden sollten. Weitere Informationen zur Consumerkonfiguration finden Sie unter [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Consumerkonfigurationen) bei „kafka.apache.org“.

    Drücken Sie zum Speichern der Datei **STRG+X**, **Y** und dann die **EINGABETASTE**.

3. Bevor Sie den Producer konfigurieren, der mit dem Zielcluster kommuniziert, müssen Sie nach den Brokerhosts für den **Ziel**cluster suchen. Verwenden Sie die folgenden Befehle, um diese Informationen abzurufen:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Ersetzen Sie `$CLUSTERNAME` durch den Namen des Zielclusters. Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein.

    Die Ausgabe des `echo`-Befehls sieht in etwa wie folgt aus:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Eine `producer.properties`-Datei kommuniziert den __Ziel__cluster. Verwenden Sie zum Erstellen der Datei den folgenden Befehl:

    ```bash
    nano producer.properties
    ```

    Verwenden Sie als Inhalt der Datei `producer.properties` den folgenden Text:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Ersetzen Sie **DEST_BROKERS** durch die Brokerinformationen aus dem vorherigen Schritt.

    Weitere Informationen zur Producerkonfiguration finden Sie unter [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Producerkonfigurationen) bei „kafka.apache.org“.

5. Verwenden Sie die folgenden Befehle, um nach den Zookeeper-Hosts für den Zielcluster zu suchen:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ersetzen Sie `$CLUSTERNAME` durch den Namen des Zielclusters. Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein.

7. Die Standardkonfiguration für Kafka auf HDInsight erlaubt keine automatische Erstellung von Themen. Bevor Sie mit der Spiegelung beginnen, müssen Sie sich für eine der folgenden Optionen entscheiden:

    * **Erstellen der Themen im Zielcluster**: Bei dieser Option haben Sie auch die Möglichkeit, die Anzahl von Partitionen und den Replikationsfaktor festzulegen.

        Mit folgendem Befehl können Sie Themen vorab erstellen:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Ersetzen Sie `testtopic` durch den Namen des zu erstellenden Themas.

    * **Konfigurieren des Clusters für die automatische Themaerstellung**: Bei dieser Option kann MirrorMaker zum automatischen Erstellen von Themen verwendet werden. Die Themen werden jedoch möglicherweise mit einer unterschiedlichen Anzahl von Partitionen bzw. einem anderen Replikationsfaktor als im Quellthema erstellt.

        Um den Zielcluster für das automatische Erstellen von Themen zu konfigurieren, führen Sie diese Schritte aus:

        1. Wählen Sie im [Azure-Portal](https://portal.azure.com) den Kafka-Zielcluster aus.
        2. Wählen Sie in der Clusterübersicht die Option __Clusterdashboard__. Wählen Sie dann __HDInsight-Clusterdashboard__. Bei Aufforderung authentifizieren Sie sich mit den Anmeldeinformationen (Admin) für den Cluster.
        3. Wählen Sie auf der linken Seite aus der Liste den Dienst __Kafka__ aus.
        4. Klicken Sie in der Mitte der Seite auf __Konfigurationen__.
        5. Geben Sie in das Feld __Filter__ den Wert `auto.create` ein. Dies filtert die Liste der Eigenschaften und zeigt die Einstellung `auto.create.topics.enable`.
        6. Ändern Sie den Wert von `auto.create.topics.enable` in „true“, und wählen Sie dann __Speichern__. Fügen Sie einen Hinweis hinzu, und wählen Sie dann erneut __Speichern__.
        7. Wählen Sie den Dienst __Kafka__, dann die Option __Neu starten__ und abschließend die Option __Neustart aller betroffenen__. Klicken Sie bei entsprechender Aufforderung auf __Neustart aller Dienste bestätigen__.

## <a name="start-mirrormaker"></a>Starten von MirrorMaker

1. Verwenden Sie für den Weg von der SSH-Verbindung zum **Ziel**cluster den folgenden Befehl, um den MirrorMaker-Prozess zu starten:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    Die in diesem Beispiel verwendeten Parameter sind:

    * **--consumer.config**: Gibt die Datei an, in der die Consumereigenschaften enthalten sind. Diese Eigenschaften werden verwendet, um einen Consumer zu erstellen, mit dem aus dem Kafka-*Quell*cluster gelesen wird.

    * **--producer.config**: Gibt die Datei an, in der die Producereigenschaften enthalten sind. Diese Eigenschaften werden zum Erstellen eines Producers verwendet, mit dem in den Kafka-*Ziel*cluster geschrieben wird.

    * **--whitelist**: Eine Liste mit Themen, die von MirrorMaker aus dem Quellcluster am Zielort repliziert werden.

    * **--num.streams**: Die Anzahl von Consumerthreads, die erstellt werden sollen.

   Nach dem Starten gibt MirrorMaker Informationen zurück, die dem folgenden Text ähneln:

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Verwenden Sie für den Weg von der SSH-Verbindung zum **Quell**cluster den folgenden Befehl, um einen Producer zu starten und Nachrichten an das Thema zu senden:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Ersetzen Sie `$CLUSTERNAME` durch den Namen des Quellclusters. Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein.

     Sobald der Cursor an einer leeren Zeile steht, geben Sie einige Textnachrichten ein. Diese Nachrichten werden an das Thema im **Quell**cluster gesendet. Drücken Sie anschließend **STRG + C**, um den Producer-Prozess zu beenden.

3. Drücken Sie für die SSH-Verbindung zum **Zielcluster** **STRG + C**, um den MirrorMaker-Prozess zu beenden. Es kann einige Sekunden in Anspruch nehmen, um den Prozess zu beenden. Um sicherzustellen, dass die Nachrichten am Ziel repliziert wurden, verwenden Sie den folgenden Befehl:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Ersetzen Sie `$CLUSTERNAME` durch den Namen des Zielclusters. Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein.

    Die Themenliste enthält nun auch das Thema `testtopic`, das erstellt wurde, als MirrorMaster das Thema vom Quellcluster an das Ziel gespiegelt hat. Die aus dem Thema abgerufenen Nachrichten sind identisch mit den im Quellcluster eingegebenen Nachrichten.

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Da mit den Schritten in diesem Dokument beide Cluster in derselben Azure-Ressourcengruppe erstellt werden, können Sie die Ressourcengruppe im Azure-Portal löschen. Durch das Löschen der Ressourcengruppe werden alle beim Durcharbeiten dieses Dokuments erstellten Ressourcen sowie das virtuelle Azure-Netzwerk und das von den Clustern verwendete Speicherkonto entfernt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) zur Erstellung eines Replikats eines [Apache Kafka](https://kafka.apache.org/)-Clusters verwendet wird. Verwenden Sie die folgenden Links, um weitere Möglichkeiten zur Arbeit mit Kafka kennenzulernen:

* [Dokumentation zu Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) auf cwiki.apache.org.
* [Erste Schritte mit Apache Kafka in HDInsight](apache-kafka-get-started.md)
* [Verwenden von Apache Spark mit Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verwenden von Apache Storm mit Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Herstellen einer Verbindung mit Apache Kafka über Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)
