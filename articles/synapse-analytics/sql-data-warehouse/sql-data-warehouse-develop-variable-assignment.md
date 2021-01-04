---
title: Zuweisen von Variablen
description: In diesem finden Sie wichtige Tipps zum Zuweisen von T-SQL-Variablen für dedizierte SQL-Pools in Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459221"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Zuweisen von Variablen für dedizierte SQL-Pools in Azure Synapse Analytics

In diesem Artikel finden Sie wichtige Tipps zum Zuweisen von T-SQL-Variablen in einem dedizierten SQL-Pool.

## <a name="set-variables-with-declare"></a>Festlegen von Variablen mit DECLARE

Variablen im dedizierten SQL-Pool werden mit der Anweisung `DECLARE` oder der Anweisung `SET` festgelegt. Das Initialisieren von Variablen mit DECLARE ist eine der flexibelsten Möglichkeiten zum Festlegen eines Variablenwerts im SQL-Pool.

```sql
DECLARE @v  int = 0
;
```

Sie können mit DECLARE auch mehrere Variablen gleichzeitig festlegen. Folgende Vorgänge können nicht mithilfe von SELECT oder UPDATE ausgeführt werden:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Eine Variable kann nicht in der gleichen DECLARE-Anweisung initialisiert und verwendet werden. Zur Veranschaulichung: Im folgenden Beispiel, das **nicht** zulässig ist, wird @p1 in der gleichen DECLARE-Anweisung sowohl initialisiert als auch verwendet. Daher liefert das folgende Beispiel einen Fehler:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Festlegen von Werten mit SET

SET ist eine allgemeine Methode zum Festlegen einer einzelnen Variablen.

Die folgenden Anweisungen sind alle gültige Möglichkeiten zum Festlegen einer Variablen mit SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Sie können mit SET nur jeweils eine einzige Variable festlegen. Allerdings sind zusammengesetzte Operatoren zulässig.

## <a name="limitations"></a>Einschränkungen

Sie können UPDATE nicht für die Zuweisung von Variablen verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).
