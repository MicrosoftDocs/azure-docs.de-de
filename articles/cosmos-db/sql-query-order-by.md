---
title: ORDER BY-Klausel in Azure Cosmos DB
description: Erfahren Sie mehr über die ORDER BY-Klausel von SQL für Azure Cosmos DB. Verwenden Sie SQL als eine JSON-Abfragesprache in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338295"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-Klausel in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die optionale `ORDER BY`-Klausel gibt die Sortierreihenfolge für die von der Abfrage zurückgegebenen Ergebnisse an.

## <a name="syntax"></a>Syntax
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumente
  
- `<sort_specification>`  
  
   Gibt eine Eigenschaft oder einen Ausdruck an, wonach das Resultset der Abfrage sortiert werden soll. Eine Sortierspalte kann als Name oder Eigenschaftenalias angegeben werden.  
  
   Mehrere Eigenschaften können angegeben werden. Namen von Eigenschaften müssen eindeutig sein. Die Sequenz der Sortiereigenschaften in der `ORDER BY`-Klausel definiert die Anordnung des sortierten Resultsets. Dies bedeutet: Das Resultset wird nach der ersten Eigenschaft sortiert, und dann wird diese sortierte Liste nach der zweiten Eigenschaft sortiert usw.  
  
   Die Eigenschaftennamen, auf die in der `ORDER BY`-Klausel verwiesen wird, müssen entweder einer Eigenschaft in der Auswahlliste oder einer Eigenschaft entsprechen, die in der Sammlung definiert ist, die in der `FROM`-Klausel eindeutig angegeben ist.  
  
- `<sort_expression>`  
  
   Hiermit wird mindestens eine Eigenschaft oder ein Ausdruck festgelegt, nach dem die Abfrageergebnisse sortiert werden.  
  
- `<scalar_expression>`  
  
   Weitere Informationen finden Sie im Abschnitt [Skalarausdrücke](sql-query-scalar-expressions.md).  
  
- `ASC | DESC`  
  
   Gibt an, dass die Werte in der angegebenen Spalte in aufsteigender oder absteigender Reihenfolge sortiert werden sollen. `ASC` sortiert vom niedrigsten Wert zum höchsten Wert. `DESC` sortiert vom höchsten Wert zum niedrigsten Wert. `ASC` ist die Standardsortierreihenfolge. NULL-Werte werden als die niedrigsten Werte behandelt, die möglich sind.  
  
