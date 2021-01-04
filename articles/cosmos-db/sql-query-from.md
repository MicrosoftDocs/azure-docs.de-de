---
title: FROM-Klausel in Azure Cosmos DB
description: Erfahren Sie mehr über die SQL-Syntax, und untersuchen Sie ein Beispiel für die FROM-Klausel für Azure Cosmos DB. In diesem Artikel werden auch Beispiele für die Bereichsergebnisse und das Abrufen von untergeordneten Elementen mithilfe der FROM-Klausel gezeigt.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 3844a628a8e3773821736324bfdaab3f64ff9da5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336249"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM-Klausel in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die FROM-Klausel (`FROM <from_specification>`) ist optional, es sei denn, die Quelle wird später in der Abfrage gefiltert oder projiziert. Eine Abfrage wie `SELECT * FROM Families` erstellt eine Auflistung für den gesamten `Families`-Container. Sie können auch den Sonderbezeichner „ROOT“ für den Container anstelle des Containernamens verwenden.

Die `FROM`-Klausel erzwingt die folgenden Regeln pro Abfrage:

* Der Container kann Aliase enthalten, z.B. `SELECT f.id FROM Families AS f` oder einfach `SELECT f.id FROM Families f`. Hier ist `f` der Alias für `Families`. AS ist ein optionales Schlüsselwort, das als [Alias](sql-query-working-with-json.md#aliasing) für den Bezeichner fungiert.  

* Nach der Aliasverwendung kann der ursprüngliche Name der Quelle nicht mehr gebunden werden. `SELECT Families.id FROM Families f` ist beispielsweise syntaktisch ungültig, da der Bezeichner `Families` durch einen Alias ersetzt wurde und nicht mehr aufgelöst werden kann.  

* Alle referenzierten Eigenschaften müssen vollqualifiziert sein, um bei Fehlen einer strikten Schemaverwendung mehrdeutige Bindungen zu vermeiden. Beispielsweise ist `SELECT id FROM Families f` syntaktisch ungültig, da die Eigenschaft `id` nicht gebunden ist.

## <a name="syntax"></a>Syntax
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=
        ROOT
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argumente
  
- `<from_source>`  
  
  Gibt eine Datenquelle mit oder ohne Alias an. Wenn der Alias nicht angegeben wird, wird er mithilfe der folgenden Regeln aus dem `<container_expression>` abgeleitet:  
  
-  Wenn der Ausdruck „container_name“ ist, wird „container_name“ als Alias verwendet.  
  
-  Wenn der Ausdruck `<container_expression>` ist, wird „property_name“ als Alias verwendet. Wenn der Ausdruck „container_name“ ist, wird „container_name“ als Alias verwendet.  
  
- AS `input_alias`  
  
  Gibt an, dass `input_alias` ein Satz von Werten ist, die von dem zugrunde liegenden Containerausdruck zurückgegeben werden.  
 
- `input_alias` IN  
  
  Gibt an, dass `input_alias` den Satz von Werten darstellen soll, die durch Iteration in allen Arrayelementen jedes Arrays gewonnen wurden, das vom zugrunde liegenden Containerausdruck zurückgegeben werden. Jeder vom zugrunde liegenden Containerausdruck zurückgegebene Wert, der kein Array ist, wird ignoriert.  
  
- `<container_expression>`  
  
  Gibt den Containerausdruck an, der zum Abrufen der Dokumente verwendet wird.  
  
- `ROOT`  
  
  Gibt an, dass das Dokument vom standardmäßigen, derzeit verbundenen Container abgerufen werden soll.  
  
- `container_name`  
  
  Gibt an, dass das Dokument vom bereitgestellten Container abgerufen werden soll. Der Name des Containers muss mit dem Namen des aktuell verbundenen Containers übereinstimmen.  
  
- `input_alias`  
  
  Gibt an, dass das Dokument von der anderen, durch den bereitgestellten Alias definierten Quelle abgerufen werden sollte.  
  
- `<container_expression> '.' property_name`  
  
  Gibt an, dass das Dokument durch Zugriff auf die Eigenschaft `property_name` abgerufen werden soll.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Gibt an, dass das Dokument durch den Zugriff auf die Eigenschaft `property_name` oder das Arrayelement „array_index“ für alle Dokumente abgerufen werden soll, die der angegebene Containerausdruck abruft.  
  
## <a name="remarks"></a>Bemerkungen
  
Alle in den `<from_source>(`s) bereitgestellten oder abgeleiteten Aliase müssen eindeutig sein. Die Syntax `<container_expression>.`property_name ist identisch mit `<container_expression>' ['"property_name"']'`. Die letztgenannte Syntax kann jedoch verwendet werden, wenn ein Eigenschaftenname Nicht-ID-Zeichen enthält.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Behandeln fehlender Eigenschaften, fehlender Arrayelemente und undefinierter Werte
  
Wenn ein Containerausdruck auf Eigenschaften oder Arrayelemente zugreift, und der Wert nicht vorhanden ist, wird dieser Wert ignoriert und nicht weiterverarbeitet.  
  
### <a name="container-expression-context-scoping"></a>Kontextbereich des Containerausdrucks  
  
Ein Containerausdruck kann container- oder dokumentbezogen sein:  
  
- Ein Ausdruck ist containerbezogen, wenn die zugrunde liegende Quelle des Containerausdrucks entweder „ROOT“ oder `container_name` lautet. Ein solcher Ausdruck stellt eine Reihe von Dokumenten dar, die direkt aus dem Container abgerufen werden, und ist nicht von der Verarbeitung anderer Containerausdrücke abhängig.  
  
- Ein Ausdruck ist dokumentbezogen, wenn die zugrunde liegende Quelle des Containerausdrucks `input_alias` ist und zuvor in der Abfrage eingeführt wurde. Ein solcher Ausdruck stellt eine Reihe von Dokumenten dar, die durch das Auswerten des Containerausdrucks in den einzelnen Dokumenten gewonnen werden, die zu dem Satz gehören, der dem Aliascontainer zugeordnet ist. Das Resultset ist eine Vereinigung von Sätzen, die durch die Auswertung des Containerausdrucks in jedem Dokument im zugrunde liegenden Satz gewonnen werden.

## <a name="examples"></a>Beispiele

### <a name="get-subitems-by-using-the-from-clause"></a>Abrufen von Unterelementen mit der FROM-Klausel

Mit der FROM-Klausel kann die Quelle auf eine kleinere Teilmenge reduziert werden. Um nur eine Teilstruktur in jedem Element aufzulisten, kann der Unterstamm der Teilstruktur zur Quelle werden, wie es im folgenden Beispiel gezeigt wird:

```sql
    SELECT *
    FROM Families.children
```

Die Ergebnisse sind:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Das oben gezeigte Beispiel verwendet ein Array als Quelle. Sie können aber auch ein Objekt als Quelle verwenden. Die Abfrage zieht jeden gültigen, definierten JSON-Wert in der Quelle für die Aufnahme in das Ergebnis in Betracht. Im folgenden Beispiel werden `Families` ohne `address.state`-Wert ausgeschlossen.

```sql
    SELECT *
    FROM Families.address.state
```

Die Ergebnisse sind:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [SELECT-Klausel](sql-query-select.md)
- [WHERE-Klausel](sql-query-where.md)
