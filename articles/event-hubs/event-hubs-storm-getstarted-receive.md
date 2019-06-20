---
title: Empfangen von Ereignissen mithilfe von Apache Storm – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zum Empfangen von Ereignissen aus Azure Event Hubs mit Apache Storm.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 75a96127c48186befc48b2240f78e49cd5914239
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60343415"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Empfangen von Ereignissen von Event Hubs mithilfe von Apache Storm

[Apache Storm](https://storm.incubator.apache.org) ist ein verteiltes System für Echtzeitberechnungen, das die zuverlässige Verarbeitung unbegrenzter Datenströme vereinfacht. In diesem Abschnitt wird gezeigt, wie ein Azure Event Hubs-Storm-Spout verwendet wird, um Ereignisse von Event Hubs zu empfangen. Mit Apache Storm können Sie Ereignisse auf mehrere Prozesse aufteilen, die in verschiedenen Knoten gehostet werden. Die Ereignis-Hub-Integration in Storm vereinfacht die Ereignisnutzung durch transparente Prüfung des Fortschritts mithilfe der Zookeeper Installation von Storm, der Verwaltung von permanenten Prüfpunkten und dem parallelen von Ereignissen von Ereignis-Hubs.

Weitere Informationen zu Empfangsmustern von Event Hubs finden Sie unter [Event Hubs – Übersicht][Event Hubs overview].

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit dem Schnellstart beginnen, **erstellen Sie einen Event Hubs-Namespace und einen Event Hub**. Verwenden Sie das [Azure-Portal](https://portal.azure.com), um einen Namespace vom Typ „Event Hubs“ zu erstellen, und rufen Sie die Verwaltungsanmeldeinformationen ab, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. 

## <a name="create-project-and-add-code"></a>Erstellen eines Projekts und Hinzufügen von Code

In diesem Tutorial wird eine [HDInsight Storm][HDInsight Storm]-Installation verwendet, in der der Event Hubs-Spout bereits verfügbar ist.

1. Gehen Sie wie unter [HDInsight Storm – Erste Schritte](../hdinsight/storm/apache-storm-overview.md) beschrieben vor, um einen neuen HDInsight-Cluster zu erstellen und über Remotedesktop eine Verbindung mit diesem herzustellen.
2. Kopieren Sie die Datei `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` in Ihre lokale Entwicklungsumgebung. Sie enthält events-storm-spout.
3. Führen Sie den folgenden Befehl aus, um das Paket im lokalen Maven-Speicher zu installieren. Auf diese Weise können Sie das Paket später als Verweis im Storm-Projekt hinzufügen.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Erstellen Sie in Eclipse ein neues Maven-Projekt. Klicken Sie hierzu auf **File** (Datei) > **New** (Neu) > **Project** (Projekt).
   
    ![„Datei“ > „Neu“ > „Projekt“][12]
5. Wählen Sie **Use default Workspace location** (Workspace-Standardort verwenden) aus, und klicken Sie anschließend auf **Next** (Weiter).
6. Wählen Sie den Archetyp **maven-archetype-quickstart** aus, und klicken Sie auf anschließend auf **Next**.
7. Fügen Sie eine Gruppen-ID (**GroupId**) und eine Artefakt-ID (**ArtifactId**) ein, und klicken Sie auf **Finish** (Fertig stellen).
8. Fügen Sie in der Datei **pom.xml** die folgenden Abhängigkeiten in den Knoten `<dependency>` ein.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. Erstellen Sie im Ordner **src** eine Datei namens **Config.properties**, und kopieren Sie den folgenden Inhalt, wobei Sie die Werte `receive rule key` und `event hub name` ersetzen:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Der Wert für **eventhub.receiver.credits** bestimmt, wie viele Ereignisse als Stapel verarbeitet werden, bevor sie für die Storm-Pipeline freigegeben werden. Der Einfachheit halber wird in diesem Beispiel der Wert auf 10 gesetzt. In einer Produktionsumgebung sollten normalerweise höhere Werte festgelegt werden, beispielsweise 1024.
10. Erstellen Sie mit dem folgenden Code eine neue Klasse namens **LoggerBolt** :
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Diese Storm-Klasse protokolliert den Inhalt der empfangenen Ereignisse. Sie kann problemlos erweitert werden, um Tupel in einem Speicherdienst speichern. Das [Beispiel für HDInsight Storm mit Event Hub] verfolgt denselben Ansatz, um Daten in Azure Storage und Power BI zu speichern.
11. Erstellen Sie mit dem folgenden Code eine neue Klasse mit dem Namen **LogTopology** :
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Diese Klasse erstellt einen neuen Ereignis-Hubs-Spout, und verwendet die Eigenschaften in der Konfigurationsdatei zum Instanziieren. Beachten Sie unbedingt, dass in diesem Beispiel eine der Anzahl der Partitionen auf dem Event Hub entsprechende die Anzahl von Spout-Aufgaben erstellt wird, um die maximale für diesen Event Hub zulässige Parallelität zu verwenden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs][Event Hubs overview]
* [Erstellen eines Event Hubs](event-hubs-create.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Beispiel für HDInsight Storm mit Event Hub]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
