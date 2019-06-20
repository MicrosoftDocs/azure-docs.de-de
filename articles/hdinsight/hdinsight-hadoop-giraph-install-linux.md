---
title: Installieren und Verwenden von Giraph in Azure HDInsight
description: Erfahren Sie, wie Sie Giraph auf HDInsight-Clustern mit Skriptaktionen installieren. Sie können mit Giraph die Grafikverarbeitung in Apache Hadoop in der Azure-Cloud durchführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: aa13d8dfc65f020f3f27183423913933cd0b9404
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64697603"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installieren von Apache Giraph in HDInsight Hadoop-Clustern und Verwenden von Giraph zur Verarbeitung großer Diagramme

In diesem Artikel wird erläutert, wie Sie Apache Giraph in einem HDInsight-Cluster installieren. Das Skriptaktionenfeature von HDInsight ermöglicht Ihnen die Anpassung des Clusters durch Ausführen eines Bash-Skripts. Mithilfe von Skripts können Sie Cluster während und nach der Erstellung anpassen.

## <a name="whatis"></a>Was ist Giraph?

[Apache Giraph](https://giraph.apache.org/) ermöglicht die Graphverarbeitung mit Hadoop und lässt sich mit Azure HDInsight nutzen. Graphe veranschaulichen die Beziehungen zwischen Objekten. Ein Beispiel hierfür sind die Verbindungen zwischen den Routern eines großen Netzwerks wie dem Internet oder auch Beziehungen zwischen Personen in sozialen Netzwerken. Mit der Graphverarbeitung können Sie sich Gedanken über die Beziehungen zwischen den Objekten im Diagramm machen wie etwa:

* Ermitteln potenzieller Freunde aufgrund Ihrer aktuellen Beziehungen.

* Ermitteln der kürzesten Route zwischen zwei Computern in einem Netzwerk.

* Berechnen des Seitenrangs von Webseiten.

> [!WARNING]  
> Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft-Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten wie Giraph steht in wirtschaftlich angemessenem Rahmen Support für eine weiterführende Behebung des Problems zur Verfügung. Microsoft-Support kann Ihnen bei der Lösung des Problems helfen. Andernfalls finden Sie in Open-Source-Communitys Unterstützung, die oft über umfassende Kenntnisse zu dieser Technologie verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Für Apache-Projekte gibt es auch Projektwebsites auf [https://apache.org](https://apache.org). Beispiel: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Funktion des Skripts

Dieses Skript führt folgende Aktionen aus:

* Installiert Giraph in `/usr/hdp/current/giraph`

* Kopiert die `giraph-examples.jar`-Datei in den Standardspeicher (WASB) für den Cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installation von Giraph mithilfe von Skriptaktionen

Ein Beispielskript zum Installieren von Giraph in einem HDInsight-Cluster ist an folgendem Ort verfügbar:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Erstellung des Clusters mithilfe des Azure-Portals.

> [!NOTE]  
> Eine Skriptaktion kann mit einer der folgenden Methoden angewendet werden:
> * Azure PowerShell
> * Die Azure-CLI
> * Das HDInsight .NET SDK
> * Azure-Ressourcen-Manager-Vorlagen
> 
> Sie können auch Skriptaktionen auf bereits ausgeführte Cluster anwenden. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Erstellung eines Clusters anhand der Schritte in [Erstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-create-linux-clusters-portal.md), schließen Sie sie jedoch nicht ab.

2. Wählen Sie im Abschnitt **Optionale Konfiguration** die Option **Skriptaktionen** aus, und geben Sie die folgenden Informationen an:

   * **NAME**: Geben Sie einen Anzeigenamen für die Skriptaktion ein.

   * **SKRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: Aktivieren Sie diese Option.

   * **WORKER**: Lassen Sie diese Option deaktiviert.

   * **ZOOKEEPER**: Lassen Sie diese Option deaktiviert.

   * **PARAMETER**: Lassen Sie dieses Feld leer.

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern. Klicken Sie abschließend auf die Schaltfläche **Auswählen** im unteren Bereich des Abschnitts **Optionale Konfiguration**, um die optionalen Konfigurationsinformationen zu speichern.

4. Setzen Sie die Erstellung des Clusters wie unter [Erstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-create-linux-clusters-portal.md)beschrieben fort.

## <a name="usegiraph"></a>Wie verwende ich Giraph in HDInsight?

Nachdem Sie den Cluster erstellt haben, gehen Sie folgendermaßen vor, um das in Giraph enthaltene Beispiel „SimpleShortestPathsComputation“ auszuführen. Dieses Beispiel verwendet die grundlegende [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf)-Implementierung zum Ermitteln des kürzesten Pfads zwischen Objekten in einem Graph.

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen **tiny_graph.txt**zu erstellen:

    ```bash
    nano tiny_graph.txt
    ```

    Verwenden Sie als Inhalt der Datei den folgenden Text:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Diese Daten beschreiben die Beziehung zwischen Objekten in einem gerichteten Graphen unter Verwendung des Formats `[source_id, source_value,[[dest_id], [edge_value],...]]`. Jede Zeile repräsentiert eine Beziehung zwischen einem `source_id`-Objekt und mindestens einem `dest_id`-Objekt. Den `edge_value` kann man sich als die Stärke oder Distanz der Verbindung zwischen `source_id` und `dest\_id` vorstellen.

    Wenn die Daten auseinandergezogen und der Wert (die Gewichtung) als Abstand zwischen den Objekten verwendet werden, könnte es wie das folgende Diagramm aussehen:

    ![tiny_graph.txt als Kreise dargestellt mit Linien unterschiedlicher Länge dazwischen](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. Drücken Sie zum Speichern der Datei **STRG + X**, dann **Y**, und schließlich die **Eingabetaste**, um den Dateinamen zu akzeptieren.

4. Verwenden Sie zum Speichern der Daten im primären Speicher für den HDInsight-Cluster den folgenden Befehl:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Führen Sie das Beispiel „SimpleShortestPathsComputation“ mit dem folgenden Befehl aus:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    Die mit diesem Befehl verwendeten Parameter werden in der folgenden Tabelle beschrieben:

   | Parameter | Funktionsbeschreibung |
   | --- | --- |
   | `jar` |Die JAR-Datei, die die Beispiele enthält |
   | `org.apache.giraph.GiraphRunner` |Die Klasse, die zum Starten der Beispiele verwendet wird. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Das verwendete Beispiel. In diesem Beispiel wird der kürzeste Pfad zwischen ID 1 und allen anderen IDs im Graphen berechnet. |
   | `-ca mapred.job.tracker` |Der Hauptknoten für den Cluster. |
   | `-vif` |Das Eingabeformat, das für die Eingabedaten verwendet wird. |
   | `-vip` |Die Eingabedatendatei. |
   | `-vof` |Das Ausgabeformat. In diesem Beispiel die ID und der Wert als Nur-Text. |
   | `-op` |Der Ausgabespeicherort. |
   | `-w 2` |Die Anzahl der zu verwendenden Worker. In diesem Beispiel ist dies 2. |

    Weitere Informationen zu diesen und anderen mit Giraph-Beispielen verwendeten Parametern finden Sie unter [Giraph Schnellstart](https://giraph.apache.org/quick_start.html).

6. Nach Abschluss des Auftrags werden die Ergebnisse im Verzeichnis **/example/out/shortestpaths** gespeichert. Die Ausgabedateinamen beginnen mit **part-m-** und enden mit einer Zahl, die die erste, zweite, dritte Datei usw. anzeigt. Verwenden Sie den folgenden Befehl, um die Ausgabe anzuzeigen:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    Das Beispiel "SimpleShortestPathComputation" wurde hartcodiert und beginnt mit der Objekt-ID 1. Es findet den kürzesten Pfad zu anderen Objekten. Die Ausgabe weist die Formate `destination_id` und `distance` auf. Die `distance` stellt den Wert (oder das Gewicht) der Kanten dar, der zwischen Objekt-ID 1 und der Ziel-ID zurückgelegt wird.

    Wenn Sie diese Daten visualisieren, können Sie die Ergebnisse überprüfen, indem Sie den kürzesten Weg zwischen ID 1 und allen anderen Objekten zurücklegen. Der kürzeste Weg zwischen ID 1 und ID 4 ist 5. Dieser Wert ist die gesamte Entfernung zwischen <span style="color:orange">ID 1 und 3</span> und dann zwischen <span style="color:red">ID 3 und 4</span>.

    ![Zeichnen von Objekten als Kreise mit dem kürzesten Pfad dazwischen](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>Nächste Schritte

* [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md).
