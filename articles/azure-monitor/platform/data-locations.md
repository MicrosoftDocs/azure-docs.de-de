---
title: Speicherorte für Überwachungsdaten in Azure Monitor | Microsoft-Dokumentation
description: Beschreibt die verschiedenen Speicherorte, an denen Überwachungsdaten in Azure gespeichert werden, einschließlich der Azure Monitor-Datenplattform.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416912"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Speicherorte für Überwachungsdaten in Azure Monitor

Azure Monitor basiert auf einer [Datenplattform](data-platform.md) von [Protokollen](data-platform-logs.md) und [Metriken](data-platform-metrics.md), wie es unter [Azure Monitor-Datenplattform](data-platform.md) beschrieben ist. Überwachungsdaten von Azure-Ressourcen können jedoch vor dem Kopieren in Azure Monitor oder zur Unterstützung weiterer Szenarien an andere Speicherorte geschrieben werden. 

## <a name="monitoring-data-locations"></a>Speicherorte für Überwachungsdaten

In der folgenden Tabelle sind die verschiedenen Speicherorte angegeben, an die Überwachungsdaten in Azure gesendet werden. Außerdem sind die verschiedenen Methoden für den Zugriff auf diese Daten aufgeführt.

| Location | BESCHREIBUNG | Zugriffsmethoden |
|:---|:---|:---|:--|
| Azure Monitor-Metriken | Zeitreihendatenbank, die für das Analysieren von Daten mit Zeitstempel optimiert ist. | [Metrik-Explorer](metrics-getting-started.md)<br>[Azure Monitor-Metrik-API](/rest/api/monitor/metrics) |
| Azure Monitor-Protokolle    | Log Analytics-Arbeitsbereich basierend auf Azure Data Explorer, der eine leistungsstarke Analyse-Engine und eine umfangreiche Abfragesprache bereitstellt. | [Log Analytics](../log-query/portals.md)<br>[Log Analytics-API](https://dev.loganalytics.io/)<br>[Application Insights-API](https://dev.applicationinsights.io/reference/get-query) |
| Aktivitätsprotokoll | Daten aus dem Aktivitätsprotokoll sind besonders nützlich, wenn sie zur Analyse mit anderen Daten an Azure Monitor-Protokolle gesendet werden, doch werden die Daten auch eigenständig erfasst, damit sie direkt im Azure-Portal angezeigt werden können. | [Azure-Portal](activity-log-view.md#azure-portal)<br>[Azure Monitor-Ereignis-API](/rest/api/monitor/eventcategories) |
| Azure Storage | Einige Datenquellen schreiben direkt in Azure Storage und müssen konfiguriert werden, damit Daten in Protokolle verschoben werden. Sie können Daten auch zum Archivieren und zur Integration in externe Systeme an Azure Storage senden.  | [Speicheranalyse](/rest/api/storageservices/storage-analytics)<br>[Server-Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage-Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Senden von Daten an Azure Event Hubs, um sie an andere Speicherorte zu streamen. | [Erfassung in Storage](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor für VMs | Azure Monitor für VMs speichert Integritätsdaten der Workload an einem benutzerdefinierten Speicherort, der über seine Überwachungsoberfläche im Azure-Portal verwendet wird. | [Azure-Portal](../insights/vminsights-overview.md)<br>[Workloadüberwachungs-REST-API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource Health-REST-API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alerts | Von Azure Monitor erstellte Warnungen. | [Azure-Portal](alerts-managing-alert-instances.md)<br>[Warnungsverwaltungs-REST-API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die verschiedenen Quellen für [von Azure Monitor gesammelte Überwachungsdaten](data-sources.md).
- Erfahren Sie mehr über die [Typen der von Azure Monitor gesammelten Überwachungsdaten](data-platform.md) und wie diese Daten angezeigt und analysiert werden.
