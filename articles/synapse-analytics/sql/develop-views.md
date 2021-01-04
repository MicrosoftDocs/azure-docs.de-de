---
title: T-SQL-Sichten mit SQL-Pools
description: Dieser Artikel enthält Tipps für das Verwenden von T-SQL-Sichten und das Entwickeln von Lösungen mit einem dedizierten SQL-Pool und einem serverlosen SQL-Pool in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6fb75257a86e9e46d6c180a2a38193adecb2b36a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451685"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>T-SQL-Sichten mit dediziertem SQL-Pool und serverlosem SQL-Pool in Azure Synapse Analytics

In diese Artikel finden Sie Tipps für das Verwenden von T-SQL-Sichten und das Entwickeln von Lösungen mit einem dedizierten SQL-Pool und einem serverlosen SQL-Pool in Azure Synapse Analytics.

## <a name="why-use-views"></a>Gründe für die Verwendung von Sichten

Sichten können auf verschiedene Weisen zur Verbesserung der Qualität Ihrer Lösung verwendet werden.  Dieser Artikel enthält einige Beispiele dafür, wie Sie Ihre Lösung mit Sichten bereichern können, und er umfasst die Einschränkungen, die berücksichtigt werden müssen.

### <a name="sql-pool---create-view"></a>SQL-Pool: Sicht erstellen

> [!NOTE]
> Die Syntax für CREATE VIEW wird in diesem Artikel nicht erörtert. Weitere Informationen finden Sie in der Dokumentation zu [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Architekturabstraktion

Ein häufig verwendetes Anwendungsmuster ist das erneute Erstellen von Tabellen mit [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), gefolgt von einem Muster zur Objektumbenennung beim Laden von Daten.

Im folgenden Beispiel werden einer Datumsdimension neue Datumsdatensätze hinzugefügt. Beachten Sie, wie eine neue Tabelle (DimDate_New) zuerst erstellt und dann umbenannt wird, um die ursprüngliche Version der Tabelle zu ersetzen.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;
```

Beachten Sie, dass dieser Ansatz aber auch dazu führen kann, dass Tabellen in der Sicht eines Benutzers ein- und ausgeblendet und Fehlermeldungen der Art „Tabelle nicht vorhanden“ angezeigt werden. Sichten können verwendet werden, um eine konsistente Darstellungsschicht für Benutzer bereitzustellen, während die zugrunde liegenden Objekte umbenannt werden.

Wenn Zugriff auf Daten über Sichten bereitgestellt wird, sind Benutzer nicht auf die Sichtbarkeit der zugrunde liegenden Tabellen angewiesen. Zusätzlich zu einer konsistenten Benutzererfahrung stellt diese Ebene sicher, dass Analysedesigner das Datenmodell weiterentwickeln können. Dank der Möglichkeit, die zugrunde liegenden Tabellen weiterentwickeln zu können, können Designer die Leistung beim Laden von Daten mit CTAS maximieren.

## <a name="performance-optimization"></a>Leistungsoptimierung

Sichten können auch genutzt werden, um leistungsoptimierte Verknüpfungen zwischen Tabellen durchzusetzen. Beispielsweise kann eine Sicht einen redundanten Verteilungsschlüssel als Teil des Verknüpfungskriteriums enthalten, um die Datenverschiebung zu minimieren.

Das Erzwingen einer bestimmten Abfrage oder eines Verknüpfungshinweises ist ein weiterer Vorteil der Verwendung von T-SQL-Sichten. Somit stellt die Sichtfunktion sicher, dass Verknüpfungen immer in optimaler Weise durchgeführt werden. Sie vermeiden, dass sich Benutzer das richtige Konstrukt für ihre Verknüpfungen merken müssen.

## <a name="limitations"></a>Einschränkungen

Sichten in Synapse SQL werden nur als Metadaten gespeichert. Daher sind die folgenden Optionen nicht verfügbar:

* Es gibt keine Option zur Schemabindung.
* Basistabellen können nicht über die Sicht aktualisiert werden.
* Es können keine Sichten für temporäre Tabellen erstellt werden.
* EXPAND/NOEXPAND-Hinweise werden nicht unterstützt.
* Es sind keine indizierten Sichten in Synapse SQL verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Weitere Tipps zur Entwicklung finden Sie in der [Entwicklungsübersicht für Synapse SQL](develop-overview.md).



