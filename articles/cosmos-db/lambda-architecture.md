---
title: Lambda-Architektur mit Azure Cosmos DB und HDInsight (Apache Spark)
description: In diesem Artikel wird beschrieben, wie Sie eine Lambda-Architektur mit Azure Cosmos DB, HDInsight und Spark verwenden.
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: a997f1d0fd304b43f56953c51e6a8944a4c93ce0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257180"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementieren einer Lambda-Architektur auf der Azure Platform 

Lambda-Architekturen ermöglichen eine effiziente Datenverarbeitung für sehr große Datasets. Bei Lambda-Architekturen werden die Batchverarbeitung, Datenstromverarbeitung und eine Bereitstellungsebene verwendet, um die Wartezeit bei Big Data-Abfragen zu verringern. 

Für die Implementierung einer Lambda-Architektur in Azure können Sie die folgenden Technologien kombinieren, um Big Data-Echtzeitanalysen zu beschleunigen:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ist der erste global verteilte Datenbankdienst der Branche mit mehreren Modellen. 
* Bei [Apache Spark für Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) handelt es sich um ein Datenverarbeitungsframework, mit dem Anwendungen für umfangreiche Datenanalysen ausgeführt werden können.
* [Änderungsfeed](change-feed.md) von Azure Cosmos DB, mit dem neue Daten zur Verarbeitung durch HDInsight an die Batchebene gestreamt werden.
* [Spark-Connector für Azure Cosmos DB](spark-connector.md)

In diesem Artikel werden die Grundlagen einer Lambda-Architektur anhand des ursprünglichen Entwurfs mit mehreren Ebenen und die Vorteile einer neu gestalteten Lambda-Architektur zur Vereinfachung der Abläufe beschrieben.  

