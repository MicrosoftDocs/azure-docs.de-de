---
title: Konfigurieren der Überwachung für ASP.NET mit Azure Application Insights | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Leistung, die Verfügbarkeit und die Nutzungsanalysen für Ihre lokal oder in Azure gehostete ASP.NET-Website.
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f9cb5370dcddf783e533664c4c141779402103f0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028987"
---
# <a name="configure-application-insights-for-your-aspnet-website"></a>Konfigurieren von Application Insights für Ihre ASP.NET-Website

Mit diesem Verfahren wird Ihre ASP.NET-Web-App so konfiguriert, dass sie Telemetriedaten an den [Azure Application Insights](./app-insights-overview.md)-Dienst sendet. Dies funktioniert für ASP.NET-Apps, die entweder lokal auf Ihren eigenen IIS-Servern oder in der Cloud gehostet werden. 

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen Folgendes, um Application Insights Ihrer ASP.NET-Website hinzuzufügen:

- Installieren Sie die aktuelle Version von [Visual Studio 2019 für Windows](https://www.visualstudio.com/downloads/) mit den folgenden Arbeitsauslastungen:
    - ASP.NET und Webentwicklung.
    - Azure-Entwicklung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Erstellen Sie eine [arbeitsbereichsbasierte Ressource für Application Insights](create-workspace-resource.md).

> [!IMPORTANT]
> Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die [Verbindungszeichenfolge](./sdk-connection-string.md?tabs=net) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.


## <a name="create-a-basic-aspnet-web-app"></a>Erstellen einer einfachen ASP.NET-Web-App

1. Starten Sie Visual Studio 2019.
2. Klicken Sie auf **Datei** > **Neu** > **Projekt**.
3. Klicken Sie auf **ASP.NET-Webanwendung (.NET Framework) C#** .
4. Geben Sie einen Projektnamen ein, und klicken Sie auf **Erstellen**.
5. Klicken Sie auf **MVC** > **Erstellen**. 

## <a name="add-application-insights-automatically"></a>Automatisches Hinzufügen von Application Insights

In diesem Abschnitt erhalten Sie Informationen zu den Schritten, die für das automatische Hinzufügen von Application Insights zu einer vorlagenbasierten ASP.NET-Web-App erforderlich sind. Führen Sie in Ihrem ASP.NET-Web-App-Projekt in Visual Studio die folgenden Schritte aus:

1. Klicken Sie auf **Application Insights-Telemetrie hinzufügen…**  > **Application Insights SDK (lokal)**  > **Weiter** > **Fertig stellen** > **Schließen**.
2. Öffnen Sie die Datei `ApplicationInsights.config` . 
3. Fügen Sie vor dem schließenden `</ApplicationInsights>`-Tag eine Zeile hinzu, die den Instrumentierungsschlüssel für Ihre Application Insights-Ressource enthält.  Ihren Instrumentierungsschlüssel finden Sie auf der Übersichtsseite Ihrer neu erstellten Application Insights-Ressource, die Sie als Teil der Voraussetzungen für diesen Artikel erstellt haben.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```
4. Klicken Sie auf **Projekt** > **NuGet-Pakete verwalten…**  > **Updates**, und aktualisieren Sie die einzelnen `Microsoft.ApplicationInsights`-NuGet-Pakete auf die aktuelle stabile Version.   
5. Führen Sie Ihre Anwendung aus, indem Sie auf **IIS Express** klicken. Eine einfache ASP.NET-App wird gestartet. Wenn Sie auf den Seiten der Website navigieren, werden Telemetriedaten an Application Insights gesendet.

## <a name="add-application-insights-manually"></a>Manuelles Hinzufügen von Application Insights

In diesem Abschnitt erhalten Sie Informationen zu den Schritten, die für das manuelle Hinzufügen von Application Insights zu einer vorlagenbasierten ASP.NET-Web-App erforderlich sind. Für diesen Abschnitt wird angenommen, Sie verwenden eine Web-App, die auf der MVC-Web-App-Vorlage des ASP.NET-Frameworks basiert.

1. Fügen Sie die folgenden NuGet-Pakete und ihre jeweiligen Abhängigkeiten Ihrem Projekt hinzu:

    - [`Microsoft.ApplicationInsights.WindowsServer`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer)
    - [`Microsoft.ApplicationInsights.Web`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
    - [`Microsoft.AspNet.TelemetryCorrelation`](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation)

2. In manchen Fällen wird die `ApplicationInsights.config`-Datei automatisch für Sie erstellt. Wenn die Datei bereits vorhanden ist, können Sie mit Schritt 4 fortfahren. Wenn die Datei nicht automatisch erstellt wird, müssen Sie sie selbst erstellen. Erstellen Sie auf derselben Projektebene wie die `Global.asax`-Datei eine neue Datei namens `ApplicationInsights.config`.

3. Kopieren Sie die folgende XML-Konfiguration in Ihre neu erstellte Datei:

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
      <TelemetryInitializers>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureRoleEnvironmentTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.Web.WebTestTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SyntheticUserAgentTelemetryInitializer, Microsoft.AI.Web">
          <!-- Extended list of bots:
                search|spider|crawl|Bot|Monitor|BrowserMob|BingPreview|PagePeeker|WebThumb|URL2PNG|ZooShot|GomezA|Google SketchUp|Read Later|KTXN|KHTE|Keynote|Pingdom|AlwaysOn|zao|borg|oegp|silk|Xenu|zeal|NING|htdig|lycos|slurp|teoma|voila|yahoo|Sogou|CiBra|Nutch|Java|JNLP|Daumoa|Genieo|ichiro|larbin|pompos|Scrapy|snappy|speedy|vortex|favicon|indexer|Riddler|scooter|scraper|scrubby|WhatWeb|WinHTTP|voyager|archiver|Icarus6j|mogimogi|Netvibes|altavista|charlotte|findlinks|Retreiver|TLSProber|WordPress|wsr-agent|http client|Python-urllib|AppEngine-Google|semanticdiscovery|facebookexternalhit|web/snippet|Google-HTTP-Java-Client-->
          <Filters>search|spider|crawl|Bot|Monitor|AlwaysOn</Filters>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ClientIpHeaderTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AzureAppServiceRoleNameFromHostNameHeaderInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationNameTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationCorrelationTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.UserTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SessionTelemetryInitializer, Microsoft.AI.Web" />
      </TelemetryInitializers>
      <TelemetryModules>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
          <ExcludeComponentCorrelationHttpHeadersOnDomains>
            <!-- 
            Requests to the following hostnames will not be modified by adding correlation headers.         
            Add entries here to exclude additional hostnames.
            NOTE: this configuration will be lost upon NuGet upgrade.
            -->
            <Add>core.windows.net</Add>
            <Add>core.chinacloudapi.cn</Add>
            <Add>core.cloudapi.de</Add>
            <Add>core.usgovcloudapi.net</Add>
          </ExcludeComponentCorrelationHttpHeadersOnDomains>
          <IncludeDiagnosticSourceActivities>
            <Add>Microsoft.Azure.EventHubs</Add>
            <Add>Microsoft.Azure.ServiceBus</Add>
          </IncludeDiagnosticSourceActivities>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <!--
          Use the following syntax here to collect additional performance counters:
          
          <Counters>
            <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
            ...
          </Counters>
          
          PerformanceCounter must be either \CategoryName(InstanceName)\CounterName or \CategoryName\CounterName
          
          NOTE: performance counters configuration will be lost upon NuGet upgrade.
          
          The following placeholders are supported as InstanceName:
            ??APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
            ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
            ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AppServicesHeartbeatTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureInstanceMetadataTelemetryModule, Microsoft.AI.WindowsServer">
          <!--
          Remove individual fields collected here by adding them to the ApplicationInsighs.HeartbeatProvider 
          with the following syntax:
          
          <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
            <ExcludedHeartbeatProperties>
              <Add>osType</Add>
              <Add>location</Add>
              <Add>name</Add>
              <Add>offer</Add>
              <Add>platformFaultDomain</Add>
              <Add>platformUpdateDomain</Add>
              <Add>publisher</Add>
              <Add>sku</Add>
              <Add>version</Add>
              <Add>vmId</Add>
              <Add>vmSize</Add>
              <Add>subscriptionId</Add>
              <Add>resourceGroupName</Add>
              <Add>placementGroupId</Add>
              <Add>tags</Add>
              <Add>vmScaleSetName</Add>
            </ExcludedHeartbeatProperties>
          </Add>
                
          NOTE: exclusions will be lost upon upgrade.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule, Microsoft.AI.WindowsServer">
          <!--</Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.FirstChanceExceptionStatisticsTelemetryModule, Microsoft.AI.WindowsServer">-->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule, Microsoft.AI.Web">
          <Handlers>
            <!-- 
            Add entries here to filter out additional handlers: 
            
            NOTE: handler configuration will be lost upon NuGet upgrade.
            -->
            <Add>Microsoft.VisualStudio.Web.PageInspector.Runtime.Tracing.RequestDataHttpHandler</Add>
            <Add>System.Web.StaticFileHandler</Add>
            <Add>System.Web.Handlers.AssemblyResourceLoader</Add>
            <Add>System.Web.Optimization.BundleHandler</Add>
            <Add>System.Web.Script.Services.ScriptHandlerFactory</Add>
            <Add>System.Web.Handlers.TraceHandler</Add>
            <Add>System.Web.Services.Discovery.DiscoveryRequestHandler</Add>
            <Add>System.Web.HttpDebugHandler</Add>
          </Handlers>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web" />
      </TelemetryModules>
      <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
      <TelemetrySinks>
        <Add Name="default">
          <TelemetryProcessors>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryProcessor, Microsoft.AI.PerfCounterCollector" />
            <Add Type="Microsoft.ApplicationInsights.Extensibility.AutocollectedMetricsExtractor, Microsoft.ApplicationInsights" />
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <ExcludedTypes>Event</ExcludedTypes>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <IncludedTypes>Event</IncludedTypes>
            </Add>
          </TelemetryProcessors>
          <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel" />
        </Add>
      </TelemetrySinks>
      <!-- 
        Learn more about Application Insights configuration with ApplicationInsights.config here: 
        http://go.microsoft.com/fwlink/?LinkID=513840
      -->
      <InstrumentationKey>your-instrumentation-key-here</InstrumentationKey>
    </ApplicationInsights>
     ```

4. Fügen Sie vor dem schließenden `</ApplicationInsights>`-Tag Ihren Instrumentierungsschlüssel für Ihre Application Insights-Ressource hinzu.  Ihren Instrumentierungsschlüssel finden Sie auf der Übersichtsseite Ihrer neu erstellten Application Insights-Ressource, die Sie als Teil der Voraussetzungen für diesen Artikel erstellt haben.

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```

5. Erstellen Sie auf derselben Projektebene wie die `ApplicationInsights.config`-Datei einen Ordner namens `ErrorHandler` mit einer neuen C#-Datei namens `AiHandleErrorAttribute.cs`. Der Inhalt der Datei sollte nun wie folgt aussehen:

    ```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;
    
    namespace WebApplication10.ErrorHandler //namespace will vary based on your project name
    {
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
        public class AiHandleErrorAttribute : HandleErrorAttribute
        {
            public override void OnException(ExceptionContext filterContext)
            {
                if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
                {
                    //If customError is Off, then AI HTTPModule will report the exception
                    if (filterContext.HttpContext.IsCustomErrorEnabled)
                    {   
                        var ai = new TelemetryClient();
                        ai.TrackException(filterContext.Exception);
                    } 
                }
                base.OnException(filterContext);
            }
        }
    }
    
    ```

6. Öffnen Sie im `App_Start`-Ordner die `FilterConfig.cs`-Datei, und ändern Sie sie so, dass sie dem Beispiel entspricht:

    ```csharp
    using System.Web;
    using System.Web.Mvc;
    
    namespace WebApplication10 //Namespace will vary based on project name
    {
        public class FilterConfig
        {
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());
            }
        }
    }
    ```

7. Aktualisieren Sie die Web.config-Datei folgendermaßen:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <!--
      For more information on how to configure your ASP.NET application, please visit
      https://go.microsoft.com/fwlink/?LinkId=301880
      -->
    <configuration>
      <appSettings>
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.7.2" />
        <httpRuntime targetFramework="4.7.2" />
        <httpModules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" />
        </httpModules>
      </system.web>
      <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
          <dependentAssembly>
            <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" />
            <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" />
            <bindingRedirect oldVersion="0.0.0.0-12.0.0.0" newVersion="12.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Optimization" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="1.1.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-5.2.7.0" newVersion="5.2.7.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Memory" publicKeyToken="cc7b13ffcd2ddd51" culture="neutral" />
            <bindingRedirect oldVersion="0.0.0.0-4.0.1.1" newVersion="4.0.1.1" />
          </dependentAssembly>
        </assemblyBinding>
      </runtime>
      <system.codedom>
        <compilers>
          <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
          <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
        </compilers>
      </system.codedom>
      <system.webServer>
        <validation validateIntegratedModeConfiguration="false" />
        <modules>
          <remove name="TelemetryCorrelationHttpModule" />
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="managedHandler" />
          <remove name="ApplicationInsightsWebTracking" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
        </modules>
      </system.webServer>
    </configuration>
    
    ```

