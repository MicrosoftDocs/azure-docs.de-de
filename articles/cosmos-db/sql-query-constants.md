---
title: SQL-Konstanten in Azure Cosmos DB
description: Erfahren Sie, wie die SQL-Abfragekonstanten in Azure Cosmos DB verwendet werden, um einen bestimmten Datenwert darzustellen.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339200"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>SQL-Abfragekonstanten in Azure Cosmos DB  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Eine Konstante, gelegentlich auch als Literal- oder Skalarwert bezeichnet, ist ein Symbol, das einen bestimmten Datenwert repräsentiert. Das Format einer Konstante ist abhängig vom Datentyp des Werts, den sie repräsentiert.  
  
 **Unterstützte skalare Datentypen:**  
  
|**Typ**|**Reihenfolge der Werte**|  
|-|-|  
|**Undefiniert**|Einzelner Wert: **undefiniert**|  
|**NULL**|Einzelner Wert: **NULL**|  
|**Boolescher Wert**|Werte: **false** , **true**.|  
|**Number**|Eine Gleitkommazahl mit doppelter Genauigkeit, IEEE 754-Standard.|  
|**String**|Eine Sequenz von null (0) oder mehr Unicode-Zeichen. Zeichenfolgen müssen in einfache oder doppelte Anführungszeichen eingeschlossen sein.|  
|**Array**|Eine Sequenz von null (0) oder mehr Elementen. Jedes Element kann einen Wert eines beliebigen skalaren Datentyps mit Ausnahme von **Undefiniert** aufweisen.|  
|**Object**|Ungeordneter Satz von null (0) oder mehr Name/Wert-Paaren. Der Name ist eine Unicode-Zeichenfolge, der Wert kann einen beliebigen skalaren Datentyp mit Ausnahme von **Undefiniert** aufweisen.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Argumente
  
* `<undefined_constant>; Undefined`  
  
  Stellt einen undefinierten Wert vom Typ „Undefiniert“ dar.  
  
* `<null_constant>; null`  
  
  Stellt einen **NULL** -Wert des Typs **NULL** dar.  
  
* `<boolean_constant>`  
  
  Stellt eine Konstante des Typs „Boolesch“ dar.  
  
* `false`  
  
  Stellt einen **false** -Wert des Typs „Boolesch“ dar.  
  
* `true`  
  
  Stellt einen **true** -Wert des Typs „Boolesch“ dar.  
  
* `<number_constant>`  
  
  Stellt eine Konstante dar.  
  
* `decimal_literal`  
  
  Dezimalliterale sind Zahlen, die mithilfe von Dezimal- oder wissenschaftlicher Schreibweise dargestellt werden.  
  
* `hexadecimal_literal`  
  
  Hexadezimalliterale sind Zahlen, die mit Präfix „0x“ gefolgt von einer oder mehreren hexadezimalen Ziffern dargestellt werden.  
  
* `<string_constant>`  
  
  Stellt eine Konstante des Typs „String“ dar.  
  
* `string _literal`  
  
  Zeichenfolgenliterale sind Unicode-Zeichenfolgen, die durch eine Sequenz aus null oder mehr Unicode-Zeichen oder Escapesequenzen dargestellt werden. Zeichenfolgenliterale werden in einfache Anführungszeichen (Apostrophen: ') oder doppelte Anführungszeichen (Anführungszeichen: ") eingeschlossen.  
  
  Folgende Escapesequenzen sind zulässig:  
  
|**Escapesequenz**|**Beschreibung**|**Unicode-Zeichen**|  
|-|-|-|  
|\\'|Apostroph (')|U+0027|  
|\\"|Anführungszeichen (")|U+0022|  
|\\\ |umgekehrter Schrägstrich (\\)|U+005C|  
|\\/|Schrägstrich (/)|U+002F|  
|\b|Rücktaste|U+0008|  
|\f|Seitenvorschub|U+000C|  
|\n|Zeilenvorschub|U+000A|  
|\r|Wagenrücklauf|U+000D|  
|\t|Tabulatorzeichen|U+0009|  
|\uXXXX|Ein Unicode-Zeichen, das durch 4 Hexadezimalstellen definiert wird.|U+XXXX|  

## <a name="next-steps"></a>Nächste Schritte

- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdaten](modeling-data.md)
