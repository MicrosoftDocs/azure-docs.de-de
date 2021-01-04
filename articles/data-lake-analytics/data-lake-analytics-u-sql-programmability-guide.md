---
title: U-SQL-Programmierbarkeitshandbuch für Azure Data Lake
description: Hier finden Sie eine Übersicht über U-SQL sowie Informationen zur UDF-Programmierbarkeit in Azure Data Lake Analytics, um die Erstellung guter U-SQL-Skripts zu ermöglichen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510861"
---
# <a name="u-sql-programmability-guide-overview"></a>Übersicht über das U-SQL-Programmierbarkeitshandbuch

U-SQL ist eine für Big Data-Workloads konzipierte Abfragesprache. Eines der einzigartigen Features von U-SQL ist die Kombination der SQL-ähnlichen deklarativen Sprache mit dem Erweiterungs- und Programmierbarkeitspotenzial von C#. In diesem Handbuch konzentrieren wir uns auf die durch C# ermöglichte Erweiterbarkeit und Programmierbarkeit der U-SQL-Sprache.

## <a name="requirements"></a>Requirements (Anforderungen)

Laden Sie [Azure Data Lake Tools für Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) herunter, und installieren Sie es.

## <a name="get-started-with-u-sql"></a>Erste Schritte mit U-SQL  

Sehen Sie sich das folgende U-SQL-Skript an:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Dieses Skript definiert zwei Rowsets: `@a` und `@results`. Rowset `@results` wird von `@a` definiert.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-Typen und -Ausdrücke in einem U-SQL-Skript

Bei einem U-SQL-Ausdruck handelt es sich um eine Kombination aus einem C#-Ausdruck und logischen U-SQL-Operatoren wie `AND`, `OR` und `NOT`. U-SQL-Ausdrücke können mit SELECT, EXTRACT, WHERE, HAVING, GROUP BY und DECLARE verwendet werden. Das folgende Skript analysiert beispielsweise eine Zeichenfolge als DateTime-Wert:

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Der folgende Codeausschnitt analysiert eine Zeichenfolge als DateTime-Wert in einer DECLARE-Anweisung:

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Verwenden von C#-Ausdrücken für Datentypkonvertierungen

Im folgenden Beispiel sehen Sie, wie Sie mithilfe von C#-Ausdrücken eine DateTime-Datenkonvertierung durchführen. In diesem speziellen Szenario werden DateTime-Zeichenfolgendaten in das Standard-DateTime-Format mit der Zeitnotation „00:00:00“ für Mitternacht konvertiert.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Verwenden von C#-Ausdrücken für das aktuelle Datum

Das aktuelle Datum können wir mithilfe des folgenden C#-Ausdrucks abrufen: `DateTime.Now.ToString("M/d/yyyy")`

Das folgende Beispiel zeigt, wie Sie diesen Ausdruck in einem Skript verwenden:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Verwenden von .NET-Assemblys

Das U-SQL-Erweiterbarkeitsmodell hängt stark davon ab, benutzerdefinierten Code aus .NET-Assemblys hinzufügen zu können. 

### <a name="register-a-net-assembly"></a>Registrieren einer .NET-Assembly

Verwenden Sie die `CREATE ASSEMBLY`-Anweisung, um eine .NET-Assembly in einer U-SQL-Datenbank zu platzieren. Anschließend können U-SQL-Skripts diese Assemblys mithilfe der `REFERENCE ASSEMBLY`-Anweisung verwenden. 

Der folgende Code zeigt, wie Sie eine Assembly registrieren:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Der folgende Code zeigt, wie Sie auf eine Assembly verweisen:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Eine ausführlichere Beschreibung dieses Themas finden Sie unter [How to register U-SQL Assemblies in your U-SQL Catalog](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) (Registrieren von U-SQL-Assemblys im U-SQL-Katalog).


### <a name="use-assembly-versioning"></a>Verwenden der Versionsverwaltung für Assemblys
U-SQL verwendet derzeit .NET Framework 4.7.2. Achten Sie daher darauf, dass Ihre eigenen Assemblys mit dieser Laufzeitversion kompatibel sind.

Wie bereits erwähnt, wird Code von U-SQL in einem 64-Bit-Format (x64) ausgeführt. Stellen Sie daher sicher, dass Ihr Code für x64 kompiliert wird. Andernfalls tritt der zuvor gezeigte Formatfehler auf.

Jede hochgeladene Assembly-DLL und Ressourcendatei (beispielsweise eine andere Laufzeit, eine native Assembly oder eine Konfigurationsdatei) darf maximal 400 MB groß sein. Die Gesamtgröße der Ressourcen, die entweder mithilfe von „DEPLOY RESOURCE“ oder mittels Verweis auf Assemblys und deren zusätzliche Dateien bereitgestellt werden, darf maximal 3 GB betragen.

Beachten Sie außerdem, dass jede U-SQL-Datenbank nur eine einzelne Version einer bestimmten angegebenen Assembly enthalten kann. Wenn Sie also beispielsweise sowohl Version 7 als auch Version 8 der Bibliothek „Newtonsoft Json.NET“ benötigen, müssen diese in zwei verschiedenen Datenbanken registriert werden. Darüber hinaus kann jedes Skript nur auf eine einzelne Version einer bestimmten Assembly-DLL verweisen. Hierbei folgt U-SQL der C#-Semantik für die Assembly- und Versionsverwaltung.

