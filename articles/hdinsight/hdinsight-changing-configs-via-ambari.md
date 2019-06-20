---
title: Optimieren von Clusterkonfigurationen mit Apache Ambari – Azure HDInsight
description: Verwenden Sie die Apache Ambari-Webbenutzeroberfläche, um HDInsight-Cluster zu konfigurieren und zu optimieren.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: f0db36fa380d0d1bb7f2b581c4bf8fa1abfaadaf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60698827"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Verwenden von Apache Ambari zum Optimieren von HDInsight-Clusterkonfigurationen

HDInsight stellt [Apache Hadoop](https://hadoop.apache.org/)-Cluster für größere Datenverarbeitungsanwendungen bereit. Das Verwalten, Überwachen und Optimieren dieser komplexen Cluster mit mehreren Knoten kann eine Herausforderung darstellen. [Apache Ambari](https://ambari.apache.org/) ist eine Webbenutzeroberfläche zum Verwalten und Überwachen von HDInsight-Linux-Clustern.  Verwenden Sie für Windows-Cluster die [Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md).

Eine Einführung in die Nutzung der Ambari-Webbenutzeroberfläche finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

Melden Sie sich unter `https://CLUSTERNAME.azurehdidnsight.net` mit Ihren Clusteranmeldeinformationen an Ambari an. Auf dem ersten Bildschirm wird ein Dashboard mit einer Übersicht angezeigt.

![Ambari-Dashboard](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Die Ambari-Webbenutzeroberfläche kann verwendet werden, um Hosts, Dienste, Warnungen, Konfigurationen und Ansichten zu verwalten. Ambari kann nicht verwendet werden, um einen HDInsight-Cluster zu erstellen, Dienste zu aktualisieren, Stapel und Versionen zu verwalten, für Hosts die In- bzw. Außerbetriebnahme durchzuführen oder dem Cluster Dienste hinzuzufügen.

## <a name="manage-your-clusters-configuration"></a>Verwalten der Konfiguration Ihres Clusters

Konfigurationseinstellungen können für die Optimierung eines bestimmten Diensts verwendet werden. Wählen Sie zum Ändern der Konfigurationseinstellungen eines Diensts den Dienst in der Randleiste **Dienste** aus (auf der linken Seite), und navigieren Sie dann auf der Seite mit den Dienstdetails zur Registerkarte **Configs** (Konfigurationen).

![Randleiste „Dienste“](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Ändern der NameNode-Java-Heapgröße

Die NameNode-Java-Heapgröße hängt von vielen Faktoren ab, z.B. der Auslastung des Clusters, der Anzahl von Dateien und der Anzahl von Blöcken. Die Standardgröße 1 GB funktioniert für die meisten Cluster gut, aber für einige Workloads kann mehr oder weniger Arbeitsspeicher erforderlich sein. 

Gehen Sie wie folgt vor, um die NameNode-Java-Heapgröße zu ändern:

1. Wählen Sie in der Randleiste „Dienste“ die Option **HDFS**, und navigieren Sie zur Registerkarte **Configs** (Konfigurationen).

    ![HDFS-Konfiguration](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Suchen Sie nach der Einstellung **NameNode Java heap size** (NameNode-Java-Heapgröße). Sie können auch das Textfeld **Filter** verwenden, um nach einer bestimmten Einstellung zu suchen. Wählen Sie das Symbol **Stift** neben dem Namen der Einstellung aus.

    ![NameNode-Java-Heapgröße](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Geben Sie den neuen Wert in das Textfeld ein, und drücken Sie anschließend die **EINGABETASTE**, um die Änderung zu speichern.

    ![Bearbeiten der NameNode-Java-Heapgröße](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. Die NameNode-Java-Heapgröße wird von 2 GB in 1 GB geändert.

    ![Bearbeitete NameNode-Java-Heapgröße](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Speichern Sie Ihre Änderungen, indem Sie oben auf dem Konfigurationsbildschirm auf die grüne Schaltfläche **Speichern** klicken.

    ![Speichern der Änderungen](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Apache Hive-Optimierung

In den folgenden Abschnitten werden die Konfigurationsoptionen zur Optimierung der Apache Hive-Gesamtleistung beschrieben.

1. Wählen Sie zum Ändern der Hive-Konfigurationsparameter in der Randleiste „Dienste“ die Option **Hive**.
1. Navigieren Sie zur Registerkarte **Configs** (Konfigurationen).

### <a name="set-the-hive-execution-engine"></a>Festlegen des Hive-Ausführungsmoduls

Hive verfügt über zwei Ausführungsmodule: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) und [Apache TEZ](https://tez.apache.org/). Tez ist schneller als MapReduce. Für HDInsight Linux-Cluster wird Tez als Standardausführungsmodul verwendet. Gehen Sie wie folgt vor, um das Ausführungsmodul zu ändern:

1. Geben Sie auf der Hive-Registerkarte **Configs** (Konfigurationen) im Feld „Filter“ den Text **execution engine** (Ausführungsmodul) ein.

    ![Suchen nach dem Ausführungsmodul](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. Der Standardwert der Eigenschaft **Optimization** (Optimierung) lautet **Tez**.

    ![Optimierung – Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Optimieren von Zuordnungen

Hadoop versucht, eine einzelne Datei in mehrere Dateien aufzuteilen (*Mapping*) und die sich ergebenden Dateien parallel zu verarbeiten. Die Anzahl von Zuordnungen (Mappers) richtet sich nach der Anzahl von Unterteilungen. Die Anzahl von Unterteilungen für das Ausführungsmodul Tez basiert auf den folgenden beiden Konfigurationsparametern:

* `tez.grouping.min-size`: Unterer Grenzwert für die Größe einer gruppierten Teilung, wobei der Standardwert 16 MB (16.777.216 Byte) lautet.
* `tez.grouping.max-size`: Oberer Grenzwert für die Größe einer gruppierten Teilung, wobei der Standardwert 1 GB (1.073.741.824 Byte) lautet.

Die Faustregel in Bezug auf die Leistung lautet: Verringern Sie diese beiden Parameter, um die Wartezeit zu verkürzen, und erhöhen Sie sie, um den Durchsatz zu erhöhen.

Wenn Sie beispielsweise vier Mapper-Tasks für eine Datengröße von 128 MB festlegen möchten, legen Sie für beide Parameter jeweils 32 MB fest (33.554.432 Byte).

1. Navigieren Sie zum Ändern der Grenzwertparameter zur Registerkarte **Configs** (Konfigurationen) des Tez-Diensts. Erweitern Sie den Bereich **General** (Allgemein), und suchen Sie nach den Parametern `tez.grouping.max-size` und `tez.grouping.min-size`.

1. Legen Sie für beide Parameter **33.554.432** Byte (32 MB) fest.

    ![Tez-Gruppierungsgrößen](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Diese Änderungen wirken sich auf alle Tez-Aufträge des gesamten Servers aus. Wählen Sie die entsprechenden Parameterwerte aus, um ein optimales Ergebnis zu erhalten.

### <a name="tune-reducers"></a>Optimieren von Reducern

[Apache ORC](https://orc.apache.org/) und [Snappy](https://google.github.io/snappy/) bieten jeweils eine hohe Leistung. Standardmäßig verfügt Hive unter Umständen über eine zu geringe Zahl von Reducern, sodass sich Engpässe ergeben.

Angenommen, die Eingabedatengröße beträgt bei Ihnen 50 GB. Diese Daten haben im ORC-Format mit Snappy-Komprimierung eine Größe von 1 GB. Hive schätzt die Anzahl von erforderlichen Reducern wie folgt: (Anzahl für Byte-Eingabe in Mapper/`hive.exec.reducers.bytes.per.reducer`).

Mit den Standardeinstellungen ergeben sich für dieses Beispiel vier Reducer.

Mit dem Parameter `hive.exec.reducers.bytes.per.reducer` wird die Anzahl von Byte angegeben, die pro Reducer verarbeitet werden. Der Standardwert beträgt 64 MB. Wenn Sie diesen Wert verringern, erhöht sich die Parallelität und ggf. auch die Leistung. Wenn Sie einen zu niedrigen Wert wählen, können auch zu viele Reducer erzeugt werden, was sich negativ auf die Leistung auswirken kann. Dieser Parameter basiert auf Ihren jeweiligen Datenanforderungen, Komprimierungseinstellungen und anderen Umgebungsfaktoren.

1. Navigieren Sie zum Ändern des Parameters zur Hive-Registerkarte **Configs** (Konfigurationen), und suchen Sie auf der Seite mit den Einstellungen nach dem Parameter **Data per Reducer** (Daten pro Reducer).

    ![Daten pro Reducer](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Wählen Sie **Bearbeiten**, um den Wert in 128 MB (134.217.728 Byte) zu ändern, und drücken Sie anschließend die **EINGABETASTE**, um dies zu speichern.

    ![Data pro Reducer – Bearbeitet](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Bei einer Eingabegröße von 1.024 MB mit 128 MB Daten pro Reducer ergeben sich acht Reducer (1024/128).

1. Ein falscher Wert für den Parameter **Data per Reducer** (Daten pro Reducer) kann zu einer hohen Zahl von Reducern führen, was sich negativ auf die Abfrageleistung auswirkt. Legen Sie `hive.exec.reducers.max` auf einen geeigneten Wert fest, um die maximale Anzahl von Reducern zu begrenzen. Der Standardwert ist 1009.

### <a name="enable-parallel-execution"></a>Aktivieren der parallelen Ausführung

Eine Hive-Abfrage wird in einer oder mehreren Stufen ausgeführt. Wenn die unabhängigen Stufen parallel ausgeführt werden können, wird die Abfrageleistung verbessert.

1.  Navigieren Sie zum Aktivieren der parallelen Abfragenausführung zur Hive-Registerkarte **Configs** (Konfigurationen), und suchen Sie nach der Eigenschaft `hive.exec.parallel`. Der Standardwert ist „false“. Ändern Sie den Wert in „true“, und drücken Sie anschließend die **EINGABETASTE**, um den Wert zu speichern.
 
1.  Ändern Sie die Eigenschaft `hive.exec.parallel.thread.number`, um die Anzahl von Aufträgen zu begrenzen, die parallel ausgeführt werden. Der Standardwert lautet 8.

    ![Parallele Hive-Ausführung](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Aktivieren der Vektorisierung

Hive verarbeitet Daten zeilenweise. Bei der Vektorisierung wird Hive angewiesen, Daten in Blöcken von 1.024 Zeilen zu verarbeiten, anstatt jede Zeile einzeln. Die Vektorisierung gilt nur für das ORC-Dateiformat.

1. Navigieren Sie zum Aktivieren einer vektorisierten Abfrageausführung zur Hive-Registerkarte **Configs** (Konfigurationen), und suchen Sie nach dem Parameter `hive.vectorized.execution.enabled`. Der Standardwert lautet für Hive 0.13.0 oder höher „true“.
 
1. Legen Sie den Parameter `hive.vectorized.execution.reduce.enabled` auf „true“ fest, um die vektorisierte Ausführung für die Reducerseite der Abfrage zu aktivieren. Der Standardwert ist „false“.

    ![Vektorisierte Hive-Ausführung](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Aktivieren der kostenbasierten Optimierung

Standardmäßig wird für Hive eine Gruppe von Regeln befolgt, um einen optimalen Plan für die Abfrageausführung zu ermitteln. Bei der kostenbasierten Optimierung (Cost-Based Optimization, CBO) werden mehrere Pläne zum Ausführen einer Abfrage evaluiert, und jedem Plan werden Kosten zugewiesen. Anschließend wird der kostengünstigste Plan zum Ausführen einer Abfrage ermittelt.

Navigieren Sie zum Aktivieren von CBO zur Hive-Registerkarte **Configs** (Konfigurationen), und suchen Sie nach `parameter hive.cbo.enable`. Legen Sie die Umschaltfläche auf **Ein** fest.

![CBO-Konfiguration](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Mit den folgenden zusätzlichen Konfigurationsparametern wird die Hive-Abfrageleistung erhöht, wenn CBO aktiviert ist:

* `hive.compute.query.using.stats`

    Bei der Einstellung „true“ nutzt Hive Statistiken, die im eigenen Metastore enthalten sind, um einfache Abfragen wie `count(*)` zu beantworten.

    ![CBO-Statistiken](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Spaltenstatistiken werden erstellt, wenn CBO aktiviert ist. Hive verwendet Spaltenstatistiken, die im Metastore gespeichert sind, um Abfragen zu optimieren. Das Abrufen von Spaltenstatistiken dauert für die einzelnen Spalten länger, wenn sehr viele Spalten vorhanden sind. Bei der Einstellung „false“ wird das Abrufen von Spaltenstatistiken aus dem Metastore deaktiviert.

    ![Hive-Statistiken – Festlegen von Spaltenstatistiken](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Einfache Partitionsstatistiken, z.B. die Anzahl von Zeilen, Datengröße und Dateigröße, werden im Metastore gespeichert. Bei der Einstellung „true“ werden die Partitionsstatistiken aus dem Metastore abgerufen. Bei „false“ wird die Dateigröße aus dem Dateisystem und die Anzahl von Zeilen aus dem Zeilenschema abgerufen.

    ![Hive-Statistiken – Festlegen von Partitionsstatistiken](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Aktivieren der Zwischenkomprimierung

Für Mapper-Tasks werden Zwischendateien erstellt, die von den Reducer-Tasks verwendet werden. Bei der Zwischenkomprimierung wird die Größe der Zwischendatei reduziert.

Für Hadoop-Aufträge kommt es normalerweise zu E/A-Engpässen. Durch die Komprimierung der Daten können die E/A-Vorgänge und die gesamte Netzwerkübertragung beschleunigt werden.

Die verfügbaren Komprimierungstypen sind:

| Format | Tool | Algorithmus | Dateierweiterung | Teilbar? |
| -- | -- | -- | -- | -- |
| GZip | GZip | VERKLEINERN | .gz | Nein |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ja |
| LZO | Lzop | LZO | .lzo | Ja, wenn indiziert |
| Snappy | – | Snappy | Snappy | Nein |

Hier gilt die folgende allgemeine Regel: Es ist wichtig, dass die Komprimierungsmethode teilbar ist, da andernfalls nur sehr wenige Mapper erstellt werden. Wenn es sich bei den Eingabedaten um Text handelt, ist `bzip2` die beste Option. Für das ORC-Format ist Snappy die schnellste Komprimierungsoption.

1. Navigieren Sie zum Aktivieren der Zwischenkomprimierung zur Hive-Registerkarte **Configs** (Konfigurationen), und legen Sie den Parameter `hive.exec.compress.intermediate` auf „true“ fest. Der Standardwert ist „false“.

    ![Hive-Ausführung – Zwischenkomprimierung](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Wählen Sie zum Komprimieren von Zwischendateien auch dann einen Komprimierungscodec mit geringeren CPU-Kosten aus, wenn der Codec nicht über eine hohe Komprimierungsausgabe verfügt.

1. Fügen Sie zum Festlegen des Codecs für die Zwischenkomprimierung die benutzerdefinierte Eigenschaft `mapred.map.output.compression.codec` der Datei `hive-site.xml` oder `mapred-site.xml` hinzu.

1. Gehen Sie wie folgt vor, um eine benutzerdefinierte Einstellung hinzuzufügen:

    a. Navigieren Sie zur Hive-Registerkarte **Configs** (Konfigurationen), und wählen Sie die Registerkarte **Advanced** (Erweitert).

    b. Suchen Sie unter der Registerkarte **Advanced** (Erweitert) nach dem Bereich **Custom hive-site** (Benutzerdefinierte Hive-Site), und erweitern Sie ihn.

    c. Klicken Sie unten im Bereich „Custom hive-site“ (Benutzerdefinierte Hive-Site) auf **Add Property** (Eigenschaft hinzufügen).

    d. Geben Sie im Fenster „Add Property“ (Eigenschaft hinzufügen) `mapred.map.output.compression.codec` als Schlüssel und `org.apache.hadoop.io.compress.SnappyCodec` als Wert ein.

    e. Klicken Sie auf **Hinzufügen**.

    ![Benutzerdefinierte Hive-Eigenschaft](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Die Zwischendatei wird per Snappy-Komprimierung verkleinert. Nach dem Hinzufügen der Eigenschaft wird sie im Bereich „Custom hive-site“ (Benutzerdefinierte Hive-Site) angezeigt.

    > [!NOTE]  
    > Mit dieser Prozedur wird die Datei `$HADOOP_HOME/conf/hive-site.xml` geändert.

### <a name="compress-final-output"></a>Komprimieren der endgültigen Ausgabe

Die endgültige Hive-Ausgabe kann auch komprimiert werden.

1. Navigieren Sie zum Komprimieren der endgültigen Hive-Ausgabe zur Hive-Registerkarte **Configs** (Konfigurationen), und legen Sie den Parameter `hive.exec.compress.output` auf „true“ fest. Der Standardwert ist „false“.

1. Fügen Sie zum Auswählen des Codecs für die Ausgabekomprimierung die benutzerdefinierte Eigenschaft `mapred.output.compression.codec` dem Bereich „Custom hive-site“ (Benutzerdefinierte Hive-Site) hinzu, wie in Schritt 3 des vorherigen Abschnitts beschrieben.

    ![Benutzerdefinierte Hive-Eigenschaft](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Aktivieren der spekulativen Ausführung

Bei der spekulativen Ausführung wird eine bestimmte Anzahl von doppelten Tasks gestartet, um den langsamsten Task-Tracker zu erkennen und in eine Sperrliste einzutragen, während die allgemeine Auftragsausführung verbessert wird, indem einzelne Taskergebnisse optimiert werden.

Die spekulative Ausführung sollte für MapReduce-Aufträge mit großen Eingabemengen und langer Ausführungsdauer nicht aktiviert werden.

* Navigieren Sie zum Aktivieren der spekulativen Ausführung zur Hive-Registerkarte **Configs** (Konfigurationen), und legen Sie den Parameter `hive.mapred.reduce.tasks.speculative.execution` auf „true“ fest. Der Standardwert ist „false“.

    ![Hive-mapred-Reduce-Tasks – Spekulative Ausführung](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Optimieren von dynamischen Partitionen

Hive ermöglicht die Erstellung von dynamischen Partitionen, wenn Datensätze in eine Tabelle eingefügt werden, ohne dass jede Partition einzeln vordefiniert werden muss. Obwohl es zur Erstellung einer großen Zahl von Partitionen und einer großen Zahl von Dateien für jede Partition kommen kann, ist dies ein leistungsstarkes Feature.

1. Damit Hive dynamische Partitionen nutzen kann, sollte der Wert des Parameters `hive.exec.dynamic.partition` auf „true“ (Standardeinstellung) festgelegt werden.

1. Ändern Sie den Modus für dynamische Partitionen in *strict*. Im Modus „strict“ muss mindestens eine Partition statisch sein. So wird verhindert, dass Abfragen ohne Partitionsfilter in der WHERE-Klausel enthalten sind. Mit *strict* werden also Abfragen verhindert, bei denen alle Partitionen gescannt werden. Navigieren Sie zur Hive-Registerkarte **Configs** (Konfigurationen), und legen Sie `hive.exec.dynamic.partition.mode` auf **strict** fest. Der Standardwert lautet **nonstrict**.
 
1. Ändern Sie den Parameter `hive.exec.max.dynamic.partitions`, um die Anzahl von zu erstellenden dynamischen Partitionen zu begrenzen. Der Standardwert lautet 5000.
 
1. Ändern Sie `hive.exec.max.dynamic.partitions.pernode`, um die Gesamtzahl von dynamischen Partitionen pro Knoten zu begrenzen. Der Standardwert lautet 2000.

### <a name="enable-local-mode"></a>Aktivieren des lokalen Modus

Im lokalen Modus kann Hive alle Tasks eines Auftrags auf einem einzelnen Computer oder ggf. auch innerhalb eines einzelnen Prozesses durchführen. Auf diese Weise wird die Abfrageleistung verbessert, wenn die Menge der Eingabedaten gering ist und das Starten von Tasks für Abfragen einen erheblichen Prozentsatz der gesamten Abfrageausführung einnimmt.

Fügen Sie zum Aktivieren des lokalen Modus den Parameter `hive.exec.mode.local.auto` dem Bereich „Custom hive-site“ (Benutzerdefinierte Hive-Site) hinzu. Dies wird in Schritt 3 des Abschnitts [Aktivieren der Zwischenkomprimierung](#enable-intermediate-compression) beschrieben.

![Hive-Ausführung – Automatischer lokaler Modus](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Festlegen eines einzelnen MapReduce-Vorgangs (MultiGROUP BY)

Wenn diese Eigenschaft auf „true“ festgelegt ist, generiert eine MultiGROUP BY-Abfrage mit allgemeinen group-by-Schlüsseln einen einzelnen MapReduce-Auftrag.  

Fügen Sie zum Aktivieren dieses Verhaltens den Parameter `hive.multigroupby.singlereducer` dem Bereich „Custom hive-site“ (Benutzerdefinierte Hive-Site) hinzu. Dies wird in Schritt 3 des Abschnitts [Aktivieren der Zwischenkomprimierung](#enable-intermediate-compression) beschrieben.

![Hive – Festlegen eines einzelnen MapReduce-Vorgangs (MultiGROUP BY)](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Zusätzliche Hive-Optimierungen

In den folgenden Abschnitten werden zusätzliche Hive-bezogene Optimierungen beschrieben, die Sie festlegen können.

#### <a name="join-optimizations"></a>Join-Optimierungen

Der Join-Standardtyp in Hive ist *shuffle join*. In Hive lesen spezielle Mapper die Eingabe und geben ein Join-Schlüssel-Wert-Paar für eine Zwischendatei aus. Hadoop sortiert diese Paare in einem so genannten Shuffle-Schritt und führt sie zusammen. Dieser Shuffle-Schritt ist kostenintensiv. Wenn Sie basierend auf Ihren Daten die richtige Join-Auswahl treffen, kann die Leistung erheblich verbessert werden.

| Join-Typ | Wenn | Vorgehensweise | Hive-Einstellungen | Kommentare |
| -- | -- | -- | -- | -- |
| Shuffle Join | <ul><li>Standardauswahl</li><li>Funktioniert immer</li></ul> | <ul><li>Liest aus einem Teil von einer der Tabellen</li><li>Buckets und Sortierungen für einen Join-Schlüssel</li><li>Sendet einen Bucket an jeden Reduce-Vorgang</li><li>Join erfolgt auf Reduce-Seite</li></ul> | Keine nennenswerte Hive-Einstellung erforderlich | Funktioniert immer |
| Map Join | <ul><li>Eine Tabelle passt in den Arbeitsspeicher</li></ul> | <ul><li>Kleine Tabelle wird in die Arbeitsspeicher-Hashtabelle eingelesen</li><li>Stream durch einen Teil der großen Datei</li><li>Join-Vorgang für jeden Datensatz der Hashtabelle</li><li>Joins werden nur vom Mapper durchgeführt</li></ul> | `hive.auto.confvert.join=true` | Sehr schnell, aber begrenzt |
| Sort/Merge-Bucket | Wenn für beide Tabellen Folgendes gilt: <ul><li>Gleich sortiert</li><li>Gleiche Buckets</li><li>Join-Vorgang für sortierte bzw. Bucket-Spalten</li></ul> | Für jeden Prozess gilt: <ul><li>Liest einen Bucket aus jeder Tabelle</li><li>Verarbeitet die Zeile mit dem niedrigsten Wert</li></ul> | `hive.auto.convert.sortmerge.join=true` | Sehr effizient |

#### <a name="execution-engine-optimizations"></a>Optimierungen des Ausführungsmoduls

Weitere Empfehlungen zum Optimieren des Hive-Ausführungsmoduls:

| Einstellung | Empfohlen | HDInsight-Standard |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | true = sicherer, langsamer; false = schneller | false |
| `tez.am.resource.memory.mb` | Oberer Grenzwert von 4 GB für die meisten Fälle geeignet | Automatisch optimiert |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20\.000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40\.000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig-Optimierung

[Apache Pig](https://pig.apache.org/)-Eigenschaften können über die Ambari-Webbenutzeroberfläche geändert werden, um Pig-Abfragen zu optimieren. Beim Ändern von Pig-Eigenschaften über Ambari werden die Pig-Eigenschaften direkt in der Datei `/etc/pig/2.4.2.0-258.0/pig.properties` geändert.

1. Navigieren Sie zum Ändern von Pig-Eigenschaften zur Pig-Registerkarte **Configs** (Konfigurationen), und erweitern Sie den Bereich **Advanced pig-properties** (Erweiterte Pig-Eigenschaften).

1. Suchen Sie nach dem Wert der Eigenschaft, den Sie ändern möchten, kommentieren Sie ihn aus, und ändern Sie ihn.

1. Wählen Sie oben rechts im Fenster die Option **Speichern**, um den neuen Wert zu speichern. Für einige Eigenschaften ist ggf. ein Neustart des Diensts erforderlich.

    ![Erweiterte Pig-Eigenschaften](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> Alle Einstellungen auf Sitzungsebene setzen die Eigenschaftswerte in der Datei `pig.properties` außer Kraft.

### <a name="tune-execution-engine"></a>Optimieren des Ausführungsmoduls

Zwei Ausführungsmodule sind für die Ausführung von Pig-Skripts verfügbar: MapReduce und Tez. Tez ist ein optimiertes Modul, das deutlich schneller als MapReduce ist.

1. Suchen Sie im Bereich **Advanced pig-properties** (Erweiterte Pig-Eigenschaften) nach der Eigenschaft `exectype`, um das Ausführungsmodul zu ändern.

1. Der Standardwert lautet **MapReduce**. Ändern Sie ihn in **Tez**.


### <a name="enable-local-mode"></a>Aktivieren des lokalen Modus

Ähnlich wie in Hive wird der lokale Modus verwendet, um Aufträge mit relativ kleiner Datenmenge zu beschleunigen.

1. Legen Sie zum Aktivieren des lokalen Modus `pig.auto.local.enabled` auf **true** fest. Der Standardwert ist „false“.

1. Aufträge mit einer Eingabedatengröße unter dem Wert der Eigenschaft `pig.auto.local.input.maxbytes` werden als kleine Aufträge angesehen. Der Standardwert ist 1 GB.


### <a name="copy-user-jar-cache"></a>Kopieren von JARs in den Cache für Benutzer

Pig kopiert die JAR-Dateien, die für UDFs erforderlich sind, in einen verteilten Cache, um sie für Taskknoten verfügbar zu machen. Diese JARs ändern sich nicht häufig. Bei einer Aktivierung können JARs mit der Einstellung `pig.user.cache.enabled` in einem Cache platziert werden, damit sie für Aufträge wiederverwendet werden können, die von demselben Benutzer ausgeführt werden. Dies führt zu einer leichten Verbesserung der Auftragsleistung.

1. Legen Sie `pig.user.cache.enabled` auf „true“ fest, um dies zu aktivieren. Die Standardeinstellung ist „false“.

1. Legen Sie `pig.user.cache.location` auf den Basispfad fest, um den Basispfad für die JARs im Cache festzulegen. Der Standardwert lautet `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimieren der Leistung über Arbeitsspeichereinstellungen

Die folgenden Einstellungen für den Arbeitsspeicher können zur Optimierung der Pig-Skriptleistung beitragen.

* `pig.cachedbag.memusage`: Die Arbeitsspeichermenge, die einem Behälter zugeordnet wird. Ein Behälter (Englisch: bag) ist eine Sammlung mit Tupeln. Ein Tupel ist eine geordnete Menge von Feldern, und ein Feld ist ein Datenelement. Wenn der zugeordnete Arbeitsspeicher für die Daten in einem Behälter nicht mehr ausreicht, werden sie auf dem Datenträger gespeichert. Der Standardwert ist „0,2“. Dies sind 20 Prozent des verfügbaren Arbeitsspeichers. Dieser Arbeitsspeicher wird für alle Behälter einer Anwendung gemeinsam genutzt.

* `pig.spill.size.threshold`: Die Daten von Behältern, die diesen Überlauf-Schwellenwert (in Byte) überschreiten, werden auf dem Datenträger gespeichert. Der Standardwert ist 5 MB.


### <a name="compress-temporary-files"></a>Komprimieren von temporären Dateien

Pig generiert während der Auftragsausführung temporäre Dateien. Das Komprimieren der temporären Dateien führt zu einer Leistungsverbesserung beim Lesen oder Schreiben von Dateien auf Datenträger. Die folgenden Einstellungen können verwendet werden, um temporäre Dateien zu komprimieren.

* `pig.tmpfilecompression`: Bei der Einstellung „true“ wird die Komprimierung von temporären Dateien aktiviert. Der Standardwert ist „false“.

* `pig.tmpfilecompression.codec`: Der Komprimierungscodec zum Komprimieren der temporären Dateien. Die empfohlenen Komprimierungscodecs für eine geringe CPU-Auslastung sind [LZO](https://www.oberhumer.com/opensource/lzo/) und Snappy.

### <a name="enable-split-combining"></a>Aktivieren von kombinierten Teilungen

Wenn diese Einstellung aktiviert ist, werden kleinere Dateien kombiniert, um weniger Zuordnungsaufgaben zu erhalten. Dies verbessert die Effizienz von Aufträgen mit vielen kleinen Dateien. Legen Sie `pig.noSplitCombination` auf „true“ fest, um dies zu aktivieren. Der Standardwert ist „false“.


### <a name="tune-mappers"></a>Optimieren von Zuordnungen

Die Anzahl von Zuordnungen (Mappern) wird gesteuert, indem die Eigenschaft `pig.maxCombinedSplitSize` geändert wird. Hiermit wird die Größe der Daten angegeben, die von einer einzelnen Mapper-Task verarbeitet werden sollen. Der Standardwert ist die Standardblockgröße des Dateisystems. Eine Erhöhung dieses Werts führt zu einer Verringerung bei der Anzahl von Mapper-Tasks.


### <a name="tune-reducers"></a>Optimieren von Reducern

Die Anzahl von Reducern wird basierend auf dem Parameter `pig.exec.reducers.bytes.per.reducer` berechnet. Mit dem Parameter wird die Anzahl von Bytes angegeben, die pro Reducer verarbeitet werden. Der Standardwert ist 1 GB. Legen Sie einen Wert für die Eigenschaft `pig.exec.reducers.max` fest, um die maximale Anzahl von Reducern anzugeben. Der Standardwert ist 999.


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase-Optimierung mit der Ambari-Webbenutzeroberfläche

Die [Apache HBase](https://hbase.apache.org/)-Konfiguration wird über die Registerkarte **HBase-Konfigurationen** geändert. In den folgenden Abschnitten werden einige wichtige Konfigurationseinstellungen beschrieben, die sich auf die HBase-Leistung auswirken.

### <a name="set-hbaseheapsize"></a>Festlegen von HBASE_HEAPSIZE

Mit der HBase-Heapgröße wird die maximale Heapmenge in MB angegeben, die von Servern vom Typ *region* und *master* verwendet werden kann. Der Standardwert ist 1.000 MB. Es ist ratsam, diesen Wert je nach Clusterworkload anzupassen.

1. Navigieren Sie zum Ändern des Werts auf der HBase-Registerkarte **Configs** (Konfigurationen) zum Bereich **Advanced HBase-env** (HBase-env – Erweitert), und suchen Sie nach der Einstellung `HBASE_HEAPSIZE`.

1. Ändern Sie den Standardwert in 5.000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimieren von Workloads mit vielen Lesevorgängen

Die folgenden Konfigurationen sind wichtig, um die Leistung von Workloads mit vielen Lesevorgängen zu verbessern.

#### <a name="block-cache-size"></a>Größe des Blockcaches

Der Blockcache ist der Lesecache. Seine Größe wird mit dem Parameter `hfile.block.cache.size` gesteuert. Der Standardwert ist „0,4“. Dies sind 40 Prozent des gesamten Arbeitsspeichers des Regionsservers. Je größer der Blockcache ist, desto schneller werden die zufälligen Lesevorgänge durchgeführt.

1. Navigieren Sie zum Ändern dieses Parameters auf der HBase-Registerkarte **Configs** (Konfigurationen) zur Registerkarte **Settings** (Einstellungen), und suchen Sie nach **% of RegionServer Allocated to Read Buffers** (RegionServer-Zuteilung für Lesepuffer in Prozent).

    ![Größe des HBase-Blockcaches](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Wählen Sie das Symbol **Bearbeiten**, um den Wert zu ändern.


#### <a name="memstore-size"></a>Memstore-Größe

Alle Bearbeitungen werden im Arbeitsspeicherpuffer gespeichert, der als *Memstore* bezeichnet wird. Hierdurch wird die Gesamtmenge der Daten erhöht, die mit einem einzelnen Vorgang auf den Datenträger geschrieben werden können, und der nachfolgende Zugriff auf die letzten Bearbeitungen wird beschleunigt. Die Memstore-Größe wird mit den beiden folgenden Parametern definiert:

* `hbase.regionserver.global.memstore.UpperLimit`: Definiert den maximalen Prozentsatz des Regionsservers, der von kombinierten Memstore-Einheiten verwendet werden kann.

* `hbase.regionserver.global.memstore.LowerLimit`: Definiert den minimalen Prozentsatz des Regionsservers, der von kombinierten Memstore-Einheiten verwendet werden kann.

Sie können den oberen und unteren Grenzwert für Memstore reduzieren, um eine Optimierung für zufällige Lesevorgänge zu erzielen.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Anzahl von Zeilen, die beim Scannen vom Datenträger abgerufen werden

Mit der Einstellung `hbase.client.scanner.caching` wird die Anzahl von Zeilen definiert, die vom Datenträger gelesen werden, wenn für einen Scanner die `next`-Methode aufgerufen wird.  Der Standardwert ist 100. Je höher der Wert ist, desto weniger Remoteaufrufe werden vom Client an den Regionsserver gesendet, sodass für die Scans weniger Zeit benötigt wird. Hierdurch wird aber auch die Arbeitsspeicherauslastung des Clients erhöht.

![HBase – Anzahl von abgerufenen Zeilen](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Legen Sie den Wert nicht so fest, dass der Zeitraum zwischen dem Aufruf der nächsten Methode auf einem Scanner größer als der Zeitraum für den Scanner-Timeout ist. Die Dauer bis zum Scanner-Timeout wird mit der Eigenschaft `hbase.regionserver.lease.period` definiert.


### <a name="optimize-write-heavy-workloads"></a>Optimieren von Workloads mit vielen Schreibvorgängen

Die folgenden Konfigurationen sind wichtig, um die Leistung von Workloads mit vielen Schreibvorgängen zu verbessern.


#### <a name="maximum-region-file-size"></a>Maximale Regionsdateigröße

HBase speichert Daten in einem internen Dateiformat (*HFile*). Mit der Eigenschaft `hbase.hregion.max.filesize` wird die Größe einer einzelnen HFile für eine Region definiert.  Eine Region wird in zwei Regionen unterteilt, wenn die Summe aller HFiles in einer Region diese Einstellung übersteigt.
 
![HBase – Maximale Dateigröße für HRegion](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Je größer die Regionsdatei ist, desto kleiner ist die Anzahl von Unterteilungen. Sie können die Dateigröße erhöhen, um einen Wert zu ermitteln, der zur maximalen Schreibleistung führt.


#### <a name="avoid-update-blocking"></a>Vermeiden der Updateblockierung

* Mit der Eigenschaft `hbase.hregion.memstore.flush.size` wird die Größe definiert, mit der für Memstore der Flushvorgang auf den Datenträger durchgeführt wird. Die Standardgröße beträgt 128 MB.

* Der Blockmultiplikator für die HBase-Region wird mit `hbase.hregion.memstore.block.multiplier` definiert. Der Standardwert ist 4. Der zulässige Höchstwert ist 8.

* HBase blockiert Updates, wenn der Memstore eine Größe von (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) Byte hat.

    Bei den Standardwerten für die Flushgröße und den Blockmultiplikator werden Updates blockiert, wenn die Größe für den Memstore 128 * 4 = 512 MB beträgt. Erhöhen Sie den Wert von `hbase.hregion.memstore.block.multiplier`, um die Anzahl für die Updateblockierung zu reduzieren.

![HBase-Region – Blockmultiplikator](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definieren der Memstore-Größe

Die Memstore-Größe wird mit den Parametern `hbase.regionserver.global.memstore.UpperLimit` und `hbase.regionserver.global.memstore.LowerLimit` definiert. Wenn Sie Werte hierfür gleich festlegen, werden die Pausen zwischen den Schreibvorgängen reduziert (und häufiger Flushvorgänge durchgeführt), und die Schreibleistung verbessert sich.


### <a name="set-memstore-local-allocation-buffer"></a>Festlegen des lokalen Memstore-Zuteilungspuffers

Die Nutzung des lokalen Memstore-Zuteilungspuffers wird mit der Eigenschaft `hbase.hregion.memstore.mslab.enabled` ermittelt. Wenn die Einstellung aktiviert ist (true), wird die Heapfragmentierung bei einer hohen Zahl von Schreibvorgängen verhindert. Der Standardwert lautet „true“.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)
