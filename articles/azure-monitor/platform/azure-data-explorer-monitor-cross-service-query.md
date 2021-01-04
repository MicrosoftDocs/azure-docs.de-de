---
title: Dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer (Vorschau)
description: Fragen Sie Daten in Azure Data Explorer über Azure Log Analytics-Tools und umgekehrt ab, um alle Daten an einem Ort zusammenzuführen und zu analysieren.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 0af9111223bb16f1c5843223e7fe1661ce99c623
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749229"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>Dienstübergreifende Abfragen – Azure Monitor und Azure Data Explorer (Vorschau)
Erstellen Sie dienstübergreifende Abfragen zwischen [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/), [Application Insights](/azure/azure-monitor/app/app-insights-overview) und [Log Analytics](/azure/azure-monitor/platform/data-platform-logs).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer
Mit dieser Funktion können Sie [dienstübergreifende Abfragen zwischen Azure Data Explorer und Azure Monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) sowie [dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy) erstellen.

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure Data Explorer-Proxyflow":::

Beispiel (Abfrage von Azure Data Explorer aus Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Hierbei wird mit der äußeren Abfrage eine Tabelle im Arbeitsbereich abgefragt und anschließend mit einer anderen Tabelle in einem Azure Data Explorer-Cluster (in diesem Fall „clustername=help“, „databasename=samples“) mithilfe einer neuen „adx()“-Funktion verknüpft. Auf ähnliche Weise können Sie einen anderen Arbeitsbereich aus dem Abfragetext abfragen.

> [!NOTE]
> * Die Funktion zum Abfragen von Azure Monitor-Daten in Azure Data Explorer, entweder direkt über Azure Data Explorer-Clienttools oder indirekt durch Ausführen einer Abfrage in einem Azure Data Explorer-Cluster, befindet sich im Vorschaumodus.
> * Wenden Sie sich bei Fragen an das [Team für dienstübergreifende Abfragen](mailto:adxproxy@microsoft.com).

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Abfragen exportierter Log Analytics-Daten aus dem Azure Blob Storage-Konto

Das Exportieren von Daten aus Azure Monitor in ein Azure Storage-Konto ermöglicht eine kostengünstige Aufbewahrung und bietet die Möglichkeit, Protokolle verschiedenen Regionen neu zuzuordnen.

Verwenden Sie Azure Data Explorer, um Daten abzufragen, die aus Ihrem Log Analytics-Arbeitsbereich exportiert wurden. Nach der Konfiguration stehen unterstützte Tabellen, die von Ihren Arbeitsbereichen an ein Azure Storage-Konto gesendet werden, als Datenquelle für Azure Data Explorer bereit. [Fragen Sie exportierte Daten aus Azure Monitor mit Azure Data Explorer (Vorschau) ab](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Ablauf einer Azure Data Explorer-Abfrage aus dem Speicher":::

>[!tip] 
> * Wenn Sie alle Daten aus dem Log Analytics-Arbeitsbereich in ein Azure Storage-Konto oder einen Event Hub exportieren möchten, verwenden Sie das Datenexportfeature von Azure Monitor-Protokollen im Log Analytics-Arbeitsbereich. Informationen hierzu finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen:
* [Erstellen Sie dienstübergreifende Abfragen zwischen Azure Data Explorer und Azure Monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Abfragen von Azure Monitor-Daten aus Azure Data Explorer
* [Erstellen Sie dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Abfragen von Azure Data Explorer-Daten aus Azure Monitor
* [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor (Vorschau)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Verknüpfen Sie Azure Blob Storage-Konten mit exportierten Log Analytics-Daten, und fragen Sie Daten ab.