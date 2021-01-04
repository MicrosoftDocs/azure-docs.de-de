---
title: RegexMatch in der Abfragesprache für Azure Cosmos DB
description: Hier erfahren Sie mehr über die SQL-Systemfunktion RegexMatch in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341636"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Diese Funktion stellt Funktionalität für reguläre Ausdrücke bereit. Bei regulären Ausdrücken handelt es sich um eine präzise und flexible Notation zum Suchen von Textmustern. Azure Cosmos DB verwendet [mit Perl kompatible reguläre Ausdrücke (Perl Compatible Regular Expressions, PCRE)](http://www.pcre.org/). 

## <a name="syntax"></a>Syntax
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argumente
  
*str_expr1*  
   Ist der Zeichenfolgenausdruck, der durchsucht werden soll.  
  
*str_expr2*  
   Dies ist der reguläre Ausdruck.

*str_expr3*  
   Dies ist die Zeichenfolge aus ausgewählten Modifizierern, die mit dem regulären Ausdruck verwendet werden sollen. Dieser Zeichenfolgenwert ist optional. Wenn Sie RegexMatch ohne Modifizierer ausführen möchten, können Sie entweder eine leere Zeichenfolge hinzufügen oder die Zeichenfolge ganz auslassen. 

Auf der verlinkten Website finden Sie weitere Informationen zur [Syntax zum Erstellen von regulären Ausdrücken in Perl](https://perldoc.perl.org/perlre). 

Azure Cosmos DB unterstützt die folgenden vier Modifizierer:

| Modifizierer | BESCHREIBUNG |
| ------ | ----------- |
| `m` | Hiermit wird der zu durchsuchende Zeichenfolgenausdruck als mehrere Zeilen behandelt. Ohne diese Option gleichen „^“ und „$“ den Anfang oder das Ende der Zeichenfolge ab und nicht jede einzelne Zeile. |
| `s` | Hiermit legen Sie fest, dass „.“ mit allen Zeichen übereinstimmt, einschließlich Neue-Zeile-Zeichen. | 
| `i` | Hiermit wird beim Musterabgleich die Groß-/Kleinschreibung ignoriert. |
| `x` | Hiermit werden alle Leerzeichen ignoriert. |

## <a name="return-types"></a>Rückgabetypen
  
  Gibt einen booleschen Ausdruck zurück. Die Funktion gibt „Undefined“ (Nicht definiert) zurück, wenn der zu durchsuchende Zeichenfolgenausdruck, der reguläre Ausdruck oder die ausgewählten Modifizierer ungültig sind.
  
## <a name="examples"></a>Beispiele
  
Im folgenden einfachen RegexMatch-Beispiel wird die Zeichenfolge „abcd“ mit regulären Ausdrücken mit einigen verschiedenen Modifizierern abgeglichen.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Hier ist das Resultset.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Mit RegexMatch können Sie Metazeichen verwenden, um komplexere Durchsuchungen von Zeichenfolgen durchzuführen, die andernfalls mit den Systemfunktionen StartsWith, EndsWith, Contains und StringEquals nicht möglich wären. Hier einige weitere Beispiele, die Sie für das Dataset zu Nährwerten ausführen können, das über den [Azure Cosmos DB-Abfrageplayground](https://www.documentdb.com/sql/demo) verfügbar ist. 

> [!NOTE] 
> Wenn Sie ein Metazeichen in einem regulären Ausdruck verwenden müssen und nicht möchten, dass es eine besondere Bedeutung hat, sollten Sie dieses Zeichen mit dem Escapezeichen `\` versehen.

**Überprüfen von Elementen mit einer Beschreibung, die das Wort „salt“ genau einmal enthält:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Überprüfen von Elementen mit einer Beschreibung, die eine Zahl zwischen 0 und 99 enthält:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Überprüfen von Elementen mit einer Beschreibung, die Wörter mit vier Buchstaben enthält, die mit „S“ oder „s“ beginnen:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Bemerkungen

Diese Systemfunktion profitiert von einem [Bereichsindex](index-policy.md#includeexclude-strategy), wenn der reguläre Ausdruck in StartsWith-, EndsWith-, Contains- oder StringEquals-Systemfunktionen unterteilt werden kann.

## <a name="next-steps"></a>Nächste Schritte

- [Zeichenfolgenfunktionen in Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktionen in Azure Cosmos DB](sql-query-system-functions.md)
- [Einführung in Azure Cosmos DB](introduction.md)
