---
title: Problembehandlung ohne Daten – Application Insights für .NET
description: Sie sehen in Azure Application Insights keine Daten? Versuchen Sie es hier.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: cf818756f583974a8a9b53a9a0cce31dd93d042b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299300"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Problembehandlung ohne Daten – Application Insights für .NET
## <a name="some-of-my-telemetry-is-missing"></a>Einige meiner Telemetriedaten fehlen
*In Application Insights wird nur ein Bruchteil der Ereignisse angezeigt, die von meiner App generiert werden.*

* Wenn immer der gleiche Anteil angezeigt wird, ist dies wahrscheinlich auf die adaptive [Stichprobenerstellung](../../azure-monitor/app/sampling.md)zurückzuführen. Um dies zu bestätigen, öffnen Sie „Suchen“ (auf dem Blatt „Übersicht“), und suchen Sie nach einer Instanz einer Anforderung oder eines anderen Ereignisses. Klicken Sie unten im Eigenschaftenbereich auf „...“, um die vollständigen Eigenschaftendetails anzuzeigen. Wenn „Anforderungsanzahl“ kleiner als 1 ist, ist die Stichprobenerstellung aktiviert.
* Andernfalls ist es möglich, dass Sie einen [Grenzwert für die Datenrate](../../azure-monitor/app/pricing.md#limits-summary) in Ihrem Tarif erreicht haben. Diese Grenzwerte gelten pro Minute.

*Daten gehen nach dem Zufallsprinzip verloren.*

* Überprüfen Sie, ob es im [Telemetriekanal](telemetry-channels.md#does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost) zu Datenverlust kommt.

* Suchen Sie nach bekannten Problemen im [Github-Repository](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) zum Telemetriekanal.

*In der Konsolen-App oder in der Web-App kommt es beim Beenden der App zu Datenverlusten.*

* Der SDK-Kanal speichert Telemetriedaten im Puffer und sendet sie in Batches. Wenn die Anwendung gerade heruntergefahren wird, müssen Sie möglicherweise [Flush()](api-custom-events-metrics.md#flushing-data) explizit aufrufen. Das Verhalten von `Flush()` hängt vom tatsächlich verwendeten [Kanal](telemetry-channels.md#built-in-telemetrychannels) ab.

## <a name="no-data-from-my-server"></a>Keine Daten vom Server
*Ich habe meine App auf meinem Webserver installiert, und nun werden mit keine Telemetriedaten von ihm angezeigt. Auf dem Entwicklungscomputer hat dies aber funktioniert.*

* Wahrscheinlich handelt es sich hierbei um ein Problem mit der Firewall. [Legen Sie Firewallausnahmen für Application Insights fest, damit das Senden von Daten möglich ist](../../azure-monitor/app/ip-addresses.md).
* In IIS-Server fehlen unter Umständen einige erforderliche Komponenten: .NET-Erweiterbarkeit 4.5 und ASP.NET 4.5.

*Ich habe [den Statusmonitor auf meinem Webserver installiert](../../azure-monitor/app/monitor-performance-live-website-now.md), um vorhandene Apps zu überwachen. Es werden keine Ergebnisse angezeigt.*

* Siehe [Problembehandlung für den Statusmonitor](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Keine Option „Application Insights hinzufügen“ in Visual Studio
*Wenn ich im Projektmappen-Explorer mit der rechten Maustaste auf ein vorhandenes Projekt klicke, werden keine Application Insights-Optionen angezeigt.*

* Nicht alle Typen von .NET-Projekten werden von den Tools unterstützt. Web- und WCF-Projekte werden unterstützt. Für andere Projekttypen, z.B. Desktop- oder Dienstanwendungen, können Sie [Ihrem Projekt trotzdem manuell ein Application Insights SDK hinzufügen](../../azure-monitor/app/windows-desktop.md).
* Stellen Sie sicher, dass Sie über [Visual Studio 2013 Update 3 oder höher](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs)verfügen. Darin sind die Developer Analytics-Tools mit dem Application Insights SDK bereits vorinstalliert.
* Wählen Sie **Extras** > **Erweiterungen und Updates** aus, und stellen Sie sicher, dass **Developer Analytics Tools** installiert und aktiviert ist. Wenn dies der Fall ist, klicken Sie auf **Updates** , um zu prüfen, ob ein Update verfügbar ist.
* Öffnen Sie das Dialogfeld „Neues Projekt“, und wählen Sie die ASP.NET-Webanwendung aus. Wenn die Application Insights-Option hier angezeigt wird, sind die Tools installiert. Wenn dies nicht der Fall ist, können Sie versuchen, Developer Analytics Tools zu deinstallieren und dann neu zu installieren.

## <a name="q02"></a>Fehler beim Hinzufügen von Application Insights
*Wenn ich versuche, Application Insights einem vorhandenen Projekt hinzuzufügen, wird eine Fehlermeldung angezeigt.*

Wahrscheinliche Ursachen:

* Es liegt ein Fehler bei der Kommunikation mit dem Application Insights-Portal vor.
* Es gibt ein Problem mit Ihrem Azure-Konto.
* Sie verfügen nur über [Lesezugriff auf das Abonnement oder die Gruppe, in dem oder der Sie die neue Ressource erstellen möchten](../../azure-monitor/app/resources-roles-access-control.md).

Behebung:

* Stellen Sie sicher, dass Sie die Anmeldeinformationen für das richtige Azure-Konto eingegeben haben.
* Überprüfen Sie in Ihrem Browser, ob Sie auf das [Azure-Portal](https://portal.azure.com)zugreifen können. Öffnen Sie "Einstellungen", und stellen Sie fest, ob eine Einschränkung besteht.
* [Hinzufügen von Application Insights zu Ihrem vorhandenen Projekt](../../azure-monitor/app/asp-net.md): Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights hinzufügen“ aus.

## <a name="emptykey"></a>Eine Fehlermeldung "Instrumentationsschlüssel darf nicht leer sein" wird angezeigt.
Es scheint ein Fehler aufgetreten zu sein, während Sie Application Insights oder vielleicht einen Protokollierungsadapter installiert haben.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Application Insights > Application Insights konfigurieren**. Ein Dialogfeld wird angezeigt, das Sie zur Anmeldung bei Azure und zum Erstellen einer Application Insights-Ressource oder dem Wiederverwenden einer vorhandenen Ressource einlädt.

## <a name="NuGetBuild"></a> „NuGet-Pakete fehlen“ auf meinem Buildserver
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

Für die Microsoft-Anmeldung, die Sie zuletzt in Ihrem Standardbrowser verwendet haben, besteht kein Zugriff auf [die Ressource, die beim Hinzufügen von Application Insights zu dieser App erstellt wurde](../../azure-monitor/app/asp-net.md). Es gibt zwei wahrscheinliche Ursachen:

* Sie besitzen mehr als ein Microsoft-Konto, z. B. ein Geschäftskonto und ein persönliches Microsoft-Konto. Die Anmeldung, die Sie zuletzt in Ihrem Standardbrowser verwendet haben, gilt für ein anderes Konto als das Konto, für das Zugriff zum [Hinzufügen von Application Insights zum Projekt](../../azure-monitor/app/asp-net.md) besteht.
  * Behebung: Klicken Sie oben rechts im Browserfenster auf Ihren Namen, und melden Sie sich ab. Melden Sie sich mit dem Konto an, für das Zugriff besteht. Klicken Sie dann in der linken Navigationsleiste auf „Application Insights“, und wählen Sie Ihre App aus.
* Eine andere Person hat Application Insights dem Projekt hinzugefügt und vergessen, Ihnen [Zugriff auf die Ressourcengruppe](../../azure-monitor/app/resources-roles-access-control.md) zu gewähren, in der die Erstellung durchgeführt wurde.
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

* Klicken Sie in der linken Navigationsleiste auf „Application Insights“ und dann auf den Namen Ihrer App. Wenn noch keine Projekte vorhanden sind, müssen Sie [Application Insights konfigurieren oder Ihrem Webprojekt hinzufügen](../../azure-monitor/app/asp-net.md).  
  Es werden einige Diagramme mit Zusammenfassungen angezeigt. Beim Durchklicken können Sie weitere Details einblenden.
* Klicken Sie in Visual Studio beim Debuggen Ihrer App auf die Schaltfläche „Application Insights“.

## <a name="q03"></a> Keine Serverdaten (oder überhaupt keine Daten)
*Ich habe meine App ausgeführt und dann den Application Insights-Dienst in Microsoft Azure geöffnet, aber für alle Diagramme wird „Erfahren Sie, wie Sie...“ oder „Nicht konfiguriert“ angezeigt.* Oder es sind *nur Seitenansichts- und Benutzerdaten zu sehen, aber keine Serverdaten*.

* Führen Sie Ihre Anwendung in Visual Studio im Debugmodus aus (F5). Verwenden Sie die Anwendung, um einige Telemetriedaten zu generieren. Überprüfen Sie, ob im Visual Studio-Ausgabefenster protokollierte Ereignisse angezeigt werden.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Öffnen Sie im Application Insights-Portal die [Diagnosesuche](../../azure-monitor/app/diagnostic-search.md). Hier werden Daten normalerweise zuerst angezeigt.
* Klicken Sie auf die Schaltfläche "Aktualisieren". Das Blatt aktualisiert sich in regelmäßigen Abständen selbst, doch Sie können es auch manuell aktualisieren. Das Aktualisierungsintervall für größere Zeiträume ist länger.
* Überprüfen Sie, ob die Instrumentierungsschlüssel übereinstimmen. Sehen Sie sich im Application Insights-Portal auf dem Hauptblatt Ihrer App in der Dropdownliste **Zusammenfassung** den **Instrumentierungsschlüssel** an. Öffnen Sie dann in Ihrem Projekt in Visual Studio die Datei „ApplicationInsights.config“, und suchen Sie nach `<instrumentationkey>`. Überprüfen Sie, ob die beiden Schlüssel identisch sind. Gehen Sie wie folgt vor, falls dies nicht so ist:  
  * Klicken Sie im Portal auf „Application Insights“, und suchen Sie nach der App-Ressource mit dem richtigen Schlüssel. -oder-
  * Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights“ und dann „Konfigurieren“. Setzen Sie die App zurück, um Telemetriedaten an die richtige Ressource zu senden.
  * Gehen Sie wie folgt vor, wenn Sie die übereinstimmenden Schlüssel nicht finden können: Stellen Sie sicher, dass Sie in Visual Studio und im Portal die gleichen Anmeldeinformationen verwenden.
* Sehen Sie sich auf dem [Dashboard der Microsoft Azure-Startseite](https://portal.azure.com)die Karte zur Dienstintegrität an. Falls es eine Warnungsanzeige gibt, warten Sie, bis sie wieder "OK" anzeigt, und schließen Sie das Application Insights-Anwendungsfenster, bevor Sie es erneut öffnen.
* Schauen Sie auch in [unserem Statusblog](https://blogs.msdn.microsoft.com/servicemap-status/)nach.
* Haben Sie Code für das [serverseitige SDK](../../azure-monitor/app/api-custom-events-metrics.md) geschrieben, mit dem der Instrumentierungsschlüssel in `TelemetryClient`-Instanzen oder in `TelemetryContext` geändert wird? Oder haben Sie eine [Konfiguration für die Filterung oder Stichprobenerstellung](../../azure-monitor/app/api-filtering-sampling.md) geschrieben, bei der ggf. zu viel herausgefiltert wird?
* Wenn Sie „ApplicationInsights.config“ bearbeitet haben, überprüfen Sie die Konfiguration von [TelemetryInitializers und TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Ein falsch benannter Typ oder Parameter kann dazu führen, dass das SDK keine Daten gesendet.

## <a name="q04"></a>Keine Daten zu Seitenansichten, Browsern, Verwendung
*Ich sehe Daten in Diagrammen zur Serverantwortzeit und zu Serveranforderungen, aber keine Daten zur Dauer der Seitenansicht oder auf den Blättern „Browser“ oder „Verwendung“.*

Die Daten kommen von Skripts auf den Webseiten. 

* Wenn Sie Application Insights zu einem vorhandenen Webprojekt hinzugefügt haben, müssen Sie die [Skripts manuell hinzufügen](../../azure-monitor/app/javascript.md).
* Stellen Sie sicher, dass Internet Explorer Ihre Website nicht im Kompatibilitätsmodus anzeigt.
* Verwenden Sie die Debugfunktion Ihres Browsers (bei einigen Browsern erfolgt dies über F12 und anschließendes Wählen von „Netzwerk“), um sicherzustellen, dass Daten an `dc.services.visualstudio.com`gesendet werden.

## <a name="no-dependency-or-exception-data"></a>Keine Abhängigkeits- oder Ausnahmedaten
Weitere Informationen finden Sie unter [Telemetriedaten zu Abhängigkeiten](../../azure-monitor/app/asp-net-dependencies.md) und [Telemetriedaten für Ausnahmen](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Keine Leistungsdaten
Leistungsdaten (CPU, E/A-Rate usw.) sind für [Java-Webdienste](../../azure-monitor/app/java-collectd.md), [Windows-Desktop-Apps](../../azure-monitor/app/windows-desktop.md), [IIS-Web-Apps und -Dienste bei Installation des Statusmonitors](../../azure-monitor/app/monitor-performance-live-website-now.md) und [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md) verfügbar. Sie finden die Leistungsdaten unter „Einstellungen“ > „Server“.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Keine Daten (Serverdaten), seitdem ich die App auf meinem Server veröffentlicht habe
* Überprüfen Sie, ob Sie tatsächlich alle Daten von Microsoft kopiert haben. ApplicationInsights-DLLs auf dem Server, zusammen mit „Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll“
* Möglicherweise müssen Sie in der Firewall [einige TCP-Ports öffnen](../../azure-monitor/app/ip-addresses.md).
* Wenn Sie einen Proxy verwenden müssen, um Daten aus Ihrem Unternehmensnetzwerk zu senden, legen Sie [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) in der Datei "Web.config" fest.
* Windows Server 2008: Stellen Sie sicher, dass Sie die folgenden Updates installiert haben: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Zuvor wurden Daten angezeigt, jetzt jedoch nicht mehr.
* Überprüfen Sie den [Statusblog](https://blogs.msdn.com/b/applicationinsights-status/).
* Ist Ihr monatliches Kontingent an Datenpunkten erreicht? Öffnen Sie "Einstellungen – Kontingente und Preisübersicht", um es herauszufinden. Wenn dies der Fall ist, können Sie Ihren Plan aktualisieren oder zusätzliche Kapazität erwerben. Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nicht alle Daten werden erwartungsgemäß angezeigt.
Wenn Ihre Anwendung eine große Menge von Daten sendet und Sie das Application Insights-SDK für ASP.NET Version 2.0.0-beta3 oder höher verwenden, wird möglicherweise die [adaptive Stichprobenerstellung](../../azure-monitor/app/sampling.md) verwendet, bei der nur ein bestimmter Prozentsatz der Telemetriedaten übermittelt wird.

Sie können diese Funktion deaktivieren, aber dies ist nicht zu empfehlen. Die Stichprobenerstellung ist so konzipiert, dass die zugehörigen Telemetriedaten zu Diagnosezwecken richtig übertragen werden.

## <a name="client-ip-address-is-0000"></a>Die Client-IP-Adresse lautet 0.0.0.0.

Am 5. Februar 2018 wurde angekündigt, dass die Protokollierung der Client-IP-Adresse entfernt wurde. Dies wirkt sich nicht auf geografische Standorte aus.

> [!NOTE]
> Wenn Sie die ersten drei Oktette der IP-Adresse erfassen möchten, können Sie einen [Telemetrieinitialisierer](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) verwenden, um ein benutzerdefiniertes Attribut hinzuzufügen.
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

1. Installieren Sie das Paket [Microsoft.AspNetCore.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.ApplicationInsights.HostingStartup) von NuGet. Die Version, die Sie installieren, muss mit der aktuell installierten Version von `Microsoft.ApplicationInsights` übereinstimmen.

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

## <a name="still-not-working"></a>Noch nicht funktionsfähig ...
* [Application Insights-Forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
