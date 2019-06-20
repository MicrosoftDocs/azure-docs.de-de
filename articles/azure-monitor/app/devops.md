---
title: Leistungsüberwachung für Webanwendungen – Azure Application Insights | Microsoft-Dokumentation
description: Application Insights im DevOps-Zyklus
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: 24b0bc01b5cb4f1d2696a7c9526d586c9b42d0fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60899718"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Eingehende Diagnose für Web-Apps und Dienste mit Application Insights
## <a name="why-do-i-need-application-insights"></a>Wofür benötigen Sie Application Insights?
Application Insights überwacht Ihre Web-App während der Ausführung. Der Dienst informiert Sie über Fehler und Leistungsprobleme und hilft Ihnen dabei, zu analysieren, wie Kunden Ihre App nutzen. Der Dienst funktioniert mit Apps, die auf unterschiedlichen Plattformen ausgeführt werden (ASP.NET, Java EE, Node.js usw.) und wird entweder in der Cloud oder lokal gehostet. 

![Komplexitätsaspekte bei der Bereitstellung von Web-Apps](./media/devops/010.png)

Es ist von essenzieller Bedeutung, eine moderne Anwendung während der Ausführung zu überwachen. Am wichtigsten ist natürlich, dass Sie Fehler erkennen, bevor die meisten Ihrer Kunden sie entdecken. Sie möchten auch Leistungsprobleme ermitteln und beheben, die zwar keine katastrophalen Folgen haben, aber Ihre Anwendung verlangsamen oder andere Unannehmlichkeiten für Ihre Benutzer verursachen. Und wenn das System zu Ihrer Zufriedenheit ausgeführt wird, möchten Sie wissen, wie die Benutzer die Anwendung nutzen: Verwenden sie die neuesten Features? Sind sie damit erfolgreich?

Moderne Webanwendungen werden in einem Zyklus kontinuierlicher Bereitstellung entwickelt: Sie veröffentlichen ein neues Feature oder eine Verbesserung. Sie beobachten, wie gut das Feature oder die Verbesserung für die Benutzer funktioniert. Sie planen den nächsten Entwicklungsschritt basierend auf diesen Informationen. Die Beobachtungsphase ist ein wichtiger Bestandteil dieses Zyklus. Application Insights stellt Tools bereit, mit denen Sie die Leistung und Nutzung einer Webanwendung überwachen können.

Der wichtigste Aspekt dieses Prozesses ist die Fehlerermittlung und -diagnose. Wenn bei einer Anwendung ein Fehler auftritt, bedeutet dies zumeist Umsatzverlust. Die wichtigste Funktion eines Überwachungsframeworks ist es daher, Fehler zuverlässig zu erkennen, Sie sofort zu benachrichtigen und Ihnen die Informationen bereitzustellen, die Sie zur Diagnose des Problems benötigen. Genau das macht Application Insights.

### <a name="where-do-bugs-come-from"></a>Wo kommen Fehler her?
Fehler in Websystemen entstehen in der Regel aufgrund von Konfigurationsproblemen oder unzureichenden Interaktionen zwischen den vielen beteiligten Komponenten. Die erste Aufgabe beim Beheben von Problemen mit Livewebsites ist es daher, den Ursprung des Fehlers zu identifizieren: Welche Komponente oder Beziehung ist die Ursache?

Einige von uns können sich noch an einfachere Zeiten erinnern, als ein Computerprogramm einfach auf einem Computer ausgeführt wurde. Damals testeten die Entwickler ein Programm gründlich, bevor es ausgeliefert wurde. Und nach der Auslieferung haben sie kaum jemals wieder über das Programm nachgedacht. Die Benutzer mussten sich mit den verbleibenden Fehlern einfach jahrelang abfinden. 

Heute ist das ganz anders. Ihre App kann auf einer Unmenge verschiedener Geräte ausgeführt werden, und es kann schwierig sein, das exakt gleiche Verhalten auf jedem Gerät zu gewährleisten. Indem Apps in der Cloud gehostet werden, können Fehler schnell behoben werden. Gleichzeitig unterliegen Apps dadurch einem ständigen Wettbewerb, und die Benutzer erwarten, dass häufig neue Features entwickelt werden. 

