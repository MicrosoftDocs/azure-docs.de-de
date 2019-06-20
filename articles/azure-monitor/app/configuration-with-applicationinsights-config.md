---
title: Referenz zu „ApplicationInsights.config“ – Azure | Microsoft-Dokumentation
description: Aktivieren oder deaktivieren Sie Datensammlungsmodule, und fügen Sie Leistungsindikatoren und andere Parameter hinzu.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 13bf27fd58530c357e3bb83f7cbc503855d40304
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075334"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurieren des Application Insights-SDK mit "ApplicationInsights.config" oder XML
Das Application Insights .NET-SDK umfasst eine Reihe von NuGet-Paketen. Das [Kernpaket](https://www.nuget.org/packages/Microsoft.ApplicationInsights) stellt die API für das Senden von Telemetriedaten an Application Insights bereit. [Zusätzliche Pakete](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) bieten *Telemetriemodule* und *-initialisierer* für die automatische Nachverfolgung von Telemetriedaten von Ihrer Anwendung und deren Kontext. Durch Anpassen der Konfigurationsdatei können Sie Telemetriemodule und -initialisierer aktivieren oder deaktivieren sowie Parameter für einige von ihnen festlegen.

Die Konfigurationsdatei heißt `ApplicationInsights.config` oder `ApplicationInsights.xml`, je nach dem Typ der Anwendung. Sie wird dem Projekt bei der [Installation der meisten SDK-Versionen][start] automatisch hinzugefügt. Über den [Statusmonitor auf einem IIS-Server][redfield] oder durch Auswahl der [Application Insights-Erweiterung für eine Azure-Website oder einen virtuellen Computer](azure-web-apps.md) wird sie außerdem einer Web-App hinzugefügt.

Es gibt keine gleichwertige Datei zum Steuern des [SDK in einer Webseite][client].

In diesem Dokument sind die Abschnitte beschrieben, die in der Konfigurationsdatei enthalten sind, wie die Steuerung der Komponenten des SDK damit durchgeführt wird und welche NuGet-Pakete diese Komponenten laden.

> [!NOTE]
> Die Anweisungen für „ApplicationInsights.config“ und „ApplicationInsights.xml“ gelten nicht für das .NET Core SDK. Anweisungen zum Konfigurieren von .NET Core-Anwendungen finden Sie in [dieser](../../azure-monitor/app/asp-net-core.md) Anleitung.

## <a name="telemetry-modules-aspnet"></a>Telemetriemodule (ASP.NET)
Jedes Telemetriemodul erfasst eine bestimmte Art von Daten und verwendet die Kern-API zum Senden der Daten. Die Module werden von verschiedenen NuGet-Paketen installiert, mit denen der CONFIG-Datei auch die erforderlichen Zeilen hinzugefügt werden.

Für jedes Modul gibt es in der Konfigurationsdatei einen Knoten. Um ein Modul zu deaktivieren, löschen Sie den Knoten, oder kommentieren Sie ihn aus.

### <a name="dependency-tracking"></a>Abhängigkeitsüberwachung
[Dependency tracking](../../azure-monitor/app/asp-net-dependencies.md) werden Telemetriedaten zu Aufrufen erfasst, die von Ihrer App für Datenbanken und externe Dienste und Datenbanken durchgeführt werden. Damit dieses Modul auf einem IIS-Server funktioniert, müssen Sie den [Statusmonitor installieren][redfield]. Zur Verwendung in Azure-Web-Apps oder auf virtuellen Computern [wählen Sie die Application Insights-Erweiterung](azure-web-apps.md).

Sie können auch eigenen Code zur Abhängigkeitsnachverfolgung mithilfe der [TrackDependency-API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)schreiben.

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Leistungserfassung
Dient zum [Sammeln von Systemleistungsindikatoren](../../azure-monitor/app/performance-counters.md), z.B. CPU-, Arbeitsspeicher- und Netzwerkauslastung von IIS-Installationen. Sie können angeben, welche Leistungsindikatoren erfasst werden sollen, z. B. auch Leistungsindikatoren, die Sie selbst eingerichtet haben.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights-Diagnosetelemetrie
`DiagnosticsTelemetryModule` gibt Fehler im eigentlichen Application Insights-Instrumentationscode an. Dies geschieht beispielsweise, wenn der Code nicht auf Leistungsindikatoren zugreifen kann oder wenn `ITelemetryInitializer` eine Ausnahme auslöst wird. Telemetriedaten der Ablaufverfolgung, die in diesem Modul nachverfolgt werden, werden in der [Diagnosesuche][diagnostic] angezeigt.

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Entwicklermodus
`DeveloperModeWithDebuggerAttachedTelemetryModule` erzwingt, dass `TelemetryChannel` von Application Insights Daten sofort sendet (jeweils nur ein Telemetrieelement), wenn ein Debugger an den Anwendungsprozess angefügt ist. So wird die Zeitdauer zwischen dem Moment, in dem die Anwendung die Telemetrie nachverfolgt, und der Anzeige im Application Insights-Portal reduziert. Für die CPU und die Netzwerkbandbreite ist dies eine erhebliche Belastung.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) -NuGet-Paket.

