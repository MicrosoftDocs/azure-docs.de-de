---
title: SELECT-Klausel in Azure Cosmos DB
description: Erfahren Sie mehr zur SELECT-Klausel von SQL für Azure Cosmos DB. Verwenden Sie SQL als eine JSON-Abfragesprache in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335438"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-Klausel in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Jede Abfrage besteht aus einer `SELECT`-Klausel und optionalen [FROM](sql-query-from.md)- und [WHERE](sql-query-where.md)-Klauseln nach ANSI SQL-Standards. Normalerweise wird die Quelle in der `FROM`-Klausel aufgelistet, und die `WHERE`-Klausel wendet einen Filter auf die Quelle an, um eine Teilmenge der JSON-Elemente abzurufen. Dann projiziert die `SELECT`-Klausel die angeforderten JSON-Werte in die ausgewählte Liste.

## <a name="syntax"></a>Syntax

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argumente
  
- `<select_specification>`  

  Eigenschaften oder Wert, die für das Resultset ausgewählt werden.  
  
- `'*'`  

  Gibt an, dass der Wert abgerufen werden sollte, ohne Änderungen vorzunehmen. Insbesondere, wenn der verarbeitete Wert ein Objekt ist, werden alle Eigenschaften abgerufen.  
  
- `<object_property_list>`  
  
  Gibt die Liste der abzurufenden Eigenschaften an. Jeder zurückgegebene Wert wird ein Objekt mit den angegebenen Eigenschaften sein.  
  
- `VALUE`  

  Gibt an, dass der JSON-Wert anstelle des vollständigen JSON-Objekts abgerufen werden sollte. Hiermit wird im Gegensatz zu `<property_list>` der projizierte Wert nicht in ein Objekt gehüllt.  

- `DISTINCT`
  
  Hiermit wird festgelegt, dass Duplikate von voraussichtlichen Eigenschaften entfernt werden sollen.  

- `<scalar_expression>`  

  Ausdruck, der den zu berechnenden Wert darstellt. Weitere Informationen finden Sie im Abschnitt [Skalarausdrücke](sql-query-scalar-expressions.md).  

## <a name="remarks"></a>Bemerkungen

Die `SELECT *`-Syntax ist nur gültig, wenn die FROM-Klausel genau einen Alias deklariert hat. `SELECT *` bietet eine Identitätsprojektion, die hilfreich sein kann, wenn keine Projektion erforderlich ist. SELECT * ist nur gültig, wenn die FROM-Klausel angegeben und nur eine einzelne Eingabequelle eingeführt ist.  
  
Sowohl `SELECT <select_list>` als auch `SELECT *` sind sogenannter „syntaktischer Zucker“ und können alternativ durch das Verwenden einfacher SELECT-Anweisungen ausgedrückt werden (s. unten).  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   entspricht:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   entspricht:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Beispiele

Das folgende Beispiel für eine SELECT-Abfrage gibt `address` aus `Families` zurück, deren `id` dem Text `AndersenFamily` entspricht:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-Klausel](sql-query-where.md)
