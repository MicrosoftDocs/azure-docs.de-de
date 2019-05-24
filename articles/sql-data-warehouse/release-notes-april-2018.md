---
title: Azure SQL Data Warehouse Versionshinweise April 2018 | Microsoft-Dokumentation
description: Versionshinweise zu Azure SQL Data Warehouse
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 5fd2d5e2022cc1cf552ee7b525dcc484cc718f1f
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912246"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>Neuerungen in Azure SQL Data Warehouse April 2018
Azure SQL Data Warehouse wird fortlaufend verbessert. In diesem Artikel informieren wir Sie über neue Features und Änderungen, die im April 2018 eingeführt wurden.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Möglichkeit des Kürzens von Partitionen vor einem Wechsel
Kunden nutzen häufig Partitionswechsel als Muster zum Laden von Daten aus einer Tabelle in eine andere. Dabei werden die Metadaten der Tabelle mithilfe der Syntax `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X` geändert. SQL Data Warehouse unterstützt keinen Partitionswechsel, wenn die Zielpartition Daten enthält. Ist dies der Fall, ist der Kunde gezwungen, vor der Ausführung des Wechsels die Zielpartition zu kürzen.

SQL Data Warehouse unterstützt diesen Vorgang jetzt in Form einer einzigen T-SQL-Anweisung.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Weitere Informationen finden Sie im Artikel zu [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="improved-query-compilation-performance"></a>Abfragekompilierung mit verbesserter Leistung
Für SQL Data Warehouse wurde eine Reihe Änderungen eingeführt, die bei verteilten Abfragen der Verbesserung der Kompilierungsphase dienen. Dank dieser Änderungen ist die Dauer der Abfragekompilierung bis zu **10x** niedriger, wodurch sich die allgemeinen Laufzeiten für die Abfrageausführung verringern. Diese Änderungen machen sich besonders in Data Warehouses mit einer großen Anzahl von Objekten (Tabellen, Funktionen, Ansichten, Prozeduren) bemerkbar.

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Nichtbelegung von Parallelitätsslots durch Datenbankkonsolenbefehle (Behavior Change)
SQL Data Warehouse unterstützt eine Teilmenge der T-SQL-[Datenbankkonsolenbefehle](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql), z.B. [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Bis jetzt haben diese Befehle einen [Parallelitätsslot](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots) belegt, wodurch sich die Zahl der ausführbaren Benutzerauslastungen/Abfragen verringert hat. Die `DBCC` werden nun in einer lokalen Warteschlange ausgeführt, wobei kein Ressourcenslot belegt wird, was eine allgemeine Leistungssteigerung bei der Abfrageausführung bedingt.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Aktualisierte Fehlermeldung bei übermäßigen Literalen (Behavior Change)
Bisher gab es in SQL Data Warehouse eine *ungefähre* Zählung, wenn eine Abfrage zu viele Literale enthielt.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

Die Fehlermeldung wurde dahingehend aktualisiert, dass Ihnen nun nur angezeigt wird, dass Sie das Literallimit erreicht haben.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Weitere Informationen zu maximalen Grenzwerten finden Sie im Abschnitt [Abfragen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) des Artikels [Kapazitätsgrenzen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits).

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Entfernen der Ansicht SYS.PDW_DATABASE_MAPPINGS (Behavior Change)
Die Ansicht `sys.pdw_database_mappings` wird in SQL Data Warehouse nicht verwendet. Bisher wurden bei der AUSWAHL dieser Ansicht keine Ergebnisse angezeigt. Die Ansicht wurde entfernt. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Grundlagen von SQL Data Warehouse vertraut gemacht haben, können Sie sich nun darüber informieren, wie Sie schnell [eine SQL Data Warehouse-Instanz erstellen][create a SQL Data Warehouse]. Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar][Azure glossary] sehr nützlich. Weitere SQL Data Warehouse-Ressourcen:  

* [Kundenerfolgsgeschichten]
* [Blogs]
* [Funktionsanfragen]
* [Videos]
* [Customer Advisory Team-Blogs]
* [Stack Overflow-Forum]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundenerfolgsgeschichten]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
