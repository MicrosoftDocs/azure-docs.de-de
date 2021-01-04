---
title: 'Azure Monitor Application Insights Java: Konfigurationsoptionen'
description: Konfigurationsoptionen für Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: e9240253b385a0b53868c5a8a79c751c2f7c09c0
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601083"
---
# <a name="configuration-options-for-azure-monitor-application-insights-java"></a>Konfigurationsoptionen für Azure Monitor Application Insights Java

> [!WARNING]
> **Bei einem Upgrade von Vorschauversion 3.0**
>
> Lesen Sie sich unten sorgfältig alle Konfigurationsoptionen durch, da neben dem Dateinamen, der nun komplett in Kleinbuchstaben geschrieben wird, auch die JSON-Struktur vollständig geändert wurde.

## <a name="connection-string-and-role-name"></a>Verbindungszeichenfolge und Rollenname

Die Verbindungszeichenfolge und der Rollenname sind die gängigsten Einstellungen, die für den Einstieg erforderlich sind:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Die Verbindungszeichenfolge ist erforderlich, und der Rollenname ist immer dann relevant, wenn Sie Daten unterschiedlicher Anwendungen an dieselbe Application Insights-Ressource senden.

Weitere Informationen und zusätzliche Konfigurationsoptionen finden Sie unten.

## <a name="configuration-file-path"></a>Pfad der Konfigurationsdatei

Standardmäßig erwartet Application Insights Java 3.0 eine Konfigurationsdatei mit dem Namen `applicationinsights.json`, die sich im gleichen Verzeichnis wie `applicationinsights-agent-3.0.0.jar` befindet.

Verwenden Sie eines der folgenden Elemente, um einen eigenen Pfad für Ihre Konfigurationsdatei anzugeben:

* Umgebungsvariable `APPLICATIONINSIGHTS_CONFIGURATION_FILE` oder
* Java-Systemeigenschaft `applicationinsights.configuration.file`

Wenn Sie einen relativen Pfad angeben, wird dieser relativ zum Verzeichnis von `applicationinsights-agent-3.0.0.jar` aufgelöst.

## <a name="connection-string"></a>Verbindungszeichenfolge

Eine Verbindungszeichenfolge ist erforderlich. Die Verbindungszeichenfolge finden Sie in der Application Insights-Ressource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights-Verbindungszeichenfolge":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Sie können auch die Verbindungszeichenfolge mit der Umgebungsvariablen `APPLICATIONINSIGHTS_CONNECTION_STRING` festlegen.

Wird die Verbindungszeichenfolge nicht festgelegt, wird der Java-Agent deaktiviert.

## <a name="cloud-role-name"></a>Cloudrollenname

Der Name der Cloudrolle wird verwendet, um die Komponente in der Anwendungsübersicht zu beschriften.

Wenn Sie den Cloudrollennamen festlegen möchten:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Falls der Cloudrollenname nicht festgelegt ist, wird der Name der Application Insights-Ressource zur Beschriftung der Komponente in der Anwendungsübersicht verwendet.

Sie können den Namen der Cloudrolle auch mithilfe der Umgebungsvariablen `APPLICATIONINSIGHTS_ROLE_NAME` festlegen.

## <a name="cloud-role-instance"></a>Cloudrolleninstanz

Der Name der Cloudrolleninstanz entspricht standardmäßig dem Computernamen.

Wenn Sie für die Cloudrolleninstanz einen anderen Namen festlegen möchten:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Sie können den Namen der Cloudrolleninstanz auch mithilfe der Umgebungsvariablen `APPLICATIONINSIGHTS_ROLE_INSTANCE` festlegen.

## <a name="sampling"></a>Stichproben

Stichproben sind nützlich, wenn Sie die Kosten senken müssen.
Stichproben werden als Funktion für die Vorgangs-ID (auch als Nachverfolgungs-ID bezeichnet) ausgeführt, sodass dieselbe Vorgangs-ID immer zur selben Stichprobenentscheidung führt. So wird sichergestellt, dass Stichproben nicht nur für Teile einer verteilten Transaktion erfasst werden, während andere Teile ausgelassen werden.

