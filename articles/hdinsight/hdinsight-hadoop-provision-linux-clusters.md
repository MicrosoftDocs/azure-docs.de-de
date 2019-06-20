---
title: Einrichten von Clustern für Apache Hadoop, Apache Spark, Apache Kafka, Apache HBase oder R Server – Azure HDInsight
description: Richten Sie Hadoop-, Kafka-, Spark-, HBase-, R Server- oder Storm-Cluster für HDInsight über einen Browser, die klassische Azure-Befehlszeilenschnittstelle, Azure PowerShell, REST oder ein SDK ein.
keywords: Hadoop-Cluster einrichten, Kafka-Cluster einrichten, Spark-Cluster einrichten, was ist ein Hadoop-Cluster?
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 351b6a8e056d22fa8f2d695a2722b39b9771c8b0
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299385"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Erfahren Sie, wie Sie Cluster in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services oder Apache Storm einrichten und konfigurieren. Darüber hinaus lernen Sie Cluster anzupassen und Sicherheit hinzuzufügen, indem Sie sie mit einer Domäne verknüpfen.

Ein Hadoop-Cluster besteht aus mehreren virtuellen Computern (Knoten), die zur verteilten Verarbeitung von Aufgaben verwendet werden. Azure HDInsight verwaltet die Implementierungsdetails der Installation und Konfiguration einzelner Knoten, sodass Sie nur allgemeine Konfigurationsinformationen bereitstellen müssen.

> [!IMPORTANT]  
> Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden. Erfahren Sie, wie Sie [einen Cluster löschen](hdinsight-delete-cluster.md).

## <a name="cluster-setup-methods"></a>Methoden für die Clustereinrichtung
Die folgende Tabelle zeigt die verschiedenen Methoden, die Sie zum Einrichten eines HDInsight-Clusters verwenden können.

