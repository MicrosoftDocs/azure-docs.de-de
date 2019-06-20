---
title: Ausführen benutzerdefinierter MapReduce-Programme – Azure HDInsight
description: Wann und wie benutzerdefinierte MapReduce-Programme in HDInsight ausgeführt werden sollten.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 5ed82fc21aedc9af394922059859f81cfba1867e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713094"
---
# <a name="run-custom-mapreduce-programs"></a>Ausführen von MapReduce-Programmen

Apache Hadoop-basierte Big Data-Systeme wie HDInsight ermöglichen die Verarbeitung von Daten unter Verwendung einer breiten Palette von Tools und Technologien. In der folgenden Tabelle werden die Hauptvorteile mit ihren jeweils zu berücksichtigenden Aspekten beschrieben.

| Abfragemechanismus | Vorteile | Überlegungen |
| --- | --- | --- |
| **Apache Hive mit HiveQL** | <ul><li>Eine ausgezeichnete Lösung für die Batchverarbeitung und Analyse großer Mengen unveränderlicher Daten, für die Datenzusammenfassung und für Abfragen nach Bedarf. Verwendet eine vertraute SQL-ähnliche Syntax.</li><li>Kann verwendet werden, um persistente Datentabellen zu erzeugen, die sich leicht partitionieren und indizieren lassen.</li><li>Es können mehrere externe Tabellen und Sichten über dieselben Daten erstellt werden.</li><li>Unterstützt eine einfache Data Warehouse-Implementierung, die massive horizontale Skalierung und fehlertolerante Funktionen für die Datenspeicherung und -verarbeitung bereitstellt.</li></ul> | <ul><li>Erfordert, dass die Quelldaten zumindest eine gewisse identifizierbare Struktur aufweisen.</li><li>Ist nicht für Echtzeitabfragen und Aktualisierungen auf Zeilenebene geeignet. Eignet sich am besten für Batchaufträge über große Mengen von Daten.</li><li>Möglicherweise können manche Arten von komplexen Verarbeitungsaufgaben nicht durchgeführt werden.</li></ul> |
| **Apache Pig mit Pig Latin** | <ul><li>Eine ausgezeichnete Lösung für die Manipulation von Daten in Sätzen, für das Zusammenführen und Filtern von Datasets, das Anwenden von Funktionen auf Datensätze oder Gruppen von Datensätzen sowie die Neustrukturierung von Daten durch Definieren von Spalten, Gruppieren von Werten oder Konvertieren von Spalten in Zeilen.</li><li>Kann einen workflowbasierten Ansatz als Abfolge von Vorgängen mit Daten verwenden.</li></ul> | <ul><li>SQL-Benutzer finden Pig Latin möglicherweise weniger vertraut und schwieriger zu verwenden als HiveQL.</li><li>Die Standardausgabe ist in der Regel eine Textdatei, deren Verwendung mit Visualisierungstools wie Excel sich daher schwieriger gestalten kann. In der Regel schichten Sie eine Hive-Tabelle über die Ausgabe.</li></ul> |
| **Benutzerdefiniertes Zuordnen/Reduzieren** | <ul><li>Bietet volle Kontrolle über die Zuordnungs- und Reduzierungsphasen sowie deren Ausführung.</li><li>Gestattet die Optimierung von Abfragen, um eine maximale Leistung des Clusters zu erzielen, oder um die Last auf den Servern bzw. im Netzwerk zu minimieren.</li><li>Die Komponenten können in einer ganzen Reihe bekannter Sprachen geschrieben werden.</li></ul> | <ul><li>Lässt sich schwieriger verwenden als Pig oder Hive, weil Sie Ihre eigenen Zuordnungs- und Reduzierungskomponenten erstellen müssen.</li><li>Prozesse, die das Verknüpfen von Datensätzen erfordern, sind schwieriger zu implementieren.</li><li>Obwohl Testframeworks verfügbar sind, ist das Debuggen von Code komplexer als eine normale Anwendung, da der Code als Batchauftrag unter der Kontrolle des Hadoop-Auftragsplaners ausgeführt wird.</li></ul> |
| **Apache HCatalog** | <ul><li>Abstrahiert die Pfaddetails des Speichers und erleichtert so die Verwaltung und enthebt Benutzer der Notwendigkeit, zu wissen, wo die Daten gespeichert sind.</li><li>Ermöglicht die Benachrichtigung über Ereignisse, z. B. Verfügbarkeit von Daten, wodurch es andere Tools wie Oozie erkennen lässt, wenn Vorgänge aufgetreten sind.</li><li>Stellt eine relationale Sicht der Daten bereit, einschließlich der Partitionierung nach Schlüssel, und erleichtert den Zugriff auf die Daten.</li></ul> | <ul><li>Unterstützt RCFile-, CSV-Text-, JSON-Text-, SequenceFile- und ORC-Dateiformate standardmäßig, aber möglicherweise müssen Sie ein benutzerdefiniertes Serialisierungs-/Deserialisierungsprogramm (SerDe) für andere Formate schreiben.</li><li>HCatalog ist nicht threadsicher.</li><li>Gibt einige Einschränkungen für die Datentypen für Spalten, wenn das HCatalog-Ladeprogramm in Pig-Skripts verwendet wird. Weitere Informationen finden Sie in der Apache HCatalog-Dokumentation unter [HCatLoader-Datentypen](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes).</li></ul> |

