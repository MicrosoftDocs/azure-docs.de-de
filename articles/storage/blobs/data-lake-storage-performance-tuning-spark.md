---
title: 'Optimieren der Leistung: Spark, HDInsight und Azure Data Lake Storage Gen2 | Microsoft-Dokumentation'
description: Informieren Sie sich über die Richtlinien zur Optimierung der Leistung von Spark mit Azure HDInsight und Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: c99d57ddd86ecff71c35ad6c0f2c2561e279b4b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912806"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Optimieren der Leistung: Spark, HDInsight und Azure Data Lake Storage Gen2

Beim Optimieren der Leistung in Spark müssen Sie die Anzahl von Apps berücksichtigen, die in Ihrem Cluster ausgeführt werden sollen.  Standardmäßig können Sie bis zu 4 Apps gleichzeitig in einem HDI-Cluster ausführen (Hinweis: Die Standardeinstellung wird in Zukunft geändert).  Wenn Sie weniger Apps verwenden möchten, setzen Sie die Standardeinstellungen außer Kraft, und verwenden Sie einen größeren Teil des Clusters für diese Apps.  

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen2-Konto**. Anweisungen zum Erstellen eines solchen Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](../common/storage-account-create.md).
* Einen **Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen2-Konto. Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.
* **Ausführen eines Spark-Clusters für Azure Data Lake Storage Gen2**.  Weitere Informationen finden Sie unter [Verwenden des HDInsight Spark-Clusters, um Daten in Data Lake Storage Gen2 zu analysieren](../../hdinsight/spark/apache-spark-use-with-data-lake-store.md).
* **Leitlinien für die Leistungsoptimierung von Data Lake Storage Gen2**.  Allgemeine Leistungskonzepte finden Sie unter [Leitfaden für die Leistungsoptimierung von Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md). 

## <a name="parameters"></a>Parameter

Hier finden Sie die wichtigsten Einstellungen, die optimiert werden können, um die Leistung von Azure Data Lake Storage Gen2 beim Ausführen von Spark-Aufträgen zu verbessern:

* **Num-executors**: Die Anzahl von gleichzeitigen Tasks, die ausgeführt werden können

* **Executor-memory**: Die Arbeitsspeichermenge, die jedem Executor zugeordnet ist

* **Executor-cores**: Die Anzahl von Kernen, die jedem Executor zugeordnet sind                     

**Num-executors**: Dieser Parameter legt die maximale Anzahl von Tasks fest, die parallel ausgeführt werden können.  Die tatsächliche Anzahl von Tasks, die parallel ausgeführt werden können, wird durch den Arbeitsspeicher und die CPU-Ressourcen Ihres Clusters begrenzt.

**Executor-memory**: Dies ist die Arbeitsspeichermenge, die jedem Executor zugeordnet wird.  Der Arbeitsspeicherbedarf für jeden Executor richtet sich nach dem Auftrag.  Bei komplexen Vorgängen muss der Arbeitsspeicher höher sein.  Bei einfachen Vorgänge wie Lese- und Schreibvorgängen ist der Arbeitsspeicherbedarf niedriger.  Die Arbeitsspeichermenge für jeden Executor kann in Ambari angezeigt werden.  Navigieren Sie in Ambari zu Spark, und zeigen Sie die Registerkarte für die Konfiguration an.  

**Executor-cores**: Dieser Parameter legt fest, wie viele Kerne pro Executor verwendet werden. Dadurch wird die Anzahl von parallelen Threads festgelegt, die pro Executor ausgeführt werden können.  Wenn der Wert für „executor-cores“ 2 beträgt, kann jeder Executor 2 parallele Tasks ausführen.  Die erforderliche Menge an Executorkernen richtet sich nach dem Auftrag.  E/A-intensive Aufträge erfordern keine große Menge an Arbeitsspeicher pro Task, daher kann jeder Executor mehr parallele Tasks verarbeiten.

Standardmäßig sind bei der Ausführung von Spark in HDInsight für jeden physischen Kern zwei virtuelle YARN-Kerne definiert.  Diese Zahl ermöglicht eine gute Balance zwischen Parallelität und der Menge an Kontextwechseln bei mehreren Threads.  

## <a name="guidance"></a>Anleitungen

