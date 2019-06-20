---
title: 'Tutorial: Anomalieerkennung für Streamingdaten mit Azure Databricks'
description: Verwenden Sie die Anomalieerkennungs-API und Azure Databricks, um Ihre Daten auf Anomalien zu überwachen.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: a00ad2523c215fa54d7d19d8c9e923b621f3081a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791867"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Tutorial: Anomalieerkennung für Streamingdaten mit Azure Databricks

Microsoft Power BI Desktop ist eine kostenlose Anwendung, mit der Sie Ihre Daten vernetzen, transformieren und visualisieren können. Die Anomalieerkennungs-API ist eine Komponente von Azure Cognitive Services und bietet eine Möglichkeit zum Überwachen Ihrer Zeitreihendaten. In diesem Tutorial erfahren Sie, wie Sie die Anomalieerkennung für einen Datenstrom von Daten nahezu in Echtzeit mit Azure Databricks ausführen. Sie erfassen Twitter-Daten mit Azure Event Hubs und importieren sie mit dem Spark-Event Hubs-Connector in Azure Databricks. Anschließend verwenden Sie die API zum Erkennen von Anomalien in den gestreamten Daten. 

In der folgenden Abbildung ist der Anwendungsfluss dargestellt:

![Azure Databricks mit Event Hubs und Cognitive Services](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks mit Event Hubs und Cognitive Services")

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen eines Azure Databricks-Arbeitsbereichs
> * Erstellen eines Spark-Clusters in Azure Databricks
> * Erstellen einer Twitter-App für den Zugriff auf Streamingdaten
> * Erstellen von Notebooks in Azure Databricks
> * Anfügen von Bibliotheken für Event Hubs und die Twitter-API
> * Erstellen einer Anomalieerkennungsressource und Abrufen des Zugriffsschlüssels
> * Senden von Tweets an Event Hubs
> * Lesen von Tweets aus Event Hubs
> * Ausführen der Anomalieerkennung für Tweets

> [!Note]
> Dieses Tutorial erläutert einen Ansatz zum Implementieren der empfohlenen [Lösungsarchitektur](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) für die Anomalieerkennungs-API.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!Note]
> Dieses Tutorial kann nicht mit einem Schlüssel einer kostenlosen Testversion für die Anomalieerkennungs-API abgeschlossen werden. Navigieren Sie vor dem Erstellen des Clusters zu Ihrem Profil, und legen Sie für Ihr Abonnement die **nutzungsbasierte Bezahlung** fest, um für die Erstellung des Azure Databricks-Clusters ein kostenloses Konto zu verwenden. Weitere Informationen finden Sie unter [Kostenloses Azure-Konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure Event Hubs-Namespace](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) und Event Hub

- Die [Verbindungszeichenfolge](../../../event-hubs/event-hubs-get-connection-string.md) für den Zugriff auf den Event Hubs-Namespace. Die Verbindungszeichenfolge sollte ungefähr das folgende Format aufweisen:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Den Namen der SAS-Richtlinie und den Richtlinienschlüssel für Event Hubs.

Im [Schnellstart](../../../event-hubs/event-hubs-create.md) zu Azure Event Hubs finden Sie Informationen zum Erstellen eines Namespace und einer Event Hubs-Instanz.

## <a name="create-an-azure-databricks-workspace"></a>Erstellen eines Azure Databricks-Arbeitsbereichs

