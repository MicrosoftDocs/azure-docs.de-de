---
title: Programmierbarkeitshandbuch für U-SQL-UDTs und -UDAGGs für Azure Data Lake
description: Erfahren Sie mehr über die Programmierbarkeit von U-SQL-UDTs und -UDAGGs, um in Azure Data Lake Analytics gute U-SQL-Skripts zu erstellen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: bf2e1e1bc30aeb3306d0a643eca4725d8765edac
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512485"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs



## <a name="use-user-defined-types-udt"></a>Verwenden benutzerdefinierter Typen: UDT
Benutzerdefinierte Typen (User-Defined Types, UDTs) sind ein weiteres Programmierbarkeitsfeature von U-SQL. Ein U-SQL-UDT verhält sich wie ein regulärer benutzerdefinierter C#-Typ. C# ist eine stark typisierte Sprache, die die Verwendung integrierter und benutzerdefinierter Typen ermöglicht.

Beliebige UDTs können von U-SQL nicht implizit serialisiert oder deserialisiert werden, wenn die UDT zwischen Scheitelpunkten in Rowsets übergeben wird. Der Benutzer muss daher die IFormatter-Schnittstelle verwenden, um einen expliziten Formatierer bereitzustellen. Dadurch erhält U-SQL die Serialisierungs- und Deserialisierungsmethoden für die UDT.

> [!NOTE]
> Die in U-SQL integrierten Extraktoren und Outputter können derzeit keine UDT-Daten in oder aus Dateien serialisieren oder deserialisieren – unabhängig davon, ob IFormatter festgelegt wurde. Wenn Sie also mithilfe der OUTPUT-Anweisung UDT-Daten in eine Datei schreiben oder mit einem Extraktor lesen, müssen Sie sie als Zeichenfolge oder Bytearray übergeben. Anschließend müssen Sie explizit den Serialisierungs- und Deserialisierungscode (sprich: die ToString()-Methode des UDTs) aufrufen. Benutzerdefinierte Extraktoren und Outputter können hingegen UDTs lesen und schreiben.

Angenommen, wir versuchen, den UDT in EXTRACTOR oder OUTPUTTER (außerhalb des vorherigen SELECT-Ausdrucks) zu verwenden, wie hier zu sehen:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

In diesem Fall erhalten wir folgende Fehlermeldung:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Für die Zusammenarbeit von UDT und Outputter müssen wir den UDT entweder mit der ToString()-Methode in eine Zeichenfolge serialisieren oder einen benutzerdefinierten Outputter erstellen.

UDTs können derzeit nicht in der GROUP BY-Klausel verwendet werden. Wenn der UDT in der GROUP BY-Klausel verwendet wird, wird der folgende Fehler ausgelöst:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Folgendermaßen definieren wir einen UDT:

1. Fügen Sie die folgenden Namespaces hinzu:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Fügen Sie `Microsoft.Analytics.Interfaces` hinzu. Dieses Element wird für die UDT-Schnittstellen benötigt. Außerdem wird `System.IO` unter Umständen zum Definieren der IFormatter-Schnittstelle benötigt.

3. Definieren Sie einen benutzerdefinierten Typ mit dem SqlUserDefinedType-Attribut.

**SqlUserDefinedType** wird verwendet, um in U-SQL die Definition eines Typs in einer Assembly als benutzerdefinierten Typ (UDT) zu markieren. Die Eigenschaften des Attributs spiegeln die physischen Merkmale des UDT wider. Diese Klasse kann nicht vererbt werden.

SqlUserDefinedType ist ein erforderliches Attribut für die UDT-Definition.

Der Konstruktor der Klasse:  

* SqlUserDefinedTypeAttribute (Typformatierung)

* Typformatierer: Erforderlicher Parameter zum Definieren eines UDT-Formatierers. Insbesondere muss hier der Typ der `IFormatter`-Schnittstelle übergeben werden.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Für einen typischen UDT muss außerdem die IFormatter-Schnittstelle definiert werden, wie im folgenden Beispiel zu sehen:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

Die `IFormatter`-Schnittstelle serialisiert und deserialisiert einen Objektgraph mit dem Stammtyp von \<typeparamref name="T">.

\<typeparam name="T">Der Stammtyp für Serialisierung und Deserialisierung des Objektgraphs.

* **Deserialize:** Deserialisiert die Daten im angegebenen Datenstrom und stellt das Objektdiagramm wieder her.

* **Serialize:** Serialisiert ein Objekt oder Objektdiagramm mit dem angegebenen Stamm in den angegebenen Datenstrom.

`MyType`-Instanz: Die Instanz des Typs.  
Writer `IColumnWriter`; Reader `IColumnReader`: Der zugrunde liegende Spaltendatenstrom.  
Kontext `ISerializationContext`: Enumeration, die eine Gruppe von Flags definiert, die den Quell- oder Zielkontext für den Datenstrom während der Serialisierung angibt.

