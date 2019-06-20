---
title: Verwenden von Zeppelin Notebooks mit Apache Spark-Cluster unter Azure HDInsight
description: Enthält eine Schritt-für-Schritt-Anleitung zur Verwendung von Zeppelin Notebooks mit Apache Spark-Clustern unter Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 9fc18b11b24791c1e154d89d757408da4ab20539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709550"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Verwenden von Apache Zeppelin Notebooks mit Apache Spark-Cluster in Azure HDInsight

HDInsight Spark-Cluster enthalten [Apache Zeppelin](https://zeppelin.apache.org/) Notebooks, mit denen Sie [Apache Spark](https://spark.apache.org/)-Aufträge ausführen können. In diesem Artikel wird beschrieben, wie Sie das Zeppelin Notebook in einem HDInsight-Cluster verwenden.

**Voraussetzungen:**

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Das URI-Schema für Ihren primären Clusterspeicher. Dies ist `wasb://` für Azure Blob Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Blob Storage oder Data Lake Storage Gen2 aktiviert ist, lautet der URI `wasbs://` bzw. `abfss://`.  Weitere Informationen finden Sie auch unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../../storage/common/storage-require-secure-transfer.md).

## <a name="launch-an-apache-zeppelin-notebook"></a>Starten des Apache Zeppelin Notebooks