## <a name="remarks"></a>Bemerkungen  
  
   Die `ORDER BY`-Klausel erfordert, dass die Indizierungsrichtlinie einen Index für die Felder enthält, die sortiert werden. Die Abfragelaufzeit von Azure Cosmos DB unterstützt die Sortierung für einen Eigenschaftennamen und nicht für berechnete Eigenschaften. Azure Cosmos DB unterstützt mehrere `ORDER BY`-Eigenschaften. Zum Ausführen einer Abfrage mit mehreren ORDER BY-Eigenschaften sollten Sie einen [zusammengesetzten Index](index-policy.md#composite-indexes) für die Felder definieren, die sortiert werden.

> [!Note]
> Wenn die Eigenschaften, die sortiert werden, für einige Dokumente möglicherweise nicht definiert sind und Sie diese mit einer ORDER BY-Abfrage abrufen möchten, müssen Sie diesen Pfad explizit in den Index aufnehmen. Die Standardindizierungsrichtlinie lässt das Abrufen der Dokumente, in denen die Sortierungseigenschaft nicht definiert ist, nicht zu. [Überprüfen Sie Beispielabfragen für Dokumente mit fehlenden Feldern](#documents-with-missing-fields).

## <a name="examples"></a>Beispiele

Hier sehen Sie beispielsweise eine Abfrage, mit der Familien sortiert nach dem Namen des Wohnorts in aufsteigender Reihenfolge abgerufen werden:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Die Ergebnisse sind:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Mit der folgenden Abfrage wird die `id` von Familien in der Reihenfolge abgerufen, in der das Element erstellt wurde. Das Element `creationDate` ist eine Zahl, die die *Epoche* repräsentiert, also die seit dem 1. Januar 1970 verstrichene Zeit in Sekunden.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Die Ergebnisse sind:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Darüber hinaus können Sie nach mehreren Eigenschaften sortieren. Eine Abfrage, die nach mehreren Eigenschaften sortiert, erfordert einen [zusammengesetzten Index](index-policy.md#composite-indexes). Betrachten Sie die folgende Abfrage:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Diese Abfrage ruft die `id` der Familien in aufsteigender Reihenfolge nach dem Namen der Stadt ab. Wenn mehrere Elemente den gleichen Ort aufweisen, sortiert die Abfrage in absteigender Reihenfolge nach dem `creationDate`.

## <a name="documents-with-missing-fields"></a>Dokumente mit fehlenden Feldern

Abfragen mit `ORDER BY`, die mit der Standardindizierungsrichtlinie gegen Container ausgeführt werden, geben keine Dokumente zurück, in denen die Sortiereigenschaft nicht definiert ist. Wenn Sie Dokumente einschließen möchten, bei denen die Sortiereigenschaft nicht definiert ist, sollten Sie diese Eigenschaft explizit in die Indizierungsrichtlinie aufnehmen.

Hier sehen Sie beispielsweise einen Container mit einer Indizierungsrichtlinie, die explizit keine Pfade außer `"/*"` enthält:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Wenn Sie eine Abfrage ausführen, die `lastName` in der `Order By`-Klausel enthält, enthalten die Ergebnisse nur Dokumente, für die eine `lastName`-Eigenschaft definiert ist. Wir haben keinen explizit eingeschlossenen Pfad für `lastName` definiert, sodass alle Dokumente ohne `lastName` nicht in den Abfrageergebnissen angezeigt werden.

Im Folgenden finden Sie eine Abfrage, die für zwei Dokumente nach `lastName` sortiert, wobei für eins davon kein `lastName` definiert ist:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Die Ergebnisse enthalten nur das Dokument, für das der `lastName` definiert ist:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Wenn wir die Indizierungsrichtlinie des Containers so aktualisieren, das ein Pfad für `lastName` explizit eingeschlossen wird, nehmen wir Dokumente mit einer nicht definierten Sortiereigenschaft in die Abfrageergebnisse auf. Sie müssen den Pfad explizit definieren, um zu diesem skalaren Wert zu gelangen (und nicht darüber hinaus). Sie sollten das Zeichen `?` in Ihrer Pfaddefinition in der Indizierungsrichtlinie verwenden, um sicherzustellen, dass Sie die Eigenschaften `lastName` explizit indizieren und keine darüber hinausgehenden geschachtelten Pfade. Wenn in der `Order By`-Abfrage ein [zusammengesetzter Index](index-policy.md#composite-indexes) verwendet wird, enthalten die Ergebnisse immer Dokumente mit einer nicht definierten Sortiereigenschaft in den Abfrageergebnissen.

Im Folgenden finden Sie eine Beispielindizierungsrichtlinie, die gestattet, dass Dokumente ohne definierten `lastName` in den Abfrageergebnissen angezeigt werden:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Wenn Sie dieselbe Abfrage erneut ausführen, werden fehlende `lastName` in den Abfrageergebnissen zuerst angezeigt:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Die Ergebnisse sind:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Wenn Sie die Sortierreihenfolge in `DESC` ändern, werden Dokumente ohne `lastName` in den Abfrageergebnissen zuletzt angezeigt:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Die Ergebnisse sind:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Nur Version 3.4.0 oder höher des .NET SDK unterstützt ORDER BY-Klauseln mit gemischten Typen. Wenn Sie also nach einer Kombination aus nicht definierten und definierten Werten sortieren möchten, sollten Sie diese Version (oder höher) verwenden.

Sie können nicht steuern, in welcher Reihenfolge verschiedene Typen in den Ergebnissen angezeigt werden. Im obigen Beispiel wurde gezeigt, wie nicht definierte Werte vor Zeichenfolgenwerten sortiert wurden. Wenn Sie z. B. eine bessere Kontrolle über die Sortierreihenfolge von nicht definierten Werten möchten, können Sie allen nicht definierten Eigenschaften den Zeichenfolgenwert "aaaaaaaaa" oder "zzzzzzzz" zuweisen, um sicherzustellen, dass sie entweder an erster oder letzter Stelle stehen.

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Indexing policies in Azure Cosmos DB](index-policy.md) (Indizierungsrichtlinien in Azure Cosmos DB)
- [OFFSET LIMIT-Klausel](sql-query-offset-limit.md)
