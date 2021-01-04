---
title: 'Schnellstart: Java-Web-App-Analyse mit Azure Application Insights'
description: 'Überwachung der Anwendungsleistung für Java-Web-Apps mithilfe von Application Insights. '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 6bdad71f0b36995abdeb3b1edb87cbef32df8b67
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96919429"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Schnellstart: Erste Schritte mit Application Insights in einem Java-Webprojekt


> [!CAUTION]
> Ab November 2020 sollten Sie für die Überwachung von Java-Anwendungen die automatische Instrumentierung mithilfe des Azure Monitor Application Insights Java 3.0-Agents verwenden. Weitere Informationen zu ersten Schritten finden Sie unter [Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights](./java-in-process-agent.md).

In dieser Schnellstartanleitung verwenden Sie das Application Insights SDK, um Anforderungen zu instrumentieren, Abhängigkeiten zu verfolgen und Leistungsindikatoren zu sammeln, Leistungsprobleme und Ausnahmen zu diagnostizieren und Code zu schreiben, um die Aktivitäten der Benutzer Ihrer App zu verfolgen.

Application Insights ist ein erweiterbarer Analysedienst für Webentwickler, der Ihnen dabei hilft, die Leistung und die Verwendung der Liveanwendung zu verstehen. Application Insights unterstützt Java-Apps, die unter Linux, Unix oder Windows ausgeführt werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Eine funktionierende Java-Anwendung.

## <a name="get-an-application-insights-instrumentation-key"></a>Abrufen eines Application Insights-Instrumentationsschlüssels

> [!IMPORTANT]
> Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die [Verbindungszeichenfolge](./sdk-connection-string.md?tabs=java) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Erstellen Sie im Azure-Portal eine Application Insights-Ressource. Legen Sie den Anwendungstyp auf "Java-Webanwendung" fest.

