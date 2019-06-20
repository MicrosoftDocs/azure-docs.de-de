---
title: MapReduce mit Apache Hadoop in HDInsight
description: Erfahren Sie, wie MapReduce-Aufträge in Apache Hadoop in HDInsight-Clustern ausgeführt werden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9da6b6ba3ab697887e55f9077b44cf6fa100a981
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707965"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Verwenden von MapReduce mit Apache Hadoop in HDInsight

Erfahren Sie, wie MapReduce-Aufträge in HDInsight-Clustern ausgeführt werden. 

## <a id="whatis"></a>Was ist MapReduce?

Apache Hadoop MapReduce ist ein Softwareframework zum Schreiben von Aufträgen, die sehr große Datenmengen verarbeiten. Eingabedaten werden in unabhängigen Blöcke aufgeteilt. Jeder Block wird in den Knoten im Cluster parallel verarbeitet. Ein MapReduce-Auftrag besteht aus zwei Funktionen:

* **Mapper:** Verarbeitet Eingabedaten, analysiert sie (in der Regel mit Filter- und Sortiervorgängen) und gibt Tupel (Schlüssel-Wert-Paare) aus

* **Reducer:** Verarbeitet Tupel, die vom Mapper ausgegeben wurden, und führt einen Zusammenfassungsvorgang aus, der kleinere, kombinierte Ergebnisse aus den Mapper-Daten erstellt

Ein Beispiel für einen grundlegenden MapReduce-Auftrag zum Zählen von Wörtern wird im folgenden Diagramm veranschaulicht:

![HDI.Wortzähldiagramm][image-hdi-wordcountdiagram]

Dieses Auftrag gibt die Anzahl der Vorkommen jedes Worts im Text aus.

* Der Mapper verwendet die einzelnen Zeilen des Eingabetexts als Eingabe und unterteilt diese in Wörter. Er gibt bei jedem Vorkommen eines Worts ein Schlüssel-Wert-Paar des Worts gefolgt von 1 aus. Die Ausgabe wird sortiert, bevor sie an den Reducer gesendet wird.
* Der Reducer addiert die einzelnen Zählwerte jedes Worts und gibt ein einziges Schlüssel-Wert-Paar aus, das aus dem Wort gefolgt von der Summe seiner Vorkommen besteht.

MapReduce kann in verschiedenen Sprachen implementiert werden. Java ist die am häufigsten verwendete Implementierung und wird in diesem Dokument zu Demonstrationszwecken verwendet.

## <a name="development-languages"></a>Entwicklungssprachen

Sprachen oder Frameworks auf der Grundlage von Java und der Java Virtual Machine können direkt als MapReduce-Auftrag ausgeführt werden. Das in diesem Artikel verwendete Beispiel ist eine Java-MapReduce-Anwendung. Nicht Java-basierte Sprachen (etwa C# oder Python) bzw. eigenständige ausführbare Dateien müssen **Hadoop-Datenströme** verwenden.

Hadoop-Datenströme kommunizieren über STDIN und STDOUT mit Mapper und Reducer. Mapper und Reducer lesen zeilenweise Daten aus STDIN, und schreiben die Ausgabe in STDOUT. Jede vom Mapper und vom Reducer gelesene oder ausgegebene Zeile muss als Schlüssel-Wert-Paar mit Tabstopptrennzeichen formatiert sein:

    [key]/t[value]

Weitere Informationen finden Sie unter [Hadoop Streaming](https://hadoop.apache.org/docs/r1.2.1/streaming.html)(in englischer Sprache).

Beispiele für die Verwendung von Hadoop-Datenströmen mit HDInsight finden Sie in dem folgenden Artikel:

* [Entwickeln von C#-MapReduce-Aufträgen](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a id="data"></a>Beispieldaten

HDInsight enthält verschiedene Beispieldatasets, die in den Verzeichnissen `/example/data` und `/HdiSamples` gespeichert sind. Diese Verzeichnisse befinden sich im Standardspeicher für den Cluster. In diesem Artikel wird die Datei `/example/data/gutenberg/davinci.txt` verwendet. Diese Datei enthält die Notizbücher von Leonardo da Vinci.

## <a id="job"></a>MapReduce-Beispiel

Eine MapReduce-Beispielanwendung zum Zählen von Wörtern ist in Ihrem HDInsight-Cluster enthalten. Dieses Beispiel befindet sich unter `/example/jars/hadoop-mapreduce-examples.jar` im Standardspeicher für Ihren Cluster.

Der folgende Java-Code ist die Quelle der in der Datei `hadoop-mapreduce-examples.jar` enthaltenen MapReduce-Anwendung:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Anweisungen zum Schreiben eigener MapReduce-Anwendungen finden Sie im folgenden Artikel:

* [Entwickeln von Java MapReduce-Anwendungen für HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Ausführen von MapReduce

HDInsight kann HiveQL-Aufträge mithilfe verschiedener Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

| **Option** | **Zweck** | ...mit diesem **Clusterbetriebssystem** | ...von diesem **Clientbetriebssystem** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Verwenden des Hadoop-Befehls über **SSH** |Linux |Linux, Unix, Mac OS X oder Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Remoteübermittlung des Auftrags mit **REST** |Linux oder Windows |Linux, Unix, Mac OS X oder Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Remoteübermittlung des Auftrags mit **Windows PowerShell** |Linux oder Windows |Windows |

## <a id="nextsteps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Daten in HDInsight finden Sie in den folgenden Artikeln:

* [Entwickeln von Java MapReduce-Programmen für HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Verwenden von Apache Hive mit HDInsight][hdinsight-use-hive]

* [Verwenden von Apache Pig mit HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
