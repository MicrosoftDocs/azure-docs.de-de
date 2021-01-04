---
title: Analysieren von Protokollen und Metriken in Azure Spring Cloud | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Diagnosedaten in Azure Spring Cloud analysieren.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 11f5fcd74b228fa2d57658f5c268e3bebc3c7e93
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499525"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Mithilfe der Diagnosefunktionen von Azure Spring Cloud können Sie Protokolle und Metriken mit jedem der folgenden Dienste analysieren:

* Verwenden Sie Azure Log Analytics. Dabei werden Daten in Azure Storage geschrieben. Beim Exportieren von Protokollen in Log Analytics tritt eine Verzögerung auf.
* Speichern Sie Protokolle zur Überwachung oder manuellen Überprüfung in einem Speicherkonto. Sie können die Vermerkdauer (in Tagen) angeben.
* Sie können Protokolle zur Erfassung durch einen Drittanbieterdienst oder durch eine benutzerdefinierte Analyselösung an Event Hubs streamen.

Wählen Sie die Protokollkategorie und die Metrikkategorie aus, die Sie überwachen möchten.

> [!TIP]
> Möchten Sie einfach nur Ihre Protokolle streamen? Sehen Sie sich diesen [Azure CLI-Befehl](/cli/azure/ext/spring-cloud/spring-cloud/app?preserve-view=true&view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs) an.

## <a name="logs"></a>Protokolle

|Log | BESCHREIBUNG |
|----|----|
| **ApplicationConsole** | Konsolenprotokoll aller Kundenanwendungen. |
| **SystemLogs** | Zurzeit befinden sich nur [Spring Cloud-Konfigurationsserver](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) in dieser Kategorie. |

## <a name="metrics"></a>Metriken

Eine vollständige Liste der Metriken finden Sie unter [Spring Cloud-Metriken](./spring-cloud-concept-metrics.md#user-metrics-options).

Zum Einstieg aktivieren Sie einen dieser Dienste, um die Daten empfangen zu können. Weitere Informationen zum Konfigurieren von Log Analytics finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).

## <a name="configure-diagnostics-settings"></a>Konfigurieren von Diagnoseeinstellungen

1. Wechseln Sie im Azure-Portal zu Ihrer Azure Spring Cloud-Instanz.
1. Wählen Sie die Option **Diagnoseeinstellungen** aus, und wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.
1. Geben Sie einen Namen für die Einstellung ein, und wählen Sie dann aus, wohin die Protokolle gesendet werden sollen. Sie können eine beliebige Kombination der folgenden drei Optionen auswählen:
    * **In einem Speicherkonto archivieren**
    * **An einen Event Hub streamen**
    * **An Log Analytics senden**

1. Wählen Sie die zu überwachende Protokoll- und Metrikkategorie aus, und geben Sie dann die Vermerkdauer (in Tagen) an. Die Vermerkdauer gilt nur für das Speicherkonto.
1. Wählen Sie **Speichern** aus.

> [!NOTE]
> 1. Zwischen der Ausgabe der Protokolle oder Metriken und der Anzeige in Ihrem Speicherkonto, Ihrem Event Hub oder in Log Analytics kann eine Verzögerung von bis zu 15 Minuten liegen.
> 1. Wenn die Azure Spring Cloud-Instanz gelöscht oder verschoben wurde, wird der Vorgang nicht an die **Diagnoseeinstellungen**-Ressourcen übergeben. Die **Diagnoseeinstellungen**-Ressourcen müssen manuell gelöscht werden, bevor der Vorgang für das übergeordnete Element (die Azure Spring Cloud-Instanz) ausgeführt werden kann. Wenn andernfalls eine neue Azure Spring Cloud-Instanz mit derselben Ressourcen-ID wie die gelöschte Instanz bereitgestellt oder die Azure Spring Cloud-Instanz zurück verschoben wird, erweitern die vorherigen **Diagnoseeinstellungen**-Ressourcen diese weiterhin.

## <a name="view-the-logs-and-metrics"></a>Anzeigen der Protokolle und Metriken
Es gibt verschiedene Methoden zum Anzeigen von Protokollen und Metriken, wie in den folgenden Abschnitten beschrieben.

### <a name="use-the-logs-blade"></a>Verwenden des Blatts „Protokolle“

1. Wechseln Sie im Azure-Portal zu Ihrer Azure Spring Cloud-Instanz.
1. Um den Bereich **Protokollsuche** zu öffnen, wählen Sie **Protokolle** aus.
1. Im Suchfeld **Tabellen**
   * Geben Sie zum Anzeigen von Protokollen eine einfache Abfrage ein, z. B. die folgende:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Geben Sie zum Anzeigen von Metriken eine einfache Abfrage ein, z. B. die folgende:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Um das Suchergebnis anzuzeigen, wählen Sie **Ausführen** aus.

### <a name="use-log-analytics"></a>Verwenden von Log Analytics

1. Wählen Sie im Azure-Portal im linken Bereich **Log Analytics** aus.
1. Wählen Sie den Log Analytics-Arbeitsbereich aus, den Sie beim Hinzufügen Ihrer Diagnoseeinstellungen ausgewählt haben.
1. Um den Bereich **Protokollsuche** zu öffnen, wählen Sie **Protokolle** aus.
1. Im Suchfeld **Tabellen**:
   * Geben Sie zum Anzeigen von Protokollen eine einfache Abfrage ein, z. B. die folgende:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Geben Sie zum Anzeigen von Metriken eine einfache Abfrage ein, z. B. die folgende:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Um das Suchergebnis anzuzeigen, wählen Sie **Ausführen** aus.
