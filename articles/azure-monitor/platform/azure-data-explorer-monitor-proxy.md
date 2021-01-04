---
title: Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)
description: Verwenden Sie Azure Data Explorer, um produktübergreifende Abfragen zwischen Azure Data Explorer, Log Analytics-Arbeitsbereichen und klassischen Application Insights-Anwendungen in Azure Monitor auszuführen.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: dd29b18dda46a00966a0e5adea7e06be8f43ad35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444965"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)

Azure Data Explorer unterstützt dienstübergreifende Abfragen zwischen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) und [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs). Anschließend können Sie den Log Analytics/Application Insights-Arbeitsbereich mithilfe von Azure Data Explorer-Tools abfragen und in einer dienstübergreifenden Abfrage darauf verweisen. In diesem Artikel wird gezeigt, wie Sie eine dienstübergreifende Abfrage erstellen und den Log Analytics/Application Insights-Arbeitsbereich zur Azure Data Explorer-Webbenutzeroberfläche hinzufügen.

Ablauf dienstübergreifender Abfragen mit Azure Data Explorer: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure Data Explorer-Proxyflow":::

> [!NOTE]
> * Die Funktion zum Abfragen von Azure Monitor-Daten in Azure Data Explorer, entweder direkt über Azure Data Explorer-Clienttools oder indirekt durch Ausführen einer Abfrage in einem Azure Data Explorer-Cluster, befindet sich im Vorschaumodus.
>* Wenden Sie sich bei Fragen an das [Team für dienstübergreifende Abfragen](mailto:adxproxy@microsoft.com).

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Hinzufügen eines Log Analytics/Application Insights-Arbeitsbereichs zu Azure Data Explorer-Clienttools

