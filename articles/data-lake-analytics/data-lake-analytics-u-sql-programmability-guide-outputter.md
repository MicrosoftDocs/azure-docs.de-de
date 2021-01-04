---
title: Programmierbarkeitshandbuch für benutzerdefinierten U-SQL-Outputter für Azure Data Lake
description: Erfahren Sie mehr über das U-SQL-UDO-Programmierbarkeitshandbuch für den benutzerdefinierten Outputter.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512445"
---
# <a name="use-user-defined-outputter"></a>Verwenden eines benutzerdefinierten Outputters

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL-UDO: benutzerdefinierter Outputter
Der benutzerdefinierte Outputter ist ein weiteres U-SQL-UDO, das die Erweiterung der integrierten U-SQL-Funktionen ermöglicht. Ähnlich wie beim Extraktor gibt es verschiedene integrierte Outputter.

* *Outputters.Text()* : Schreibt Daten in durch Trennzeichen getrennte Textdateien mit verschiedenen Codierungen.
* *Outputters.Csv():* Schreibt Daten in durch Trennzeichen getrennte Textdateien (CSV) mit verschiedenen Codierungen.
* *Outputters.Tsv():* Schreibt Daten in durch Tabstopp getrennte Textdateien (TSV) mit verschiedenen Codierungen.

Benutzerdefinierte Outputter ermöglichen Ihnen, Daten in einem benutzerdefinierten Format zu schreiben. Dies kann bei folgenden Aufgaben hilfreich sein:

* Schreiben von Daten in teilweise strukturierte oder unstrukturierte Dateien
* Schreiben von Daten mit nicht unterstützter Codierung
* Ändern von Ausgabedaten oder Hinzufügen von benutzerdefinierten Attributen

## <a name="how-to-define-and-use-user-defined-outputter"></a>Definieren und Verwenden des benutzerdefinierten Outputters
Um einen benutzerdefinierten Outputter zu definieren, müssen wir die `IOutputter`-Schnittstelle erstellen.

Im Anschluss sehen Sie die `IOutputter`-Basisklassenimplementierung:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alle Eingabeparameter des Outputters (wie etwa Spalten-/Zeilentrennzeichen und die Codierung) müssen im Konstruktor der Klasse definiert werden. Die `IOutputter`-Schnittstelle muss auch eine Definition für die `void Output`-Überschreibung enthalten. Das `[SqlUserDefinedOutputter(AtomicFileProcessing = true)`-Attribut kann optional für die Verarbeitung atomischer Dateien festgelegt werden. Ausführlichere Informationen finden Sie im weiteren Verlauf.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` wird für jede Eingabezeile aufgerufen. Es gibt das Rowset `IUnstructuredWriter output` zurück.
* Die Konstruktorklasse dient zum Übergeben von Parametern an den benutzerdefinierten Outputter.
* `Close` dient zum optionalen Überschreiben, um den ressourcenintensiven Zustand aufzuheben oder zu ermitteln, wann die letzte Zeile geschrieben wurde.

Das **SqlUserDefinedOutputter**-Attribut gibt an, dass der Typ als benutzerdefinierter Outputter registriert werden muss. Diese Klasse kann nicht vererbt werden.

„SqlUserDefinedOutputter“ ist ein optionales Attribut für die Definition eines benutzerdefinierten Outputters. Das Element wird verwendet, um die AtomicFileProcessing-Eigenschaft zu definieren.

* bool     AtomicFileProcessing   

* **true:** Gibt an, dass dieser Outputter atomische Ausgabedateien (JSON, XML, ...) benötigt.
* **false:** Gibt an, dass dieser Outputter aufgeteilte/verteilte Dateien (CSV, SEQ, ...) behandeln kann.

Die wichtigsten Programmierbarkeitsobjekte sind **row** und **output**. Das Objekt **row** dient zum Aufzählen von Ausgabedaten als `IRow`-Schnittstelle. **Output** wird verwendet, um die Ausgabedaten auf die Zieldatei festzulegen.

Der Zugriff auf die Ausgabedaten erfolgt über die `IRow`-Schnittstelle. Ausgabedaten werden jeweils zeilenweise übergeben.

Die einzelnen Werte werden durch den Aufruf der Get-Methode der IRow-Schnittstelle aufgezählt:

```csharp
row.Get<string>("column_name")
```

Einzelne Spaltennamen können durch Aufrufen von `row.Schema` ermittelt werden:

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Dieser Ansatz ermöglicht die Erstellung eines flexiblen Outputters für alle Metadatenschemas.

Die Ausgabedaten werden mithilfe von `System.IO.StreamWriter` in eine Datei geschrieben. Der Streamparameter wird im Rahmen von `IUnstructuredWriter output` auf `output.BaseStream` festgelegt.

Wichtig: Der Datenpuffer muss nach jeder Zeileniteration in die Datei geleert werden. Darüber hinaus muss das `StreamWriter`-Objekt mit aktiviertem Disposable-Attribut (Standardeinstellung) und mit dem Schlüsselwort **using** verwendet werden:

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Rufen Sie andernfalls nach jeder Iteration explizit die Flush()-Methode auf. Dies wird im folgenden Beispiel veranschaulicht.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Festlegen von Kopf- und Fußzeilen für benutzerdefinierte Outputter
Verwenden Sie zum Festlegen einer Kopfzeile einen einzelnen Durchlauf der Iterationsausführung.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Der Code im ersten `if (isHeaderRow)`-Block wird nur einmal ausgeführt.

Verwenden Sie für die Fußzeile den Verweis auf die Instanz des `System.IO.Stream`-Objekts (`output.BaseStream`). Schreiben Sie die Fußzeile in die Close()-Methode der `IOutputter`-Schnittstelle.  (Weitere Informationen finden Sie im folgenden Beispiel.)

Das folgende Beispiel zeigt einen benutzerdefinierten Outputter:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

U-SQL-Basisskript:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Hierbei handelt es sich um einen HTML-Outputter, der eine HTML-Datei mit Tabellendaten erstellt.

### <a name="call-outputter-from-u-sql-base-script"></a>Aufrufen des Outputters über das U-SQL-Basisskript
Für den Aufruf eines benutzerdefinierten Outputters aus dem U-SQL-Basisskript muss die neue Instanz des Outputterobjekts erstellt werden.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Um die Erstellung einer Instanz des Objekts im Basisskript zu vermeiden, können wir einen Funktionswrapper erstellen, wie in unserem vorherigen Beispiel zu sehen:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

In diesem Fall sieht der ursprüngliche Aufruf wie folgt aus:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Nächste Schritte
* [U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)