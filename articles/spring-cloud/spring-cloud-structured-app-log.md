---
title: Strukturiertes Anwendungsprotokoll für Azure Spring Cloud | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie strukturierte Anwendungsprotokolldaten in Azure Spring Cloud generiert und erfasst werden.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: c5c35fe8a352a1bc3467e9512a7fcbc068375bfb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056846"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Strukturiertes Anwendungsprotokoll für Azure Spring Cloud

In diesem Artikel wird erläutert, wie strukturierte Anwendungsprotokolldaten in Azure Spring Cloud generiert und erfasst werden. Mit der richtigen Konfiguration bietet Azure Spring Cloud nützliche Anwendungsprotokollabfragen und -analysen über Log Analytics.

## <a name="log-schema-requirements"></a>Protokollschemaanforderungen
Um die Protokollabfrage zu verbessern, muss ein Anwendungsprotokoll im JSON-Format vorliegen und einem Schema entsprechen. Azure Spring Cloud verwendet dieses Schema, um Ihre Anwendung zu analysieren und in Log Analytics zu streamen. 

**JSON-Schemaanforderungen:**

| JSON-Schlüssel      | JSON-Werttyp|  Erforderlich | Spalte in Log Analytics| BESCHREIBUNG |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | Zeichenfolge      |     Ja   | AppTimestamp    | Zeitstempel im UTC-Format  |
| Protokollierungstool        | Zeichenfolge      |     Nein    | Protokollierungstool          | Protokollierungstool                   |
| Level         | Zeichenfolge      |     Nein    | CustomLevel     | log level                |
| Thread        | Zeichenfolge      |     Nein    | Thread          | Thread                   |
| message       | Zeichenfolge      |     Nein    | `Message`         | Protokollnachricht              |
| stackTrace    | Zeichenfolge      |     Nein    | StackTrace      | Ausnahme Stapelüberwachung    |
| exceptionClass| Zeichenfolge      |     Nein    | exceptionClass  | Name der Ausnahmeklasse     |
| mdc           | Geschachteltes JSON |     Nein    |                 | Zugeordneter Diagnosekontext|
| mdc.traceId   | Zeichenfolge      |     Nein    | TraceId         |Ablaufverfolgungs-ID für verteilte Ablaufverfolgung|
| mdc.spanId    | Zeichenfolge      |     Nein    | SpanId          |Spannen-ID für verteilte Ablaufverfolgung |
|               |             |           |                 |                          |

* Das Feld „timestamp“ ist erforderlich und sollte im UTC-Format vorliegen. Alle anderen Felder sind optional.
* „traceid“ und „spanid“ im Feld „mdc“ werden für die Ablaufverfolgungszwecke verwendet.
* Protokolliert jeden JSON-Datensatz in einer Zeile. 

**Beispiel für Protokolldatensatz** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Generieren eines schemakompatiblen JSON-Protokolls  
Für Spring-Anwendungen können Sie das erwartete JSON-Protokollformat mithilfe allgemeiner [Protokollierungsframeworks](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration) generieren, z. B. mit [logback](http://logback.qos.ch/) und [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Protokollieren mit logback 
Bei Verwendung von Spring Boot Starter wird standardmäßig logback verwendet. Verwenden Sie für logback-Apps [logstash-encoder](https://github.com/logstash/logstash-logback-encoder), um ein JSON-formatiertes Protokoll zu generieren. Diese Methode wird in Spring Boot, Version 2.1 und höher, unterstützt. 

Die Prozedur:

1. Fügen Sie eine logstash-Abhängigkeit in Ihrer Datei „Pom.xml“ hinzu. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Aktualisieren Sie die Konfigurationsdatei „logback.xml“, um das JSON-Format festzulegen.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Protokollieren mit log4j2 

Verwenden Sie für log4j2-Apps [json-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html), um das JSON-formatierte Protokoll zu generieren. Diese Methode wird in Spring Boot, Version 2.1 und höher, unterstützt.

Die Prozedur:

1. Schließen Sie `spring-boot-starter-logging` aus `spring-boot-starter` aus, und fügen Sie die Abhängigkeiten `spring-boot-starter-log4j2`, `log4j-layout-template-json` in der Datei „Pom.xml“ hinzu.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Bereiten Sie eine JSON-Layoutvorlageandatei „jsonTemplate.json“ in Ihrem Klassenpfad vor.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Verwenden Sie diese JSON-Layoutvorlage in Ihrer Konfigurationsdatei „log4j2.json“. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analysieren der Protokolle in Log Analytics

Nachdem Ihre Anwendung ordnungsgemäß eingerichtet wurde, wird das Anwendungskonsolenprotokoll an Log Analytics gestreamt. Die Struktur ermöglicht eine effiziente Abfrage in Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Überprüfen der Protokollstruktur in Log Analytics
Gehen Sie dazu wie folgt vor:
1. Navigieren Sie zur Dienstübersichtsseite Ihrer Dienstinstanz.
2. Klicken Sie im Abschnitt `Monitoring` auf den Eintrag `Logs`.
3. Führen Sie die folgende Abfrage aus.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Die Rückgabe von Anwendungsprotokollen wird in der folgenden Abbildung dargestellt:

![Anzeigen des JSON-Protokolls](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Anzeigen von Protokolleinträgen mit Fehlern

Um Protokolleinträge mit einem Fehler zu überprüfen, führen Sie die folgende Abfrage aus:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Verwenden Sie diese Abfrage, um Fehler zu finden, oder ändern Sie die Abfragebedingungen, um eine bestimmte Ausnahmeklasse oder Fehlercodes zu ermitteln. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Anzeigen von Protokolleinträgen für eine bestimmte traceId
Zum Überprüfen von Protokolleinträgen für eine bestimmte Ablaufverfolgungs-ID „trace_id“ führen Sie die folgende Abfrage aus:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur Protokollabfrage finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries).