Sie haben nun erfolgreich die serverseitige Anwendungsüberwachung konfiguriert. Wenn Sie Ihre Web-App ausführen, werden in Application Insights Telemetriedaten angezeigt.

## <a name="add-client-side-monitoring"></a>Hinzufügen der clientseitigen Überwachung

In den vorherigen Abschnitten haben Sie Anleitungen zu den Methoden erhalten, mit denen die serverseitige Überwachung automatisch und manuell konfiguriert werden kann. Wenn Sie eine clientseitige Überwachung hinzufügen möchten, müssen Sie das [clientseitige JavaScript SDK](javascript.md) verwenden. Sie können clientseitige Transaktionen einer beliebigen Webseite überwachen, indem Sie ein [JavaScript-Codeausschnitt](javascript.md#snippet-based-setup) vor dem schließenden `</head>`-Tag im HTML-Code der Seite hinzufügen. 

Es ist zwar möglich, den Codeausschnitt dem Header jeder einzelnen HTML-Seite manuell hinzuzufügen, es wird jedoch empfohlen, den Codeausschnitt stattdessen auf einer ersten Seite hinzuzufügen, die den Codeausschnitt dann in alle Seiten der Website injiziert. Für die vorlagenbasierte ASP.NET-MVC-App in diesem Artikel heißt die Datei, die Sie bearbeiten müssen, `_Layout.cshtml`. Sie befindet sich unter **Ansichten** > **Freigegeben**.

Wenn Sie clientseitige Überwachung hinzufügen möchten, öffnen Sie die `_Layout.cshtml`-Datei, und folgen Sie der [auf dem Codeausschnitt basierenden Einrichtungsanleitung](javascript.md#snippet-based-setup) aus dem Artikel für die clientseitige Konfiguration des JavaScript SDK.

## <a name="troubleshooting"></a>Problembehandlung

Es gibt ein bekanntes Problem bei der aktuellen Version von Visual Studio 2019, dass der Instrumentierungsschlüssel beim Speichern in einem Benutzergeheimnis für .NET Framework-basierte Apps nicht mehr verwendet werden kann. Der Schlüssel muss dann letztendlich in der applicationInsights.config-Datei hartcodiert werden, um dieses Problem zu umgehen. Dieser Artikel wurde so verfasst, dass dieses Problem komplett umgangen wird, indem keine Benutzergeheimnisse verwendet werden.  

## <a name="open-source-sdk"></a>Open Source SDK

* [Lesen und Hinzufügen von Code](https://github.com/microsoft/ApplicationInsights-dotnet).

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](./release-notes.md).

## <a name="next-steps"></a>Nächste Schritte

* Fügen Sie synthetische Transaktionen hinzu, um zu testen, ob Ihre Website überall auf der Welt verfügbar ist. Informationen dazu finden Sie unter [Überwachen der Verfügbarkeit von Websites](monitor-web-app-availability.md).
* Unter [Erstellen von Stichproben in Application Insights](sampling.md) finden Sie Informationen, die Sie dabei unterstützen, den Telemetriedatenverkehr und Datenspeicherkosten zu senken.


