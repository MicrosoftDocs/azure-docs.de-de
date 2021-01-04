---
title: Benutzerdefinierte Funktionen (UDFs) in Azure Cosmos DB
description: Erfahren Sie mehr zu benutzerdefinierten Funktionen in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546334"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Benutzerdefinierte Funktionen (UDFs) in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die SQL-API bietet Unterstützung für benutzerdefinierte Funktionen (UDFs). Bei skalaren UDFs können null oder mehrere Argumente übergeben und ein einziges Argumentergebnis zurückgegeben werden. Die API überprüft, ob jedes Argument ein gültiger JSON-Wert ist.  

## <a name="udf-use-cases"></a>Anwendungsfälle für benutzerdefinierte Funktionen

Die API erweitert die SQL-Syntax, um benutzerdefinierte Anwendungslogik mit benutzerdefinierten Funktionen (UDFs) zu unterstützen. Sie können UDFs mit der SQL-API registrieren und in SQL-Abfragen referenzieren. Im Gegensatz zu gespeicherten Prozeduren und Triggern sind benutzerdefinierte Funktionen schreibgeschützt.

Mithilfe von benutzerdefinierten Funktionen können Sie die Abfragesprache von Azure Cosmos DB erweitern. Benutzerdefinierte Funktionen sind eine hervorragende Möglichkeit, um komplexe Geschäftslogik in einer Abfrage auszudrücken.

In den folgenden Fällen wird jedoch von einer Verwendung benutzerdefinierter Funktionen abgeraten:

- In Azure Cosmos DB ist bereits eine äquivalente [Systemfunktion](sql-query-system-functions.md) vorhanden. Systemfunktionen benötigen stets weniger RUs als eine äquivalente benutzerdefinierte Funktion.
- Die benutzerdefinierte Funktion ist der einzige Filter in der `WHERE`-Klausel Ihrer Abfrage. Benutzerdefinierte Funktionen machen keinen Gebrauch vom Index, deshalb müssen zum Auswerten der benutzerdefinierten Funktion Dokumente geladen werden. Die Kombination von zusätzlichen Filterprädikaten, die den Index verwenden, mit einer benutzerdefinierten Funktion in der `WHERE`-Klausel verringert die Anzahl von Dokumenten, die von der benutzerdefinierten Funktion verarbeitet werden.

Wenn Sie dieselbe benutzerdefinierte Funktion mehrfach in einer Abfrage verwenden müssen, sollten Sie die benutzerdefinierte Funktion in einer [Unterabfrage](sql-query-subquery.md#evaluate-once-and-reference-many-times) referenzieren. So können Sie einen JOIN-Ausdruck verwenden, um die benutzerdefinierte Funktion einmalig auszuwerten und viele Male zu referenzieren.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird eine UDF unter einem Elementcontainer in der Cosmos-Datenbank registriert. Im Beispiel wird eine UDF mit dem Namen `REGEX_MATCH` erstellt. Es werden die beiden JSON-Zeichenfolgenwerte `input` und `pattern` akzeptiert, und es wird überprüft, ob der erste Wert mit dem im zweiten Wert angegebenen Muster übereinstimmt. Hierfür wird die JavaScript-Funktion `string.match()` verwendet.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Verwenden Sie nun diese UDF in einer Abfrageprojektion. Sie müssen UDFs mit dem Präfix `udf.` unter Berücksichtigung der Groß-/Kleinschreibung qualifizieren, wenn sie innerhalb von Abfragen aufgerufen werden.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Die Ergebnisse sind:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Sie können die UDF, die mit dem Präfix `udf.` qualifiziert ist, innerhalb eines Filters verwenden, wie es im folgenden Beispiel gezeigt wird:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Die Ergebnisse sind:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

UDFs sind gültige skalare Ausdrücke, die Sie sowohl in Projektionen als auch in Filtern verwenden können.

Zum Erweitern des Funktionsumfangs von UDFs sehen Sie hier ein weiteres Beispiel mit konditionaler Logik:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Hier sehen Sie ein Verwendungsbeispiel für die UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Die Ergebnisse sind:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Wenn die von den UDF-Parametern referenzierten Eigenschaften nicht im JSON-Wert verfügbar sind, wird der Parameter als „Undefined“ betrachtet und der UDF-Aufruf übersprungen. Wenn das Ergebnis der UDF „Undefined“ ist, wird es ebenfalls nicht in das Ergebnis aufgenommen.

Wie im vorherigen Beispiel gezeigt, vereinen UDFs den Funktionsumfang der JavaScript-Sprache mit der SQL-API. UDFs bieten eine umfassende, programmierbare Schnittstelle für komplexe verfahrensorientierte und konditionale Logik mithilfe der Fähigkeiten der integrierten JavaScript-Laufzeit. Die SQL-API stellt den UDFs die Argumente für jedes Quellelemente in der aktuellen Ausführungsphase der WHERE- oder SELECT-Klausel bereit. Das Ergebnis wird nahtlos in die Gesamt-Ausführungspipeline aufgenommen. UDFs sind also hervorragende Werkzeuge, um komplexe Geschäftslogik als Teil der Abfragen zu integrieren.

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Cosmos DB](introduction.md)
- [System functions (Systemfunktionen)](sql-query-system-functions.md)
- [Aggregate](sql-query-aggregate-functions.md)