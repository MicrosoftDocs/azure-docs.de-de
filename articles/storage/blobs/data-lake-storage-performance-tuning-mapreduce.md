---
title: 'Optimieren der Leistung: MapReduce, HDInsight und Azure Data Lake Storage Gen2 | Microsoft-Dokumentation'
description: Informieren Sie sich über die Richtlinien zur Optimierung der Leistung von MapReduce-Aufträgen in Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912925"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Optimieren der Leistung: MapReduce, HDInsight und Azure Data Lake Storage Gen2

Es werden die Faktoren beschrieben, die Sie berücksichtigen sollten, wenn Sie die Leistung von MapReduce-Aufträgen optimieren. Dieser Artikel enthält einen Bereich mit Richtlinien für die Leistungsoptimierung.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen2-Konto**. Anweisungen zum Erstellen eines solchen Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](../common/storage-account-create.md).
* Einen **Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen2-Konto. Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
* **Verwendung von MapReduce in HDInsight**.  Weitere Informationen finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight](../../hdinsight/hadoop/hdinsight-use-mapreduce.md).
* **Leitlinien für die Leistungsoptimierung von Data Lake Storage Gen2**.  Allgemeine Leistungskonzepte finden Sie unter [Leitfaden für die Leistungsoptimierung von Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md).

## <a name="parameters"></a>Parameter

Im Folgenden finden Sie die Parameter, die Sie für die Ausführung von MapReduce konfigurieren können, um die Leistung in Azure Data Lake Storage Gen2 zu verbessern:

* **Mapreduce.map.memory.mb** – die Menge an Arbeitsspeicher, die jedem Mapper zugeordnet werden soll
* **Mapreduce.job.maps** – die Anzahl an Zuordnungstasks pro Auftrag
* **Mapreduce.reduce.memory.mb** – die Menge an Arbeitsspeicher, die jedem Reducer zugeordnet werden soll
* **Mapreduce.job.reduces** – die Anzahl von Reduzierungstasks pro Auftrag

**Mapreduce.map.memory / Mapreduce.reduce.memory**: Diese Zahl muss angepasst werden, je nachdem, wie viel Arbeitsspeicher für den Zuordnungs- oder Reduzierungstask erforderlich ist.  Die Standardwerte von „mapreduce.map.memory“ und „mapreduce.reduce.memory“ können in Ambari über die YARN-Konfiguration angezeigt werden.  Navigieren Sie in Ambari zu YARN, und zeigen Sie die Registerkarte für die Konfiguration an.  Der YARN-Arbeitsspeicher wird angezeigt.  

**Mapreduce.job.maps / Mapreduce.job.reduces**: Diese Einstellung legt die maximale Anzahl von Mappern oder Reducern fest, die erstellt werden.  Die Anzahl der Teilungen legt fest, wie viele Mapper für den MapReduce-Auftrag erstellt werden.  Daher erhalten Sie möglicherweise weniger Mapper als angefordert, wenn weniger Teilungen als angeforderte Mapper vorhanden sind.       

## <a name="guidance"></a>Anleitungen

> [!NOTE]
> Bei den Anleitungen in diesem Dokument wird davon ausgegangen, dass Ihre Anwendung die einzige Anwendung ist, die in Ihrem Cluster ausgeführt wird.

**Schritt 1: Ermitteln der Anzahl von ausgeführten Aufträgen**

Standardmäßig nutzt MapReduce den gesamten Cluster für Ihren Auftrag.  Sie können einen kleineren Teil des Clusters verwenden, indem Sie weniger Mapper verwenden als Container verfügbar sind.        

**Schritt 2: Festlegen von „mapreduce.map.memory/mapreduce.reduce.memory“**

Die Größe des Arbeitsspeichers für Mapper/Reducer-Aufgaben richtet sich nach Ihrem jeweiligen Auftrag.  Sie können die Arbeitsspeichergröße reduzieren, wenn Sie die Parallelität erhöhen möchten.  Die Anzahl von gleichzeitig ausgeführten Tasks richtet sich nach der Anzahl von Containern.  Durch Verringern der Arbeitsspeichermenge pro Mapper oder Reducer können mehr Container erstellt werden, wodurch mehr Mapper oder Reducer gleichzeitig ausgeführt werden können.  Wenn die Arbeitsspeichermenge zu stark verringert wird, steht für einige Prozesse möglicherweise nicht genügend Arbeitsspeicher zur Verfügung.  Wenn Sie beim Ausführen Ihres Auftrags einen Heapfehler erhalten, sollten Sie den Arbeitsspeicher pro Mapper oder Reducer erhöhen.  Denken Sie daran, dass beim Hinzufügen weiterer Container auch zusätzlicher Overhead für jeden zusätzlichen Container entsteht, wodurch die Leistung beeinträchtigt werden kann.  Eine andere Möglichkeit besteht darin, mehr Arbeitsspeicher zur Verfügung zu stellen, indem Sie einen Cluster verwenden, der über mehr Arbeitsspeicher verfügt, oder indem Sie die Anzahl von Knoten in Ihrem Cluster erhöhen.  Eine größere Menge an Arbeitsspeicher ermöglicht die Verwendung einer größeren Anzahl von Containern, was wiederum mehr Parallelität bedeutet.  

