---
title: Behandeln von Problemen mit Azure Application Insights-Verfügbarkeitstests
description: Behandeln von Problemen mit Webtests in Azure Application Insights Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/19/2020
ms.reviewer: sdash
ms.openlocfilehash: 368c45433247c441631bdf79bfc9caa28a41f1b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546748"
---
# <a name="troubleshooting"></a>Problembehandlung

Dieser Artikel soll Ihnen beim Behandeln von häufig bei der Verfügbarkeitsüberwachung auftretenden Problemen helfen.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>Schritte im Problembehandlungsbericht für Pingtests

Der Problembehandlungsbericht ermöglicht Ihnen eine einfache Diagnose häufig auftretender Probleme, die zu Fehlern bei **Pingtests** führen.

![Animation der Navigation von der Registerkarte „Verfügbarkeit“ über die Auswahl eines Fehlers zu den End-to-End-Transaktionsdetails zum Anzeigen des Problembehandlungsberichts](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. Wählen Sie auf der Registerkarte „Verfügbarkeit“ der Application Insights-Ressource die gesamten oder einen bestimmten Verfügbarkeitstest aus.
2. Wählen Sie entweder **Fehlerhaft** und dann einen Test unter „Drilldown“ auf der linken Seite aus, oder wählen Sie einen der Punkte im Punktdiagramm aus.
3. Wählen Sie auf der Seite mit den End-to-End-Transaktionsdetails ein Ereignis aus, und wählen Sie dann unter der Übersicht über Problembehandlungsberichte die Option **[Gehe zu Schritt]** aus, um den Problembehandlungsbericht anzuzeigen.

> [!NOTE]
>  Wenn der Schritt zur Wiederverwendung von Verbindungen vorhanden ist, sind die Schritte für DNS-Auflösung, Verbindungsherstellung und TLS-Transport nicht vorhanden.

|Schritt | Fehlermeldung | Mögliche Ursache |
|-----|---------------|----------------|
| Wiederverwendung von Verbindungen | – | Hängt in der Regel von einer zuvor hergestellten Verbindung ab. Das bedeutet, dass der Webtestschritt davon abhängig ist. Es ist also kein DNS-, Verbindungs- oder SSL-Schritt erforderlich. |
| DNS-Auflösung | Der Remotename konnte nicht aufgelöst werden: „Ihre URL“ | Der DNS-Auflösungsprozess ist wahrscheinlich aufgrund von falsch konfigurierten DNS-Einträgen oder vorübergehenden DNS-Serverfehlern fehlgeschlagen. |
| Verbindungsherstellung | Ein Verbindungsversuch ist fehlgeschlagen, weil die Partei, mit der eine Verbindung hergestellt werden sollte, in einem bestimmten Zeitraum nicht reagiert hat. | Im Allgemeinen bedeutet dies, dass Ihr Server nicht auf die HTTP-Anforderung reagiert. Eine häufige Ursache ist, dass unsere Test-Agents durch eine Firewall auf Ihrem Server blockiert werden. Wenn Sie Tests innerhalb eines virtuellen Azure-Netzwerks ausführen möchten, sollten Sie Ihrer Umgebung das Diensttag für Verfügbarkeit hinzufügen.|
| TLS-Transport  | Client und Server können nicht kommunizieren, da sie keinen gemeinsamen Algorithmus besitzen.| Es werden nur die TLS-Versionen 1.0, 1.1 und 1.2 unterstützt SSL wird nicht unterstützt. In diesem Schritt werden SSL-Zertifikate nicht überprüft, und es wird nur eine sichere Verbindung hergestellt. Dieser Schritt wird nur angezeigt, wenn ein Fehler auftritt. |
| Empfang des Antwortheaders | Es können keine Daten aus der Transportverbindung gelesen werden. Die Verbindung wurde geschlossen. | Beim Server ist ein Protokollfehler im Antwortheader aufgetreten. Beispielsweise wird die Verbindung vom Server geschlossen, wenn die Antwort nicht vollständig ist. |
| Empfang des Antworttextes | Es können keine Daten aus der Transportverbindung gelesen werden: Die Verbindung wurde geschlossen. | Beim Server ist ein Protokollfehler im Antworttext aufgetreten. Beispielsweise wird die Verbindung vom Server geschlossen, wenn die Antwort nicht vollständig gelesen wird oder die Segmentgröße im segmentierten Antworttext falsch ist. |
| Prüfung der Weiterleitungsbeschränkung | Diese Webseite weist zu viele Weiterleitungen auf. Diese Schleife wird hier beendet, da die Anforderung das Limit für automatische Weiterleitungen überschritten hat. | Pro Test können maximal 10 Weiterleitungen verwendet werden. |
| Überprüfung des Statuscodes | `200 - OK` stimmt nicht mit dem erwarteten Status `400 - BadRequest` überein. | Der zurückgegebene Statuscode, der als Erfolg gezählt wird. 200 ist der Code, der angibt, dass eine normale Webseite zurückgegeben wurde. |
| Validierung von Inhalten | Der erforderliche Text „hello“ war nicht in der Antwort enthalten. | Die Zeichenfolge stimmt unter Berücksichtigung der Groß- und Kleinschreibung in der Antwort nicht exakt überein, z. B. die Zeichenfolge „Welcome!“. Hierbei muss es sich um eine einfache Zeichenfolge ohne Platzhalterzeichen (z. B. ein Sternchen) handeln. Wenn sich der Seiteninhalt ändert, müssen Sie die Zeichenfolge möglicherweise aktualisieren. Inhaltsübereinstimmungen werden nur für englische Zeichen unterstützt. |
  
## <a name="common-troubleshooting-questions"></a>Allgemeine Fragen zur Problembehandlung

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Die Website sieht korrekt aus, aber ich sehe Testfehler. Warum warnt mich Application Insights?

   * Ist für Ihren Test die Option **Abhängige Anforderungen analysieren** aktiviert? Dies führt zu einer strengen Überprüfung von Ressourcen wie Skripts, Bildern usw. Diese Typen von Fehlern machen sich in einem Browser möglicherweise nicht bemerkbar. Überprüfen Sie alle Bilder, Skripts, Stylesheets und anderen Dateien, die von der Seite geladen werden. Wenn eines dieser Elemente einen Fehler verursacht, wird der Test auch dann als fehlerhaft gemeldet, wenn die HTML-Hauptseite problemlos geladen wird. Deaktivieren Sie einfach in der Testkonfiguration die Option „Abhängige Anforderungen analysieren“, um solche Ressourcenfehler vom Test auszuschließen.

   * Stellen Sie sicher, dass die Konfigurationsoption „Enable retries for test failures“ (Weitere Versuche bei Testfehlern zulassen) aktiviert ist, um Störungen infolge vorübergehender Netzwerkprobleme zu verringern. Sie können den Test auch an mehreren Standorten durchführen und den Schwellenwert der Warnungsregel entsprechen verwalten, um zu verhindern, dass standortspezifische Probleme übermäßige Warnungen auslösen.

   * Klicken Sie auf einen der roten Punkte im Punktdiagramm zur Verfügbarkeit oder auf einen Verfügbarkeitsfehler im Such-Explorer, um die Details anzuzeigen, warum wir den Fehler gemeldet haben. Das Testergebnis sollte, zusammen mit der korrelierten serverseitigen Telemetrie (sofern aktiviert), Aufschluss darüber geben, warum der Test nicht erfolgreich war. Häufige Ursachen für vorübergehende Probleme sind Netzwerk- oder Verbindungsprobleme.

   * Kam es beim Test zum Timeout? Wir brechen Tests nach 2 Minuten ab. Wenn Ihr Ping- oder mehrstufiger Test länger als zwei Minuten dauert, melden wir ihn als Fehler. Erwägen Sie, den Test in mehrere aufzuteilen, die sich schneller abschließen lassen.

   * Haben allel Standorte Fehler gemeldet oder nur einige? Wenn nur einige Standorte Fehler gemeldet haben, kann es an Netzwerk-/CDN-Problemen liegen. Auch in diesem Fall sollte das Klicken auf die roten Punkte Ihnen zu einem besseren Verständnis verhelfen, warum der Standort Fehler gemeldet hat.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Ich habe keine E-Mail erhalten, als die Warnung ausgelöst und/oder behoben wurde.

Überprüfen Sie die klassische Warnungskonfiguration, um zu bestätigen, dass Ihre E-Mail-Adresse direkt aufgeführt ist, oder dass eine Verteilerliste, deren Mitglied Sie sind, für den Empfang von Benachrichtigungen konfiguriert ist. Ist dies der Fall, überprüfen Sie die Konfiguration der Verteilerliste, um zu bestätigen, dass sie externe E-Mails empfangen kann. Überprüfen Sie außerdem, ob Ihr E-Mail-Administrator eventuell Richtlinien konfiguriert hat, die dieses Problem verursachen können.

### <a name="i-did-not-receive-the-webhook-notification"></a>Ich habe die Webhookbenachrichtigung nicht empfangen.

Überprüfen Sie, ob die Anwendung, die die Webhookbenachrichtigung empfängt, verfügbar ist und die Webhookanforderungen erfolgreich verarbeitet. Weitere Informationen finden Sie [hier](../platform/alerts-log-webhook.md).

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Ich erhalte die Fehler „Unzulässig – 403“, was bedeutet das?

Dieser Fehler zeigt an, dass Sie Firewallausnahmen hinzufügen müssen, damit die Verfügbarkeits-Agents Ihre Ziel-URL testen können. Eine vollständige Liste der zuzulassenden Agent-IP-Adressen finden Sie im Artikel zur [IP-Ausnahme](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Ein zeitweiliger Testfehler aufgrund einer Protokollverletzung ist aufgetreten.

Der Fehler („protocol violation..CR must be followed by LF“) weist auf ein Problem mit dem Server (oder mit Abhängigkeiten) hin. Er wird angezeigt, wenn falsch formatierte Header in der Antwort festgelegt werden. Der Fehler kann durch Lastenausgleichsmodule oder CDNs verursacht werden. Genauer gesagt geben einige Header unter Umständen das Zeilenende nicht mit CRLF an, was gegen die HTTP-Spezifikation verstößt. Daher schlägt die Validierung auf .NET-WebRequest-Ebene fehl. Überprüfen Sie die Antwort, um Header zu ermitteln, die möglicherweise gegen diese Spezifikation verstoßen.

> [!NOTE]
> Bei der URL tritt in Browsern mit einer nicht so strengen Validierung von HTTP-Headern unter Umständen kein Fehler auf. Eine ausführliche Erläuterung des Problems finden Sie in diesem Blogbeitrag: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/.  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Ich sehe keine zugehörigen serverseitigen Telemetriedaten zum Diagnostizieren von Testfehlern.*

Wenn Sie Application Insights für Ihre serverseitige Anwendung eingerichtet haben, liegt dies möglicherweise daran, dass [Sampling](./sampling.md) in Betrieb ist. Wählen Sie ein anderes Verfügbarkeitsergebnis aus.

### <a name="can-i-call-code-from-my-web-test"></a>Kann ich Code aus meinem Webtest aufrufen?

Nein. Die Schritte des Tests müssen in der Webtest-Datei enthalten sein. Und Sie können keine anderen Webtests aufrufen oder Schleifen verwenden. Aber es gibt mehrere hilfreiche Plug-Ins.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Gibt es einen Unterschied zwischen „Webtests“ und „Verfügbarkeitstests“?

Die beiden Begriffe sind austauschbar. „Verfügbarkeitstests“ ist ein allgemeinerer Begriff, der neben den mehrstufigen Webtests auch die einzelnen URL-Pingtests enthält.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Ich möchte Verfügbarkeitstests auf unserem internen Server verwenden, der hinter einer Firewall ausgeführt wird.

   Es gibt zwei mögliche Lösungen:

   * Konfigurieren Sie die Firewall so, dass eingehende Anforderungen von den [IP-Adressen der Webtest-Agents](./ip-addresses.md) zugelassen werden.
   * Schreiben Sie eigenen Code zum regelmäßigen Testen Ihres internen Servers. Führen Sie den Code als Hintergrundprozess auf einem Testserver hinter Ihrer Firewall aus. Die Ergebnisse des Testvorgangs können an Application Insights gesendet werden, indem die [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)-API im Core SDK-Paket verwendet wird. Hierfür ist es erforderlich, dass Ihr Testserver Zugriff in ausgehender Richtung auf den Application Insights-Erfassungsendpunkt hat. Dies ist aber ein deutlich geringeres Sicherheitsrisiko als bei der Alternativlösung, bei der eingehende Anforderungen zugelassen werden. Die Ergebnisse werden auf den Blättern der Verfügbarkeitswebtests angezeigt. Die Funktionalität ist jedoch leicht vereinfacht gegenüber den Funktionen, die für über das Portal erstellte Tests zur Verfügung stehen. Benutzerdefinierte Verfügbarkeitstests werden auch als Verfügbarkeitsergebnisse in Analysen, Suchen und Metriken angezeigt.

### <a name="uploading-a-multi-step-web-test-fails"></a>Fehler beim Hochladen eines mehrstufigen Webtests

Einige Gründe, warum dies geschehen kann:
   * Die Größenbeschränkung beträgt 300 K.
   * Schleifen werden nicht unterstützt.
   * Verweise auf andere Webtests werden nicht unterstützt.
   * Datenquellen werden nicht unterstützt.

### <a name="my-multi-step-test-doesnt-complete"></a>Mein mehrstufiger Test wird nicht abgeschlossen.

Pro Test können maximal 100 Anforderungen verwendet werden. Der Test wird außerdem beendet, wenn seine Ausführung länger als zwei Minuten dauert.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Wie kann ich einen Test mit Clientzertifikaten durchführen?

Dies wird derzeit nicht unterstützt.

## <a name="who-receives-the-classic-alert-notifications"></a>Wer erhält die (klassischen) Warnungsbenachrichtigungen?

Dieser Abschnitt gilt nur für klassische Benachrichtigungen und hilft Ihnen, Ihre Warnungsbenachrichtigungen zu optimieren, um sicherzustellen, dass nur die gewünschten Empfänger Benachrichtigungen erhalten. Um mehr über den Unterschied zwischen [klassischen Benachrichtigungen](../platform/alerts-classic.overview.md) und der neuen Benutzeroberfläche für Warnungen zu erfahren, lesen Sie den Artikel [Überblick über Warnungen in Microsoft Azure](../platform/alerts-overview.md). Um die Warnungsbenachrichtigung in der neuen Benutzeroberfläche für Warnungen zu steuern, verwenden Sie [Aktionsgruppen](../platform/action-groups.md).

* Wir empfehlen die Verwendung bestimmter Empfänger für klassische Warnungsbenachrichtigungen.

* Für Warnungen zu Fehlern bei X von Y Standorten werden bei aktivierter Option **Massenversand/Gruppe** Benutzer mit Administrator-/Co-Administratorrolle benachrichtigt.  Im Wesentlichen erhalten _alle_ Administratoren des _Abonnements_ Benachrichtigungen.

* Für Warnungen zu Verfügbarkeitsmetriken werden bei aktivierter Option **bulk/group** (Masse/Gruppe) Benachrichtigungen an Benutzer mit der Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ im Abonnement gesendet. Tatsächlich sind _alle_ Benutzer mit Zugriff auf das Abonnement der Application Insights-Ressource im Umfang enthalten und erhalten Benachrichtigungen. 

> [!NOTE]
> Wenn Sie aktuell die Option **Massenversand/Gruppe** verwenden und diese deaktivieren, können Sie die Änderung nicht rückgängig machen.

Verwenden Sie die neue Benutzeroberfläche für Warnungen/Warnungen nahezu in Echtzeit, wenn Sie Benutzer basierend auf ihren Rollen benachrichtigen müssen. Mit [Aktionsgruppen](../platform/action-groups.md) können Sie E-Mail-Benachrichtigungen für Benutzer mit den Rollen „Mitwirkender“, „Besitzer“, „Leser“ konfigurieren (nicht kombiniert als eine einzige Option).

## <a name="next-steps"></a>Nächste Schritte

* [Multi-step web testing (Mehrstufiger Webtest)](availability-multistep.md)
* [URL ping tests (URL-Pingtests)](monitor-web-app-availability.md)