### <a name="web-request-tracking"></a>Nachverfolgung von Webanforderungen
Meldet die [Antwortzeit und den Ergebniscode](../../azure-monitor/app/asp-net.md) von HTTP-Anforderungen.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) -NuGet-Paket

### <a name="exception-tracking"></a>Ausnahmeverfolgung
`ExceptionTrackingTelemetryModule` verfolgt nicht behandelte Ausnahmen in Ihrer Web-App. Informationen hierzu finden Sie unter [Fehler und Ausnahmen][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) -NuGet-Paket
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` – Verfolgt [nicht überwachte Aufgabenausnahmen](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` – Verfolgt nicht behandelte Ausnahmen für Workerrollen, Windows-Dienste und Konsolenanwendungen.
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) .

### <a name="eventsource-tracking"></a>EventSource-Nachverfolgung
Mit `EventSourceTelemetryModule` können Sie EventSource-Ereignisse so konfigurieren, dass sie als Ablaufverfolgungen an Application Insights gesendet werden. Informationen zum Nachverfolgen von EventSource-Ereignissen finden Sie unter [Verwenden von EventSource-Ereignissen](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW-Ereignisnachverfolgung
Mit `EtwCollectorTelemetryModule` können Sie Ereignisse von ETW-Anbietern so konfigurieren, dass Sie als Ablaufverfolgungen an Application Insights gesendet werden. Informationen zum Nachverfolgen von ETW-Ereignissen finden Sie unter [Verwenden von ETW-Ereignissen](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Das Microsoft.ApplicationInsights-Paket enthält die [Kern-API](https://msdn.microsoft.com/library/mt420197.aspx) des SDK. Sie wird von den anderen Telemetriemodulen verwendet, und Sie können sie auch [verwenden, um Ihre eigene Telemetrie zu definieren](../../azure-monitor/app/api-custom-events-metrics.md).

* Kein Eintrag in „ApplicationInsights.config“.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) . Wenn Sie nur dieses NuGet-Paket installieren, wird keine CONFIG-Datei generiert.

## <a name="telemetry-channel"></a>Telemetriekanal
Der [Telemetriekanal](telemetry-channels.md) verwaltet die Pufferung und Übertragung von Telemetriedaten an den Application Insights-Dienst.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` ist der Standardkanal für Webanwendungen. Er puffert Daten im Arbeitsspeicher und nutzt Wiederholungsmechanismen und lokalen Datenträgerspeicher für eine zuverlässigere Übermittlung von Telemetriedaten.
* `Microsoft.ApplicationInsights.InMemoryChannel` ist ein einfacher Telemetriekanal, der dann verwendet wird, wenn kein anderer Kanal konfiguriert ist. 

## <a name="telemetry-initializers-aspnet"></a>Telemetrieinitialisierer (ASP.NET)
Mit Telemetrieinitialisierern werden Kontexteigenschaften festgelegt, die mit jedem Element der Telemetrie gesendet werden.

Sie können [eigene Initialisierer schreiben](../../azure-monitor/app/api-filtering-sampling.md#add-properties) , um Kontexteigenschaften festzulegen.

Die standardmäßigen Initialisierer werden entweder von den Web- oder WindowsServer-NuGet-Paketen festgelegt:

* `AccountIdTelemetryInitializer` legt die AccountId-Eigenschaft fest.
* `AuthenticatedUserIdTelemetryInitializer` legt die AuthenticatedUserId-Eigenschaft so fest, wie sie vom JavaScript-SDK festgelegt wurde.
* `AzureRoleEnvironmentTelemetryInitializer` aktualisiert die `RoleName`- und `RoleInstance`-Eigenschaften des `Device`-Kontexts für alle Telemetrieelemente mit Informationen, die aus der Azure-Laufzeitumgebung extrahiert wurden.
* `BuildInfoConfigComponentVersionTelemetryInitializer` aktualisiert die `Version`-Eigenschaft des `Component`-Kontexts für alle Telemetrieelemente mit dem Wert, der aus der von MS Build erzeugten Datei `BuildInfo.config` extrahiert wurde.
* `ClientIpHeaderTelemetryInitializer` aktualisiert die `Ip`-Eigenschaft des `Location`-Kontexts aller Telemetrieelemente basierend auf dem `X-Forwarded-For`-HTTP-Header der Anforderung.
* `DeviceTelemetryInitializer` aktualisiert die folgenden Eigenschaften des `Device`-Kontexts für alle Telemetrieelemente.
  * `Type` wird auf "PC" festgelegt.
  * `Id` wird auf den Domänennamen des Computers festgelegt, auf dem die Webanwendung ausgeführt wird.
  * `OemName` wird auf den Wert festgelegt, der mithilfe von WMI aus dem Feld `Win32_ComputerSystem.Manufacturer` extrahiert wurde.
  * `Model` wird auf den Wert festgelegt, der mithilfe von WMI aus dem Feld `Win32_ComputerSystem.Model` extrahiert wurde.
  * `NetworkType` wird auf den Wert festgelegt, der aus dem Feld `NetworkInterface` extrahiert wurde.
  * `Language` wird auf den Namen von `CurrentCulture` festgelegt.
* `DomainNameRoleInstanceTelemetryInitializer` aktualisiert die `RoleInstance`-Eigenschaft des `Device`-Kontexts für alle Telemetrieelemente mit dem Domänennamen des Computers, auf dem die Webanwendung ausgeführt wird.
* `OperationNameTelemetryInitializer` aktualisiert die `Name`-Eigenschaft von `RequestTelemetry` und die `Name`-Eigenschaft des `Operation`-Kontexts aller Telemetrieelemente basierend auf der HTTP-Methode sowie die Namen von ASP.NET-MVC-Controllern und Aktionen, die aufgerufen werden, um die Anforderung zu verarbeiten.
* `OperationIdTelemetryInitializer` oder `OperationCorrelationTelemetryInitializer` aktualisiert die `Operation.Id`-Kontexteigenschaft aller verfolgten Telemetrieelemente, während eine Anforderung mit der automatisch generierten `RequestTelemetry.Id` behandelt wird.
* `SessionTelemetryInitializer` aktualisiert die `Id`-Eigenschaft des `Session`-Kontexts für alle Telemetrieelemente mit Werten, die aus dem `ai_session`-Cookie extrahiert werden, der vom Application Insights-JavaScript-Instrumentationscode generiert wird, der im Browser des Benutzers ausgeführt wird.
* `SyntheticTelemetryInitializer` oder `SyntheticUserAgentTelemetryInitializer` aktualisiert die Kontexteigenschaften `User`, `Session` und `Operation` aller Telemetrieelemente, die beim Behandeln einer Anforderung von einer synthetischen Quelle (beispielsweise ein Verfügbarkeitstest oder Suchmaschinen-Bot) nachverfolgt werden. Standardmäßig werden vom [Metrik-Explorer](../../azure-monitor/app/metrics-explorer.md) keine synthetischen Telemetriedaten angezeigt.

    Die `<Filters>` legen identifizierende Eigenschaften der Anforderungen fest.
* `UserTelemetryInitializer` aktualisiert die `Id`- und `AcquisitionDate`-Eigenschaften des `User`-Kontexts für alle Telemetrieelemente mit Werten, die aus dem `ai_user`-Cookie extrahiert werden, der vom Application Insights-JavaScript-Instrumentationscode generiert wird, der im Browser des Benutzers ausgeführt wird.
* `WebTestTelemetryInitializer` legt die Benutzer-ID, Sitzungs-ID und synthetischen Quelleneigenschaften für die HTTP-Anforderungen fest, die aus [Verfügbarkeitstests](../../azure-monitor/app/monitor-web-app-availability.md) stammen.
  Die `<Filters>` legen identifizierende Eigenschaften der Anforderungen fest.

Für .NET-Anwendungen in Service Fabric können Sie das `Microsoft.ApplicationInsights.ServiceFabric`-NuGet-Paket aufnehmen. Dieses Paket enthält ein `FabricTelemetryInitializer`-Element, wodurch Service Fabric-Eigenschaften zu Telemetrieelementen hinzugefügt werden. Weitere Informationen finden Sie auf der [GitHub-Seite](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) zu den Eigenschaften, die von diesem NuGet-Paket hinzugefügt werden.

## <a name="telemetry-processors-aspnet"></a>Telemetrieprozessoren (ASP.NET)
Mit Telemetrieprozessoren kann jedes Telemetrieelement direkt vor dem Senden vom SDK an das Portal gefiltert und geändert werden.

Sie können [Ihre eigenen Telemetrieprozessoren schreiben](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Telemetrieprozessor für adaptive Stichprobenerstellung (von 2.0.0-beta3)
Diese Einstellung ist standardmäßig aktiviert. Wenn die App viele Telemetriedaten sendet, entfernt dieser Prozessor einige davon.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Der Parameter enthält das Ziel, das der Algorithmus zu erreichen versucht. Die Instanzen des SDK funktionieren unabhängig voneinander. Wenn Ihr Server also aus mehreren Computern zu einem Cluster gruppiert ist, erhöht sich das tatsächliche Volumen an Telemetriedaten entsprechend.

[Erfahren Sie mehr über Sampling](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Telemetrieprozessor für die Stichprobenerstellung mit festem Prozentsatz (von 2.0.0-beta1)
Es gibt auch einen standardmäßigen [Telemetrieprozessor für die Stichprobenerstellung](../../azure-monitor/app/api-filtering-sampling.md) (von 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Kanalparameter (Java)
Diese Parameter beeinflussen, wie das Java-SDK die gesammelten Telemetriedaten speichert und leert.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Die Anzahl der Telemetrieelemente, die im In-Memory-Speicher des SDK gespeichert werden können. Wenn diese Zahl erreicht ist, wird der Telemetriepuffer geleert – d. h. die Telemetrieelemente werden an den Application Insights-Server gesendet.

* Min: 1
* Max: 1000
* Standardwert: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Bestimmt, wie oft der In-Memory-Speicher geleert (die Daten an Application Insights gesendet) werden sollen.

* Min: 1
* Max: 300
* Standardwert: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Bestimmt die maximale Größe in MB, die dem beständigen Speicher auf dem lokalen Datenträger zugewiesen wird. Dieser Speicher wird zur dauerhaften Speicherung von Telemetrieelementen verwendet, die nicht an den Application Insights-Endpunkt übertragen werden konnten. Wenn die Speichergröße erreicht ist, werden neue Telemetrieelemente verworfen.

* Min: 1
* Max: 100
* Standardwert: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

#### <a name="local-forwarder"></a>Lokale Weiterleitung

[Die lokale Weiterleitung](opencensus-local-forwarder.md) ist ein Agent, der Application Insights- oder [OpenCensus](https://opencensus.io/)-Telemetriedaten aus einer Vielzahl von SDKs und Frameworks erfasst und an Application Insights weiterleitet. Sie kann unter Windows und Linux ausgeführt werden. In Kombination mit dem Application Insights Java SDK bietet die lokale Weiterleitung vollständige Unterstützung für [Livemetriken](../../azure-monitor/app/live-stream.md) und adaptive Stichprobenerstellung.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>

<!-- The properties below are optional. The values shown are the defaults for each property -->

<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

Wenn Sie das SpringBoot-Startprogramm verwenden, fügen Sie Folgendes in Ihre Konfigurationsdatei (application.properties) ein:

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

Die Standardwerte sind für die Konfiguration von SpringBoot „application.properties“ und „applicationinsights.xml“ identisch.

## <a name="instrumentationkey"></a>InstrumentationKey
Hierdurch wird die Application Insights-Ressource bestimmt, in der die Daten angezeigt werden. In der Regel erstellen Sie für jede Ihrer Anwendungen eine separate Ressource mit einem separaten Schlüssel.

Wenn Sie den Schlüssel dynamisch festlegen möchten, um z. B. Ergebnisse aus Ihrer Anwendung an andere Ressourcen zu senden, können Sie den Schlüssel aus der Konfigurationsdatei entfernen und stattdessen im Code festlegen.

Um den Schlüssel für alle Instanzen von TelemetryClient festzulegen, einschließlich der Standard-Telemetriemodule, legen Sie den Schlüssel in "TelemetryConfiguration.Active" fest. Verwenden Sie hierzu eine Initialisierungsmethode wie "global.aspx.cs" in einem ASP.NET-Dienst:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      //...
```

Wenn Sie nur einen bestimmten Satz von Ereignissen an eine andere Ressource senden möchten, können Sie den Schlüssel für einen bestimmten TelemetryClient festlegen:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Um einen neuen Schlüssel abzurufen, [erstellen Sie eine neue Ressource im Application Insights-Portal][new].



## <a name="applicationid-provider"></a>ApplicationId-Anbieter

_Verfügbar ab v2.6.0_

Der Zweck dieses Anbieters besteht darin, eine Anwendungs-ID auf der Grundlage eines Instrumentierungsschlüssels zu suchen. Die Anwendungs-ID ist in „RequestTelemetry“ und „DependencyTelemetry“ enthalten und wird zum Festlegen der Korrelation im Portal verwendet.

Diese ist verfügbar, indem Sie `TelemetryConfiguration.ApplicationIdProvider` im Code oder in der Konfigurationsdatei festlegen.

### <a name="interface-iapplicationidprovider"></a>Schnittstelle: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Das SDK [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) bietet zwei Implementierungen: `ApplicationInsightsApplicationIdProvider` und `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Dies ist ein Wrapper um die Profil-API. Er drosselt Anforderungen und Cacheergebnisse.

Dieser Anbieter wird der Konfigurationsdatei hinzugefügt, wenn Sie [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) oder [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) installieren.

Diese Klasse enthält die optionale Eigenschaft `ProfileQueryEndpoint`.
Sie ist standardmäßig auf `https://dc.services.visualstudio.com/api/profiles/{0}/appId` festgelegt.
Wenn Sie einen Proxy für diese Konfiguration konfigurieren müssen, wird empfohlen, die Proxyfunktion für die Basisadresse zu verwenden und „/api/profiles/{0}/appId“ einzufügen. Beachten Sie, dass „{0}“ zur Laufzeit pro Anforderung durch den Instrumentierungsschlüssel ersetzt wird.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Beispielkonfiguration per „ApplicationInsights.config“:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Beispielkonfiguration per Code:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Hierbei handelt es sich um einen statischen Anbieter, der auf den konfigurierten Kombinationen aus Instrumentierungsschlüssel und Anwendungs-ID basiert.

Die `Defined`-Eigenschaft dieser Klasse hat das Format „Dictionary<Zeichenfolge,Zeichenfolge>“ und gibt die Kombinationen aus Instrumentierungsschlüssel und Anwendungs-ID an.

Diese Klasse enthält die optionale Eigenschaft `Next`, mit der ein weiterer Anbieter konfiguriert werden kann. Dieser wird verwendet, wenn ein Instrumentierungsschlüssel angefordert wird, der in Ihrer Konfiguration nicht vorhanden ist.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Beispielkonfiguration per „ApplicationInsights.config“:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Beispielkonfiguration per Code:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Nächste Schritte
[Weitere Informationen zur API][api]

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
