---
title: Apache Kafka mit Schemaregistrierung von Confluent in Azure HDInsight
description: Stellen Sie einen von HDInsight verwalteten Kafka-Cluster mit einem Edgeknoten in einem virtuellen Netzwerk bereit, und installieren Sie anschließend die Schemaregistrierung von Confluent auf dem Edgeknoten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500321"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka mit Schemaregistrierung von Confluent in Azure HDInsight

Die Schemaregistrierung von Kafka enthält Serialisierungsmodule als Plug-Ins für Apache Kafka-Clients, für die die Speicherung und der Abruf von Nachrichtenschemas für Kafka-Nachrichten durchgeführt werden, die im Avro-Format gesendet werden. Anfänglich hat es sich um ein OSS-Projekt von Confluent gehandelt, aber nun gilt hierfür die [Confluent-Communitylizenz](https://www.confluent.io/blog/license-changes-confluent-platform/). Darüber hinaus dient die Schemaregistrierung auch den folgenden Zwecken:

* Speichern und Abrufen von Schemas für Producer und Consumer
* Erzwingen der Abwärts-, Vorwärts- und vollständigen Kompatibilität für Themen
* Verringern der Größe von Nutzdaten, die an Kafka gesendet werden

In einem von HDInsight verwalteten Kafka-Cluster wird die Schemaregistrierung normalerweise auf einem Edgeknoten bereitgestellt, um die Trennung der Computeressourcen von den Hauptknoten zu ermöglichen.

Unten ist eine gängige Architektur für die Bereitstellung der Schemaregistrierung in einem HDInsight-Cluster angegeben. Von der Schemaregistrierung wird standardmäßig eine REST-API für darin durchgeführte Vorgänge verfügbar gemacht.  Producer und Consumer können mit der Schemaregistrierung aus dem VNET oder per [Kafka-REST-Proxy](rest-proxy.md) interagieren.

![HDInsight-Kafka-Schemaregistrierung](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Bereitstellen eines mit HDInsight verwalteten Kafka-Clusters mit einer Schemaregistrierung von Confluent

In diesem Abschnitt stellen wir einen von HDInsight verwalteten Kafka-Cluster mit einem Edgeknoten in einem virtuellen Netzwerk bereit und installieren anschließend die Schemaregistrierung von Confluent auf dem Edgeknoten.  

1. Wählen Sie unten die Schaltfläche **In Azure bereitstellen** aus, um sich bei Azure anzumelden und die Resource Manager-Vorlage zu öffnen.

    [![In Azure bereitstellen](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. Füllen Sie die Felder in der Vorlage für die benutzerdefinierte Bereitstellung wie unten beschrieben aus:

    |Eigenschaft |Beschreibung |
    |---|---|
    |Subscription|Wählen Sie in der Dropdownliste das Azure-Abonnement aus, das für den Cluster verwendet wird.|
    |Resource group|Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
    |Region|Wählen Sie in der Dropdownliste eine Region für die Erstellung des Clusters aus.|
    |Clustername|Geben Sie einen global eindeutigen Namen ein. Oder lassen Sie die Option unverändert, um den Standardnamen zu verwenden.|
    |Benutzername für Clusteranmeldung|Geben Sie den Benutzernamen an, der Standardwert ist **admin**.|
    |Kennwort für Clusteranmeldung|Geben Sie das Kennwort an.|
    |SSH-Benutzername|Geben Sie den Benutzernamen an. Der Standardwert ist **sshuser**.|
    |SSH-Kennwort|Geben Sie das Kennwort an.|

    Lassen Sie die anderen Felder unverändert. Wählen Sie zum Fortfahren **Überprüfen + erstellen** aus.

1. Überprüfen Sie die Bereitstellungsdetails, und wählen Sie dann die Option **Erstellen** aus, um die Bereitstellung zu initialisieren. Die Bereitstellung kann ggf. 45 Minuten dauern.

## <a name="configure-the-confluent-schema-registry"></a>Konfigurieren der Schemaregistrierung von Confluent

In diesem Abschnitt konfigurieren wir die Kafka-Schemaregistrierung von Confluent, die wir auf dem Edgeknoten installiert haben. Die Schemaregistrierung von Confluent befindet sich unter `/etc/schema-registry/schema-registry.properties`, und die Mechanismen zum Starten und Beenden von ausführbaren Dienstdateien finden Sie im Ordner `/usr/bin/`.

Die Schemaregistrierung muss über die Information verfügen, dass der Zookeeper-Dienst mit dem HDInsight-Kafka-Cluster interagieren kann. Führen Sie die unten angegebenen Schritte aus, um die Details zum Zookeeper-Quorum zu erhalten.

1. Verwenden Sie einen [ssh-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit Ihrem Edgeknoten des Clusters. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Richten Sie eine Kennwortvariable ein. Ersetzen Sie PASSWORD durch das Kennwort für die Clusteranmeldung, und geben Sie dann den folgenden Befehl ein:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahieren Sie den Clusternamen mit korrekter Groß-/Kleinschreibung. Führen Sie den folgenden Befehl aus:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Extrahieren Sie die Kafka Zookeeper-Hosts. Führen Sie den folgenden Befehl aus:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Notieren Sie sich diesen Wert, da Sie ihn später noch benötigen.

1. Extrahieren Sie die Kafka-Brokerhosts. Führen Sie den folgenden Befehl aus:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Öffnen Sie die Eigenschaftendateien der Schemaregistrierung im Bearbeitungsmodus. Führen Sie den folgenden Befehl aus:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Aktualisieren Sie den Wert für `kafkastore.connection.url` mit der weiter oben ermittelten Zookeeper-Zeichenfolge.
    1. Aktualisieren Sie den Wert für `debug` in `true`.

    Die Eigenschaftendatei sieht nun in etwa wie folgt aus:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Drücken Sie zum Speichern der Datei **STRG+X**, **Y** und dann die **EINGABETASTE**.

1. Starten Sie die Schemaregistrierung, und legen Sie per Verweis fest, dass die aktualisierte Eigenschaftendatei der Schemaregistrierung verwendet werden soll. Führen Sie die folgenden Befehle aus:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Starten Sie ein weiteres SSH-Fenster, während die Schemaregistrierung in einer SSH-Sitzung ausgeführt wird.  Registrieren Sie eine neue Version eines Schemas unter dem Betreff „Kafka-key“, und notieren Sie sich die Ausgabe:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Registrieren Sie eine neue Version eines Schemas unter dem Betreff „Kafka-value“, und notieren Sie sich die Ausgabe:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Listen Sie alle Betreffeinträge auf, und überprüfen Sie die Ausgabe:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Sie können bei Bedarf auch einige andere erweiterte Befehle ausprobieren, die auf [dieser Seite](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart) aufgeführt sind.

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Senden und Nutzen von Avro-Daten aus Kafka per Schemaregistrierung

In diesem Abschnitt lesen wir Daten aus der Standardeingabe und schreiben sie in einem bestimmten Format in ein Kafka-Thema. Anschließend lesen wir die Daten aus den Kafka-Themen aus, indem wir einen Consumer mit einem Avro-Formatierer verwenden, um sie in ein lesbares Format umzuwandeln.

1. Erstellen Sie das Kafka-Thema `agkafkaschemareg`.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Verwenden Sie den **Kafka Avro-Konsolenproducer**, um ein Schema zu erstellen, das Schema dem Thema zuzuweisen und mit dem Senden von Daten an das Thema im Avro-Format zu beginnen. Stellen Sie sicher, dass das Kafka-Thema im vorherigen Schritt erfolgreich erstellt wurde und dass **$KAFKABROKERS** einen Wert enthält.

    Das von uns gesendete Schema ist ein Schlüssel-Wert-Paar mit dem Schlüssel: Und dem Wert:

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Verwenden Sie den folgenden Befehl, um den **Kafka Avro-Konsolenproducer** zu starten:

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Wenn der Producer zum Akzeptieren von Nachrichten bereit ist, können Sie mit dem Senden der Nachrichten im vordefinierten Avro-Schemaformat beginnen. Verwenden Sie die TAB-TASTE, um einen Abstand zwischen dem Schlüssel und dem Wert einzufügen.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Versuchen Sie, zufällige Nicht-Schemadaten in den Konsolenproducer einzugeben. So können Sie verfolgen, dass vom Producer keine Daten zugelassen werden, die nicht dem vordefinierten Avro-Schema entsprechen.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Starten Sie auf einem anderen Bildschirm den Kafka Avro-Konsolenconsumer.

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Die folgende Ausgabe sollte angezeigt werden:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der Apache Kafka Producer- und Consumer-APIs](apache-kafka-producer-consumer-api.md)