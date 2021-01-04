---
title: Azure Monitor Application Insights Java
description: Überwachung der Anwendungsleistung ohne Codeänderungen für Java-Anwendungen, die in einer beliebigen Umgebung ausgeführt werden. Verteilte Ablaufverfolgung und Anwendungszuordnung.
ms.topic: conceptual
ms.date: 03/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 4b29e5375c10fc3c1aaa203df720fdd24090d11e
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601134"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights"></a>Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights

Bei der Java-Anwendungsüberwachung ohne Code geht es um Einfachheit. Es gibt keine Codeänderungen, und der Java-Agent kann durch nur wenige Konfigurationsänderungen aktiviert werden.

 Der Java-Agent kann in jeder Umgebung verwendet werden und ermöglicht Ihnen die Überwachung aller Ihrer Java-Anwendungen. Anders ausgedrückt: Ganz gleich, ob Sie Ihre Java-Apps auf VMs, lokal, in AKS, unter Windows oder unter Linux ausführen – Sie nennen die App, und der Java 3.0-Agent überwacht sie.

Das Application Insights Java SDK muss nicht mehr zu Ihrer Anwendung hinzugefügt werden, da der 3.0-Agent Anforderungen, Abhängigkeiten und Protokolle eigenständig und automatisch sammelt.

Sie können weiterhin benutzerdefinierte Telemetriedaten aus Ihrer Anwendung senden. Der 3.0-Agent verfolgt und korreliert diese zusammen mit der gesamten automatisch gesammelten Telemetrie.

Der 3.0-Agent unterstützt Java 8 und höher.

## <a name="quickstart"></a>Schnellstart

**1. Herunterladen des Agents**

> [!WARNING]
> **Bei einem Upgrade von Vorschauversion 3.0**
>
> Lesen Sie sich sorgfältig alle [Konfigurationsoptionen](./java-standalone-config.md) durch, da neben dem Dateinamen, der nun komplett in Kleinbuchstaben geschrieben wird, auch die JSON-Struktur vollständig geändert wurde.

Laden Sie [applicationinsights-agent-3.0.0.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0/applicationinsights-agent-3.0.0.jar) herunter.

**2. Verweisen der JVM auf den Agent**

Fügen Sie den JVM-Argumenten Ihrer Anwendung den Eintrag `-javaagent:path/to/applicationinsights-agent-3.0.0.jar` hinzu.

Typische JVM-Argumente sind `-Xmx512m` und `-XX:+UseG1GC`. Wenn Sie wissen, wo diese hinzugefügt werden, wissen Sie auch, wo dieses Argument hinzuzufügen ist.

Weitere Informationen zum Konfigurieren der JVM-Argumente Ihrer Anwendung finden Sie unter [Tipps für das Updaten Ihrer JVM-Argumente](./java-standalone-arguments.md).

**3. Verweisen des Agents auf die Application Insights-Ressource**

Wenn Sie noch nicht über eine Application Insights-Ressource verfügen, können Sie eine neue Ressource erstellen, indem Sie die Schritte im [Leitfaden zum Erstellen einer Ressource](./create-new-resource.md) ausführen.

Verweisen Sie den Agent auf Ihre Application Insights-Ressource, indem Sie eine Umgebungsvariable festlegen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

Sie können aber auch eine Konfigurationsdatei mit dem Namen `applicationinsights.json` erstellen und sie im gleichen Verzeichnis wie `applicationinsights-agent-3.0.0.jar` mit folgendem Inhalt ablegen:

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Die Verbindungszeichenfolge finden Sie in der Application Insights-Ressource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-Verbindungszeichenfolge":::

**4. Das war's schon!**

Starten Sie nun Ihre Anwendung, und wechseln Sie zur Application Insights-Ressource im Azure-Portal, um die Überwachungsdaten anzuzeigen.

> [!NOTE]
> Es kann einige Minuten dauern, bis die Überwachungsdaten im Portal angezeigt werden.


## <a name="configuration-options"></a>Konfigurationsoptionen

In der Datei `applicationinsights.json` können Sie zusätzlich Folgendes konfigurieren:

* Cloudrollenname
* Cloudrolleninstanz
* Stichproben
* JMX-Metriken
* Benutzerdefinierte Dimensionen
* Telemetrieprozessoren (Vorschauversion)
* Automatisch gesammelte Protokolle
* Automatisch gesammelte Micrometer-Metriken (einschließlich Spring Boot Actuator-Metriken)
* Heartbeat
* HTTP-Proxy
* Selbstdiagnose

Ausführliche Informationen finden Sie unter [Konfigurationsoptionen](./java-standalone-config.md).

## <a name="auto-collected-requests-dependencies-logs-and-metrics"></a>Automatisch gesammelte Anforderungen, Abhängigkeiten, Protokolle und Metriken

### <a name="requests"></a>Requests

* JMS-Consumer
* Kafka-Consumer
* Netty/WebFlux
* Servlets
* Spring-Zeitplanung

### <a name="dependencies-with-distributed-trace-propagation"></a>Abhängigkeiten mit Weitergabe der verteilten Ablaufverfolgung

* Apache HttpClient und HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* JMS
* Kafka
* Netty-Client
* OkHttp

