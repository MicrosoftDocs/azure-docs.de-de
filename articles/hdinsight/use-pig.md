---
title: Verwenden von Apache Pig
titleSuffix: Azure HDInsight
description: Erfahren Sie, wie Sie Pig mit Apache Hadoop in HDInsight verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 9474a1845898e3fda0c810314f41f6f4deabc96b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538730"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Verwenden von Apache Pig mit Apache Hadoop in HDInsight

Erfahren Sie, wie [Apache Pig](https://pig.apache.org/) mit HDInsight verwendet wird.

Apache Pig ist eine Plattform zum Erstellen von Programmen für Apache Hadoop mithilfe einer prozeduralen Sprache namens *Pig Latin* . Pig ist eine Alternative zu Java für das Erstellen von *MapReduce* -Lösungen, und es ist in Azure HDInsight enthalten. In der folgenden Tabelle finden Sie verschiedene Möglichkeiten der Verwendung von Pig mit HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Gründe für die Verwendung von Apache Pig

Eine der Herausforderungen der Verarbeitung von Daten mithilfe von MapReduce in Hadoop ist die Implementierung der Verarbeitungslogik mit nur einer Karte und einer Reduce-Funktion. Für die komplexe Verarbeitung muss die diese häufig in mehrere MapReduce-Vorgänge unterteilt werden, die miteinander verkettet sind, um das gewünschte Ergebnis zu erzielen.

Pig ermöglicht Ihnen, die Verarbeitung als eine Reihe von Transformationen zu definieren, die die Daten durchlaufen, um die gewünschte Ausgabe zu erzeugen.

Mit der Pig Latin-Sprache können Sie den Datenfluss von Rohdaten durch eine oder mehrere Transformationen beschreiben, um die gewünschte Ausgabe zu erzeugen. Pig Latin-Programme folgen diesem allgemeinen Muster:

* **Laden** : Lesen der zu ändernden Daten aus dem Dateisystem

* **Transformieren** : Manipulieren der Daten

* **Ablage oder Speicherung** : Ausgeben der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

### <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

Pig Latin unterstützt auch benutzerdefinierte Funktionen (UDF), wodurch Sie externe Komponenten aufrufen können, die in Pig Latin schwer zu modellierende Logik implementieren.

Weitere Informationen zu Pig Latin finden Sie unter [Pig Latin-Referenzhandbuch 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) und [Pig Latin-Referenzhandbuch 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Beispieldaten

HDInsight enthält verschiedene Beispieldatasets, die in den Verzeichnissen `/example/data` und `/HdiSamples` gespeichert sind. Diese Verzeichnisse befinden sich im Standardspeicher für den Cluster. Im Pig-Beispiel in diesem Dokument wird die Datei *log4j* aus `/example/data/sample.log` verwendet.

Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens `[LOG LEVEL]` befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt, beispielsweise:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

Die Protokollebene im vorherigen Beispiel ist ERROR.

> [!NOTE]  
> Sie können auch eine log4j-Datei generieren, indem Sie das [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) -Protokollierungstool verwenden und die Datei dann in Ihren Blob hochladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight](hdinsight-upload-data.md) . Weitere Informationen zur Verwendung von Blobs in Azure Storage mit HDInsight finden Sie unter [Verwenden von Azure Blob Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Beispielauftrag

Mit dem folgenden Pig Latin-Auftrag wird die Datei `sample.log` aus dem Standardspeicher für den HDInsight-Cluster geladen. Er führt dann eine Reihe von Transformationen durch, die als Ergebnis anzeigen, wie oft jede Protokollebene in den Eingabedaten aufgetreten ist. Die Ergebnisse werden an STDOUT geschrieben.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

In der folgenden Abbildung ist zusammengefasst, wie sich die einzelnen Transformationen auf die Daten auswirken.

![Grafische Darstellung der Transformationen][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Pig Latin-Auftrag ausführen

HDInsight kann Pig Latin-Aufträge mithilfe verschiedener Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

## <a name="pig-and-sql-server-integration-services"></a>Pig und SQL Server Integration Services

Sie können mit SQL Server Integration Services (SSIS) einen Pig-Auftrag ausführen. Das Azure Feature Pack für SSIS bietet die folgenden Komponenten, die mit Pig-Aufträgen in HDInsight funktionieren.

* [Azure HDInsight Pig-Task][pigtask]

* [Verbindungs-Manager für Azure-Abonnements][connectionmanager]

[Hier][ssispack] erfahren Sie mehr über das Azure Feature Pack für SSIS.

## <a name="next-steps"></a><a id="nextsteps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Pig mit HDInsight verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Hochladen von Daten in HDInsight](hdinsight-upload-data.md)
* [Verwenden von Apache Hive mit HDInsight](./hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Sqoop mit HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif