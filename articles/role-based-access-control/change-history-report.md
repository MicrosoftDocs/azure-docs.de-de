---
title: Anzeigen von Aktivitätsprotokollen für RBAC-Änderungen an Azure-Ressourcen | Microsoft-Dokumentation
description: Anzeigen von Aktivitätsprotokollen für RBAC-Änderungen (Role-Based Access Control, rollenbasierte Zugriffssteuerung) an Azure-Ressourcen für die letzten 90 Tage
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842d3146bf1927871e29eb750cde09e9029b7c12
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242100"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Anzeigen von Aktivitätsprotokollen für RBAC-Änderungen an Azure-Ressourcen

In einigen Fällen benötigen Sie Informationen zu RBAC-Änderungen an Azure-Ressourcen, beispielsweise für die Überwachung oder Problembehandlung. Jedes Mal, wenn ein Benutzer Änderungen an den Rollenzuweisungen oder Rollendefinitionen innerhalb Ihrer Abonnements vornimmt, werden die Änderungen im [Azure-Aktivitätsprotokoll](../azure-monitor/platform/activity-logs-overview.md) aufgezeichnet. Sie können die Aktivitätsprotokolle öffnen, um alle RBAC-Änderungen für die letzten 90 Tage anzuzeigen.

## <a name="operations-that-are-logged"></a>Protokollierte Vorgänge

Folgende Vorgänge im Bezug auf die rollenbasierte Zugriffssteuerung werden im Aktivitätsprotokoll erfasst:

- Erstellen von Rollenzuweisungen
- Löschen von Rollenzuweisungen
- Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition
- Löschen von benutzerdefinierten Rollendefinition

## <a name="azure-portal"></a>Azure-Portal

Der einfachste Einstieg besteht im Anzeigen der Aktivitätsprotokolle im Azure-Portal. Im folgendem Screenshot sehen Sie ein Beispiel für ein Aktivitätsprotokoll, das gefiltert wurde, um Vorgänge rund um Rollenzuweisungen und -definitionen anzuzeigen. Es enthält ebenfalls einen Link, um die Protokolle als CSV-Datei herunterzuladen.

![Screenshot: Aktivitätsprotokolle im Portal](./media/change-history-report/activity-log-portal.png)

Das Aktivitätsprotokoll im Portal verfügt über mehrere Filter. Im Folgenden die Filter für die rollenbasierte Zugriffssteuerung:

|Filter  |Wert  |
|---------|---------|
|Ereigniskategorie     | <ul><li>Administrative</li></ul>         |
|Vorgang     | <ul><li>Erstellen von Rollenzuweisungen</li> <li>Löschen von Rollenzuweisungen</li> <li>Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition</li> <li>Löschen von benutzerdefinierten Rollendefinition</li></ul>      |


Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Anzeigen von Ereignissen im Aktivitätsprotokoll](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Verwenden Sie den Befehl [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog), um Aktivitätsprotokolle mit Azure PowerShell anzuzeigen.

Dieser Befehl führt alle Änderungen an der Rollenzuweisung in einem Abonnement für die letzten sieben Tage auf:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Dieser Befehl führt alle Änderungen an der Rollendefinition in einer Ressourcengruppe für die letzten sieben Tage auf:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Dieser Befehl führt alle Änderungen an der Rollenzuweisung und der Rollendefinition in einem Abonnement für die letzten sieben Tage auf und stellt die Ergebnisse in einer Liste dar:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie den Befehl [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list), um Aktivitätsprotokolle mit der Azure CLI anzuzeigen.

Dieser Befehl führt die Aktivitätsprotokolle in einer Ressourcengruppe seit dem Start auf:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Dieser Befehl führt die Aktivitätsprotokolle für den Ressourcenanbieter der Autorisierung seit dem Start auf:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md) ist ein weiteres Tool, das Sie verwenden können, um RBAC-Änderungen für alle Azure-Ressourcen zu erfassen und zu analysieren. Azure Monitor-Protokolle bietet die folgenden Vorteile:

- Schreiben komplexer Abfragen und Logiken
- Integration in Warnungen, Power BI und andere Tools
- Aufbewahren von Daten für längere Zeiträume
- Querverweise mit anderen Protokollen wie Sicherheitsprotokollen, Protokollen virtueller Computer und benutzerdefinierten Protokollen

Folgende Schritte sind für den Einstieg grundlegend:

1. [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md)

1. [Konfigurieren der Lösung für die Aktivitätsprotokollanalyse](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) für Ihren Arbeitsbereich.

1. [Anzeigen der Aktivitätsprotokolle](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) Sie können schnell zur Übersichtsseite für die Lösung zur Aktivitätsprotokollanalyse navigieren, indem Sie auf die Option **Log Analytics** klicken.

   ![Option „Azure Monitor-Protokolle“ im Portal](./media/change-history-report/azure-log-analytics-option.png)

1. Verwenden Sie optional die Seite [Protokollsuche](../log-analytics/log-analytics-log-search.md) oder das [Advanced Analytics-Portal](../azure-monitor/log-query/get-started-portal.md), um die Protokolle abzufragen und anzuzeigen. Weitere Informationen zu diesen beiden Optionen finden Sie unter [Log Search page or the Advanced Analytics portal (Seite „Protokollsuche“ bzw. das Advanced Analytics-Portal)](../azure-monitor/log-query/portals.md).

Im Folgenden finden Sie eine Abfrage, die neue Rollenzuweisungen zurückgibt, die vom Anbieter der Zielressource geordnet wurden:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Im Folgenden finden Sie eine Abfrage, die Änderungen an der Rollenzuweisung zurückgibt, die in einem Diagramm angezeigt werden:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Screenshot: Aktivitätsprotokolle im Advanced Analytics-Portal](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nächste Schritte
* [Anzeigen von Ereignissen im Aktivitätsprotokoll](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitor Subscription Activity with the Azure Activity Log (Überwachen der Abonnementaktivität per Azure-Aktivitätsprotokoll)](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
