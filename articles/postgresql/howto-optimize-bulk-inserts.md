---
title: Optimieren von Masseneinfügungen auf einem Azure Database for PostgreSQL-Einzelserver
description: In diesem Artikel wird beschrieben, wie Sie Vorgänge für Masseneinfügungen auf einem Azure Database for PostgreSQL-Einzelserver optimieren können.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c1ae29f7c498a79af09aaaf6d7aeae29561aa500
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067044"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optimieren von Masseneinfügungen und Verwendung kurzlebiger Daten auf einem Azure Database for PostgreSQL-Einzelserver 
In diesem Artikel wird beschrieben, wie Sie Vorgänge für Masseneinfügungen optimieren und kurzlebige Daten auf einem Azure Database for PostgreSQL-Server verwenden können.

## <a name="use-unlogged-tables"></a>Verwenden von nicht protokollierten Tabellen
Erwägen Sie die Verwendung von nicht protokollierten Tabellen für Workloadvorgänge, die kurzlebige Daten oder Masseneinfügungen von großen Datasets nutzen.

Nicht protokollierte Tabellen sind ein PostgreSQL-Feature, das auf effektive Weise zum Optimieren von Masseneinfügungen eingesetzt werden kann. PostgreSQL verwendet die Write-Ahead-Protokollierung (WAL). Es bietet standardmäßig Unteilbarkeit und Dauerhaftigkeit. Unteilbarkeit, Konsistenz, Isolation und Dauerhaftigkeit stellen die ACID-Eigenschaften dar. 

Das Einfügen in eine nicht protokollierte Tabelle bedeutet, dass PostgreSQL Einfügungen ohne Schreibvorgang in das Transaktionsprotokoll durchführt, wobei es sich um einen E/A-Vorgang handelt. Daher sind diese Tabellen wesentlich schneller als normale Tabellen.

Verwenden Sie die folgenden Optionen, um eine nicht protokollierte Tabelle zu erstellen:
- Erstellen Sie eine neue nicht protokollierte Tabelle mit der Syntax `CREATE UNLOGGED TABLE <tableName>`.
- Konvertieren Sie eine vorhandene protokollierte Tabelle in eine nicht protokollierte Tabelle mit der Syntax `ALTER TABLE <tableName> SET UNLOGGED`.  

Verwenden Sie die Syntax `ALTER TABLE <tableName> SET LOGGED`, um den Prozess umzukehren.

## <a name="unlogged-table-tradeoff"></a>Nachteile von nicht protokollierten Tabellen
Nicht protokollierte Tabellen sind nicht vor Abstürzen geschützt. Eine nicht protokollierte Tabelle wird nach einem Absturz automatisch gekürzt, oder es erfolgt das nicht korrekte Herunterfahren. Der Inhalt einer nicht protokollierten Tabelle wird zudem nicht auf Standbyservern repliziert. Alle Indizes, die für eine nicht protokollierte Tabelle erstellt werden, werden ebenfalls automatisch nicht protokolliert. Nachdem der Einfügevorgang abgeschlossen wurde, konvertieren Sie die Tabelle in den protokollierten Zustand, damit der Einfügevorgang dauerhafter Art ist.

Für einige Kundenworkloads konnte eine Leistungsverbesserung von 15 bis 20 Prozent erzielt werden, indem nicht protokollierte Tabellen verwendet wurden.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie Ihre Workload auf die Nutzung von kurzlebigen Daten und umfangreichen Masseneinfügungen. Sehen Sie sich die folgende PostgreSQL-Dokumentation an:
 
- [SQL-Befehle zum Erstellen von Tabellen](https://www.postgresql.org/docs/current/static/sql-createtable.html)
