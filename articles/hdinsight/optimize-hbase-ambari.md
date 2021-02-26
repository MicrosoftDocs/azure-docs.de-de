---
title: Optimieren von Apache HBase mit Apache Ambari in Azure HDInsight
description: Verwenden Sie die Apache Ambari-Webbenutzeroberfläche zum Konfigurieren und Optimieren von Apache HBase.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 60c9916bc7d7b3b380a332f41924ee744002fd66
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428199"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Optimieren von Apache HBase mit Apache Ambari in Azure HDInsight

Apache Ambari ist eine Webbenutzeroberfläche zum Verwalten und Überwachen von HDInsight-Clustern. Eine Einführung in die Ambari-Webbenutzeroberfläche finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

Die Apache HBase-Konfiguration wird über die Registerkarte **HBase Configs** (HBase-Konfigurationen) geändert. In den folgenden Abschnitten werden einige wichtige Konfigurationseinstellungen beschrieben, die sich auf die HBase-Leistung auswirken.

## <a name="set-hbase_heapsize"></a>Festlegen von HBASE_HEAPSIZE

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Master*, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Mit der HBase-Heapgröße wird die maximale Heapmenge in MB angegeben, die von Servern vom Typ *region* und *master* verwendet werden kann. Der Standardwert ist 1.000 MB. Es ist ratsam, diesen Wert je nach Clusterworkload anzupassen.

1. Navigieren Sie zum Ändern des Werts auf der HBase-Registerkarte **Configs** (Konfigurationen) zum Bereich **Advanced HBase-env** (HBase-env – Erweitert), und suchen Sie nach der Einstellung `HBASE_HEAPSIZE`.

