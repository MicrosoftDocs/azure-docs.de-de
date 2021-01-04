---
title: Azure HDInsight Accelerated Writes für Apache HBase
description: Bietet eine Übersicht über das Azure HDInsight Accelerated Writes-Feature, das verwaltete Premium-Datenträger zum Verbessern der Leistung des Apache HBase-Write-Ahead-Protokolls verwendet.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 27b0485fdd7b3c352a85fe7eb39fba33c53bf727
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519211"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight Accelerated Writes für Apache HBase

Dieser Artikel bietet Hintergrundinformationen zum **Accelerated Writes**-Feature für Apache HBase in Azure HDInsight und zu dessen effektiver Verwendung zur Verbesserung der Schreibleistung. **Accelerated Writes** verwendet [verwaltete Azure-Premium-SSD-Datenträger](../../virtual-machines/disks-types.md#premium-ssd) zur Verbesserung der Leistung des Apache HBase-Write-Ahead-Protokolls (Write Ahead Log, WAL). Weitere Informationen zu Apache HBase finden Sie unter [Überblick über Apache HBase in HDInsight: Eine NoSQL-Datenbank, die BigTable-ähnliche Funktionen für Apache Hadoop bereitstellt](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Übersicht über die HBase-Architektur

In HBase besteht eine **Zeile** aus einer oder mehreren **Spalten** und wird durch einen **Zeilenschlüssel** identifiziert. Mehrere Zeilen bilden eine **Tabelle**. Spalten enthalten **Zellen**, welche mit einem Zeitstempel versehene Versionen des Werts in dieser Spalte sind. Spalten werden in **Spaltenfamilien** gruppiert, und alle Spalten in einer Spaltenfamilie werden gemeinsam in Speicherdateien gespeichert, die **HFiles** genannt werden.

**Regionen** werden in HBase verwendet, um die Datenverarbeitungslast auszugleichen. HBase speichert zunächst die Zeilen einer Tabelle in einer einzelnen Region. Die Zeilen werden über mehrere Regionen verteilt, wenn die Menge der Daten in der Tabelle steigt. **Regionsserver** können Anforderungen für mehrere Regionen verarbeiten.

## <a name="write-ahead-log-for-apache-hbase"></a>Write-Ahead-Protokoll für Apache Hbase

HBase schreibt Datenaktualisierungen zuerst in einen Commitprotokolltyp, der als Write-Ahead-Protokoll (WAL) bezeichnet wird. Sobald das Update im WAL gespeichert ist, wird es in den In-Memory-**MemStore** geschrieben. Wenn die maximale Datenkapazität im Arbeitsspeicher erreicht ist, werden die Daten als **HFile** auf den Datenträger geschrieben.

Wenn ein **RegionServer** abstürzt oder nicht mehr verfügbar ist, bevor der MemStore geleert wird, kann das Write-Ahead-Protokoll verwendet werden, um Updates zu wiederholen. Wenn ein **RegionServer** ohne WAL abstürzt, bevor Updates in einen **HFile** geleert werden, gehen alle diese Updates verloren.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Das Accelerated Writes-Feature in HDInsight für Apache HBase

Das Accelerated Writes-Feature löst das Problem der höheren Schreiblatenzen, das durch Verwendung von Write-Ahead-Protokollen verursacht wird, die sich im Cloudspeicher befinden.  Das Accelerated Writes-Feature für HDInsight Apache HBase-Cluster fügt verwaltete SSD-Premium-Datenträger jedem RegionServer (Workerknoten) hinzu. Write-Ahead-Protokolle werden dann auf das in diese verwalteten Premium-Datenträger eingebundene Hadoop Distributed File System (HDFS) statt in den Cloudspeicher geschrieben.  Verwaltete Premium-Datenträger verwenden Solid State-Laufwerke (SSDs) und bieten eine ausgezeichnete E/A-Leistung mit Fehlertoleranz.  Wenn eine Speichereinheit ausfällt, wirkt sich das im Gegensatz zu nicht verwalteten Datenträgern nicht auf andere Speichereinheiten in derselben Verfügbarkeitsgruppe aus.  Daher bieten verwaltete Datenträger geringe Schreiblatenz und eine höhere Flexibilität für Ihre Anwendungen. Weitere Informationen zu verwalteten Azure-Datenträgern finden Sie unter [Einführung in verwaltete Azure-Datenträger](../../virtual-machines/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Gewusst wie: Aktivieren von Accelerated Writes für HBase in HDInsight

Um einen neuen HBase-Cluster mit dem Accelerated Writes-Feature zu erstellen, führen Sie die Schritte in [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.](../hdinsight-hadoop-provision-linux-clusters.md) aus. Wählen Sie auf der Registerkarte **Grundlagen** als Clustertyp „HBase“ aus, geben Sie eine Komponentenversion an, und klicken Sie dann auf das Kontrollkästchen neben **Beschleunigte HBase-Schreibvorgänge aktivieren**. Fahren Sie dann mit den verbleibenden Schritten zur Erstellung des Clusters fort.

![Aktivieren der Accelerated Writes-Option für HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png)

## <a name="other-considerations"></a>Weitere Überlegungen

Damit die Dauerhaftigkeit der Daten erhalten bleibt, erstellen Sie einen Cluster mit mindestens drei Workerknoten. Nach der Erstellung können Sie den Cluster nicht auf weniger als drei Workerknoten zentral herunterskalieren.

Leeren oder deaktivieren Sie die HBase-Tabellen vor dem Löschen des Clusters, damit Sie keine Write-Ahead-Protokoll-Daten verlieren.

```
flush 'mytable'
```

```
disable 'mytable'
```

Führen Sie beim zentralen Herunterskalieren des Clusters ähnliche Schritte aus: Leeren Sie Ihre Tabellen, und deaktivieren Sie sie, um den Eingang von Daten zu beenden. Sie können Ihren Cluster nicht auf weniger als drei Knoten zentral herunterskalieren.

Wenn Sie diese Schritte ausführen, stellen Sie eine erfolgreiche zentrale Herunterskalierung sicher und vermeiden die Möglichkeit, dass ein Namensknoten aufgrund von unterreplizierten oder temporären Dateien in den abgesicherten Modus wechselt.

Wenn Ihr Namensknoten nach dem zentralen Herunterskalieren in den abgesicherten Modus wechselt, verwenden Sie HDFS-Befehle, um die unterreplizierten Blöcke erneut zu replizieren und den abgesicherten Modus für HDFS aufzuheben. Mit dieser erneuten Replikation können Sie HBase erfolgreich neu starten.

## <a name="next-steps"></a>Nächste Schritte

* Offizielle Dokumentation zu Apache HBase im [Write-Ahead-Protokoll-Feature](https://hbase.apache.org/book.html#wal)
* Wie Sie Ihren HDInsight Apache HBase-Cluster für die Verwendung von Accelerated Writes aktualisieren, erfahren Sie unter [Migrieren eines Apache HBase-Clusters zu einer neuen Version](apache-hbase-migrate-new-version.md).
