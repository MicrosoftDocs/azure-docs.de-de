---
title: Übermitteln von Aufträgen über R Tools für Visual Studio – Azure HDInsight
description: Übermitteln Sie R-Aufträge von Ihrem lokalen Visual Studio-Computer an einen HDInsight-Cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 08426c74b26c18b15466578d9921520da1e9c923
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993787"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Übermitteln von Aufträgen über R Tools für Visual Studio

[R Tools für Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) ist eine kostenlose Open Source-Erweiterung für die (kostenlose) Community-Edition sowie für die Professional- und Enterprise-Edition von [Visual Studio 2017](https://www.visualstudio.com/downloads/) und [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) (oder höher). RTVS ist für [Visual Studio 2019](/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?preserve-view=true&view=vs-2019) nicht verfügbar.

RTVS verbessert den R-Workflow mit Tools wie dem [R Interactive-Fenster](/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (Codevervollständigung), [Plotvisualisierung](/visualstudio/rtvs/visualizing-data) über R-Bibliotheken wie „ggplot2“ und „ggviz“ und [R-Codedebugging](/visualstudio/rtvs/debugging).

## <a name="set-up-your-environment"></a>Einrichten Ihrer Umgebung

1. Installieren Sie [R Tools für Visual Studio](/visualstudio/rtvs/installing-r-tools-for-visual-studio).

    ![Installieren von RTVS in Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Wählen Sie die Workload *Data Science und analytische Anwendungen* und anschließend die Optionen **R-Sprachunterstützung**, **Runtimeunterstützung für R-Entwicklungstools** und **Microsoft R Client** aus.

3. Sie benötigen öffentliche und private Schlüssel für die SSH-Authentifizierung.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installieren Sie [ML Server](/previous-versions/machine-learning-server/install/r-server-install-windows) auf Ihrem Computer. ML Server stellt die Funktionen [`RevoScaleR`](/machine-learning-server/r-reference/revoscaler/revoscaler) und `RxSpark` bereit.

5. Installieren Sie [PuTTY](https://www.putty.org/), um einen Computekontext für die Ausführung von `RevoScaleR`-Funktionen zwischen Ihrem lokalen Client und Ihrem HDInsight-Cluster bereitzustellen.

6. Sie haben die Möglichkeit, die Data Science-Einstellungen auf Ihre Visual Studio-Umgebung anzuwenden. Dadurch erhalten Sie ein neues Layout für Ihren Arbeitsbereich für die R Tools.
   1. Wenn Sie Ihre aktuellen Visual Studio-Einstellungen speichern möchten, verwenden Sie den Befehl **Tools > Einstellungen importieren/exportieren**, klicken Sie auf **Ausgewählte Umgebungseinstellungen exportieren**, und geben Sie einen Dateinamen an. Wenn Sie die Einstellungen wiederherstellen möchten, verwenden Sie den gleichen Befehl, und klicken Sie auf **Ausgewählte Umgebungseinstellungen importieren**.

   2. Navigieren Sie zum Menüelement **R Tools**, und klicken Sie auf **Data Science-Einstellungen...** .

       ![Data Science-Einstellungen in Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Mit der Vorgehensweise in Schritt 1 können Sie auch Ihr personalisiertes Data Scientist-Layout speichern und wiederherstellen, anstatt den Befehl **Data Science-Einstellungen** zu wiederholen.

## <a name="execute-local-r-methods"></a>Ausführen lokaler R-Methoden

1. Erstellen Sie Ihren HDInsight-ML Services-Cluster.
2. Installieren Sie die [RTVS-Erweiterung](/visualstudio/rtvs/installation).
3. Laden Sie die [Beispiel-ZIP-Datei](https://github.com/Microsoft/RTVS-docs/archive/master.zip) herunter.
4. Öffnen Sie `examples/Examples.sln`, um die Projektmappe in Visual Studio zu starten.
5. Öffnen Sie die Datei `1-Getting Started with R.R` im Projektmappenordner `A first look at R`.
6. Beginnen Sie am Anfang der Datei, und senden Sie die einzelnen Zeilen durch Drücken von STRG+EINGABETASTE nacheinander an das R Interactive-Fenster. Bei einigen Zeilen dauert der Vorgang unter Umständen etwas, da sie Pakete installieren.
    * Alternativ können Sie alle Zeilen in der R-Datei durch Drücken von STRG+A markieren und dann entweder alle ausführen (STRG+EINGABETASTE) oder auf der Symbolleiste auf das Symbol für die interaktive Ausführung klicken.

        ![Interaktive Visual Studio-Ausführung](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Nach dem Ausführen aller Zeilen des Skripts sollte die Ausgabe in etwa wie folgt aussehen:

    ![R-Tools für Visual Studio-Arbeitsbereiche](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Übermitteln von Aufträgen an einen HDInsight-ML Services-Cluster

Mithilfe einer Microsoft ML Server-/Microsoft R Client-Instanz auf einem Windows-Computer mit PuTTY können Sie einen Computekontext erstellen, der verteilte `RevoScaleR`-Funktionen zwischen Ihrem lokalen Client und Ihrem HDInsight-Cluster ausführt. Verwenden Sie `RxSpark`, um den Computekontext zu erstellen, und geben Sie dabei Ihren Benutzernamen, den Edgeknoten des Apache Hadoop-Clusters, SSH-Switches und so weiter an.

1. Die Edgeknotenadresse für ML Services in HDInsight ist `CLUSTERNAME-ed-ssh.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres ML Services-Clusters ist.

1. Fügen Sie in Visual Studio den folgenden Code in das R Interactive-Fenster ein, und passen Sie dabei die Werte der Einrichtungsvariablen an Ihre Umgebung an:

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![Apache Spark: Festlegen des Kontextes](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Führen Sie im R Interactive-Fenster die folgenden Befehle aus:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Es sollte eine Ausgabe angezeigt werden, die Folgendem ähnelt:

    ![Erfolgreiche rx-Befehlsausführung](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png)
1. Vergewissern Sie sich, dass `rxHadoopCopy` die Datei `people.json` erfolgreich aus dem Beispieldatenordner in den neu erstellten Ordner `/user/RevoShare/newUser` kopiert hat:

    1. Klicken Sie in Azure im Bereich Ihres HDInsight-ML Services-Clusters im linken Menü auf **Speicherkonten**.

        ![Azure HDInsight Storage-Konten](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Wählen Sie das Standardspeicherkonto für Ihren Cluster aus, und notieren Sie sich den Container-/Verzeichnisnamen.

    3. Klicken Sie im linken Menü des Speicherkontobereichs auf **Container**.

        ![Azure HDInsight Storage-Container](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Wählen Sie den Containernamen Ihres Clusters aus, navigieren Sie zum Ordner **user** (hierzu müssen Sie ggf. am Listenende auf *Weitere laden* klicken), und wählen Sie dann *RevoShare* > **newUser** aus. Die Datei `people.json` sollte im Ordner `newUser` angezeigt werden.

        ![Speicherort des kopierten HDInsight-Dateiordners](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Der aktuelle Apache Spark-Kontext muss nach der Verwendung wieder beendet werden. Es können nicht gleichzeitig mehrere Kontexte ausgeführt werden.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Computekontextoptionen für ML Services in HDInsight](r-server-compute-contexts.md)
* Unter [Kombinieren von ScaleR und SparkR in HDInsight](../hdinsight-hadoop-r-scaler-sparkr.md) finden Sie ein Beispiel für die Vorhersage von Flugverspätungen für Fluglinien.