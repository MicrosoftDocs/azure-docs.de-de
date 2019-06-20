---
title: Clusterkapazitätsplanung in Azure HDInsight
description: Erfahren Sie, wie Sie Kapazität und Leistung eines HDInsight-Clusters angeben.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: c910ed9f1160d30e1d4bda2e85b029eb2ad85b02
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237149"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Kapazitätsplanung für HDInsight-Cluster

Planen Sie vor der Bereitstellung eines HDInsight-Clusters die gewünschte Clusterkapazität, indem Sie die erforderliche Leistung und Skalierung bestimmen. Diese Planung trägt zur Optimierung von Nutzbarkeit und Kosten bei. Einige Entscheidungen zur Clusterkapazität können nach der Bereitstellung nicht geändert werden. Wenn sich die Leistungsparameter ändern, kann ein Cluster deinstalliert und ohne Verlust von gespeicherten Daten neu erstellt werden.

Die wichtigsten Fragen zur Kapazitätsplanung sind:

* In welcher geografischen Region sollten Sie Ihren Cluster bereitstellen?
* Wie viel Speicher benötigen Sie?
* Welchen Clustertyp sollten Sie bereitstellen?
* Welche VM-Größe und welchen VM-Typ sollten die Clusterknoten verwenden?
* Wie viele Workerknoten sollte Ihr Cluster besitzen?

## <a name="choose-an-azure-region"></a>Auswahl einer Azure-Region

Die Azure-Region bestimmt, wo Ihr Cluster physisch bereitgestellt wird. Um die Latenz der Lese- und Schreibvorgänge zu minimieren, sollte der Cluster sich in der Nähe Ihrer Daten befinden.

HDInsight ist in vielen Azure-Regionen verfügbar. Die nächstgelegene Region finden Sie unter dem Eintrag *HDInsight* unter *Analysen* in [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Auswahl von Speicherort und -größe

### <a name="location-of-default-storage"></a>Speicherort des Standardspeichers

Der Standardspeicher, ein Azure Storage-Konto oder Azure Data Lake Storage, muss sich am gleichen Standort wie der Cluster befinden. Azure Storage ist an allen Speicherorten verfügbar. Data Lake Storage Gen1 ist in einigen Regionen verfügbar – unter *Speicher* auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/regions/services/) können Sie sich über die aktuelle Verfügbarkeit von Data Lake Storage informieren.

### <a name="location-of-existing-data"></a>Speicherort der vorhandenen Daten

Wenn Sie bereits über ein Speicherkonto oder Data Lake Storage mit Ihren Daten verfügen und diesen Speicher als Standardspeicher für Ihren Cluster verwenden möchten, müssen Sie dem Cluster am selben Standort bereitstellen.

### <a name="storage-size"></a>Speichergröße

Nachdem Sie einen HDInsight-Cluster bereitgestellt haben, können Sie zusätzliche Azure Storage-Konten anfügen oder auf andere Data Lake Storages zugreifen. Alle Ihre Speicherkonten müssen sich an demselben Speicherort wie Ihr Cluster befinden. Ein Data Lake Storage kann sich an einem anderen Standort befinden, obwohl dies zu einer gewissen Latenz beim Lesen/Schreiben von Daten führen kann.

