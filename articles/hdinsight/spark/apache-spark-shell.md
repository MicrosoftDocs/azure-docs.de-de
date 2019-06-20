---
title: Verwenden einer interaktiven Spark-Shell in Azure HDInsight
description: Eine interaktive Spark-Shell bietet einen „Lesen-Ausführen-Anzeigen“-Prozesses, um Spark-Befehle nacheinander auszuführen und die Ergebnisse anzuzeigen.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 9044ed3ad9cf9ffa2f54d130bb50b37df121b86f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696799"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Ausführen von Apache Spark aus der Spark-Shell

Eine interaktive [Apache Spark](https://spark.apache.org/)-Shell bietet eine REPL-Umgebung (Read-Execute-Print-Loop, „Lesen-Ausführen-Anzeigen“-Schleife), um Spark-Befehle nacheinander auszuführen und die Ergebnisse anzuzeigen. Dieser Vorgang ist nützlich für Entwicklung und Debuggen. Spark bietet für jede unterstützte Sprache eine Shell: Scala, Python und R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Zugreifen auf eine Apache Spark-Shell mit SSH

Greifen Sie in HDInsight durch Herstellen einer Verbindung mit dem primären Hauptknoten des Clusters über SSH auf eine Apache Spark-Shell zu:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

Sie können den vollständigen SSH-Befehl für Ihren Cluster aus dem Azure-Portal abrufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Navigieren Sie zu dem Bereich für Ihren HDInsight-Spark-Cluster.
3. Wählen Sie „Secure Shell (SSH)“.

    ![HDInsight-Bereich im Azure-Portal](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Kopieren Sie den angezeigten SSH-Befehl, und führen Sie ihn auf Ihrem Terminal aus.

    ![HDInsight-SSH-Bereich im Azure-Portal](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Details zum Verwenden von SSH zum Herstellen einer Verbindung mit HDInsight finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Ausführen einer Apache Spark-Shell

Spark bietet Shells für Scala (spark-shell), Python (pyspark) und R (sparkR). Geben Sie in der SSH-Sitzung auf dem Hauptknoten des HDInsight-Clusters einen der folgenden Befehle ein:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Sie können jetzt Spark-Befehle in der entsprechenden Sprache eingeben.

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession- und SparkContext-Instanzen

Beim Ausführen der Spark-Shell werden standardmäßig automatisch Instanzen von SparkSession und SparkContext für Sie erzeugt.

Um auf die SparkSession-Instanz zuzugreifen, geben Sie `spark` ein. Um auf die SparkContext-Instanz zuzugreifen, geben Sie `sc` ein.

## <a name="important-shell-parameters"></a>Wichtige Shellparameter

Der Spark-Shell-Befehl (`spark-shell`, `pyspark` oder `sparkR`) unterstützt viele Befehlszeilenparameter. Um eine vollständige Liste der Parameter anzuzeigen, starten Sie die Spark-Shell mit dem Schalter `--help`. Beachten Sie, dass einige dieser Parameter möglicherweise nur für `spark-submit` gelten, den die Spark-Shell umhüllt.

| Schalter | description | Beispiel |
| --- | --- | --- |
| --master MASTER-URL | Gibt die Master-URL an. In HDInsight ist dieser Wert immer `yarn`. | `--master yarn`|
| --jars JAR-LISTE | Durch Trennzeichen getrennte Liste der lokalen JAR-Dateien zum Einschließen der Treiber- und Executorklassenpfade. In HDInsight besteht diese Liste aus Pfaden zum Standarddateisystem in Azure Storage oder Data Lake Storage. | `--jars /path/to/examples.jar` |
| --packages MAVEN-KOORDINATEN | Durch Trennzeichen getrennte Liste der Maven-Koordinaten von JAR-Dateien zum Einschließen der Treiber- und Executorklassenpfade. Durchsucht zuerst das lokale, anschließend das zentrale Maven-Repository und dann etwaige zusätzliche Remote-Repositorys, die mit `--repositories` angegeben werden. Das Format für die Koordinaten ist *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LISTE | Nur für Python eine durch Trennzeichen getrennte Liste der im PYTHONPATH zu platzierenden ZIP-, EGG- oder PY-Dateien. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Einführung in Apache Spark in Azure HDInsight](apache-spark-overview.md).
- Informationen zum Arbeiten mit Spark-Clustern und SparkSQL finden Sie unter [Erstellen eines Apache Spark-Clusters in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
- Informationen zum Schreiben von Anwendungen, die Streamingdaten mit Spark verarbeiten, finden Sie unter [Übersicht über strukturierte Apache Spark-Streamings](apache-spark-streaming-overview.md).
