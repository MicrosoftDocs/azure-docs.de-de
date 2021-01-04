---
title: Programmierbarkeitshandbuch für benutzerdefinierten U-SQL-Extraktor für Azure Data Lake
description: Hier finden Sie Informationen zum U-SQL-UDO-Programmierbarkeitshandbuch für den benutzerdefinierten Extraktor.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a3613c2b95f13e6bbaaf570f522ad1f7b7edd756
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512452"
---
# <a name="use-user-defined-extractor"></a>Verwenden eines benutzerdefinierten Extraktors

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL-UDO: Benutzerdefinierter Extraktor
In U-SQL können Sie externe Daten mithilfe einer EXTRACT-Anweisung importieren. Eine EXTRACT-Anweisung kann integrierte UDO-Extraktoren verwenden:  

* *Extractors.Text()* : Ermöglicht das Extrahieren aus durch Trennzeichen getrennten Textdateien mit verschiedenen Codierungen.

* *Extractors.Csv():* Ermöglicht das Extrahieren aus durch Trennzeichen getrennten Dateien (CSV) mit verschiedenen Codierungen.

* *Extractors.Tsv():* Ermöglicht das Extrahieren aus durch Tabstopp getrennten Dateien (TSV) mit verschiedenen Codierungen.

Es empfiehlt sich unter Umständen, einen benutzerdefinierten Extraktor zu entwickeln. Dies kann hilfreich sein, wenn wir während des Datenimports eine der folgenden Aufgaben ausführen möchten:

* Ändern von Eingabedaten durch Aufteilen von Spalten und Ändern einzelner Werte. Die PROCESSOR-Funktion eignet sich besser zum Kombinieren von Spalten.
* Analysieren unstrukturierter Daten wie Webseiten und E-Mails oder teilweise unstrukturierter Daten wie XML/JSON.
* Analysieren von Daten mit nicht unterstützter Codierung.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Definieren und Verwenden eines benutzerdefinierten Extraktors
Um einen benutzerdefinierten Extraktor (User-Defined Extractor, UDE) zu definieren, müssen wir eine `IExtractor`-Schnittstelle erstellen. Alle Eingabeparameter des Extraktors (wie etwa Spalten-/Zeilentrennzeichen und die Codierung) müssen im Konstruktor der Klasse definiert werden. Die `IExtractor`-Schnittstelle muss auch eine Definition für die `IEnumerable<IRow>`-Überschreibung enthalten:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Das Attribut **SqlUserDefinedExtractor** gibt an, dass der Typ als benutzerdefinierter Extraktor registriert werden muss. Diese Klasse kann nicht vererbt werden.

SqlUserDefinedExtractor ist ein optionales Attribut für die UDE-Definition. Es wird verwendet, um die AtomicFileProcessing-Eigenschaft für das UDE-Objekt zu definieren.

* bool     AtomicFileProcessing   

* **true:** Gibt an, dass dieser Extraktor atomische Eingabedateien (JSON, XML, ...) benötigt.
* **false:** Gibt an, dass dieser Extraktor aufgeteilte/verteilte Dateien (CSV, SEQ, ...) behandeln kann.

Die wichtigsten UDE-Programmierbarkeitsobjekte sind **input** und **output**. Das Eingabeobjekt dient zum Aufzählen von Eingabedaten als `IUnstructuredReader`. Das Ausgabeobjekt dient zum Festlegen von Ausgabedaten als Ergebnis der Extraktoraktivität.

Der Zugriff auf die Eingabedaten erfolgt über `System.IO.Stream` und `System.IO.StreamReader`.

Für die Eingabespaltenenumeration teilen wir zunächst den Eingabedatenstrom mit Zeilentrennzeichen auf.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Dann teilen wir die Eingabezeile weiter in Spaltenteile auf.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Zum Festlegen der Ausgabedaten verwenden wir die `output.Set`-Methode.

Wichtig: Der benutzerdefinierte Extraktor gibt nur Spalten und Werte aus, die mit der Ausgabe definiert wurden. Wir legen den Methodenaufruf fest.

```csharp
output.Set<string>(count, part);
```

Die tatsächliche Extraktorausgabe wird durch Aufrufen von `yield return output.AsReadOnly();` ausgelöst.

Hier sehen Sie das Extraktorbeispiel:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

In diesem Szenario generiert der Extraktor die GUID für die Spalte „guid“ neu und konvertiert die Werte der Spalte „user“ in Großbuchstaben. Benutzerdefinierte Extraktoren können durch Analysieren der Eingabedaten und deren Bearbeitung kompliziertere Ergebnisse hervorbringen.

Im Anschluss sehen Sie ein U-SQL-Basisskript mit einem benutzerdefinierten Extraktor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über das U-SQL-Programmierbarkeitshandbuch](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)