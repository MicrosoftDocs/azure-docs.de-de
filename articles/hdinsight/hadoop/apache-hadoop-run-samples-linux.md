---
title: 'Ausführen der Apache Hadoop MapReduce-Beispiele in HDInsight: Azure'
description: Lernen Sie die ersten Schritte mit MapReduce-Beispielen in JAR-Dateien in HDInsight kennen. Verwenden Sie SSH, um eine Verbindung mit dem Cluster herzustellen, und verwenden Sie dann den Hadoop-Befehl, um Beispielaufträge auszuführen.
keywords: Hadoop-Beispiel-JAR, Hadoop Beispiele JAR, Hadoop MapReduce-Beispiele, MapReduce-Beispiele
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: hrasheed
ms.openlocfilehash: f0251e3926c569b45ebebcd18b98df5af4564443
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706669"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Ausführen von MapReduce-Beispielen in HDInsight

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Hier erfahren Sie, wie Sie die MapReduce-Beispiele ausführen, die in Apache Hadoop in HDInsight enthalten sind.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Die MapReduce-Beispiele

**Speicherort:** Die Beispiele befinden sich im HDInsight-Cluster unter `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Inhalt:** Die folgenden Beispiele sind in diesem Archiv enthalten:

* `aggregatewordcount`: Ein aggregatbasiertes MapReduce-Programm, das die Wörter in den Eingabedateien zählt
* `aggregatewordhist`: Ein aggregatbasiertes MapReduce-Programm, das das Histogramm der Wörter in den Eingabedateien berechnet
* `bbp`: Ein MapReduce-Programm, das Bailey-Borwein-Plouffe verwendet, um genaue Stellen von Pi zu berechnen
* `dbcount`: Ein Beispielauftrag, der die in einer Datenbank gespeicherten Seitenabrufe zählt
* `distbbp`: Ein MapReduce-Programm, das eine BBP-Formel verwendet, um genaue Teile von Pi zu berechnen
* `grep`: Ein MapReduce-Programm, das die Übereinstimmungen mit einem RegEx in der Eingabe zählt
* `join`: Ein Auftrag, der eine Verknüpfung von sortierten, gleichmäßig partitionierten DataSets durchführt
* `multifilewc`: Ein Auftrag, der Wörter aus verschiedenen Dateien zählt
* `pentomino`: Ein MapReduce-Programm für die Ablage von Platten, um Lösungen für Probleme mit Pentomino zu finden
* `pi`: Ein MapReduce-Programm, das Pi anhand einer Monte-Carlo-ähnlichen Methode schätzt
* `randomtextwriter`: Ein MapReduce-Programm, das 10 GB zufälliger Textdaten pro Knoten schreibt
* `randomwriter`: Ein MapReduce-Programm, das 10 GB zufälliger Daten pro Knoten schreibt
* `secondarysort`: Ein Beispiel, das eine sekundäre Reduce-Phase-Sortierung definiert
* `sort`: Ein MapReduce-Programm, das die vom zufälligen Writer geschriebenen Daten sortiert
* `sudoku`: Ein Programm zum Lösen von Sudokus
* `teragen`: Generiert Daten für „terasort“.
* `terasort`: Führt „terasort“ aus.
* `teravalidate`: Überprüft die Ergebnisse von „terasort“.
* `wordcount`: Ein MapReduce-Programm, das die Wörter in den Eingabedateien zählt
* `wordmean`: Ein MapReduce-Programm, das die durchschnittliche Länge der Wörter in den Eingabedateien zählt
* `wordmedian`: Ein MapReduce-Programm, das die mittlere Länge der Wörter in den Eingabedateien zählt
* `wordstandarddeviation`: Ein MapReduce-Programm, das die Standardabweichung der Länge der Wörter in den Eingabedateien zählt

**Quellcode:** Quellcode für diese Beispiele befindet sich im HDInsight-Cluster unter `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

## <a name="run-the-wordcount-example"></a>Ausführen des wordcount-Beispiels

