---
title: Leistungsoptimierung für Apache Kafka HDInsight-Cluster
description: Überblick über Techniken zum Optimieren von Apache Kafka-Workloads in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 8226d1f49b8ba73870dba009e97ff2718a0eee27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689356"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Leistungsoptimierung für Apache Kafka HDInsight-Cluster

Dieser Artikel enthält Vorschläge zum Optimieren der Leistung Ihrer Apache Kafka-Workloads in HDInsight. Der Schwerpunkt liegt auf dem Anpassen der Producer- und Brokerkonfiguration. Es gibt verschiedene Möglichkeiten zum Messen der Leistung. Die Optimierungen, die Sie anwenden, hängen von Ihren geschäftlichen Anforderungen ab.

## <a name="architecture-overview"></a>Übersicht über die Architektur

Kafka-Themen dienen zum Organisieren von Datensätzen. Datensätze werden von Producern erstellt und von Consumern genutzt. Producer senden Datensätze an Kafka-Broker, die die Daten anschließend speichern. Jeder Workerknoten in Ihrem HDInsight-Cluster ist ein Kafka-Broker.

Themen partitionieren Datensätze Broker übergreifend. Bei der Nutzung von Datensätzen können Sie bis zu einen Consumer pro Partition einsetzen, um parallele Verarbeitung der Daten zu erzielen.

Replikation kommt zum Einsatz, um Partitionen auf Knoten zu duplizieren. Dies dient als Schutz vor Ausfällen von Knoten (Brokern). Eine einzelne Partition in der Gruppe der Replikate wird als übergeordnete Partition festgelegt. Producer-Datenverkehr wird an die führende Komponente jedes Knotens weitergeleitet, indem der von ZooKeeper verwaltete Zustand verwendet wird.

## <a name="identify-your-scenario"></a>Identifizieren des Szenarios

Hinsichtlich Leistung von Apache Kafka gibt es zwei Hauptaspekte: Durchsatz und Latenz. Durchsatz ist die maximale Rate, mit der Daten verarbeitet werden können. Ein hoher Durchsatz ist in der Regel besser. Latenz ist der Zeitaufwand für die Speicherung oder den Abruf von Daten. Eine geringe Latenz ist in der Regel besser. Die richtige Balance zwischen Durchsatz, Latenz und den Kosten der Anwendungsinfrastruktur zu finden, kann eine echte Herausforderung sein. Ihre Leistungsanforderungen entsprechen mit hoher Wahrscheinlichkeit einer der folgenden drei gängigen Situationen, je nachdem, ob Sie hohen Durchsatz, geringe Latenz oder beides benötigen:

* Hoher Durchsatz/Geringe Latenz. Dieses Szenario erfordert sowohl hohen Durchsatz als auch geringe Latenz (~100 Millisekunden). Ein Beispiel für diesen Anwendungstyp ist die Überwachung der Verfügbarkeit von Diensten.
* Hoher Durchsatz/Hohe Latenz. Dieses Szenario erfordert einen hohen Durchsatz (~1,5 Gbit/s), toleriert jedoch eine höhere Latenz (< 250 ms). Ein Beispiel für diese Art von Anwendung ist die Erfassung von Telemetriedaten für echtzeitnahe Prozesse wie in Anwendungen für Sicherheit und Angriffserkennung.
* Niedriger Durchsatz/Geringe Latenz. Dieses Szenario erfordert eine geringe Latenz (< 10 ms), kann jedoch einen niedrigeren Durchsatz tolerieren. Ein Beispiel für diesen Anwendungstyp ist die Onlineprüfung von Rechtschreibung und Grammatik.

## <a name="producer-configurations"></a>Konfigurationen von Producern

