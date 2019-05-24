---
title: 'Häufig gestellte Fragen zur Anwendungsleistung: Azure App Service | Microsoft-Dokumentation'
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zu Verfügbarkeits-, Leistungs- und Anwendungsproblemen in Azure App Service-Web-Apps.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: f455985d2a7d05f45100d4a88b43c688fe1a7767
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955757"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Häufig gestellte Fragen zur Anwendungsleistung von Web-Apps in Azure

> [!NOTE]
> Einige der folgende Richtlinien funktionieren eventuell nur mit Windows oder Linux App Services. Linux App Services werden beispielsweise standardmäßig im 64-Bit-Modus ausgeführt.
>

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zu Problemen mit der Anwendungsleistung bei [Azure App Service-Web-Apps](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Warum ist meine App langsam?

Mehrere Faktoren können für eine langsame App-Leistung verantwortlich sein. Ausführliche Schritte zur Problembehandlung finden Sie unter [Problembehandlung bei niedriger Web-App-Leistung in Azure App Service](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Wie behebe ich das Problem einer hohen CPU-Auslastung?

In einigen Szenarien mit hoher CPU-Auslastung kann Ihre App tatsächlich mehr Computeressourcen benötigen. Erwägen Sie in diesem Fall eine Skalierung auf eine höhere Dienstebene, damit die Anwendung alle Ressourcen erhält, die sie benötigt. In einigen Fällen wird eine hohe CPU-Auslastung möglicherweise durch eine fehlerhafte Schleife oder einen fehlerhaften Programmierstil verursacht. Das Gewinnen von Einblicken, wodurch die gestiegene CPU-Auslastung ausgelöst wird, erfolgt in zwei Schritten. Erstellen Sie zunächst ein Speicherabbild des Prozesses, das Sie anschließend untersuchen. Weitere Informationen finden Sie unter [Capture and analyze a dump file for high CPU consumption for Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/) (Erfassen und Analysieren einer Speicherabbilddatei bei hohe CPU-Nutzung durch Web-Apps).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Wie behebe ich das Problem einer hohen Arbeitsspeicherbelegung?

In einigen Szenarien mit hoher Arbeitsspeicherbelegung kann Ihre App tatsächlich mehr Computeressourcen benötigen. Erwägen Sie in diesem Fall eine Skalierung auf eine höhere Dienstebene, damit die Anwendung alle Ressourcen erhält, die sie benötigt. Mitunter kann ein Fehler im Code einen Arbeitsspeicherverlust verursachen. Ein bestimmter Programmierstil kann auch die Arbeitsspeichernutzung erhöhen. Das Gewinnen von Einblicken, wodurch die gestiegene Arbeitsspeichernutzung ausgelöst wird, erfolgt in zwei Schritten. Erstellen Sie zunächst ein Speicherabbild des Prozesses, das Sie anschließend untersuchen. Mit dem Tool Crash Diagnoser im Katalog mit den Azure-Websiteerweiterungen können Sie diese beiden Schritte effizient ausführen. Weitere Informationen finden Sie unter [Capture and analyze a dump file for intermittent high memory for Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/) (Erfassen und Analysieren einer Speicherabbilddatei bei zeitweiliger hoher Arbeitsspeichernutzung durch Web-Apps).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Wie automatisiere ich App Service-Web-Apps mithilfe von PowerShell?

Sie können PowerShell-Cmdlets verwenden, um App Service-Web-Apps zu verwalten und zu warten. Im Blogbeitrag [Automate web apps hosted in Azure App Service by using PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/) (Automatisieren von Web-Apps, die in Azure App Service gehostet werden, mithilfe von PowerShell) wird beschrieben, wie Sie auf Azure Resource Manager basierende PowerShell-Skripts zum Automatisieren gängiger Aufgaben verwenden können. Der Blogbeitrag enthält außerdem Beispielcode für verschiedene Verwaltungsaufgaben für Web-Apps. Beschreibungen und Syntax für alle Cmdlets für App Service-Web-Apps-finden Sie unter [Az.Websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Wie zeige ich Ereignisprotokolle meiner Web-App an?

So zeigen Sie die Ereignisprotokolle für Ihre Web-App an

1. Melden Sie sich bei Ihrer [Kudu-Website](https://*yourwebsitename*.scm.azurewebsites.net) an.
2. Wählen Sie im Menü **Debug Console** > **CMD** aus.
3. Wählen Sie den Ordner **LogFiles** aus.
4. Zum Überprüfen von Ereignisprotokollen aktivieren Sie das Stiftsymbol neben **eventlog.xml**.
5. Um die Protokolle herunterzuladen, führen Sie das PowerShell-Cmdlet `Save-AzureWebSiteLog -Name webappname` aus.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Wie erfasse ich ein Speicherabbild im Benutzermodus meiner Web-App?

So erfassen Sie ein Speicherabbild im Benutzermodus Ihrer Web-App

1. Melden Sie sich bei Ihrer [Kudu-Website](https://*yourwebsitename*.scm.azurewebsites.net) an.
2. Wählen Sie das Menü **Process Explorer** aus.
3. Klicken Sie mit der rechten Maustaste auf den Prozess **w3wp.exe** oder Ihren WebJob-Prozess.
4. Wählen Sie **Download Memory Dump** > **Download Memory Dump** (Speicherabbild herunterladen > Vollständiges Speicherabbild) aus.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Wie zeige ich Informationen auf Prozessebene für meine Web-App an?

Sie haben zwei Optionen zum Anzeigen von Informationen auf Prozessebene für Ihre Web-App:

*   Führen Sie im Azure-Portal die folgenden Schritte aus:
    1. Öffnen Sie den **Prozess-Explorer** für die Web-App.
    2. Um die Details anzuzeigen, wählen Sie den Prozess **w3wp.exe** aus.
*   Gehen Sie in der Kudu-Konsole so vor:
    1. Melden Sie sich bei Ihrer [Kudu-Website](https://*yourwebsitename*.scm.azurewebsites.net) an.
    2. Wählen Sie das Menü **Process Explorer** aus.
    3. Wählen Sie für den Prozess **w3wp.exe** die Option **Properties** (Eigenschaften) aus.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Wenn ich zu meiner App navigiere, wird „Fehler 403 – Diese Web-App wird beendet“ angezeigt. Wie löse ich dieses Problem?

Drei Bedingungen können diesen Fehler verursachen:

* Für die Web-App wurde ein Abrechnungslimit erreicht, weshalb Ihre Website deaktiviert wurde.
* Die Web-App wurde im Portal beendet.
* Die Web-App hat einen Kontingentgrenzwert für Ressourcen erreicht, der für einen Serviceplan des Typs „Free“ oder „Shared“ gelten kann.

Um die Ursache des Fehlers zu ermitteln und das Problem zu beheben, führen Sie die Schritte unter [Web-Apps: Error 403 – This web app is stopped](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/) (Web-Apps: Fehler 403 – Diese Web-App wurde beendet) aus.

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Wo kann ich mehr zu Kontingenten und Limits für verschiedene App Service-Pläne erfahren?

Informationen zu Kontingenten und Limits finden Sie unter [App Service-Limits](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Wie verringere ich die Antwortzeit für die erste Anforderung nach einer Leerlaufzeit?

Web-Apps werden standardmäßig entladen, wenn sie einen festgelegten Zeitraum im Leerlauf waren. Dadurch kann das System Ressourcen sparen. Der Nachteil ist, dass die Antwort auf die erste Anforderung nach Entladen der Web-App länger dauert, um der Web-App das Laden und Beginnen mit dem Senden von Antworten zu ermöglichen. In den Serviceplänen „Basic“ und „Standard“ können Sie die Einstellung **Always On** aktivieren, damit die App stets geladen bleibt. Dies verhindert nach einer Leerlaufzeit der App längere Ladezeiten. So ändern Sie die **Always On**-Einstellung

1. Navigieren Sie im Azure-Portal zu Ihrer Web-App.
2. Wählen Sie **Anwendungseinstellungen** aus.
3. Aktivieren Sie für **Always On** die Einstellung **Ein**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Wie schalte ich die Ablaufverfolgung für Anforderungsfehler ein?

So schalten Sie die Ablaufverfolgung für Anforderungsfehler ein

1. Navigieren Sie im Azure-Portal zu Ihrer Web-App.
3. Wählen Sie **Alle Einstellungen** > **Diagnoseprotokolle** aus.
4. Wählen Sie für **Ablaufverfolgung für Anforderungsfehler** die Einstellung **Ein**.
5. Wählen Sie **Speichern** aus.
6. Wählen Sie auf dem Web-App-Blatt **Tools** aus.
7. Wählen Sie **Visual Studio Online** aus.
8. Wenn die Einstellung nicht **Ein** ist, wählen Sie **Ein** aus.
9. Klicken Sie auf **Starten**.
10. Wählen Sie **Web.config** aus.
11. Fügen Sie „system.webServer“ diese Konfiguration hinzu (um eine bestimmte URL zu erfassen):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Um Probleme aufgrund langsamer Leistung zu beheben, fügen Sie diese Konfiguration hinzu (wenn die Erfassungsanforderung mehr als 30 Sekunden benötigt):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Wechseln Sie zum Herunterladen der Ablaufverfolgung für Anforderungsfehler im [Portal](https://portal.azure.com) zu Ihrer Website.
15. Wählen Sie **Extras** > **Kudu** > **Starten** aus.
18. Wählen Sie im Menü **Debug Console** > **CMD** aus.
19. Wählen Sie den Ordner **LogFiles** und dann den Ordner mit einem Namen aus, der mit **W3SVC** beginnt.
20. Um die XML-Datei anzuzeigen, klicken Sie auf das Stiftsymbol.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Mir wird eine Meldung angezeigt, die besagt, dass der Arbeitsprozess aufgrund des Grenzwerts „% Arbeitsspeicher“ einen Neustart angefordert habe. Wie behebe ich dieses Problem?

Der maximal verfügbare Arbeitsspeicher für einen 32-Bit-Prozess (selbst unter einem 64-Bit-Betriebssystem) ist 2 GB. Standardmäßig ist der Arbeitsprozess in App Service (für Kompatibilität mit älteren Webanwendungen) auf 32-Bit festgelegt.

Erwägen Sie einen Wechsel zu 64-Bit-Prozessen, damit Sie den zusätzlichen Arbeitsspeicher nutzen können, der in Ihrer Web-Worker-Rolle verfügbar ist. Dies löst einen Neustart der Web-App aus, weshalb Sie entsprechend planen müssen.

Beachten Sie außerdem, dass eine 64-Bit-Umgebung einen Serviceplan des Typs „Basic“ oder „Standard“ erfordert. Die Servicepläne „Free“ und „Shared“ werden stets in einer 32-Bit-Umgebung ausgeführt.

Weitere Informationen finden Sie unter [Konfigurieren von Web-Apps in App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Warum erfolgt für meine Anforderung nach 230 Sekunden ein Timeout?

Die Standardeinstellung für die Zeitüberschreitung nach Leerlauf von Azure Load Balancer ist vier Minuten. Dies ist im Allgemeinen ein angemessenes Zeitlimit für eine Reaktion auf eine Webanforderung. Wenn Ihre Web-App eine Hintergrundverarbeitung erfordert, wird empfohlen, mit Azure WebJobs zu arbeiten. Die Azure-Web-App kann WebJobs aufrufen und benachrichtigt werden, sobald die Hintergrundverarbeitung abgeschlossen ist. Sie können unter mehreren Methoden zum Verwenden von WebJobs wählen, einschließlich Warteschlangen und Trigger.

WebJobs werden zur Hintergrundverarbeitung eingesetzt. Sie können in einem WebJob so viel Hintergrundverarbeitung ausführen, wie Sie möchten. Weitere Informationen zu WebJobs finden Sie unter [Ausführen von Hintergrundaufgaben mit WebJobs](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core-Anwendungen, die in App Service gehostet werden, reagieren mitunter nicht mehr. Wie wird dieses Problem behoben?

Ein bekanntes Problem mit einer früheren [Kestrel-Version](https://github.com/aspnet/KestrelHttpServer/issues/1182) kann möglicherweise dazu führen, dass eine ASP.NET Core 1.0-App, die in App Service gehostet wird, zeitweise nicht mehr reagiert. Möglicherweise wird auch die folgende Meldung angezeigt: „Fehler bei der angegebenen CGI-Anwendung, und der Server hat den Prozess beendet.“

Dieses Problem wurde in der Kestrel-Version 1.0.2 behoben. Diese Version ist im Update für ASP.NET Core 1.0.3 enthalten. Um dieses Problem zu beheben, aktualisieren Sie Ihre App-Abhängigkeiten auf Kestrel 1.0.2. Alternativ können Sie eine der beiden Problemumgehungen nutzen, die im Blogbeitrag [ASP.NET Core 1.0 slow perf issues in App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites) (ASP.NET Core 1.0-Probleme aufgrund langsamer Leistung in App Service-Web-Apps) beschrieben werden.


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Ich kann meine Protokolldateien nicht in der Dateistruktur meiner Web-App finden. Wie kann ich sie finden?

Wenn Sie die Funktion „Lokaler Cache“ von App Service nutzen, ist die Ordnerstruktur der Ordner „LogFiles“ und „Data“ Ihrer App Service-Instanz betroffen. Bei Verwenden des lokalen Caches werden in den Speicherordnern „LogFiles“ und „Data“ Unterordner erstellt. Die Unterordner folgen dem Benennungsmuster „Eindeutiger Bezeichner + Zeitstempel“. Jeder Unterordner entspricht einer VM-Instanz, in der die Web-App ausgeführt wird oder wurde.

Um zu bestimmen, ob Sie den lokalen Cache verwenden, überprüfen Sie die App Service-Registerkarte **Anwendungseinstellungen**. Wenn die lokalen Cache verwendet wird, ist die App-Einstellung `WEBSITE_LOCAL_CACHE_OPTION` auf `Always` festgelegt.

Wenn Sie den lokalen Cache nicht verwenden und dieses Problem auftritt, senden Sie eine Supportanfrage.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Mir wird eine Meldung angezeigt, die besagt, dass ein Versuch erfolgt sei, auf eine Socket in einer Weise zuzugreifen, die gemäß ihren Zugriffsberechtigungen unzulässig ist. Wie löse ich dieses Problem?

Dieser Fehler tritt normalerweise auf, wenn die ausgehenden TCP-Verbindungen in der VM-Instanz voll ausgelastet sind. Im App Service werden Grenzwerte für die maximale Anzahl ausgehender Verbindungen erzwungen, die für jede VM-Instanz hergestellt werden können. Weitere Informationen finden Sie unter [Cross-VM numerical limits](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) (VM-übergreifende numerische Grenzwerte).

Dieser Fehler kann außerdem auftreten, wenn Sie versuchen, in Ihrer Anwendung auf eine lokale Adresse zuzugreifen. Weitere Informationen finden Sie unter [Local address requests](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests) (Anforderungen des Zugriffs auf lokale Adressen).

Weitere Informationen zu ausgehenden Verbindungen in Ihrer Web-App finden Sie im Blogbeitrag zu [ausgehenden Verbindungen mit Azure-Websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Wie verwende ich Visual Studio zum Remotedebuggen meiner App Service-Web-App?

Eine ausführliche exemplarische Vorgehensweise, die Ihnen zeigt, wie Sie Ihre Web-App mithilfe von Visual Studio debuggen, finden Sie unter [Remotedebuggen Ihrer App Service-Web-App](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
