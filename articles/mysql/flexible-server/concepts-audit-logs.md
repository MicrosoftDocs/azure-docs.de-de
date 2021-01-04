---
title: Überwachungsprotokolle in Azure Database for MySQL – Flexible Server
description: In diesem Artikel werden die in Azure Database for MySQL – Flexible Server verfügbaren Überwachungsprotokolle beschrieben.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 5aab78ad99b80ff1d7be92bd36847b01dbc0e33b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542217"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Überwachen der Datenbankaktivität mit Überwachungsprotokolle in Azure Database for MySQL – Flexible Server

> [!IMPORTANT] 
> Azure Database for MySQL – Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL – Flexible Server bietet Benutzern die Möglichkeit, Überwachungsprotokolle zu konfigurieren. Überwachungsprotokolle können dazu verwendet werden, die Aktivität auf Datenbankebene zu überwachen, einschließlich Verbindungs-, Administrator-, DDL- und DML-Ereignisse. Diese Arten von Protokollen werden häufig zu Compliancezwecken verwendet.

## <a name="configure-audit-logging"></a>Konfigurieren der Überwachungsprotokollierung

>[!IMPORTANT]
> Es wird empfohlen, nur die Ereignistypen und Benutzer zu protokollieren, die für Ihre Überwachungszwecke erforderlich sind, um sicherzustellen, dass die Leistung Ihres Servers nicht stark beeinträchtigt wird.

Überwachungsprotokolle sind standardmäßig deaktiviert. Legen Sie den Serverparameter `audit_log_enabled` auf *ON* (EIN) fest, um sie zu aktivieren. Diese Konfiguration kann über das Azure-Portal oder die Azure CLI erfolgen. <!-- add link to server parameter-->. 

Sie können die folgenden anderen Parameter verwenden, um das Verhalten der Überwachungsprotokollierung zu konfigurieren:

- `audit_log_events`: steuert die zu protokollierenden Ereignisse. In der nachstehenden Tabelle finden Sie spezifische Überwachungsereignisse.
- `audit_log_include_users`: MySQL-Benutzer, die zur Protokollierung einbezogen werden sollen. Der Standardwert für diesen Parameter ist leer, wodurch alle Benutzer zur Protokollierung einbezogen werden. Dies hat eine höhere Priorität als `audit_log_exclude_users`. Die maximale Länge des Parameters ist 512 Zeichen.
- `audit_log_exclude_users`: MySQL-Benutzer, die aus der Protokollierung ausgeschlossen werden sollen. Die maximale Länge des Parameters ist 512 Zeichen.

> [!NOTE]
> `audit_log_include_users` hat eine höhere Priorität als `audit_log_exclude_users`. Wenn z. B. `audit_log_include_users` = `demouser` und `audit_log_exclude_users` = `demouser` einbezogen werden, wird der Benutzer in die Überwachungsprotokolle eingeschlossen, da `audit_log_include_users` eine höhere Priorität hat.

| **Event** | **Beschreibung** |
|---|---|
| `CONNECTION` | : Initiierung der Verbindung (erfolgreich oder nicht erfolgreich) <br> : Erneute Authentifizierung des Benutzers mit anderem Benutzernamen/Kennwort während der Sitzung <br> : Beendigung der Verbindung |
| `DML_SELECT`| SELECT-Abfragen |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE-Abfragen |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Abfragen wie DROP DATABASE |
| `DCL` | Abfragen wie GRANT PERMISSION |
| `ADMIN` | Abfragen wie SHOW STATUS |
| `GENERAL` | Alle in DML_SELECT, DML_NONSELECT, DML, DDL, DCL und ADMIN |
| `TABLE_ACCESS` | - Nur für MySQL 5.7 verfügbar <br> - Leseanweisungen für Tabelle, z. B. SELECT oder INSERT INTO... SELECT <br> - Löschanweisungen für Tabelle, z. B. DELETE oder TRUNCATE TABLE <br> - Einfügeanweisungen für Tabelle, z. B. INSERT oder REPLACE <br> - Aktualisierungsanweisungen für Tabelle, z. B. UPDATE |

## <a name="access-audit-logs"></a>Zugreifen auf Überwachungsprotokolle

