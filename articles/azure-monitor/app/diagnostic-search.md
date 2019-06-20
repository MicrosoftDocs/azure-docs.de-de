---
title: Verwenden von Search in Azure Application Insights | Microsoft-Dokumentation
description: Durchsuchen und filtern Sie unformatierte Telemetriedaten, die von Ihrer Web-App gesendet werden.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: dfbaabd3d27804909334a7a370bcc89115e625c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900061"
---
# <a name="using-search-in-application-insights"></a>Verwenden von Search in Application Insights
Search ist eine Funktion in [Application Insights](../../azure-monitor/app/app-insights-overview.md), mit der Sie einzelne Telemetrieelemente wie Seitenaufrufe, Ausnahmen und Webanforderungen suchen und untersuchen können. Außerdem können Sie Protokollablaufverfolgungen und Ereignisse anzeigen, die Sie programmiert haben.

(Verwenden Sie [Analytics](../../azure-monitor/log-query/get-started-portal.md) zum Anwenden komplexerer Abfragen auf Ihre Daten.)

## <a name="where-do-you-see-search"></a>Wo steht Search zur Verfügung?

### <a name="in-the-azure-portal"></a>Im Azure-Portal

Sie können die Diagnosesuche explizit auf dem Blatt „Application Insights-Übersicht“ Ihrer Anwendung öffnen:

![Diagnosesuche öffnen](./media/diagnostic-search/001.png)

![Screenshot der Graphen zur Diagnosesuche](./media/diagnostic-search/002.png)

Den Hauptteil der Diagnosesuche bildet eine Liste von Telemetrieelementen: Serveranforderungen, Seitenaufrufe, benutzerdefinierte Ereignisse, die Sie programmiert haben, usw. Am Anfang der Liste befindet sich ein Übersichtsdiagramm, das die Anzahl der Ereignisse im Zeitablauf zeigt.

Klicken Sie auf „Aktualisieren“, um neue Ereignisse abzurufen.

### <a name="in-visual-studio"></a>In Visual Studio

In Visual Studio gibt es auch das Fenster „Application Insights-Suche“. Es eignet sich am besten für die Anzeige von Telemetrieereignissen, die von der Anwendung generiert wurden, die Sie debuggen. Es kann aber auch die Ereignisse zeigen, die in Ihrer veröffentlichten App im Azure-Portal gesammelt wurden.

Öffnen Sie das Fenster „Search“ in Visual Studio:

![Geöffnetes Visual Studio-Fenster „Application Insights-Suche“](./media/diagnostic-search/32.png)

Das Fenster „Search“ bietet ähnliche Features wie das Webportal:

![Visual Studio-Fenster „Application Insights-Suche“](./media/diagnostic-search/34.png)

Die Registerkarte „Nachverfolgungsvorgang“ ist verfügbar, wenn Sie eine Anforderung oder einen Seitenaufruf öffnen. Ein „Vorgang“ ist eine Folge von Ereignissen, die einer einzelnen Anforderung oder einem einzelnem Seitenaufruf zugeordnet sind. Abhängigkeitsaufrufe, Ausnahmen, Ablaufverfolgungsprotokolle und benutzerdefinierte Ereignisse können beispielsweise Teil eines einzelnen Vorgangs sein. Die Registerkarte "Nachverfolgungsvorgang" zeigt den Zeitpunkt und die Dauer dieser Ereignisse in Bezug auf die Anforderung oder den Seitenaufruf. 

## <a name="inspect-individual-items"></a>Überprüfen einzelner Elemente

Wählen Sie ein beliebiges Telemetrieelement aus, um Schlüsselfelder und verwandte Elemente anzuzeigen.

![Screenshot einer einzelnen Abhängigkeitsanforderung](./media/diagnostic-search/003.png)

Dies startet die Detailansicht der End-to-End-Transaktion:

