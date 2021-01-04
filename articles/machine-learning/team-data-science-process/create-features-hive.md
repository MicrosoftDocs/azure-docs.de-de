---
title: 'Erstellen von Features für Daten in einem Azure HDInsight Hadoop-Cluster: Team Data Science-Prozess'
description: Beispiele für Hive-Abfragen, mit denen Funktionen in den Daten erstellt werden, die in einem Azure HDInsight Hadoop-Cluster gespeichert sind.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 30c0a02c2cbc11002f8e0bf0295dab91de5d0365
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020584"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Erstellen von Features für Daten in einem Hadoop-Cluster mit Hive-Abfragen
Dieses Dokument veranschaulicht, wie Features für Daten in einem Azure HDInsight Hadoop-Cluster mithilfe von Hive-Abfragen erstellt werden. Diese Hive-Abfragen verwenden eingebettete Hive-UDFs (User Defined Function, benutzerdefinierte Funktion), für die die Skripts bereitgestellt werden.

Die Vorgänge zum Erstellen von Features können speicherintensiv sein. In solchen Fällen fällt die Leistung von Hive-Abfragen noch stärker ins Gewicht. Sie kann durch die Optimierung bestimmter Parameter verbessert werden. Die Optimierung dieser Parameter wird im letzten Abschnitt erläutert.

