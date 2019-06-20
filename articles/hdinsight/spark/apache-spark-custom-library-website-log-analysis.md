---
title: Analysieren von Websiteprotokollen mit Python-Bibliotheken in Spark – Azure
description: Dieses Notebook veranschaulicht das Analysieren von Protokolldaten unter Verwendung einer benutzerdefinierten Bibliothek mit Spark unter Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: bef71f210e015dc10cd6f5c0c655d0d3beee3655
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728924"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analysieren von Websiteprotokollen mithilfe einer benutzerdefinierten Python-Bibliothek mit Apache Spark-Cluster unter HDInsight

Dieses Notebook veranschaulicht das Analysieren von Protokolldaten unter Verwendung einer benutzerdefinierten Bibliothek mit Apache Spark unter HDInsight. Als benutzerdefinierte Bibliothek verwenden wir eine Python-Bibliothek namens **iislogparser.py**.

> [!TIP]  
> Dieses Tutorial steht auch als Jupyter Notebook für einen Spark-Cluster (Linux) zur Verfügung, den Sie in HDInsight erstellen. In der Notebook-Umgebung können Sie die Python-Ausschnitte direkt im Notebook ausführen. Wenn Sie das Tutorial innerhalb eines Notebooks ausführen möchten, erstellen Sie einen Spark-Cluster, starten Sie ein Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), und führen Sie dann das Notebook **Analyze logs with Spark using a custom library.ipynb** im Ordner **PySpark** aus.
>
>

**Voraussetzungen:**

