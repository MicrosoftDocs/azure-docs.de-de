---
title: Geschäftskontinuität in Azure HDInsight
description: Dieser Artikel bietet einen Überblick über bewährte Methoden, Verfügbarkeit bei einer einzelnen Region und Optimierungsmöglichkeiten für die Geschäftskontinuitätsplanung in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: Hochverfügbarkeit, Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: f83f5aec264aeae1a729e81932843825a0ce6673
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546941"
---
# <a name="azure-hdinsight-business-continuity"></a>Geschäftskontinuität in Azure HDInsight

Azure HDInsight-Cluster sind von vielen Azure-Diensten wie Speicher, Datenbanken, Active Directory, Active Directory Domain Services, Netzwerkdiensten und Key Vault abhängig. Eine gut entworfene, hochverfügbare und fehlertolerante Analyseanwendung sollte mit ausreichend Redundanz entworfen werden, um regionalen oder lokalen Unterbrechungen bei einem oder mehreren dieser Dienste standzuhalten. Dieser Artikel bietet einen Überblick über bewährte Methoden, Verfügbarkeit bei einer einzelnen Region und Optimierungsoptionen für die Geschäftskontinuitätsplanung.

## <a name="general-best-practices"></a>Allgemeine bewährte Methoden

In diesem Abschnitt werden einige bewährte Methoden erläutert, die Sie bei der Geschäftskontinuitätsplanung berücksichtigen sollten.

* Ermitteln Sie die minimale Geschäftsfunktionalität, die Sie bei einem Notfall benötigen und die Gründe dafür. Überprüfen Sie z. B., ob Sie Failoverfunktionen für die Datentransformationsebene (gelb) *und* die Datenbereitstellungsebene (blau) benötigen oder nur ein Failover für die Datenbereitstellungsebene erforderlich ist.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="Datentransformations- und -bereitstellungsebene":::

* Segmentieren Sie Ihre Cluster basierend auf der Workload, dem Entwicklungslebenszyklus und der Abteilung. Eine höhere Anzahl von Clustern verringert die Wahrscheinlichkeit eines einzelnen großen Ausfalls, der sich auf mehrere verschiedene Geschäftsprozesse auswirkt.

* Konfigurieren Sie Ihre sekundären Regionen als schreibgeschützt. Failoverregionen mit sowohl Lese- als auch Schreibfunktionen können zu komplexen Architekturen führen.

* Vorübergehende Cluster sind bei einem Notfall einfacher zu verwalten. Entwerfen Sie Ihre Workloads so, dass die Cluster durchlaufen werden können und in den Clustern kein Status beibehalten wird.

* Häufig können Workloads bei einem Notfall nicht abgeschlossen werden und müssen in der neuen Region neu gestartet werden. Entwerfen Sie Ihre Workloads so, dass sie idempotent sind.

* Verwenden Sie bei der Clusterbereitstellung Automatisierung, und stellen Sie sicher, dass die Clusterkonfigurationseinstellungen so weit wie möglich im entsprechenden Skript enthalten sind, um bei einem Notfall eine schnelle und vollständig automatisierte Bereitstellung sicherzustellen.

