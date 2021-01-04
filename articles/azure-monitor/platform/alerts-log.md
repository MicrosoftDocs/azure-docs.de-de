---
title: Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Monitor zum Erstellen, Anzeigen und Verwalten von Protokollwarnungsregeln verwenden.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 61aba3e5792d1cc20da16686d052de91744dab76
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186778"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor

## <a name="overview"></a>Übersicht

Mithilfe von Protokollwarnungen können Benutzer eine [Log Analytics](../log-query/log-analytics-tutorial.md)-Abfrage verwenden, um Ressourcenprotokolle mit einer bestimmten Frequenz auszuwerten und eine Warnung auf der Grundlage der Ergebnisse auszulösen. Durch Regeln können über [Aktionsgruppen](./action-groups.md) einzelne oder mehrere Aktionen ausgelöst werden. Weitere Informationen zu Funktionen und zur Terminologie von Protokollwarnungen finden Sie [hier](alerts-unified-log.md).

In diesem Artikel erfahren Sie, wie Sie Protokollwarnungen mithilfe von Azure Monitor erstellen und verwalten. Warnungsregeln werden durch drei Komponenten definiert:
- Ziel: Eine bestimmte zu überwachende Azure-Ressource
- Kriterien: Logik für die Auswertung. Bei Erfüllung wird die Warnung ausgelöst.  
- Aktion: Benachrichtigungen oder Automatisierung: E-Mail, SMS, Webhook usw.

Sie können auch Protokollwarnungsregeln mithilfe von Azure Resource Manager-Vorlagen erstellen. Diese werden in einem [separaten Artikel](alerts-log-create-templates.md) beschrieben.

