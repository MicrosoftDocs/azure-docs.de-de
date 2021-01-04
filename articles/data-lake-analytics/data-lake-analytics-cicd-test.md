---
title: Testen des Azure Data Lake Analytics-Codes
description: Hier erfahren Sie, wie Sie Testfälle für den U-SQL- und erweiterten C#-Code für Azure Data Lake Analytics hinzufügen.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 7310c67ef20a4134d4f613ea969c96802958bf62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015229"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testen des Azure Data Lake Analytics-Codes

Azure Data Lake stellt die [U-SQL](data-lake-analytics-u-sql-get-started.md)-Sprache bereit. U-SQL kombiniert deklarative SQL mit imperativen C#-Elementen, damit Daten jedweden Umfangs verarbeitet werden können. In diesem Dokument erfahren Sie, wie Sie Testfälle für den U-SQL- und den erweiterten C#-UDO-Code (User-Defined Operator, benutzerdefinierter Operator) erstellen.

## <a name="test-u-sql-scripts"></a>Testen von U-SQL-Skripts

Das U-SQL-Skript wird für ausführbaren Code zur Ausführung in Azure oder auf dem lokalen Computer kompiliert und optimiert. Beim Kompilierungs- und Optimierungsprozess wird das gesamte U-SQL-Skript als Ganzes behandelt. Sie können keine herkömmlichen Komponententests für jede Anweisung durchführen. Mithilfe des U-SQL-Test-SDK und des SDK zur lokalen Ausführung können Sie jedoch Skriptebenentests durchführen.

### <a name="create-test-cases-for-u-sql-script"></a>Erstellen von Testfällen für ein U-SQL-Skript

In Azure Data Lake Tools für Visual Studio können Sie Testfälle für U-SQL-Skripts erstellen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf ein U-SQL-Skript, und wählen Sie **Create Unit Test** (Komponententest erstellen) aus.

