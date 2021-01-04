---
title: Überwachungsprotokolle in Azure Database for MariaDB
description: Informationen zu den Überwachungsprotokollen, die in Azure Database for MariaDB verfügbar sind, sowie die verfügbaren Parameter zum Aktivieren von Protokolliergraden.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: e66eaf62b54759222421b17ebc7e33b82e018338
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541758"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Überwachungsprotokolle in Azure Database for MariaDB

In Azure Database for MariaDB ist das Überwachungsprotokoll für Benutzer verfügbar. Das Überwachungsprotokoll dient zum Nachverfolgen von Aktivitäten auf Datenbankebene und häufig zu Konformitätszwecken.

## <a name="configure-audit-logging"></a>Konfigurieren der Überwachungsprotokollierung

>[!IMPORTANT]
> Es wird empfohlen, nur die Ereignistypen und Benutzer zu protokollieren, die für Ihre Überwachungszwecke erforderlich sind, um sicherzustellen, dass die Leistung Ihres Servers nicht stark beeinträchtigt wird.

Das Überwachungsprotokoll ist standardmäßig deaktiviert. Um sie zu aktivieren, legen Sie `audit_log_enabled` auf ON fest.

Weitere Parameter, die Sie anpassen können:

- `audit_log_events`: steuert die zu protokollierenden Ereignisse. In der nachstehenden Tabelle finden Sie spezifische Überwachungsereignisse.
- `audit_log_include_users`: MariaDB-Benutzer, die zur Protokollierung einbezogen werden sollen. Der Standardwert für diesen Parameter ist leer, wodurch alle Benutzer zur Protokollierung einbezogen werden. Dies hat eine höhere Priorität als `audit_log_exclude_users`. Die maximale Länge des Parameters ist 512 Zeichen.
- `audit_log_exclude_users`: MariaDB-Benutzer, die von der Protokollierung ausgeschlossen werden sollen. Maximal sind vier Benutzer zulässig. Die maximale Länge des Parameters ist 256 Zeichen.

> [!Note]
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

## <a name="access-audit-logs"></a>Zugreifen auf Überwachungsprotokolle

Überwachungsprotokolle sind in Azure Monitor-Diagnoseprotokolle integriert. Nachdem Sie auf Ihrem MariaDB-Server Überwachungsprotokolle aktiviert haben, können Sie sie an Azure Monitor-Protokolle, Event Hubs oder Azure Storage senden. Weitere Informationen zum Aktivieren von Diagnoseprotokollen im Azure-Portal finden Sie im Artikel zum [Portal für Überwachungsprotokolle](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Schemas für Diagnoseprotokolle

In den folgenden Abschnitten wird beschrieben, was in MariaDB-Überwachungsprotokollen basierend auf dem Ereignistyp ausgegeben wird. Je nach Ausgabemethode können die enthaltenen Felder und ihre Reihenfolge variieren.

### <a name="connection"></a>Verbindung

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Von MariaDB generierte eindeutige Verbindungs-ID |
| `host_s` | Leer |
| `ip_s` | IP-Adresse des Clients, der die Verbindung mit MariaDB herstellt |
| `user_s` | Name des Benutzers, der die Abfrage ausführt |
| `db_s` | Name der Datenbank, mit der die Verbindung besteht |
| `\_ResourceId` | Ressourcen-URI |

### <a name="general"></a>Allgemein

Das unten angegebene Schema gilt für die Ereignistypen GENERAL, DML_SELECT, DML_NONSELECT, DML, DDL, DCL und ADMIN.

> [!NOTE]
> Bei `sql_text` wird das Protokoll abgeschnitten, wenn es länger als 2.048 Zeichen ist.

| **Eigenschaft** | **Beschreibung** |
|---|---|
| `TenantId` | Ihre Mandanten-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Zeitstempel für den Aufzeichnungsbeginn des Protokolls in UTC |
| `Type` | Typ des Protokolls Immer `AzureDiagnostics` |
| `SubscriptionId` | GUID für das Abonnement, zu dem der Server gehört |
| `ResourceGroup` | Name der Ressourcengruppe, zu der der Server gehört |
| `ResourceProvider` | Name des Ressourcenanbieters Immer `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Ressourcen-URI |
| `Resource` | Name des Servers |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Name des Servers |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Sekunden des Abfragestarts im UNIX-Zeitstempel |
| `error_code_d` | Fehlercode bei fehlerhafter Abfrage. `0` bedeutet, dass kein Fehler vorliegt |
| `thread_id_d` | ID des Threads, der die Abfrage ausgeführt hat |
| `host_s` | Leer |
| `ip_s` | IP-Adresse des Clients, der die Verbindung mit MariaDB herstellt |
| `user_s` | Name des Benutzers, der die Abfrage ausführt |
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

- Auflisten von überwachten Ereignissen auf allen MariaDB-Servern mit aktivierten Diagnoseprotokollen für Überwachungsprotokolle

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Überwachungsprotokollen im Azure-Portal](howto-configure-audit-logs-portal.md)