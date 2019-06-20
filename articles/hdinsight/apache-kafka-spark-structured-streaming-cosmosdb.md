---
title: 'Strukturiertes Apache Spark-Streaming von Apache Kafka zu Azure Cosmos DB: Azure HDInsight'
description: Erfahren Sie, wie Sie strukturiertes Apache Spark-Streaming verwenden, um Daten aus Apache Kafka zu lesen und in Azure Cosmos DB zu speichern. In diesem Beispiel streamen Sie Daten mithilfe eines Jupyter Notebooks aus Spark auf HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: c2f3d882ac01427ea017d4f9b81edc3c64cc932d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728719"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka und Azure Cosmos DB

Erfahren Sie, wie Sie [strukturiertes](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) [Apache Spark](https://spark.apache.org/)-Streaming verwenden, um Daten aus [Apache Kafka](https://kafka.apache.org/) in Azure HDInsight zu lesen und dann in Azure Cosmos DB zu speichern.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ist eine global verteilte Datenbank mit Unterstützung mehrerer Modelle. Dieses Beispiel verwendet ein SQL-API-Datenbankmodell. Weitere Informationen finden Sie im Dokument [Willkommen bei Azure Cosmos DB](../cosmos-db/introduction.md).

Strukturiertes Spark-Streaming ist eine auf Spark SQL basierende Stream-Verarbeitungs-Engine. Sie können damit Streamingberechnungen genauso wie Batchberechnung auf statischen Daten ausdrücken. Weitere Informationen zu strukturiertem Streaming finden Sie unter [Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) (Programmierhandbuch zu strukturiertem Streaming) auf Apache.org.

> [!IMPORTANT]  
> In diesem Beispiel wird Spark 2.2 auf HDInsight 3.6 verwendet.
>
> Mit den in diesem Dokument beschriebenen Schritten wird eine Azure-Ressourcengruppe erstellt, die jeweils einen Spark- und einen Kafka-Cluster in HDInsight beinhaltet. Die Cluster befinden sich innerhalb eines virtuellen Azure-Netzwerks, wodurch Spark- und Kafka-Cluster direkt miteinander kommunizieren können.
>
> Denken Sie nach dem Ausführen der Schritte in diesem Dokument daran, die Cluster zu löschen, um das Anfallen von Gebühren zu verhindern.

## <a name="create-the-clusters"></a>Erstellen von Clustern

Apache Kafka in HDInsight ermöglicht keinen Zugriff auf die Kafka-Broker über das öffentliche Internet. Komponenten, die mit Kafka kommunizieren, müssen sich jeweils im selben virtuellen Azure-Netzwerk befinden wie die Knoten im Kafka-Cluster. Für dieses Beispiel sind die Kafka- und Spark-Cluster in einem virtuellen Azure-Netzwerk angeordnet. Im folgenden Diagramm ist dargestellt, wie der Kommunikationsfluss zwischen den Clustern abläuft:

![Diagramm der Spark- und Kafka-Cluster in einem virtuellen Azure-Netzwerk](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Der Kafka-Dienst ist auf die Kommunikation innerhalb des virtuellen Netzwerks beschränkt. Auf andere Dienste auf dem Cluster, wie z.B. SSH und Ambari, kann über das Internet zugegriffen werden. Weitere Informationen zu den öffentlichen Ports, die für HDInsight verfügbar sind, finden Sie unter [Von HDInsight verwendete Ports und URIs](hdinsight-hadoop-port-settings-for-services.md).

Es ist zwar möglich, ein virtuelles Azure-Netzwerk, einen Kafka-Cluster und einen Spark-Cluster manuell zu erstellen, aber mit einer Azure Resource Manager-Vorlage ist dies erheblich einfacher. Führen Sie die folgenden Schritte aus, um ein virtuelles Azure-Netzwerk, Kafka- und Spark-Cluster für Ihr Azure-Abonnement bereitzustellen.

1. Verwenden Sie die folgende Schaltfläche, um sich bei Azure anzumelden, und öffnen Sie die Vorlage im Azure-Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

    Die Azure Resource Manager-Vorlage befindet sich im GitHub-Repository für dieses Projekt ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Diese Vorlage erstellt die folgenden Ressourcen:

   * Ein Kafka-auf-HDInsight-3.6-Cluster.

   * Ein Spark-auf-HDInsight-3.6-Cluster.

   * Ein Azure Virtual Network, das die HDInsight-Cluster enthält.

       > [!NOTE]  
       > Das von der Vorlage erstellte virtuelle Netzwerk verwendet den 10.0.0.0/16-Adressraum.

   * Eine Azure Cosmos DB-SQL-API-Datenbank.

     > [!IMPORTANT]  
     > Das in diesem Beispiel für strukturiertes Streaming verwendete Notebook benötigt Spark auf HDInsight 3.6. Bei Verwendung einer früheren Version von Spark auf HDInsight erhalten Sie Fehlermeldungen, wenn Sie das Notebook verwenden.

2. Verwenden Sie die folgenden Informationen, um die Einträge auf dem Abschnitt **Benutzerdefinierte Bereitstellung** aufzufüllen:
   
    ![Benutzerdefinierte HDInsight-Bereitstellung](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement.
   
    * **Ressourcengruppe:** : Erstellen Sie eine Gruppe, oder wählen Sie eine vorhandene Gruppe aus. Diese Gruppe enthält den HDInsight-Cluster.

    * **Standort**: Wählen Sie einen Standort in Ihrer Nähe aus.

    * **Cosmos DB-Kontoname**: Dieser Wert wird als Name für das Cosmos DB-Konto verwendet.

    * **Basisclustername**: Dieser Wert wird als Basisname für Spark- und Kafka-Cluster verwendet. Wenn Sie beispielsweise **myhdi** eingeben, werden ein Spark-Cluster mit dem Namen __spark-myhdi__ und ein Kafka-Cluster mit dem Namen **kafka-myhdi** erstellt.

    * **Clusterversion**: Die HDInsight-Clusterversion.

        > [!IMPORTANT]  
        > Dieses Beispiel wird mit HDInsight 3.6 getestet und funktioniert möglicherweise nicht mit anderen Clustern.

    * **Benutzername für Clusteranmeldung**: Der Administratorbenutzername für die Spark- und Kafka-Cluster.

    * **Kennwort für Clusteranmeldung**: Das Administratorbenutzerkennwort für die Spark- und Kafka-Cluster.

    * **SSH-Benutzername**: SSH-Benutzer, der für die Spark- und Kafka-Cluster erstellt wird.

    * **SSH-Kennwort**: Kennwort für den SSH-Benutzer für die Spark- und Kafka-Cluster.

3. Lesen Sie die **Geschäftsbedingungen**, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu**.

4. Wählen Sie abschließend **Kaufen** aus. Das Erstellen der Cluster dauert ca. 20 Minuten.

> [!IMPORTANT]  
> Das Erstellen des Clusters, des virtuellen Netzwerks und des Cosmos DB-Kontos kann bis zu 45 Minuten dauern.

## <a name="create-the-cosmos-db-database-and-collection"></a>Erstellen der Cosmos DB-Datenbank und -Sammlung

Das in diesem Dokument verwendete Projekt speichert Daten in Cosmos DB. Bevor Sie den Code ausführen, müssen Sie zuerst eine _Datenbank_ und _Sammlung_ in Ihrer Cosmos DB-Instanz erstellen. Sie müssen auch den Dokumentendpunkt abrufen und den _Schlüssel_, der zum Authentifizieren von Anforderungen an Cosmos DB verwendet wird. 

Hierfür bietet sich die [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) an. Das folgende Skript erstellt eine Datenbank namens `kafkadata` und eine Sammlung mit dem Namen `kafkacollection`. Anschließend gibt sie den primären Schlüssel zurück.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

Dokumentendpunkt und Primärschlüsselinformationen entsprechen folgendem Text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Speichern Sie den Endpunkt und die Schlüsselwerte, da sie in den Jupyter Notebooks benötigt werden.

## <a name="get-the-apache-kafka-brokers"></a>Abrufen der Apache Kafka-Broker

Der Code in diesem Beispiel stellt eine Verbindung mit den Kafka-Broker-Hosts im Kafka-Cluster her. Verwenden Sie das folgende PowerShell- oder Bashbeispiel, um die Adressen der beiden Kafka-Broker-Hosts zu finden:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> Das Bashbeispiel erwartet, dass `$CLUSTERNAME` den Namen des Kafka-Clusters enthält.
>
> Dieses Beispiel verwendet das [jq](https://stedolan.github.io/jq/)-Hilfsprogramm, um Daten aus dem JSON-Dokument zu analysieren.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Geben Sie bei der entsprechenden Aufforderung das Kennwort des Anmeldekontos (Administrator) für den Cluster ein.

Die Ausgabe sieht in etwa wie folgender Text aus:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Speichern Sie diese Informationen, da sie in den folgenden Abschnitten dieses Dokuments verwendet werden.

## <a name="get-the-notebooks"></a>Abrufen der Notebooks

Den Code für das in diesem Dokument beschriebene Beispiel finden Sie unter [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Hochladen der Notebooks

Befolgen Sie die folgenden Schritte, um die Notebooks aus dem Projekt auf Ihr Spark auf HDInsight-Cluster hochzuladen:

1. Verbinden Sie in Ihrem Webbrowser das Jupyter Notebook mit Ihrem Spark-Cluster. Ersetzen Sie in der folgenden URL `CLUSTERNAME` durch den Namen Ihres __Spark__-Clusters:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Geben Sie bei Aufforderung den Clusterbenutzernamen (Administrator) und das Kennwort ein, den bzw. das Sie beim Erstellen des Clusters verwendet haben.

2. Klicken Sie oben rechts auf der Seite auf die Schaltfläche __Hochladen__, um die Datei __Stream-taxi-data-to-kafka.ipynb__ auf den Cluster hochzuladen. Wählen Sie __Öffnen__, um das Hochladen zu starten.

3. Suchen Sie in der Liste der Notebooks nach dem Eintrag __Stream-taxi-data-to-kafka.ipynb__, und klicken Sie auf die Schaltfläche __Hochladen__ neben dem Eintrag.

4. Wiederholen Sie die Schritte 1–3, um das Notebook __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ zu laden.

## <a name="load-taxi-data-into-kafka"></a>Laden von Taxidaten in Kafka

Nachdem die Dateien hochgeladen wurden, wählen Sie den Eintrag __Stream-taxi-data-to-kafka.ipynb__, um das Notebook zu öffnen. Befolgen Sie die Schritte im Notebook, um Daten in Kafka zu laden.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Verarbeiten von Taxidaten mithilfe von strukturiertem Spark-Streaming

Wählen Sie auf der Startseite [Jupyter Notebook](https://jupyter.org/) den Eintrag __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ aus. Befolgen Sie die Schritte im Notebook, um Daten mithilfe des strukturierten Spark-Streamings aus Kafka in Azure Cosmos DB zu streamen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt wissen, wie Sie strukturiertes Apache Spark-Streaming verwenden, finden Sie in den folgenden Dokumenten weitere Informationen zum Arbeiten mit Apache Spark, Apache Kafka und Azure Cosmos DB:

* [Verwenden von Apache Spark-Streaming (DStream) mit Apache Kafka](hdinsight-apache-spark-with-kafka.md)
* [Erste Schritte mit Jupyter Notebook und Apache Spark unter HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Willkommen bei Azure Cosmos DB](../cosmos-db/introduction.md)
