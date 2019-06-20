---
title: Verwalten eines ML Services-Clusters in HDInsight – Azure
description: Erfahren Sie, wie Sie einen ML Services-Cluster in Azure HDInsight verwalten.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 607f85c10183366e88d597d84090f49fc30aff48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687983"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Verwalten eines ML Services-Clusters in Azure HDInsight

In diesem Artikel erfahren Sie, wie Sie einen vorhandenen Machine Learning Services-Cluster in Azure HDInsight verwalten, um Aufgaben wie das Hinzufügen mehrerer gleichzeitiger Benutzer, das Herstellen einer Remoteverbindung mit einem Machine Learning Services-Cluster, das Ändern des Computekontexts usw. auszuführen.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein ML Services-Cluster in HDInsight:** Anweisungen finden Sie unter [Erste Schritte mit ML Services in HDInsight](r-server-get-started.md).

* **Ein SSH-Client (Secure Shell):** Ein SSH-Client wird verwendet, um Remoteverbindungen mit dem HDInsight-Cluster herzustellen und Befehle direkt im Cluster auszuführen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Aktivieren mehrerer gleichzeitiger Benutzer

Sie können mehrere gleichzeitige Benutzer für ML Services-Cluster in HDInsight aktivieren, indem Sie mehr Benutzer für den Edgeknoten hinzufügen, auf dem die RStudio Community-Version ausgeführt wird. Wenn Sie einen HDInsight-Cluster erstellen, müssen Sie zwei Benutzer (einen HTTP-Benutzer und einen SSH-Benutzer) angeben:

