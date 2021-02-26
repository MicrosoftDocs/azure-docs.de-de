---
title: Was ist Azure Application Insights? | Microsoft-Dokumentation
description: Anwendungsleistungsverwaltung und Nachverfolgen der Nutzung Ihrer aktiven Webanwendung.  Erkennung, Triage und Diagnose von Problemen, Analyse der App-Nutzung.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: ac6b6f2b47df0bce9ae164c83df54adfdf00b184
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575697"
---
# <a name="what-is-application-insights"></a>Was ist Application Insights?
Application Insights, ein Feature von [Azure Monitor](../overview.md), ist ein erweiterbarer Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) für Entwickler und DevOps-Profis. Überwachen Sie damit Ihre aktiven Anwendungen. Der Dienst erkennt automatisch Leistungsanomalien und verfügt über leistungsstarke Analysetools, mit denen Sie Probleme diagnostizieren und nachvollziehen können, wie Ihre App von den Benutzern verwendet wird.  Der Dienst unterstützt Sie bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit Ihrer App. Er lässt sich für Apps auf einer Vielzahl von Plattformen einsetzen. Dazu zählen unter anderem .NET, Node.js, Java und Python (lokal gehostet, als Hybridmodell oder in einer öffentlichen Cloud). Der Dienst lässt sich in Ihren DevOps-Prozess integrieren und verfügt über Verbindungspunkte mit einer Vielzahl von Entwicklungstools. Sie können Telemetriedaten von mobilen Apps durch die Integration in Visual Studio App Center überwachen und analysieren.

## <a name="how-does-application-insights-work"></a>Funktionsweise von Application Insights
Sie installieren ein kleines Instrumentierungspaket (SDK) in Ihrer Anwendung oder aktivieren Application Insights mithilfe des Application Insights-Agents (sofern [unterstützt](./platforms.md)). Die Instrumentierung überwacht Ihre App und leitet die Telemetriedaten an eine Azure Application Insights-Ressource weiter. Dabei wird eine eindeutige GUID (ein sogenannter Instrumentierungsschlüssel) verwendet.

Sie können nicht nur die Webdienstanwendung instrumentieren, sondern auch Hintergrundkomponenten und den JavaScript-Code in den Webseiten selbst. Die Anwendung und die zugehörigen Komponenten können überall ausgeführt und müssen nicht in Azure gehostet werden.

![Die Application Insights-Instrumentierung in Ihrer App sendet Telemetriedaten an Ihre Application Insights-Ressource.](./media/app-insights-overview/diagram.png)

Darüber hinaus können Sie aus Hostumgebungen Telemetriedaten abrufen, wie z.B. Leistungsindikatoren, Azure-Diagnosen oder Docker-Protokolle. Sie können auch Webtests einrichten, die in regelmäßigen Abständen synthetische Anforderungen an den Webdienst senden.

Alle diese Telemetriedatenströme sind in Azure Monitor integriert. Im Azure-Portal können Sie auf die Rohdaten leistungsstarke Analysen und Suchtools anwenden.

### <a name="whats-the-overhead"></a>Wie sieht der Aufwand aus?
Die Auswirkungen auf die Leistung Ihrer App sind gering. Aufrufe zur Nachverfolgung sind nicht blockierend und werden zusammengefasst und in einem separaten Thread gesendet.

## <a name="what-does-application-insights-monitor"></a>Was wird von Application Insights überwacht?

Application Insights ist für Entwicklerteams konzipiert und hilft Ihnen dabei, die Leistung und Verwendung Ihrer App nachzuvollziehen. Der Dienst überwacht:

* **Anforderungsraten, Antwortzeiten und Fehlerraten**: Finden Sie heraus, welche Seiten zu welchen Tageszeiten am häufigsten verwendet werden und wo Ihre Benutzer sind. Stellen Sie fest, welche Seiten die beste Leistung aufweisen. Wenn die Antwortzeiten und Fehlerraten bei mehr Anforderungen ansteigen, haben Sie möglicherweise ein Problem mit den Ressourcen. 
* **Abhängigkeitsraten, Antwortzeiten und Fehlerraten**: Finden Sie heraus, ob Sie von externen Diensten verlangsamt werden.
* **Ausnahmen**: Analysieren Sie die aggregierten Statistiken, oder wählen Sie bestimmte Instanzen aus, und untersuchen Sie die Stapelüberwachung und die zugehörigen Anforderungen. Sowohl die Server- als auch die Browserausnahmen werden gemeldet.
* **Seitenansichten und Ladeleistung**: Von den Browsern der Benutzer gemeldet.
* **AJAX-Aufrufe** von Webseiten: Raten, Antwortzeiten und Fehlerraten.
* **Anzahl von Benutzern und Sitzungen**.
* **Leistungsindikatoren** von Ihren Windows- oder Linux-Servercomputern, z.B. CPU, Arbeitsspeicher und Netzwerkverwendung. 
* **Hostdiagnose** von Docker oder Azure. 
* **Diagnose-Ablaufverfolgungsprotokolle** aus Ihrer App, sodass Sie Ablaufverfolgungsereignisse mit Anforderungen korrelieren können.
* **Benutzerdefinierte Ereignisse und Metriken**, die Sie selbst im Client- oder Servercode schreiben, um Geschäftsereignisse zu verfolgen, wie z.B. verkaufte oder gewonnene Spiele.