Für Azure Storage gelten einige [Kapazitätsgrenzen](../azure-subscription-service-limits.md#storage-limits), während Data Lake Storage Gen1 praktisch unbegrenzt ist.

Ein Cluster kann auf eine Kombination verschiedener Speicherkonten zugreifen. Typische Beispiele hierfür sind:

* Die Menge der Daten überschreitet wahrscheinlich die Speicherkapazität eines einzelnen Blobspeichercontainers.
* Die Zugriffsrate könnte beim Zugriff auf den Blobcontainer den Schwellenwert überschreiten, bei dem eine Drosselung auftritt.
* Sie möchten Daten bearbeiten, die Sie bereits in einen Blobcontainer hochgeladen haben, der für den Cluster verfügbar ist.
* Sie möchten verschiedene Teile des Speichers aus Sicherheitsgründen, oder um die Administration zu vereinfachen, isolieren.

Für einen Cluster mit 48 Knoten sollten Sie 4 bis 8 Speicherkonten verwenden. Es mag bereits ausreichend Gesamtspeicher verfügbar sein, doch jedes Speicherkonto bietet zusätzliche Netzwerkbandbreite für die Serverknoten. Wenn Sie mehrere Speicherkonten haben, verwenden Sie für jedes Speicherkonto einen zufälligen Namen ohne Präfix. Die zufällige Benennung soll die Wahrscheinlichkeit alle Konten übergreifender Speicherengpässe (Drosselung) oder üblicher Fehler reduzieren. Verwenden Sie nur einen Container pro Speicherkonto, um die Leistung zu verbessern.

## <a name="choose-a-cluster-type"></a>Auswahl eines Clustertyps

Der Clustertyp bestimmt die Workload, für die Ihr HDInsight-Cluster konfiguriert ist, z.B. [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/) oder [Apache Spark](https://spark.apache.org/). Eine detaillierte Beschreibung der verfügbaren Clustertypen finden Sie unter [Einführung in Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Jeder Clustertyp verfügt über eine bestimmte Bereitstellungstopologie, die Anforderungen an die Größe und Anzahl der Knoten enthält.

## <a name="choose-the-vm-size-and-type"></a>Auswahl von Größe und Typ des virtuellen Computers

Jeder Clustertyp hat einen Satz von Knotentypen, und jeder Knotentyp hat bestimmte Optionen für VM-Größe und -Typ.

Um die optimale Clustergröße für Ihre Anwendung zu ermitteln, können Sie einen Vergleichstest für die Clusterkapazität durchführen und die Größe wie angegeben heraufsetzen. Sie können z.B. eine simulierte Workload oder eine *Testabfrage* verwenden. Bei einer simulierten Workload führen Sie die erwarteten Workloads auf Clustern verschiedener Größen aus und steigern allmählich die Größe, bis die gewünschte Leistung erreicht ist. Eine Testabfrage kann in regelmäßigen Abständen zwischen anderen Produktionsabfragen eingefügt werden, um zu zeigen, ob der Cluster über genügend Ressourcen verfügt.

Größe und Typ des virtuellen Computers richten sich nach CPU-Verarbeitungsleistung, RAM-Größe und Netzwerklatenz:

* CPU: Die VM-Größe bestimmt die Anzahl der Kerne. Je mehr Kerne, in desto höherem Maße kann jeder Knoten parallele Berechnungen durchführen. Außerdem weisen einige VM-Typen schnellere Kerne auf.

* RAM: Die VM-Größe bestimmt auch, wie viel RAM die VM bietet. Stellen Sie für Workloads, bei denen Daten eher für die Verarbeitung im Arbeitsspeicher gespeichert als vom Datenträger gelesen werden, sicher, dass Ihre Workerknoten ausreichend Arbeitsspeicher für die Datenmenge aufweisen.

* Netzwerk: Bei den meisten Clustertypen befinden sich die vom Cluster verarbeiteten Daten nicht auf dem lokalen Datenträger, sondern in einem externen Speicherdienst wie Data Lake Storage oder Azure Storage. Berücksichtigen Sie die Netzwerkbandbreite und den Durchsatz zwischen der Knoten-VM und dem Speicherdienst. Die für einen virtuellen Computer verfügbare Netzwerkbandbreite wird bei Erweiterungen in der Regel heraufgesetzt. Weitere Informationen finden Sie unter [Größen für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Auswahl der Clusterskalierung

Die Skalierung eines Clusters wird durch die Menge seiner VM-Knoten bestimmt. Für alle Clustertypen gibt es Knotentypen mit bestimmter Skalierung, und Knotentypen, die horizontales Hochskalieren unterstützen. Beispielsweise könnte ein Cluster genau drei [Apache ZooKeeper](https://zookeeper.apache.org/)-Knoten oder zwei Hauptknoten erfordern. Workerknoten, die eine verteilte Datenverarbeitung praktizieren, können vom horizontalen Hochskalieren durch das Hinzufügen zusätzlicher Workerknoten profitieren.

Je nach Clustertyp kann durch zusätzliche Workerknoten die Rechenkapazität (z.B. durch weitere Kerne) heraufgesetzt werden, damit jedoch auch der Gesamtbedarf an Arbeitsspeicher zur Unterstützung der In-Memory-Speicherung verarbeiteter Daten für den gesamten Cluster steigen. Wie bei der Wahl von VM-Größe und -Typ gilt für die richtige Clusterskalierung, dass sie in der Regel mit simulierten Workloads oder Testabfragen empirisch ermittelt wird.

Sie können Ihren Cluster für Spitzenlastanforderungen horizontal hochskalieren und wieder herunterskalieren, wenn diese zusätzlichen Knoten nicht mehr benötigt werden. Weitere Informationen finden Sie unter [Skalieren von HDInsight-Clustern](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Clusterlebenszyklus

Die Lebensdauer des Clusters wird Ihnen in Rechnung gestellt. Wenn Sie Ihren Cluster nur zu bestimmten Zeiten betreiben müssen, können Sie [mithilfe von Azure Data Factory bedarfsgesteuerte Cluster erstellen](hdinsight-hadoop-create-linux-clusters-adf.md). Sie können auch PowerShell-Skripts erstellen, die Ihren Cluster bereitstellen und löschen, und diese Skripts mit [Azure Automation](https://azure.microsoft.com/services/automation/) planen.

> [!NOTE]  
> Wenn ein Cluster gelöscht wird, wird sein standardmäßiger Hive-Metastore ebenfalls gelöscht. Um den Metastore für die nächste Clusterneuerstellung beizubehalten, verwenden Sie einen externen Metadatenspeicher, z.B. Azure Database oder [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isolieren von Clusterauftragsfehlern

Manchmal können Fehler aufgrund paralleler Ausführung von Mehrfachzuordnungen auftreten und Komponenten auf einem Cluster mit mehreren Knoten reduzieren. Um das Problem zu isolieren, versuchen Sie, verteilte Tests durchzuführen, indem Sie gleichzeitig mehrere Aufträge auf einem Cluster mit einem einzelnen Knoten ausführen und diesen Ansatz dann zur gleichzeitigen Ausführung mehrerer Aufträge auf Clustern mit mehreren Knoten erweitern. Um einen Einzelknoten-HDInsight-Cluster in Azure zu erstellen, verwenden Sie die Option *erweitert*.

Sie können auch eine Einzelknoten-Entwicklungsumgebung auf dem lokalen Computer installieren und die Lösung dort testen. Hortonworks bietet für Hadoop-basierten Lösungen eine für anfängliche Entwicklung, Proof of Concept und Testen hilfreiche lokale Einzelknoten-Entwicklungsumgebung. Weitere Informationen finden Sie unter [Starten Sie mit Hortonworks Sandbox](https://hortonworks.com/products/hortonworks-sandbox/).

Um das Problem auf einem lokalen Cluster mit einzelnem Knoten zu identifizieren, können Sie fehlgeschlagene Aufträge erneut ausführen und die Eingabedaten anpassen bzw. kleinere Datasets verwenden. Die Ausführung dieser Aufträge hängt von Plattform und Anwendungstyp ab.

## <a name="quotas"></a>Kontingente

Überprüfen Sie nach der Bestimmung von Größe, Skalierung und Typ der Zielcluster-VM die aktuellen Kontingentkapazitätsgrenzen Ihres Abonnements. Wenn Sie eine Kontingentgrenze erreichen, können Sie möglicherweise nicht neue Cluster bereitstellen oder vorhandene Cluster durch Hinzufügen weiterer Workerknoten horizontal hochskalieren. Die einzige Kontingentgrenze ist das Kontingent für CPU-Kerne, das auf Regionsebene für jedes Abonnement vorhanden ist. Ihr Abonnement kann z. B. den Grenzwert von 30 Kernen in der Region „USA, Osten“ aufweisen. Wenn Sie eine Kontingenterhöhung anfordern müssen, gehen Sie wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie unten links auf der Seite **Hilfe und Support** aus.
1. Wählen Sie **Neue Supportanfrage** aus.
1. Wählen Sie auf der Seite **Neue Supportanfrage** unter der Registerkarte **Grundlagen** die folgenden Optionen aus:
   - **Problemtyp**: **Grenzwerte für Dienste und Abonnements (Kontingente)**
   - **Abonnement**: Das zu ändernde Abonnement.
   - **Kontingenttyp**: **HDInsight**
    
     ![Erstellen einer Supportanfrage zur Erhöhung des HDInsight-Kernkontingents](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Klicken Sie auf **Weiter: Lösungen >>** .
1. Geben Sie auf der Seite **Details** eine Beschreibung des Problems ein, wählen Sie den Schweregrad des Problems, Ihre bevorzugte Kontaktmethode und weitere Pflichtfelder aus.
1. Klicken Sie auf **Weiter: Bewerten + erstellen >>** .
1. Wählen Sie auf der Registerkarte **Überprüfen + erstellen** die Option **Erstellen** aus.

> [!NOTE]  
> Wenn Sie das HDInsight-Kernkontingent in einer privaten Region erhöhen müssen, [senden Sie eine Whitelist-Anforderung](https://aka.ms/canaryintwhitelist).

Sie können [sich an den Support wenden, um eine Erhöhung von Kernkontingenten anzufordern](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Es gibt jedoch einige feste Kontingentbeschränkungen, z.B. kann ein einzelnes Azure-Abonnement höchstens 10.000 Kerne haben. Informationen über diese Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten von Clustern in HDInsight mit Apache Hadoop, Spark, Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md): Erfahren Sie, wie Sie Cluster in HDInsight mit Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services oder Storm einrichten und konfigurieren.
* [Überwachen der Clusterleistung](hdinsight-key-scenarios-to-monitor.md): Lernen Sie wichtige Szenarien zur Überwachung des HDInsight-Clusters kennen, die sich auf die Kapazität des Clusters auswirken können.
