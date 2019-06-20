---
title: Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in Visual Studio Code
description: Hier erfahren Sie, wie Sie CodeBehind mit Python, R und C# verwenden, um Aufträge in Azure Data Lake zu übermitteln.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814063"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Entwickeln von U-SQL mit Python, R und C# für Azure Data Lake Analytics in Visual Studio Code
Hier erfahren Sie, wie Sie Visual Studio Code (VSCode) verwenden, um Python-, R- und C#-CodeBehind-Dateien mit U-SQL zu schreiben und Aufträge an den Azure Data Lake-Dienst zu übermitteln. Weitere Informationen zu Azure Data Lake Tools für VSCode finden Sie unter [Verwenden von Azure Data Lake Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Bevor Sie CodeBehind-Code schreiben, müssen Sie einen Ordner oder Arbeitsbereich in VSCode öffnen.


## <a name="prerequisites-for-python-and-r"></a>Voraussetzungen für Python und R
Registrieren Sie Assemblys für Python- und R-Erweiterungen für Ihr ADL-Konto. 
1. Öffnen Sie Ihr Konto im Portal.
   - Wählen Sie **Übersicht**. 
   - Klicken Sie auf **Beispielskript**.
2. Klicken Sie auf **Mehr**.
3. Wählen Sie **U-SQL-Erweiterungen installieren**. 
4. Nachdem die U-SQL-Erweiterungen installiert wurden, wird eine Bestätigungsmeldung angezeigt. 

   ![Einrichten der Umgebung für Python und R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Installieren Sie zur optimalen Nutzung des Sprachdiensts für Python und R die Python- und R-Erweiterungen für VSCode. 

## <a name="develop-python-file"></a>Entwickeln einer Python-Datei
1. Klicken Sie in Ihrem Arbeitsbereich auf **Neue Datei**.
2. Schreiben Sie Ihren Code in U-SQL. Im Anschluss sehen Sie ein Codebeispiel.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Klicken Sie mit der rechten Maustaste auf eine Skriptdatei, und wählen Sie Folgendes aus: **ADL: Python-CodeBehind-Datei generieren**. 
4. Die Datei **xxx.usql.py** wird in Ihrem Arbeitsordner erstellt. Schreiben Sie Ihren Code in die Python-Datei. Im Anschluss sehen Sie ein Codebeispiel.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Klicken Sie mit der rechten Maustaste in die **USQL**-Datei. Sie können auf **Compile Script** (Skript kompilieren) oder **Auftrag übermitteln** klicken, um den Auftrag auszuführen.

## <a name="develop-r-file"></a>Entwickeln einer R-Datei
1. Klicken Sie in Ihrem Arbeitsbereich auf **Neue Datei**.
2. Schreiben Sie Ihren Code in die U-SQL-Datei. Im Anschluss sehen Sie ein Codebeispiel.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Klicken Sie mit der rechten Maustaste auf eine **USQL**-Datei, und wählen Sie Folgendes aus: **ADL: R-CodeBehind-Datei generieren**. 
4. Die Datei **xxx.usql.r** wird in Ihrem Arbeitsordner erstellt. Schreiben Sie Ihren Code in die R-Datei. Im Anschluss sehen Sie ein Codebeispiel.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Klicken Sie mit der rechten Maustaste in die **USQL**-Datei. Sie können auf **Compile Script** (Skript kompilieren) oder **Auftrag übermitteln** klicken, um den Auftrag auszuführen.

## <a name="develop-c-file"></a>Entwickeln einer C#-Datei
Eine CodeBehind-Datei ist eine C#-Datei, die einem U-SQL-Skript zugeordnet ist. Sie können ein Skript speziell für UDO, UDA, UDT und UDF in der CodeBehind-Datei definieren. UDO, UDA, UDT und UDF können direkt im Skript verwendet werden, ohne die Assembly zuvor zu registrieren. Die CodeBehind-Datei wird im gleichen Ordner wie die U-SQL-Skriptdatei für das Peering gespeichert. Wenn das Skript den Namen „xxx.usql“ erhält, heißt die CodeBehind-Datei „xxx.usql.cs“. Durch manuelles Löschen der CodeBehind-Datei wird das CodeBehind-Feature für das zugehörige U-SQL-Skript deaktiviert. Weitere Informationen zum Schreiben von benutzerdefiniertem Code für ein U-SQL-Skript finden Sie unter [Writing and Using Custom Code in U-SQL: User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Schreiben und Verwenden von benutzerdefiniertem Code in U-SQL: benutzerdefinierte Funktionen).

1. Klicken Sie in Ihrem Arbeitsbereich auf **Neue Datei**.
2. Schreiben Sie Ihren Code in die U-SQL-Datei. Im Anschluss sehen Sie ein Codebeispiel.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Klicken Sie mit der rechten Maustaste auf eine **USQL**-Datei, und wählen Sie Folgendes aus: **ADL: CS-CodeBehind-Datei generieren**. 
4. Die Datei **xxx.usql.cs** wird in Ihrem Arbeitsordner erstellt. Schreiben Sie Ihren Code in die CS-Datei. Im Anschluss sehen Sie ein Codebeispiel.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Klicken Sie mit der rechten Maustaste in die **USQL**-Datei. Sie können auf **Compile Script** (Skript kompilieren) oder **Auftrag übermitteln** klicken, um den Auftrag auszuführen.

## <a name="next-steps"></a>Nächste Schritte
* [Verwenden der Azure Data Lake-Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Lokales Ausführen und lokales Debuggen von U-SQL mit Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Erste Schritte mit Data Lake Analytics mithilfe von PowerShell](data-lake-analytics-get-started-powershell.md)
* [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
* [Verwenden von Data Lake-Tools für Visual Studio zum Entwickeln von U-SQL-Anwendungen](data-lake-analytics-data-lake-tools-get-started.md)
* [Verwenden des Azure Data Lake Analytics-Katalogs (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
