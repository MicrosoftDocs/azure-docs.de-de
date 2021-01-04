---
title: Protokollwarnungen in Azure Monitor
description: E-Mails, Benachrichtigungen oder Automatisierung werden ausgelöst und URLs (Webhooks) von Websites aufgerufen, wenn die von Ihnen angegebene Bedingung für Protokollabfragen erfüllt ist.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 9f8004b41e8048dfc97fb61bb67a634963c0c575
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317553"
---
# <a name="log-alerts-in-azure-monitor"></a>Protokollwarnungen in Azure Monitor

## <a name="overview"></a>Übersicht

Protokollwarnungen sind einer der Warnungstypen, die in [Azure Alerts](./alerts-overview.md) unterstützt werden. Mithilfe von Protokollwarnungen können Benutzer eine [Log Analytics](../log-query/log-analytics-tutorial.md)-Abfrage verwenden, um Ressourcenprotokolle mit einer bestimmten Häufigkeit auszuwerten und basierend auf den Ergebnissen eine Warnung auszulösen. Durch Regeln können über [Aktionsgruppen](./action-groups.md) einzelne oder mehrere Aktionen ausgelöst werden.

> [!NOTE]
> Protokolldaten aus einem [Log Analytics-Arbeitsbereich](../log-query/log-analytics-tutorial.md) können an den Azure Monitor-Metrikspeicher gesendet werden. Metrikwarnungen weisen ein [anderes Verhalten](alerts-metric-overview.md) auf. Je nach den Daten, mit denen Sie arbeiten, kann dies wünschenswert sein. Informationen zu den Protokollen, die Sie an Metriken weiterleiten können, und der zugehörigen Vorgehensweise finden Sie unter [Metrikwarnungen für Protokolle](alerts-metric-logs.md).

> [!NOTE]
> Für die API-Version `2020-05-01-preview` und ressourcenbezogene Protokollwarnungen fallen aktuell keine zusätzlichen Gebühren an.  Die Preise für Previewfunktionen werden später bekannt gegeben, und vor Abrechnungsbeginn erhalten Sie eine entsprechende Benachrichtigung. Falls Sie sich dafür entscheiden, die neue API-Version und ressourcenbezogene Protokollwarnungen über den Benachrichtigungszeitraum hinaus zu verwenden, wird Ihnen der entsprechende Tarif in Rechnung gestellt.

## <a name="prerequisites"></a>Voraussetzungen

Bei Protokollwarnungen werden Abfragen für Log Analytics-Daten ausgeführt. Zunächst sollten Sie [Protokolldaten erfassen](resource-logs.md) und für die Protokolldaten abfragen, ob Probleme vorhanden sind. Unter [Gespeicherte Abfragen in Azure Monitor Log Analytics](../log-query/example-queries.md) können Sie sich anhand von Beispielen mit den Ermittlungsoptionen vertraut machen. Alternativ können Sie [mit dem Schreiben einer eigenen Abfrage beginnen](../log-query/log-analytics-tutorial.md).

[Mitwirkender der Azure-Überwachung](./roles-permissions-security.md) ist eine häufig verwendete Rolle, die zum Erstellen, Ändern und Aktualisieren von Protokollwarnungen benötigt wird. Rechte zum Zugreifen und zum Ausführen von Abfragen für die Ressourcenprotokolle werden ebenfalls benötigt. Bei einem Teilzugriff auf Ressourcenprotokolle kann es zu Abfragefehlern kommen, oder es wird nur ein Teil der Ergebnisse zurückgegeben. Informieren Sie sich über das [Konfigurieren von Protokollwarnungen in Azure](./alerts-log.md).

