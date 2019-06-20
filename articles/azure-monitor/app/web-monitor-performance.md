---
title: Überwachen der Integrität und Nutzung Ihrer Anwendung mit Application Insights
description: Erste Schritte mit Application Insights. Analysieren Sie die Auslastung, Verfügbarkeit und Leistung Ihres lokalen oder Microsoft Azure-Anwendungen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d7b8037f50fc4877fe233925f3e922648169f73b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60373085"
---
# <a name="monitor-performance-in-web-applications"></a>Leistung in Webanwendungen überwachen


Stellen sie sicher, dass Ihre Anwendung optimal funktioniert, und stellen Sie Fehler umgehend fest. [Application Insights][start] informiert Sie über alle Leistungsprobleme und Ausnahmefälle. So können Sie die Ursachen schnell ermitteln und diagnostizieren.

Application Insights kann Java- und ASP.NET-Webanwendungen und -Dienste sowie WCF-Dienste überwachen. Das Hosting kann lokal, auf virtuellen Computern oder als Microsoft Azure-Websites erfolgen. 

Auf Clientseite kann Application Insights Telemetriedaten von Webseiten und eine Vielzahl von Geräten sammeln, einschließlich iOS-, Android- und Windows Store-Apps.

## <a name="setup"></a>Einrichten der Leistungsüberwachung
Falls Sie Application Insights Ihrem Projekt noch nicht hinzugefügt haben (d. h., wenn es nicht über ApplicationInsights.config verfügt), gehen Sie nach einer der folgenden Methoden vor, um zu beginnen:

