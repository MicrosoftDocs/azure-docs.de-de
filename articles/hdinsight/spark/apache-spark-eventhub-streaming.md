---
title: 'Tutorial: Verarbeiten von Daten aus Azure Event Hubs mit Apache Spark in Azure HDInsight '
description: Stellen Sie eine Verbindung zwischen Apache Spark in Azure HDInsight und Azure Event Hubs her, um Streamingdaten zu verarbeiten.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: bf6a728f66f5eeaf5983ecd50d65326a59cc3267
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066192"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Tutorial: Verarbeiten von Tweets mit Azure Event Hubs und Apache Spark in HDInsight

In diesem Tutorial erfahren Sie, wie Sie eine [Apache Spark](https://spark.apache.org/)-Streaminganwendung erstellen, um Tweets an einen Azure Event Hub zu senden. Sie erstellen außerdem eine zweite Anwendung, die die Tweets aus dem Event Hub liest. Eine ausführliche Erläuterung des Spark-Streamings finden Sie unter [Apache Spark streaming overview](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview) (Übersicht über das Apache Spark-Streaming). HDInsight stellt einem Spark-Cluster in Azure dieselben Streamingfeatures bereit.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Senden von Nachrichten an Azure Event Hub
> * Lesen von Nachrichten aus Azure Event Hub

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Spark-Cluster unter HDInsight. Weitere Informationen finden Sie unter [Erstellen eines Apache Spark-Clusters](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kenntnisse im Umgang mit Jupyter Notebooks mit Spark in HDInsight. Weitere Informationen finden Sie unter [Tutorial: Laden von Daten und Ausführen von Abfragen auf einem Apache Spark-Cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* Ein [Twitter-Konto](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Erstellen einer Twitter-Anwendung

Für den Empfang eines Datenstroms mit Tweets erstellen Sie eine Anwendung in Twitter. Gehen Sie gemäß der Anleitung vor, um eine Twitter-Anwendung zu erstellen, und notieren Sie die Werte, die Sie für dieses Tutorial benötigen.

1. Navigieren Sie zu [Twitter Application Management](https://apps.twitter.com/).

1. Wählen Sie **Create New App** (Neue App erstellen) aus.

1. Geben Sie außerdem die folgenden Werte an:

    |Eigenschaft |Wert |
    |---|---|
    |NAME|Geben Sie einen Anwendungsnamen an. Der für dieses Tutorial verwendete Wert lautet **HDISparkStreamApp0423**. Dieser Name muss eindeutig sein.|
    |BESCHREIBUNG|Geben Sie eine kurze Beschreibung der Anwendung an. Der für dieses Tutorial verwendete Wert lautet **Eine einfache HDInsight Spark-Streaminganwendung**.|
    |Website|Geben Sie die Website der Anwendung an. Es muss keine gültige Website sein.  Der für dieses Tutorial verwendete Wert lautet **http://www.contoso.com** .|
    |Rückruf-URL|Dies können Sie leer lassen.|

1. Wählen Sie **Yes, I have read and agree to the Twitter Developer Agreement** (Ja, ich habe die Twitter-Vereinbarung für Entwickler gelesen und stimme ihr zu) und danach **Create your Twitter application** (Twitter-Anwendung erstellen) aus.

1. Wählen Sie die Registerkarte **Keys and Access Tokens** .

1. Wählen Sie am Ende der Seite **Create my access token** (Zugriffstoken erstellen) aus.

1. Notieren Sie sich die folgenden Werte von der Seite.  Sie benötigen diese Werte später im Tutorial:

    - **Consumer Key (API Key)** (Consumerschlüssel [API-Schlüssel])    
    - **Consumer Secret (API Secret)** (Consumergeheimnis [API-Geheimnis])  
    - **Zugriffstoken**
    - **Access Token Secret** (Zugriffstokengeheimnis)   

## <a name="create-an-azure-event-hubs-namespace"></a>Erstellen eines Azure Event Hubs-Namespace

Sie verwenden diesen Event Hub zum Speichern von Tweets.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

2. Wählen Sie im Menü links **Alle Dienste** aus.  

3. Wählen Sie unter **INTERNET DER DINGE** **Event Hubs** aus. 

    ![Erstellen einer Event Hub-Instanz für das Spark-Streamingbeispiel](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Erstellen einer Event Hub-Instanz für das Spark-Streamingbeispiel")

4. Wählen Sie **+ Hinzufügen**.

5. Geben Sie die folgenden Werte für den neuen Event Hubs-Namespace ein:

    |Eigenschaft |Wert |
    |---|---|
    |NAME|Geben Sie einen Namen für den Event Hub an.  Der für dieses Tutorial verwendete Wert lautet **myeventhubns20180403**.|
    |Tarif|Wählen Sie **Standard** aus.|
    |Abonnement|Wählen Sie Ihr entsprechendes Abonnement aus.|
    |Ressourcengruppe|Wählen Sie eine vorhandene Ressourcengruppe aus der Dropdownliste aus, oder wählen Sie die Option **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen.|
    |Location|Wählen Sie den gleichen **Speicherort** wie für Ihren Apache Spark-Cluster in HDInsight aus, um Latenz und Kosten zu reduzieren.|
    |Automatische Vergrößerung aktivieren (optional) |Die „Automatische Vergrößerung“ skaliert die Anzahl der Durchsatzeinheiten, die Ihrem Event Hubs-Namespace zugewiesen werden, wenn Ihr Datenverkehr die Kapazität der zugewiesenen Durchsatzeinheiten überschreitet.  |
    |Maximale Durchsatzeinheiten für die automatische Vergrößerung (optional)|Dieser Schieberegler wird nur angezeigt, wenn Sie **Automatische Vergrößerung aktivieren** aktivieren.  |

    ![Angeben eines Event Hub-Namens für das Spark-Streamingbeispiel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Angeben eines Event Hub-Namens für das Spark-Streamingbeispiel")

6. Klicken Sie auf **Erstellen**, um den Namespace zu erstellen.  Die Bereitstellung wird in wenigen Minuten abgeschlossen.

## <a name="create-an-azure-event-hub"></a>Erstellen eines Azure-Event Hubs
Erstellen Sie einen Event Hub, nachdem der Event Hubs-Namespace bereitgestellt wurde.  Im Portal:

1. Wählen Sie im Menü links **Alle Dienste** aus.  

1. Wählen Sie unter **INTERNET DER DINGE** **Event Hubs** aus.  

1. Wählen Sie Ihren Event Hubs-Namespace in der Liste aus.  

1. Wählen Sie auf der Seite **Event Hubs-Namespace** **+ Event Hub** aus.  
1. Geben Sie die folgenden Werte auf der Seite **Event Hub erstellen** ein:

    - **Name**: Geben Sie einen Namen für den Event Hub ein. 
 
    - **Partitionsanzahl**: 10.  

    - **Nachrichtenaufbewahrung**: 1.   
   
      ![Angeben von Event Hub-Informationen für das Spark-Streamingbeispiel](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Angeben von Event Hub-Informationen für das Spark-Streamingbeispiel")

1. Klicken Sie auf **Erstellen**.  Die Bereitstellung sollte in wenigen Sekunden abgeschlossen sein, und Sie kehren zur Seite „Event Hubs-Namespace“ zurück.

1. Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.

1. Wählen Sie **RootManageSharedAccessKey**.
    
     ![Festlegen von Event Hub-Richtlinien für das Spark-Streamingbeispiel](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Festlegen von Event Hub-Richtlinien für das Spark-Streamingbeispiel")

1. Speichern Sie die Werte für **Primärschlüssel** und **Verbindungszeichenfolge – Primärschlüssel**, um sie später im Tutorial zu verwenden.

     ![Anzeigen der Event Hub-Richtlinienschlüssel für das Spark-Streamingbeispiel](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Anzeigen der Event Hub-Richtlinienschlüssel für das Spark-Streamingbeispiel")


## <a name="send-tweets-to-the-event-hub"></a>Senden von Tweets an den Event Hub

Erstellen Sie ein Jupyter-Notebook mit dem Namen **SendTweetsToEventHub**. 

1. Führen Sie folgenden Code aus, um die externen Apache Maven-Bibliotheken hinzuzufügen:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Bearbeiten Sie den Code, indem Sie `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` und `<TOKEN SECRET>` mit den entsprechenden Werten ersetzen. Führen Sie den bearbeiteten Code aus, um Tweets an Ihren Event Hub zu senden:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Öffnen Sie den Event Hub im Azure-Portal.  In der **Übersicht** sehen Sie einige Diagramme, die an den Event Hub gesendete Nachrichten zeigen.

## <a name="read-tweets-from-the-event-hub"></a>Lesen von Tweets aus dem Event Hub

Erstellen Sie ein weiteres Jupyter-Notebook mit dem Namen **ReadTweetsFromEventHub**. 

1. Führen Sie folgenden Code aus, um eine externen Apache Maven-Bibliothek hinzuzufügen:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```

2. Bearbeiten Sie den Code, indem Sie `<Event hub name>` und `<Event hub namespace connection string>` mit den entsprechenden Werten ersetzen. Führen Sie den bearbeiteten Code aus, um Tweets aus Ihrem Event Hub zu lesen:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Mit HDInsight werden Ihre Daten in Azure Storage oder Azure Data Lake Storage gespeichert, sodass Sie einen Cluster ohne Weiteres löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Wenn Sie vorhaben, sofort mit dem nächsten Tutorial fortzufahren, sollten Sie den Cluster beibehalten, andernfalls können Sie ihn nun löschen.

Öffnen Sie den Cluster im Azure-Portal, und wählen Sie **Löschen** aus.

![Löschen eines HDInsight-Clusters](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Löschen eines HDInsight-Clusters")

Sie können auch den Namen der Ressourcengruppe auswählen, um die Seite für die Ressourcengruppe zu öffnen, und dann **Ressourcengruppe löschen** auswählen. Indem Sie die Ressourcengruppe löschen, löschen Sie sowohl den HDInsight Spark-Cluster als auch das Standardspeicherkonto.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine Apache Spark-Streaminganwendung erstellen, um Tweets an einen Azure Event Hub zu senden. Sie erstellten außerdem eine zweite Anwendung, die die Tweets aus dem Event Hub liest.  Fahren Sie mit dem nächsten Artikel fort, um zu erfahren, wie Sie eine Anwendung für maschinelles Lernen erstellen.

> [!div class="nextstepaction"]
> [Erstellen einer Machine Learning-Anwendung](./apache-spark-ipython-notebook-machine-learning.md)