* Verwenden Sie die Azure-Überwachungstools in HDInsight, um ungewöhnliches Verhalten im Cluster zu erkennen und entsprechende Warnmeldungen festzulegen. Sie können die vorkonfigurierten spezifischen Verwaltungslösungen für HDInsight-Cluster bereitstellen, die wichtige Leistungsmetriken für den jeweiligen Clustertyp erfassen. Weitere Informationen finden Sie unter [Verwenden von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Abonnieren Sie Azure-Integritätswarnungen, um über Dienstprobleme, geplante Wartungen sowie Integritäts- und Sicherheitsempfehlungen für ein Abonnement, einen Dienst oder eine Region benachrichtigt zu werden. Integritätsbenachrichtigungen, die die Problemursache und eine feste ETA enthalten, helfen Ihnen bei der besseren Ausführung von Failovern und Failbacks. Weitere Informationen finden Sie in der [Dokumentation zu Azure Service Health](../service-health/index.yml).

## <a name="single-region-availability"></a>Verfügbarkeit bei einer einzelnen Region

Ein einfaches HDInsight-System verfügt über die folgenden Komponenten. Alle Komponenten verfügen über eigene Fehlertoleranzmechanismen für eine einzelne Region.

* Compute (virtuelle Computer): Azure HDInsight-Cluster
* Metastore(s): Azure SQL-Datenbank
* Speicher: Azure Data Lake Gen2 oder Blob Storage
* Authentifizierung: Azure Active Directory, Azure Active Directory Domain Services, Enterprise-Sicherheitspaket
* Domänennamensauflösung: Azure DNS

Es können auch weitere, optionale Dienste verwendet werden, z. B. Azure Key Vault und Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="Datentransformations- und -bereitstellungsebene":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight-Cluster (Compute)

HDInsight bietet eine SLA mit einer Verfügbarkeit von 99,9 %. HDInsight arbeitet mit vielen Diensten zusammen, die standardmäßig im Hochverfügbarkeitsmodus sind, um Hochverfügbarkeit in einer einzelnen Bereitstellung zu gewährleisten. Fehlertoleranzmechanismen in HDInsight werden sowohl von hochverfügbaren Diensten von Microsoft als auch solchen aus dem Apache-OSS-Ökosystem bereitgestellt.

Die folgenden Dienste sind für Hochverfügbarkeit konzipiert:

#### <a name="infrastructure"></a>Infrastruktur

* Aktive Hauptknoten und Standbyhauptknoten
* Mehrere Gatewayknoten
* Drei Zookeeper-Quorumknoten
* Auf Fehler- und Updatedomänen verteilte Workerknoten

#### <a name="service"></a>Dienst

* Apache Ambari-Server
* Anwendungszeitachsenserver für YARN
* Auftragsverlaufsserver für Hadoop MapReduce
* Apache Livy
* HDFS
* YARN Resource Manager
* HBase Master

Weitere Informationen finden Sie in der Dokumentation zu [von Azure HDInsight unterstützten Hochverfügbarkeitsdiensten](./hdinsight-high-availability-components.md).

Für eine Beeinträchtigung der Geschäftsfunktionalität muss nicht unbedingt ein Notfall eintreten. Dienstincidents in einem oder mehreren der folgenden Dienste in einer einzelnen Region können ebenfalls zu einem Verlust der erwarteten Geschäftsfunktionalität führen.

### <a name="hdinsight-metastore"></a>HDInsight-Metastore

HDInsight verwendet [Azure SQL-Datenbank](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) als Metastore, der eine SLA mit einer Verfügbarkeit von 99,99 % bietet. Drei Replikate der Daten werden in einem Rechenzentrum mit synchroner Replikation beständig gespeichert. Bei Verlust eines Replikats wird nahtlos ein neues Replikat bereitgestellt. [Aktive Georeplikation](../azure-sql/database/active-geo-replication-overview.md) wird standardmäßig mit maximal vier Rechenzentren unterstützt. Bei einem Failover (manuell oder durch das Rechenzentrum) erhält das erste Replikat in der Hierarchie automatisch Lese- und Schreibfunktionen. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>HDInsight Storage

Für HDInsight wird als zugrunde liegende Speicherebene Azure Data Lake Storage Gen2 empfohlen. [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), einschließlich Azure Data Lake Storage Gen2, bietet eine SLA mit einer Verfügbarkeit von 99,9 %. HDInsight verwendet den LRS-Dienst, bei dem drei Replikate der Daten beständig in einem Rechenzentrum gespeichert werden und die Replikation synchron erfolgt. Bei Verlust eines Replikats wird nahtlos ein neues Replikat bereitgestellt.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) bietet eine SLA mit einer Verfügbarkeit von 99,9 %. Active Directory ist ein globaler Dienst mit mehreren Ebenen interner Redundanz und automatischer Wiederherstellbarkeit. Informieren Sie sich darüber, wie [Microsoft die Zuverlässigkeit von Azure Active Directory kontinuierlich verbessert](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) bietet eine SLA mit einer Verfügbarkeit von 99,9 %. Azure AD DS ist ein hochverfügbarer Dienst, der in Rechenzentren weltweit gehostet wird. Replikatgruppen sind eine Previewfunktion in Azure AD DS, die eine geografische Notfallwiederherstellung ermöglicht, wenn eine Azure-Region offline geht. Weitere Informationen finden Sie unter [Konzepte und Features zu Replikatgruppen für Azure Active Directory Domain Services (Vorschauversion)](../active-directory-domain-services/concepts-replica-sets.md).  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) bietet eine SLA mit einer Verfügbarkeit von 100 %. HDInsight verwendet Azure DNS an verschiedenen Stellen für die Auflösung von Domänennamen.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Kosten- und Komplexitätsoptimierungen bei mehreren Regionen

