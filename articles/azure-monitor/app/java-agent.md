---
title: Leistungsüberwachung für Java-Web-Apps in Azure Application Insights | Microsoft-Dokumentation
description: Erweiterte Leistungs- und Nutzungsüberwachung Ihrer Java-Website mit Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900607"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Überwachen von Abhängigkeiten, abgefangene Ausnahmen und Methodenausführungszeiten in Java-Web-Apps


Wenn Sie [Ihre Java-Web-App mit Application Insights instrumentiert haben][java], können Sie den Java-Agent ohne Codeänderungen verwenden, um detailliertere Informationen zu erhalten:

* **Abhängigkeiten**: Daten über Aufrufe der Anwendung an andere Komponenten, einschließlich:
  * **REST-Aufrufe** über „HttpClient“, „OkHttp“ und „RestTemplate“ (Spring) werden erfasst.
  * **Redis-Aufrufe** über den Jedis-Client werden erfasst.
  * **[JDBC-Aufrufe:](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** MySQL-, SQL Server- and Oracle DB-Befehle werden automatisch erfasst. Für MySQL gilt: Wenn der Aufruf länger als zehn Sekunden dauert, wird der Abfrageplan gemeldet.
* **Abgefangene Ausnahmen**: Informationen zu Ausnahmen, die von Ihrem Code behandelt werden.
* **Methodenausführungszeit**: Informationen zur Ausführungsdauer bestimmter Methoden.

Um den Java-Agent zu verwenden, installieren Sie ihn auf Ihrem Server. Ihre Web-Apps müssen mit dem [Application Insights Java SDK][java] instrumentiert werden. 

## <a name="install-the-application-insights-agent-for-java"></a>Installieren des Application Insights-Agents für Java
1. Laden Sie auf dem Computer, auf dem Ihr Java-Server ausgeführt wird, [den Agent herunter](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Die Version des heruntergeladenen Agents muss der Version der Core- und Webpakete des Application Insights Java SDKs entsprechen.
2. Bearbeiten Sie das Startskript des Anwendungsservers, und fügen Sie die folgende JVM hinzu:
   
    `javaagent:`*Vollständiger Pfad der JAR-Datei des Agents*
   
    Beispielsweise in Tomcat auf einem Linux-Computer:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Starten Sie den Anwendungsserver neu.

## <a name="configure-the-agent"></a>Konfigurieren des Agents
Erstellen Sie eine Datei mit dem Namen `AI-Agent.xml` , und speichern Sie sie im selben Ordner wie die JAR-Datei des Agents.

Legen Sie den Inhalt der XML-Datei fest. Bearbeiten Sie das folgende Beispiel, um Features Ihrer Wahl aufzunehmen oder nicht.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />
           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Sie müssen für einzelne Methoden die Berichtausnahmen und Methodenzeiten aktivieren.

`reportExecutionTime` ist standardmäßig „true“, `reportCaughtExceptions` ist standardmäßig „false“.

## <a name="additional-config-spring-boot"></a>Zusätzliche Konfiguration (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Führen Sie für Azure App Services die folgenden Schritte aus:

* Wählen Sie „Einstellungen“ > „Anwendungseinstellungen“ aus.
* Fügen Sie unter „App-Einstellungen“ ein neues Schlüssel-Wert-Paar hinzu:

Schlüssel: `JAVA_OPTS` Wert: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Die neueste Version des Java-Agents finden Sie unter den Releases [hier](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

Der Agent muss als Ressource in Ihrem Projekt enthalten sein, sodass er sich letztendlich im Verzeichnis „D:/home/site/wwwroot/“ befindet. Sie können sich vergewissern, dass der Agent im richtigen App Service-Verzeichnis enthalten ist, indem Sie zu **Entwicklungstools** > **Erweiterte Tools** > **Debugging-Konsole** wechseln und den Inhalt des Siteverzeichnisses prüfen.    

* Speichern Sie die Einstellungen, und starten Sie die App neu. (Diese Schritte gelten nur für App Services unter Windows.)

> [!NOTE]
> „AI-Agent.xml“ und die Agent-JAR-Datei sollten sich im selben Ordner befinden. Sie werden häufig zusammen im `/resources`-Ordner des Projekts platziert.  

### <a name="spring-rest-template"></a>Spring-REST-Vorlage

Damit Application Insights HTTP-Aufrufe über die Spring-REST-Vorlage erfolgreich instrumentieren kann, muss der Apache-HTTP-Client verwendet werden. Standardmäßig ist die REST-Vorlage von Spring nicht für die Verwendung des Apache-HTTP-Clients konfiguriert. Durch Angabe von [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) im Konstruktor einer Spring-REST-Vorlage wird Apache-HTTP verwendet.

Hier ist ein Beispiel dazu mit Spring-Beans. Dies ist ein sehr einfaches Beispiel, in dem die Standardeinstellungen der Factoryklasse verwendet werden.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Aktivieren der verteilten W3C-Ablaufverfolgung

Fügen Sie der Datei „AI-Agent.xml“ Folgendes hinzu:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> Der Abwärtskompatibilitätsmodus ist standardmäßig aktiviert, und der Parameter „enableW3CBackCompat“ ist optional und sollte nur verwendet werden, wenn Sie diese Funktion deaktivieren möchten. 

Idealerweise wäre dies der Fall, wenn alle Dienste auf neuere Versionen der SDKs aktualisiert wurden, die das W3C-Protokoll unterstützen. Es wird dringend empfohlen, so bald wie möglich auf neuere Versionen der SDKs mit W3C-Unterstützung umzustellen.

Stellen Sie sicher, dass die **Konfigurationen für [eingehende](correlation.md#w3c-distributed-tracing) und ausgehende Vorgänge (Agent)** exakt gleich sind.

## <a name="view-the-data"></a>Anzeigen der Daten
In der Application Insights-Ressource werden aggregierte Remoteabhängigkeiten und Methodenausführungszeiten [auf der Kachel „Leistung“][metrics] angezeigt.

Um nach den einzelnen Instanzen der Abhängigkeits-, Ausnahmen- und Methodenberichte zu suchen, öffnen Sie die [Suche][diagnostic].

[Diagnostizieren von Problemen mit Abhängigkeiten – weitere Informationen](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Fragen? Probleme?
* Sie sehen keine Daten? [Festlegen von Firewallausnahmen](../../azure-monitor/app/ip-addresses.md)
* [Problembehandlung für Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
