---
title: 'Tutorial: Verwenden von R in einem Spark-Computekontext in Azure HDInsight'
description: 'Tutorial: Erste Schritte mit R und Spark in einem Azure HDInsight Machine Learning Services-Cluster'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 6e727bc7ad8b7f0b8a04c48f3abd1f1ac0806c66
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545904"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Tutorial: Verwenden von R in einem Spark-Computekontext in Azure HDInsight

Dieses Tutorial enthält eine schrittweise Einführung in die Verwendung der R-Funktionen in Apache Spark, die auf einem Machine Learning Services-Cluster in Azure HDInsight ausgeführt werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Herunterladen der Beispieldaten in den lokalen Speicher
> * Kopieren der Daten in den Standardspeicher
> * Einrichten eines Datasets
> * Erstellen von Datenquellen
> * Erstellen eines Computekontexts für Spark
> * Anpassen eines linearen Modells
> * Verwenden von XDF-Verbunddateien
> * Konvertieren von XDF in CSV

## <a name="prerequisites"></a>Voraussetzungen

* Azure HDInsight Machine Learning Services-Cluster Navigieren Sie zu [Erstellen von Linux-basierten Clustern in HDInsight mit dem Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md), und wählen Sie für **Clustertyp** die Option **ML Services** aus.

## <a name="connect-to-rstudio-server"></a>Herstellen einer Verbindung mit RStudio Server

RStudio Server wird auf dem Edgeknoten des Clusters ausgeführt. Navigieren Sie zur folgenden Website. (Dabei steht *CLUSTERNAME* in der URL für den Namen des von Ihnen erstellten HDInsight Machine Learning Services-Clusters.)

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Bei der ersten Anmeldung authentifizieren Sie sich zweimal. Geben Sie bei der ersten Authentifizierungseingabeaufforderung den Administratorbenutzernamen und das Kennwort für den Cluster an. (Der Standardwert lautet *admin* .) Geben Sie bei der zweiten Authentifizierungseingabeaufforderung den Benutzernamen und das Kennwort für SSH an. (Der Standardwert lautet *sshuser* .) Für nachfolgende Anmeldungen sind nur noch die SSH-Anmeldeinformationen erforderlich.

## <a name="download-the-sample-data-to-local-storage"></a>Herunterladen der Beispieldaten in den lokalen Speicher

Das *Dataset „Airline 2012 On-Time“* besteht aus zwölf CSV-Dateien mit Informationen über Ankunft und Abflug für alle kommerziellen Flüge innerhalb der USA für das Jahr 2012. Dieses Dataset ist groß und enthält mehr als sechs Millionen Beobachtungen.

