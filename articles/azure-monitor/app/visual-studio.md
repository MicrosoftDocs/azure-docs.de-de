---
title: Debuggen in Visual Studio mit Azure Application Insights
description: Es wird beschrieben, wie Sie die Web-App-Leistungsanalyse und Diagnose während des Debuggens und in der Produktion durchführen.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: d2de29d897c84496fd44d74d88d58decbc64b7f8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532901"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Debuggen von Anwendungen mit Azure Application Insights in Visual Studio
In Visual Studio (2015 und höher) können Sie mithilfe von Telemetriedaten aus [Azure Application Insights](./app-insights-overview.md) sowohl beim Debuggen als auch in der Produktion in Ihrer ASP.NET-Web-App die Leistung analysieren und Probleme diagnostizieren.

Wenn Sie die ASP.NET-Web-App mit Visual Studio 2017 oder höher erstellt haben, ist das Application Insights-SDK bereits vorhanden. Andernfalls können Sie [Application Insights Ihrer App hinzufügen](./asp-net.md), falls Sie dies noch nicht getan haben.

Zur Liveüberwachung Ihrer App in der Produktion zeigen Sie die Application Insights-Telemetrie normalerweise im [Azure-Portal](https://portal.azure.com) an, in dem Sie Warnungen festlegen und leistungsfähige Überwachungstool anwenden können. Für das Debuggen können Sie die Telemetriedaten in Visual Studio auch durchsuchen und analysieren. Mit Visual Studio können Sie Telemetriedaten von Ihrem Produktionsstandort sowie aus Debugläufen auf Ihrem Entwicklungscomputer analysieren. Im letzteren Fall können Sie die Debugläufe auch dann analysieren, wenn Sie das SDK nicht zum Senden von Telemetriedaten an das Azure-Portal konfiguriert haben. 

## <a name="debug-your-project"></a><a name="run"></a> Debuggen des Projekts
Führen Sie Ihre Web-App im lokalen Debugmodus aus, indem Sie F5 drücken. Öffnen Sie verschiedene Seiten, um Telemetriedaten zu generieren.

In Visual Studio wird die Anzahl von Ereignissen angezeigt, die vom Application Insights-Modul in Ihrem Projekt protokolliert wurden.

![In Visual Studio wird die Schaltfläche „Application Insights“ während des Debuggens angezeigt.](./media/visual-studio/appinsights-09eventcount.png)

Klicken Sie auf diese Schaltfläche, um Ihre Telemetriedaten zu durchsuchen. 

## <a name="application-insights-search"></a>Application Insights-Suche
Im Fenster „Application Insights-Suche“ werden Ereignisse angezeigt, die protokolliert wurden. (Wenn Sie sich bei der Einrichtung von Application Insights bei Azure angemeldet haben, können Sie die gleichen Ereignisse im Azure-Portal durchsuchen.)

![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights > Durchsuchen“ aus.](./media/visual-studio/34.png)

> [!NOTE] 
> Klicken Sie nach dem Aktivieren bzw. Deaktivieren von Filtern am Ende des Textsuchfelds auf die Schaltfläche „Suchen“.
>

Die Freitextsuche funktioniert in allen Feldern in den Ereignissen. Suchen Sie z. B. einen Teil der URL einer Seite, den Wert einer Eigenschaft, z. B. Ort des Kunden, oder bestimmte Wörter in einem Ablaufverfolgungsprotokoll.

Klicken Sie auf ein beliebiges Ereignis, um seine detaillierten Eigenschaften anzuzeigen.

Für Anforderungen an Ihre Web-App können Sie sich bis zum Code durchklicken.

![Klicken unter „Anforderungsdetails“ bis zum Code](./media/visual-studio/31.png)

Außerdem können Sie zum Diagnostizieren von fehlerhaften Anforderungen oder von Ausnahmen auch verwandte Elemente öffnen.

![Scrollen zu verwandten Elementen unter „Anforderungsdetails“](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Anzeigen von Ausnahmen und nicht erfolgreichen Anforderungen
Ausnahmeberichte werden im Suchfenster angezeigt. (Bei einigen älteren Typen der ASP.NET-Anwendung müssen Sie die [Ausnahmeüberwachung einrichten](./asp-net-exceptions.md), um Ausnahmen anzuzeigen, die vom Framework verarbeitet werden.)

Klicken Sie auf eine Ausnahme, um eine Stapelüberwachung zu erhalten. Wenn der Code der App in Visual Studio geöffnet ist, können Sie sich durch die Stapelüberwachung bis zur entsprechenden Zeile im Code klicken.

![Screenshot des „About“-Objekts in einer Stapelüberwachung](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Anzeigen von Zusammenfassungen von Anforderungen und Ausnahmen im Code
In der Codebereichszeile wird oberhalb jeder Handlermethode die Anzahl von Anforderungen und Ausnahmen angezeigt, die von Application Insights innerhalb der letzten 24 Stunden protokolliert wurden.

![Screenshot einer Ausnahme in einem Kontextdialogfeld](./media/visual-studio/21.png)

> [!NOTE] 
> CodeLens zeigt Application Insights-Daten nur an, wenn Sie Ihre [App zum Senden von Telemetriedaten an das Application Insights-Portal konfiguriert haben](./asp-net.md).
>

[Weitere Informationen zu Application Insights in CodeLens](./visual-studio-codelens.md)

## <a name="trends"></a>Trends
Mit dem Tool Trends können Sie visualisieren, wie sich Ihre App im Laufe der Zeit verhält. 

Wählen Sie auf der Application Insights-Symbolleiste oder im Application Insights-Suchfenster die Schaltfläche **Telemetrietrends untersuchen** . Wählen Sie eine der fünf allgemeinen Abfragen aus, um zu beginnen. Sie können unterschiedliche Datasets basierend auf Telemetrietypen, Zeiträumen und anderen Eigenschaften analysieren. 

Wählen Sie zum Ermitteln von Anomalien in Ihren Daten in der Dropdownliste „Ansichtstyp“ eine Anomalieoption. Mit den Filteroptionen am unteren Rand des Fensters ist es einfach, bestimmte Teilmengen Ihrer Telemetriedaten anzuzeigen.

![Trends](./media/visual-studio/51.png)

[Weitere Informationen zu Trends](./visual-studio-trends.md)

## <a name="local-monitoring"></a>Lokale Überwachung
(Visual Studio 2015 Update 2) Falls Sie das SDK nicht zum Senden von Telemetriedaten an das Application Insights-Portal konfiguriert haben (sodass kein Instrumentationsschlüssel in „ApplicationInsights.config“ enthalten ist), werden im Diagnosefenster Telemetriedaten aus der letzten Debugsitzung angezeigt. 

Dies ist wünschenswert, wenn Sie bereits eine frühere Version der Anwendung veröffentlicht haben. Die Telemetriedaten aus den Debugsitzungen und die Telemetriedaten der veröffentlichten App im Application Insights-Portal müssen getrennt behandelt werden.

Dies ist ebenfalls hilfreich, wenn Sie über [benutzerdefinierte Telemetriedaten](./api-custom-events-metrics.md) verfügen, die Sie vor dem Senden an das Portal debuggen möchten.

* *Zunächst habe ich Application Insights vollständig für das Senden von Telemetriedaten an das Portal konfiguriert. Aber nun möchte ich die Telemetriedaten nur in Visual Studio anzeigen.*
  
  * In den Einstellungen des Suchfensters steht eine Option zum Durchsuchen der lokalen Diagnosen zur Verfügung, auch wenn Ihre App Telemetriedaten an das Portal sendet.
  * Damit keine Telemetriedaten mehr an das Portal gesendet werden, kommentieren Sie die Zeile `<instrumentationkey>...` in „ApplicationInsights.config“ aus. Wenn Sie bereit sind, Telemetriedaten erneut an das Portal zu senden, heben Sie die Auskommentierung auf.


## <a name="next-steps"></a>Nächste Schritte

 * **[Arbeiten mit dem Application Insights-Portal](./overview-dashboard.md)** . Zeigen Sie Dashboards, leistungsstarke Diagnose- und Analysetools, Warnungen, eine Livezuordnung der Abhängigkeiten Ihrer Anwendung sowie exportierte Telemetriedaten an. 