Beim Ausführen von Spark-Analyseworkloads zum Arbeiten mit Daten in Data Lake Storage Gen2 empfiehlt sich die Verwendung der neuesten HDInsight-Version, um die beste Leistung mit Data Lake Storage Gen2 zu erzielen. Wenn Ihr Auftrag E/A-intensiver ist, können bestimmte Parameter konfiguriert werden, um die Leistung zu verbessern.  Data Lake Storage Gen2 ist eine hochgradig skalierbare Speicherplattform, die einen hohen Durchsatz verarbeiten kann.  Wenn ein Auftrag hauptsächlich aus Lese- oder Schreibvorgängen besteht, kann die Leistung durch Erhöhen der Parallelität für die Eingabe in und Ausgabe aus Data Lake Storage Gen2 gesteigert werden.

Es gibt einige allgemeine Möglichkeiten, die Parallelität für E/A-intensive Aufträge zu erhöhen.

**Schritt 1: Ermitteln, wie viele Apps im Cluster ausgeführt werden**: Sie sollten wissen, wie viele Apps einschließlich der aktuellen im Cluster ausgeführt werden.  Bei den Standardwerten für jede Spark-Einstellung wird davon ausgegangen, dass vier Apps gleichzeitig ausgeführt werden.  Daher stehen Ihnen nur 25% des Clusters für jede App zur Verfügung.  Um eine bessere Leistung zu erzielen, können Sie die Standardwerte außer Kraft setzen, indem Sie die Anzahl von Executors ändern.  

**Schritt 2: Festlegen von „executor-memory“** : Die erste festzulegende Option ist der Arbeitsspeicher für die Executors.  Der Arbeitsspeicher hängt von dem Auftrag ab, den Sie ausführen möchten.  Sie können die Parallelität erhöhen, indem Sie weniger Arbeitsspeicher pro Executor zuweisen.  Wenn beim Ausführen des Auftrags Ausnahmen wegen unzureichenden Arbeitsspeichers angezeigt werden, sollten Sie den Wert für diesen Parameter erhöhen.  Eine Möglichkeit besteht darin, mehr Arbeitsspeicher zur Verfügung zu stellen, indem Sie einen Cluster verwenden, der über mehr Arbeitsspeicher verfügt, oder indem Sie Ihren Cluster vergrößern.  Eine größere Menge an Arbeitsspeicher ermöglicht die Verwendung einer größeren Anzahl von Executors, was wiederum mehr Parallelität bedeutet.

**Schritt 3: Festlegen von „executor-cores“** : Für E/A-intensive Workloads ohne komplexe Vorgänge empfiehlt es sich, mit einer großen Anzahl von Executorkernen zu beginnen, um die Anzahl von parallelen Tasks pro Executor zu erhöhen.  Das Festlegen von vier Executorkernen ist ein guter Ausgangspunkt.   

executor-cores = 4

Durch Erhöhen der Anzahl von Executorkernen steigt die Parallelität, sodass Sie mit verschiedenen Executorkernen experimentieren können.  Bei Aufträgen mit komplexeren Vorgängen sollten Sie die Anzahl von Kernen pro Executor reduzieren.  Wenn der Wert für „executor-cores“ höher als 4 ist, wird die Garbage Collection möglicherweise ineffizient und senkt die Leistung.

**Schritt 4: Ermitteln der YARN-Arbeitsspeichermenge im Cluster**: Diese Informationen sind in Ambari verfügbar.  Navigieren Sie zu YARN, und zeigen Sie die Registerkarte für die Konfiguration an.  Die Größe des YARN-Arbeitsspeichers wird in diesem Fenster angezeigt.  
Beachten Sie, dass auch die Größe des YARN-Standardcontainers in diesem Fenster angezeigt wird.  Die YARN-Containergröße entspricht dem Parameter für die Menge des Arbeitsspeichers pro Executor.

YARN-Arbeitsspeicher gesamt = Knoten × YARN-Arbeitsspeicher pro Knoten

**Schritt 5: Berechnen von „num-executors“**

**Berechnen der Arbeitsspeicherbeschränkung**: Der Parameter „num-executors“ wird entweder durch den Arbeitsspeicher oder durch die CPU beschränkt.  Die Speicherbeschränkung wird durch die Menge des verfügbaren YARN-Arbeitsspeichers für Ihre Anwendung bestimmt.  Teilen Sie den YARN-Gesamtarbeitsspeicher durch „executor-memory“.  Die Beschränkung muss an die Anzahl von Apps angepasst werden, daher wird durch die Anzahl von Apps dividiert.

Arbeitsspeicherbeschränkung = (YARN-Arbeitsspeicher gesamt / Executor-Arbeitsspeicher) / Anzahl von Apps