> [!NOTE]
> Protokolldaten aus einem [Log Analytics-Arbeitsbereich](../log-query/log-analytics-tutorial.md) können an den Azure Monitor-Metrikspeicher gesendet werden. Metrikwarnungen weisen ein [anderes Verhalten](alerts-metric-overview.md) auf. Je nach den Daten, mit denen Sie arbeiten, kann dies wünschenswert sein. Informationen zu den Protokollen, die Sie an Metriken weiterleiten können, und der zugehörigen Vorgehensweise finden Sie unter [Metrikwarnungen für Protokolle](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Erstellen einer Protokollwarnungsregel mit dem Azure-Portal

Zum Schreiben von Abfragen für Warnungen müssen folgende erste Schritte ausgeführt werden:

1. Navigieren Sie zu der Ressource, für die Sie eine Warnung einrichten möchten.
1. Wählen Sie unter **Monitor** die Option **Protokolle** aus.
1. Fragen Sie die Protokolldaten ab, die ggf. einen Hinweis auf das Problem enthalten. Unter [Gespeicherte Abfragen in Azure Monitor Log Analytics](../log-query/example-queries.md) können Sie sich anhand von Beispielen mit den Ermittlungsoptionen vertraut machen. Alternativ können Sie [mit dem Schreiben einer eigenen Abfrage beginnen](../log-query/log-analytics-tutorial.md). Informieren Sie sich auch über das [Erstellen von optimierten Warnungsabfragen](alerts-log-query.md).
1. Klicken Sie auf die Schaltfläche „+ Neue Warnungsregel“, um mit der Warnungserstellung zu beginnen.

    ![Log Analytics – Warnung festlegen](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Wenn Sie den Ressourcenzugriffsmodus für Protokolle verwenden, empfiehlt es sich, Warnungen im großen Stil zu erstellen. Dieser Modus wird für mehrere Ressourcen mit einer Ressourcengruppe oder einem Abonnementbereich ausgeführt. Warnungen im großen Stil verringern den Verwaltungsaufwand. Schließen Sie die Spalte mit der Ressourcen-ID in die Ergebnisse ein, um die Ausrichtung auf die Ressourcen zu ermöglichen. Informationen zum Aufteilen von Warnungen nach Dimensionen finden Sie [hier](alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Protokollwarnung für Log Analytics und Application Insights

1. Wenn die Abfragesyntax korrekt ist, werden Verlaufsdaten für die Abfrage als Graph angezeigt. Der Diagrammzeitraum kann dabei auf einen Zeitraum zwischen den letzten sechs Stunden und der letzten Woche festgelegt werden.
 
    Wenn die Abfrageergebnisse zusammengefasste Daten oder [projektspezifische](/azure/kusto/query/projectoperator) Spalten ohne Zeitspalte enthalten, enthält das Diagramm einen einzelnen Wert.

    ![Konfigurieren einer Warnungsregel](media/alerts-log/AlertsPreviewAlertLog.png)

1. Wählen Sie mithilfe der Option [**Zeitraum**](alerts-unified-log.md#query-time-range) den Zeitbereich aus, für den die angegebene Bedingung überprüft werden soll.

1. Protokollwarnungen können auf zwei Arten von [**Measures**](alerts-unified-log.md#measure) basieren:
    1. **Anzahl von Ergebnissen**: Anzahl der von der Abfrage zurückgegebenen Datensätze.
    1. **Metrische Maßeinheit**: *Aggregatwert*, der unter Verwendung von „summarize“ berechnet wird (gruppiert nach ausgewählten Ausdrücken und [bin()](/azure/kusto/query/binfunction)-Auswahl). Beispiel:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. Bei Warnungslogik mit Metrikmessungen können Sie optional mithilfe der Option **Aggregieren nach** angeben, wie Sie die [Warnungen nach Dimensionen aufteilen](alerts-unified-log.md#split-by-alert-dimensions) möchten. Der Zeilengruppierungsausdruck muss eindeutig und sortiert sein.

    > [!NOTE]
    > Da [bin()](/azure/kusto/query/binfunction) zu uneinheitlichen Zeitintervallen führen kann, wird die Funktion [bin()](/azure/kusto/query/binfunction) zur Laufzeit automatisch durch den Warnungsdienst in die Funktion [bin_at()](/azure/kusto/query/binatfunction) mit der richtigen Zeit konvertiert, um Ergebnisse mit Fixpunkt sicherzustellen.

    > [!NOTE]
    > Die Aufteilung nach Warnungsdimensionen ist nur für die aktuelle scheduledQueryRules-API verfügbar. Bei Verwendung der älteren [Log Analytics-Warnungs-API](api-alerts.md) müssen Sie auf die neue API umstellen. Weitere Informationen zur Umstellung finden Sie [hier](./alerts-log-api-switch.md). Ressourcenbezogene Warnungen im großen Stil werden erst ab der API-Version `2020-05-01-preview` unterstützt.

    ![Option „Aggregieren auf“](media/alerts-log/aggregate-on.png)

1. Legen Sie als Nächstes auf der Grundlage der Vorschaudaten den [**Operator**, den **Schwellenwert**](alerts-unified-log.md#threshold-and-operator) und die [**Frequenz**](alerts-unified-log.md#frequency) fest.

1. Optional können Sie mithilfe von **„Sicherheitsverletzungen gesamt“ oder „Aufeinanderfolgende Sicherheitsverletzungen“** auch die [Anzahl von Verstößen zum Auslösen einer Warnung](alerts-unified-log.md#number-of-violations-to-trigger-alert) festlegen.

1. Wählen Sie **Fertig** aus. 

1. Definieren Sie **Name der Warnungsregel** und **Beschreibung**, und wählen Sie den **Schweregrad** der Warnung aus. Diese Angaben werden in allen Warnungsaktionen verwendet. Darüber hinaus können Sie durch Auswählen von **Regel beim Erstellen aktivieren** festlegen, dass die Warnungsregel bei der Erstellung nicht aktiviert werden soll.

1. Mithilfe der Option [**Warnungen unterdrücken**](alerts-unified-log.md#state-and-resolving-alerts) können Sie auswählen, ob Regelaktionen eine Weile unterdrückt werden sollen, nachdem eine Warnung ausgelöst wurde. Die Regel wird weiterhin ausgeführt und generiert Warnungen, es werden jedoch keine Aktionen ausgelöst, um Störungen zu vermeiden. Der Wert für „Aktionen unterdrücken“ muss größer sein als die Frequenz der Warnung, um wirksam zu sein.

    ![Unterdrücken von Warnungen für Protokollwarnungen](media/alerts-log/AlertsPreviewSuppress.png)

1. Geben Sie an, ob durch die Warnungsregel einzelne oder mehrere [**Aktionsgruppen**](action-groups.md#webhook) ausgelöst werden sollen, wenn die Warnungsbedingung erfüllt ist.

    > [!NOTE]
    > Informationen zu den Einschränkungen für mögliche Aktionen finden Sie unter [Einschränkungen für Azure-Abonnementdienste](../../azure-resource-manager/management/azure-subscription-service-limits.md).  

1. Aktionen in Protokollwarnungsregeln können optional angepasst werden:

    - **E-Mail-Betreff**: Überschreibt den *E-Mail-Betreff* von E-Mail-Aktionen. Der Text der E-Mail kann nicht geändert werden, und dieses Feld ist **nicht für E-Mail-Adressen vorgesehen**.
    - **Benutzerdefinierte JSON-Nutzlast einschließen**: Setzt den von Aktionsgruppen verwendeten Webhook-JSON-Code außer Kraft, wenn die Aktionsgruppe eine Webhookaktion enthält. Weitere Informationen zu Webhookaktionen für Protokollwarnungsregeln finden Sie [hier](./alerts-log-webhook.md).

    ![Aktionsüberschreibungen für Protokollwarnungen](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Wenn alle Felder ordnungsgemäß festgelegt wurden, kann auf die Schaltfläche **Warnungsregel erstellen** geklickt werden, und es wird eine Warnung erstellt.

    Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.

    ![Regelerstellung](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Erstellen einer Protokollwarnung für Log Analytics und Application Insights über die Warnungsverwaltung

> [!NOTE]
> Die Erstellung über die Warnungsverwaltung wird derzeit für ressourcenbezogene Protokolle nicht unterstützt.

1. Navigieren Sie im [Portal](https://portal.azure.com/) zu **Monitor** > **Warnungen**.

    ![Überwachung](media/alerts-log/AlertsPreviewMenu.png)

1. Wählen Sie **Neue Warnungsregel** aus. 

    ![Warnung hinzufügen](media/alerts-log/AlertsPreviewOption.png)

1. Der Bereich **Warnung erstellen** wird geöffnet. Es besteht aus vier Teilen: 
    - Die Ressource, für die die Warnung gilt
    - Die zu überprüfende Bedingung
    - Die durchzuführenden Aktionen, wenn die Bedingung erfüllt ist
    - Die Details zur Benennung und Beschreibung der Warnung 

    ![Erstellen einer Regel](media/alerts-log/AlertsPreviewAdd.png)

1. Klicken Sie auf die Schaltfläche **Ressource auswählen**. Filtern Sie die Ansicht, indem Sie das *Abonnement* und den *Ressourcentyp* auswählen, und wählen Sie anschließend eine Ressource aus. Vergewissern Sie sich, dass für die Ressource Protokolle verfügbar sind.

   ![Auswählen einer Ressource](media/alerts-log/Alert-SelectResourceLog.png)

1. Wählen Sie als Nächstes die Schaltfläche **Bedingung** aus, um die Liste mit den für die Ressource verfügbaren Signaloptionen anzuzeigen. Wählen Sie die Option **Benutzerdefinierte Protokollsuche** aus.

   ![Auswählen einer Ressource – benutzerdefinierte Protokollsuche](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > Im Warnungsportal werden gespeicherte Abfragen von Log Analytics und Application Insights aufgeführt. Diese können als Vorlage für Warnungsabfragen verwendet werden.

1. Nachdem Sie eine Option ausgewählt haben, können Sie die Warnungsabfrage im Feld **Suchabfrage** schreiben, einfügen oder bearbeiten.

1. Fahren Sie mit den nächsten Schritten fort, die im [letzten Abschnitt](#log-alert-for-log-analytics-and-application-insights) beschrieben sind.

### <a name="log-alert-for-all-other-resource-types"></a>Protokollwarnung für alle anderen Ressourcentypen

> [!NOTE]
> Für die API-Version `2020-05-01-preview` und ressourcenbezogene Protokollwarnungen fallen aktuell keine zusätzlichen Gebühren an.  Die Preise für Previewfunktionen werden später bekannt gegeben, und vor Abrechnungsbeginn erhalten Sie eine entsprechende Benachrichtigung. Falls Sie sich dafür entscheiden, die neue API-Version und ressourcenbezogene Protokollwarnungen über den Benachrichtigungszeitraum hinaus zu verwenden, wird Ihnen der entsprechende Tarif in Rechnung gestellt.

1. Beginnen Sie auf der Registerkarte **Bedingung**:

    1. Vergewissern Sie sich, dass die Angaben für [**Measure**](alerts-unified-log.md#measure), [**Aggregationstyp**](alerts-unified-log.md#aggregation-type) und [**Aggregationsgranularität**](alerts-unified-log.md#aggregation-granularity) korrekt sind. 
        1. Von der Regel wird standardmäßig die Anzahl von Ergebnissen in den letzten fünf Minuten gezählt.
        1. Bei Erkennung zusammengefasster Abfrageergebnisse wird die Regel innerhalb weniger Sekunden automatisch aktualisiert, um dies zu erfassen.

    1. Wählen Sie bei Bedarf die [Warnungsaufteilung nach Dimensionen](alerts-unified-log.md#split-by-alert-dimensions) aus: 
       - **Spalte mit Ressourcen-ID** wird automatisch ausgewählt (sofern erkannt) und ändert den Kontext der ausgelösten Warnung in die Ressource des Datensatzes. 
       - Die Auswahl von **Spalte mit Ressourcen-ID** kann aufgehoben werden, um Warnungen für Abonnements oder Ressourcengruppen auszulösen. Das Aufheben der Auswahl ist hilfreich, wenn Abfrageergebnisse auf mehreren Ressourcen basieren. Ein Beispiel wäre etwa eine Abfrage, mit der überprüft wird, ob 80 Prozent der virtuellen Computer der Ressourcengruppe eine hohe CPU-Auslastung aufweisen.
       - Mithilfe der Dimensionstabelle können außerdem bis zu sechs weitere Aufteilungen für beliebige Zahlen- oder Textspaltentypen ausgewählt werden.
       - Warnungen werden separat gemäß der Aufteilung und auf der Grundlage individueller Kombinationen ausgelöst, und die Warnungsnutzdaten enthalten diese Informationen.
    
        ![Auswählen von Aggregationsparametern und Aufteilung](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. Im Diagramm **Vorschau** werden die Ergebnisse von Abfrageauswertungen im Zeitverlauf angezeigt. Sie können den Diagrammzeitraum ändern oder andere Zeitreihen auswählen, die sich aus der individuellen Warnungsaufteilung nach Dimensionen ergeben haben.

        ![Vorschaudiagramm](media/alerts-log/preview-chart.png)

    1. Legen Sie als Nächstes auf der Grundlage der Vorschaudaten die **Warnungslogik**, den [**Operator**, den **Schwellenwert**](alerts-unified-log.md#threshold-and-operator) und die [**Frequenz**](alerts-unified-log.md#frequency) fest.

        ![Vorschaudiagramm mit Schwellenwert und Warnungslogik](media/alerts-log/chart-and-alert-logic.png)

    1. Sie können optional im Abschnitt **Erweiterte Optionen** die [**Anzahl von Verstößen zum Auslösen der Warnung**](alerts-unified-log.md#number-of-violations-to-trigger-alert) festlegen.
    
        ![Erweiterte Optionen](media/alerts-log/advanced-options.png)

1. Wählen Sie auf der Registerkarte **Aktionen** die erforderlichen [Aktionsgruppen](action-groups.md) aus, oder erstellen Sie sie.

    ![Registerkarte „Aktionen“](media/alerts-log/actions-tab.png)

1. Legen Sie auf der Registerkarte **Details** die **Details zur Warnungsregel** und die **Projektdetails** fest. Optional können Sie mit **Jetzt ausführen** festlegen, dass die Ausführung jetzt beginnen soll, oder [**Aktionen unterdrücken**](alerts-unified-log.md#state-and-resolving-alerts) verwenden, um Aktionen nach dem Auslösen der Warnungsregel für eine gewisse Zeit zu unterdrücken.

    > [!NOTE]
    > Protokollwarnungsregeln sind aktuell zustandslos und lösen jedes Mal, wenn eine Warnung erstellt wird, eine Aktion aus, sofern keine Unterdrückung definiert ist.

    ![Registerkarte „Details“](media/alerts-log/details-tab.png)

1. Legen Sie auf der Registerkarte **Tags** alle erforderlichen Tags für die Warnungsregelressource fest.

    ![Registerkarte „Tags“](media/alerts-log/tags-tab.png)

1. Auf der Registerkarte **Überprüfen und erstellen** wird eine Überprüfung durchgeführt, und Sie werden auf ggf. vorliegende Probleme hingewiesen. Überprüfen und genehmigen Sie die Regeldefinition.
1. Sind alle Angaben korrekt, wählen Sie die Schaltfläche **Erstellen** aus, und schließen Sie die Erstellung der Warnungsregel ab. Alle Warnungen können über die Warnungsverwaltung angezeigt werden.
 
    ![Registerkarte „Überprüfen und erstellen“](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Anzeigen und Verwalten von Warnungen im Azure-Portal

1. Wählen Sie im [Portal](https://portal.azure.com/) die relevante Ressource oder den Dienst **Monitor** aus. Wählen Sie im Abschnitt „Monitor“ die Option **Warnungen** aus.

1. In der Warnungsverwaltung werden alle ausgelösten Warnungen angezeigt. Weitere Informationen zur Warnungsverwaltung finden Sie [hier](alerts-managing-alert-instances.md).

    > [!NOTE]
    > Protokollwarnungsregeln sind aktuell [zustandslos und werden nicht aufgelöst](alerts-unified-log.md#state-and-resolving-alerts).

1. Klicken Sie auf der oberen Leiste auf **Warnungsregeln verwalten**, um Regeln zu bearbeiten:

    ![ Verwalten von Warnungsregeln](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Verwalten von Protokollwarnungen mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell wird derzeit in der API-Version `2020-05-01-preview` nicht unterstützt.

Die folgenden PowerShell-Cmdlets können zum Verwalten von Regeln mit der [API für Regeln für geplante Abfragen](/rest/api/monitor/scheduledqueryrules/) verwendet werden:

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule): PowerShell-Cmdlet zum Erstellen einer neuen Protokollwarnungsregel
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule): PowerShell-Cmdlet zum Aktualisieren einer vorhandenen Protokollwarnungsregel
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Quellparameter für eine Protokollwarnung angegeben werden. Wird von den Cmdlets [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) und [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) als Eingabe verwendet.
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Planungsparameter für eine Protokollwarnung angegeben werden. Wird von den Cmdlets [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) und [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) als Eingabe verwendet.
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Aktionsparameter für eine Protokollwarnung angegeben werden. Wird von den Cmdlets [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) und [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) als Eingabe verwendet.
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Aktionsgruppenparameter für eine Protokollwarnung angegeben werden. Wird vom Cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) als Eingabe verwendet.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Auslöserbedingungsparameter für eine Protokollwarnung angegeben werden. Wird vom Cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) als Eingabe verwendet.
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger): PowerShell-Cmdlet zum Erstellen oder Aktualisieren von Objekten, mit denen Bedingungsparameter von Metriktriggern für [Warnungsregeln des Typs „Metrische Maßeinheit“](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) angegeben werden. Wird vom Cmdlet [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) als Eingabe verwendet.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule): PowerShell-Cmdlet zum Auflisten der vorhandenen Protokollwarnungsregeln oder einer bestimmten Protokollwarnungsregel
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule): PowerShell-Cmdlet zum Aktivieren oder Deaktivieren von Protokollwarnungsregeln
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell-Cmdlet zum Löschen einer vorhandenen Protokollwarnungsregel

> [!NOTE]
> Mit PowerShell-Cmdlets vom Typ „ScheduledQueryRules“ können nur Regeln verwaltet werden, die in der aktuellen [API für Regeln für geplante Abfragen](/rest/api/monitor/scheduledqueryrules/) erstellt wurden. Protokollwarnungsregeln, bei denen die ältere [Log Analytics-Warnungs-API](api-alerts.md) verwendet wird, können erst nach der [Umstellung auf die API für Regeln für geplante Abfragen](alerts-log-api-switch.md) mit PowerShell verwaltet werden.

Im Anschluss finden Sie Beispielschritte für die Erstellung einer Protokollwarnungsregel mit PowerShell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

Die Protokollwarnung kann auch unter Verwendung [einer Vorlagen- und Parameterdatei](./alerts-log-create-templates.md) mithilfe von PowerShell erstellt werden:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Verwalten von Protokollwarnungen über die Befehlszeilenschnittstelle

> [!NOTE]
> Die Azure-Befehlszeilenschnittstelle wird erst ab Version `2020-05-01-preview` der scheduledQueryRules-API unterstützt. Für ältere API-Versionen kann die Azure Resource Manager-Befehlszeilenschnittstelle mit Vorlagen verwendet werden, wie weiter unten beschrieben. Bei Verwendung der älteren [Log Analytics-Warnungs-API](api-alerts.md) müssen Sie auf die neue API umstellen, um die Befehlszeilenschnittstelle verwenden zu können. Weitere Informationen zur Umstellung finden Sie [hier](./alerts-log-api-switch.md).

In den vorangegangenen Abschnitten haben Sie erfahren, wie Sie über das Azure-Portal Protokollwarnungsregeln erstellen, anzeigen und verwalten. In diesem Abschnitt wird beschrieben, wie Sie dasselbe mit der plattformübergreifenden [Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli) (Azure CLI) erreichen können. Die schnellste Möglichkeit, mit der Verwendung der Azure CLI zu beginnen, ist über [Azure Cloud Shell](../../cloud-shell/overview.md). In diesem Artikel wird Cloud Shell verwendet.

1. Wählen Sie im Azure-Portal die Option **Cloud Shell** aus.

1. An der Eingabeaufforderung können Sie Befehle mit der Option ``--help`` verwenden, um mehr über den Befehl und seine Verwendung zu erfahren. Der folgende Befehl zeigt beispielsweise die Liste der verfügbaren Befehle zum Erstellen, Anzeigen und Verwalten von Protokollwarnungen an:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. Sie können eine Protokollwarnungsregel erstellen, die die Anzahl von Systemereignisfehlern überwacht:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. Mithilfe des folgenden Befehls können Sie alle Protokollwarnungen in einer Ressourcengruppe anzeigen:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. Sie können die Details einer bestimmten Protokollwarnungsregel unter Verwendung des Namens oder der Ressourcen-ID der Regel anzeigen:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. Mithilfe des folgenden Befehls können Sie eine Protokollwarnungsregel deaktivieren:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. Mithilfe des folgenden Befehls können Sie eine Protokollwarnungsregel löschen:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Sie können auch die Azure Resource Manager-Befehlszeilenschnittstelle mit [Vorlagendateien](./alerts-log-create-templates.md) verwenden:

```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Bei erfolgreicher Erstellung wird 201 zurückgegeben. Bei erfolgreicher Aktualisierung wird 200 zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Protokollwarnungen](./alerts-unified-log.md).
* Erstellen Sie Protokollwarnungen mithilfe von [Azure Resource Manager-Vorlagen](./alerts-log-create-templates.md).
* Machen Sie sich mit [Webhookaktionen für Protokollwarnungen](./alerts-log-webhook.md) vertraut.
* Weitere Informationen zum [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md).