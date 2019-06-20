---
title: Migrieren von Azure-Warnungen für Verwaltungsereignisse zu Aktivitätsprotokollwarnungen
description: Warnungen für Verwaltungsereignisse werden am 1. Oktober entfernt. Bereiten Sie sich darauf vor, indem Sie vorhandene Warnungen migrieren.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 78519dad85739b6e4d760bc34719837956638f48
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388714"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrieren von Azure-Warnungen für Verwaltungsereignisse zu Aktivitätsprotokollwarnungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!WARNING]
> Warnungen für Verwaltungsereignisse werden am oder nach dem 01. Oktober 2017 deaktiviert. Befolgen Sie die nachstehenden Anweisungen, um herauszufinden, ob Sie über solche Warnungen verfügen, und diese gegebenenfalls zu migrieren.

## <a name="what-is-changing"></a>Was ändert sich?

Azure Monitor (ehemals „Azure Insights“) hat eine Funktion zum Erstellen einer Warnung bereitgestellt, die Verwaltungsereignisse ausgelöst und Benachrichtigungen für eine Webhook-URL oder für E-Mail-Adressen generiert hat. Möglicherweise haben Sie eine dieser Warnungen auf eine der folgenden Weisen erstellt:
* Durch Auswählen von „Überwachung“ -> „Warnungen“ -> „Warnung hinzufügen“ im Azure-Portal für bestimmte Ressourcentypen und Festlegen der Option „Warnung bei“ auf „Ereignisse“
* Durch Ausführen des PowerShell-Cmdlets „Add-AzLogAlertRule“
* Durch direktes Verwenden [der REST-API für Warnungen](https://docs.microsoft.com/rest/api/monitor/alertrules) mit den Werten „ManagementEventRuleCondition“ für „odata.type“ und „RuleManagementEventDataSource“ für „dataSource.odata.type“
 
Das folgende PowerShell-Skript gibt eine Liste aller Warnungen für Verwaltungsereignisse zurück, die in Ihrem Abonnement vorliegen, sowie die für die einzelnen Warnungen festgelegten Bedingungen.

```powershell
Connect-AzAccount
$alerts = $null
foreach ($rg in Get-AzResourceGroup ) {
  $alerts += Get-AzAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Wenn keine Warnungen für Verwaltungsereignisse vorliegen, gibt das oben aufgeführte PowerShell-Cmdlet eine Reihe von Warnmeldungen wie die Folgende aus:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Diese Warnmeldungen können ignoriert werden. Wenn Warnungen für Verwaltungsereignisse vorliegen, sieht die Ausgabe dieses PowerShell-Cmdlets wie folgt aus:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Jede Warnung wird durch eine gestrichelte Linie getrennt und enthält Details wie die Ressourcen-ID der Warnung und die jeweilige zu überwachende Regel.

Diese Funktionalität wurde auf [Azure Monitor-Aktivitätsprotokollwarnungen](../../azure-monitor/platform/activity-log-alerts.md) übertragen. Mithilfe dieser neuen Warnungen können Sie eine Bedingung für Aktivitätsprotokollereignisse festlegen und eine Benachrichtigung erhalten, wenn ein neues Ereignis der Bedingung entspricht. Zudem bringen sie auch verschiedene Verbesserungen für Warnungen zu Verwaltungsereignisse mit sich:
* Sie können Ihre Gruppe von Benachrichtigungsempfängern („Aktionen“) mittels [Aktionsgruppen](../../azure-monitor/platform/action-groups.md) für eine Vielzahl von Warnungen wiederverwenden, wodurch die Komplexität beim Ändern der Person, die eine Warnung erhalten soll, verringert wird.
* Sie können durch Aktionsgruppen direkt per SMS eine Benachrichtigung auf Ihrem Telefon empfangen.
* Sie können [Aktivitätsprotokollwarnungen mit Resource Manager-Vorlagen erstellen](../../azure-monitor/platform/alerts-activity-log.md).
* Sie können Bedingungen mit größerer Flexibilität und Komplexität im Hinblick auf Ihre speziellen Anforderungen erstellen.
* Benachrichtigungen werden schneller übermittelt.
 
## <a name="how-to-migrate"></a>Vorgehensweise zum Migrieren
 
Für die Erstellung einer neuen Aktivitätsprotokollwarnung stehen Ihnen folgende Informationsquellen zur Verfügung:
* Unser Leitfaden zum [Erstellen einer Warnung im Azure-Portal](../../azure-monitor/platform/activity-log-alerts.md)
* Der Artikel [Erstellen einer Warnung mithilfe einer Resource Manager-Vorlage](../../azure-monitor/platform/alerts-activity-log.md)
 
Zuvor erstellte Warnungen für Verwaltungsereignisse werden nicht automatisch zu Aktivitätsprotokollwarnungen migriert. Sie müssen das vorangehende PowerShell-Skript verwenden, um die gegenwärtig konfigurierten Warnungen für Verwaltungsereignisse aufzulisten und diese manuell als Aktivitätsprotokollwarnungen neu zu erstellen. Dies muss vor dem 1. Oktober geschehen, da Warnungen für Verwaltungsereignisse danach nicht mehr in Ihrem Azure-Abonnement angezeigt werden. Andere Arten von Azure-Warnungen, einschließlich Azure Monitor-Metrikwarnungen, Application Insights-Warnungen und Log Search-Warnungen, sind von dieser Änderung nicht betroffen. Bei Fragen fügen Sie in den unten stehenden Kommentaren Ihren Beitrag hinzu.


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Aktivitätsprotokollen](../../azure-monitor/platform/activity-logs-overview.md)
* Konfigurieren von [Aktivitätsprotokollwarnungen über das Azure-Portal](../../azure-monitor/platform/activity-log-alerts.md)
* Konfigurieren von [Aktivitätsprotokollwarnungen über den Resource Manager](../../azure-monitor/platform/alerts-activity-log.md)
* Weitere Informationen zum [Webhookschema für Aktivitätsprotokollwarnungen](../../azure-monitor/platform/activity-log-alerts-webhook.md)
* Weitere Informationen zu [Dienstbenachrichtigungen](../../azure-monitor/platform/service-notifications.md)
* Weitere Informationen zu [Aktionsgruppen](../../azure-monitor/platform/action-groups.md)

