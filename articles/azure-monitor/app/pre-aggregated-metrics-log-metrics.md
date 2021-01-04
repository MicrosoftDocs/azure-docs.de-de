---
title: Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights | Microsoft-Dokumentation
description: Wann Sie protokollbasierte oder vorab aggregierte Metriken in Azure Application Insights verwenden
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9b93ac774dffb837d93853353e83b8da4ab4d8d4
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027158"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Protokollbasierte und vorab aggregierte Metriken in Azure Application Insights

Dieser Artikel erläutert den Unterschied zwischen „traditionellen“ Application Insights-Metriken, die auf Protokollen basieren, und vorab aggregierten Metriken. Beide Arten von Metriken stehen den Benutzern von Application Insights zur Verfügung und bringen jeweils einen einzigartigen Wert bei Überwachung von Anwendungsintegrität, Diagnose und Analyse. Entwickler, die Anwendungen instrumentieren, können entscheiden, welche Art von Metrik für ein bestimmtes Szenario am besten geeignet ist, abhängig von der Größe der Anwendung, dem erwarteten Telemetriedatenvolumen und den Geschäftsanforderungen an Genauigkeit der Metriken und Warnungen.

## <a name="log-based-metrics"></a>Protokollbasierte Metriken

In der Vergangenheit basierte das Telemetriedatenmodell für die Anwendungsüberwachung in Application Insights ausschließlich auf einer kleinen Anzahl von vordefinierten Ereignistypen, z. B. Anforderungen, Ausnahmen, Abhängigkeitsaufrufen, Seitenaufrufen, etc. Entwickler können das SDK verwenden, um diese Ereignisse entweder manuell auszusenden (indem sie Code schreiben, der das SDK explizit aufruft), oder sie können sich auf die automatische Sammlung von Ereignissen aus der automatischen Instrumentierung verlassen. In beiden Fällen speichert das Application Insights-Backend alle gesammelten Ereignisse als Protokolle, und die Application Insights-Blätter im Azure-Portal dienen als Analyse- und Diagnosewerkzeug zur Visualisierung ereignisbasierter Daten aus Protokollen.

Die Verwendung von Protokollen zur Erhaltung eines vollständigen Satzes von Ereignissen kann einen großen analytischen und diagnostischen Wert darstellen. So können Sie beispielsweise eine genaue Anzahl von Anforderungen an eine bestimmte URL mit der Anzahl der verschiedenen Benutzer abrufen, die diese Aufrufe getätigt haben. Oder Sie können detaillierte Diagnoseablaufverfolgungsdaten, einschließlich Ausnahmen und Aufrufe von Abhängigkeiten für jede Benutzersitzung abrufen. Diese Art von Informationen kann die Transparenz über Integrität und Nutzung der Anwendung erheblich verbessern, wodurch die Zeit für die Diagnose von Problemen mit einer Anwendung verkürzt wird.

Gleichzeitig kann das Sammeln eines kompletten Satzes an Ereignissen für Anwendungen, die große Mengen an Telemetriedaten erzeugen, schwer umsetzbar (oder sogar unmöglich) sein. Für Situationen, in denen die Menge der Ereignisse zu hoch ist, implementiert Application Insights mehrere Techniken zur Reduzierung des Telemetriedatenvolumens, wie z. B. [Stichprobenerstellung](./sampling.md) und [Filterung](./api-filtering-sampling.md), die die Anzahl der gesammelten und gespeicherten Ereignisse reduzieren. Leider senkt die Verringerung der Anzahl gespeicherter Ereignisse auch die Genauigkeit der Metriken, die hinter den Kulissen Abfragezeitaggregationen der in Protokollen gespeicherten Ereignisse durchführen müssen.

> [!NOTE]
> In Application Insights werden die Metriken, die auf der Abfragezeitaggregation von Ereignissen und Messungen basieren, die in Protokollen gespeichert sind, als protokollbasierte Metriken bezeichnet. Diese Metriken haben typischerweise viele Dimensionen aus den Ereigniseigenschaften, wodurch sie für die Analyse besser geeignet sind, aber die Genauigkeit dieser Metriken wird durch Stichprobenerstellung und Filterung negativ beeinflusst.

## <a name="pre-aggregated-metrics"></a>Vorab aggregierte Metriken

Zusätzlich zu protokollbasierten Metriken lieferte das Application Insights-Team Ende 2018 eine öffentliche Vorschau von Metriken, die in einem speziellen, für Zeitreihen optimierten Repository gespeichert sind. Die neuen Metriken werden nicht mehr als einzelne Ereignisse mit vielen Eigenschaften, sondern als vorab aggregierte Zeitreihen und nur mit Schlüsseldimensionen gespeichert. Dadurch sind die neuen Metriken hinsichtlich der Abfragezeit überlegen: Der Datenabruf erfolgt viel schneller und erfordert weniger Rechenleistung. Dies ermöglicht neue Szenarien wie [Warnungen zu Metrikdimensionen nahezu in Echtzeit](../platform/alerts-metric-near-real-time.md), reaktionsschnellere [Dashboards](./overview-dashboard.md) und mehr.

