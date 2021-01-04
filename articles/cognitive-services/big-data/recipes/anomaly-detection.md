---
title: 'Anleitung: Predictive Maintenance mit Cognitive Services für Big Data'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit Cognitive Services für Big Data eine verteilte Anomalieerkennung durchführen.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: d2995f39bc61ae5bb87abafd674f411271e57ca2
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366280"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Anleitung: Predictive Maintenance mit Cognitive Services für Big Data

Diese Anleitung verdeutlicht, wie Sie Azure Synapse Analytics und Cognitive Services unter Apache Spark für Predictive Maintenance-Vorgänge von IoT-Geräten verwenden können. Hierbei halten wir uns an das Beispiel [Cosmos DB und Synapse Link](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples). Der Einfachheit halber lesen wir die Daten in dieser Anleitung direkt aus einer CSV-Datei aus, anstatt gestreamte Daten über Cosmos DB und Synapse Link abzurufen. Wir empfehlen Ihnen dringend, sich das Synapse Link-Beispiel anzusehen.

## <a name="hypothetical-scenario"></a>Hypothetisches Szenario

Bei diesem hypothetischen Szenario geht es um ein Kraftwerk, in dem IoT-Geräte [Dampfturbinen](https://en.wikipedia.org/wiki/Steam_turbine) überwachen. Die Sammlung „IoTSignals“ enthält für jede Turbine die Umdrehungen pro Minute (RPM) und den Megawatt-Wert (MW). Die Signale der Dampfturbinen werden analysiert, und anomale Signale werden erkannt.

Die Daten können auch zufällig auftretende Ausreißer enthalten. In diesen Situationen erhöhen sich die RPM-Werte, und die MW-Leistung verringert sich, um das Leitungsnetz zu schützen. Die Idee besteht darin, Datenabweichungen gleichzeitig – aber basierend auf unterschiedlichen Signalen – überwachen zu können.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services)
* Konfiguration eines [Azure Synapse-Arbeitsbereichs](../../../synapse-analytics/quickstart-create-workspace.md) mit einem [serverlosen Apache Spark-Pool](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

## <a name="setup"></a>Einrichten

### <a name="create-an-anomaly-detector-resource"></a>Erstellen einer Anomalieerkennungsressource

Azure Cognitive Services werden von Azure-Ressourcen dargestellt, die Sie abonnieren. Erstellen Sie über das [Azure-Portal](../../cognitive-services-apis-create-account.md) oder mithilfe der [Azure-Befehlszeilenschnittstelle](../../cognitive-services-apis-create-account-cli.md) eine Ressource für Translator. Außerdem können Sie Folgendes durchführen:

- Zeigen Sie eine vorhandene Ressource im [Azure-Portal](https://portal.azure.com/) an.

Notieren Sie sich den Endpunkt und den Schlüssel für diese Ressource, da Sie diese Angaben für diesen Leitfaden noch benötigen.

## <a name="enter-your-service-keys"></a>Eingeben Ihrer Dienstschlüssel

Zunächst fügen Sie Ihren Schlüssel und den Speicherort hinzu.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Einlesen von Daten in einen Datenrahmen

Als Nächstes lesen wir die „IoTSignals“-Datei in einen Datenrahmen ein. Öffnen Sie in Ihrem Synapse-Arbeitsbereich ein neues Notebook, und erstellen Sie aus der Datei einen Datenrahmen.

```python
df_device_info = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Ausführen der Anomalieerkennung mit Cognitive Services unter Spark

Das Ziel ist hierbei die Ermittlung von Instanzen, bei denen die Signale der IoT-Geräte anomale Werte ausgegeben haben. So können wir erkennen, wenn Fehler auftreten, und entsprechende Predictive Maintenance-Maßnahmen ergreifen. Hierfür verwenden wir die Anomalieerkennung unter Spark:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Wir sehen uns nun die Daten an:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Diese Zelle sollte ein Ergebnis zurückgeben, das wie folgt aussieht:

| timestamp           |   value | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | False       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | False       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | False       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Visualisieren von Anomalien für eines der Geräte

Die Datei „IoTSignals.csv“ enthält Signale von mehreren IoT-Geräten. Wir konzentrieren uns auf ein bestimmtes Gerät und visualisieren dessen anomale Ausgaben.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Nachdem wir nun einen Datenrahmen erstellt haben, der die Anomalien für ein bestimmtes Gerät darstellt, können wir diese Anomalien visualisieren:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Wenn der Vorgang erfolgreich ist, sieht Ihre Ausgabe wie folgt aus:

![Plot zur Anomalieerkennung](../media/anomaly-output.png)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie Predictive Maintenance mit Azure Cognitive Services, Azure Synapse Analytics und Azure Cosmos DB im großen Stil durchführen. Weitere Informationen finden Sie unter dem vollständigen Beispiel auf [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).