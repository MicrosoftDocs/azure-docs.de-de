---
title: Cognitive Services für Big Data – Python-Beispiele
description: Testen Sie Cognitive Services-Beispiele in Python für Azure Databricks, um Ihre MMLSpark-Pipeline für Big Data auszuführen.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 590ddef27315f37719da5b28c68b6c402371e986
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363254"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Python-Beispiele für Cognitive Services für Big Data

Die folgenden Codeausschnitte sind bereit zur Ausführung und helfen Ihnen beim Einstieg in die Nutzung von Cognitive Services in Spark mit Python.

Für die Beispiele in diesem Artikel werden diese Cognitive Services-Komponenten verwendet:

- Textanalyse: Ermitteln der Stimmung (oder Laune) in einer Reihe von Sätzen
- Maschinelles Sehen: Abrufen der Tags (Einwortbeschreibungen) die einem Satz von Bildern zugeordnet sind
- Bing-Bildersuche: Suchen im Web nach Bildern im Zusammenhang mit einer Abfrage in natürlicher Sprache
- Spracherkennung: Transkribieren von Audiodateien zum Extrahieren textbasierter Transkriptionen
- Anomalieerkennung: Erkennen von Anomalien innerhalb von Zeitreihendaten

## <a name="prerequisites"></a>Voraussetzungen

1. Führen Sie die Schritte in [Erste Schritte](getting-started.md) aus, um Ihre Azure Databricks- und Cognitive Services-Umgebung einzurichten. In diesem Tutorial wird gezeigt, wie Sie MMLSpark installieren und Ihren Spark-Cluster in Databricks erstellen.
1. Nachdem Sie in Azure Databricks ein neues Notebook erstellt haben, kopieren Sie den **freigegebenen Code** unten, und fügen Sie ihn in eine neue Zelle des Notebooks ein.
1. Wählen Sie unten ein Dienstbeispiel aus, und kopieren Sie es in eine zweite neue Zelle in Ihrem Notebook.
1. Ersetzen Sie alle Schlüsselplatzhalter für Dienstabonnements durch ihren eigenen Schlüssel.
1. Wählen Sie in der Zelle rechts oben die Schaltfläche zum Ausführen (Dreieckssymbol), und wählen Sie dann **Zelle ausführen** aus.
1. Zeigen Sie Ergebnisse in einer Tabelle unterhalb der Zelle an.

## <a name="shared-code"></a>Freigegebener Code

Zunächst müssen wir dem Projekt folgenden Code hinzufügen:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Beispiel für Textanalyse

Der [Textanalyse](../text-analytics/index.yml)-Dienst bietet mehrere Algorithmen zum Extrahieren intelligenter Erkenntnisse aus Text. Beispielsweise können wir die Stimmung eines Eingabetexts ermitteln. Der Dienst gibt eine Bewertung zwischen 0,0 und 1,0 zurück, wobei niedrige Scores auf eine negative Stimmung und hohe Scores auf eine positive Stimmung hindeuten.  In diesem Beispiel werden drei einfache Sätze verwendet, und es wird jeweils die Stimmung zurückgegeben.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| text                                      | Stimmung                                             |
|:------------------------------------------|:------------------------------------------------------|
| Ich bin heute so glücklich, die Sonne scheint!           | Positiv                                              |
| Ich bin frustriert über diesen Berufsverkehr. | Negativ                                              |
| Cognitive Services in Spark sind gar nicht schlecht  | Positiv                                              |

## <a name="computer-vision-sample"></a>Beispiel für maschinelles Sehen

