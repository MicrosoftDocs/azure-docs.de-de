---
title: Apache Hadoop-Architektur – Azure HDInsight
description: Beschreibt Apache Hadoop-Speicher und die Verarbeitung in HDInsight-Clustern.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 3fd85232ff7044c699a3e68ce34b267bf50c4dc3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257858"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-Architektur in HDInsight

[Apache Hadoop](https://hadoop.apache.org/) umfasst zwei Kernkomponenten: [Apache HDFS (Hadoop Distributed File System)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) für die Speicherung und [Apache YARN (Yet Another Resource Negotiator)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) für die Verarbeitung. Mit Speicher- und Verarbeitungsfunktionen kann ein Cluster [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)-Programme und somit die gewünschte Datenverarbeitung ausführen.

> [!NOTE]  
> Ein HDFS wird üblicherweise nicht innerhalb des HDInsight-Clusters bereitgestellt, um Speicher bereitzustellen. Stattdessen verwenden Hadoop-Komponenten eine HDFS-kompatible Schnittstellenebene. Die eigentliche Speicherfunktion wird entweder durch Azure Storage oder durch Azure Data Lake Storage bereitgestellt. Für Hadoop werden MapReduce-Aufträge im HDInsight-Cluster so ausgeführt, als wäre ein HDFS vorhanden, sodass zur Unterstützung ihres Speicherbedarfs keine Änderungen erforderlich sind. Der Speicher wird bei Hadoop in HDInsight zwar ausgelagert, die YARN-Verarbeitung bleibt jedoch eine Kernkomponente. Weitere Informationen finden Sie unter [Einführung in Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Dieser Artikel enthält eine Einführung in YARN und erläutert die Koordinierung der Anwendungsausführung in HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Grundlagen zu Apache Hadoop YARN 

YARN steuert und orchestriert die Datenverarbeitung in Hadoop. YARN verfügt über zwei Kerndienste, die als Prozesse auf Knoten im Cluster ausgeführt werden: 

* ResourceManager 
* NodeManager

Der ResourceManager-Dienst gewährt Anwendungen (beispielsweise MapReduce-Aufträgen) Zugriff auf Computeressourcen des Clusters. Die Ressourcen werden in Form von Containern bereitgestellt, wobei sich jeder Container aus einer Zuweisung von CPU-Kernen und Arbeitsspeicher zusammensetzt. Wenn Sie alle in einem Cluster verfügbaren Ressourcen kombinieren und dann die Kerne und den Arbeitsspeicher auf Blöcke verteilen, stellt jeder Ressourcenblock einen Container dar. Jeder Knoten im Cluster hat eine Kapazität für eine bestimmte Anzahl von Containern, sodass die Anzahl verfügbarer Container für den Cluster auf einen festen Wert begrenzt ist. Die Zuteilung von Ressourcen in einem Container kann konfiguriert werden. 

Wenn eine MapReduce-Anwendung in einem Cluster ausgeführt wird, stellt der ResourceManager-Dienst der Anwendung die Container zur Verfügung, in denen die Ausführung erfolgt. Der ResourceManager-Dienst überwacht den Status ausgeführter Anwendungen, die verfügbare Clusterkapazität sowie Anwendungen, die abgeschlossen und deren Ressourcen freigegeben werden. 

Darüber hinaus führt der ResourceManager-Dienst einen Webserverprozess aus, der eine Webbenutzeroberfläche zum Überwachen des Status von Anwendungen bereitstellt.

Wenn ein Benutzer eine MapReduce-Anwendung zur Ausführung im Cluster übermittelt, wird diese an den ResourceManager-Dienst übermittelt. Der ResourceManager-Dienst ordnet wiederum einen Container auf verfügbaren NodeManager-Knoten zu. Auf den NodeManager-Knoten findet die eigentliche Anwendungsausführung statt. Im ersten zugeordneten Container wird eine besondere Anwendung namens „ApplicationMaster“ ausgeführt. Diese ApplicationMaster-Anwendung ist für den Bezug von Ressourcen in Form von weiteren Containern zuständig, die zum Ausführen der übermittelten Anwendung erforderlich sind. Die ApplicationMaster-Anwendung untersucht die Phasen der Anwendung (z.B. die Zuordnungs- und Reduzierungsphase) und berücksichtigt die zu verarbeitende Datenmenge. Anschließend werden von der ApplicationMaster-Anwendung im Namen der Anwendung die nötigen Ressourcen vom ResourceManager-Dienst angefordert (*ausgehandelt*). Der ResourceManager-Dienst gewährt der ApplicationMaster-Anwendung wiederum Zugriff auf Ressourcen der NodeManager-Instanzen im Cluster, die die Anwendung zur Ausführung der Anwendung verwendet. 

Die NodeManager-Instanzen führen die Aufgaben aus, die die Anwendung bilden, und melden ihren Fortschritt und Status wieder der ApplicationMaster-Anwendung. Die ApplicationMaster-Anwendung meldet den Status der Anwendung wiederum dem ResourceManager-Dienst. Der ResourceManager-Dienst gibt die Ergebnisse an den Client zurück.

## <a name="yarn-on-hdinsight"></a>YARN in HDInsight

YARN wird von allen HDInsight-Clustertypen bereitgestellt. Der ResourceManager-Dienst wird für hohe Verfügbarkeit mit einer primären und sekundären Instanz bereitgestellt, die auf dem ersten bzw. zweiten Hauptknoten im Cluster ausgeführt werden. Es ist immer nur eine einzelne Instanz des ResourceManager-Diensts aktiv. Die NodeManager-Instanzen werden auf den verfügbaren Workerknoten im Cluster ausgeführt.

![YARN in HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Einführung in Azure HDInsight](hadoop/apache-hadoop-introduction.md)
