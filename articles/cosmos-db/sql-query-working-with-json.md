---
title: Arbeiten mit JSON in Azure Cosmos DB
description: Erfahren Sie etwas über das Abfragen von geschachtelten JSON-Eigenschaften und das Zugreifen darauf sowie über das Verwenden von Sonderzeichen in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/19/2020
ms.author: tisande
ms.openlocfilehash: 9a9300db1adc3ff238c44887012400702690b0e8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337828"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Arbeiten mit JSON in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In der SQL (Core)-API von Azure Cosmos DB werden Elemente als JSON-Code gespeichert. Typsystem und Ausdrücke sind auf JSON-Typen beschränkt. Weitere Informationen finden Sie in der [JSON-Spezifikation](https://www.json.org/).

Im Folgenden werden einige wichtige Aspekte bei der Arbeit mit JSON-Code zusammengefasst:

- JSON-Objekte beginnen immer mit einer öffnenden geschweiften Klammer (`{`) und enden mit einer schließenden geschweiften Klammer (`}`).
- Sie können JSON-Eigenschaften in anderen Eigenschaften [schachteln](#nested-properties).
- JSON-Eigenschaftswerte können Arrays sein.
- Bei JSON-Eigenschaftennamen wird zwischen Groß- und Kleinschreibung unterschieden.
- Der JSON-Eigenschaftenname kann ein beliebiger Zeichenfolgenwert (einschließlich Leerzeichen oder Zeichen, die keine Buchstaben sind) sein.

## <a name="nested-properties"></a>Verschachtelte Eigenschaften

Sie können mit einem Punkt-Accessor auf geschachtelte JSON-Eigenschaften zugreifen. Sie können geschachtelte JSON-Eigenschaften in Ihren Abfragen auf die gleiche Weise verwenden, wie Sie auch andere Eigenschaften verwenden.

Das folgende Dokument enthält geschachtelten JSON-Code:

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

In diesem Fall sind die Eigenschaften `state`, `country` und `city` in der `address`-Eigenschaft geschachtelt.

Im folgenden Beispiel werden zwei geschachtelte Eigenschaften (`f.address.state` und `f.address.city`) projiziert.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>Verwenden von Arrays

Zusätzlich zu den geschachtelten Eigenschaften unterstützen JSON-Eigenschaften auch Arrays.

Im folgenden Beispiel finden Sie ein Dokument mit einem Array:

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

Bei Verwendung von Arrays können Sie auf ein bestimmtes Element innerhalb des Arrays zugreifen, indem Sie auf seine Position verweisen:

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

In den meisten Fällen verwenden Sie jedoch eine [Unterabfrage](sql-query-subquery.md) oder [Selbstverknüpfung](sql-query-join.md), wenn Sie mit Arrays arbeiten.

Im Folgenden finden Sie ein Dokument, das den täglichen Saldo des Bankkontos eines Kunden anzeigt.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

Wenn Sie eine Abfrage ausführen möchten, mit der alle Kunden angezeigt werden, die zu einem bestimmten Zeitpunkt einen negativen Saldo hatten, können Sie [EXISTS](sql-query-subquery.md#exists-expression) mit einer Unterabfrage verwenden:

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="difference-between-null-and-undefined"></a>Unterschied zwischen NULL und „nicht definiert“

Ist eine Eigenschaft nicht in einem Element definiert, lautet ihr Wert `undefined`. Eine Eigenschaft mit dem Wert `null` muss explizit definiert und ihr muss der Wert `null` zugewiesen sein.

Stellen Sie sich etwa das folgende Beispielelement vor:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": null
}
```

In diesem Beispiel hat die Eigenschaft `isRegistered` den Wert `undefined`, da sie im Element ausgelassen wird. Die Eigenschaft `creationDate` weist den Wert `null` auf.

Azure Cosmos DB unterstützt zwei nützliche Systemfunktionen für die Typüberprüfung für Eigenschaften vom Typ `null` und `undefined`:

* [IS_NULL](sql-query-is-null.md): Überprüft, ob ein Eigenschaftswert `null` lautet.
* [IS_DEFINED](sql-query-is-defined.md): Überprüft, ob ein Eigenschaftswert definiert ist.

Informationen zu unterstützten Operatoren für die Werte `null` und `undefined` finden Sie [hier](sql-query-operators.md).

## <a name="reserved-keywords-and-special-characters-in-json"></a>Reservierte Schlüsselwörter und Sonderzeichen in JSON-Code

Sie können auf Eigenschaften zugreifen, indem Sie den Anführungszeichenoperator (`[]`) für Eigenschaften verwenden. `SELECT c.grade` and `SELECT c["grade"]` gleichwertig. Diese Syntax ist hilfreich, um eine Eigenschaft mit Escapezeichen zu versehen, die Leerzeichen oder Sonderzeichen enthält oder den gleichen Namen wie ein SQL-Schlüsselwort oder ein reserviertes Wort hat.

Hier ist beispielsweise ein Dokument mit einer Eigenschaft namens `order` und einer Eigenschaft `price($)`, die Sonderzeichen enthält:

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Wenn Sie Abfragen ausführen, die die Eigenschaften `order` oder `price($)` enthalten, erhalten Sie eine Syntaxfehlermeldung.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

Es wird folgendes Ergebnis ausgegeben:

`
Syntax error, incorrect syntax near 'order'
`

Sie sollten dieselben Abfragen wie unten beschrieben neu schreiben:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON-Ausdrücke

Die Projektion unterstützt auch JSON-Ausdrücke wie im folgenden Beispiel gezeigt:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Im vorherigen Beispiel muss die `SELECT`-Klausel ein JSON-Objekt erstellen. Da im Beispiel kein Schlüssel bereitgestellt wird, verwendet die Klausel außerdem den impliziten Argumentvariablennamen `$1`. Die folgende Abfrage gibt zwei implizite Argumentvariablen zurück: `$1` und `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>Aliase

Sie können explizit Aliase für Werte in Abfragen verwenden. Wenn eine Abfrage zwei Eigenschaften mit demselben Namen hat, verwenden Sie Aliase, um mindestens eine der Eigenschaften umzubenennen, sodass sie im projizierten Ergebnis eindeutig sind.

### <a name="examples"></a>Beispiele

Das für das Aliasing verwendete `AS`-Schlüsselwort ist optional, wie im folgenden Beispiel bei der Projektion des zweiten Werts als `NameInfo` gezeigt wird:

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>Aliasing mit reservierten Schlüsselwörtern oder Sonderzeichen

Sie können Aliasing nicht verwenden, um einen Wert als Eigenschaftennamen mit einem Leerzeichen, einem Sonderzeichen oder einem reservierten Wort zu projizieren. Wenn Sie die Projektion eines Werts ändern möchten, z. B. in einen Eigenschaftennamen mit einem Leerzeichen, können Sie einen [JSON-Ausdruck](#json-expressions) verwenden.

Hier sehen Sie ein Beispiel:

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [SELECT-Klausel](sql-query-select.md)
- [WHERE-Klausel](sql-query-where.md)