> [!NOTE]
> Früher wurden Protokollwarnungen für Log Analytics mit der [Log Analytics-Legacy-API für Warnungen](api-alerts.md) verwaltet. [Informieren Sie sich über den Wechsel zur aktuellen ScheduledQueryRules-API](alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Definition der Abfrageauswertung

Die Definition der Bedingung für Protokollsuchregeln beginnt mit:

- Welche Abfrage soll ausgeführt werden?
- Wie sollen die Ergebnisse verwendet werden?

In den folgenden Abschnitten werden die verschiedenen Parameter beschrieben, die Sie zum Festlegen der obigen Logik verwenden können.

### <a name="log-query"></a>Protokollabfrage
Die [Log Analytics](../log-query/log-analytics-tutorial.md)-Abfrage, die zum Auswerten der Regel verwendet wird. Mit den von dieser Abfrage zurückgegebenen Ergebnissen wird ermittelt, ob eine Warnung ausgelöst werden muss. Die Abfrage kann auf den folgenden Bereich festgelegt werden:

- Eine bestimmte Ressource, z. B. einen virtuellen Computer.
- Eine umfassende Ressource, z. B. ein Abonnement oder eine Ressourcengruppe.
- Mehrere Ressourcen per [ressourcenübergreifender Abfrage](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> Für Warnungsabfragen gelten Einschränkungen, um eine optimale Leistung und die Relevanz der Ergebnisse sicherzustellen. [Hier erhalten Sie weitere Informationen](./alerts-log-query.md).

> [!IMPORTANT]
> Ressourcenbezogene und [-übergreifende Abfragen](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) werden nur unterstützt, wenn die aktuelle scheduledQueryRules-API verwendet wird. Bei Verwendung der älteren [Log Analytics-Warnungs-API](api-alerts.md) müssen Sie auf die neue API umstellen. Weitere Informationen zur Umstellung finden Sie [hier](./alerts-log-api-switch.md).

#### <a name="query-time-range"></a>Abfragezeitbereich

Der Zeitbereich wird in der Definition für die Regelbedingung festgelegt. Bei Arbeitsbereichen und in Application Insights wird dies als **Periode** bezeichnet. Für alle anderen Ressourcentypen lautet die Bezeichnung **Zeitbereich für Außerkraftsetzungsabfrage**.

Wie auch bei der Protokollanalyse werden die Abfragedaten durch den Zeitbereich auf den angegebenen Bereich begrenzt. Der Zeitbereich gilt auch, wenn in der Abfrage der Befehl **ago** (vor) verwendet wird.

Beispielsweise führt eine Abfrage eine Überprüfung für einen Zeitraum von 60 Minuten durch, wenn ein Zeitbereich von 60 Minuten angegeben ist. Dies gilt auch, wenn der Text **ago(1d)** enthält. Die Filterung für den Zeitbereich und den Abfragezeitraum muss übereinstimmen. Im Fallbeispiel führt das Ändern von **Periode** / **Zeitbereich für Außerkraftsetzungsabfrage** in „1 Tag“ zum erwarteten Ergebnis.

### <a name="measure"></a>"Measure"

Bei Protokollwarnungen werden Protokolle in numerische Werte umgewandelt, die ausgewertet werden können. Sie können zwei verschiedene Dinge messen:

#### <a name="count-of-the-results-table-rows"></a>Anzahl von Zeilen in der Ergebnistabelle

Die Anzahl von Ergebnissen ist das Standardmeasure. Es ist ideal für die Verwendung von Ereignissen geeignet, z. B. Windows-Ereignisprotokolle, Syslog und Anwendungsausnahmen. Wird ausgelöst, wenn im ausgewerteten Zeitfenster Protokollaufzeichnungen erfolgen oder nicht erfolgen.

Protokollwarnungen funktionieren am besten, wenn Sie versuchen, Daten im Protokoll zu erkennen. Sie funktionieren weniger gut, wenn Sie versuchen, in den Protokollen das Fehlen von Daten zu erkennen. Ein Beispiel hierfür sind Warnungen zum VM-Heartbeat.

Für Arbeitsbereiche und Application Insights wird hierfür unter **Basierend auf** die Option **Anzahl von Ergebnissen** ausgewählt. Für alle anderen Ressourcentypen wird unter **Measure** die Option **Tabellenzeilen** ausgewählt.

> [!NOTE]
> Da es sich bei Protokollen um halbstrukturierte Daten handelt, ist die Latenz von Haus aus höher als bei Metriken. Unter Umständen kommt es zu fehlerhaften Auslösungen, wenn Sie versuchen, in den Protokollen das Fehlen von Daten zu erkennen. Daher sollten Sie erwägen, [Metrikwarnungen](alerts-metric-overview.md) zu verwenden. Sie können Daten aus Protokollen an den Metrikspeicher senden, indem Sie [Metrikwarnungen für Protokolle](alerts-metric-logs.md) verwenden.

##### <a name="example-of-results-table-rows-count-use-case"></a>Beispiel: Anwendungsfall mit Zeilenanzahl in Ergebnistabelle

Sie möchten wissen, wann Ihre Anwendung mit dem Fehlercode 500 (Interner Serverfehler) reagiert hat. Dazu erstellen Sie eine Warnungsregel mit den folgenden Details:

- **Abfrage:** 

```Kusto
requests
| where resultCode == "500"
```

- **Zeitraum/Aggregationsgranularität**: 15 Minuten
- **Warnungshäufigkeit:** 15 Minuten
- **Schwellenwert:** Größer als 0

Die Warnungsregeln führen dann eine Überwachung auf Anforderungen durch, die mit dem Fehlercode 500 enden. Die Abfrage wird alle 15 Minuten für die letzten 15 Minuten ausgeführt. Sobald auch nur ein Datensatz gefunden wird, werden die Warnung und die konfigurierten Aktionen ausgelöst.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Berechnung des Measures basierend auf einer numerischen Spalte (z. B. CPU-Indikatorwert)

Für Arbeitsbereiche und Application Insights wird hierfür unter **Basierend auf** die Option **Metrische Maßeinheit** ausgewählt. Für alle anderen Ressourcentypen wird dies als **Measure** mit der Auswahl eines beliebigen Namens einer Zahlenspalte bezeichnet.

### <a name="aggregation-type"></a>Aggregationstyp

Die Berechnung, die für mehrere Datensätze ausgeführt wird, um sie unter einem numerischen Wert zu aggregieren. Beispiel:
- **Count** (Anzahl): Gibt die Anzahl von Datensätzen in der Abfrage zurück.
- **Average** (Durchschnitt): Gibt den Durchschnittswert der definierten Measurespalte [**Aggregationsgranularität**](#aggregation-granularity) zurück.

Bei Arbeitsbereichen und in Application Insights wird nur der Measuretyp **Metrische Maßeinheit** unterstützt. Das Abfrageergebnis muss eine Spalte mit dem Namen „AggregatedValue“ enthalten, in der ein numerischer Wert nach einer benutzerdefinierten Aggregation angegeben ist. Bei allen anderen Ressourcentypen wird der **Aggregationstyp** über das entsprechende Feld ausgewählt.

### <a name="aggregation-granularity"></a>Aggregationsgranularität

Bestimmt das Intervall, das zum Aggregieren von mehreren Datensätzen in einem numerischen Wert verwendet wird. Wenn Sie beispielsweise **5 Minuten** angegeben haben, werden die Datensätze in 5-Minuten-Intervallen gruppiert, indem der angegebene **Aggregationstyp** verwendet wird.

Bei Arbeitsbereichen und in Application Insights wird nur der Measuretyp **Metrische Maßeinheit** unterstützt. Das Abfrageergebnis muss [bin()](/azure/kusto/query/binfunction) enthalten. Hiermit wird das Intervall in den Abfrageergebnissen festgelegt. Bei allen anderen Ressourcentypen wird das Feld, mit dem diese Einstellung gesteuert wird, als **Aggregationsgranularität** bezeichnet.

> [!NOTE]
> Da [bin()](/azure/kusto/query/binfunction) zu uneinheitlichen Zeitintervallen führen kann, wird die Funktion [bin()](/azure/kusto/query/binfunction) zur Laufzeit automatisch durch den Warnungsdienst in die Funktion [bin_at()](/azure/kusto/query/binatfunction) mit der richtigen Zeit konvertiert, um Ergebnisse mit Fixpunkt sicherzustellen.

### <a name="split-by-alert-dimensions"></a>Aufteilen nach Warnungsdimensionen

Teilen Sie Warnungen nach Zahlen- oder Zeichenfolgenspalten in separate Warnungen auf, indem Sie eine Gruppierung nach eindeutigen Kombinationen durchführen. Beim Erstellen von ressourcenbezogenen Warnungen im großen Stil (Abonnement- oder Ressourcengruppenbereich) können Sie eine Aufteilung nach der Spalte mit der Azure-Ressourcen-ID vornehmen. Beim Aufteilen nach der Spalte mit der Azure-Ressourcen-ID ändert sich das Ziel der Warnung in die angegebene Ressource.

Bei Arbeitsbereichen und in Application Insights wird nur der Measuretyp **Metrische Maßeinheit** unterstützt. Das Feld hat die Bezeichnung **Aggregieren nach**. Es ist auf drei Spalten beschränkt. Wenn eine Abfrage mehr als drei Gruppierungen nach Spalten enthält, kann dies zu unerwarteten Ergebnissen führen. Bei allen anderen Ressourcentypen wird dies im Abschnitt **Split by dimensions** (Nach Dimensionen aufteilen) der Bedingung konfiguriert (auf sechs Aufteilungen beschränkt).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Beispiel: Aufteilung nach Warnungsdimensionen

Es kann beispielsweise sein, dass Sie Fehler für mehrere virtuelle Computer überwachen möchten, auf denen Ihre Website bzw. App in einer bestimmten Ressourcengruppe ausgeführt wird. Verwenden Sie hierfür wie folgt eine Protokollwarnungsregel:

- **Abfrage:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Bei Verwendung von Arbeitsbereichen und Application Insights mit einer Warnungslogik vom Typ **Metrische Maßeinheit** muss diese Zeile dem Abfragetext hinzugefügt werden:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Spalte mit Ressourcen-ID:** _ResourceId (Die Aufteilung nach der Spalte mit der Ressourcen-ID in Warnungsregeln ist derzeit nur für Abonnements und Ressourcengruppen verfügbar.)
- **Dimensionen/Aggregieren nach:**
  - Computer = VM1, VM2 (Die Filterung von Werten in Warnungsregeldefinitionen ist für Arbeitsbereiche und Application Insights derzeit nicht verfügbar. Filtern Sie stattdessen im Abfragetext.)
- **Zeitraum/Aggregationsgranularität**: 15 Minuten
- **Warnungshäufigkeit:** 15 Minuten
- **Schwellenwert:** Größer als 0

Mit dieser Regel wird überwacht, ob für einen virtuellen Computer in den letzten 15 Minuten Fehlerereignisse aufgetreten sind. Jeder virtuelle Computer wird separat überwacht, und es werden jeweils individuelle Aktionen ausgelöst.

> [!NOTE]
> Die Aufteilung nach Warnungsdimensionen ist nur für die aktuelle scheduledQueryRules-API verfügbar. Bei Verwendung der älteren [Log Analytics-Warnungs-API](api-alerts.md) müssen Sie auf die neue API umstellen. Weitere Informationen zur Umstellung finden Sie [hier](./alerts-log-api-switch.md). Ressourcenbezogene Warnungen im großen Stil werden erst ab der API-Version `2020-05-01-preview` unterstützt.

## <a name="alert-logic-definition"></a>Definition der Warnungslogik

Nachdem Sie die auszuführende Abfrage und die Auswertung der Ergebnisse definiert haben, müssen Sie die Warnungslogik und die Bedingungen für die Auslösung der Aktionen definieren. In den folgenden Abschnitten werden die verschiedenen Parameter beschrieben, die Sie verwenden können:

### <a name="threshold-and-operator"></a>Schwellenwert und Operator

Die Abfrageergebnisse werden in eine Zahl transformiert, die mit dem Schwellenwert und Operator verglichen wird.

### <a name="frequency"></a>Häufigkeit

Das Intervall für die Ausführung der Abfrage. Hierfür kann ein Zeitraum festgelegt werden, der 5 Minuten bis einen Tag lang sein kann. Der Zeitraum muss kleiner oder gleich dem [Abfragezeitbereich](#query-time-range) sein, damit keine Protokolldatensätze fehlen.

Beispiel: Sie legen den Zeitraum auf 30 Minuten und die Häufigkeit auf 1 Stunde fest.  Wenn die Abfrage um 00:00 Uhr ausgeführt wird, gibt sie die Datensätze für den Zeitraum zwischen 23:30 und 00:00 Uhr zurück. Wenn die Abfrage dann das nächste Mal um 01:00 Uhr ausgeführt wird, gibt sie die Datensätze für den Zeitraum zwischen 00:30 und 01:00 Uhr zurück. Alle Datensätze, die zwischen 00:00 und 00:30 erstellt werden, werden also nicht ausgewertet.

### <a name="number-of-violations-to-trigger-alert"></a>Auslösung der Warnung aufgrund der Anzahl von Verstößen

Sie können den Zeitraum für die Warnungsauswertung und die Anzahl von Fehlern angeben, die erforderlich sind, damit eine Warnung ausgelöst wird. Dies erleichtert es Ihnen, einen Auswirkungszeitpunkt für die Auslösung einer Warnung zu definieren. 

Wenn für die [**Aggregationsgranularität**](#aggregation-granularity) Ihrer Regel beispielsweise „5 Minuten“ definiert ist, können Sie festlegen, dass eine Warnung nur dann ausgelöst wird, wenn innerhalb der letzten Stunde drei Fehler (15 Minuten) aufgetreten sind. Diese Einstellung wird durch die Geschäftsrichtlinie Ihrer Anwendung definiert.

## <a name="state-and-resolving-alerts"></a>Zustand und Beseitigung von Warnungen

Protokollwarnungen sind zustandslos. Warnungen werden jedes Mal ausgelöst, wenn die Bedingung erfüllt ist. Dies gilt auch, wenn sie bereits zuvor ausgelöst wurden. Ausgelöste Warnungen werden nicht beseitigt. Sie können [die Warnung als geschlossen markieren](alerts-managing-alert-states.md). Darüber hinaus können Sie Aktionen auch stummschalten, um zu verhindern, dass sie während eines bestimmten Zeitraums nach der Auslösung einer Warnungsregel ausgelöst werden.

Bei Arbeitsbereichen und in Application Insights wird dies als **Warnungen unterdrücken** bezeichnet. Bei allen anderen Ressourcentypen lautet die Bezeichnung **Aktionen unterdrücken**. 

Sehen Sie sich dieses Beispiel für eine Warnungsauswertung an:

| Time    | Auswertung der Protokollbedingung | Ergebnis 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | Warnung wird nicht ausgelöst. Es werden keine Aktionen aufgerufen.
| 00:10 | TRUE  | Die Warnung wird ausgelöst, und Aktionsgruppen werden aufgerufen. Neuer Warnungsstatus lautet AKTIV.
| 00:15 | TRUE  | Die Warnung wird ausgelöst, und Aktionsgruppen werden aufgerufen. Neuer Warnungsstatus lautet AKTIV.
| 00:20 | FALSE | Warnung wird nicht ausgelöst. Es werden keine Aktionen aufgerufen. Status für vorherige Warnungen lautet weiter AKTIV.

## <a name="pricing-and-billing-of-log-alerts"></a>Preise und Abrechnung von Protokollwarnungen

Die Preisinformationen finden Sie auf der Seite [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/). Protokollwarnungen werden unter dem Ressourcenanbieter `microsoft.insights/scheduledqueryrules` mit folgenden Angaben aufgeführt:

- Protokollwarnungen für Application Insights werden mit dem genauen Namen der Ressource zusammen mit der Ressourcengruppe und den Warnungseigenschaften angezeigt.
- Protokollwarnungen in Log Analytics werden mit dem exakten Namen der Ressource zusammen mit der Ressourcengruppe und den Warnungseigenschaften angezeigt, wenn sie unter Verwendung der [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules) erstellt werden.
- Bei Protokollwarnungen, die mit der [Log Analytics-Legacy-API](./api-alerts.md) erstellt werden, handelt es sich nicht um nachverfolgte [Azure-Ressourcen](../../azure-resource-manager/management/overview.md), und sie verfügen nicht über erzwungene eindeutige Ressourcennamen. Diese Warnungen werden unter `microsoft.insights/scheduledqueryrules` weiterhin als ausgeblendete Ressourcen erstellt und verfügen über die folgende Struktur für die Ressourcenbenennung: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. Protokollwarnungen in der Legacy-API werden mit dem obigen ausgeblendeten Ressourcennamen sowie der Ressourcengruppe und den Warnungseigenschaften angezeigt.

> [!NOTE]
> Nicht unterstützte Zeichen für Ressourcen, z. B. `<, >, %, &, \, ?, /`, werden in den ausgeblendeten Ressourcennamen durch `_` ersetzt. Dies spiegelt sich auch in den Abrechnungsinformationen wider.

> [!NOTE]
> Protokollwarnungen für Log Analytics wurden bisher über die ältere [Log Analytics-Warnungs-API](api-alerts.md) und über Legacyvorlagen von [gespeicherten Log Analytics-Suchen und -Warnungen](../insights/solutions.md) verwaltet. [Informieren Sie sich über den Wechsel zur aktuellen ScheduledQueryRules-API](alerts-log-api-switch.md). Sie sollten die gesamte Verwaltung von Warnungsregeln mit der [Log Analytics-Legacy-API](api-alerts.md) durchführen, bis Sie sich für die Umstellung entscheiden und die ausgeblendeten Ressourcen nicht mehr verwenden können.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Erstellen von Protokollwarnungen in Azure](./alerts-log.md).
* Machen Sie sich mit [Webhooks in Protokollwarnungen in Azure](alerts-log-webhook.md) vertraut.
* Erfahren Sie mehr über [Azure-Warnungen](./alerts-overview.md).
* Erfahren Sie mehr über [Log Analytics](../log-query/log-query-overview.md).