1. Initialisieren Sie einige Umgebungsvariablen. Geben Sie in der RStudio Server-Konsole den folgenden Code ein:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Wählen Sie im rechten Bereich die Registerkarte **Umgebung** aus. Die Variablen werden unter **Werte** angezeigt.

    ![HDInsight R Studio-Webkonsole](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Erstellen Sie ein lokales Verzeichnis, und laden Sie die Beispieldaten herunter. Geben Sie in RStudio den folgenden Code ein:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Der Download sollte in etwa neuneinhalb Minuten abgeschlossen sein.

## <a name="copy-the-data-to-default-storage"></a>Kopieren der Daten in den Standardspeicher

Der HDFS-Speicherort (Hadoop Distributed File System) wird mit der Variablen `airDataDir` angegeben. Geben Sie in RStudio den folgenden Code ein:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Der Schritt sollte in etwa zehn Sekunden abgeschlossen sein.

## <a name="set-up-a-dataset"></a>Einrichten eines Datasets

1. Erstellen Sie ein Dateisystemobjekt, das die Standardwerte verwendet. Geben Sie in RStudio den folgenden Code ein:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Die ursprünglichen CSV-Dateien haben ziemlich sperrige Variablennamen, daher stellen Sie zur einfacheren Verwaltung eine Liste vom Typ *colInfo* bereit. Geben Sie in RStudio den folgenden Code ein:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Erstellen von Datenquellen

In einem Spark-Computekontext können Sie Datenquellen mit den folgenden Funktionen erstellen:

|Funktion | BESCHREIBUNG |
|---------|-------------|
|`RxTextData` | Eine durch Trennzeichen getrennte Textdatenquelle. |
|`RxXdfData` | Daten im XDF-Datendateiformat. In RevoScaleR wird das XDF-Dateiformat so geändert, dass Hadoop Daten in einem Verbundsatz von Dateien anstatt in einer einzigen Datei speichert. |
|`RxHiveData` | Generiert ein Hive-Datenquellenobjekt.|
|`RxParquetData` | Generiert ein Parquet-Datenquellenobjekt.|
|`RxOrcData` | Generiert ein Orc-Datenquellenobjekt.|

Erstellen Sie mit den Dateien, die Sie in HDFS kopiert haben, ein [RxTextData](/machine-learning-server/r-reference/revoscaler/rxtextdata)-Objekt. Geben Sie in RStudio den folgenden Code ein:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Erstellen eines Computekontexts für Spark

Zum Laden von Daten und Ausführen von Analysen auf Workerknoten legen Sie den Computekontext in Ihrem Skript auf [RxSpark](/machine-learning-server/r-reference/revoscaler/rxspark) fest. In diesem Kontext verteilen R-Funktionen die Arbeitsauslastung automatisch auf alle Workerknoten, ohne dass eine integrierte Anforderung für die Verwaltung Einzelvorgängen oder der Warteschlange besteht. Der Spark-Computekontext wird durch `RxSpark` oder `rxSparkConnect()` eingerichtet, um den Spark-Computekontext zu erstellen. Er verwendet `rxSparkDisconnect()`, um zu einem lokalen Computekontext zurückzukehren. Geben Sie in RStudio den folgenden Code ein:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Anpassen eines linearen Modells

1. Verwenden Sie die [RxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod)-Funktion, um mithilfe Ihrer `airDS`-Datenquelle ein lineares Modell anzupassen. Geben Sie in RStudio den folgenden Code ein:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Dieser Schritt sollte nach zwei bis drei Minuten abgeschlossen sein.

1. Zeigen Sie die Ergebnisse an. Geben Sie in RStudio den folgenden Code ein:

    ```R
    summary(delayArr)
    ```

    Die Ergebnisse sollten wie folgt aussehen:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 **_ | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 _*_ | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 _*_ | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 _*_ | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 _*_ | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 _*_ | 858366
    ---
    Signif. codes:  0 ‘_*_’ 0.001 ‘_*’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Die Ergebnisse zeigen, dass Sie alle Daten – sechs Millionen Beobachtungen – mit allen CSV-Dateien im angegebenen Verzeichnis verarbeitet haben. Da Sie `cube = TRUE` angegeben haben, besitzen Sie einen geschätzten Koeffizienten für jeden Tag der Woche (und nicht für das konstante Glied).

## <a name="use-composite-xdf-files"></a>Verwenden von XDF-Verbunddateien

Wie Sie gesehen haben, können Sie CSV-Dateien direkt mit R in Hadoop analysieren. Die Analyse ist jedoch schneller, wenn Sie die Daten in einem effizienteren Format speichern. Das R-Format XDF ist effizient, wird aber für HDFS etwas verändert, sodass einzelne Dateien innerhalb eines einzigen HDFS-Blocks bleiben. (Die HDFS-Blockgröße variiert von Installation zu Installation, beträgt aber in der Regel 64 MB oder 128 MB.) 

Wenn Sie zum Erstellen eines Verbundsatzes von XDF-Dateien [rxImport](/machine-learning-server/r-reference/revoscaler/rximport) in Hadoop verwenden, geben Sie eine `RxTextData`-Datenquelle wie `AirDS` als inData-Argument und eine `RxXdfData`-Datenquelle, bei der „fileSystem“ auf ein HDFS-Dateisystem festgelegt ist, als outFile-Argument an. Das `RxXdfData`-Objekt kann dann in nachfolgenden R-Analysen als Datenargument verwendet werden.

1. Definieren Sie ein `RxXdfData`-Objekt. Geben Sie in RStudio den folgenden Code ein:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Legen Sie eine Blockgröße von 250.000 Zeilen fest, und geben Sie an, dass wir alle Daten gelesen haben. Geben Sie in RStudio den folgenden Code ein:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importieren Sie die Daten mithilfe von `rxImport`. Geben Sie in RStudio den folgenden Code ein:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Dieser Schritt sollte nach einigen Minuten abgeschlossen sein.

1. Schätzen Sie das gleiche lineare Modell unter Verwendung der neuen, schnelleren Datenquelle neu. Geben Sie in RStudio den folgenden Code ein:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Der Schritt sollte in weniger als einer Minute abgeschlossen sein.

1. Zeigen Sie die Ergebnisse an. Die Ergebnisse sollten die gleichen sein wie bei den CSV-Dateien. Geben Sie in RStudio den folgenden Code ein:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konvertieren von XDF in CSV

### <a name="in-a-spark-context"></a>In einem Spark-Kontext

Wenn Sie Ihre CSV-Dateien in das Dateiformat XDF konvertiert haben, um die Effizienz bei Analysen zu steigern, Ihre Daten nun aber wieder in CSV konvertieren möchten, können Sie dazu [rxDataStep](/machine-learning-server/r-reference/revoscaler/rxdatastep) verwenden.

Generieren Sie zum Erstellen eines Ordners mit CSV-Dateien zunächst ein `RxTextData`-Objekt. Verwenden Sie dazu einen Verzeichnisnamen als Dateiargument. Dieses Objekt stellt den Ordner dar, in dem die CSV-Dateien erstellt werden sollen. Dieses Verzeichnis wird bei der Ausführung von `rxDataStep` erstellt. Zeigen Sie dann im `outFile`-Argument von `rxDataStep` auf dieses `RxTextData`-Objekt. Jede erstellte CSV-Datei erhält den Verzeichnisnamen gefolgt von einer Nummer.

Angenommen, Sie möchten einen Ordner mit CSV-Dateien aus unserer XDF-Verbunddatei `airDataXdf` in HDFS schreiben, nachdem Sie die logistische Regression und Vorhersage durchgeführt haben, sodass die neuen CSV-Dateien die vorhergesagten Werte und Restwerte enthalten. Geben Sie in RStudio den folgenden Code ein:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Dieser Schritt sollte nach etwa zweieinhalb Minuten abgeschlossen sein.

`rxDataStep` hat für jede XDFD-Datei in der XDF-Verbundeingabedatei eine CSV-Datei ausgegeben. Dies ist das Standardverhalten für das Schreiben von CSV-Dateien aus XDF-Verbunddateien in HDFS, wenn der Computekontext auf `RxSpark` festgelegt ist.

### <a name="in-a-local-context"></a>In einem lokalen Kontext

Alternativ können Sie Ihren Computekontext nach Abschluss der Analysen wieder auf `local` zurücksetzen und zwei Argumente innerhalb von `RxTextData` nutzen, die Ihnen etwas mehr Kontrolle beim Schreiben von CSV-Dateien in HDFS geben: `createFileSet` und `rowsPerOutFile`. Wenn `createFileSet` auf `TRUE` festgelegt ist, wird ein Ordner mit CSV-Dateien in das von Ihnen angegebene Verzeichnis geschrieben. Wenn `createFileSet` auf `FALSE` festgelegt ist, wird eine einzelne CSV-Datei geschrieben. Sie können das zweite Argument `rowsPerOutFile` auf einen Integer festlegen, um anzugeben, wie viele Zeilen in jede CSV-Datei geschrieben werden sollen, wenn `TRUE` für `createFileSet` angegeben ist.

Geben Sie in RStudio den folgenden Code ein:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Dieser Schritt sollte nach etwa zehn Minuten abgeschlossen sein.

Bei Verwendung eines `RxSpark`-Computekontexts ist `createFileSet` standardmäßig auf `TRUE` festgelegt, und `rowsPerOutFile` hat keine Auswirkung. Wenn Sie eine einzelne CSV-Datei erstellen oder die Anzahl der Zeilen pro Datei anpassen möchten, führen Sie `rxDataStep` daher in einem `local`-Computekontext aus (Daten können sich dennoch in HDFS befinden).

## <a name="final-steps"></a>Letzte Schritte

1. Bereinigen Sie die Daten. Geben Sie in RStudio den folgenden Code ein:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Beenden Sie die Spark-Remoteanwendung. Geben Sie in RStudio den folgenden Code ein:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Beenden Sie die R-Sitzung. Geben Sie in RStudio den folgenden Code ein:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den Cluster, wenn Sie das Tutorial beendet haben. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mithilfe von Browser, PowerShell oder Azure-Befehlszeilenschnittstelle](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie R-Funktionen in Apache Spark bei der Ausführung in einem HDInsight Machine Learning Services-Cluster verwendet werden. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Computekontextoptionen für ML Services in HDInsight](r-server-compute-contexts.md)
* [R-Funktionen für Spark in Hadoop](/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)