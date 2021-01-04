---
title: 'Infrastruktur: Lokales Apache Hadoop zu Azure HDInsight'
description: Erfahren Sie mehr zu Best Practices für die Infrastruktur bei der Migration von lokalen Hadoop-Clustern zu Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: e412b82be911f0b4ba2e5cda51495cdcd7826917
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542300"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight – Best Practices für Infrastruktur

Dieser Artikel enthält Empfehlungen für die Verwaltung der Infrastruktur von Azure HDInsight-Clustern. Er ist Teil einer Reihe von Artikeln, die bewährte Methoden für die Migration von lokalen Apache Hadoop-Systemen zu Azure HDInsight enthalten.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planen der HDInsight-Clusterkapazität

Die wichtigsten Entscheidungen für die Kapazitätsplanung von HDInsight-Clustern sind die folgenden:

**Region**  
Die Azure-Region bestimmt, wo der Cluster physisch bereitgestellt wird. Um die Latenz der Lese- und Schreibvorgänge zu minimieren, sollte sich der Cluster in der gleichen Region wie Ihre Daten befinden.

**Speicherort und -größe**  
Der Standardspeicher muss sich in derselben Region wie der Cluster befinden.  Für einen Cluster mit 48 Knoten wird empfohlen, 4 bis 8 Speicherkonten zu verwenden. Es mag bereits ausreichend Gesamtspeicher verfügbar sein, doch jedes Speicherkonto bietet zusätzliche Netzwerkbandbreite für die Serverknoten. Wenn Sie mehrere Speicherkonten haben, verwenden Sie für jedes Speicherkonto einen zufälligen Namen ohne Präfix. Die zufällige Benennung soll die Wahrscheinlichkeit alle Konten übergreifender Speicherengpässe (Drosselung) oder üblicher Fehler reduzieren. Verwenden Sie nur einen Container pro Speicherkonto, um die Leistung zu verbessern.

**VM-Größe und -Typ (unterstützt jetzt die G-Serie)**  
Jeder Clustertyp hat einen Satz von Knotentypen, und jeder Knotentyp hat bestimmte Optionen für VM-Größe und -Typ. Größe und Typ des virtuellen Computers richten sich nach CPU-Verarbeitungsleistung, RAM-Größe und Netzwerklatenz. Mit einer simulierten Workload kann für jeden Knotentyp die optimale VM-Größe und der richtige Typ ermittelt werden.

**Anzahl der Workerknoten**  
Die anfängliche Anzahl von Workerknoten kann mit den simulierten Workloads bestimmt werden. Der Cluster kann später durch Hinzufügen weiterer Workerknoten skaliert werden, um Spitzenlastanforderungen zu erfüllen. Der Cluster kann später herunterskaliert werden, wenn die zusätzlichen Workerknoten nicht benötigt werden.

Weitere Informationen finden Sie im Artikel [Kapazitätsplanung für HDInsight-Cluster](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Verwenden des empfohlenen VM-Typs den für Cluster

In [Standardknotenkonfiguration und Standardgrößen von virtuellen Computern für Cluster](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) finden Sie Informationen zu empfohlenen VM-Typen für jeden Typ von HDInsight-Cluster.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Überprüfen der Verfügbarkeit von Hadoop-Komponenten in HDInsight

Jede HDInsight-Version ist eine Clouddistribution einer Reihe von Komponenten des Hadoop-Ökosystems. Unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md) finden Sie Einzelheiten zu allen HDInsight-Komponenten und deren aktuelle Versionen.

Sie können auch die Benutzeroberfläche von Apache Ambari oder die Ambari-REST-API verwenden, um die Hadoop-Komponenten und -Versionen in HDInsight zu überprüfen.

Anwendungen oder Komponenten, die in lokalen Clustern verfügbar waren, aber nicht Teil der HDInsight-Cluster sind, können auf einem Edgeknoten oder auf einer VM im gleichen VNet wie der HDInsight-Cluster hinzugefügt werden. Eine Hadoop-Anwendung eines Drittanbieters, die in Azure HDInsight nicht verfügbar ist, kann über die Option „Anwendungen“ im HDInsight-Cluster installiert werden. Benutzerdefinierte Hadoop-Anwendungen können auf dem HDInsight-Cluster mithilfe von „Skriptaktionen“ installiert werden. Die folgende Tabelle enthält einige allgemeine Anwendungen sowie die Optionen für die HDInsight-Integration:

