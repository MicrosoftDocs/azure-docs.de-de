---
title: Analysieren von Benutzernavigationsmustern mit Benutzerabläufen in Azure Application Insights | Microsoft-Dokumention
description: Analysieren Sie, wie Benutzer zwischen den Seiten und Features Ihrer Web-App navigieren.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 91274fad4e56c69777333c81ea3b32dccdcf64ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60373307"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analysieren von Benutzernavigationsmustern mit Benutzerabläufen in Application Insights

![Application Insights-Tool für Benutzerabläufe](./media/usage-flows/00001-flows.png)

Das Tool für Benutzerabläufe visualisiert die Navigation von Benutzern zwischen den Seiten und Features Ihrer Website. Dadurch liefert es beispielsweise Antworten auf folgende Fragen:

* Wie verlassen Benutzer eine Seite auf Ihrer Website?
* Worauf klicken Benutzer auf einer Seite Ihrer Website?
* An welchen Stellen verlassen Benutzer Ihre Website am häufigsten?
* Gibt es Bereiche, in denen Benutzer immer wieder die gleiche Aktion wiederholen?

Das Tool für Benutzerabläufe startet bei einem Seitenaufruf, einem benutzerdefinierten Ereignis oder einer von Ihnen angegebenen Ausnahme. Nach einem Ausgangsereignis dieser Art werden für die Benutzerabläufe die Ereignisse angezeigt, die im Verlauf von Benutzersitzungen vorher und nachher aufgetreten sind. Linien mit unterschiedlicher Stärke geben Aufschluss darüber, wie oft Benutzer den einzelnen Pfaden folgen. Mit speziellen Knoten vom Typ **Sitzung gestartet** wird angezeigt, wo die nachfolgenden Knoten mit einer Sitzung begonnen haben. Mit speziellen Knoten vom Typ **Sitzung beendet** wird angezeigt, wie viele Benutzer nach dem vorherigen Knoten keine Seitenaufrufe oder benutzerdefinierten Ereignisse mehr gesendet haben. Hierdurch wird deutlich, wo Benutzer Ihre Website wahrscheinlich verlassen haben.