Beispiele für die vorgestellten Abfragen gelten speziell für Szenarien mit den [NYC Taxi Trip-Daten](https://chriswhong.com/open-data/foil_nyc_taxi/) und stehen auch im [GitHub-Repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) zur Verfügung. Für diese Abfragen ist bereits ein Datenschema angegeben, sodass sie bereit für die Übermittlung zur Ausführung sind. Im letzten Abschnitt werden außerdem Parameter beschrieben, mit denen Benutzer die Leistung der Hive-Abfragen optimieren können.

Dieser Task ist ein Schritt im [Team Data Science-Prozess (TDSP)](./index.yml).

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../../storage/common/storage-account-create.md).
* Sie haben einen angepassten Hadoop-Cluster mit dem HDInsight-Dienst bereitgestellt.  Anweisungen hierzu finden Sie unter [Anpassen von Azure HDInsight Hadoop-Clustern für die erweiterte Analyse](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Sie haben die Daten in Hive-Tabellen auf Azure HDInsight Hadoop-Clustern hochgeladen. Wenn dies nicht der Fall ist, führen Sie die unter [Erstellen und Laden von Daten in Hive-Tabellen](move-hive-tables.md) beschriebenen Schritte zum Hochladen von Daten in Hive-Tabellen aus.
* Sie haben den Remotezugriff auf den Cluster aktiviert. Anweisungen finden Sie unter [Zugreifen auf den Hauptknoten von Hadoop-Clustern](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generieren von Funktionen
In diesem Abschnitt sind verschiedene Beispiele dafür enthalten, wie Funktionen mithilfe von Hive-Abfragen generiert werden können. Wenn Sie zusätzliche Funktionen generiert haben, können Sie diese als Spalten in der vorhandenen Tabelle hinzufügen oder eine neue Tabelle mit den zusätzlichen Funktionen und einem Primärschlüssel erstellen, die dann mit der ursprünglichen Tabelle zusammengeführt werden kann. Beispiele:

1. [Häufigkeitsbasierte Funktionsgenerierung](#hive-frequencyfeature)
2. [Risiken kategorischer Variablen in binären Klassifizierungen](#hive-riskfeature)
3. [Extrahieren von Funktionen aus "datetime"-Feldern](#hive-datefeatures)
4. [Extrahieren von Funktionen aus "Text"-Feldern](#hive-textfeatures)
5. [Berechnen der Entfernung zwischen GPS-Koordinaten](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Häufigkeitsbasierte Funktionsgenerierung
Oft ist es sinnvoll, die Häufigkeit der Ebenen einer kategorischen Variable oder die Häufigkeiten von bestimmten Ebenenkombinationen aus mehreren kategorischen Variablen zu berechnen. Mit dem folgenden Skript können Sie die Häufigkeiten berechnen:

```hiveql
select
    a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
from
(
    select
        <column_name1>,<column_name2>, count(*) as sub_count
    from <databasename>.<tablename> group by <column_name1>, <column_name2>
)a
order by frequency desc;
```


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Risiken kategorischer Variablen in binären Klassifizierungen
Bei binären Klassifizierungen müssen nicht numerische kategorische Variablen in numerische Funktionen umgewandelt werden, wenn die verwendeten Modelle nur numerische Funktionen akzeptieren. Bei dieser Umwandlung werden die nicht numerischen Ebenen durch numerische Risiken ersetzt. In diesem Abschnitt werden einige generische Hive-Abfragen zur Berechnung der Risikowerte (logarithmische Wahrscheinlichkeiten) einer kategorischen Variable gezeigt.

```hiveql
set smooth_param1=1;
set smooth_param2=20;
select
    <column_name1>,<column_name2>,
    ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
from
    (
    select
        <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
    from
        (
        select
            <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
        from <databasename>.<tablename>
        )a
    group by <column_name1>, <column_name2>
    )b
```

In diesem Beispiel werden die Variablen `smooth_param1` und `smooth_param2` zum Glätten der aus den Daten berechneten Risikowerte festgelegt. Die Risiken liegen zwischen "-Inf" und "Inf". Ein Risiko > 0 steht für die Wahrscheinlichkeit, dass das Ziel 1 größer als 0,5 ist.

Nach dem Berechnen der Risikotabelle können Sie einer Tabelle Risikowerte zuweisen, indem Sie sie mit der Risikotabelle zusammenführen. Die Hive-Abfrage für das Zusammenführen wurde im vorherigen Abschnitt beschrieben.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Extrahieren von Funktionen aus datetime-Feldern
Hive bietet eine Reihe von UDFs für die Verarbeitung von "datetime"-Feldern. In Hive lautet das Standardformat für "datetime" "JJJJ-MM-TT 00:00:00" (wie z. B. in "1970-01-01 12:21:32"). In diesem Abschnitt werden Beispiele für das Extrahieren des Tags eines Monats und des Monats aus einem datetime-Feld gezeigt. Außerdem gibt es Beispiele zum Konvertieren einer datetime-Zeichenfolge in einem anderen Format als dem Standardformat in eine datetime-Zeichenfolge im Standardformat.

```hiveql
select day(<datetime field>), month(<datetime field>)
from <databasename>.<tablename>;
```

Bei dieser Hive-Abfrage wird vorausgesetzt, dass das *\<datetime field>* im Standardformat für „datetime“ vorliegt.

Liegt ein "datetime"-Feld nicht im Standardformat vor, muss zunächst das "datetime"-Feld in einen Unix-Zeitstempel konvertierten werden, der dann in eine "datetime"-Zeichenfolge im Standardformat konvertiert wird. Wenn "datetime" das Standardformat aufweist, können Sie die eingebetteten "datetime"-UDFs anwenden, um Funktionen zu extrahieren.

```hiveql
select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
from <databasename>.<tablename>;
```

Wenn in dieser Abfrage das *\<datetime field>* ein Muster wie *03/26/2015 12:04:39* aufweist, sollte das *\<pattern of the datetime field>'* wie folgt aussehen: `'MM/dd/yyyy HH:mm:ss'`. Zum Testen können Sie folgenden Code ausführen:

```hiveql
select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
from hivesampletable limit 1;
```

Die Tabelle *hivesampletable* in dieser Abfrage enthält in der Voreinstellung alle Azure HDInsight Hadoop-Cluster, wenn die Cluster bereitgestellt wurden.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Extrahieren von Funktionen aus Textfeldern
Wenn die Hive-Tabelle ein Textfeld enthält, das eine Zeichenfolge von durch Leerzeichen voneinander getrennten Wörtern ist, können Sie mit der folgenden Abfrage die Länge der Zeichenfolge und die Anzahl der Wörter in der Zeichenfolge extrahieren.

```hiveql
select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
from <databasename>.<tablename>;
```

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Berechnen der Entfernung zwischen GPS-Koordinaten
Die in diesem Abschnitt angegebene Abfrage kann direkt auf die "NYC Taxi Trip"-Daten angewendet werden. Diese Abfrage soll veranschaulichen, wie Sie eine eingebettete mathematische Funktion in Hive zum Generieren von Funktionen verwenden.

Die in dieser Abfrage verwendeten Felder sind GPS-Koordinaten von Start- und Zielorten mit den Bezeichnungen *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* und *dropoff\_latitude*. Die Abfragen zur Berechnung der direkten Entfernung zwischen den Start- und Zielkoordinaten sind:

```hiveql
set R=3959;
set pi=radians(180);
select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
    *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
    *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
    +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
    pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxi.trip
where pickup_longitude between -90 and 0
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and 0
and dropoff_latitude between 30 and 90
limit 10;
```

Die mathematischen Gleichungen zur Berechnung der Entfernung zwischen zwei GPS-Koordinaten finden Sie auf der Website <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> von Peter Lapisu. In seinem JavaScript-Code ist die `toRad()`-Funktion nur *lat_or_lon* pi/180 und rechnet Grad in Radianten um. Hierbei ist *lat_or_lon* der Längen- oder Breitengrad. Da Hive keine `atan2`-Funktion bereitstellt, jedoch die `atan`-Funktion, wird die `atan2`-Funktion in der oben angegebenen Hive-Abfrage anhand der in <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a> angegebenen Definition durch die `atan`-Funktion implementiert.

![Arbeitsbereich erstellen](./media/create-features-hive/atan2new.png)

Eine vollständige Liste der eingebetteten Hive-UDFs finden Sie im Abschnitt **Built-in Functions** im <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive-Wiki</a>.  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a> Weiterführende Themen: Optimieren von Hive-Parametern zur Verbesserung der Abfragegeschwindigkeit
Die Standardeinstellungen für die Parameter von Hive-Clustern eignen sich möglicherweise nicht für die Hive-Abfragen und die von den Abfragen verarbeiteten Daten. In diesem Abschnitt werden einige Parameter beschrieben, mit denen Sie die Leistung der Hive-Abfragen optimieren können. Sie müssen die Abfragen zur Parameteroptimierung vor den Abfragen der Verarbeitungsdaten einfügen.

1. **Java-Heapspeicher**: Für Abfragen, bei denen große Datasets zusammengeführt oder lange Datensätze verarbeitet werden, besteht ein typischer Fehler darin, dass **nicht genügend Heapspeicher verfügbar ist**. Dieser Fehler kann durch Festlegen der Parameter *mapreduce.map.java.opts* und *mapreduce.task.io.sort.mb* auf die gewünschten Werte vermieden werden. Beispiel:
   
    ```hiveql
    set mapreduce.map.java.opts=-Xmx4096m;
    set mapreduce.task.io.sort.mb=-Xmx1024m;
    ```

    Dieser Parameter ordnet dem Java-Heapspeicher 4 GB Arbeitsspeicher zu. Außerdem gestaltet er die Sortierung effizienter, da er ihr mehr Arbeitsspeicher zuweist. Sie sollten etwas mit diesen Zuordnungen experimentieren, wenn bei einem Auftrag Fehler im Zusammenhang mit dem Heapspeicher auftreten.

1. **DFS-Blockgröße**: Mit diesem Parameter wird die kleinste Dateneinheit festgelegt, die das Dateisystem speichert. Wenn die DFS-Blockgröße beispielsweise 128 MB beträgt, werden alle Daten mit einer Größe von höchstens 128 MB in einem einzelnen Block gespeichert. Daten über 128 MB werden zusätzliche Blöcke zugewiesen. 
2. Die Auswahl einer geringen Blockgröße führt zu starkem zusätzlichem Verbrauch in Hadoop, da der Namensknoten viel mehr Anforderungen verarbeiten muss, um den entsprechenden Block für die Datei zu finden. Eine empfohlene Einstellung bei der Arbeit mit Daten im Gigabytebereich (oder größer) ist:

    ```hiveql
    set dfs.block.size=128m;
    ```

2. **Optimieren von Join-Vorgängen in Hive**: Join-Vorgänge im Map/Reduce-Framework erfolgen in der Regel in der Reduce-Phase. In einigen Fällen können jedoch enorme Vorteile erzielt werden, indem die Join-Vorgänge in die Map-Phase verlegt werden (so genannte „mapjoins“). Legen Sie die folgende Option fest:
   
    ```hiveql
    set hive.auto.convert.join=true;
    ```

3. **Angeben der Anzahl von Zuordnungen für Hive**: Hadoop erlaubt das Festlegen der Anzahl von Reducern. Die Anzahl der Zuordnungen kann aber in der Regel nicht vom Benutzer festgelegt werden. Mit einem Trick erhalten Sie ein gewisses Maß an Kontrolle über diese Anzahl: Wählen Sie die Hadoop-Variablen *mapred.min.split.size* und *mapred.max.split.size* aus, da die Größe der einzelnen Zuordnungsaufgaben durch Folgendes bestimmt wird:
   
    ```hiveql
    num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
    ```
   
    Die typischen Standardwerte sind:
    
   - bei *mapred.min.split.size* 0 (null),
   - bei *mapred.max.split.size* **Long.MAX** und bei 
   - *dfs.block.size* 64 MB.

     Bei entsprechender Datengröße können Sie durch Festlegen dieser Parameter die Anzahl der verwendeten Mapper optimieren.

4. Hier finden Sie einige andere **weiterführende Optionen** zur Optimierung der Hive-Leistung. Diese Optionen ermöglichen das Festlegen des zugeordneten Speichers für „map“- und „reduce“-Aufgaben, die beim Optimieren der Leistung nützlich sein können. Beachten Sie, dass *mapreduce.reduce.memory.mb* nicht größer sein darf als die Größe des physischen Speichers der einzelnen Workerknoten im Hadoop-Cluster.
   
    ```hiveql
    set mapreduce.map.memory.mb = 2048;
    set mapreduce.reduce.memory.mb=6144;
    set mapreduce.reduce.java.opts=-Xmx8192m;
    set mapred.reduce.tasks=128;
    set mapred.tasktracker.reduce.tasks.maximum=128;
    ```