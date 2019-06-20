---
title: Behandeln von Protokollwarnungen in Azure Monitor | Microsoft-Dokumentation
description: Allgemeine Probleme, Fehler und deren Behandlung für Protokollwarnungsregeln in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683391"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Behandeln von Protokollwarnungen in Azure Monitor  

Dieser Artikel zeigt die Lösung häufiger Probleme beim Einrichten von Protokollwarnungen in Azure Monitor. Er bietet auch Lösungen für häufige Probleme bezüglich der Funktionalität oder der Konfiguration von Protokollwarnungen. 

Der Begriff *Protokollwarnungen* beschreibt Warnungen, die basierend auf einer Protokollabfrage in einem [Azure Log Analytics-Workspace](../learn/tutorial-viewdata.md) oder in [Azure Application Insights](../../azure-monitor/app/analytics.md) ausgelöst werden. Erfahren Sie mehr über Funktionen, Terminologie und Typen unter [Protokollwarnungen in Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> In diesem Artikel werden keine Fälle berücksichtigt, in denen das Azure-Portal eine ausgelöste Warnungsregel anzeigt und eine Benachrichtigung nicht durch eine zugeordnete Aktionsgruppe erfolgt. Weitere Informationen zu solchen Fällen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Protokollwarnung wurde nicht ausgelöst.

Im Folgenden finden Sie einige häufige Gründe, warum ein Status einer konfigurierten [Protokollwarnungsregel in Azure Monitor](../platform/alerts-log.md) nicht wie erwartet [als *Ausgelöst* angezeigt wird](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Datenerfassungszeit für Protokolle

Eine Protokollwarnung führt Ihre Abfrage in regelmäßigen Abständen basierend auf [Log Analytics](../learn/tutorial-viewdata.md) oder [Application Insights](../../azure-monitor/app/analytics.md) aus. Da Azure Monitor viele Terabyte an Daten von Tausenden Kunden und aus verschiedenen Quellen auf der ganzen Welt verarbeitet, ist der Dienst anfällig für unterschiedliche zeitliche Verzögerungen. Weitere Informationen finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](../platform/data-ingestion-time.md).

Um Verzögerungen zu verringern, wartet das System und wiederholt die Warnungsabfrage mehrere Male, falls die benötigten Daten noch nicht erfasst wurden. Für das System ist eine exponentiell zunehmende Wartezeit festgelegt. Die Protokollwarnung wird erst ausgelöst, nachdem die Daten verfügbar sind, sodass die Verzögerung möglicherweise auf die langsame Erfassung der Protokolldaten zurückzuführen ist. 

### <a name="incorrect-time-period-configured"></a>Falscher Zeitraum konfiguriert

Wie im Artikel zur [Terminologie für Protokollwarnungen](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types) beschrieben, steht der in der Konfiguration angegebene Zeitraum für den Zeitbereich der Abfrage. Die Abfrage gibt nur Datensätze zurück, die innerhalb dieses Bereichs erstellt wurden. 

Der Zeitraum schränkt die Daten ein, die für eine Protokollabfrage abgerufen werden, um Missbrauch zu verhindern, und alle in einer Protokollabfrage verwendeten Zeitbefehle (z. B. **ago**) werden umgangen. Wenn der Zeitraum beispielsweise auf 60 Minuten festgelegt ist und die Abfrage um 13:15 Uhr ausgeführt wird, werden nur Datensätze, die zwischen 12:15 und 13:15 Uhr erstellt wurden, für die Protokollabfrage verwendet. Wenn die Protokollabfrage einen Zeitbefehl wie **ago (1d)** verwendet, nutzt die Abfrage nach wie vor nur Daten zwischen 12:15 und 13:15 Uhr, da der Zeitraum auf dieses Intervall festgelegt ist.

Überprüfen Sie, ob der Zeitraum in der Konfiguration mir Ihrer Abfrage übereinstimmt. Wird für die Protokollabfrage aus dem zuvor gezeigten Beispiel **ago (1d)** mit grünem Marker verwendet, sollte der Zeitraum auf 24 Stunden oder 1,440 Minuten (in Rot dargestellt) festgelegt werden. Diese Einstellung stellt sicher, dass die Abfrage wie geplant ausgeführt wird.

![Zeitraum](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Option „Warnungen unterdrücken“ ist festgelegt.

Wie in Schritt 8 des Artikels über das [Erstellen einer Protokollwarnungsregel im Azure-Portal](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) beschrieben, bieten Protokollwarnungen die Option **Warnungen unterdrücken**, um Auslöse- und Benachrichtigungsaktionen für einen konfigurierten Zeitraum zu unterdrücken. Infolgedessen könnten Sie den Eindruck erhalten, dass eine Warnung nicht ausgelöst wurde. Tatsächlich wurde sie ausgelöst, aber unterdrückt.  

![Suppress alerts (Warnungen unterdrücken)](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Warnungsregel für metrische Maßeinheit ist falsch.

*Protokollwarnungen vom Typ „Metrische Maßeinheit“* sind ein Untertyp von Protokollwarnungen, die spezielle Funktionen und eine eingeschränkte Warnungsabfragesyntax aufweisen. Eine Regel für eine Protokollwarnung vom Typ „Metrische Maßeinheit“ erfordert, dass die Abfrageausgabe eine metrische Zeitreihe ist. Das bedeutet, dass die Ausgabe eine Tabelle mit unterschiedlichen, gleich großen Zeiträumen und entsprechenden aggregierten Werten ist. 

Sie können in die Tabelle neben **AggregatedValue** zusätzliche Variablen aufnehmen. Diese Variablen können zum Sortieren der Tabelle verwendet werden. 

Beispiel: Eine Regel für eine Protokollwarnung vom Typ „Metrische Maßeinheit“ wurde folgendermaßen konfiguriert:

- Abfrage von `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Zeitraum: 6 Stunden
- Schwellenwert: 50
- Warnungslogik: drei aufeinanderfolgende Sicherheitsverletzungen
- **Aggregate Upon**: **$table**

Da der Befehl **summarize … by** umfasst und zwei Variablen (**timestamp** und **$table**) angibt, wählt das System $ **$table** für **Aggregate Upon** aus. Das System sortiert die Ergebnistabelle nach dem Feld **$table**, wie im folgenden Screenshot gezeigt. Dann betrachtet es die zahlreichen **AggregatedValue**-Instanzen für die einzelnen Tabellentypen (wie **availabilityResults**), um zu prüfen, ob es drei oder mehr aufeinanderfolgende Sicherheitsverletzungen gab.

![Abfrageausführung für „Metrische Maßeinheit“ mit mehreren Werten](media/alert-log-troubleshoot/LogMMQuery.png)

Da **Aggregate Upon** für **$table** definiert ist, werden die Daten in einer **$table**-Spalte sortiert (rot markiert). Dann gruppieren und suchen wir nach Typen des Felds **Aggregate Upon**. 

Beispielsweise werden für **$table** die Werte für **availabilityResults** als ein Plot bzw. eine Entität betrachtet (orange markiert). In diesem Plot bzw. dieser Entität des Werts überprüft der Warnungsdienst drei aufeinanderfolgende Verletzungen (grün markiert). Die Verletzungen lösen eine Warnung für den Tabellenwert **availabilityResults** aus. 

Wenn drei aufeinanderfolgende Verletzungen für einen anderen Wert von **$table** auftreten, wird ebenfalls eine weitere Warnbenachrichtigung für dieselbe Situation ausgelöst. Der Warnungsdienst sortiert die Werte in einem Plot bzw. in einer Entität (orange markiert) automatisch nach Zeit.

Nehmen Sie jetzt an, dass die Regel für die Protokollwarnung vom Typ „Metrische Maßeinheit“ geändert wurde und die Abfrage `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` war. Der Rest der Konfiguration blieb unverändert, einschließlich der Warnungslogik für drei aufeinanderfolgende Verletzungen. Die Option **Aggregate Upon** ist in diesem Fall standardmäßig **timestamp**. In der Abfrage für **summarize ... by** wird nur ein Wert angegeben. (d. h. **timestamp**). Wie im vorherigen Beispiel würde die Ausgabe am Ende der Ausführung wie folgt aussehen.

   ![Abfrageausführung für „Metrische Maßeinheit“ mit einzelnem Wert](media/alert-log-troubleshoot/LogMMtimestamp.png)

Da **Aggregate Upon** für **timestamp** definiert ist, werden die Daten in der **timestamp**-Spalte sortiert (rot markiert). Dann gruppieren wir nach **timestamp**. Die Werte für `2018-10-17T06:00:00Z` werden z. B. als ein Plot bzw. eine Entität betrachtet (orange markiert). In diesem Plot bzw. dieser Entität des Werts findet der Warnungsdienst keine drei aufeinanderfolgenden Sicherheitsverletzungen (da für jeden **timestamp**-Wert nur ein Eintrag vorhanden ist). Die Warnung wird also nie ausgelöst. In einem solchen Fall muss der Benutzer eine der folgenden Aktionen ausführen:

- Hinzufügen einer Dummyvariable oder einer vorhandenen Variable (z. B. **$table**) für eine richtige Sortierung über das Feld **Aggregate Upon**
- Neukonfigurieren der Warnungsregel für die Verwendung von Warnungslogik basierend auf **Sicherheitsverletzungen insgesamt**

## <a name="log-alert-fired-unnecessarily"></a>Protokollwarnung wird unnötigerweise ausgelöst.

Eine konfigurierte [Protokollwarnungsregel in Azure Monitor](../platform/alerts-log.md) kann unerwartet ausgelöst werden, wenn Sie sie in [Azure-Warnungen](../platform/alerts-managing-alert-states.md) anzeigen. In den folgenden Abschnitten werden einige häufige Gründe beschrieben.

### <a name="alert-triggered-by-partial-data"></a>Warnung wird durch unvollständige Daten ausgelöst.

Log Analytics und Application Insights unterliegen Erfassungsverzögerungen und Verarbeitungen. Wenn Sie eine Protokollwarnungsabfrage ausführen, werden Sie möglicherweise feststellen, dass keine oder nur einige Daten verfügbar sind. Weitere Informationen finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](../platform/data-ingestion-time.md).

Abhängig von der Konfiguration der Warnungsregel kann es zu einer fehlerhaften Auslösung kommen, wenn zum Zeitpunkt der Warnungsausführung in den Protokollen keine Daten oder nur ein Teil der Daten vorhanden sind. In solchen Fällen ist es empfehlenswert, die Warnungsabfrage oder -konfiguration zu ändern. 

Beispiel: Wenn die Protokollwarnungsregel so konfiguriert ist, dass sie während einer Analyseabfrage bei weniger als fünf Ergebnissen ausgelöst wird, dann wird die Warnung bereits bei keinen Daten (null Datensätze) oder Teilergebnissen (ein Datensatz) ausgelöst. Aber nach der Verzögerung bei der Datenerfassung, kann dieselbe Abfrage mit vollständigen Daten jedoch zu einem Ergebnis von zehn Datensätzen führen.

### <a name="alert-query-output-is-misunderstood"></a>Die Ausgabe der Warnungsabfrage wurde falsch verstanden.

Die Logik für Protokollwarnungen in einer Analyseabfrage stellen Sie bereit. Die Analyseabfrage kann verschiedene Big-Data-Funktionen und mathematische Funktionen verwenden. Der Warnungsdienst führt Ihre Abfrage in Intervallen aus, die mit Daten für einen bestimmten Zeitraum angegeben sind. Der Warnungsdienst nimmt je nach Warnungstyp geringfügige Änderungen an der Abfrage vor. Sie können diese Änderung im Abschnitt **Auszuführende Abfrage** auf dem Bildschirm **Signallogik konfigurieren** anzeigen:

![Auszuführende Abfrage](media/alert-log-troubleshoot/LogAlertPreview.png)

Das Feld **Auszuführende Abfrage** enthält, was der Warnungsprotokolldienst ausführt. Wenn Sie vor der Erstellung der eigentlichen Warnung erfahren möchten, wie die Ausgabe der Warnungsabfrage aussehen könnte, können Sie die angegebene Abfrage und die Zeitspanne über das [Analytics-Portal](../log-query/portals.md) oder die [Textanalyse-API](https://docs.microsoft.com/rest/api/loganalytics/) ausführen.

## <a name="log-alert-was-disabled"></a>Protokollwarnung wurde deaktiviert

In den folgenden Abschnitten werden einige Gründe aufgeführt, warum Azure Monitor die [Protokollwarnungsregel](../platform/alerts-log.md) deaktivieren könnte.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Die Ressource, für die die Warnung erstellt wurde, ist nicht mehr vorhanden

Die in Azure Monitor erstellten Protokollwarnungsregeln beziehen sich auf eine bestimmte Ressource wie einen Azure Log Analytics-Arbeitsbereich, eine Azure Application Insights-App und eine Azure-Ressource. Der Protokollwarnungsdienst führt dann die Analyseabfrage aus, die in der Regel für das angegebene Ziel bereitgestellt wurde. Nach der Regelerstellung setzen die Benutzer den Vorgang oft fort, indem sie das Ziel der Protokollwarnungsregel aus Azure löschen oder innerhalb von Azure verschieben. Da das Ziel der Warnungsregel nicht mehr gültig ist, schlägt die Ausführung der Regel fehl.

In solchen Fällen deaktiviert Azure Monitor die Protokollwarnung und stellt sicher, dass Ihnen nicht unnötig eine Rechnung gestellt wird, wenn die Regel nicht kontinuierlich über einen längeren Zeitraum (z. B. eine Woche) ausgeführt werden kann. Sie können den genauen Zeitpunkt, zu dem Azure Monitor die Protokollwarnung deaktiviert hat, über [Azure-Aktivitätsprotokoll](../../azure-resource-manager/resource-group-audit.md) ermitteln. Im Azure-Aktivitätsprotokoll wird ein Ereignis hinzugefügt, wenn Azure Monitor die Protokollwarnungsregel deaktiviert.

Das folgende Beispielereignis im Azure-Aktivitätsprotokoll bezieht sich auf eine Warnungsregel, die aufgrund eines kontinuierlichen Fehlers deaktiviert wurde.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Die in der Protokollwarnung verwendete Abfrage ist ungültig

In jeder Protokollwarnungsregel, die in Azure Monitor bei dessen Konfiguration erstellt wurde, muss eine Analyseabfrage angegeben werden, die vom Warnungsdienst regelmäßig auszuführen ist. Zum Zeitpunkt der Erstellung oder Aktualisierung der Protokollwarnungsregel weist die Analyseabfrage möglicherweise eine korrekte Syntax auf. Doch manchmal kann die in der Warnungsregel bereitgestellte Abfrage im Laufe der Zeit Syntaxprobleme entwickeln und dazu führen, dass bei der Regelausführung Fehler auftreten. Einige häufige Gründe, aus denen eine Analyseabfrage in einer Protokollwarnungsregel Fehler entwickeln kann, sind die folgenden:

- Die Abfrage wird für die [Ausführung in mehreren Ressourcen](../log-query/cross-workspace-query.md) geschrieben. Und mindestens eine angegebene Ressource ist nicht mehr vorhanden.
- Es gab keinen Datenfluss zur Analyseplattform. Die [Abfrageausführung liefert einen Fehler](https://dev.loganalytics.io/documentation/Using-the-API/Errors), da keine Daten für die bereitgestellte Abfrage vorhanden sind.
- Änderungen an der [Abfragesprache](https://docs.microsoft.com/azure/kusto/query/) umfassen ein überarbeitetes Format für Befehle und Funktionen. Daher ist die zuvor in einer Warnungsregel angegebene Abfrage nicht mehr gültig.

[Azure Advisor](../../advisor/advisor-overview.md) warnt Sie vor diesem Verhalten. Eine Empfehlung wird für die spezifische Protokollwarnungsregel in Azure Advisor hinzugefügt, und zwar unter der Kategorie „Hochverfügbarkeit“ mit mittlerer Auswirkung und einer Beschreibung wie „Protokollwarnungsregel zum Sicherstellen der Überwachung reparieren“. Wenn eine Warnungsabfrage in der Protokollwarnungsregel nicht korrigiert wird, nachdem Azure Advisor sieben Tage lang eine Empfehlung bereitgestellt hat, deaktiviert Azure Monitor die Protokollwarnung und stellt sicher, dass Ihnen keine unnötige Rechnung gestellt wird, wenn die Regel über einen längeren Zeitraum (z. B. eine Woche) nicht kontinuierlich ausgeführt werden kann.

Sie können den genauen Zeitpunkt ermitteln, zu dem Azure Monitor die Protokollwarnungsregel deaktiviert hat, indem Sie im [Azure-Aktivitätsprotokoll](../../azure-resource-manager/resource-group-audit.md) nach einem Ereignis suchen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Protokollwarnungen in Azure](../platform/alerts-unified-log.md).
- Erfahren Sie mehr über [Application Insights](../../azure-monitor/app/analytics.md).
- Weitere Informationen zum [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md).
