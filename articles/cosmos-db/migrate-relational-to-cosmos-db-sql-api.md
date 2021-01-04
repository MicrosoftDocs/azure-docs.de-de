---
title: Migrieren von relationalen Daten mit 1:n-Beziehungen zur Azure Cosmos DB-SQL-API
description: Informationen zum Bewältigen einer komplexen Migration von Daten mit 1:n-Beziehungen zur SQL-API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096749"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrieren von relationalen Daten mit 1:n-Beziehungen in ein Azure Cosmos DB-SQL-API-Konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zum Migrieren von einer relationalen Datenbank zur Azure Cosmos DB-SQL-API müssen möglicherweise Änderungen am Datenmodell zur Optimierung vorgenommen werden.

Eine gängige Transformation ist die Denormalisierung von Daten durch das Einbetten verwandter Unterelemente innerhalb eines JSON-Dokuments. In diesem Artikel werden einige Optionen dafür unter Verwendung von Azure Data Factory oder Azure Databricks aufgezeigt. Allgemeine Anleitungen zur Datenmodellierung für Cosmos DB finden Sie unter [Datenmodellierung in Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Beispielszenario

Angenommen unsere SQL-Datenbank enthält die beiden Tabellen „Orders“ und „OrderDetails“.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Screenshot mit Tabellen „Orders“ und „OrderDetails“ in der SQL-Datenbank" border="false" :::

Wir möchten diese 1:n-Beziehung während der Migration zu einem JSON-Dokument kombinieren. Hierzu können wir eine T-SQL-Abfrage mit „FOR JSON“ (wie nachstehend dargestellt) erstellen:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Die Ergebnisse dieser Abfrage würden wie folgt aussehen: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Auftragsdetails" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Im Idealfall würden Sie in Azure Data Factory (ADF) eine einzige Kopieraktivität ausführen, um SQL-Daten als Quelle abzufragen und die Ausgabe direkt als ordnungsgemäße JSON-Objekte in die Azure Cosmos DB-Senke zu schreiben. Aktuell ist es nicht möglich, die erforderliche JSON-Transformation in einer Kopieraktivität auszuführen. Wenn wir versuchen, die Ergebnisse der obigen Abfrage in einen Azure Cosmos DB-SQL-API-Container zu kopieren, wird das Feld „OrderDetails“ als Zeichenfolgeneigenschaft des Dokuments und nicht das erwartete JSON-Array angezeigt.

Wir können diese aktuelle Einschränkung auf folgende Weise umgehen:

* **Verwenden von Azure Data Factory mit zwei Kopieraktivitäten** : 
  1. Speichern Sie JSON-formatierte Daten aus SQL in einer Textdatei an einem zwischengeschalteten Blobspeicherort, und 
  2. Laden Sie die Daten aus der JSON-Textdatei in einen Container in Azure Cosmos DB.

* **Verwenden von Azure Databricks zum Lesen aus SQL und zum Schreiben in Azure Cosmos DB** – Wir stellen hier zwei Optionen vor.


Nachfolgend werden diese Ansätze ausführlicher erörtert:

## <a name="azure-data-factory"></a>Azure Data Factory

„OrderDetails“ kann zwar nicht als JSON-Array in das Cosmos DB-Zieldokument eingebettet werden, aber wir können das Problem mit zwei separaten Kopieraktivitäten umgehen.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Kopieraktivität Nummer 1: SqlJsonToBlobText

Für die Quelldaten verwenden wir eine SQL-Abfrage, um das Resultset als einzelne Spalte mit einem (den Auftrag darstellenden) JSON-Objekt pro Zeile mithilfe der SQL Server-Funktionen OPENJSON und FOR JSON PATH abzurufen:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF-Kopieraktivität":::


Für die Senke der Kopieraktivität „SqlJsonToBlobText“ wählen wir „Durch Trennzeichen getrennter Text“ aus und verweisen auf einen bestimmten Ordner in Azure Blob Storage mit einem dynamisch generierten eindeutigen Dateinamen (z. B. „@concat(pipeline().RunId,'.json')“.
Da unsere Textdatei nicht wirklich „durch Trennzeichen getrennt“ ist und nicht in separate Spalten mit Kommas analysiert werden soll und die doppelten Anführungszeichen (") beibehalten werden sollen, legen wir „Spaltentrennzeichen“ auf „Tabstopp (\t)“ oder auf ein anderes Zeichen, das nicht in den Daten vorkommt, und „Anführungszeichen“ auf „Kein Anführungszeichen“ fest.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Screenshot, der die Einstellungen für das Spaltentrennzeichen und die Anführungszeichen hervorhebt.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Kopieraktivität Nummer 2: BlobJsonToCosmos

Als Nächstes ändern wir die ADF-Pipeline, indem wir die zweite Kopieraktivität hinzufügen, die in Azure Blob Storage nach der Textdatei sucht, die von der ersten Aktivität erstellt wurde. Sie wird als „JSON“-Quelle verarbeitet und als ein Dokument pro in der Textdatei gefundener JSON-Zeile in die Cosmos DB-Senke eingefügt.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Screenshot, der die JSON-Quelldatei und die Dateipfadfelder hervorhebt.":::

Optional fügen wir der Pipeline auch eine Aktivität „Löschen“ hinzu, sodass alle im Ordner „/Orders/“ verbliebenen vorherigen Dateien vor jeder Ausführung gelöscht werden. Unsere ADF-Pipeline sieht jetzt in etwa wie folgt aus:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Screenshot, der die Aktivität „Löschen“ hervorhebt.":::

Nachdem wir die oben genannte Pipeline ausgelöst haben, wird in unserem Azure Blob Storage-Zwischenspeicherort eine Datei erstellt, die ein JSON-Objekt pro Zeile enthält:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Screenshot, der die erstellte Datei mit den JSON-Objekten anzeigt.":::

Außerdem werden Dokumente des Ordners „Orders“ mit ordnungsgemäß eingebetteten „OrderDetails“ in unsere Cosmos DB-Sammlung eingefügt:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Screenshot, der die „OrderDetails“ als Teil des Cosmos DB-Dokuments anzeigt.":::


## <a name="azure-databricks"></a>Azure Databricks

Wir können auch Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) verwenden, um die Daten aus unserer SQL-Datenbankquelle in das Azure Cosmos DB-Ziel zu kopieren, ohne die Text-/JSON-Zwischendateien in Azure Blob Storage zu erstellen. 

> [!NOTE]
> Der Klarheit und Einfachheit halber enthalten die nachstehenden Codeausschnitte (explizit inline dargestellte) Dummy-Datenbankkennwörter. Sie sollten jedoch immer Azure Databricks-Geheimnisse verwenden.
>

Zuerst erstellen wir die erforderlichen [SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html)-Connector- und [Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html)-Connectorbibliotheken und fügen sie an unseren Azure Databricks-Cluster an. Starten Sie den Cluster neu, um sicherzustellen, dass die Bibliotheken geladen werden.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Screenshot, der zeigt, wo der erforderliche SQL-Connector und die Azure Cosmos DB Connector-Bibliotheken für den Azure Databricks-Cluster erstellt und angefügt werden.":::

Als Nächstes stellen wir zwei Beispiele für Scala und Python vor. 

### <a name="scala"></a>Scala
Hier rufen wir die Ergebnisse der SQL-Abfrage mit der „FOR JSON“-Ausgabe in einem Datenrahmen ab:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Screenshot, der die Ausgabe der SQL-Abfrage in einem Datenrahmen anzeigt.":::

Als Nächstes stellen wir eine Verbindung mit unserer Cosmos DB-Datenbank und -Sammlung her:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Zum Schluss definieren wir unser Schema und verwenden „from_json“, um den Datenrahmen vor dem Speichern in der Cosmos DB-Sammlung anzuwenden.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Screenshot, der das richtige Array zum Speichern in einer Cosmos DB-Sammlung hervorhebt.":::


### <a name="python"></a>Python

Als Alternative müssen Sie möglicherweise JSON-Transformationen in Spark ausführen (wenn die Quelldatenbank „FOR JSON“ oder einen vergleichbaren Vorgang nicht unterstützt), oder Sie möchten parallele Vorgänge für ein sehr großes Dataset verwenden. Hier stellen wir ein PySpark-Beispiel vor. Zuerst werden die Quell- und Zieldatenbankverbindungen in der ersten Zelle konfiguriert:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Anschließend fragen wir die Quelldatenbank (in diesem Fall SQL Server) nach allen „orders“- und „orderdetails“-Datensätzen ab, wobei die Ergebnisse in Spark-Datenrahmen angegeben werden. Wir erstellen auch eine Liste mit allen Auftrags-IDs und einen Threadpool für parallele Vorgänge:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Erstellen Sie dann eine Funktion zum Schreiben von Aufträgen in die SQL-API-Zielsammlung. Diese Funktion filtert alle Auftragsdetails für die angegebene Auftrags-ID, konvertiert sie in ein JSON-Array und fügt das Array in ein JSON-Dokument ein, das in die SQL-API-Zielsammlung für den Auftrag geschrieben wird:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Zum Schluss rufen wir die obige Funktion mithilfe einer Zuordnungsfunktion für den Threadpool auf, die parallel ausgeführt wird und die zuvor erstellte Liste der Auftrags-IDs übergibt:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Bei beiden Ansätzen sollten wir am Ende ordnungsgemäß gespeicherte eingebettete „OrderDetails“ in jedem „Order“-Dokument in der Cosmos DB-Sammlung erhalten:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie [Datenmodellierung in Azure Cosmos DB](./modeling-data.md).
* Lesen Sie [Modellieren und Partitionieren von Daten in Azure Cosmos DB](./how-to-model-partition-example.md).