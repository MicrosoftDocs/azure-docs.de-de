---
title: Azure HDInsight Accelerated Writes für Apache HBase
description: Bietet eine Übersicht über das Azure HDInsight Accelerated Writes-Feature, das verwaltete Premium-Datenträger zum Verbessern der Leistung des Apache HBase-Write-Ahead-Protokolls verwendet.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 219899c2e336f544ff6572589cc79f84f555490d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233836"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight Accelerated Writes für Apache HBase

Dieser Artikel bietet Hintergrundinformationen zum **Accelerated Writes**-Feature für Apache HBase in Azure HDInsight und zu dessen effektiver Verwendung zur Verbesserung der Schreibleistung. **Accelerated Writes** verwendet [verwaltete Azure-Premium-SSD-Datenträger](../../virtual-machines/linux/disks-types.md#premium-ssd) zur Verbesserung der Leistung des Apache HBase-Write-Ahead-Protokolls (Write Ahead Log, WAL). Weitere Informationen zu Apache HBase finden Sie unter [Überblick über Apache HBase in HDInsight: Eine NoSQL-Datenbank, die BigTable-ähnliche Funktionen für Apache Hadoop bereitstellt](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Übersicht über die HBase-Architektur

In HBase besteht eine **Zeile** aus einer oder mehreren **Spalten** und wird durch einen **Zeilenschlüssel** identifiziert. Mehrere Zeilen bilden eine **Tabelle**. Spalten enthalten **Zellen**, welche mit einem Zeitstempel versehene Versionen des Werts in dieser Spalte sind. Spalten werden in **Spaltenfamilien** gruppiert, und alle Spalten in einer Spaltenfamilie werden gemeinsam in Speicherdateien gespeichert, die **HFiles** genannt werden.

**Regionen** werden in HBase verwendet, um die Datenverarbeitungslast auszugleichen. HBase speichert zunächst die Zeilen einer Tabelle in einer einzelnen Region. Die Zeilen werden über mehrere Regionen verteilt, wenn die Menge der Daten in der Tabelle steigt. **Regionsserver** können Anforderungen für mehrere Regionen verarbeiten.

## <a name="write-ahead-log-for-apache-hbase"></a>Write-Ahead-Protokoll für Apache Hbase

HBase schreibt Datenaktualisierungen zuerst in einen Commitprotokolltyp, der als Write-Ahead-Protokoll (WAL) bezeichnet wird. Sobald das Update im WAL gespeichert ist, wird es in den In-Memory-**MemStore** geschrieben. Wenn die maximale Datenkapazität im Arbeitsspeicher erreicht ist, werden die Daten als **HFile** auf den Datenträger geschrieben.

Wenn ein **RegionServer** abstürzt oder nicht mehr verfügbar ist, bevor der MemStore geleert wird, kann das Write-Ahead-Protokoll verwendet werden, um Updates zu wiederholen. Wenn ein **RegionServer** ohne WAL abstürzt, bevor Updates in einen **HFile** geleert werden, gehen alle diese Updates verloren.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Das Accelerated Writes-Feature in HDInsight für Apache HBase

Das Accelerated Writes-Feature löst das Problem der höheren Schreiblatenzen, das durch Verwendung von Write-Ahead-Protokollen verursacht wird, die sich im Cloudspeicher befinden.  Das Accelerated Writes-Feature für HDInsight Apache HBase-Cluster fügt verwaltete SSD-Premium-Datenträger jedem RegionServer (Workerknoten) hinzu. Write-Ahead-Protokolle werden dann auf das in diese verwalteten Premium-Datenträger eingebundene Hadoop Distributed File System (HDFS) statt in den Cloudspeicher geschrieben.  Verwaltete Premium-Datenträger verwenden Solid State-Laufwerke (SSDs) und bieten eine ausgezeichnete E/A-Leistung mit Fehlertoleranz.  Wenn eine Speichereinheit außer Betrieb geht, wirkt sich dies im Gegensatz zu nicht verwalteten Datenträgern nicht auf andere Speichereinheiten in derselben Verfügbarkeitsgruppe aus.  Daher bieten verwaltete Datenträger geringe Schreiblatenz und eine höhere Flexibilität für Ihre Anwendungen. Weitere Informationen zu verwalteten Azure-Datenträgern finden Sie unter [Einführung in verwaltete Azure-Datenträger](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Gewusst wie: Aktivieren von Accelerated Writes für HBase in HDInsight

Um einen neuen HBase-Cluster mit dem Accelerated Writes-Feature zu erstellen, führen Sie die Schritte in [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.](../hdinsight-hadoop-provision-linux-clusters.md) bis zu **Schritt 3, Speicher** aus. Klicken Sie unter **Metastore-Einstellungen** auf das Kontrollkästchen neben **Accelerated Writes aktivieren (Vorschau)** . Fahren Sie dann mit den verbleibenden Schritten zur Erstellung des Clusters fort.

![Aktivieren der Accelerated Writes-Option für HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Weitere Überlegungen

Damit die Dauerhaftigkeit der Daten erhalten bleibt, erstellen Sie einen Cluster mit mindestens drei Workerknoten. Nach der Erstellung können Sie den Cluster nicht auf weniger als drei Workerknoten zentral herunterskalieren.

Leeren oder deaktivieren Sie die HBase-Tabellen vor dem Löschen des Clusters, damit Sie keine Write-Ahead-Protokoll-Daten verlieren.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Nächste Schritte

* Offizielle Dokumentation zu Apache HBase im [Write-Ahead-Protokoll-Feature](https://hbase.apache.org/book.html#wal)
* Wie Sie Ihren HDInsight Apache HBase-Cluster für die Verwendung von Accelerated Writes aktualisieren, erfahren Sie unter [Migrieren eines Apache HBase-Clusters zu einer neuen Version](apache-hbase-migrate-new-version.md).
