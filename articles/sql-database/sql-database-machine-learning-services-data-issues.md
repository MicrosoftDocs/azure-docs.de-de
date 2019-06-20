---
title: Arbeiten mit R- und SQL-Datentypen und -objekten
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Es wird beschrieben, wie Sie mit Datentypen und -objekten in R mit Azure SQL-Datenbank und Machine Learning Services (Vorschauversion) arbeiten. Außerdem wird auf häufige Probleme eingegangen, die ggf. auftreten können.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 01d3af14963e92393d34a952bddc8097b7b08f18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232609"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Arbeiten mit R- und SQL-Daten in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)

In diesem Artikel werden einige häufige Probleme beschrieben, die ggf. auftreten können, wenn Sie Daten zwischen R und SQL-Datenbank in [Machine Learning Services (mit R) in Azure SQL-Datenbank](sql-database-machine-learning-services-overview.md) verschieben. Das Wissen, dass Sie sich in dieser Übung aneignen, stellt eine wichtige Grundlage für die Arbeit mit Daten in Ihrem eigenen Skript dar.

Beispiele für häufige Probleme, die auftreten können:

- Datentypen stimmen manchmal nicht überein
- Es kann zu impliziten Konvertierungen kommen
- Umwandlungs- und Konvertierungsvorgänge sind ggf. erforderlich
- Für R und SQL werden unterschiedliche Datenobjekte genutzt

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