**Schritt 3: Ermitteln des gesamten YARN-Arbeitsspeichers**

Beim Optimieren von „mapreduce.job.maps/mapreduce.job.reduces“ müssen Sie die Menge an YARN-Arbeitsspeicher berücksichtigen, die zur Verwendung verfügbar ist.  Diese Informationen sind in Ambari verfügbar.  Navigieren Sie zu YARN, und zeigen Sie die Registerkarte für die Konfiguration an.  Die Größe des YARN-Arbeitsspeichers wird in diesem Fenster angezeigt.  Um den YARN-Gesamtarbeitsspeicher zu erhalten, müssen Sie den YARN-Arbeitsspeicher pro Knoten mit der Anzahl von Knoten in Ihrem Cluster multiplizieren.

YARN-Arbeitsspeicher gesamt = Knoten × YARN-Arbeitsspeicher pro Knoten

Wenn Sie einen leeren Cluster verwenden, kann der Arbeitsspeicher der gesamte YARN-Arbeitsspeicher für den Cluster sein.  Wenn andere Anwendungen auch Arbeitsspeicher belegen, können Sie nur einen Teil des Clusterarbeitsspeichers nutzen, indem Sie die Anzahl von Mappern oder Reducern auf die Anzahl von Containern verringern, die Sie verwenden möchten.  

**Schritt 4: Berechnen der Anzahl von YARN-Containern**

YARN-Container geben vor, wie viel Parallelität für den Auftrag verfügbar ist.  Teilen Sie den YARN-Gesamtarbeitsspeicher durch „mapreduce.map.memory“.  

Anzahl der YARN-Container = YARN-Arbeitsspeicher gesamt / mapreduce.map.memory

**Schritt 5: Festlegen von „mapreduce.job.maps/mapreduce.job.reduces“**

Legen Sie „mapreduce.job.maps/mapreduce.job.reduces“ mindestens auf die Anzahl verfügbarer Container fest.  Sie können weiter experimentieren, indem Sie die Anzahl von Mappern und Reducern erhöhen, um zu ermitteln, ob sich die Leistung verbessert.  Denken Sie daran, dass eine größere Anzahl von Mappern zu weiterem Overhead führt und zu viele Mapper daher die Leistung beeinträchtigen können.  

CPU-Planung und -Isolierung sind standardmäßig deaktiviert, sodass die Anzahl von YARN-Containern durch den Arbeitsspeicher begrenzt ist.

## <a name="example-calculation"></a>Beispielberechnung

Angenommen, wir haben einen Cluster mit 8 D14-Knoten und möchten einen E/A-intensiven Auftrag ausführen.  Dann müssen Sie folgende Berechnungen anstellen:

**Schritt 1: Ermitteln der Anzahl von ausgeführten Aufträgen**

In diesem Beispiel nehmen wir an, dass unser Auftrag der einzige ausgeführte Auftrag ist.  

**Schritt 2: Festlegen von „mapreduce.map.memory/mapreduce.reduce.memory“**

In diesem Beispiel führen wir einen E/A-intensiven Auftrag aus und treffen die Entscheidung, dass 3 GB Arbeitsspeicher für die Mapper-Aufgaben ausreichend sind.

mapreduce.map.memory = 3 GB

**Schritt 3: Ermitteln des gesamten YARN-Arbeitsspeichers**

Der Gesamtarbeitsspeicher aus dem Cluster beträgt 8 Knoten × 96 GB YARN-Arbeitsspeicher für einen D14 = 768 GB

**Schritt 4: Berechnen der Anzahl von YARN-Containern**

Anzahl der YARN-Container = 768 GB verfügbarer Arbeitsspeicher / 3 GB Arbeitsspeicher = 256

**Schritt 5: Festlegen von „mapreduce.job.maps/mapreduce.job.reduces“**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Beispiele für die Ausführung

Um zu zeigen, wie MapReduce in Data Lake Storage Gen2 ausgeführt wird, finden Sie hier Beispielcode, der mit folgenden Einstellungen in einem Cluster ausgeführt wurde:

* D14v2 mit 16 Knoten
* Hadoop-Cluster mit HDI 3.6

Im Folgenden finden Sie einige Beispielbefehle zum Ausführen von MapReduce Teragen, Terasort und Teravalidate.  Sie können diese Befehle basierend auf Ihren Ressourcen anpassen.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```