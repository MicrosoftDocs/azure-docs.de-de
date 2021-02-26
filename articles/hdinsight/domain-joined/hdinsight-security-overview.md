---
title: Übersicht über die Unternehmenssicherheit in Azure HDInsight
description: Erfahren Sie mehr über die verschiedenen Methoden zur Sicherstellung der Unternehmenssicherheit in Azure HDInsight.
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: f4fa1e64e00f2ae027d80960072da7d73d3a89cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946841"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Übersicht über die Unternehmenssicherheit in Azure HDInsight

Azure HDInsight bietet eine Reihe von Methoden, um Ihre Sicherheitsanforderungen im Unternehmen zu erfüllen. Die meisten dieser Lösungen sind standardmäßig nicht aktiviert. Diese Flexibilität ermöglicht es Ihnen, die Sicherheitsfeatures auszuwählen, die für Sie am wichtigsten sind, und hilft Ihnen zu vermeiden, das Sie für nicht erforderliche Features bezahlen. Diese Flexibilität bedeutet auch, dass Sie dafür verantwortlich sind, sicherzustellen, dass die richtigen Lösungen für Ihr Setup und Ihre Umgebung aktiviert sind.

Dieser Artikel befasst sich mit Sicherheitslösungen, indem die Sicherheitslösungen nach den vier traditionellen Sicherheitssäulen gegliedert werden: Umgebungssicherheit, Authentifizierung, Autorisierung und Verschlüsselung.

Dieser Artikel stellt auch das **Azure HDInsight Enterprise-Sicherheitspaket (ESP)** vor, das Active Directory-basierte Authentifizierung, Unterstützung mehrerer Benutzer und die rollenbasierte Zugriffssteuerung für HDInsight-Cluster bietet.

## <a name="enterprise-security-pillars"></a>Enterprise-Sicherheitssäulen

Eine Art der Betrachtung der Unternehmenssicherheit unterteilt Sicherheitslösungen in vier Hauptgruppen, die sich nach der Art der Kontrolle richten. Diese Gruppen werden auch als Sicherheitssäulen bezeichnet und sind die folgenden Typen: Umkreissicherheit, Authentifizierung, Autorisierung und Verschlüsselung.

### <a name="perimeter-security"></a>Umgebungssicherheit

Die Umgebungssicherheit in HDInsight wird mithilfe von [virtuellen Netzwerken](../hdinsight-plan-virtual-network-deployment.md) erreicht. Ein Unternehmensadministrator kann einen Cluster in einem virtuellen Netzwerk (VNET) erstellen und den Zugriff auf das virtuelle Netzwerk mithilfe von Netzwerksicherheitsgruppen (NSG) einschränken. Nur die zulässigen IP-Adressen in den eingehenden NSG-Regeln können mit dem HDInsight-Cluster kommunizieren. Diese Konfiguration bietet Umgebungssicherheit.

Alle in einem VNET bereitgestellten Cluster verfügen ebenfalls über einen privaten Endpunkt. Dieser Endpunkt wird in eine private IP-Adresse innerhalb des VNET für privaten HTTP-Zugriff auf die Clustergateways aufgelöst.

### <a name="authentication"></a>Authentifizierung

Das [Enterprise-Sicherheitspaket](apache-domain-joined-architecture.md) von HDInsight unterstützt die Active Directory-basierte Authentifizierung, Unterstützung mehrerer Benutzer und rollenbasierte Zugriffssteuerung. Die Active Directory-Integration wird durch die Verwendung von [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) erreicht. Mit diesen Funktionen können Sie einen HDInsight-Cluster erstellen, der in eine verwaltete Active Directory-Domäne eingebunden ist. Dann können Sie eine Liste mit Mitarbeitern des Unternehmens konfigurieren, die sich beim Cluster authentifizieren können.

In dieser Konfiguration können sich Mitarbeiter des Unternehmens mit ihren Domänenanmeldeinformationen bei den Clusterknoten anmelden. Sie können auch ihre Domänenanmeldeinformationen verwenden, um sich bei anderen genehmigten Endpunkten zu authentifizieren. Markieren Sie Apache Ambari Views, ODBC, JDBC, PowerShell und REST-APIs zur Interaktion mit dem Cluster mit „Gefällt mir“.