In diesem Abschnitt erstellen Sie einen Azure Databricks-Arbeitsbereich über das [Azure-Portal](https://portal.azure.com/).

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Analysen** > **Azure Databricks**.

    ![Databricks im Azure-Portal](../media/tutorials/azure-databricks-on-portal.png "Databricks im Azure-Portal")

3. Geben Sie unter **Azure Databricks-Dienst** die folgenden Werte an, um einen Databricks-Arbeitsbereich zu erstellen:


    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |**Arbeitsbereichsname**     | Geben Sie einen Namen für Ihren Databricks-Arbeitsbereich an.        |
    |**Abonnement**     | Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.        |
    |**Ressourcengruppe**     | Geben Sie an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe verwenden möchten. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Weitere Informationen finden Sie in der [Übersicht über den Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md). |
    |**Location**     | Wählen Sie **USA, Osten 2** oder eine andere der verfügbaren Regionen aus. Informationen zur regionalen Verfügbarkeit finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).        |
    |**Tarif**     |  Wählen Sie zwischen **Standard** und **Premium**. Wählen Sie NICHT **Testversion** aus. Weitere Informationen zu diesen Tarifen, finden Sie unter [Azure Databricks – Preise](https://azure.microsoft.com/pricing/details/databricks/).       |

    Klicken Sie auf **Erstellen**.

4. Die Kontoerstellung dauert einige Minuten. 

## <a name="create-a-spark-cluster-in-databricks"></a>Erstellen eines Spark-Clusters in Databricks

1. Navigieren Sie im Azure-Portal zum erstellten Databricks-Arbeitsbereich, und klicken Sie auf **Launch Workspace** (Arbeitsbereich starten).

2. Sie werden zum Azure Databricks-Portal weitergeleitet. Wählen Sie im Portal **Neuer Cluster** aus.

    ![Databricks in Azure](../media/tutorials/databricks-on-azure.png "Databricks in Azure")

3. Geben Sie auf der Seite **Neuer Cluster** die erforderlichen Werte an, um einen Cluster zu erstellen.

    ![Erstellen eines Databricks-Spark-Clusters in Azure](../media/tutorials/create-databricks-spark-cluster.png "Erstellen eines Databricks-Spark-Clusters in Azure")

    Übernehmen Sie alle anderen Standardwerte bis auf Folgendes:

   * Geben Sie einen Namen für den Cluster ein.
   * Erstellen Sie im Rahmen dieses Artikels einen Cluster mit der Runtime **5.2**. Wählen Sie NICHT die Runtime **5.3** aus.
   * Aktivieren Sie das Kontrollkästchen **Terminate after \_\_ minutes of inactivity** (Nach \_\_ Minuten Inaktivität beenden). Geben Sie an, nach wie vielen Minuten der Cluster beendet werden soll, wenn er nicht verwendet wird.

     Klicken Sie auf **Cluster erstellen**. Sobald der Cluster ausgeführt wird, können Sie Notizbücher an den Cluster anfügen und Spark-Aufträge ausführen.

## <a name="create-a-twitter-application"></a>Erstellen einer Twitter-Anwendung

Für den Empfang eines Datenstroms mit Tweets müssen Sie eine Anwendung in Twitter erstellen. Führen Sie die Schritte aus, um eine Twitter-Anwendung zu erstellen, und notieren Sie sich die Werte, die Sie zum Durcharbeiten dieses Tutorials benötigen.

1. Navigieren Sie im Webbrowser zur Anwendungsverwaltung von Twitter ([Twitter Application Management](https://apps.twitter.com/)), und wählen Sie **Create New App** (Neue App erstellen).

    ![Erstellen einer Twitter-Anwendung](../media/tutorials/databricks-create-twitter-app.png "Erstellen einer Twitter-Anwendung")

2. Geben Sie auf der Seite **Create an application** (Anwendung erstellen) die Details für die neue App an, und klicken Sie anschließend auf **Create your Twitter application** (Twitter-Anwendung erstellen).

    ![Twitter-Anwendungsdetails](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter-Anwendungsdetails")

3. Klicken Sie auf der Anwendungsseite auf die Registerkarte **Keys and Access Tokens** (Schlüssel und Zugriffstoken), und kopieren Sie die Werte für **Consumer Key** (Consumerschlüssel) und **Consumer Secret** (Consumergeheimnis). Klicken Sie außerdem auf **Create my access token** (Mein Zugriffstoken erstellen), um die Zugriffstoken zu generieren. Kopieren Sie die Werte für **Access Token** (Zugriffstoken) und **Access Token Secret** (Zugriffstokengeheimnis).

    ![Twitter-Anwendungsdetails](../media/tutorials/twitter-app-key-secret.png "Twitter-Anwendungsdetails")

Speichern Sie die Werte, die Sie für die Twitter-Anwendung abgerufen haben. Diese werden im weiteren Verlauf des Tutorials benötigt.

## <a name="attach-libraries-to-spark-cluster"></a>Anfügen von Bibliotheken an einen Spark-Cluster

In diesem Tutorial verwenden Sie die Twitter-APIs, um Tweets an Event Hubs zu senden. Außerdem verwenden Sie den [Apache Spark-Event Hubs-Connector](https://github.com/Azure/azure-event-hubs-spark), um Daten in Azure Event Hubs zu lesen und zu schreiben. Fügen Sie diese APIs als Bibliotheken in Azure Databricks hinzu, und ordnen Sie sie dann Ihrem Spark-Cluster zu, um sie als Teil Ihres Clusters zu nutzen. In der folgenden Anleitung wird veranschaulicht, wie Sie die Bibliotheken in Ihrem Arbeitsbereich dem Ordner **Freigegeben** hinzufügen.

1. Klicken Sie im Azure Databricks-Arbeitsbereich auf **Arbeitsbereich** und anschließend mit der rechten Maustaste auf **Freigegeben**. Klicken Sie im Kontextmenü auf **Erstellen** > **Bibliothek**.

   ![Dialogfeld „Bibliothek hinzufügen“](../media/tutorials/databricks-add-library-option.png "Dialogfeld „Bibliothek hinzufügen“")

2. Wählen Sie auf der Seite „Neue Bibliothek“ unter **Quelle** die Option **Maven Coordinate** (Maven-Koordinate) aus. Geben Sie unter **Koordinate** die Koordinate für das Paket ein, das Sie hinzufügen möchten. Im Anschluss finden Sie die Maven-Koordinaten für die Bibliotheken, die in diesem Tutorial verwendet werden:

   * Spark-Event Hubs-Connector: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter-API: `org.twitter4j:twitter4j-core:4.0.7`

     ![Angeben von Maven-Koordinaten](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Angeben von Maven-Koordinaten")

3. Klicken Sie auf **Erstellen**.

4. Wählen Sie den Ordner aus, dem Sie die Bibliothek hinzugefügt haben, und wählen Sie anschließend den Namen der Bibliothek aus.

    ![Auswählen der hinzuzufügenden Bibliothek](../media/tutorials/select-library.png "Auswählen der hinzuzufügenden Bibliothek")

5. Wenn auf der Seite mit den Bibliotheken kein Cluster angezeigt wird, wählen Sie **Cluster** aus, und führen Sie den Cluster aus, den Sie erstellt haben. Warten Sie, bis der Zustand „Wird ausgeführt“ angezeigt wird, und wechseln Sie zurück zur Bibliotheksseite.
Wählen Sie auf der Bibliotheksseite den Cluster aus, in dem Sie die Bibliothek verwenden möchten. Wählen Sie anschließend **Installieren** aus. Nachdem die Bibliothek dem Cluster zugeordnet wurde, ändert sich der Status sofort in **Installiert**.

    ![Installieren der Bibliothek im Cluster](../media/tutorials/databricks-library-attached.png "Installieren der Bibliothek im Cluster")

6. Wiederholen Sie diese Schritte für das Twitter-Paket `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Abrufen eines Cognitive Services-Zugriffsschlüssels

In diesem Tutorial verwenden Sie die [Anomalieerkennungs-APIs von Azure Cognitive Services](../overview.md), um nahezu in Echtzeit eine Anomalieerkennung für einen Datenstrom mit Tweets durchzuführen. Bevor Sie die APIs verwenden, müssen Sie in Azure eine Anomalieerkennungsressource erstellen und einen Zugriffsschlüssel abrufen, um die Anomalieerkennungs-APIs verwenden zu können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **+ Ressource erstellen**.

3. Wählen Sie im Azure Marketplace **KI und Machine Learning** > **Alle anzeigen** > **Cognitive Services – weitere** > **Anomalieerkennung** aus. Sie könnten auch [diesen Link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) verwenden, um direkt zum Dialogfeld **Erstellen** zu navigieren.

    ![Erstellen einer Anomalieerkennungsressource](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Erstellen einer Anomalieerkennungsressource")

4. Geben Sie im Dialogfeld **Erstellen** die folgenden Werte an:

    |Wert |BESCHREIBUNG  |
    |---------|---------|
    |NAME     | Ein Name für die Anomalieerkennungsressource        |
    |Abonnement     | Das Azure-Abonnement, dem die Ressource zugeordnet wird        |
    |Location     | Ein Azure-Standort        |
    |Tarif     | Ein Tarif für den Dienst. Weitere Informationen zu den Preisen für die Anomalieerkennung finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Ressourcengruppe     | Geben Sie an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene auswählen möchten.        |


     Klicken Sie auf **Erstellen**.

5. Wählen Sie nach der Erstellung der Ressource auf der Registerkarte **Übersicht** die Option **Zugriffsschlüssel anzeigen** aus.

    ![Anzeigen von Zugriffsschlüsseln](../media/tutorials/cognitive-services-get-access-keys.png "Anzeigen von Zugriffsschlüsseln")

    Kopieren Sie außerdem einen Teil der Endpunkt-URL, wie im Screenshot dargestellt. Sie benötigen diese URL im Tutorial.

6. Wählen Sie unter **Schlüssel** das Kopiersymbol für den gewünschten Schlüssel aus.

    ![Kopieren von Zugriffsschlüsseln](../media/tutorials/cognitive-services-copy-access-keys.png "Kopieren von Zugriffsschlüsseln")

7. Speichern Sie die Werte für die Endpunkt-URL und den Zugriffsschlüssel, die Sie in diesem Schritt abgerufen haben. Sie benötigen sie später in diesem Tutorial.

## <a name="create-notebooks-in-databricks"></a>Erstellen von Notebooks in Databricks

In diesem Abschnitt erstellen Sie zwei Notebooks mit den folgenden Namen im Databricks-Arbeitsbereich.

- **SendTweetsToEventHub**: Ein Producer-Notebook, um Tweets von Twitter abzurufen und an Event Hubs zu streamen.
- **AnalyzeTweetsFromEventHub:** Consumer-Notebook zum Lesen der Tweets von Event Hubs und Ausführen der Anomalieerkennung.

1. Klicken Sie im linken Bereich auf **Arbeitsbereich**. Wählen Sie in der Dropdownliste **Arbeitsbereich** die Option **Erstellen** und dann **Notebook**.

    ![Erstellen eines Notizbuchs in Databricks](../media/tutorials/databricks-create-notebook.png "Erstellen eines Notizbuchs in Databricks")

2. Geben Sie im Dialogfeld **Notizbuch erstellen** als Namen **SendTweetsToEventHub** ein, wählen Sie **Scala** als Sprache aus, und wählen Sie den zuvor erstellten Spark-Cluster aus.

    ![Erstellen eines Notizbuchs in Databricks](../media/tutorials/databricks-notebook-details.png "Erstellen eines Notizbuchs in Databricks")

    Klicken Sie auf **Erstellen**.

3. Wiederholen Sie die Schritte zum Erstellen des Notebooks **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Senden von Tweets an Event Hubs

Fügen Sie im Notebook **SendTweetsToEventHub** den folgenden Code ein, und ersetzen Sie den Platzhalter durch Werte für Ihren Event Hubs-Namespace und die zuvor erstellte Twitter-Anwendung. Mit diesem Notebook werden Tweets mit dem Schlüsselwort „Azure“ in Echtzeit an Event Hubs gestreamt.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Drücken Sie zum Ausführen des Notebooks **UMSCHALT+EINGABE**. Die Ausgabe sieht wie im folgenden Codeausschnitt aus. Jedes Ereignis der Ausgabe ist ein Tweet, der in Event Hubs erfasst wird.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Lesen von Tweets aus Event Hubs

Fügen Sie im Notebook **AnalyzeTweetsFromEventHub** den folgenden Code ein, und ersetzen Sie den Platzhalter durch Werte für Ihre zuvor erstellten Azure Event Hubs. Mit diesem Notebook werden die Tweets gelesen, die Sie weiter oben mit dem Notebook **SendTweetsToEventHub** an Event Hubs gestreamt haben.

Schreiben Sie zunächst einen Client für das Aufrufen der Anomalieerkennung. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API for timeserie.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API for timeserie.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Drücken Sie **UMSCHALT+EINGABE** , um das Notebook auszuführen. Die Ausgabe sieht wie im folgenden Codeausschnitt aus. :

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Bereiten Sie dann eine Aggregationsfunktion für die zukünftige Verwendung vor.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
//import org.apache.spark.sql.functions._
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction_Hourly extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms. 
      // Check Anomaly detector API reference (https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-last-detect)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Drücken Sie **UMSCHALT+EINGABE** , um das Notebook auszuführen. Die Ausgabe sieht wie im folgenden Codeausschnitt aus. 

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Laden Sie dann Daten aus Event Hub für die Anomalieerkennung.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

Die Ausgabe ähnelt nun der folgenden Abbildung. Beachten Sie, dass sich die Daten in Ihrer Tabelle von denen in diesem Tutorial unterscheiden, da die Daten in Echtzeit abgerufen werden.
![Laden von Daten von Event Hub](../media/tutorials/load-data-from-eventhub.png "Laden von Daten von Event Hub")

Sie haben nun mithilfe des Event Hubs-Connectors für Apache Spark Daten aus Azure Event Hubs nahezu in Echtzeit in Azure Databricks gestreamt. Weitere Informationen zur Verwendung des Event Hubs-Connectors für Spark finden Sie in der [Connector-Dokumentation](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Ausführen der Anomalieerkennung für Tweets

In diesem Abschnitt führen Sie mithilfe der Anomalieerkennung-API die Anomalieerkennung für die empfangenen Tweets aus. Für diesen Abschnitt fügen Sie die Codeausschnitte wieder dem Notebook **AnalyzeTweetsFromEventHub** hinzu.

Für die Anomalieerkennung müssen Sie zunächst die Metrikanzahl pro Stunde aggregieren.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
Die Ausgabe ähnelt nun den folgenden Codeausschnitten.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Führen Sie anschließend ein Delta für die ausgegebenen Ergebnisse aus. Da für die Anomalieerkennung ein längeres Verlaufsfenster erforderlich ist, verwenden wir Delta, um die Verlaufsdaten für den zu untersuchenden Zeitpunkt beizubehalten. 


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
Die Ausgabe sieht wie folgt aus: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Nun werden die aggregierten Zeitreihendaten kontinuierlich für das Delta erfasst. Anschließend können Sie einen Auftrag einmal pro Stunde planen, um Anomalien seit dem letzten Zeitpunkt zu erkennen. 

```scala
//
// Anomaly Detection with Batch query
//

import java.time.Instant
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// How long history you want to use in anomaly detection. It is hourly time series in this tutorial, so 72 means 72 hours. 
val batchSize = 72

// Change the endTime to where you want to detect. You could use Databricks to schedule a job and change it to the latest hour. 
val endTime = Instant.parse("2019-04-16T00:00:00Z")
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val series = detectData.filter($"groupTime" < endTime.toString && $"groupTime" >= startTime.toString).sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as timestamp, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Das Ergebnis sieht wie folgt aus: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+

```
Geben Sie das Ergebnis der Anomalieerkennung wieder an das Delta aus. 
```scala
//
// Output Batch AD Result to delta
//

adResult.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")
  
```


Das ist alles! Mit Azure Databricks haben Sie Daten erfolgreich in Azure Event Hubs gestreamt, die Daten des Datenstroms mit dem Event Hubs-Connector genutzt und dann nahezu in Echtzeit eine Anomalieerkennung für die Streamingdaten ausgeführt.
Auch wenn in diesem Tutorial ein Zeitraum von einer Stunde verwendet wurde, können Sie diesen jederzeit gemäß Ihren Anforderungen ändern. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Tutorials können Sie den Cluster beenden. Klicken Sie hierzu im linken Bereich des Azure Databricks-Arbeitsbereichs auf **Cluster**. Bewegen Sie den Cursor zum Beenden des Clusters auf die Auslassungspunkte in der Spalte **Aktionen**, und klicken Sie auf das Symbol **Beenden**.

![Beenden eines Databricks-Clusters](../media/tutorials/terminate-databricks-cluster.png "Beenden eines Databricks-Clusters")

Wenn Sie den Cluster nicht manuell beenden, wird er automatisch beendet, sofern Sie bei der Erstellung des Clusters das Kontrollkästchen **Terminate after \_\_ minutes of inactivity** (Nach \_\_ Minuten Inaktivität beenden) aktiviert haben. Der Cluster wird dann automatisch beendet, wenn er für den angegebenen Zeitraum inaktiv war.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie Azure Databricks zum Streamen von Daten in Azure Event Hubs verwenden und die Streamingdaten dann in Echtzeit von Event Hubs lesen. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie die Anomalieerkennungs-API aufrufen und Anomalien mithilfe von Power BI Desktop visualisieren. 

> [!div class="nextstepaction"]
>[Batchanomalieerkennung mit Power BI Desktop](batch-anomaly-detection-powerbi.md)
