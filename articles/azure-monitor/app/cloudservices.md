---
title: Application Insights für Azure Cloud Services | Microsoft-Dokumentation
description: Effektives Überwachen Ihrer Web- und Workerrollen mit Application Insights
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: ccd863db55ef0ff9f4051947321321c8b01430c4
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920689"
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights für Azure Cloud Services
[Application Insights][start] kann [Azure Cloud Services-Apps](https://azure.microsoft.com/services/cloud-services/) auf Verfügbarkeit, Leistung, Fehler und Verwendung überwachen. Dabei werden Daten aus den Application Insights-SDKs mit Daten der [Azure-Diagnose](../platform/diagnostics-extension-overview.md) aus Cloud Services kombiniert. Mit dem Feedback zur Leistung und Effektivität der App in der Praxis können Sie in jedem Entwicklungslebenszyklus eine fundierte Entscheidung für die Richtung des Entwurfs treffen.

![Übersichtsdashboard](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie beginnen, benötigen Sie Folgendes:

* Ein [Azure](https://azure.com)-Abonnement. Melden Sie sich mit Ihrem Microsoft-Konto für Windows, Xbox Live oder andere Microsoft-Clouddienste an. 
* Microsoft Azure-Tools 2.9 oder höher.
* Developer Analytics Tools 7.10 oder höher.

## <a name="get-started-quickly"></a>Schneller Einstieg
Am schnellsten und einfachsten können Sie Ihren Clouddienst mit Application Insights überwachen, wenn Sie diese Option beim Veröffentlichen des Diensts in Azure auswählen.

![Seite mit Beispiel für Diagnoseeinstellungen](./media/cloudservices/azure-cloud-application-insights.png)

Mit dieser Option wird die App zur Laufzeit instrumentiert, sodass Sie alle erforderlichen Telemetriedaten zum Überwachen von Anforderungen, Ausnahmen und Abhängigkeiten in Ihrer Webrolle erhalten. Leistungsindikatoren aus Ihren Workerrollen werden ebenfalls überwacht. Auch alle von Ihrer App generierten Diagnoseablaufverfolgungen werden an Application Insights gesendet.

Wenn Sie nur diese Option benötigen, sind Sie fertig. 

Die nächsten Schritte sind: [Anzeigen von Metriken aus der App](../platform/metrics-charts.md) und [Abfragen von Daten mit Analytics](../log-query/log-query-overview.md). 

Zur Überwachung der Leistung im Browser sollten Sie [Verfügbarkeitstests](./monitor-web-app-availability.md) einrichten und [Ihren Webseiten Code hinzufügen](./javascript.md).

In den nächsten Abschnitten werden die folgenden zusätzlichen Optionen erläutert:

* Senden von Daten aus verschiedenen Komponenten und Erstellen von Konfigurationen zum Trennen von Ressourcen
* Hinzufügen von benutzerdefinierten Telemetriedaten aus der App

## <a name="sample-app-instrumented-with-application-insights"></a>Mit Application Insights instrumentierte Beispiel-App
In dieser [Beispiel-App](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) wird Application Insights einem Clouddienst mit zwei in Azure gehosteten Workerrollen hinzugefügt. 

Im nächsten Abschnitt erfahren Sie, wie Sie Ihr eigenes Clouddienstprojekt auf die gleiche Weise anpassen können.

## <a name="plan-resources-and-resource-groups"></a>Planen von Ressourcen und Ressourcengruppen
Die Telemetriedaten aus Ihrer App werden in einer Azure-Ressource vom Typ Application Insights gespeichert, analysiert und angezeigt. 

Jede Ressource gehört jeweils zu einer Ressourcengruppe. Ressourcengruppen werden zum Verwalten von Kosten, zum Gewähren des Zugriffs für Teammitglieder und zum Bereitstellen von Updates in einer koordinierten Transaktion verwendet. Beispielsweise können Sie [ein Skript zum Bereitstellen](../../azure-resource-manager/templates/deploy-powershell.md) eines Azure-Clouddiensts und der zugehörigen Application Insights-Überwachungsressourcen in einem kombinierten Vorgang schreiben.

### <a name="resources-for-components"></a>Ressourcen für Komponenten
Es wird empfohlen, für jede Komponente Ihrer App eine separate Ressource zu erstellen. D.h. Sie erstellen eine Ressource für jede Web- und Workerrolle. Sie können jede Komponente einzeln analysieren, können jedoch auch ein [Dashboard](./overview-dashboard.md) erstellen, das die wichtigsten Diagramme aus allen Komponenten vereint, sodass Sie diese in einer zentralen Ansicht vergleichen und zusammen überwachen können. 

Ein alternativer Ansatz besteht darin, die Telemetriedaten von mehreren Rollen an dieselbe Ressource zu senden, jedoch [jedem Telemetriedatenelement eine Dimensionseigenschaft hinzuzufügen](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), mit der die jeweilige Quellrolle identifiziert wird. In diesem Ansatz wird in Metrikdiagrammen (z.B. Ausnahmen) normalerweise eine Aggregation der Zählungen aus den unterschiedlichen Rollen angezeigt. Sie können jedoch das Diagramm bei Bedarf nach Rollenbezeichnern segmentieren. Suchvorgänge können auch nach der gleichen Dimension gefiltert werden. Mit dieser Alternative können alle Daten etwas einfacher gleichzeitig angezeigt werden, es kann jedoch zu einer gewissen Verwirrung im Hinblick auf die Rollen führen.

Browsertelemetriedaten sind in der Regel in der gleichen Ressource wie die zugehörige serverseitige Webrolle enthalten.

Legen Sie die Application Insights-Ressourcen für die verschiedenen Komponenten in einer Ressourcengruppe ab. Mit diesem Ansatz können sie einfach zusammen verwaltet werden. 

### <a name="separate-development-test-and-production"></a>Trennen von Entwicklung, Test und Produktion
Wenn Sie benutzerdefinierte Ereignisse für das nächste Feature entwickeln, während die vorherige Version aktiv ist, sollten die Entwicklungstelemetriedaten an eine separate Application Insights-Ressource gesendet werden. Andernfalls ist es schwierig, die Testtelemetriedaten im gesamten Datenverkehr von der Livewebsite zu finden.

Um dies zu vermeiden, erstellen Sie separate Ressourcen für jede Buildkonfiguration oder jeden „Stempel“ (Entwicklung, Test, Produktion usw.) des Systems. Legen Sie die Ressourcen für jede Buildkonfiguration jeweils in einer separaten Ressourcengruppe ab. 

Um die Telemetriedaten an die entsprechenden Ressourcen zu senden, können Sie das Application Insights SDK so einrichten, dass abhängig von der Buildkonfiguration jeweils ein anderer Instrumentierungsschlüssel abgerufen wird. 

Erfahren Sie, wie Sie den [Instrumentierungsschlüssel für verschiedene Phasen dynamisch festlegen](./separate-resources.md#dynamic-ikey). 

## <a name="create-an-application-insights-resource-for-each-role"></a>Erstellen einer Application Insights-Ressource für die einzelnen Rollen

Wenn Sie eine separate Ressource für die einzelnen Rollen und eventuell eine separate Gruppe für die einzelnen Buildkonfigurationen erstellen möchten, ist es am einfachsten, diese im Application Insights-Portal zu erstellen. Wenn Sie häufig Ressourcen erstellen, können Sie [den Prozess automatisieren](./powershell.md).

1. Wählen Sie im [Azure-Portal][portal]**Neu** > **Entwicklerdienste** > **Application Insights** aus.  

    ![Bereich „Application Insights“](./media/cloudservices/01-new.png)

1. Wählen Sie in der Dropdownliste **Anwendungstyp** die Option **ASP.NET-Webanwendung** aus.

Jede Ressource wird durch einen Instrumentierungsschlüssel identifiziert. Möglicherweise benötigen Sie diesen Schlüssel später, wenn Sie die Konfiguration des SDK manuell konfigurieren oder überprüfen möchten.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Einrichten von Azure-Diagnose für die einzelnen Rollen
Legen Sie diese Option zum Überwachen Ihrer App mit Application Insights fest. Für Webrollen umfasst diese Option die Leistungsüberwachung, Warnungen, Diagnose und die Verwendungsanalyse. Für andere Rollen können Sie Azure-Diagnose durchsuchen und überwachen, z.B. Neustarten, Leistungsindikatoren und Aufrufe von „System.Diagnostics.Trace“. 

1. Öffnen Sie im Visual Studio-Projektmappen-Explorer unter **\<YourCloudService>**  > **Rollen** die Eigenschaften der einzelnen Rollen.

1. Aktivieren Sie unter **Konfiguration** das Kontrollkästchen **Diagnosedaten an Application Insights senden**, und wählen Sie dann die entsprechende Application Insights-Ressource aus, die Sie zuvor erstellt haben.

Wenn Sie sich entschieden haben, eine separate Application Insights-Ressource für jede Buildkonfiguration zu verwenden, wählen Sie zuerst die Konfiguration aus.

![Application Insights konfigurieren](./media/cloudservices/configure-azure-diagnostics.png)

Dadurch werden die Application Insights-Instrumentierungsschlüssel in die Dateien mit dem Namen *ServiceConfiguration.\*.cscfg* eingefügt. Hier ist der [Beispielcode](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Wenn Sie festlegen möchten, welche Diagnosedaten an Application Insights gesendet werden, können Sie dies [direkt durch Bearbeiten der *CSCFG*-Dateien erledigen](../platform/diagnostics-extension-to-application-insights.md).

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Installieren des SDK in den einzelnen Projekten
Mit dieser Option können Sie jeder Rolle benutzerdefinierte Geschäftstelemetriedaten hinzufügen. Die Option bietet eine genauere Analyse der Nutzung und Leistung Ihrer App.

Konfigurieren Sie in Visual Studio das Application Insights SDK für jedes Cloud-App-Projekt.

1. Zum Konfigurieren von **Webrollen** klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Application Insights konfigurieren** oder **Hinzufügen &gt; Application Insights-Telemetrie** aus.

1. So konfigurieren Sie **Workerrollen**: 

    a. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.

    b. Fügen Sie [Application Insights für Windows-Dienste](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) hinzu.

1. So konfigurieren Sie das SDK zum Senden von Daten an die Application Insights-Ressource:

    a. Legen Sie in einer geeigneten Startfunktion den Instrumentierungsschlüssel aus der Konfigurationseinstellung in der *CSCFG*-Datei fest:
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Wiederholen Sie „Schritt a“ für jede Rolle in Ihrer App. Beispiele:
   
    * [Webrolle](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Workerrolle](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Für Webseiten](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Legen Sie für die Datei *ApplicationInsights.config* fest, dass sie immer in das Ausgabeverzeichnis kopiert wird.

   Sie werden über eine Meldung in der *CONFIG*-Datei aufgefordert, den Instrumentationsschlüssel hier zu platzieren. Für Cloud-Apps ist es jedoch besser, ihn aus der *CSCFG* festzulegen. Dadurch wird sichergestellt, dass die Rolle im Portal korrekt identifiziert wird.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Einrichten des Statusmonitors zum Sammeln von vollständigen SQL-Abfragen (optional)

Dieser Schritt ist nur erforderlich, wenn Sie vollständige SQL-Abfragen für .NET Framework erfassen möchten. 

1. Fügen Sie in der `\*.csdef`-Datei die [Startaufgabe](../../cloud-services/cloud-services-startup-tasks.md) für jede Rolle hinzu, die Folgendem ähnelt: 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Laden Sie [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) und [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1) herunter, und fügen Sie sie dem `AppInsightsAgent`-Ordner für jedes Rollenprojekt hinzu. Stellen Sie sicher, dass Sie sie über Visual Studio-Dateieigenschaften oder Buildskripts in das Ausgabeverzeichnis kopieren.

3. Fügen Sie für alle Workerrollen Umgebungsvariablen hinzu: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Ausführen und Veröffentlichen der App

1. Führen Sie Ihre App aus, und melden Sie sich bei Azure an. 

1. Öffnen Sie die zuvor erstellten Application Insights-Ressourcen.

   Einzelne Datenpunkte werden in [Suche][diagnostic] angezeigt, und aggregierte Daten werden im [Metrik-Explorer](../platform/metrics-charts.md) angezeigt.

1. Fügen Sie weitere Telemetriedaten hinzu (siehe folgende Abschnitte), und veröffentlichen Sie dann Ihre App, um Livediagnosen und Nutzungsfeedback zu erhalten. 

Wenn keine Daten vorhanden sind, gehen Sie folgendermaßen vor:

1. Zum Anzeigen einzelner Ereignisse öffnen Sie die Kachel [Suche][diagnostic].
1. Öffnen Sie in der App verschiedene Seiten, damit sie einige Telemetriedaten generiert.
1. Warten Sie einige Sekunden, und klicken Sie dann auf **Aktualisieren**.  

Weitere Informationen finden Sie unter [Problembehandlung][qna].

## <a name="view-azure-diagnostics-events"></a>Anzeigen von Azure-Diagnoseereignissen
Sie finden Sie Informationen der [Azure-Diagnose](../platform/diagnostics-extension-overview.md) in Application Insights an den folgenden Speicherorten:

* Leistungsindikatoren werden als benutzerdefinierte Metriken angezeigt. 
* Windows-Ereignisprotokolle werden als Ablaufverfolgungen und benutzerdefinierte Ereignisse angezeigt.
* Anwendungsprotokolle, ETW-Protokolle und alle Diagnoseinfrastrukturprotokolle werden als Ablaufverfolgungen angezeigt.

Zum Anzeigen von Leistungsindikatoren und Angaben zur Anzahl von Ereignissen öffnen Sie den [Metrik-Explorer](../platform/metrics-charts.md) und fügen das folgende Diagramm hinzu:

![Azure-Diagnosedaten](./media/cloudservices/23-wad.png)

Verwenden Sie zum Durchsuchen der verschiedenen Ablaufverfolgungsprotokolle, die von der Azure-Diagnose gesendet werden, die [Suche](./diagnostic-search.md) oder eine [Analytics-Abfrage](../log-query/log-analytics-tutorial.md). Angenommen, es liegt eine nicht behandelte Ausnahme vor, die das Abstürzen und erneute Aktivieren einer Rolle verursacht hat. Diese Informationen werden im Kanal „Anwendung“ des Windows-Ereignisprotokolls angezeigt. Sie können das Windows-Ereignisprotokoll mithilfe der Suchfunktion anzeigen und die vollständige Stapelüberwachung für die Ausnahme abrufen. So können Sie die Ursache des Problems ermitteln.

![Durchsuchen der Azure-Diagnosedaten](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Mehr Telemetrie
In den folgenden Abschnitten wird erläutert, wie Sie weitere Telemetriedaten zu verschiedenen Aspekten Ihrer Anwendung abrufen.

## <a name="track-requests-from-worker-roles"></a>Nachverfolgen von Anforderungen bei Workerrollen
In Webrollen sammelt das Modul für Anforderungen automatisch Daten zu HTTP-Anforderungen. Beispiele zum Überschreiben des Standardsammelverhaltens finden Sie im [MVCWebRole-Beispiel](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Sie können die Leistung der Aufrufe von Workerrollen erfassen, indem Sie diese auf die gleiche Weise wie HTTP-Anforderungen nachverfolgen. In Application Insights misst der Telemetrietyp „Anforderungen“ eine Arbeitseinheit auf dem benannten Server, die zeitlich bestimmt werden und unabhängig erfolgreich sein oder einen Fehler verursachen kann. HTTP-Anforderungen werden zwar durch das SDK automatisch erfasst, Sie können jedoch Ihren eigenen Code zum Nachverfolgen von Anforderungen an Workerrollen einfügen.

Siehe dazu die zwei Beispielworkerrollen, die zum Melden von Anforderungen instrumentiert sind: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Ausnahmen
Informationen zum Sammeln von Ausnahmefehlern aus verschiedenen Web-App-Typen finden Sie unter [Diagnostizieren von Ausnahmen in Ihren Web-Apps mit Application Insights](./asp-net-exceptions.md).

Die Beispielwebrolle verfügt über MVC5- und Web-API 2-Controller. Die nicht behandelten Ausnahmen von beiden werden mit den folgenden Handlern erfasst:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs), [wie in diesem Beispiel gezeigt](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) für MVC5-Controller eingerichtet 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs), [wie in diesem Beispiel gezeigt](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) für Web-API 2-Controller eingerichtet 

Für Workerrollen gibt es zwei Möglichkeiten zum Nachverfolgen von Ausnahmen:

* Verwenden Sie TrackException(ex).
* Wenn Sie das NuGet-Paket für Application Insights-Ablaufverfolgungslistener hinzugefügt haben, können Sie Ausnahmen mithilfe von „System.Diagnostics.Trace“ [wie in diesem Beispiel gezeigt](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107) protokollieren.

## <a name="performance-counters"></a>Leistungsindikatoren
Die folgenden Leistungsindikatoren werden standardmäßig erfasst:

* \Process(??APP_WIN32_PROC??)\% Prozessorzeit
* \Memory\Verfügbare Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\%Prozessorzeit

Für Webrollen werden auch diese Leistungsindikatoren erfasst:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Sie können zusätzliche benutzerdefinierte oder andere Windows-Leistungsindikatoren angeben, indem Sie die Datei *ApplicationInsights.config* [wie in diesem Beispiel gezeigt](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14) bearbeiten.

  ![Leistungsindikatoren](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Korrelierte Telemetriedaten für Workerrollen
Für eine umfassende Diagnose können Sie die Ursachen für Anforderungen mit Fehlern oder hoher Latenz anzeigen. Bei Webrollen richtet das SDK automatisch eine Korrelation zwischen verknüpften Telemetriedaten ein. 

Für Workerrollen können Sie zu diesem Zweck mithilfe eines benutzerdefinierten Telemetrieinitialisierers ein allgemeines Operation.Id-Kontextattribut für alle Telemetriedaten festlegen. Dadurch können Sie auf einen Blick sehen, ob die Latenz oder der Fehler durch eine Abhängigkeit oder durch Ihren Code verursacht wurde. 

Gehen Sie dabei folgendermaßen vor:

* Fügen Sie die Korrelations-ID [wie in diesem Beispiel gezeigt](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36) in einen CallContext ein. In diesem Fall wird die Anforderungs-ID als correlationId-Element verwendet.
* Fügen Sie eine benutzerdefinierte TelemetryInitializer-Implementierung hinzu, die das Attribut „Operation.Id“ auf das zuvor festgelegte correlationId-Element festlegt. Ein Beispiel finden Sie unter [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Fügen Sie den benutzerdefinierten Telemetrieinitialisierer hinzu. Sie können dies in der Datei *ApplicationInsights.config* oder [wie in diesem Beispiel gezeigt](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233) im Code vornehmen.

## <a name="client-telemetry"></a>Clienttelemetrie
Informationen zum Abrufen von browserbasierten Telemetriedaten wie Anzahl der Seitenaufrufe, Seitenladezeiten oder Skriptausnahmen sowie zum Schreiben von benutzerdefinierten Telemetriedaten in Ihre Seitenskripts finden Sie unter [Application Insights für Webseiten][client].

## <a name="availability-tests"></a>Verfügbarkeitstests
[Richten Sie Webtests ein][availability], um sicherzustellen, dass die App online und reaktionsfähig bleibt.

## <a name="display-everything-together"></a>Anzeigen aller Daten
Für eine allgemeine Übersicht über Ihr System können Sie die wichtigsten Überwachungsdiagramme in einem [Dashboard](./overview-dashboard.md) anzeigen. Beispielsweise können Sie die Anforderungs- und Fehleranzahl der einzelnen Rollen anheften. 

Wenn in Ihrem System andere Azure-Dienste, z.B. Stream Analytics, verwendet werden, können Sie die entsprechenden Überwachungsdiagramme auch einfügen. 

Wenn Sie über eine mobile Client-App verfügen, verwenden Sie [App Center](../learn/mobile-center-quickstart.md). Erstellen Sie Abfragen in [Analytics](../log-query/log-query-overview.md), um die Ereigniszähler anzuzeigen, und heften Sie sie an das Dashboard an.

## <a name="example"></a>Beispiel
[In diesem Beispiel](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) wird ein Dienst mit einer Webrolle und zwei Workerrollen überwacht.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Ausnahme „Methode nicht gefunden“ bei der Ausführung in Azure Cloud Services
Haben Sie für .NET 4.6 erstellt? .NET 4.6 wird in Azure Cloud Services-Rollen nicht automatisch unterstützt. [Installieren Sie .NET 4.6 für jede Rolle](../../cloud-services/cloud-services-dotnet-install-dotnet.md), bevor Sie Ihre App ausführen.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nächste Schritte
* [Konfigurieren der Azure-Diagnose zum Protokollieren in Application Insights](../platform/diagnostics-extension-to-application-insights.md)
* [Automatisches Erstellen von Application Insights-Ressourcen](./powershell.md)
* [Automatisierung von Azure-Diagnose](./powershell-azure-diagnostics.md)
* [Azure-Funktionen](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md