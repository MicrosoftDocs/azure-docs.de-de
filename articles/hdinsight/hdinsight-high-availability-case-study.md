---
title: Fallstudie für eine hochverfügbare Lösungsarchitektur für Azure HDInsight
description: Dieser Artikel ist eine fiktive Fallstudie für eine mögliche hochverfügbare Lösungsarchitektur für Azure HDInsight.
keywords: Hochverfügbarkeit, Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508590"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Fallstudie für eine hochverfügbare Lösungsarchitektur für Azure HDInsight

Die Replikationsmechanismen von Azure HDInsight können in eine hochverfügbare Lösungsarchitektur integriert werden. In diesem Artikel werden im Rahmen einer fiktiven Fallstudie für Contoso Retail mögliche Ansätze für Notfallwiederherstellung mit Hochverfügbarkeit sowie Kostenüberlegungen und die entsprechenden Strukturen erläutert.

Bei Empfehlungen für Notfallwiederherstellung mit Hochverfügbarkeit sind viele verschiedene Abwandlungen und Kombinationen möglich. Bevor Sie sich für eine Lösung entscheiden, sollten Sie zunächst die Vor- und Nachteile der einzelnen Optionen abwägen. In diesem Artikel wird nur eine mögliche Lösung erläutert.

## <a name="customer-architecture"></a>Kundenarchitektur

Die folgende Abbildung zeigt die primäre Architektur von Contoso Retail. Die Architektur besteht aus einer Streamingworkload, einer Batchworkload, einer Bereitstellungsebene, einer Nutzungsebene, einer Speicherebene und einem Versionskontrollsystem.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Architektur von Contoso Retail":::

### <a name="streaming-workload"></a>Streamingworkload

Geräte und Sensoren senden Daten an das Messagingframework, in diesem Fall HDInsight Kafka. Ein HDInsight Spark-Consumer liest in den Kafka-Themen. Spark transformiert die eingehenden Nachrichten und schreibt sie in einen HDInsight HBase-Cluster auf der Bereitstellungsebene.

### <a name="batch-workload"></a>Batchworkload

Ein HDInsight Hadoop-Cluster, auf dem Hive und MapReduce ausgeführt werden, erfasst Daten aus lokalen Transaktionssystemen. Von Hive und MapReduce transformierte Rohdaten werden in Hive-Tabellen auf einer logischen Partition des Data Lake gespeichert, die von Azure Data Lake Storage Gen2 unterstützt wird. In Hive-Tabellen gespeicherte Daten werden auch Spark SQL zur Verfügung gestellt, wo die Batches transformiert werden, bevor die zusammengestellten Daten für die Bereitstellung in HBase gespeichert werden.

### <a name="serving-layer"></a>Bereitstellungsebene

Zum Bereitstellen von Daten für Webanwendungen und Visualisierungsdashboards wird ein HDInsight HBase-Cluster mit Apache Phoenix verwendet. Ein HDInsight LLAP-Cluster wird verwendet, um interne Berichterstellungsanforderungen zu erfüllen.

### <a name="consumption-layer"></a>Nutzungsebene

Eine Ebene mit Azure API-Apps und API Management unterstützt eine öffentliche Webseite. Die internen Berichterstellungsanforderungen werden durch Power BI erfüllt.

### <a name="storage-layer"></a>Speicherebene

Als Data Lake für das Unternehmen wird eine logisch partitionierte Azure Data Lake Storage Gen2-Instanz verwendet. Die HDInsight-Metastores werden von Azure SQL-Datenbank unterstützt.

### <a name="version-control-system"></a>Versionskontrollsystem

Es wird ein Versionskontrollsystem verwendet, das in eine Azure Pipelines-Instanz integriert ist und außerhalb von Azure gehostet wird.

## <a name="customer-business-continuity-requirements"></a>Anforderungen des Kunden an die Geschäftskontinuität

Es ist wichtig, die minimale Geschäftsfunktionalität zu ermitteln, die Sie bei einem Notfall benötigen.

