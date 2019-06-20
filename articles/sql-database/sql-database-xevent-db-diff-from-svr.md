---
title: Erweiterte Ereignisse in Azure SQL-Datenbank | Microsoft Docs
description: Beschreibt erweiterte Ereignisse (XEvents) in Azure SQL-Datenbank und wie sich Ereignissitzungen im Vergleich zu Microsoft SQL Server geringfügig unterscheiden.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 7f742b094575b78f453fb735b23cc5319a27fa7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65206655"
---
# <a name="extended-events-in-sql-database"></a>Erweiterte Ereignisse in Azure SQL-Datenbank
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

In diesem Thema wird erläutert, wie sich die Implementierung erweiterter Ereignisse in Azure SQL-Datenbank im Vergleich zu erweiterten Ereignissen in Microsoft SQL Server geringfügig unterscheidet.

- SQL-Datenbank V12 wurde das Feature der erweiterte Ereignisse in der zweiten Hälfte des Jahres 2015 hinzugefügt.
- SQL Server bietet erweiterte Ereignisse seit 2008.
- Der Funktionsumfang erweiterter Ereignisse für SQL-Datenbank ist eine stabile Teilmenge der Funktionen in SQL Server.

*XEvents* ist ein informeller Spitzname, der in Blogs und anderen informellen Quellen mitunter für "erweiterte Ereignisse" verwendet wird.

Weitere Informationen zu erweiterten Ereignissen für Azure SQL-Datenbank und Microsoft SQL Server finden Sie unter:

