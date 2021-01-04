---
title: Behandeln von Problemen mit Azure Application Insights Profiler
description: Dieser Artikel enthält Schritte zur Problembehandlung sowie Informationen, um Entwickler bei der Aktivierung oder Verwendung von Application Insights Profiler zu unterstützen.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d9acd322c454002613e21e8591c3e83aeec2d51e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995351"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Behandeln von Problemen mit dem Aktivieren oder Anzeigen von Application Insights Profiler

> [!CAUTION]
> Beim Ausführen des Profilers für ASP.NET Core-Apps in Azure App Service tritt ein Fehler auf. Wir haben eine Korrektur entwickelt, aber es dauert einige Wochen, bis diese weltweit bereitgestellt ist. Sie können den Fehler umgehen, indem Sie das Application Insights SDK zu Ihrer Anwendung hinzufügen. Eine Anleitung dazu finden Sie [hier](./asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Allgemeine Problembehandlung

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profile werden nur bei vorhandenen Anforderungen für die Anwendung während der Profiler-Ausführung hochgeladen.

Azure Application Insights Profiler sammelt Profiler-Daten jede Stunde zwei Minuten lang. Außerdem werden Daten gesammelt, wenn Sie im Bereich **Application Insights Profiler konfigurieren** die Schaltfläche **Jetzt Profil erstellen** auswählen. Die Profilerstellungsdaten werden jedoch nur hochgeladen, wenn sie an eine Anforderung angefügt werden können, die während der Profiler-Ausführung erfolgt ist. 

Profiler schreibt Meldungen zur Ablaufverfolgung und benutzerdefinierten Ereignissen in Ihre Application Insights-Ressource. Anhand dieser Ereignisse können Sie feststellen, wie Profiler ausgeführt wird. Wenn Profiler ausgeführt werden und Ablaufverfolgungen erfassen soll, diese auf der Seite **Leistung** aber nicht angezeigt werden, können Sie überprüfen, wie Profiler ausgeführt wird:

1. Suchen Sie nach Meldungen zur Ablaufverfolgung, die von Profiler an Ihre Application Insights-Ressource gesendet wurden. Anhand dieser Suchzeichenfolge können Sie die relevanten Daten suchen:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Die folgende Abbildung zeigt zwei Beispiele für Suchvorgänge aus zwei KI-Ressourcen: 
    
   * Auf der linken Seite erhält die Anwendung keine Anforderungen, während Profiler ausgeführt wird. Die Meldung erläutert, dass der Upload aufgrund von Inaktivität abgebrochen wurde. 

   * Auf der rechten Seite wurde Profiler gestartet und hat benutzerdefinierte Ereignisse gesendet, wenn während der Profiler-Ausführung Anforderungen erkannt wurden. Wenn das benutzerdefinierte Ereignis „ServiceProfilerSample“ angezeigt wird, bedeutet dies, dass Profiler eine Ablaufverfolgung an eine Anforderung angefügt hat. Die Ablaufverfolgung können Sie auf der Seite zur **Application Insights-Leistung** anzeigen.

     Wenn keine Telemetriedaten angezeigt werden, wird Profiler nicht ausgeführt. Informationen zur Problembehandlung finden Sie in den entsprechenden Abschnitten für Ihren spezifischen App-Typ weiter unten in diesem Artikel.  

     ![Suchen nach Profiler-Telemetriedaten][profiler-search-telemetry]

1. Wenn während der Ausführung von Profiler Anforderungen gesendet wurden, stellen Sie sicher, dass diese Anforderungen von der Komponente Ihrer Anwendung verarbeitet werden, die Profiler aktiviert hat. Anwendungen bestehen zwar manchmal aus mehreren Komponenten, Profiler ist jedoch nur für einige dieser Komponenten aktiviert. Im Bereich **Application Insights-Profiler konfigurieren** werden die Komponenten angezeigt, die Ablaufverfolgungen hochgeladen haben.

### <a name="other-things-to-check"></a>Weitere erforderliche Überprüfungen
* Vergewissern Sie sich, dass Ihre App unter .NET Framework 4.6 ausgeführt wird.
* Wenn Ihre Web-App eine ASP.NET Core-Anwendung ist, muss sie mindestens ASP.NET Core 2.0 ausführen.
* Falls die Daten, die Sie anzeigen möchten, bereits mehrere Wochen alt sind, schränken Sie Ihren Zeitfilter ein, und versuchen Sie es noch mal. Ablaufverfolgungen werden nach sieben Tagen gelöscht.
* Stellen Sie sicher, dass der Zugriff auf https://gateway.azureserviceprofiler.net nicht durch Proxys oder durch eine Firewall blockiert wird.
* Profiler wird in App Service-Plänen vom Typ „Free“ oder „Shared“ nicht unterstützt. Wenn Sie einen dieser Pläne verwenden, sollten Sie eine Skalierung auf einen der „Basic“-Pläne durchführen. Profiler sollte dann funktionieren.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Doppelte Erfassung in parallelen Threads

Manchmal übersteigt die Gesamtzeitmetrik im Stapel-Viewer die Dauer der Anforderung.

Diese Situation kann eintreten, wenn einer Anforderung mehrere Threads zugeordnet sind, die parallel ausgeführt werden. In diesem Fall übersteigt die Gesamtzeit der Threads die verstrichene Zeit. Ein Thread wartet möglicherweise auf den Abschluss eines anderen Threads. Der Viewer versucht, solche Fälle zu erkennen und die nicht relevante Wartezeit zu ignorieren. Im Zweifelsfall werden jedoch eher zu viele Informationen angezeigt, um zu vermeiden, dass womöglich entscheidende Informationen ausgelassen werden.

Wenn Ihnen in Ihren Ablaufverfolgungen parallele Threads auffallen, bestimmen Sie die wartenden Threads, um den kritischen Pfad für die Anforderung ermitteln zu können. In der Regel wartet der Thread, der schnell in einen Wartezustand wechselt, einfach auf den Abschluss der anderen Threads. Konzentrieren Sie sich auf die anderen Threads, und ignorieren Sie die Zeit in den wartenden Threads.

### <a name="error-report-in-the-profile-viewer"></a>Fehlerbericht im Profil-Viewer
Senden Sie ein Supportticket über das Portal. Geben Sie dabei die Korrelations-ID aus der Fehlermeldung an.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Problembehandlung für Profiler in Azure App Service
Damit Profiler ordnungsgemäß funktioniert, müssen die folgenden Voraussetzungen erfüllt sein:
* Für die Web-App muss mindestens ein App Service-Plan mit Basic-Tarif verwendet werden.
* Für Ihre Web-App muss Application Insights aktiviert sein.
* Ihre Web-App muss die folgenden App-Einstellungen aufweisen:

    |App-Einstellung    | Wert    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey für Ihre Application Insights-Ressource    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Der Webauftrag **ApplicationInsightsProfiler3** muss ausgeführt werden. So überprüfen Sie den Webauftrag:
   1. Wechseln Sie zu [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. Wählen Sie im Menü **Tools** das **WebJobs-Dashboard** aus.  
      Der Bereich **WebJobs** wird geöffnet. 
   
      ![Screenshot: Bereich „WebJobs“, in dem der Name, der Status und die Uhrzeit der letzten Ausführung von Jobs angezeigt wird][profiler-webjob]   
   
   1. Klicken Sie zum Anzeigen der WebJob-Details einschließlich des Protokolls auf den Link **ApplicationInsightsProfiler3**.  
     Der Bereich **Continuous WebJob Details** (Details des fortlaufenden WebJobs) wird geöffnet.

      ![Screenshot: Bereich „Continuous WebJob Details“ (Details des fortlaufenden WebJobs).][profiler-webjob-log]

Wenn Sie nicht feststellen können, warum Profiler nicht funktioniert, haben Sie die Möglichkeit, das Protokoll herunterzuladen und an unser Team zu senden, um Unterstützung zu erhalten (serviceprofilerhelp@microsoft.com). 
    
### <a name="manual-installation"></a>Manuelle Installation

Wenn Sie Profiler konfigurieren, werden an den Einstellungen der Web-App Aktualisierungen vorgenommen. Sie können die Updates manuell anwenden, wenn dies für Ihre Umgebung erforderlich ist. Beispiel: Ihre Anwendung wird in einer Web-Apps-Umgebung für PowerApps ausgeführt. So wenden Sie Updates manuell an:

1. Öffnen Sie im **Steuerungsbereich der Web-App** die Option **Einstellungen**.

1. Legen Sie die **.NET Framework-Version** auf **v4.6** fest.

1. Legen Sie **Immer bereit** auf **Ein** fest.
1. Erstellen Sie die folgenden App-Einstellungen:

    |App-Einstellung    | Wert    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey für Ihre Application Insights-Ressource    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Zu viele aktive Profilerstellungssitzungen

Zurzeit können Sie Profiler für maximal vier Azure Web-Apps und Bereitstellungsslots aktivieren, die im selben Serviceplan ausgeführt werden. Wenn Sie mehr als vier Web-Apps in einem App Service-Plan ausführen, gibt Profiler möglicherweise die Meldung *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException* aus. Profiler wird für jede Web-App separat ausgeführt und versucht, für jede App eine Sitzung der Ereignisablaufverfolgung für Windows (ETW) Sitzung zu starten. Die Anzahl von ETW-Sitzungen, die gleichzeitig aktiv sein können, ist jedoch begrenzt. Wenn der Profiler-Webauftrag zu viele aktive Profilerstellungssitzungen meldet, verschieben Sie einige Web-Apps in einen anderen Serviceplan.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Bereitstellungsfehler: Verzeichnis nicht leer 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Wenn Sie Ihre Web-App erneut für eine Web-Apps-Ressource bereitstellen und Profiler aktiviert ist, wird möglicherweise eine Meldung wie die folgende angezeigt:

*Verzeichnis nicht leer „D:\\home\\site\\wwwroot\\App_Data\\jobs“*

Dieser Fehler tritt auf, wenn Sie Web Deploy über Skripts oder die Azure DevOps-Bereitstellungspipeline ausführen. Als Lösung fügen Sie die folgenden zusätzlichen Bereitstellungsparameter zum Web Deploy-Task hinzu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Diese Parameter löschen den Ordner, der von Application Insights Profiler verwendet wird, und heben die Sperre der erneuten Bereitstellung auf. Sie haben keine Auswirkungen auf die Profiler-Instanz, die gerade ausgeführt wird.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Wie kann ich feststellen, ob Application Insights Profiler ausgeführt wird?

Profiler wird als fortlaufender Webauftrag in der Web-App ausgeführt. Sie können die Web-App-Ressource im [Azure-Portal](https://portal.azure.com) öffnen. Überprüfen Sie im Bereich **WebJobs** den Status von **ApplicationInsightsProfiler**. Wenn er nicht ausgeführt wird, öffnen Sie **Protokolle**, um weitere Informationen zu erhalten.

## <a name="troubleshoot-vms-and-cloud-services"></a>Problembehandlung bei VMs und Cloud Services

>**Der Fehler im Profiler, der in WAD für Cloud Services enthalten ist, wurde behoben.** Die neueste Version von WAD (1.12.2.0) für Cloud Services funktioniert mit allen neueren Versionen des App Insights-SDK. Cloud Service-Hosts führen ein automatisches Upgrade für WAD durch, doch erfolgt dies nicht unmittelbar. Soll ein Upgrade erzwungen werden, können Sie den Dienst erneut bereitstellen oder den Knoten neu starten.

Führen Sie die folgenden drei Schritte aus, um festzustellen, ob Profiler von der Azure-Diagnose richtig konfiguriert ist: 
1. Erstens: Überprüfen Sie, ob die bereitgestellten Inhalte der Azure-Diagnosekonfiguration Ihren Erwartungen entsprechen. 

1. Zweitens: Vergewissern Sie sich, dass die Azure-Diagnose in der Profiler-Befehlszeile den richtigen iKey übergibt. 

1. Drittens: Prüfen Sie in der Profiler-Protokolldatei, ob Profiler ausgeführt wurde, aber einen Fehler zurückgegeben hat. 

Überprüfen der Einstellungen, die zum Konfigurieren der Azure-Diagnose verwendet wurden:

1. Melden Sie sich beim virtuellen Computer (VM) an, und öffnen Sie die Protokolldatei an diesem Speicherort. Die Plug-in-Version ist auf Ihrem Computer möglicherweise neuer.
    
    Für VMs:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Für Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. Suchen Sie in der Datei nach der Zeichenfolge **WadCfg**, um die Einstellungen zu finden, die zum Konfigurieren der Azure-Diagnose an den virtuellen Computer übergeben wurden. Sie können überprüfen, ob der von der Profiler-Senke verwendete iKey richtig ist.

1. Überprüfen Sie die Befehlszeile, mit der Profiler gestartet wird. Die zum Starten von Profiler verwendeten Argumente befinden sich in der folgenden Datei. (Das Laufwerk kann „c:“ oder „d:“ sein, und das Verzeichnis ist möglicherweise ausgeblendet.)

    Für VMs:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    Für Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Stellen Sie sicher, dass der iKey in der Profiler-Befehlszeile richtig ist. 

1. Überprüfen Sie die Profiler-Protokolldatei namens **BootstrapN.log** anhand des Pfads in der vorherigen Datei *config.json*. Darin werden die Debuginformationen angezeigt, welche die von Profiler verwendeten Einstellungen angeben. Außerdem werden Status- und Fehlermeldungen von Profiler angezeigt.  

    Für VMs befindet sich die Datei in der Regel hier:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Für Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Wenn Profiler ausgeführt wird, während Ihre Anwendung Anforderungen empfängt, wird die folgende Meldung angezeigt: *Aktivität aus iKey erkannt*. 

    Wenn die Ablaufverfolgung hochgeladen wird, wird die folgende Meldung angezeigt: *Start to upload trace* (Upload der Ablaufverfolgung starten). 


## <a name="edit-network-proxy-or-firewall-rules"></a>Bearbeiten von Netzwerkproxy- oder Firewallregeln

Wenn Ihre Anwendung über einen Proxy oder über eine Firewall mit dem Internet verbunden ist, müssen Sie ggf. die Regeln bearbeiten, damit Ihre Anwendung mit dem Application Insights Profiler-Dienst kommunizieren kann. Die von Application Insights Profiler verwendeten IP-Adressen sind im Azure Monitor-Diensttag enthalten.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
