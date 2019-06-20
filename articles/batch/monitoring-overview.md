---
title: Überwachen von Azure Batch | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu Azure-Überwachungsdiensten, Metriken Diagnoseprotokollen und weiteren Überwachungsfeatures für Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: b0243b37f725fc977337b72998d610e9bda71a86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128854"
---
# <a name="monitor-batch-solutions"></a>Überwachen von Batch-Lösungen

Azure und der Batch-Dienst bieten verschiedene Dienste, Tools und APIs zur Überwachung von Batch-Lösungen. Dieser Übersichtsartikel hilft Ihnen bei der Wahl eines geeigneten Überwachungskonzepts für Ihre Anforderungen.

Eine Übersicht über die verfügbaren Azure-Komponenten und -Dienste zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Anwendungen und -Ressourcen](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Überwachung auf Abonnementebene

Auf der Abonnementebene (die auch Batch-Konten einschließt) werden im [Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-logs-overview.md) Betriebsereignisdaten in [verschiedenen Kategorien](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log) gesammelt.

Speziell für Batch-Konten werden Ereignisse im Zusammenhang mit der Kontoerstellung/-löschung und der Schlüsselverwaltung erfasst.

Ereignisse können unter anderem über das Azure-Portal aus dem Aktivitätsprotokoll abgerufen werden. Klicken Sie auf **Alle Dienste** > **Aktivitätsprotokoll**. Alternativ können Ereignisse über die Azure-Befehlszeilenschnittstelle, mithilfe von PowerShell-Cmdlets oder unter Verwendung der Azure Monitor-REST-API abgefragt werden. Sie können das Aktivitätsprotokoll auch exportieren oder [Aktivitätsprotokollwarnungen](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md) konfigurieren.

## <a name="batch-account-level-monitoring"></a>Überwachung auf Batch-Kontoebene

Überwachen Sie die einzelnen Batch-Konten mithilfe der Features von [Azure Monitor](../azure-monitor/overview.md). Azure Monitor erfasst [Metriken](../azure-monitor/platform/data-platform-metrics.md) und optional [Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md) für Ressourcen auf der Ebene eines Batch-Kontos. Hierzu zählen beispielsweise Pools, Aufträge und Aufgaben. Erfassen und nutzen Sie diese Daten manuell oder programmgesteuert, um Aktivitäten in Ihrem Batch-Konto zu überwachen und Probleme zu diagnostizieren. Ausführliche Informationen finden Sie unter [Batch-Metriken, -Warnungen und -Protokolle für die Diagnoseauswertung und -überwachung](batch-diagnostics.md).
 
> [!NOTE]
> Metriken sind standardmäßig ohne zusätzliche Konfiguration in Ihrem Batch-Konto verfügbar und decken jeweils die letzten 30 Tage ab. Sie müssen die Diagnoseprotokollierung für ein Batch-Konto aktivieren, und durch die Speicherung oder Verarbeitung von Diagnoseprotokolldaten entstehen unter Umständen zusätzliche Kosten. 

## <a name="batch-resource-monitoring"></a>Überwachung von Batch-Ressourcen

Verwenden Sie in Ihren Batch-Anwendungen die Batch-APIs, um den Status von Ressourcen wie Aufträgen, Aufgaben, Knoten und Pools zu überwachen oder abzufragen. Beispiel:

* [Monitor Batch solutions by counting tasks and nodes by state (Überwachen von Batch-Lösungen durch das Zählen von Tasks und Knoten nach Bundesstaat)](batch-get-resource-counts.md)
* [Erstellen von Abfragen zum effizienten Auflisten von Batch-Ressourcen](batch-efficient-list-queries.md)
* [Erstellen von Auftragsabhängigkeiten](batch-task-dependencies.md)
* Verwenden einer [Auftrags-Manager-Aufgabe](/rest/api/batchservice/job/add#jobmanagertask)
* Überwachen des [Auftragsstatus](/rest/api/batchservice/task/list#taskstate)
* Überwachen des [Knotenstatus](/rest/api/batchservice/computenode/list#computenodestate)
* Überwachen des [Poolstatus](/rest/api/batchservice/pool/get#poolstate)
* Überwachen der [Poolverwendung im Konto](/rest/api/batchservice/pool/listusagemetrics)
* [Zählen der Poolknoten nach Status](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM-Leistungsindikatoren und Anwendungsüberwachung

* Mit dem Azure-Dienst [Application Insights](../azure-monitor/app/app-insights-overview.md) können Sie die Verfügbarkeit, Leistung und Verwendung Ihrer Batch-Aufträge und -Aufgaben programmgesteuert überwachen. So können Sie ganz einfach Leistungsindikatoren von Computeknoten (VMs) und benutzerdefinierte Informationen für Aufgaben von den virtuellen Computer erhalten. 

  Ein Beispiel finden Sie unter [Überwachen und Debuggen einer Azure Batch-.NET-Anwendung mit Application Insights](monitor-application-insights.md) und im dazugehörigen [Codebeispiel](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Durch die Verwendung von Application Insights können zusätzliche Kosten entstehen. Weitere Informationen finden Sie in den [Preisoptionen](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* Der [Batch Explorer](https://github.com/Azure/BatchExplorer) ist ein kostenloses eigenständiges Clienttool mit zahlreichen Features, das Sie beim Erstellen, Debuggen und Überwachen von Azure Batch-Anwendungen unterstützt. Ein Installationspaket für Mac, Linux oder Windows können Sie [hier](https://azure.github.io/BatchExplorer/) herunterladen. Konfigurieren Sie optional die Batch-Lösung zum [Anzeigen von Application Insights-Daten](https://github.com/Azure/batch-insights) (etwa VM-Leistungsindikatoren) in Batch Explorer.


## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
* Weitere Informationen zur Diagnoseprotokollierung mit Batch finden Sie [hier](batch-diagnostics.md).