Normalerweise verwenden Sie den einfachsten dieser Ansätze, der Ihnen die gewünschten Ergebnisse liefern kann. Beispielsweise können Sie eventuell solche Ergebnisse erzielen, indem Sie einfach Hive verwenden, für komplexere Szenarien aber müssen Sie ggf. Pig verwenden oder sogar Ihre eigene Zuordnungs- und Reduzierungskomponenten schreiben. Möglicherweise entscheiden Sie sich auch, nachdem Sie mit Hive oder Pig experimentiert haben, dass die benutzerdefinierten Zuordnungs- und Reduzierungskomponenten eine bessere Leistung bereitstellen können, indem sie es Ihnen gestatten, die Verarbeitung feiner abzustimmen und zu optimieren.

## <a name="custom-mapreduce-components"></a>Benutzerdefinierte Zuordnungs-/Reduzierungskomponenten

Code zum Zuordnen/Reduzieren besteht aus zwei getrennten Funktionen, die als **map**- und **reduce**-Komponenten implementiert werden. Die **map**-Komponente wird parallel auf mehreren Clusterknoten ausgeführt, wobei jeder Knoten die Zuordnung auf die eigene Teilmenge der Daten des Knotens anwendet. Die **reduce**-Komponente sortiert die Ergebnisse von allen map-Funktionen und fasst sie zusammen. Weitere Informationen zu diesen beiden Komponenten finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md).

Bei den meisten HDInsight-Verarbeitungsszenarien ist es einfacher und effizienter, eine Abstraktion einer höheren Ebene zu verwenden, z. B. Pig oder Hive. Sie können auch benutzerdefinierte Zuordnungs- und Reduzierungsomponenten für die Verwendung in Hive-Skripts erstellen, um komplexere Verarbeitungsvorgänge auszuführen.

Benutzerdefinierte Zuordnungs- und Reduzierungsomponenten werden in der Regel in Java geschrieben. Hadoop bietet eine Streamingschnittstelle, die außerdem die Verwendung von Komponenten gestattet, die in anderen Sprachen wie C#, F#, Visual Basic, Python oder JavaScript entwickelt wurden.

* Eine exemplarische Vorgehensweise zum Entwickeln benutzerdefinierter Java MapReduce-Programme finden Sie unter [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Unter folgenden Bedingungen sollten Sie erwägen, Ihre eigenen Zuordnungs- und Reduzierungskomponenten zu erstellen:

* Sie müssen Daten verarbeiten, die vollständig unstrukturiert sind, indem Sie die Daten analysieren und benutzerdefinierte Logik verwenden, um strukturierte Informationen daraus zu gewinnen.
* Sie möchten komplexe Aufgaben ausführen, die schwierig (oder überhaupt nicht) in Pig oder Hive auszudrücken sind, ohne dafür eine benutzerdefinierte Funktion erstellen zu müssen. So müssten Sie beispielsweise einen externen Geocodierungsdienst verwenden, um Koordinaten aus Breiten- und Längengraden oder IP-Adressen in den Quelldaten in geografischen Standortnamen umzuwandeln.
* Sie möchten Ihren vorhandenen .NET-, Python- oder JavaScript-Code map/reduce-Komponenten wiederverwenden, indem Sie die Hadoop-Streamingschnittstelle verwenden.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Hochladen und Ausführen Ihres benutzerdefinierten MapReduce-Programms

Die gängigsten MapReduce-Programme sind in Java geschrieben und werden in eine JAR-Datei kompiliert.

1. Nachdem Sie MapReduce-Programm entwickelt, kompiliert und getestet haben, verwenden Sie den Befehl `scp`, um Ihre JAR-Datei auf den Hauptknoten hochzuladen.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersetzen Sie **USERNAME** durch das SSH-Benutzerkonto Ihres Clusters. Ersetzen Sie **CLUSTERNAME** durch den Namen des Clusters. Wenn Sie zum Schutz des SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden, um die Datei mit dem privaten Schlüssel anzugeben.

2. Stellen Sie mithilfe von [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) eine Verbindung mit dem Cluster her.

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Führen Sie aus der SSH-Sitzung Ihr MapReduce-Programm über YARN aus.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Dieser Befehl übermittelt den MapReduce-Auftrag an YARN. Die Eingabedatei ist `/example/data/sample.log`, und das Ausgabeverzeichnis ist `/example/data/logoutput`. Die Eingabedatei und alle Ausgabedateien werden am Standardspeicherort des Clusters gespeichert.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von C# mit MapReduce-Streaming auf Apache Hadoop in HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Entwickeln von Java MapReduce-Programmen für Apache Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Erstellen von Apache Spark-Anwendungen für HDInsight-Cluster mit dem Azure-Toolkit für Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)
* [Verwenden benutzerdefinierter Python-Funktionen mit Apache Hive und Apache Pig in HDInsight](python-udf-hdinsight.md)