* [ASP.NET-Web-Apps](../../azure-monitor/app/asp-net.md)
  * [Ausnahmeüberwachung hinzufügen](../../azure-monitor/app/asp-net-exceptions.md)
  * [Abhängigkeitsüberwachung hinzufügen](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE-Web-Apps](../../azure-monitor/app/java-get-started.md)
  * [Abhängigkeitsüberwachung hinzufügen](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Untersuchen von Leistungsmetriken
Navigieren Sie im [Azure-Portal](https://portal.azure.com)zu der Application Insights-Ressource, die Sie für Ihre Anwendung eingerichtet haben. Das Blatt "Übersicht" zeigt grundlegende Leistungsdaten:

Klicken Sie auf ein beliebiges Diagramm, um weitere Details und Ergebnisse über einen längeren Zeitraum anzuzeigen. Klicken Sie beispielsweise auf die Kachel "Requests", und wählen Sie dann einen Zeitraum aus.

![Zu mehr Daten durchklicken und einen Zeitraum auswählen](./media/web-monitor-performance/appinsights-48metrics.png)

Klicken Sie auf ein Diagramm, um die anzuzeigenden Metriken auszuwählen, oder fügen Sie ein neues Diagramm hinzu, und wählen Sie dann die Metriken aus:

![Auf Graph klicken, um Metrik auszuwählen](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Deaktivieren Sie alle Metriken**, um die insgesamt verfügbare Auswahl anzuzeigen. Die Metriken werden in Gruppen unterteilt. Wenn ein Mitglied einer Gruppe ausgewählt wird, werden nur die weiteren Mitglieder dieser Gruppe angezeigt.

## <a name="metrics"></a>Was bedeutet was? Leistungskacheln und Berichte
Ihnen stehen verschiedene Leistungsmetriken zur Verfügung. Lassen Sie uns mit denen beginnen, die standardmäßig im Anwendungsblatt angezeigt werden.

### <a name="requests"></a>Requests
Die in einem angegebenen Zeitraum empfangenen HTTP-Anforderungen. Vergleichen Sie den Wert mit den Ergebnissen anderer Berichte, um das Verhalten Ihrer Anwendung mit wechselnder Last zu beurteilen.

HTTP-Anforderungen umfassen alle GET- oder POST-Anforderungen für Seiten, Daten und Bilder.

Klicken Sie auf die Kachel, um Zählwerte für bestimmte URLs zu erhalten.

### <a name="average-response-time"></a>Average response time
Misst die Zeit zwischen dem Eingang einer Webanforderung bei Ihrer Anwendung und der zurückgegebenen Antwort.

Die Punkte zeigen einen variablen Durchschnittswert. Falls viele Anforderungen eingehen, gibt es ggf. vom Durchschnitt deutlich nach oben oder unten abweichende Spitzen im Graphen.

Achten Sie auf ungewöhnliche Spitzen. Im Allgemeinen können Sie mit einer längeren Antwortzeit bei steigender Zahl der Anforderungen rechnen. Falls der Anstieg nicht proportional ist, gelangt Ihre Anwendung möglicherweise an Ressourcengrenzen wie die CPU-Leistung oder die Kapazität eines verwendeten Diensts.

Klicken Sie auf die Kachel, um Zeiten für bestimmte URLs zu erhalten.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Slowest requests
![](./media/web-monitor-performance/appinsights-44slowest.png)

Zeigt, welche Anforderungen möglicherweise eine Leistungsfeinabstimmung erfordern.

### <a name="failed-requests"></a>Failed requests
![](./media/web-monitor-performance/appinsights-46failed.png)

Zählwert von Anforderungen, die nicht abgefangene Ausnahmefehler verursacht haben.

Klicken Sie auf die Kachel, um Details zu bestimmten Fehlern anzuzeigen, und wählen Sie einzelne Anforderungen aus, um die jeweiligen Details anzuzeigen. 

Nur eine repräsentative Menge an Fehler wird zur individuellen Überprüfung zurückgehalten.

### <a name="other-metrics"></a>Other metrics
Um andere verfügbare Metriken aufzurufen, klicken Sie auf einen Graphen und wählen dann alle Metriken ab. Daraufhin werden alle verfügbaren Metriken angezeigt. Klicken Sie auf (i), um die jeweilige Definition der Metrik anzuzeigen.

![Alle Metriken abwählen, um alle verfügbaren anzuzeigen](./media/web-monitor-performance/appinsights-62allchoices.png)

Wenn Sie eine Metrik auswählen, werden alle anderen deaktiviert, die nicht im selben Diagramm angezeigt werden können.

## <a name="set-alerts"></a>Festlegen von Benachrichtigungen
Fügen Sie eine Benachrichtigung hinzu, wenn Sie per E-Mail über ungewöhnliche Werte einer beliebigen Metrik informiert werden möchten. Sie können auswählen, ob die E-Mail an die Kontoadministratoren oder an bestimmte E-Mail-Adressen gesendet wird.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Legen Sie die Ressource vor den anderen Eigenschaften fest. Wählen Sie nicht die Webtestressourcen, wenn Sie Benachrichtigungen für Leistungs- oder Nutzungsmetriken festlegen möchten.

Achten Sie auf die Einheiten, die beim Eingeben des Schwellenwerts gefordert sind.

*Ich sehe keine Schaltfläche zum Hinzufügen von Benachrichtigungen.* – Handelt es sich um ein Gruppenkonto, für das Sie nur über schreibgeschützten Zugriff verfügen? Wenden Sie sich an den Kontoadministrator.

## <a name="diagnosis"></a>Diagnostizieren von Problemen
Im Folgenden finden Sie einige Tipps zum Feststellen und Diagnostizieren von Leistungsproblemen:

* Richten Sie [Webtests][availability] ein, um benachrichtigt zu werden, falls Ihre Website nicht erreichbar ist oder fehlerhaft bzw. langsam reagiert. 
* Vergleichen Sie den Request-Zählwert mit anderen Metriken, um festzustellen, ob Fehler oder langsame Reaktionen mit der Last zusammenhängen.
* [Fügen Sie Ihrem Code Trace-Anweisungen hinzu bzw. suchen Sie diese][diagnostic], um Probleme besser einzugrenzen.
* Überwachen Sie den Betrieb Ihrer Web-App mithilfe von [Live Metrics Stream][livestream].
* Erfassen Sie den Zustand Ihrer .NET-Anwendung mithilfe des [Momentaufnahmedebuggers][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Auffinden und Beseitigen von Leistungsengpässen über die Oberfläche für die Leistungsuntersuchung

Sie können die Oberfläche für die Leistungsuntersuchung verwenden, um langsame Vorgänge in Ihrer Web-App zu überprüfen. Sie können schnell einen bestimmten Vorgang auswählen und [Profiler](../../azure-monitor/app/profiler.md) verwenden, um die Ursache für die geringe Leistung bis zum Code nachzuverfolgen. Mit der neuen Ansicht der Verteilung der Dauer, die für den ausgewählten Vorgang angezeigt wird, können Sie auf einen Blick bewerten, wie schlecht das Benutzererlebnis für Ihre Kunden tatsächlich ist. Sie können für jeden langsamen Vorgang anzeigen, wie viele Benutzerinteraktionen betroffen sind. Im folgenden Beispiel betrachten wir das Benutzererlebnis für den Vorgang „GET Customers/Details“. In der Verteilung der Dauer sehen wir drei Spitzenwerte. Der Wert ganz links beträgt ca. 400 ms und stellt eine sehr gute Reaktion dar. Der mittlere Wert liegt bei etwa 1,2 s und steht für ein mittelmäßiges Benutzererlebnis. Bei 3,6 s stellen wir eine weitere kleine Spitze fest, die das Benutzererlebnis im 99. Perzentil repräsentiert. Dieser Wert wird wahrscheinlich dazu führen, dass Ihre Kunden Ihre App enttäuscht verlassen. Diese Reaktion ist zehnmal langsamer als die sehr gute Reaktion für den gleichen Vorgang. 

![Drei Spitzenwerte für die Dauer von „GET Customers/Details“](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Um ein besseres Gefühl für das Benutzererlebnis bei diesem Vorgang zu entwickeln, können wir einen größeren Zeitbereich auswählen. Wir können die Auswahl dann auch auf ein bestimmtes Zeitfenster begrenzen, in dem der Vorgang langsam ausgeführt wurde. Im folgenden Beispiel haben wir den Zeitbereich von 24 Stunden (Standardeinstellung) in 7 Tage geändert und dann das Zeitfenster von 21:47 bis 00:47 zwischen Dienstag, dem 12., und Mittwoch, dem 13., ausgewählt. Auf der rechten Seite wurde sowohl die Verteilung der Dauer als auch die Anzahl von Stichproben und Profiler-Ablaufverfolgungen aktualisiert.

![Drei Spitzenwerte für die Dauer von „GET Customers/Details“ im 7-Tage-Zeitbereich mit einem Zeitfenster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Um die Ansicht auf die langsamen Reaktionen einzugrenzen, zeigen wir als Nächstes die Werte für die Dauer an, die zwischen dem 95. und dem 99. Perzentil liegen. Diese Werte repräsentieren die 4% Benutzerinteraktionen, die eine langsame Reaktion aufgewiesen haben.

![Drei Spitzenwerte für die Dauer von „GET Customers/Details“ im 7-Tage-Zeitbereich mit einem Zeitfenster](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Nun können wir uns entweder die repräsentativen Stichproben ansehen, indem wir auf die Schaltfläche „Stichproben“ klicken, oder die repräsentativen Profiler-Ablaufverfolgungen anzeigen, indem wir auf die Schaltfläche „Profiler-Ablaufverfolgungen“ klicken. Dieses Beispiel enthält vier Ablaufverfolgungen, die für „GET Customers/Details“ in dem Zeitfenster und dem Bereich der Dauer gesammelt wurden, das bzw. der von Interesse ist.

In einigen Fällen liegt das Problem nicht in Ihrem Code, sondern in einer Abhängigkeit, die von Ihrem Code aufgerufen wird. Sie können in der Ansicht der Leistungsselektierung zur Registerkarte „Abhängigkeiten“ wechseln, um solche Abhängigkeiten mit langsamer Reaktion zu untersuchen. In der Leistungsansicht werden standardmäßig durchschnittliche Trends angezeigt. Von größerem Interesse ist aber das 95. Perzentil (oder das 99., wenn Sie einen ausgereiften Dienst überwachen). Im folgenden Beispiel konzentrieren wir uns auf die langsame Abhängigkeit eines Azure-Blobs, in dem wir „PUT fabrikamaccount“ aufrufen. Die guten Erlebnisse bewegen sich um 40 ms herum. Die langsamen Aufrufe der gleichen Abhängigkeit sind dreimal langsamer und bewegen sich um 120 ms herum. Es müssen sich nicht viele dieser Aufrufe summieren, um zu bewirken, dass der entsprechende Vorgang sich erheblich verlangsamt. Sie können – ebenso wie auf der Registerkarte „Vorgänge“ – Details der repräsentativen Stichproben und Profiler-Ablaufverfolgungen anzeigen.

![Drei Spitzenwerte für die Dauer von „GET Customers/Details“ im 7-Tage-Zeitbereich mit einem Zeitfenster](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Auf der Oberfläche für die Leistungsuntersuchung werden relevante Erkenntnisse neben dem Stichprobensatz angezeigt, den Sie untersuchen. Die beste Methode, alle verfügbaren Einblicke anzuzeigen, besteht darin, zu einem 30-Tage-Zeitbereich zu wechseln und dann die Option „Gesamt“ auszuwählen, um Einblicke in alle Vorgänge des vergangenen Monats zu erhalten.

![Drei Spitzenwerte für die Dauer von „GET Customers/Details“ im 7-Tage-Zeitbereich mit einem Zeitfenster](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Nächste Schritte
[Webtests][availability]: Lassen Sie in regelmäßigen Abständen aus aller Welt Webanforderungen an Ihre Anwendung senden.

[Diagnostische Spuren protokollieren und suchen][diagnostic]: Fügen Sie Trace-Aufrufe ein, und durchsuchen Sie die Ergebnisse, um Probleme einzugrenzen.

[Nutzungsnachverfolgung][usage]: Erfahren Sie, wie Ihre Anwendung genutzt wird.

[Problembehandlung][qna] und Fragen und Antworten



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



