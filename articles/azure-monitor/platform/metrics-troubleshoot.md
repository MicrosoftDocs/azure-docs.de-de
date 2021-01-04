---
title: Problembehandlung für Azure Monitor-Metrikdiagramme
description: Problembehandlung beim Erstellen, Anpassen oder Interpretieren von Metrikdiagrammen
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1a9286ff15834fafe4a69907836ce1abd17abca6
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168068"
---
# <a name="troubleshooting-metrics-charts"></a>Problembehandlung für Metrikdiagramme

Verwenden Sie diesen Artikel, wenn Probleme bei der Erstellung, Anpassung oder Interpretation von Diagrammen im Azure-Metrik-Explorer auftreten. Wenn Sie noch nicht mit Metriken vertraut sind, erhalten Sie Informationen zu den [ersten Schritten mit dem Metrik-Explorer](metrics-getting-started.md) und zu [den erweiterten Features des Metrik-Explorers](metrics-charts.md). Sie können auch [Beispiele](metric-chart-samples.md) zu den konfigurierten Metrikdiagrammen anzeigen.

## <a name="cant-find-your-resource-to-select-it"></a>Sie können Ihre Ressource nicht finden, um sie auszuwählen.

Sie haben auf die Schaltfläche **Ressource auswählen** geklickt, aber Ihre Ressource wird im Dialogfeld zur Ressourcenauswahl nicht angezeigt.

**Lösung:** Der Metrik-Explorer erfordert, dass Sie Abonnements und Ressourcengruppen auswählen, bevor Sie verfügbare Ressourcen auflisten. Wenn Ihre Ressource nicht angezeigt wird:

1. Stellen Sie sicher, dass Sie in der Dropdownliste **Abonnement** das richtige Abonnement ausgewählt haben. Wenn Ihr Abonnement nicht aufgeführt ist, klicken Sie auf **Verzeichnis + Abonnementeinstellungen** , und fügen Sie ein Abonnement mit Ihrer Ressource hinzu.

1. Stellen Sie sicher, dass Sie die richtige Ressourcengruppe ausgewählt haben.
    > [!WARNING]
    > Um die beste Leistung zu erzielen, weist die Dropdownliste **Ressourcengruppe** beim erstmaligen Öffnen des Metrik-Explorers keine vorab ausgewählten Ressourcengruppen auf. Sie müssen mindestens eine Gruppe auswählen, bevor Ressourcen angezeigt werden.

## <a name="chart-shows-no-data"></a>Diagramm enthält keine Daten

Gelegentlich zeigen die Diagramme nach der Auswahl der richtigen Ressourcen und Metriken keine Daten an. Dieses Verhalten kann durch mehrere der folgenden Gründe verursacht werden:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Der Microsoft.Insights-Ressourcenanbieter ist nicht für Ihr Abonnement registriert.

Das Untersuchen von Metriken erfordert einen *Microsoft.Insights* -Ressourcenanbieter, der in Ihrem Abonnement registriert ist. In vielen Fällen wird er automatisch registriert (d. h. nachdem Sie eine Warnungsregel konfiguriert, die Diagnoseeinstellungen für eine Ressource angepasst oder eine Regel für die automatische Skalierung konfiguriert haben). Wenn der Microsoft.Insights-Ressourcenanbieter nicht registriert ist, müssen Sie ihn manuell registrieren, indem Sie die unter [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md) beschriebenen Schritte ausführen.

**Lösung:** Öffnen Sie die Registerkarte **Abonnements** , **Ressourcenanbieter** und überprüfen Sie, ob *Microsoft.Insights* für Ihr Abonnement registriert ist.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Ihre Zugriffsrechte sind für den Zugriff auf die Ressource nicht ausreichend

In Azure wird der Zugriff auf Metriken durch die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/overview.md) gesteuert. Sie müssen Mitglied von [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader), [Überwachungsmitwirkender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) oder [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) sein, um Metriken für eine Ressource untersuchen zu können.

**Lösung:** Stellen Sie sicher, dass Sie über ausreichende Berechtigungen für die Ressource verfügen, deren Metriken Sie untersuchen.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Ihre Ressource hat während des ausgewählten Zeitraums keine Metriken ausgegeben