3. Suchen Sie den Instrumentationsschlüssel der neuen Ressource. Dieser Schlüssel muss in Kürze in das Codeprojekt eingefügt werden.

    ![Klicken Sie in der Übersicht über neue Ressourcen auf "Eigenschaften", und kopieren Sie den Instrumentationsschlüssel](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>Hinzufügen des Application Insights SDK für Java zu Ihrem Projekt

*Wählen Sie Ihren Projekttyp aus.*

# <a name="maven"></a>[Maven](#tab/maven)

Wenn Ihr Projekt bereits für die Verwendung von Maven für den Buildprozess eingerichtet ist, fügen Sie Ihrer Datei *pom.xml* den folgenden Code hinzu.

Aktualisieren Sie dann die Projektabhängigkeiten, damit die Binärdateien heruntergeladen werden.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Wenn Ihr Projekt bereits für die Verwendung von Gradle für den Buildprozess eingerichtet ist, fügen Sie Ihrer Datei *build.gradle* den folgenden Code hinzu.

Aktualisieren Sie dann die Projektabhängigkeiten, damit die Binärdateien heruntergeladen werden.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Andere Typen](#tab/other)

Laden Sie die [neueste Version](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) herunter, kopieren Sie die erforderlichen Dateien in Ihr Projekt, und ersetzen Sie dabei alle früheren Versionen.

---

### <a name="questions"></a>Fragen
* *Welche Beziehung besteht zwischen den Komponenten `-web-auto`, `-web` und `-core`?*
  * `applicationinsights-web-auto` bietet Ihnen Metriken, die die Anzahl der HTTP-Servletanforderungen und die Antwortzeiten nachverfolgen, indem sie den Application Insights-Servletfilter zur Runtime automatisch registrieren.
  * `applicationinsights-web` bietet Ihnen auch Metriken, die die Anzahl der HTTP-Servletanforderungen und die Antwortzeiten nachverfolgen, erfordert jedoch manuelle Registrierung der Application Insights-Servletfilter in Ihrer Anwendung.
  * `applicationinsights-core` bietet Ihnen lediglich die reine API, beispielsweise wenn Ihre Anwendung nicht auf Servlets basiert.
  
* *Wie aktualisiere ich das SDK auf die neueste Version?*
  * Wenn Sie Gradle oder Maven verwenden...
    * Aktualisieren Sie Ihre Builddatei, um die neueste Version anzugeben.
  * Wenn Sie Abhängigkeiten manuell verwalten...
    * Laden Sie das aktuelle [Application Insights-SDK für Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) herunter, und ersetzen Sie die alte Version. Änderungen werden in den [SDK-Versionshinweisen](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)beschrieben.

## <a name="add-an-applicationinsightsxml-file"></a>Hinzufügen einer Datei vom Typ *ApplicationInsights.xml*
Fügen Sie dem Ressourcenordner in Ihrem Projekt die Datei *ApplicationInsights.xml* hinzu, oder stellen Sie sicher, dass sie dem Bereitstellungsklassenpfad Ihres Projekts hinzugefügt wird. Kopieren Sie den folgenden XML-Code in die Datei.

Ersetzen Sie den Instrumentationsschlüssel durch den, den Sie aus dem Azure-Portal abgerufen haben.

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

Die Konfigurationsdatei kann sich optional an einem beliebigen Speicherort befinden, auf den Ihre Anwendung Zugriff hat.  Die Systemeigenschaft `-Dapplicationinsights.configurationDirectory` gibt das Verzeichnis an, in dem sich die Datei *ApplicationInsights.xml* befindet. Eine Konfigurationsdatei unter `E:\myconfigs\appinsights\ApplicationInsights.xml` wird beispielsweise mit der Eigenschaft `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` konfiguriert.

* Der Instrumentationsschlüssel wird zusammen mit jedem Telemetrieelement übermittelt und weist Application Insights an, ihn in Ihrer Ressource anzuzeigen.
* Die Komponente "HTTP-Anforderung" ist optional. Sie sendet automatisch Telemetriedaten zu Anforderungen und Antwortzeiten zum Portal.
* Die Ereigniskorrelation ist eine Ergänzung der HTTP-Anforderungskomponente. Sie weist jeder vom Server empfangenen Anforderung einen Bezeichner zu. Anschließend weist sie diesen Bezeichner jedem Telemetrieelement als die Eigenschaft „Operation.Id“ zu. Diese Eigenschaft ermöglicht das Korrelieren der jeder Anforderung zugeordneten Telemetriedaten, indem in [Diagnosesuche][diagnostic]ein Filter festgelegt wird.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Alternative Methoden zum Festlegen des Instrumentationsschlüssels
Das Application Insights SDK sucht in dieser Reihenfolge nach dem Schlüssel:

1. Systemeigenschaft: -DAPPINSIGHTS_INSTRUMENTATIONKEY=Ihr_Instrumentierungsschlüssel
2. Umgebungsvariable: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurationsdatei: *ApplicationInsights.xml*

Sie können dies auch [per Code festlegen](./api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Hinzufügen des Agent

[Installieren Sie den Java-Agent](java-agent.md) zum Erfassen ausgehender HTTP-Aufrufe, für JDBC-Abfragen, Anwendungsprotokollierung und zur besseren Vorgangsbenennung.

## <a name="run-your-application"></a>Ausführen der Anwendung
Führen Sie sie entweder im Debugmodus auf dem Entwicklungscomputer aus, oder veröffentlichen Sie sie auf Ihrem Server.

## <a name="view-your-telemetry-in-application-insights"></a>Anzeigen Ihrer Telemetriedaten in Application Insights
Kehren Sie zur Application Insights-Ressource im [Microsoft Azure-Portal](https://portal.azure.com)zurück.

HTTP-Anforderungsdaten werden auf dem Übersichtsblatt angezeigt. (Wenn sie nicht vorhanden sind, warten Sie einige Sekunden, und klicken Sie dann auf "Aktualisieren".)

![Screenshot der Übersicht über die Beispieldaten](./media/java-get-started/overview-graphs.png)

[Weitere Informationen zu Metriken.][metrics]

Klicken Sie sich durch ein beliebiges Diagramm, um ausführliche aggregierte Metriken anzuzeigen.

![Bereich für Application Insights-Fehler mit Diagrammen](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Instanzdaten
Klicken Sie sich durch einen bestimmten Anforderungstyp, um einzelne Instanzen anzuzeigen.

![Detailinformationen in einer spezifischen Beispielansicht](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics: Leistungsfähige Abfragesprache
Wenn sich mehr Daten ansammeln, können Sie Abfragen sowohl zum Aggregieren von Daten als auch zum Ermitteln einzelner Instanzen ausführen.  [Analytics](../log-query/log-query-overview.md) ist ein leistungsfähiges Tool zum Nachvollziehen der Leistung und Nutzung sowie für Diagnosezwecke.

![Analytics-Beispiel](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Installieren der App auf dem Server
Jetzt veröffentlichen Sie Ihre App auf dem Server, erlauben deren Benutzung und sehen sich an, wie die Telemetrie im Portal angezeigt wird.

* Stellen Sie sicher, dass die Firewall der Anwendung das Senden von Telemetrie an die folgenden Ports erlaubt:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Wenn ausgehender Datenverkehr über eine Firewall geleitet werden muss, legen Sie Systemeigenschaften wie `http.proxyHost` und `http.proxyPort` fest.

* Installieren Sie auf Windows-Servern:

  * [Microsoft Visual C++ Redistributable](https://www.microsoft.com/download/details.aspx?id=40784)

    (Diese Komponente aktiviert Leistungsindikatoren.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AKS, VM-Konfiguration

Der beste und einfachste Ansatz zur Überwachung Ihrer Anwendungen, die bei einem der Azure-Ressourcenanbieter laufen, ist die Verwendung der automatischen Instrumentierung von Application Insights über den [Java 3.0-Agent](./java-in-process-agent.md).


## <a name="exceptions-and-request-failures"></a>Ausnahmen und Anforderungsfehler
Unbehandelte Ausnahmen und Anforderungsfehler werden automatisch vom Application Insights-Webfilter erfasst.

Zum Sammeln von Daten zu anderen Ausnahmen können Sie [Aufrufe von trackException() in Ihren Code einfügen][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Überwachen von Methodenaufrufen und externen Abhängigkeiten
[Installieren Sie den Java-Agent](java-agent.md) , um die angegebenen internen Methoden und Aufrufe über JDBC mit Zeitdaten zu protokollieren.

Außerdem für die automatische Vorgangsbenennung.

## <a name="w3c-distributed-tracing"></a>W3C – verteilte Ablaufverfolgung

Das Application Insights Java SDK unterstützt jetzt die [verteilte Ablaufverfolgung W3C](https://w3c.github.io/trace-context/).

Die SDK-Eingangskonfiguration wird ausführlicher in unserem Artikel zu [Korrelation](correlation.md) behandelt.

Die SDK-Ausgangskonfiguration wird in der Datei [AI-Agent.xml](java-agent.md) definiert.

## <a name="performance-counters"></a>Leistungsindikatoren
Unter **Untersuchen** > **Metriken** finden Sie eine Reihe von Leistungsindikatoren.

![Screenshot des Bereichs für Metriken mit ausgewählter Option für die Verarbeitung privater Bytes](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Anpassen der Erfassung von Leistungsindikatoren
Um die Erfassung der Standardgruppe von Leistungsindikatoren zu deaktivieren, fügen Sie unter dem Stammknoten der Datei *ApplicationInsights.xml* den folgenden Code hinzu:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Erfassen weiterer Leistungsindikatoren
Sie können weitere Leistungsindikatoren angeben, die erfasst werden sollen.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-Leistungsindikatoren (von der Java Virtual Machine bereitgestellt)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – Der im Application Insights-Portal angezeigte Name.
* `objectName` – Der JMX-Objektname.
* `attribute` – Das Attribut des abzurufenden JMX-Objektnamens
* `type` (optional) – Der Typ des Attributs des JMX-Objekts:
  * Standard: ein einfacher Typ wie "int" oder "long".
  * `composite`: Die Leistungsindikatordaten haben das Format 'Attribut.Daten'.
  * `tabular`: Die Leistungsindikatordaten haben das Format einer Tabellenzeile.

#### <a name="windows-performance-counters"></a>Windows-Leistungsindikatoren
Jeder [Windows-Leistungsindikator](/windows/win32/perfctrs/performance-counters-portal) gehört zu einer Kategorie (genauso wie ein Feld zu einer Klasse gehört). Kategorien können entweder global sein oder nummerierte oder benannte Instanzen haben.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Der im Application Insights-Portal angezeigte Name
* categoryName – Die Leistungsindikatorkategorie (Leistungsobjekt), der dieser Leistungsindikator zugeordnet ist
* counterName – Der Name des Leistungsindikators
* instanceName – Der Name der Instanz der Leistungsindikatorkategorie oder eine leere Zeichenfolge (""), wenn die Kategorie eine einzelne Instanz enthält. Wenn "categoryName" auf "Process" festgelegt ist und der Leistungsindikator, den Sie erfassen möchten, aus dem aktuellen JVM-Prozess stammt, in dem Ihre Anwendung ausgeführt wird, geben Sie `"__SELF__"`an.

### <a name="unix-performance-counters"></a>Unix-Leistungsindikatoren
* [Installieren Sie collectd mit dem Application Insights-Plug-In](java-collectd.md) , um eine Vielzahl von System- und Netzwerkdaten abzurufen.

## <a name="get-user-and-session-data"></a>Abrufen von Benutzer- und Sitzungsdaten
Sie senden also Telemetriedaten vom Webserver. Um jetzt eine Rundum-Ansicht Ihrer Anwendung zu erhalten, können Sie weitere Überwachungsfunktionen hinzufügen:

* [Fügen Sie Ihren Webseiten Telemetrie hinzu][usage] , um Seitenaufrufe und Benutzermetriken zu überwachen.
* [Richten Sie Webtests ein][availability] , um sicherzustellen, dass die Anwendung online und reaktionsfähig bleibt.

## <a name="send-your-own-telemetry"></a>Senden eigener Telemetriedaten
Nachdem Sie das SDK installiert haben, können Sie die API verwenden, um eigene Telemetriedaten senden.

* [Verfolgen Sie benutzerdefinierte Ereignisse und Metriken nach][api], um zu erfahren, wie Benutzer Ihre Anwendung nutzen.
* [Durchsuchen Sie Ereignisse und Protokolle][diagnostic] , um Probleme besser zu diagnostizieren.

## <a name="availability-web-tests"></a>Verfügbarkeitswebtests
Application Insights kann Ihre Website in regelmäßigen Abständen testen, um zu überprüfen, ob sie betriebsbereit ist und gut reagiert.

[Erfahren Sie mehr über das Einrichten von Verfügbarkeitswebtests.][availability]

## <a name="questions-problems"></a>Fragen? Probleme?
[Problembehandlung für Java](java-troubleshoot.md)

## <a name="next-steps"></a>Nächste Schritte
* [Überwachen von Abhängigkeitsaufrufen](java-agent.md)
* [Überwachen von Unix-Leistungsindikatoren](java-collectd.md)
* Richten Sie die [Überwachung für Ihre Webseiten ein](javascript.md), um Seitenladezeiten, AJAX-Aufrufe und Browserausnahmen zu überwachen.
* Schreiben Sie [benutzerdefinierte Telemetriedaten](./api-custom-events-metrics.md), um die Nutzung im Browser oder auf dem Server nachzuverfolgen.
* Verwenden Sie [Analytics](../log-query/log-query-overview.md) für leistungsfähige Abfragen über Telemetriedaten in Ihrer App.
* Weitere Informationen finden Sie im Artikel [Azure für Java-Entwickler](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md
[usage]: javascript.md