* **Intermediate:** Gibt an, dass der Quell- oder Zielkontext kein permanenter Speicher ist.

* **Persistence:** Gibt an, dass der Quell- oder Zielkontext ein permanenter Speicher ist.

Als regulärer C#-Typ kann eine U-SQL-UDT-Definition Überschreibungen für Operatoren wie +/==/!= enthalten. Sie kann auch statische Methoden enthalten. Wenn wir diesen UDT beispielsweise als Parameter für eine U-SQL-MIN-Aggregatfunktion verwenden, müssen wir die Überschreibung des <-Operators definieren.

Weiter oben in dieser Anleitung haben wir uns mit einem Beispiel zur Identifizierung des Geschäftszeitraums ab einem bestimmten Datum im Format `Qn:Pn (Q1:P10)` beschäftigt. Das folgende Beispiel zeigt das Definieren eines benutzerdefinierten Typs für Geschäftszeitraumwerte.

Im Anschluss sehen Sie ein Beispiel für einen CodeBehind-Abschnitt mit UDT und IFormatter-Schnittstelle:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Der definierte Typ umfasst zwei Zahlen: Quartal und Monat. Die Operatoren `==/!=/>/<` und die statische Methode `ToString()` sind hier definiert.

Wie bereits erwähnt, kann der UDT in SELECT-Ausdrücken verwendet werden. In OUTPUTTER-/EXTRACTOR-Ausdrücken ist dies hingegen nur mit benutzerdefinierter Serialisierung möglich. Er muss entweder mithilfe von `ToString()` als Zeichenfolge serialisiert oder mit einem benutzerdefinierten OUTPUTTER-/EXTRACTOR-Ausdruck verwendet werden.

Jetzt betrachten wird die Verwendung des UDT. In einem CodeBehind-Abschnitt haben wir unsere GetFiscalPeriod-Funktion wie folgt geändert:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Wie Sie sehen, wird der Wert unseres FiscalPeriod-Typs zurückgegeben.

Hier finden Sie ein Beispiel zur weiteren Verwendung im U-SQL-Basisskript. Dieses Beispiel zeigt verschiedene Möglichkeiten des UDT-Aufrufs im U-SQL-Skript.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Im Anschluss folgt ein Beispiel für einen vollständigen CodeBehind-Abschnitt:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Benutzerdefinierte Aggregate: UDAGG
Benutzerdefinierte Aggregate sind alle aggregationsbezogenen Funktionen, die nicht standardmäßig in U-SQL verfügbar sind. Ein Beispiel wäre etwa ein Aggregat zur Durchführung benutzerdefinierter mathematischer Berechnungen, Zeichenfolgenverkettungen oder Bearbeitungen mit Zeichenfolgen.

Die Basisklassendefinition eines benutzerdefinierten Aggregats sieht wie folgt aus:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** gibt an, dass der Typ als benutzerdefiniertes Aggregat registriert werden muss. Diese Klasse kann nicht vererbt werden.

Das SqlUserDefinedType-Attribut ist für die UDAGG-Definition **optional**.


Mithilfe der Basisklasse können Sie drei abstrakte Parameter übergeben: zwei als Eingabe und einen als Ergebnis. Die Datentypen sind variabel und sollten während der Klassenvererbung definiert werden.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** wird während der Berechnung für jede Gruppe einmal aufgerufen. Bietet eine Initialisierungsroutine für jede Aggregationsgruppe.  
* **Accumulate** wird für jeden Wert einmal ausgeführt. Stellt die Hauptfunktion für den Aggregationsalgorithmus bereit. Kann zum Aggregieren von Werten mit verschiedenen, während der Klassenvererbung definierten Datentypen verwendet werden. Kann zwei Parameter mit variablen Datentypen akzeptieren.
* **Terminate** wird am Ende der Verarbeitung einmal pro Aggregationsgruppe ausgeführt, um das Ergebnis für die einzelnen Gruppen auszugeben.

Verwenden Sie die Klassendefinition wie folgt, um korrekte Ein- und Ausgabedatentypen zu deklarieren:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Erster Parameter für „accumulate“
* T2: Zweiter Parameter für „accumulate“
* TResult: Rückgabetyp von „terminate“

Beispiel:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

oder

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Verwenden von UDAGGs in U-SQL
Wenn Sie ein UDAGG verwenden möchten, definieren Sie es zuerst im CodeBehind, oder verweisen Sie über die vorhandene Programmierbarkeits-DLL darauf (wie weiter oben bereits erläutert).

Verwenden Sie dann die folgende Syntax:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Hier sehen Sie ein UDAGG-Beispiel:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

U-SQL-Basisskript:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In diesem Szenario verketten wir Klassen-GUIDs für bestimmte Benutzer.

## <a name="next-steps"></a>Nächste Schritte
* [U-SQL-Programmierbarkeitshandbuch – Übersicht](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL-Programmierbarkeitshandbuch – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