![Gleichzeitiger Benutzer 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Benutzername für Clusteranmeldung**: ein HTTP-Benutzer für die Authentifizierung über das HDInsight-Gateway, das zum Schutz der von Ihnen erstellten HDInsight-Cluster verwendet wird. Mit diesem HTTP-Benutzer wird auf die Apache Ambari- und die Apache Hadoop YARN-Benutzeroberfläche sowie auf andere UI-Komponenten zugegriffen.
- **SSH-Benutzername (Secure Shell)** : ein SSH-Benutzer für den Zugriff auf den Cluster über Secure Shell. Dieser Benutzer ist ein Benutzer im Linux-System für alle Hauptknoten, Workerknoten und Edgeknoten. Somit können Sie Secure Shell zum Zugreifen auf einen beliebigen Knoten in einem Remotecluster verwenden.

Die RStudio Server Community-Version, die im ML Services-Cluster in HDInsight verwendet wird, akzeptiert für die Anmeldung nur Linux-Benutzernamen und -Kennwörter. Die Übergabe von Token wird nicht unterstützt. Wenn Sie zum ersten Mal versuchen, auf einem ML Services-Cluster auf RStudio zuzugreifen, müssen Sie sich zweimal anmelden.

- Melden Sie sich zunächst mit den HTTP-Benutzeranmeldeinformationen über das HDInsight-Gateway an. 

- Verwenden Sie dann die SSH-Benutzeranmeldeinformationen für die Anmeldung bei RStudio.
  
Derzeit kann bei der Bereitstellung eines HDInsight-Clusters nur ein SSH-Benutzerkonto erstellt werden. Um mehreren Benutzern den Zugriff auf ML Services-Cluster in HDInsight zu ermöglichen, müssen Sie zusätzliche Benutzer im Linux-System erstellen.

Da RStudio auf dem Edgeknoten des Clusters ausgeführt wird, müssen mehrere Schritte ausgeführt werden:

1. Anmelden beim Edgeknoten mit dem bestehenden SSH-Benutzer
2. Hinzufügen weiterer Linux-Benutzer im Edgeknoten
3. Verwenden der RStudio Community-Version mit dem erstellten Benutzer

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>Schritt 1: Anmelden beim Edgeknoten mit dem erstellten SSH-Benutzer

Befolgen Sie die Anweisungen unter [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md), um auf den Edgeknoten zuzugreifen. Die Edgeknotenadresse für ML Services-Cluster in HDInsight lautet `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Schritt 2: Hinzufügen weiterer Linux-Benutzer im Edgeknoten

Fügen Sie zum Hinzufügen eines Benutzers zum Edgeknoten die folgenden Befehle aus:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Im folgenden Screenshot sind die Ausgaben dargestellt.

![Gleichzeitiger Benutzer 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Wenn Sie aufgefordert werden, das aktuelle Kerberos-Kennwort einzugeben, drücken Sie einfach die **EINGABETASTE**, um die Aufforderung zu ignorieren. Die `-m`-Option im Befehl `useradd` gibt an, dass das System für den Benutzer einen Basisordner erstellt, der für die RStudio Community-Version erforderlich ist.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Schritt 3: Verwenden der RStudio Community-Version mit dem erstellten Benutzer

Greifen Sie aus https://CLUSTERNAME.azurehdinsight.net/rstudio/ auf RStudio zu. Wenn Sie sich zum ersten Mal nach dem Erstellen des Clusters anmelden, geben Sie die Clusteradministrator-Anmeldeinformationen ein, gefolgt von den erstellten SSH-Benutzeranmeldeinformationen. Wenn dies nicht Ihre erste Anmeldung ist, geben Sie nur die Anmeldeinformationen für den SSH-Benutzer ein, den Sie erstellt haben.

Sie können sich auch gleichzeitig in einem anderen Browserfenster mit den ursprünglichen Anmeldeinformationen (standardmäßig *sshuser*) anmelden.

Beachten Sie außerdem, dass die neu hinzugefügten Benutzer nicht über Stammberechtigungen im Linux-System verfügen, aber den gleichen Zugriff auf alle Dateien im HDFS- und WASB-Remotespeicher haben.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Herstellen einer Remoteverbindung mit Microsoft ML Services

Sie können den Zugriff auf den HDInsight Spark-Computekontext über eine auf Ihrem Desktop ausgeführte Remoteinstanz von ML Client einrichten. Hierfür müssen Sie die Optionen (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches und sshProfileScript) beim Definieren des RxSpark-Computekontexts auf Ihrem Laptop angeben: Beispiel:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Weitere Informationen finden Sie im Abschnitt „Verwenden von Microsoft Machine Learning Server als Apache Hadoop-Client“ unter [Verwenden von RevoScaleR in einem Apache Spark-Computekontext](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios).

## <a name="use-a-compute-context"></a>Verwenden eines Rechenkontexts

Mit einem Rechenkontext können Sie steuern, ob die Berechnung lokal auf dem Edgeknoten ausgeführt oder auf die Knoten im HDInsight-Cluster verteilt wird.

1. Verwenden Sie über RStudio Server oder die R-Konsole (in einer SSH-Sitzung) den folgenden Code, um Beispieldaten in den Standardspeicher für HDInsight zu laden:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Erstellen Sie als Nächstes einige Dateninformationen, und definieren Sie zwei Datenquellen.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Führen Sie mit dem lokalen Rechenkontext eine logistische Regression für die Daten aus.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Es sollte eine Ausgabe, die mit ähnlichen Zeilen wie im folgenden Ausschnitt endet, angezeigt werden:

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Führen Sie die gleiche logistische Regression mit dem Spark-Kontext aus. Im Spark-Kontext wird die Verarbeitung auf alle Workerknoten im HDInsight-Cluster verteilt.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )

        # Display a summary
        summary(modelSpark)


## <a name="distribute-r-code-to-multiple-nodes"></a>Verteilen von R-Code auf mehrere Knoten

Mit ML Services in HDInsight können Sie mithilfe von `rxExec` vorhandenen R-Code einfach nutzen und auf mehreren Knoten im Cluster ausführen. Diese Funktion ist nützlich für Parameterbereinigungen oder für Simulationen. Im folgenden Code wird die Verwendung von `rxExec` veranschaulicht:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Wenn Sie noch den Spark-Kontext verwenden, gibt dieser Befehl den nodename-Wert für die Workerknoten zurück, auf denen der Code `(Sys.info()["nodename"])` ausgeführt wird. In einem Cluster mit vier Knoten erwarten Sie beispielsweise eine Ausgabe, die etwa dem folgenden Ausschnitt gleicht:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Zugreifen auf Daten in Apache Hive und Parquet

HDInsight ML Services bietet direkten Zugriff auf Daten in Hive und Parquet, um diese für ScaleR-Funktionen im Spark-Computekontext zu verwenden. Diese Funktionalität steht über die neuen ScaleR-Datenquellenfunktionen namens „RxHiveData“ und „RxParquetData“ zur Verfügung, die Daten unter Verwendung von Spark SQL direkt in einen Spark-Dataframe laden, um sie mit ScaleR zu analysieren.

Der nachfolgende Code zeigt die Verwendung der neuen Funktionen:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Zusätzliche Informationen zur Verwendung dieser neuen Funktionen finden Sie in der Onlinehilfe zu ML Services über die Befehle `?RxHivedata` und `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installieren zusätzlicher R-Pakete auf dem Cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>So installieren Sie R-Pakete auf dem Edgeknoten

Falls Sie zusätzliche R-Pakete auf dem Edgeknoten installieren möchten, können Sie `install.packages()` direkt in der R-Konsole verwenden, wenn Sie über SSH eine Verbindung mit dem Edgeknoten hergestellt haben. 

### <a name="to-install-r-packages-on-the-worker-node"></a>So installieren Sie R-Pakete auf dem Workerknoten

Um R-Pakete auf den Workerknoten des Clusters zu installieren, müssen Sie eine Skriptaktion verwenden. Skriptaktionen sind Bash-Skripts, mit denen Konfigurationsänderungen am HDInsight-Cluster vorgenommen werden oder zusätzliche Software (etwa zusätzliche R-Pakete) installiert wird. 

> [!IMPORTANT]  
> Skriptaktionen können nur zum Installieren zusätzlicher R-Pakete verwendet werden, nachdem der Cluster erstellt wurde. Verwenden Sie dieses Verfahren nicht bei der Clustererstellung, da für das Skript ML Services vollständig konfiguriert sein muss.

1. Befolgen Sie die Schritte unter [Anpassen von Clustern mithilfe von Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md).

3. Geben Sie für **Skriptaktion übermitteln** die folgenden Informationen an:

   * Wählen Sie als **Skripttyp** die Option **Benutzerdefiniert** aus.

   * Geben Sie einen **Namen** für die Skriptaktion an.

     * Geben Sie für **Bash-Skript-URI** `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh` ein. Dies ist das Skript, das zusätzliche R-Pakete auf dem Workerknoten installiert

   * Aktivieren Sie nur das Kontrollkästchen für **Worker**.

   * **Parameter**: Die zu installierenden R-Pakete. Zum Beispiel, `bitops stringr arules`

   * Aktivieren Sie das Kontrollkästchen, um **diese Skriptaktion beizubehalten**.  

   > [!NOTE]
   > 1. Standardmäßig werden alle R-Pakete über eine Momentaufnahme des Microsoft MRAN-Repositorys erstellt, die der installierten Version von ML Server entspricht. Wenn Sie neuere Versionen der Pakete installieren möchten, besteht das Risiko von Inkompatibilitäten. Diese Art von Installation ist jedoch möglich, wenn Sie `useCRAN` als erstes Element der Paketliste angeben, z.B. `useCRAN bitops, stringr, arules`.  
   > 2. Für einige R-Pakete werden zusätzliche Linux-Systembibliotheken benötigt. Um Ihnen die Arbeit zu erleichtern, sind die abhängigen Komponenten für die 100 populärsten R-Pakete in HDInsight ML Services für Sie vorinstalliert. Wenn für die R-Pakete, die Sie installieren, weitere Bibliotheken hinzugefügt werden müssen, müssen Sie das hier verwendete Basisskript herunterladen und Schritte zum Installieren der Systembibliotheken hinzufügen. Dann müssen Sie das geänderte Skript in einen öffentlichen Blobcontainer im Azure-Speicher hochladen und das geänderte Skript zum Installieren der Pakete verwenden.
   >    Weitere Informationen zum Entwickeln von Skriptaktionen finden Sie unter [Entwickeln von Skriptaktionen](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Hinzufügen einer Skriptaktion](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Wählen Sie **Erstellen** aus, um das Skript auszuführen. Nach Abschluss der Skriptausführung sind die R-Pakete auf allen Workerknoten verfügbar.

## <a name="next-steps"></a>Nächste Schritte

* [Operationalize ML Services cluster on Azure HDInsight](r-server-operationalize.md) (Operationalisieren eines ML Services-Clusters in HDInsight)
* [Computekontextoptionen für ML Service-Cluster in HDInsight](r-server-compute-contexts.md)
* [Azure Storage solutions for ML Services on Azure HDInsight](r-server-storage.md) (Azure Storage-Lösungen für ML Services in Azure HDInsight)