### <a name="other-dependencies"></a>Andere Abhängigkeiten

* Cassandra
* JDBC
* MongoDB (asynchron und synchron)
* Redis (Lettuce und Jedis)

### <a name="logs"></a>Protokolle

* java.util.logging
* Log4j (einschließlich MDC-Eigenschaften)
* SLF4J/Logback (einschließlich MDC-Eigenschaften)

### <a name="metrics"></a>Metriken

* Micrometer (einschließlich Metriken des Spring Boot-Aktors)
* JMX-Metriken

## <a name="send-custom-telemetry-from-your-application"></a>Senden benutzerdefinierter Telemetriedaten aus Ihrer Anwendung

Unser Ziel in Version 3.0 und höher besteht darin, Ihnen das Senden benutzerdefinierter Telemetriedaten mithilfe von Standard-APIs zu ermöglichen.

Wir unterstützen bisher Micrometer, beliebte Protokollierungsframeworks und das Application Insights Java 2.x SDK.
Application Insights Java 3.0 erfasst automatisch die über diese APIs gesendeten Telemetriedaten und korreliert sie mit automatisch gesammelten Telemetriedaten.

### <a name="supported-custom-telemetry"></a>Unterstützte benutzerdefinierte Telemetrie

Die folgende Tabelle zeigt die derzeit unterstützten benutzerdefinierten Telemetrietypen, die Sie zur Ergänzung des Java 3.0-Agents aktivieren können. Zusammenfassung für das [Application Insights Java 2.x SDK](#send-custom-telemetry-using-application-insights-java-2x-sdk): Benutzerdefinierte Metriken werden über Micrometer unterstützt, benutzerdefinierte Ausnahmen und Ablaufverfolgungen können durch Protokollierungsframeworks aktiviert werden, und jede Art von benutzerdefinierter Telemetrie wird unterstützt.

|                     | Mikrometer | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **Benutzerdefinierte Ereignisse**   |            |                     |  Ja    |
| **Benutzerdefinierte Metriken**  |  Ja       |                     |  Ja    |
| **Abhängigkeiten**    |            |                     |  Ja    |
| **Ausnahmen**      |            |  Ja                |  Ja    |
| **Seitenansichten**      |            |                     |  Ja    |
| **Anforderungen**        |            |                     |  Ja    |
| **Traces**          |            |  Ja                |  Ja    |

Wir planen derzeit kein SDK mit Application Insights 3.0.

Application Insights Java 3.0 lauscht bereits auf Telemetriedaten, die an das Application Insights Java 2.x SDK gesendet werden. Diese Funktionalität ist ein wichtiger Bestandteil der Upgrades für vorhandene 2.x-Benutzer und füllt eine wichtige Lücke in der Unterstützung benutzerdefinierter Telemetriedaten bis zur allgemeinen Verfügbarkeit der OpenTelemetry-API.

### <a name="send-custom-metrics-using-micrometer"></a>Senden benutzerdefinierter Metriken mithilfe von Micrometer

Fügen Sie der Anwendung Micrometer hinzu:

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Verwenden Sie die [globale Registrierung](https://micrometer.io/docs/concepts#_global_registry) von Micrometer, um eine Verbrauchseinheit zu erstellen:

```java
static final Counter counter = Metrics.counter("test_counter");
```

und zeichnen Sie damit Metriken auf:

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>Senden benutzerdefinierter Ablaufverfolgungen und Ausnahmen mit Ihrem bevorzugten Protokollierungsframework

Log4j, Logback und java.util.logging sind automatisch instrumentiert, und die Protokollierung dieser Protokollierungsframeworks wird automatisch als Telemetrie für Ablaufverfolgung und Ausnahmen erfasst.

Die Protokollierung wird standardmäßig nur gesammelt, wenn diese ab der Ebene INFO erfolgt.
Weitere Informationen zum Ändern dieser Ebene finden Sie in unter [Konfigurationsoptionen](./java-standalone-config.md#auto-collected-logging).

Wenn Sie benutzerdefinierte Dimensionen an Ihre Protokolle anfügen möchten, können Sie [Log4j 1 MDC](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html), [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) oder [Logback MDC](http://logback.qos.ch/manual/mdc.html) verwenden, damit Application Insights Java 3.0 diese MDC-Eigenschaften automatisch als benutzerdefinierte Dimensionen in Ihrer Telemetrie für Ablaufverfolgung und Ausnahmen erfasst.

### <a name="send-custom-telemetry-using-application-insights-java-2x-sdk"></a>Senden benutzerdefinierter Telemetriedaten mit dem Application Insights Java 2.x SDK

Fügen Sie `applicationinsights-core-2.6.0.jar` zu Ihrer Anwendung hinzu (alle 2.x-Versionen werden von Application Insights Java 3.0 unterstützt, aber es lohnt sich, die neueste Version zu verwenden, wenn Sie die Wahl haben):

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.0</version>
</dependency>
```

Erstellen Sie einen TelemetryClient:

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

und senden Sie damit benutzerdefinierte Telemetriedaten:

##### <a name="events"></a>Ereignisse

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>Metriken

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>Abhängigkeiten

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>Protokolle

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>Ausnahmen

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```