- [Schnellstart: Erweiterte Ereignisse in SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Erweiterte Ereignisse](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Voraussetzungen

Dieses Thema setzt voraus, dass Sie mit Folgendem grundlegend vertraut sind:

- [Azure SQL-Datenbank-Dienst](https://azure.microsoft.com/services/sql-database/).
- [Erweiterte Ereignisse](https://msdn.microsoft.com/library/bb630282.aspx) in Microsoft SQL Server.

- Der größte Teil unserer Dokumentation zu erweiterten Ereignissen gilt für SQL Server und SQL-Datenbank.

Vorherige Kenntnisse in Bezug auf die folgenden Elemente ist hilfreich bei der Auswahl der Ereignisdatei als [Ziel](#AzureXEventsTargets):

- [Azure Storage-Dienst](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Verwenden von Azure PowerShell mit Azure Storage](../storage/common/storage-powershell-guide-full.md) – Bietet umfassende Informationen zu PowerShell und zum Azure Storage-Dienst.

## <a name="code-samples"></a>Codebeispiele

In verwandten Themen werden zwei Codebeispiele geboten:


- [Code des Ringpufferziels für erweiterte Ereignisse in SQL-Datenbank](sql-database-xevent-code-ring-buffer.md)
    - Kurzes einfaches Transact-SQL-Skript.
    - Wir betonen im Codebeispielthema, dass Sie die Ressourcen des Ringpufferziels durch Ausführen der Anweisung „alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` “ freigeben müssen, wenn Sie damit fertig sind. Später können Sie eine weitere Ringpufferinstanz durch `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`hinzufügen.


- [Code des Ziels "Ereignisdatei" für erweiterte Ereignisse in SQL-Datenbank](sql-database-xevent-code-event-file.md)
    - Phase 1 ist PowerShell zum Erstellen eines Azure-Speichercontainers.
    - Phase 2 ist Transact-SQL-Code, der den Azure Storage-Container verwendet.

## <a name="transact-sql-differences"></a>Transact-SQL-Unterschiede


- Beim Ausführen des [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) -Befehls für SQL Server verwenden Sie die **ON SERVER** -Klausel. Doch bei SQL-Datenbank verwenden Sie stattdessen die **ON DATABASE** -Klausel.


- Die **ON DATABASE**-Klausel gilt auch für die Transact-SQL-Befehle [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) und [DROP EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx).


- Eine bewährte Methode ist das Einschließen der Ereignissitzungsoption von **STARTUP_STATE = ON** in Ihre Anweisungen **CREATE EVENT SESSION** oder **ALTER EVENT SESSION**.
    - Der Wert **= ON** unterstützt einen automatischen Neustart nach einer Neukonfiguration der logischen Datenbank aufgrund eines Failovers.

## <a name="new-catalog-views"></a>Neue Katalogsichten

Das Feature "Erweiterte Ereignisse" wird von mehreren [Katalogsichten](https://msdn.microsoft.com/library/ms174365.aspx)unterstützt. Katalogsichten informieren Sie über *Metadaten oder Definitionen* von vom Benutzer erstellten Ereignissitzungen in der aktuellen Datenbank. Die Sichten geben keine Informationen zu Instanzen aktiver Ereignissitzungen zurück.

| Name der<br/>Katalogsicht | BESCHREIBUNG |
|:--- |:--- |
| **sys.database_event_session_actions** |Gibt eine Zeile für jede Aktion in jedem Ereignis einer Ereignissitzung zurück. |
| **sys.database_event_session_events** |Gibt eine Zeile für jedes Ereignis in einer Ereignissitzung zurück. |
| **sys.database_event_session_fields** |Gibt eine Zeile für jede anpassbare Spalte zurück, die für Ereignisse und Ziele explizit festgelegt wurde. |
| **sys.database_event_session_targets** |Gibt eine Zeile für jedes Ereignisziel einer Ereignissitzung zurück. |
| **sys.database_event_sessions** |Gibt eine Zeile für jede Ereignissitzung in der SQL-Datenbank zurück. |

In Microsoft SQL Server haben ähnliche Katalogsichten Namen, die *.server\_* anstelle von *.database\_* enthalten. Das Namensmuster entspricht **sys.server_event_%** .

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Neue dynamische Verwaltungssichten [(DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL-Datenbank hat [dynamische Verwaltungsansichten (DMVs)](https://msdn.microsoft.com/library/bb677293.aspx) , die erweiterte Ereignisse unterstützen. DMVs informieren Sie über *aktive* Ereignissitzungen.

| DMV-Name | BESCHREIBUNG |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Gibt Informationen zu Ereignissitzungsaktionen zurück. |
| **sys.dm_xe_database_session_events** |Gibt Informationen zu Sitzungsereignissen zurück. |
| **sys.dm_xe_database_session_object_columns** |Zeigt die Konfigurationswerte für Objekte, die an eine Sitzung gebunden sind. |
| **sys.dm_xe_database_session_targets** |Gibt Informationen zu Sitzungszielen zurück. |
| **sys.dm_xe_database_sessions** |Gibt eine Zeile für jede Ereignissitzung zurück, die auf die aktuelle Datenbank begrenzt ist. |

In Microsoft SQL Server werden ähnliche Katalogsichten ohne den Teil *\_database* des Namens benannt, wie z.B.:

- **sys.dm_xe_sessions**, anstelle von Namen<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs, die Azure SQL-Datenbank und Microsoft SQL Server gemeinsam haben
Für erweiterte Ereignisse gibt es zusätzliche DMVs, die Azure SQL-Datenbank und Microsoft SQL Server gemeinsam haben:

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Ziele für SQL-Datenbank-Ereignissitzungen

Es folgen Ziele, die Ergebnisse aus Ihren Ereignissitzungen für SQL-Datenbank erfassen können:

- [Ringpuffer](https://msdn.microsoft.com/library/ff878182.aspx) : Enthält für kurze Zeit Ereignisdaten im Arbeitsspeicher.
- [Ereigniszähler](https://msdn.microsoft.com/library/ff878025.aspx) : Zählt alle Ereignisse, die während einer Sitzung mit erweiterter Ereignissen auftreten.
- [Ereignisdatei](https://msdn.microsoft.com/library/ff878115.aspx) : Schreibt vollständige Puffer in einen Azure Storage-Container.

Die [ETW](https://msdn.microsoft.com/library/ms751538.aspx) -API (Event Tracing for Windows) ist für erweiterte Ereignisse in SQL-Datenbank nicht verfügbar.

## <a name="restrictions"></a>Einschränkungen

Es gibt einige sicherheitsbezogene Unterschiede, die für die Cloudumgebung von SQL-Datenbank angemessen sind:

- Erweiterte Ereignisse beruhen auf dem Modell der Isolation einzelner Mandanten. Eine Ereignissitzung in einer Datenbank kann nicht auf Daten oder Ereignisse in einer anderen Datenbank zugreifen.
- Sie können keine **CREATE EVENT SESSION**-Anweisung im Kontext der **master**-Datenbank angeben.

## <a name="permission-model"></a>Berechtigungsmodell

Sie benötigen die Berechtigung **Control** für die Datenbank, um eine **CREATE EVENT SESSION**-Anweisung angeben zu können. Der Datenbankbesitzer (dbo) hat die Berechtigung **Control** .

### <a name="storage-container-authorizations"></a>Speichercontainerautorisierungen

Das SAS-Token, das Sie für Ihren Azure Storage-Container generieren, muss **rwl** für die Berechtigungen angeben. Der Wert **rwl** bietet die folgenden Berechtigungen:

- Lesen
- Schreiben
- Auflisten

## <a name="performance-considerations"></a>Überlegungen zur Leistung

Es gibt Szenarien, in denen durch die intensive Nutzung erweiterter Ereignisse mehr aktiver Arbeitsspeicher kumuliert wird, als für das System vorteilhaft ist. Deshalb sorgt das Azure SQL-Datenbank-System für eine dynamische Festlegung und Anpassung von Grenzwerten für die Menge an aktivem Arbeitsspeicher, die von einer Ereignissitzung kumuliert werden kann. Viele Faktoren fließen in die dynamische Berechnung ein.

Wenn Sie eine Fehlermeldung erhalten, die besagt, dass ein Arbeitsspeichermaximum erzwungen wurde, können Sie z. B. die folgenden Maßnahmen ergreifen:

- Führen Sie weniger gleichzeitige Ereignissitzungen durch.
- Verringern Sie über die Anweisungen **CREATE** und **ALTER** für Ereignissitzungen die Arbeitsspeichermenge, die Sie für die **MAX\_MEMORY**-Klausel angeben.

### <a name="network-latency"></a>Netzwerklatenz

Beim Ziel **Ereignisdatei** können Netzwerklatenz oder -fehler auftreten, während Daten in Azure Storage-Blobs dauerhaft gespeichert werden. Andere Ereignisse in der SQL-Datenbank können möglicherweise verzögert werden, während auf den Abschluss der Netzwerkkommunikation gewartet wird. Diese Verzögerung kann Ihren Workload verlangsamen.

- Um dieses Leistungsrisiko zu vermeiden, legen Sie die Option **EVENT_RETENTION_MODE** in Ihren Ereignissitzungsdefinitionen nicht auf **NO_EVENT_LOSS** fest.

## <a name="related-links"></a>Verwandte Links

- [Verwenden von Azure PowerShell mit Azure Storage](../storage/common/storage-powershell-guide-full.md)
- [Azure Storage-Cmdlets](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Verwenden von Azure PowerShell mit Azure Storage](../storage/common/storage-powershell-guide-full.md) – Bietet umfassende Informationen zu PowerShell und zum Azure Storage-Dienst.
- [Verwenden des Blob-Speichers mit .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias' Blogbeiträge zu erweiterten Ereignissen in Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Die Webseite mit *Azure-Dienstupdates* wird mittels Parameter auf „Azure SQL-Datenbank“ festgelegt:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Weitere Themen mit Codebeispielen für erweiterte Ereignisse finden Sie unter den folgenden Links. Allerdings müssen Sie routinemäßig alle Beispiele dahingehend prüfen, ob das Beispiel für Microsoft SQL Server oder Azure SQL-Datenbank gilt. Dann können Sie entscheiden, ob kleinere Änderungen erforderlich sind, um das Beispiel auszuführen.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
