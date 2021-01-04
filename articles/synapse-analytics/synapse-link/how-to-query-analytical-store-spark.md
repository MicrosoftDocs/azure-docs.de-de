---
title: Interagieren mit Azure Cosmos DB mithilfe von Apache Spark in Azure Synapse Link (Vorschau)
description: Interagieren mit Azure Cosmos DB mithilfe von Apache Spark in Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 28af603c0969419cd2e7b8683373faf3838e2242
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458940"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Interagieren mit Azure Cosmos DB mithilfe von Apache Spark in Azure Synapse Link

In diesem Artikel erfahren Sie, wie Sie mit Azure Cosmos DB mithilfe von Synapse Apache Spark interagieren. Synapse Apache Spark bietet vollständige Unterstützung für Scala, Python, SparkSQL und C# und ist essentiell für Analyse-, Datentechnik-, Data-Science-, und Datenerkundungsszenarios in [Azure Synapse Link für Azure Cosmos DB](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Die folgenden Funktionen werden für die Interaktion mit Azure Cosmos DB unterstützt:
* Mit Synapse Apache Spark können Sie Daten in Ihren Azure Cosmos DB-Containern mit Azure Synapse Link-Aktivierung in Quasi-Echtzeit analysieren, ohne dass sich dies auf die Leistung Ihrer Transaktionsarbeitsauslastungen auswirkt. Die folgenden beiden Optionen stehen zur Verfügung, um den [Analysespeicher](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) von Azure Cosmos DB in Spark abzufragen:
    + Laden in Datenrahmen in Spark
    + Spark-Tabelle erstellen
* Synapse Apache Spark ermöglicht es Ihnen außerdem, Daten in Azure Cosmos DB zu erfassen. Es ist wichtig, im Hinterkopf zu behalten, dass Daten immer über den Transaktionsspeicher in Azure Cosmos DB-Container erfasst werden. Wenn Synapse Link aktiviert ist, dann werden alle neuen Einfüge-, Aktualisierungs- und Löschvorgänge automatisch mit dem Analysespeicher synchronisiert.
* Synapse Apache Spark unterstützt außerdem strukturiertes Streaming in Spark mit Azure Cosmos DB als Quelle sowie als Senke. 

Die folgenden Abschnitte führen Sie durch die Syntax der oben genannten Funktionen. Gesten in Azure Synapse Analytics-Arbeitsbereichen dienen einem einfachen Start der Verwendung. Gesten werden angezeigt, wenn Sie auf einen Azure Cosmos DB-Container auf der Registerkarte **Daten** im Synapse-Arbeitsbereich rechtsklicken. Mit Gesten können Sie schnell Code generieren und an Ihre Anforderungen anpassen. Gesten eignen sich auch ideal zum Ermitteln von Daten mit nur einem Mausklick.

## <a name="query-azure-cosmos-db-analytical-store"></a>Abfragen von Azure Cosmos DB-Analysespeicher

Bevor Sie Informationen zu den zwei verfügbaren Optionen erhalten, mit denen der Analysespeicher in Azure Cosmos DB abgefragt werden kann, mit denen Ladevorgänge für Datenrahmen in Spark ausgeführt und Spark-Tabellen erstellt werden können, sollten Sie sich die Unterschiede genauer ansehen, damit Sie die Option auswählen können, die Ihre Anforderungen am besten erfüllt.

Ein Unterschied liegt darin, ob die zugrunde liegenden Datenänderungen im Azure Cosmos DB-Container automatisch in der in Spark durchgeführten Analyse widergespiegelt werden sollten oder nicht. Wenn für den Analysespeicher eines Containers entweder ein Datenrahmen in Spark registriert oder eine Tabelle in Spark erstellt wird, werden die Metadaten zur aktuellen Momentaufnahme der Daten im Analysespeicher in Spark abgerufen, um eine effiziente Weitergabe für anschließende Analysen zu ermöglichen. Denken Sie daran, dass die tatsächlichen Daten nicht aus dem Analysespeicher des zugrunde liegenden Containers abgerufen werden, da Spark eine verzögerte Auswertungsrichtlinie befolgt, es sei denn, eine Aktion wird für den Datenrahmen in Spark durchgeführt oder für die Tabelle in Spark wird eine SparkSQL-Abfrage ausgeführt.

Im Fall des **Ladens für einen Datenrahmen in Spark** werden die abgerufenen Metadaten für die Lebensdauer der Spark-Sitzung zwischengespeichert, und folglich werden Aktionen, die anschließend für den Datenrahmen aufgerufen werden, für die Momentaufnahme des Analysespeichers zum Zeitpunkt der Erstellung des Datenrahmens bewertet.

Andererseits werden die Metadaten des Analysespeichers im Fall des **Erstellens einer Spark-Tabelle** nicht in Spark zwischengespeichert und für jede SparkSQL-Abfrageausführung für die Spark-Tabelle neu geladen.

Folglich können Sie entscheiden, ob für den Datenrahmen in Spark geladen werden soll oder ob eine Spark-Tabelle erstellt werden soll. Dies basiert darauf, ob Sie Ihre Spark-Analyse jeweils für eine festgelegte Momentaufnahme des Analysespeichers oder für eine aktuelle Momentaufnahme des Analysespeichers bewerten möchten.

