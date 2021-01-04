---
title: Azure HDInsight für Visual Studio Code
description: Erfahren Sie, wie Sie die Spark- und Hive-Tools (Azure HDInsight) für Visual Studio Code verwenden können. Verwenden Sie die Tools zum Erstellen und Übermitteln von Abfragen und Skripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: 302f1a081ca44cf6436f2c318b03e227f6640489
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001965"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Verwenden von Spark- und Hive-Tools für Visual Studio Code

Erfahren Sie, wie Sie Apache Spark- und Hive-Tools für Visual Studio Code verwenden. Verwenden Sie die Tools zum Erstellen und Übermitteln von Batchaufträgen für Apache Hive, interaktiven Hive-Abfragen und PySpark-Skripts für Apache Spark. Zunächst wird beschrieben, wie Sie Spark- und Hive-Tools in Visual Studio Code installieren. Anschließend werden die Schritte zum Übermitteln von Aufträgen an Spark- und Hive-Tools erläutert.  

Spark- und Hive-Tools können auf allen von Visual Studio Code unterstützten Plattformen installiert werden. Die folgenden Voraussetzungen gelten für die verschiedenen Plattformen.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Elemente sind zum Ausführen der Schritte in diesem Artikel erforderlich:

- Ein Azure HDInsight-Cluster. Informationen zum Erstellen eines Clusters finden Sie unter [Hadoop-Tutorial: Erste Schritte bei der Verwendung von Hadoop in HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). Alternativ können Sie einen Spark- und Hive-Cluster verwenden, der einen Apache Livy-Endpunkt unterstützt.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono wird nur für Linux und macOS benötigt.
- [Eine interaktive PySpark-Umgebung für Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Ein lokales Verzeichnis. In diesem Artikel wird das Verzeichnis **C:\HD\HDexample** verwendet.

## <a name="install-spark--hive-tools"></a>Installieren von Spark & Hive Tools

Wenn die Voraussetzungen erfüllt sind, können Sie Spark- und Hive-Tools für Visual Studio Code installieren, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie Visual Studio Code.

2. Navigieren Sie über die Menüleiste zu **Ansicht** > **Erweiterungen**.

3. Geben Sie **Spark & Hive** in das Suchfeld ein.

4. Wählen Sie in den Suchergebnissen **Spark & Hive Tools** aus, und wählen Sie dann **Installieren** aus:

   ![Installation der Python-Erweiterung für Spark- und Hive-Tools für Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Wählen Sie bei Bedarf **Erneut laden** aus.

## <a name="open-a-work-folder"></a>Öffnen eines Arbeitsordners

Um einen Arbeitsordner zu öffnen und eine Datei in Visual Studio Code zu erstellen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie auf der Menüleiste zu **Datei** > **Ordner öffnen...**  > **C:\HD\HDexample**, und klicken Sie dann auf die Schaltfläche **Ordner auswählen**. Daraufhin wird der Ordner links in der Ansicht **Explorer** angezeigt.

2. Wählen Sie in der **Explorer**-Ansicht den Ordner **HDexample** aus, und wählen Sie dann neben dem Arbeitsordner das Symbol **Neue Datei** aus:

   ![Visual Studio Code: Symbol „Neue Datei“](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Benennen Sie die neue Datei mit der Dateierweiterung `.hql` (Hive-Abfragen) oder `.py` (Spark-Skript). In diesem Beispiel wird **HelloWorld.hql** verwendet.

## <a name="set-the-azure-environment"></a>Einrichten der Azure-Umgebung

Führen Sie für Benutzer der nationalen Cloud diese Schritte aus, um zunächst die Azure-Umgebung festzulegen, und verwenden Sie anschließend den Befehl **Azure: Anmelden**, um sich bei Azure anzumelden:

1. Navigieren Sie zu **Datei** > **Einstellungen** > **Einstellungen**.
2. Suchen Sie nach folgender Zeichenfolge: **Azure: Cloud**.
3. Wählen Sie in der Liste die nationale Cloud aus:

   ![Festlegen der Konfiguration des Standardanmeldeeintrags](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Verbinden mit einem Azure-Konto

Bevor Sie Skripts aus Visual Studio Code an Ihre Cluster übermitteln können, können sich Benutzer entweder beim Azure-Abonnement anmelden oder [einen HDInsight-Cluster verknüpfen](#link-a-cluster). Verwenden Sie zum Herstellen einer Verbindung mit Ihrem HDInsight-Cluster den Ambari-Benutzernamen mit dem zugehörigen Kennwort oder die in eine Domäne eingebundenen Anmeldeinformationen für das ESP-Cluster. Führen Sie diese Schritte aus, um eine Verbindung mit Azure herzustellen:

1. Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette...** , und geben Sie **Azure: Anmelden** ein:

   ![Spark- und Hive-Tools für Visual Studio Code: Anmeldung](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Befolgen Sie die Anleitung für die Anmeldung, um sich bei Azure anzumelden. Nach dem Herstellen der Verbindung wird Ihr Azure-Kontoname unten im Visual Studio Code-Fenster in der Statusleiste angezeigt.  

## <a name="link-a-cluster"></a>Verknüpfen eines Clusters

### <a name="link-azure-hdinsight"></a>Verknüpfung: Azure HDInsight

Sie können einen normalen Cluster verknüpfen, indem Sie einen von [Apache Ambari](https://ambari.apache.org/) verwalteten Benutzernamen verwenden, oder Sie können einen per Enterprise Security Pack geschützten Hadoop-Cluster verknüpfen, indem Sie einen Domänenbenutzernamen (z.B. `user1@contoso.com`) verwenden.

1. Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette...** , und geben Sie **Spark/Hive: Link a Cluster** (Spark / Hive: Cluster verknüpfen) ein.

   ![Befehlspalette: Befehl für die Clusterverknüpfung](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Wählen Sie als Typ des verknüpften Clusters **Azure HDInsight** aus.

3. Geben Sie die HDInsight-Cluster-URL ein.

4. Geben Sie Ihren Ambari-Benutzernamen ein. Der Standardname ist **admin**.

5. Geben Sie Ihr Ambari-Kennwort ein.

6. Wählen Sie den Clustertyp aus.

7. Legen Sie den Anzeigenamen des Clusters fest (optional).

8. Überprüfen Sie die **Ausgabe**.

   > [!NOTE]  
   > Der verknüpfte Benutzername und das verknüpfte Kennwort werden verwendet, wenn der Cluster beim Azure-Abonnement angemeldet ist und einen Cluster verknüpft hat.  

### <a name="link-generic-livy-endpoint"></a>Verknüpfung: Generic Livy Endpoint (Generischer Livy-Endpunkt)

1. Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette...** , und geben Sie **Spark/Hive: Link a Cluster** (Spark / Hive: Cluster verknüpfen) ein.

2. Wählen Sie als Typ des verknüpften Clusters **Generic Livy Endpoint** (Generischer Livy-Endpunkt) aus.

3. Geben Sie den generischen Livy-Endpunkt ein. Beispiel: http\://10.172.41.42:18080.

4. Wählen Sie als Autorisierungstyp **Standard** oder **Keine** aus.  Wenn Sie **Standard** auswählen:  
   
   1. Geben Sie Ihren Ambari-Benutzernamen ein. Der Standardname ist **admin**.  

   2. Geben Sie Ihr Ambari-Kennwort ein.

5. Überprüfen Sie die **Ausgabe**.

## <a name="list-clusters"></a>Auflisten der Cluster

1. Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette...** , und geben Sie **Spark/Hive: List Cluster** (Spark / Hive: Cluster auflisten).

2. Wählen Sie das gewünschte Abonnement aus.

3. Überprüfen Sie die Ansicht **Ausgabe**. Diese Ansicht zeigt Ihre verknüpften Cluster und alle Cluster in Ihrem Azure-Abonnement:

   ![Legen Sie eine Standardkonfiguration für die Cluster fest.](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Festlegen des Standardclusters

1. Öffnen Sie den Ordner **HDexample**, der [weiter oben](#open-a-work-folder) erläutert ist, erneut (sofern er geschlossen ist).  

2. Wählen Sie die Datei **HelloWorld.hql** aus, die [zuvor](#open-a-work-folder) erstellt wurde. Sie wird im Skript-Editor geöffnet.

3. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann **Spark / Hive: Set Default Cluster** (Spark / Hive: Standardcluster festlegen) aus.  

4. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-an-azure-account), oder verknüpfen Sie einen Cluster, sofern dies noch nicht erfolgt ist.

5. Legen Sie einen Cluster als Standardcluster für die aktuelle Skriptdatei fest. Die Tools aktualisieren die Konfigurationsdatei **.VSCode\settings.json** automatisch:

   ![Standardclusterkonfiguration festlegen](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Übermitteln von interaktiven Hive-Abfragen und von Hive-Batchskripts

Mit Spark- und Hive-Tools für Visual Studio Code können Sie interaktive Hive-Abfragen und Hive-Batchskripts an Ihre Cluster übermitteln.

1. Öffnen Sie den Ordner **HDexample**, der [weiter oben](#open-a-work-folder) erläutert ist, erneut (sofern er geschlossen ist).  

2. Wählen Sie die Datei **HelloWorld.hql** aus, die [zuvor](#open-a-work-folder) erstellt wurde. Sie wird im Skript-Editor geöffnet.

3. Kopieren Sie den folgenden Code, fügen Sie ihn in Ihre Hive-Datei ein, und speichern Sie diese dann:

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-an-azure-account), oder verknüpfen Sie einen Cluster, sofern dies noch nicht erfolgt ist.

5. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **Hive: Interactive** aus, um die Abfrage zu übermitteln, oder verwenden Sie die Tastenkombination STRG+ALT+I.  Wählen Sie **Hive: Batch** aus, um das Skript zu übermitteln, oder verwenden Sie die Tastenkombination STRG+ALT+H.  

6. Wenn Sie keinen Standardcluster angegeben haben, wählen Sie einen Cluster aus. Die Tools unterstützen es auch, dass Sie über das Kontextmenü einen Codeblock anstelle der gesamten Skriptdatei übermitteln. Nach kurzer Zeit werden die Abfrageergebnisse auf einer neuen Registerkarte angezeigt:

   ![Ergebnis der interaktiven Apache Hive-Abfrage](./media/hdinsight-for-vscode/interactive-hive-result.png)

   - Bereich **ERGEBNISSE**: Sie können das gesamte Ergebnis als CSV-, JSON- oder Excel-Datei in einem lokalen Pfad speichern oder einfach mehrere Zeilen auswählen.

   - Bereich **MELDUNGEN**: Wenn Sie eine **Zeilennummer** auswählen, gelangen Sie zur ersten Zeile des Skripts, das derzeit ausgeführt wird.

## <a name="submit-interactive-pyspark-queries"></a>Übermitteln interaktiver PySpark-Abfragen

Benutzer können interaktive PySpark-Abfrage wie folgt ausführen:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>Mithilfe des interaktiven PySpark-Befehls in einer PY-Datei
Gehen Sie wie folgt vor, um die Abfragen mithilfe des interaktiven PySpark-Befehls zu übermitteln:

1. Öffnen Sie den Ordner **HDexample**, der [weiter oben](#open-a-work-folder) erläutert ist, erneut (sofern er geschlossen ist).  

2. Erstellen Sie mit den [zuvor](#open-a-work-folder) erläuterten Schritten eine neue **HelloWorld.py**-Datei.

3. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Skriptdatei ein:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. Die Aufforderung, den PySpark/Synapse PySpark-Kernel zu installieren, wird unten rechts im Fenster angezeigt. Sie können auf **Install** (Installieren) klicken, um mit der PySpark/Synapse PySpark-Installation fortzufahren, oder auf **Skip** (Überspringen), um diesen Schritt auszulassen.

   ![Der Screenshot zeigt eine Option zum Überspringen der PySpark-Installation.](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Wenn Sie es später installieren müssen, können Sie zu **Datei** > **Einstellung** > **Einstellungen** navigieren und **HDInsight: Enable Skip PySpark Installation** (HDInsight: Überspringen der PySpark-Installation aktivieren) in den Einstellungen deaktivieren. 
    
    ![Der Screenshot zeigt die Option „Überspringen der PySpark-Installation aktivieren“.](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Wenn die Installation in Schritt 4 erfolgreich war, wird das Nachrichtenfeld „PySpark installed successfully“ (PySpark erfolgreich installiert) in der unteren rechten Ecke des Fensters angezeigt. Klicken Sie auf **Erneut laden**, um das Fenster neu zu laden.

   ![PySpark erfolgreich installiert](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. Navigieren Sie in der Menüleiste zu **View** > **Command Palette...** (Ansicht > Befehlspalette...), oder verwenden Sie die Tastenkombination **UMSCHALT+STRG+P**, und geben Sie **Python: Select Interpreter to start Jupyter Server** (Interpreter auswählen, um Jupyter-Server zu starten) ein.

   ![Select Interpreter to start Jupyter Server (Interpreter auswählen, um Jupyter-Server zu starten)](./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png)

8. Wählen Sie unten die Python-Option aus.

   ![Auswählen der unten angegebenen Option](./media/hdinsight-for-vscode/choose-the-below-option.png)
    
9. Navigieren Sie in der Menüleiste zu **View** > **Command Palette...** (Ansicht > Befehlspalette...), oder verwenden Sie die Tastenkombination **UMSCHALT+STRG+P**, und geben Sie **Developer: Fenster neu laden** ein.

   ![Fenster neu laden](./media/hdinsight-for-vscode/reload-window.png)

10. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-an-azure-account), oder verknüpfen Sie einen Cluster, sofern dies noch nicht erfolgt ist.

11. Wählen Sie den gesamten Code aus, klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie **Spark: PySpark Interactive/Synapse: PySpark Interactive** aus, um die Abfrage zu übermitteln. 

    ![PySpark Interactive-Kontextmenü](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

12. Wählen Sie den Cluster aus, wenn Sie keinen Standardcluster angegeben haben. Nach kurzer Zeit werden die **Python Interactive**-Ergebnisse in einer neuen Registerkarte angezeigt. Klicken Sie auf PySpark, um den Kernel auf **PySpark/Synapse Pyspark** umzustellen. Anschließend wird der Code erfolgreich ausgeführt. Wenn Sie zum Synapse PySpark-Kernel wechseln möchten, empfehlen wir Ihnen, die automatischen Einstellungen im Azure-Portal zu deaktivieren. Andernfalls kann es sehr lange dauern, den Cluster zu aktivieren und den Synapse-Kernel für die erstmalige Verwendung festzulegen. Die Tools unterstützen es auch, dass Sie über das Kontextmenü einen Codeblock anstelle der gesamten Skriptdatei übermitteln:

    ![PySpark Interactive: Python Interactive-Fenster](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

13. Geben Sie **%%info** ein, und drücken Sie dann UMSCHALT+EINGABETASTE, um die Auftragsinformationen anzuzeigen (optional):

    ![PySpark: Auftragsinformationen in der interaktiven Ansicht](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

Das Tool unterstützt auch die **Spark SQL**-Abfrage:

  ![PySpark Interactive-Ergebnis anzeigen](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Ausführen einer interaktiven Abfrage in der PY-Datei mithilfe des Kommentars #%%

1. Fügen Sie **#%%** vor dem Py-Code hinzu, um zur Notebook-Oberfläche zu wechseln.

   ![#%% hinzugefügt](./media/hdinsight-for-vscode/run-cell.png)

2. Klicken Sie auf **Zelle ausführen**. Nach kurzer Zeit werden die Python Interactive-Ergebnisse auf einer neuen Registerkarte angezeigt. Klicken Sie auf PySpark, um den Kernel auf PySpark/Synapse PySpark umzustellen. Wenn Sie anschließend erneut auf **Zelle ausführen** klicken, ist die Ausführung erfolgreich.

   ![Ergebnisse von „Zelle ausführen“](./media/hdinsight-for-vscode/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Nutzen der IPYNB-Unterstützung über die Python-Erweiterung

1. Sie können eine Jupyter Notebook-Instanz per Befehl über die Befehlspalette oder durch Erstellen einer neuen IPYNB-Datei in Ihrem Arbeitsbereich erstellen. Weitere Informationen finden Sie unter [Arbeiten mit Jupyter Notebook in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

2. Klicken Sie auf **Zelle ausführen**, folgen Sie den Aufforderungen bis zu **Set the default spark pool** (Spark-Standardpool festlegen) (das Festlegen des Standardclusters/-pools vor dem Öffnen eines Notebooks ist dringend zu empfehlen), und wählen Sie dann **Neu laden** aus, um das Fenster neu zu laden.

   ![Festlegen des Spark-Standardpools und erneutes Laden](./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png)

3. Klicken Sie auf PySpark, um den Kernel auf **PySpark/Synapse PySpark** umzustellen, und klicken Sie dann auf **Zelle ausführen**. Nach einer Weile wird das Ergebnis angezeigt.

   ![Ausführen von IPYNB-Ergebnissen](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
> Version 2020.5.78807 und höher von „ms-python“ wird von dieser Erweiterung nicht unterstützt. Dies ist ein [bekanntes Problem](#known-issues).

## <a name="submit-pyspark-batch-job"></a>Übermitteln von PySpark-Batchaufträgen

1. Öffnen Sie den Ordner **HDexample**, der [weiter oben](#open-a-work-folder) erläutert ist, erneut (sofern er geschlossen ist).  

2. Erstellen Sie mit den [zuvor](#open-a-work-folder) erläuterten Schritten eine neue **BatchFile.py**-Datei.

3. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Skriptdatei ein:

   ```python
   from __future__ import print_function
   import sys
   from operator import add
   from pyspark.sql import SparkSession
   if __name__ == "__main__":
       spark = SparkSession\
           .builder\
           .appName("PythonWordCount")\
           .getOrCreate()
   
       lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
       counts = lines.flatMap(lambda x: x.split(' '))\
                  .map(lambda x: (x, 1))\
                   .reduceByKey(add)
       output = counts.collect()
       for (word, count) in output:
           print("%s: %i" % (word, count))
       spark.stop()
   ```

4. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-an-azure-account), oder verknüpfen Sie einen Cluster, sofern dies noch nicht erfolgt ist.

5. Klicken Sie mit der rechten Maustaste in den Skript-Editor, und wählen Sie dann **Spark: PySpark Batch** oder **Synapse: PySpark Batch** _ aus.

6. Wählen Sie einen Cluster/Spark-Pool aus, an den Ihr PySpark-Auftrag übermittelt werden soll:

   ![Ergebnisausgabe für die Übermittlung des Python-Auftrags](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Nachdem Sie einen Python-Auftrag übermittelt haben, werden Übermittlungsprotokolle in Visual Studio Code im Fenster _ *AUSGABE** angezeigt. Die URL der Spark-Benutzeroberfläche und die URL der Yarn-Benutzeroberfläche werden ebenfalls angezeigt. Wenn Sie den Batchauftrag an einen Apache Spark-Pool übermitteln, werden die URL der Spark-Verlaufsoberfläche und die URL der Benutzeroberfläche der Spark-Auftragsanwendung ebenfalls angezeigt. Sie können die URL in einem Webbrowser öffnen, um den Status des Auftrags zu verfolgen.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrieren in den HDInsight-Identitätsbroker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Herstellen einer Verbindung mit Ihrem HDInsight ESP-Cluster mit Identitätsbroker (HIB)

Sie können die normalen Schritte zum Anmelden beim Azure-Abonnement ausführen, um eine Verbindung mit Ihrem HDInsight ESP-Cluster mit Identitätsbroker (HIB) herzustellen. Nach der Anmeldung wird die Clusterliste im Azure Explorer angezeigt. Weitere Anweisungen finden Sie unter [Herstellen einer Verbindung mit Ihrem HDInsight-Cluster](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Ausführen eines Hive-/PySpark-Auftrags in einem ESP-Cluster in HDInsight mit Identitätsbroker (HIB)

Zum Ausführen eines Hive-Auftrags können Sie die normalen Schritte ausführen, um einen Auftrag an einen HDInsight-ESP-Cluster mit Identitätsbroker zu übermitteln. Weitere Informationen finden Sie unter [Übermitteln von interaktiven Hive-Abfragen und Hive-Batchskripts](#submit-interactive-hive-queries-and-hive-batch-scripts).

Zum Ausführen eines interaktiven PySpark-Auftrags können Sie die normalen Schritte ausführen, um einen Auftrag an einen HDInsight-ESP-Cluster mit Identitätsbroker zu übermitteln. Weitere Informationen finden Sie unter [Übermitteln interaktiver PySpark-Abfragen](#submit-interactive-pyspark-queries).

Zum Ausführen eines PySpark-Batchauftrags können Sie die normalen Schritte ausführen, um einen Auftrag an einen HDInsight-ESP-Cluster mit Identitätsbroker zu übermitteln. Weitere Informationen finden Sie unter [Übermitteln von PySpark-Batchaufträgen](#submit-pyspark-batch-job).

## <a name="apache-livy-configuration"></a>Apache Livy-Konfiguration

Die [Apache Livy](https://livy.incubator.apache.org/)-Konfiguration wird unterstützt. Sie können Sie in der Datei **.VSCode\settings.json** im Arbeitsbereichsordner konfigurieren. Derzeit wird in der Livy-Konfiguration nur das Python-Skript unterstützt. Weitere Informationen finden Sie in der [Livy-Infodatei](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Auslösen der Livy-Konfiguration**

### <a name="method-1"></a>Methode 1  

1. Navigieren Sie in der Menüleiste zu **Datei** > **Einstellungen** > **Einstellungen**.
2. Geben Sie in das Feld **Sucheinstellungen** die Zeichenfolge **HDInsight Job Submission: Livy Conf** (HDInsight-Auftragsübermittlung: Livy-Konfiguration) ein.  
3. Klicken Sie beim relevanten Suchergebnis auf **In „settings.json“ bearbeiten**.

### <a name="method-2"></a>Methode 2

Übermitteln Sie eine Datei. Daraufhin wird der Ordner `.vscode` automatisch dem Arbeitsordner hinzugefügt. Sie können die Livy-Konfiguration anzeigen, indem Sie **.vscode\settings.json** auswählen.

- Die Projekteinstellungen:

  ![HDInsight Apache: Livy-Konfiguration](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

  >[!NOTE]
  >Legen Sie für die Einstellungen **driverMemory** und **executorMemory** den Wert und die Einheit fest. Beispiel: 1g oder 1024m.

- Unterstützte Livy-Konfigurationen:

  **POST /batches**
  
  **Anforderungstext**

  | name | description | type |
  | --- | --- | --- |
  | file | Die Datei, die die auszuführende Anwendung enthält | Pfad (erforderlich) |
  | proxyUser | Der Benutzer, dessen Identität beim Ausführen des Auftrags angenommen werden soll | String |
  | className | Die Java-/Spark-Hauptklasse der Anwendung | String |
  | args | Die Befehlszeilenargumente für die Anwendung | Liste von Zeichenfolgen |
  | jars | JAR-Dateien, die in dieser Sitzung verwendet werden | Liste von Zeichenfolgen | 
  | pyFiles | Die Python-Dateien, die in dieser Sitzung verwendet werden sollen | Liste von Zeichenfolgen |
  | files | Dateien, die in dieser Sitzung verwendet werden | Liste von Zeichenfolgen |
  | driverMemory | Die Menge an Arbeitsspeicher, die für den Treiberprozess verwendet werden soll | String |
  | driverCores | Die Anzahl der Kerne, die für den Treiberprozess verwendet werden soll | Int |
  | executorMemory | Die Menge an Arbeitsspeicher, die pro Executorprozess verwendet werden soll | String |
  | executorCores | Die Anzahl von Kernen, die für jeden Executor verwendet werden sollen | Int |
  | numExecutors | Die Anzahl der Executors, die für diese Sitzung gestartet werden sollen | Int |
  | archives | Die Archive, die in dieser Sitzung verwendet werden sollen | Liste von Zeichenfolgen |
  | queue | Der Name der YARN-Warteschlange, an die gesendet wird| String |
  | name | Der Name dieser Sitzung | String |
  | conf | Spark-Konfigurationseigenschaften | Zuordnung von Schlüsseln zu Werten |

  **Antworttext** Das erstellte Batchobjekt.

  | name | description | type |
  | --- | ---| --- |
  | id | Sitzungs-ID | Int |
  | appId | Die Anwendungs-ID dieser Sitzung | String |
  | appInfo | Detaillierte Anwendungsinformationen | Zuordnung von Schlüsseln zu Werten |
  | log | Protokollzeilen | Liste von Zeichenfolgen |
  | state |Batchstatus | String |

  > [!NOTE]
  > Die zugewiesene Livy-Konfiguration wird im Ausgabebereich angezeigt, wenn Sie das Skript übermitteln.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integration mit Azure HDInsight per Explorer

Sie können eine Vorschau der Hive-Tabelle in Ihren Clustern direkt über den **Azure HDInsight**-Explorer anzeigen:

1. [Stellen Sie eine Verbindung mit Ihrem Azure-Konto her](#connect-to-an-azure-account), sofern noch keine Verbindung besteht.

2. Wählen Sie das **Azure**-Symbol in der äußerst linken Spalte aus.

3. Erweitern Sie im linken Bereich **AZURE: HDINSIGHT**. Die verfügbaren Abonnements und Cluster werden aufgelistet.

4. Erweitern Sie den Cluster, um die Hive-Metadatendatenbank und das Hive-Tabellenschema anzuzeigen.

5. Klicken Sie mit der rechten Maustaste auf die Hive-Tabelle. Zum Beispiel: **hivesampletable**. Wählen Sie **Vorschau** aus.

   ![Spark und Hive für Visual Studio Code: Vorschau für Hive-Tabelle](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Das Fenster **Ergebnisvorschau** wird geöffnet:

   ![Spark und Hive für Visual Studio Code: Fenster „Ergebnisvorschau“](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Bereich ERGEBNISSE

   Sie können das gesamte Ergebnis als CSV-, JSON- oder Excel-Datei in einem lokalen Pfad speichern, oder wählen Sie einfach mehrere Zeilen aus.

- Bereich MELDUNGEN

  1. Wenn die Tabelle mehr als 100 Zeilen enthält, sehen Sie die folgende Meldung: „Die ersten 100 Zeilen werden für die Hive-Tabelle angezeigt.“
  2. Wenn die Tabelle bis zu 100 Zeilen enthält, sehen Sie die folgende Meldung: „60 Zeilen werden für die Hive-Tabelle angezeigt.“
  3. Wenn die Tabelle keine Zeilen enthält, sehen Sie die folgende Meldung: „`0 rows are displayed for Hive table.`“

     >[!NOTE]
     >
     >Installieren Sie unter Linux xclip, um das Kopieren von Tabellendaten zu ermöglichen.
     >
     >![Spark und Hive für Visual Studio Code unter Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>Zusätzliche Features

Spark und Hive für Visual Studio Code unterstützt auch die folgenden Features:

- **Die automatische Vervollständigung von IntelliSense:** Es werden Vorschläge für Schlüsselwörter, Methoden, Variablen und weitere Programmierelemente aufgelistet. Die unterschiedlichen Objekttypen werden durch entsprechende Symbole dargestellt:

    ![Spark- und Hive-Tools für Visual Studio Code: IntelliSense-Objekte](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense-Fehlermarkierung:** Der Sprachdienst unterstreicht Bearbeitungsfehler im Hive-Skript.     
- **Syntaxhervorhebungen:** Der Sprachdienst verwendet verschiedene Farben, um Variablen, Schlüsselwörter, Datentypen, Funktionen und weitere Programmierelemente zu unterscheiden:

    ![Syntaxhervorhebungen in Spark & Hive Tools für Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rolle nur mit Leseberechtigung

Benutzer, denen für den Cluster die Rolle „Nur Leser“ (reader-only) zugewiesen ist, können keine Aufträge an den HDInsight-Cluster übermitteln und die Hive-Datenbank nicht anzeigen. Wenden Sie sich an den Clusteradministrator, damit Ihre Rolle im [Azure-Portal](https://portal.azure.com/) auf [**HDInsight-Clusteroperator**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) aktualisiert wird. Wenn Sie gültige Ambari-Anmeldeinformationen haben, können Sie manuell mit dem Cluster verknüpfen, indem Sie die weiter unten aufgeführten Anweisungen ausführen.

### <a name="browse-the-hdinsight-cluster"></a>Durchsuchen des HDInsight-Clusters  

Wenn Sie im Azure HDInsight-Explorer einen HDInsight-Cluster auswählen, um diesen zu erweitern, werden Sie zum Verknüpfen mit dem Cluster aufgefordert, wenn Sie für den Cluster die Rolle „Nur Leser“ haben. Verwenden Sie die folgende Methode, um über Ihre Ambari-Anmeldeinformationen die Verknüpfung mit dem Cluster herzustellen.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Übermitteln des Auftrags an den HDInsight-Cluster

Wenn Sie für den Cluster die Rolle „Nur Leser“ haben und einen Auftrag an den HDInsight-Cluster übermitteln, werden Sie aufgefordert, mit dem Cluster zu verknüpfen. Führen Sie die folgenden Schritte aus, um über die Ambari-Anmeldeinformationen die Verknüpfung mit dem Cluster herzustellen.

### <a name="link-to-the-cluster"></a>Verknüpfen mit dem Cluster

1. Geben Sie einen gültigen Ambari-Benutzernamen ein.
2. Geben Sie ein gültiges Kennwort ein.

   ![Spark- und Hive-Tools für Visual Studio Code: Benutzername](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark- und Hive-Tools für Visual Studio Code: Kennwort](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

   > [!NOTE]
   >
   >Sie können `Spark / Hive: List Cluster` verwenden, um den verknüpften Cluster zu überprüfen:
   >
   >![Spark- und Hive-Tools für Visual Studio Code: Leser verknüpft](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Durchsuchen eines Data Lake Storage Gen2-Kontos

Wählen Sie Azure HDInsight-Explorer aus, um ein Data Lake Storage Gen2-Konto zu erweitern. Sie werden aufgefordert, den Speicherzugriffsschlüssel einzugeben, wenn Ihr Azure-Konto keinen Zugriff auf den Gen2-Speicher hat. Sobald der Zugriffsschlüssel validiert ist, wird das Data Lake Storage Gen2-Konto automatisch erweitert.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Übermitteln von Aufträgen an einen HDInsight-Cluster mit Data Lake Storage Gen2

Übermitteln Sie einen Auftrag an einen HDInsight-Cluster mit Data Lake Storage Gen2. Sie werden aufgefordert, den Speicherzugriffsschlüssel einzugeben, wenn Ihr Azure-Konto keinen Schreibzugriff auf den Gen2-Speicher hat. Sobald der Zugriffsschlüssel validiert ist, wird der Auftrag erfolgreich übermittelt.

![Spark- und Hive-Tools für Visual Studio Code: Zugriffsschlüssel](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Den Zugriffsschlüssel für das Speicherkonto erhalten Sie über das Azure-Portal. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Aufheben der Verknüpfung von Clustern

1. Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette**, und geben Sie dann **Spark / Hive: Unlink a Cluster** (Spark / Hive: Clusterverknüpfung aufheben) ein.  

2. Wählen Sie den Cluster aus, dessen Verknüpfung aufgehoben werden soll.  

3. Sehen Sie sich die Ansicht **AUSGABE** zur Überprüfung an.  

## <a name="sign-out"></a>Abmelden  

Navigieren Sie auf der Menüleiste zu **Ansicht** > **Befehlspalette**, und geben Sie dann **Azure: Abmelden** ein.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>Version 2020.5.78807 und höher von „ms-python“ wird von dieser Erweiterung nicht unterstützt 

„Failed to connect to Jupyter notebook.“ (Fehler beim Verbinden mit Jupyter Notebook.) ist ein bekanntes Problem in Python-Version 2020.5.78807 und höher. Es wird empfohlen, dass Benutzer Version **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** von „ms-python“ verwenden, um dieses Problem zu vermeiden.

![Bekannte Probleme](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>Nächste Schritte

Ein Video, in dem die Verwendung von Spark und Hive für Visual Studio Code gezeigt wird, finden Sie unter [Spark und Hive für Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).