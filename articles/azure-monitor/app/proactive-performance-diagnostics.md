---
title: Intelligente Erkennung von Leistungsabweichungen | Microsoft Docs
description: Application Insights führt eine intelligente Analyse Ihrer App-Telemetriedaten durch und warnt Sie vor potenziellen Problemen. Dieses Feature muss nicht konfiguriert werden.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: ead71a5ac6e2cfa0df6654c7ba9aa7b5b9238d04
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763773"
---
# <a name="smart-detection---performance-anomalies"></a>Intelligente Erkennung – Leistungsabweichungen

[Application Insights](./app-insights-overview.md) analysiert die Leistung Ihrer Webanwendung automatisch und kann Sie vor potenziellen Problemen warnen. Sie lesen diese Information möglicherweise, weil Sie eine Benachrichtigung der intelligenten Erkennung erhalten haben.

Für dieses Feature müssen Sie lediglich Ihre App in der von Ihnen [unterstützten Sprache](./platforms.md) für Application Insights konfigurieren. Sie ist aktiv, wenn Ihre App genügend Telemetriedaten generiert.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Wann erhalte ich eine Benachrichtigung der intelligenten Erkennung?

Application Insights hat festgestellt, dass die Leistung Ihrer Anwendung auf eine der folgenden Weisen beeinträchtigt wird:

* **Beeinträchtigte Antwortzeit** – Ihre App reagiert langsamer auf Anforderungen als gewöhnlich. Die Änderung erfolgte möglicherweise plötzlich, z.B. aufgrund einer Regression in Ihrer aktuellen Bereitstellung. Oder sie erfolgte schrittweise, wurde möglicherweise durch einen Speicherverlust verursacht. 
* **Beeinträchtigte Dauer der Abhängigkeit** – Ihre App ruft eine REST-API, Datenbank oder eine andere Abhängigkeit auf. Die Abhängigkeit reagiert langsamer als gewöhnlich.
* **Langsames Leistungsmuster** – In Ihrer App scheint ein Leistungsproblem vorzuliegen, das sich nur auf einige Anforderungen auswirkt. Beispielsweise werden Seiten mit einigen Browsertypen deutlich langsamer als mit anderen Browsern geladen, oder Anforderungen werden über einen bestimmten Server deutlich langsamer bereitgestellt. Derzeit überprüfen wir mit unseren Algorithmen die Seitenladezeiten, Antwortzeiten von Anforderungen und Antwortzeit von Abhängigkeiten.  

Die intelligente Erkennung benötigt ein bearbeitbares Volumen an Telemetriedaten von mindestens acht Tagen, um eine Baseline für normale Leistung zu erstellen. Nachdem Ihre Anwendung über diesen Zeitraum ausgeführt wurde, erhalten Sie daher eine Benachrichtigung über wichtige Probleme.


## <a name="does-my-app-definitely-have-a-problem"></a>Liegt wirklich ein Problem mit meiner App vor?

Nein, eine Benachrichtigung bedeutet nicht zwingend, dass ein Problem mit Ihrer App vorliegt. Es handelt sich lediglich um Vorschlag über etwas, das Sie sich möglicherweise genauer ansehen möchten.

## <a name="how-do-i-fix-it"></a>Wie behebe ich das Problem?

Die Benachrichtigungen enthalten Diagnoseinformationen. Hier sehen Sie ein Beispiel:


