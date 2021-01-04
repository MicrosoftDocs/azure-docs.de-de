---
title: SQL-Abfrageoperatoren für Azure Cosmos DB
description: Erfahren Sie mehr über SQL-Operatoren wie Gleichheit, Vergleich und logische Operatoren, die von Azure Cosmos DB unterstützt werden.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335437"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatoren in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden die verschiedenen Operatoren beschrieben, die von Azure Cosmos DB unterstützt werden.

## <a name="equality-and-comparison-operators"></a>Gleichheits- und Vergleichsoperatoren

Die folgende Tabelle zeigt die Ergebnisse für Gleichheitsvergleiche in der SQL-API zwischen den einzelnen JSON-Typen.

| **Op** | **Undefiniert** | **NULL** | **Boolescher Wert** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefiniert** | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert |
| **NULL** | Nicht definiert | **OK** | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert |
| **Boolescher Wert** | Nicht definiert | Nicht definiert | **OK** | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert |
| **Number** | Nicht definiert | Nicht definiert | Nicht definiert | **OK** | Nicht definiert | Nicht definiert | Nicht definiert |
| **String** | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | **OK** | Nicht definiert | Nicht definiert |
| **Object** | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | **OK** | Nicht definiert |
| **Array** | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | Nicht definiert | **OK** |

Bei Vergleichsoperatoren wie `>`, `>=`, `!=`, `<` und `<=` ergeben Vergleiche unterschiedlicher Typen oder zwischen zwei Objekten oder Arrays den Wert `Undefined`.  

Wenn das Ergebnis des skalaren Ausdrucks `Undefined` ist, wird das Element nicht in das Ergebnis eingeschlossen, da `Undefined` nicht gleich `true` ist.

Beispielsweise erzeugt die folgende Abfrage durch den Vergleich einer Zahl mit einem Zeichenfolgenwert `Undefined`. Daher enthält der Filter keine Ergebnisse.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>Logische Operatoren (AND, OR und NOT)

Logische Operatoren arbeiten mit booleschen Werten. Es folgt eine Liste der logischen Wahrheitstabellen für diese Operatoren:

**OR-Operator**

Gibt `true` zurück, wenn der Wert für mindestens eine der Bedingungen `true` ist.

|  | **Wahr** | **False** | **Undefiniert** |
| --- | --- | --- | --- |
| **Wahr** |True |True |True |
| **False** |True |False |Nicht definiert |
| **Undefiniert** |True |Nicht definiert |Nicht definiert |

**AND-Operator**

Gibt `true` zurück, wenn beide Ausdrücke `true` sind.

|  | **Wahr** | **False** | **Undefiniert** |
| --- | --- | --- | --- |
| **Wahr** |True |False |Nicht definiert |
| **False** |False |False |False |
| **Undefiniert** |Nicht definiert |False |Nicht definiert |

**NOT-Operator**

Kehrt den Wert eines beliebigen booleschen Ausdrucks um.

|  | **NOT** |
| --- | --- |
| **Wahr** |False |
| **False** |True |
| **Undefiniert** |Nicht definiert |

**Operatorrangfolge**

Die logischen Operatoren `OR`, `AND` und `NOT` weisen die unten gezeigte Rangfolge auf:

| **Operator** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>*-Operator

Mit dem Sonderoperator * wird das gesamte Element unverändert projiziert. Wenn dieser Operator verwendet wird, dürfen keine weiteren projizierten Felder existieren. Eine Abfrage wie `SELECT * FROM Families f` ist gültig, aber `SELECT VALUE * FROM Families f` und `SELECT *, f.id FROM Families f` sind nicht gültig.

## <a name="-and--operators"></a>? und ?? – Operatoren

Sie können ternäre (?) und koaleszierte (??) Operatoren wie in den Programmiersprachen C# und JavaScript zum Erstellen von bedingten Ausdrücken verwenden.

Mit dem Operator ? können Sie JSON-Eigenschaften Ad hoc erstellen. Beispielsweise werden mit der folgenden Abfrage die Klassenstufen in `elementary` oder `other` klassifiziert:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Sie können auch Aufrufe des Operators ? wie in der folgenden Abfrage verschachteln: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Wie bei anderen Abfrageoperatoren schließt der Operator ? Elemente aus, wenn die referenzierten Eigenschaften fehlen oder unterschiedliche Typen miteinander verglichen werden.

Der Operator ?? kann bei Abfragen für teilweise strukturiert oder gemischte Datentypen zur effizienten Prüfung des Vorkommens einer Eigenschaft in einem Element verwendet werden. Die folgende Abfrage gibt z.B. `lastName` zurück, falls vorhanden, oder `surname` zurück, falls `lastName` nicht vorhanden ist.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nächste Schritte

- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Schlüsselwörter](sql-query-keywords.md)
- [SELECT-Klausel](sql-query-select.md)
