---
title: 'Tutorial: Zugreifen auf Azure-Daten vom Typ „Data Lake Storage Gen2“ mit Azure Databricks unter Verwendung von Spark | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie Spark-Abfragen in einem Azure Databricks-Cluster ausführen, um auf Daten in einem Azure-Speicherkonto vom Typ „Data Lake Storage Gen2“ zuzugreifen.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: b332c11e76ad335772cc607edcf569f896acb873
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951398"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Tutorial: Zugreifen auf Daten vom Typ „Data Lake Storage Gen2“ mit Azure Databricks unter Verwendung von Spark

In diesem Tutorial erfahren Sie, wie Sie Ihren Azure Databricks-Cluster mit Daten in einem Azure-Speicherkonto verbinden, für das Azure Data Lake Storage Gen2 aktiviert ist. Diese Verbindung ermöglicht die native Ausführung von Datenabfragen und -analysen über Ihren Cluster.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Databricks-Clusters
> * Erfassen von unstrukturierten Daten in einem Speicherkonto
> * Ausführen von Analysen für Ihre Daten in Blob Storage

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein Azure Data Lake Storage Gen2-Konto.

  Siehe [Erstellen eines Azure-Kontos vom Typ „Data Lake Storage Gen2“](data-lake-storage-quickstart-create-account.md).

* Vergewissern Sie sich, dass Ihrem Benutzerkonto die Rolle [Mitwirkender an Storage-Blobdaten](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) zugewiesen ist.

* Installieren Sie AzCopy v10. Weitere Informationen finden Sie unter [Übertragen von Daten mit AzCopy v10 (Vorschau)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Erstellen eines Dienstprinzipals Informationen finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Bei den Schritten in diesem Artikel müssen einige bestimmte Aktionen ausgeführt werden.

  :heavy_check_mark: Achten Sie beim Ausführen der Schritte im Abschnitt [Zuweisen der Anwendung zu einer Rolle](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) des Artikels darauf, dem Dienstprinzipal die Rolle **Mitwirkender an Storage-Blobdaten** zuzuweisen.

  > [!IMPORTANT]
  > Achten Sie darauf, die Rolle im Kontext des Data Lake Storage Gen2-Kontos zuzuweisen. Sie können eine Rolle der übergeordneten Ressourcengruppe oder dem übergeordneten Abonnement zuweisen. In diesem Fall tritt jedoch ein Berechtigungsfehler auf, bis die Rollenzuweisungen an das Speicherkonto weitergegeben wurden.

  :heavy_check_mark: Fügen Sie beim Ausführen der Schritte im Abschnitt [Abrufen von Werten für die Anmeldung](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) des Artikels die Werte für Mandanten-ID, App-ID und Kennwort in eine Textdatei ein. Sie benötigen sie in Kürze.

### <a name="download-the-flight-data"></a>Herunterladen der Flugdaten

In diesem Tutorial werden Flugdaten des Bureau of Transportation Statistics verwendet, um die Durchführung eines ETL-Vorgangs zu veranschaulichen. Sie müssen diese Daten zum Durchführen des Tutorials herunterladen.

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA) auf.

2. Aktivieren Sie das Kontrollkästchen **Prezipped file** (ZIP-Datei), um alle Datenfelder auszuwählen.

3. Wählen Sie die Downloadschaltfläche aus, und speichern Sie die Ergebnisse auf Ihrem Computer **.** 

4. Entzippen Sie den Inhalt der ZIP-Datei, und notieren Sie sich den Namen und den Pfad der Datei. Diese Informationen werden in einem späteren Schritt benötigt.

## <a name="create-an-azure-databricks-service"></a>Erstellen eines Azure Databricks-Diensts