|**Anwendung**|**Integration**
|---|---|
|Luftströmung|IaaS- oder HDInsight-Edgeknoten
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Keine (nur HDP)
|Datameer|HDInsight-Edgeknoten
|Datastax (Cassandra)|IaaS (COSMOS DB als eine Alternative in Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (COSMOS DB als eine Alternative in Azure)
|NiFi|IaaS 
|Presto|IaaS- oder HDInsight-Edgeknoten
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW als eine Alternative in Azure)
|Tableau|IaaS 
|Waterline|HDInsight-Edgeknoten
|StreamSets|HDInsight-Edge 
|Palantir|IaaS 
|Sailpoint|Iaas 

Weitere Informationen finden Sie im Artikel [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Anpassen von HDInsight-Clustern mit Skriptaktionen

HDInsight bietet eine Methode für die Clusterkonfiguration namens **Skriptaktion** . Eine Skriptaktion ist ein Bash-Skript, das auf den Knoten in einem HDInsight-Cluster ausgeführt wird und dazu verwendet werden kann, zusätzliche Komponenten zu installieren und Konfigurationseinstellungen zu ändern.

Skriptaktionen müssen als URI gespeichert werden, der über den HDInsight-Cluster verfügbar ist. Sie können während oder nach der Clustererstellung verwendet werden und können auch auf die Ausführung auf bestimmten Knotentypen beschränkt werden.

Das Skript kann permanent sein oder einmal ausgeführt werden. Permanente Skripts werden verwendet, um mithilfe von Skalierungsvorgängen neue Workerknoten anzupassen, die dem Cluster hinzugefügt wurden. Bei Skalierungsvorgängen kann ein permanentes Skript auch Änderungen auf einen anderen Knotentyp anwenden, z.B. einen Hauptknoten.

HDInsight verfügt über vorgefertigte Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

- Hinzufügen eines Azure Storage-Kontos
- Installieren von Hue
- Installieren von Presto
- Installieren von Solr
- Installieren von Giraph
- Vorabladen von Hive-Bibliotheken
- Installieren oder Aktualisieren von Mono

> [!Note]  
> HDInsight bietet keine direkte Unterstützung für benutzerdefinierte Hadoop-Komponenten oder Komponenten, die über Skriptaktionen installiert werden.

Skriptaktionen können auch als HDInsight-Anwendung im Azure Marketplace veröffentlicht werden.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Installieren von Apache Hadoop-Anwendungen von Drittanbietern in Azure HDInsight](../hdinsight-apps-install-applications.md)
- [Anpassen von HDInsight-Clustern mit Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md)
- [Veröffentlichen von HDInsight-Anwendungen im Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Anpassen von HDInsight-Konfigurationen mithilfe von Bootstrap

Änderungen an Konfigurationen in den Konfigurationsdateien wie `core-site.xml`, `hive-site.xml` und `oozie-env.xml` können mit Bootstrap vorgenommen werden. Das folgende Skript ist ein Beispiel für die Verwendung des [AZ-Moduls](/powershell/azure/new-azureps-module-az) und des Cmdlets [New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster) von PowerShell:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Weitere Informationen finden Sie im Artikel [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Siehe auch [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Zugreifen auf Clienttools über Edgeknoten des HDInsight Hadoop-Clusters

Ein leerer Edgeknoten ist ein virtueller Linux-Computer, auf dem die gleichen Clienttools installiert und konfiguriert sind wie auf den Hauptknoten, aber keine Hadoop-Dienste ausgeführt werden. Der Edgeknoten kann für folgende Zwecke verwendet werden:

- Zugriff auf den Cluster
- Testen der Clientanwendungen
- Hosten der Clientanwendungen

Edgeknoten können über das Azure-Portal erstellt und gelöscht und während oder nach der Clustererstellung verwendet werden. Nach der Erstellung eines Edgeknotens können Sie über SSH eine Verbindung zum Edgeknoten herstellen und Client-Tools ausführen, um auf den Hadoop-Cluster in HDInsight zuzugreifen. Der SSH-Endpunkt des Edgeknotens ist `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


Weitere Informationen finden Sie im Artikel [Verwenden leerer Edgeknoten in Apache Hadoop-Clustern in HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Verwenden des Features zum Hoch- und Herunterskalieren von Clustern

HDInsight bietet Flexibilität, indem Sie die Anzahl der Workerknoten in Ihren Clustern hoch- und herunterskalieren können. So können Sie einen Cluster nach den Geschäftsstunden oder am Wochenende verkleinern und während der Spitzenbelastungen erweitern. Weitere Informationen finden Sie unter

* [Skalieren von HDInsight-Clustern](../hdinsight-scaling-best-practices.md)
* [Skalieren von Clustern](../hdinsight-administer-use-portal-linux.md#scale-clusters)

## <a name="use-hdinsight-with-azure-virtual-network"></a>Verwenden von HDInsight mit Azure Virtual Network

Azure Virtual Networks ermöglichen Azure-Ressourcentypen, beispielsweise Azure-VMs, die sichere Kommunikation miteinander sowie mit dem Internet und lokalen Netzwerken, indem Netzwerkdatenverkehr gefiltert und weitergeleitet wird.

Die Nutzung eines Azure Virtual Network mit HDInsight ermöglicht die folgenden Szenarien:

- Herstellen einer Verbindung mit HDInsight direkt über ein lokales Netzwerk
- Herstellen einer Verbindung für HDInsight mit Datenspeichern in einem Azure Virtual Network
- Direktes Zugreifen auf Hadoop-Dienste, die nicht öffentlich über das Internet verfügbar sind (Beispiele: Kafka-APIs oder die HBase-Java-API)

HDInsight kann entweder zu einem neuen oder vorhandenen Azure Virtual Network hinzugefügt werden. Wenn HDInsight zu einem bestehenden Virtual Network hinzugefügt wird, müssen die bestehenden Netzwerksicherheitsgruppen und benutzerdefinierten Routen aktualisiert werden, um uneingeschränkten Zugriff auf [mehrere IP-Adressen](../hdinsight-management-ip-addresses.md) im Azure-Rechenzentrum zu ermöglichen. Stellen Sie außerdem sicher, dass Sie den Datenverkehr zu den [Ports](../control-network-traffic.md#required-ports), die von HDInsight-Diensten verwendet werden, nicht blockieren.

> [!Note]  
> Die Tunnelerzwingung wird derzeit von HDInsight nicht unterstützt. Die Tunnelerzwingung ist eine Subnetzeinstellung, bei der für ausgehenden Internetdatenverkehr der Umweg über ein Gerät zur Untersuchung und Protokollierung erzwungen wird. Entfernen Sie entweder die Tunnelerzwingung, bevor Sie HDInsight in einem Subnetz installieren, oder erstellen Sie ein neues Subnetz für HDInsight. HDInsight unterstützt auch keine Beschränkung der ausgehenden Netzwerkverbindung.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Azure Virtual Networks – Übersicht](../../virtual-network/virtual-networks-overview.md)
- [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Herstellen einer sicheren Verbindung mit Azure-Diensten unter Verwendung von Azure Virtual Network-Dienstendpunkten

HDInsight unterstützt [Virtual Network-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md), mit denen Sie eine sichere Verbindung zu Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB und SQL-Datenbanken herstellen können. Durch die Aktivierung eines Dienstendpunkts für Azure HDInsight, durchläuft der Datenverkehr eine geschützte Route im Azure-Rechenzentrum. Dank dieser höheren Sicherheit in der Netzwerkschicht können Sie große Datenspeicherkonten auf ihre spezifischen Virtual Networks (VNETs) festlegen und dennoch HDInsight-Cluster nahtlos nutzen, um auf diese Daten zuzugreifen und sie zu verarbeiten.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [VNET-Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Erhöhen der Sicherheit von HDInsight mit Dienstendpunkten](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Verbinden von HDInsight mit einem lokalen Netzwerk

HDInsight kann mit Ihrem lokalen Netzwerk verbunden werden, indem Sie Azure Virtual Networks und ein VPN-Gateway einsetzen. Die folgenden Schritte können zum Herstellen einer Verbindung verwendet werden:

- Verwenden Sie HDInsight in einem Azure Virtual Network, das mit dem lokalen Netzwerk verbunden ist.
- Konfigurieren Sie die DNS-Namensauflösung zwischen dem virtuellen Netzwerk und Ihrem lokalen Netzwerk.
- Konfigurieren Sie Netzwerksicherheitsgruppen oder benutzerdefinierte Routen zum Steuern des Netzwerkdatenverkehrs.

Weitere Informationen finden Sie im Artikel [Herstellen einer Verbindung von HDInsight mit Ihrem lokalen Netzwerk](../connect-on-premises-network.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den nächsten Artikel in dieser Reihe: [Best Practices für den Speicher bei der Migration von lokalen Hadoop-Clustern zu Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-storage.md)