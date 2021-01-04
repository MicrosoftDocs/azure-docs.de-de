---
title: Problembehandlung bei Hive mit Azure HDInsight
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Arbeiten mit Apache Hive und Azure HDInsight.
keywords: Azure HDInsight, HIVE, FAQ, Problembehandlungshandbuch, häufig gestellte Fragen
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f1a26e3323e4d1db2e9b2bda9afaa2756307749b
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288955"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Behandeln von Problemen mit Apache Hive unter Verwendung von Azure HDInsight

Hier werden die Antworten auf die wichtigsten Fragen bei der Arbeit mit Apache Hive-Nutzlasten in Apache Ambari behandelt.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Wie exportiere ich einen Hive-Metastore und importiere ihn in einen anderen Cluster?

### <a name="resolution-steps"></a>Lösungsschritte

1. Stellen Sie mit einem Secure Shell-Client (SSH) eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen finden Sie unter [Zusätzliche Lektüre](#additional-reading-end).

2. Führen Sie den folgenden Befehl auf dem HDInsight-Cluster aus, aus dem Sie den Metastore exportieren möchten:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Mit diesem Befehl wird die Datei „allatables.sql“ generiert.

3. Kopieren Sie die Datei „alltables.sql“ in den neuen HDInsight-Cluster, und führen Sie den folgenden Befehl aus:

    ```apache
    hive -f alltables.sql
    ```

Für den Code in den Schritten zur Behebung wird davon ausgegangen, dass die Datenpfade im neuen Cluster identisch mit denen im alten Cluster sind. Wenn sich die Datenpfade unterscheiden, können die Sie generierte Datei `alltables.sql` manuell so ändern, dass etwaige Änderungen widergespiegelt werden.

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Herstellen einer Verbindung mit einem HDInsight-Cluster mit SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Wie finde ich Hive-Protokolle in einem Cluster?

### <a name="resolution-steps"></a>Lösungsschritte

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen finden Sie unter **Zusätzliche Lektüre**.

2. Führen Sie den folgenden Befehl aus, um Hive-Clientprotokolle anzuzeigen:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Führen Sie den folgenden Befehl aus, um Hive-Metastoreprotokolle anzuzeigen:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Führen Sie den folgenden Befehl aus, um Hive-Server-Protokolle anzuzeigen:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Herstellen einer Verbindung mit einem HDInsight-Cluster mit SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Wie starte ich die Hive-Shell mit bestimmten Konfigurationen in einem Cluster?

### <a name="resolution-steps"></a>Lösungsschritte

1. Geben Sie beim Starten der Hive-Shell ein Schlüssel-Wert-Paar für die Konfiguration an. Weitere Informationen finden Sie unter [Zusätzliche Lektüre](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Führen Sie den folgenden Befehl aus, um alle effektiven Konfigurationen auf Hive-Shell aufzulisten:

   ```apache
   hive> set;
   ```

   Verwenden Sie beispielsweise den folgenden Befehl, um Hive-Shell mit auf der Konsole aktivierter Debugprotokollierung zu starten:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Hive configuration properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties) (Eigenschaften der Hive-Konfiguration)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Wie analysiere ich Apache Tez DAG-Daten (Directed Acyclic Graph, gerichteter azyklischer Graph) in einem clusterkritischen Pfad?

### <a name="resolution-steps"></a>Lösungsschritte

1. Stellen Sie zum Analysieren eines Apache Tez DAG (Directed Acyclic Graph, gerichteter azyklischer Graph) in einem clusterkritischen Pfad mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen finden Sie unter [Zusätzliche Lektüre](#additional-reading-end).

2. Führen Sie an der Eingabeaufforderung folgenden Befehl aus:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Führen Sie zum Auflisten anderer Analyzer für die Analyse von Tez DAG-Daten (Directed Acyclic Graph, gerichteter azyklischer Graph) den folgenden Befehl aus:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Ein Beispielprogramm muss als erstes Argument angegeben werden.

   Gültige Programmnamen:
    - **ContainerReuseAnalyzer** : Ausgeben von Details zum Wiederverwenden von Containern in einem gerichteten azyklischen Graph
    - **CriticalPath** : Suchen des kritischen Pfads eines gerichteten azyklischen Graphs
    - **LocalityAnalyzer** : Ausgeben von Ortsdetails in einem gerichteten azyklischen Graph
    - **ShuffleTimeAnalyzer** : Analysieren von Durchmischungszeitdetails in einem gerichteten azyklischen Graph
    - **SkewAnalyzer** : Analysieren der Neigungsdetails in einem gerichteten azyklischen Graph
    - **SlowNodeAnalyzer** : Ausgeben von Knotendetails in einem gerichteten azyklischen Graph
    - **SlowTaskIdentifier** : Ausgeben von Details zu langsamen Aufgaben in einem gerichteten azyklischen Graph
    - **SlowestVertexAnalyzer** : Ausgeben von Details zu den langsamsten Scheitelpunkten in einem gerichteten azyklischen Graph
    - **SpillAnalyzer** : Ausgeben von Details zum Überlauf in einem gerichteten azyklischen Graph
    - **TaskConcurrencyAnalyzer** : Ausgeben von Details zur Parallelität von Aufgaben in einem gerichteten azyklischen Graph
    - **VertexLevelCriticalPathAnalyzer** : Suchen des kritischen Pfads auf Scheitelpunktebene in einem gerichteten azyklischen Graph

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Herstellen einer Verbindung mit einem HDInsight-Cluster mit SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Wie lade ich Tez DAG-Daten (Directed Acyclic Graph, gerichteter azyklischer Graph) aus einem Cluster herunter?

#### <a name="resolution-steps"></a>Lösungsschritte

Es gibt zwei Möglichkeiten zum Erfassen der Tez DAG-Daten:

- Über die Befehlszeile:

    Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Mithilfe der Ambari-Tez-Ansicht:

  1. Navigieren Sie zu Ambari.
  2. Wechseln Sie zur Tez-Ansicht (unter dem Kachelsymbol in der oberen rechten Ecke).
  3. Wählen Sie den gerichteten azyklischen Graph, der angezeigt werden soll.
  4. Wählen Sie **Download data** (Daten herunterladen) aus.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Zusätzliche Lektüre

[Herstellen einer Verbindung mit einem HDInsight-Cluster mit SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]