In diesem Abschnitt erstellen Sie einen Azure Databricks-Dienst über das Azure-Portal.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Analysen** > **Azure Databricks**.

    ![Databricks im Azure-Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks im Azure-Portal")

2. Geben Sie unter **Azure Databricks-Dienst** die folgenden Werte an, um einen Databricks-Dienst zu erstellen:

    |Eigenschaft  |BESCHREIBUNG  |
    |---------|---------|
    |**Arbeitsbereichsname**     | Geben Sie einen Namen für Ihren Databricks-Arbeitsbereich an.  |
    |**Abonnement**     | Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.        |
    |**Ressourcengruppe**     | Geben Sie an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe verwenden möchten. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Weitere Informationen finden Sie in der [Übersicht über den Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). |
    |**Location**     | Wählen Sie **USA, Westen 2** aus. Informationen zu weiteren verfügbaren Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).       |
    |**Tarif**     |  Wählen Sie **Standard** aus.     |

    ![Erstellen eines Azure Databricks-Arbeitsbereichs](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Erstellen eines Azure Databricks-Diensts")

3. Die Kontoerstellung dauert einige Minuten. Den Status des Vorgangs können Sie anhand der Statusanzeige im oberen Bereich verfolgen.

4. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**, und klicken Sie anschließend auf **Erstellen**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Erstellen eines Spark-Clusters in Azure Databricks

1. Navigieren Sie im Azure-Portal zu dem erstellten Databricks-Dienst, und wählen Sie **Launch Workspace** (Arbeitsbereich starten) aus.

2. Sie werden zum Azure Databricks-Portal weitergeleitet. Klicken Sie im Portal auf **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks in Azure")

3. Geben Sie auf der Seite **Neuer Cluster** die erforderlichen Werte an, um einen Cluster zu erstellen.

    ![Erstellen eines Databricks-Spark-Clusters in Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Erstellen eines Databricks-Spark-Clusters in Azure")

4. Geben Sie Werte für die folgenden Felder an, und übernehmen Sie bei den anderen Feldern die Standardwerte:

    * Geben Sie einen Namen für den Cluster ein.

    * Erstellen Sie für diesen Artikel einen Cluster mit der Runtime **5.1**.

    * Aktivieren Sie das Kontrollkästchen **Terminate after \_\_ minutes of inactivity** (Nach \_\_ Minuten Inaktivität beenden). Falls der Cluster nicht verwendet wird, geben Sie an, nach wie vielen Minuten er beendet werden soll.

    * Klicken Sie auf **Cluster erstellen**. Wenn der Cluster ausgeführt wird, können Sie Notebooks an den Cluster anfügen und Spark-Aufträge ausführen.

## <a name="ingest-data"></a>Erfassen von Daten

### <a name="copy-source-data-into-the-storage-account"></a>Kopieren von Quelldaten in das Speicherkonto

Kopieren Sie mithilfe von AzCopy Daten aus Ihrer *CSV*-Datei in Ihr Data Lake Storage Gen2-Konto.

1. Öffnen Sie ein Eingabeaufforderungsfenster, und geben Sie den folgenden Befehl ein, um sich bei Ihrem Speicherkonto anzumelden:

   ```bash
   azcopy login
   ```

   Folgen Sie den Anweisungen im Eingabeaufforderungsfenster, um Ihr Benutzerkonto zu authentifizieren.

2. Geben Sie den folgenden Befehl ein, um Daten aus der *CSV*-Datei zu kopieren:

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time.csv
   ```

   * Ersetzen Sie den Platzhalterwert `<csv-folder-path>` durch den Pfad zu der *CSV*-Datei.

   * Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

   * Ersetzen Sie den Platzhalter `<file-system-name>` durch einen beliebigen Namen für Ihr Dateisystem.

## <a name="create-a-file-system-and-mount-it"></a>Erstellen und Einbinden eines Dateisystems

In diesem Abschnitt erstellen Sie ein Dateisystem und einen Ordner in Ihrem Speicherkonto.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem erstellten Azure Databricks-Dienst, und wählen Sie **Launch Workspace** (Arbeitsbereich starten) aus.

2. Wählen Sie im linken Bereich die Option **Arbeitsbereich** aus. Wählen Sie in der Dropdownliste **Arbeitsbereich** die Option **Erstellen** > **Notebook** aus.

    ![Erstellen eines Notebooks in Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Erstellen eines Notebooks in Databricks")

3. Geben Sie im Dialogfeld **Notizbuch erstellen** einen Namen für das Notebook ein. Wählen Sie **Python** als Sprache und anschließend den zuvor erstellten Spark-Cluster aus.

4. Klicken Sie auf **Erstellen**.

5. Kopieren Sie den folgenden Codeblock, und fügen Sie ihn in die erste Zelle ein, führen Sie den Code jedoch noch nicht aus.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. Ersetzen Sie in diesem Codeblock die Platzhalterwerte `appId`, `password`, `tenant` und `storage-account-name` durch die Werte, die Sie bei der Vorbereitung dieses Tutorials gesammelt haben. Ersetzen Sie den Platzhalterwert `file-system-name` durch den Namen, den Sie dem Azure Data Lake Storage-Dateisystem im vorherigen Schritt gegeben haben.

Verwenden Sie die nachstehenden Werte zum Ersetzen der genannten Platzhalter.

   * `appId` und `password` stammen aus der App, die Sie im Rahmen der Dienstprinzipalerstellung bei Active Directory registriert haben.

   * `tenant-id` stammt aus Ihrem Abonnement.

   * `storage-account-name` ist der Name Ihres Azure Data Lake Storage Gen2-Speicherkontos.

   * Ersetzen Sie den Platzhalter `file-system-name` durch einen beliebigen Namen für Ihr Dateisystem.

   > [!NOTE]
   > In einer Produktionsumgebung empfiehlt es sich, Ihr Kennwort in Azure Databricks zu speichern. Fügen Sie dem Codeblock dann einen Suchschlüssel anstelle des Kennworts hinzu. Sehen Sie sich nach Abschluss dieses Schnellstarts die Beispiele für diesen Ansatz im Artikel [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) auf der Azure Databricks-Website an.

19. Drücken Sie **UMSCHALT+EINGABE**, um den Code in diesem Block auszuführen.

   Lassen Sie dieses Notebook geöffnet, da Sie ihm später Befehle hinzufügen.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Verwenden des Databricks-Notebooks zum Konvertieren von CSV in Parquet

Fügen Sie dem zuvor erstellten Notebook eine neue Zelle hinzu, und fügen Sie in diese Zelle den folgenden Code ein. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Erkunden von Daten

Fügen Sie in einer neuen Zelle den folgenden Code ein, um eine Liste mit CSV-Dateien abzurufen, die per AzCopy hochgeladen wurden.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Führen Sie dieses Skript aus, um eine neue Datei zu erstellen und die Dateien im Ordner *parquet/flights* aufzulisten:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Anhand dieser Codebeispiele haben Sie die hierarchische Struktur von HDFS unter Verwendung von Daten untersucht, die in einem Speicherkonto gespeichert sind, für das Azure Data Lake Storage Gen2 aktiviert ist.

## <a name="query-the-data"></a>Abfragen von Daten

Als Nächstes können Sie damit beginnen, die Daten abzufragen, die Sie in Ihr Speicherkonto hochgeladen haben. Geben Sie die folgenden Codeblöcke unter **Cmd 1** ein, und drücken Sie **CMD+EINGABE**, um das Python-Skript auszuführen.

Führen Sie das folgende Skript aus, um Dataframes für Ihre Datenquellen zu erstellen:

* Ersetzen Sie den Platzhalterwert `<csv-folder-path>` durch den Pfad zu der *CSV*-Datei.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

Geben Sie das folgende Skript ein, um einige grundlegende Analyseabfragen für die Daten auszuführen:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe für das Speicherkonto und anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Extrahieren, Transformieren und Laden von Daten mithilfe von Apache Hive in Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
