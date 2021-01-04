---
title: Bewährte Methoden für die Clusterverwaltung – Azure HDInsight
description: Informieren Sie sich über bewährte Methoden für die Verwaltung von HDInsight-Clustern.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3a96a7116c83bf9ea2c3798c335c6cefcbdbc36d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659487"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Verwaltung von HDInsight-Clustern – Bewährte Methoden

Informieren Sie sich über bewährte Methoden für die Verwaltung von HDInsight-Clustern.

## <a name="how-do-i-create-hdinsight-clusters"></a>Wie erstelle ich HDInsight-Cluster?

| Option | Dokumente |
|---|---|
| Azure Data Factory | [Erstellen bedarfsgesteuerter Apache Hadoop-Cluster in HDInsight mit Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Benutzerdefinierte Resource Manager-Vorlage | [Erstellen von Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Schnellstartvorlagen | [HDInsight-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-Beispiele | [HDInsight – Azure-Beispiele](/samples/browse/?products=azure-hdinsight) |
| Azure-Portal | [Erstellen von Linux-basierten Clustern in HDInsight über das Azure-Portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure-Befehlszeilenschnittstelle | [Erstellen von HDInsight-Clustern mit der Azure-Befehlszeilenschnittstelle](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Erstellen von Linux-basierten Clustern in HDInsight mit Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Erstellen von Apache Hadoop-Clustern mithilfe der Azure-REST-API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true), [Python](/python/api/overview/azure/hdinsight?preserve-view=true&view=azure-python), [Java](/java/api/overview/azure/hdinsight?preserve-view=true&view=azure-java-stable), [Go](./hdinsight-go-sdk-overview.md) |

> [!Note]
> Wenn Sie einen Cluster erstellen und den Clusternamen eines zuvor erstellten Clusters wiederverwenden, warten Sie, bis der vorherige Cluster vollständig gelöscht ist, bevor Sie den neuen Cluster erstellen.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Wie passe ich HDInsight-Cluster an?

| Option | Dokumente |
|---|---|
| Skriptaktionen | [Anpassen von Azure HDInsight-Clustern mithilfe von Skriptaktionen](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Externe Metastores | [Verwenden von externen Metadatenspeichern in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Benutzerdefinierte Ambari-Datenbank | [Einrichten von HDInsight-Clustern mit einer benutzerdefinierten Ambari-Datenbank](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Welche Fehler können beim Erstellen von Clustern auftreten?

| Fehler | Weitere Informationen |
|---|---|
| Kein Kontingent | Es gibt Kontingente für die Anzahl von Kernen, die Sie in Ihrem Abonnement in jeder Region erstellen können. Weitere Informationen finden Sie unter [Kapazitätsplanung: Kontingente](./hdinsight-capacity-planning.md). |
| Keine weiteren IP-Adressen verfügbar | Jedes VNet verfügt über eine begrenzte Anzahl von IP-Adressen. Wenn Sie einen HDInsight-Cluster erstellen, verwendet jeder Knoten (einschließlich ZooKeeper- und Gatewayknoten) einige dieser zugewiesenen IP-Adressen. Wenn alle IP-Adressen verwendet werden, tritt dieser Fehler auf.  |
| Regeln für Netzwerksicherheitsgruppen (NSG) verhindern die Kommunikation mit HDInsight-Ressourcenanbietern | Wenn Sie NSGs oder benutzerdefinierte Routen (UDRs) verwenden, um den beim HDInsight-Cluster eingehenden Datenverkehr zu steuern, müssen Sie sicherstellen, dass der Cluster mit wichtigen Integritäts- und Verwaltungsdiensten von Azure kommunizieren kann. Weitere Informationen finden Sie unter [NSG-Diensttags (Netzwerksicherheitsgruppen) für Azure HDInsight](./hdinsight-service-tags.md). |
| Wiederverwendung des Clusternamens | Wenn Sie einen zuvor bereits verwendeten Clusternamen nutzen, müssen Sie einige Minuten warten, bevor Sie den Cluster neu erstellen. Andernfalls wird die Meldung angezeigt, dass die Ressource bereits vorhanden ist. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Wie werden laufende HDInsight-Cluster verwaltet?

| Option | Dokumente |
|---|---|
| Autoscale | [Automatisches Skalieren von Azure HDInsight-Clustern](./hdinsight-autoscale-clusters.md) |
| Manuelle Skalierung | [Skalieren von Azure HDInsight-Clustern](./hdinsight-scaling-best-practices.md) |
| Überwachung mit Ambari| [Überwachen der Clusterleistung in Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Überwachen mit Azure Monitor-Protokollen | [Verwenden von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| Dienstprobleme, geplante Wartung, Integritäts- und Sicherheitsempfehlungen | [Abonnieren von abonnementspezifischen Service Health-Warnungen](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Wie erhalte ich Informationen zu gelöschten HDInsight-Clustern?

### <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

Sie können die folgende Abfrage in Verbindung mit Azure Monitor-Protokollen verwenden, um gelöschte Cluster zu überwachen.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Nächste Schritte

* [Kapazitätsplanung für HDInsight-Cluster](./hdinsight-capacity-planning.md)
* [Was sind die Standard- und die empfohlenen Knotenkonfigurationen für Azure HDInsight?](./hdinsight-supported-node-configuration.md)