**Berechnen der CPU-Beschränkung**: Die CPU-Beschränkung wird als Gesamtanzahl virtueller Kerne dividiert durch die Anzahl von Kernen pro Executor berechnet.  Für jeden physischen Kern gibt es zwei virtuelle Kerne.  Ähnlich wie bei der Arbeitsspeicherbeschränkung muss auch hier durch die Anzahl von Apps dividiert werden.

- Virtuelle Kerne = (Knoten im Cluster × Anzahl physischer Kerne im Knoten × 2)
- CPU-Einschränkung = (Gesamtanzahl virtueller Kerne / Anzahl der Kerne pro Executor) / Anzahl der Apps

**Festlegen von „num-executors“** : Der Parameter „num-executors“ wird durch die Mindestwerte für die Arbeitsspeicher- und CPU-Beschränkung bestimmt. 

num-executors = Min (Gesamtanzahl virtueller Kerne / Anzahl der Kerne pro Executor, verfügbarer YARN-Arbeitsspeicher / executor-memory)

Durch Festlegen eines höheren Werts für „num-executors“ wird die Leistung nicht unbedingt erhöht.  Denken Sie daran, dass beim Hinzufügen weiterer Executors auch zusätzlicher Overhead für jeden zusätzlichen Executor entsteht, wodurch die Leistung beeinträchtigt werden kann.  Der Wert für „num-executors“ ist durch die Clusterressourcen begrenzt.    

## <a name="example-calculation"></a>Beispielberechnung

Annahme: Sie haben zurzeit einen Cluster aus acht D4v2-Knoten, in dem zwei Apps ausgeführt werden – einschließlich der App, die Sie ausführen möchten.  

**Schritt 1: Ermitteln, wie viele Apps im Cluster ausgeführt werden**: Sie wissen, dass im Cluster zwei Apps ausgeführt werden – einschließlich der App, die Sie ausführen möchten.  

**Schritt 2: Festlegen von „executor-memory“** : Für dieses Beispiel legen wir fest, dass ein Wert von 6 GB für „executor-memory“ für einen E/A-intensiven Auftrag ausreichend ist.  

executor-memory = 6 GB

**Schritt 3: Festlegen von „executor-cores“** : Da es sich um einen E/A-intensiven Auftrag handelt, können wir die Anzahl von Kernen für jeden Executor auf 4 festlegen.  Wenn mehr als vier Kerne pro Executor festgelegt werden, können Probleme mit der Garbage Collection auftreten.  

executor-cores = 4

**Schritt 4: Ermitteln der YARN-Arbeitsspeichermenge im Cluster**: Wir wechseln zu Ambari und ermitteln, dass jeder D4v2-Knoten über 25 GB YARN-Arbeitsspeicher verfügt.  Da acht Knoten vorhanden sind, wird der verfügbare YARN-Arbeitsspeicher mit 8 multipliziert.

- YARN-Arbeitsspeicher gesamt = Knoten × YARN-Arbeitsspeicher* pro Knoten
- YARN-Arbeitsspeicher gesamt = 8 Knoten × 25 GB = 200 GB

**Schritt 5: Berechnen von „num-executors“** : Der Parameter „num-executors“ wird ermittelt, indem die Mindestwerte für die Arbeitsspeicher- und CPU-Beschränkung dividiert durch die Anzahl von in Spark ausgeführten Apps verwendet werden.    

**Berechnen der Arbeitsspeicherbeschränkung**: Die Arbeitsspeicherbeschränkung wird als YARN-Gesamtarbeitsspeicher dividiert durch die Arbeitsspeichermenge pro Executor berechnet.

- Arbeitsspeicherbeschränkung = (YARN-Arbeitsspeicher gesamt / Executor-Arbeitsspeicher) / Anzahl von Apps
- Arbeitsspeicherbeschränkung = (200 GB/6 GB) / 2
- Arbeitsspeicherbeschränkung = 16 (gerundet)

**Berechnen der CPU-Beschränkung**: Die CPU-Beschränkung wird als Gesamtanzahl der YARN-Kerne dividiert durch die Anzahl von Kernen pro Executor berechnet.

- YARN-Kerne = Knoten im Cluster × Anzahl der Kerne pro Knoten × 2
- YARN-Kerne = 8 Knoten × 8 Kerne pro D14 × 2 = 128
- CPU-Einschränkung = (Gesamtanzahl der YARN-Kerne / Anzahl der Kerne pro Executor) / Anzahl der Apps
- CPU-Einschränkung = (128 / 4) / 2
- CPU-Einschränkung = 16

**Festlegen von „num-executors“**

- num-executors = Min (Arbeitsspeicherbeschränkung, CPU-Beschränkung)
- num-executors = Min (16, 16)
- num-executors = 16