> [!IMPORTANT]
> Protokollbasierte und vorab aggregierte Metriken können in Azure Application Insights parallel verwendet werden. Zur Unterscheidung werden auf der Application Insights-Benutzeroberfläche die vorab aggregierten Metriken jetzt als „Standardmetriken (Vorschau)“ bezeichnet, während die traditionellen Metriken aus den Ereignissen in „protokollbasierte Metriken“ umbenannt wurden.

Die neueren SDKs (SDK [Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) oder höher für .NET) aggregieren die Metriken während der Auflistung vorab. Dies gilt für [Standardmetriken, die standardmäßig gesendet werden](../platform/metrics-supported.md#microsoftinsightscomponents), sodass die Genauigkeit nicht durch die Stichprobenentnahme oder das Filtern beeinträchtigt wird. Dies gilt auch für benutzerdefinierte Metriken, die mit [GetMetric](./api-custom-events-metrics.md#getmetric) gesendet werden, was zu einer geringeren Datenerfassung und geringeren Kosten führt.

Bei SDKs, die keine Vorabaggregation implementieren (d. h. ältere Versionen von Application Insights SDKs oder SDKs zur Browserinstrumentierung), füllt das Application Insights-Back-End weiterhin die neuen Metriken auf, indem es die Ereignisse aggregiert, die vom Application Insights-Endpunkt zur Ereignissammlung empfangen werden. So können Sie zwar nicht von dem reduzierten Datenvolumen profitieren, das über die Leitung übertragen wird, können die vorab aggregierten Metriken aber dennoch bei SDKs nutzen, die Metriken während der Erfassung nicht vorab aggregieren. Auf diese Weise erhalten Sie eine bessere Leistung und Unterstützung für Dimensionswarnungen nahezu in Echtzeit.

Es ist erwähnenswert, dass der Sammlungsendpunkt Ereignisse vor der Erfassungs-Stichprobenerstellung aggregiert, was bedeutet, dass die [Erfassungs-Stichprobenerstellung](./sampling.md) niemals die Genauigkeit von vorab aggregierten Metriken beeinträchtigt, unabhängig davon, welche SDK-Version Sie mit Ihrer Anwendung verwenden.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>Tabelle mit vorab aggregierten Metriken (mit SDK-Unterstützung)

| Aktuelle Produktions-SDKs | Standardmetriken (SDK-Vorabaggregation) | Benutzerdefinierte Metriken (ohne SDK-Vorabaggregation) | Benutzerdefinierte Metriken (mit SDK-Vorabaggregation)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core und .NET Framework | Unterstützt (V2.13.1+)| Unterstützt über [TrackMetric](api-custom-events-metrics.md#trackmetric)| Unterstützt (V2.7.2+) über [GetMetric](get-metric.md) |
| Java                         | Nicht unterstützt       | Unterstützt über [TrackMetric](api-custom-events-metrics.md#trackmetric)| Nicht unterstützt                           |
| Node.js                      | Nicht unterstützt       | Unterstützt über [TrackMetric](api-custom-events-metrics.md#trackmetric)| Nicht unterstützt                           |
| Python                       | Nicht unterstützt       | Unterstützt                                 | Unterstützt über [OpenCensus.stats](opencensus-python.md#metrics) |  


### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Tabelle mit vorab aggregierten Metriken (Unterstützung ohne Code)

| Aktuelle Produktions-SDKs | Standardmetriken (SDK-Vorabaggregation) | Benutzerdefinierte Metriken (ohne SDK-Vorabaggregation) | Benutzerdefinierte Metriken (mit SDK-Vorabaggregation)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Unterstützt <sup>1<sup>    | Nicht unterstützt                             | Nicht unterstützt                           |
| ASP.NET Core            | Unterstützt <sup>2<sup>    | Nicht unterstützt                             | Nicht unterstützt                           |
| Java                    | Nicht unterstützt            | Nicht unterstützt                             | [Unterstützt](java-in-process-agent.md#metrics) |
| Node.js                 | Nicht unterstützt            | Nicht unterstützt                             | Nicht unterstützt                           |

1. Beim Anfügen ohne Code (ASP.NET) in App Service werden nur Metriken im Überwachungsmodus „Vollständig“ ausgegeben. Beim Anfügen ohne Code (ASP.NET) in App Service, auf virtuellen Computern, in VMSS und in lokalen Umgebungen werden Standardmetriken ohne Dimensionen ausgegeben. Ein SDK ist für alle Dimensionen erforderlich.
2. Beim Anfügen ohne Code (ASP.NET) in App Service werden Standardmetriken ohne Dimensionen ausgegeben. Ein SDK ist für alle Dimensionen erforderlich.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Verwenden der Vorabaggregation mit benutzerdefinierten Application Insights-Metriken

Sie können die Vorabaggregation mit benutzerdefinierten Metriken verwenden. Die beiden Hauptvorteile sind die Möglichkeit, die Dimension einer benutzerdefinierten Metrik zu konfigurieren und entsprechende Warnungen auszugeben, und die Reduzierung des vom SDK an den Application Insights-Sammlungsendpunkt gesendeten Datenvolumens.

Es gibt mehrere [Möglichkeiten zum Senden benutzerdefinierter Metriken aus dem Application Insights SDK](./api-custom-events-metrics.md). Wenn Ihre Version des SDK die Methoden [GetMetric und TrackValue](./api-custom-events-metrics.md#getmetric) bereitstellt, ist dies die bevorzugte Art und Weise, benutzerdefinierte Metriken zu senden, da in diesem Fall eine Vorabaggregation innerhalb des SDK stattfindet, die nicht nur das Volumen der in Azure gespeicherten Daten reduziert, sondern auch das Volumen der vom SDK an Application Insights übertragenen Daten. Andernfalls verwenden Sie die Methode [trackMetric](./api-custom-events-metrics.md#trackmetric), die Metrikereignisse während der Datenerfassung vorab aggregiert.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Benutzerdefinierte Metrikdimensionen und Vorabaggregation

Alle Metriken, die Sie mit [trackMetric](./api-custom-events-metrics.md#trackmetric) oder [GetMetric- und TrackValue](./api-custom-events-metrics.md#getmetric)-API-Aufrufen senden, werden automatisch in Protokollen und Metrikspeichern gespeichert. Während die protokollbasierte Version Ihrer benutzerdefinierten Metrik jedoch immer alle Dimensionen beibehält, wird die vorab aggregierte Version der Metrik standardmäßig ohne Dimensionen gespeichert. Sie können die Sammlung von Dimensionen für benutzerdefinierte Metriken auf der Registerkarte [Nutzung und geschätzte Kosten](./pricing.md) aktivieren, indem Sie „Dimensionswarnungen für benutzerdefinierte Metriken aktivieren“ aktivieren: 

![Nutzung und geschätzte Kosten](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Warum ist die Sammlung benutzerdefinierter Metrikdimensionen standardmäßig deaktiviert?

Die Sammlung von benutzerdefinierten Metrikdimensionen ist standardmäßig deaktiviert, da in Zukunft die Speicherung von benutzerdefinierten Metriken mit Dimensionen getrennt von Application Insights abgerechnet wird, während die Speicherung der nicht-dimensionalen benutzerdefinierten Metriken (bis zu einem bestimmten Kontingent) kostenfrei bleibt. Weitere Informationen zu den bevorstehenden Änderungen des Preismodells finden Sie auf unserer offiziellen [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Erstellen von Diagrammen und Erkunden von protokollbasierten und standardmäßig vorab aggregierten Metriken

Verwenden Sie den [Metrik-Explorer von Azure Monitor](../platform/metrics-getting-started.md), um Diagramme aus vorab aggregierten und protokollbasierten Metriken darzustellen, und um Dashboards mit Diagrammen zu erstellen. Nachdem Sie die gewünschte Application Insights-Ressource ausgewählt haben, können Sie mit der Namespaceauswahl zwischen Standard- (Vorschau) und protokollbasierten Metriken wechseln oder einen benutzerdefinierten Metriknamespace auswählen:

![Metriknamespace](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Preismodelle für Application Insights-Metriken

Die Erfassung von Metriken in Application Insights – unabhängig davon, ob diese protokollbasiert oder vorab aggregiert erfolgt – erzeugt Kosten. Diese hängen vom Umfang der erfassten Daten ab, wie [hier](./pricing.md#pricing-model) beschrieben. Ihre benutzerdefinierten Metriken, einschließlich sämtlicher Dimensionen, werden immer im Application Insights-Protokollspeicher gespeichert. Darüber hinaus wird standardmäßig eine vorab aggregierte Version dieser Metriken (ohne Dimensionen) an den Metrikspeicher weitergeleitet.

Die Auswahl der Option [Dimensionswarnungen für benutzerdefinierte Metriken aktivieren](#custom-metrics-dimensions-and-pre-aggregation) zum Speichern aller Dimensionen vorab aggregierter Metriken im Metrikspeicher kann **zusätzliche** Kosten basierend auf den [Preisen für benutzerdefinierte Metriken](https://azure.microsoft.com/pricing/details/monitor/) verursachen.

## <a name="next-steps"></a>Nächste Schritte

* [Warnungen nahezu in Echtzeit](../platform/alerts-metric-near-real-time.md)
* [GetMetric und TrackValue](./api-custom-events-metrics.md#getmetric)
