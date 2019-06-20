---
title: Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights | Microsoft-Dokumentation
description: Wann Sie protokollbasierte oder vorab aggregierte Metriken in Azure Application Insights verwenden
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitalyg
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9520cbb9973071bf1c52266d7718837607c1d10f
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256126"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights

Dieser Artikel erklärt den Unterschied zwischen „traditionellen“ Application Insights-Metriken, die auf Protokollen basieren, und vorab aggregierten Metriken, die sich derzeit in der öffentlichen Vorschau befinden. Beide Arten von Metriken stehen den Benutzern von Application Insights zur Verfügung und bringen jeweils einen einzigartigen Wert bei Überwachung von Anwendungsintegrität, Diagnose und Analyse. Entwickler, die Anwendungen instrumentieren, können entscheiden, welche Art von Metrik für ein bestimmtes Szenario am besten geeignet ist, abhängig von der Größe der Anwendung, dem erwarteten Telemetriedatenvolumen und den Geschäftsanforderungen an Genauigkeit der Metriken und Warnungen.

## <a name="log-based-metrics"></a>Protokollbasierte Metriken

Bis vor kurzem basierte das anwendungsüberwachende Telemetriedatenmodell in Application Insights ausschließlich auf einer kleinen Anzahl von vordefinierten Ereignistypen, wie z. B. Anforderungen, Ausnahmen, Abhängigkeitsaufrufen, Seitenaufrufen, etc. Entwickler können das SDK verwenden, um diese Ereignisse entweder manuell auszusenden (indem sie Code schreiben, der das SDK explizit aufruft), oder sie können sich auf die automatische Sammlung von Ereignissen aus der automatischen Instrumentierung verlassen. In beiden Fällen speichert das Application Insights-Backend alle gesammelten Ereignisse als Protokolle, und die Application Insights-Blätter im Azure-Portal dienen als Analyse- und Diagnosewerkzeug zur Visualisierung ereignisbasierter Daten aus Protokollen.

Die Verwendung von Protokollen zur Erhaltung eines vollständigen Satzes von Ereignissen kann einen großen analytischen und diagnostischen Wert darstellen. So können Sie beispielsweise eine genaue Anzahl von Anforderungen an eine bestimmte URL mit der Anzahl der verschiedenen Benutzer abrufen, die diese Aufrufe getätigt haben. Oder Sie können detaillierte Diagnoseablaufverfolgungsdaten, einschließlich Ausnahmen und Aufrufe von Abhängigkeiten für jede Benutzersitzung abrufen. Diese Art von Informationen kann die Transparenz über Integrität und Nutzung der Anwendung erheblich verbessern, wodurch die Zeit für die Diagnose von Problemen mit einer Anwendung verkürzt wird. 

Gleichzeitig kann das Sammeln eines vollständigen Satzes von Ereignissen für Anwendungen, die umfangreiche Telemetriedaten erzeugen, unpraktisch (oder sogar unmöglich) sein. Für Situationen, in denen die Menge der Ereignisse zu hoch ist, implementiert Application Insights mehrere Techniken zur Reduzierung des Telemetriedatenvolumens, wie z. B. [Stichprobenerstellung](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) und [Filterung](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling), die die Anzahl der gesammelten und gespeicherten Ereignisse reduzieren. Leider senkt die Verringerung der Anzahl gespeicherter Ereignisse auch die Genauigkeit der Metriken, die hinter den Kulissen Abfragezeitaggregationen der in Protokollen gespeicherten Ereignisse durchführen müssen.

> [!NOTE]
> In Application Insights werden die Metriken, die auf der Abfragezeitaggregation von Ereignissen und Messungen basieren, die in Protokollen gespeichert sind, als protokollbasierte Metriken bezeichnet. Diese Metriken haben typischerweise viele Dimensionen aus den Ereigniseigenschaften, wodurch sie für die Analyse besser geeignet sind, aber die Genauigkeit dieser Metriken wird durch Stichprobenerstellung und Filterung negativ beeinflusst.

## <a name="pre-aggregated-metrics"></a>Vorab aggregierte Metriken

Zusätzlich zu den protokollbasierten Metriken lieferte das Application Insights-Team im Herbst 2018 eine öffentliche Vorschau von Metriken aus, die in einem speziellen, für Zeitreihen optimierten Repository gespeichert sind. Die neuen Metriken werden nicht mehr als einzelne Ereignisse mit vielen Eigenschaften, sondern als vorab aggregierte Zeitreihen und nur mit Schlüsseldimensionen gespeichert. Dadurch sind die neuen Metriken hinsichtlich der Abfragezeit überlegen: Der Datenabruf erfolgt viel schneller und erfordert weniger Rechenleistung. Dies ermöglicht neue Szenarien wie [Warnungen zu Metrikdimensionen nahezu in Echtzeit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts), reaktionsschnellere [Dashboards](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard) und mehr.

