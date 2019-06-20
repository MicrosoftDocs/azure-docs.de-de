---
title: Verwenden von Apache Spark zum Lesen und Schreiben von Daten in Azure SQL-Datenbank
description: Informationen zum Einrichten einer Verbindung zwischen einem HDInsight Spark-Cluster und einer Azure SQL-Datenbank zum Lesen, Schreiben und Streamen von Daten in einer SQL-Datenbank
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 3812cf55a26a12ef110b8acf14edd0e8bfd36851
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236530"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Verwenden eines HDInsight Spark-Clusters zum Lesen und Schreiben von Daten in einer Azure SQL-Datenbank

In diesem Artikel wird erläutert, wie Sie eine Verbindung zwischen einem Apache Spark-Cluster in Azure HDInsight und einer Azure SQL-Datenbank herstellen und dann Daten in der SQL-Datenbank lesen, schreiben und streamen. In diesem Artikel wird ein [Jupyter Notebook](https://jupyter.org/) zum Ausführen der Scala-Codeausschnitte verwendet. Sie können jedoch auch eine eigenständige Anwendung in Scala oder Python erstellen und die gleichen Aufgaben ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure HDInsight Spark-Cluster**.  Befolgen Sie dazu die Anweisungen unter [Erstellen eines Apache Spark-Clusters in HDInsight](apache-spark-jupyter-spark-sql.md).

* **Azure SQL-Datenbank**. Befolgen Sie dazu die Anweisungen unter [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../../sql-database/sql-database-get-started-portal.md). Stellen Sie sicher, dass Sie eine Datenbank mit dem **AdventureWorksLT**-Beispielschema und den zugehörigen Daten erstellen. Stellen Sie außerdem sicher, dass Sie eine Firewallregel auf Serverebene erstellen, sodass die IP-Adresse des Clients auf die SQL-Datenbank auf dem Server zugreifen kann. Die Anweisungen zum Hinzufügen der Firewallregel sind im gleichen Artikel enthalten. Halten Sie nach dem Erstellen der Azure SQL-Datenbank die folgenden Werte bereit. Sie benötigen sie, um über einen Spark-Cluster eine Verbindung mit der Datenbank herzustellen.

    * Name des Servers, der die Azure SQL-Datenbank hostet.
    * Name der Azure SQL-Datenbank.
    * Benutzername/Kennwort des Azure SQL-Datenbankadministrators.

* **SQL Server Management Studio**: Befolgen Sie dazu die Anweisungen unter [Verwenden von SQL Server Management Studio zum Herstellen der Verbindung und Abfragen von Daten](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Erstellen eines Jupyter Notebooks 

Erstellen Sie zunächst ein dem Spark-Cluster zugeordnetes [Jupyter Notebook](https://jupyter.org/). Sie verwenden dieses Notebook, um die in diesem Artikel verwendeten Codeausschnitte auszuführen. 

1. Öffnen Sie Ihren Cluster im [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie auf der rechten Seite unterhalb von **Clusterdashboards** die Option **Jupyter Notebook** aus.  Sollte **Clusterdashboards** nicht angezeigt werden, wählen Sie im linken Menü die Option **Übersicht** aus. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

    ![Jupyter-Notebook unter Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter-Notebook unter Spark")
   
   > [!NOTE]  
   > Sie können das Jupyter Notebook im Spark-Cluster auch aufrufen, indem Sie im Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Klicken Sie im Jupyter Notebook oben rechts auf **New** (Neu) und dann auf **Spark**, um ein Scala-Notebook zu erstellen. Jupyter Notebooks in einem HDInsight Spark-Cluster umfassen zudem den **PySpark**-Kernel für Python2-Anwendungen und den **PySpark3**-Kernel für Python3-Anwendungen. In diesem Artikel wird ein Scala-Notebook erstellt.
   
    ![Kernel für Jupyter-Notebooks unter Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernel für Jupyter-Notebooks unter Spark")

    Weitere Informationen zu den Kernels finden Sie unter [Verfügbare Kernels für Jupyter Notebooks mit Apache Spark-Clustern unter HDInsight (Linux)](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > In diesem Artikel wird ein Spark-Kernel (Scala) verwendet, da das Streamen von Daten von Spark in eine SQL-Datenbank derzeit nur in Scala und Java unterstützt wird. Das Lesen und Schreiben von Daten in SQL kann zwar auch über Python erfolgen. Aus Gründen der Einheitlichkeit wird in diesem Artikel jedoch Scala für alle drei Vorgänge verwendet.

1. Ein neues Notebook mit dem Standardnamen **Untitled** wird geöffnet. Klicken Sie auf den Namen des Notebooks, und geben Sie den gewünschten Namen ein.

    ![Angeben eines neuen Namens für das Notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Angeben eines neuen Namens für das Notebook")

Nun können Sie die Anwendung erstellen.
    
## <a name="read-data-from-azure-sql-database"></a>Lesen von Daten aus der Azure SQL-Datenbank

In diesem Abschnitt lesen Sie Daten aus einer Tabelle (z.B. **SalesLT.Address**) in der AdventureWorks-Datenbank.

1. Fügen Sie den folgenden Codeausschnitt in einem neuen Jupyter Notebook in einer Codezelle ein, und ersetzen Sie die Platzhalterwerte durch die Werte für Ihre Azure SQL-Datenbank.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Drücken Sie **UMSCHALT+EINGABE**, um die Codezelle auszuführen.  

1. Verwenden Sie den folgenden Codeausschnitt, um eine JDBC-URL, die Sie an die Spark-Dataframe-APIs übergeben können, und ein `Properties`-Objekt zum Speichern der Parameter zu erstellen. Fügen Sie den Codeausschnitt in einer Codezelle ein, und drücken Sie **UMSCHALT+EINGABE**, um sie auszuführen.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Verwenden Sie den folgenden Codeausschnitt, um einen Dataframe mit den Daten aus einer Tabelle in der Azure SQL-Datenbank zu erstellen. In diesem Codeausschnitt wird die Tabelle **SalesLT.Address** verwendet, die als Bestandteil der **AdventureWorksLT**-Datenbank verfügbar ist. Fügen Sie den Codeausschnitt in einer Codezelle ein, und drücken Sie **UMSCHALT+EINGABE**, um sie auszuführen.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Sie können nun Vorgänge für den Dataframe ausführen, z.B. Abrufen des Datenschemas:

       sqlTableDF.printSchema
   
    Eine Ausgabe ähnlich der folgenden wird angezeigt:

    ![Angeben eines neuen Namens für das Notebook](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Angeben eines neuen Namens für das Notebook")

1. Sie können beispielsweise auch die ersten 10 Zeilen abrufen.

       sqlTableDF.show(10)

1. Oder Sie können bestimmte Spalten aus dem Dataset abrufen.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Schreiben von Daten in die Azure SQL-Datenbank

In diesem Abschnitt wird über eine im Cluster verfügbare CSV-Datei eine Tabelle in der Azure SQL-Datenbank erstellt und diese mit Daten gefüllt. Die CSV-Beispieldatei (**HVAC.csv**) steht für alle HDInsight-Cluster unter `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` zur Verfügung.

1. Fügen Sie den folgenden Codeausschnitt in einem neuen Jupyter Notebook in einer Codezelle ein, und ersetzen Sie die Platzhalterwerte durch die Werte für Ihre Azure SQL-Datenbank.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Drücken Sie **UMSCHALT+EINGABE**, um die Codezelle auszuführen.  

1. Mit dem folgenden Codeausschnitt werden eine JDBC-URL, die Sie an die Spark-Dataframe-APIs übergeben können, und ein `Properties`-Objekt zum Speichern der Parameter erstellt. Fügen Sie den Codeausschnitt in einer Codezelle ein, und drücken Sie **UMSCHALT+EINGABE**, um sie auszuführen.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. Verwenden Sie den folgenden Codeausschnitt, um das Schema der Daten in der Datei „HVAC.csv“ zu extrahieren und das Schema zum Laden der Daten aus der CSV-Datei in den Dataframe `readDf` zu verwenden. Fügen Sie den Codeausschnitt in einer Codezelle ein, und drücken Sie **UMSCHALT+EINGABE**, um sie auszuführen.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Verwenden Sie den Dataframe `readDf` zum Erstellen der temporären Tabelle `temphvactable`. Verwenden Sie die temporäre Tabelle dann zum Erstellen der Hive-Tabelle `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Verwenden Sie schließlich die Hive-Tabelle zum Erstellen einer Tabelle in der Azure SQL-Datenbank. Mit dem folgenden Codeausschnitt wird `hvactable` in der Azure SQL-Datenbank erstellt.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Stellen Sie über SSMS eine Verbindung mit der Azure SQL-Datenbank her, und überprüfen Sie, ob darin `dbo.hvactable` angezeigt wird.

    a. Starten Sie SSMS, und stellen Sie unter Angabe der entsprechenden Verbindungsdetails (siehe dazu Screenshot unten) eine Verbindung mit der Azure SQL-Datenbank her.

    ![Herstellen einer Verbindung mit der SQL-Datenbank über SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Herstellen einer Verbindung mit der SQL-Datenbank über SSMS")

    b. Erweitern Sie im Objekt-Explorer die Azure SQL-Datenbank und den Knoten „Tables“, um die erstellte Tabelle **dbo.hvactable** anzuzeigen.

    ![Herstellen einer Verbindung mit der SQL-Datenbank über SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Herstellen einer Verbindung mit der SQL-Datenbank über SSMS")

1. Führen Sie eine Abfrage in SSMS aus, um die Spalten in der Tabelle zu sehen.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Streamen von Daten in Azure SQL-Datenbank

In diesem Abschnitt werden Daten in die Tabelle **hvactable** gestreamt, die Sie im vorherigen Abschnitt in der Azure SQL-Datenbank erstellt haben.

1. Stellen Sie in einem ersten Schritt sicher, dass in **hvactable** keine Datensätze vorhanden sind. Führen Sie unter Verwendung von SSMS die folgende Abfrage für die Tabelle aus.

       TRUNCATE TABLE [dbo].[hvactable]

1. Erstellen Sie ein neues Jupyter Notebook im HDInsight Spark-Cluster. Fügen Sie den folgenden Codeausschnitt in einer Codezelle ein, und drücken Sie **UMSCHALT+EINGABE**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Die Daten aus der Datei **HVAC.csv** werden in die Tabelle „hvactable“ gestreamt. Die Datei „HVAC.csv“ ist auf dem Cluster unter `/HdiSamples/HdiSamples/SensorSampleData/HVAC/` verfügbar. Mit dem folgenden Codeausschnitt wird zunächst das Schema der zu streamenden Daten abgerufen. Anschließend wird unter Verwendung dieses Schemas ein Streaming-Dataframe erstellt. Fügen Sie den Codeausschnitt in einer Codezelle ein, und drücken Sie **UMSCHALT+EINGABE**, um sie auszuführen.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. In der Ausgabe wird das Schema von **HVAC.csv** angezeigt. Die Tabelle **hvactable** weist dasselbe Schema auf. In der Ausgabe werden die Spalten in der Tabelle angezeigt.

    ![Schema der Tabelle](./media/apache-spark-connect-to-sql-database/schema-of-table.png "Schema der Tabelle")

1. Verwenden Sie schließlich den folgenden Codeausschnitt, um Daten aus der Datei „HVAC.csv“ zu lesen und in die Tabelle **hvactable** in Azure SQL-Datenbank zu streamen. Fügen Sie den Codeausschnitt in eine Codezelle ein, ersetzen Sie die Platzhalterwerte durch die Werte für Ihre Azure SQL-Datenbank, und drücken Sie dann **UMSCHALT+EINGABE**, um die Codezelle auszuführen.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

1. Überprüfen Sie, ob die Daten in die Tabelle **hvactable** gestreamt werden, indem Sie die folgende Abfrage in SQL Server Management Studio (SSMS) ausführen. Bei jedem Ausführen der Abfrage wird die zunehmende Anzahl der Zeilen in der Tabelle angezeigt.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden des HDInsight Spark-Clusters zum Analysieren von Daten in Data Lake Storage](apache-spark-use-with-data-lake-store.md)
* [Apache Spark Structured Streaming unter HDInsight zum Verarbeiten von Ereignissen von Event Hubs](apache-spark-eventhub-structured-streaming.md)
* [Tutorial: Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
