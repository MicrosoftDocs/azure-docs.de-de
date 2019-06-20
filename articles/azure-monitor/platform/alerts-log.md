---
title: Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor | Microsoft-Dokumentation
description: Verwenden Sie Azure Monitor, um Protokollwarnungsregeln in Azure zu erstellen, anzuzeigen und zu verwalten.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: f758007a0fa0d7fb619873d94d762e7019077e05
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427451"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor

## <a name="overview"></a>Übersicht
In diesem Artikel wird erläutert, wie Sie im Azure-Portal mithilfe der Oberfläche „Warnungen“ Protokollwarnungen einrichten. Die Definition einer Warnungsregel setzt sich aus drei Teilen zusammen:
- Ziel: Eine bestimmte zu überwachende Azure-Ressource
- Kriterien: Eine bestimmte Bedingung oder Logik, die beim Vorkommen in einem Signal eine Aktion auslösen soll
- Aktion: Eine bestimmte Form der Kontaktaufnahme, die an einen Empfänger einer Benachrichtigung (z. B. E-Mail, SMS oder Webhook usw.) gesendet wird.

Der Begriff **Protokollwarnungen** beschreibt Warnungen, die von Protokollabfragen in einem [Log Analytics-Arbeitsbereich](../learn/tutorial-viewdata.md) oder in [Application Insights](../app/analytics.md) ausgelöst werden. Erfahren Sie mehr über Funktionen, Terminologie und Typen von [Protokollwarnungen – Übersicht](alerts-unified-log.md).

> [!NOTE]
> In [Log Analytics-Arbeitsbereichen](../../azure-monitor/learn/tutorial-viewdata.md) beliebte Protokolldaten stehen nun ebenfalls auf der Metrikplattform in Azure Monitor zur Verfügung. Eine Detailansicht finden Sie unter [Metrikwarnung für Protokolle](alerts-metric-logs.md).

## <a name="managing-log-alerts-from-the-azure-portal"></a>Verwalten von Protokollwarnungen über das Azure-Portal

