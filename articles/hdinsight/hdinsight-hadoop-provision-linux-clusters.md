---
title: Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.
description: Richten Sie Hadoop-, Kafka-, Spark-, HBase-, R Server- oder Storm-Cluster für HDInsight über einen Browser, die klassische Azure-Befehlszeilenschnittstelle, Azure PowerShell, REST oder ein SDK ein.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: 6ce5de354583da04905f9f889cfabe36e6da6667
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546125"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Erfahren Sie, wie Sie Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services oder Apache Storm in HDInsight einrichten und konfigurieren. Darüber hinaus lernen Sie Cluster anzupassen und Sicherheit hinzuzufügen, indem Sie sie mit einer Domäne verknüpfen.

Ein Hadoop-Cluster besteht aus mehreren virtuellen Computern (Knoten), die zur verteilten Verarbeitung von Aufgaben verwendet werden. Azure HDInsight verwaltet die Implementierungsdetails der Installation und Konfiguration einzelner Knoten, sodass Sie nur allgemeine Konfigurationsinformationen bereitstellen müssen.

> [!IMPORTANT]  
> Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden. Erfahren Sie, wie Sie [einen Cluster löschen](hdinsight-delete-cluster.md).

Bei gemeinsamer Verwendung mehrerer Cluster empfiehlt sich die Erstellung eines virtuellen Netzwerks. Bei Verwendung eines Spark-Clusters sollten Sie den Hive Warehouse Connector verwenden. Weitere Informationen finden Sie unter [Planen eines virtuellen Netzwerks für Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md) sowie unter [Integrieren von Apache Spark und Apache Hive per Hive Warehouse Connector](interactive-query/apache-hive-warehouse-connector.md).

## <a name="cluster-setup-methods"></a>Methoden für die Clustereinrichtung

Die folgende Tabelle zeigt die verschiedenen Methoden, die Sie zum Einrichten eines HDInsight-Clusters verwenden können.

| Verfahren zur Clustererstellung | Webbrowser | Befehlszeile | REST-API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure-Ressourcen-Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

