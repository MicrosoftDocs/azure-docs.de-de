---
title: Aggregatfunktionen in Azure Cosmos DB
description: Informationen zur Syntax von SQL-Aggregatfunktionen und Typen von Aggregatfunktionen, die von Azure Cosmos DB unterstützt werden.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551078"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Aggregatfunktionen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Aggregatfunktionen führen eine Berechnung für eine Gruppe von Werten in der `SELECT`-Klausel durch und geben einen einzelnen Wert zurück. Bei der folgenden Abfrage wird beispielsweise die Anzahl von Elementen in einem Container zurückgegeben:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Typen von Aggregatfunktionen

Die SQL-API unterstützt die folgenden Aggregatfunktionen. `SUM` und `AVG` werden für numerische Werte verwendet. `COUNT`, `MIN` und `MAX` werden für Zahlen, Zeichenfolgen, boolesche Werte und NULL-Werte verwendet.

| Funktion | BESCHREIBUNG |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | Gibt den Durchschnitt aller Werte im Ausdruck zurück. |
| [COUNT](sql-query-aggregate-count.md) | Gibt die Anzahl der Elemente im Ausdruck zurück. |
| [MAX](sql-query-aggregate-max.md) | Gibt den größten Wert im Ausdruck zurück. |
| [MIN](sql-query-aggregate-min.md) | Gibt den kleinsten Wert im Ausdruck zurück. |
| [SUM](sql-query-aggregate-sum.md) | Gibt die Summe aller Werte im Ausdruck zurück. |


Sie können auch nur den skalaren Wert des Aggregats zurückgeben, indem Sie das Schlüsselwort VALUE verwenden. Bei dieser Abfrage wird beispielsweise die Anzahl der Werte als einzelne Zahl zurückgegeben:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Die Ergebnisse sind:

```json
    [ 2 ]
```

Sie können auch Aggregationen mit Filtern kombinieren. Bei der folgenden Abfrage wird beispielsweise die Anzahl von Elementen mit dem Bundesstaat `WA` in der Adresse zurückgegeben.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Die Ergebnisse sind:

```json
    [ 1 ]
```

## <a name="remarks"></a>Bemerkungen

Diese Aggregatsystemfunktionen profitierten von einem [Bereichsindex](index-policy.md#includeexclude-strategy). Wenn Sie `AVG`, `COUNT`, `MAX`, `MIN` oder `SUM` für eine Eigenschaft erwarten, sollten Sie [den relevanten Pfad in die Indizierungsrichtlinie einschließen](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Cosmos DB](introduction.md)
- [System functions (Systemfunktionen)](sql-query-system-functions.md)
- [User defined functions (UDFs) in Azure Cosmos DB (Benutzerdefinierte Funktionen (UDFs) in Azure Cosmos DB)](sql-query-udfs.md)