Als nächstes wird die schrittweise Anleitung zur Verwendung von Protokollwarnungen über die Benutzeroberfläche des Azure-Portals eingehend erläutert.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Erstellen einer Protokollwarnungsregel mit dem Azure-Portal
1. Wählen Sie im [Portal](https://portal.azure.com/) die Option **Monitor** und im Abschnitt „MONITOR“ dann **Warnungen** aus.

    ![Überwachung](media/alerts-log/AlertsPreviewMenu.png)

1. Wählen Sie die Schaltfläche **Neue Warnungsregel** aus, um eine neue Warnung in Azure zu erstellen.

    ![Warnung hinzufügen](media/alerts-log/AlertsPreviewOption.png)

1. Der Abschnitt „Warnung erstellen“ wird mit den drei Teilen angezeigt, die aus Folgendem bestehen: *Warnungsbedingung definieren*, *Warnungsdetails definieren* und *Aktionsgruppe definieren*.

    ![Erstellen einer Regel](media/alerts-log/AlertsPreviewAdd.png)

1. Definieren Sie die Warnungsbedingung, indem Sie auf den Link **Ressource auswählen** klicken und dann durch Auswahl einer Ressource das Ziel angeben. Filtern Sie, indem Sie die Optionen _Abonnement_, _Ressourcentyp_ und schließlich die erforderliche Option _Ressource_ auswählen.

   > [!NOTE]
   > 
   > Überprüfen Sie beim Erstellen einer Protokollwarnung das für die ausgewählte Ressource verfügbare **Protokoll**signal, bevor Sie den Vorgang fortsetzen.
   >  ![Auswählen einer Ressource](media/alerts-log/Alert-SelectResourceLog.png)

1. *Protokollwarnungen*: Stellen Sie sicher, dass als **Ressourcentyp** eine Analysequelle wie *Log Analytics* oder *Application Insights* und der Signaltyp **Protokoll** ausgewählt sind, und klicken Sie nach Auswahl der entsprechenden **Ressource** auf *Fertig*. Klicken Sie als Nächstes auf die Schaltfläche **Kriterien hinzufügen**, um die Liste mit den für die Ressource verfügbaren Signaloptionen und die Option **Benutzerdefinierte Protokollsuche** für den ausgewählten Protokollüberwachungsdienst (z.B. *Log Analytics* oder *Application Insights*) aus der Signalliste anzuzeigen.

   ![Auswählen einer Ressource – benutzerdefinierte Protokollsuche](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Über die Listen von „Warnungen“ kann eine Analyseabfrage als Signaltyp **Protokoll (gespeicherte Abfrage)** importiert werden. Dies ist in der obigen Abbildung dargestellt. Benutzer können Ihre Abfrage so in Analytics verfeinern und zur zukünftigen Nutzung in Warnungen speichern. Weitere Informationen zum Speichern von Abfragen finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md) oder [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md).

1. *Protokollwarnungen*: Wenn Sie diese Option ausgewählt haben, können im Feld **Suchabfrage** Abfragen für Warnungen angegeben werden. Ist die Abfragesyntax falsch, wird eine Fehlermeldung in ROT angezeigt. Wenn die Abfragesyntax korrekt ist, werden für die angegebene Abfrage Referenzverlaufsdaten als Diagramm angezeigt. Dabei besteht die Möglichkeit, das Zeitfenster von den letzten sechs Stunden bis zur letzten Woche anzupassen.

    ![Konfigurieren einer Warnungsregel](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Die Visualisierung von Verlaufsdaten kann nur angezeigt werden, wenn die Abfrageergebnisse Details in Bezug auf die Zeit enthalten. Wenn Ihre Abfrage zusammengefasste Daten oder bestimmte Spaltenwerte zurückgibt, werden diese als einzelner Plot angezeigt.
   > Wenn Sie als Typ für die Metrikmessung Protokollwarnungen mit Application Insights verwenden oder [auf die neue API umgestiegen](alerts-log-api-switch.md) sind, können Sie angeben, nach welcher Variablen die Daten gruppiert werden sollen. Verwenden Sie dazu die Option **Aggregieren auf** wie hier gezeigt:
   > 
   > ![Option „Aggregieren auf“](media/alerts-log/aggregate-on.png)

1. *Protokollwarnungen*: Ist die Visualisierung eingerichtet, kann die **Warnungslogik** aus den angezeigten Optionen „Bedingung“, „Aggregation“ und schließlich „Schwellenwert“ ausgewählt werden. Geben Sie schließlich über die Option **Zeitraum** den Zeitraum für die Bewertung der angegebenen Bedingung in der Logik an. Legen Sie durch Auswahl der **Häufigkeit** außerdem fest, wie oft die Warnung ausgeführt werden soll. **Protokollwarnungen** können auf Folgendem basieren:
    - [Anzahl von Datensätzen](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): Eine Warnung wird erstellt, wenn die Anzahl der von der Abfrage zurückgegebenen Datensätze entweder größer als oder kleiner als der Wert ist, den Sie angeben.
    - [Metrische Maßeinheit](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): Eine Warnung wird erstellt, wenn die einzelnen *aggregierten Werte* in den Ergebnissen den angegebenen Schwellenwert überschreiten und *nach dem ausgewählten Wert gruppiert* werden. Die Anzahl von Sicherheitsverletzungen für eine Warnung ist die Anzahl der Häufigkeit, mit der im ausgewählten Zeitraum der Schwellenwert überschritten wird. Sie können „Sicherheitsverletzungen gesamt“ für eine beliebige Kombination aus Verletzungen in den Ergebnissen angeben oder „Aufeinanderfolgende Sicherheitsverletzungen“, um vorauszusetzen, dass die Sicherheitsverletzungen in aufeinanderfolgenden Stichproben auftreten müssen.


1. Legen Sie im zweiten Schritt einen Namen für ihre Warnung im Feld **Name der Warnungsregel** fest, und geben Sie eine **Beschreibung** mit Einzelheiten zur Warnung sowie den **Schweregrad** über die angegebenen Optionen an. Diese Details werden in allen Warnungs-E-Mails bzw. Benachrichtigungen wiederverwendet oder mithilfe von Push von Azure Monitor übertragen. Darüber hinaus kann der Benutzer durch Umschalten der Option **Regel beim Erstellen aktivieren** festlegen, dass die Warnungsregel bei der Erstellung sofort aktiviert werden soll.

    Nur bei **Protokollwarnungen** sind einige zusätzliche Funktionen in den Warnungsdetails verfügbar:

    - **Warnungen unterdrücken**: Wenn Sie die Unterdrückung für die Warnungsregel aktivieren, werden Aktionen für die Regel nach dem Erstellen einer neuen Warnung für einen vorher festgelegten Zeitraum deaktiviert. Die Regel wird weiter ausgeführt und erstellt Warnungsdatensätze, wenn die Kriterien erfüllt sind. Dies erspart Ihnen die Zeit, das Problem zu beheben, ohne doppelte Aktionen durchzuführen.

        ![Unterdrücken von Warnungen für Protokollwarnungen](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Geben Sie einen größeren Wert für das Unterdrücken von Warnungen als für die Häufigkeit der Warnung an, um sicherzustellen, dass Benachrichtigungen ohne Überlappung beendet werden.

1. Legen Sie im dritten und letzten Schritt bei Bedarf fest, dass eine **Aktionsgruppe** für die Warnungsregel ausgelöst werden muss, wenn die Warnungsbedingung erfüllt ist. Sie können eine vorhandene Aktionsgruppe mit einer Warnung auswählen oder eine neue Aktionsgruppe erstellen. Entsprechend der ausgewählten Aktionsgruppe werden beim Auslösen einer Warnung u.a. folgende Vorgänge in Azure ausgeführt: Senden von E-Mails, Senden von SMS, Aufrufen von Webhooks, Wiederherstellen mit Azure Runbooks und Übertragen von Inhalten an das ITSM-Tool mithilfe von Push. Erfahren Sie mehr über [Aktionsgruppen](action-groups.md).

    > [!NOTE]
    > Informationen zu den Einschränkungen für Runbook-Nutzlasten, die für Protokollwarnungen über Azure-Aktionsgruppen ausgelöst werden, finden Sie unter [Azure-Abonnementdienstgrenzen](../../azure-subscription-service-limits.md).

    Für **Protokollwarnungen** sind einige zusätzliche Funktionen zum Überschreiben der Standardaktionen verfügbar:

    - **E-Mail-Benachrichtigung**: Überschreibt den *E-Mail-Betreff* in der E-Mail, die über die Aktionsgruppe gesendet wurde, wenn mindestens eine E-Mail-Aktion in der genannten Aktionsgruppe vorhanden ist. Der Text der E-Mail kann nicht geändert werden, und dieses Feld ist **nicht** für E-Mail-Adressen vorgesehen.
    - **Benutzerdefinierte JSON-Nutzlast einschließen**: Diese Option überschreibt den von Aktionsgruppen verwendeten JSON-Webhook, wenn mindestens eine Webhookaktion in der genannten Aktionsgruppe vorhanden ist. Der Benutzer kann das JSON-Format angeben, das für alle in zugewiesenen Aktionsgruppen konfigurierte Webhooks verwendet werden soll. Weitere Informationen zu Webhookformaten finden Sie unter [Webhookaktionen für Protokollwarnungsregeln](../../azure-monitor/platform/alerts-log-webhook.md). Die Option „Webhook anzeigen“ bietet die Möglichkeit, das Format anhand von JSON-Beispieldaten zu überprüfen.

        ![Aktionsüberschreibungen für Protokollwarnungen](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Wenn alle Felder gültig sind und mit einem grünen Häkchen versehen sind, kann auf die Schaltfläche **Warnungsregel erstellen** geklickt werden, wodurch eine Warnung in Azure Monitor unter „Warnungen“ erstellt wird. Alle Warnungen können über das Dashboard „Warnungen“ angezeigt werden.

     ![Regelerstellung](media/alerts-log/AlertsPreviewCreate.png)

     Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.

Benutzer können ihre Analyseabfrage auch in [Logs Analytics](../log-query/portals.md) abschließen und dann über die Schaltfläche „Warnung festlegen“ eine Warnung erstellen. Anschließend können sie die Anweisungen ab Schritt 6 im obigen Tutorial befolgen.

 ![Log Analytics – Warnung festlegen](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Anzeigen und Verwalten von Warnungen im Azure-Portal

1. Wählen Sie im [Portal](https://portal.azure.com/) die Option **Monitor** und im Abschnitt „MONITOR“ dann **Warnungen** aus.

1. Das **Dashboard „Warnungen“** wird angezeigt, wobei alle Azure-Warnungen (einschließlich Protokollwarnungen) in einem einzelnen Board angezeigt werden, und zwar einschließlich jeder Instanz seit der Auslösung der Protokollwarnungsregel. Weitere Informationen finden Sie unter [Warnungsverwaltung](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Protokollwarnungsregeln bestehen aus einer benutzerdefinierten, abfragebasierten Logik, die von Benutzern bereitgestellt wird und somit keinen aufgelösten Zustand aufweisen. Daher wird sie jedes Mal ausgelöst, wenn die in der Protokollwarnungsregel festgelegten Bedingungen erfüllt sind.

1. Wählen Sie in der oberen Leiste die Schaltfläche **Regeln verwalten** aus, um zum Abschnitt „Regelverwaltung“ zu navigieren, wobei alle erstellten Warnungsregeln sowie deaktivierte Warnungen aufgeführt sind.
    ![Verwalten von Warnungsregeln](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Verwalten von Protokollwarnungen mithilfe von Azure-Ressourcenvorlagen

Protokollwarnungen in Azure Monitor sind dem Ressourcentyp `Microsoft.Insights/scheduledQueryRules/` zugeordnet. Weitere Informationen zu diesem Ressourcentyp finden Sie unter [Azure Monitor: Referenz für die Scheduled Query Rules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Protokollwarnungen für Application Insights oder Log Analytics können mithilfe der [Scheduled Query Rules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) erstellt werden.

> [!NOTE]
> Protokollwarnungen für Log Analytics können zudem über die [Legacywarnungs-API von Log Analytics](api-alerts.md) sowie über Legacyvorlagen von [gespeicherten Log Analytics-Suchen und -Warnungen](../insights/solutions-resources-searches-alerts.md) verwaltet werden. Weitere Informationen zur standardmäßigen Verwendung der hier beschriebenen neuen ScheduledQueryRules-API finden Sie unter [Wechseln zur neuen API für Log Analytics-Warnungen](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Beispiel für das Erstellen einer Protokollwarnung mithilfe einer Azure-Ressourcenvorlage

Im Folgenden sehen Sie die Struktur für die auf der [Scheduled Query Rules-Erstellung](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) basierende Ressourcenvorlage mithilfe der Standardprotokollsuchabfrage [Anzahl der Ergebnistyp-Protokollwarnungen](alerts-unified-log.md#number-of-results-alert-rules) mit einem Stichprobendataset als Variablen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "tags":{"[variables('alertTag')]": "Resource"},
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Das Tag-Feld mit einem ausgeblendetem Link zur Zielressource ist bei Verwendung des API-Aufrufs von [Regeln für geplante Abfrage](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) oder der Ressourcenvorlage zwingend erforderlich.

Das JSON-Beispiel oben kann im Rahmen dieser exemplarischen Vorgehensweise z.B. als „sampleScheduledQueryRule.json“ gespeichert und mithilfe von [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) bereitgestellt werden.


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Protokollwarnung mit ressourcenübergreifender Abfrage mithilfe einer Azure-Ressourcenvorlage

Im Folgenden sehen Sie die Struktur für die auf der [Scheduled Query Rules-Erstellung](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) basierende Ressourcenvorlage mithilfe der [ressourcenübergreifenden Protokollsuchabfrage](../../azure-monitor/log-query/cross-workspace-query.md) [metrische Messungstyp-Protokollwarnung](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) mit einem Stichprobendataset als Variablen.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "tags":{"[variables('alertTag')]": "Resource"},
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Das Tag-Feld mit einem ausgeblendetem Link zur Zielressource ist bei Verwendung des API-Aufrufs von [Regeln für geplante Abfrage](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) oder der Ressourcenvorlage zwingend erforderlich. Bei Verwendung einer ressourcenübergreifenden Abfrage in der Protokollwarnung ist die Verwendung von [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) zwingend, und der Benutzer muss Zugriff auf die Liste der angegebenen Ressourcen besitzen.

Das JSON-Beispiel oben kann im Rahmen dieser exemplarischen Vorgehensweise z.B. als „sampleScheduledQueryRule.json“ gespeichert und mithilfe von [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) bereitgestellt werden.

## <a name="managing-log-alerts-using-powershell"></a>Verwalten von Protokollwarnungen mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor – [API für geplante Abfrageregeln](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) ist eine REST-API und vollständig kompatibel mit der Azure Resource Manager-REST-API. Die unten aufgeführten PowerShell-Cmdlets stehen zur Nutzung der [API für Regeln für geplante Abfragen](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) zur Verfügung.

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule): PowerShell-Cmdlet zum Erstellen einer neuen Protokollwarnungsregel.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule): PowerShell-Cmdlet zum Aktualisieren einer vorhandenen Protokollwarnungsregel.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Quellparameter für eine Protokollwarnung angegeben werden. Wird von den Cmdlets [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) und [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) als Eingabe verwendet.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Planungsparameter für eine Protokollwarnung angegeben werden. Wird von den Cmdlets [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) und [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) als Eingabe verwendet.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Aktionsparameter für eine Protokollwarnung angegeben werden. Wird von den Cmdlets [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) und [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) als Eingabe verwendet.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Aktionsgruppenparameter für eine Protokollwarnung angegeben werden. Wird vom Cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) als Eingabe verwendet.
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Auslöserbedingungsparameter für eine Protokollwarnung angegeben werden. Wird vom Cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) als Eingabe verwendet.
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Metrik-Auslöserbedingungsparameter für [Warnungsregeln des Typs „Metrische Maßeinheit“](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) angegeben werden. Wird vom Cmdlet [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) als Eingabe verwendet.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule): PowerShell-Cmdlet zum Auflisten von vorhandenen Protokollwarnungsregeln oder einer bestimmten Protokollwarnungsregel.
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule): PowerShell-Cmdlet zum Aktivieren oder Deaktivieren von Protokollwarnungsregeln.
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-Cmdlet zum Löschen einer vorhandenen Protokollwarnungsregel.

> [!NOTE]
> Mit ScheduledQueryRules-PowerShell-Cmdlets können nur Regeln verwaltet werden, die vom Cmdlet selbst oder mit der [API für Regeln für geplante Abfragen](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) von Azure Monitor erstellt werden. Protokollwarnungsregeln, die mit der [Legacy-API für Warnungen von Log Analytics](api-alerts.md) und mit Legacyvorlagen von [gespeicherten Log Analytics-Suchen und -Warnungen](../insights/solutions-resources-searches-alerts.md) erstellt wurden, können erst dann mit ScheduledQueryRules-PowerShell-Cmdlets verwaltet werden, wenn der Benutzer die [API-Einstellung für Protokollwarnungen wechselt](alerts-log-api-switch.md).

## <a name="managing-log-alerts-using-cli-or-api"></a>Verwalten von Protokollwarnungen per CLI oder API

Azure Monitor – [API für geplante Abfrageregeln](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) ist eine REST-API und vollständig kompatibel mit der Azure Resource Manager-REST-API. Daher ist die Verwendung über PowerShell mit Resource Manager-Befehlen für die Azure CLI möglich.


> [!NOTE]
> Protokollwarnungen für Log Analytics können zudem über die [Legacywarnungs-API von Log Analytics](api-alerts.md) sowie über Legacyvorlagen von [gespeicherten Log Analytics-Suchen und -Warnungen](../insights/solutions-resources-searches-alerts.md) verwaltet werden. Weitere Informationen zur standardmäßigen Verwendung der hier beschriebenen neuen ScheduledQueryRules-API finden Sie unter [Wechseln zur neuen API für Log Analytics-Warnungen](alerts-log-api-switch.md).

Protokollwarnungen verfügen derzeit nicht über dedizierte CLI-Befehle. Sie können aber über den CLI-Befehl von Azure Resource Manager für die weiter oben im Abschnitt „Ressourcenvorlage“ dargestellte Ressourcenvorlage (sampleScheduledQueryRule.json) verwendet werden. Dies ist hier dargestellt:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Bei erfolgreicher Ausführung wird 201 zurückgegeben, wenn eine neue Warnungsregel erstellt wurde, bzw. 200, wenn eine vorhandene Warnungsregel geändert wurde.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Protokollwarnungen in Azure-Warnungen](../../azure-monitor/platform/alerts-unified-log.md).
* Machen Sie sich mit [Webhookaktionen für Protokollwarnungen](../../azure-monitor/platform/alerts-log-webhook.md) vertraut.
* Weitere Informationen zu [Application Insights](../../azure-monitor/app/analytics.md)
* Weitere Informationen zum [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md).