Für eine Verbesserung der Geschäftskontinuität mithilfe von regionsübergreifender Notfallwiederherstellung mit Hochverfügbarkeit sind komplexere und mit höheren Kosten verbundene Architekturstrukturen erforderlich. In den folgenden Tabellen werden einige technische Bereiche erläutert, die möglicherweise die Gesamtkosten erhöhen.

### <a name="cost-optimizations"></a>Kostenoptimierungen

|Fläche|Ursache für die hohen Kosten|Optimierungsstrategien|
|----|------------------------|-----------------------|
|Datenspeicherung|Duplizieren von primären Daten/Tabellen in eine sekundäre Region|Replizieren Sie nur zusammengestellte Daten.|
|Ausgehende Daten|Ausgehende regionsübergreifende Datenübertragungen haben ihren Preis. Sehen Sie sich die Preisinformationen für Bandwidth an.|Replizieren Sie nur zusammengestellte Daten, um die Menge ausgehender Daten in den Regionen zu verringern.|
|Clustercomputeressourcen|Weitere HDInsight-Cluster in der sekundären Region|Verwenden Sie automatisierte Skripts zum Bereitstellen sekundärer Computeressourcen bei einem Ausfall der primären Region. Verwenden Sie automatische Skalierung, um die Größe der sekundären Cluster auf das Minimum zu beschränken. Verwenden Sie günstigere VM-SKUs. Erstellen Sie sekundäre Cluster in Regionen, in denen VM-SKUs möglicherweise ermäßigt sind.|
|Authentifizierung |Mehrbenutzerszenarios in der sekundären Region verursachen zusätzliche Azure AD DS-Setups.|Vermeiden Sie Mehrbenutzersetups in der sekundären Region.|

### <a name="complexity-optimizations"></a>Komplexitätsoptimierungen

|Fläche|Ursache für die hohe Komplexität|Optimierungsstrategien|
|----|------------------------|-----------------------|
|Lese- und Schreibmuster |Ein aktivierter Lese- und Schreibzugriff ist sowohl für die primäre als auch für die sekundäre Region erforderlich. |Entwerfen Sie die sekundäre Region als schreibgeschützte Region.|
|Keine RPO & RTO |Es wird gefordert, dass es zu keinem Datenverlust (RPO = 0) und keiner Downtime (RTO = 0) kommt. |Entwerfen Sie RPO und RTO so, dass Sie die Anzahl von Komponenten reduzieren, für die ein Failover erforderlich ist.|
|Geschäftsfunktionalität |In der sekundären Region wird die vollständige Geschäftsfunktionalität der primären Region verlangt. |Überprüfen Sie, ob das absolute Minimum wichtiger Teile der Geschäftsfunktionalität in der sekundären Region ausreicht.|
|Konnektivität |Es wird gefordert, dass alle Upstream- und Downstreamsysteme in der primären Region auch eine Verbindung mit der sekundären Region herstellen.|Beschränken Sie die Konnektivität mit der sekundären Region auf das absolute Minimum.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel erörterten Themen finden Sie unter:

* [Architekturen zur Geschäftskontinuität von Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Fallstudie für eine hochverfügbare Lösungsarchitektur für Azure HDInsight](./hdinsight-high-availability-case-study.md)
* [Was sind Apache Hive und HiveQL in Azure HDInsight?](./hadoop/hdinsight-use-hive.md)