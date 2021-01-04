---
title: Kopieren der Daten von Synapse Link für Azure Cosmos DB in einen dedizierten SQL-Pool mithilfe von Apache Spark
description: Laden der Daten in einen Spark-Datenrahmen, Zusammenstellen der Daten und Laden der Daten in eine Tabelle im dedizierten SQL-Pool
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 13891f9614e658be39adbb69fed1503a0c66d5e4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309226"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Kopieren von Daten aus Azure Cosmos DB in einen dedizierten SQL-Pool mithilfe von Apache Spark

Azure Synapse Link für Azure Cosmos DB ermöglicht Benutzern das Ausführen von Analysen in Quasi-Echtzeit über operative Daten in Azure Cosmos DB. Manchmal müssen einige Daten für die Nutzung durch Data Warehouse-Benutzer jedoch aggregiert und angereichert werden. Synapse Link-Daten können mit wenigen Zellen in einem Notebook zusammengestellt und exportiert werden.

## <a name="prerequisites"></a>Voraussetzungen
* [Bereitstellen eines Synapse-Arbeitsbereichs](../quickstart-create-workspace.md) mit:
    * [Serverloser Apache Spark-Pool](../quickstart-create-apache-spark-pool-studio.md)
    * [Dedizierter SQL-Pool](../quickstart-create-sql-pool-studio.md)
* [Bereitstellen eines Cosmos DB-Kontos mit einem HTAP-Container mit Daten](../../cosmos-db/configure-synapse-link.md)
* [Herstellen einer Verbindung zwischen dem Azure Cosmos DB-HTAP-Container mit dem Arbeitsbereich](./how-to-connect-synapse-link-cosmos-db.md)
* [Einrichten des richtigen Setups zum Importieren von Daten in einen dedizierten SQL-Pool aus Spark](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Schritte
In diesem Tutorial stellen Sie eine Verbindung mit dem Analysespeicher her, sodass der Transaktionsspeicher nicht beeinträchtigt wird (es werden keine Anforderungseinheiten beansprucht). Wir durchlaufen die folgenden Schritte:
1. Einlesen des Cosmos DB-HTAP-Containers in einen Spark-Datenrahmen
2. Aggregieren der Ergebnisse in einem neuen Datenrahmen
3. Erfassen der Daten in einem dedizierten SQL-Pool

[![Schritte für Spark zu SQL 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Daten
In diesem Beispiel verwenden wir einen HTAP-Container namens **RetailSales**. Er ist Teil eines verknüpften Diensts namens **ConnectedData** und hat das folgende Schema:
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

Die Verkäufe ( *quantity* , *revenue* (Preis × Menge) werden zur Berichterstellung nach *productCode* und *weekStarting* aggregiert. Schließlich werden diese Daten in eine Tabelle im dedizierten SQL-Pool mit dem Namen **dbo.productsales** exportiert.

## <a name="configure-a-spark-notebook"></a>Konfigurieren eines Spark-Notebooks
Erstellen Sie ein Spark-Notebook mit Scala as Spark (Scala) als Hauptsprache. Für die Sitzung wird die Standardeinstellung des Notebooks verwendet.

## <a name="read-the-data-in-spark"></a>Lesen der Daten in Spark
Lesen Sie den Cosmos DB-HTAP-Container mit Spark in einem Datenrahmen in der ersten Zelle.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Aggregieren der Ergebnisse in einem neuen Datenrahmen

In der zweiten Zelle führen Sie die Transformation und Aggregate aus, die für den neuen Datenrahmen benötigt werden, bevor er in eine Datenbank im dedizierten SQL-Pool geladen wird.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>Laden der Ergebnisse in einen dedizierten SQL-Pool

In der dritten Zelle werden die Daten in einen dedizierten SQL-Pool geladen. Es werden automatisch eine temporäre externe Tabelle, eine externe Datenquelle und ein externes Dateiformat erstellt, die nach Abschluss des Auftrags gelöscht werden.

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Abfragen der Ergebnisse mit SQL

Sie können das Ergebnis mithilfe einer einfachen SQL-Abfrage abfragen, etwa mit dem folgenden SQL-Skript:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Ihre Abfrage zeigt die folgenden Ergebnisse in einem Diagrammmodus: [![Schritte für Spark zu SQL 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte
* [Abfragen des Azure Cosmos DB-Analysespeichers mit Apache Spark](./how-to-query-analytical-store-spark.md)