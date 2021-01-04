---
title: Visualisieren von Daten mit Apache Spark
description: Erstellen von umfangreichen Datenvisualisierungen mit Apache Spark und Azure Synapse Analytics-Notebooks
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 8735514b639cb0322a83ffb19d661027327c0f73
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458760"
---
# <a name="analyze-data-with-apache-spark"></a>Analysieren von Daten mit Apache Spark

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Open Datasets und Apache Spark eine explorative Datenanalyse durchführen und die Ergebnisse anschließend in einem Azure Synapse Studio-Notebook visualisieren.

Sie werden das Dataset [New York City (NYC) Taxi](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) analysieren. Die Daten sind über Azure Open Datasets verfügbar. Diese Teilmenge des Datasets enthält Informationen über Taxifahrten von Yellow Cabs, einschließlich Informationen zu jeder einzelnen Fahrt, den Start- und Endzeiten/-orten und den Kosten sowie weitere interessante Attribute.
  
## <a name="before-you-begin"></a>Voraussetzungen
- Erstellen Sie einen Apache Spark-Pool, indem Sie das Tutorial [Erstellen eines Apache Spark-Pools](../articles/../quickstart-create-apache-spark-pool-studio.md) absolvieren. 

## <a name="download-and-prepare-the-data"></a>Herunterladen und Vorbereiten der Daten
1. Erstellen Sie ein Notebook unter Verwendung des PySpark-Kernels. Eine entsprechende Anleitung finden Sie unter [Erstellen eines Notebooks](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook). 
   
> [!Note]
> 
> Durch den PySpark-Kernel müssen Sie keine Kontexte explizit erstellen. Der Spark-Kontext wird automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen.
>

2. In diesem Tutorial verwenden wir verschiedene Bibliotheken, um das Dataset zu visualisieren. Für die Analyse müssen wir die folgenden Bibliotheken importieren: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Da die Rohdaten im Parquet-Format vorliegen, können Sie den Spark-Kontext verwenden, um die Datei als Dataframe direkt in den Arbeitsspeicher zu lesen. Erstellen Sie ein Spark-Dataframe, indem Sie die Daten über die Open Datasets-API abrufen. Hier verwenden wir die Spark-Dataframe-Eigenschaften *schema on read*, um die Datentypen und das Schema abzuleiten.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Nachdem die Daten gelesen wurden, müssen wir eine erste Filterung durchführen, um das Dataset zu bereinigen. Wir können nicht benötigte Spalten entfernen und zusätzliche Spalten hinzufügen, die wichtige Informationen extrahieren. Darüber hinaus werden wir auch Anomalien aus dem Dataset herausfiltern.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Daten analysieren
 Als Datenanalyst steht Ihnen eine Vielzahl von Tools zur Verfügung, die Ihnen beim Extrahieren von Erkenntnissen aus Daten helfen. In diesem Teil des Tutorials werden einige nützliche Tools erläutert, die in Azure Synapse Analytics-Notebooks verfügbar sind. In dieser Analyse möchten wir die Faktoren verstehen, die im ausgewählten Zeitraum zu höheren Trinkgeldern führen.

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL-Magic-Befehl 
Als erstes führen wir eine explorative Datenanalyse mithilfe von Apache Spark SQL und Magic-Befehlen mit dem Synapse-Notebook aus. Wenn die Abfrage fertig ist, visualisieren wir die Ergebnisse mithilfe der integrierten ```chart options```-Funktion. 

1. Erstellen Sie eine neue Zelle im Notebook, und kopieren Sie den unten stehenden Code. Mit dieser Abfrage möchten wir herausfinden, wie sich die durchschnittlichen Trinkgeldbeträge im ausgewählten Zeitraum verändert haben. Mit dieser Abfrage können wir auch andere nützliche Erkenntnisse erzielen, beispielsweise den minimalen und den maximalen Trinkgeldbetrag pro Tag sowie den durchschnittlichen Fahrpreis.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. Nach dem Ausführen der Abfrage können wir die Ergebnisse visualisieren, indem wir zur **Diagrammansicht** wechseln. In diesem Beispiel erstellen wir ein **Liniendiagramm**, indem wir das Feld ```day_of_month``` als **Schlüssel** und ```avgTipAmount``` als **Wert** angeben. Wenn Sie die entsprechende Auswahl getroffen haben, klicken Sie auf **Anwenden**, um das Diagramm zu aktualisieren. 
   
