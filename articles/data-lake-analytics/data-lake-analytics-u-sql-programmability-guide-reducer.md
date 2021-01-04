---
title: Programmierbarkeitshandbuch für benutzerdefinierten U-SQL-Reducer für Azure Data Lake
description: Hier finden Sie Informationen zum U-SQL-UDO-Programmierbarkeitshandbuch für den benutzerdefinierten Reducer.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512444"
---
# <a name="use-user-defined-reducer"></a>Verwenden eines benutzerdefinierten Reducers

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL-UDO: Benutzerdefinierter Reducer

Mit U-SQL können Sie benutzerdefinierte Rowsetreducer in C# schreiben. Hierzu müssen Sie das Erweiterbarkeitsframework des benutzerdefinierten Operators verwenden und eine IReducer-Schnittstelle implementieren.

Mit einem benutzerdefinierten Reducer (User-Defined Reducer, UDR) können Sie überflüssige Zeilen während der Datenextraktion (Import) entfernen. Darüber hinaus kann er zur Bearbeitung und Auswertung von Zeilen und Spalten verwendet werden. Auf der Grundlage der Programmierbarkeitslogik kann er zudem die zu extrahierenden Zeilen definieren.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Definieren und Verwenden eines benutzerdefinierten Reducers
Zum Definieren einer UDR-Klasse müssen wir eine `IReducer`-Schnittstelle mit optionalem `SqlUserDefinedReducer`-Attribut erstellen.

Diese Klassenschnittstelle muss eine Definition für die Überschreibung des `IEnumerable`-Schnittstellenrowsets enthalten.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Das Attribut **SqlUserDefinedReducer** gibt an, dass der Typ als benutzerdefinierter Reducer registriert werden muss. Diese Klasse kann nicht vererbt werden.
**SqlUserDefinedReducer** ist ein optionales Attribut für die Definition eines benutzerdefinierten Reducers. Es wird verwendet, um die IsRecursive-Eigenschaft zu definieren.

* bool     IsRecursive    
* **true** = Gibt an, ob dieser Reducer assoziativ und kommutativ ist.

Die wichtigsten Programmierbarkeitsobjekte sind **input** und **output**. Das Eingabeobjekt dient zum Aufzählen von Eingabezeilen. Die Ausgabe dient zum Festlegen von Ausgabezeilen als Ergebnis der Reduzierungsaktivität.

Zum Aufzählen der Eingabezeilen verwenden wir die `Row.Get`-Methode.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

Der Parameter für die `Row.Get`-Methode ist eine Spalte, die als Teil der `PRODUCE`-Klasse der `REDUCE`-Anweisung des U-SQL-Basisskripts übergeben wird. Wir müssen hier ebenfalls den richtigen Datentyp verwenden.

Verwenden Sie für die Ausgabe die `output.Set`-Methode.

Wichtig: Benutzerdefinierte Reducer geben nur Werte aus, die mit dem `output.Set`-Methodenaufruf definiert wurden.

```csharp
output.Set<string>("mycolumn", guid);
```

Die tatsächliche Reducerausgabe wird durch Aufrufen von `yield return output.AsReadOnly();` ausgelöst.

Im Anschluss finden Sie ein Beispiel für den Reducer:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

In diesem Szenario überspringt der Reducer Zeilen mit leerem Benutzernamen. Er liest für jede Zeile im Rowset die einzelnen erforderlichen Spalten und wertet dann die Länge des Benutzernamens aus. Die eigentliche Zeile wird nur ausgegeben, wenn der Längenwert des Benutzernamens größer 0 ist.

Im Anschluss sehen Sie ein U-SQL-Basisskript mit einem benutzerdefinierten Reducer:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über das U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)