| Verfahren zur Clustererstellung | Webbrowser | Befehlszeile | REST-API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure-Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Azure-Ressourcen-Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Schnellerfassung: Grundlegende Clustereinrichtung
Dieser Artikel enthält die Schritte im [Azure-Portal](https://portal.azure.com) zum Erstellen eines HDInsight-Clusters mithilfe der *Schnellerfassung* oder *benutzerdefiniert*. 

![HDInsight-Erstellungsoptionen – benutzerdefinierte Schnellerfassung](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Befolgen Sie die Anweisungen auf dem Bildschirm, um eine grundlegenden Clustereinrichtung durchzuführen. Sie finden im Folgenden folgende Informationen:

* [Ressourcengruppenname](#resource-group-name)
* [Clustertypen und Konfiguration](#cluster-types) 
* Clusteranmeldung und SSH-Benutzername
* [Location](#location)

## <a name="resource-group-name"></a>Ressourcengruppenname

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, bezeichnet als „Azure-Ressourcengruppe“. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen.

## <a name="cluster-types"></a> Clustertypen und Konfiguration
Azure HDInsight bietet derzeit die folgenden Typen von Clustern mit je einer Reihe von Komponenten, um bestimmte Funktionen bereitzustellen.

> [!IMPORTANT]  
> HDInsight-Cluster sind jeweils für einzelne Workloads oder Technologien in verschiedenen Typen verfügbar. Es ist keine unterstützte Methode zum Erstellen eines Clusters vorhanden, bei der mehrere Typen kombiniert werden, z.B. Storm und HBase in einem Cluster. Wenn für Ihre Lösung Technologien erforderlich sind, die auf mehrere HDInsight-Clustertypen verteilt sind, können Sie die erforderlichen Clustertypen über ein [virtuelles Azure-Netzwerk](https://docs.microsoft.com/azure/virtual-network) miteinander verbinden. 

| Clustertyp | Funktionalität |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-Abfragen und -analysen gespeicherter Daten |
| [HBase](hbase/apache-hbase-overview.md) |Verarbeitung großen Mengen von schemalosen NoSQL-Daten |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |Interaktive und schnellere Hive-Abfragen durch speicherinternes Caching |
| [Kafka](kafka/apache-kafka-introduction.md) | Dies ist eine verteilte Open-Source-Streamingplattform, die zum Erstellen von Datenpipelines und Anwendungen mit Echtzeitstreaming verwendet werden kann. |
| [ML Services](r-server/r-server-overview.md) |Verschiedene Funktionen für Big Data-Statistiken, Vorhersagemodellierung und Machine Learning |
| [Spark](spark/apache-spark-overview.md) |Arbeitsspeicherinterne Verarbeitung, interaktive Abfragen, Microbatch-Datenstromverarbeitung |
| [Storm](storm/apache-storm-overview.md) |Ereignisverarbeitung in Echtzeit |


### <a name="hdinsight-version"></a>HDInsight-Version
Wählen Sie die Version von HDInsight für diesen Cluster aus. Weitere Informationen finden Sie unter [Unterstützte HDInsight-Versionen](hdinsight-component-versioning.md#supported-hdinsight-versions).


## <a name="cluster-login-and-ssh-username"></a>Clusteranmeldung und SSH-Benutzername
Während der Clustererstellung ermöglichen die HDInsight-Cluster Ihnen das Konfigurieren von zwei Benutzerkonten:

* HTTP-Benutzer: Der Standard-Benutzername lautet *admin*. Für ihn gilt im Azure-Portal die Standardkonfiguration. Er wird auch als „Clusterbenutzer“ bezeichnet.
* SSH-Benutzer: Wird verwendet, um die Verbindung mit dem Cluster über SSH herzustellen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Mit dem Sicherheitspaket für Unternehmen können Sie HDInsight mit Active Directory und Apache Ranger integrieren. Mithilfe des Enterprise-Sicherheitspakets können mehrere Benutzer erstellt werden.

## <a name="location"></a>Orte (Regionen) für Cluster und Speicher

Sie müssen den Clusterstandort nicht explizit angeben: Der Cluster befindet sich an derselben Position wie der Standardspeicher. Um eine Liste der unterstützten Regionen zu erhalten, klicken Sie in der Dropdownliste **Region** auf [HDInsight-Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Speicherendpunkte für Cluster

Obwohl eine lokale Installation von Hadoop das Hadoop Distributed File System (HDFS) für die Speicherung im Cluster verwendet, nutzen Sie in der Cloud Speicherendpunkte, die mit dem Cluster verbunden sind. HDInsight-Cluster verwenden entweder [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) oder [Blobs in Azure Storage](hdinsight-hadoop-use-blob-storage.md). Durch Verwendung von Azure Storage oder Data Lake Storage können Sie die für Berechnungen verwendeten HDInsight-Cluster sicher löschen und gleichzeitig Ihre Daten beibehalten. 

> [!WARNING]  
> Die Verwendung eines zusätzlichen Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

Während der Konfiguration geben Sie für den Standardspeicherendpunkt einen Blobcontainer für ein Azure-Speicherkonto oder eine Data Lake Storage-Instanz an. Der Standardspeicher enthält Anwendungs- und Systemprotokolle. Optional können Sie zusätzliche verknüpfte Azure-Speicherkonten und Data Lake Storage-Konten angeben, auf die der Cluster zugreifen kann. Der HDInsight-Cluster und die abhängigen Speicherkonten müssen sich an demselben Azure-Standort befinden.

![Clusterspeichereinstellungen: HDFS-kompatible Speicherendpunkte](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Optionale Metastores
Sie können optionale Hive- oder Apache Oozie Metastores erstellen. Allerdings unterstützen nicht alle Clustertypen Metastores, und Azure SQL Data Warehouse ist nicht mit Metastores kompatibel. 

Weitere Informationen finden Sie unter [Verwenden von externen Metadatenspeichern in Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Verwenden Sie beim Erstellen eines benutzerdefinierten Metastores keinen Datenbanknamen, der Gedankenstriche, Bindestriche oder Leerzeichen enthält. Dies kann dazu führen, dass der Clustererstellungsprozess fehlschlägt.

### <a name="use-hiveoozie-metastore"></a>Hive-Metastore

Verwenden Sie einen benutzerdefinierten Metastore, wenn Sie nach dem Löschen des HDInsight-Clusters Ihre Hive-Tabellen beibehalten möchten. Sie können diesen Metastore anschließend an einen anderen HDInsight-Cluster anfügen.

Ein HDInsight-Metastore, der für eine HDInsight-Clusterversion erstellt wurde, kann nicht über verschiedene HDInsight-Clusterversionen freigegeben werden. Eine Liste mit den HDInsight-Versionen finden Sie unter [Unterstützte HDInsight-Versionen](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie-Metastore

Verwenden Sie zur Erhöhung der Leistung bei Verwendung von Oozie einen benutzerdefinierten Metastore. Ein Metastore kann auch Zugriff auf Oozie-Auftragsdaten bieten, nachdem Sie Ihren Cluster gelöscht haben. 

> [!IMPORTANT]  
> Ein benutzerdefinierter Oozie-Metastore kann nicht wiederverwendet werden. Wenn Sie einen benutzerdefinierten Oozie-Metastore verwenden möchten, müssen Sie beim Erstellen des HDInsight-Clusters eine leere Azure SQL-Datenbank bereitstellen.


## <a name="custom-cluster-setup"></a>Benutzerdefinierte Clustereinrichtung
Die benutzerdefinierte Clustereinrichtung basiert auf den Einstellungen der Schnellerfassung mit den folgenden zusätzlichen Optionen:
- [Enterprise-Sicherheitspaket](#enterprise-security-package)
- [HDInsight-Anwendungen](#install-hdinsight-applications-on-clusters)
- [Clustergröße](#configure-cluster-size)
- [Skriptaktionen](#advanced-settings-script-actions)
- [Virtuelles Netzwerk](#advanced-settings-extend-clusters-with-a-virtual-network)
 
## <a name="enterprise-security-package"></a>Sicherheitspaket für Unternehmen

Für Cluster der Typen Hadoop, Spark, HBase, Kafka und Interactive Query können Sie das **Enterprise-Sicherheitspaket** aktivieren. Dieses Paket bietet die Möglichkeit, mithilfe von Apache Ranger und der Integration in Azure Active Directory eine sicherere Clustereinrichtung zu erreichen. Weitere Informationen finden Sie unter [Sicherheitspaket für Unternehmen in Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

![HDInsight-Erstellungsoptionen – Sicherheitspaket für Unternehmen auswählen](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

Weitere Informationen zum Erstellen eines in eine Domäne eingebundenen HDInsight-Clusters finden Sie unter [Erstellen einer in eine Domäne eingebundenen HDInsight-Sandboxumgebung](./domain-joined/apache-domain-joined-configure.md). 

## <a name="install-hdinsight-applications-on-clusters"></a>Installieren von HDInsight-Anwendungen in Clustern

Eine HDInsight-Anwendung kann von Benutzern in einem Linux-basierten HDInsight-Cluster installiert werden. Sie können Anwendungen von Microsoft, Anwendungen von Drittanbietern oder selbst entwickelte Anwendungen verwenden. Weitere Informationen finden Sie unter [Installieren von Apache Hadoop-Anwendungen von Drittanbietern in Azure HDInsight](hdinsight-apps-install-applications.md).

Die meisten HDInsight-Anwendungen werden auf einem leeren Edgeknoten installiert.  Ein leerer Edgeknoten ist ein virtueller Linux-Computer, auf dem die gleichen Clienttools installiert und konfiguriert sind wie im Hauptknoten. Sie können den Edgeknoten zum Zugreifen auf den Cluster sowie zum Testen und Hosten Ihrer Clientanwendungen verwenden. Weitere Informationen finden Sie unter [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Verwenden leerer Edgeknoten in HDInsight).

## <a name="configure-cluster-size"></a>Konfigurieren der Clustergröße

Die Verwendung der Knoten wird so lange abgerechnet, wie der Cluster vorhanden ist. Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und sie endet, wenn der Cluster gelöscht wird. Bei Clustern ist kein Aufheben der Zuweisung oder ein Anhalten möglich.

### <a name="number-of-nodes-for-each-cluster-type"></a>Anzahl der Knoten für jeden Clustertyp
Jeder Clustertyp verfügt über eine eigene Anzahl von Knoten, Terminologie für Knoten und eine VM-Standardgröße. In der folgenden Tabelle ist die Anzahl von Knoten für jeden Knotentyp jeweils in Klammern angegeben.

| Type | Nodes | Diagramm |
| --- | --- | --- |
| Hadoop |Hauptknoten (2), Workerknoten (1+) |![HDInsight-Hadoop-Clusterknoten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| hbase |Hauptserver (2), Regionsserver (1+), Master-/Zookeeper-Knoten (3) |![HDInsight-HBase-Clusterknoten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus-Knoten (2), Supervisor-Server (1+), Zookeeper-Knoten (3) |![HDInsight-Storm-Clusterknoten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hauptknoten (2), Workerknoten (1+), ZooKeeper-Knoten (3) (kostenlos für ZooKeeper-VMs der Größe A1) |![HDInsight-Spark-Clusterknoten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Weitere Informationen finden Sie unter [Standardknotenkonfiguration und VM-Größen für Cluster](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) in „Welche Hadoop-Komponenten und -Versionen gibt es in HDInsight?“.

Die Kosten von HDInsight-Clustern ergeben sich aus der Anzahl der Knoten und aus der Größe der virtuellen Computer für die Knoten. 

Unterschiedliche Clustertypen weisen verschiedene Knotentypen, eine unterschiedliche Anzahl von Knoten sowie verschiedene Knotengrößen auf:
* Hadoop-Standardclustertyp: 
    * Zwei *Hauptknoten*  
    * Vier *Workerknoten*
* Storm-Standardclustertyp: 
    * Zwei *Nimbusknoten*
    * Drei *Zookeeper-Knoten*
    * Vier *Supervisorknoten* 

Wenn Sie HDInsight gerade erst testen, empfehlen wir, nur einen Workerknoten zu verwenden. Weitere Informationen zu den Preisen von HDInsight finden Sie unter [HDInsight – Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Die Begrenzung der Clustergröße variiert je nach Azure-Abonnement. Wenden Sie sich an den [Azure-Abrechnungssupport](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request), um diese Begrenzung zu erhöhen.

Wenn Sie das Azure-Portal zum Konfigurieren des Clusters verwenden, ist die Knotengröße über das Blatt **Knotentarife** verfügbar. Außerdem werden im Portal die Kosten angezeigt, die den unterschiedlichen Knotengrößen zugeordnet sind. 

![VM-Größen für HDInsight-Knoten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Größen virtueller Computer 
Wählen Sie bei der Bereitstellung von Clustern die Computeressourcen basierend auf der Lösung aus, die Sie bereitstellen möchten. Für HDInsight-Cluster werden die folgenden virtuellen Computer verwendet:
* VMs der Serien A und D1–4: [Größen universeller Linux-VMs](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* VMs der Serien D11–14: [Größen speicheroptimierter Linux-VMs](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Wenn Sie wissen möchten, welchen Wert Sie beim Erstellen eines Clusters mithilfe unterschiedlicher SDKs oder von Azure PowerShell als VM-Größe angeben sollten, lesen Sie unter [VM-Größen für HDInsight-Cluster](../cloud-services/cloud-services-sizes-specs.md#size-tables) nach. Verwenden Sie im verknüpften Artikel in den Tabellen den Wert in der Spalte **Größe**.

> [!IMPORTANT]  
> Wenn Sie mehr als 32 Workerknoten in einem Cluster benötigen, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.

Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../virtual-machines/windows/sizes.md). Informationen zu den Preisen der unterschiedlichen Größen finden Sie unter [HDInsight-Preise](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="advanced-settings-script-actions"></a>Erweiterte Einstellungen: Skriptaktionen

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Erstellung anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion**aufgerufen, die vom Azure-Verwaltungsportal, von HDInsight Windows PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

Einige systemeigene Java-Komponenten wie Apache Mahout und Cascading können auf dem Cluster als JAR-Dateien (Java Archive) ausgeführt werden. Diese JAR-Dateien können an Azure Storage verteilt und mit den Verfahren zur Übermittlung von Hadoop-Aufträgen an HDInsight-Cluster gesendet werden. Weitere Informationen finden Sie unter [Programmgesteuerte Übermittlung von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Wenn bei der Bereitstellung von JAR-Dateien für HDInsight-Cluster oder beim Aufrufen von JAR-Dateien für HDInsight-Cluster Probleme auftreten, wenden Sie sich an den [Microsoft-Support](https://azure.microsoft.com/support/options/).
>
> Cascading wird von HDInsight nicht unterstützt, und es steht kein Microsoft-Support dafür zur Verfügung. Listen der unterstützten Komponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](hdinsight-component-versioning.md).

Es kann vorkommen, dass Sie die folgenden Konfigurationsdateien während des Erstellungsprozesses bearbeiten möchten:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Erweiterte Einstellungen: Erweitern von Clustern mit einem virtuellen Netzwerk
Wenn für Ihre Lösung Technologien erforderlich sind, die auf mehrere HDInsight-Clustertypen verteilt sind, können Sie die erforderlichen Clustertypen über ein [virtuelles Azure-Netzwerk](https://docs.microsoft.com/azure/virtual-network) miteinander verbinden. Durch diese Konfiguration können die Cluster und der gesamte Code, den Sie dafür bereitstellen, direkt miteinander kommunizieren.

Weitere Informationen zur Verwendung eines virtuellen Azure-Netzwerks finden Sie unter [Erweitern von HDInsight mit virtuellen Azure-Netzwerken](hdinsight-extend-hadoop-virtual-network.md).

Ein Beispiel für die Verwendung von zwei Clustertypen in einem virtuellen Azure-Netzwerk finden Sie unter [Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Weitere Informationen zur Verwendung von HDInsight mit einem virtuellen Netzwerk, einschließlich spezifischer Konfigurationsanforderungen für das virtuelle Netzwerk, finden Sie unter [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).


## <a name="next-steps"></a>Nächste Schritte

- [Was sind HDInsight, das Apache Hadoop-Ökosystem und Hadoop-Cluster?](hadoop/apache-hadoop-introduction.md)
- [Erste Schritte mit Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Verwenden eines Windows-Computers mit Apache Hadoop in HDInsight](hdinsight-hadoop-windows-tools.md)
