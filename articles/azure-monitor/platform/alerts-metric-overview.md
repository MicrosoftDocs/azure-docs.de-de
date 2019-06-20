---
title: Erhalten Sie Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor.
description: Verschaffen Sie sich einen Überblick darüber, was Sie mit Metrikwarnungen erreichen können und wie sie in Azure Monitor funktionieren.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 6138a9ff6bb6d34b09c49fa7b5dbb67cbf5eb1b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244903"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor

Die Metrikwarnungen in Azure Monitor ergänzen die mehrdimensionalen Metriken. Diese Metriken können [Plattformmetriken](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [benutzerdefinierte Metriken](../../azure-monitor/platform/metrics-custom-overview.md), [gängige Protokolle von Azure Monitor, die in Metriken umgewandelt wurden](../../azure-monitor/platform/alerts-metric-logs.md), und Application Insights-Metriken sein. Metrikwarnungen werden in regelmäßigen Abständen ausgewertet, um zu überprüfen, ob die Bedingungen für eine oder mehrere metrische Zeitreihen erfüllt sind, und um Sie darüber zu informieren, wann die Auswertungen erfüllt sind. Metrikwarnungen sind zustandsbehaftet. Sie senden Benachrichtigungen nur dann, wenn sich der Zustand ändert.

## <a name="how-do-metric-alerts-work"></a>Wie funktionieren Metrikwarnungen?

Sie können eine Metrikwarnungsregel definieren, indem Sie eine zu überwachende Zielressource, den Namen der Metrik, den Bedingungstyp (statisch oder dynamisch) und die Bedingung (einen Operator und einen Schwellenwert) sowie eine Aktionsgruppe angeben, die bei Auslösung der Warnregel ausgelöst werden soll. Bedingungstypen wirken sich darauf aus, wie Schwellenwerte festgelegt werden. [Erfahren Sie mehr über den Bedingungstyp „Dynamische Schwellenwerte“ und Empfindlichkeitsoptionen.](alerts-dynamic-thresholds.md)

### <a name="alert-rule-with-static-condition-type"></a>Warnungsregel mit dem statischen Bedingungstyp

Nehmen wir an, Sie haben wie folgt eine einfache statische Metrikwarnung mit Schwellenwert erstellt:

- Zielressource (die zu überwachende Azure-Ressource): myVM
- Metrik: CPU in Prozent
- Bedingungstyp: statischen
- Zeitaggregation (Statistik, die über Rohmetriken geführt wird. Unterstützte Zeitaggregationen sind Minimum, Maximum, Durchschnitt, Gesamtwert, Anzahl): Durchschnitt
- Zeitraum (das zurückliegende Zeitfenster, über das Metrikwerte geprüft werden): Über die letzten 5 Minuten
- Häufigkeit (die Häufigkeit, mit der die Metrikwarnung überprüft werden soll, wenn die Bedingungen erfüllt sind): 1 Minute
- Operator: Größer als
- Schwellenwert: 70

Ab dem Zeitpunkt der Erstellung der Warnungsregel wird der Monitor jede Minute ausgeführt, der dann die Metrikwerte der letzten fünf Minuten betrachtet und überprüft, ob der Durchschnitt dieser Werte 70 übersteigt. Wenn die Bedingung erfüllt ist, d. h. der durchschnittliche Wert für „CPU in Prozent“ überschreitet für die letzten fünf Minuten den Wert 70, löst die Warnungsregel eine aktivierte Benachrichtigung aus. Wenn Sie eine E-Mail- oder Webhook-Aktion in der Aktionsgruppe konfiguriert haben, die der Warnungsregel zugeordnet ist, erhalten Sie für beide eine aktivierte Benachrichtigung.

### <a name="alert-rule-with-dynamic-condition-type"></a>Warnungsregel mit dem dynamischen Bedingungstyp

Nehmen wir an, Sie haben wie folgt eine einfache dynamische Metrikwarnung mit Schwellenwert erstellt:

- Zielressource (die zu überwachende Azure-Ressource): myVM
- Metrik: CPU in Prozent
- Bedingungstyp: Dynamisch
- Zeitaggregation (Statistik, die über Rohmetriken geführt wird. Unterstützte Zeitaggregationen sind Minimum, Maximum, Durchschnitt, Gesamtwert, Anzahl): Durchschnitt
- Zeitraum (das zurückliegende Zeitfenster, über das Metrikwerte geprüft werden): Über die letzten 5 Minuten
- Häufigkeit (die Häufigkeit, mit der die Metrikwarnung überprüft werden soll, wenn die Bedingungen erfüllt sind): 1 Minute
- Operator: Größer als
- Empfindlichkeit: Mittel
- Zurückliegende Zeiträume: 4
- Anzahl von Verstößen: 4

Sobald die Warnungsregel erstellt wurde, ruft der Machine Learning-Algorithmus für dynamische Schwellenwerte verfügbare historische Daten ab, berechnet den besten Schwellenwert für das Verhaltensmuster der Metrikreihe und lernt fortlaufend anhand von neuen Daten, um die Genauigkeit des Schwellenwerts zu verbessern.

Ab der Erstellung der Warnungsregel erfolgt die Prüfung im 1-Minuten-Takt. Dabei werden Metrikwerte aus den letzten 20 Minuten untersucht und in Zeiträume von fünf Minuten zusammengefasst. Darüber hinaus wird überprüft, ob die durchschnittlichen Werte in jedem der vier Zeiträume den erwarteten Schwellenwert überschreiten. Wenn die Bedingung erfüllt ist, dass der durchschnittliche CPU-Prozentsatz in den letzten 20 Minuten (vier Zeiträume von fünf Minuten) viermal vom erwarteten Verhalten abweicht, gibt die Warnungsregel eine aktivierte Benachrichtigung aus. Wenn Sie eine E-Mail- oder Webhook-Aktion in der Aktionsgruppe konfiguriert haben, die der Warnungsregel zugeordnet ist, erhalten Sie für beide eine aktivierte Benachrichtigung.

### <a name="view-and-resolution-of-fired-alerts"></a>Anzeige und Auflösung der ausgelösten Warnungen

Die obigen Beispiele für die Auslösung der Warnungsregel finden Sie auch im Azure-Portal auf dem Blatt **Alle Warnungen**.

Wenn die Verwendung von „myVM“ bei nachfolgenden Prüfungen weiterhin über dem Schwellenwert liegt, wird die Warnungsregel erst nach Auflösen der Bedingungen wieder ausgelöst.

Nach einer gewissen Zeit, wenn die Nutzung von „myVM“ wieder einen normalen Wert erreicht, fällt sie wieder unter den Schwellenwert. Die Warnungsregel überwacht die Bedingung noch zweimal, um eine Benachrichtigung zur aufgelösten Bedingung zu versenden. Die Warnungsregel sendet eine aufgelöste/deaktivierte Nachricht, wenn die Warnungsbedingung drei aufeinanderfolgende Zeiträume lang nicht erfüllt ist, um Störungen im Falle von Fluktuationsbedingungen zu reduzieren.

Da die aufgelöste Benachrichtigung über einen Webhook oder eine E-Mail versendet wird, wird auch der Status der Warnungsinstanz (Monitorzustand) im Azure-Portal auf „aufgelöst“ festgelegt.

### <a name="using-dimensions"></a>Verwenden von Dimensionen

Metrikwarnungen in Azure Monitor unterstützen auch die Überwachung von mehrdimensionalen Wertekombinationen mit einer Regel. Lassen Sie uns anhand eines Beispiels erläutern, warum Sie mehrere Dimensionskombinationen verwenden können.

Angenommen, Sie verfügen über einen App Service-Plan für Ihre Website. Sie möchten die CPU-Auslastung mehrerer Instanzen überwachen, die Ihre Website/App ausführen. Verwenden Sie dazu eine Metrikwarnungsregel wie folgt:

- Zielressource: myAppServicePlan
- Metrik: CPU in Prozent
- Bedingungstyp: statischen
- Dimensionen
  - Instanz = InstanceName1, InstanceName2
- Zeitaggregation: Durchschnitt
- Zeitraum: Über die letzten 5 Minuten
- Häufigkeit: 1 Minute
- Operator: GreaterThan
- Schwellenwert: 70

Wie zuvor überwacht diese Regel, ob die durchschnittliche CPU-Auslastung in den letzten 5 Minuten 70 % übersteigt. Mit derselben Regel können Sie jedoch zwei Instanzen überwachen, die Ihre Website ausführen. Jede Instanz wird einzeln überwacht und Sie erhalten individuelle Benachrichtigungen.

Nehmen wir an, Sie verfügen über eine Web-App, die eine massive Nachfrage verzeichnet, und Sie müssen weitere Instanzen hinzufügen. Die obige Regel überwacht weiterhin nur die beiden Instanzen. Sie können jedoch wie folgt eine Regel erstellen:

- Zielressource: myAppServicePlan
- Metrik: CPU in Prozent
- Bedingungstyp: statischen
- Dimensionen
  - Instanz = *
- Zeitaggregation: Durchschnitt
- Zeitraum: Über die letzten 5 Minuten
- Häufigkeit: 1 Minute
- Operator: GreaterThan
- Schwellenwert: 70

Diese Regel überwacht automatisch alle Werte für die Instanz, d. h. Sie können Ihre Instanzen überwachen, sobald sie auftreten, ohne Ihre Metrikwarnungsregel erneut ändern zu müssen.

Beim Überwachen mehrerer Dimensionen können Sie mit der Warnungsregel für dynamische Schwellenwerte individuelle Schwellenwerte für Hunderte von Metrikserien erstellen. Mit dynamischen Schwellenwerten müssen Sie weniger Warnungsregeln verwalten und sparen viel Zeit bei der Verwaltung und Erstellung von Warnungsregeln.

Nehmen wir an, Sie haben eine Web-App mit vielen Instanzen, und Sie wissen nicht, welcher Schwellenwert am besten geeignet ist. Die obigen Regeln verwenden stets den Schwellenwert von 70 Prozent. Sie können jedoch wie folgt eine Regel erstellen:

- Zielressource: myAppServicePlan
- Metrik: CPU in Prozent
- Bedingungstyp: Dynamisch
- Dimensionen
  - Instanz = *
- Zeitaggregation: Durchschnitt
- Zeitraum: Über die letzten 5 Minuten
- Häufigkeit: 1 Minute
- Operator: GreaterThan
- Empfindlichkeit: Mittel
- Zurückliegende Zeiträume: 1
- Anzahl von Verstößen: 1

Diese Regel überwacht, ob die durchschnittliche CPU-Auslastung in den letzten fünf Minuten das erwartete Verhalten für jede Instanz überschreitet. Mit der gleichen Regel können Sie Ihre Instanzen überwachen, ohne Ihre Metrikwarnungsregel erneut ändern zu müssen. Jede Instanz erhält einen Schwellenwert, der dem Verhaltensmuster der Metrikreihe entspricht. Er wird basierend auf neuen Daten fortlaufend geändert, um die Genauigkeit des Schwellenwerts zu verbessern. Wie zuvor wird jede Instanz einzeln überwacht, und Sie erhalten individuelle Benachrichtigungen.

Wenn Sie die Anzahl der zurückliegenden Zeiträume und Verstöße erhöhen, können Sie Warnungen filtern, damit Sie nur Warnungen erhalten, die Ihrer Definition von einer erheblichen Abweichung entsprechen. [Erfahren Sie mehr über erweiterte Optionen für dynamische Schwellenwerte.](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean)

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Bedarfsorientierte Überwachung mithilfe von Metrikwarnungen in Azure Monitor

Bisher haben Sie gesehen, wie eine einzelne Metrikwarnung zum Überwachen einer oder mehrerer metrischer Zeitreihen im Zusammenhang mit einer einzelnen Azure-Ressource verwendet werden kann. In vielen Fällen soll aber die gleiche Warnungsregel auf viele Ressourcen angewendet werden. Azure Monitor unterstützt auch die Überwachung von mehreren Ressourcen mit einer Metrikwarnungsregel. Dieses Feature wird derzeit nur auf virtuellen Computern unterstützt. Mit nur einer Metrikwarnung können die Ressourcen einer Azure-Region überwacht werden.

Sie können den Bereich für die Überwachung mit einer einzelnen Metrikwarnung auf drei Arten angeben:

- Als Liste mit virtuellen Computern einer Azure-Region unter einem Abonnement
- Alle virtuellen Computer (in einer Azure-Region) in einer oder mehreren Ressourcengruppen eines Abonnements
- Alle virtuellen Computer (in einer Azure-Region) unter einem Abonnement

Das Erstellen von Metrikwarnungsregeln, mit denen mehrere Ressourcen überwacht werden, ähnelt dem [Erstellen einer Metrikwarnung](alerts-metric.md), mit der eine einzelne Ressource überwacht wird. Der einzige Unterschied ist, dass Sie alle Ressourcen auswählen, die überwacht werden sollen. Sie können diese Regeln auch mit [Azure Resource Manager-Vorlagen](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources) erstellen. Sie erhalten für jeden virtuellen Computer gesonderte Benachrichtigungen.

## <a name="typical-latency"></a>Typische Wartezeit

Bei Metrikwarnungen werden Sie in der Regel in weniger als 5 Minuten benachrichtigt, wenn Sie die Häufigkeit der Warnungsregeln auf 1 Minute einstellen. Bei Benachrichtigungssystemen mit hoher Last kann es zu einer längeren Wartezeit kommen.

## <a name="supported-resource-types-for-metric-alerts"></a>Unterstützte Ressourcentypen für Metrikwarnungen

Die vollständige Liste der unterstützten Ressourcentypen finden Sie in diesem [Artikel](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Wenn Sie im Moment klassische Metrikwarnungen verwenden und erfahren möchten, ob Metrikwarnungen alle verwendeten Ressourcentypen unterstützen, zeigt die folgende Tabelle die Ressourcentypen, die von klassischen Metrikwarnungen unterstützt werden, und ob sie von aktuellen Metrikwarnungen unterstützt werden.

|Ressourcentyp – von klassischen Metrikwarnungen unterstützt | Unterstützt von Metrikwarnungen |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Ja |
| Microsoft.Batch/batchAccounts| Ja|
|Microsoft.Cache/redis| Ja |
|Microsoft.ClassicCompute/virtualMachines | Nein |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nein|
|Microsoft.CognitiveServices/accounts | Nein |
|Microsoft.Compute/virtualMachines | Ja|
|Microsoft.Compute/virtualMachineScaleSets| Ja|
|Microsoft.ClassicStorage/storageAccounts| Nein |
|Microsoft.DataFactory/datafactories | Ja|
|Microsoft.DBforMySQL/servers| Ja|
|Microsoft.DBforPostgreSQL/servers| Ja|
|Microsoft.Devices/IotHubs | Nein|
|Microsoft.DocumentDB/databaseAccounts| Ja|
|Microsoft.EventHub/namespaces | Ja|
|Microsoft.Logic/workflows | Ja|
|Microsoft.Network/loadBalancers |Ja|
|Microsoft.Network/publicIPAddresses| Ja|
|Microsoft.Network/applicationGateways| Ja|
|Microsoft.Network/expressRouteCircuits| Ja|
|Microsoft.Network/trafficManagerProfiles | Ja|
|Microsoft.Search/searchServices | Ja|
|Microsoft.ServiceBus/namespaces| Ja |
|Microsoft.Storage/storageAccounts | Ja|
|Microsoft.StreamAnalytics/streamingjobs| Ja|
|Microsoft.TimeSeriesInsights/environments | Ja|
|Microsoft. Web/serverfarms | Ja |
|Microsoft. Web/sites (ohne Funktionen) | Ja|
|Microsoft. Web/hostingEnvironments/multiRolePools | Nein|
|Microsoft. Web/hostingEnvironments/workerPools| Nein |
|Microsoft.SQL/Servers | Nein |

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie Sie in Azure Metrikwarnungen erstellen, anzeigen und verwalten können.](alerts-metric.md)
- [Erfahren Sie, wie Sie Metrikwarnungen mithilfe von Azure Resource Manager-Vorlagen bereitstellen können](../../azure-monitor/platform/alerts-metric-create-templates.md).
- [Erfahren Sie mehr über Aktionsgruppen](action-groups.md).
- [Erfahren Sie mehr über den Bedingungstyp für dynamische Schwellenwerte.](alerts-dynamic-thresholds.md)
