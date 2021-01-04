---
title: Programmierbarkeitshandbuch für benutzerdefinierten U-SQL-Combiner für Azure Data Lake
description: Hier finden Sie Informationen zum U-SQL-UDO-Programmierbarkeitshandbuch für den benutzerdefinierten Combiner.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512455"
---
# <a name="use-user-defined-combiner"></a>Verwenden eines benutzerdefinierten Combiners

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL-UDO: Benutzerdefinierter Combiner
Mit einem benutzerdefinierten Combiner (User-Defined Combiner, UDC) können Sie Zeilen aus linken und rechten Rowsets auf der Grundlage einer benutzerdefinierten Logik kombinieren. Ein benutzerdefinierter Combiner wird mit dem COMBINE-Ausdruck verwendet.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Definieren und Verwenden eines benutzerdefinierten Combiners

Ein Combiner wird mit dem COMBINE-Ausdruck aufgerufen, der die erforderlichen Informationen über die Eingaberowsets, die Gruppierungsspalten, das erwartete Ergebnisschema und die zusätzlichen Informationen bereitstellt.

Um einen Combiner in einem U-SQL-Basisskript aufzurufen, verwenden Sie die folgende Syntax:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Weitere Informationen finden Sie unter [COMBINE Expression (U-SQL)](/u-sql/statements-and-expressions/combine-expression) (COMBINE-Ausdruck [U-SQL]).

Um einen benutzerdefinierten Combiner zu definieren, müssen wir die `ICombiner`-Schnittstelle mit dem [`SqlUserDefinedCombiner`]-Attribut (für die Definition eines benutzerdefinierten Combiners optional) erstellen.

`ICombiner`-Basisklassendefinition:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Die benutzerdefinierte Implementierung einer `ICombiner`-Schnittstelle muss die Definition für eine Combine-Überschreibung für `IEnumerable<IRow>` enthalten.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Das Attribut **SqlUserDefinedCombiner** gibt an, dass der Typ als benutzerdefinierter Combiner registriert werden muss. Diese Klasse kann nicht vererbt werden.

**SqlUserDefinedCombiner** dient zum Definieren der Combiner-Moduseigenschaft. Das Attribut ist für die Definition eines benutzerdefinierten Combiners optional.

CombinerMode-Modus

Die CombinerMode-Aufzählung akzeptiert die folgenden Werte:

* „Full“ (0): Jede Ausgabezeile ist potenziell von allen Eingabezeilen von links und rechts mit dem gleichen Schlüsselwert abhängig.

* „Left“ (1): Jede Ausgabezeile ist von einer einzelnen Eingabezeile von links (und potenziell von allen Zeilen von rechts mit dem gleichen Schlüsselwert) abhängig.

* „Right“ (2): Jede Ausgabezeile ist von einer einzelnen Eingabezeile von rechts (und potenziell von allen Zeilen von links mit dem gleichen Schlüsselwert) abhängig.

* „Inner“ (3): Jede Ausgabezeile ist von einer einzelnen Eingabezeile von links und rechts mit dem gleichen Wert abhängig.

Beispiel:    [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Die wichtigsten Programmierbarkeitsobjekte sind:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Eingaberowsets werden als **left**- und **right**-`IRowset`-Schnittstellentyp übergeben. Beide Rowsets müssen für die Verarbeitung aufgezählt werden. Die einzelnen Schnittstellen dürfen jeweils nur einmal aufgezählt werden, sodass die Aufzählung ggf. zwischengespeichert werden muss.

Für die Zwischenspeicherung können wir einen List\<T\>-Typ der Arbeitsspeicherstruktur als Ergebnis einer LINQ-Abfragenausführung (genauer gesagt: List<`IRow`>) erstellen. Der anonyme Datentyp kann während der Aufzählung ebenfalls verwendet werden.

Unter [Introduction to LINQ Queries (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) (Einführung in LINQ-Abfragen (C#)) finden Sie weitere Informationen zu LINQ-Abfragen. Unter [IEnumerable\<T\>-Schnittstelle](/dotnet/api/system.collections.generic.ienumerable-1) finden Sie weitere Informationen zur IEnumerable\<T\>-Schnittstelle.

Um die tatsächlichen Datenwerte aus dem eingehenden `IRowset`-Element abzurufen, verwenden wir die Get()-Methode der `IRow`-Schnittstelle.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Einzelne Spaltennamen können durch Aufrufen der `IRow`-Schemamethode ermittelt werden.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Alternativ können wir auch den Schemaspaltennamen verwenden:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

Die allgemeine Enumeration mit LINQ sieht wie folgt aus:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Nach dem Aufzählen beider Rowsets durchlaufen wir alle Zeilen mithilfe einer Schleife. Für jede Zeile im linken Rowset suchen wir nach allen Zeilen, die die Bedingung unseres Combiners erfüllen.

Die Ausgabewerte müssen mit der `IUpdatableRow`-Ausgabe festgelegt werden.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Die tatsächliche Ausgabe wird durch Aufrufen von `yield return output.AsReadOnly();` ausgelöst.

Im Anschluss finden Sie ein Beispiel für den Combiner:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

In diesem Szenario erstellen wir einen Analysebericht für den Einzelhandel. Es sollen alle Produkte gefunden werden, deren Preis über 20.000 USD liegt und die innerhalb eines bestimmten Zeitraums schneller über die Website verkauft werden als über den regulären Einzelhandel.

So sieht das U-SQL-Basisskript aus. Sie können die Logik zwischen einem regulären JOIN-Element und einem Combiner vergleichen:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Ein benutzerdefinierter Combiner kann als neue Instanz des Applierobjekts aufgerufen werden:

```csharp
USING new MyNameSpace.MyCombiner();
```


Eine weitere Möglichkeit ist das Aufrufen einer Wrapper-Factorymethode:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über das U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)