1. Ändern Sie den Standardwert in 5.000 MB.

    ![Apache Ambari – Heapsize von HBase-Speicher](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Optimieren von Workloads mit vielen Lesevorgängen

Die folgenden Konfigurationen sind wichtig, um die Leistung von Workloads mit vielen Lesevorgängen zu verbessern.

### <a name="block-cache-size"></a>Größe des Blockcaches

Der Blockcache ist der Lesecache. Seine Größe wird mit dem Parameter `hfile.block.cache.size` gesteuert. Der Standardwert ist „0,4“. Dies sind 40 Prozent des gesamten Arbeitsspeichers des Regionsservers. Je größer der Blockcache ist, desto schneller werden die zufälligen Lesevorgänge durchgeführt.

1. Navigieren Sie zum Ändern dieses Parameters auf der HBase-Registerkarte **Configs** (Konfigurationen) zur Registerkarte **Settings** (Einstellungen), und suchen Sie nach **% of RegionServer Allocated to Read Buffers** (RegionServer-Zuteilung für Lesepuffer in Prozent).

    ![Apache HBase – Speicherblock-Cachegröße](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Wählen Sie das Symbol **Bearbeiten**, um den Wert zu ändern.

### <a name="memstore-size"></a>Memstore-Größe

Alle Bearbeitungen werden im Arbeitsspeicherpuffer gespeichert, der als *Memstore* bezeichnet wird. Durch diesen Puffer wird die Gesamtmenge der Daten erhöht, die mit einem einzelnen Vorgang auf den Datenträger geschrieben werden können. Außerdem wird der Zugriff auf die letzten Bearbeitungen beschleunigt. Die Memstore-Größe wird mit den beiden folgenden Parametern definiert:

* `hbase.regionserver.global.memstore.UpperLimit`: Definiert den maximalen Prozentsatz des Regionsservers, der von kombinierten Memstore-Einheiten verwendet werden kann.

* `hbase.regionserver.global.memstore.LowerLimit`: Definiert den minimalen Prozentsatz des Regionsservers, der von kombinierten Memstore-Einheiten verwendet werden kann.

Sie können den oberen und unteren Grenzwert für Memstore reduzieren, um eine Optimierung für zufällige Lesevorgänge zu erzielen.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Anzahl von Zeilen, die beim Scannen vom Datenträger abgerufen werden

Mit der Einstellung `hbase.client.scanner.caching` wird die Anzahl von Zeilen definiert, die vom Datenträger gelesen werden, wenn für einen Scanner die `next`-Methode aufgerufen wird.  Der Standardwert ist 100. Je höher der Wert ist, desto weniger Remoteaufrufe werden vom Client an den Regionsserver gesendet, sodass für die Scans weniger Zeit benötigt wird. Durch diese Einstellung wird aber auch die Arbeitsspeicherauslastung des Clients erhöht.

![Apache HBase – Anzahl abgerufener Zeilen](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Legen Sie den Wert nicht so fest, dass der Zeitraum zwischen dem Aufruf der nächsten Methode auf einem Scanner größer als der Zeitraum für den Scanner-Timeout ist. Die Dauer bis zum Scanner-Timeout wird mit der Eigenschaft `hbase.regionserver.lease.period` definiert.

## <a name="optimize-write-heavy-workloads"></a>Optimieren von Workloads mit vielen Schreibvorgängen

Die folgenden Konfigurationen sind wichtig, um die Leistung von Workloads mit vielen Schreibvorgängen zu verbessern.

### <a name="maximum-region-file-size"></a>Maximale Regionsdateigröße

HBase speichert Daten in einem internen Dateiformat (*HFile*). Mit der Eigenschaft `hbase.hregion.max.filesize` wird die Größe einer einzelnen HFile für eine Region definiert.  Eine Region wird in zwei Regionen unterteilt, wenn die Summe aller HFiles in einer Region diese Einstellung übersteigt.

![Apache HBase – Maximale Dateigröße für HRegion](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Je größer die Regionsdatei ist, desto kleiner ist die Anzahl von Unterteilungen. Sie können die Dateigröße erhöhen, um einen Wert zu ermitteln, der zur maximalen Schreibleistung führt.

### <a name="avoid-update-blocking"></a>Vermeiden der Updateblockierung

* Mit der Eigenschaft `hbase.hregion.memstore.flush.size` wird die Größe definiert, mit der für Memstore der Flushvorgang auf den Datenträger durchgeführt wird. Die Standardgröße beträgt 128 MB.

* Der Blockmultiplikator für die HBase-Region wird mit `hbase.hregion.memstore.block.multiplier` definiert. Der Standardwert ist 4. Der zulässige Höchstwert ist 8.

* HBase blockiert Updates, wenn der Memstore eine Größe von (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) Byte hat.

    Bei den Standardwerten für die Flushgröße und den Blockmultiplikator werden Updates blockiert, wenn die Größe für den Memstore 128 * 4 = 512 MB beträgt. Erhöhen Sie den Wert von `hbase.hregion.memstore.block.multiplier`, um die Anzahl für die Updateblockierung zu reduzieren.

![Apache HBase – Blockmultiplikator für Region](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definieren der Memstore-Größe

Die Memstore-Größe wird mit den Parametern `hbase.regionserver.global.memstore.upperLimit` und `hbase.regionserver.global.memstore.lowerLimit` definiert. Wenn Sie Werte hierfür gleich festlegen, werden die Pausen zwischen den Schreibvorgängen reduziert (und häufiger Flushvorgänge durchgeführt), und die Schreibleistung verbessert sich.

## <a name="set-memstore-local-allocation-buffer"></a>Festlegen des lokalen Memstore-Zuteilungspuffers

Die Nutzung des lokalen Memstore-Zuteilungspuffers wird mit der Eigenschaft `hbase.hregion.memstore.mslab.enabled` ermittelt. Wenn die Einstellung aktiviert ist (TRUE), wird die Heapfragmentierung bei einer hohen Zahl von Schreibvorgängen verhindert. Der Standardwert lautet „true“.

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimieren von Clustern](./hdinsight-changing-configs-via-ambari.md)
* [Optimieren von Apache Hive](./optimize-hive-ambari.md)
* [Optimieren von Apache Pig](./optimize-pig-ambari.md)
