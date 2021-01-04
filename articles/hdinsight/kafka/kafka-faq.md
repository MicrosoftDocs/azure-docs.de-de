---
title: Häufig gestellte Fragen zu Apache Kafka in Azure HDInsight
description: Hier erhalten Sie Antworten zu häufig gestellten Fragen zu Apache Kafka in Azure HDInsight, einem verwalteten Hadoop-Clouddienst.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 84c48f14ae57cfa4aa3290924d3d170c075fe3a6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532549"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Häufig gestellte Fragen zu Apache Kafka in Azure HDInsight

In diesem Artikel werden einige übliche Fragen zum Verwenden von Apache Kafka in Azure HDInsight beantwortet.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Welche Kafka-Versionen werden von HDInsight unterstützt?

Weitere Informationen zu den von HDInsight offiziell unterstützten Komponenten finden Sie unter [Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Sie sollten immer die neueste Version zu verwenden, um die bestmögliche Leistung und Benutzerumgebung sicherzustellen.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Welche Ressourcen werden in einem HDInsight Kafka-Cluster bereitgestellt, und welche Ressourcen werden mir in Rechnung gestellt?

Ein HDInsight Kafka-Cluster umfasst die folgenden Ressourcen:

* Hauptknoten
* Zookeeper-Knoten
* Brokerknoten (Workerknoten) 
* Azure Managed Disks, die Brokerknoten zugeordnet sind
* Gatewayknoten

Alle diese Ressourcen werden Ihnen auf Basis Ihres [HDInsight-Preismodells](https://azure.microsoft.com/pricing/details/hdinsight/) in Rechnung gestellt. Davon ausgenommen sind nur Gatewayknoten. Gatewayknoten werden Ihnen nicht in Rechnung gestellt.

Eine ausführlichere Beschreibung der verschiedenen Knotentypen finden Sie unter [Virtuelle Netzwerkarchitektur mit Azure HDInsight](../hdinsight-virtual-network-architecture.md). Die Preisgestaltung basiert auf der Nutzung der Knoten pro Minute. Die Preise variieren abhängig von Knotengröße, Anzahl der Knoten, Typ des verwendeten verwalteten Datenträgers und Region.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Funktionieren Apache Kafka-APIs mit HDInsight?

Ja, HDInsight verwendet native Kafka-APIs. Ihr Clientanwendungscode muss nicht geändert werden. Siehe [Tutorial: Verwenden der Apache Kafka Producer- und Consumer-APIs](./apache-kafka-producer-consumer-api.md). In diesem Tutorial erfahren Sie, wie Sie Java-basierte Producer-/Consumer-APIs mit Ihrem Cluster verwenden können.

## <a name="can-i-change-cluster-configurations"></a>Kann ich Clusterkonfigurationen ändern?

Ja, über das Ambari-Portal. Jede Komponente im Portal hat einen **configs** -Abschnitt, in dem Komponentenkonfigurationen geändert werden können. Einige Änderungen bedingen möglicherweise Brokerneustarts.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Welchen Authentifizierungstyp unterstützt HDInsight für Apache Kafka?

Durch Verwenden von [Enterprise-Sicherheitspaket (ESP)](../domain-joined/apache-domain-joined-architecture.md) können Sie für Kafka-Cluster Sicherheit auf Themenebene erreichen. Siehe [Tutorial: Konfigurieren von Apache Kafka-Richtlinien in HDInsight mit dem Enterprise-Sicherheitspaket (Vorschau)](../domain-joined/apache-domain-joined-run-kafka.md).

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Werden meine Daten verschlüsselt? Kann ich meine eigenen Schlüssel verwenden?

Alle Kafka-Nachrichten auf den verwalteten Datenträgern werden mit [Azure-Speicherdienstverschlüsselung](../../storage/common/storage-service-encryption.md) (Storage Service Encryption, SSE) verschlüsselt. Daten während einer Übertragung (etwa Daten, die von Clients an Broker übermittelt werden bzw. umgekehrt) sind standardmäßig nicht verschlüsselt. Sie können solchen Datenverkehr verschlüsseln, indem [Sie TLS selbst einrichten](./apache-kafka-ssl-encryption-authentication.md). Außerdem können Sie mit HDInsight eigene Schlüssel verwalten, um die ruhenden Daten zu verschlüsseln. Weitere Informationen finden Sie unter [Datenträgerverschlüsselung mit kundenseitig verwalteten Schlüsseln](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Wie verbinde ich Clients mit meinem Cluster?

Damit Kafka-Clients mit Kafka-Brokern kommunizieren können, müssen sie die Broker über das Netzwerk erreichen können. Für HDInsight-Cluster ist das virtuelle Netzwerk (VNet) die Sicherheitsgrenze. Daher besteht die einfachste Möglichkeit zum Verbinden von Clients mit Ihrem HDInsight-Cluster darin, die Clients im selben VNet zu erstellen wie den Cluster. Weitere Szenarien sind:

* Verbinden von Clients in einem anderen Azure-VNet: Koppeln Sie das Cluster-VNet und das Client-VNet, und konfigurieren Sie den Cluster für [IP-Ankündigung](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Wenn IP-Ankündigung verwendet wird, müssen Kafka-Clients anstelle von voll qualifizierten Domänennamen (FQDNs) Broker-IP-Adressen verwenden, um Verbindungen mit den Brokern herzustellen.

* Verbinden von lokalen Clients: Verwenden eines VPN-Netzwerks und Einrichten von benutzerdefinierten DNS-Servern, wie dies unter [Planen eines virtuellen Netzwerks für Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) beschrieben ist.

* Erstellen eines öffentlichen Endpunkts für Ihren Kafka-Dienst: Wenn die Sicherheitsanforderungen Ihres Unternehmens dies zulassen, können Sie einen öffentlichen Endpunkt für Ihre Kafka-Broker oder einen selbstverwalteten Open-Source-REST-Endpunkt mit einem öffentlichen Endpunkt bereitstellen.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Kann ich weiteren Speicherplatz zu einem vorhandenen Cluster hinzufügen?

Um den für Kafka-Nachrichten verfügbaren Speicherplatz zu erhöhen, können Sie die Anzahl der Knoten erhöhen. Das Hinzufügen von weiteren Datenträgern zu einem vorhandenen Cluster wird derzeit nicht unterstützt.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kann ein Kafka-Cluster mit Databricks arbeiten? 

Ja, Kafka-Cluster arbeiten mit Databricks, solange sie sich im selben virtuellen Netzwerk befinden. Um einen Kafka-Cluster mit Databricks zu verwenden, erstellen Sie ein virtuelles Netzwerk mit einem HDInsight-Kafka-Cluster, und geben Sie dieses Netzwerk an, wenn Sie Ihren Databricks-Arbeitsbereich verwenden und VNet Injection verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Azure Databricks in Ihrem virtuellen Azure-Netzwerk (VNet Injection)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Beim Erstellen des Databricks-Arbeitsbereichs müssen Sie die Bootstrap-Brokernamen des Kafka-Clusters angeben. Informationen zum Abrufen der Kafka-Brokernamen finden Sie unter [Beschaffen der Apache Zookeeper- und Broker-Hostinformationen](./apache-kafka-get-started.md#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Wie kann ich maximale Datendauerhaftigkeit erreichen?

Datendauerhaftigkeit ermöglicht es Ihnen, das niedrigste Risiko von Nachrichtenverlust zu erzielen. Um die maximale Datendauerhaftigkeit zu erreichen, empfehlen wir die folgenden Einstellungen:

* Verwenden Sie in den meisten Regionen einen Mindestreplikationsfaktor von 3.
* Verwenden Sie in Regionen mit nur zwei Fehlerdomänen einen Mindestreplikationsfaktor von 4.
* Deaktivieren Sie unklare Koordinatorauswahlen (Leader Elections).
* Legen Sie **min.insync.replicas** auf mindestens „2“ fest. Hierdurch wird die Anzahl der Replikate geändert, die vollständig mit dem Koordinator synchronisiert sein müssen, bevor ein Schreibvorgang ausgeführt werden kann.
* Legen Sie die **ack** -Eigenschaft auf **all** fest. Diese Eigenschaft erfordert, dass alle Replikate alle Meldungen bestätigen.

Das Konfigurieren von Kafka für eine höhere Datenkonsistenz wirkt sich auf die Verfügbarkeit von Brokern aus, die Anforderungen erstellen.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Kann ich meine Daten in mehrere Cluster replizieren?

Ja, Daten können über Kafka MirrorMaker in mehrere Cluster repliziert werden. Ausführliche Informationen zum Einrichten von MirrorMaker finden Sie unter [Verwenden von MirrorMaker zum Replizieren von Apache Kafka-Themen mit Kafka in HDInsight](apache-kafka-mirroring.md). Außerdem gibt es weitere selbstverwaltete Open-Source-Technologien und Anbieter, mit denen sich Replikation in mehrere Cluster umsetzen lässt, so z. B. [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Kann ich meinen Cluster aktualisieren (upgraden)? Wie sollte ich meinen Cluster aktualisieren?

Zurzeit werden keine direkten Clusterversionsupgrades unterstützt. Wenn Sie Ihren Cluster auf eine höhere Kafka-Version aktualisieren möchten, erstellen Sie einen neuen Cluster mit der gewünschten Version, und migrieren Sie Ihre Kafka-Clients, sodass diese den neuen Cluster verwenden.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Wie überwache ich meinen Kafka-Cluster?

Verwenden Sie Azure Monitor, um ihre [Kafka-Protokolle](./apache-kafka-log-analytics-operations-management.md) zu analysieren.

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten von TLS-Verschlüsselung und -Authentifizierung für Apache Kafka in Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Verwenden von MirrorMaker zum Replizieren von Apache Kafka-Themen mit Kafka in HDInsight](./apache-kafka-mirroring.md)