Dieser Artikel enthält die Schritte im [Azure-Portal](https://portal.azure.com) zum Erstellen eines HDInsight-Clusters.

## <a name="basics"></a>Grundlagen

![HDInsight-Erstellungsoptionen benutzerdefiniert schnell](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Projektdetails

Mit [Azure Resource Manager](../azure-resource-manager/management/overview.md) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-[Ressourcengruppe](../azure-resource-manager/management/overview.md#resource-groups) bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen.

### <a name="cluster-details"></a>Clusterdetails

#### <a name="cluster-name"></a>Clustername

Für Namen von HDInsight-Clustern gelten folgende Einschränkungen:

* Zulässige Zeichen: a-z, 0–9, A-Z
* Max. Länge: 59
* Reservierte Namen: apps
* Der Benennungsbereich des Clusters gilt überall in Azure und in allen Abonnements. Der Clustername muss somit weltweit eindeutig sein.
* Die ersten sechs Zeichen müssen innerhalb eines virtuellen Netzwerks eindeutig sein.

#### <a name="region"></a>Region

Sie müssen den Clusterstandort nicht explizit angeben: Der Cluster befindet sich an derselben Position wie der Standardspeicher. Um eine Liste der unterstützten Regionen zu erhalten, wählen Sie in der Dropdownliste **Region** die Option [HDInsight-Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409) aus.

#### <a name="cluster-type"></a>Clustertyp

Azure HDInsight bietet derzeit die folgenden Typen von Clustern mit je einer Reihe von Komponenten, um bestimmte Funktionen bereitzustellen.

> [!IMPORTANT]  
> HDInsight-Cluster sind jeweils für einzelne Workloads oder Technologien in verschiedenen Typen verfügbar. Es ist keine unterstützte Methode zum Erstellen eines Clusters vorhanden, bei der mehrere Typen kombiniert werden, z.B. Storm und HBase in einem Cluster. Wenn für Ihre Lösung Technologien erforderlich sind, die auf mehrere HDInsight-Clustertypen verteilt sind, können Sie die erforderlichen Clustertypen über ein [virtuelles Azure-Netzwerk](../virtual-network/index.yml) miteinander verbinden.

| Clustertyp | Funktionalität |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-Abfragen und -analysen gespeicherter Daten |
| [HBase](hbase/apache-hbase-overview.md) |Verarbeitung großen Mengen von schemalosen NoSQL-Daten |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |Interaktive und schnellere Hive-Abfragen durch speicherinternes Caching |
| [Kafka](kafka/apache-kafka-introduction.md) | Dies ist eine verteilte Open-Source-Streamingplattform, die zum Erstellen von Datenpipelines und Anwendungen mit Echtzeitstreaming verwendet werden kann. |
| [ML Services](r-server/r-server-overview.md) |Verschiedene Funktionen für Big Data-Statistiken, Vorhersagemodellierung und Machine Learning |
| [Spark](spark/apache-spark-overview.md) |Arbeitsspeicherinterne Verarbeitung, interaktive Abfragen, Microbatch-Datenstromverarbeitung |
| [Storm](storm/apache-storm-overview.md) |Ereignisverarbeitung in Echtzeit |

#### <a name="version"></a>Version

Wählen Sie die Version von HDInsight für diesen Cluster aus. Weitere Informationen finden Sie unter [Unterstützte HDInsight-Versionen](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Clusteranmeldeinformationen

Während der Clustererstellung ermöglichen die HDInsight-Cluster Ihnen das Konfigurieren von zwei Benutzerkonten:

* Benutzername für Clusteranmeldung: Der Standard-Benutzername lautet *admin* . Für ihn gilt im Azure-Portal die Standardkonfiguration. Er wird manchmal auch als „Clusterbenutzer“ oder „HTTP-Benutzer“ bezeichnet.
* Secure Shell-Benutzername (SSH): Wird verwendet, um die Verbindung mit dem Cluster über SSH herzustellen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Für den HTTP-Benutzernamen gelten folgende Einschränkungen:

* Zulässige Sonderzeichen: `_` und `@`
* Unzulässige Zeichen: #;."',\/:`!*?$(){}[]<>|&--=+%~^Leerzeichen
* Max. Länge: 20

Für den SSH-Benutzernamen gelten folgende Einschränkungen:

* Zulässige Sonderzeichen: `_` und `@`
* Unzulässige Zeichen: #;."',\/:`!*?$(){}[]<>|&--=+%~^Leerzeichen
* Max. Länge: 64
* Reservierte Namen: hadoop, users, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, yarn, hcat, ams, hbase, storm, administrator, admin, user, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, guest, john, owner, root, server, sql, support, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Storage

![Clusterspeichereinstellungen: HDFS-kompatible Endpunkte](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Obwohl eine lokale Installation von Hadoop das Hadoop Distributed File System (HDFS) für die Speicherung im Cluster verwendet, nutzen Sie in der Cloud Speicherendpunkte, die mit dem Cluster verbunden sind. Durch Verwendung von Cloudspeicher können Sie die für Berechnungen verwendeten HDInsight-Cluster sicher löschen und gleichzeitig Ihre Daten beibehalten.

HDInsight-Cluster können die folgenden Speicheroptionen verwenden:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage vom Typ „Allgemein v2“
* Azure Storage vom Typ „Allgemein v1“
* Azure Storage-Blockblob ( **nur als sekundärer Speicher unterstützt** )

Weitere Informationen zu Speicheroptionen mit HDInsight finden Sie unter [Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Die Verwendung eines zusätzlichen Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

Während der Konfiguration geben Sie für den Standardspeicherendpunkt einen Blobcontainer für ein Azure-Speicherkonto oder eine Data Lake Storage-Instanz an. Der Standardspeicher enthält Anwendungs- und Systemprotokolle. Optional können Sie zusätzliche verknüpfte Azure-Speicherkonten und Data Lake Storage-Konten angeben, auf die der Cluster zugreifen kann. Der HDInsight-Cluster und die abhängigen Speicherkonten müssen sich an demselben Azure-Standort befinden.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> Das Aktivieren der sicheren Speicherübertragung nach dem Erstellen eines Clusters kann zu Fehlern bei der Verwendung Ihres Speicherkontos führen und wird daher nicht empfohlen. Es ist besser, einen neuen Cluster mit einem Speicherkonto zu erstellen, bei dem die sichere Übertragung bereits aktiviert ist.

> [!Note]  
> Azure HDInsight überträgt, verschiebt oder kopiert Ihre in Azure Storage gespeicherten Daten nicht automatisch zwischen Regionen.

### <a name="metastore-settings"></a>Metastore-Einstellungen

Sie können optionale Hive- oder Apache Oozie Metastores erstellen. Allerdings unterstützen nicht alle Clustertypen Metastores, und Azure Synapse Analytics ist nicht mit Metastores kompatibel.

Weitere Informationen finden Sie unter [Verwenden von externen Metadatenspeichern in Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Verwenden Sie beim Erstellen eines benutzerdefinierten Metastores keinen Datenbanknamen, der Gedankenstriche, Bindestriche oder Leerzeichen enthält. Dies kann dazu führen, dass der Clustererstellungsprozess fehlschlägt.

#### <a name="sql-database-for-hive"></a>SQL-Datenbank für Hive

Verwenden Sie einen benutzerdefinierten Metastore, wenn Sie nach dem Löschen des HDInsight-Clusters Ihre Hive-Tabellen beibehalten möchten. Sie können diesen Metastore anschließend an einen anderen HDInsight-Cluster anfügen.

Ein HDInsight-Metastore, der für eine HDInsight-Clusterversion erstellt wurde, kann nicht über verschiedene HDInsight-Clusterversionen freigegeben werden. Eine Liste mit den HDInsight-Versionen finden Sie unter [Unterstützte HDInsight-Versionen](hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Der Standardmetastore stellt eine Azure SQL-Datenbank mit einem **Basic-Tarif mit einer Begrenzung auf fünf DTUs (nicht aktualisierbar)** bereit. Er eignet sich für grundlegende Testzwecke. Bei großen Workloads bzw. Produktionsworkloads empfiehlt sich die Migration zu einem externen Metastore.

#### <a name="sql-database-for-oozie"></a>SQL-Datenbank für Oozie

Verwenden Sie zur Erhöhung der Leistung bei Verwendung von Oozie einen benutzerdefinierten Metastore. Ein Metastore kann auch Zugriff auf Oozie-Auftragsdaten bieten, nachdem Sie Ihren Cluster gelöscht haben.

#### <a name="sql-database-for-ambari"></a>SQL-Datenbank für Ambari

Ambari wird zum Überwachen von HDinsight-Clustern, Vornehmen von Konfigurationsänderungen und Speichern von Informationen zur Clusterverwaltung sowie zum Auftragsverlauf verwendet. Mit der Funktion der benutzerdefinierten Ambari-Datenbank können Sie einen neuen Cluster bereitstellen und Ambari in einer externen Datenbank einrichten, die Sie verwalten. Weitere Informationen finden Sie unter [Einrichten von HDInsight-Clustern mit einer benutzerdefinierten Ambari-Datenbank](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Ein benutzerdefinierter Oozie-Metastore kann nicht wiederverwendet werden. Wenn Sie einen benutzerdefinierten Oozie-Metastore verwenden möchten, müssen Sie beim Erstellen des HDInsight-Clusters eine leere Azure SQL-Datenbank bereitstellen.

## <a name="security--networking"></a>Sicherheit + Netzwerkbetrieb

![HDInsight-Erstellungsoptionen – Sicherheitspaket für Unternehmen auswählen](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Sicherheitspaket für Unternehmen

Für Cluster der Typen Hadoop, Spark, HBase, Kafka und Interactive Query können Sie das **Enterprise-Sicherheitspaket** aktivieren. Dieses Paket bietet die Möglichkeit, mithilfe von Apache Ranger und der Integration in Azure Active Directory eine sicherere Clustereinrichtung zu erreichen. Weitere Informationen finden Sie unter [Übersicht über die Unternehmenssicherheit in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Mit dem Sicherheitspaket für Unternehmen können Sie HDInsight mit Active Directory und Apache Ranger integrieren. Mithilfe des Enterprise-Sicherheitspakets können mehrere Benutzer erstellt werden.

Weitere Informationen zum Erstellen eines in eine Domäne eingebundenen HDInsight-Clusters finden Sie unter [Erstellen einer in eine Domäne eingebundenen HDInsight-Sandboxumgebung](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).

### <a name="tls"></a>TLS

Weitere Informationen finden Sie unter [Transport Layer Security](./transport-layer-security.md).

### <a name="virtual-network"></a>Virtuelles Netzwerk

Wenn für Ihre Lösung Technologien erforderlich sind, die auf mehrere HDInsight-Clustertypen verteilt sind, können Sie die erforderlichen Clustertypen über ein [virtuelles Azure-Netzwerk](../virtual-network/index.yml) miteinander verbinden. Durch diese Konfiguration können die Cluster und der gesamte Code, den Sie dafür bereitstellen, direkt miteinander kommunizieren.

Weitere Informationen zur Verwendung eines virtuellen Azure-Netzwerks mit HDInsight finden Sie unter [Planen eines virtuellen Netzwerks für HDInsight](hdinsight-plan-virtual-network-deployment.md).

Ein Beispiel für die Verwendung von zwei Clustertypen in einem virtuellen Azure-Netzwerk finden Sie unter [Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Weitere Informationen zur Verwendung von HDInsight mit einem virtuellen Netzwerk, einschließlich spezifischer Konfigurationsanforderungen für das virtuelle Netzwerk, finden Sie unter [Planen eines virtuellen Netzwerks für HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Datenträgerverschlüsselungs-Einstellung

Weitere Informationen finden Sie unter [Datenträgerverschlüsselung mit kundenseitig verwalteten Schlüsseln](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Kafka-REST-Proxy

Diese Einstellung ist nur für den Clustertyp Kafka verfügbar. Weitere Informationen finden Sie unter [Interagieren mit Apache Kafka-Clustern in Azure HDInsight mithilfe eines REST-Proxys](./kafka/rest-proxy.md).

### <a name="identity"></a>Identity

Weitere Informationen finden Sie unter [Verwaltete Identitäten in Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguration + Preise

![HDInsight – Knotengröße auswählen](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Die Verwendung der Knoten wird so lange abgerechnet, wie der Cluster vorhanden ist. Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und sie endet, wenn der Cluster gelöscht wird. Bei Clustern ist kein Aufheben der Zuweisung oder ein Anhalten möglich.

### <a name="node-configuration"></a>Knotenkonfiguration

Jeder Clustertyp verfügt über eine eigene Anzahl von Knoten, Terminologie für Knoten und eine VM-Standardgröße. In der folgenden Tabelle ist die Anzahl von Knoten für jeden Knotentyp jeweils in Klammern angegeben.

| type | Nodes | Diagramm |
| --- | --- | --- |
| Hadoop |Hauptknoten (2), Workerknoten (1+) |![HDInsight-Hadoop-Clusterknoten](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| hbase |Hauptserver (2), Regionsserver (1+), Master-/Zookeeper-Knoten (3) |![HDInsight-HBase-Clustertypeinrichtung](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus-Knoten (2), Supervisor-Server (1+), Zookeeper-Knoten (3) |![HDInsight-Storm-Clustertypeinrichtung](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Hauptknoten (2), Workerknoten (1+), ZooKeeper-Knoten (3) (kostenlos für ZooKeeper-VMs der Größe A1) |![HDInsight-Spark-Clustertypeinrichtung](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

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

Wenn Sie HDInsight gerade erst testen, sollten Sie nur einen einzigen Workerknoten verwenden. Weitere Informationen zu den Preisen von HDInsight finden Sie unter [HDInsight – Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Die Begrenzung der Clustergröße variiert je nach Azure-Abonnement. Wenden Sie sich an den [Azure-Abrechnungssupport](../azure-portal/supportability/how-to-create-azure-support-request.md), um diese Begrenzung zu erhöhen.

Wenn Sie das Azure-Portal zum Konfigurieren des Clusters verwenden, ist die Knotengröße über die Registerkarte **Configuration + Pricing** (Konfiguration + Preise) verfügbar. Außerdem werden im Portal die Kosten angezeigt, die den unterschiedlichen Knotengrößen zugeordnet sind.

### <a name="virtual-machine-sizes"></a>Größen virtueller Computer

Wählen Sie bei der Bereitstellung von Clustern die Computeressourcen basierend auf der Lösung aus, die Sie bereitstellen möchten. Für HDInsight-Cluster werden die folgenden virtuellen Computer verwendet:

* VMs der Serien A und D1–4: [Größen universeller Linux-VMs](../virtual-machines/sizes-general.md)
* VMs der Serien D11–14: [Größen speicheroptimierter Linux-VMs](../virtual-machines/sizes-memory.md)

Wenn Sie wissen möchten, welchen Wert Sie beim Erstellen eines Clusters mithilfe unterschiedlicher SDKs oder von Azure PowerShell als VM-Größe angeben sollten, lesen Sie unter [VM-Größen für HDInsight-Cluster](../cloud-services/cloud-services-sizes-specs.md#size-tables) nach. Verwenden Sie im verknüpften Artikel in den Tabellen den Wert in der Spalte **Größe** .

> [!IMPORTANT]  
> Wenn Sie mehr als 32 Workerknoten in einem Cluster benötigen, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.

Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../virtual-machines/sizes.md). Informationen zu den Preisen der unterschiedlichen Größen finden Sie unter [HDInsight-Preise](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Anwendung hinzufügen

Eine HDInsight-Anwendung kann von Benutzern in einem Linux-basierten HDInsight-Cluster installiert werden. Sie können Anwendungen von Microsoft, Anwendungen von Drittanbietern oder selbst entwickelte Anwendungen verwenden. Weitere Informationen finden Sie unter [Installieren von Apache Hadoop-Anwendungen von Drittanbietern in Azure HDInsight](hdinsight-apps-install-applications.md).

Die meisten HDInsight-Anwendungen werden auf einem leeren Edgeknoten installiert.  Ein leerer Edgeknoten ist ein virtueller Linux-Computer, auf dem die gleichen Clienttools installiert und konfiguriert sind wie im Hauptknoten. Sie können den Edgeknoten zum Zugreifen auf den Cluster sowie zum Testen und Hosten Ihrer Clientanwendungen verwenden. Weitere Informationen finden Sie unter [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Verwenden leerer Edgeknoten in HDInsight).

### <a name="script-actions"></a>Skriptaktionen

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Erstellung anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion** aufgerufen, die vom Azure-Verwaltungsportal, von HDInsight Windows PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

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

## <a name="next-steps"></a>Nächste Schritte

* [Behandeln von Fehlern bei der Clustererstellung mit Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Was sind HDInsight, das Apache Hadoop-Ökosystem und Hadoop-Cluster?](hadoop/apache-hadoop-introduction.md)
* [Erste Schritte mit Apache Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Verwenden eines Windows-Computers mit Apache Hadoop in HDInsight](hdinsight-hadoop-windows-tools.md)