Wenn Sie beispielsweise die Stichprobenentnahme auf 10 % festlegen, werden nur 10 % Ihrer Transaktionen angezeigt, aber in diesen 10 % sind vollständige End-to-End-Transaktionsdetails enthalten.

Hier sehen Sie ein Beispiel für das Festlegen der Stichprobenentnahme, um ungefähr **1/3 aller Transaktionen** zu erfassen. Stellen Sie sicher, dass Sie eine Stichprobenhäufigkeit festlegen, die sich für Ihren Anwendungsfall eignet:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Sie können den Prozentsatz für die Stichprobenentnahme auch mithilfe der Umgebungsvariablen `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` festlegen.

> [!NOTE]
> Für den Prozentsatz der Stichprobenerstellung wählen Sie einen Prozentsatz, der sich als Bruch darstellen lässt (100/N, wobei N eine Ganzzahl ist). Andere Werte werden bei der Stichprobenerstellung gegenwärtig nicht unterstützt.

## <a name="jmx-metrics"></a>JMX-Metriken

Verwenden Sie folgenden JSON-Code, wenn Sie einige zusätzliche JMX-Metriken erfassen möchten:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` entspricht dem Metriknamen, der dieser JMX-Metrik zugewiesen wird (ein beliebiger Name kann verwendet werden).

`objectName` entspricht dem [Objektnamen](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) des verwalteten Beans (MBean) von JMX, das Sie erfassen möchten.

`attribute` entspricht dem Attributnamen innerhalb des verwalteten Beans (MBean) von JMX, das Sie erfassen möchten.

Numerische und boolesche JMX-Metrikwerte werden unterstützt. Boolesche JMX-Metriken werden `0` für „false“ und `1` für „true“ zugeordnet.

[//]: # "HINWEIS: APPLICATIONINSIGHTS_JMX_METRICS ist hier nicht dokumentiert."
[//]: # "In Umgebungsvariable eingebettete JSON-Daten sind verwirrend und sollten nur für das Szenario zum Anfügen ohne Code dokumentiert werden."

## <a name="custom-dimensions"></a>Benutzerdefinierte Dimensionen

Verwenden Sie den folgenden JSON-Code, wenn Sie benutzerdefinierte Dimensionen zu all Ihren Telemetriedaten hinzufügen möchten:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` kann zum Lesen des Werts aus der angegebenen Umgebungsvariable beim Start verwendet werden.

## <a name="telemetry-processors-preview"></a>Telemetrieprozessoren (Vorschauversion)

Dieses Feature befindet sich in der Vorschauphase.

Mit diesem Feature können Sie Regeln konfigurieren, die auf Anforderungs-, Abhängigkeits- und Überwachungstelemetriedaten angewendet werden. Zum Beispiel:
 * Maskieren vertraulicher Daten
 * Bedingtes Hinzufügen benutzerdefinierter Dimensionen
 * Aktualisieren des Telemetrienamens für Aggregation und Anzeige

Weitere Informationen finden Sie in der [Dokumentation zu Telemetrieprozessoren](./java-standalone-telemetry-processors.md).

## <a name="auto-collected-logging"></a>Automatisch gesammelte Protokolle

„Log4j“, „Logback“ und „java.util.logging“ werden automatisch instrumentiert, und die Protokollierung dieser Protokollierungsframeworks wird automatisch erfasst.

Die Protokollierung wird standardmäßig nur gesammelt, wenn diese auf der Ebene `INFO` oder höher erfolgt.

Verwenden Sie den folgenden JSON-Code, wenn Sie diese Sammlungsebene ändern möchten:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Sie können den Schwellenwert auch mithilfe der Umgebungsvariable `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` festlegen.

Nachfolgend werden die gültigen `level`-Werte, die Sie in der Datei `applicationinsights.json` angeben können, und deren Zuordnung zu den Protokolliergraden in verschiedenen Protokollierungsframeworks aufgeführt:

| Level             | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| ERROR (oder SEVERE) | ERROR  | ERROR   | SEVERE  |
| WARN (oder WARNING) | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (oder FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (oder FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatisch gesammelte Micrometer-Metriken (einschließlich Spring Boot Actuator-Metriken)

Wenn Ihre Anwendung [Micrometer](https://micrometer.io) verwendet, werden Metriken, die an die globale Micrometer-Registrierung gesendet werden, automatisch erfasst.

Wenn Ihre Anwendung auch [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html) verwendet, werden mit Spring Boot Actuator konfigurierte Metriken ebenfalls automatisch erfasst.

So deaktivieren Sie die automatische Erfassung von Micrometer-Metriken (einschließlich Spring Boot Actuator-Metriken):

> [!NOTE]
> Benutzerdefinierte Metriken werden separat in Rechnung gestellt und können zu zusätzlichen Kosten führen. Es wird dringend empfohlen, dass Sie die ausführlichen [Preisinformationen](https://azure.microsoft.com/pricing/details/monitor/) überprüfen. Fügen Sie die folgende Konfiguration zu ihrer Konfigurationsdatei hinzu, um die Micrometer- und Spring Actuator-Metriken zu deaktivieren.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Application Insights Java 3.0 sendet standardmäßig alle 15 Minuten eine Heartbeatmetrik. Wenn Sie die Taktmetrik zum Auslösen von Warnungen verwenden, können Sie die Frequenz für den Takt erhöhen:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Es ist nicht möglich, die Häufigkeit der Heartbeats zu verringern, weil die Heartbeatdaten auch zum Überwachen der Application Insights-Nutzung verwendet werden.

## <a name="http-proxy"></a>HTTP-Proxy

Wenn sich Ihre Anwendung hinter einer Firewall befindet und nicht direkt mit Application Insights verbunden werden kann (siehe [Von Application Insights verwendete IP-Adressen](./ip-addresses.md)), können Sie Application Insights Java 3.0 zur Verwendung eines HTTP-Proxys konfigurieren:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

[//]: # "HINWEIS: Die Unterstützung von OpenTelemetry wird nicht beworben, bis Version 0.10.0 unterstützt wird, die gravierende Breaking Changes gegenüber Version 0.9.0 aufweist."

[//]: # "## Unterstützung von Releases der OpenTelemetry-API vor Version 1.0"

[//]: # "Die Unterstützung von Versionen vor 1.0 der OpenTelemetry-API ist aktivierbar, da die OpenTelemetry-API noch nicht stabil ist,"
[//]: # "daher unterstützt jede Version des Agents nur spezifische Versionen der OpenTelemetry-API, die unter Version 1.0 liegen."
[//]: # "(Diese Einschränkung gilt nicht mehr, sobald Version 1.0 der OpenTelemetry-API veröffentlicht wird.)"

[//]: # "```json"
[//]: # "{"
[//]: # "  \"preview\": {"
[//]: # "    \"openTelemetryApiSupport\": true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Selbstdiagnose

„Selbstdiagnose“ bezeichnet die interne Protokollierung von Application Insights Java 3.0.

Diese Funktionalität kann hilfreich sein, um Probleme mit Application Insights selbst zu identifizieren und zu diagnostizieren.

Application Insights Java 3.0 protokolliert standardmäßig auf Ebene `INFO` in der Datei `applicationinsights.log` und der Konsole entsprechend der folgenden Konfiguration:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` kann `file`, `console` oder `file+console` sein.

`level` kann `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG` oder `TRACE` sein.

`path` kann ein absoluter oder ein relativer Pfad sein. Relative Pfade werden anhand des Verzeichnisses aufgelöst, in dem sich `applicationinsights-agent-3.0.0.jar` befindet.

`maxSizeMb` entspricht der maximalen Größe der Protokolldatei, bevor ein Rollover durchgeführt wird.

`maxHistory` entspricht der Anzahl der beibehaltenen Protokolldateien, für die ein Rollover durchgeführt wurde (zusätzlich zur aktuellen Protokolldatei).

## <a name="an-example"></a>Beispiel

Hierbei handelt es sich lediglich um ein Beispiel zur Veranschaulichung einer Konfigurationsdatei mit mehreren Komponenten.
Konfigurieren Sie spezifische Optionen basierend auf Ihren Anforderungen.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```