---
title: Hinzufügen eines R-Pakets zu Machine Learning Services in Azure SQL-Datenbank (Vorschauversion)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In diesem Artikel wird erläutert, wie Sie ein R-Paket installieren, das in Machine Learning Services (Vorschauversion) in Azure SQL-Datenbank noch nicht installiert ist.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ms.openlocfilehash: 4e7145570cbc906ea540c9d8f95f6c3cbde1c610
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928625"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Hinzufügen eines R-Pakets zu Machine Learning Services in Azure SQL-Datenbank (Vorschauversion)

In diesem Artikel wird erläutert, wie Sie ein R-Paket zu Machine Learning Services (Vorschauversion) in Azure SQL-Datenbank hinzufügen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie [R](https://www.r-project.org) und [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) auf dem lokalen Computer. R ist für Windows, macOS und Linux verfügbar. In diesem Artikel wird davon ausgegangen, dass Sie Windows verwenden.

- Dieser Artikel enthält ein Beispiel für die Verwendung von [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) oder [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms). (SSMS) zum Ausführen eines R-Skripts in der Azure SQL-Datenbank. Sie können R-Skripts auch mithilfe anderer Datenbankverwaltungs- oder Abfragetools ausführen, aber in diesem Beispiel wird davon ausgegangen, dass Sie Azure Data Studio oder SSMS verwenden.
   
> [!NOTE]
> Sie können ein Paket nicht installieren, indem Sie ein R-Skript mit **sp_execute_external_script** in Azure Data Studio oder SSMS ausführen. Sie können Pakete nur wie in diesem Artikel beschrieben über die R-Befehlszeile und RStudio installieren und entfernen. Wenn das Paket installiert ist, können Sie mit **sp_execute_external_script** auf die Paketfunktionen in einem R-Skript zugreifen.

## <a name="list-r-packages"></a>Liste mit R-Paketen

Microsoft stellt verschiedene R-Pakete bereit, die mit Machine Learning Services in Ihrer Azure SQL-Datenbank vorinstalliert werden.
Sie können eine Liste der installierten R-Pakete anzeigen, indem Sie den folgenden Befehl in Azure Data Studio oder SSMS ausführen.

1. Öffnen Sie Azure Data Studio oder SSMS, und stellen Sie eine Verbindung mit Ihrer Azure SQL-Datenbank her.

1. Führen Sie den folgenden Befehl aus:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

Die Ausgabe sollte in etwa wie folgt aussehen:

**Ergebnisse**

![Installierte Pakete in R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Hinzufügen eines Pakets mit sqlmlutils

Wenn Sie ein Paket verwenden müssen, das nicht bereits in Ihrer Azure SQL-Datenbank installiert ist, können Sie es per [sqlmlutils](https://github.com/Microsoft/sqlmlutils) installieren. **sqlmlutils** ist ein Paket, das Benutzern die Interaktion mit SQL-Datenbanken (SQL Server und Azure SQLL-Datenbank) sowie die Ausführung von R- oder Python-Code in SQL von einem R- oder Python-Client aus ermöglicht. Derzeit wird in Azure SQL-Datenbank nur die R-Version von **sqlmlutils** unterstützt.

Im folgenden Beispiel installieren Sie das **[glue](https://cran.r-project.org/web/packages/glue/)** -Paket, mit dem eine Zeichenfolge formatiert und interpoliert werden kann. Diese Schritte installieren **sqlmlutils** und **RODBCext** (eine für **sqlmlutils** erforderliche Komponente), und fügen das **glue**-Paket hinzu.

### <a name="install-sqlmlutils"></a>Installieren von **sqlmlutils**

1. Laden Sie die neueste **sqlmlutils**-ZIP-Datei von https://github.com/Microsoft/sqlmlutils/tree/master/R/dist auf Ihren lokalen Computer herunter. Sie brauchen die Datei nicht zu entzippen.

1. Öffnen Sie eine **Eingabeaufforderung**, und führen Sie die folgenden Befehle zum Installieren von **RODBCext** und **sqlmlutils** auf dem lokalen Computer aus. Ersetzen Sie den vollständigen Pfad zur heruntergeladenen **sqlmlutils**-ZIP-Datei (im Beispiel wird davon ausgegangen, dass sich die Datei im Ordner „Dokumente“ befindet).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Wenn Sie den Fehler „R wird nicht als interner oder externer Befehl, ausführbares Programm oder Batchdatei erkannt“ erhalten, bedeutet dies normalerweise, dass der Pfad zu „R.exe“ unter Windows nicht in Ihrer Umgebungsvariablen **PATH** enthalten ist. Sie können entweder den Pfad zur Umgebungsvariablen hinzufügen oder über die Eingabeaufforderung zum Ordner navigieren (z.B. `cd C:\Program Files\R\R-3.5.3\bin`) und den Befehl dann erneut ausführen.

### <a name="add-the-package"></a>Hinzufügen des Pakets

1. Öffnen Sie RStudio, und erstellen Sie eine neue **R-Skript**-Datei. 

1. Verwenden Sie den folgenden R-Code, um ein **glue**-Paket mit **sqlmlutils** zu installieren. Ersetzen Sie die Azure SQL-Datenbank-Verbindungsinformationen durch Ihre eigenen.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > Der **Bereich** kann entweder **ÖFFENTLICH** oder **PRIVAT** sein. Der öffentliche Bereich ist für den Datenbankadministrator nützlich, um Pakete zu installieren, die von allen Benutzern verwendet werden können. Beim privaten Bereich ist das Paket nur für den Benutzer verfügbar, der es installiert. Wenn Sie den Bereich nicht angeben, wird standardmäßig **PRIVAT** verwendet.

### <a name="verify-the-package"></a>Überprüfen des Pakets

Vergewissern Sie sich, dass das **glue**-Paket installiert wurde, indem Sie das folgende R-Skript in RStudio ausführen. Verwenden Sie die gleiche **Verbindung**, die Sie im vorherigen Schritt definiert haben.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Ergebnisse**

![Inhalt der RTestData-Tabelle](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Verwenden des Pakets

Nachdem das Paket installiert wurde, können Sie es in einem R-Skript mit **sp_execute_external_script** verwenden.

1. Öffnen Sie Azure Data Studio oder SSMS, und stellen Sie eine Verbindung mit Ihrer Azure SQL-Datenbank her.

1. Führen Sie den folgenden Befehl aus:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    Auf der Registerkarte **Meldungen** wird das folgende Ergebnis angezeigt.

    **Ergebnisse**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Entfernen des Pakets

Führen Sie in RStudio das folgende R-Skript aus, wenn Sie das Paket entfernen möchten. Verwenden Sie die gleiche **Verbindung**, die Sie zuvor definiert haben.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Eine weitere Möglichkeit, ein R-Paket in Ihrer Azure SQL-Datenbank zu installieren, besteht darin, das R-Paket aus einem Bytedatenstrom unter Verwendung der T-SQL-Anweisung **CREATE EXTERNAL LIBRARY** hochzuladen. Weitere Informationen finden Sie unter [Erstellen einer Bibliothek aus einem Bytedatenstrom](/sql/t-sql/statements/create-external-library-transact-sql#c-create-a-library-from-a-byte-stream) in der [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)-Referenzdokumentation.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion) finden Sie in den folgenden Artikeln.

- [Machine Learning Services (mit R) in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-overview.md)
- [Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-functions.md)
- [Arbeiten mit R- und SQL-Daten in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-data-issues.md)