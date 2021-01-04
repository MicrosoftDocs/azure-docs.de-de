---
title: Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen
description: Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen (Azure Role-Based Access Control, auf Azure-Rollen basierte Zugriffssteuerung) an Azure-Ressourcen für die letzten 90 Tage.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 3e4d75c59d0e3c97b0900dd72a27d4afbf4015c9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492317"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Anzeigen von Aktivitätsprotokollen für Azure RBAC-Änderungen

In einigen Fällen benötigen Sie Informationen zu Änderungen bei der auf Azure-Rollen basierten Zugriffssteuerung (Role-Based Access Control, Azure RBAC), beispielsweise für die Überwachung oder Problembehandlung. Jedes Mal, wenn ein Benutzer Änderungen an den Rollenzuweisungen oder Rollendefinitionen innerhalb Ihrer Abonnements vornimmt, werden die Änderungen im [Azure-Aktivitätsprotokoll](../azure-monitor/platform/platform-logs-overview.md) aufgezeichnet. Sie können die Aktivitätsprotokolle öffnen, um alle Azure RBAC-Änderungen für die letzten 90 Tage anzuzeigen.

## <a name="operations-that-are-logged"></a>Protokollierte Vorgänge

Folgende Vorgänge im Bezug auf Azure RBAC werden im Aktivitätsprotokoll erfasst:

- Erstellen von Rollenzuweisungen
- Löschen von Rollenzuweisungen
- Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition
- Löschen von benutzerdefinierten Rollendefinition

## <a name="azure-portal"></a>Azure-Portal

Der einfachste Einstieg besteht im Anzeigen der Aktivitätsprotokolle im Azure-Portal. Der folgende Screenshot zeigt ein Beispiel für Rollenzuordnungsvorgänge im Aktivitätsprotokoll. Es enthält ebenfalls eine Option, um die Protokolle als CSV-Datei herunterzuladen.

![Screenshot: Aktivitätsprotokolle im Portal](./media/change-history-report/activity-log-portal.png)

Das Aktivitätsprotokoll im Portal verfügt über mehrere Filter. Im Folgenden die Filter für die auf Azure-Rollen basierte Zugriffssteuerung:

| Filtern | value |
| --------- | --------- |
| Ereigniskategorie | <ul><li>Administrative</li></ul> |
| Vorgang | <ul><li>Erstellen von Rollenzuweisungen</li><li>Löschen von Rollenzuweisungen</li><li>Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition</li><li>Löschen von benutzerdefinierten Rollendefinition</li></ul> |

Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Anzeigen von Aktivitätsprotokollen, um Aktionen an Ressourcen zu überwachen](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

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
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Wenn Sie einen Dienstprinzipal zum Erstellen von Rollenzuweisungen verwenden, ist der Wert der Caller-Eigenschaft eine Objekt-ID. Sie können mithilfe von [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) Informationen zum Dienstprinzipal abrufen.

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Verwenden Sie den Befehl [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list), um Aktivitätsprotokolle mit der Azure CLI anzuzeigen.

Dieser Befehl listet die Aktivitätsprotokolle in einer Ressourcengruppe vom 27. Februar auf und blickt sieben Tage voraus:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Dieser Befehl listet die Aktivitätsprotokolle für den Autorisierungsressourcenanbieter vom 27. Februar auf und blickt sieben Tage voraus:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md) ist ein weiteres Tool, das Sie verwenden können, um Azure RBAC-Änderungen für alle Azure-Ressourcen zu erfassen und zu analysieren. Azure Monitor-Protokolle bietet die folgenden Vorteile:

- Schreiben komplexer Abfragen und Logiken
- Integration in Warnungen, Power BI und andere Tools
- Aufbewahren von Daten für längere Zeiträume
- Querverweise mit anderen Protokollen wie Sicherheitsprotokollen, Protokollen virtueller Computer und benutzerdefinierten Protokollen

Folgende Schritte sind für den Einstieg grundlegend:

1. [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md)

1. [Konfigurieren der Lösung für die Aktivitätsprotokollanalyse](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution) für Ihren Arbeitsbereich.

1. [Anzeigen der Aktivitätsprotokolle](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution) Sie können schnell zur Übersichtsseite für die Lösung zur Aktivitätsprotokollanalyse navigieren, indem Sie auf die Option **Protokolle** klicken.

   ![Option „Azure Monitor-Protokolle“ im Portal](./media/change-history-report/azure-log-analytics-option.png)

1. Verwenden Sie optional [Azure Monitor Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md), um die Protokolle abzufragen und anzuzeigen. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Monitor-Protokollabfragen](../azure-monitor/log-query/get-started-queries.md).

Im Folgenden finden Sie eine Abfrage, die neue Rollenzuweisungen zurückgibt, die vom Anbieter der Zielressource geordnet wurden:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Im Folgenden finden Sie eine Abfrage, die Änderungen an der Rollenzuweisung zurückgibt, die in einem Diagramm angezeigt werden:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Screenshot: Aktivitätsprotokolle im Advanced Analytics-Portal](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Nächste Schritte
* [Anzeigen von Ereignissen im Aktivitätsprotokoll](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitor Subscription Activity with the Azure Activity Log (Überwachen der Abonnementaktivität per Azure-Aktivitätsprotokoll)](../azure-monitor/platform/platform-logs-overview.md)