Sie benötigen Folgendes:

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Speichern von Rohdaten als RDD
In diesem Abschnitt verwenden wir das [Jupyter](https://jupyter.org) Notebook, das einem Apache Spark-Cluster in HDInsight zugeordnet ist, zum Ausführen von Aufträgen, bei denen Ihre Beispielrohdaten verarbeitet und dann als Hive-Tabelle gespeichert werden. Die Beispieldaten sind in einer CSV-Datei (hvac.csv) enthalten, die standardmäßig auf allen Clustern verfügbar ist.

Nachdem Ihre Daten als Apache Hive-Tabelle gespeichert wurden, können wir im nächsten Abschnitt eine Verbindung mit der Hive-Tabelle herstellen. Hierzu verwenden wir BI-Tools wie Power BI und Tableau.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/)im Startmenü auf die Kachel für Ihren Spark-Cluster (sofern Sie die Kachel ans Startmenü angeheftet haben). Sie können auch unter **Alle durchsuchen** > **HDInsight-Cluster** zu Ihrem Cluster navigieren.   
2. Klicken Sie auf dem Blatt für den Spark-Cluster auf **Cluster-Dashboard** und anschließend auf **Jupyter Notebook**. Geben Sie die Administratoranmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden.

   > [!NOTE]
   > Sie können auch das Jupyter Notebook für Ihren Cluster aufrufen, indem Sie in Ihrem Browser die folgende URL öffnen. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Erstellen Sie ein neues Notebook. Klicken Sie auf **Neu** und dann auf **PySpark**.

    ![Erstellen eines neuen Jupyter Notebooks](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Erstellen eines neuen Jupyter Notebooks")
4. Ein neues Notebook mit dem Namen „Untitled.pynb“ wird erstellt und geöffnet. Klicken Sie oben auf den Namen des Notebooks, und geben Sie einen Anzeigenamen ein.

    ![Angeben eines neuen Namens für das Notebook](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Angeben eines neuen Namens für das Notebook")
5. Da Sie ein Notebook mit dem PySpark-Kernel erstellt haben, müssen Sie keine Kontexte explizit erstellen. Die Spark- und Hive-Kontexte werden automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen. Sie können zunächst die Typen importieren, die für dieses Szenario erforderlich sind. Fügen Sie den folgenden Codeausschnitt in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABETASTE**.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Erstellen Sie unter Verwendung der bereits im Cluster verfügbaren Beispielprotokolldaten ein RDD. Die Daten stehen im standardmäßigen, dem Cluster zugeordneten Speicherkonto unter **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log** zur Verfügung.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Rufen Sie einen Beispielprotokollsatz ab, um sich zu vergewissern, dass der vorherige Schritt erfolgreich ausgeführt wurde.

        logs.take(5)

    Es sollte eine Ausgabe angezeigt werden, die Folgendem ähnelt:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analysieren von Protokolldaten mithilfe einer benutzerdefinierten Python-Bibliothek
1. Die ersten Zeilen der oben gezeigten Ausgabe enthalten die Headerinformationen. Die restlichen Zeilen entsprechen jeweils dem im Header beschriebenen Schema. Die Analyse solcher Protokolle ist nicht immer ganz einfach. Daher verwenden wir eine benutzerdefinierte Python-Bibliothek (**iislogparser.py**), um Protokolle dieser Art leichter analysieren zu können. Standardmäßig ist diese Bibliothek in Ihrem Spark-Cluster in HDInsight unter **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py** enthalten.

    Da sie jedoch nicht in `PYTHONPATH` enthalten ist, können wir sie nicht mithilfe einer Importanweisung wie `import iislogparser` verwenden. Zur Verwendung dieser Bibliothek müssen wir sie an alle Workerknoten verteilen. Führen Sie den folgenden Codeausschnitt aus:

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser` stellt die Funktion `parse_log_line` bereit, die `None` zurückgibt, wenn es sich bei einer Protokollzeile um eine Kopfzeile handelt. Handelt es sich dagegen um eine Protokollzeile, gibt die Funktion eine Instanz der Klasse `LogLine` zurück. Verwenden Sie die Klasse `LogLine`, um nur die Protokollzeilen aus dem RDD zu extrahieren:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Rufen Sie einige extrahierte Protokollzeilen ab, um sich zu vergewissern, dass der Schritt erfolgreich ausgeführt wurde.

       logLines.take(2)

   Die Ausgabe sollte in etwa wie folgt aussehen:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. Die Klasse `LogLine` bietet ihrerseits einige hilfreiche Methoden. Hierzu zählt beispielsweise `is_error()`, um zu ermitteln, ob ein Protokolleintrag einen Fehlercode besitzt. Damit können Sie die Anzahl von Fehlern in den extrahierten Protokollzeilen berechnen und anschließend alle Fehler in einer anderen Datei protokollieren.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Folgendes sollte angezeigt werden:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. Sie können auch **Matplotlib** verwenden, um eine Visualisierung der Daten zu erstellen. Wenn Sie also beispielsweise die Ursache von Anforderungen mit hoher Ausführungsdauer isolieren möchten, empfiehlt es sich unter Umständen, die Dateien zu ermitteln, deren Bereitstellung im Schnitt am längsten dauert.
   Der folgende Codeausschnitt ruft die 25 Ressourcen mit der höchsten Anforderungsabwicklungsdauer ab.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Folgendes sollte angezeigt werden:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]
5. Die Informationen können auch als Grafik dargestellt werden. Als ersten Schritt beim Erstellen einer Grafik erstellen wir zunächst eine temporäre Tabelle **AverageTime**. Die Tabelle gruppiert die Protokolle nach Zeit, um zu ermitteln, ob ungewöhnliche Latenzspitzen vorlagen.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Sie können dann die folgende SQL-Abfrage ausführen, um alle Datensätze in der Tabelle **AverageTime** abzurufen.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   Durch den Befehl `%%sql` gefolgt von `-o averagetime` wird sichergestellt, dass die Ausgabe der Abfrage lokal auf dem Jupyter-Server (in der Regel der Hauptknoten des Clusters) beibehalten wird. Die Ausgabe wird als [Pandas](https://pandas.pydata.org/) -Dataframe mit dem angegebenen Namen **averagetime**beibehalten.

   Folgendes sollte angezeigt werden:

   ![SQL-Abfrageausgabe](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-Abfrageausgabe")

   Weitere Informationen zur `%%sql`-Magic finden Sie unter [Mit %%sql-Magic unterstützte Parameter](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Sie können nun Matplotlib verwenden, eine Bibliothek zur Visualisierung von Daten, um eine Grafik zu erstellen. Da die Grafik aus dem lokal gespeicherten **averagetime**-Dataframe erstellt werden muss, muss der Codeausschnitt mit der `%%local`-Magic beginnen. Dadurch wird sichergestellt, dass der Code lokal auf dem Jupyter-Server ausgeführt wird.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Folgendes sollte angezeigt werden:

   ![Matplotlib-Ausgabe](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib-Ausgabe")
8. Nach Ausführen der Anwendung empfiehlt es sich, das Notebook herunterzufahren, um die Ressourcen freizugeben. Klicken Sie hierzu im Menü **Datei** des Notebooks auf die Option zum **Schließen und Anhalten**. Dadurch wird das Notebook heruntergefahren und geschlossen.

## <a name="seealso"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen
* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Apache Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden von HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