In den folgenden Abschnitten werden einige der wichtigsten Konfigurationseigenschaften zur Optimierung der Leistung Ihrer Kafka-Producer vorgestellt. Eine ausführliche Erläuterung aller Konfigurationseigenschaften finden Sie in der [Apache Kafka-Dokumentation zu Producerkonfigurationen](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Batchgröße

Apache Kafka-Producer stellen Gruppen von Nachrichten (sog. Batches) zusammen, die als Einheit gesendet und in einer einzigen Speicherpartition gespeichert werden. Die Batchgröße bezeichnet die Anzahl der Bytes, die vorhanden sein muss, ehe diese Gruppe übertragen wird. Die Erhöhung des Parameters `batch.size` kann den Durchsatz steigern, da der Verarbeitungsaufwand für Netzwerk- und E/A-Anforderungen reduziert wird. Bei geringer Last kann eine erhöhte Batchgröße die Sendelatenz von Kafka erhöhen, da der Producer darauf wartet, dass ein Batch fertig wird. Bei hoher Last wird empfohlen, die Batchgröße zu erhöhen, um Durchsatz und Latenz zu verbessern.

### <a name="producer-required-acknowledgements"></a>Vom Producer angeforderte Bestätigungen

Die vom Producer geforderte Konfiguration von `acks` bestimmt die Anzahl der Bestätigungen, die die übergeordnete Partition verlangt, ehe eine Schreibanforderung als abgeschlossen gilt. Diese Einstellung wirkt sich auf die Datenzuverlässigkeit aus und lässt die Werte `0`, `1` oder `-1` zu. Der Wert `-1` bedeutet, dass von allen Replikaten eine Bestätigung empfangen werden muss, bevor der Schreibvorgang abgeschlossen ist. Die Einstellung `acks = -1` bietet mehr Garantien gegen Datenverlust, führt aber auch zu mehr Latenz und weniger Durchsatz. Wenn Ihre Anwendung einen höheren Durchsatz erfordert, legen Sie `acks = 0` oder `acks = 1` fest. Beachten Sie, dass wenn nicht alle Replikate bestätigt werden, die Datenzuverlässigkeit abnehmen kann.

### <a name="compression"></a>Komprimierung

Ein Kafka-Producer kann so konfiguriert werden, dass Nachrichten komprimiert werden, ehe sie an Broker gesendet werden. Die Einstellung `compression.type` gibt den Komprimierungscodec an, der verwendet werden soll. Unterstützte Komprimierungscodecs sind „gzip“, „snappy“ und „lz4“. Die Komprimierung ist vorteilhaft und sollte erwogen werden, wenn die Datenträgerkapazität limitiert ist.

Bei den beiden häufig verwendeten Komprimierungscodecs `gzip` und `snappy` hat `gzip` ein höheres Komprimierungsverhältnis, was zu einer geringeren Datenträgerauslastung auf Kosten einer höheren CPU-Last führt. Der Codec `snappy` bietet weniger Komprimierung bei weniger CPU-Aufwand. Sie können basierend auf den Einschränkungen des Datenträgers des Brokers oder der CPU des Producers entscheiden, welchen Codec Sie verwenden möchten. `gzip` bietet eine fünfmal höhere Komprimierungsrate als `snappy`.

Die Datenkomprimierung erhöht die Anzahl der Datensätze, die auf einem Datenträger gespeichert werden können. Sie kann auch den CPU-Aufwand erhöhen, wenn die vom Producer und Broker verwendeten Komprimierungsformate nicht übereinstimmen. Denn die Daten müssen vor dem Senden komprimiert und vor der Verarbeitung dekomprimiert werden.

## <a name="broker-settings"></a>Brokereinstellungen

In den folgenden Abschnitten werden einige der wichtigsten Einstellungen zur Optimierung der Leistung Ihrer Kafka-Broker vorgestellt. Eine ausführliche Erläuterung aller Brokereinstellungen finden Sie in der [Apache Kafka-Dokumentation zu Brokerkonfigurationen](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Anzahl der Datenträger

Speicherdatenträger bieten begrenzte Kapazität für IOPS (Ein-/Ausgabevorgänge pro Sekunde) und Lese-/Schreibvorgänge von Bytes pro Sekunde. Beim Erstellen neuer Partitionen speichert Kafka jede neue Partition auf dem Datenträger mit den wenigsten vorhandenen Partitionen, um sie gleichmäßig auf die verfügbaren Datenträger zu verteilen. Trotz Speicherungsstrategie kann Kafka bei der Verarbeitung von Hunderten von Partitionsreplikaten auf jedem Datenträger den verfügbaren Datenträgerdurchsatz leicht komplett ausschöpfen. Zwischen Durchsatz und Kosten liegt hier der Kompromiss. Wenn Ihre Anwendung mehr Durchsatz erfordert, erstellen Sie einen Cluster mit mehr verwalteten Datenträgern pro Broker. HDInsight unterstützt derzeit nicht das Hinzufügen verwalteter Datenträger zu einem aktiven Cluster. Weitere Informationen zum Konfigurieren der Anzahl verwalteter Datenträger finden Sie unter [Konfigurieren von Speicher und Skalierbarkeit von Apache Kafka in HDInsight](apache-kafka-scalability.md). Machen Sie sich damit vertraut, wie sich die Vergrößerung des Speicherplatzes für die Knoten in Ihrem Cluster auf die Kosten auswirkt.

### <a name="number-of-topics-and-partitions"></a>Anzahl von Themen und Partitionen

Kafka-Producer schreiben Daten in Themen. Kafka-Consumer lesen Daten aus Themen. Einem Thema ist ein Protokoll zugeordnet, das eine Datenstruktur auf dem Datenträger darstellt. Kafka fügt Datensätze von Producern am Ende eines Themenprotokolls an. Ein Themenprotokoll besteht aus vielen Partitionen, die auf mehrere Dateien verteilt sind. Diese Dateien sind wiederum auf mehrere Kafka-Clusterknoten verteilt. Consumer lesen mit der gewählten Taktung Daten aus Kafka-Themen und können ihre Position (Offset) im Themenprotokoll wählen.

Jede Kafka-Partition ist eine Protokolldatei im System. Producerthreads können gleichzeitig in mehrere Protokolle schreiben. Da jeder Consumerthread Nachrichten aus einer Partition liest, wird auch die Nutzung von Daten aus mehreren Partitionen parallel verarbeitet.

Die Erhöhung der Partitionsdichte (also der Anzahl von Partitionen pro Broker) führt zu mehr Verarbeitungsaufwand im Zusammenhang mit Metadatenvorgängen und pro Partitionsanfordung/-antwort zwischen der übergeordneten Partition und ihren untergeordneten Partitionen. Selbst wenn keine Daten durchfließen, rufen Partitionsreplikate dennoch Daten aus der übergeordneten Partition ab, was zu einer zusätzlichen Verarbeitung von Sende- und Empfangsanforderungen über das Netzwerk führt.

Für Apache Kafka-Cluster ab Version 1.1 in HDInsight empfehlen wir maximal 1.000 Partitionen pro Broker einschließlich Replikaten. Die Erhöhung der Partitionsanzahl pro Broker verringert den Durchsatz und kann auch zur Nichtverfügbarkeit von Themen führen. Weitere Informationen zur Unterstützung von Kafka-Partitionen finden Sie im [offiziellen Apache Kafka-Blogbeitrag zur Erhöhung der Anzahl unterstützter Partitionen in Version 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Einzelheiten zum Ändern von Themen finden Sie unter [Apache Kafka: Modifying topics](https://kafka.apache.org/documentation/#basic_ops_modify_topic) (Apache Kafka: Ändern von Themen).

### <a name="number-of-replicas"></a>Anzahl von Replikaten

Ein höherer Replikationsfaktor führt zu zusätzlichen Anforderungen zwischen der übergeordneten und den untergeordneten Partitionen. Folglich beansprucht ein höherer Replikationsfaktor mehr Speicherplatz und CPU-Leistung, um zusätzliche Anforderungen zu verarbeiten, was die Schreiblatenz erhöht und den Durchsatz verringert.

Wir empfehlen, in Azure HDInsight mindestens die Dreifachreplikation für Kafka zu verwenden. Die meisten Azure-Regionen haben drei Fehlerdomänen, aber in Regionen mit nur zwei Fehlerdomänen sollten Benutzer die Vierfachreplikation verwenden.

Weitere Informationen zur Replikation finden Sie unter [Apache Kafka: Replication](https://kafka.apache.org/documentation/#replication) (Apache Kafka: Replikation) und [Apache Kafka: Increasing replication factor](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor) (Apache Kafka: Erhöhen des Replikationsfaktors).

## <a name="next-steps"></a>Nächste Schritte

* [Processing trillions of events per day with Apache Kafka on Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/) (Verarbeiten von Billionen von Ereignissen pro Tag mit Apache Kafka unter Azure)
* [Was ist Apache Kafka in HDInsight?](apache-kafka-introduction.md)