![Screenshot der Detailansicht der End-to-End-Transaktion](./media/diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtern von Ereignistypen
Öffnen Sie das Blatt "Filter", und wählen Sie die Ereignistypen, die Sie anzeigen möchten. (Wenn Sie später die Filter wiederherstellen möchten, mit denen Sie das Blatt geöffnet haben, klicken Sie auf "Zurücksetzen".)

![Wählen Sie "Filter" und dann Telemetrietypen](./media/diagnostic-search/02-filter-req.png)

Die Ereignistypen sind:

* **Ablaufverfolgung** - [Diagnoseprotokolle](../../azure-monitor/app/asp-net-trace-logs.md), einschließlich TrackTrace, log4Net, NLog und Aufrufe von „System.Diagnostic.Trace“.
* **Anforderung**: Von Ihrer Serveranwendung empfangene HTTP-Anforderungen, einschließlich Seiten, Skripts, Bilder, Formatdateien und Daten. Diese Ereignisse werden verwendet, um die Übersichtsdiagramme für Anforderungen und Antworten zu erstellen.
* **Seitenaufruf** - [Vom Webclient gesendete Telemetriedaten](../../azure-monitor/app/javascript.md), die zum Erstellen von Seitenaufrufberichten verwendet werden. 
* **Benutzerdefiniertes Ereignis**: Wenn Sie Aufrufe von „TrackEvent()“ zum [Überwachen der Nutzung](../../azure-monitor/app/api-custom-events-metrics.md) eingefügt haben, können Sie diese hier suchen.
* **Ausnahme**: Nicht vom [Server abgefangene Ausnahmen](../../azure-monitor/app/asp-net-exceptions.md) und Ausnahmen, die Sie mit „TrackException()“ protokollieren können.
* **Abhängigkeit** - [Aufrufe aus Ihrer Serveranwendung](../../azure-monitor/app/asp-net-dependencies.md) an andere Dienste wie REST-APIs oder Datenbanken und AJAX-Aufrufe aus Ihrem [Clientcode](../../azure-monitor/app/javascript.md).
* **Verfügbarkeit**: Ergebnisse von [Verfügbarkeitstests](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtern von Eigenschaftswerten
Sie können Ereignisse anhand der Werte ihrer Eigenschaften filtern. Die verfügbaren Eigenschaften hängen von den ausgewählten Ereignistypen ab. 

Wählen Sie z. B. Anforderungen mit einem bestimmten Antwortcode aus. 

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/diagnostic-search/03-response500.png)

Das Auswählen keiner Werte einer bestimmten Eigenschaft hat dieselbe Wirkung wie das Auswählen aller Werte, nämlich dass das Filtern anhand dieser Eigenschaft deaktiviert wird.

### <a name="narrow-your-search"></a>Eingrenzen der Suche
Beachten Sie, dass die Zahlen rechts neben den Filterwerten anzeigen, wie viele Vorkommen es in der aktuell gefilterten Gruppe gibt. 

In diesem Beispiel ist es klar, dass die Anforderung „Rpt/Employees“ die Mehrzahl der 500-er Fehler ausmacht:

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Suchen von Ereignissen mit der gleichen Eigenschaft
Suchen Sie alle Elemente mit dem gleichen Eigenschaftswert:

![Klicken Sie mit der rechten Maustaste auf eine Eigenschaft](./media/diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Durchsuchen der Daten

> [!NOTE]
> Um komplexere Abfragen zu stellen, öffnen Sie oben auf dem Blatt „Suche“ das Feld [**Analytics**](../../azure-monitor/log-query/get-started-portal.md).
> 

Sie können alle Eigenschaftswerte nach Ausdrücken durchsuchen. Dies ist besonders nützlich, wenn Sie [benutzerdefinierte Ereignisse](../../azure-monitor/app/api-custom-events-metrics.md) mit Eigenschaftswerten geschrieben haben. 

Sie können auch einen Zeitbereich festlegen, da die Suche in einem kürzeren Bereich schneller erfolgt. 

![Diagnosesuche öffnen](./media/diagnostic-search/appinsights-311search.png)

Suchen Sie nach vollständigen Wörtern, nicht nach Teilzeichenfolgen. Verwenden Sie Anführungszeichen, um Sonderzeichen zu umschließen.

| Zeichenfolge | wurde *nicht* gefunden von | wird aber von diesen gefunden |
| --- | --- | --- |
| HomeController.Info |home<br/>controller<br/>out | homecontroller<br/>about<br/>"homecontroller.about"|
|USA|Uni<br/>ted|united<br/>states<br/>united AND states<br/>"united states"

Dies sind einige Suchausdrücke, die Sie verwenden können:

| Beispielabfrage | Wirkung |
| --- | --- |
| `apple` |Findet alle Ereignisse im Uhrzeitbereich, deren Felder den Begriff „apple“ enthalten. |
| `apple AND banana` <br/>`apple banana` |Findet Ereignisse, die beide Wörter enthalten. Verwenden Sie „AND“ in Großbuchstaben, nicht „and“. <br/>Kurzform. |
| `apple OR banana` |Findet Ereignisse, die eines der beiden Wörter enthalten. Verwenden Sie „OR“ in Großbuchstaben, nicht „or“. |
| `apple NOT banana` |Findet Ereignisse, die das eine Wort enthalten, aber nicht das andere. |

## <a name="sampling"></a>Stichproben
Wenn die Anwendung viele Telemetriedaten generiert (und Sie die ASP.NET SDK-Version 2.0.0-beta3 oder höher verwenden), reduziert das adaptive Stichprobenmodul automatisch die an das Portal gesendete Datenmenge, indem nur ein repräsentativer Bruchteil der Ereignisse gesendet wird. Ereignisse, die mit derselben Anforderung im Zusammenhang stehen, werden als Gruppe aus- oder abgewählt, sodass Sie zwischen verwandten Ereignissen navigieren können. 

[Erfahren Sie mehr über das Erstellen von Stichproben.](../../azure-monitor/app/sampling.md)

## <a name="create-work-item"></a>Erstellen eines Arbeitselements
Sie können einen Fehler mit den Details aus einem beliebigen Telemetrieelement in GitHub oder Azure DevOps erstellen. 

![Klicken Sie auf „Neues Arbeitselement“, bearbeiten Sie die Felder, und klicken Sie dann auf „OK“.](./media/diagnostic-search/42.png)

Wenn Sie diesen Vorgang zum ersten Mal ausführen, werden Sie aufgefordert, einen Link zu Ihrer Azure DevOps-Organisation und Ihrem Projekt zu konfigurieren.

![Geben Sie die URL Ihrer Azure DevOps Services-Instanz und den Projektnamen ein, und klicken Sie auf „Autorisieren“.](./media/diagnostic-search/41.png)

(Sie können den Link auch auf dem Blatt „Arbeitselemente“ konfigurieren.)

## <a name="send-more-telemetry-to-application-insights"></a>Senden weiterer Telemetriedaten an Application Insights
Neben der standardmäßig vom Application Insights SDK gesendeten Telemetriedaten können Sie folgende Aktionen ausführen:

* Erfassen von Protokollablaufverfolgungen mithilfe Ihres bevorzugten Protokollierungsframeworks in [.NET](../../azure-monitor/app/asp-net-trace-logs.md) oder [Java](../../azure-monitor/app/java-trace-logs.md). Das heißt, dass Sie Ihre Protokollablaufverfolgungen durchsuchen und mit Seitenaufrufen, Ausnahmen und anderen Ereignissen korrelieren können. 
* [Schreiben von Code](../../azure-monitor/app/api-custom-events-metrics.md) zum Senden benutzerdefinierter Ereignisse, Seitenaufrufe und Ausnahmen. 

[Erfahren Sie, wie Sie Protokolle und benutzerdefinierte Telemetrie an Application Insights senden können](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="questions"></a>FRAGEN UND ANTWORTEN
### <a name="limits"></a>Wie viele Daten werden beibehalten?

Entsprechende Informationen finden Sie unter [Zusammenfassung der Grenzwerte](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Wie kann ich die POST-Daten in meinen Serveranforderungen anzeigen?
POST-Daten werden nicht automatisch protokolliert. Sie können jedoch [TrackTrace oder Protokollaufrufe](../../azure-monitor/app/asp-net-trace-logs.md) verwenden. Fügen Sie die POST-Daten in den "message"-Parameter ein. Sie können nicht anhand der Nachricht wie anhand von Eigenschaften filtern, aber dafür ist der Größengrenzwert höher.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Nächste Schritte
* [Stellen von komplexeren Abfragen mit Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Senden von Protokollen und benutzerdefinierter Telemetrie an Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Einrichten von Tests der Verfügbarkeit und Reaktionsfähigkeit](../../azure-monitor/app/monitor-web-app-availability.md)
* [Problembehandlung](../../azure-monitor/app/troubleshoot-faq.md)
