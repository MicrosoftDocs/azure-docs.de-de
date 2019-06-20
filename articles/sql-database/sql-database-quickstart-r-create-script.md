---
title: Erstellen und Ausführen einfacher R-Skripts
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ausführen einfacher R-Skripts in Machine Learning Services von Azure SQL-Datenbank (Vorschauversion).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: cfc70b3d8e364c25ccf9fd221699695641a66ef0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708591"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Erstellen und Ausführen einfacher R-Skripts in Machine Learning Services von Azure SQL-Datenbank (Vorschauversion)

Wenn Sie diese Schnellstartanleitung befolgen, erstellen Sie einen Satz von einfachen R-Skripts mithilfe der Public Preview von [Machine Learning Services (mit R) in Azure SQL-Datenbank](sql-database-machine-learning-services-overview.md) und führen ihn aus. Sie erfahren, wie Sie ein wohlgeformtes R-Skript mit der gespeicherten Prozedur [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) umschließen und das Skript in einer SQL­-Datenbank-Instanz ausführen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

- Zum Ausführen des Beispielcodes in diesen Übungen müssen Sie zuerst eine Azure SQL-Datenbank-Instanz mit Machine Learning Services (mit R) aktivieren. Während der öffentlichen Vorschauphase führt Microsoft für Sie das Onboarding durch und aktiviert das maschinelle Lernen für Ihre vorhandene oder neue Datenbank. Führen Sie die Schritte in [Registrieren für die Vorschauversion](sql-database-machine-learning-services-overview.md#signup) aus.

- Vergewissern Sie sich, dass die aktuelle Version von [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) installiert ist. In dieser Schnellstartanleitung verwenden Sie SSMS, obwohl Sie auch mithilfe anderer Datenbankverwaltungen oder Abfragetools R-Skripts ausführen können.

- Diese Schnellstartanleitung setzt voraus, dass Sie eine Firewallregel auf Serverebene konfigurieren. Informationen zur Vorgehensweise finden Sie unter [Erstellen einer Firewallregel auf Serverebene](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Ausführen eines einfachen Skripts

Um ein R-Skript auszuführen, übergeben Sie es als Argument der gespeicherten Systemprozedur [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

In den folgenden Schritten führen Sie dieses R-Beispielskript in Ihrer SQL-Datenbank-Instanz aus:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit Ihrer SQL-Datenbank her.

   Wenn Sie Unterstützung beim Herstellen der Verbindung benötigen, nutzen Sie die Informationen in [Schnellstart: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung mit einer Instanz von Azure SQL-Datenbank und deren Abfrage](sql-database-connect-query-ssms.md).

1. Übergeben Sie das vollständige R-Skript an die gespeicherte Prozedur [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Das Skript wird über das `@script`-Argument übergeben. Das `@script`-Argument darf nur gültigen R-Code enthalten.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Falls Sie Fehler erhalten, kann dies daran liegen, dass die öffentliche Vorschauversion von Machine Learning Services (mit R) für Ihre SQL-Datenbank nicht aktiviert ist. Weitere Informationen zu den [Voraussetzungen](#prerequisites) finden Sie oben.

   > [!NOTE]
   > Wenn Sie Administrator sind, können Sie externen Code automatisch ausführen. Sie können mit dem folgenden Befehl anderen Benutzern eine Berechtigung erteilen:
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<Benutzername\>* .

2. Das richtige Ergebnis wird berechnet, und die R-Funktion `print` gibt das Ergebnis im **Meldungen**-Fenster zurück.

   Das sollte in etwa so aussehen.

    **Ergebnisse**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Ausführen eines „Hello World“-Skripts

Dieses typische Beispielskript gibt einfach nur die Zeichenfolge „Hello World“ aus. Führen Sie den folgenden Befehl aus:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Zu den Eingaben, die der gespeicherten Prozedur übergeben werden, zählen:

| | |
|-|-|
| @language | -Parameter zur Definition der aufzurufenden Spracherweiterung, in diesem Fall R. |
| @script | -Parameter zur Definition der Befehle, die an die R-Runtime übergeben werden. Ihr gesamtes R-Skript muss als Unicode-Text in dieses Argument eingeschlossen werden. Sie könnten den Text auch einer Variablen des Typs **nvarchar** hinzufügen und die Variable anschließend aufrufen. |
| @input_data_1 | sind von der Abfrage zurückgegebene Daten, die an die R-Runtime übergeben werden, von der die Daten als Datenrahmen an SQL Server zurückgegeben werden. |
|WITH RESULT SETS | -Klausel zur Definition des Schemas der für SQL Server zurückgegebenen Datentabelle, wobei „Hello World“ als Name der Spalte und **int** für den Datentyp hinzugefügt wird. |

Der Befehl gibt den folgenden Text aus:

| Hallo Welt |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Verwenden von Eingaben und Ausgaben

Standardmäßig akzeptiert [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ein einzelnes Dataset als Eingabe, das Sie normalerweise in Form einer gültigen SQL-Abfrage bereitstellen. Anschließend wird ein einzelner R-Datenrahmen als Ausgabe zurückgegeben.

Nur ein Eingabedataset kann als Parameter übergeben werden, und Sie können nur ein Dataset zurückgeben. Sie können aber andere Datasets in Ihrem R-Code aufrufen und zusätzlich zum Dataset Ausgaben anderer Typen zurückgeben. Außerdem können Sie auch allen Parametern das Schlüsselwort OUTPUT hinzufügen, damit es zusammen mit den Ergebnissen zurückgegeben wird.

Hier verwenden wir die Standardvariablen für die Ein- und Ausgabe von [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataset** und **OutputDataset**.

1. Erstellen Sie eine kleine Tabelle mit Testdaten.

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

1. Verwenden Sie die `SELECT`-Anweisung zum Abfragen der Tabelle.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Ergebnisse**

    ![Inhalt der RTestData-Tabelle](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Führen Sie das folgende R-Skript aus. Es ruft die Daten aus der Tabelle mit der `SELECT`-Anweisung ab, übergibt sie über die R-Runtime und gibt die Daten als Datenrahmen zurück. Mit der `WITH RESULT SETS`-Klausel wird das Schema der für SQL-Datenbank zurückgegebenen Datentabelle definiert und der Spaltenname *NewColName* hinzugefügt.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Ergebnisse**

    ![Ausgabe des R-Skripts, mit dem Daten aus einer Tabelle zurückgegeben werden](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Wir ändern jetzt den Namen der Eingabe- und Ausgabevariablen. Die Namen der Standardvariablen für Ein- und Ausgabe lauten **InputDataSet** und **OutputDataSet**. Dieses Skript ändert die Namen in **SQL_in** und **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Beachten Sie, dass R zwischen Groß- und Kleinschreibung unterscheidet. Die im R-Skript verwendeten Ein- und Ausgabevariablen (**SQL_out**, **SQL_in**) müssen unter Berücksichtigung von Groß-/Kleinschreibung den mit `@input_data_1_name` und `@output_data_1_name` definierten Werten entsprechen.

   > [!TIP]
   > Nur ein Eingabedataset kann als Parameter übergeben werden, und Sie können nur ein Dataset zurückgeben. Sie können aber andere Datasets in Ihrem R-Code aufrufen und zusätzlich zum Dataset Ausgaben anderer Typen zurückgeben. Außerdem können Sie auch allen Parametern das Schlüsselwort OUTPUT hinzufügen, damit es zusammen mit den Ergebnissen zurückgegeben wird.

1. Sie können auch Werte generieren, indem Sie das R-Skript einfach ohne Eingabedaten verwenden (`@input_data_1` wird leer gelassen).

   Das folgende Skript gibt den Text „hello“ und „world“ aus.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Ergebnisse**

    ![Abfrageergebnisse mit @script als Eingabe](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Überprüfen der R-Version

Wenn Sie ermitteln möchten, welche Version von R in Ihrer SQL-Datenbank installiert ist, führen Sie das folgende Skript aus.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Mit der R-Funktion `print` wird die Version im Bereich mit den **Meldungen** zurückgegeben. In der Beispielausgabe unten sehen Sie, dass für SQL-Datenbank in diesem Fall die R-Version 3.4.4 installiert ist.

**Ergebnisse**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Liste mit R-Paketen

Microsoft stellt verschiedene R-Pakete bereit, die mit Machine Learning Services in Ihrer SQL-Datenbank vorinstalliert werden.

Um eine Liste mit den Informationen anzuzeigen, welche R-Pakete installiert sind, einschließlich Version, Abhängigkeiten, Lizenz und Bibliothekspfad, führen Sie das folgende Skript aus.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Die Ausgabe stammt aus `installed.packages()` in R und wird als Resultset zurückgegeben.

**Ergebnisse**

![Installierte Pakete in R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Nächste Schritte

Um ein Machine Learning-Modell mithilfe von R in einer SQL-Datenbank-Instanz zu erstellen, befolgen Sie diese Schnellstartanleitung:

> [!div class="nextstepaction"]
> [Erstellen und Trainieren eines Vorhersagemodells in R mit Machine Learning Services (Vorschauversion) für Azure SQL-Datenbank](sql-database-quickstart-r-train-score-model.md)

Weitere Informationen zu Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion) finden Sie in den folgenden Artikeln.

- [Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-overview.md)
- [Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-functions.md)
- [Arbeiten mit R- und SQL-Daten in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-data-issues.md)