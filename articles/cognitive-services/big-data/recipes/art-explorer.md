---
title: 'Anleitung: Intelligente Erkundung von Kunstwerken mit Cognitive Services für Big Data'
titleSuffix: Azure Cognitive Services
description: In dieser Anleitung wird die Erstellung einer durchsuchbaren Kunstdatenbank mit Azure Search und MMLSpark gezeigt.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363288"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Anleitung: Intelligente Erkundung von Kunstwerken mit Cognitive Services für Big Data

In diesem Beispiel wird Cognitive Services für Big Data verwendet, um die frei zugängliche Sammlung des Metropolitan Museum of Art (Met) mit intelligenten Anmerkungen zu versehen. Dies ermöglicht die Erstellung einer intelligenten Suchmaschine mit Azure Search ganz ohne manuelle Anmerkungen. 

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen einen Abonnementschlüssel für maschinelles Sehen und Cognitive Search. Gehen Sie wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](../../cognitive-services-apis-create-account.md) beschrieben vor, um maschinelles Sehen zu abonnieren und Ihren Schlüssel zu erhalten.
  > [!NOTE]
  > Preisinformationen finden Sie unter [Leistung, Steuerung und Anpassungsmöglichkeiten nach Bedarf mit flexibler Preisgestaltung](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Importieren von Bibliotheken

Führen Sie den folgenden Befehl aus, um Bibliotheken für diese Anleitung zu importieren:

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Einrichten von Abonnementschlüsseln

Führen Sie den folgenden Befehl aus, um Variablen für Dienstschlüssel einzurichten: Fügen Sie Ihre Abonnementschlüssel für maschinelles Sehen und Azure Cognitive Search ein.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Lesen der Daten

Führen Sie den folgenden Befehl aus, um Daten aus der öffentlich zugänglichen Sammlung des Met zu laden:

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Analysieren der Bilder

Führen Sie den folgenden Befehl aus, um maschinelles Sehen für die öffentlich zugängliche Kunstsammlung des Met zu verwenden. Als Ergebnis erhalten Sie visuelle Features der Kunstwerke.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Erstellen des Suchindex

Führen Sie den folgenden Befehl aus, um die Ergebnisse in Azure Search zu schreiben und eine Suchmaschine für die Kunstwerke zu erstellen, deren Metadaten durch maschinelles Sehen angereichert wurden:

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Abfragen des Suchindex

Führen Sie den folgenden Befehl aus, um den Azure Search-Index abzufragen:

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie [Cognitive Services für Big Data zur Anomalieerkennung](anomaly-detection.md) verwenden.