> [!NOTE]
> Ihre Application Insights-Ressource muss Seitenaufrufe oder benutzerdefinierte Ereignisse enthalten, um das Tool für Benutzerabläufe verwenden zu können. [Informieren Sie sich darüber, wie Sie Ihre App so einrichten können, dass Seitenansichten automatisch mit dem Application Insights-JavaScript-SDK erfasst werden](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Starten durch Auswählen eines Ausgangsereignisses

![Auswählen eines Ausgangsereignisses für Benutzerabläufe](./media/usage-flows/00002-flows-initial-event.png)

Wählen Sie zur Beantwortung von Fragen mit dem Tool für Benutzerabläufe einen Seitenaufruf, ein benutzerdefiniertes Ereignis oder eine Ausnahme als Ausgangspunkt für die Visualisierung aus:

1. Klicken Sie im Titel **What do users do after...?** (Was tun Benutzer nach Folgendem: ...?) auf den Link, oder klicken Sie auf die Schaltfläche **Bearbeiten**.
2. Wählen Sie im Dropdownmenü **Ursprüngliches Ereignis** einen Seitenaufruf, ein benutzerdefiniertes Ereignis oder eine Ausnahme aus.
3. Klicken Sie auf **Diagramm erstellen**.

Die Spalte „Step 1“ (Schritt 1) der Visualisierung zeigt, welche Aktion Benutzer direkt nach dem Ausgangsereignis am häufigsten ausgeführt haben (absteigend sortiert nach Häufigkeit). Die Spalte „Step 2“ (Schritt 2) und die nachfolgenden Spalten zeigen, welche Aktionen Benutzer danach ausgeführt haben. So können Sie sich einen Überblick über die Navigation sämtlicher Benutzer auf Ihrer Website verschaffen.

Standardmäßig zieht das Tool nur willkürliche Stichproben von Seitenaufrufen und benutzerdefinierten Ereignissen auf Ihrer Website aus den letzten 24 Stunden heran. Über das Bearbeitungsmenü können Sie den Zeitbereich vergrößern und das Verhältnis zwischen Leistung und Genauigkeit für willkürliche Stichproben verändern.

Sollten einige der Seitenaufrufe, benutzerdefinierten Ereignisse und Ausnahmen für Sie nicht relevant sein, klicken Sie für die Knoten, die Sie ausblenden möchten, auf **X**. Klicken Sie nach der Wahl der auszublendenden Knoten unter der Visualisierung auf die Schaltfläche **Diagramm erstellen**. Wenn Sie alle ausgeblendeten Knoten anzeigen möchten, klicken Sie auf die Schaltfläche **Bearbeiten**, und sehen Sie sich den Abschnitt **Ausgeschlossene Ereignisse** an.

Sollten bestimmte Seitenaufrufe oder benutzerdefinierte Ereignisse nicht wie erwartet in der Visualisierung angezeigt werden, gehen Sie wie folgt vor:

* Überprüfen Sie im Menü **Bearbeiten** den Abschnitt **Ausgeschlossene Ereignisse**.
* Verwenden Sie die Schaltflächen mit dem Pluszeichen für Knoten vom Typ **Andere**, um weniger häufige Ereignisse in die Visualisierung einzubinden.
* Wenn der erwartete Seitenaufruf oder das erwartete benutzerdefinierte Ereignis nur selten von Benutzern gesendet wird, können Sie versuchen, den Zeitbereich der Visualisierung über das Menü **Bearbeiten** zu vergrößern.
* Vergewissern Sie sich, dass der erwartete Seitenaufruf, das benutzerdefinierte Ereignis oder die Ausnahme im Quellcode Ihrer Website so eingerichtet ist, dass dies vom Application Insights SDK erfasst wird. Informationen zum Erfassen benutzerdefinierter Ereignisse finden Sie [hier](../../azure-monitor/app/api-custom-events-metrics.md).

Verwenden Sie die Dropdownmenüs **Previous steps** (Vorherige Schritte) und **Next steps** (Nächste Schritte) oberhalb der Visualisierung, wenn Sie mehr Schritte anzeigen möchten.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Wohin wechseln Besucher nach dem Besuch einer Seite oder nach der Verwendung eines Features, und worauf klicken sie?

![Verwenden von Benutzerabläufen, um nachzuvollziehen, worauf Benutzer klicken](./media/usage-flows/00003-flows-one-step.png)

Wenn es sich bei Ihrem Ausgangsereignis um einen Seitenaufruf handelt, können Sie über die erste Spalte der Visualisierung (Schritt 1) schnell ermitteln, welche Aktion Benutzer direkt im Anschluss an den Seitenbesuch ausgeführt haben. Öffnen Sie Ihre Website in einem Fenster neben der Visualisierung von Benutzerabläufen. Vergleichen Sie Ihre Erwartungen hinsichtlich der Benutzerinteraktion mit der Liste von Ereignissen aus der Spalte „Step 1“ (Schritt 1). Es kommt immer wieder vor, dass ein Seitenelement, dem Ihr Team keine besondere Bedeutung beimisst, zu den meistgenutzten Elementen der Seite gehört. Dies kann ein guter Ausgangspunkt für die Optimierung des Websitedesigns sein.

Wenn es sich bei Ihrem Ausgangsereignis um ein benutzerdefiniertes Ereignis handelt, zeigt die erste Spalte,welche Aktion Benutzer direkt im Anschluss an diese Aktion ausgeführt haben. Überlegen Sie sich auch hier, ob das beobachtete Verhalten der Benutzer den Zielen und Erwartungen Ihres Teams entspricht. Wenn Sie als Ausgangsereignis also beispielsweise „Artikel in den Warenkorb gelegt“ ausgewählt haben, prüfen Sie in der Visualisierung, ob kurz darauf „Zur Kasse“ und „Kauf abgeschlossen“ erscheinen. Falls das Benutzerverhalten von Ihren Erwartungen abweicht, können Sie anhand der Visualisierung nachvollziehen, inwiefern dies auf das aktuelle Design Ihrer Website zurückzuführen ist.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>An welchen Stellen verlassen Benutzer Ihre Website am häufigsten?

Achten Sie auf Knoten vom Typ **Sitzung beendet**, die weit oben in einer Visualisierungsspalte erscheinen (insbesondere zu Beginn eines Ablaufs). Das bedeutet, dass viele Benutzer Ihre Website im Anschluss an die zuvor besuchten Seiten und ausgeführten Benutzeroberflächeninteraktionen wahrscheinlich verlassen haben. Dies ist manchmal zu erwarten (beispielsweise nach Abschluss eines Einkaufs auf einer E-Commerce-Seite). In der Regel deutet das Verlassen von Benutzern jedoch auf Designprobleme, Leistungsdefizite oder andere korrigierbare Probleme Ihrer Website hin.

Beachten Sie, dass Knoten vom Typ **Sitzung beendet** nur auf Telemetriedaten basieren, die durch diese Application Insights-Ressource gesammelt wurden. Wenn Application Insights für bestimmte Benutzerinteraktionen keine Telemetriedaten erhält, haben Benutzer möglicherweise trotzdem auf diese Weise mit Ihrer Website interagiert, obwohl die Sitzung laut dem Tool für Benutzerabläufe beendet wurde.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Gibt es Bereiche, in denen Benutzer immer wieder die gleiche Aktion wiederholen?

Halten Sie die Augen nach Seitenaufrufen oder benutzerdefinierten Ereignissen offen, die von vielen Benutzern über mehrere Visualisierungsschritte hinweg wiederholt werden. Das bedeutet in der Regel, dass Benutzer auf Ihrer Website repetitive Aktionen ausführen. Überlegen Sie sich in diesem Fall, wie Sie das Design der Website ändern oder neue Funktionen hinzufügen können, um Wiederholungen zu reduzieren. Ein Beispiel wäre etwa das Hinzufügen einer Massenbearbeitungsfunktion, wenn Sie feststellen, dass Benutzer repetitive Aktionen für jede Zeile eines Tabellenelements ausführen.

## <a name="common-questions"></a>Häufig gestellte Fragen

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Stellt das Ausgangsereignis das erste Vorkommen des Ereignisses in einer Sitzung oder ein beliebiges Vorkommen in einer Sitzung dar?

Das Ausgangsereignis in der Visualisierung stellt nur die erste Instanz eines Seitenaufrufs oder benutzerdefinierten Ereignisses dar, den bzw. das ein Benutzer während einer Sitzung gesendet hat. Wenn Benutzer das Ausgangsereignis mehrmals in einer Sitzung senden können, zeigt die Spalte „Step 1“ (Schritt 1) nur das Verhalten von Benutzern nach der *ersten* Instanz des Ausgangsereignisses (nicht alle Instanzen).

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Einige Knoten in meiner Visualisierung sind zu allgemein gehalten. Beispiel: Ein Knoten, für den nur „Button Clicked“ (Auf Schaltfläche geklickt) angegeben ist. Wie kann ich ihn in ausführlichere Knoten unterteilen?

Verwenden Sie im Menü **Bearbeiten** die Optionen unter **Teilen nach**:

1. Wählen Sie das Ereignis, das Sie unterteilen möchten, im Menü **Ereignis** aus.
2. Wählen Sie im Menü **Dimension** eine Dimension aus. Wenn Sie beispielsweise über ein Ereignis „Button Clicked“ (Auf Schaltfläche geklickt) verfügen, können Sie es mit der benutzerdefinierten Eigenschaft „Button Name“ (Schaltflächenname) versuchen.

## <a name="next-steps"></a>Nächste Schritte

* [Nutzungsübersicht](usage-overview.md)
* [Benutzer-, Sitzungs- und Ereignisanalyse in Azure Application Insights](usage-segmentation.md)
* [Bindung](usage-retention.md)
* [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](../../azure-monitor/app/api-custom-events-metrics.md)