- Zum Ausführen des Beispielcodes in diesen Übungen müssen Sie zuerst eine Azure SQL-Datenbank-Instanz mit Machine Learning Services (mit R) aktivieren. Während der öffentlichen Vorschauphase führt Microsoft für Sie das Onboarding durch und aktiviert das maschinelle Lernen für Ihre vorhandene oder neue Datenbank. Führen Sie die Schritte in [Registrieren für die Vorschauversion](sql-database-machine-learning-services-overview.md#signup) aus.

- Vergewissern Sie sich, dass die aktuelle Version von [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) installiert ist. In dieser Schnellstartanleitung verwenden Sie SSMS, obwohl Sie auch mithilfe anderer Datenbankverwaltungen oder Abfragetools R-Skripts ausführen können.

## <a name="working-with-a-data-frame"></a>Arbeiten mit einem Datenrahmen

Wenn mit Ihrem Skript Ergebnisse von R für SQL zurückgegeben werden, muss die Rückgabe der Daten als Datenrahmen (**data.frame**) erfolgen. Alle anderen Objekttypen, die Sie in Ihrem Skript generieren – ob als Liste, Faktor, Vektor oder Binärdaten –, müssen in einen Datenrahmen konvertiert werden, wenn Sie sie als Teil der Ergebnisse von gespeicherten Prozeduren ausgeben möchten. Glücklicherweise gibt es mehrere R-Funktionen zur Unterstützung von sich ändernden Objekten in einem Datenrahmen. Sie können auch ein binäres Modell serialisieren und in einem Datenrahmen zurückgeben. Dies führen Sie weiter unten in diesem Artikel durch.

Zunächst experimentieren wir mit einigen grundlegenden R-Objekten – Vektoren, Matrizen und Listen – und sehen uns an, wie sich die an SQL übergebene Ausgabe durch die Konvertierung in einen Datenrahmen ändert.

Vergleichen Sie diese beiden „Hello World“-Skripts in R. Sie sehen nahezu identisch aus, aber im ersten Skript wird eine einzelne Spalte mit drei Werten zurückgegeben, während im zweiten Skript drei Spalten mit nur jeweils einem Wert zurückgegeben werden.

**Beispiel 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Beispiel 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Warum sind die Ergebnisse so unterschiedlich?

Die Antwort kann normalerweise mit dem R-Befehl `str()` ermittelt werden. Fügen Sie die Funktion `str(object_name)` an einer beliebigen Stelle in Ihrem R-Skript hinzu, damit das Datenschema des angegebenen R-Objekts als Informationsnachricht zurückgegeben wird. Sie können die Nachrichten in SSMS auf der Registerkarte **Nachrichten** anzeigen.

Fügen Sie die Zeile `str(OutputDataSet)` wie folgt am Ende der Variablen `@script` in jeder Anweisung ein, um zu ermitteln, warum Beispiel 1 und 2 so unterschiedliche Ergebnisse aufweisen:

**Beispiel 1 mit hinzugefügter str-Funktion**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Beispiel 2 mit hinzugefügter str-Funktion**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Überprüfen Sie nun den Text unter **Nachrichten**, um zu ermitteln, warum sich die Ausgabe unterscheidet.

**Ergebnisse: Beispiel 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Ergebnisse: Beispiel 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Sie sehen, dass eine geringfügige Änderung der R-Syntax eine große Auswirkung auf das Schema mit den Ergebnissen hat. Die Unterschiede der R-Datentypen sind in [„Advanced R“ von Hadley Wickham](http://adv-r.had.co.nz) im Abschnitt *Data Structures* ausführlich beschrieben.

Vorerst sollten Sie nur bedenken, dass Sie die erwarteten Ergebnisse überprüfen sollten, wenn Sie für R-Objekte Datenrahmen verwenden.

> [!TIP]
> Sie können auch R-Identitätsfunktionen verwenden, z. B. `is.matrix` bzw. `is.vector`, um Informationen zur internen Datenstruktur zurückzugeben.

## <a name="implicit-conversion-of-data-objects"></a>Implizite Konvertierung von Datenobjekten

Jedes R-Datenobjekt verfügt über eigene Regeln zur Verarbeitung von Werten, wenn diese mit anderen Datenobjekten kombiniert werden. Es wird auch überprüft, ob die beiden Datenobjekte die gleiche Anzahl von Dimensionen aufweisen oder ob ein Datenobjekt heterogene Datentypen enthält.

Angenommen, Sie möchten mit R eine Matrixmultiplikation durchführen. Eine Matrix mit nur einer Spalte und drei Werten soll mit einem Array mit vier Werten multipliziert werden, und Sie erwarten als Ergebnis eine 4x3-Matrix.

Erstellen Sie zuerst eine kleine Tabelle mit Testdaten.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Führen Sie nun das folgende Skript aus.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Im Hintergrund wird die Spalte mit drei Werten in eine Matrix mit einer Spalte konvertiert. Da eine Matrix lediglich einen Sonderfall eines Arrays in R darstellt, wird das Array `y` implizit in eine Matrix mit nur einer Spalte umgewandelt, damit die beiden Argumente konform sind.

**Ergebnisse**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

Beachten Sie aber, was passiert, wenn Sie die Größe des Arrays `y` ändern.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

R gibt jetzt einen einzelnen Wert als Ergebnis zurück.

**Ergebnisse**
    
|Col1|
|---|
|1542|

Warum? In diesem Fall gibt R das innere Produkt als Matrix zurück, weil die beiden Argumente als Vektoren gleicher Länge verarbeitet werden können.  Dies ist das erwartete Verhalten gemäß den Regeln der linearen Algebra. Es kann hierbei aber zu Problemen kommen, wenn von Ihrer Downstreamanwendung erwartet wird, dass sich das Ausgabeschema nie ändert!

## <a name="merge-or-multiply-columns-of-different-length"></a>Zusammenführen oder Multiplizieren von Spalten unterschiedlicher Länge

R bietet hohe Flexibilität, was das Arbeiten mit Vektoren unterschiedlicher Größe und das Kombinieren dieser spaltenähnlichen Strukturen zu Datenrahmen betrifft. Die Liste mit den Vektoren kann wie eine Tabelle aussehen, aber es werden nicht alle Regeln eingehalten, die für Datenbanktabellen gelten.

Mit dem folgenden Skript wird beispielsweise ein numerisches Array der Länge 6 definiert und unter der R-Variablen `df1` gespeichert. Das numerische Array wird anschließend mit den ganzen Zahlen der Tabelle „RTestData“ (weiter oben erstellt) kombiniert, die drei (3) Werte enthält, um den neuen Datenrahmen `df2` zu erstellen.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Zum Ausfüllen des Datenrahmens wiederholt R die aus „RTestData“ abgerufenen Elemente so oft wie nötig, um die gleiche Anzahl von Elementen wie im Array `df1` zu erzielen.

**Ergebnisse**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Beachten Sie, dass ein Datenrahmen nur wie eine Tabelle aussieht und eigentlich eine Liste mit Vektoren ist.

## <a name="cast-or-convert-sql-data"></a>Umwandeln oder Konvertieren von SQL-Daten

Für R und SQL werden nicht die gleichen Datentypen verwendet. Wenn Sie eine Abfrage in SQL ausführen, um Daten abzurufen, und diese Daten anschließend an die R-Runtime übergeben, wird normalerweise eine Art von impliziter Konvertierung durchgeführt. Es werden weitere Konvertierungen durchgeführt, wenn Sie Daten von R an SQL zurückgeben.

- SQL überträgt die Daten von der Abfrage an den R-Prozess und konvertiert sie in eine interne Darstellung, um die Effizienz zu erhöhen.
- Die R-Runtime lädt die Daten in eine data.frame-Variable und führt eigene Vorgänge für die Daten durch.
- Die Datenbank-Engine gibt die Daten an SQL zurück, indem eine geschützte interne Verbindung verwendet wird und die Daten in Form von SQL-Datentypen dargestellt werden.
- Sie rufen die Daten ab, indem Sie eine Verbindung mit SQL über eine Client- oder Netzwerkbibliothek herstellen, mit der SQL-Abfragen ausgegeben und tabellarische Datasets verarbeitet werden können. Diese Clientanwendung kann sich unter Umständen auch auf andere Art und Weise auf die Daten auswirken.

Führen Sie zur Verdeutlichung der Funktionsweise wie unten angegeben eine Abfrage für das Data Warehouse [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) aus. In dieser Ansicht werden Verkaufsdaten zurückgegeben, die zum Erstellen von Vorhersagen verwendet werden.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Sie können eine beliebige Version von AdventureWorks verwenden oder eine andere Abfrage erstellen, indem Sie eine eigene Datenbank nutzen. Das Ziel besteht in der Verarbeitung von Daten, die Text, Datums-/Uhrzeitangaben und numerische Werte enthalten.

Versuchen Sie jetzt, diese Abfrage als Eingabe für die gespeicherte Prozedur zu verwenden.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Wenn Sie einen Fehler erhalten, müssen Sie unter Umständen einige Änderungen am Abfragetext vornehmen. Beispielsweise muss das Zeichenfolgenprädikat in der WHERE-Klausel doppelt in einfache Anführungszeichen gesetzt werden.

Wenn die Abfrage funktioniert, sollten Sie sich die Ergebnisse der `str`-Funktion ansehen, um zu ermitteln, wie die Eingabedaten von R verarbeitet werden.

**Ergebnisse**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- Die Spalte für Datums-/Uhrzeitangaben wurde mit dem R-Datentyp **POSIXct** verarbeitet.
- Die Textspalte „ProductSeries“ wurde als **factor** angegeben. Dies steht für eine Kategorievariable. Zeichenfolgenwerte werden standardmäßig als Faktoren verarbeitet. Wenn Sie eine Zeichenfolge an R übergeben, wird sie in eine ganze Zahl für die interne Verwendung konvertiert und in der Ausgabe dann wieder der Zeichenfolge zugeordnet.

## <a name="summary"></a>Zusammenfassung

Diese Beispiele sind zwar nur kurz, aber sie verdeutlichen die Notwendigkeit einer Überprüfung der Auswirkungen von Datenkonvertierungen, wenn SQL-Abfragen als Eingabe übergeben werden. Da einige SQL-Datentypen von R nicht unterstützt werden, sollten Sie diese Vorgehensweisen kennen, um Fehler zu vermeiden:

- Testen Sie Ihre Daten vorher, und überprüfen Sie die Spalten bzw. Werte in Ihrem Schema, die bei der Übergabe an R-Code ein Problem darstellen können.
- Geben Sie Spalten in Ihrer Eingabedatenquelle einzeln an, anstatt `SELECT *` zu verwenden, und machen Sie sich damit vertraut, wie die einzelnen Spalten verarbeitet werden.
- Führen Sie nach Bedarf explizite Umwandlungen durch, wenn Sie Ihre Eingabedaten vorbereiten, um Überraschungen zu vermeiden.
- Vermeiden Sie die Übergabe von Spalten mit Daten (z. B. GUIDs oder rowguids), die Fehler verursachen und für die Modellierung nicht hilfreich sind.

Weitere Informationen zu unterstützten und nicht unterstützten R-Datentypen finden Sie unter [Data type mappings between R and SQL Server](/sql/advanced-analytics/r/r-libraries-and-data-types) (Datentypzuordnungen zwischen R und SQL Server).