1. Stellen Sie per SSH eine Verbindung mit HDInsight her. Ersetzen Sie `CLUSTER` durch den Namen Ihres Clusters, und geben Sie dann den folgenden Befehl ein:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Verwenden Sie an der Eingabeaufforderung `username@#######:~$` den folgenden Befehl zum Auflisten der Beispiele:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Dieser Befehl generiert die Liste der Beispiele aus dem vorherigen Abschnitt dieses Dokuments.

3. Verwenden Sie den folgenden Befehl, um Hilfe zu einem bestimmten Beispiel zu erhalten. In diesem Fall das Beispiel **wordcount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Sie erhalten folgende Meldung:

        Usage: wordcount <in> [<in>...] <out>

    Diese Meldung bedeutet, dass Sie mehrere Eingabepfade für die Quelldokumente angeben können. Im endgültigen Pfad wird die Ausgabe (Anzahl der Wörter in den Quelldokumenten) gespeichert.

4. Verwenden Sie den folgenden Code, um alle Wörter in den Notizbüchern von Leonardo da Vinci zu zählen, die als Beispieldaten in Ihrem Cluster bereitgestellt sind:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Eingabe für diesen Auftrag wird aus `/example/data/gutenberg/davinci.txt` gelesen. Ausgabe für dieses Beispiel wird in `/example/data/davinciwordcount` gespeichert. Beide Pfade befinden sich in Standardspeicher für den Cluster, nicht im lokalen Dateisystem.

   > [!NOTE]  
   > Wie in der Hilfe für das wordcount-Beispiel erwähnt, können Sie auch mehrere Eingabedateien angeben. Beispiel: Mit `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` werden die Wörter in "davinci.txt" und "ulysses.txt" gezählt.

5. Sobald der Auftrag abgeschlossen ist, verwenden Sie den folgenden Befehl zum Anzeigen der Ausgabe.

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Dieser Befehl verkettet alle Ausgabedateien, die vom Auftrag erzeugt wurden. Er zeigt die Ausgabe an der Konsole an. Die Ausgabe sieht in etwa wie folgender Text aus:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Jede Zeile steht für ein Wort und gibt an, wie oft es in den Eingabedaten aufgetreten ist.

## <a name="the-sudoku-example"></a>Das Sudoku-Beispiel

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) ist ein Logikrätsel, das sich aus neun 3 x 3-Quadraten zusammensetzt. Einige Zellen der Quadrate erhalten Zahlen, während andere leer sind, und das Ziel besteht darin, die leeren Zellen richtig zu füllen. Unter dem obigen Link finden Sie weitere Informationen zu dem Rätsel, aber der Zweck dieses Beispiels ist, die leeren Zellen richtig zu füllen. Unsere Eingabe sollte also eine Datei im folgenden Format sein:

* Neun Zeilen mit neun Spalten
* Jede Spalte kann eine Zahl oder `?` (was eine leere Zelle angibt) enthalten
* Zellen werden durch ein Leerzeichen voneinander getrennt

Sudoku-Rätsel müssen auf eine bestimmte Weise erstellt werden, da eine Zahl in einer Spalte oder Zeile nur einmal vorkommen darf. Es gibt ein Beispiel im HDInsight-Cluster, das entsprechend erstellt wurde. Es befindet sich unter `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` und enthält den folgenden Text:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Führen Sie dieses Beispielproblem anhand des Sudoku-Beispiels mit dem folgenden Befehl aus:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Die Ergebnisse sollten in etwa folgendem Text entsprechen:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Beispiel für Pi (π)

Das Beispiel verwendet eine statistische (Quasi-Monte-Carlo-) Methode, um den Wert von Pi zu schätzen. Punkte werden nach dem Zufallsprinzip in einem Einheitenquadrat platziert. Das Quadrat enthält auch einen Kreis. Die Wahrscheinlichkeit, dass die Punkte innerhalb des Kreises liegen, entspricht der Fläche des Kreises, Pi/4. Der Wert von Pi kann aus dem Wert 4R geschätzt werden. Dabei ist R das Verhältnis zwischen der Anzahl der Punkte innerhalb des Kreises zur Gesamtanzahl der Punkte innerhalb des Quadrats. Je größer die Anzahl der Punkte, desto genauer die Schätzung.