1. Erstellen Sie ein neues Testprojekt, oder fügen Sie den Testfall in ein vorhandenes Testprojekt ein.

   ![Data Lake Tools für Visual Studio: Konfiguration zum Erstellen eines U-SQL-Testprojekts](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Verwalten der Testdatenquelle

Beim Testen von U-SQL-Skripts werden Testeingabedateien benötigt. Zum Verwalten der Testdaten klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das U-SQL-Projekt, und wählen Sie **Eigenschaften** aus. Sie können eine Quelle in **Testdatenquelle** eingeben.

![Data Lake Tools für Visual Studio: Konfigurieren der Testdatenquelle des Projekts](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Wenn Sie die `Initialize()`-Schnittstelle im U-SQL-Test-SDK aufgerufen haben, wird unter dem Arbeitsverzeichnis des Testprojekts ein temporärer lokaler Datenstammordner erstellt. Bevor Sie die U-SQL-Skripttestfälle ausführen, werden alle Dateien und Unterordner im Ordner „Testdatenquelle“ in den temporären lokalen Datenstammordner kopiert. Sie können weitere Testdatenquellenordner hinzufügen, indem Sie den Testdatenordnerpfad durch Semikolons trennen.

### <a name="manage-the-database-environment-for-testing"></a>Verwalten der Datenbankumgebung für Tests

Wenn bei den U-SQL-Skripts U-SQL-Datenbankobjekte verwendet oder abgefragt werden, müssen Sie die Datenbankumgebung vor dem Ausführen der U-SQL-Testfälle initialisieren. Diese Vorgehensweise kann beim Aufrufen gespeicherter Prozeduren erforderlich sein. Über die `Initialize()`-Schnittstelle im U-SQL-Test-SDK können Sie alle Datenbanken, auf die durch das U-SQL-Projekt verwiesen wird, im temporären lokalen Datenstammordner im Arbeitsverzeichnis des Testprojekts bereitstellen.

Weitere Informationen zum Verwalten von U-SQL-Datenbank-Projektverweisen für ein U-SQL-Projekt finden Sie unter [Verweisen auf ein U-SQL-Datenbankprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Überprüfen der Testergebnisse

Die `Run()`-Schnittstelle gibt ein Ergebnis der Auftragsausführung zurück. *0* bedeutet Erfolg, und *1* bedeutet Fehler. Sie können zudem C#-Assert-Funktionen verwenden, um die Ausgaben zu überprüfen.

### <a name="run-test-cases-in-visual-studio"></a>Ausführen von Testfällen in Visual Studio

Ein Testprojekt für das U-SQL-Skript basiert auf dem C#-Komponententest-Framework. Nachdem Sie das Projekt erstellt haben, wählen Sie **Test** > **Windows** > **Test-Explorer** aus. Testfälle können im **Test-Explorer** ausgeführt werden. Klicken Sie alternativ mit der rechten Maustaste auf die CS-Datei in Ihrem Komponententest, und wählen Sie **Tests ausführen** aus.

## <a name="test-c-udos"></a>Testen von C#-UDOs

### <a name="create-test-cases-for-c-udos"></a>Erstellen von Testfällen für C#-UDOs

Sie können das C#-Komponententest-Framework verwenden, um Ihre C#-UDOs (User-Defined Operator, benutzerdefinierter Operator) zu testen. Beim Testen von UDOs müssen Sie die entsprechenden **IRowset**-Objekte als Eingabe vorbereiten.

Es gibt zwei Möglichkeiten zum Erstellen eines **IRowset**-Objekts:

- Laden von Daten aus einer Datei zum Erstellen von **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Verwenden von Daten aus einer Datensammlung zum Erstellen von **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Überprüfen der Testergebnisse

Nach dem Aufrufen von UDO-Funktionen können Sie die Ergebnisse unter Verwendung von C#-Assert-Funktionen über die Überprüfung des Schemas und Rowset-Werts überprüfen. Sie können der Projektmappe ein **U-SQL-C#-UDO-Komponententestprojekt** hinzufügen. Dazu wählen Sie in Visual Studio **Datei > Neu > Projekt** aus.

### <a name="run-test-cases-in-visual-studio"></a>Ausführen von Testfällen in Visual Studio

Nachdem Sie das Projekt erstellt haben, wählen Sie **Test** > **Windows** > **Test-Explorer** aus. Testfälle können im **Test-Explorer** ausgeführt werden. Klicken Sie alternativ mit der rechten Maustaste auf die CS-Datei in Ihrem Komponententest, und wählen Sie **Tests ausführen** aus.

## <a name="run-test-cases-in-azure-pipelines"></a>Ausführen von Testfällen in Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Die **Testprojekte für das U-SQL-Skript** sowie die **C#-UDO-Testprojekte** erben von den C#-Komponententestprojekten. Die [Visual Studio-Testaufgabe](/azure/devops/pipelines/test/getting-started-with-continuous-testing) in Azure Pipelines kann diese Testfälle ausführen.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Ausführen von U-SQL-Testfällen in Azure Pipelines

Stellen Sie für einen U-SQL-Test sicher, dass Sie `CPPSDK` auf dem Buildcomputer laden, und übergeben Sie dann den `CPPSDK`-Pfad an `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`.

#### <a name="what-is-cppsdk"></a>Was ist CPPSDK?

CPPSDK ist ein Paket, das Microsoft Visual C++ 14 und Windows SDK 10.0.10240.0 enthält. Dieses Paket enthält die für die U-SQL-Runtime erforderliche Umgebung. Sie finden dieses Paket im Installationsordner von Azure Data Lake Tools für Visual Studio:

- Für Visual Studio 2015 befindet es sich unter `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Für Visual Studio 2017 befindet es sich unter `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Für Visual Studio 2019 befindet es sich unter `C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Vorbereiten von CPPSDK im Azure Pipelines-Build-Agent

Die gängigste Methode für die Vorbereitung dieser CPPSDK-Abhängigkeit in Azure Pipelines ist folgende:

1. Zippen Sie den Ordner, der die CPPSDK-Bibliotheken enthält.

1. Checken Sie die ZIP-Datei in das Quellcodeverwaltungssystem ein. Mit der ZIP-Datei wird sichergestellt, dass alle Bibliotheken im CPPSDK-Ordner eingecheckt und Dateien nicht aufgrund einer `.gitignore`-Datei ignoriert werden.

1. Entzippen Sie die ZIP-Datei in die Buildpipeline.

1. Verweisen Sie `USqlScriptTestRunner` auf diesen entzippten Ordner auf dem Buildcomputer.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Ausführen von C#-UDO-Testfällen in Azure Pipelines

Stellen Sie für den C#-UDO-Test sicher, dass Sie auf die folgenden Assemblys verweisen, die für UDOs erforderlich sind.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

Wenn Sie über das [Microsoft.Azure.DataLake.USQL.Interfaces-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) auf sie verweisen, stellen Sie sicher, dass Sie eine NuGet-Wiederherstellungsaufgabe in der Buildpipeline hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Ausführen von U-SQL-Skripts auf dem lokalen Computer](data-lake-analytics-data-lake-tools-local-run.md)
- [Entwickeln einer U-SQL-Datenbank mithilfe eines U-SQL-Datenbankprojekts](data-lake-analytics-data-lake-tools-develop-usql-database.md)