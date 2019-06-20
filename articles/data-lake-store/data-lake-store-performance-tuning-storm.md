---
title: Leitlinien für die Optimierung der Leistung von Azure Data Lake Storage Gen1 Storm | Microsoft Docs
description: Leitlinien für die Optimierung der Leistung von Azure Data Lake Storage Gen1 Storm
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 8066a759cf80be6e9ca232bcd3693a5fa4d2f2f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61436476"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Anleitung für die Leistungsoptimierung für Storm in HDInsight und Azure Data Lake Storage Gen1

Es werden die Faktoren beschrieben, die berücksichtigt werden sollten, wenn Sie die Leistung einer Azure Storm-Topologie optimieren. Beispielsweise ist es wichtig, die Arbeitsschritte der Spouts und Bolts zu verstehen (ob der E/A- bzw. Arbeitsspeicheraufwand hoch ist). In diesem Artikel werden verschiedene Richtlinien der Leistungsoptimierung behandelt, z.B. die Problembehandlung für allgemeine Probleme.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* Einen **Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen1-Konto. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.
* **Ausführung eines Storm-Clusters in Data Lake Storage Gen1**. Weitere Informationen finden Sie unter [Storm in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Richtlinien für die Leistungsoptimierung von Data Lake Storage Gen1**.  Allgemeine Leistungskonzepte finden Sie unter [Anleitung für die Leistungsoptimierung von Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Optimieren der Parallelität der Topologie

Sie können die Leistung unter Umständen verbessern, indem Sie die E/A-Parallelität für Data Lake Storage Gen1 in ein- und ausgehender Richtung erhöhen. Eine Storm-Topologie verfügt über eine Reihe von Konfigurationen, mit denen die Parallelität bestimmt wird:
* Anzahl von Workerprozessen (die Worker sind gleichmäßig auf die VMs verteilt)
* Anzahl von Spout Executor-Instanzen
* Anzahl von Bolt Executor-Instanzen
* Anzahl von Spout-Aufgaben
* Anzahl von Bolt-Aufgaben

Beachten Sie Folgendes, wenn Sie beispielsweise einen Cluster mit vier VMs und vier Workerprozessen, 32 Spout Executors und 32 Spout-Aufgaben sowie 256 Bolt Executors und 512 Bolt-Aufgaben verwenden:

Jeder Supervisor, bei dem es sich um einen Workerknoten handelt, verfügt über einen einzelnen JVM-Workerprozess (Java Virtual Machine). Mit diesem JVM-Prozess werden vier Spout-Threads und 64 Bolt-Threads verwaltet. Innerhalb jedes Threads werden Aufgaben sequenziell ausgeführt. Bei der obigen Konfiguration hat jeder Spout-Thread eine Aufgabe und jeder Bolt-Thread zwei Aufgaben.

Für Storm gilt Folgendes (hier sind die verschiedenen beteiligten Komponenten und ihre Auswirkung auf den Parallelitätsgrad aufgeführt):
* Der Hauptknoten (bei Storm als „Nimbus“ bezeichnet) wird zum Senden und Verwalten von Aufträgen verwendet. Diese Knoten haben keine Auswirkung auf den Parallelitätsgrad.
* Supervisorknoten: In HDInsight entspricht dies einer Workerknoten-Azure-VM.
* Die Workertasks sind Storm-Prozesse, die auf den VMs ausgeführt werden. Jede Workertask entspricht einer JVM-Instanz. Storm verteilt die von Ihnen angegebene Anzahl von Workerprozessen so gleichmäßig wie möglich auf die Workerknoten.
* Spout Executor- und Bolt Executor-Instanzen: Jede Executor-Instanz entspricht einem Thread, der in den Workern (JVMs) ausgeführt wird.
* Storm-Aufgaben: Dies sind logische Aufgaben, die auf diesen Threads jeweils ausgeführt werden. Der Parallelitätsgrad wird hierdurch nicht geändert. Sie sollten also ermitteln, ob Sie mehrere Aufgaben pro Executor benötigen oder ob dies nicht erforderlich ist.

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>Erzielen der besten Leistung für Data Lake Storage Gen1

Bei Verwendung von Data Lake Storage Gen1 erzielen Sie die beste Leistung, wenn Sie wie folgt vorgehen:
* Fügen Sie kleinere Anfügungen zu größeren Paketen zusammen (idealerweise 4 MB).
* Führen Sie so viele gleichzeitige Anforderungen wie möglich durch. Da von jedem Bolt-Thread blockierende Lesevorgänge durchgeführt werden, sollten Sie ca. 8 bis 12 Threads pro Kern verwenden. So werden die NIC und die CPU ausreichend stark ausgelastet. Eine größere VM ermöglicht mehr gleichzeitige Anforderungen.  

### <a name="example-topology"></a>Topologiebeispiel

Angenommen, Sie verwenden einen Cluster mit acht Workerknoten mit einer Azure-VM vom Typ D13v2. Diese VM hat acht Kerne, sodass sich für die acht Workerknoten insgesamt 64 Kerne ergeben.

Angenommen, es werden acht Bolt-Threads pro Kern verwendet. Bei 64 Kernen bedeutet dies, dass wir insgesamt 512 Bolt Executor-Instanzen (also Threads) benötigen. In diesem Fall beginnen wir beispielsweise mit einer JVM pro VM und verwenden hauptsächlich die Threadparallelität in der JVM, um Parallelität zu erzielen. Dies bedeutet, dass wir acht Workertasks (eine pro Azure-VM) und 512 Bolt Executors benötigen. Bei dieser Konfiguration versucht Storm, die Worker gleichmäßig auf die Workerknoten (auch als Supervisorknoten bezeichnet) zu verteilen und jedem Workerknoten eine JVM zuzuordnen. Innerhalb der Supervisor versucht Storm, die Executors gleichmäßig auf die Supervisor zu verteilen, sodass jeder Supervisor (also jede JVM) acht Threads aufweist.

## <a name="tune-additional-parameters"></a>Optimieren zusätzlicher Parameter
Nachdem Sie über die grundlegende Topologie verfügen, können Sie überlegen, ob Sie Parameter optimieren möchten:
* **Anzahl von JVMs pro Workerknoten:** Wenn Sie eine große Datenstruktur haben (z.B. eine Suchtabelle), die Sie im Arbeitsspeicher hosten, wird für jede JVM eine separate Kopie benötigt. Alternativ dazu können Sie die Datenstruktur über viele Threads hinweg nutzen, wenn Sie eine geringere Zahl von JVMs verwenden. Für den Ein-/Ausgang des Bolts macht die Anzahl von JVMs keinen so großen Unterschied wie die Anzahl von Threads aus, die für diese JVMs hinzugefügt werden. Der Einfachheit halber ist es ratsam, eine JVM pro Worker zu verwenden. Aber je nachdem, was der Zweck Ihres Bolts ist oder welche Anwendungsverarbeitung Sie benötigen, müssen Sie diese Anzahl ggf. ändern.
* **Anzahl von Spout Executors:** Da im vorherigen Beispiel Bolts zum Schreiben in Data Lake Storage Gen1 verwendet werden, ist die Anzahl von Spouts für die Bolt-Leistung nicht direkt relevant. Je nach Verarbeitungs- oder E/A-Aufwand im Spout kann es ratsam sein, die Spouts zu optimieren, um die beste Leistung zu erzielen. Achten Sie darauf, dass Sie über genügend Spouts verfügen, um die Bolts auszulasten. Die Ausgaberaten der Spouts sollten mit dem Durchsatz der Bolts übereinstimmen. Die tatsächliche Konfiguration hängt vom Spout ab.
* **Anzahl von Aufgaben:** Jeder Bolt wird als einzelner Thread ausgeführt. Weitere Aufgaben pro Bolt führen nicht zu einer Erhöhung der Parallelität. Es ergibt sich nur dann ein Vorteil, wenn Ihr Prozess zur Bestätigung des Tupels einen Großteil Ihrer Bolt-Ausführungsdauer einnimmt. Wir empfehlen, viele Tupel in einem größeren Anfügepaket zu gruppieren, bevor Sie eine Bestätigung vom Bolt senden. In den meisten Fällen führen mehrere Aufgaben also nicht zu weiteren Vorteilen.
* **Lokale oder Shuffle-Gruppierung:** Wenn diese Einstellung aktiviert ist, werden Tupel in demselben Workerprozess an Bolts gesendet. Auf diese Weise werden die prozessübergreifende Kommunikation und Netzwerkaufrufe reduziert. Dies wird für die meisten Topologien empfohlen.

Dieses einfache Szenario ist ein guter Ausgangspunkt. Führen Sie einen Test mit Ihren eigenen Daten durch, um die vorherigen Parameter zu optimieren und so eine optimale Leistung zu erzielen.

## <a name="tune-the-spout"></a>Optimieren des Spouts

Sie können die folgenden Einstellungen ändern, um den Spout zu optimieren.

- **Tupeltimeout: topology.message.timeout.secs**: Mit dieser Einstellung wird ermittelt, wie viel Zeit eine Nachricht für den Abschluss des Vorgangs und den Erhalt der Bestätigung benötigen darf, bevor ein Fehler auftritt.

- **Maximaler Arbeitsspeicher pro Workerprozess: worker.childopts**: Mit dieser Einstellung können Sie weitere Befehlszeilenparameter für die Java-Worker angeben. Die am häufigsten verwendete Einstellung hier ist XmX, mit der bestimmt wird, wie viel Arbeitsspeicher dem Heap einer JVM maximal zugeordnet wird.

- **Max. ausstehende Spouts: topology.max.spout.pending**: Mit dieser Einstellung wird die Anzahl von Tupeln bestimmt, die pro Spout-Thread jeweils aktiv sein können (noch nicht auf allen Knoten der Topologie bestätigt).

  Eine hilfreiche Berechnung ist die Schätzung, wie groß die einzelnen Tupel sind. Ermitteln Sie anschließend, über wie viel Arbeitsspeicher ein Spout-Thread verfügt. Wenn Sie den gesamten Arbeitsspeicher, der einem Thread zugeordnet ist, durch diesen Wert teilen, sollten Sie die Obergrenze für den Parameter zur Bestimmung der maximalen Anzahl von ausstehenden Spouts erhalten.

## <a name="tune-the-bolt"></a>Optimieren des Bolts
Legen Sie beim Schreiben in Data Lake Storage Gen1 eine Größensynchronisierungsrichtlinie (Puffer auf Clientseite) auf 4 MB fest. Anschließend wird nur dann die Leerung oder ein hsync()-Vorgang durchgeführt, wenn die Puffergröße auf diesen Wert festgelegt ist. Der Data Lake Storage Gen1-Treiber auf der Worker-VM führt diese Pufferung automatisch durch, sofern Sie nicht explizit einen hsync()-Vorgang durchführen.

Der Data Lake Storage Gen1 Storm-Standardbolt verfügt über einen Parameter für die Richtlinie zur Größensynchronisierung (fileBufferSize), den Sie zum Optimieren dieses Parameters verwenden können.

Bei Topologien mit hohem E/A-Aufwand ist es ratsam, dass jeder Bolt-Thread in seine eigene Datei schreibt und dass eine Dateirotationsrichtlinie (fileRotationSize) festgelegt wird. Wenn die Datei eine bestimmte Größe erreicht, wird der Datenstrom automatisch geleert, und es wird in eine neue Datei geschrieben. Die empfohlene Dateigröße für die Rotation ist 1 GB.

### <a name="handle-tuple-data"></a>Behandeln von Tupeldaten

In Storm hält ein Spout ein Tupel vor, bis es vom Bolt explizit bestätigt wird. Wenn ein Tupel vom Bolt gelesen, aber noch nicht bestätigt wurde, wurde der Spout unter Umständen nicht dauerhaft in das Data Lake Storage Gen1-Back-End übernommen. Nachdem ein Tupel bestätigt wurde, kann für den Spout vom Bolt die Beibehaltung garantiert werden, und anschließend können die Quelldaten von der jeweiligen Quelle, von der gelesen wird, gelöscht werden.  

Für Data Lake Storage Gen1 erzielen Sie die beste Leistung, wenn für den Bolt eine Tupeldatenmenge von 4 MB gepuffert wird. Führen Sie den Schreibvorgang auf das Data Lake Storage Gen1-Back-End dann als einen 4-MB-Vorgang durch. Nachdem die Daten erfolgreich in den Speicher geschrieben wurden (durch Aufruf von „hflush()“), kann der Bolt die Daten gegenüber dem Spout bestätigen. Dies ist die Vorgehensweise des hier angegebenen Beispiel-Bolts. Es ist auch akzeptabel, eine größere Anzahl von Tupeln vorzuhalten, bevor ein hflush()-Aufruf durchgeführt wird und die Tupel bestätigt werden. Hierdurch wird aber die Anzahl von In-Flight-Tupeln erhöht, die vom Spout vorgehalten werden müssen, sodass sich die Menge des erforderlichen Arbeitsspeichers pro JVM erhöht.

> [!NOTE]
> Anwendungen verfügen ggf. über die Anforderung, die Bestätigung von Tupeln aus anderen nicht leistungsbezogenen Gründen häufiger durchzuführen (bei Datengrößen von weniger als 4 MB). Dies kann sich aber auf den E/A-Durchsatz des Speicher-Back-Ends auswirken. Wägen Sie diesen Nachteil sorgfältig gegenüber der E/A-Leistung des Bolts ab.

Falls die Eingangsrate der Tupel nicht sonderlich hoch ist und es daher lange dauert, bis der 4-MB-Puffer gefüllt ist, können Sie dies ggf. wie folgt lösen:
* Reduzieren Sie die Anzahl von Bolts, damit weniger Puffer gefüllt werden müssen.
* Verwenden Sie eine Richtlinie auf Zeit- oder Anzahlbasis, bei der jeweils nach x Leerungsvorgängen oder y Millisekunden ein hflush()-Vorgang ausgelöst wird und die bisher angesammelten Tupel bestätigt werden.

Beachten Sie, dass der Durchsatz in diesem Fall geringer ist. Bei einer niedrigen Ereignisrate ist der maximale Durchsatz aber sowieso nicht das oberste Ziel. Mit diesen Lösungsansätzen können Sie die Gesamtzeit reduzieren, die ein Tupel für das Durchlaufen des Speichers benötigt. Dies kann eine Rolle spielen, wenn Sie auch bei einer geringen Ereignisrate eine Echtzeitpipeline verwenden möchten. Beachten Sie auch, dass Sie den Parameter „topology.message.timeout_secs“ ebenfalls anpassen sollten, wenn die Eingangsrate der Tupel niedrig ist. So tritt für die Tupel keine Zeitüberschreitung auf, während sie gepuffert oder verarbeitet werden.

## <a name="monitor-your-topology-in-storm"></a>Überwachen Ihrer Topologie in Storm  
Während der Topologieausführung können Sie sie über die Storm-Benutzeroberfläche überwachen. Die wichtigsten Parameter sind:

* **Total process execution latency** (Gesamtwartezeit der Prozessausführung): Dies ist die durchschnittliche Zeit, die benötigt wird, bis ein Tupel vom Spout ausgegeben, vom Bolt verarbeitet und dann bestätigt wird.

* **Total bolt process latency** (Gesamtwartezeit des Boltprozesses): Gibt an, wie lange sich der Tupel im Durchschnitt auf dem Bolt befindet, bis eine Bestätigung eingeht.

* **Total bolt execute latency** (Gesamtwartezeit der Boltausführung): Gibt an, wie lange sich der Bolt im Durchschnitt in der execute-Methode befindet.

* **Number of failures** (Anzahl von Fehlern): Gibt die Anzahl von Tupeln an, die vor dem Eintreten der Zeitüberschreitung nicht vollständig verarbeitet werden konnten.

* **Capacity** (Kapazität): Dies ist eine Kennzahl dafür, wie ausgelastet Ihr System ist. Wenn diese Zahl 1 ist, arbeiten Ihre Bolts so schnell, wie es geht. Erhöhen Sie die Parallelität, wenn der Wert kleiner als 1 ist. Verringern Sie die Parallelität, wenn der Wert größer als 1 ist.

## <a name="troubleshoot-common-problems"></a>Behandeln gängiger Probleme
Hier sind einige gängige Problembehandlungsszenarien aufgeführt:
* **Für viele Tupel treten Timeouts auf**: Sehen Sie sich die einzelnen Knoten der Topologie an, um zu ermitteln, wo sich der Engpass befindet. Der häufigste Grund ist, dass die Bolts nicht mit den Spouts mithalten können. Dies führt dazu, dass Tupel die internen Puffer verstopfen, während sie auf ihre Verarbeitung warten. Sie können erwägen, den Wert für die Zeitüberschreitung zu erhöhen oder die Anzahl von maximal ausstehenden Spouts zu verringern.

* **Lange Wartezeit für die Ausführung des Gesamtprozesses, aber eine kurze Wartezeit für den Boltprozess**: In diesem Fall ist es möglich, dass die Tupel nicht schnell genug bestätigt werden. Überprüfen Sie, ob eine ausreichende Anzahl von Bestätigungseinheiten vorhanden ist. Eine andere Möglichkeit besteht darin, dass sie sich zu lange in der Warteschlange befinden, bevor die Bolts mit der Verarbeitung beginnen. Verringern Sie die maximale Anzahl von ausstehenden Spouts.

* **Lange Wartezeit für die Boltausführung**: Dies bedeutet, dass die execute()-Methode Ihres Bolts zu lange dauert. Optimieren Sie den Code, oder sehen Sie sich die Schreibgrößen und das Leerungsverhalten an.

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage Gen1-Einschränkung
Wenn Sie die Data Lake Storage Gen1-Grenzwerte für die Bandbreite erreichen, kommt es unter Umständen zu Aufgabenfehlern. Überprüfen Sie die Aufgabenprotokolle auf Drosselungsfehler. Sie können die Parallelität verringern, indem Sie die Containergröße erhöhen.    

Um zu prüfen, ob eine Drosselung vorliegt, müssen Sie die Debugprotokollierung auf Clientseite aktivieren:

1. Ändern Sie unter **Ambari** > **Storm** > **Config** > **Advanced storm-worker-log4j** den Eintrag **&lt;root level="info"&gt;** in **&lt;root level="debug"&gt;** . Starten Sie alle Knoten und Dienste neu, damit die Konfiguration wirksam wird.
2. Überwachen Sie die Protokolle der Storm-Topologie auf Workerknoten (unter „/var/log/storm/worker-artifacts/&lt;TopologyName&gt;/&lt;port&gt;/worker.log“) auf Data Lake Storage Gen1-Einschränkungsausnahmen.

## <a name="next-steps"></a>Nächste Schritte
Informationen zur weiteren Leistungsoptimierung für Storm finden Sie in [diesem Blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Ein weiteres Beispiel, das Sie ausführen können, finden Sie [auf GitHub](https://github.com/hdinsight/storm-performance-automation).
