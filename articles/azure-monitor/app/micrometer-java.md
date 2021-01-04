---
title: Verwenden von Micrometer mit dem Azure Application Insights Java SDK
description: Eine Schritt-für-Schritt-Anleitung zur Verwendung von Micrometer mit Ihren Application Insights Spring Boot- und Spring Boot-fremden Anwendungen.
ms.topic: conceptual
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.date: 11/01/2018
ms.openlocfilehash: a80684fbaa34f8906d321f56d3819039d3798ecb
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600981"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>Verwenden von Micrometer mit dem Azure Application Insights Java SDK (nicht empfohlen)

> [!IMPORTANT]
> Der empfohlene Ansatz zur Überwachung von Java-Anwendungen ist die automatische Instrumentierung ohne Änderung des Codes. Die Micrometer-Telemetrie wird automatisch mit dem Application Insights Java 3.0-Agent erfasst. Befolgen Sie die Richtlinien für den [Application Insights Java 3.0-Agent](./java-in-process-agent.md).

> [!NOTE]
> Das Java SDK für Application Insights unterstützt Spring Webflux nicht. Verwenden Sie stattdessen den [Application Insights Java 3.0-Agent](./java-in-process-agent.md). 
>
> Sowohl Webflux als auch Micrometer werden im [Application Insights-Java 3.0-Agent](./java-on-premises.md) unterstützt, ohne dass eine Instrumentierung erforderlich ist. 

Die Micrometer-Anwendungsüberwachung misst die Metriken für JVM-basierten Anwendungscode und ermöglicht den Export der Daten in Ihre bevorzugten Überwachungssysteme. In diesem Artikel erfahren Sie, wie Sie Micrometer mit Ihren Application Insights Spring Boot- und Spring Boot-fremden Anwendungen verwenden.

## <a name="using-spring-boot-15x"></a>Verwenden von Spring Boot 1.5x
Fügen Sie die folgenden Abhängigkeiten zur pom.xml- oder build.gradle-Datei hinzu: 
* [Application Insights-Spring-Boot-Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 oder höher
* Micrometer Azure-Registrierung 1.1.0 oder höher
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 oder höher (dies unterstützt den Autoconfig-Code im Spring-Framework).
* [ApplicationInsights-Ressource](./create-new-resource.md)

Schritte

1. Aktualisieren Sie die „pom.xml“-Datei Ihrer Spring Boot-Anwendung und fügen Sie die folgenden Abhängigkeiten hinzu:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Aktualisieren Sie die application.properties- oder die yml-Datei mit dem Application Insights Instrumentation-Schlüssel mithilfe der folgenden Eigenschaft:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Erstellen und Ausführen Ihrer Anwendung
2. Das oben Aufgeführte sollte mit vorab aggregierten Metriken, die automatisch in Azure Monitor gesammelt werden, funktionsfähig sein. Informationen zum Optimieren von der Application Insights Spring Boot Starter-Option finden Sie in der [Readme auf GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Verwenden von Spring 2.x

Fügen Sie die folgenden Abhängigkeiten zur pom.xml- oder build.gradle-Datei hinzu:

* Application Insights-Spring-Boot-Starter2.1.2-BETA oder höher
* Azure-Spring-Boot-Metrics-Starters 2.0.7 oder höher
* [Eine Application Insights-Ressource](./create-new-resource.md)

Schritte:

1. Aktualisieren Sie die „pom.xml“-Datei Ihrer Spring Boot-Anwendung und fügen Sie die folgende Abhängigkeit hinzu:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Aktualisieren Sie die application.properties- oder die yml-Datei mit dem Application Insights Instrumentation-Schlüssel mithilfe der folgenden Eigenschaft:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Erstellen und Ausführen Ihrer Anwendung
4. Das oben Aufgeführte sollte mit vorab aggregierten Metriken, die automatisch in Azure Monitor gesammelt werden, funktionsfähig sein. Informationen zum Optimieren von der Application Insights Spring Boot Starter-Option finden Sie in der [Readme auf GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Standardmetriken:

*    Automatisch konfigurierte Metriken für Tomcat, JVM, Logback-Metriken, Log4J-Metriken, Betriebszeitmetriken, Prozessormetriken, FileDescriptorMetrics.
*    Wenn Netflix Hystrix im Klassenpfad vorhanden ist, erhalten wir diese Metriken z. B. ebenfalls. 
*    Die folgenden Metriken können durch Hinzufügen der entsprechenden Beans verfügbar sein. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - OkHttp3-Metriken 
        - Kafka-Metriken 

 

So deaktivieren Sie automatische Metrikauflistung: 
 
- JVM-Metriken: 
    - management.metrics.binders.jvm.enabled=false 
- Logback-Metriken: 
    - management.metrics.binders.logback.enabled=false
- Betriebszeitmetriken: 
    - management.metrics.binders.uptime.enabled=false 
- Prozessormetriken:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix-Metriken, wenn sich die Bibliothek im Klassenpfad befindet: 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ-Metriken, wenn sich die Bibliothek im Klassenpfad befindet: 
    - spring.aop.enabled=false 

> [!NOTE]
> Geben Sie die oben genannten Eigenschaften in der application.properties- oder application.yml--Datei Ihrer Spring Boot-Anwendung an.

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Verwenden von Micrometer für Spring Boot-fremde Webanwendungen

Fügen Sie die folgenden Abhängigkeiten zur pom.xml- oder build.gradle-Datei hinzu:

* Application Insights Web Auto 2.5.0 oder höher
* Micrometer Azure-Registrierung 1.1.0 oder höher
* [Eine Application Insights-Ressource](./create-new-resource.md)

Schritte:

1. Fügen Sie die folgenden Abhängigkeiten zur pom.xml- oder build.gradle-Datei hinzu:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Fügen Sie die Datei `ApplicationInsights.xml` in den Ressourcenordner ein:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Beispiel-Servlet-Klasse (gibt eine Timer-Metrik aus):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Beispiel für Konfigurationsklasse:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Weitere Informationen zu Metriken finden Sie in der [Micrometerdokumentation](https://micrometer.io/docs/).

Weiteren Beispielcode zum Erstellen verschiedener Arten von Metriken finden Sie im [offiziellen Micrometer-GitHub-Repository](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Gewusst wie: Binden zusätzlicher Metrikauflistungen

### <a name="springbootspring"></a>SpringBoot/Spring

Erstellen Sie ein Bean der jeweiligen Metrikkategorie. Wie benötigen beispielsweise Guave-Cachemetriken:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Es gibt mehrere Metriken, die standardmäßig nicht aktiviert sind, aber in der oben genannten Weise gebunden werden können. Eine vollständige Liste finden Sie im [offiziellen Micrometer-GitHub-Repository](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Spring-fremde Apps
Fügen Sie den folgenden Bindungscode zur Konfigurationsdatei hinzu:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Micrometer finden Sie in der offiziellen [Micrometer-Dokumentation](https://micrometer.io/docs).
* Informationen zu Spring in Azure finden Sie in der offiziellen [Dokumentation zu Spring in Azure](/java/azure/spring-framework/?view=azure-java-stable).