> [!IMPORTANT]
> Protokollbasierte und vorab aggregierte Metriken können in Azure Application Insights parallel verwendet werden. Zur Unterscheidung werden in Application Insights UX die vorab aggregierten Metriken nun „Standardmetriken (Vorschau)“ genannt, während die traditionellen Metriken aus den Ereignissen in „protokollbasierte Metriken“ umbenannt wurden.

Die neueren SDKs ([Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK oder höher für .NET) aggregieren Metriken während der Erfassung, bevor die Techniken zur Telemetrievolumenreduzierung beginnen. Das bedeutet, dass die Genauigkeit der neuen Metriken bei Verwendung der neuesten Application Insights SDKs nicht durch Stichprobenerstellung und Filterung beeinträchtigt wird.

Für SDKs, die keine Vorabaggregation implementieren (d. h. ältere Versionen von Application Insights SDKs oder zur Browserinstrumentierung), füllt das Application Insights-Backend weiterhin die neuen Metriken, indem es die Ereignisse aggregiert, die vom Application Insights-Ereignissammlungsendpunkt empfangen werden. Das bedeutet, dass Sie zwar nicht von dem reduzierten Datenvolumen profitieren, das über die Leitung übertragen wird, die vorab aggregierten Metriken aber dennoch bei SDKs, die Metriken während der Erfassung nicht vorab aggregieren, verwenden können und eine bessere Leistung und Unterstützung der Dimensionswarnungen nahezu in Echtzeit erhalten.

Es ist erwähnenswert, dass der Sammlungsendpunkt Ereignisse vor der Erfassungs-Stichprobenerstellung aggregiert, was bedeutet, dass die [Erfassungs-Stichprobenerstellung](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) niemals die Genauigkeit von vorab aggregierten Metriken beeinträchtigt, unabhängig davon, welche SDK-Version Sie mit Ihrer Anwendung verwenden.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Verwenden der Vorabaggregation mit benutzerdefinierten Application Insights-Metriken

Sie können die Vorabaggregation mit benutzerdefinierten Metriken verwenden. Die beiden Hauptvorteile sind die Möglichkeit, die Dimension einer benutzerdefinierten Metrik zu konfigurieren und entsprechende Warnungen auszugeben, und die Reduzierung des vom SDK an den Application Insights-Sammlungsendpunkt gesendeten Datenvolumens.

Es gibt mehrere [Möglichkeiten zum Senden benutzerdefinierter Metriken aus dem Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Wenn Ihre Version des SDK die Methoden [GetMetric und TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) bereitstellt, ist dies die bevorzugte Art und Weise, benutzerdefinierte Metriken zu senden, da in diesem Fall eine Vorabaggregation innerhalb des SDK stattfindet, die nicht nur das Volumen der in Azure gespeicherten Daten reduziert, sondern auch das Volumen der vom SDK an Application Insights übertragenen Daten. Andernfalls verwenden Sie die Methode [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric), die Metrikereignisse während der Datenerfassung vorab aggregiert.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Benutzerdefinierte Metrikdimensionen und Vorabaggregation

Alle Metriken, die Sie mit [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) oder [GetMetric- und TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)-API-Aufrufen senden, werden automatisch in Protokollen und Metrikspeichern gespeichert. Während die protokollbasierte Version Ihrer benutzerdefinierten Metrik jedoch immer alle Dimensionen beibehält, wird die vorab aggregierte Version der Metrik standardmäßig ohne Dimensionen gespeichert. Sie können die Sammlung von Dimensionen benutzerdefinierter Metriken auf der Registerkarte [Nutzung und geschätzte Kosten](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) aktivieren, indem Sie „Warnung für benutzerdefinierte Metrikdimensionen aktivieren“ aktivieren: 

![Nutzung und geschätzte Kosten](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Warum ist die Sammlung benutzerdefinierter Metrikdimensionen standardmäßig deaktiviert?

Die Sammlung von benutzerdefinierten Metrikdimensionen ist standardmäßig deaktiviert, da in Zukunft die Speicherung von benutzerdefinierten Metriken mit Dimensionen getrennt von Application Insights abgerechnet wird, während die Speicherung der nicht-dimensionalen benutzerdefinierten Metriken (bis zu einem bestimmten Kontingent) kostenfrei bleibt. Weitere Informationen zu den bevorstehenden Änderungen des Preismodells finden Sie auf unserer offiziellen [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Erstellen von Diagrammen und Erkunden von protokollbasierten und standardmäßig vorab aggregierten Metriken

Verwenden Sie den [Metrik-Explorer von Azure Monitor](../platform/metrics-getting-started.md), um Diagramme aus vorab aggregierten und protokollbasierten Metriken darzustellen, und um Dashboards mit Diagrammen zu erstellen. Nachdem Sie die gewünschte Application Insights-Ressource ausgewählt haben, können Sie mit der Namespaceauswahl zwischen Standard- (Vorschau) und protokollbasierten Metriken wechseln oder einen benutzerdefinierten Metriknamespace auswählen:

![Metriknamespace](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Nächste Schritte

* [Warnungen nahezu in Echtzeit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric und TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)