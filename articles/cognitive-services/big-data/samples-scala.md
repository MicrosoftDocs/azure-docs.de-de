---
title: Cognitive Services für Big Data – Scala-Beispiele
description: Verwenden Sie Cognitive Services für Azure Databricks, um Ihre MMLSpark-Pipeline für Big Data auszuführen.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363271"
---
# <a name="quick-examples"></a>Kurze Beispiele

Die folgenden Codeausschnitte sind bereit zur Ausführung und helfen Ihnen beim Einstieg in die Nutzung von Cognitive Services in Spark. In den folgenden Beispielen wird Scala verwendet.

Für die Beispiele werden diese Cognitive Services-Komponenten verwendet:

- Textanalyse: Ermitteln der Stimmung (oder Laune) in einer Reihe von Sätzen
- Maschinelles Sehen: Abrufen der Tags (Einwortbeschreibungen) die einem Satz von Bildern zugeordnet sind
- Bing-Bildersuche: Suchen im Web nach Bildern im Zusammenhang mit einer Abfrage in natürlicher Sprache
- Spracherkennung: Transkribieren von Audiodateien zum Extrahieren textbasierter Transkriptionen
- Anomalieerkennung: Erkennen von Anomalien innerhalb von Zeitreihendaten

## <a name="prerequisites"></a>Voraussetzungen

1. Führen Sie die Schritte in [Erste Schritte](getting-started.md) aus, um Ihre Azure Databricks- und Cognitive Services-Umgebung einzurichten. In diesem Tutorial wird u. a. gezeigt, wie Sie MMLSpark installieren und Ihren Spark-Cluster in Databricks erstellen.
1. Nachdem Sie in Azure Databricks ein neues Notebook erstellt haben, kopieren Sie den **freigegebenen Code** unten, und fügen Sie ihn in eine neue Zelle des Notebooks ein.
1. Wählen Sie unten ein Dienstbeispiel aus, und kopieren Sie es in eine zweite neue Zelle in Ihrem Notebook.
1. Ersetzen Sie alle Schlüsselplatzhalter für Dienstabonnements durch ihren eigenen Schlüssel.
1. Wählen Sie in der Zelle rechts oben die Schaltfläche zum Ausführen (Dreieckssymbol), und wählen Sie dann **Zelle ausführen** aus.
1. Zeigen Sie Ergebnisse in einer Tabelle unterhalb der Zelle an.

## <a name="shared-code"></a>Freigegebener Code
Fügen Sie Ihrem Projekt zunächst den folgenden Code hinzu:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Textanalyse

Der [Textanalyse](../text-analytics/index.yml)-Dienst bietet mehrere Algorithmen zum Extrahieren intelligenter Erkenntnisse aus Text. Beispielsweise können wir die Stimmung eines Eingabetexts ermitteln. Der Dienst gibt eine Bewertung zwischen `0.0` und `1.0` zurück, wobei niedrige Scores auf eine negative Stimmung und hohe Scores auf eine positive Stimmung hindeuten.  Im folgenden Beispiel werden drei einfache Sätze verwendet, und es wird jeweils ein Score für die Stimmung zurückgegeben.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| text                                      | Stimmung                                             |
|:------------------------------------------|:------------------------------------------------------|
| Ich bin heute so glücklich, die Sonne scheint!           | 0,9789592027664185                                    |
| Ich bin frustriert über diesen Berufsverkehr. | 0,023795604705810547                                  |
| Cognitive Services in Spark sind gar nicht schlecht  | 0,8888956308364868                                    |

## <a name="computer-vision"></a>Maschinelles Sehen

[Maschinelles Sehen](../computer-vision/index.yml) analysiert Bilder, um die Struktur zu identifizieren, wie z. B. Gesichter, Objekte und Beschreibungen in natürlicher Sprache.
In diesem Beispiel markieren wir eine Liste mit Bildern. Tags sind Einwortbeschreibungen der Dinge im Bild, wie z. B. erkennbare Objekte, Personen, Szenen und Aktionen.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Erwartetes Ergebnis

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | [„Skaten“ „Person“ „Mann“ „draußen“ „Reiten“ „Sport“ „Skateboard“ „jung“ „Brett“ „Hemd“ „Luft“ „schwarz“ „Park“ „Junge“ „Seite“ „Springen „Trick“ „Rampe“ „tun“ „Fliegen“]
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | [„Hund“ „draußen“ „Zaun“ „hölzern“ „klein“ „braun“ „Gebäude“ „Sitzen“ „vorne“ „Bank“ „Stehen“ „Tisch“ „Gehen“ „Brett“ „Strand“ „weiß“ „Halten“ „Brücke“ „Spur“]                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | [„draußen“ „Gras“ „Haus“ „Gebäude“ „alt“ „Zuhause“ „vorne“ „klein“ „Kirche“ „Stein“ „groß“ „Grasen“ „Hof“ „grün“ „Sitzen“ „Führen“ „Schafe“ „Ziegel“ „Bank“„Straße“ „weiß“ „Land“ „Uhr“ „Schild“„geparkt“„Feld“„Stehen“„Garten“„Wasser“„rot“ „Pferd“ „Mann“ „groß“ „Feuer“ „Gruppe“] 

## <a name="bing-image-search"></a>Bing-Bildersuche

Die [Bing-Bildersuche](../bing-image-search/overview.md) durchsucht das Web nach Bildern im Zusammenhang mit einer Abfrage in natürlicher Sprache eines Benutzers. In diesem Beispiel verwenden wir eine Textabfrage, die nach Bildern mit Zitaten sucht. Sie gibt eine Liste mit Bild-URLs zurück, die Fotos im Zusammenhang mit unserer Abfrage enthalten.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Spracherkennung

Der [Spracherkennungsdienst](../speech-service/index-speech-to-text.yml) konvertiert Streams oder Dateien gesprochener Audiodaten in Text. In diesem Beispiel transkribieren wir zwei Audiodateien. Die erste Datei ist leicht verständlich, und die zweite ist schwieriger.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Custom speech provides tools that allow you to visually inspect the recognition quality of a model by comparing audio data with the corresponding recognition result from the custom speech portal. You can playback uploaded audio and determine if the provided recognition result is correct. This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Add a gentleman Sir thinking visual check.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I hear me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I like the reassurance for radio that I can hear it as well. |

## <a name="anomaly-detector"></a>Anomalieerkennung

Die [Anomalieerkennung](../anomaly-detector/index.yml) eignet sich hervorragend zum Erkennen von Unregelmäßigkeiten in ihren Zeitreihendaten In diesem Beispiel verwenden wir den Dienst, um Anomalien in der gesamten Zeitreihe zu suchen.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| timestamp            |   value | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |