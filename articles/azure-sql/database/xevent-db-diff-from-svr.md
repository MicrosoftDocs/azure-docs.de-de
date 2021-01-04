---
title: Erweiterte Ereignisse
description: Beschreibt erweiterte Ereignisse (XEvents) in Azure SQL-Datenbank und wie sich Ereignissitzungen im Vergleich zu Microsoft SQL Server geringfügig unterscheiden.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: 139673e46421aa0dc19298697872fbff5fe587af
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501208"
---
# <a name="extended-events-in-azure-sql-database"></a>Erweiterte Ereignisse in Azure SQL-Datenbank 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Der Funktionsumfang von erweiterten Ereignissen in Azure SQL-Datenbank ist eine stabile Teilmenge der Features in SQL Server und Azure SQL Managed Instance.

*XEvents* ist ein informeller Spitzname, der in Blogs und anderen informellen Quellen mitunter für "erweiterte Ereignisse" verwendet wird.

Zusätzliche Informationen über erweiterte Ereignisse finden Sie unter:

- [Schnellstart: Erweiterte Ereignisse in SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Erweiterte Ereignisse](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Voraussetzungen

Dieses Thema setzt voraus, dass Sie mit Folgendem grundlegend vertraut sind:

- [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/)
- [Erweiterte Ereignisse](/sql/relational-databases/extended-events/extended-events)

- Der Großteil der Dokumentation über erweiterte Ereignisse gilt für SQL Server, Azure SQL-Datenbank und Azure SQL Managed Instance.

Vorherige Kenntnisse in Bezug auf die folgenden Elemente ist hilfreich bei der Auswahl der Ereignisdatei als [Ziel](#AzureXEventsTargets):

- [Azure Storage-Dienst](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell mit Azure Storage](/powershell/module/az.storage/)

## <a name="code-samples"></a>Codebeispiele

In verwandten Themen werden zwei Codebeispiele geboten:

- [Code des Ringpufferziels für erweiterte Ereignisse in Azure SQL-Datenbank](xevent-code-ring-buffer.md)

  - Kurzes einfaches Transact-SQL-Skript.
  - Wir betonen im Codebeispielthema, dass Sie die Ressourcen des Ringpufferziels durch Ausführen der Anweisung „alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` “ freigeben müssen, wenn Sie damit fertig sind. Später können Sie eine weitere Ringpufferinstanz durch `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`hinzufügen.

- [Code des Ereignisdateiziels für erweiterte Ereignisse in Azure SQL-Datenbank](xevent-code-event-file.md)

  - Phase 1 ist PowerShell zum Erstellen eines Azure-Speichercontainers.
  - Phase 2 ist Transact-SQL-Code, der den Azure Storage-Container verwendet.

## <a name="transact-sql-differences"></a>Transact-SQL-Unterschiede

- Beim Ausführen des [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) -Befehls für SQL Server verwenden Sie die **ON SERVER** -Klausel. Für Azure SQL-Datenbank verwenden Sie jedoch stattdessen die Klausel **ON DATABASE**.
- Die **ON DATABASE**-Klausel gilt auch für die Transact-SQL-Befehle [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) und [DROP EVENT SESSION](/sql/t-sql/statements/drop-event-session-transact-sql).

- Eine bewährte Methode ist das Einschließen der Ereignissitzungsoption von **STARTUP_STATE = ON** in Ihre Anweisungen **CREATE EVENT SESSION** oder **ALTER EVENT SESSION**.
  - Der Wert **= ON** unterstützt einen automatischen Neustart nach einer Neukonfiguration der logischen Datenbank aufgrund eines Failovers.

## <a name="new-catalog-views"></a>Neue Katalogsichten

Das Feature "Erweiterte Ereignisse" wird von mehreren [Katalogsichten](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)unterstützt. Katalogsichten informieren Sie über *Metadaten oder Definitionen* von vom Benutzer erstellten Ereignissitzungen in der aktuellen Datenbank. Die Sichten geben keine Informationen zu Instanzen aktiver Ereignissitzungen zurück.

| Name der<br/>Katalogsicht | BESCHREIBUNG |
|:--- |:--- |
| **sys.database_event_session_actions** |Gibt für jede Aktion jedes Ereignisses einer Ereignissitzung eine Zeile zurück. |
| **sys.database_event_session_events** |Gibt eine Zeile für jedes Ereignis in einer Ereignissitzung zurück. |
| **sys.database_event_session_fields** |Gibt eine Zeile für jede anpassbare Spalte zurück, die für Ereignisse und Ziele explizit festgelegt wurde. |
| **sys.database_event_session_targets** |Gibt für eine Ereignissitzung eine Zeile für jedes Ereignisziel zurück. |
| **sys.database_event_sessions** |Gibt eine Zeile für jede Ereignissitzung in der Datenbank zurück. |

In Microsoft SQL Server haben ähnliche Katalogsichten Namen, die *.server\_* anstelle von *.database\_* enthalten. Das Namensmuster entspricht **sys.server_event_%** .

## <a name="new-dynamic-management-views-dmvs"></a>Neue dynamische Verwaltungssichten [(DMVs)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

Azure SQL-Datenbank hat [dynamische Verwaltungsansichten (DMVs)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views) , die erweiterte Ereignisse unterstützen. DMVs informieren Sie über *aktive* Ereignissitzungen.

| DMV-Name | BESCHREIBUNG |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Gibt Informationen zu Ereignissitzungsaktionen zurück. |
| **sys.dm_xe_database_session_events** |Gibt Informationen zu Sitzungsereignissen zurück. |
| **sys.dm_xe_database_session_object_columns** |Zeigt die Konfigurationswerte für Objekte an, die an eine Sitzung gebunden sind. |
| **sys.dm_xe_database_session_targets** |Gibt Informationen zu Sitzungszielen zurück. |
| **sys.dm_xe_database_sessions** |Gibt eine Zeile für jede Ereignissitzung zurück, die auf die aktuelle Datenbank begrenzt ist. |

In Microsoft SQL Server werden ähnliche Katalogsichten ohne den Teil *\_database* des Namens benannt, wie z.B.:

- **sys.dm_xe_sessions**, anstelle von Namen<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs, die Azure SQL-Datenbank und Microsoft SQL Server gemeinsam haben

Für erweiterte Ereignisse gibt es zusätzliche DMVs (dynamische Verwaltungssichten), die Azure SQL-Datenbank, Azure SQL Managed Instance und Microsoft SQL Server gemeinsam haben:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Suchen der verfügbaren erweiterten Ereignisse, Aktionen und Ziele

Sie können mithilfe einer einfachen SQL **SELECT** -Anweisung eine Liste der verfügbaren Ereignisse, Aktionen und Ziele abrufen.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Ziele für Azure SQL-Datenbank-Ereignissitzungen

Im Folgenden werden Ziele aufgeführt, die Ergebnisse aus Ihren Ereignissitzungen in Azure SQL-Datenbank erfassen können:

- [Ringpuffer](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)) : Enthält für kurze Zeit Ereignisdaten im Arbeitsspeicher.
- [Ereigniszähler](/previous-versions/sql/sql-server-2016/ff878025(v=sql.130)) : Zählt alle Ereignisse, die während einer Sitzung mit erweiterter Ereignissen auftreten.
- [Ereignisdatei](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) : Schreibt vollständige Puffer in einen Azure Storage-Container.

Die [ETW](/dotnet/framework/wcf/samples/etw-tracing)-API (Event Tracing for Windows) ist für erweiterte Ereignisse in Azure SQL-Datenbank nicht verfügbar.

## <a name="restrictions"></a>Beschränkungen

Es gibt einige sicherheitsbezogene Unterschiede, die für die Cloudumgebung von Azure SQL-Datenbank angemessen sind:

- Erweiterte Ereignisse beruhen auf dem Modell der Isolation einzelner Mandanten. Eine Ereignissitzung in einer Datenbank kann nicht auf Daten oder Ereignisse in einer anderen Datenbank zugreifen.
- Sie können keine **CREATE EVENT SESSION**-Anweisung im Kontext der **master**-Datenbank angeben.

## <a name="permission-model"></a>Berechtigungsmodell

Sie benötigen die Berechtigung **Control** für die Datenbank, um eine **CREATE EVENT SESSION**-Anweisung angeben zu können. Der Datenbankbesitzer (dbo) hat die Berechtigung **Control** .

### <a name="storage-container-authorizations"></a>Speichercontainerautorisierungen

Das SAS-Token, das Sie für Ihren Azure Storage-Container generieren, muss **rwl** für die Berechtigungen angeben. Der Wert **rwl** bietet die folgenden Berechtigungen:

- Lesen
- Schreiben
- List

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Es gibt Szenarien, in denen durch die intensive Nutzung erweiterter Ereignisse mehr aktiver Arbeitsspeicher kumuliert wird, als für das System vorteilhaft ist. Daher werden Grenzwerte für die Menge des aktiven Arbeitsspeichers dynamisch von Azure SQL-Datenbank festgelegt, der von einer Ereignissitzung angesammelt werden kann. Viele Faktoren fließen in die dynamische Berechnung ein.

Wenn Sie eine Fehlermeldung erhalten, die besagt, dass ein Arbeitsspeichermaximum erzwungen wurde, können Sie z. B. die folgenden Maßnahmen ergreifen:

- Führen Sie weniger gleichzeitige Ereignissitzungen durch.
- Verringern Sie über die Anweisungen **CREATE** und **ALTER** für Ereignissitzungen die Arbeitsspeichermenge, die Sie für die **MAX\_MEMORY**-Klausel angeben.

### <a name="network-latency"></a>Netzwerklatenz

Beim Ziel **Ereignisdatei** können Netzwerklatenz oder -fehler auftreten, während Daten in Azure Storage-Blobs dauerhaft gespeichert werden. Andere Ereignisse in Azure SQL-Datenbank können möglicherweise verzögert werden, während auf den Abschluss der Netzwerkkommunikation gewartet wird. Diese Verzögerung kann Ihren Workload verlangsamen.

- Um dieses Leistungsrisiko zu vermeiden, legen Sie die Option **EVENT_RETENTION_MODE** in Ihren Ereignissitzungsdefinitionen nicht auf **NO_EVENT_LOSS** fest.

## <a name="related-links"></a>Verwandte Links

- [Verwenden von Azure PowerShell mit Azure Storage](/powershell/module/az.storage/)
- [Azure Storage-Cmdlets](/powershell/module/Azure.Storage)
- [Verwenden von Azure PowerShell mit Azure Storage](/powershell/module/az.storage/)
- [Verwenden des Blob-Speichers mit .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [CREATE EVENT SESSION (Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- [Jonathan Kehayias' Blogbeiträge zu erweiterten Ereignissen in Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- Die Webseite mit *Azure-Dienstupdates* wird mittels Parameter auf „Azure SQL-Datenbank“ festgelegt:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->