Einige Ressourcen geben ihre Metriken nicht kontinuierlich aus. Beispielsweise sammelt Azure keine Metriken für angehaltene virtuelle Computer. Andere Ressourcen können ihre Metriken nur dann ausgeben, wenn eine Bedingung erfüllt ist. Beispielsweise erfordert eine Metrik, die die Verarbeitungszeit einer Transaktion anzeigt, mindestens eine Transaktion. Wenn im ausgewählten Zeitbereich keine Transaktionen erfolgt sind, ist das Diagramm natürlich leer. Darüber hinaus werden die meisten Metriken in Azure zwar minütlich erfasst, aber es gibt auch einige Metriken, die seltener erfasst werden. Weitere Informationen zu der Metrik, die Sie untersuchen möchten, finden Sie in der Dokumentation zur Metrik.

**Lösung:** Vergrößern Sie den Zeitbereich des Diagramms. Sie können mit „Letzte 30 Tage“ mit einer größeren Zeitgranularität beginnen (oder sich auf die Option „Automatische Zeitgranularität“ verlassen).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>Sie haben einen Zeitraum von mehr als 30 Tagen ausgewählt

[Die meisten Metriken werden in Azure 93 Tage lang gespeichert](data-platform-metrics.md#retention-of-metrics). Sie können jedoch nur Daten für maximal 30 Tage in einem einzelnen Diagramm abfragen. Diese Einschränkung gilt nicht für [protokollbasierte Metriken](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

**Lösung:** Wenn ein leeres Diagramm angezeigt wird oder Ihr Diagramm nur einen Teil der Metrikdaten anzeigt, stellen Sie sicher, dass die Differenz zwischen Start- und Enddatum in der Zeitauswahl das Intervall von 30 Tagen nicht überschreitet.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Alle Metrikwerte lagen außerhalb des gesperrten y-Achsenbereichs

Wenn Sie [die Grenzen der y-Achse](metrics-charts.md#lock-boundaries-of-chart-y-axis) des Diagramms sperren, können Sie unbeabsichtigt bewirken, dass der Diagrammanzeigebereich die Diagrammzeile nicht anzeigt. Wenn z. B. die y-Achse auf einen Bereich zwischen 0 % und 50 % festgelegt ist und die Metrik einen konstanten Wert von 100 % aufweist, wird die Zeile immer außerhalb des sichtbaren Bereichs dargestellt, wodurch das Diagramm leer aussieht.

**Lösung:** Stellen Sie sicher, dass die y-Achsenbegrenzungen des Diagramms nicht außerhalb des Bereichs der Metrikwerte gesperrt sind. Wenn die y-Achsenbegrenzungen gesperrt sind, sollten Sie sie möglicherweise vorübergehend zurücksetzen, um sicherzustellen, dass die Metrikwerte nicht außerhalb des Diagrammbereichs liegen. Die Sperrung des y-Achsenbereichs wird nicht mit automatischer Granularität für die Diagramme mit der Aggregation **sum** , **min** und **max** empfohlen, da sich ihre Werte mit der Granularität ändern, indem die Größe des Browserfensters geändert oder von einer Bildschirmauflösung zur anderen gewechselt wird. Durch den Wechsel der Granularität bleibt der Anzeigebereich Ihres Diagramms möglicherweise leer.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Sie betrachten die Metrik eines Gastbetriebssystems, haben aber die Azure-Diagnoseerweiterung nicht aktiviert

Das Sammeln von **Gastbetriebssystem** -Metriken erfordert die Konfiguration der Azure-Diagnoseerweiterung oder deren Aktivierung über das Fenster **Diagnoseeinstellungen** für Ihre Ressource.

**Lösung:** Wenn die Azure-Diagnoseerweiterung aktiviert ist, Ihre Metriken aber immer noch nicht angezeigt werden, führen Sie die im [Handbuch zur Problembehandlung der Azure-Diagnoseerweiterung](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal) beschriebenen Schritte aus. Weitere Informationen finden Sie auch in den Schritten zur Problembehandlung für [Namespace und Metriken des Gastbetriebssystems können nicht ausgewählt werden](#cannot-pick-guest-os-namespace-and-metrics).

## <a name="error-retrieving-data-message-on-dashboard"></a>Meldung „Fehler beim Abrufen von Daten“ im Dashboard

Dieses Problem kann auftreten, wenn Ihr Dashboard mit einer Metrik erstellt wurde, die später nicht mehr verwendet und aus Azure entfernt wurde. Um sicherzustellen, dass dies der Fall ist, öffnen Sie die Registerkarte **Metriken** Ihrer Ressource, und überprüfen Sie die verfügbaren Metriken in der Metrikauswahl. Wenn die Metrik nicht angezeigt wird, wurde die Metrik aus Azure entfernt. Wenn eine Metrik veraltet ist, existiert in der Regel eine bessere neue Metrik, die eine ähnliche Sicht auf die Ressourcenintegrität bietet.

**Lösung:** Aktualisieren Sie die fehlerhafte Kachel, indem Sie eine alternative Metrik für Ihr Diagramm im Dashboard auswählen. Sie können [eine Liste der verfügbaren Metriken für Azure-Dienste](metrics-supported.md) anzeigen.

## <a name="chart-shows-dashed-line"></a>Das Diagramm zeigt eine gestrichelte Linie an

Azure-Metrikdiagramme verwenden den gestrichelten Linienstil, um anzuzeigen, dass zwischen zwei bekannten Aggregationsintervall-Datenpunkten ein fehlender Wert (auch als „Nullwert“ bezeichnet) liegt. Wenn Sie z. B. in der Zeitauswahl die Zeitgranularität „1 Minute“ ausgewählt haben, die Metrik aber um 07:26, 07:27, 07:29 und 07:30 Uhr (beachten Sie die Lücke von einer Minute zwischen dem zweiten und dritten Datenpunkt) gemeldet wurde, dann sind 07:27 und 07:29 durch eine gestrichelte Linie und alle anderen Datenpunkte durch eine durchgezogene Linie verbunden. Die gestrichelte Linie fällt auf Null, wenn die Metrik die Aggregation **count** und **sum** verwendet. Für die Aggregationen **avg** , **min** oder **max** verbindet die gestrichelte Linie zwei nächstgelegene bekannte Datenpunkte. Wenn die Daten außerdem auf der rechten oder linken Seite des Diagramms fehlen, wird die gestrichelte Linie in Richtung des fehlenden Datenpunkts erweitert.
  ![Screenshot der Erweiterung der gestrichelte Linie in Richtung des fehlenden Datenpunkts bei fehlenden Daten auf der rechten oder linken Seite des Diagramms](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**Lösung:** Dieses Verhalten ist beabsichtigt. Es ist hilfreich, um fehlende Datenpunkte zu identifizieren. Das Liniendiagramm ist eine bevorzugte Wahl für die Visualisierung von Trends von Metriken mit hoher Dichte, kann aber für die Metriken mit wenigen Werten schwierig zu interpretieren sein, insbesondere wenn die Korrelation von Werten mit dem Aggregationsintervall wichtig ist. Die gestrichelte Linie erleichtert das Lesen dieser Diagramme, aber wenn Ihr Diagramm weiterhin nicht eindeutig ist, sollten Sie überlegen, Ihre Metriken mit einem anderen Diagrammtyp darzustellen. So zeigt z. B. ein Streuplotdiagramm für dieselbe Metrik jedes Aggregationsintervall deutlich an, indem es nur einen Punkt darstellt, wenn ein Wert vorhanden ist, und den Datenpunkt ganz überspringt, wenn der Wert fehlt: ![Screenshot mit hervorgehobener Option für das Menü „Punktdiagramm“](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > Wenn Sie dennoch ein Liniendiagramm für Ihre Metrik bevorzugen, kann es hilfreich sein, den Mauszeiger über das Diagramm zu bewegen, um die Zeitgranularität zu beurteilen, indem Sie den Datenpunkt an der Position des Mauszeigers markieren.

## <a name="chart-shows-unexpected-drop-in-values"></a>Das Diagramm zeigt einen unerwarteten Abfall der Werte

In vielen Fällen ist der beobachtete Abfall der Metrikwerte eine Fehlinterpretation der in der Grafik dargestellten Daten. Sie können sich durch einen Abfall von Summen oder Werten täuschen lassen, wenn das Diagramm die letzten Minuten anzeigt, da die letzten metrischen Datenpunkte noch nicht von Azure empfangen oder verarbeitet wurden. Je nach Dienst kann die Wartezeit bei der Verarbeitung von Metriken einige Minuten betragen. Bei Diagrammen, die einen aktuellen Zeitbereich mit einer Granularität von 1 oder 5 Minuten darstellen, wird ein Abfall des Werts über die letzten paar Minuten deutlicher: ![Screenshot eines Abfalls des Werts über die letzten paar Minuten](./media/metrics-troubleshoot/drop-in-values.png)

**Lösung:** Dieses Verhalten ist beabsichtigt. Wir sind der Meinung, dass die sofortige Anzeige von Daten beim Eintreffen von Vorteil ist, auch wenn die Daten nur *teilweise* vorliegen oder *unvollständig* sind. Auf diese Weise können Sie wichtige Schlussfolgerungen früher ziehen und sofort mit der Untersuchung beginnen. Wenn z. B. für eine Metrik, die die Anzahl der Fehler anzeigt, der Teilwert X angezeigt wird, bedeutet dies, dass es in einer bestimmten Minute mindestens X Fehler gab. Sie können sofort mit der Untersuchung des Problems beginnen, anstatt darauf zu warten, dass die genaue Anzahl der in dieser Minute aufgetretenen Fehler angezeigt wird, was vielleicht nicht so entscheidend ist. Das Diagramm wird aktualisiert, sobald das gesamte Dataset vorliegt, aber zu diesem Zeitpunkt werden möglicherweise auch neue unvollständige Datenpunkte aus den letzten Minuten angezeigt.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>Der Namespace und die Metriken des Gastbetriebssystems können nicht ausgewählt werden

Virtuelle Computer und VM-Skalierungsgruppen weisen zwei Kategorien von Metriken auf: **Hostmetriken des virtuellen Computers** , die von der Azure-Hostumgebung gesammelt werden, und Metriken des **Gastbetriebssystems (klassisch)** , die von dem [Überwachungs-Agent](agents-overview.md) gesammelt werden, der auf Ihren virtuellen Computern ausgeführt wird. Sie installieren den Überwachungs-Agent, indem Sie die [Azure-Diagnoseerweiterung](diagnostics-extension-overview.md) aktivieren.

Standardmäßig werden die Metriken des Gastbetriebssystems im Azure Storage-Konto gespeichert, das Sie auf der Registerkarte **Diagnoseeinstellungen** Ihrer Ressource auswählen. Wenn die Metriken des Gastbetriebssystems nicht gesammelt werden oder der Metrik-Explorer nicht darauf zugreifen kann, wird nur der Namespace für die **Hostmetriken des virtuellen Computers** angezeigt:

![Abbildung eines Diagramms mit Metriken](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**Lösung:** Wenn Namespace und Metriken des **Gastbetriebssystems (klassisch)** nicht im Metrik-Explorer angezeigt werden:

1. Vergewissern Sie sich, dass die [Azure-Diagnoseerweiterung](diagnostics-extension-overview.md) aktiviert und konfiguriert ist, um Metriken zu sammeln.
    > [!WARNING]
    > Sie können den [Log Analytics-Agent](agents-overview.md#log-analytics-agent) nicht verwenden (auch als Microsoft Monitoring Agent oder „MMA“ bezeichnet), um das **Gastbetriebssystem** an ein Speicherkonto zu senden.

1. Stellen Sie sicher, dass der **Microsoft.Insights** -Ressourcenanbieter [für Ihr Abonnement registriert ist](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Stellen Sie sicher, dass das Speicherkonto nicht durch die Firewall geschützt ist. Das Azure-Portal benötigt Zugriff auf das Speicherkonto, um Metrikdaten abzurufen und die Diagramme auszugeben.

1. Verwenden Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), um zu überprüfen, ob Metriken in das Speicherkonto gelangen. Wenn keine Metriken gesammelt werden, folgen Sie dem [Handbuch zur Problembehandlung für die Azure-Diagnoseerweiterung](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit dem Metrik-Explorer](metrics-getting-started.md)
* [Erweiterte Funktionen des Metrik-Explorers](metrics-charts.md)
* [Unterstützte Metriken von Azure Monitor](metrics-supported.md)
* [Beispiele für Metrikdiagramme](metric-chart-samples.md)
