---
title: Beispiele für Machine Learning mit Spark MLlib auf HDInsight – Azure
description: Erfahren Sie, wie Sie Spark MLlib verwenden, um eine Machine Learning-App zu erstellen, die ein Dataset analysiert, indem eine Klassifizierung durch logistische Regression vorgenommen wird.
keywords: Spark Machine Learning, Beispiel für Spark Machine Learning
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: bdc645bf8de95265158c3bb7ebf71952369e4ab2
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190907"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Verwenden Sie Apache Spark MLlib zum Erstellen einer Machine Learning-Anwendung und zur Analyse eines Datasets.

Lernen Sie, wie man mithilfe von Apache Spark [MLlib](https://spark.apache.org/mllib/) eine Machine-Learning-Anwendung erstellt, um eine einfache Vorhersageanalyse für ein offenes Dataset auszuführen. Aus den in Spark-integrierten Machine Learning-Bibliotheken verwendet dieses Beispiel die *Klassifizierung* durch logistische Regression. 

MLLib ist eine Spark-Kernbibliothek, die viele Hilfsprogramme enthält, die nützlich für Aufgaben aus dem Bereich des Machine Learning sind, darunter befinden sich auch Hilfsprogramme für folgende Aufgaben:

* Classification
* Regression
* Clustering
* Themenmodellierung
* Singulärwertzerlegung (Singular Value Decomposition, SVD) und Hauptkomponentenanalyse (Principal Component Analysis, PCA)
* Testen von Hypothesen und Berechnen von Beispielstatistiken

## <a name="understand-classification-and-logistic-regression"></a>Grundlegendes zu Klassifizierung und logistischer Regression
*Klassifizierung*, eine Aufgabe im Bereich des Machine Learning, ist der Prozess, bei dem Eingabedaten in Kategorien sortiert werden. Der Klassifizierungsalgorithmus hat die Aufgabe, herauszufinden, wie „Labels“ den von Ihnen bereitgestellten Eingabedaten zugeordnet werden. So kann ein Machine-Learning-Algorithmus beispielsweise Börsendaten als Eingabe akzeptieren und die Daten in zwei Kategorien einteilen: Aktien, die Sie verkaufen sollten, und solche, die Sie behalten sollten.

Logistische Regression ist der Algorithmus, den Sie für die Klassifizierung verwenden. Die API für die logistische Regression von Spark ist nützlich für eine *binäre Klassifizierung*oder für die Klassifizierung der Eingabedaten in einer von zwei Gruppen. Weitere Informationen zur logistischen Regression finden Sie in [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Zusammenfassend gesagt, erzeugt der Prozess der logistischen Regression eine *logistische Funktion* , die verwendet werden kann, um die Wahrscheinlichkeit vorherzusagen, dass ein Eingabevektor zu einer Gruppe oder der anderen gehört.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Beispiel für Vorhersageanalysen für Lebensmittelkontrolldaten
Sie verwenden in diesem Beispiel Spark, um einige Vorhersageanalysen für Lebensmittelkontrolldaten (**Food_Inspections1.csv**) auszuführen, die über das [Datenportal von Chicago](https://data.cityofchicago.org/) erhoben wurden. Dieses Dataset enthält Informationen zu Lebensmittelkontrollen, die in Chicago durchgeführt wurden, darunter Informationen zu jedem überprüften Betrieb, (ggf.) gefundene Verstöße und die Ergebnisse der Überprüfung. Die CSV-Datendatei ist bereits im Speicherkonto verfügbar, das dem Cluster in **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv** zugeordnet ist.

In den folgenden Schritten entwickeln Sie ein Modell, um zu ermitteln, wie Sie eine Lebensmittelkontrolle erfolgreich bestehen können bzw. wann Sie nicht bestehen.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Erstellen einer Apache Spark-MLlib-Machine Learning-App

1. Erstellen Sie ein Jupyter Notebook mit dem PySpark-Kernel. Informationen dazu finden Sie unter [Erstellen eines Jupyter Notebooks](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importieren Sie die Typen, die für diese Anwendung benötigt werden. Kopieren Sie den folgenden Code, fügen Sie ihn in eine leere Zelle ein, und drücken Sie **UMSCHALT+EINGABE**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Durch den PySpark-Kernel müssen Sie keine Kontexte explizit erstellen. Die Spark- und Hive-Kontexte werden automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen. 

## <a name="construct-the-input-dataframe"></a>Erstellen des Eingabedatenrahmens

Da die Rohdaten im CSV-Format vorliegen, können Sie den Spark-Kontext verwenden, um die Datei als unstrukturierten Text in den Arbeitsspeicher zu verschieben. Analysieren Sie dann mit der Python-CSV-Bibliothek jede Datenzeile.

1. Führen Sie die folgenden Zeilen aus, um ein Resilient Distributed Dataset (RDD) zu erstellen, indem Sie die Eingabedaten importieren und analysieren.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Führen Sie den folgenden Code aus, um eine Zeile aus dem RDD abzurufen, damit Sie sich das Datenschema ansehen können:

    ```PySpark
    inspections.take(1)
    ```

    Die Ausgabe ist:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Die Ausgabe gewährt Ihnen einen Einblick in das Schema der Eingabedatei. Sie enthält unter anderem den Namen und die Art jedes Betriebs, die Adresse, die Kontrolldaten und den Speicherort. 

3. Führen Sie den folgenden Code aus, um einen Datenrahmen (*df*) und eine temporäre Tabelle (*CountResults*) mit einigen Spalten zu erstellen, die für Vorhersageanalysen geeignet sind. `sqlContext` wird verwendet, um Transformationen strukturierter Daten auszuführen. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Die vier für uns interessanten Spalten im Datenrahmen sind **id**, **name**, **results** und **violations**.

4. Führen Sie den folgenden Code aus, um eine kleine Stichprobe der Daten abzurufen:

    ```PySpark
    df.show(5)
    ```

    Die Ausgabe ist:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Grundlegendes zu den Daten

Verschaffen Sie sich zunächst einen Überblick darüber, was in dem Dataset enthalten ist. 

1. Führen Sie den folgenden Code aus, um die unterschiedlichen Werte in der Spalte **results** anzuzeigen:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Die Ausgabe ist:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Führen Sie den folgenden Code aus, um die Verteilung der Ergebnisse zu visualisieren:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Durch den Befehl `%%sql` gefolgt von `-o countResultsdf` wird sichergestellt, dass die Ausgabe der Abfrage lokal auf dem Jupyter-Server (in der Regel der Hauptknoten des Clusters) beibehalten wird. Die Ausgabe wird als [Pandas](https://pandas.pydata.org/) -Dataframe mit dem angegebenen Namen **countResultsdf**beibehalten. Weitere Informationen zur `%%sql`-Magic sowie anderen für den PySpark-Kernel verfügbaren Magics finden Sie unter [Verfügbare Kernels für Jupyter Notebooks mit Apache Spark-Clustern unter HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Die Ausgabe ist:

    ![SQL-Abfrageausgabe](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-Abfrageausgabe")


3. Sie können auch [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib) verwenden, eine Bibliothek zur Visualisierung von Daten, um einen Plot zu erstellen. Da die Grafik aus dem lokal gespeicherten **countResultsdf**-Dataframe erstellt werden muss, muss der Codeausschnitt mit der `%%local`-Magic beginnen. Dadurch wird sichergestellt, dass der Code lokal auf dem Jupyter-Server ausgeführt wird.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Die Ausgabe ist:

    ![Ausgabe der Spark-Machine Learning-Anwendung im Kreisdiagramm mit fünf unterschiedlichen Messergebnissen](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Spark-Machine-Learning-Ergebnisausgaben")

    Um das Ergebnis einer Lebensmittelkontrolle vorherzusagen, müssen Sie ein Modell basierend auf den Verstößen entwickeln. Da die logistische Regression eine binäre Klassifizierungsmethode ist, können Sie die Ergebnisdaten in zwei Kategorien gruppieren: **Fail** und **Pass**:

   - Pass
       - Pass
       - Pass w/ Conditions
   - Fail
       - Fail
   - Verwerfen
       - Business not located
       - Out of Business

     Daten mit den anderen Ergebnissen („Business Not Located“ oder „Out of Business“) sind nicht sinnvoll, und sie machen auch nur einen sehr kleinen Prozentsatz der Ergebnisse aus.

4. Führen Sie den folgenden Code aus, um den bestehenden Datenrahmen (`df`) in einen neuen Datenrahmen zu konvertieren, wobei jede Kontrolle als Label-Violations-Paar dargestellt wird. In diesem Fall stellt das Label `0.0` ein Nichtbestehen dar, das Label `1.0` steht für das Bestehen der Kontrolle und das Label `-1.0` steht für andere Ergebnisse. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Führen Sie den folgenden Code aus, um eine Zeile von Daten mit Label anzuzeigen:

    ```PySpark
    labeledData.take(1)
    ```

    Die Ausgabe ist:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Erstellen eines logistischen Regressionsmodells anhand des Eingabedataframes

Die letzte Aufgabe besteht darin, die Daten mit Label in ein Format zu konvertieren, das mit der logistische Regression analysiert werden kann. Die Eingabe für einen logistischen Regressionsalgorithmus muss eine Reihe von *Label-Feature-Vektorpaaren* sein, wobei der „Funktionsvektor“ aus Zahlen besteht und den Eingabepunkt darstellt. Daher benötigen Sie eine Möglichkeit, die Spalte „violations“, die nur teilweise strukturiert ist und viele Freitextkommentare enthält, in ein Array reeller Zahlen zu konvertieren, die ein Computer problemlos interpretieren kann.

Ein Standardansatz beim maschinellen Lernen für die Verarbeitung natürlicher Sprache besteht darin, jedem klaren Wort einen „Index“ zuzuweisen und dann einen Vektor an den Algorithmus für maschinelles Lernen zu übergeben, sodass der Wert jedes Indexes die relative Häufigkeit dieses Worts in der Textzeichenfolge enthält.

MLlib bietet eine einfache Möglichkeit um diesen Vorgang auszuführen. Zunächst wird jeder Verstoßzeichenfolge ein Token zugewiesen, um die einzelnen Wörter in jeder Zeichenfolge abzurufen. Verwenden Sie dann eine `HashingTF`, um jeden Satz von Token in einen Funktionsvektor zu konvertieren, der dann an den logistischen Regressionsalgorithmus übergeben werden kann, um ein Modell zu erstellen. Sie müssen alle diese Schritte der Reihe nach über eine „Pipeline“ durchführen.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Auswerten des Modells mit einem anderen Dataset

Mithilfe des zuvor erstellten Modells können Sie basierend auf den beobachteten Verstößen *vorhersagen*, wie die Ergebnisse der neuen Kontrollen ausfallen werden. Sie haben dieses Modell mit dem Dataset **Food_Inspections1.csv** trainiert. Sie können nun ein zweites Dataset, **Food_Inspections2.csv**, verwenden, um die Stärke dieses Modells für die neuen Daten *auszuwerten*. Dieses zweite Dataset (**Food_Inspections2.csv**) befindet sich bereits im Standardspeichercontainer, der dem Cluster zugeordnet ist.

1. Führen Sie den folgenden Code aus, um einen neuen Datenrahmen, **predictionsDf**, zu erstellen, der die vom Modell generierte Vorhersage enthält. Der Codeausschnitt erstellt basierend auf dem Dataframe ebenfalls eine temporäre Tabelle namens **Predictions**.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Folgendes sollte angezeigt werden:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Betrachten Sie eine der Vorhersagen. Führen Sie diesen Codeausschnitt aus:

    ```PySpark
    predictionsDf.take(1)
    ```

   Sie sehen die Vorhersage für den ersten Eintrag im Testdataset.
1. Die `model.transform()`-Methode wendet dieselbe Transformation auf alle neuen Daten mit dem gleichen Schema an und erhält eine Vorhersage, wie die Daten klassifiziert werden können. Mit nur einigen einfachen statistischen Daten gewinnen Sie einen Eindruck davon, wie präzise die Vorhersagen waren:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Die Ausgabe sieht wie folgt aus:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Durch Verwenden der logistischen Regression mit Spark erhalten Sie ein präzises Modell der Beziehung zwischen Verstoßbeschreibungen auf Englisch und darüber, ob ein bestimmter Betrieb eine Lebensmittelkontrolle bestehen bzw. nicht bestehen würde.

## <a name="create-a-visual-representation-of-the-prediction"></a>Erstellen einer visuellen Darstellung der Vorhersage
Nun können Sie eine endgültige Visualisierung erstellen, um sich mit den Ergebnissen dieses Tests auseinanderzusetzen.

1. Beginnen Sie mit dem Extrahieren der unterschiedlichen Vorhersagen und Ergebnisse aus der zuvor erstellten temporären Tabelle **Predictions**. Die folgenden Abfragen teilt die Ausgabe in *true_positive*, *false_positive*, *true_negative*, und *false_negative* auf. In den folgenden Abfragen deaktivieren Sie die Visualisierung mithilfe von `-q` und speichern auch die Ausgabe (mithilfe von `-o`) als Datenrahmen, die dann mit der `%%local`-Magic verwendet werden können.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Abschließend verwenden Sie den folgenden Ausschnitt, um die Grafik mithilfe von **Matplotlib**zu generieren.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Die folgende Ausgabe wird angezeigt.

    ![Spark-Machine Learning-Anwendungsausgabe: Prozentsätze der nicht bestandenen Lebensmittelkontrollen im Kreisdiagramm](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Spark-Machine Learning-Ergebnisausgaben")

    In diesem Diagramm bezieht sich ein „positives“ Ergebnis auf eine nicht bestandene Lebensmittelkontrolle, wohingegen sich ein negatives Ergebnis auf eine bestandene Kontrolle bezieht.

## <a name="shut-down-the-notebook"></a>Herunterfahren des Notebooks
Nach dem Ausführen der Anwendung empfiehlt es sich, das Notebook herunterzufahren, um die Ressourcen freizugeben. Wählen Sie hierzu im Menü **Datei** des Notebooks die Option **Schließen und Anhalten** aus. Hierdurch wird das Notebook heruntergefahren und geschlossen.

## <a name="seealso"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen
* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
