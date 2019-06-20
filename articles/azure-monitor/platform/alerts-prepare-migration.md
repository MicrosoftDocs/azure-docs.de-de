---
title: Vorbereiten der Migration von klassischen Azure Monitor-Warnungen durch Aktualisieren Ihrer Logik-Apps und Runbooks
description: Erfahren Sie, wie Sie Ihre Webhooks, Logik-Apps und Runbooks zur Vorbereitung der freiwilligen Migration ändern.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: bdbd45c2b10dec8f1c0a85110747a470e818dbf9
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015608"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Vorbereiten Ihrer Logik-Apps und Runbooks für die Migration von klassischen Warnungsregeln

Wie [bereits angekündigt](monitoring-classic-retirement.md) werden klassische Warnungen in Azure Monitor im September 2019 (ursprünglich Juli 2019) eingestellt. Im Azure-Portal steht ein Migrationstool für Kunden bereit, die klassische Warnungsregeln verwenden und die Migration selbst auslösen möchten.

> [!NOTE]
> Aufgrund einer Verzögerung beim Rollout des Migrationstools wurde der Deaktivierungstermin vom ursprünglich angekündigten Datum, dem 30. Juni 2019, auf den 31. August 2019 verschoben.

Wenn Sie sich entscheiden, Ihre klassischen Warnungsregeln freiwillig zu neuen Warnungsregeln zu migrieren, beachten Sie, dass einige Unterschiede zwischen den beiden Systemen bestehen. In diesem Artikel werden die Unterschiede erläutert, und Sie erfahren, wie Sie sich auf die Änderung vorbereiten können.

## <a name="api-changes"></a>API-Änderungen

Die APIs zum Erstellen und Verwalten klassischer Warnungsregeln (`microsoft.insights/alertrules`) unterscheiden sich von den APIs zum Erstellen und Verwalten der neuen Metrikwarnungen (`microsoft.insights/metricalerts`). Wenn Sie klassische Warnungsregeln derzeit programmgesteuert erstellen und verwalten, aktualisieren Sie Ihre Bereitstellungsskripts, damit sie mit den neuen APIs funktionieren.

In der folgenden Tabelle sind befehlsorientierte Benutzerschnittstellen für klassische und neue Warnungen aufgeführt:

|         |Klassische Warnungen  |Neue Metrikwarnungen |
|---------|---------|---------|
|REST-API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure-Befehlszeilenschnittstelle     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referenz](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referenz](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-Vorlage | [Für klassische Warnungen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Für neue Metrikwarnungen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Änderungen der Benachrichtigungsnutzlast

Das Format der Benachrichtigungsnutzlast für [klassische Warnungsregeln](alerts-webhooks.md) und [neue Metrikwarnungen](alerts-metric-near-real-time.md#payload-schema) unterscheidet sich geringfügig. Wenn Sie Webhook-, Logik-App- oder Runbookaktionen haben, die durch klassische Warnungsregeln ausgelöst werden, müssen Sie diese Benachrichtigungsendpunkte zum Akzeptieren des Nutzlastformats der neuen Metrikwarnungen aktualisieren.

Mithilfe der folgenden Tabelle können Sie die Felder für die Webhooknutzlast im klassischen Format dem neuen Format zuordnen:

|  |Klassische Warnungen  |Neue Metrikwarnungen |
|---------|---------|---------|
|Wurde die Warnung aktiviert oder behoben?    | **status**       | **data.status** |
|Kontextinformationen zur Warnung     | **Kontextvariable**        | **data.context**        |
|Zeitstempel der Aktivierung oder Auflösung der Warnung     | **context.timestamp**       | **data.context.timestamp**        |
| Warnungsregel-ID | **context.id** | **data.context.id** |
| Name der Warnungsregel | **context.name** | **data.context.name** |
| Beschreibung der Warnungsregel | **context.description** | **data.context.description** |
| Warnungsregelbedingung | **context.condition** | **data.context.condition** |
| Metrikname | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Zeitaggregation (wie die Metrik im Auswertungsfenster aggregiert wird)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Auswertungszeitraum | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operator (Vergleich des aggregierten Metrikwerts mit dem Schwellenwert) | **context.condition.operator** | **data.context.condition.operator** |
| Schwellenwert | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Metrikwert | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Abonnement-ID | **context.subscriptionId** | **data.context.subscriptionId** |
| Ressourcengruppe der betroffenen Ressource | **context.resourceGroup** | **data.context.resourceGroup** |
| Name der betroffenen Ressource | **context.resourceName** | **data.context.resourceName** |
| Typ der betroffenen Ressource | **context.resourceType** | **data.context.resourceType** |
| Ressourcen-ID der betroffenen Ressource | **context.resourceId** | **data.context.resourceId** |
| Direkter Link zur Ressourcenzusammenfassungsseite des Portals | **context.portalLink** | **data.context.portalLink** |
| Benutzerdefinierte Nutzlastfelder, die an den Webhook oder die Logik-App übergeben werden sollen | **properties** | **data.properties** |

Wie Sie sehen, sind die Nutzlasten ähnlich. Der nächste Abschnitt enthält Folgendes:

- Ausführliche Informationen zum Ändern von Logik-Apps zur Verwendung mit dem neuen Format
- Ein Runbook-Beispiel, in dem die Benachrichtigungsnutzlast für neue Warnungen analysiert wird

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Ändern einer Logik-App zum Empfangen einer Benachrichtigung über eine Metrikwarnung

Wenn Sie Logik-Apps mit klassischen Warnungen verwenden, müssen Sie den Logik-App-Code ändern, damit die neue Metrikwarnungsnutzlast analysiert wird. Folgen Sie diesen Schritten:

1. Erstellen Sie eine neue Logik-App.

1. Verwenden Sie die Vorlage „Azure Monitor – Metrics Alert Handler“ (Azure Monitor – Metrikwarnungshandler). Diese Vorlage enthält einen Auslöser **HTTP-Anforderung**, für den das entsprechende Schema definiert ist.

    ![Logik-App-Vorlage](media/alerts-migration/logic-app-template.png "Metrikwarnungsvorlage")

1. Fügen Sie eine Aktion zum Hosten Ihrer Verarbeitungslogik hinzu.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Verwenden eines Automation-Runbooks, das eine Benachrichtigung über eine Metrikwarnung empfängt

Das folgende Beispiel enthält PowerShell-Code zur Verwendung in Ihrem Runbook. Mit diesem Code können die Nutzlasten sowohl für klassische Metrikwarnungsregeln als auch neue Metrikwarnungsregeln analysiert werden.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Ein vollständiges Beispiel für ein Runbook, das bei Auslösung einer Warnung einen virtuellen Computer beendet, finden Sie in der [Dokumentation zu Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Partnerintegration über Webhooks

Die meisten [unserer Partner, die integrierte Lösungen für klassische Warnungen anbieten](https://docs.microsoft.com/azure/azure-monitor/platform/partners), unterstützen neuere Metrikwarnungen bereits über ihre Integrationen. Bekannte Integrationen, die bereits mit neuen Metrikwarnungen funktionieren:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Wenn Sie eine Partnerintegration verwenden, die hier nicht aufgeführt ist, erkundigen Sie sich beim Integrationsanbieter, ob die Integration mit neuen Metrikwarnungen funktioniert.

## <a name="next-steps"></a>Nächste Schritte

- [How to use the migration tool](alerts-using-migration-tool.md) (Verwenden des Migrationstools)
- [Funktionsweise des Migrationstools](alerts-understand-migration.md)