Überwachungsprotokolle sind mit den Diagnoseeinstellungen von Azure Monitor integriert. Nachdem Sie Überwachungsprotokolle auf Ihrem flexiblen MySQL-Server aktiviert haben, können Sie sie an Azure Monitor-Protokolle, Event Hubs oder Azure Storage senden. Weitere Informationen über Diagnoseeinstellungen finden Sie in der [Dokumentation zu Diagnoseprotokollen](../../azure-monitor/platform/platform-logs-overview.md). Weitere Informationen zum Aktivieren von Diagnoseeinstellungen im Azure-Portal finden Sie im Artikel zum [Portal für Überwachungsprotokolle](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

In den folgenden Abschnitten wird beschrieben, was von MySQL-Überwachungsprotokollen basierend auf dem Ereignistyp ausgegeben wird. Je nach Ausgabemethode können die enthaltenen Felder und ihre Reihenfolge variieren.

### <a name="connection"></a>Verbindung

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (nur für MySQL 5.7 verfügbar) |
| `connection_id_d` | Von MySQL generierte eindeutige Verbindungs-ID |
| `host_s` | Leer |
| `ip_s` | IP-Adresse des Clients, der die Verbindung mit MySQL herstellt |
| `user_s` | Name des Benutzers, der die Abfrage ausführt |
| `db_s` | Name der Datenbank, mit der die Verbindung besteht |
| `\_ResourceId` | Ressourcen-URI |

### <a name="general"></a>Allgemein

Das unten angegebene Schema gilt für die Ereignistypen GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL und ADMIN.

> [!NOTE]
> Bei `sql_text_s` wird das Protokoll abgeschnitten, wenn es länger als 2.048 Zeichen ist.

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (nur für MySQL 5.6 verfügbar) |
| `event_time` | Startzeit der Abfrage im UTC-Zeitstempel |
| `error_code_d` | Fehlercode bei fehlerhafter Abfrage. `0` bedeutet, dass kein Fehler vorliegt |
| `thread_id_d` | ID des Threads, der die Abfrage ausgeführt hat |
| `host_s` | Leer |
| `ip_s` | IP-Adresse des Clients, der die Verbindung mit MySQL herstellt |
| `user_s` | Name des Benutzers, der die Abfrage ausführt |
| `sql_text_s` | Vollständiger Abfragetext |
| `\_ResourceId` | Ressourcen-URI |

### <a name="table-access"></a>Tabellenzugriff

> [!NOTE]
> Tabellenzugriffsprotokolle werden nur für MySQL 5.7 ausgegeben.<br>Bei `sql_text_s` wird das Protokoll abgeschnitten, wenn es länger als 2.048 Zeichen ist.

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` oder `DELETE` |
| `connection_id_d` | Von MySQL generierte eindeutige Verbindungs-ID |
| `db_s` | Name der Datenbank, auf die zugegriffen wird |
| `table_s` | Name der Tabelle, auf die zugegriffen wird |
| `sql_text_s` | Vollständiger Abfragetext |
| `\_ResourceId` | Ressourcen-URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysieren von Protokollen in Azure Monitor-Protokollen

Sobald Ihre Überwachungsprotokolle über Diagnoseprotokolle an Azure Monitor-Protokolle weitergeleitet wurden, können Sie weitere Analysen Ihrer überwachten Ereignisse durchführen. Im Folgenden finden Sie einige Beispielabfragen, die Ihnen beim Einstieg helfen. Stellen Sie sicher, dass Sie die Abfragen unten mit Ihrem Servernamen aktualisieren.

- Auflisten von allgemeinen (GENERAL) Ereignissen auf einem bestimmten Server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Auflisten von Verbindungsereignissen (CONNECTION) auf einem bestimmten Server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Zusammenfassen von überwachten Ereignissen auf einem bestimmten Server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Erstellen eines Diagramms der Verteilung von Überwachungsereignistypen auf einem bestimmten Server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Auflisten von überwachten Ereignissen auf allen MySQL-Servern mit aktivierten Diagnoseprotokollen für Überwachungsprotokolle

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Protokolle für langsame Abfragen](concepts-slow-query-logs.md).
- Konfigurieren von Protokollen für Überwachungsabfragen über das [Azure-Portal](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->