1. Überprüfen Sie vor dem Herstellen der Verbindung mit dem Log Analytics- oder Application Insights-Cluster, ob im linken Menü der native Azure Data Explorer-Cluster (z. B. der *Hilfecluster*) angezeigt wird.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Nativer Azure Data Explorer-Cluster":::

 Wählen Sie auf der Azure Data Explorer-Benutzeroberfläche (https://dataexplorer.azure.com/clusters)**Cluster hinzufügen** aus.

2. Fügen Sie im Fenster **Cluster hinzufügen** dem LA- oder AI-Cluster die URL hinzu.

    * Für LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Für AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Wählen Sie **Hinzufügen**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Schaltfläche „Add Cluster“ (Cluster hinzufügen)":::
 
>[!NOTE]
>Wenn Sie eine Verbindung zu mehreren Log Analytics/Application Insights-Arbeitsbereichen hinzufügen, geben Sie jedem einen anderen Namen. Andernfalls haben alle Cluster im linken Bereich den gleichen Namen.

 Nachdem die Verbindung hergestellt wurde, wird Ihr Log Analytics- oder Application Insights-Arbeitsbereich im linken Bereich mit Ihrem nativen Azure Data Explorer-Cluster angezeigt.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics- und Azure Data Explorer-Cluster":::
 
> [!NOTE]
> Die Anzahl von Azure Monitor-Arbeitsbereichen, die zugeordnet werden können, ist auf 100 beschränkt.

## <a name="create-queries-using-azure-monitor-data"></a>Erstellen von Abfragen mithilfe von Azure Monitor-Daten

Sie können die Abfragen mithilfe von Clienttools ausführen, die Kusto-Abfragen unterstützen, z. B.: Kusto Explorer, Azure Data Explorer-Webbenutzeroberfläche, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens und REST-API.

> [!NOTE]
> Die Funktion für dienstübergreifende Abfragen wird nur für den Datenabruf verwendet. Weitere Informationen finden Sie unter [Funktionsunterstützbarkeit](#function-supportability).

> [!TIP]
> * Datenbanken müssen den gleichen Namen wie die in der dienstübergreifenden Abfrage angegebene Ressource haben. Bei Namen wird die Groß-/Kleinschreibung beachtet.
> * Achten Sie bei clusterübergreifenden Abfragen auf eine korrekte Benennung der Application Insights-Apps und Log Analytics-Arbeitsbereiche.
> * Wenn Namen Sonderzeichen enthalten, werden diese in der dienstübergreifenden Abfrage durch URL-Codierung ersetzt.
> * Wenn Namen Zeichen enthalten, die nicht den [KQL-Regeln für Bezeichnernamen](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names) entsprechen, werden sie durch einen Bindestrich ( **-** ) ersetzt.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Direkte Abfrage der Log Analytics- oder Application Insights-Arbeitsbereiche über Azure Data Explorer-Clienttools

Führen Sie Abfragen für Ihre Log Analytics- oder Application Insights-Arbeitsbereiche aus. Vergewissern Sie sich, dass Ihr Arbeitsbereich im linken Bereich ausgewählt ist.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Abfragen eines Log Analytics-Arbeitsbereichs":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Übergreifende Abfrage von Log Analytics oder Application Insights und des nativen Azure Data Explorer-Clusters

Wenn Sie clusterübergreifende Dienstabfragen ausführen, überprüfen Sie, ob der native Azure Data Explorer-Cluster im linken Bereich ausgewählt ist. In den folgenden Beispielen wird veranschaulicht, wie Sie Azure Data Explorer-Clustertabellen mithilfe von [union](/azure/data-explorer/kusto/query/unionoperator) mit einem Log Analytics-Arbeitsbereich kombinieren.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Dienstübergreifende Abfrage über Azure Data Explorer":::

Wenn Sie anstelle von „union“ den [`join`-Operator](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator) verwenden, ist möglicherweise ein [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) erforderlich, um die Abfrage für einen nativen Azure Data Explorer-Cluster auszuführen.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Verknüpfen von Daten eines Azure Data Explorer-Clusters in einem Mandanten mit einer Azure Monitor-Ressource in einem anderen Mandanten

Mandantenübergreifende Abfragen zwischen den Diensten werden nicht unterstützt. Sie sind nur bei einem Mandanten angemeldet, um die Abfrage für beide Ressourcen auszuführen.

Wenn sich die Azure Data Explorer-Ressource im Mandanten „A“ und der Log Analytics-Arbeitsbereich im Mandanten „B“ befindet, sollten Sie eine der beiden folgenden Methoden verwenden:

1. Mit Azure Data Explorer können Sie Rollen für Prinzipale auf unterschiedlichen Mandanten hinzufügen. Fügen Sie Ihre Benutzer-ID auf Mandant „B“ als autorisierten Benutzer im Azure Data Explorer-Cluster hinzu. Vergewissern Sie sich, dass die Eigenschaft *[ExternalTrustedTenant](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* im Azure Data Explorer-Cluster den Mandanten „B“ enthält. Führen Sie die übergreifende Abfrage vollständig auf Mandant „B“ aus.

2. Verwenden Sie [Lighthouse](https://docs.microsoft.com/azure/lighthouse/), um die Azure Monitor-Ressource auf Mandant „A“ zu projizieren.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Herstellen einer Verbindung mit Azure Data Explorer-Clustern von unterschiedlichen Mandanten

Bei Kusto-Explorer werden Sie automatisch bei dem Mandanten angemeldet, zu dem das Benutzerkonto ursprünglich gehört. Für den Zugriff auf Ressourcen in anderen Mandanten mit demselben Benutzerkonto muss `tenantId` explizit in der Verbindungszeichenfolge angegeben werden: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Funktionsunterstützbarkeit

Dienstübergreifende Abfragen mit Azure Data Explorer unterstützen Funktionen sowohl für Application Insights als auch für Log Analytics.
Dadurch können clusterübergreifende Abfragen direkt auf eine tabellarische Azure Monitor-Funktion verweisen.
Die folgenden Befehle werden bei dienstübergreifenden Abfragen unterstützt:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Die folgende Abbildung enthält ein Beispiel für das Abfragen einer tabellarischen Funktion über die Webbenutzeroberfläche von Azure Data Explorer.
Führen Sie zur Verwendung der Funktion den Namen im Abfragefenster aus.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Abfragen einer tabellarischen Funktion über die Webbenutzeroberfläche von Azure Data Explorer":::

## <a name="additional-syntax-examples"></a>Zusätzliche Syntaxbeispiele

Beim Aufrufen der Log Analytics- oder Application Insights-Cluster sind die folgenden Syntaxoptionen verfügbar:

|Syntaxbeschreibung  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Datenbank in einem Cluster, der nur die definierte Ressource in diesem Abonnement enthält (**empfohlen für clusterübergreifende Abfragen**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, der alle Apps/Arbeitsbereiche in diesem Abonnement enthält    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, der alle Apps/Arbeitsbereiche im Abonnement enthält, und diese sind Mitglieder dieser Ressourcengruppe    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, der nur die definierte Ressource in diesem Abonnement enthält      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den Artikel zur [Datenstruktur von Log Analytics-Arbeitsbereichen und Application Insights](data-platform-logs.md).
- Informieren Sie sich über das [Schreiben von Abfragen für Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/write-queries).