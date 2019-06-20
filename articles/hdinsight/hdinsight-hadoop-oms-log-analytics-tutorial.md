---
title: Verwenden von Azure Monitor-Protokollen zum Überwachen von Azure HDInsight-Clustern
description: Erfahren Sie, wie Sie Azure Monitor-Protokolle zum Überwachen von Aufträgen verwenden, die in einem HDInsight-Cluster ausgeführt werden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 16659a335ef6126e75f5a9a99784e71afa056bef
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479266"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Verwenden von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern

Erfahren Sie, wie Sie Azure Monitor-Protokolle für die Überwachung von Hadoop-Clustervorgängen in HDInsight aktivieren und eine HDInsight-Überwachungslösung hinzufügen.

[Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md) ist ein Dienst in Azure Monitor, der Ihre cloudbasierten und lokalen Umgebungen überwacht, um deren Verfügbarkeit und Leistung sicherzustellen. Er sammelt Daten, die von Ressourcen in Ihren cloudbasierten und lokalen Umgebungen sowie von anderen Überwachungstools generiert werden, um Analysen für mehrere Quellen zu ermöglichen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Log Analytics-Arbeitsbereich**. Sie können sich diesen Arbeitsbereich als einzigartige Azure Monitor-Protokollumgebung mit eigenem Datenrepository, eigenen Datenquellen und eigenen Lösungen vorstellen. Eine Anleitung hierzu finden Sie unter [Erste Schritte mit einem Log Analytics-Arbeitsbereich](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Ein Azure HDInsight-Cluster**. Derzeit können Sie Azure Monitor-Protokolle mit den folgenden HDInsight-Clustertypen verwenden:

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Anweisungen zum Erstellen von HDInsight-Clustern finden Sie unter [Hadoop-Tutorial: Erste Schritte bei der Verwendung von Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Azure PowerShell Az-Modul**.  Siehe [Einführung in das neue Azure PowerShell Az-Modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> Es wird empfohlen, sowohl den HDInsight-Cluster als auch den Log Analytics-Arbeitsbereich in derselben Region anzuordnen, um die Leistung zu erhöhen. Azure Monitor-Protokolle sind nicht in allen Azure-Regionen verfügbar.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Aktivieren von Azure Monitor-Protokollen im Azure-Portal

In diesem Abschnitt konfigurieren Sie einen vorhandenen HDInsight Hadoop-Cluster zur Verwendung eines Azure Log Analytics-Arbeitsbereichs zum Überwachen von Aufträgen, Debugprotokollen usw.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Cluster aus.  Anweisungen dazu finden Sie unter [Auflisten und Anzeigen von Clustern](./hdinsight-administer-use-portal-linux.md#showClusters). Der Cluster wird auf einer neuen Portalseite geöffnet.

1. Wählen Sie links unter **Überwachung** die Option **Operations Management Suite** aus.

1. Wählen Sie in der Hauptansicht unter **OMS-Überwachung** die Option **Aktivieren** aus.

1. Wählen Sie in der Dropdownliste **Arbeitsbereich auswählen** einen vorhandenen Log Analytics-Arbeitsbereich aus.

1. Wählen Sie **Speichern** aus.  Das Speichern der Einstellung dauert einige Zeit.

    ![Aktivieren der Überwachung für HDInsight-Cluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Aktivieren der Überwachung für HDInsight-Cluster")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Aktivieren von Azure Monitor-Protokollen mit der Azure PowerShell

Sie können die Azure Monitor-Protokolle im Azure PowerShell Az-Modul mit dem Cmdlet [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) aktivieren.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Zum Deaktivieren verwenden Sie das Cmdlet [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite):

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installieren von HDInsight-Clusterverwaltungslösungen

HDInsight bietet clusterspezifische Verwaltungslösungen, die Sie zu Azure Monitor-Protokollen hinzufügen können. [Verwaltungslösungen](../log-analytics/log-analytics-add-solutions.md) erweitern den Funktionsumfang von Azure Monitor-Protokollen und stellen zusätzliche Daten und Analysetools bereit. Diese Lösungen sammeln wichtige Leistungsmetriken aus Ihrem HDInsight-Cluster und stellen die Tools bereit, um die Metriken zu durchsuchen. Außerdem bieten diese Lösungen Visualisierungen und Dashboards für die meisten in HDInsight unterstützten Clustertypen. Anhand der mit der Lösung erfassten Kennzahlen können Sie benutzerdefinierte Überwachungsregeln und -warnungen erstellen.

Dies sind die verfügbaren HDInsight-Lösungen:

* HDInsight Hadoop-Überwachung
* HDInsight HBase-Überwachung
* HDInsight Interactive Query-Überwachung
* HDInsight Kafka-Überwachung
* HDInsight Spark-Überwachung
* HDInsight Storm-Überwachung

Die Anleitung zum Installieren einer Verwaltungslösung finden Sie unter [Verwaltungslösungen in Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Installieren Sie zum Experimentieren eine HDInsight Hadoop-Überwachungslösung. Wenn der Vorgang abgeschlossen ist, wird unter **Zusammenfassung** die Kachel **HDInsightHadoop** angezeigt. Wählen Sie die Kachel **HDInsightHadoop** aus. Die HDInsightHadoop-Lösung sieht wie folgt aus:

![HDInsight-Überwachungslösung – Ansicht](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Im Bericht werden keine Aktivitäten angezeigt, da es sich um einen brandneuen Cluster handelt.

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern](hdinsight-hadoop-oms-log-analytics-use-queries.md)