Unter diesen Umständen sind automatisierte Unittests die einzige Möglichkeit, die Fehleranzahl unter Kontrolle zu halten. Es wäre unmöglich, sämtliche Aspekte bei jeder Bereitstellung erneut manuell zu testen. Unittests sind heute ein alltäglicher Teil des Buildprozesses. Tools wie z.B. Xamarin Test Cloud bieten automatisierte Tests der Benutzeroberfläche in verschiedenen Browserversionen. Durch Einsatz solcher Testsysteme hoffen wir, die Menge an Fehlern in einer App auf ein Minimum zu reduzieren.

Webanwendungen verfügen in der Regel über viele Livekomponenten. Zusätzlich zum Client (in einer Browser- oder Geräte-App) und dem Webserver besteht wahrscheinlich ein hoher Verarbeitungsbedarf im Back-End. Möglicherweise handelt es sich beim Back-End um eine Komponentenpipeline oder verschiedene voneinander unabhängige, aber ineinandergreifende Bestandteile. Wahrscheinlich können Sie viele dieser Bestandteile nicht selbst steuern, da es sich um externe Dienste handelt, die Sie benötigen, aber nicht kontrollieren können.

In solchen Konfigurationen kann es schwierig und unwirtschaftlich sein, jeden möglichen Fehlermodus vorherzusehen oder zu testen – das kann nur im Livesystem selbst erfolgen. 

### <a name="questions-"></a>Fragen...
Beim Entwickeln eines Websystems stellen sich folgende Fragen:

* Stürzt meine App ab? 
* Was passiert genau? Wenn bei einer Anforderung ein Fehler aufgetreten ist, möchte ich wissen, wie es dazu gekommen ist. Wir benötigen eine Ereignisablaufverfolgung.
* Ist meine App schnell genug? Wie lange dauert die Antwort auf typische Anforderungen?
* Kann der Server die Last bewältigen? Wenn die Anforderungsrate steigt, bleibt die Antwortzeit gleich?
* Wie reaktionsschnell sind die Abhängigkeiten – REST-APIs, Datenbanken und weitere Komponenten, die meine App aufruft? Insbesondere: Wenn das System langsam reagiert, liegt das an meiner Komponente oder daran, dass ein anderes Element langsam antwortet?
* Ist meine App betriebsbereit oder ausgefallen? Ist sie von überall auf der Welt aus zugänglich? Ich muss informiert werden, wenn sie ausfällt.
* Was ist die Ursache? Liegt der Fehler in meiner Komponente oder einer Abhängigkeit? Liegt ein Kommunikationsproblem vor?
* Wie viele Benutzer sind betroffen? Wenn mehrere Probleme zu lösen sind – welches ist das wichtigste?

## <a name="what-is-application-insights"></a>Was ist Application Insights?
![Grundlegender Workflow von Application Insights](./media/devops/020.png)

1. Application Insights instrumentiert Ihre App und sendet während der Ausführung Telemetriedaten über Ihre App. Sie können das Application Insights SDK in die App integrieren oder die Instrumentierung zur Laufzeit anwenden. Die erste Methode ist flexibler, weil Sie den regulären Modulen Ihre eigene Telemetrie hinzufügen können.
2. Die Telemetriedaten werden an das Application Insights-Portal gesendet und dort gespeichert und verarbeitet. (Application Insights wird zwar in Microsoft Azure gehostet, kann aber jede Web-App überwachen, nicht nur Azure-Apps.)
3. Die Telemetriedaten werden in Form von Diagrammen und Tabellen zu Ereignissen angezeigt.

Es gibt zwei Arten von Telemetriedaten: aggregierte Daten und unformatierte Instanzdaten. 

* Instanzdaten können z.B. einen Bericht zu einer Anforderung umfassen, die von Ihrer Web-App empfangen wurde. Sie können die Details zu einer Anforderung über das Suchtool im Application Insights-Portal suchen und überprüfen. Die Instanz umfasst beispielsweise folgende Daten: Antwortzeit der App nach der Anforderung, angeforderte URL, ungefährer Standort des Clients sowie weitere Daten.
* Aggregierte Daten enthalten z.B. die Anzahl von Ereignissen pro Zeiteinheit, sodass Sie die Anforderungsrate mit den Antwortzeiten vergleichen können. Zu diesen Daten gehören auch Durchschnittswerte von Metriken, wie etwa Antwortzeiten nach Anforderungen.

