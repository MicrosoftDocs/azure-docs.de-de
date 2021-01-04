---
title: Protokolle für langsame Abfragen – Azure Database for MySQL – Flexible Server
description: In diesem Artikel werden die in Azure Database for MySQL Flexible Server verfügbaren Protokolle für langsame Abfragen beschrieben.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: dde9575a70ea80ad262bc01bb9d5d0015c803427
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543016"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Protokolle für langsame Abfragen für Azure Database for MySQL Flexible Server (Vorschau)

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

In Azure Database for MySQL Flexible Server ist das Protokoll für langsame Abfragen für Benutzer zur Konfiguration und zum Zugriff verfügbar. Protokolle für langsame Abfragen sind standardmäßig deaktiviert und können aktiviert werden, um bei der Identifizierung von Leistungsengpässen während der Problembehandlung zu helfen.

Weitere Informationen zum MySQL-Protokoll für langsame Abfragen finden Sie im [Abschnitt zu Protokollen für langsame Abfragen](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) in der MySQL-Engine-Dokumentation.

## <a name="configure-slow-query-logging"></a>Konfigurieren der Protokollierung für langsame Abfragen 
Das Protokoll für langsame Abfragen ist standardmäßig deaktiviert. Legen Sie den Serverparameter `slow_query_log` auf *ON* (EIN) fest, um Protokolle zu aktivieren. Diese Konfiguration kann über das Azure-Portal oder die Azure CLI erfolgen <!-- add link to server parameter-->. 

Sie können die folgenden anderen Parameter verwenden, um das Verhalten der Protokolle für langsame Abfragen zu konfigurieren:

- **long_query_time**: Protokolliert eine Abfrage, wenn die Ausführung länger als `long_query_time` dauert (in Sekunden). Die Standardeinstellung beträgt 10 Sekunden.
- **log_slow_admin_statements**: Bestimmt, ob Verwaltungsanweisungen (z. B. `ALTER_TABLE`, `ANALYZE_TABLE`) protokolliert werden.
- **log_queries_not_using_indexes**: Bestimmt, ob Abfragen, die keine Indizes verwenden, protokolliert werden.
- **log_throttle_queries_not_using_indexes**: Schränkt die Anzahl der nicht indizierten Abfragen ein, die in das Protokoll für langsame Abfragen geschrieben werden können. Dieser Parameter tritt in Kraft, wenn `log_queries_not_using_indexes` auf *ON* (EIN) festgelegt ist.

> [!IMPORTANT]
> Wenn Ihre Tabellen nicht indiziert sind, kann das Festlegen der Parameter `log_queries_not_using_indexes` und `log_throttle_queries_not_using_indexes` auf **ON** (EIN) die Leistung von MySQL beeinträchtigen, da alle Anforderungen, die für diese nicht indizierten Tabellen ausgeführt werden, in das Protokoll für langsame Abfragen geschrieben werden.

Vollständige Beschreibungen der Parameter des Protokolls für langsame Abfragen finden Sie in der [MySQL-Dokumentation zum Protokoll für langsame Abfragen](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-slow-query-logs"></a>Zugreifen auf Protokolle für langsame Abfragen

Protokolle zu langsamen Abfragen sind in die Azure Monitor-Diagnoseeinstellungen integriert. Nachdem Sie Protokolle zu langsamen Abfragen auf Ihrem flexiblen MySQL-Server aktiviert haben, können Sie sie an Azure Monitor-Protokolle, Event Hubs oder Azure Storage senden. Weitere Informationen über Diagnoseeinstellungen finden Sie in der [Dokumentation zu Diagnoseprotokollen](../../azure-monitor/platform/platform-logs-overview.md). Weitere Informationen zum Aktivieren von Diagnoseeinstellungen im Azure-Portal finden Sie im Artikel zum [Portal für Protokolle für langsame Abfragen](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

In der folgenden Tabelle wird die Ausgabe des Protokolls für langsame Abfragen beschrieben. Je nach Ausgabemethode können die enthaltenen Felder und ihre Reihenfolge variieren.

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Name des Servers |
| `start_time_t` [UTC] | Uhrzeit, zu der die Abfrage begann |
| `query_time_s` | Gesamtzeit (in Sekunden), die die Ausführung der Abfrage gedauert hat |
| `lock_time_s` | Gesamtzeit in Sekunden, während der die Abfrage gesperrt war |
| `user_host_s` | Username |
| `rows_sent_s` | Anzahl gesendeter Zeilen |
| `rows_examined_s` | Anzahl untersuchter Zeilen |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Einfüge-ID |
| `sql_text_s` | Vollständige Abfrage |
| `server_id_s` | Die ID des Servers |
| `thread_id_s` | Thread-ID |
| `\_ResourceId` | Ressourcen-URI |

> [!Note]
> Bei `sql_text_s` wird das Protokoll abgeschnitten, wenn es länger als 2.048 Zeichen ist.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysieren von Protokollen in Azure Monitor-Protokollen

Sobald die Protokolle für langsamen Abfragen an Azure Monitor-Protokolle über Diagnoseprotokolle weitergeleitet werden, können Sie weitere Analysen Ihrer langsamen Abfragen durchführen. Im Folgenden finden Sie einige Beispielabfragen, die Ihnen beim Einstieg helfen. Stellen Sie sicher, dass Sie die Abfragen unten mit Ihrem Servernamen aktualisieren.

- Abfragen auf einem bestimmten Server, die länger als 10 Sekunden dauern

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Auflisten der 5 längsten Abfragen auf einem bestimmten Server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Zusammenfassen langsamer Abfragen nach minimaler, maximaler, durchschnittlicher Dauer und standardmäßigen Abweichungen der Abfragezeit auf einem bestimmten Server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Diagramm der Verteilung langsamer Abfragen auf einem bestimmten Server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Anzeigen von Abfragen, die länger als 10 Sekunden für alle MySQL-Server mit aktivierten Diagnoseprotokollen dauern

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über [Überwachungsprotokolle](concepts-audit-logs.md).
- Konfigurieren von Protokollen für langsame Abfragen im [Azure-Portal](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->