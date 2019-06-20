---
title: Überwachungsprotokollformate in SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Struktur von Überwachungsprotokollen in SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61417387"
---
# <a name="sql-database-audit-log-format"></a>Überwachungsprotokollformate in SQL-Datenbank

Bei der [Azure SQL-Datenbank-Überwachung](sql-database-auditing.md) werden Datenbankereignisse nachverfolgt und in Überwachungsprotokolle in Ihrem Azure-Speicherkonto geschrieben oder für die Downstreamverarbeitung und -analyse an Event Hub bzw. Log Analytics gesendet.

## <a name="naming-conventions"></a>Namenskonventionen

### <a name="blob-audit"></a>Blobüberwachung

Überwachungsprotokoll, die in Blob Storage gespeichert werden, werden in einem Container namens `sqldbauditlogs` im Azure Storage-Konto gespeichert. Die Verzeichnishierarchie innerhalb des Containers weist das Format `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` auf. Die Dateinamen für Blobs weisen das Format `<CreationTime>_<FileNumberInSession>.xel` auf. Hierbei entspricht `CreationTime` dem UTC-Format `hh_mm_ss_ms`, und `FileNumberInSession` ist ein laufender Index, der für den Fall vorhanden ist, dass Sitzungsprotokolle sich über mehrere Blobdateien erstrecken.

Für die Datenbank `Database1` unter `Server1` wäre beispielsweise folgender Pfad gültig:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>Event Hub

Überwachungsereignisse werden in den Namespace und Event Hub geschrieben, der während der Konfiguration festgelegt wurde, im Textkörper von [Apache Avro](https://avro.apache.org/)-Ereignissen erfasst und im JSON-Format mit UTF-8-Codierung gespeichert. Zum Lesen der Überwachungsprotokolle können Sie [Avro Tools](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) oder ähnliche Tools verwenden, die dieses Format verarbeiten können.

### <a name="log-analytics"></a>Log Analytics

Überwachungsereignisse werden in eine `AzureDiagnostics`-Tabelle mit der Kategorie `SQLSecurityAuditEvents` in dem Log Analytics-Arbeitsbereich protokolliert, der während der Konfiguration festgelegt wurde. Weitere nützliche Informationen zu Log Analytics-Suchsprache und -Suchbefehlen finden Sie unter [Referenz zur Log Analytics-Suche](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search).

## <a id="subheading-1"></a>Felder eines Überwachungsprotokolls

| Name (Blob) | Name (Event Hubs/Log Analytics) | BESCHREIBUNG | Blobtyp | Event Hubs-/Log Analytics-Typ |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID der Aktion | varchar(4) | Zeichenfolge |
| action_name | action_name_s | Name der Aktion | – | Zeichenfolge |
| additional_information | additional_information_s | Zusätzliche Informationen zum Ereignis (im XML-Format gespeichert) | nvarchar(4000) | Zeichenfolge |
| affected_rows | affected_rows_d | Anzahl der von der Abfrage betroffenen Zeilen | bigint | int |
| application_name | application_name_s| Name der Clientanwendung | nvarchar(128) | Zeichenfolge |
| audit_schema_version | audit_schema_version_d | Immer 1 | int | int |
| class_type | class_type_s | Typ der überwachbaren Entität, für die die Überwachung ausgeführt wird | varchar(2) | Zeichenfolge |
| class_type_desc | class_type_description_s | Beschreibung der überwachbaren Entität, für die die Überwachung ausgeführt wird | – | Zeichenfolge |
| client_ip | client_ip_s | Quell-IP-Adresse der Clientanwendung | nvarchar(128) | Zeichenfolge |
| connection_id | – | ID der Verbindung auf dem Server | GUID | – |
| data_sensitivity_information | data_sensitivity_information_s | Informationstypen und Vertraulichkeitsbezeichnungen, die von der überwachten Abfrage zurückgegeben werden (je nach klassifizierter Spalte in der Datenbank) Weitere Informationen: [Azure SQL-Datenbank: Datenermittlung und -klassifizierung](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | Zeichenfolge |
| database_name | database_name_s | Datenbankkontext, in dem die Aktion durchgeführt wurde | sysname | Zeichenfolge |
| database_principal_id | database_principal_id_d | ID des Datenbankbenutzerkontexts, in dem die Aktion durchgeführt wurde | int | int |
| database_principal_name | database_principal_name_s | Name des Datenbankbenutzerkontexts, in dem die Aktion durchgeführt wurde | sysname | Zeichenfolge |
| duration_milliseconds | duration_milliseconds_d | Ausführungsdauer der Abfrage in Millisekunden | bigint | int |
| event_time | event_time_t | Datum und Uhrzeit, zu dem die überwachbare Aktion ausgelöst wurde | datetime2 | datetime |
| host_name | – | Clienthostname | Zeichenfolge | – |
| is_column_permission | is_column_permission_s | Flag, das angibt, ob die Berechtigung auf Benutzerebene erteilt wurde 1 = ja, 0 = nein | bit | Zeichenfolge |
| – | is_server_level_audit_s | Flag, das angibt, ob die Überwachung auf Serverebene durchgeführt wird | – | Zeichenfolge |
| object_ id | object_id_d | ID der Entität, für die die Überwachung durchgeführt wurde Dazu zählen Serverobjekte, Datenbanken, Datenbankobjekte und Schemaobjekte. 0, wenn die Entität den Server selbst darstellt oder die Überwachung nicht auf Objektebene durchgeführt wurde | int | int |
| object_name | object_name_s | Name der Entität, für die die Überwachung durchgeführt wurde Dazu zählen Serverobjekte, Datenbanken, Datenbankobjekte und Schemaobjekte. 0, wenn die Entität den Server selbst darstellt oder die Überwachung nicht auf Objektebene durchgeführt wurde | sysname | Zeichenfolge |
| permission_bitmask | permission_bitmask_s | Zeigt die gewährten, verweigerten oder widerrufenen Berechtigungen an (falls verfügbar) | varbinary(16) | Zeichenfolge |
| response_rows | response_rows_d | Anzahl der im Resultset zurückgegebenen Zeilen | bigint | int |
| schema_name | schema_name_s | Schemakontext, in dem die Aktion durchgeführt wurde NULL, wenn eine Überwachung außerhalb eines Schemas durchgeführt wird | sysname | Zeichenfolge |
| – | securable_class_type_s | Sicherungsfähige Objekte, die dem überwachten class_type zugeordnet werden | – | Zeichenfolge |
| sequence_group_id | sequence_group_id_g | Eindeutiger Bezeichner | varbinary | GUID |
| sequence_number | sequence_number_d | Verfolgt die Reihenfolge der Datensätze in einem einzelnen Überwachungsdatensatz nach, der zu groß für den Schreibpuffer für Überwachungen war | int | int |
| server_instance_name | server_instance_name_s | Name der Serverinstanz, in der die Überwachung durchgeführt wurde | sysname | Zeichenfolge |
| server_principal_id | server_principal_id_d | ID des Anmeldekontexts, in dem die Aktion durchgeführt wurde | int | int |
| server_principal_name | server_principal_name_s | Aktuelle Anmeldung | sysname | Zeichenfolge |
| server_principal_sid | server_principal_sid_s | Aktuelle Anmelde-SID | varbinary | Zeichenfolge |
| session_id | session_id_d | ID der Sitzung, in der das Ereignis aufgetreten ist | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Serverprinzipal für die Sitzung | sysname | Zeichenfolge |
| statement | statement_s | Ausgeführte T-SQL-Anweisung (falls verfügbar) | nvarchar(4000) | Zeichenfolge |
| succeeded | succeeded_s | Gibt an, ob die Aktion, die das Ereignis ausgelöst hat, erfolgreich war Für Ereignisse, die keine Anmelde- oder Batchereignisse sind, wird nur erfasst, ob die Berechtigungsüberprüfung erfolgreich war oder nicht. Der Vorgang wird nicht erfasst. 1 = Erfolg, 0 = Fehler | bit | Zeichenfolge |
| target_database_principal_id | target_database_principal_id_d | Datenbankprinzipal, auf dem der GRANT-, DENY- oder REVOKE-Vorgang ausgeführt wird 0, falls nicht zutreffend | int | int |
| target_database_principal_name | target_database_principal_name_s | Zielbenutzer der Aktion NULL, falls nicht zutreffend | Zeichenfolge | Zeichenfolge |
| target_server_principal_id | target_server_principal_id_d | Serverprinzipal, auf dem der GRANT-, DENY- oder REVOKE-Vorgang ausgeführt wird Gibt 0 zurück, falls nicht zutreffend | int | int |
| target_server_principal_name | target_server_principal_name_s | Zielanmeldung der Aktion NULL, falls nicht zutreffend | sysname | Zeichenfolge |
| target_server_principal_sid | target_server_principal_sid_s | SID der Zielanmeldung NULL, falls nicht zutreffend | varbinary | Zeichenfolge |
| transaction_id | transaction_id_d | nur SQL Server (ab 2016), 0 für Azure SQL-Datenbank | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Benutzerdefinierte Ereignis-ID, die als Argument an sp_audit_write übergeben wird NULL für Systemereignisse (Standardwert), ungleich 0 für benutzerdefinierte Ereignisse Weitere Informationen: [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | Benutzerdefinierte Informationen, die als Argument an sp_audit_write übergeben werden NULL für Systemereignisse (Standardwert), ungleich 0 für benutzerdefinierte Ereignisse Weitere Informationen: [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | Zeichenfolge |

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie mehr zur [Überwachung in Azure SQL-Datenbank](sql-database-auditing.md).