## <a name="what-is-a-lambda-architecture"></a>Was ist eine Lambda-Architektur?
Eine Lambda-Architektur ist eine generische, skalierbare und fehlertolerante Datenverarbeitungsarchitektur für Batch- und Beschleunigungsszenarien. Dies wurde von [Nathan Marz](https://twitter.com/nathanmarz) beschrieben.

![Diagramm mit einer Lambda-Architektur](./media/lambda-architecture/lambda-architecture-intro.png)

Quelle: http://lambda-architecture.net/

Die grundlegenden Prinzipien einer Lambda-Architektur sind im obigen Diagramm gemäß [http://lambda-architecture.net](http://lambda-architecture.net/) dargestellt.

 1. Alle **Daten** werden per Pushvorgang *sowohl* auf die *Batchebene* als auch auf die *Geschwindigkeitsebene* übertragen.
 2. Die **Batchebene** verfügt über ein Masterdataset (unveränderlich, Nur-Anhängen-Satz mit Rohdaten), und es wird eine Vorabberechnung der Batchansichten durchgeführt.
 3. Die **Bereitstellungsebene** verfügt über Batchansichten für schnelle Abfragen. 
 4. Die **Geschwindigkeitsebene** sorgt für eine Kompensation der Verarbeitungszeit (für die Bereitstellungsebene) und ist nur für neuere Daten bestimmt.
 5. Alle Abfragen können beantwortet werden, indem Ergebnisse von Batchansichten und Echtzeitansichten zusammengeführt werden oder einzeln ein Ping ausgeführt wird.

Im weiteren Verlauf dieses Artikels wird deutlich, dass wir diese Architektur implementieren können, indem wir nur die folgenden Mittel verwenden:

* Azure Cosmos DB-Sammlung(en)
* HDInsight-Cluster (Apache Spark 2.1)
* Spark Connector [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Geschwindigkeitsebene

Aus Sicht des Betriebsablaufs kann die Wartung von zwei Datenströmen bei gleichzeitiger Sicherstellung des richtigen Zustands der Daten ein kompliziertes Unterfangen sein. Nutzen Sie zum Vereinfachen der Betriebsabläufe die [Unterstützung durch den Azure Cosmos DB-Änderungsfeed](change-feed.md), um den Zustand für die *Batchebene* beizubehalten, während das Azure Cosmos DB-Änderungsprotokoll über die *Änderungsfeed-API* für Ihre *Geschwindigkeitsebene* bereitgestellt wird.  
![Diagramm mit Hervorhebung der neuen Daten, der Geschwindigkeitsebene und des Masterdataset-Teils der Lambda-Architektur](./media/lambda-architecture/lambda-architecture-change-feed.png)

Wichtige Aspekte dieser Ebenen:

 1. Alle **Daten** werden per Pushvorgang *nur* an Azure Cosmos DB übertragen, sodass Probleme aufgrund von Multicastvorgängen vermieden werden.
 2. Die **Batchebene** verfügt über ein Masterdataset (unveränderlich, Nur-Anhängen-Satz mit Rohdaten), und es wird eine Vorabberechnung der Batchansichten durchgeführt.
 3. Die **Bereitstellungsebene** wird im nächsten Abschnitt beschrieben.
 4. Die **Geschwindigkeitsebene** nutzt HDInsight (Apache Spark), um den Azure Cosmos DB-Änderungsfeed zu lesen. Auf diese Weise können Sie Ihre Daten persistent machen und sie gleichzeitig abfragen und verarbeiten.
 5. Alle Abfragen können beantwortet werden, indem Ergebnisse von Batchansichten und Echtzeitansichten zusammengeführt werden oder einzeln ein Ping ausgeführt wird.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Codebeispiel: Strukturierter Spark-Stream für einen Azure Cosmos DB-Änderungsfeed
Zum Ausführen eines schnellen Prototyps des Azure Cosmos DB-Änderungsfeeds als Teil der **Geschwindigkeitsebene** können Sie einen Test mit Twitter-Daten im Rahmen des Beispiels unter [Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Streamen von Verarbeitungsänderungen per Azure Cosmos DB-Änderungsfeed und Apache Spark) durchführen. Sehen Sie sich als Starthilfe für Ihre Twitter-Ausgabe das Codebeispiel unter [Stream feed from Twitter to Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed) (Streamen des Feeds von Twitter zu Cosmos DB) an. Im vorherigen Beispiel laden Sie Twitter-Daten in Azure Cosmos DB, und anschließend können Sie Ihren HDInsight-Cluster (Apache Spark) einrichten, um eine Verbindung mit dem Änderungsfeed herzustellen. Weitere Informationen zum Einrichten dieser Konfiguration finden Sie unter [Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Setup für Connector von Apache Spark zu Azure Cosmos DB).  

Im folgenden Codeausschnitt wird veranschaulicht, wie Sie `spark-shell` zum Ausführen eines strukturierten Streamauftrags konfigurieren, um eine Verbindung mit einem Azure Cosmos DB-Änderungsfeed herzustellen. Hierbei wird der Twitter-Echtzeit-Datenstrom überprüft, um die Anzahl für das laufende Intervall zu ermitteln.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Vollständige Codebeispiele finden Sie unter [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), z.B.:
* [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Streaming-Abfrage von „Cosmos DB Change Feed.scala“)
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Streaming-Tagabfrage von „Cosmos DB Change Feed.scala“)

Die Ausgabe ist hierbei eine `spark-shell`-Konsole, von der kontinuierlich ein strukturierter Streamauftrag ausgeführt wird, mit dem für die Twitter-Daten des Azure Cosmos DB-Änderungsfeeds eine Intervallanzahl ermittelt wird. In der folgenden Abbildung sind die Ausgabe des Streamauftrags und die Intervallanzahl zu sehen.

![Streamingausgabe mit Intervallanzahl für die Twitter-Daten aus dem Azure Cosmos DB-Änderungsfeed](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Weitere Informationen zum Azure Cosmos DB-Änderungsfeed finden Sie hier:

* [Verwenden der Unterstützung von Änderungsfeeds in Azure Cosmos DB](change-feed.md)
* [Introducing the Azure Cosmos DB Change Feed Processor Library](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/) (Einführung in die Prozessorbibliothek für den Azure Cosmos DB-Änderungsfeed)
* [Änderungen bei der Streamverarbeitung: Azure Cosmos DB-Änderungsfeed und Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- und Bereitstellungsebene
Da die neuen Daten in Azure Cosmos DB geladen werden (wo der Änderungsfeed für die Geschwindigkeitsebene verwendet wird), befindet sich hier das **Masterdataset** (unveränderlich, Nur-Anhängen-Satz mit Rohdaten). Verwenden Sie ab jetzt HDInsight (Apache Spark) zum Durchführen der Funktionen für die Vorabberechnung von der **Batchebene** zur **Bereitstellungsebene**. Dies ist in der folgenden Abbildung dargestellt:

![Diagramm mit Hervorhebung der Batch- und Bereitstellungsebene der Lambda-Architektur](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Wichtige Aspekte dieser Ebenen:

 1. Alle **Daten** werden nur per Pushvorgang an Azure Cosmos DB übertragen (zur Vermeidung von Multicastproblemen).
 2. Für die **Batchebene** ist ein Masterdataset (unveränderlich, Nur-Anhängen-Satz mit Rohdaten) in Azure Cosmos DB gespeichert. Mithilfe von HDI Spark können Sie Ihre Aggregationen vorab berechnen, die in Ihren berechneten Batchansichten gespeichert werden sollen.
 3. Die **Bereitstellungsebene** ist eine Azure Cosmos DB-Datenbank mit Sammlungen für das Masterdataset und die berechnete Batchansicht.
 4. Die **Geschwindigkeitsebene** wird weiter unten in diesem Artikel beschrieben.
 5. Alle Abfragen können beantwortet werden, indem Ergebnisse der Batchansichten und Echtzeitansichten zusammengeführt werden oder einzeln ein Ping ausgeführt wird.

### <a name="code-example-pre-computing-batch-views"></a>Codebeispiel: Vorabberechnung von Batchansichten
Verwenden Sie die folgenden Codeausschnitte aus den Notebooks [Lambda Architecture Rearchitected – Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) (Neu gestaltete Lambda-Architektur – Batchebene) und [Lambda Architecture Rearchitected – Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) (Neu gestaltete Lambda-Architektur – Batch- zu Bereitstellungsebene), um zu veranschaulichen, wie Sie vorab berechnete Ansichten für Ihr **Masterdataset** von Apache Spark zu Azure Cosmos DB ausführen. Verwenden Sie in diesem Szenario die in Azure Cosmos DB gespeicherten Twitter-Daten.

Beginnen Sie, indem Sie die Konfigurationsverbindung mit den Twitter-Daten in Azure Cosmos DB mithilfe des unten angegebenen PySpark-Codes erstellen.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Als Nächstes führen wir die folgende Spark SQL-Anweisung aus, um die Top 10 der Hashtags für den Tweetsatz zu ermitteln. Für diese Spark SQL-Abfrage erfolgt die Ausführung in einem Jupyter-Notebook ohne das Ausgabebalkendiagramm, das direkt auf diesen Codeausschnitt folgt.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Diagramm mit der Anzahl von Tweets pro Hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Nachdem Sie nun über Ihre Abfrage verfügen, können wir sie wieder in einer Sammlung speichern, indem wir den Spark-Connector zum Speichern der Ausgabedaten in einer anderen Sammlung verwenden.  Verwenden Sie in diesem Beispiel Scala, um die Verbindung zu veranschaulichen. Erstellen Sie ähnlich wie im vorherigen Beispiel die Konfigurationsverbindung, um den Apache Spark DataFrame in einer anderen Azure Cosmos DB-Sammlung zu speichern.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Erstellen Sie nach dem Angeben von `SaveMode` (mit Angabe von `Overwrite` oder `Append` für Dokumente) den DataFrame `tweets_bytags` auf ähnliche Weise wie in der Spark SQL-Abfrage im vorherigen Beispiel.  Nachdem der DataFrame `tweets_bytags` erstellt wurde, können Sie ihn mit der `write`-Methode speichern, indem Sie das zuvor angegebene `writeConfig`-Element verwenden.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Mit dieser letzten Anweisung wurde Ihr Spark DataFrame jetzt in einer neuen Azure Cosmos DB-Sammlung gespeichert. Aus Sicht einer Lambda-Architektur ist dies Ihre **Batchansicht** auf **Bereitstellungsebene**.
 
#### <a name="resources"></a>Ressourcen

Vollständige Codebeispiele finden Sie unter [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), z.B.:
* Lambda Architecture Rearchitected – Batch Layer (Neu gestaltete Lambda-Architektur – Batchebene) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected – Batch to Serving Layer (Neu gestaltete Lambda-Architektur – Von Batch- zu Bereitstellungsebene) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Geschwindigkeitsebene
Wie bereits erwähnt, können Sie mit der Azure Cosmos DB-Änderungsfeedbibliothek die Vorgänge zwischen Batch- und Geschwindigkeitsebene vereinfachen. Verwenden Sie in dieser Architektur Apache Spark (per HDInsight), um für die Daten Abfragen vom Typ *strukturiertes Streaming* durchzuführen. Es kann auch ratsam sein, die Ergebnisse Ihrer strukturierten Streamingabfragen vorübergehend beizubehalten, damit andere Systeme auf diese Daten zugreifen können.

![Diagramm mit Hervorhebung der Geschwindigkeitsebene der Lambda-Architektur](./media/lambda-architecture/lambda-architecture-speed.png)

Hierzu erstellen Sie eine separate Azure Cosmos DB-Sammlung zum Speichern der Ergebnisse Ihrer strukturierten Streamingabfragen.  Auf diese Weise können außer Apache Spark auch andere Systeme auf diese Informationen zugreifen. Mit dem Feature „Gültigkeitsdauer“ (Time-to-Live, TTL) von Cosmos DB können Sie Ihre Dokumente so konfigurieren, dass sie nach einer festgelegten Dauer automatisch gelöscht werden.  Weitere Informationen zum Feature „Gültigkeitsdauer“ von Azure Cosmos DB finden Sie unter [Festlegen einer Gültigkeitsdauer für den automatischen Ablauf von Daten in Azure Cosmos DB-Sammlungen](time-to-live.md).

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Lambda-Architektur: Neu gestaltet
Wie in den vorherigen Abschnitten erwähnt, können Sie die ursprüngliche Lambda-Architektur mit den folgenden Komponenten vereinfachen:
* Azure Cosmos DB
* Azure Cosmos DB-Änderungsfeedbibliothek, um zu vermeiden, dass für Ihre Daten zwischen Batch- und Geschwindigkeitsebene ein Multicast durchgeführt werden muss
* Apache Spark in HDInsight
* Spark-Connector für Azure Cosmos DB

![Diagramm: Neugestaltung der Lambda-Architektur mit Azure Cosmos DB, Spark und der Azure Cosmos DB-Änderungsfeed-API](./media/lambda-architecture/lambda-architecture-re-architected.png)

Bei diesem Entwurf benötigen Sie nur zwei verwaltete Dienste: Azure Cosmos DB und HDInsight. Zusammen werden hiermit die Batch-, Bereitstellungs- und Geschwindigkeitsebenen der Lambda-Architektur abgedeckt. Dies führt nicht nur zu einer Vereinfachung der Vorgänge, sondern auch des Datenflusses. 
 1. Alle Daten werden zur Verarbeitung per Pushvorgang an Azure Cosmos DB übertragen.
 2. Die Batchebene verfügt über ein Masterdataset (unveränderlich, Nur-Anhängen-Satz mit Rohdaten), und es wird eine Vorabberechnung der Batchansichten durchgeführt.
 3. Die Bereitstellungsebene verfügt über Batchansichten mit Daten für schnelle Abfragen.
 4. Die Geschwindigkeitsebene sorgt für eine Kompensation der Verarbeitungszeit (für die Bereitstellungsebene) und ist nur für neuere Daten bestimmt.
 5. Alle Abfragen können beantwortet werden, indem Ergebnisse von Batchansichten und Echtzeitansichten zusammengeführt werden.

### <a name="resources"></a>Ressourcen

* **Neue Daten**: Der [Streamfeed von Twitter zu CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed). Dies ist der Mechanismus zum Übertragen von neuen Daten an Azure Cosmos DB per Pushvorgang.
* **Batchebene:** Die Batchebene umfasst das *Masterdataset* (unveränderlich, Nur-Anhängen-Satz mit Rohdaten) und die Möglichkeit zum Vorabberechnen von Batchansichten der Daten, die per Pushvorgang an die **Bereitstellungsebene** übertragen werden.
   * Im Notebook **Lambda Architecture Rearchitected – Batch Layer** (Neu gestaltete Lambda-Architektur – Batchebene) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) wird der *Masterdataset*-Satz mit den Batchansichten abgefragt.
* **Bereitstellungsebene:** Die **Bereitstellungsebene** umfasst vorab berechnete Daten, aus denen sich Batchansichten (z. B. Aggregationen, bestimmte Slicer usw.) für schnelle Abfragen ergeben.
  * Im Notebook **Lambda Architecture Rearchitected – Batch to Serving Layer** (Neu gestaltete Lambda-Architektur – Batch- zu Bereitstellungsebene) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) werden die Batchdaten per Pushvorgang an die Bereitstellungsebene übertragen. Dies bedeutet, dass Spark eine Batchsammlung mit Tweets abfragt, verarbeitet und in einer anderen Sammlung (berechneter Batch) speichert.
    * **Geschwindigkeitsebene:** Bei der **Geschwindigkeitsebene** wird für Spark der Azure Cosmos DB-Änderungsfeed verwendet. Er wird gelesen, und es werden sofort die entsprechenden Maßnahmen getroffen. Die Daten können auch als berechnete Echtzeitdaten (*Computed RT*) gespeichert werden, damit andere Systeme die verarbeiteten Echtzeitdaten abfragen können, anstatt selbst eine Echtzeitabfrage durchzuführen.
  * Mit dem Scala-Skript unter [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Streamingabfrage für Cosmos DB-Änderungsfeed) wird eine Streamingabfrage aus dem Azure Cosmos DB-Änderungsfeed ausgeführt, um eine Intervallanzahl über die Spark-Shell zu berechnen.
  * Mit dem Scala-Skript unter [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Streaming-Tagabfrage für Cosmos DB-Änderungsfeed) wird eine Streamingabfrage aus dem Azure Cosmos DB-Änderungsfeed ausgeführt, um eine Intervallanzahl für Tags über die Spark-Shell zu berechnen.
  
## <a name="next-steps"></a>Nächste Schritte
Laden Sie – falls noch nicht geschehen – den Spark-Connector für Azure Cosmos DB aus dem GitHub-Repository [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) herunter, und sehen Sie sich die zusätzlichen Ressourcen im Repository an:
* [Lambda architecture](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) (Lambda-Architektur)
* [Distributed aggregations examples](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples) (Beispiele für verteilte Aggregationen)
* [Sample scripts and notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples) (Beispielskripts und Notebooks)
* [Structured streaming demos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos) (Demos für strukturiertes Streaming)
* [Change feed demos](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos) (Demos zu Änderungsfeeds)
* [Stream processing changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Streamen von Verarbeitungsänderungen per Azure Cosmos DB-Änderungsfeed und Apache Spark)

Es kann auch ratsam sein, die Informationen unter [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Leitfaden zu Apache Spark SQL, DataFrames und Datasets) und den Artikel [Apache Spark on Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) zu lesen.