[Maschinelles Sehen](../computer-vision/index.yml) analysiert Bilder, um die Struktur zu identifizieren, wie z. B. Gesichter, Objekte und Beschreibungen in natürlicher Sprache. In diesem Beispiel markieren wir eine Liste mit Bildern. Tags sind Einwortbeschreibungen der Dinge im Bild, wie z. B. erkennbare Objekte, Personen, Szenen und Aktionen.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | [„Skaten“ „Person“ „Mann“ „im Freien“ „Reiten“ „Sport“ „Skateboard“ „jung“ „Brett“ „Hemd“ „Luft“ „schwarz“ „Park“ „Junge“ „Seite“ „Springen „Trick“ „Rampe“ „tun“ „Fliegen“]
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | [„Hund“ „draußen“ „Zaun“ „hölzern“ „klein“ „braun“ „Gebäude“ „Sitzen“ „vorne“ „Bank“ „Stehen“ „Tisch“ „Gehen“ „Brett“ „Strand“ „weiß“ „Halten“ „Brücke“ „Spur“]                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | [„draußen“ „Gras“ „Haus“ „Gebäude“ „alt“ „Zuhause“ „vorne“ „klein“ „Kirche“ „Stein“ „groß“ „Grasen“ „Hof“ „grün“ „Sitzen“ „Führen“ „Schafe“ „Ziegel“ „Bank“„Straße“ „weiß“ „Land“ „Uhr“ „Schild“„geparkt“„Feld“„Stehen“„Garten“„Wasser“„rot“ „Pferd“ „Mann“ „groß“ „Feuer“ „Gruppe“]


## <a name="bing-image-search-sample"></a>Beispiel für Bing-Bildersuche

Die [Bing-Bildersuche](../bing-image-search/overview.md) durchsucht das Web nach Bildern im Zusammenhang mit einer Abfrage in natürlicher Sprache eines Benutzers. In diesem Beispiel verwenden wir eine Textabfrage, die nach Bildern mit Zitaten sucht. Sie gibt eine Liste mit Bild-URLs zurück, die Fotos im Zusammenhang mit unserer Abfrage enthalten.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Beispiel für Spracherkennung
Der [Spracherkennungsdienst](../speech-service/index-speech-to-text.yml) konvertiert Streams oder Dateien gesprochener Audiodaten in Text. In diesem Beispiel transkribieren wir zwei Audiodateien. Die erste Datei ist leicht verständlich, und die zweite ist schwieriger.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Custom speech provides tools that allow you to visually inspect the recognition quality of a model by comparing audio data with the corresponding recognition result from the custom speech portal. You can playback uploaded audio and determine if the provided recognition result is correct. This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Add a gentleman Sir thinking visual check.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I hear me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I like the reassurance for radio that I can hear it as well. |


## <a name="anomaly-detector-sample"></a>Beispiel für Anomalieerkennung

Die [Anomalieerkennung](../anomaly-detector/index.yml) eignet sich hervorragend zum Erkennen von Unregelmäßigkeiten in ihren Zeitreihendaten In diesem Beispiel verwenden wir den Dienst, um Anomalien in der gesamten Zeitreihe zu suchen.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>Beliebige Web-APIs

Mit HTTP in Spark können alle Webdienste in Ihrer Big Data-Pipeline verwendet werden. In diesem Beispiel verwenden wir die [World Bank-API](http://api.worldbank.org/v2/country/), um Informationen zu verschiedenen Ländern auf der ganzen Welt zu erhalten.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Erwartetes Ergebnis

| country   | Antwort |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","name":"Brazil","region":{"id":"LCN","iso2code":"ZJ","value":"Latin America & Caribbean "},"adminregion":{"id":"LAC","iso2code":"XJ","value":"Latin America & Caribbean (excluding high income)"},"incomeLevel":{"id":"UMC","iso2code":"XT","value":"Upper middle income"},"lendingType":{"id":"IBD","iso2code":"XF","value":"IBRD"},"capitalCity":"Brasilia","longitude":"-47.9292","latitude":"-15.7801"}]] |
| usa  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region":{"id":"NAC","iso2code":"XU","value":"North America"},"adminregion":{"id":"","iso2code":"","value":""},"incomeLevel":{"id":"HIC","iso2code":"XD","value":"High income"},"lendingType":{"id":"LNX","iso2code":"XX","value":"Not classified"},"capitalCity":"Washington D.C.","longitude":"-77.032","latitude":"38.8895"}]] |

## <a name="see-also"></a>Weitere Informationen

* [Anleitung: Anomalieerkennung](./recipes/anomaly-detection.md)
* [Anleitung: Art-Explorer](./recipes/art-explorer.md)