> [!NOTE]
> Wenn Sie die Azure Cosmos DB-API von Mongo DB-Konten abfragen möchten, finden Sie [hier](../../cosmos-db/analytical-store-introduction.md#analytical-schema) weitere Informationen zur Schemadarstellung mit vollständiger Genauigkeit im Analysespeicher und den dabei zu verwendenden Namen für erweiterte Eigenschaften.

### <a name="load-to-spark-dataframe"></a>Laden in Datenrahmen in Spark

In diesem Beispiel erstellen Sie einen Datenrahmen in Spark, der auf den Analysespeicher in Azure Cosmos DB verweist. Auf dieser Grundlage können Sie zusätzliche Analysen durchführen, indem Sie Spark-Aktionen für den Datenrahmen aufrufen. Dieser Vorgang wirkt sich nicht auf den Transaktionsspeicher aus.

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark-Tabelle erstellen

In diesem Beispiel erstellen Sie eine Tabelle in Spark, die auf den Analysespeicher in Azure Cosmos DB verweist. Auf dieser Grundlage können Sie zusätzliche Analysen durchführen, indem Sie SparkSQL-Abfragen für die Tabelle aufrufen. Dieser Vorgang wirkt sich weder auf den Transaktionsspeicher aus, noch führt er zu Datenverschiebungen jeglicher Art. Wenn Sie diese Spark-Tabelle löschen möchten, wirkt sich dies nicht auf den zugrunde liegenden Azure Cosmos DB-Container (und den entsprechenden Analysespeicher) aus. 

Dieses Szenario ist praktisch, wenn Sie Spark-Tabellen über Drittanbietertools wiederverwenden und den Zugriff auf die zugrunde liegenden Daten für die Laufzeit ermöglichen möchten.

Die Syntax für das Erstellen einer Spark-Tabelle ist folgende:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Wenn Sie über Szenarios verfügen, in denen sich das Schema des zugrunde liegenden Azure Cosmos DB-Containers im Laufe der Zeit ändert, und wenn das aktualisierte Schema automatisch in Abfragen für die Spark-Tabelle widergespiegelt werden soll, können Sie dies festlegen, indem die `spark.cosmos.autoSchemaMerge`-Option in den Optionen für Spark-Tabellen auf `true` festgelegt wird.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Schreiben eines Spark-Datenrahmens in Azure Cosmos DB-Container

In diesem Beispiel schreiben Sie einen Spark-Datenrahmen in einen Azure Cosmos DB-Container. Dieser Vorgang wirkt sich auf die Leistung transaktionaler Arbeitsauslastungen aus und benötigt Anforderungseinheiten, die im Azure Cosmos DB-Container oder in der freigegebenen Datenbank bereitgestellt werden.

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Streamingdatenrahmen aus Container laden
Bei dieser Geste verwenden Sie die Spark-Streamingfunktion, um Daten aus einem Container in einen Datenrahmen zu laden. Die Daten werden im primären Data Lake-Konto (und Dateisystem) gespeichert, das Sie mit dem Arbeitsbereich verbunden haben. 
> [!NOTE]
> Wenn Sie Verweise auf externe Bibliotheken in Synapse Apache Spark hinzufügen möchten, finden Sie [hier](#external-library-management) weitere Informationen. Wenn Sie beispielsweise einen Spark-Datenrahmen in einem Container der Cosmos DB-API für Mongo DB erfassen möchten, können Sie den Mongo DB-Connector für Spark [hier](https://docs.mongodb.com/spark-connector/master/) nutzen.

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Laden von Streamingdatenrahmen aus Azure Cosmos DB-Containern
In diesem Beispiel verwenden Sie die Funktion für strukturiertes Streaming in Spark, um Daten aus einem Azure Cosmos DB-Container in einen Streamingdatenrahmen in Spark zu laden. Dafür nutzen Sie die Änderungsfeedfunktion in Azure Cosmos DB. Die von Spark verwendeten Prüfpunktdaten werden im primären Data Lake-Konto (und Dateisystem) gespeichert, das Sie mit dem Arbeitsbereich verbunden haben.

Wurde der Ordner */localReadCheckpointFolder* nicht erstellt (siehe Beispiel unten), wird er automatisch erstellt. Dieser Vorgang wirkt sich auf die Leistung transaktionaler Arbeitsauslastungen aus und benötigt Anforderungseinheiten, die im Azure Cosmos DB-Container oder in der freigegebenen Datenbank bereitgestellt werden.

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Schreiben von Streamingdatenrahmen in Azure Cosmos DB-Container
In diesem Beispiel schreiben Sie einen Streamingdatenrahmen in einen Azure Cosmos DB-Container. Dieser Vorgang wirkt sich auf die Leistung transaktionaler Arbeitsauslastungen aus und benötigt Anforderungseinheiten, die im Azure Cosmos DB-Container oder in der freigegebenen Datenbank bereitgestellt werden. Wurde der Ordner */localWriteCheckpointFolder* nicht erstellt (siehe Beispiel unten), wird er automatisch erstellt. 

Die Syntax in **Python** sieht folgendermaßen aus:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Die entsprechende Syntax in **Scala** entspräche dem folgenden Code:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Verwaltung externer Bibliotheken

In diesem Beispiel erfahren Sie, wie Sie Verweise auf externe Bibliotheken in JAR-Dateien hinzufügen, wenn Spark-Notebooks in Synpase Apache Spark-Arbeitsbereichen verwendet werden. Sie können die JAR-Dateien in einem Container im primären Data-Lake-Konto speichern, das Sie mit dem Arbeitsbereich verbunden haben, und dann die folgende `%configure`-Anweisung in Ihrem Spark-Notebook hinzufügen:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Wenn Sie Definitionen für Remoteaufträge in Spark einem serverlosen Apache Spark-Pool hinzufügen möchten, erfahren Sie in [diesem Tutorial](../spark/apache-spark-job-definitions.md), wie Sie Verweise auf externe Bibliotheken hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

* [Beispiele für die ersten Schritte mit Azure Synapse Link auf GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Von Azure Synapse Link unterstützte Features für Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Herstellen einer Verbindung mit Synapse Link für Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