### <a name="contoso-retails-business-continuity-requirements"></a>Anforderungen von Contoso Retail an die Geschäftskontinuität

* Das Unternehmen muss vor regionalen Ausfällen oder regionalen Dienstintegritätsproblemen geschützt sein.
* Kunden darf nie der Fehler 404 angezeigt werden. Öffentliche Inhalte müssen immer bereitgestellt sein. (RTO = 0)  
* Den Großteil des Jahres über können öffentliche Inhalte angezeigt werden, die bereits 5 Stunden alt sind. (RPO = 5 Stunden)
* Während der Weihnachtszeit müssen die öffentlichen Inhalte immer auf dem neuesten Stand sein. (RPO = 0)
* Die internen Berichterstellungsanforderungen werden nicht als wichtig für die Geschäftskontinuität angesehen.
* Die Geschäftskontinuitätskosten sollen optimiert werden.

## <a name="proposed-solution"></a>Vorgeschlagene Lösung

Die folgende Abbildung zeigt die Architektur von Contoso Retail für Notfallwiederherstellung mit Hochverfügbarkeit.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Lösung von Contoso":::

**Kafka** verwendet [Aktiv-Passiv-Replikation](hdinsight-business-continuity-architecture.md#apache-kafka) zum Spiegeln von Kafka-Themen aus der primären in die sekundäre Region. Eine Alternative zur Kafka-Replikation könnte darin bestehen, in beiden Regionen Daten an Kafka zu senden.

**Hive und Spark** verwenden als Replikationsmodell im Normalbetrieb [einen aktiven primären Cluster und einen bedarfsgesteuerten sekundären Cluster](hdinsight-business-continuity-architecture.md#apache-spark). Der Hive-Replikationsprozess wird in regelmäßigen Abständen ausgeführt und begleitet die Replikation des Azure SQL-Hive-Metastores und des Hive-Speicherkontos. Das Spark-Speicherkonto wird in regelmäßigen Abständen mithilfe von ADF DistCP repliziert. Die nur vorübergehende Aktivität dieser Cluster trägt zur Kostenoptimierung bei. Replikationen sind für alle 4 Stunden geplant, um eine RPO zu gewährleisten, die die 5-Stunden-Anforderung problemlos erfüllt.

Bei der **HBase**-Replikation wird im Normalbetrieb das [Leader-Follower-Modell](hdinsight-business-continuity-architecture.md#apache-hbase) verwendet, um sicherzustellen, dass Daten immer unabhängig von der Region bereitgestellt werden und die RPO sehr gering ist.

Bei einem regionalen Ausfall in der primären Region werden die Inhalte der Webseite und des Back-Ends 5 Stunden lang mit einer gewissen Veraltung von der sekundären Region bereitgestellt. Wenn das Dashboard zur Azure-Dienstintegrität keine Wiederherstellungs-ETA in diesem fünfstündigen Zeitfenster angibt, erstellt Contoso Retail die Hive- und Spark-Transformationsebene in der sekundären Region und dann Verweise für alle Upstreamdatenquellen in die sekundäre Region. Würde die sekundäre Region beschreibbar gemacht, so würde dadurch ein Failbackprozess ausgelöst, der eine Replikation zurück in die primäre Region umfasst.

Während der Einkaufshochsaison ist die gesamte sekundäre Pipeline immer aktiv und wird ausgeführt. Die Kafka-Producer senden Daten an beide Regionen, und die HBase-Replikation wird von Leader-Follower in Leader-Leader geändert, um sicherzustellen, dass die öffentlichen Inhalte immer auf dem neuesten Stand sind.

Für die interne Berichterstellung muss keine Failoverlösung entworfen werden, da diese für die Geschäftskontinuität nicht wichtig ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel erörterten Themen finden Sie unter:

* [Geschäftskontinuität von Azure HDInsight](./hdinsight-business-continuity.md)
* [Architekturen zur Geschäftskontinuität von Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Was sind Apache Hive und HiveQL in Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
