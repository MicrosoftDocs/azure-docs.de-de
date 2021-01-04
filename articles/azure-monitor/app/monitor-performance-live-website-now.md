---
title: Überwachen einer ASP.NET-Live-Web-App mit Azure Application Insights | Microsoft-Dokumentation
description: Überwachen Sie die Leistung einer Website, ohne sie erneut bereitzustellen. Funktioniert mit ASP.NET-Web-Apps, die lokal oder auf virtuellen Computern gehostet werden.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 53dbcc341fdd4bc194d34d40cdd2a975df496376
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186302"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrumentieren von Web-Apps zur Laufzeit mit Anfügen ohne Code in Application Insights

> [!IMPORTANT]
> Die Verwendung des Statusmonitors wird nicht mehr empfohlen, und **ab 1. Juni 2021** wird diese Version des Statusmonitors nicht mehr unterstützt. Er wurde durch den Azure Monitor Application Insights-Agent (früher Statusmonitor v2) ersetzt. Weitere Informationen finden Sie in der Dokumentation zu [lokalen Serverbereitstellungen](./status-monitor-v2-overview.md) oder [Bereitstellungen von Azure-VMs und in VM-Skalierungsgruppen](./azure-vm-vmss-apps.md).

Sie können eine Live-Web-App mit Azure Application Insights instrumentieren, ohne dass Sie Ihren Code ändern oder erneut bereitstellen müssen. Sie benötigen ein [Microsoft Azure](https://azure.com) -Abonnement.

Der Statusmonitor wird zum Instrumentieren einer in IIS lokal oder auf einem virtuellen Computer gehosteten .NET-Anwendung verwendet.

- Wenn Ihre App in einer Azure-VM oder einer Azure-VM-Skalierungsgruppe bereitgestellt wird, befolgen Sie [diese Anweisungen](azure-vm-vmss-apps.md).
- Wenn Ihre App in Azure App Services bereitgestellt wird, befolgen Sie [diese Anweisungen](azure-web-apps.md).
- Wenn Ihre App auf einer Azure-VM bereitgestellt ist, können Sie die Application Insights-Überwachung über die Azure-Systemsteuerung aktivieren.
- (Es gibt auch separate Artikel zum Instrumentieren von [Azure Cloud Services](./cloudservices.md).)


![Screenshot von App Insights-Übersichtsdiagrammen mit Informationen zu Anforderungsfehlern, Serverantwortzeit und Serveranforderungen](./media/monitor-performance-live-website-now/overview-graphs.png)

Bei der Anwendung von Application Insights auf die .NET-Webanwendungen können Sie zwischen zwei Möglichkeiten wählen:

* **Buildzeit:** [Fügen][greenbrown] Sie Ihrem Web-App-Code das Application Insights-SDK hinzu.
* **Laufzeit:** Instrumentieren Sie Ihre Web-App auf dem Server wie unten beschrieben, ohne den Code neu zu erstellen und bereitzustellen.

> [!NOTE]
> Wenn Sie die Instrumentierung zur Buildzeit verwenden, funktioniert die Runtimeinstrumentierung auch dann nicht, wenn sie aktiviert ist.

Hier ist zusammengefasst, was Sie jeweils erhalten:

|  | Buildzeit | Laufzeit |
| --- | --- | --- |
| **Anforderungen und Ausnahmen** |Ja |Ja |
| **[Ausführlichere Ausnahmen](./asp-net-exceptions.md)** | |Ja |
| **[Abhängigkeitsdiagnose](./asp-net-dependencies.md)** |Bei .NET 4.6 und höheren Versionen, aber weniger Details |Ja, vollständige Details: Ergebniscodes, SQL-Befehlstext, HTTP-Verb|
| **[Systemleistungsindikatoren](./performance-counters.md)** |Ja |Ja |
| **[API für benutzerdefinierte Telemetrie][api]** |Ja |Nein |
| **[Ablaufverfolgungsprotokoll-Integration](./asp-net-trace-logs.md)** |Ja |Nein |
| **[Seitenansicht und Benutzerdaten](./javascript.md)** |Ja |Nein |
| **Neuerstellung des Codes erforderlich** |Ja | Nein |



## <a name="monitor-a-live-iis-web-app"></a>Überwachen einer IIS-Live-Web-App

Wenn Ihre App auf einem IIS-Server gehostet wird, aktivieren Sie Application Insights über den Statusmonitor.

1. Melden Sie sich mit Administratorrechten auf Ihrem IIS-Webserver an.
2. Falls der Application Insights-Statusmonitor noch nicht installiert ist, [laden Sie den Statusmonitor-Installer herunter, und starten Sie ihn](#download).
3. Wählen Sie im Statusmonitor die installierte Webanwendung oder Website aus, die Sie überwachen möchten. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

    Konfigurieren Sie die Ressource, in der die Ergebnisse im Application Insights-Portal angezeigt werden sollen. (In der Regel empfiehlt es sich, eine neue Ressource zu erstellen. Wählen Sie eine vorhandene Ressource aus, falls Sie bereits über [Webtests][availability] oder über eine [Clientüberwachung][client] für die App verfügen.) 

    ![Wählen Sie eine App und eine Ressource.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Starten Sie IIS neu.

    ![Wählen Sie im oberen Bereich des Dialogfelds "Neustart".](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Der Webdienst wird für kurze Zeit unterbrochen.

## <a name="customize-monitoring-options"></a>Anpassen der Überwachungsoptionen

Wenn Sie Application Insights aktivieren, werden Ihrer Web-App DLLs und „ApplicationInsights.config“ hinzugefügt. Sie können [die CONFIG-Datei bearbeiten](./configuration-with-applicationinsights-config.md), um einige der Optionen zu ändern.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Erneutes Aktivieren von Application Insights bei erneuter Veröffentlichung der App

Überlegen Sie sich vor dem erneuten Veröffentlichen Ihrer App, ob Sie [Application Insights dem Code in Visual Studio hinzufügen möchten][greenbrown]. Sie erhalten ausführlichere Telemetriedaten und können benutzerdefinierte Telemetrie schreiben.

Wenn Sie die App erneut veröffentlichen möchten, ohne Application Insights dem Code hinzuzufügen, beachten Sie, dass die DLLs und „ApplicationInsights.config“ im Rahmen des Bereitstellungsprozesses unter Umständen aus der veröffentlichten Website gelöscht werden. Deshalb gilt Folgendes:

1. Falls Sie „ApplicationInsights.config“ bearbeitet haben, erstellen Sie eine Kopie der Datei, bevor Sie Ihre App erneut veröffentlichen.
2. Veröffentlichen Sie Ihre App erneut.
3. Reaktivieren Sie die Application Insights-Überwachung. (Verwenden Sie die geeignete Methode: entweder die Systemsteuerung der Azure-Web-App oder den Statusmonitor auf einem IIS-Host.)
4. Stellen Sie alle Änderungen wieder her, die Sie an der CONFIG-Datei vorgenommen haben.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Problembehandlung

### <a name="confirm-a-valid-installation"></a>Bestätigen einer gültigen Installation 

Dies sind einige Schritte, die Sie ausführen können, um zu bestätigen, dass die Installation erfolgreich war.

- Vergewissern Sie sich, dass sich die Datei „applicationInsights.config“ im Verzeichnis der Ziel-App befindet und den ikey enthält.

- Wenn Sie vermuten, dass Daten fehlen, können Sie eine Abfrage in [Analytics](../log-query/log-analytics-tutorial.md) ausführen, um alle Cloudrollen aufzulisten, die derzeit Telemetriedaten senden.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Wenn Sie sicherstellen möchten, dass Application Insights erfolgreich angefügt wurde, können Sie [Sysinternals Handle](/sysinternals/downloads/handle) in einem Befehlsfenster ausführen, um zu bestätigen, dass „applicationinsights.dll“ von IIS geladen wurde.

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Keine Verbindung möglich? Keine Telemetriedaten?

* Öffnen Sie [die erforderlichen ausgehenden Ports](./ip-addresses.md#outgoing-ports) in der Firewall des Servers, damit der Statusmonitor funktioniert.

### <a name="unable-to-login"></a>Anmeldung nicht möglich

Wenn der Statusmonitor keine Anmeldung ausführen kann, führen Sie die Installation stattdessen über die Befehlszeile aus. Der Statusmonitor versucht, eine Anmeldung auszuführen, um Ihren ikey abzurufen, Sie können diesen jedoch auch manuell mit folgendem Befehl angeben:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Datei oder Assembly „System.Diagnostics.DiagnosticSource“ konnte nicht geladen werden.

Diese Fehlermeldung wird möglicherweise nach dem Aktivieren von Application Insights ausgegeben. Dies liegt daran, dass das Installationsprogramm diese DLL-Datei in Ihrem Verzeichnis „bin“ ersetzt.
So aktualisieren Sie die Datei „web.config“, um dieses Problem zu beheben

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Wir verfolgen dieses Problem [hier](https://github.com/MohanGsk/ApplicationInsights-Home).


### <a name="application-diagnostic-messages"></a>Diagnosemeldungen der Anwendung

* Öffnen Sie den Statusmonitor, und wählen Sie Ihre Anwendung auf der linken Seite aus. Prüfen Sie, ob für diese Anwendung Diagnosemeldungen im Abschnitt mit den Konfigurationsbenachrichtigungen vorliegen:

  ![Öffnen Sie das Blatt „Leistung“, um die Anforderung, Antwortzeit, Abhängigkeiten und andere Daten anzuzeigen.](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Detaillierte Protokolle

* Standardmäßig gibt der Statusmonitor Diagnoseprotokolle an diesem Speicherort aus: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Um ausführliche Protokolle auszugeben, ändern Sie die Konfigurationsdatei `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config`, und fügen Sie in `appsettings``<add key="TraceLevel" value="All" />` hinzu.
Starten Sie dann den Statusmonitor neu.

* Da es sich bei dem Statusmonitor um eine .NET-Anwendung handelt, können Sie auch die [.NET-Ablaufverfolgung durch Hinzufügen der entsprechenden Diagnose zur Konfigurationsdatei](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element) aktivieren. In einigen Szenarien kann es beispielsweise hilfreich sein, durch [Konfigurieren der Netzwerkablaufverfolgung](/dotnet/framework/network-programming/how-to-configure-network-tracing) zu erfahren, was auf Netzwerkebene geschieht.

### <a name="insufficient-permissions"></a>Unzureichende Berechtigungen
  
* Wenn auf dem Server eine Meldung über "unzureichende Berechtigungen" angezeigt wird, versuchen Sie Folgendes:
  * Wählen Sie in IIS-Manager Ihren Anwendungspool aus, öffnen Sie **Erweiterte Einstellungen**, und überprüfen Sie die Identität unter **Prozessmodell**.
  * Fügen Sie in der Systemsteuerung "Computerverwaltung" diese Identität der Gruppe "Systemmonitorbenutzer" hinzu.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt mit System Center Operations Manager

* Es kann zu Versionskonflikten kommen, wenn Sie MMA/SCOM (Systems Center Operations Manager) auf Ihrem Server installiert haben. Deinstallieren Sie sowohl SCOM als auch den Statusmonitor, und installieren Sie die neuesten Versionen.

### <a name="failed-or-incomplete-installation"></a>Fehlerhafte oder unvollständige Installation

Wenn beim Statusmonitor während einer Installation ein Fehler auftritt, kann eventuell eine unvollständige Installation verbleiben, die der Statusmonitor nicht wiederherstellen kann. Dies erfordert ein manuelles Zurücksetzen.

Löschen Sie diese Dateien in Ihrem Anwendungsverzeichnis:
- Alle DLLs im Verzeichnis „bin“, die mit „Microsoft.AI“ oder „Microsoft.ApplicationInsights“ beginnen
- Diese DLL-Datei in Ihrem Verzeichnis „bin“: „Microsoft.Web.Infrastructure.dll“
- Diese DLL-Datei in Ihrem Verzeichnis „bin“: „System.Diagnostics.DiagnosticSource.dll“
- Entfernen Sie in Ihrem Anwendungsverzeichnis „App_Data\packages“.
- Entfernen Sie in Ihrem Anwendungsverzeichnis „applicationinsights.config“.


### <a name="additional-troubleshooting"></a>Weitere Informationen zur Problembehandlung

* Weitere Informationen zur [Problembehandlung][qna].

## <a name="system-requirements"></a>Systemanforderungen
Betriebssystemunterstützung für den Application Insights-Statusmonitor auf dem Server:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

mit den neuesten Service Packs und .NET Framework 4.5 (der Statusmonitor basiert auf dieser Version des Frameworks)

Auf der Clientseite: Windows 7, 8, 8.1 und 10, ebenfalls mit .NET Framework 4.5

IIS-Unterstützung: IIS 7, 7.5, 8, 8.5 (IIS ist erforderlich)

## <a name="automation-with-powershell"></a>Automation mit PowerShell
Sie können die Überwachung mit PowerShell auf Ihrem IIS-Server starten und beenden.

Importieren Sie zunächst das Application Insights-Modul:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

Ermitteln Sie, welche Apps überwacht werden:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Optional) Dies ist der Name einer Web-App.
* Zeigt den Application Insights-Überwachungsstatus für jede Web-App (oder die benannte App) in diesem IIS-Server an.
* Gibt `ApplicationInsightsApplication` für jede App zurück:

  * `SdkState==EnabledAfterDeployment`: Die App wird überwacht und wurde zur Laufzeit instrumentiert, und zwar entweder mit dem Statusmonitor-Tool oder mit `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Die App ist nicht für Application Insights instrumentiert. Entweder wurde sie nie instrumentiert, oder die Laufzeitüberwachung wurde mit dem Statusmonitor-Tool oder mit `Stop-ApplicationInsightsMonitoring`deaktiviert.
  * `SdkState==EnabledByCodeInstrumentation`: Die App wurde instrumentiert, indem das SDK dem Quellcode hinzugefügt wurde. Das SDK kann nicht aktualisiert oder beendet werden.
  * `SdkVersion` wird die Version angezeigt, die für die Überwachung dieser App verwendet wird.
  * Mit `LatestAvailableSdkVersion` wird die Version angezeigt, die im NuGet-Katalog derzeit verfügbar ist. Verwenden Sie zum Aktualisieren der App auf diese Version `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Gibt den Namen der App in IIS an.
* `-InstrumentationKey` Dies ist der iKey der Application Insights-Ressource, für die die Ergebnisse angezeigt werden sollen.
* Dieses Cmdlet wirkt sich nur auf Apps aus, die nicht bereits instrumentiert wurden (SdkState==NotInstrumented).

    Das Cmdlet wirkt sich nicht auf eine App aus, die bereits instrumentiert wurde. Es spielt keine Rolle, ob die App zur Buildzeit instrumentiert wurde, indem dem Code das SDK hinzugefügt wurde, oder zur Laufzeit durch eine vorherige Verwendung dieses Cmdlets.

    Die zum Instrumentieren der App verwendete SDK-Version ist die Version, die zuletzt auf diesen Server heruntergeladen wurde.

    Verwenden Sie zum Herunterladen der aktuellen Version „Update-ApplicationInsightsVersion“.
* Es wird `ApplicationInsightsApplication` zurückgegeben, wenn der Vorgang erfolgreich ist. Wenn nicht, wird unter stderr eine Ablaufverfolgung protokolliert.

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Dies ist der Name einer App in IIS.
* `-All` Beendet die Überwachung aller Apps auf diesem IIS-Server, für die `SdkState==EnabledAfterDeployment` gilt.
* Beendet die Überwachung der angegebenen Apps und entfernt die Instrumentierung. Dies funktioniert nur für Apps, die zur Laufzeit mit dem Tool für die Statusüberwachung oder mit „Start-ApplicationInsightsApplication“ überwacht wurden. (`SdkState==EnabledAfterDeployment`)
* Gibt „ApplicationInsightsApplication“ zurück.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Dies ist der Name einer Web-App in IIS.
* `-InstrumentationKey` (Optional.) Verwenden Sie dieses Element zum Ändern der Ressource, an die die Telemetriedaten der App gesendet werden.
* Mit diesem Cmdlet wird Folgendes durchgeführt:
  * Aktualisiert die benannte App auf die Version des SDK, die zuletzt auf den Computer heruntergeladen wurde. (Funktioniert nur, wenn `SdkState==EnabledAfterDeployment`gilt.)
  * Wenn Sie einen Instrumentierungsschlüssel angeben, wird die benannte App neu konfiguriert, um Telemetriedaten an die Ressource mit diesem Schlüssel zu senden. (Funktioniert, wenn `SdkState != Disabled`gilt.)

`Update-ApplicationInsightsVersion`

* Lädt das aktuelle Application Insights SDK auf den Server herunter.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Fragen zum Statusmonitor

### <a name="what-is-status-monitor"></a>Was ist der Statusmonitor?

Eine Desktopanwendung, die Sie auf Ihrem IIS-Webserver installieren. Sie dient zur Unterstützung beim Instrumentieren und Konfigurieren von Web-Apps. 

### <a name="when-do-i-use-status-monitor"></a>Wann sollte ich den Statusmonitor verwenden?

* Verwenden Sie ihn zum Instrumentieren aller Web-Apps, die auf Ihrem IIS-Server ausgeführt werden sollen. Dies ist auch möglich, wenn diese bereits ausgeführt werden.
* Verwenden Sie ihn beim Kompilieren zum Aktivieren von zusätzlichen Telemetriedaten für Web-Apps, die [mit dem Application Insights SDK erstellt wurden](./asp-net.md). 

### <a name="can-i-close-it-after-it-runs"></a>Kann ich den Statusmonitor nach dem Ausführen schließen?

Ja. Nachdem die ausgewählten Websites instrumentiert wurden, können Sie ihn schließen.

Der Statusmonitor selbst erfasst keine Telemetriedaten. Er dient nur zum Konfigurieren der Web-Apps und Festlegen einiger Berechtigungen.

### <a name="what-does-status-monitor-do"></a>Welche Schritte werden vom Statusmonitor ausgeführt?

Wenn Sie eine Web-App auswählen, die mit dem Statusmonitor instrumentiert werden soll, werden folgende Schritte ausgeführt:

* Die Application Insights-Assemblys und die Datei „ApplicationInsights.config“ werden heruntergeladen und im Binärdateiordner der Web-App abgelegt.
* Die CLR-Profilerstellung wird aktiviert, um Abhängigkeitsaufrufe zu erfassen.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Welche Version des Application Insights SDK wird vom Statusmonitor installiert?

Ab sofort können über den Statusmonitor nur noch die Application Insights SDK-Versionen 2.3 oder 2.4 installiert werden. 

Die Application Insights SDK-Version 2.4 ist die [letzte Version, die .NET 4.0 unterstützt](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1). Dieses hatte [im Januar 2016 das Ende des Lebenszyklus](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/) erreicht. Daher kann der Statusmonitor ab sofort zum Instrumentieren einer .NET 4.0-Anwendung verwendet werden. 

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Muss ich den Statusmonitor immer ausführen, wenn ich die App aktualisiere?

Dies ist nicht erforderlich, wenn Sie die erneute Bereitstellung inkrementell durchführen. 

Wenn Sie während des Veröffentlichungsvorgangs die Option „Vorhandene Dateien löschen“ auswählen, müssen Sie den Statusmonitor erneut ausführen, um Application Insights zu konfigurieren.

### <a name="what-telemetry-is-collected"></a>Welche Telemetriedaten werden erfasst?

Für Anwendungen, die Sie nur zur Laufzeit mit dem Statusmonitor instrumentieren:

* HTTP-Anforderungen
* Aufrufe von Abhängigkeiten
* Ausnahmen
* Leistungsindikatoren

Für Anwendungen, die beim Kompilieren bereits instrumentiert sind:

 * Prozessleistungsindikatoren
 * Abhängigkeitsaufrufe (.NET 4.5), Rückgabewerte in Abhängigkeitsaufrufen (.NET 4.6)
 * Werte der Ausnahmestapelüberwachung

[Weitere Informationen](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Herunterladen des Statusmonitors

- Verwenden des neuen [PowerShell-Moduls](./status-monitor-v2-overview.md)
- Laden Sie den [Statusmonitor-Installer](https://go.microsoft.com/fwlink/?LinkId=506648) herunter, und starten Sie ihn.
- Oder führen Sie den [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx) aus, und suchen Sie darin nach dem Application Insights-Statusmonitor.

## <a name="next-steps"></a><a name="next"></a>Nächste Schritte

Anzeigen der Telemetrie:

* [Untersuchen Sie Metriken](../platform/metrics-charts.md) für die Überwachung von Leistung und Auslastung.
* [Durchsuchen Sie Ereignisse und Protokolle][diagnostic], um Probleme zu diagnostizieren.
* [Verwenden Sie Analytics](../log-query/log-query-overview.md) für erweiterte Abfragen.

Hinzufügen weiterer Telemetrieelemente:

* [Erstellen Sie Webtests][availability], um sicherzustellen, dass Ihre Website live bleibt.
* [Fügen Sie Webclient-Telemetriedaten hinzu][usage], um Ausnahmen von Webseitencode anzuzeigen und Ablaufverfolgungsaufrufe einfügen zu können.
* [Fügen Sie Ihrem Code das Application Insights SDK hinzu][greenbrown], um Ablaufverfolgungs- und Protokollaufrufe einfügen zu können.

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md