## <a name="use-user-defined-functions-udf"></a>Verwenden benutzerdefinierter Funktionen: UDF
Benutzerdefinierte Funktionen (User Defined Functions, UDFs) von U-SQL sind Programmierroutinen, die Parameter akzeptieren, eine Aktion (beispielsweise eine komplexe Berechnung) ausführen und das Ergebnis dieser Aktion als Wert zurückgeben. Der Rückgabewert der benutzerdefinierten Funktion kann nur ein einzelner Skalarwert sein. Eine U-SQL-UDF kann im U-SQL-Basisskript wie jede andere skalare Funktion in C# aufgerufen werden.

Es empfiehlt sich, benutzerdefinierte U-SQL-Funktionen als **öffentlich** und **statisch** zu initialisieren.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Sehen wir uns zunächst das einfache Beispiel zur UDF-Erstellung an.

In diesem Szenario müssen wir den Geschäftszeitraum bestimmen – einschließlich des Geschäftsquartals und -monats der ersten Anmeldung für einen bestimmten Benutzer. Der erste Geschäftsmonat des Jahres ist in unserem Szenario der Juni.

Zur Berechnung des Geschäftszeitraums verwenden wir die folgende C#-Funktion:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
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

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Sie berechnet einfach Geschäftsmonat und -quartal und gibt einen Zeichenfolgenwert zurück. Für Juni (den ersten Monat des ersten Geschäftsquartals) verwenden wir „Q1:P1“. Für Juli verwenden wir „Q1:P2“. Und so weiter.

Dies ist eine reguläre C#-Funktion, die wir in unserem U-SQL-Projekt verwenden.

Der CodeBehind-Abschnitt sieht in diesem Szenario wie folgt aus:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
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

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Jetzt rufen wir diese Funktion aus dem U-SQL-Basisskript heraus auf. Zu diesem Zweck müssen wir für die Funktion einen vollqualifizierten Namen angeben und dabei auch den Namespace einbeziehen. In diesem Fall ist das „NameSpace.Class.Function(parameter)“.
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Hier sehen Sie das tatsächliche U-SQL-Basisskript:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Im Anschluss finden Sie die Ausgabedatei der Skriptausführung:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Dieses Beispiel zeigt eine einfache Verwendung einer Inline-UDF in U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Beibehalten des Zustands zwischen UDF-Aufrufen
U-SQL-C#-Programmierbarkeitsobjekte können komplexer sein und Interaktivität über die globalen CodeBehind-Variablen nutzen. Sehen wir uns einmal das folgende Geschäftsszenario an:

In großen Organisationen können Benutzer zwischen Varianten interner Anwendungen wechseln. Beispiele wären etwa Microsoft Dynamics CRM und Power BI. Kunden möchten unter Umständen mithilfe einer Telemetrieanalyse ermitteln, wie Benutzer zwischen verschiedenen Anwendungen wechseln, welche Nutzungstrends zu beobachten sind und Ähnliches. Das Unternehmen möchte so die Anwendungsnutzung optimieren. Außerdem möchte das Unternehmen unter Umständen verschiedene Anwendungen oder bestimmte Anmelderoutinen miteinander kombinieren.

Hierzu müssen wir Sitzungs-IDs und die Verzögerung für die letzte Sitzung bestimmen.

Wir müssen eine vorherige Anmeldung finden und sie allen Sitzungen zuweisen, die für die gleiche Anwendung generiert werden. Die erste Herausforderung besteht darin, dass es uns mit dem U-SQL-Basisskript nicht möglich ist, Berechnungen auf bereits berechnete Spalten mit LAG-Funktion anzuwenden. Die zweite Herausforderung ist, dass wir die bestimmte Sitzung für alle Sitzungen im gleichen Zeitraum halten müssen.

Dieses Problem lösen wir mit einer globalen Variablen in einem CodeBehind-Abschnitt: `static public string globalSession;`.

Diese globale Variable wird bei der Skriptausführung auf das gesamte Rowset angewendet.

Hier ist der CodeBehind-Abschnitt unseres U-SQL-Programms:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Dieses Beispiel zeigt die globale Variable `static public string globalSession;`, die innerhalb der Funktion `getStampUserSession` verwendet und bei jeder Änderung des Sitzungsparameters neu initialisiert wird.

Das U-SQL-Basisskript sieht wie folgt aus:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Die Funktion `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` wird hier während der Berechnung des zweiten Arbeitsspeicherrowsets aufgerufen. Sie übergibt die Spalte `UserSessionTimestamp` und gibt den Wert zurück, bis `UserSessionTimestamp` geändert wurde.

Die Ausgabedatei sieht wie folgt aus:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Dieses Beispiel zeigt ein etwas komplizierteres Szenario, in dem wir eine globale Variable im CodeBehind-Abschnitt verwenden und auf das gesamte Arbeitsspeicherrowset anwenden.

## <a name="next-steps"></a>Nächste Schritte
* [U-SQL-Programmierbarkeitshandbuch: UDTs und UDAGGs](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL-Programmierbarkeitshandbuch – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)