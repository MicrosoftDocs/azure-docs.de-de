---
title: Erste Schritte mit ML Services in HDInsight – Azure
description: Hier erfahren Sie, wie Sie einen Cluster mit Apache Spark in HDInsight erstellen, der über ML Services verfügt, und ein R-Skript für den Cluster übermitteln.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 465b53e1c5f56c5c05c860ebd69a825141f7e703
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706064"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Erste Schritte mit ML Services in Azure HDInsight

Azure HDInsight ermöglicht die Erstellung eines ML Services-Clusters. Mit dieser Option können R-Skripts [Apache Spark](https://spark.apache.org/) und [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) verwenden, um verteilte Berechnungen auszuführen. In diesem Artikel erfahren Sie, wie Sie in HDInsight einen ML Services-Cluster erstellen und ein R-Skript ausführen, das die Verwendung von Spark für verteilte R-Berechnungen veranschaulicht.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**: Für dieses Tutorial wird ein Azure-Abonnement benötigt. Weitere Informationen finden Sie unter [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Beziehen der kostenlosen Testversion von Microsoft Azure).
* **Ein SSH-Client (Secure Shell):** Ein SSH-Client wird verwendet, um Remoteverbindungen mit dem HDInsight-Cluster herzustellen und Befehle direkt im Cluster auszuführen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Erstellen des Clusters mit dem Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zu **+ Ressource erstellen** > **Analytics** > **HDInsight**.

3. Geben Sie unter **Grundlagen** Folgendes ein:

    * **Clustername:** Der Name des HDInsight-Clusters.
    * **Abonnement**: Wählen Sie das zu verwendende Abonnement aus.
    * **Benutzername für Clusteranmeldung** und **Kennwort für Clusteranmeldung**: Die für den Clusterzugriff über HTTPS verwendete Anmeldung. Sie verwenden diese Anmeldeinformationen für den Zugriff auf Dienste wie die Apache Ambari-Webbenutzeroberfläche oder die REST-API.
    * **SSH-Benutzername (Secure Shell):** Die für den Clusterzugriff über SSH verwendete Anmeldung. Das Kennwort ist standardmäßig mit dem Kennwort für die Clusteranmeldung identisch.
    * **Ressourcengruppe**: Die Ressourcengruppe, in der der Cluster erstellt wird.
    * **Standort**: Die Azure-Region, in der der Cluster erstellt werden soll.

        ![Grundlegende Details zu Clustern](./media/r-server-get-started/clustername.png)

4. Wählen Sie **Clustertyp** aus, und legen Sie dann im Abschnitt **Clusterkonfiguration** die folgenden Werte fest:

    * **Clustertyp:** ML Services

    * **Betriebssystem**: Linux

    * **Version**: ML Server 9.3 (HDI 3.6). Versionsanmerkungen für ML Server 9.3 finden Sie unter [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **RStudio Community Edition für ML Server:** Diese browserbasierte IDE wird standardmäßig auf dem Edgeknoten installiert. Deaktivieren Sie das Kontrollkästchen, falls Sie die Installation nicht wünschen. Wenn Sie eine Installation durchführen, ist die URL für den Zugriff auf die RStudio Server-Anmeldung auf einem Blatt der Portalanwendung für Ihren Cluster verfügbar, nachdem dieser erstellt wurde.

        ![Grundlegende Details zu Clustern](./media/r-server-get-started/clustertypeconfig.png)

4. Legen Sie nach dem Auswählen des Clustertyps mit der Schaltfläche __Auswählen__ den Clustertyp fest. Schließen Sie dann mit der Schaltfläche __Weiter__ die grundlegende Konfiguration ab.

5. Wählen Sie im Abschnitt **Speicher** ein Speicherkonto aus, oder erstellen Sie eines. Behalten Sie für die Schritte in diesem Dokument für die weiteren Felder in diesem Abschnitt die Standardwerte bei. Speichern Sie mit der Schaltfläche __Weiter__ die Speicherkonfiguration.

    ![Festlegen der Speicherkontoeinstellungen für HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Überprüfen Sie im Abschnitt **Zusammenfassung** die Konfiguration für den Cluster. Ändern Sie ggf. falsche Einstellungen mithilfe der Link zum __Bearbeiten__. Verwenden Sie abschließend die Schaltfläche__Erstellen__, um den Cluster zu erstellen.

    ![Festlegen der Speicherkontoeinstellungen für HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > Das Erstellen des Clusters kann bis zu 20 Minuten dauern.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Herstellen einer Verbindung mit RStudio Server

Sie können mit einer der beiden folgenden Methoden auf die RStudio-Anmeldung zugreifen, wenn Sie sich dafür entschieden haben, die RStudio Server Community Edition als Teil Ihres HDInsight-Clusters zu installieren:

* **Option 1**: Navigieren Sie zur folgenden URL (wobei **CLUSTERNAME** der Name des von Ihnen erstellten ML Services-Clusters ist):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Option 2:** Verwenden des Azure-Portals.
  Im Portal:
  1. Wählen Sie im Menü links **Alle Dienste** aus.
  2. Wählen Sie unter **ANALYTICS** die Option **HDInsight-Cluster** aus.
  3. Wählen Sie auf der Seite **HDInsight-Cluster** den Namen Ihres Clusters aus.
  4. Wählen Sie unter **ML Services-Dashboards** die Option **R Studio-Server** aus. 

     ![Festlegen der Speicherkontoeinstellungen für HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

     > [!IMPORTANT]  
     > Unabhängig von der verwendeten Zugriffsmethode müssen Sie sich bei der ersten Anmeldung zweimal authentifizieren.  Geben Sie für die erste Authentifizierungseingabeaufforderung die *Benutzer-ID* und das *Kennwort* für den Clusteradministrator an. Geben Sie für die zweite Authentifizierungseingabeaufforderung die *Benutzer-ID* und das *Kennwort* für SSH an. Für nachfolgende Anmeldungen sind dann nur noch die SSH-Anmeldeinformationen erforderlich.

Nachdem die Verbindung hergestellt wurde, sollte die Anzeige in etwa wie im folgenden Screenshot aussehen:

![Herstellen einer Verbindung mit RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Ausführen eines Beispielauftrags

Sie können einen Auftrag mithilfe von ScaleR-Funktionen senden. Hier sehen Sie ein Beispiel für die zum Ausführen eines Auftrags verwendeten Befehle:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Herstellen einer Verbindung mit dem Edgeknoten des Clusters

In diesem Abschnitt erfahren Sie, wie Sie mit SSH eine Verbindung mit dem Edgeknoten eines ML Services-HDInsight-Clusters herstellen. Sie können sich unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) mit der Verwendung von SSH vertraut machen.

Der SSH-Befehl zum Herstellen einer Verbindung mit dem Edgeknoten des ML Services-Clusters lautet:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Gehen Sie wie folgt vor, um den SSH-Befehl für Ihren Cluster zu ermitteln: Klicken Sie im Azure-Portal auf den Clusternamen und auf **SSH + Clusteranmeldung**, und wählen Sie unter **Hostname** dann den Edgeknoten aus. Dadurch werden die SSH-Endpunktinformationen für den Edgeknoten angezeigt.

![Bild des SSH-Endpunkts für den Edgeknoten](./media/r-server-get-started/sshendpoint.png)

Wenn Sie zum Schutz Ihres SSH-Benutzerkontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden, um den passenden privaten Schlüssel anzugeben. Beispiel:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Nachdem die Verbindung hergestellt wurde, wird in etwa folgende Eingabeaufforderung angezeigt:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Verwenden der R-Konsole

1. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die R-Konsole zu starten:  

        R

2. Neben anderen Informationen sollte eine Ausgabe mit der Version von ML Server angezeigt werden.
    
3. An der `>` -Eingabeaufforderung können Sie R-Code eingeben. ML Services in HDInsight enthält Pakete, mit denen Sie auf einfache Weise mit Hadoop interagieren und verteilte Berechnungen ausführen können. Verwenden Sie beispielsweise den folgenden Befehl, um das Stammverzeichnis des Standarddateisystems für den HDInsight-Cluster anzuzeigen:

        rxHadoopListFiles("/")

4. Sie können auch eine Adressierung mit der WASB-Syntax verwenden.

        rxHadoopListFiles("wasb:///")

5. Verwenden Sie den folgenden Befehl, um die R-Konsole zu beenden:

        quit()

## <a name="automated-cluster-creation"></a>Automatisierte Clustererstellung

Sie können die Erstellung des ML Services-Clusters für HDInsight über das SDK und über PowerShell automatisieren.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Informationen zur Erstellung eines ML Services-Clusters mit dem .NET SDK finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem .NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Informationen zur Erstellung eines ML Services-Clusters mit PowerShell finden Sie im Artikel [Erstellen von HDInsight-Clustern mit Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problembehandlung

Falls beim Erstellen von HDInsight-Clustern Probleme auftreten, sehen Sie sich die [Voraussetzungen für die Zugriffssteuerung](../hdinsight-hadoop-create-linux-clusters-portal.md) an.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen neuen ML Services-Cluster in Azure HDInsight erstellen, und sich mit den Grundlagen für die Verwendung der R-Konsole über eine SSH-Sitzung vertraut gemacht. In den folgenden Artikeln werden weitere Verwaltungs- und Verwendungsmöglichkeiten für ML Services in HDInsight erläutert:

* [Übermitteln von Aufträgen über R Tools für Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Manage ML Services cluster on HDInsight](r-server-hdinsight-manage.md) (Verwalten eines ML Services-Clusters in Azure HDInsight)
* [Operationalize ML Services cluster on Azure HDInsight](r-server-operationalize.md) (Operationalisieren eines ML Services-Clusters in HDInsight)
* [Compute context options for ML Services on HDInsight](r-server-compute-contexts.md) (Computekontextoptionen für ML Services in HDInsight)
* [Azure Storage solutions for ML Services on Azure HDInsight](r-server-storage.md) (Azure Storage-Lösungen für ML Services in Azure HDInsight)