Die Daten lassen sich in folgende Hauptkategorien unterteilen:

* Anforderungen an Ihre App (üblicherweise HTTP-Anforderungen) mit Daten zu URL, Antwortzeit und Meldung zu Erfolg bzw. Fehler.
* Abhängigkeiten: REST- und SQL-Aufrufe Ihrer App, ebenfalls mit URI, Antwortzeiten und Erfolgsmeldung.
* Ausnahmen, einschließlich Stapelüberwachungen.
* Seitenzugriffsdaten, die aus den Browsern der Benutzer stammen.
* Metriken wie z.B. Leistungsindikatoren sowie von Ihnen selbst erstellte Metriken. 
* Benutzerdefinierte Ereignisse, die Sie zum Nachverfolgen von geschäftlichen Ereignissen verwenden können.
* Protokollablaufverfolgungen, die zum Debuggen verwendet werden.

## <a name="case-study-real-madrid-fc"></a>Fallstudie: Real Madrid CF
Der Webdienst von [Real Madrid](https://www.realmadrid.com/) wird von 450 Millionen Fans auf der ganzen Welt genutzt. Fans greifen sowohl über Webbrowser als auch über die mobilen Apps des Vereins darauf zu. Sie können dort nicht nur Eintrittskarten kaufen, sondern auch auf Informationen und Videoclips zu Ergebnissen, Spielern und den nächsten Begegnungen zugreifen. Sie können mithilfe von Filtern z.B. nach der Anzahl der erzielten Tore suchen. Es gibt auch Links zu sozialen Medien. Das Benutzererlebnis ist hochgradig personalisiert und als bidirektionale Kommunikation gestaltet, um die Fans einzubeziehen.

Die Lösung [ist ein System aus Diensten und Anwendungen in Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Skalierbarkeit ist eine wesentliche Voraussetzung: Der Datenverkehr variiert und kann vor, während und nach Spielen sehr hohe Volumen erreichen.

Für Real Madrid ist es von entscheidender Bedeutung, die Leistung des Systems zu überwachen. Azure Application Insights bietet einen umfassenden Überblick über das gesamte System, um eine zuverlässige und leistungsstarke Dienstausführung sicherzustellen. 

Der Verein erhält auch detaillierte Erkenntnisse über seine Fans: wo leben sie (nur 3% in Spanien), für welche Spieler, bisherigen Ergebnisse und bevorstehenden Begegnungen interessieren sie sich, wie reagieren sie auf Spielergebnisse?

Der Großteil dieser Telemetriedaten wird automatisch ohne zusätzlichen Code erfasst – dadurch wurde die Lösung vereinfacht und die operative Komplexität reduziert.  Bei Real Madrid verarbeitet Application Insights jeden Monat 3,8 Milliarden Telemetriedaten.

Real Madrid verwendet das Power BI-Modul zur Anzeige dieser Telemetriedaten.

![Power BI-Ansichten von Application Insights-Telemetrie](./media/devops/080.png)

## <a name="smart-detection"></a>Intelligente Erkennung
Die [proaktive Diagnose](../../azure-monitor/app/proactive-diagnostics.md) ist ein neues Feature. Application Insights erkennt automatisch eine ungewöhnliche Zunahme der Fehlerraten in Ihrer App und informiert Sie darüber, ohne dass Sie spezielle Konfigurationseinstellungen vornehmen müssen. Der Dienst ist intelligent genug, gelegentliche Fehler sowie eine Fehlerzunahme zu ignorieren, die einfach einer Zunahme an Anforderungen entspricht. Wenn z.B. in einem Dienst, von dem Ihre App abhängig ist, ein Fehler auftritt oder der neue, von Ihnen gerade bereitgestellte Build nicht gut funktioniert, werden Sie per E-Mail darüber informiert. (Mithilfe von Webhooks können Sie andere Apps auslösen.)

Ein weiterer Aspekt: Dieses Feature führt täglich eine detaillierte Analyse Ihrer Telemetrie durch und sucht nach ungewöhnlichen Leistungsmustern, die schwer zu erkennen sind. Das Feature kann beispielsweise unzureichende Leistungsdaten in Zusammenhang mit einer bestimmten geografischen Region oder einer bestimmten Browserversion finden.

In beiden Fällen informiert Sie die Warnung nicht nur über die gefundenen Symptome, sondern stellt Ihnen auch Daten zur Verfügung, die Sie für die Diagnose des Problems benötigen, wie etwa relevante Ausnahmeberichte.

![E-Mail der proaktiven Diagnose](./media/devops/030.png)

Ein Erfahrungsbericht von Samtec: „Als wir kürzlich einige Features umstellten, fanden wir eine nicht ausreichend skalierte Datenbank, die an die Grenzen ihrer Ressourcen gelangt war und Timeouts verursachte. Als wir das Problem untersuchten, kamen buchstäblich gleichzeitig proaktive Warnungen herein – nahezu in Echtzeit, wie angekündigt. Dank dieser Warnung, zusammen mit den Warnungen der Azure-Plattform, konnten wir das Problem fast sofort beheben. Gesamtausfallzeit: keine 10 Minuten.“

## <a name="live-metrics-stream"></a>Live Metrics Stream
Die Bereitstellung des neuesten Builds kann eine nervenaufreibende Angelegenheit sein. Wenn es Probleme gibt, sollten Sie sofort darüber Bescheid wissen, damit Sie die Bereitstellung ggf. rückgängig machen können. Live Metrics Stream zeigt Ihnen wichtige Metriken innerhalb von etwa einer Sekunde an.

![Livemetriken](./media/devops/0040.png)

Zudem können Sie sofort eine Stichprobe der Fehler oder Ausnahmen zu überprüfen.

![Live-Fehlerereignisse](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Anwendungszuordnung
Die Anwendungszuordnung ermittelt automatisch Ihre Anwendungstopologie und legt die Leistungsinformationen darüber, sodass Sie Leistungsengpässe und problematische Datenflüsse in Ihrer gesamten verteilten Umgebung leicht identifizieren können. Sie ermöglicht die Ermittlung von Anwendungsabhängigkeiten in Azure-Diensten. Sie können das Problem untersuchen und herausfinden, ob es durch Code oder Abhängigkeiten entsteht. Über die gleiche Benutzeroberfläche können Sie eine Komponente zur Diagnose aufrufen. Ein Beispiel: Bei Ihrer Anwendung tritt aufgrund eines Leistungsabfalls in der SQL-Schicht ein Fehler auf. Mit der Anwendungszuordnung sehen Sie ihn direkt und können den SQL-Indexratgeber oder Query Insights aufrufen.

![Anwendungszuordnung](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
Mit [Analytics](../../azure-monitor/app/analytics.md)können Sie beliebige Abfragen in einer leistungsfähigen SQL-ähnlichen Sprache schreiben.  Die Diagnose über den gesamten App-Stapel hinweg wird erheblich vereinfacht, da verschiedene Perspektiven miteinander verknüpft werden und Sie die richtigen Fragen stellen können, um die Leistung des Diensts mit Geschäftsmetriken und Kundenzufriedenheit zu korrelieren. 

Sie können alle im Portal gespeicherten Telemetrieinstanz- und Metrikrohdaten abfragen. Die Sprache enthält filter-, join- und aggregation-Operationen sowie weitere Vorgänge. Sie können Felder berechnen und statistische Analysen durchführen. Die Daten können in tabellarischer oder grafischer Form visualisiert werden.

![Analytics-Abfrage und Ergebnisdiagramm](./media/devops/0025.png)

Folgende Vorgänge werden erheblich vereinfacht:

* Segmentieren Sie die Anforderungsleistungsdaten Ihrer Anwendung nach Kundenebene, um mehr über das Kundenerlebnis mit Ihrer Anwendung zu erfahren.
* Suchen Sie während der Überprüfung der Livewebsite nach bestimmten Fehlercodes oder benutzerdefinierten Ereignisnamen.
* Zeigen Sie Details zur App-Nutzung durch bestimmte Kunden an, um herauszufinden, wie Features abgerufen und angenommen werden.
* Verfolgen Sie Sitzungen und Antwortzeiten für bestimmte Benutzer, damit Support- und Betriebsteams den Kunden sofortigen Support bieten können.
* Ermitteln Sie häufig genutzte App-Features, um Fragen zur Priorisierung von Features beantworten zu können.

Ein Erfahrungsbericht von DNN: „Application Insights hat uns den fehlenden Teil der Formel geliefert – jetzt können wir Daten nach Bedarf kombinieren, sortieren, abfragen und filtern. Unser Team konnte seine Fähigkeiten und Erfahrungen in Bezug auf das Suchen von Daten optimal in diese leistungsstarke Abfragesprache einbringen – so konnten wir Erkenntnisse gewinnen und Probleme lösen, von denen wir nicht einmal wussten, dass wir sie hatten. Aus Fragen, die mit *,Ich frage mich, ob...‘* beginnen, entstehen viele interessante Antworten.“

## <a name="development-tools-integration"></a>Integration von Entwicklungstools
### <a name="configuring-application-insights"></a>Konfigurieren von Application Insights
Visual Studio und Eclipse bieten Tools, mit denen Sie die richtigen SDK-Pakete für die von Ihnen entwickelten Projekte konfigurieren können. Zum Hinzufügen von Application Insights gibt es einen Menübefehl.

Wenn Sie ein Ablaufprotokollierungsframework wie Log4N, NLog oder System.Diagnostics.Trace verwenden, haben Sie die Möglichkeit, die Protokolle zusammen mit den anderen Telemetriedaten an Application Insights zu senden, damit Sie die Ablaufverfolgungen problemlos mit Anforderungen, Abhängigkeitsaufrufen und Ausnahmen korrelieren können.

### <a name="search-telemetry-in-visual-studio"></a>Suchen nach Telemetriedaten in Visual Studio
Beim Entwickeln und Debuggen eines Features können Sie die Telemetrie direkt in Visual Studio anzeigen und durchsuchen. Dabei verwenden Sie die gleichen Suchfunktionen wie im Webportal.

Und wenn Application Insights eine Ausnahme protokolliert, können Sie den Datenpunkt in Visual Studio anzeigen und direkt zum entsprechenden Code springen.

![Visual Studio-Suche](./media/devops/060.png)

Während des Debuggens haben Sie die Option, die Telemetrie auf dem Entwicklungscomputer zu behalten und sie in Visual Studio anzuzeigen, ohne sie an das Portal zu senden. Durch diese lokale Option vermeiden Sie beim Debuggen eventuelle Verwechslungen mit Produktionstelemetriedaten.

### <a name="work-items"></a>Arbeitselemente
Wenn eine Warnung ausgelöst wird, kann Application Insights automatisch ein Arbeitselement in Ihrem Arbeitsüberwachungssystem erstellen.

## <a name="but-what-about"></a>Aber was ist mit...?
* [Datenschutz und Speicher:](../../azure-monitor/app/data-retention-privacy.md) Ihre Telemetrie wird auf sicheren Azure-Servern gespeichert.
* Leistung: Die Auswirkungen sind sehr gering. Die Telemetrie wird in Batches verarbeitet.
* [Preise:](../../azure-monitor/app/pricing.md) Sie können mit einer kostenlosen Version beginnen und diese beibehalten, solange Ihre Datenvolumen gering sind.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nächste Schritte
Die ersten Schritte mit Application Insights sind ganz einfach. Dies sind die wichtigsten Optionen:

* [IIS-Server](../../azure-monitor/app/monitor-performance-live-website-now.md) und auch für [Azure App Service](../../azure-monitor/app/app-insights-overview.md).
* Instrumentieren Sie Ihr Projekt während der Entwicklung. Dies ist für [ASP.NET](../../azure-monitor/app/asp-net.md)- oder [Java](../../azure-monitor/app/java-get-started.md)-Apps sowie für [Node.js](../../azure-monitor/app/nodejs.md) und eine Vielzahl [weiterer Sprachen und Plattformen](../../azure-monitor/app/platforms.md) möglich. 
* Instrumentieren Sie [beliebige Webseiten](../../azure-monitor/app/javascript.md) durch Hinzufügen eines kurzen Codeausschnitts.