1. Sie können die Protokolle der jeweiligen Anwendung oder Instanz suchen, indem Sie eine Filterbedingung festlegen:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> Für `==` wird Groß-/Kleinschreibung beachtet, für `=~` jedoch nicht.

Weitere Informationen zu der in Log Analytics verwendeten Abfragesprache erhalten Sie unter [Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/).

### <a name="use-your-storage-account"></a>Verwenden Ihres Speicherkontos

1. Suchen Sie im Azure-Portal im linken Navigationsbereich oder im Suchfeld nach **Speicherkonten**.
1. Wählen Sie das Speicherkonto aus, das Sie beim Hinzufügen Ihrer Diagnoseeinstellungen ausgewählt haben.
1. Um den Bereich **Blobcontainer** zu öffnen, wählen Sie **Blobs** aus.
1. Um Anwendungsprotokolle zu überprüfen, suchen Sie nach einem Container mit dem Namen **insights-logs-applicationconsole**.
1. Um Anwendungsmetriken zu überprüfen, suchen Sie nach einem Container mit dem Namen **insights-metrics-pt1m**.

Weitere Informationen zum Senden von Diagnoseinformationen an ein Speicherkonto finden Sie unter [Speichern und Anzeigen von Diagnosedaten in Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Verwenden Ihres Event Hubs

1. Suchen Sie im Azure-Portal im linken Navigationsbereich oder im Suchfeld nach **Event Hubs**.

1. Suchen Sie nach dem Event Hub, den Sie beim Hinzufügen Ihrer Diagnoseeinstellungen ausgewählt haben, und wählen Sie ihn aus.
1. Um den Bereich **Event Hub-Liste** zu öffnen, wählen Sie **Event Hubs** aus.
1. Um Anwendungsprotokolle zu überprüfen, suchen Sie nach einem Event Hub mit dem Namen **insights-logs-applicationconsole**.
1. Um Anwendungsmetriken zu überprüfen, suchen Sie nach einem Event Hub mit dem Namen **insights-metrics-pt1m**.

Weitere Informationen zum Senden von Diagnoseinformationen an einen Event Hub finden Sie unter [Streamen von Azure-Diagnosedaten im langsamsten Pfad mithilfe von Event Hubs](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>Analysieren der Protokolle

Azure Log Analytics wird mit einer Kusto-Engine ausgeführt, damit Sie Ihre Protokolle zu Analysezwecken abfragen können. Eine kurze Einführung in das Abfragen mithilfe von Kusto finden Sie im [Log Analytics-Tutorial ](../azure-monitor/log-query/log-analytics-tutorial.md).

Anwendungsprotokolle bieten wichtige Informationen und ausführliche Protokolle zur Integrität und Leistung Ihrer Anwendung und zu vielem mehr. In den nächsten Abschnitten finden Sie einige einfache Abfragen, die Ihnen helfen, die aktuellen und früheren Zustände Ihrer Anwendung besser zu verstehen.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Anzeigen von Anwendungsprotokollen aus Azure Spring Cloud

Um eine Liste mit Anwendungsprotokollen aus Azure Spring Cloud zu überprüfen, sortiert nach der Zeit mit den neuesten Protokollen zuerst, führen Sie die folgende Abfrage aus:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Anzeigen von Protokolleinträgen mit Fehlern oder Ausnahmen

Um unsortierte Protokolleinträge zu überprüfen, die einen Fehler oder eine Ausnahme enthalten, führen Sie die folgende Abfrage aus:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Verwenden Sie diese Abfrage, um Fehler zu finden, oder ändern Sie die Abfragebedingungen, um bestimmte Fehlercodes oder Ausnahmen zu suchen.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Anzeigen der Anzahl von Fehlern und Ausnahmen, die von Ihrer Anwendung in der letzten Stunde gemeldet wurden

Um ein Kreisdiagramm zu erstellen, das die Anzahl der Fehler und Ausnahmen darstellt, die in der letzten Stunde von Ihrer Anwendung protokolliert wurden, führen Sie die folgende Abfrage aus:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Weitere Informationen zum Abfragen von Anwendungsprotokollen

Azure Monitor bietet umfassende Unterstützung für das Abfragen von Anwendungsprotokollen mithilfe von Log Analytics. Weitere Informationen zu diesem Dienst finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md). Weitere Informationen zum Erstellen von Abfragen zur Analyse Ihrer Anwendungsprotokolle finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Wie können mehrzeilige Java-Stapelüberwachungen in eine einzelne Zeile konvertiert werden?

Es gibt eine Möglichkeit, Ihre mehrzeiligen Stapelüberwachungen in eine einzelne Zeile zu konvertieren. Sie können die Java-Protokollausgabe ändern, um Stapelüberwachungsnachrichten neu zu formatieren, sodass Zeilenvorschubzeichen durch ein Token ersetzt werden. Bei Verwendung der Java-Logback-Bibliothek können Sie Stapelüberwachungsnachrichten durch Hinzufügen von `%replace(%ex){'[\r\n]+', '\\n'}%nopex` neu formatieren:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
In Log Analytics können Sie das Token dann wieder durch Zeilenvorschubzeichen ersetzen, wie hier zu sehen:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Die gleiche Strategie kann ggf. auch für andere Java-Protokollbibliotheken verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Bereitstellen Ihrer ersten Azure Spring Cloud-Anwendung](spring-cloud-quickstart.md)