1. Wählen Sie im Spark-Cluster **Übersicht** unter **Clusterdashboards** die Option **Zeppelin-Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein.  

   > [!NOTE]  
   > Sie können auch das Zeppelin Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Erstellen Sie ein neues Notebook. Navigieren Sie im Headerbereich zu **Notebook** > **Neue Notiz erstellen**.

    ![Erstellen eines neuen Zeppelin Notebooks](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Erstellen eines neuen Zeppelin Notebooks")

    Geben Sie einen Namen für das Notebook ein, und wählen Sie anschließend **Notiz erstellen**.

3. Stellen Sie sicher, dass im Header des Notebooks der Status „Verbunden“ angezeigt wird. Dies wird durch einen grünen Punkt in der oberen rechten Ecke angezeigt.

    ![Zeppelin Notebook-Status](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook-Status")

4. Laden Sie Beispieldaten in eine temporäre Tabelle. Wenn Sie einen Spark-Cluster in HDInsight erstellen, wird die Beispieldatei `hvac.csv` in das zugeordnete Speicherkonto unter `\HdiSamples\SensorSampleData\hvac` kopiert.

    Fügen Sie in den leeren Absatz, der im neuen Notebook standardmäßig erstellt wird, den folgenden Codeausschnitt ein.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Drücken Sie **UMSCHALT+EINGABETASTE**, oder klicken Sie auf die Schaltfläche **Wiedergeben** für den Absatz, um den Codeausschnitt auszuführen. Der Status in der rechten Ecke des Absatzes sollte sich entsprechend ändern: BEREIT, AUSSTEHEND, WIRD AUSGEFÜHRT bis zu BEENDET. Die Ausgabe wird unten im Absatz angezeigt. Der Screenshot sieht folgendermaßen aus:

    ![Erstellen einer temporären Tabelle auf der Grundlage von Rohdaten](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Erstellen einer temporären Tabelle auf der Grundlage von Rohdaten")

    Sie können auch einen Titel für jeden Absatz angeben. Wählen Sie in der rechten Ecke des Absatzes das Symbol **Einstellungen** (Zahnrad) und dann **Titel anzeigen**.  

    > [!NOTE]  
    > Der %spark2-Interpreter wird in Zeppelin-Notebooks bei keiner HDInsight-Version unterstützt, und der %sh-Interpreter wird ab HDInsight 4.0 nicht unterstützt.

5. Sie können jetzt Spark SQL-Anweisungen für die Tabelle `hvac` ausführen. Fügen Sie die folgende Abfrage in einen neuen Absatz ein. Mit der Abfrage werden die Gebäude-ID und der Unterschied zwischen den Ziel- und Ist-Temperaturen für jedes Gebäude an einem bestimmten Datum abgerufen. Drücken Sie **UMSCHALT+EINGABETASTE**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    Mit der **%sql**-Anweisung am Anfang wird das Notebook angewiesen, den Livy Scala-Interpreter zu verwenden.

6. Wählen Sie das Symbol **Balkendiagramm** aus, um die Anzeige zu ändern.  Unter der Option **Einstellungen**, die nach dem Auswählen von **Balkendiagramm** angezeigt wird, können Sie **Schlüssel** und **Werte** auswählen.  Im folgenden Screenshot ist die Ausgabe dargestellt.

    ![Ausführen einer Spark-SQL-Anweisung mit dem Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Ausführen einer Spark SQL-Anweisung mit dem Notebook")

7. Sie können auch Spark-SQL-Anweisungen ausführen, indem Sie die Variablen in der Abfrage verwenden. Der nächste Codeausschnitt zeigt, wie Sie eine Variable (`Temp`) in der Abfrage mit den möglichen Werten definieren, die für die Abfrage verwendet werden sollen. Beim ersten Ausführen der Abfrage wird automatisch eine Dropdownliste mit den Werten aufgefüllt, die Sie für die Variable angegeben haben.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Fügen Sie diesen Codeausschnitt in einen neuen Absatz ein, und drücken Sie **UMSCHALT+EINGABETASTE**. Wählen Sie anschließend in der Dropdownliste **Temp** den Wert **65** aus. 

8. Wählen Sie das Symbol **Balkendiagramm** aus, um die Anzeige zu ändern.  Wählen Sie anschließend **Einstellungen**, und nehmen Sie die folgenden Änderungen vor:

   * **Gruppen:**  Fügen Sie **targettemp** hinzu.  
   * **Werte:** 1. Entfernen Sie **date**.  2. Fügen Sie **temp_diff** hinzu.  3.  Ändern Sie den Aggregator von **SUM** in **AVG**.  

     Im folgenden Screenshot ist die Ausgabe dargestellt.

     ![Ausführen einer Spark-SQL-Anweisung mit dem Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Ausführen einer Spark SQL-Anweisung mit dem Notebook")

9. Starten Sie den Livy-Interpreter neu, um die Anwendung zu beenden. Öffnen Sie zu diesem Zweck die Einstellungen des Interpreters, indem Sie oben rechts den Namen des angemeldeten Benutzers und dann **Interpreter** auswählen.  

    ![Starten des Interpreters](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-Ausgabe")

10. Scrollen Sie zu **livy**, und wählen Sie dann **restart**.  Wählen Sie in der Eingabeaufforderung die Option **OK**.

    ![Neustarten des Livy-Interpreters](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Neustarten des Zeppelin-Interpreters")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Wie verwende ich externe Pakete mit dem Notebook?
Sie können das Zeppelin Notebook in einem Apache Spark-Cluster in HDInsight konfigurieren, um externe, von der Community bereitgestellte Pakete zu verwenden, die nicht im Lieferumfang des Clusters enthalten sind. Sie können das [Maven Repository](https://search.maven.org/) nach einer vollständigen Liste der verfügbaren Pakete durchsuchen. Sie können die Liste der verfügbaren Pakete auch aus anderen Quellen abrufen. Beispielsweise steht eine vollständige Liste der von der Community bereitgestellten Pakete auf [Spark-Pakete](https://spark-packages.org/)zur Verfügung.

In diesem Artikel wird beschrieben, wie Sie das Paket [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) mit Jupyter Notebook verwenden.

1. Öffnen Sie die Einstellungen des Interpreters. Wählen Sie in der Ecke oben rechts den Namen des angemeldeten Benutzers und dann **Interpreter** aus.

    ![Starten des Interpreters](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-Ausgabe")

2. Scrollen Sie zu **livy**, und wählen Sie dann **edit**.

    ![Ändern der Interpreter-Einstellungen](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Ändern der Interpreter-Einstellungen")

3. Fügen Sie einen neuen Schlüssel mit dem Namen `livy.spark.jars.packages` hinzu, und legen Sie seinen Wert im Format `group:id:version` fest. Wenn Sie das Paket [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) verwenden möchten, müssen Sie den Wert des Schlüssels auf `com.databricks:spark-csv_2.10:1.4.0` festlegen.

    ![Ändern der Interpreter-Einstellungen](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Ändern der Interpreter-Einstellungen")

    Wählen Sie **Speichern**, und starten Sie dann den Livy-Interpreter neu.

4. Hier ist angegeben, wie Sie zum Wert des oben eingegebenen Schlüssels gelangen, falls dies für Sie interessant ist.
   
    a. Suchen Sie das Paket im Maven-Repository. In diesem Tutorial haben wir [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) verwendet.
   
    b. Sammeln Sie im Repository die Werte für **GroupId**, **ArtifactId** und **Version**.
   
    ![Verwenden externer Pakete mit Jupyter Notebooks](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Verwenden externer Pakete mit Jupyter Notebooks")
   
    c. Verketten Sie die drei Werte, getrennt durch einen Doppelpunkt ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Wo werden Zeppelin Notebooks gespeichert?
Die Zeppelin Notebooks werden in den Clusterhauptknoten gespeichert. Wenn Sie den Cluster löschen, werden also auch die Notebooks gelöscht. Falls Sie Ihre Notebooks zur späteren Verwendung auf anderen Clustern beibehalten möchten, müssen Sie sie exportieren, nachdem Sie die Ausführung der Aufträge abgeschlossen haben. Wählen Sie zum Exportieren eines Notebooks das Symbol **Exportieren**. Dies ist in der Abbildung unten dargestellt.

![Herunterladen des Notebooks](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Herunterladen des Notebooks")

Das Notebook wird als JSON-Datei in Ihrem Downloadverzeichnis gespeichert.

## <a name="livy-session-management"></a>Livy-Sitzungsverwaltung
Wenn Sie den ersten Codeabsatz in Ihrem Zeppelin-Notebook ausführen, wird in Ihrem HDInsight Spark-Cluster eine neue Livy-Sitzung erstellt. Diese Sitzung kann für alle Zeppelin Notebooks gemeinsam verwendet werden, die Sie erstellen. Falls die Livy-Sitzung aus irgendeinem Grund beendet wird (Clusterneustart usw.), können Sie keine Aufträge über das Zeppelin Notebook ausführen.

In diesem Fall müssen Sie die folgenden Schritte ausführen, bevor Sie mit dem Ausführen von Aufträgen über ein Zeppelin Notebook beginnen können.  

1. Starten Sie den Livy-Interpreter über das Zeppelin Notebook neu. Öffnen Sie zu diesem Zweck die Einstellungen des Interpreters, indem Sie oben rechts den Namen des angemeldeten Benutzers und dann **Interpreter** auswählen.

    ![Starten des Interpreters](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive-Ausgabe")

2. Scrollen Sie zu **livy**, und wählen Sie dann **restart**.

    ![Neustarten des Livy-Interpreters](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Neustarten des Zeppelin-Interpreters")

3. Führen Sie eine Codezelle über ein vorhandenes Zeppelin Notebook aus. Im HDInsight-Cluster wird eine neue Livy-Sitzung erstellt.

## <a name="seealso"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen
* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Apache Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
