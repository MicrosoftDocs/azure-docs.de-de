---
title: Bewährte Methoden und Problembehandlung bei Node.js
description: Erfahren Sie etwas über bewährte Methoden und Problembehandlungsschritte für Node.js-Anwendungen, die in Azure App Service ausgeführt werden.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 370b84f451e22c20c798018951a7a801e0bba826
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763943"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Bewährte Methoden und Problembehandlungsschritte für Node-Anwendungen in Azure App Service unter Windows

In diesem Artikel werden die bewährten Methoden und Problembehandlungsschritte für [Windows-Node.js-Anwendungen](quickstart-nodejs.md?pivots=platform-windows) beschrieben, die in Azure App Service (mit [iisnode](https://github.com/azure/iisnode)) ausgeführt werden.

> [!WARNING]
> Gehen Sie mit Bedacht vor, wenn Sie für Ihre Produktionswebsite Schritte zur Problembehandlung ausführen. Es wird empfohlen, die Problembehandlung für Ihre App nicht in der Produktionsumgebung durchzuführen, sondern z.B. in Ihrem Stagingslot. Nachdem das Problem behoben wurde, können Sie den Stagingslot dann durch Ihren Produktionsslot austauschen.
>

## <a name="iisnode-configuration"></a>IISNODE-Konfiguration

Diese [Schemadatei](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) enthält alle Einstellungen, die Sie für iisnode konfigurieren können. Einige Einstellungen, die für Ihre Anwendung nützlich sind, lauten:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Mit dieser Einstellung wird die Anzahl von Knotenprozessen gesteuert, die pro IIS-Anwendung gestartet werden. Der Standardwert ist 1. Sie können so viele Instanzen von „node.exe“ starten, wie VM-vCPUs vorhanden sind, indem Sie diesen Wert in 0 ändern. Für die meisten Anwendungen ist der empfohlene Wert 0, damit Sie alle vCPUs des Computers nutzen können. Für „node.exe“ gilt das Singlethread-Verfahren, sodass eine „node.exe“-Instanz maximal eine vCPU nutzt. Sie möchten erreichen, dass alle vCPUs genutzt werden, um für Ihre Knotenanwendung die höchste Leistung zu erzielen.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Mit dieser Einstellung wird der Pfad zu „node.exe“ gesteuert. Sie können diesen Wert so festlegen, dass er auf Ihre „node.exe“-Version zeigt.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Mit dieser Einstellung wird die maximale Anzahl von gleichzeitigen Anforderungen gesteuert, die von iisnode an jede Instanz von „node.exe“ gesendet werden. In Azure App Service lautet der Standardwert „Infinite“. Sie können den Wert in Abhängigkeit davon konfigurieren, wie viele Anforderungen Ihre Anwendung erhält und wie schnell sie die einzelnen Anforderungen verarbeitet.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Mit dieser Einstellung wird gesteuert, wie oft iisnode maximal versucht, die Verbindung für die Named Pipe erneut herzustellen, um die Anforderungen an „node.exe“ zu senden. Diese Einstellung in Kombination mit namedPipeConnectionRetryDelay bestimmt den Gesamttimeout jeder Anforderung in iisnode. In Azure App Service lautet der Standardwert 200. Gesamttimeout in Sekunden = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Mit dieser Einstellung wird gesteuert, wie lange (in ms) iisnode vor jedem erneuten Versuch zum Senden der Anforderung an „node.exe“ über die benannte Pipe wartet. Der Standardwert ist 250 ms.
Gesamttimeout in Sekunden = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

In Azure App Service beträgt der Gesamttimeoutwert in „iisnode“ standardmäßig 200 \* 250 ms = 50 Sekunden.

### <a name="logdirectory"></a>logDirectory

Mit dieser Einstellung wird das Verzeichnis gesteuert, in dem iisnode die Protokollierung für stdout/stderr durchführt. Der Standardwert ist „iisnode“, der relativ zum Skripthauptverzeichnis gilt (Verzeichnis, in dem die Hauptversion von „main server.js“ enthalten ist).

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Mit dieser Einstellung wird gesteuert, welche Version von Node-Inspector von iisnode beim Debuggen der Node-Anwendung verwendet wird. Derzeit sind „iisnode-inspector-0.7.3.dll“ und „iisnode-inspector.dll“ die einzigen beiden gültigen Werte für diese Einstellung. Der Standardwert lautet „iisnode-inspector-0.7.3.dll“. Für die Version „iisnode-inspector-0.7.3.dll“ werden „node-inspector-0.7.3“ und Websockets verwendet. Aktivieren Sie Websockets in Ihrer Azure-Web-App, um diese Version nutzen zu können. Ausführlichere Informationen zur Konfiguration von iisnode zur Verwendung der neuen Node-Inspector-Version finden Sie unter <https://ranjithblogs.azurewebsites.net/?p=98>.

### <a name="flushresponse"></a>flushResponse

Das Standardverhalten von IIS besteht darin, dass Antwortdaten von bis zu 4 MB vor dem Leeren oder bis zum Ende der Antwort (je nachdem, was früher eintritt) gepuffert werden. iisnode enthält eine Konfigurationseinstellung zum Außerkraftsetzen dieses Verhaltens. Um ein Fragment des Antwortentität-Textkörpers zu leeren, sobald iisnode es von „node.exe“ empfängt, müssen Sie das iisnode/@flushResponse-Attribut in „web.config“ auf „true“ festlegen:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Das Aktivieren der Leerung jedes Fragments des Antwortentität-Textkörpers führt zu einem Leistungsmehraufwand, der den Durchsatz des Systems um ca. 5 % reduziert (ab v0.1.13). Es empfiehlt sich, diese Einstellung nur für Endpunkte zu verwenden, die Antwortstreaming erfordern (beispielsweise mit dem `<location>`-Element in der Datei „web.config“).

Für das Streaming von Anwendungen müssen Sie außerdem responseBufferLimit für Ihren iisnode-Handler auf 0 festlegen.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Eine durch Semikolons getrennte Liste mit Dateien, die auf Änderungen überprüft werden. Jede Änderung einer Datei bewirkt, dass die Anwendung „recycelt“ wird. Jeder Eintrag besteht aus einem optionalen Verzeichnisnamen und einem erforderlichen Dateinamen. Diese Namen gelten relativ zu dem Verzeichnis, in dem sich der Haupteinstiegspunkt der Anwendung befindet. Platzhalter sind nur im Dateinamenteil zulässig. Der Standardwert ist `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Der Standardwert ist „FALSE“. Wenn diese Einstellung aktiviert ist, kann die Node-Anwendung eine Verbindung mit einer benannten Pipe herstellen (Umgebungsvariable IISNODE\_CONTROL\_PIPE) und eine „recycle“-Nachricht senden. Dies bewirkt, dass w3wp korrekt recycelt wird.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Der Standardwert ist 0. Dies bedeutet, dass das Feature aktiviert ist. Wenn ein höherer Wert als 0 angegeben ist, lagert iisnode alle untergeordneten Prozesse jeweils nach Ablauf der „idlePageOutTimePeriod“ (in Millisekunden) aus. Grundlegende Informationen zur Auslagerung finden Sie in der entsprechenden [Dokumentation](/windows/desktop/api/psapi/nf-psapi-emptyworkingset). Diese Einstellung ist für Anwendungen nützlich, die viel Arbeitsspeicher verbrauchen und für die Arbeitsspeicher von Zeit zu Zeit auf den Datenträger ausgelagert werden soll, um RAM freizugeben.

> [!WARNING]
> Gehen Sie mit Bedacht vor, wenn Sie die folgenden Konfigurationseigenschaften für Produktionsanwendungen aktivieren. Es wird empfohlen, die Aktivierung nicht für aktive Produktionsanwendungen durchzuführen.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Der Standardwert ist „FALSE“. Wenn „true“ festgelegt ist, fügt iisnode jeder gesendeten HTTP-Antwort den HTTP-Antwortheader `iisnode-debug` hinzu. Der Headerwert `iisnode-debug` ist eine URL. Sie können einzelne Teile der Diagnoseinformationen ermitteln, indem Sie sich das URL-Fragment ansehen. Wenn Sie eine Visualisierung wünschen, öffnen Sie die URL in einem Browser.

### <a name="loggingenabled"></a>loggingEnabled

Mit dieser Einstellung wird die Protokollierung von stdout und stderr durch iisnode gesteuert. iisnode erfasst stdout/stderr von Knotenprozessen, die gestartet werden, und führt Schreibvorgänge in das Verzeichnis durch, das unter der Einstellung „logDirectory“ angegeben ist. Nach der Aktivierung schreibt Ihre Anwendung Protokolle in das Dateisystem. Je nach Umfang der von der Anwendung durchgeführten Protokollierung kann es hierbei auch zu Leistungseinbußen kommen.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Der Standardwert ist „FALSE“. Bei der Festlegung auf „true“ zeigt iisnode den HTTP-Statuscode und win32-Fehlercode in Ihrem Browser an. Der win32-Code ist hilfreich beim Debuggen bestimmter Problemtypen.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (nicht für aktive Produktionswebsite aktivieren)

Mit dieser Einstellung wird die Debugfunktion gesteuert. iisnode ist in Node-Inspector integriert. Indem Sie diese Einstellung aktivieren, aktivieren Sie das Debuggen Ihrer Node-Anwendung. Nach der Aktivierung dieser Einstellung erstellt iisnode die erforderlichen Node-Inspector-Dateien nach der ersten Debuganforderung an Ihre Node-Anwendung im Verzeichnis „debuggerVirtualDir“. Sie können den Node-Inspector laden, indem Sie eine Anforderung an `http://yoursite/server.js/debug` senden. Sie können das Debug-URL-Segment mit der Einstellung „debuggerPathSegment“ steuern. Standardmäßig lautet sie „debuggerPathSegment=’debug’“. Sie können `debuggerPathSegment` beispielsweise auf eine GUID festlegen, damit die Ermittlung durch andere Personen erschwert wird.

Weitere Informationen zum Debuggen finden Sie unter [Debug node.js applications on Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) (Debuggen von node.js-Anwendungen unter Windows).

## <a name="scenarios-and-recommendationstroubleshooting"></a>Szenarien und Empfehlungen/Problembehandlung

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Meine Node-Anwendung führt zu viele ausgehende Aufrufe durch.

Viele Anwendungen stellen im Rahmen ihres normalen Betriebs ausgehende Verbindungen her. Wenn beispielsweise eine Anforderung empfangen wird, nimmt Ihre Knoten-App in der Regel Kontakt mit einer REST-API an einem anderen Ort auf, um Informationen zur Verarbeitung der Anforderung zu erhalten. Es ist ratsam, für http- oder https-Aufrufe einen Keep-Alive-Agent zu verwenden. Sie können zum Durchführen dieser ausgehenden Aufrufe das agentkeepalive-Modul als Keep-Alive-Agent verwenden.

Das agentkeepalive-Modul stellt sicher, dass Sockets auf Ihrer Azure-Web-App-VM wieder verwendet werden. Das Erstellen eines neuen Sockets für jede ausgehende Anforderung erzeugt zusätzlichen Mehraufwand für Ihre Anwendung. Durch die Wiederverwendung von Sockets für ausgehende Anforderungen wird sichergestellt, dass Ihre Anwendung die pro VM zugeordnete maximale Anzahl von Sockets (maxSockets) nicht überschreitet. Die Empfehlung für Azure App Service lautet, den maxSockets-Wert für agentKeepAlive auf insgesamt 128 Sockets pro VM (4 Instanzen von „node.exe“ \* 32 maxSockets/Instanz) festzulegen.

Beispielkonfiguration für [agentKeepAlive](https://www.npmjs.com/package/agentkeepalive):

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 32,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> In diesem Beispiel wird davon ausgegangen, dass Sie vier „node.exe“-Instanzen auf Ihrer VM ausführen. Wenn Sie eine andere Anzahl von „node.exe“-Instanzen auf der VM ausführen, müssen Sie die maxSockets-Einstellung entsprechend ändern.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Der CPU-Verbrauch meiner Node-Anwendung ist zu hoch.

Möglicherweise erhalten Sie von Azure App Service in Ihrem Portal eine Empfehlung zum hohen CPU-Verbrauch. Sie können auch Monitore einrichten, mit denen bestimmte [Metriken](web-sites-monitor.md) überwacht werden. Sehen Sie sich beim Überprüfen der CPU-Nutzung im [Azure-Portal-Dashboard](../azure-monitor/platform/metrics-charts.md)die MAX-Werte für die CPU an, damit Ihnen die Spitzenwerte nicht entgehen.
Falls Sie der Meinung sind, dass der CPU-Verbrauch Ihrer Anwendung zu hoch ist, und den Grund dafür nicht kennen, können Sie zur Überprüfung ein Profil für Ihre Node-Anwendung erstellen.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilerstellung für die Node-Anwendung in Azure App Service mit V8-Profiler

Angenommen, Sie verfügen über eine Hello World-App, für die Sie wie folgt ein Profil erstellen möchten:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Wechseln Sie zur Website der Debugging-Konsole unter `https://yoursite.scm.azurewebsites.net/DebugConsole`.

Wechseln Sie in das Verzeichnis „site/wwwroot“. Es wird eine Eingabeaufforderung wie im folgenden Beispiel angezeigt:

![Screenshot: Verzeichnis „site/wwwroot“ und Eingabeaufforderung](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Führen Sie den Befehl `npm install v8-profiler` aus.

Dieser Befehl installiert v8-profiler im Verzeichnis „node\_modules“ und für alle Abhängigkeiten.
Bearbeiten Sie nun die Datei „server.js“, um das Profil für Ihre Anwendung zu erstellen.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Mit dem obigen Code wird das Profil für die WriteConsoleLog-Funktion erstellt, und anschließend wird die Profilausgabe in die Datei „profile.cpuprofile“ im Verzeichnis „site/wwwroot“ geschrieben. Senden Sie eine Anforderung an die Anwendung. Sie sehen, dass unter „site/wwwroot“ die Datei „profile.cpuprofile“ erstellt wird.

![Screenshot: Datei „profile.cpuprofile“](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Laden Sie diese Datei herunter, und öffnen Sie sie mit den F12-Tools von Chrome. Drücken Sie in Chrome die F12-TASTE, und wählen Sie anschließend die Registerkarte **Profile**. Wählen Sie die Schaltfläche **Laden**. Wählen Sie die Datei „profile.cpuprofile“ aus, die Sie heruntergeladen haben. Klicken Sie auf das Profil, das Sie gerade geladen haben.

![Screenshot: Die von Ihnen geladene Datei „profile.cpuprofile“](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Sie sehen, dass 95% der Zeit von der WriteConsoleLog-Funktion verbraucht wurde. Die Ausgabe enthält auch die genauen Zeilennummern und Quelldateien, die das Problem verursacht haben.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Der Arbeitsspeicherverbrauch meiner Node-Anwendung ist zu hoch

Wenn Ihre Anwendung zu viel Arbeitsspeicher verbraucht, wird für Azure App Service im Portal ein entsprechender Hinweis angezeigt. Sie können Monitore einrichten, mit denen bestimmte [Metriken](web-sites-monitor.md) überwacht werden. Sehen Sie sich beim Überprüfen der Arbeitsspeichernutzung im [Azure-Portal-Dashboard](../azure-monitor/platform/metrics-charts.md) die MAX-Werte für den Arbeitsspeicher an, damit Ihnen die Spitzenwerte nicht entgehen.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Erkennung von Arbeitsspeicherverlusten und Heap-Diff für „node.js“

Sie können [node-memwatch](https://github.com/lloyd/node-memwatch) als Unterstützung beim Identifizieren von Speicherverlusten verwenden.
Sie können `memwatch` genau wie v8-profiler installieren und Ihren Code bearbeiten, um Heaps zu erfassen und zu vergleichen und so die Arbeitsspeicherverluste der Anwendung zu identifizieren.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Meine „node.exe“-Instanzen werden nach dem Zufallsprinzip beendet

Es gibt einige Gründe, die dazu führen können, dass „node.exe“ zufällig beendet wird:

1. Ihre Anwendung löst nicht abgefangene Ausnahmen aus. Suchen Sie in der Datei „d:\\home\\LogFiles\\Application\\logging-errors.txt“ nach Details zur ausgelösten Ausnahme. Diese Datei enthält die Stapelüberwachung, die zum Debuggen und Beheben von Anwendungsfehlern verwendet werden kann.
2. Ihre Anwendung verbraucht zu viel Arbeitsspeicher, und dies wirkt sich auf den Start anderer Prozesse aus. Wenn der gesamte VM-Arbeitsspeicher nahe bei 100 % liegt, werden Ihre „node.exe“-Instanzen möglicherweise vom Prozess-Manager beendet. Der Prozess-Manager beendet einige Prozesse, damit andere Prozesse ausgeführt werden können. Erstellen Sie zum Beheben dieses Problems ein Profil zur Erkennung von Arbeitsspeicherverlusten für Ihre Anwendung. Falls die Anwendung viel Arbeitsspeicher benötigt, skalieren Sie auf eine größere VM hoch (dadurch wird der für die VM verfügbare RAM erhöht).

### <a name="my-node-application-does-not-start"></a>Meine Node-Anwendung startet nicht

Wenn die Anwendung beim Starten Fehler vom Typ 500 zurückgibt, kann dies verschiedene Gründe haben:

1. „node.exe“ ist nicht am richtigen Speicherort vorhanden. Überprüfen Sie die Einstellung nodeProcessCommandLine.
2. Die Hauptskriptdatei ist nicht am richtigen Speicherort vorhanden. Überprüfen Sie die Datei „web.config“, und stellen Sie sicher, dass der Name der Hauptskriptdatei im Abschnitt „handlers“ mit der Hauptskriptdatei übereinstimmt.
3. Die web.config-Konfiguration ist nicht korrekt. Überprüfen Sie die Namen und Werte der Einstellungen.
4. Kaltstart: Der Startvorgang Ihrer Anwendung dauert zu lange. Wenn die Anwendung länger als (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 Sekunden benötigt, gibt iisnode Fehler 500 zurück. Erhöhen Sie die Werte dieser Einstellungen, um sie an die Startdauer der Anwendung anzupassen. So wird verhindert, dass für iisnode ein Timeout auftritt und der Fehler 500 zurückgegeben wird.

### <a name="my-node-application-crashed"></a>Meine Node-Anwendung ist abgestürzt

Ihre Anwendung löst nicht abgefangene Ausnahmen aus. Suchen Sie in der Datei `d:\\home\\LogFiles\\Application\\logging-errors.txt` nach Details zur ausgelösten Ausnahme. Diese Datei enthält die Stapelüberwachung, die zum Diagnostizieren und Beheben von Anwendungsfehlern verwendet werden kann.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Meine Node-Anwendung benötigt zu viel Zeit zum Starten (Kaltstart)

Die häufigste Ursache für lange Startzeiten der Anwendung ist eine hohe Anzahl von Dateien im Verzeichnis „node\_modules“. Die Anwendung versucht, die meisten dieser Dateien beim Starten zu laden. Da Ihre Dateien auf der Netzwerkfreigabe in Azure App Service gespeichert werden, kann das Laden vieler Dateien standardmäßig lange dauern.
Beispiele für Lösungen, um diesen Prozess zu beschleunigen:

1. Stellen Sie sicher, dass Sie über eine flache Abhängigkeitsstruktur verfügen und doppelte Abhängigkeiten vermeiden, indem Sie npm3 zum Installieren der Module verwenden.
2. Versuchen Sie, Ihre node\_modules nach und nach und nicht auf einmal beim Starten der Anwendung zu laden. Um Module verzögert zu laden, sollte der Aufruf von „require(‚module‘)“ vor der ersten Ausführung des Modulcodes erfolgen, wenn Sie das Modul tatsächlich innerhalb der Funktion benötigen.
3. Azure App Service bietet ein Feature mit dem Namen „Lokaler Cache“. Bei diesem Feature wird Ihr Inhalt von der Netzwerkfreigabe auf den lokalen Datenträger auf der VM kopiert. Da die Dateien lokal vorhanden sind, geht das Laden von node\_modules deutlich schneller.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE-http-Status und -Unterstatus

In der `cnodeconstants`-[Quelldatei](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) sind alle möglichen Status/Unterstatus-Kombinationen aufgeführt, die von iisnode bei einem Fehler zurückgegeben werden können.

Aktivieren Sie FREB für Ihre Anwendung, um den win32-Fehlercode anzuzeigen. (Stellen Sie aus Leistungsgründen sicher, dass Sie FREB nur für Websites aktivieren, die nicht für die Produktion bestimmt sind.)

| Http-Status | Http-Unterstatus | Möglicher Grund? |
| --- | --- | --- |
| 500 |1000 |Es besteht ein Problem mit der Zustellung der Anforderung an IISNODE. Überprüfen Sie, ob „node.exe“ gestartet wurde. Unter Umständen ist „node.exe“ beim Starten abgestürzt. Überprüfen Sie die web.config-Konfiguration auf Fehler. |
| 500 |1001 |- Win32Error 0x2 – App reagiert nicht auf die URL. Überprüfen Sie die URL Rewrite-Regeln bzw. die Richtigkeit der Routendefinition Ihrer Express-App. - Win32Error 0x6d – Benannte Pipe ist beschäftigt – „node.exe“ akzeptiert keine Anforderungen, weil die Pipe beschäftigt ist. Überprüfen Sie den hohen CPU-Verbrauch. - Andere Fehler – Überprüfen Sie, ob „node.exe“ abgestürzt ist. |
| 500 |1002 |„node.exe“ abgestürzt: Überprüfen Sie „d:\\home\\LogFiles\\logging-errors.txt“ in Bezug auf die Stapelüberwachung. |
| 500 |1003 |Problem mit der Pipekonfiguration – die Konfiguration der Named Pipe ist falsch. |
| 500 |1004 - 1018 |Es ist ein Fehler aufgetreten, während die Anforderung gesendet oder die Antwort an bzw. von „node.exe“ verarbeitet wurde. Überprüfen Sie, ob „node.exe“ abgestürzt ist. Überprüfen Sie „d:\\home\\LogFiles\\logging-errors.txt“ in Bezug auf die Stapelüberwachung. |
| 503 |1000 |Nicht genügend Arbeitsspeicher zum Zuordnen von weiteren Verbindungen vom Typ „Benannte Pipe“. Überprüfen Sie, warum Ihre App so viel Arbeitsspeicher verbraucht. Überprüfen Sie den Wert der Einstellung maxConcurrentRequestsPerProcess. Wenn nicht „infinite“ (unendlich) festgelegt ist und Sie über eine hohe Zahl von Anforderungen verfügen, können Sie diesen Wert erhöhen, um den Fehler zu verhindern. |
| 503 |1001 |Die Anforderung konnte nicht an „node.exe“ zugestellt werden, da für die Anwendung gerade der Vorgang für die Wiederverwendung durchgeführt wird. Nach Abschluss des Vorgangs sollten Anforderungen wieder normal bereitgestellt werden. |
| 503 |1002 |Überprüfen Sie den win32-Fehlercode auf den tatsächlichen Grund. Die Anforderung konnte nicht für „node.exe“ zugestellt werden. |
| 503 |1003 |Die Named Pipe ist zu stark ausgelastet. Überprüfen Sie, ob „node.exe“ einen hohen CPU-Verbrauch aufweist. |

NODE.exe verfügt über eine Einstellung namens `NODE_PENDING_PIPE_INSTANCES`. In Azure App Service ist dieser Wert auf 5000 festgelegt. Dies bedeutet, dass „node.exe“ für die benannte Pipe 5000 Anforderungen auf einmal akzeptieren kann. Dieser Wert sollte für die meisten Node-Anwendungen ausreichen, die in Azure App Service ausgeführt werden. 503.1003 sollte in Azure App Service nicht angezeigt werden, da für `NODE_PENDING_PIPE_INSTANCES` ein hoher Wert gilt.

## <a name="more-resources"></a>Weitere Ressourcen

Unter den unten angegebenen Links finden Sie weitere Informationen zu „node.js“-Anwendungen für Azure App Service.

* [Erste Schritte mit Node.js-Web-Apps in Azure App Service](quickstart-nodejs.md)
* [Debuggen einer Node.js-Web-App in Azure App Service](/archive/blogs/azureossds/debugging-node-js-apps-on-azure-app-services)
* [Verwenden von Node.js-Modulen mit Azure-Anwendungen](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service-Web-Apps: Node.js](/archive/blogs/silverlining/windows-azure-websites-node-js)
* [Node.js Developer Center (in englischer Sprache)](../nodejs-use-node-modules-azure-apps.md)
* [Exploring the Super Secret Kudu Debug Console (Erkunden der geheimen Kudu-Debugkonsole)](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
