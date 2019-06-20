---
title: SQL-Sprachsyntax in Azure Cosmos DB
description: In diesem Artikel werden die in Azure Cosmos DB verwendete Syntax der SQL-Abfragesprache sowie verschiedene in dieser Sprache verfügbare Operatoren und Schlüsselwörter erläutert.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 70de178df86a4b202298eda63b0f59cb7bc96281
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237830"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>SQL-Sprachreferenz für Azure Cosmos DB 

Azure Cosmos DB unterstützt Abfragen von Dokumenten mithilfe einer vertrauten, SQL-ähnlichen (Structured Query Language, strukturierte Abfragesprache) Grammatik für hierarchische JSON-Dokumente, ohne dass ein explizites Schema oder die Erstellung sekundärer Indizes erforderlich ist. In diesem Artikel ist die in SQL-API-Konten verwendete Syntax der SQL-Abfragesprache dokumentiert. Eine exemplarische Vorgehensweise mit SQL-Beispielabfragen finden Sie unter [SQL-Abfragebeispiele für Azure Cosmos DB](how-to-sql-query.md).  
  
Besuchen Sie auch den [Abfrageplayground](https://www.documentdb.com/sql/demo). Dort können Sie Cosmos DB testen und SQL-Abfragen für ein Beispieldataset ausführen.  
  
## <a name="select-query"></a>SELECT-Abfrage  
Jede Abfrage besteht aus einer SELECT-Klausel und optionalen FROM- und WHERE-Klauseln nach ANSI-SQL-Standards. In jeder Abfrage wird für gewöhnlich die Quelle in der FROM-Klausel aufgezählt. Anschließend wird der Filter in der WHERE-Klausel auf die Quelle angewendet, um eine Teilmenge der JSON-Dokumente abrufen. Zuletzt wird die SELECT-Klausel verwendet, um die abgefragten JSON-Werte in die ausgewählte Liste zu projizieren. Beispiele finden Sie unter [Beispiele für die SELECT-Abfrage](how-to-sql-query.md#SelectClause).
  
**Syntax**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ] 
    [ OFFSET <offset_amount> LIMIT <limit_amount>]
```  
  
 **Anmerkungen**  
  
 Weitere Informationen zu den einzelnen Klauseln finden Sie in den folgenden Abschnitten:  
  
-   [SELECT-Klausel](#bk_select_query)    
-   [FROM-Klausel](#bk_from_clause)    
-   [WHERE-Klausel](#bk_where_clause)    
-   [ORDER BY-Klausel](#bk_orderby_clause)  
-   [OFFSET LIMIT-Klausel](#bk_offsetlimit_clause)

  
Die Klauseln in der SELECT-Anweisung müssen wie oben gezeigt sortiert werden. Jede der optionalen Klauseln kann ausgelassen werden. Aber wenn optionale Klauseln verwendet werden, müssen sie in der richtigen Reihenfolge stehen.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>Logische Verarbeitungsreihenfolge der SELECT-Anweisung  
  
Die Klauseln werden in folgender Reihenfolge verarbeitet:  

1.  [FROM-Klausel](#bk_from_clause)  
2.  [WHERE-Klausel](#bk_where_clause)  
3.  [ORDER BY-Klausel](#bk_orderby_clause)  
4.  [SELECT-Klausel](#bk_select_query)
5.  [OFFSET LIMIT-Klausel](#bk_offsetlimit_clause)

Beachten Sie, dass sich dies von der Reihenfolge unterscheidet, in der sie in der Syntax auftreten. Die Reihenfolge ist, dass alle von einer verarbeiteten Klausel eingeführten neuen Symbole sichtbar sind und in Klauseln verwendet werden können, die zu einem späteren Zeitpunkt verarbeitet werden. So können z.B. WHERE- und SELECT-Klauseln auf Aliase zugreifen, die in einer FROM-Klausel deklariert werden.  

### <a name="whitespace-characters-and-comments"></a>Leerzeichen und Kommentare  

Alle Leerzeichen, die nicht Teil einer Zeichenfolge/eines Bezeichners in Anführungszeichen sind, sind nicht Bestandteil der Sprachgrammatik und werden bei der Analyse ignoriert.  

Die Abfragesprache unterstützt Kommentare im T-SQL-Stil wie  

-   SQL-Anweisung `-- comment text [newline]`  

Obwohl Leerzeichen und Kommentare in der Grammatik keine Bedeutung haben, müssen sie zum Trennen von Tokens verwendet werden. Beispiel: `-1e5` ist ein einzelnes Zahlentoken, während `: – 1 e5` aus dem Minuszeichentoken gefolgt von der Zahl 1 und dem Bezeichner „e5“ zusammengesetzt ist.  

##  <a name="bk_select_query"></a> SELECT-Klausel  
Die Klauseln in der SELECT-Anweisung müssen wie oben gezeigt sortiert werden. Jede der optionalen Klauseln kann ausgelassen werden. Aber wenn optionale Klauseln verwendet werden, müssen sie in der richtigen Reihenfolge stehen. Beispiele finden Sie unter [Beispiele für die SELECT-Abfrage](how-to-sql-query.md#SelectClause).

**Syntax**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumente**  
  
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

  Ausdruck, der den zu berechnenden Wert darstellt. Weitere Informationen finden Sie im Abschnitt [Skalarausdrücke](#bk_scalar_expressions).  
  
**Anmerkungen**  
  
Die `SELECT *`-Syntax ist nur gültig, wenn die FROM-Klausel genau einen Alias deklariert hat. `SELECT *` bietet eine Identitätsprojektion, die hilfreich sein kann, wenn keine Projektion erforderlich ist. SELECT * ist nur gültig, wenn die FROM-Klausel angegeben und nur eine einzelne Eingabequelle eingeführt ist.  
  
Sowohl `SELECT <select_list>` als auch `SELECT *` sind sogenannter „syntaktischer Zucker“ und können alternativ durch das Verwenden einfacher SELECT-Anweisungen ausgedrückt werden (s. unten).  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   entspricht:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   entspricht:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Weitere Informationen**  
  
[Skalarausdrücke](#bk_scalar_expressions)  
[SELECT-Klausel](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM-Klausel  
Gibt die Quelle oder verknüpfte Quellen an. Die FROM-Klausel ist optional, es sei denn, die Quelle wird später in der Abfrage gefiltert oder projiziert. Mit dieser Klausel wird die Datenquelle angegeben, auf der die Abfrage operiert. Normalerweise dient der gesamte Container als Quelle, doch Sie können stattdessen auch nur eine Teilmenge des Containers angeben. Wird diese Klausel nicht angegeben, werden andere Klauseln weiterhin so ausgeführt, als würde die FROM-Klausel ein einzelnes Dokument bereitstellen. Beispiele finden Sie unter [Beispiele für die FROM-Klausel](how-to-sql-query.md#FromClause).
  
**Syntax**  
  
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
  
**Argumente**  
  
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
  
- `<container_expression> '.' property_`  
  
  Gibt an, dass das Dokument durch den Zugriff auf die Eigenschaft `property_name` oder das Arrayelement „array_index“ für alle Dokumente abgerufen werden soll, die der angegebene Containerausdruck abruft.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Gibt an, dass das Dokument durch den Zugriff auf die Eigenschaft `property_name` oder das Arrayelement „array_index“ für alle Dokumente abgerufen werden soll, die der angegebene Containerausdruck abruft.  
  
**Anmerkungen**  
  
Alle in den `<from_source>(`s) bereitgestellten oder abgeleiteten Aliase müssen eindeutig sein. Die Syntax `<container_expression>.`property_name ist identisch mit `<container_expression>' ['"property_name"']'`. Die letztgenannte Syntax kann jedoch verwendet werden, wenn ein Eigenschaftenname Nicht-ID-Zeichen enthält.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Behandeln fehlender Eigenschaften, fehlender Arrayelemente und undefinierter Werte
  
Wenn ein Containerausdruck auf Eigenschaften oder Arrayelemente zugreift, und der Wert nicht vorhanden ist, wird dieser Wert ignoriert und nicht weiterverarbeitet.  
  
### <a name="container-expression-context-scoping"></a>Kontextbereich des Containerausdrucks  
  
Ein Containerausdruck kann container- oder dokumentbezogen sein:  
  
-   Ein Ausdruck ist containerbezogen, wenn die zugrunde liegende Quelle des Containerausdrucks entweder „ROOT“ oder `container_name` lautet. Ein solcher Ausdruck stellt eine Reihe von Dokumenten dar, die direkt aus dem Container abgerufen werden, und ist nicht von der Verarbeitung anderer Containerausdrücke abhängig.  
  
-   Ein Ausdruck ist dokumentbezogen, wenn die zugrunde liegende Quelle des Containerausdrucks `input_alias` ist und zuvor in der Abfrage eingeführt wurde. Ein solcher Ausdruck stellt eine Reihe von Dokumenten dar, die durch das Auswerten des Containerausdrucks in den einzelnen Dokumenten gewonnen werden, die zu dem Satz gehören, der dem Aliascontainer zugeordnet ist.  Das Resultset ist eine Vereinigung von Sätzen, die durch die Auswertung des Containerausdrucks in jedem Dokument im zugrunde liegenden Satz gewonnen werden.  
  
### <a name="joins"></a>Joins 
  
Im aktuellen Release unterstützt Cosmos DB innere Joins. Weitere Verknüpfungsfunktionen sind in Planung. 

Aus inneren Verknüpfungen resultiert ein komplettes Kreuzungsprodukt der an der Verknüpfung beteiligten Sätze. Das Ergebnis einer N-Way-Verknüpfung ist ein Satz von N-Element-Tupeln, wo jeder Wert im Tupel dem an der Verknüpfung beteiligten Aliassatz zugeordnet ist, und der Zugriff auf den Wert durch Verweis auf diesen Alias in anderen Klauseln erfolgen kann. Beispiele finden Sie unter [Beispiele für das JOIN-Schlüsselwort](how-to-sql-query.md#Joins).
  
Die Auswertung der Verknüpfung hängt vom Kontextbereich der beteiligten Sätze ab:  
  
- Ein Join zwischen Containersatz A und containerbezogenem Satz B führt zu einem Kreuzprodukt aller Elemente in den Sätzen A und B.
  
- Eine Verknüpfung zwischen Satz A und dokumentbezogenem Satz B führt zu einer Vereinigung aller durch die Auswertung des dokumentbezogenen Satzes B für jedes Dokument aus Satz A gewonnenen Sätze.  
  
  Im aktuellen Release wird höchstens ein containerbezogener Ausdruck vom Abfrageprozessor unterstützt.  
  
### <a name="examples-of-joins"></a>Beispiele für Verknüpfungen  
  
Betrachten Sie die folgende FROM-Klausel: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Jede Quelle definiert `input_alias1, input_alias2, …, input_aliasN`. Diese FROM-Klausel gibt einen Satz von N-Tupeln (Tupel mit N-Werten) zurück. Jedes Tupel enthält Werte, die durch Iteration aller Containeraliase über deren jeweilige Sätze entstanden sind.  
  
**Beispiel 1** – zwei Quellen  
  
- `<from_source1>` ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- `<from_source2>` ist dokumentbezogen, verweist auf „input_alias1“ und stellt folgende Sätze dar:  
  
    {1, 2} für `input_alias1 = A,`  
  
    {3} für `input_alias1 = B,`  
  
    {4, 5} für `input_alias1 = C,`  
  
- Die FROM-Klausel `<from_source1> JOIN <from_source2>` führt zu folgenden Tupeln:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Beispiel 2** – drei Quellen  
  
- `<from_source1>` ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- `<from_source2>` ist dokumentbezogen, verweist auf `input_alias1` und stellt folgende Sätze dar:  
  
    {1, 2} für `input_alias1 = A,`  
  
    {3} für `input_alias1 = B,`  
  
    {4, 5} für `input_alias1 = C,`  
  
- `<from_source3>` ist dokumentbezogen, verweist auf `input_alias2` und stellt folgende Sätze dar:  
  
    {100, 200} für `input_alias2 = 1,`  
  
    {300} für `input_alias2 = 3,`  
  
- Die FROM-Klausel `<from_source1> JOIN <from_source2> JOIN <from_source3>` führt zu folgenden Tupeln:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Fehlende Tupel für andere Werte von `input_alias1`, `input_alias2`, für die `<from_source3>` keine Werte zurückgegeben hat.  
  
**Beispiel 3** – drei Quellen  
  
- <from_source1> ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- `<from_source1>` ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- <from_source2> ist dokumentbezogen, verweist auf „input_alias1“ und stellt folgende Sätze dar:  
  
    {1, 2} für `input_alias1 = A,`  
  
    {3} für `input_alias1 = B,`  
  
    {4, 5} für `input_alias1 = C,`  
  
- `<from_source3>` ist auf `input_alias1` bezogen und stellt folgende Sätze dar:  
  
    {100, 200} für `input_alias2 = A,`  
  
    {300} für `input_alias2 = C,`  
  
- Die FROM-Klausel `<from_source1> JOIN <from_source2> JOIN <from_source3>` führt zu folgenden Tupeln:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Dies führt zu einem Kreuzprodukt zwischen `<from_source2>` und `<from_source3>`, da beide auf die gleiche `<from_source1>` bezogen sind.  Dies führt zu 4 (2 x 2) Tupeln mit dem Wert A, 0 Tupeln mit dem Wert B (1 x 0) und 2 (2 x 1) Tupeln mit dem Wert C.  
  
**Weitere Informationen**  
  
 [SELECT-Klausel](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> WHERE-Klausel  
 Gibt die Suchbedingung für die Dokumente an, die von der Abfrage zurückgegeben werden. Beispiele finden Sie unter [Beispiele für die WHERE-Klausel](how-to-sql-query.md#WhereClause).
  
 **Syntax**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumente**  
  
- `<filter_condition>`  
  
   Gibt die Bedingung an, die erfüllt sein muss, damit die Dokumente zurückgegeben werden.  
  
- `<scalar_expression>`  
  
   Ausdruck, der den zu berechnenden Wert darstellt. Weitere Informationen finden Sie im Abschnitt [Skalarausdrücke](#bk_scalar_expressions).  
  
  **Anmerkungen**  
  
  Damit das Dokument zurückgegeben wird, muss ein als Filterbedingung angegebener Ausdruck mit „true“ ausgewertet werden. Nur der boolesche Wert TRUE erfüllt die Bedingung, jeder andere Wert – „undefined“, „NULL“, „FALSE“, „Number“, „Array“ oder „Object“ – erfüllt die Bedingung nicht.  
  
##  <a name="bk_orderby_clause"></a> ORDER BY-Klausel  
 Gibt die Sortierreihenfolge für die von der Abfrage zurückgegebenen Ergebnisse an. Beispiele finden Sie unter [Beispiele für die ORDER BY-Klausel](how-to-sql-query.md#OrderByClause).
  
 **Syntax**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
  
```  

 **Argumente**  
  
- `<sort_specification>`  
  
   Gibt eine Eigenschaft oder einen Ausdruck an, wonach das Resultset der Abfrage sortiert werden soll. Eine Sortierspalte kann als Name oder Eigenschaftenalias angegeben werden.  
  
   Mehrere Eigenschaften können angegeben werden. Namen von Eigenschaften müssen eindeutig sein. Die Sequenz der Sortiereigenschaften in der ORDER BY-Klausel definiert die Anordnung des sortierten Resultsets. Dies bedeutet: Das Resultset wird nach der ersten Eigenschaft sortiert, und dann wird diese sortierte Liste nach der zweiten Eigenschaft sortiert usw.  
  
   Die Eigenschaftennamen, auf die in der ORDER BY-Klausel verwiesen wird, müssen entweder einer Eigenschaft in der Auswahlliste oder einer Eigenschaft entsprechen, die in der Sammlung definiert ist, die in der FROM-Klausel eindeutig angegeben ist.  
  
- `<sort_expression>`  
  
   Hiermit wird mindestens eine Eigenschaft oder ein Ausdruck festgelegt, nach dem die Abfrageergebnisse sortiert werden.  
  
- `<scalar_expression>`  
  
   Weitere Informationen finden Sie im Abschnitt [Skalarausdrücke](#bk_scalar_expressions).  
  
- `ASC | DESC`  
  
   Gibt an, dass die Werte in der angegebenen Spalte in aufsteigender oder absteigender Reihenfolge sortiert werden sollen. ASC sortiert vom niedrigsten Wert zum höchsten Wert. DESC sortiert vom höchsten Wert zum niedrigsten Wert. ASC ist die Standardsortierreihenfolge. NULL-Werte werden als die niedrigsten möglichen Werte behandelt.  
  
  **Anmerkungen**  
  
   Die ORDER BY-Klausel erfordert, dass die Indizierungsrichtlinie einen Index für die Felder enthält, die sortiert werden. Die Abfragelaufzeit von Azure Cosmos DB unterstützt die Sortierung für einen Eigenschaftennamen und nicht für berechnete Eigenschaften. Azure Cosmos DB unterstützt mehrere ORDER BY-Eigenschaften. Zum Ausführen einer Abfrage mit mehreren ORDER BY-Eigenschaften sollten Sie einen [zusammengesetzten Index](index-policy.md#composite-indexes) für die Felder definieren, die sortiert werden.


##  <a name=bk_offsetlimit_clause></a> OFFSET LIMIT-Klausel

Mit dieser Klausel wird die Anzahl der übersprungenen und der zurückgegebenen Elemente festgelegt. Beispiele finden Sie unter [Beispiele für die OFFSET LIMIT-Klausel](how-to-sql-query.md#OffsetLimitClause).
  
 **Syntax**  
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
 **Argumente**  
 
- `<offset_amount>`

   Mit diesem Argument wird die ganzzahlige Anzahl von Elementen festgelegt, die die Abfrageergebnisse überspringen sollen.


- `<limit_amount>`
  
   Mit diesem Argument wird die ganzzahlige Anzahl von Elementen festgelegt, die die Abfrageergebnisse enthalten sollen.

  **Anmerkungen**  
  
  In der OFFSET LIMIT-Klausel müssen sowohl für OFFSET als auch für LIMIT Werte angegeben werden. Wenn eine optionale `ORDER BY`-Klausel verwendet wird, wird das Resultset durch Überspringen der sortierten Werte erzeugt. Andernfalls gibt die Abfrage eine feste Reihenfolge der Werte zurück.

##  <a name="bk_scalar_expressions"></a> Skalarausdrücke  
 Ein skalarer Ausdruck ist eine Kombination aus Symbolen und Operatoren, die ausgewertet werden können, um einen einzelnen Wert zu erhalten. Einfache Ausdrücke können Konstanten, Eigenschaftenverweise, Arrayelementverweise, Aliasverweise oder Funktionsaufrufe sein. Einfache Ausdrücke können mit Operatoren in komplexen Ausdrücken kombiniert werden. Beispiele finden Sie unter [Beispiele für skalare Ausdrücke](how-to-sql-query.md#scalar-expressions).
  
 Weitere Informationen zu Werten, die Skalarausdrücke aufweisen können, finden Sie im Abschnitt [Konstanten](#bk_constants).  
  
 **Syntax**  
  
```sql  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumente**  
  
- `<constant>`  
  
   Stellt einen konstanten Wert dar. Details hierzu finden Sie im Abschnitt [Konstanten](#bk_constants).  
  
- `input_alias`  
  
   Stellt einen Wert dar, der von dem `input_alias` definiert wird, der in der `FROM` Klausel eingeführt wird.  
  Dieser Wert ist garantiert nicht **undefiniert** –**undefinierte** Werte in der Eingabe werden übersprungen.  
  
- `<scalar_expression>.property_name`  
  
   Stellt einen Wert der Eigenschaft eines Objekts dar. Wenn die Eigenschaft nicht vorhanden ist, oder über einen Wert, der kein Objekt ist, auf die Eigenschaft verwiesen wird, ergibt der Ausdruck den Wert **undefiniert**.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Stellt einen Wert der Eigenschaft mit dem Namen `property_name` oder ein Arrayelement mit Index `array_index` eines Objekts/Arrays dar. Wenn die Eigenschaft/der Arrayindex nicht vorhanden ist, oder über einen Wert, der kein Objekt/Array ist, auf die Eigenschaft/den Arrayindex verwiesen wird, wird der Ausdruck als undefinierter Wert ausgewertet.  
  
- `unary_operator <scalar_expression>`  
  
   Stellt einen Operator dar, der auf einen einzelnen Wert angewendet wird. Details hierzu finden Sie im Abschnitt [Operatoren](#bk_operators).  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Stellt einen Operator dar, der auf zwei Werte angewendet wird. Details hierzu finden Sie im Abschnitt [Operatoren](#bk_operators).  
  
- `<scalar_function_expression>`  
  
   Stellt einen Wert dar, der durch ein Ergebnis eines Funktionsaufrufs definiert wird.  
  
- `udf_scalar_function`  
  
   Name der benutzerdefinierten Skalarfunktion.  
  
- `builtin_scalar_function`  
  
   Name der integrierten skalaren Funktion.  
  
- `<create_object_expression>`  
  
   Stellt einen Wert dar, der durch Erstellen eines neuen Objekts mit festgelegten Eigenschaften und ihren Werten gewonnen wird.  
  
- `<create_array_expression>`  
  
   Stellt einen Wert dar, der durch Erstellen eines neuen Arrays mit festgelegten Werten als Elemente gewonnen wird.  
  
- `parameter_name`  
  
   Stellt einen Wert des angegebenen Parameternamens dar. Das erste Zeichen eines Parameternamens muss ein einzelnes „\@“ sein.  
  
  **Anmerkungen**  
  
  Beim Aufrufen einer integrierten oder benutzerdefinierten Skalarfunktion müssen alle Argumente definiert werden. Wenn eines der Argumente undefiniert ist, wird die Funktion nicht aufgerufen, und das Ergebnis ist undefiniert.  
  
  Beim Erstellen eines Objekts wird jede Eigenschaft, der ein undefinierter Wert zugewiesen ist, übersprungen und nicht in das erstellte Objekt einbezogen.  
  
  Beim Erstellen eines Arrays wird jeder Elementwert, dem ein **undefinierter** Wert zugewiesen ist, übersprungen und nicht in das erstellte Objekt einbezogen. Daraufhin nimmt das nächste definierte Element seinen Platz ein, sodass das erstellte Array keine übersprungenen Indizes aufweist.  
  
##  <a name="bk_operators"></a> Operatoren  
 Dieser Abschnitt beschreibt die unterstützten Operatoren. Jeder Operator kann genau einer Kategorie zugewiesen werden.  
  
 In der folgenden Tabelle **Operatorkategorien** finden Sie weitere Informationen zur Behandlung **undefinierter** Werte, zu Typanforderungen für Eingabewerte sowie zur Behandlung von Werten mit nicht übereinstimmenden Typen.  
  
 **Operatorkategorien:**  
  
|**Kategorie**|**Details**|  
|-|-|  
|**Arithmetisch**|Der Operator erwartet (eine) Zahl(en) als Eingabe(n). Die Ausgabe ist ebenfalls eine Zahl. Wenn eine der Eingaben **undefiniert** ist oder einen anderen Typ als „Number“ aufweist, ist das Ergebnis **undefiniert**.|  
|**Bitweise**|Der Operator erwartet (eine) 32-Bit-Ganzzahl(en) mit Vorzeichen als Eingabe(n). Die Ausgabe ist ebenfalls eine 32-Bit-Ganzzahl mit Vorzeichen.<br /><br /> Jeder nicht ganzzahlige Wert wird gerundet. Positive Werte werden abgerundet, negative Werte aufgerundet.<br /><br /> Jeder Wert, der außerhalb des 32-Bit-Ganzzahlbereichs liegt, wird unter Verwendung der letzten 32 Bits seiner Zweierkomplement-Schreibweise konvertiert.<br /><br /> Wenn eine der Eingaben **undefiniert** ist oder einen anderen Typ als „Number“ aufweist, ist das Ergebnis **undefiniert**.<br /><br /> **Hinweis:** Das oben beschriebene Verhalten ist kompatibel mit dem JavaScript-Verhalten des bitweisen Operators.|  
|**Logisch**|Der Operator erwartet (eine) boolesche Eingabe(n). Die Ausgabe ist ebenfalls boolesch.<br />Wenn eine der Eingaben **undefiniert** ist oder einen anderen Typ als „boolesch“ aufweist, ist das Ergebnis **undefiniert**.|  
|**Vergleich**|Der Operator erwartet, dass die Eingabe(n) vom gleichen Typ und nicht undefiniert ist (sind). Die Ausgabe ist ein boolescher Wert.<br /><br /> Wenn eine der Eingaben **undefiniert** ist oder sie verschiedene Typen aufweisen, ist das Ergebnis **undefiniert**.<br /><br /> In der Tabelle **Reihenfolge der Werte für den Vergleich** finden Sie weitere Informationen zur Reihenfolge der Werte.|  
|**string**|Der Operator erwartet String(s) als Eingabe(n). Die Ausgabe ist ebenfalls ein String.<br />Wenn eine der Eingaben **undefiniert** ist oder einen anderen Typ als „String“ aufweist, ist das Ergebnis **undefiniert**.|  
  
 **Unäre Operatoren:**  
  
|**Name**|**Operator**|**Details**|  
|-|-|-|  
|**Arithmetisch**|+<br /><br /> -|Gibt den Zahlenwert zurück.<br /><br /> Bitweise Negation. Gibt den negierten Zahlenwert zurück.|  
|**Bitweise**|~|Einerkomplement. Gibt ein Komplement eines Zahlenwerts zurück.|  
|**Logisch**|**NOT**|Negation. Gibt den negierten booleschen Wert zurück.|  
  
 **Binäre Operatoren:**  
  
|**Name**|**Operator**|**Details**|  
|-|-|-|  
|**Arithmetisch**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Addition.<br /><br /> Subtraktion.<br /><br /> Multiplikation.<br /><br /> Division.<br /><br /> Modulation.|  
|**Bitweise**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitweises OR.<br /><br /> Bitweises AND.<br /><br /> Bitweises XOR.<br /><br /> Verschiebung nach links.<br /><br /> Verschiebung nach rechts.<br /><br /> Nullauffüllung, Verschiebung nach rechts.|  
|**Logisch**|**AND**<br /><br /> **OR**|Logische Konjunktion. Gibt **true** zurück, wenn beide Argumente **true** sind, andernfalls **false**.<br /><br /> Logische Disjunktion. Gibt **true** zurück, wenn ein Argument **true** ist, andernfalls **false**.|  
|**Vergleich**|**=**<br /><br /> **!=, &lt;&gt;**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Gleich. Gibt **true** zurück, wenn die Argumente gleich sind, andernfalls **false**.<br /><br /> Ungleich. Gibt **true** zurück, wenn die Argumente nicht gleich sind, andernfalls **false**.<br /><br /> Größer als. Gibt **true** zurück, wenn das erste Argument größer als das zweite Argument ist, andernfalls **false**.<br /><br /> Größer als oder gleich. Gibt **true** zurück, wenn das erste Argument größer als das zweite Argument oder ihm gleich ist, andernfalls **false**.<br /><br /> Kleiner als. Gibt **true** zurück, wenn das erste Argument kleiner als das zweite Argument ist, andernfalls **false**.<br /><br /> Kleiner als oder gleich. Gibt **true** zurück, wenn das erste Argument kleiner als das zweite Argument oder ihm gleich ist, andernfalls **false**.<br /><br /> Zusammenfügen. Gibt das zweite Argument zurück, wenn das erste Argument ein **undefinierter** Wert ist.|  
|**String**|**&#124;&#124;**|Verkettung. Gibt eine Verkettung der beiden Argumente zurück.|  
  
 **Ternäre Operatoren:**  

|**Name**|**Operator**|**Details**| 
|-|-|-|  
|Ternärer Operator|?|Gibt das zweite Argument zurück, wenn die Auswertung des ersten Arguments **true** ergibt; gibt andernfalls das dritte Argument zurück.|  

  
 **Reihenfolge der Werte für den Vergleich**  
  
|**Typ**|**Reihenfolge der Werte**|  
|-|-|  
|**Undefiniert**|Nicht vergleichbar.|  
|**NULL**|Einzelner Wert: **NULL**|  
|**Number**|Natürliche reelle Zahl.<br /><br /> Der negative Unendlichkeitswert ist kleiner als jeder andere Number-Wert.<br /><br /> Der positive Unendlichkeitswert ist größer als jeder andere Number-Wert. **NaN**-Wert ist nicht vergleichbar. Vergleich mit **NaN** führt zu **undefiniertem** Wert.|  
|**String**|Lexikografische Reihenfolge.|  
|**Array**|Keine Reihenfolge, aber gleichberechtigt.|  
|**Object**|Keine Reihenfolge, aber gleichberechtigt.|  
  
 **Anmerkungen**  
  
 In Cosmos DB sind die Typen von Werten oft nicht bekannt, bis sie aus der Datenbank abgerufen werden. Um die effiziente Ausführung von Abfragen zu ermöglichen, haben die meisten Operatoren strikte Typanforderungen. Außerdem führen Operatoren selbst keine impliziten Konvertierungen aus.  
  
 Dies bedeutet, dass eine Abfrage wie „SELECT * FROM ROOT r WHERE r.Age = 21“ nur Dokumente zurückgibt, deren Eigenschaft „Age“ der Zahl 21 entspricht. Dokumente, deren Eigenschaft „Age“ der Zeichenfolge „21“ oder der Zeichenfolge „0021“ entspricht, stimmen nicht überein, da der Ausdruck „21“ = 21 als undefiniert ausgewertet wird. Dies ermöglicht eine bessere Verwendung von Indizes, da die Suche nach einem bestimmten Wert (z.B. der Zahl 21) schneller ist als die Suche nach einer unbestimmten Anzahl von möglichen Übereinstimmungen (z.B. der Zahl 21 oder den Zeichenfolgen „21“, „021“, „21,0“ etc.). Dies unterscheidet sich von der Art, in der JavaScript Operatoren für Werte verschiedener Typen auswertet.  
  
 **Arrays und Objekte – Gleichheit und Vergleich**  
  
 Das Ergebnis des Vergleichs von Array- und Object-Werten mithilfe von Bereichsoperatoren (>, > =, <, < =) ist undefiniert, weil für Object- oder Array-Werte keine Reihenfolge definiert ist. Die Verwendung von Gleichheits-/Ungleichheitsoperatoren (=, ! =, <>) wird jedoch unterstützt, und die Werte werden strukturell verglichen.  
  
 Arrays sind gleich, wenn beide Arrays die gleiche Anzahl von Elementen aufweisen, und Elemente an übereinstimmenden Positionen ebenfalls gleich sind. Wenn das Ergebnis des Vergleichs eines beliebigen Paars von Elementen undefiniert ist, ist das Ergebnis des Arrayvergleichs undefiniert.  
  
 Objekte sind gleich, wenn für beide Objekte die gleichen Eigenschaften definiert sind, und wenn Werte von übereinstimmenden Eigenschaften auch gleich sind. Wenn das Ergebnis des Vergleichs eines beliebigen Paars von Eigenschaftswerten undefiniert ist, ist das Ergebnis des Objektvergleichs undefiniert.  
  
##  <a name="bk_constants"></a> Konstanten  
 Eine Konstante, auch bekannt als Literal oder skalarer Wert, ist ein Symbol, das einen bestimmten Datenwert darstellt. Das Format einer Konstante hängt vom Datentyp des Werts ab, den sie darstellt.  
  
 **Unterstützte skalare Datentypen:**  
  
|**Typ**|**Reihenfolge der Werte**|  
|-|-|  
|**Undefiniert**|Einzelner Wert: **undefiniert**|  
|**NULL**|Einzelner Wert: **NULL**|  
|**Boolescher Wert**|Werte: **false**, **true**.|  
|**Number**|Eine Gleitkommazahl mit doppelter Genauigkeit, IEEE 754-Standard.|  
|**String**|Eine Sequenz von null (0) oder mehr Unicode-Zeichen. Zeichenfolgen müssen in einfache oder doppelte Anführungszeichen eingeschlossen sein.|  
|**Array**|Eine Sequenz von null (0) oder mehr Elementen. Jedes Element kann einen Wert eines beliebigen skalaren Datentyps mit Ausnahme von „Undefiniert“ aufweisen.|  
|**Object**|Ungeordneter Satz von null (0) oder mehr Name/Wert-Paaren. Der Name ist eine Unicode-Zeichenfolge, der Wert kann einen beliebigen skalaren Datentyp mit Ausnahme von **Undefiniert** aufweisen.|  
  
 **Syntax**  
  
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
  
 **Argumente**  
  
* `<undefined_constant>; undefined`  
  
  Stellt einen undefinierten Wert vom Typ „Undefiniert“ dar.  
  
* `<null_constant>; null`  
  
  Stellt einen **NULL**-Wert des Typs **NULL** dar.  
  
* `<boolean_constant>`  
  
  Stellt eine Konstante des Typs „Boolesch“ dar.  
  
* `false`  
  
  Stellt einen **false**-Wert des Typs „Boolesch“ dar.  
  
* `true`  
  
  Stellt einen **true**-Wert des Typs „Boolesch“ dar.  
  
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
  
##  <a name="bk_query_perf_guidelines"></a> Leistungsrichtlinien zur Abfrage  
 Damit eine Abfrage für einen großen Container effizient ausgeführt werden kann, sollte sie Filter verwenden, die über mindestens einen Index bedient werden können.  
  
 Die folgenden Filter werden für die Indexsuche berücksichtigt:  
  
- Verwenden des Gleichheitsoperators (=) mit einem Dokumentenpfadausdruck und einer Konstanten.  
  
- Verwenden von Bereichsoperatoren (<, \<=, >, >=) mit einem Dokumentenpfadausdruck und Zahlenkonstanten.  
  
- Der Dokumentenpfadausdruck steht für jeden Ausdruck, der einen Konstantenpfad in den Dokumenten aus dem referenzierten Datenbankcontainer identifiziert.  
  
  **Dokumentenpfadausdruck**  
  
  Dokumentenpfadausdrücke sind Ausdrücke, die ein Eigenschaftenpfad oder Arrayindexer für ein Dokument auswertet, das aus Datenbankcontainerdokumenten stammt. Dieser Pfad kann zur Identifizierung des Speicherorts von Werten verwendet werden, auf die in einem Filter direkt in den Dokumenten im Datenbankcontainer verwiesen wird.  
  
  Für einen Ausdruck, der als Dokumentenpfadausdruck in Betracht kommt, sollten folgende Voraussetzungen gelten:  
  
1.  Direktes Verweisen auf den Stamm des Containers.  
  
2.  Verweisen auf Eigenschaften- oder Konstantenarrayindexer eines Dokumentenpfadausdrucks.  
  
3.  Verweisen auf einen Alias, der einen Dokumentenpfadausdruck darstellt.  
  
     **Syntaxkonventionen**  
  
     Die folgende Tabelle beschreibt die Konventionen zum Beschreiben der Syntax in der folgenden SQL-Referenz.  
  
    |**Konvention**|**Verwendung**|  
    |-|-|    
    |GROSSBUCHSTABEN|Schlüsselwörter, bei denen Groß-/Kleinschreibung nicht beachtet werden muss.|  
    |Kleinbuchstaben|Schlüsselwörter, bei denen Groß-/Kleinschreibung beachtet werden muss.|  
    |\<nichtterminal&gt;|Nichtterminal, separat definiert.|  
    |\<nichtterminal&gt; ::=|Syntaxdefinition von „nichtterminal“.|  
    |other_terminal|Terminal (Token), ausführlich in Wörtern beschrieben.|  
    |Bezeichner|Bezeichner. Ermöglicht nur die folgenden Zeichen: a-z, A-Z 0-9 _Erstes Zeichen darf keine Ziffer sein.|  
    |„String“|Zeichenfolge in Anführungszeichen. Kann jede gültige Zeichenfolge ein. Siehe Beschreibung von „string_literal“.|  
    |'Symbol'|Literalsymbol, das Teil der Syntax ist.|  
    |&#124; (senkrechter Strich)|Alternativen für Syntaxelemente. Sie können nur eines der angegebenen Elemente verwenden.|  
    |[] /(Klammern)|Klammern schließen ein oder mehrere optionale Elemente ein.|  
    |[ ,...n ]|Gibt an, dass das vorherige Element n-Mal wiederholt werden kann. Die einzelnen Vorkommen werden durch Kommas getrennt.|  
    |[ ...n ]|Gibt an, dass das vorherige Element n-Mal wiederholt werden kann. Die einzelnen Vorkommen werden durch Leerzeichen getrennt.|  
  
##  <a name="bk_built_in_functions"></a> Integrierte Funktionen  
 Cosmos DB bietet viele integrierte SQL-Funktionen. Die Kategorien der integrierten Funktionen sind unten aufgeführt.  
  
|Funktion|BESCHREIBUNG|  
|--------------|-----------------|  
|[Mathematische Funktionen](#bk_mathematical_functions)|Jede mathematische Funktion führt eine Berechnung durch, üblicherweise basierend auf Eingabewerten, die als Argument bereitgestellt werden, und gibt einen numerischen Wert zurück.|  
|[Funktionen für die Typüberprüfung](#bk_type_checking_functions)|Mit den Funktionen für die Typprüfung können Sie den Typ eines Ausdrucks in SQL-Abfragen prüfen.|  
|[Zeichenfolgenfunktionen](#bk_string_functions)|Die folgenden Stringfunktionen führen einen Vorgang für einen Zeichenfolgen-Eingabewert durch und geben eine Zeichenfolge, einen numerischen Wert oder einen booleschen Wert zurück.|  
|[Arrayfunktionen](#bk_array_functions)|Die Arrayfunktionen führen einen Vorgang für einen Arrayeingabewert aus und geben einen numerischen Wert, booleschen Wert oder Arraywert zurück.|
|[Datums- und Uhrzeitfunktionen](#bk_date_and_time_functions)|Mit den Datums- und Uhrzeitfunktionen können Sie die aktuellen UTC-Datums-/Uhrzeitwerte in zwei Formaten abrufen: als numerischer Zeitstempel, dessen Wert der Unix-Epochenzeit in Millisekunden entspricht, oder als Zeichenfolge gemäß dem ISO 8601-Format.|
|[Räumliche Funktionen](#bk_spatial_functions)|Die räumlichen Funktionen führen einen Vorgang für den Eingabewert eines räumlichen Objekts aus und geben einen numerischen oder booleschen Wert zurück.|  
  
###  <a name="bk_mathematical_functions"></a> Mathematische Funktionen  
 Die folgenden Funktionen führen jeweils eine Berechnung durch, üblicherweise basierend auf Eingabewerten, die als Argumente bereitgestellt werden, und geben einen numerischen Wert zurück.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Gibt den absoluten (positiven) Wert des angegebenen numerischen Ausdrucks zurück.  
  
 **Syntax**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Ergebnisse der Anwendung der ABS-Funktion auf drei verschiedene Zahlen.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Hier ist das Resultset.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Kosinus der angegebene numerische Ausdruck ist. Wird auch als Arkuskosinus bezeichnet.  
  
 **Syntax**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den ACOS von -1 zurück.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Hier ist das Resultset.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Sinus der angegebene numerische Ausdruck ist. Wird auch als Arkussinus bezeichnet.  
  
 **Syntax**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den ASIN für -1 zurück.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Hier ist das Resultset.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Tangens der angegebene numerische Ausdruck ist. Wird auch als Arkustangens bezeichnet.  
  
 **Syntax**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den ATAN des angegebenen Werts zurück.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Hier ist das Resultset.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Gibt den Hauptwert des Arcustangens von y/x zurück, ausgedrückt in Bogenmaß.  
  
 **Syntax**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel berechnet ATN2 für die angegebenen x- und y-Komponenten.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Hier ist das Resultset.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Gibt den kleinsten ganzzahligen Wert zurück, der größer oder gleich dem angegebenen numerischen Ausdruck ist.  
  
 **Syntax**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die positiven numerischen, negativen und Nullwerte mit der CEILING-Funktion an.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Hier ist das Resultset.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Gibt den trigonometrischen Kosinus des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen Ausdruck zurück.  
  
 **Syntax**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel berechnet den COS des angegebenen Winkels.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Hier ist das Resultset.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Gibt den trigonometrischen Kotangens des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen numerischen Ausdruck zurück.  
  
 **Syntax**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird der COT des angegebenen Winkels berechnet.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Hier ist das Resultset.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Gibt den entsprechenden Winkel in Grad für einen in „rad“ (Bogenmaß) angegebenen Winkel zurück.  
  
 **Syntax**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt die Gradzahl in einem Winkel des PI/2-Bogenmaßes zurück.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Hier ist das Resultset.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Gibt den größten ganzzahligen Wert zurück, der kleiner oder gleich dem angegebenen numerischen Ausdruck ist.  
  
 **Syntax**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die positiven numerischen, negativen und Nullwerte mit der FLOOR-Funktion an.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Hier ist das Resultset.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Gibt den Exponentialwert des angegebenen numerischen Ausdrucks zurück.  
  
 **Syntax**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Anmerkungen**  
  
  Die Konstante **e** (2,718281...) ist die Basis des natürlichen Logarithmus.  
  
  Der Exponent einer Zahl ist die Konstante **e** potenziert mit der Zahl. Beispiel: EXP(1,0) = e^1,0 = 2,71828182845905 und EXP(10) = e^10 = 22026,4657948067.  
  
  Der Exponentialwert des natürlichen Logarithmus einer Zahl ist die Zahl selbst: EXP(LOG(n)) = n. Und der natürliche Logarithmus des Exponentialwerts einer Zahl ist die Zahl selbst: LOG(EXP(n)) = n.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird eine Variable deklariert und der Exponentialwert der angegebenen Variablen zurückgegeben (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 Hier ist das Resultset.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 Das folgende Beispiel gibt den Exponentialwert des natürlichen Logarithmus von 20 und den natürlichen Logarithmus des Exponentialwerts von 20 zurück. Da diese Funktionen Umkehrfunktionen der jeweils anderen sind, beträgt der Rückgabewert mit Rundung für die Gleitkommaberechnung in beiden Fällen 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Hier ist das Resultset.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Gibt den natürlichen Logarithmus des angegebenen numerischen Ausdrucks zurück.  
  
 **Syntax**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
- `base`  
  
   Optionales numerisches Argument, das die Basis des Logarithmus festlegt.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Hinweise:**  
  
  Standardmäßig gibt LOG() den natürlichen Logarithmus zurück. Sie können die Basis des Logarithmus mithilfe des optionalen base-Parameters in einen anderen Wert ändern.  
  
  Der natürliche Logarithmus ist der Logarithmus zur Basis **e**, wobei **e** eine Irrationale Konstante ist, die ungefähr 2,718281828 entspricht.  
  
  Der natürliche Logarithmus des Exponentialwerts einer Zahl ist die Zahl selbst: LOG(EXP(n)) = n. Und der Exponentialwert des natürlichen Logarithmus einer Zahl ist die Zahl selbst: EXP(LOG(n)) = n.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird eine Variable deklariert und der Logarithmuswert der angegebenen Variablen zurückgegeben (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 Hier ist das Resultset.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 Im folgenden Beispiel wird der LOG für den Exponenten einer Zahl berechnet.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Hier ist das Resultset.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Gibt den dekadischen Logarithmus des angegebenen numerischen Ausdrucks zurück.  
  
 **Syntax**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Anmerkungen**  
  
  Die Funktionen LOG10 und POWER sind umgekehrt miteinander verknüpft. Beispiel: 10 ^ LOG10(n) = n.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird eine Variable deklariert und der LOG10-Wert der angegebenen Variablen zurückgegeben (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Hier ist das Resultset.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Gibt den konstanten Wert von PI zurück.  
  
 **Syntax**  
  
```  
PI ()  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den Wert von PI zurück.  
  
```  
SELECT PI() AS pi 
```  
  
 Hier ist das Resultset.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Gibt den Wert des angegebenen Ausdrucks gemäß der angegebenen Potenz zurück.  
  
 **Syntax**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
- `y`  
  
   Ist der Wert, mit der `numeric_expression` potenziert wird.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt das Potenzieren einer Zahl mit 3 (dritte Potenz der Zahl).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Hier ist das Resultset.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Gibt das Bogenmaß zurück, wenn ein numerischer Ausdruck in Grad eingegeben wird.  
  
 **Syntax**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel werden einige Winkel eingegeben und ihre entsprechenden Bogenmaßwerte zurückgegeben.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Hier ist das Resultset.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Gibt einen numerischen Wert zurück, gerundet auf den nächsten ganzzahligen Wert.  
  
 **Syntax**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Anmerkungen**
  
  Der durchgeführte Rundungsvorgang folgt der Mittelpunktrundung von null. Wenn die Eingabe ein numerischer Ausdruck ist, der genau zwischen zwei Integern liegt, ist das Ergebnis der nächste Integerwert abseits von null.  
  
  |<numeric_expression>|Gerundet|
  |-|-|
  |–6,5000|-7|
  |–0,5|-1|
  |0,5|1|
  |6,5000|7||
  
  **Beispiele**  
  
  Im folgenden Beispiel werden die folgenden positiven und negativen Zahlen auf die nächste ganze Zahl gerundet.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Hier ist das Resultset.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Gibt das positive Vorzeichen (+1), null (0) oder das negative Vorzeichen (-1) des angegebenen numerischen Ausdrucks zurück.  
  
 **Syntax**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel werden die SIGN-Werte der Zahlen von -2 bis 2 zurückgegeben.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Hier ist das Resultset.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Gibt den trigonometrischen Sinus des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen Ausdruck zurück.  
  
 **Syntax**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel berechnet den SIN des angegebenen Winkels.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Hier ist das Resultset.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Gibt die Quadratwurzel des angegebenen numerischen Werts zurück.  
  
 **Syntax**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel werden die Quadratwurzeln der Zahlen von 1 bis 3 zurückgegeben.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Hier ist das Resultset.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Gibt das Quadrat des angegebenen numerischen Werts zurück.  
  
 **Syntax**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel werden die Quadrate der Zahlen von 1 bis 3 zurückgegeben.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Hier ist das Resultset.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Gibt den Tangens des angegebenen Winkels in Bogenmaß im angegebenen Ausdruck zurück.  
  
 **Syntax**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel berechnet den Tangens von PI()/2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Hier ist das Resultset.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Gibt einen numerischen Wert zurück, gekürzt auf den nächsten ganzzahligen Wert.  
  
 **Syntax**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumente**  
  
- `numeric_expression`  
  
   Ist ein numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel werden die folgenden positiven und negativen Zahlen auf den nächsten ganzzahligen Wert abgeschnitten.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Hier ist das Resultset.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Funktionen für die Typüberprüfung  
 Die folgenden Funktionen unterstützen die Typüberprüfung für Eingabewerte und geben jeweils einen booleschen Wert zurück.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Array“ ist.  
  
 **Syntax**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, „Number“, „String“, „NULL“, „Object“, „Array“ und „undefined“ mithilfe der IS_ARRAY-Funktion.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „boolesch“ ist.  
  
 **Syntax**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, „Number“, „String“, „NULL“, „Object“, „Array“ und „undefined“ mithilfe der IS_BOOL-Funktion.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde.  
  
 **Syntax**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft das Vorhandensein einer Eigenschaft im angegebenen JSON-Dokument. Die erste Überprüfung gibt "true" zurück, da „a“ vorhanden ist, aber die zweite „false“, da „b“ nicht vorhanden ist.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Hier ist das Resultset.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „NULL“ ist.  
  
 **Syntax**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, „Number“, „String“, „NULL“, „Object“, „Array“ und „undefined“ mithilfe der IS_NULL-Funktion.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Zahl“ ist.  
  
 **Syntax**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, „Number“, „String“, „NULL“, „Object“, „Array“ und „undefined“ mithilfe der IS_NULL-Funktion.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „JSON-Objekt“ ist.  
  
 **Syntax**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, „Number“, „String“, „NULL“, „Object“, „Array“ und „undefined“ mithilfe der IS_OBJECT-Funktion.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Gibt einen booleschen Wert zurück, der anzeigt, ob der angegebene Ausdruck ein Grundtyp ist (Zeichenfolge, boolesch, numerisch oder NULL).  
  
 **Syntax**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, Number, String, NULL, Object, Array und undefiniert mithilfe der IS_PRIMITIVE-Funktion.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Zeichenfolge“ ist.  
  
 **Syntax**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumente**  
  
- `expression`  
  
   Ist ein beliebiger gültiger Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft JSON-Objekte des Typs boolesch, „Number“, „String“, „NULL“, „Object“, „Array“ und „undefined“ mithilfe der IS_STRING-Funktion.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Hier ist das Resultset.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  
  
###  <a name="bk_string_functions"></a> Zeichenfolgenfunktionen  
 Die folgenden Skalarfunktionen führen einen Vorgang für einen Zeichenfolgen-Eingabewert durch und geben eine Zeichenfolge, einen numerischen Wert oder einen booleschen Wert zurück.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Gibt eine Zeichenfolge zurück, die das Ergebnis der Verkettung von zwei oder mehr Zeichenfolgenwerten darstellt.  
  
 **Syntax**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt die verkettete Zeichenfolge der angegebenen Werte zurück.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Hier ist das Resultset.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck den zweiten enthält.  
  
 **Syntax**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird überprüft, ob „abc“ „ab“ und „d“ enthält.  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Hier ist das Resultset.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten endet.  
  
 **Syntax**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird zurückgegeben, ob „abc“ mit „b“ und „bc“ endet.  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Hier ist das Resultset.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Gibt die Anfangsposition des ersten Vorkommens des zweiten Zeichenfolgenausdrucks innerhalb des ersten angegebenen Zeichenfolgenausdrucks zurück, oder -1, wenn die Zeichenfolge nicht gefunden wird.  
  
 **Syntax**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den Index der verschiedenen Teilzeichenfolgen innerhalb von „abc“ zurück.  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Hier ist das Resultset.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Gibt den linken Teil einer Zeichenfolge mit der angegebenen Anzahl von Zeichen zurück.  
  
 **Syntax**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
- `num_expr`  
  
   Ist ein beliebiger gültiger numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den linken Teil von „abc“ für verschiedene Längenwerte zurück.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Hier ist das Resultset.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 Gibt die Anzahl der Zeichen im angegebenen Zeichenfolgenausdruck zurück.  
  
 **Syntax**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt die Länge einer Zeichenfolge zurück.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Hier ist das Resultset.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Gibt eine Zeichenfolge zurück, nachdem Großbuchstaben in Kleinbuchstaben konvertiert wurden.  
  
 **Syntax**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von LOWER in einer Abfrage.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Hier ist das Resultset.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Gibt einen Zeichenfolgenausdruck zurück, nachdem vorangestellte Leerzeichen entfernt wurden.  
  
 **Syntax**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von LTRIM in einer Abfrage.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Hier ist das Resultset.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 Ersetzt alle Vorkommen eines angegebenen Zeichenfolgenwerts durch einen anderen Zeichenfolgenwert.  
  
 **Syntax**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von REPLACE in einer Abfrage.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Hier ist das Resultset.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Wiederholt einen Zeichenfolgenwert mit der angegebenen Häufigkeit.  
  
 **Syntax**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
- `num_expr`  
  
   Ist ein beliebiger gültiger numerischer Ausdruck. Wenn num_expr negativ oder nicht finit ist, ist das Ergebnis nicht definiert.

  > [!NOTE]
  > Die maximale Länge des Ergebnisses beträgt 10.000 Zeichen (also length(str_expr)  *  num_expr) <= 10.000).
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von REPLICATE in einer Abfrage.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Hier ist das Resultset.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Gibt einen Zeichenfolgenwert in umgekehrter Reihenfolge zurück.  
  
 **Syntax**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von REVERSE in einer Abfrage.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Hier ist das Resultset.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Gibt den rechten Teil einer Zeichenfolge mit der angegebenen Anzahl von Zeichen zurück.  
  
 **Syntax**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
- `num_expr`  
  
   Ist ein beliebiger gültiger numerischer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt den rechten Teil von „abc“ für verschiedene Längenwerte zurück.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Hier ist das Resultset.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Gibt einen Zeichenfolgenausdruck zurück, nachdem vorangestellte Leerzeichen entfernt wurden.  
  
 **Syntax**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von RTRIM in einer Abfrage.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Hier ist das Resultset.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten beginnt.  
  
 **Syntax**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel überprüft, ob die Zeichenfolge „abc“ mit „b“ und „a“ beginnt.  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Hier ist das Resultset.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Gibt den Ausdruck übersetzt in ein Array zurück. Wenn der Ausdruck nicht übersetzt werden kann, wird „undefined“ zurückgegeben.  
  
 **Syntax**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumente**  
  
- `expr`  
  
   Ein beliebiger gültiger skalarer Ausdruck, der als JSON-Arrayausdruck ausgewertet werden soll. Beachten Sie, dass geschachtelte Zeichenfolgenwerte in doppelten Anführungszeichen angegeben werden müssen, damit sie gültig sind. Ausführliche Informationen zum JSON-Format finden Sie unter [json.org](https://json.org/).
  
  **Rückgabetypen**  
  
  Gibt einen Arrayausdruck oder „undefined“ zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird das typübergreifende Verhalten von StringToArray gezeigt. 
  
 Die folgenden Beispiele zeigen eine gültige Eingabe:

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Hier ist das Resultset.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Das folgende Beispiel zeigt eine ungültige Eingabe. 
   
 Einfache Anführungszeichen innerhalb des Arrays sind kein gültiger JSON-Code.
Sie sind zwar innerhalb einer Abfrage gültig, werden jedoch nicht als gültige Arrays interpretiert. Zeichenfolgen innerhalb der Arrayzeichenfolge müssen mit Escapezeichen versehen werden: "[\\"\\"]". Alternativ kann die Arrayzeichenfolge in einfache Anführungszeichen eingeschlossen werden: '[""]'.

```
SELECT
    StringToArray("['5','6','7']")
```

Hier ist das Resultset.

```
[{}]
```

Die folgenden Beispiele zeigen eine ungültige Eingabe.
   
 Der übergebene Ausdruck wird als JSON-Array analysiert. Folgendes wird nicht als Arraytyp ausgewertet und gibt daher „undefined“ zurück:
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Hier ist das Resultset.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Gibt den Ausdruck übersetzt in einen booleschen Wert zurück. Wenn der Ausdruck nicht übersetzt werden kann, wird „undefined“ zurückgegeben.  
  
 **Syntax**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumente**  
  
- `expr`  
  
   Ein beliebiger gültiger skalarer Ausdruck, der als boolescher Ausdruck ausgewertet werden soll.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck oder „undefined“ zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird das typübergreifende Verhalten von StringToBoolean gezeigt. 
 
 Die folgenden Beispiele zeigen eine gültige Eingabe.

Leerzeichen sind ausschließlich vor oder nach „true“/„false“ zulässig.

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Hier ist das Resultset.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Die folgenden Beispiele zeigen eine ungültige Eingabe.

 Bei booleschen Werten muss die Groß-/Kleinschreibung beachtet werden. „true“ und „false“ dürfen daher nur Kleinbuchstaben enthalten.

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Hier ist das Resultset.  
  
```  
[{}]
``` 

Der übergebene Ausdruck wird als boolescher Ausdruck analysiert. Die folgenden Eingaben werden nicht als boolescher Typ ausgewertet und geben daher „undefined“ zurück:

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Hier ist das Resultset.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Gibt den Ausdruck übersetzt in NULL zurück. Wenn der Ausdruck nicht übersetzt werden kann, wird „undefined“ zurückgegeben.  
  
 **Syntax**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumente**  
  
- `expr`  
  
   Ein beliebiger gültiger skalarer Ausdruck, der als NULL-Ausdruck ausgewertet werden soll.
  
  **Rückgabetypen**  
  
  Gibt einen NULL-Ausdruck oder „undefined“ zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird das typübergreifende Verhalten von StringToNull gezeigt. 

Die folgenden Beispiele zeigen eine gültige Eingabe.

 Leerzeichen sind ausschließlich vor oder nach „null“ zulässig.

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Hier ist das Resultset.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Die folgenden Beispiele zeigen eine ungültige Eingabe.

„null“ darf nur Kleinbuchstaben enthalten.

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Hier ist das Resultset.  
  
```  
[{}]
```  

Der übergebene Ausdruck wird als NULL-Ausdruck analysiert. Die folgenden Eingaben werden nicht als NULL-Typ ausgewertet und geben daher „undefined“ zurück:

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Hier ist das Resultset.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Gibt den Ausdruck übersetzt in eine Zahl zurück. Wenn der Ausdruck nicht übersetzt werden kann, wird „undefined“ zurückgegeben.  
  
 **Syntax**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumente**  
  
- `expr`  
  
   Ein beliebiger gültiger skalarer Ausdruck, der als JSON-Zahlausdruck ausgewertet werden soll. Zahlen müssen in JSON im Integer- oder Gleitkommaformat angegeben werden. Ausführliche Informationen zum JSON-Format finden Sie unter [json.org](https://json.org/).  
  
  **Rückgabetypen**  
  
  Gibt einen Ausdruck für eine Zahl oder „undefined“ zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird das typübergreifende Verhalten von StringToNumber gezeigt. 

Leerzeichen sind ausschließlich vor oder nach der Zahl zulässig.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Hier ist das Resultset.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

In JSON muss eine gültige Zahl entweder eine ganze Zahl oder eine Gleitkommazahl sein.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Hier ist das Resultset.  
  
```  
{{}}
```  

Der übergebene Ausdruck wird als Zahlenausdruck analysiert. Die folgenden Eingaben werden nicht als Zahlentyp ausgewertet und geben daher „undefined“ zurück: 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Hier ist das Resultset.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Gibt den Ausdruck übersetzt in ein Objekt zurück. Wenn der Ausdruck nicht übersetzt werden kann, wird „undefined“ zurückgegeben.  
  
 **Syntax**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumente**  
  
- `expr`  
  
   Ein beliebiger gültiger skalarer Ausdruck, der als JSON-Objektausdruck ausgewertet werden soll. Beachten Sie, dass geschachtelte Zeichenfolgenwerte in doppelten Anführungszeichen angegeben werden müssen, damit sie gültig sind. Ausführliche Informationen zum JSON-Format finden Sie unter [json.org](https://json.org/).  
  
  **Rückgabetypen**  
  
  Gibt einen Ausdruck für ein Objekt oder „undefined“ zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird das typübergreifende Verhalten von StringToObject gezeigt. 
  
 Die folgenden Beispiele zeigen eine gültige Eingabe.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Hier ist das Resultset.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Die folgenden Beispiele zeigen eine ungültige Eingabe.
Sie sind zwar innerhalb einer Abfrage gültig, werden jedoch nicht als gültige Objekte interpretiert. Zeichenfolgen innerhalb der Objektzeichenfolge müssen mit Escapezeichen versehen werden: "{\\"a\\":\\"str\\"}". Alternativ kann die Objektzeichenfolge in einfache Anführungszeichen eingeschlossen werden: '{"a": "str"}'.

Einfache Anführungszeichen um Eigenschaftennamen sind in JSON nicht gültig.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Hier ist das Resultset.

```  
[{}]
```  

Eigenschaftennamen ohne umgebende Anführungszeichen sind in JSON nicht gültig.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Hier ist das Resultset.

```  
[{}]
``` 

Die folgenden Beispiele zeigen eine ungültige Eingabe.

 Der übergebene Ausdruck wird als JSON-Objekt analysiert. Die folgenden Eingaben werden nicht als Objekttyp ausgewertet und geben daher „undefined“ zurück:

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Hier ist das Resultset.

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 Gibt einen Teil eines Zeichenfolgenausdrucks zurück. Das angegebene Zeichen ist der Nullpunkt, von dem ab die Teilzeichenfolge in angegebener Länge bzw. bis zum Ende der Zeichenfolge zurückgegeben wird.  
  
 **Syntax**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
- `num_expr`  
  
   Ist ein beliebiger gültiger numerischer Ausdruck zur Angabe des Start- und des Endzeichens angibt.    
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel gibt die Teilzeichenfolge „abc“ zurück, beginnend bei 1 und für eine Länge von 1 Zeichen.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Hier ist das Resultset.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Gibt eine Zeichenfolgendarstellung eines skalaren Ausdrucks zurück. 
  
 **Syntax**  
  
```  
ToString(<expr>)
```  
  
 **Argumente**  
  
- `expr`  
  
   Ist ein beliebiger gültiger skalarer Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird das typübergreifende Verhalten von „ToString“ gezeigt:   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Hier ist das Resultset.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Die folgende Eingabe ist gegeben:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 Im folgenden Beispiel wird gezeigt, wie „ToString“ mit anderen Zeichenfolgenfunktionen wie CONCAT verwendet werden kann.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Hier ist das Resultset.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Die folgende Eingabe ist gegeben:
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
Im folgenden Beispiel wird gezeigt, wie „ToString“ mit anderen Zeichenfolgenfunktionen wie REPLACE verwendet werden kann.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Hier ist das Resultset.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 Gibt einen Zeichenfolgenausdruck zurück, nachdem führende und nachfolgende Leerzeichen entfernt wurden.  
  
 **Syntax**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von TRIM in einer Abfrage.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Hier ist das Resultset.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 Gibt eine Zeichenfolge zurück, nachdem Kleinbuchstaben in Großbuchstaben konvertiert wurden.  
  
 **Syntax**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumente**  
  
- `str_expr`  
  
   Ist ein beliebiger gültiger Zeichenfolgenausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Zeichenfolgenausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Verwendung von UPPER in einer Abfrage.  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Hier ist das Resultset.  
  
```  
[{"upper": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Arrayfunktionen  
 Die folgenden Skalarfunktionen führen einen Vorgang für einen Arrayeingabewert durch und geben einen numerischen Wert, booleschen Wert oder Arraywert zurück.  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Gibt ein Array zurück, das das Ergebnis der Verkettung von zwei oder mehr Arraywerten darstellt.  
  
 **Syntax**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumente**  
  
- `arr_expr`  
  
   Ist ein beliebiger gültiger Arrayausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen Arrayausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird veranschaulicht, wie zwei Arrays verkettet werden.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Hier ist das Resultset.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Gibt einen booleschen Wert zurück, um anzugeben, ob das Array den angegebenen Wert enthält. Anhand eines booleschen Ausdrucks innerhalb des Befehls können Sie nach einem Objekt suchen, das vollständig oder teilweise übereinstimmt. 

**Syntax**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumente**  
  
- `arr_expr`  
  
   Ist ein beliebiger gültiger Arrayausdruck.  
  
- `expr`  
  
   Ist ein beliebiger gültiger Ausdruck.  

- `bool_expr`  
  
   Ist ein beliebiger boolescher Ausdruck. Wenn er auf TRUE festgelegt ist und als Suchwert ein Objekt angegeben wurde, prüft der Befehl auf eine teilweise Übereinstimmung. (Das Suchobjekt ist eine Teilmenge eines der Objekte.) Wenn er auf FALSE festgelegt ist, prüft der Befehl auf eine vollständige Übereinstimmung aller Objekte innerhalb des Arrays. Wenn Sie hier nichts angeben, lautet der Standardwert FALSE. 
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Wert zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Überprüfung auf Mitgliedschaft in einem Array mit ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Hier ist das Resultset.  
  
```  
[{"b1": true, "b2": false}]  
```  

Das folgende Beispiel zeigt, wie die partielle Übereinstimmung eines JSON-Codes in einem Array mit ARRAY_CONTAINS überprüft wird.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Hier ist das Resultset.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Gibt die Anzahl der Elemente des angegebenen Arrayausdrucks zurück.  
  
 **Syntax**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumente**  
  
- `arr_expr`  
  
   Ist ein beliebiger gültiger Arrayausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück.  
  
  **Beispiele**  
  
  Im folgenden Beispiel wird die Länge eines Arrays mit ARRAY_LENGTH abgerufen.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Hier ist das Resultset.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Gibt einen Teil eines Arrayausdrucks zurück.
  
 **Syntax**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumente**  
  
- `arr_expr`  
  
   Ist ein beliebiger gültiger Arrayausdruck.  
  
- `num_expr`  
  
   Nullbasierter numerischer Index, bei dem das Array beginnt. Negative Werte können verwendet werden, um den Startindex relativ zum letzten Element des Arrays anzugeben, d.h. -1 bezieht sich auf das letzte Element des Arrays.  

- `num_expr`  

   Maximale Anzahl von Elementen im resultierenden Array.    

  **Rückgabetypen**  
  
  Gibt einen Arrayausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt, wie Sie mit ARRAY_SLICE verschiedene Slices eines Arrays abgerufen können.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Hier ist das Resultset.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

###  <a name="bk_date_and_time_functions"></a> Datums- und Uhrzeitfunktionen
 Mit den folgenden Skalarfunktionen können Sie die aktuellen UTC-Datums-/Uhrzeitwerte in zwei Formaten abrufen: als numerischer Zeitstempel, dessen Wert der Unix-Epochenzeit in Millisekunden entspricht, oder als Zeichenfolge gemäß dem ISO 8601-Format. 

|||
|-|-|
|[GetCurrentDateTime](#bk_get_current_date_time)|[GetCurrentTimestamp](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GetCurrentDateTime
 Diese Funktion gibt den aktuellen UTC-Datums-/Uhrzeitwert als ISO 8601-Zeichenfolge zurück.
  
 **Syntax**
  
```
GetCurrentDateTime ()
```
  
  **Rückgabetypen**
  
  Hiermit wird der aktuelle UTC-Datums-/Uhrzeitwert als ISO 8601-Zeichenfolgenwert zurückgegeben. 

  Dies wird im Format „YYYY-MM-DDThh:mm:ss.sssZ“ ausgedrückt, das folgendermaßen aufgeschlüsselt wird:
  
  |||
  |-|-|
  |YYYY|vierstellige Jahreszahl|
  |MM|zweistellige Monatszahl (01 = Januar usw.)|
  |DD|zweistellige Zahl für den Tag des Monats (01 bis 31)|
  |T|Trennzeichen, das den Anfang der Uhrzeitelemente markiert|
  |hh|zweistellige Stundenzahl (00 bis 23)|
  |MM|zweistellige Minutenzahl (00 bis 59)|
  |ss|zweistellige Sekundenzahl (00 bis 59)|
  |.sss|dreistellige dezimale Bruchteile einer Sekunde|
  |Z|UTC-Kennzeichner||
  
  Weitere Informationen zum ISO 8601-Format finden Sie unter [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601).

  **Anmerkungen**

  GetCurrentDateTime ist eine nichtdeterministische Funktion. 
  
  Das zurückgegebene Ergebnis entspricht der koordinierten Weltzeit (UTC).

  **Beispiele**  
  
  Im folgenden Beispiel wird gezeigt, wie der aktuelle UTC-Datums-/Uhrzeitwert mithilfe der integrierten GetCurrentDateTime-Funktion abgerufen wird.
  
```  
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Hier ist ein Beispielresultset:
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GetCurrentTimestamp
 Diese Funktion gibt die Anzahl der Millisekunden zurück, die seit dem Donnerstag am 1. Januar 1970 um 00:00:00 Uhr vergangen sind. 
  
 **Syntax**  
  
```  
GetCurrentTimestamp ()  
```  
  
  **Rückgabetypen**  
  
  Ein numerischer Wert, der der aktuellen Anzahl der Millisekunden entspricht, die seit der Unix-Epoche vergangen sind, d. h. die Anzahl der Millisekunden, die seit dem Donnerstag am 1. Januar 1970 um 00:00:00 Uhr vergangen sind, wird zurückgegeben.

  **Anmerkungen**

  GetCurrentTimestamp ist eine nichtdeterministische Funktion. 
  
  Das zurückgegebene Ergebnis entspricht der koordinierten Weltzeit (UTC).

  **Beispiele**  
  
  Im folgenden Beispiel wird gezeigt, wie der Zeitstempel mithilfe der integrierten GetCurrentTimestamp-Funktion abgerufen wird.
  
```  
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Hier ist ein Beispielresultset:
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```  

###  <a name="bk_spatial_functions"></a> Räumliche Funktionen  
 Die folgenden Skalarfunktionen führen einen Vorgang für den Eingabewert eines räumlichen Objekts aus und geben einen numerischen oder booleschen Wert zurück.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Gibt den Abstand zwischen den beiden GeoJSON-Punkt-, Polygon- oder LineString-Ausdrücken zurück.  
  
 **Syntax**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumente**  
  
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen numerischen Ausdruck zurück, der den Abstand enthält. Dieser wird für das Standardreferenzsystem in Metern angegeben.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Rückgabe aller Familiendokumente, die sich innerhalb von 30 km von der angegebenen Position befinden. Dazu wird die integrierte ST_DISTANCE-Funktion verwendet. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Hier ist das Resultset.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Gibt einen booleschen Ausdruck zurück, der angibt, ob das im ersten Argument angegebene GeoJSON-Objekt (Punkt, Polygon oder LineString) im zweiten Argument im GeoJSON-Objekt (Punkt, Polygon oder LineString) enthalten ist.  
  
 **Syntax**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumente**  
  
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
 
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Wert zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt, wie alle Familiendokumente mit ST_WITHIN innerhalb eines Polygons gefunden werden.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Hier ist das Resultset.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Gibt einen booleschen Ausdruck zurück, der angibt, ob das im ersten Argument angegebene GeoJSON-Objekt (Punkt, Polygon oder LineString) sich mit dem GeoJSON-Objekt (Punkt, Polygon oder LineString) im zweiten Argument überschneidet.  
  
 **Syntax**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumente**  
  
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
 
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Objektausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Wert zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt, wie alle Bereiche gefunden werden, die sich mit dem angegebenen Polygon überschneiden.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Hier ist das Resultset.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Gibt einen booleschen Wert zurück, der angibt, ob der/das angegebene GeoJSON-Punkt, -Polygon, oder -LineString gültig ist.  
  
 **Syntax**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumente**  
  
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt-, -Polygon- oder -LineString-Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen booleschen Ausdruck zurück.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt die Überprüfung mit ST_VALID, ob ein Punkt gültig ist.  
  
  Dieser Punkt hat z.B. einen Breitengradwert, der nicht im gültigen Wertebereich [-90, 90] liegt, sodass die Abfrage „false“ zurückgibt.  
  
  Für Polygone erfordert die GeoJSON-Spezifikation, dass das letzte angegebene Koordinatenpaar mit dem ersten identisch sein muss, um eine geschlossene Form zu bilden. Punkte innerhalb eines Polygons müssen gegen den Uhrzeigersinn nacheinander angegeben werden. Ein Polygon, das im Uhrzeigersinn angegeben wird, stellt die Umkehrung der darin enthaltenen Region dar.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Hier ist das Resultset.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Gibt einen JSON-Wert mit einem booleschen Wert zurück, wenn der angegebene GeoJSON-Punkt-, -Polygon- oder -LineString-Ausdruck gültig ist. Falls ungültig, wird außerdem der Grund als Zeichenfolge zurückgegeben.  
  
 **Syntax**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argumente**  
  
- `spatial_expr`  
  
   Ist ein beliebiger gültiger GeoJSON-Punkt- oder -Polygon-Ausdruck.  
  
  **Rückgabetypen**  
  
  Gibt einen JSON-Wert mit einem booleschen Wert zurück, wenn der angegebene GeoJSON-Punkt oder -Polygon-Ausdruck gültig ist. Falls ungültig, wird außerdem der Grund als Zeichenfolge zurückgegeben.  
  
  **Beispiele**  
  
  Das folgende Beispiel zeigt das Überprüfen der Gültigkeit (mit Details) mit ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b  
```  
  
 Hier ist das Resultset.  
  
```  
[{  
  "b": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
 
## <a name="next-steps"></a>Nächste Schritte  

- [SQL-Syntax und SQL-Abfrage für Cosmos DB](how-to-sql-query.md)

- [Dokumentation für Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