### <a name="authorization"></a>Authorization

In den meisten Unternehmen hat es sich bewährt, dass nicht jeder Mitarbeiter Vollzugriff auf alle Unternehmensressourcen hat. Analog dazu kann der Administrator für die Clusterressourcen Richtlinien für die rollenbasierte Zugriffssteuerung definieren. Diese Aktion ist nur in den ESP-Clustern verfügbar.

Der Hadoop-Administrator kann die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) konfigurieren. Die Konfigurationen sichern Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) und [Kafka](apache-domain-joined-run-kafka.md) mit Apache Ranger-Plug-Ins ab. Durch die Konfiguration von RBAC-Richtlinien können Sie Berechtigungen einer Rolle in der Organisation zuordnen. Diese Abstraktionsebene erleichtert die Sicherstellung, dass Personen nur über die Berechtigungen verfügen, die sie zur Erfüllung ihrer beruflichen Aufgaben benötigen. Mit Ranger können Sie auch den Datenzugriff von Mitarbeitern und alle Änderungen an den Zugriffssteuerungsrichtlinien überwachen.

So kann der Administrator etwa [Apache Ranger](https://ranger.apache.org/) zum Festlegen von Zugriffssteuerungsrichtlinien für Hive konfigurieren. Mit dieser Funktion wird die Filterung auf Zeilen- und Spaltenebene (Datenmaskierung) sichergestellt. Zudem werden die sensiblen Daten für nicht autorisierte Benutzer gefiltert.

### <a name="auditing"></a>Überwachung

Die Überwachung des Zugriffs auf die Clusterressource ist erforderlich, um unbefugten oder versehentlichen Zugriff auf die Ressourcen nachzuverfolgen. Dies ist ebenso wichtig wie der Schutz der Clusterressourcen vor nicht autorisiertem Zugriff.

Der Administrator kann sämtliche Zugriffe auf die Ressourcen und Daten des HDInsight-Clusters anzeigen und entsprechende Berichte erstellen. Der Administrator kann sämtliche Änderungen an den Zugriffssteuerungsrichtlinien anzeigen und entsprechende Berichte erstellen.

[Aktivieren Sie Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing), und zeigen Sie die Tabellen mit Überwachungsdatensätzen an, um auf Überwachungsprotokolle von Apache Ranger und Ambari sowie auf SSH-Zugriffsprotokolle zuzugreifen.

### <a name="encryption"></a>Verschlüsselung

Der Schutz von Daten ist wichtig, um die Sicherheits- und Compliance-Anforderungen des Unternehmens zu erfüllen. Sie sollten den Zugriff auf Daten durch nicht autorisierte Mitarbeitern nicht nur beschränken, sondern Sie sollten diesen auch verschlüsseln.

HDInsight unterstützt die Verschlüsselung ruhender Daten mit plattformseitig und [kundenseitig verwalteten Schlüsseln](../disk-encryption.md). Die Verschlüsselung von Daten während der Übertragung erfolgt sowohl mit TLS als auch mit IPSec. Weitere Informationen finden Sie unter [Verschlüsselung während der Übertragung für Azure HDInsight](encryption-in-transit.md).

### <a name="compliance"></a>Kompatibilität

Azure-Complianceangebote basieren auf unterschiedlichen Arten von Zusicherungen, z. B. formalen Zertifizierungen, Nachweisen, Validierungen und Autorisierungen und Bewertungen, die von unabhängigen externen Prüfungsgesellschaften erstellt wurden, sowie Vertragsänderungen, Selbstbewertungen und Kundenleitfäden, die von Microsoft erstellt wurden. Informationen zur HDInsight-Compliance finden Sie im [Microsoft Trust Center](https://www.microsoft.com/trust-center) und in der [Übersicht der Microsoft Azure-Complianceangebote](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (in englischer Sprache).

## <a name="shared-responsibility-model"></a>Modell der gemeinsamen Zuständigkeit

Die folgende Abbildung fasst die wichtigsten Systemsicherheitsbereiche und die Sicherheitslösungen zusammen, die Ihnen in den einzelnen Bereichen zur Verfügung stehen. Außerdem wird hervorgehoben, für welche Sicherheitsbereiche Sie als Kunde die Verantwortung haben, und in welchen Bereichen HDInsight als Dienstanbieter verantwortlich ist.

![Diagramm zu den geteilten Verantwortlichkeiten von HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Die folgende Tabelle enthält Links zu Ressourcen für jede Art von Sicherheitslösung.

| Sicherheitsbereich | Verfügbare Lösungen | Verantwortliche Partei |
|---|---|---|
| Datenzugriffssicherheit | Konfigurieren von [Zugriffssteuerungslisten (ACLs)](../../storage/blobs/data-lake-storage-access-control.md) für Azure Data Lake Storage Gen1 und Gen2  | Kunde |
|  | Aktivieren der Eigenschaft [Sichere Übertragung erforderlich](../../storage/common/storage-require-secure-transfer.md) für Speicherkonten | Kunde |
|  | Konfigurieren von [Azure Storage-Firewalls](../../storage/common/storage-network-security.md) und virtuellen Netzwerken | Kunde |
|  | Konfigurieren von [Azure Virtual Network-Dienstendpunkten](../../virtual-network/virtual-network-service-endpoints-overview.md) für Cosmos DB und [Azure SQL-Datenbank](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) | Kunde |
|  | Stellen Sie sicher, dass das Feature [Verschlüsselung während der Übertragung](./encryption-in-transit.md) für die Verwendung von TLS und IPSec für die clusterinterne Kommunikation aktiviert ist. | Kunde |
|  | Konfigurieren von [Kunden verwalteter Schlüssel](../../storage/common/customer-managed-keys-configure-key-vault.md) für die Azure Storage-Verschlüsselung | Kunde |
|  | Steuern des Zugriffs auf Ihre Daten durch Azure-Support mit [Kunden-Lockbox](../../security/fundamentals/customer-lockbox-overview.md) | Kunde |
| Anwendungs- und Middleware-Sicherheit | Integration mit AAD-DS und [ESP konfigurieren](apache-domain-joined-configure-using-azure-adds.md), oder verwenden Sie [HIB für die OAuth-Authentifizierung](identity-broker.md)| Kunde |
|  | Konfigurieren der Richtlinien für die [Apache Ranger-Autorisierung](apache-domain-joined-run-hive.md) | Kunde |
|  | Verwenden von [Azure Monitor-Protokollen](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Kunde |
| Betriebssystemsicherheit | Erstellen von Clustern mit dem aktuellsten sicheren Basisimage | Kunde |
|  | Sicherstellen, dass das [Betriebssystempatching](../hdinsight-os-patching.md) in regelmäßigen Abständen durchgeführt wird | Kunde |
|  | Sicherstellen der [CMK-Datenträgerverschlüsselung für VMs](../disk-encryption.md) | Kunde |
| Netzwerksicherheit | Konfigurieren eines [virtuellen Netzwerks](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurieren [eingehender NSG-Regeln (Netzwerksicherheitsgruppe)](../control-network-traffic.md) oder [Private Link](../hdinsight-private-link.md) | Kunde |
|  | Konfigurieren einer [ausgehenden Datenverkehrseinschränkung](../hdinsight-restrict-outbound-traffic.md) mit Firewall | Kunde |
|  | Konfigurieren der [IPSec-Verschlüsselung während der Übertragung](encryption-in-transit.md) zwischen Clusterknoten | Kunde |
| Virtualisierte Infrastruktur | – | HDInsight (Cloudanbieter) |
| Sicherheit der physischen Infrastruktur | – | HDInsight (Cloudanbieter) |

## <a name="next-steps"></a>Nächste Schritte

* [Planen für HDInsight-Cluster mit Enterprise-Sicherheitspaket](apache-domain-joined-architecture.md)
* [Konfigurieren eines HDInsight-Clusters mit dem Enterprise-Sicherheitspaket mithilfe der Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md)
* [Verwalten von HDInsight-Clustern mit ESP](apache-domain-joined-manage.md)