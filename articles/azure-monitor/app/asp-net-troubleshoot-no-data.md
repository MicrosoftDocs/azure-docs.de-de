---
title: Problembehandlung ohne Daten – Application Insights für .NET
description: Sie sehen in Azure Application Insights keine Daten? Versuchen Sie es hier.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: 26ba586715c7b76ff8972c6574c3c29b837713a1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904464"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Problembehandlung ohne Daten – Application Insights für .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>Einige meiner Telemetriedaten fehlen
*In Application Insights wird nur ein Bruchteil der Ereignisse angezeigt, die von meiner App generiert werden.*

* Wenn immer der gleiche Anteil angezeigt wird, ist dies wahrscheinlich auf die adaptive [Stichprobenerstellung](./sampling.md)zurückzuführen. Um dies zu bestätigen, öffnen Sie „Suchen“ (auf dem Blatt „Übersicht“), und suchen Sie nach einer Instanz einer Anforderung oder eines anderen Ereignisses. Klicken Sie unten im Eigenschaftenbereich auf „...“, um die vollständigen Eigenschaftendetails anzuzeigen. Wenn „Anforderungsanzahl“ kleiner als 1 ist, ist die Stichprobenerstellung aktiviert.
* Andernfalls ist es möglich, dass Sie einen [Grenzwert für die Datenrate](./pricing.md#limits-summary) in Ihrem Tarif erreicht haben. Diese Grenzwerte gelten pro Minute.

*Daten gehen nach dem Zufallsprinzip verloren.*

* Überprüfen Sie, ob es im [Telemetriekanal](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) zu Datenverlust kommt.

* Suchen Sie nach bekannten Problemen im [GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) zum Telemetriekanal.

*In der Konsolen-App oder in der Web-App kommt es beim Beenden der App zu Datenverlusten.*

* Der SDK-Kanal speichert Telemetriedaten im Puffer und sendet sie in Batches. Wenn die Anwendung gerade heruntergefahren wird, müssen Sie möglicherweise [Flush()](api-custom-events-metrics.md#flushing-data) explizit aufrufen. Das Verhalten von `Flush()` hängt vom tatsächlich verwendeten [Kanal](telemetry-channels.md#built-in-telemetry-channels) ab.

## <a name="no-data-from-my-server"></a>Keine Daten vom Server
*Ich habe meine App auf meinem Webserver installiert, und nun werden mit keine Telemetriedaten von ihm angezeigt. Auf dem Entwicklungscomputer hat dies aber funktioniert.*

* Wahrscheinlich handelt es sich hierbei um ein Problem mit der Firewall. [Legen Sie Firewallausnahmen für Application Insights fest, damit das Senden von Daten möglich ist](./ip-addresses.md).
* In IIS-Server fehlen unter Umständen einige erforderliche Komponenten: .NET-Erweiterbarkeit 4.5 und ASP.NET 4.5.

*Ich habe [den Statusmonitor auf meinem Webserver installiert](./monitor-performance-live-website-now.md), um vorhandene Apps zu überwachen. Es werden keine Ergebnisse angezeigt.*

* Siehe [Problembehandlung für den Statusmonitor](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die [Verbindungszeichenfolge](./sdk-connection-string.md?tabs=net) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: Die Datei oder Assembly „Microsoft.AspNet TelemetryCorrelation“ konnte nicht geladen werden

Weitere Informationen zu diesem Fehler finden Sie unter [GitHub-Problem 1610] (https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610).

Beim Upgrade von älteren SDKs als 2.4 müssen Sie sicherstellen, dass die folgenden Änderungen auf `web.config` und `ApplicationInsights.config` angewendet werden:

1. Zwei HTTP-Module anstelle eines einzigen. In `web.config` sollten zwei HTTP-Module vorhanden sein. Die Reihenfolge ist für einige Szenarien von Bedeutung:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. In `ApplicationInsights.config` sollten Sie zusätzlich zu `RequestTrackingTelemetryModule` über das folgende Telemetriemodul verfügen:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

**_Wenn ein Upgrade nicht ordnungsgemäß durchgeführt wird, können unerwartete Ausnahmen auftreten oder Telemetriedaten nicht erfasst werden._* _


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Keine Option „Application Insights hinzufügen“ in Visual Studio
_Wenn ich im Projektmappen-Explorer mit der rechten Maustaste auf ein vorhandenes Projekt klicke, werden keine Application Insights-Optionen angezeigt.*

* Nicht alle Typen von .NET-Projekten werden von den Tools unterstützt. Web- und WCF-Projekte werden unterstützt. Für andere Projekttypen, z.B. Desktop- oder Dienstanwendungen, können Sie [Ihrem Projekt trotzdem manuell ein Application Insights SDK hinzufügen](./windows-desktop.md).
* Stellen Sie sicher, dass Sie über [Visual Studio 2013 Update 3 oder höher](/visualstudio/releasenotes/vs2013-update3-rtm-vs)verfügen. Darin sind die Developer Analytics-Tools mit dem Application Insights SDK bereits vorinstalliert.
* Wählen Sie **Extras** > **Erweiterungen und Updates** aus, und stellen Sie sicher, dass **Developer Analytics Tools** installiert und aktiviert ist. Wenn dies der Fall ist, klicken Sie auf **Updates** , um zu prüfen, ob ein Update verfügbar ist.
* Öffnen Sie das Dialogfeld „Neues Projekt“, und wählen Sie die ASP.NET-Webanwendung aus. Wenn die Application Insights-Option hier angezeigt wird, sind die Tools installiert. Wenn dies nicht der Fall ist, können Sie versuchen, Developer Analytics Tools zu deinstallieren und dann neu zu installieren.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Fehler beim Hinzufügen von Application Insights
*Wenn ich versuche, Application Insights einem vorhandenen Projekt hinzuzufügen, wird eine Fehlermeldung angezeigt.*

Wahrscheinliche Ursachen:

* Es liegt ein Fehler bei der Kommunikation mit dem Application Insights-Portal vor.
* Es gibt ein Problem mit Ihrem Azure-Konto.
* Sie verfügen nur über [Lesezugriff auf das Abonnement oder die Gruppe, in dem oder der Sie die neue Ressource erstellen möchten](./resources-roles-access-control.md).

Behebung:

* Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben.
* Überprüfen Sie in Ihrem Browser, ob Sie auf das [Azure-Portal](https://portal.azure.com)zugreifen können. Öffnen Sie "Einstellungen", und stellen Sie fest, ob eine Einschränkung besteht.
* [Hinzufügen von Application Insights zu Ihrem vorhandenen Projekt](./asp-net.md): Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights hinzufügen“ aus.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.
Es scheint ein Fehler aufgetreten zu sein, während Sie Application Insights oder vielleicht einen Protokollierungsadapter installiert haben.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Application Insights > Application Insights konfigurieren**. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> „NuGet-Pakete fehlen“ auf meinem Buildserver
*Alles wird problemlos erstellt, wenn ich auf meinem Entwicklungscomputer das Debuggen durchführe, aber ich erhalte einen NuGet-Fehler auf dem Buildserver.*

Informationen hierzu finden Sie unter [NuGet-Paketwiederherstellung](https://docs.nuget.org/Consume/Package-Restore) und [Automatische Paketwiederherstellung](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Fehlender Menübefehl zum Öffnen von Application Insights aus Visual Studio
*Wenn ich im Projektmappen-Explorer mit der rechten Maustaste auf mein Projekt klicke, werden keine Application Insights-Befehle angezeigt, oder der Befehl „Application Insights öffnen“ wird nicht angezeigt.*

Wahrscheinliche Ursachen:

* Sie haben die Application Insights-Ressource manuell erstellt, oder das Projekt hat einen Typ, der von den Application Insights-Tools nicht unterstützt wird.
* Die Developer Analytics-Tools sind in Ihrer Visual Studio-Anwendung deaktiviert.
* Ihre Visual Studio-Version ist älter als 2013 Update 3.

Behebung:

* Stellen Sie sicher, dass Sie die Visual Studio-Version 2013 Update 3 oder höher verwenden.
* Wählen Sie **Extras** > **Erweiterungen und Updates** aus, und stellen Sie sicher, dass die **Developer Analytics-Tools** installiert und aktiviert sind. Wenn dies der Fall ist, klicken Sie auf **Updates** , um zu prüfen, ob ein Update verfügbar ist.
* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt. Wenn Sie den Befehl **Application Insights > Application Insights konfigurieren** sehen, können Sie ihn verwenden, um Ihr Projekt mit den Ressource im Application Insights-Dienst zu verbinden.

Andernfalls wird Ihr Projekttyp von Developer Analytics Tools nicht direkt unterstützt. Melden Sie sich zum Anzeigen Ihrer Telemetriedaten beim [Azure-Portal](https://portal.azure.com)an, wählen Sie in der linken Navigationsleiste „Application Insights“, und wählen Sie dann Ihre Anwendung aus.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>„Zugriff verweigert“ beim Öffnen von Application Insights aus Visual Studio
*Mit dem Menübefehl „Application Insights öffnen“ gelange ich zum Azure-Portal, aber ich erhalte den Fehler „Zugriff verweigert“.*

Für die Microsoft-Anmeldung, die Sie zuletzt in Ihrem Standardbrowser verwendet haben, besteht kein Zugriff auf [die Ressource, die beim Hinzufügen von Application Insights zu dieser App erstellt wurde](./asp-net.md). Es gibt zwei wahrscheinliche Ursachen:

* Sie besitzen mehr als ein Microsoft-Konto, z. B. ein Geschäftskonto und ein persönliches Microsoft-Konto. Die Anmeldung, die Sie zuletzt in Ihrem Standardbrowser verwendet haben, gilt für ein anderes Konto als das Konto, für das Zugriff zum [Hinzufügen von Application Insights zum Projekt](./asp-net.md) besteht.
  * Behebung: Klicken Sie oben rechts im Browserfenster auf Ihren Namen, und melden Sie sich ab. Melden Sie sich mit dem Konto an, für das Zugriff besteht. Klicken Sie dann in der linken Navigationsleiste auf „Application Insights“, und wählen Sie Ihre App aus.
* Eine andere Person hat Application Insights dem Projekt hinzugefügt und vergessen, Ihnen [Zugriff auf die Ressourcengruppe](./resources-roles-access-control.md) zu gewähren, in der die Erstellung durchgeführt wurde.
  * Behebung: Falls die Person ein Unternehmenskonto verwendet hat, kann sie Ihren Namen dem Team hinzufügen, oder sie kann Ihnen individuellen Zugriff auf die Ressourcengruppe gewähren.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>„Ressource nicht gefunden“ beim Öffnen von Application Insights aus Visual Studio
*Mit dem Menübefehl „Application Insights öffnen“ gelange ich zum Azure-Portal, aber ich erhalte den Fehler „Ressource nicht gefunden“.*

Wahrscheinliche Ursachen:

* Die Application Insights-Ressource für Ihre Anwendung wurde gelöscht. -oder-
* Der Instrumentierungsschlüssel wurde in „ApplicationInsights.config“ festgelegt oder geändert, indem er direkt bearbeitet wurde, ohne die Projektdatei zu aktualisieren.

Mit dem Instrumentierungsschlüssel in „ApplicationInsights.config“ wird gesteuert, wohin die Telemetriedaten gesendet werden. Über eine Zeile in der Projektdatei wird gesteuert, welche Ressource geöffnet wird, wenn Sie den Befehl in Visual Studio verwenden.

Behebung:

* Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights“ und dann „Application Insights konfigurieren“. Im Dialogfeld können Sie wählen, ob Sie die Telemetriedaten an eine vorhandene Ressource senden oder eine neue erstellen. Oder:
* Öffnen Sie die Ressource direkt. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an, klicken Sie in der linken Navigationsleiste auf „Application Insights“, und wählen Sie Ihre App aus.

## <a name="where-do-i-find-my-telemetry"></a>Wo finde ich meine Telemetriedaten?
*Ich habe mich beim [Microsoft Azure-Portal](https://portal.azure.com) angemeldet, und das Dashboard auf der Azure-Startseite wird angezeigt. Wo finde ich nun meine Application Insights-Daten?*

* Klicken Sie in der linken Navigationsleiste auf „Application Insights“ und dann auf den Namen Ihrer App. Wenn noch keine Projekte vorhanden sind, müssen Sie [Application Insights konfigurieren oder Ihrem Webprojekt hinzufügen](./asp-net.md).  
  Es werden einige Diagramme mit Zusammenfassungen angezeigt. Beim Durchklicken können Sie weitere Details einblenden.
* Klicken Sie in Visual Studio beim Debuggen Ihrer App auf die Schaltfläche „Application Insights“.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Keine Serverdaten (oder überhaupt keine Daten)
*Ich habe meine App ausgeführt und dann den Application Insights-Dienst in Microsoft Azure geöffnet, aber für alle Diagramme wird „Erfahren Sie, wie Sie...“ oder „Nicht konfiguriert“ angezeigt.* Oder es sind *nur Seitenansichts- und Benutzerdaten zu sehen, aber keine Serverdaten*.

* Führen Sie Ihre Anwendung in Visual Studio im Debugmodus aus (F5). Verwenden Sie die Anwendung, um einige Telemetriedaten zu generieren. Überprüfen Sie, ob im Visual Studio-Ausgabefenster protokollierte Ereignisse angezeigt werden.  
  ![Screenshot: Ausführung der Anwendung im Debugmodus in Visual Studio](./media/asp-net-troubleshoot-no-data/output-window.png)
* Öffnen Sie im Application Insights-Portal die [Diagnosesuche](./diagnostic-search.md). Hier werden Daten normalerweise zuerst angezeigt.
* Klicken Sie auf die Schaltfläche "Aktualisieren". Das Blatt aktualisiert sich in regelmäßigen Abständen selbst, doch Sie können es auch manuell aktualisieren. Das Aktualisierungsintervall für größere Zeiträume ist länger.
* Überprüfen Sie, ob die Instrumentierungsschlüssel übereinstimmen. Sehen Sie sich im Application Insights-Portal auf dem Hauptblatt Ihrer App in der Dropdownliste **Zusammenfassung** den **Instrumentierungsschlüssel** an. Öffnen Sie dann in Ihrem Projekt in Visual Studio die Datei „ApplicationInsights.config“, und suchen Sie nach `<instrumentationkey>`. Überprüfen Sie, ob die beiden Schlüssel identisch sind. Gehen Sie wie folgt vor, falls dies nicht so ist:  
  * Klicken Sie im Portal auf „Application Insights“, und suchen Sie nach der App-Ressource mit dem richtigen Schlüssel. -oder-
  * Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights“ und dann „Konfigurieren“. Setzen Sie die App zurück, um Telemetriedaten an die richtige Ressource zu senden.
  * Gehen Sie wie folgt vor, wenn Sie die übereinstimmenden Schlüssel nicht finden können: Stellen Sie sicher, dass Sie in Visual Studio und im Portal die gleichen Anmeldeinformationen verwenden.
* Sehen Sie sich auf dem [Dashboard der Microsoft Azure-Startseite](https://portal.azure.com)die Karte zur Dienstintegrität an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
* Schauen Sie auch in [unserem Statusblog](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog)nach.
* Haben Sie Code für das [serverseitige SDK](./api-custom-events-metrics.md) geschrieben, mit dem der Instrumentierungsschlüssel in `TelemetryClient`-Instanzen oder in `TelemetryContext` geändert wird? Oder haben Sie eine [Konfiguration für die Filterung oder Stichprobenerstellung](./api-filtering-sampling.md) geschrieben, bei der ggf. zu viel herausgefiltert wird?
* Wenn Sie „ApplicationInsights.config“ bearbeitet haben, überprüfen Sie die Konfiguration von [TelemetryInitializers und TelemetryProcessors](./api-filtering-sampling.md). Ein falsch benannter Typ oder Parameter kann dazu führen, dass das SDK keine Daten gesendet.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Keine Daten zu Seitenansichten, Browsern, Verwendung
*Ich sehe Daten in Diagrammen zur Serverantwortzeit und zu Serveranforderungen, aber keine Daten zur Dauer der Seitenansicht oder auf den Blättern „Browser“ oder „Verwendung“.*

Die Daten kommen von Skripts auf den Webseiten. 

* Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen](./javascript.md).
* Stellen Sie sicher, dass Internet Explorer Ihre Website nicht im Kompatibilitätsmodus anzeigt.
* Verwenden Sie die Debugfunktion Ihres Browsers (bei einigen Browsern erfolgt dies über F12 und anschließendes Wählen von „Netzwerk“), um sicherzustellen, dass Daten an `dc.services.visualstudio.com`gesendet werden.

## <a name="no-dependency-or-exception-data"></a>Keine Abhängigkeits- oder Ausnahmedaten
Weitere Informationen finden Sie unter [Telemetriedaten zu Abhängigkeiten](./asp-net-dependencies.md) und [Telemetriedaten für Ausnahmen](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Keine Leistungsdaten
Leistungsdaten (CPU, E/A-Rate usw.) sind für [Java-Webdienste](./java-collectd.md), [Windows-Desktop-Apps](./windows-desktop.md), [IIS-Web-Apps und -Dienste bei Installation des Statusmonitors](./monitor-performance-live-website-now.md) und [Azure Cloud Services](./app-insights-overview.md) verfügbar. Sie finden die Leistungsdaten unter „Einstellungen“ > „Server“.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Keine Daten (Serverdaten), seitdem ich die App auf meinem Server veröffentlicht habe
* Überprüfen Sie, ob Sie tatsächlich alle Daten von Microsoft kopiert haben. ApplicationInsights-DLLs auf dem Server, zusammen mit „Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll“
* Möglicherweise müssen Sie in der Firewall [einige TCP-Ports öffnen](./ip-addresses.md).
* Wenn Sie einen Proxy verwenden müssen, um Daten aus Ihrem Unternehmensnetzwerk zu senden, legen Sie [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) in der Datei "Web.config" fest.
* Windows Server 2008: Stellen Sie sicher, dass Sie die folgenden Updates installiert haben: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.
* Ist Ihr monatliches Kontingent an Datenpunkten erreicht? Öffnen Sie "Einstellungen – Kontingente und Preisübersicht", um es herauszufinden. Sie können in diesem Fall Ihren Plan aktualisieren oder zusätzliche Kapazität erwerben. Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nicht alle Daten werden erwartungsgemäß angezeigt.
Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, wird möglicherweise die [adaptive Stichprobenerstellung](./sampling.md) verwendet, bei der nur ein bestimmter Prozentsatz der Telemetriedaten übermittelt wird.

Sie können diese Funktion deaktivieren, aber dies ist nicht zu empfehlen. Die Stichprobenerstellung ist so konzipiert, dass die zugehörigen Telemetriedaten zu Diagnosezwecken richtig übertragen werden.

## <a name="client-ip-address-is-0000"></a>Die Client-IP-Adresse lautet 0.0.0.0.

Am 5. Februar 2018 wurde angekündigt, dass die Protokollierung der Client-IP-Adresse entfernt wurde. Dies wirkt sich nicht auf geografische Standorte aus.

> [!NOTE]
> Wenn Sie die ersten drei Oktette der IP-Adresse erfassen möchten, können Sie einen [Telemetrieinitialisierer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) verwenden, um ein benutzerdefiniertes Attribut hinzuzufügen.
> Dies wirkt sich nicht auf Daten aus, die vor dem 5. Februar 2018 erfasst wurden.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Falsche geografische Daten in Benutzertelemetriedaten
Die Dimensionen für Ort, Region und Land werden von IP-Adressen abgeleitet und sind nicht immer exakt. Diese IP-Adressen werden zuerst im Hinblick auf den Standort verarbeitet und dann für die Speicherung in „0.0.0.0“ geändert.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Ausnahme „Methode nicht gefunden“ bei der Ausführung in Azure Cloud Services
Haben Sie für .NET 4.6 erstellt? 4.6 wird nicht automatisch in Azure Cloud Services-Rollen unterstützt. [Installieren Sie 4.6 für jede Rolle](../../cloud-services/cloud-services-dotnet-install-dotnet.md) , bevor Sie Ihre App ausführen.

## <a name="troubleshooting-logs"></a>Problembehandlungsprotokolle

Befolgen Sie diese Anweisungen, um Problembehandlungsprotokolle für Ihr Framework zu erfassen.

### <a name="net-framework"></a>.NET Framework

1. Installieren Sie das Paket [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) von NuGet. Die Version, die Sie installieren, muss mit der aktuell installierten Version von `Microsoft.ApplicationInsighs` übereinstimmen.

2. Ändern Sie Ihre Datei „applicationinsights.config“ so, dass sie Folgendes enthält:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Ihre Anwendung muss über Schreibrechte für den konfigurierten Speicherort verfügen.

3. Starten Sie den Prozess neu, sodass diese neuen Einstellungen vom SDK übernommen werden.

4. Setzen Sie diese Änderungen zurück, wenn Sie fertig sind.

### <a name="net-core"></a>.NET Core

1. Installieren Sie das [Application Insights SDK NuGet-Paket für ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) von NuGet. Die Version, die Sie installieren, muss mit der aktuell installierten Version von `Microsoft.ApplicationInsights` übereinstimmen.

   Die neuste Version von Microsoft.ApplicationInsights.AspNetCore ist 2.14.0. Sie bezieht sich auf Microsoft.ApplicationInsights Version 2.14.0. Daher sollten Sie Version 2.14.0 von Microsoft.ApplicationInsights.AspNetCore installieren.

2. Ändern Sie die `ConfigureServices`-Methode in Ihrer `Startup.cs`-Klasse:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Ihre Anwendung muss über Schreibrechte für den konfigurierten Speicherort verfügen.

3. Starten Sie den Prozess neu, sodass diese neuen Einstellungen vom SDK übernommen werden.

4. Setzen Sie diese Änderungen zurück, wenn Sie fertig sind.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Sammeln von Protokollen mit PerfView
[PerfView](https://github.com/Microsoft/perfview) ist ein kostenloses Diagnose- und Leistungsanalysetool, das durch Sammlung und Visualisierung von Diagnoseinformationen aus verschiedenen Quellen dabei hilft, CPU-, Arbeitsspeicher- und andere Probleme zu isolieren.

Das Application Insights SDK protokolliert EventSource-Protokolle zur eigenen Fehlerbehebung, die von PerfView erfasst werden können.

Laden Sie PerfView herunter, und führen Sie den folgenden Befehl aus, um Protokolle zu erfassen:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Sie können die folgenden Parameter nach Bedarf ändern:
- **MaxCollectSec**. Legen Sie diesen Parameter fest, damit PerfView nicht unbegrenzt läuft und die Leistung Ihres Servers beeinträchtigt.
- **OnlyProviders**. Legen Sie diesen Parameter so fest, dass nur Protokolle aus dem SDK gesammelt werden. Sie können diese Liste entsprechend Ihren jeweiligen Untersuchungen anpassen. 
- **NoGui**. Legen Sie diesen Parameter so fest, dass Protokolle ohne grafische Benutzeroberfläche (GUI) gesammelt werden.


Weitere Informationen finden Sie unter:
- [Recording performance traces with PerfView (Aufzeichnen von Leistungsnachverfolgungen mit PerfView)](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights-Ereignisquellen](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Sammeln von Protokollen mit dotnet-trace

Eine alternative Methode zum Sammeln von Protokollen für die Problembehandlung, die für Linux-basierte Umgebungen besonders hilfreich sein kann, ist [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace).

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Entfernen von Application Insights

Erfahren Sie, wie Sie Application Insights in Visual Studio entfernen, indem Sie die Schritte im entsprechenden [Artikel](./remove-application-insights.md) ausführen.

## <a name="still-not-working"></a>Noch nicht funktionsfähig ...
* [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Application Insights](/answers/topics/azure-monitor.html)