## <a name="visualize-data"></a>Visualisieren von Daten
Zusätzlich zu den Diagrammoptionen im Notebook können Sie auch beliebte Open-Source-Bibliotheken verwenden, um selbst Visualisierungen zu erstellen. In den folgenden Beispielen verwenden wir Seaborn und Matplotlib – zwei häufig genutzte Python-Bibliotheken für die Datenvisualisierung. 

> [!Note]
> 
> Standardmäßig enthält jeder Apache Spark-Pool in Azure Synapse Analytics eine Reihe gängiger Bibliotheken und Standardbibliotheken. Eine vollständige Liste der Bibliotheken finden Sie in der [Azure Synapse-Runtime](../spark/apache-spark-version-support.md). Onlinedokumentation. Wenn Sie Code eines Drittanbieters oder lokal erstellten Code für Ihre Anwendungen verfügbar machen möchten, können Sie auch in einem Ihrer Spark-Pools [eine Bibliothek installieren](../spark/apache-spark-azure-portal-add-libraries.md).
>

1. Um die Entwicklung einfacher und kostengünstiger zu gestalten, führen wir ein Downsampling des Datasets aus. Wir verwenden die integrierte Samplingfunktion von Apache Spark. Darüber hinaus benötigen sowohl Seaborn als auch Matplotlib einen Pandas-Datenrahmen oder ein Numpy-Array. Um einen Pandas-Datenrahmens zu erhalten, verwenden wir den Befehl ```toPandas()``` zum Konvertieren des Datenrahmens.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Zunächst möchten wir uns die Verteilung der Trinkgelder im Dataset ansehen. Wir verwenden Matplotlib, um ein Histogramm zu erstellen, das die Verteilung der Beträge und die Anzahl zeigt. Anhand der Verteilung können wir erkennen, dass Trinkgelder tendenziell nicht mehr als 10 USD betragen.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Histogramm der Trinkgelder](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Als Nächstes möchten wir die Beziehung zwischen Trinkgeld pro Fahrt und Wochentag verstehen. Wir verwenden Seaborn, um ein Boxplotdiagramm zu erstellen, das die Tendenz für jeden Wochentag zusammenfasst. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Verteilung der Trinkgelder pro Tag](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Nun möchten wir eine weitere Hypothese untersuchen, derzufolge möglicherweise ein positiver Zusammenhang zwischen der Anzahl von Fahrgästen und dem gesamten Trinkgeldbetrag besteht. Um diesen Zusammenhang zu überprüfen, führen wir den folgenden Code aus, um ein Boxplotdiagramm zu generieren, das die Verteilung von Trinkgeldern für die jeweilige Anzahl von Fahrgästen zeigt. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Boxplotdiagramm](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Zum Schluss möchten wir mehr über den Zusammenhang zwischen Fahrpreis und Trinkgeldbetrag erfahren. Anhand der Ergebnisse können wir feststellen, dass es mehrere Fahrten gab, bei denen die Fahrgäste gar kein Trinkgeld gegeben haben. Wir sehen jedoch auch einen positiven Zusammenhang zwischen Gesamtfahrpreis und Trinkgeldbetrag.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Punktdiagramm](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Herunterfahren der Spark-Instanz

Nachdem Sie die Ausführung der Anwendung abgeschlossen haben, fahren Sie das Notebook herunter, um die Ressourcen freizugeben, indem Sie die Registerkarte schließen, oder wählen Sie unten im Notebook im Statusbereich **Sitzung beenden** aus.

## <a name="see-also"></a>Weitere Informationen

- [Übersicht: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Erstellen eines Machine Learning-Modells mit Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Offizielle Apache Spark-Dokumentation](https://spark.apache.org/docs/latest/)