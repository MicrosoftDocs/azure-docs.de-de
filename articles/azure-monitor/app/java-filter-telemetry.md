---
title: Filtern von Azure Application Insights-Telemetriedaten in einer Java-Web-App
description: Telemetriedatenverkehr lässt sich reduzieren, indem Sie die Ereignisse herausfiltern, die Sie nicht überwachen müssen.
ms.topic: conceptual
ms.date: 3/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 9a57874652fa5ee58f39483903be211305c683f2
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601236"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtern von Telemetriedaten in einer Java-Web-App

> [!IMPORTANT]
> Der empfohlene Ansatz zur Überwachung von Java-Anwendungen ist die automatische Instrumentierung ohne Änderung des Codes. Befolgen Sie die Leitlinien für den [Application Insights Java 3.0-Agent](./java-in-process-agent.md).

Filter bieten eine Möglichkeit zur Auswahl der Telemetriedaten, die Ihre [Java-Web-App an Application Insights sendet](java-get-started.md). Es gibt einige Standardfilter, die Sie verwenden können, und Sie können auch eigene benutzerdefinierte Filter erstellen.

Es folgen die Standardfilter:

* Schweregrad der Ablaufverfolgung
* Bestimmte URLs, Schlüsselwörter oder Antwortcodes
* Schnelle Antworten, d.h. Anforderungen, auf die Ihre App schnell reagiert hat
* Bestimmte Ereignisnamen

> [!NOTE]
> Filter verzerren die Metriken Ihrer App. Wenn Sie z.B. langsame Reaktionszeiten untersuchen möchten, legen Sie einen Filter fest, der schnelle Antwortzeiten ausschließt. Ihnen sollte jedoch klar sein, dass die von Application Insights gemeldeten durchschnittlichen Antwortzeiten langsamer als die tatsächliche Geschwindigkeit sind und dass die Anzahl der Anforderungen kleiner als die tatsächliche Anzahl ist.
> Wenn dies von Belang ist, arbeiten Sie stattdessen mit [Stichproben](./sampling.md).

## <a name="setting-filters"></a>Festlegen von Filtern

Fügen Sie der Datei „ApplicationInsights.xml“ wie in diesem Beispiel den Abschnitt `TelemetryProcessors` hinzu:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```

[Überprüfen des vollständigen Satzes integrierter Prozessoren](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal).

## <a name="built-in-filters"></a>Integrierte Filter

### <a name="metric-telemetry-filter"></a>Telemetriefilter „Metrik“

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`: Durch Trennzeichen getrennte Liste benutzerdefinierter Metriknamen.


### <a name="page-view-telemetry-filter"></a>Telemetriefilter „Seitenaufruf“

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`: „Duration“ bezieht sich auf die Dauer des Ladens der Seite. Falls festgelegt, werden Seiten, die schneller als diese Angabe geladen werden, nicht gemeldet.
* `NotNeededNames`: Durch Trennzeichen getrennte Liste von Seitennamen.
* `NotNeededUrls`: Durch Trennzeichen getrennte Liste von URL-Fragmenten. Beispielsweise filtert `"home"` alle Seiten heraus, deren URL „home“ enthält.


### <a name="request-telemetry-filter"></a>Telemetriefilter „Anforderung“


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filter „Synthetische Quelle“

Filtert alle Telemetriedaten heraus, die Werte in der „SyntheticSource“-Eigenschaft aufweisen. Dazu gehören Anforderungen von Bots, Spidern und Verfügbarkeitstests.

Filtern Sie Telemetriedaten für alle synthetischen Anforderungen heraus:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtern Sie Telemetriedaten für bestimmte synthetische Quellen heraus:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`: Durch Trennzeichen getrennte Liste von synthetischen Datenquellennamen.

### <a name="telemetry-event-filter"></a>Telemetriefilter „Ereignis“

Filtert benutzerdefinierte (mit [TrackEvent()](./api-custom-events-metrics.md#trackevent) protokollierte) Ereignisse.


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`: Durch Trennzeichen getrennte Liste von Ereignisnamen.


### <a name="trace-telemetry-filter"></a>Telemetriefilter „Ablaufverfolgung“

Filtert (mit [TrackTrace()](./api-custom-events-metrics.md#tracktrace) oder einem [Protokollierungsframework-Collector](java-trace-logs.md) protokollierte) Protokollablaufverfolgungen.

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* Für `FromSeverityLevel` gültige Werte sind:
  *  OFF: Filtert ALLE Ablaufverfolgungen heraus.
  *  TRACE: Keine Filterung. Entspricht der Ebene TRACE.
  *  INFO: Filter die Ebene TRACE heraus.
  *  WARN: Filtert TRACE und INFO heraus.
  *  FEHLER: Filtert WARN, INFO und TRACE heraus.
  *  CRITICAL: Filtert alles außer CRITICAL heraus.


## <a name="custom-filters"></a>Benutzerdefinierte Filter

### <a name="1-code-your-filter"></a>1. Codieren des Filters

Erstellen Sie in Ihrem Code eine Klasse, die `TelemetryProcessor` implementiert:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Aufrufen des Filters in der Konfigurationsdatei

In „ApplicationInsights.xml“:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Aufrufen des Filters (Java Spring)

Für Anwendungen, die auf dem Spring Framework basieren, müssen benutzerdefinierte Telemetrieprozessoren in Ihrer Hauptanwendungsklasse als Bean registriert sein. Sie verwenden dann beim Start der Anwendung automatisch das Autowiring.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Sie müssen Ihre eigenen Filterparameter in `application.properties` erstellen und das ausgelagerte Konfigurationsframework von Spring Boot nutzen, um diese Parameter an Ihren benutzerdefinierten Filter zu übergeben. 


## <a name="troubleshooting"></a>Problembehandlung

*Meine Filter funktioniert nicht.*

* Überprüfen Sie, ob Sie gültige Parameterwerte angegeben haben. Die Dauer muss als ganze Zahl angegeben werden. Ungültige Werte führen dazu, dass der Filter ignoriert wird. Wenn Ihr benutzerdefinierter Filter eine Ausnahme in einem Konstruktor oder einer „Set“-Methode auslöst, wird er ignoriert.

## <a name="next-steps"></a>Nächste Schritte

* [Stichproben](./sampling.md): Erwägen Sie die Stichprobennahme als Alternative, die Ihre Metriken nicht verzerrt.

