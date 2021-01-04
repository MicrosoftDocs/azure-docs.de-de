---
title: Benutzerdefinierte Schemas in Synapse SQL
description: In den folgenden Abschnitten finden Sie verschiedene Tipps zur Verwendung von benutzerdefinierten T-SQL-Schemas, um Lösungen mit der Synapse SQL-Funktion von Azure Synapse Analytics zu entwickeln.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451703"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Benutzerdefinierte Schemas in Synapse SQL

In den folgenden Abschnitten finden Sie verschiedene Tipps für die Verwendung von benutzerdefinierten T-SQL-Schemas, um Lösungen in Synapse-SQL zu entwickeln.

## <a name="schemas-for-application-boundaries"></a>Schemas für Anwendungsgrenzen

Herkömmliche Analysearchitektur verwendet häufig separate Datenbanken, um Anwendungsgrenzen basierend auf der Workload, Domäne oder Sicherheit zu erstellen. Eine herkömmliche SQL Server Analytics-Infrastruktur kann z. B. eine Stagingdatenbank, eine Analysedatenbank und Data Mart-Datenbanken enthalten. In dieser Topologie funktioniert jede Datenbank als Workload und Sicherheitsgrenze in der Architektur.

Stattdessen führt Synapse SQL die gesamte Workload für die Analyse innerhalb einer Datenbank aus. Datenbankübergreifende Verknüpfungen sind nicht zulässig. Synapse SQL erwartet, dass alle vom Warehouse verwendeten Tabellen innerhalb der einen Datenbank gespeichert werden.

> [!NOTE]
> In dedizierten SQL-Pools werden keine datenbankübergreifenden Abfragen unterstützt. Analyseimplementierungen, die dieses Muster nutzen, müssen daher überarbeitet werden. Der serverlose SQL-Pool unterstützt datenbankübergreifende Abfragen.

## <a name="user-defined-schema-recommendations"></a>Empfehlungen für benutzerdefinierte Schemas

Enthalten sind Empfehlungen für die Konsolidierung der Workload-, Sicherheits-, Domänen- und Funktionsgrenzen mithilfe von benutzerdefinierten Schemas:

- Verwenden Sie eine Datenbank, um die gesamte Workload für die Analyse auszuführen.
- Konsolidieren Sie Ihre vorhandene Analyseumgebung, um eine einzelne Datenbank zu verwenden.
- Nutzen Sie **benutzerdefinierte Schemas** , um die Grenze bereitzustellen, die zuvor mithilfe von Datenbanken implementiert wurde.

Wenn zuvor keine benutzerdefinierten Schemas verwendet wurden, können Sie von Grund auf neu beginnen. Verwenden Sie einfach den alten Datenbanknamen als Grundlage für Ihre benutzerdefinierten Schemas in der Synapse SQL-Datenbank.

Wenn bereits Schemas verwendet wurden, stehen Ihnen einige Optionen zur Verfügung:

- Entfernen Sie die älteren Schemanamen, und beginnen Sie von vorn.
- Behalten Sie die alten Schemanamen bei, indem Sie diese vorab an den Tabellennamen anhängen.
- Behalten Sie die älteren Schemanamen bei, indem Sie Sichten auf die Tabelle in einem zusätzlichen Schema implementieren, um die alte Schemastruktur neu zu erstellen.

> [!NOTE]
> Option 3 mag auf den ersten Blick die attraktivste Wahl sein. Sichten sind in Synapse SQL schreibgeschützt. Jede Änderung von Daten oder Tabellen muss für die Basistabelle ausgeführt werden. Option 3 führt außerdem eine Schicht von Sichten im System ein. Sie sollten dies besonders berücksichtigen, wenn Sie in Ihrer Architektur bereits Sichten verwenden.
> 
> 

### <a name="examples"></a>Beispiele

Implementieren von benutzerdefinierten Schemas basierend auf Datenbanknamen.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behalten Sie die älteren Schemanamen bei, indem Sie diese vorab an den Tabellennamen anhängen. Verwenden Sie Schemas für die Workload-Grenze.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Beibehalten Sie die älteren Schemanamen mit Sichten bei.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Jede Änderung in der Schemastrategie erfordert eine Überprüfung des Sicherheitsmodells für die Datenbank. In vielen Fällen können Sie das Sicherheitsmodell vereinfachen, indem Sie Berechtigungen auf Schemaebene zuweisen.

Wenn präzisere Berechtigungen erforderlich sind, können Sie Datenbankrollen verwenden. Weitere Informationen zu Datenbankrollen finden Sie im Artikel [Verwalten von Datenbankrollen und Benutzern](../../analysis-services/analysis-services-database-users.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Tipps zur Entwicklung finden Sie in der [Entwicklungsübersicht für Synapse SQL](develop-overview.md).