## <a name="where-do-i-see-my-telemetry"></a>Wo finde ich meine Telemetriedaten?

Es gibt zahlreiche Möglichkeiten, Ihre Daten zu untersuchen. Informationen finden Sie in den folgenden Artikeln:

|  |  |
| --- | --- |
| [**Intelligente Erkennung und manuelle Warnungen**](./proactive-diagnostics.md)<br/>Richten Sie automatische Warnungen ein, die sich den normalen Telemetriemustern Ihrer App anpassen und ausgelöst werden, wenn etwas nicht den üblichen Mustern entspricht. Sie können auch auf bestimmten Ebenen benutzerdefinierter oder standardmäßiger Metriken [Warnungen festlegen](../alerts/alerts-log.md). |![Beispiel für Warnungen](./media/app-insights-overview/alerts-tn.png) |
| [**Anwendungszuordnung**](./app-map.md)<br/>Untersuchen Sie die Komponenten der App mit wichtigen Metriken und Warnungen. |![Anwendungszuordnung](./media/app-insights-overview/appmap-tn.png)  |
| [**Profilerstellung**](./profiler.md)<br/>Untersuchen Sie die Ausführungsprofile von erfassten Anforderungen. |![Screenshot: Ausführungsprofile von erfassten Anforderungen](./media/app-insights-overview/profiler.png) |
| [**Nutzungsanalyse**](./usage-overview.md)<br/>Analysieren Sie Benutzersegmentierung und Vermerkdauer.|![Vermerkdauer-Tool](./media/app-insights-overview/retention.png) |
| [**Diagnosesuche für Instanzdaten**](./diagnostic-search.md)<br/>Suchen und filtern Sie Ereignisse wie Anforderungen, Ausnahmen, Abhängigkeitsaufrufe, Protokollablaufverfolgungen und Seitenaufrufe.  |![Suchen von Telemetriedaten](./media/app-insights-overview/search-tn.png) |
| [**Metrik-Explorer für aggregierte Daten**](../essentials/metrics-charts.md)<br/>Durchsuchen, filtern und segmentieren Sie aggregierte Daten wie z.B. Anforderungs-, Fehler- und Ausnahmeraten, Antwortzeiten und Seitenladezeiten. |![Metriken](./media/app-insights-overview/metrics-tn.png) |
| [**Dashboards**](./overview-dashboard.md)<br/>Kombinieren Sie Daten aus mehreren Ressourcen, und geben Sie sie für andere frei. Dies ist sehr gut für Anwendungen mit mehreren Komponenten und für die kontinuierliche Anzeige im Teamraum geeignet. |![Beispiel für Dashboards](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](./live-stream.md)<br/>Wenn Sie einen neuen Build bereitstellen, sehen Sie sich diese beinahe in Echtzeit verfügbaren Leistungsindikatoren an, um sicherzustellen, dass alles wie erwartet funktioniert. |![Beispiel für Livemetriken](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analyse**](../logs/log-query-overview.md)<br/>Beantworten Sie schwierige Fragen zur Leistung und Nutzung Ihrer App mithilfe dieser leistungsstarken Abfragesprache. |![Beispiel für Analysen](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Zeigen Sie Leistungsdaten im Code an. Wechseln Sie von Stapelüberwachungen zum Code.|![Screenshot: Ausnahmedetails in Visual Studio und Beispiel zum Wechseln zu Code von Stapelüberwachungen](./media/app-insights-overview/visual-studio-tn.png) |
| [**Debuggen von Momentaufnahmen**](./snapshot-debugger.md)<br/>Debuggen Sie aus Livevorgängen erfasste Momentaufnahmen mit Parameterwerten.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integrieren Sie Nutzungsmetriken und andere Business Intelligence-Daten.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST-API**](https://dev.applicationinsights.io/)<br/>Schreiben Sie Code zum Ausführen von Abfragen für Ihre Metriken und Rohdaten.| ![REST-API](./media/app-insights-overview/rest-tn.png) |
| [**Fortlaufender Export**](./export-telemetry.md)<br/>Exportieren Sie große Mengen von Rohdaten in den Speicher, sobald sie eintreffen. |![Exportieren](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Wie verwende ich Application Insights?

### <a name="monitor"></a>Überwachen
Installieren Sie Application Insights in Ihrer App, richten Sie [Webtests zur Verfügbarkeit](./monitor-web-app-availability.md) ein, und gehen Sie dann wie folgt vor:

* Sehen Sie sich das standardmäßige [Anwendungsdashboard](./overview-dashboard.md) für Ihren Teamraum an, damit Aspekte wie die Auslastung, Reaktionsfähigkeit und Leistung Ihrer Abhängigkeiten, Seitenladevorgänge und AJAX-Aufrufe immer im Blick behalten werden können.
* Ermitteln Sie, welche Anforderungen am langsamsten sind und am häufigsten zu Ausfällen führen.
* Verfolgen Sie den [Livestream](./live-stream.md), wenn Sie eine neue Version bereitstellen, damit Sie über Leistungsabfälle sofort informiert sind.

### <a name="detect-diagnose"></a>Erkennen, Diagnostizieren
Gehen Sie wie folgt vor, wenn Sie eine Warnung erhalten oder ein Problem auftritt:

* Bewerten Sie, wie viele Benutzer betroffen sind.
* Korrelieren Sie Ausfälle mit Ausnahmen, Abhängigkeitsaufrufen und Nachverfolgungen.
* Untersuchen Sie Profile, Momentaufnahmen, Stapelabbilder und Ablaufverfolgungsprotokolle.

### <a name="build-measure-learn"></a>Erstellen, Messen, Lernen
[Messen Sie die Effektivität](./usage-overview.md) jeder neuen Funktion, die Sie bereitstellen.

* Planen Sie eine Messung, um zu ermitteln, wie Kunden neue Funktionen der Benutzeroberfläche bzw. des Geschäftsablaufs verwenden.
* Schreiben Sie benutzerdefinierte Telemetriedaten in Ihren Code.
* Sorgen Sie dafür, dass der nächste Entwicklungszyklus auf belastbaren Informationen aus Ihren Telemetriedaten basiert.

## <a name="get-started"></a>Erste Schritte
Application Insights ist einer der vielen in Microsoft Azure gehosteten Dienste, und Telemetriedaten werden zur Analyse und Darstellung an Azure gesendet. Als Erstes benötigen Sie also ein Abonnement für [Microsoft Azure](https://azure.com). Die Registrierung ist kostenlos. Wenn Sie den [Basistarif](https://azure.microsoft.com/pricing/details/application-insights/) von Application Insights wählen, fallen Gebühren erst an, sobald Ihre Anwendung umfassender genutzt wird. Falls Ihre Organisation bereits über ein Abonnement verfügt, kann sie diesem Ihr Microsoft-Konto hinzufügen.

Es gibt mehrere Möglichkeiten für den Einstieg. Wählen Sie die Methode aus, die sich am besten für Sie eignet. Die anderen können später hinzugefügt werden.

* **Zur Laufzeit: Instrumentieren Sie Ihre Web-App auf dem Server.** Ideal für Anwendungen, die bereits bereitgestellt werden. Bei dieser Vorgehensweise sind keine Codeaktualisierungen erforderlich.
  * [**ASP.NET- oder ASP.NET Core-Anwendungen, die in Azure-Web-Apps gehostet werden**](./azure-web-apps.md)
  * [**Auf IIS gehostete ASP.NET-Anwendungen auf virtuellem Azure-Computer oder in Azure-VM-Skalierungsgruppe**](./azure-vm-vmss-apps.md)
  * [**ASP.NET-Anwendungen, die auf IIS auf einem lokalen virtuellen Computer gehostet werden**](./monitor-performance-live-website-now.md)
* **Bei der Entwicklung: Fügen Sie Ihrem Code Application Insights hinzu.** Ermöglicht Ihnen das Anpassen der Telemetriedatenerfassung und das Senden zusätzlicher Telemetriedaten.
  * [ASP.NET-Anwendungen](./asp-net.md)
  * [ASP.NET Core-Anwendungen](./asp-net-core.md)
  * [.NET-Konsolenanwendungen](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Andere Plattformen](./platforms.md)
* **[Instrumentieren Sie Ihre Webseiten](./javascript.md)** für Seitenansicht, AJAX und andere clientseitige Telemetrie.
* **[Analysieren Sie die Auslastung der mobilen App](../app/mobile-center-quickstart.md)** durch die Integration in Visual Studio App Center.
* **[Verfügbarkeitstests](./monitor-web-app-availability.md)** : Pingen Sie Ihre Website regelmäßig über unsere Server an.

## <a name="next-steps"></a>Nächste Schritte
Beginnen mit der Laufzeitmethode mit:

* [Virtueller Azure-Computer und Azure-VM-Skalierungsgruppe – Auf IIS gehostete Apps](./azure-vm-vmss-apps.md)
* [IIS-Server](./monitor-performance-live-website-now.md)
* [Azure-Web-Apps](./azure-web-apps.md)

Beginnen mit der Entwicklungszeitmethode mit:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Support und Feedback
* Fragen und Probleme:
  * [Problembehandlung][qna]
  * [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Ihre Vorschläge:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Application Insights-Blog](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