Verwenden Sie den folgenden Befehl zum Ausführen dieses Beispiels. Dieser Befehl verwendet 16 Karten mit 10.000.000 Stichproben, um den Wert von Pi zu schätzen:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

Der zurückgegebene Wert sollte etwa **3.14159155000000000000** lauten. Als Referenz: Die ersten 10 Dezimalstellen von Pi sind 3,1415926535.

## <a name="10-gb-graysort-example"></a>10-GB-GraySort-Beispiel

GraySort ist ein Sortierungs-Benchmark. Die Metrik ist die Sortiergeschwindigkeit (TB/Minute), die beim Sortieren großer Datenmengen erreicht wird (normalerweise mindestens 100 TB).

Dieses Beispiel verwendet bescheidene 10GB an Daten, um eine zügige Ausführung zu ermöglichen. Es verwendet die MapReduce-Anwendungen von Owen O'Malley und Arun Murthy. Diese Anwendungen haben im Jahr 2009 den jährlichen allgemeinen Terabyte-Sortierbenchmark („Daytona“) mit einem Durchsatz von 0,578 TB/Min (100 TB in 173 Minuten) gewonnen. Weitere Informationen zu diesem und anderen Sortier-Benchmarks finden Sie unter [Sort Benchmark](https://sortbenchmark.org/) (Sortier-Benchmark).

Dieses Beispiel verwendet drei Sätze von MapReduce-Programmen:

* **TeraGen:** Ein MapReduce-Programm, das Zeilen mit zu sortierenden Daten generiert

* **TeraSort:** „TeraSort“ prüft die Eingangsdaten und verwendet MapReduce, um die Daten in eine Gesamtreihenfolge zu bringen.

    TeraSort ist eine MapReduce-Standardsortierung, die zusätzlich einen eigenen Partitionierer bietet. Der Partitionierer verwendet eine sortierte Liste mit N-1 Stichprobenschlüsseln, um den Schlüsselbereich für die einzelnen Reduce-Vorgänge zu definieren. Speziell werden alle Schlüssel mit Probe[i-1] <= Schlüssel < Probe[i] an Reduce-Vorgang i gesendet. Durch den Partitionierer wird sichergestellt, dass die Ausgaben der Reduce-Vorgänge i immer kleiner sind als die Ausgabe des Reduce-Vorgangs i + 1.

* **TeraValidate:** Ein MapReduce-Programm, das die globale Sortierung der Ausgabe prüft

    TeraValidate erstellt eine Map pro Datei im Ausgabeverzeichnis, und jede Map stellt sicher, dass jeder Schlüssel kleiner als der vorherige oder gleich dem vorherigen Schlüssel ist. Die Zuordnungsfunktion generiert Datensätze des ersten und letzten Schlüssels jeder Datei. Die Reduce-Funktion stellt sicher, dass der erste Schlüssel der Datei i größer ist als der letzte Schlüssel der Datei i-1. Probleme werden als Ausgabe der Reduce-Phase zusammen mit den Schlüsseln gemeldet, die nicht in der richtigen Reihenfolge sind.

Verwenden Sie die folgenden Schritte, um Daten zu generieren und die Ausgabe zu sortieren und zu überprüfen:

1. Generieren von 10GB Daten, die im Standardspeicher des HDInsight-Clusters unter `/example/data/10GB-sort-input` gespeichert werden:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` gibt für Hadoop an, wie viele Aufgaben für diesen Auftrag verwendet werden sollen. Die letzten beiden Parameter weisen den Auftrag an, 10 GB Daten zu erstellen und unter `/example/data/10GB-sort-input` zu speichern.

2. Führen Sie den folgenden Befehl aus, um die Daten zu sortieren:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` gibt für Hadoop an, wie viele Reduce-Aufgaben für den Auftrag verwendet werden sollen. Die letzten beiden Parameter sind nur die Eingabe- und Ausgabespeicherorte für Daten.

3. Verwenden Sie folgenden Code zum Überprüfen der Daten, die durch die Sortierung generiert wurden:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie die Beispiele ausgeführt werden, die in Linux-basierten HDInsight-Clustern enthalten sind. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Themen:

* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](hdinsight-use-pig.md)
* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)