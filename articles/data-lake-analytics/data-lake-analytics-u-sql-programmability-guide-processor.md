---
title: Programmierbarkeitshandbuch für benutzerdefinierten U-SQL-Prozessor für Azure Data Lake
description: Hier finden Sie Informationen zum U-SQL-UDO-Programmierbarkeitshandbuch für den benutzerdefinierten Prozessor.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512454"
---
# <a name="use-user-defined-processor"></a>Verwenden eines benutzerdefinierten Prozessors

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL-UDO: Benutzerdefinierter Prozessor
Der benutzerdefinierte Prozessor (User-Defined Processor, UDP) ist ein U-SQL-UDO, das die Verarbeitung eingehender Zeilen durch Anwenden von Programmierbarkeitsfeatures ermöglicht. Mit dem UDP können Sie bei Bedarf Spalten kombinieren, Werte ändern und neue Spalten hinzufügen. Im Wesentlichen unterstützt er die Verarbeitung eines Rowsets, um erforderliche Datenelemente zu erzeugen.

## <a name="how-to-define-and-use-user-defined-processor"></a>Definieren und Verwenden eines benutzerdefinierten Prozessors
Um einen UDP zu definieren, müssen wir eine `IProcessor`-Schnittstelle mit dem `SqlUserDefinedProcessor`-Attribut (für UDP optional) erstellen.

Diese Schnittstelle muss die Definition für die Überschreibung des `IRow`-Schnittstellenrowsets enthalten, wie im folgenden Beispiel zu sehen:

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** gibt an, dass der Typ als benutzerdefinierter Prozessor registriert werden muss. Diese Klasse kann nicht vererbt werden.

Das SqlUserDefinedProcessor-Attribut ist für die UDP-Definition **optional**.

Die wichtigsten Programmierbarkeitsobjekte sind **input** und **output**. Das Eingabeobjekt dient zum Aufzählen von Eingabespalten. Das Ausgabeobjekt dient zum Festlegen der Ausgabedaten als Ergebnis der Prozessoraktivität.

Zur Eingabespaltenenumeration verwenden wir die `input.Get`-Methode.

```csharp
string column_name = input.Get<string>("column_name");
```

Der Parameter für die `input.Get`-Methode ist eine Spalte, die als Teil der `PRODUCE`-Klausel der `PROCESS`-Anweisung des U-SQL-Basisskripts übergeben wird. Wir müssen hier den richtigen Datentyp verwenden.

Verwenden Sie für die Ausgabe die `output.Set`-Methode.

Wichtig: Der benutzerdefinierte Producer gibt nur Spalten und Werte aus, die mit dem `output.Set`-Methodenaufruf definiert wurden.

```csharp
output.Set<string>("mycolumn", mycolumn);
```

Die tatsächliche Prozessorausgabe wird durch Aufrufen von `return output.AsReadOnly();` ausgelöst.

Im Anschluss finden Sie ein Prozessorbeispiel:

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

In diesem Szenario erzeugt der Prozessor eine neue Spalte namens „full_description“, indem er die vorhandenen Spalten kombiniert (in diesem Fall: „user“ in Großbuchstaben und „des“). Darüber hinaus generiert er eine neue GUID und gibt den ursprünglichen und neuen GUID-Wert zurück.

Wie im vorherigen Beispiel zu sehen, können Sie C#-Methoden im Rahmen eines `output.Set`-Methodenaufrufs aufrufen.

Bei dem folgenden Beispiel handelt es sich um ein U-SQL-Basisskript mit einem benutzerdefinierten Prozessor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über das U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)