![Hier sehen Sie ein Beispiel, in dem eine schlechtere Antwortzeit ermittelt wurde.](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Eingrenzung**. Diese Benachrichtigung zeigt an, wie viele Benutzer oder wie viele Vorgänge betroffen sind. Dadurch können Sie dem Problem eine Priorität zuweisen.
2. **Umfang**. Betrifft das Problem den gesamten Datenverkehr oder nur bestimmte Seiten? Ist das Problem auf bestimmte Browser oder Standorte beschränkt? Diese Informationen können Sie der Benachrichtigung entnehmen.
3. **Diagnose**. Häufig finden Sie in den Diagnoseinformationen in der Benachrichtigung eine mögliche Ursache des Problems. Wird die Antwortzeit z.B. durch eine höhere Anforderungsrate verlängert, liegt möglicherweise eine Überlastung Ihres Servers oder Ihrer Abhängigkeiten vor. 

    Öffnen Sie andernfalls das Blatt „Leistung“ im Application Insights. Dort finden Sie [Profiler](profiler.md)-Daten. Wenn Ausnahmen ausgelöst werden, können Sie auch den [Snapshot Debugger](./snapshot-debugger.md) verwenden.



## <a name="configure-email-notifications"></a>Konfigurieren von E-Mail-Benachrichtigungen

Benachrichtigungen der intelligenten Erkennung sind standardmäßig aktiviert und werden an Personen gesendet, die über die Zugriffsberechtigungen [Benutzer mit Leseberechtigung für Überwachungsdaten](../../role-based-access-control/built-in-roles.md#monitoring-reader) und [Mitwirkender an der Überwachung](../../role-based-access-control/built-in-roles.md#monitoring-contributor) für das Abonnement verfügen, in dem sich die Application Insights-Ressource befindet. Um dies zu ändern, klicken Sie in der E-Mail-Benachrichtigung auf **Konfigurieren**, oder öffnen Sie die Einstellungen für die intelligente Erkennung in Application Insights. 
  
  ![Einstellungen für die intelligente Erkennung](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * Über den Link **Austragen** in der E-Mail zur intelligenten Erkennung können Sie den Empfang der E-Mail-Benachrichtigungen kündigen.

Pro Tag und Application Insights-Ressource wird nur eine E-Mail zu Leistungsanomalien der intelligenten Erkennung gesendet. Die E-Mail wird nur gesendet, wenn an diesem Tag mindestens ein neues Problem erkannt wurde. Sie erhalten Nachrichten nicht mehrfach. 

## <a name="faq"></a>Häufig gestellte Fragen

* *Microsoft-Mitarbeiter sehen sich also meine Daten an?*
  * Nein. Der Dienst ist vollständig automatisch. Nur Sie erhalten die Benachrichtigungen. Ihre Daten sind [privat](./data-retention-privacy.md).
* *Werden alle der von Application Insights erfassten Daten analysiert?*
  * Derzeit nicht. Gegenwärtig werden Antwortzeiten auf Anforderungen, Reaktionszeiten von Abhängigkeiten und Seitenladezeiten analysiert. Ein Analysen von zusätzlichen Metriken finden Sie in unserem Backlog.

* Mit welchen Anwendungstypen ist dies möglich?
  * Jede Anwendung, die die entsprechenden Telemetrie generiert, erkennt diese Beeinträchtigungen. Wenn Sie Application Insights in Ihrer Web-App installiert haben, werden Anforderungen und Abhängigkeiten automatisch nachverfolgt. Falls Sie in Back-End-Diensten oder anderen Apps Aufrufe von [TrackRequest()](./api-custom-events-metrics.md#trackrequest) oder [TrackDependency](./api-custom-events-metrics.md#trackdependency) eingefügt haben, funktioniert die intelligente Erkennung auf die gleiche Weise.

* *Kann ich meine eigenen Erkennungsregeln für Anomalien erstellen oder vorhandene Regeln anpassen?*

  * Das ist leider noch nicht möglich. Jedoch ist Folgendes möglich:
    * [Einrichten von Warnungen](../platform/alerts-log.md), die Sie informieren, wenn eine Metrik einen Schwellenwert überschreitet
    * [Exportieren von Telemetriedaten](./export-telemetry.md) in eine [Datenbank](./code-sample-export-sql-stream-analytics.md) oder [in Power BI](./export-power-bi.md), mit denen Sie selbst eine Analyse durchführen können.
* *Wie oft wird die Analyse ausgeführt?*

  * Die Analyse der Telemetriedaten vom Vortag wird täglich ausgeführt (ganzer Tag in Zeitzone UTC).
* *Ersetzt dies also die [Metrikwarnungen](../platform/alerts-log.md)?*
  * Nein.  Wir können nicht gewährleisten, dass jedes Verhalten erkannt wird, das Sie möglicherweise als ungewöhnlich erachten.


* *Erhalte ich eine Erinnerung, wenn ich nach einer Benachrichtigung nichts unternehme?*
  * Nein, Sie erhalten jeweils nur eine Nachricht zu einem Problem. Wenn das Problem weiterhin besteht, wird es im Feed-Blatt der intelligenten Erkennung aktualisiert.
* *Ich habe die E-Mail verloren. Wo finde ich die Benachrichtigungen im Portal?*
  * Klicken Sie in der Application Insights-Übersicht Ihrer App auf die Kachel für die **intelligente Erkennung**. Dort finden Sie alle Benachrichtigungen der letzten 90 Tage.

## <a name="how-can-i-improve-performance"></a>Wie kann ich die Leistung verbessern?
Langsame und fehlgeschlagene Antworten sind für Websitebenutzer eines der größten Ärgernisse, was Sie sicherlich aus eigener Erfahrung bestätigen können. Daher ist es wichtig, diese Probleme zu beheben.

### <a name="triage"></a>Eingrenzung
Erstens: Ist es wirklich ein Problem? Wenn eine Seite immer langsam geladen wird, aber nur 1 % der Benutzer Ihrer Website diese Seite anzeigen müssen, gibt es vielleicht wichtigere Dinge, mit denen Sie sich beschäftigen müssen. Falls nur 1 % der Benutzer darauf zugreifen, aber jedes Mal Ausnahmen ausgelöst werden, kann es erforderlich sein, dies zu untersuchen.

Verwenden Sie die Aussage zur Auswirkung (betroffene Benutzer oder Prozentsatz des Datenverkehrs) als allgemeinen Anhaltspunkt, aber bedenken Sie, dass damit nicht alles abgedeckt wird. Sammeln Sie zur Bestätigung weitere Beweise.

Sehen Sie sich die Parameter des Problems an. Falls eine geografische Abhängigkeit besteht, sollten Sie [Verfügbarkeitstests](./monitor-web-app-availability.md) für diese Region einrichten. Möglicherweise liegen in diesem Umfeld lediglich Netzwerkprobleme vor.

### <a name="diagnose-slow-page-loads"></a>Diagnostizieren des langsamen Ladens von Seiten
Wo liegt das Problem? Reagiert der Server langsam, ist die Seite sehr lang oder muss der Browser viel Aufwand betreiben, um die Seite anzuzeigen?

Öffnen Sie das Blatt mit den Browsermetriken. Die segmentierte Anzeige der Seitenladezeit im Browser gibt an, wo Zeit verbraucht wird. 

* Wenn die **Zeit für das Senden von Anforderungen** hoch ist, reagiert entweder der Server langsam, oder die Anforderung umfasst eine größere Datenmenge. Sehen Sie sich die [Leistungsmetriken](./performance-counters.md) an, um die Reaktionszeiten zu prüfen.
* Richten Sie die [Abhängigkeitsüberwachung](./asp-net-dependencies.md) ein, um ermitteln zu können, ob die Langsamkeit mit externen Diensten oder Ihrer Datenbank zusammenhängt.
* Wenn **Empfängt Antwortzeit** vorherrscht, sind Ihre Seite und die abhängigen Teile lang, z.B. JavaScript, CSS, Bilder usw. (jedoch nicht asynchron geladene Daten). Richten Sie einen [Verfügbarkeitstest](./monitor-web-app-availability.md) ein, und legen Sie die Option zum Laden abhängiger Teile fest. Wenn Sie einige Ergebnisse erhalten, öffnen Sie die Details eines Ergebnisses und erweitern diese, um die Ladezeiten für unterschiedliche Dateien anzuzeigen.
* Eine hohe **Clientverarbeitungszeit** deutet darauf hin, dass die Ausführung von Skripts lange dauert. Falls die Ursache nicht offensichtlich ist, können Sie erwägen, Zeitsteuerungscode hinzuzufügen und die Zeiten über trackMetric-Aufrufe zu senden.

### <a name="improve-slow-pages"></a>Verbessern langsamer Seiten
Da Sie im Web viele Ratschläge zur Verbesserung der Serverreaktionszeit und Seitenladezeiten finden, soll dies hier nicht alles wiederholt werden. Sie erhalten aber einige Tipps, die Sie vielleicht bereits kennen, aber die hier noch einmal in Erinnerung gerufen werden sollen:

* Langsames Laden aufgrund großer Dateien: Laden Sie die Skripts und anderen Teile asynchron. Verwenden Sie die Skriptbündelung. Unterteilen Sie die Hauptseite in Widgets, für die die Daten separat geladen werden. Kein Senden von reinen „alten“ HTML-Daten für lange Tabellen: Verwenden Sie ein Skript, um die Daten als JSON oder in einem anderen kompakten Format anzufordern, und füllen Sie anschließend die Tabelle mit Daten. Es gibt gute Frameworks, die hierfür hilfreich sind. (Die natürlich mit großen Skripts verbunden sind.)
* Langsame Serverabhängigkeiten: Sehen Sie sich die geografischen Standorte der Komponenten an. Stellen Sie bei Verwendung von Azure beispielsweise sicher, dass sich der Webserver und die Datenbank in derselben Region befinden. Werden bei Abfragen mehr Informationen als nötig abgerufen? Würde das Zwischenspeichern oder eine Batchverarbeitung helfen?
* Kapazitätsprobleme: Sehen Sie sich die Servermetriken von Reaktionszeiten und die Anforderungsanzahl an. Wenn für die Antwortzeiten unverhältnismäßige Spitzen gegenüber den Spitzen bei der Anzahl der Anforderungen zu beobachten sind, ist die Wahrscheinlichkeit hoch, dass Ihre Server überlastet sind.


## <a name="server-response-time-degradation"></a>Beeinträchtigte Antwortzeit des Servers

Die Benachrichtigung über die beeinträchtigte Antwortzeit enthält folgende Informationen:

* Die Antwortzeit im Vergleich zur normalen Antwortzeit für diesen Vorgang.
* Wie viele Benutzer betroffen sind.
* Die durchschnittliche Antwortzeit und die Antwortzeit des 90. Quantils für diesen Vorgang am Tag der Erkennung und an den 7 Tagen davor. 
* Die Anzahl dieser Vorgangsanforderungen am Tag der Erkennung und an den 7 Tagen davor.
* Korrelation zwischen Beeinträchtigungen bei diesem Vorgang und Beeinträchtigungen in zugehörigen Abhängigkeiten. 
* Links mit Informationen zur Problembehebung.
  * Mit Profiler-Ablaufverfolgungen können Sie die Vorgangszeit anzeigen (der Link ist verfügbar, wenn während des Erkennungszeitraums Beispiele der Profiler-Ablaufverfolgung für diesen Vorgang erfasst wurden). 
  * Leistungsberichte im Metrikexplorer, in dem Sie Zeitbereiche/Filter für diesen Vorgang segmentieren können.
  * Suchen Sie nach diesem Aufruf, um bestimmte Aufrufeigenschaften anzuzeigen.
  * Fehlerberichte – Beträgt der Zähler > 1, lagen Fehler im Vorgang vor, die zur Leistungsbeeinträchtigung beigetragen haben könnten.

## <a name="dependency-duration-degradation"></a>Beeinträchtigung der Abhängigkeitsdauer

Moderne Anwendungen verwenden zunehmend einen Mikroservices-Entwurfsansatz, der in vielen Fällen zur Abhängigkeit von externen Diensten führt. Wenn beispielsweise Ihre Anwendung von einer Datenplattform abhängt oder Sie einen eigenen Botdienst entwickeln, benötigen Sie wahrscheinlich einen Cognitive Services-Anbieter, um eine menschlichere Interaktion Ihrer Bots zu ermöglichen, und einen Datenspeicherdienst, aus dem der Bot Anworten abrufen kann.  

Beispiel einer Benachrichtigung über beeinträchtigte Abhängigkeit:

![Hier sehen Sie ein Beispiel, in dem eine beeinträchtigte Abhängigkeitsdauer erkannt wurde.](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Sie enthält folgende Angaben:

* Die Dauer im Vergleich zur normalen Antwortzeit für diesen Vorgang
* Wie viele Benutzer betroffen sind
* Die durchschnittliche Dauer und Dauer des 90. Quantils für diese Abhängigkeit am Tag der Erkennung und an den 7 Tagen davor
* Anzahl der Abhängigkeitsaufrufe am Tag der Erkennung und an den 7 Tagen davor
* Links mit Informationen zur Problembehebung
  * Leistungsberichte im Metrikexplorer für diese Abhängigkeit
  * Suche nach dieser Abhängigkeit, um Eigenschaften von Aufrufen anzuzeigen
  * Fehlerberichte – Beträgt der Zähler > 1, lagen Fehler in Abhängigkeitsaufrufen während des Erkennungszeitraums vor, die zur Beeinträchtigung der Dauer beigetragen haben könnten. 
  * Öffnen von Analytics mit Abfragen, die Dauer und Anzahl dieser Abhängigkeit berechnen  

## <a name="smart-detection-of-slow-performing-patterns"></a>Intelligente Erkennung langsamer Leistungsmuster 

Application Insights ermittelt Leistungsprobleme, die sich möglicherweise nur auf einige Benutzer auswirken oder nur in bestimmten Fällen eine Auswirkung für Benutzer darstellen. Beispielsweise werden Sie benachrichtigt, wenn Seiten mit einem Browsertyp deutlich langsamer als bei anderen Browsertypen geladen werden oder wenn Anforderungen über einen bestimmten Server deutlich langsamer bereitgestellt werden. Außerdem können Probleme mit Kombinationen von Eigenschaften erkannt werden, z.B. das langsame Laden von Seiten in einem geografischen Bereich mit Clients, die ein bestimmtes Betriebssystem verwenden.  

Anomalien dieser Art sind nicht leicht zu erkennen, wenn nur die Daten untersucht werden, aber sie kommen häufiger vor, als man denkt. Sie erfahren davon meistens erst, wenn Sie Beschwerden von Kunden erhalten. Doch dann ist es bereits zu spät: Die betroffenen Benutzer wandern zur Konkurrenz ab.

Derzeit überprüfen wir mit unseren Algorithmen die Seitenladezeiten, Reaktionszeiten auf Anforderungen auf dem Server und Reaktionszeiten von Abhängigkeiten.  

Sie müssen keine Schwellenwerte festlegen und keine Regeln konfigurieren. Machine Learning- und Data Mining-Algorithmen werden verwendet, um ungewöhnliche Muster zu erkennen.

![Klicken Sie auf den Link in der E-Mail-Benachrichtigung, um den Diagnosebericht in Azure zu öffnen.](./media/proactive-performance-diagnostics/03.png)

* **Wann** gibt die Zeit an, zu der das Problem erkannt wurde.
* **Was** beschreibt Folgendes:

  * Das erkannte Problem
  * Die Merkmale der gefundenen Gruppe von Ereignissen, die das problematische Verhalten aufwiesen
* In der Tabelle wird diese Gruppe mit beeinträchtigter Leistung mit dem durchschnittlichen Verhalten aller anderen Ereignisse verglichen.

Klicken Sie auf die Links, um den Metrik-Explorer und die Suche für die relevanten Berichte zu öffnen, die nach Zeit und Eigenschaften der Gruppe mit beeinträchtigter Leistung gefiltert sind.

Ändern Sie den Zeitraum und die Filter zum Durchsuchen der Telemetrie.

## <a name="next-steps"></a>Nächste Schritte
Mit den folgenden Diagnosetools können Sie die Telemetrie Ihrer App untersuchen:

* [Profiler](profiler.md) 
* [Momentaufnahmedebugger](./snapshot-debugger.md)
* [Analyse](../log-query/log-analytics-tutorial.md)
* [Intelligente Analysediagnose](../log-query/log-query-overview.md)

Intelligente Erkennungen sind vollkommen automatisch. Vielleicht möchten Sie aber weitere Warnungen einrichten?

* [Einrichten von Warnungen in Application Insights](../platform/alerts-log.md)
* [Verfügbarkeitswebtests](./monitor-web-app-availability.md)