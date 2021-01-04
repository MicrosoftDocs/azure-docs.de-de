---
title: Auswählen der richtigen VM-Größe für Ihren Azure HDInsight-Cluster
description: Erfahren Sie, wie Sie die richtige VM-Größe für Ihren Azure HDInsight-Cluster auswählen.
keywords: VM-Größen, Clustergrößen, Clusterkonfiguration
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: d347707b0f48314dd872bc3ad34ac624817d2937
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535313"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Auswählen der richtigen VM-Größe für Ihren Azure HDInsight-Cluster

In diesem Artikel wird erläutert, wie Sie die richtige VM-Größe für die verschiedenen Knoten in Ihrem HDInsight-Cluster auswählen. 

Machen Sie sich zunächst damit vertraut, wie sich die Eigenschaften einer VM – beispielsweise CPU-Verarbeitung, RAM-Größe und Netzwerklatenz – auf die Verarbeitung Ihrer Workloads auswirken. Betrachten Sie als Nächstes Ihre Anwendung, und untersuchen Sie, wie sie zu den Aspekten passt, für die die verschiedenen VM-Familien optimiert sind. Stellen Sie sicher, dass die zu verwendende VM-Familie mit dem Clustertyp kompatibel ist, den Sie bereitstellen möchten. Eine Liste aller unterstützten und empfohlenen VM-Größen für jeden Clustertyp finden Sie im Artikel zu den von [Azure HDInsight unterstützten Knotenkonfigurationen](hdinsight-supported-node-configuration.md). Schließlich können Sie einen Benchmarkprozess verwenden, um einige Beispielworkloads zu testen und zu überprüfen, welche SKU innerhalb dieser Familie die richtige für Sie ist.

Weitere Informationen zum Planen anderer Aspekte Ihres Clusters – etwa die Auswahl von Speichertyp oder Speichergröße – finden Sie unter [Kapazitätsplanung für HDInsight-Cluster](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>VM-Eigenschaften und Big Data-Workloads

Größe und Typ des virtuellen Computers richten sich nach CPU-Verarbeitungsleistung, RAM-Größe und Netzwerklatenz:

- CPU: Die VM-Größe bestimmt die Anzahl der Kerne. Je mehr Kerne, in desto höherem Maße kann jeder Knoten parallele Berechnungen durchführen. Außerdem weisen einige VM-Typen schnellere Kerne auf.

- RAM: Die VM-Größe bestimmt auch, wie viel RAM die VM bietet. Stellen Sie für Workloads, bei denen Daten eher für die Verarbeitung im Arbeitsspeicher gespeichert als vom Datenträger gelesen werden, sicher, dass Ihre Workerknoten ausreichend Arbeitsspeicher für die Datenmenge aufweisen.

- Netzwerk: Bei den meisten Clustertypen befinden sich die vom Cluster verarbeiteten Daten nicht auf dem lokalen Datenträger, sondern in einem externen Speicherdienst wie Data Lake Storage oder Azure Storage. Berücksichtigen Sie die Netzwerkbandbreite und den Durchsatz zwischen der Knoten-VM und dem Speicherdienst. Die für einen virtuellen Computer verfügbare Netzwerkbandbreite wird bei Erweiterungen in der Regel heraufgesetzt. Weitere Informationen finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/sizes.md).

## <a name="understanding-vm-optimization"></a>Grundlegendes zur VM-Optimierung

Die VM-Familien in Azure sind für verschiedene Anwendungsfälle optimiert. In der Tabelle unten finden Sie einige der gängigsten Anwendungsmodelle sowie die passenden VM-Familien.

| type                     | Größen           |    BESCHREIBUNG       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Einstiegsebene](../virtual-machines/sizes-general.md)          | A, Av2  | CPU-Leistung und Arbeitsspeicherkonfigurationen dieser VM-Serie sind am besten für Workloads wie Entwicklung und Test geeignet. Sie sind wirtschaftlich und bieten eine preiswerte Option für den Einstieg in Azure. |
| [Allgemeiner Zweck](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | Ausgewogenes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Tests und Entwicklung, kleine bis mittlere Datenbanken sowie Webserver mit geringer bis mittlerer Auslastung. |
| [Computeoptimiert](../virtual-machines/sizes-compute.md)        | F           | Hohes Verhältnis von CPU zu Arbeitsspeicher. Ideal für Webserver, Network Appliances, Stapelverarbeitungsvorgänge und Anwendungsserver mit mittlerer Auslastung.        |
| [Arbeitsspeicheroptimiert](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | Hohes Verhältnis von Speicher zu CPU. Hervorragend geeignet für relationale Datenbankserver, mittlere bis große Caches und In-Memory-Analysen.                 |

- Preisinformationen zu den verfügbaren VM-Instanzen in den Regionen mit HDInsight-Unterstützung finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Kostensparende VM-Typen für weniger umfangreiche Workloads

Wenn Sie geringere Anforderungen an die Verarbeitung stellen, kann die [F-Serie](https://azure.microsoft.com/blog/f-series-vm-size/) eine gute Wahl für den Einstieg in HDInsight sein. Die F-Serie hat einen niedrigeren Listenpreis pro Stunde und bietet auf Basis der Azure-Compute-Einheit (Azure Compute Unit, ACU) das beste Preis-Leistungs-Verhältnis pro vCPU im Azure-Portfolio.

Die folgende Tabelle beschreibt die Cluster- und Knotentypen, die mit den VMs der Fsv2-Serie erstellt werden können.

| Clustertyp | Version | Workerknoten | Hauptknoten | ZooKeeper-Knoten |
|---|---|---|---|---|
| Spark | All | F4 und höher | nein | nein |
| Hadoop | All | F4 und höher | nein | nein |
| Kafka | All | F4 und höher | nein | nein |
| hbase | All | F4 und höher | nein | nein |
| LLAP | deaktiviert | nein | nein | nein |
| Storm | deaktiviert | nein | nein | nein |
| ML-Dienst | NUR HDI 3.6 | F4 und höher | nein | nein |

Die Spezifikationen jeder SKU der F-Serie finden Sie unter [New F-Series VM Sizes](https://azure.microsoft.com/blog/f-series-vm-size/) (Neue VM-Größen der F-Serie).

## <a name="benchmarking"></a>Benchmarktests

Bei einem Benchmarktest werden simulierte Workload auf verschiedenen VMs ausgeführt, um zu messen, wie gut sie sich für Ihre Produktionsworkloads eignen. 

Weitere Informationen zu Benchmarktests für VM-SKUs und Clustergrößen finden Sie unter [Planen der Clusterkapazität in Azure HDInsight](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Nächste Schritte

- [Unterstützte Azure HDInsight-Knotenkonfigurationen](hdinsight-supported-node-configuration.md)
- [Größen für virtuelle Linux-Computer in Azure](../virtual-machines/sizes.md)