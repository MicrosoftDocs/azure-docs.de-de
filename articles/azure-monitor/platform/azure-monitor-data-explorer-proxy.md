---
title: 'Ressourcenübergreifende Abfrage: Azure Data Explorer mithilfe von Azure Monitor'
description: Verwenden Sie Azure Monitor, um produktübergreifende Abfragen zwischen Azure Data Explorer, Log Analytics-Arbeitsbereichen und klassischen Application Insights-Anwendungen in Azure Monitor auszuführen.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572149"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Ressourcenübergreifende Abfrage: Azure Data Explorer mithilfe von Azure Monitor
Azure Monitor unterstützt dienstübergreifende Abfragen zwischen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) und [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs). Anschließend können Sie den Azure Data Explorer-Cluster mit Log Analytics/Application Insights-Tools abfragen und in einer dienstübergreifenden Abfrage darauf verweisen. Der Artikel zeigt, wie eine dienstübergreifende Abfrage durchgeführt wird.

Der dienstübergreifende Azure Monitor-Flow: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Dienstübergreifender Flow zwischen Azure Monitor und Azure Data Explorer.":::

>[!NOTE]
>* Die dienstübergreifende Azure Monitor-Abfrage befindet sich in der privaten Vorschau. AllowListing ist erforderlich.
>* Wenden Sie sich mit Fragen an das [Service Team](mailto:ADXProxy@microsoft.com).
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Übergreifende Abfrage von Log Analytics- oder Application Insights-Ressourcen und Azure Data Explorer

Sie können die ressourcenübergreifenden Abfragen mithilfe von Clienttools ausführen, die Kusto-Abfragen unterstützen, z. B.: Log Analytics-Webbenutzeroberfläche, Workbooks, PowerShell, REST-API und mehr.

* Geben Sie den Bezeichner für einen Azure Data Explorer-Cluster in einer Abfrage innerhalb des ADX-Musters gefolgt vom Datenbanknamen und der Tabelle ein.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Beispiel für eine dienstübergreifende Abfrage.":::

> [!NOTE]
>* Bei Datenbanknamen wird die Groß-/Kleinschreibung berücksichtigt.
>* Eine ressourcenübergreifende Abfrage als Warnung wird nicht unterstützt.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Kombinieren von Azure Data Explorer-Clustertabellen (mithilfe von „Union“ und „Join“) mit dem LA-Arbeitsbereich.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Beispiel für eine dienstübergreifende Abfrage mit „Union“.":::

>[!Tip]
>* Kurzform ist zulässig – ClusterName/InitialCatalog. Beispielsweise wird adx('help/Samples') in adx('help.kusto.windows.net/Samples') übersetzt.
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Verknüpfen von Daten eines Azure Data Explorer-Clusters in einem Mandanten mit einer Azure Monitor-Ressource in einem anderen Mandanten

Mandantenübergreifende Abfragen zwischen den Diensten werden nicht unterstützt. Sie sind nur bei einem Mandanten angemeldet, um die Abfrage für beide Ressourcen auszuführen.

Wenn sich die Azure Data Explorer-Ressource im Mandanten „A“ und der Log Analytics-Arbeitsbereich im Mandanten „B“ befindet, sollten Sie eine der beiden folgenden Methoden verwenden:

*  Mit Azure Data Explorer können Sie Rollen für Prinzipale auf unterschiedlichen Mandanten hinzufügen. Fügen Sie Ihre Benutzer-ID auf Mandant „B“ als autorisierten Benutzer im Azure Data Explorer-Cluster hinzu. Vergewissern Sie sich, dass die Eigenschaft *[ExternalTrustedTenant](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* im Azure Data Explorer-Cluster den Mandanten „B“ enthält. Führen Sie die übergreifende Abfrage vollständig auf Mandant „B“ aus.
*  Verwenden Sie [Lighthouse](https://docs.microsoft.com/azure/lighthouse/), um die Azure Monitor-Ressource auf Mandant „A“ zu projizieren.
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Herstellen einer Verbindung mit Azure Data Explorer-Clustern von unterschiedlichen Mandanten

Bei Kusto-Explorer werden Sie automatisch bei dem Mandanten angemeldet, zu dem das Benutzerkonto ursprünglich gehört. Für den Zugriff auf Ressourcen in anderen Mandanten mit demselben Benutzerkonto muss `tenantId` explizit in der Verbindungszeichenfolge angegeben werden: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="next-steps"></a>Nächste Schritte
* [Schreiben von Abfragen](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Abfragen von Daten in Azure Monitor mit Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Ausführen ressourcenübergreifender Protokollabfragen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
