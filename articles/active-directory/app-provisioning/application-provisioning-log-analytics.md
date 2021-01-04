---
title: Grundlegendes zur Integration der Bereitstellung mit Azure Monitor-Protokollen in Azure Active Directory
description: Grundlegendes zur Integration der Bereitstellung mit Azure Monitor-Protokollen in Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 167ed7e5c00452db4ee77e10236fec3ff86f0439
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175099"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>Grundlegendes zur Integration der Bereitstellung in Azure Monitor-Protokolle

Die Bereitstellung ist mit Azure Monitor-Protokollen und Log Analytics integriert. Mit der Azure-Überwachung (Azure Monitor) können Sie beispielsweise Arbeitsmappen erstellen, die auch als Dashboards bezeichnet werden, Bereitstellungsprotokolle länger als 30 Tage speichern und benutzerdefinierte Abfragen und Warnungen erstellen. In diesem Artikel wird erläutert, wie Bereitstellungsprotokolle in Azure Monitor-Protokolle integriert werden. Weitere Informationen zur allgemeinen Funktionsweise von Bereitstellungsprotokollen finden Sie unter [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>Aktivieren von Bereitstellungsprotokollen

Sie sollten bereits mit Azure Monitor und Log Analytics vertraut sein. Wenn dies nicht der Fall ist, machen Sie sich zunächst mit diesen Features vertraut, bevor Sie sich über die Protokolle für die Anwendungsbereitstellung informieren. Weitere Informationen zur Azure-Überwachung finden Sie unter [Azure Monitor – Übersicht](../../azure-monitor/overview.md). Weitere Informationen zu Azure Monitor-Protokollen und Log Analytics finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

Nachdem Sie die Azure-Überwachung (Azure Monitor) konfiguriert haben, können Sie Protokolle für die Anwendungsbereitstellung aktivieren. Die Option befindet sich auf der Seite **Diagnoseeinstellungen**.

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Zugreifen auf Diagnoseeinstellungen" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Aktivieren von Protokollen für die Anwendungsbereitstellung" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Wenn Sie erst kürzlich einen Arbeitsbereich bereitgestellt haben, kann es einige Zeit dauern, bis Sie Protokolle an diesen senden können. Wenn eine Fehlermeldung angezeigt wird, dass das Abonnement nicht für die Verwendung von *microsoft.insights* registriert ist, versuchen Sie es nach einigen Minuten erneut.
 
## <a name="understanding-the-data"></a>Grundlegendes zu den Daten
Der zugrunde liegende Datenstrom, der über die Bereitstellung an Protokollanzeigen gesendet wird, ist nahezu identisch. Azure Monitor-Protokolle empfangen fast denselben Datenstrom wie die Benutzeroberfläche des Azure-Portals und die Azure API. Wie in der folgenden Tabelle aufgeführt, weisen die Protokollfelder nur einige wenige **Unterschiede** auf. Weitere Informationen zu diesen Feldern finden Sie unter [ProvisioningObjectSummary auflisten](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta).

|Azure Monitor-Protokolle   |Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor-Arbeitsmappen

Azure Monitor-Arbeitsmappen bieten eine flexible Canvas für die Datenanalyse. Sie ermöglichen außerdem die Erstellung umfassender visueller Berichte im Azure-Portal. Weitere Informationen finden Sie unter [Übersicht über Azure Monitor-Arbeitsmappen](../../azure-monitor/platform/workbooks-overview.md).

Die Anwendungsbereitstellung umfasst eine Reihe vorkonfigurierter Arbeitsmappen. Diese finden Sie auf der Seite „Arbeitsmappen“. Zum Anzeigen der Daten müssen Sie sicherstellen, dass alle Filter (timeRange, jobID, appName) ausgefüllt sind. Sie müssen auch sicherstellen, dass Sie eine Anwendung bereitgestellt haben. Andernfalls enthalten die Protokolle keine Daten.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Arbeitsmappen für die Anwendungsbereitstellung" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Dashboard für die Anwendungsbereitstellung" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Benutzerdefinierte Abfragen

Sie können benutzerdefinierte Abfragen erstellen und die Daten in Azure-Dashboards anzeigen. Informationen dazu finden Sie unter [Erstellen und Freigeben von Dashboards von Log Analytics-Daten](../../azure-monitor/log-query/get-started-queries.md). Sehen Sie sich auch die [Übersicht über Protokollabfragen in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) an.

Nachfolgend finden Sie einige Beispiele für den Einstieg in die Anwendungsbereitstellung.

Abfragen der Protokolle für einen Benutzer basierend auf der jeweiligen ID im Quellsystem:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Zusammenfassen der Anzahl pro Fehlercode:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Zusammenfassen der Anzahl der Ereignisse pro Tag nach Aktion:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Erfassen von 100 Ereignissen und Projektschlüsseleigenschaften:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Benutzerdefinierte Warnungen

Mit Azure Monitor können Sie benutzerdefinierte Warnungen konfigurieren, damit Sie über wichtige Ereignisse in Verbindung mit der Bereitstellung benachrichtigt werden. So können Sie beispielsweise Warnungen über Spitzen bei Ausfällen oder über Spitzen bei Deaktivierungen oder Löschvorgängen erhalten. Ein weiteres Beispiel ist eine Warnung bei einer fehlenden Bereitstellung, die auf einen möglichen Fehler hinweist.

Weitere Informationen zu Warnungen finden Sie unter [Reagieren auf Ereignisse mit Azure Monitor-Warnungen](../../azure-monitor/learn/tutorial-response.md).

Warnung über Spitze bei Ausfällen Ersetzen Sie die jobID durch die Auftrags-ID Ihrer Anwendung.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Warnung über Spitze bei Ausfällen" lightbox="media/application-provisioning-log-analytics/alert1.png":::

Möglicherweise ist ein Problem aufgetreten, das dazu führt, dass der Bereitstellungsdienst nicht mehr ausgeführt wird. Verwenden Sie die folgende Warnung, um zu ermitteln, wann während eines bestimmten Zeitintervalls keine Bereitstellungsereignisse auftreten.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Möglicherweise ist ein Problem aufgetreten, das dazu führt, dass der Bereitstellungsdienst nicht mehr ausgeführt wird." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Warnung über eine Spitze bei Deaktivierungen oder Löschvorgängen

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Warnung über eine Spitze bei Deaktivierungen oder Löschvorgängen" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Beiträge aus der Community

Wir verfolgen bei Abfragen und Dashboards für die Anwendungsbereitstellung einen Open-Source- und communitybasierten Ansatz. Wenn Sie eine Abfrage, Warnung oder Arbeitsmappe erstellt haben, die Ihnen nützlich für andere Benutzer zu sein scheint, veröffentlichen Sie sie unbedingt im [GitHub-Repository AzureMonitorCommunity](https://github.com/microsoft/AzureMonitorCommunity). Senden Sie uns dann eine E-Mail mit dem entsprechenden Link. Wir überprüfen und veröffentlichen sie für den Dienst, damit auch andere Benutzer davon profitieren können. Sie können sich unter provisioningfeedback@microsoft.com an uns wenden.

## <a name="next-steps"></a>Nächste Schritte

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Erste Schritte mit Abfragen in Azure Monitor-Protokollen](../../azure-monitor/log-query/get-started-queries.md)
- [Erstellen und Verwalten von Benachrichtigungsgruppen im Azure-Portal](../../azure-monitor/platform/action-groups.md)
- [Installieren und Verwenden der Log Analytics-Ansichten für Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [API für Bereitstellungsprotokolle](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta.md)