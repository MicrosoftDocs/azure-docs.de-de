---
title: Programmierbarkeitshandbuch für benutzerdefinierten U-SQL-Applier für Azure Data Lake
description: Hier finden Sie Informationen zum U-SQL-UDO-Programmierbarkeitshandbuch für den benutzerdefinierten Applier.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512446"
---
# <a name="use-user-defined-applier"></a>Verwenden eines benutzerdefinierten Appliers 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL-UDO: Benutzerdefinierter Applier
Mit einem benutzerdefinierten U-SQL-Applier können Sie für jede Zeile, die vom äußeren Tabellenausdruck einer Abfrage zurückgegeben wird, eine benutzerdefinierte C#-Funktion aufrufen. Die rechte Eingabe wird für jede Zeile aus der linken Eingabe ausgewertet, und die erstellten Zeilen werden für die endgültige Ausgabe kombiniert. Bei der Liste mit den Spalten, die durch den APPLY-Operator erzeugt werden, handelt es sich um die Kombination aus dem Satz von Spalten in der linken und rechten Eingabe.


## <a name="how-to-define-and-use-user-defined-applier"></a>Definieren und Verwenden eines benutzerdefinierten Appliers
Der benutzerdefinierte Applier wird im Rahmen des U-SQL SELECT-Ausdrucks aufgerufen.

Ein typischer Aufruf des benutzerdefinierten Appliers sieht wie folgt aus:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Weitere Informationen zur Verwendung von Appliers in einem SELECT-Ausdruck finden Sie unter [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply) (U-SQL SELECT: Auswählen aus CROSS APPLY und OUTER APPLY).

Die Basisklassendefinition eines benutzerdefinierten Appliers sieht wie folgt aus:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Um einen benutzerdefinierten Applier zu definieren, müssen wir die `IApplier`-Schnittstelle mit dem [`SqlUserDefinedApplier`]-Attribut (für die Definition eines benutzerdefinierten Appliers optional) erstellen.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* „Apply“ wird für jede Zeile der äußeren Tabelle aufgerufen. Es gibt das `IUpdatableRow`-Ausgaberowset zurück.
* Die Konstruktorklasse dient zum Übergeben von Parametern an den benutzerdefinierten Applier.

**SqlUserDefinedApplier** gibt an, dass der Typ als benutzerdefinierter Applier registriert werden muss. Diese Klasse kann nicht vererbt werden.

**SqlUserDefinedApplier** ist für die Definition benutzerdefinierter Applier **optional**.


Die wichtigsten Programmierbarkeitsobjekte sind:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Eingaberowsets werden als `IRow`-Eingabe übergeben. Die Ausgabezeilen werden als `IUpdatableRow`-Ausgabeschnittstelle generiert.

Einzelne Spaltennamen können durch Aufrufen der `IRow`-Schemamethode ermittelt werden.

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Um die tatsächlichen Datenwerte aus dem eingehenden `IRow`-Element abzurufen, verwenden wir die Get()-Methode der `IRow`-Schnittstelle.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Alternativ können wir auch den Schemaspaltennamen verwenden:

```csharp
row.Get<int>(row.Schema[0].Name)
```

Die Ausgabewerte müssen mit der `IUpdatableRow`-Ausgabe festgelegt werden:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Wichtig: Benutzerdefinierte Applier geben nur Spalten und Werte aus, die mit dem `output.Set`-Methodenaufruf definiert wurden.

Die tatsächliche Ausgabe wird durch Aufrufen von `yield return output.AsReadOnly();` ausgelöst.

Die Parameter des benutzerdefinierten Appliers können an den Konstruktor übergeben werden. Der Applier kann eine variable Anzahl von Spalten zurückgeben, die während des Applieraufrufs im U-SQL-Basisskript definiert werden müssen.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Hier sehen Sie das Beispiel für den benutzerdefinierten Applier:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Im Anschluss finden Sie das U-SQL-Basisskript für diesen benutzerdefinierten Applier:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In diesem Szenario fungiert der benutzerdefinierte Applier als Parser mit Werttrennung durch Trennzeichen für die Eigenschaften der Autoflotte. Die Zeilen der Eingabedatei sehen wie folgt aus:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Es handelt sich um eine typische TSV-Datei mit Tabstopptrennzeichen und einer Eigenschaftenspalte, die Fahrzeugeigenschaften wie Marke und Modell enthält. Diese Eigenschaften müssen für die Tabellenspalten analysiert werden. Der bereitgestellte Applier ermöglicht auch die Generierung einer dynamischen Anzahl von Eigenschaften im Ergebnisrowset auf der Grundlage des übergebenen Parameters. Sie können entweder alle Eigenschaften generieren oder nur einen bestimmten Satz von Eigenschaften.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

Der benutzerdefinierte Applier kann als neue Instanz des Applierobjekts aufgerufen werden:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Eine weitere Möglichkeit ist das Aufrufen einer Wrapper-Factorymethode:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über das U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)