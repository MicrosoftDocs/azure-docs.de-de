---
title: Testen des Azure Data Lake Analytics-Codes
description: Hier erfahren Sie, wie Sie Testfälle für den U-SQL- und erweiterten C#-Code für Azure Data Lake Analytics hinzufügen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 4532e0c6e8095c9d64897410e0492e2135d8a478
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630086"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testen des Azure Data Lake Analytics-Codes

Azure Data Lake umfasst die U-SQL-Sprache, bei der deklarative SQL-Elemente mit imperativem C#-Code kombiniert werden, damit Daten jeden Umfangs verarbeitet werden können. In diesem Dokument erfahren Sie, wie Sie Testfälle für den U-SQL- und den erweiterten C#-UDO-Code (User-Defined Operator, benutzerdefinierter Operator) erstellen.

## <a name="test-u-sql-scripts"></a>Testen von U-SQL-Skripts

Das U-SQL-Skript wird für ausführbaren Code zur Ausführung auf Computern in der Cloud oder auf dem lokalen Computer kompiliert und optimiert. Beim Kompilierungs- und Optimierungsprozess wird das gesamte U-SQL-Skript als Ganzes behandelt. Sie können keine herkömmlichen „Komponententests“ für jede Anweisung durchführen. Mithilfe des U-SQL-Test-SDK und des SDK zur lokalen Ausführung können Sie jedoch Skriptebenentests durchführen.

### <a name="create-test-cases-for-u-sql-script"></a>Erstellen von Testfällen für ein U-SQL-Skript

In Azure Data Lake Tools für Visual Studio können Sie Testfälle für U-SQL-Skripts erstellen.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf ein U-SQL-Skript, und wählen Sie **Create Unit Test** (Komponententest erstellen) aus.
2.  Erstellen Sie ein neues Testprojekt, oder fügen Sie den Testfall in ein vorhandenes Testprojekt ein.

    ![Data Lake Tools für Visual Studio: Erstellen eines U-SQL-Testprojekts](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools für Visual Studio: Konfiguration zum Erstellen eines U-SQL-Testprojekts](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>Verwalten der Testdatenquelle

Beim Testen von U-SQL-Skripts werden Testeingabedateien benötigt. Sie können diese Testdaten verwalten, indem Sie die **Testdatenquelle** in den U-SQL-Projekteigenschaften konfigurieren. 

Beim Aufrufen der `Initialize()`-Schnittstelle im U-SQL-Test-SDK wird ein temporärer lokaler Datenstammordner unter dem Arbeitsverzeichnis des Testprojekts erstellt, und alle Dateien und Unterordner (und Dateien in den Unterordnern) im Ordner der Testdatenquelle werden vor dem Ausführen der Testfälle für U-SQL-Skripts in den temporären lokalen Datenstammordner kopiert. Sie können weitere Testdatenquellenordner hinzufügen, indem Sie den Testdatenordnerpfad durch Semikolons trennen.

![Data Lake Tools für Visual Studio: Konfigurieren der Testdatenquelle des Projekts](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>Verwalten der Datenbankumgebung für Tests

Wenn bei den U-SQL-Skripts U-SQL-Datenbankobjekte verwendet oder abgefragt werden (etwa beim Aufrufen von gespeicherten Prozeduren), müssen Sie die Datenbankumgebung vor dem Ausführen der U-SQL-Testfälle initialisieren. Über die `Initialize()`-Schnittstelle im U-SQL-Test-SDK können Sie alle Datenbanken, auf die durch das U-SQL-Projekt verwiesen wird, im temporären lokalen Datenstammordner im Arbeitsverzeichnis des Testprojekts bereitstellen. 

Erfahren Sie mehr über das [Verwalten von Verweisen auf U-SQL-Datenbankprojekte für ein U-SQL-Projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Überprüfen der Testergebnisse

Die `Run()`-Schnittstelle gibt ein Ergebnis der Auftragsausführung zurück. 0 bedeutet Erfolg, und 1 bedeutet Fehler. Sie können zudem C#-Assert-Funktionen verwenden, um die Ausgaben zu überprüfen. 

### <a name="run-test-cases-in-visual-studio"></a>Ausführen von Testfällen in Visual Studio

Ein Testprojekt für das U-SQL-Skript basiert auf dem C#-Komponententest-Framework. Nach dem Erstellen des Projekts können Sie alle Testfälle über **Test-Explorer > Wiedergabeliste** ausführen. Klicken Sie alternativ mit der rechten Maustaste auf die CS-Datei, und wählen Sie **Tests ausführen** aus.

## <a name="test-c-udos"></a>Testen von C#-UDOs

### <a name="create-test-cases-for-c-udos"></a>Erstellen von Testfällen für C#-UDOs

Sie können das C#-Komponententest-Framework verwenden, um Ihre C#-UDOs (User-Defined Operator, benutzerdefinierter Operator) zu testen. Beim Testen von UDOs müssen Sie die entsprechenden **IRowset**-Objekte als Eingabe vorbereiten.

Es gibt zwei Möglichkeiten zum Erstellen eines IRowset-Objekts:

- Laden von Daten aus einer Datei zum Erstellen von IRowset:

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

- Verwenden von Daten aus einer Datensammlung zum Erstellen von IRowset:

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

Nach dem Aufrufen von UDO-Funktionen können Sie die Ergebnisse unter Verwendung von C#-Assert-Funktionen über die Überprüfung des Schemas und Rowset-Werts überprüfen. Sie können Beispielcode in einem Beispielprojekt für den U-SQL-C#-UDO-Komponententest über **Datei > Neu > Projekt** in Visual Studio verwenden.

### <a name="run-test-cases-in-visual-studio"></a>Ausführen von Testfällen in Visual Studio

Nach dem Erstellen des Testprojekts können Sie alle Testfälle über **Test-Explorer > Wiedergabeliste** oder durch Klicken mit der rechten Maustaste auf die CS-Datei und Auswählen von **Tests ausführen** ausführen.

## <a name="run-test-cases-in-azure-devops"></a>Ausführen von Testfällen in Azure DevOps

Die **Testprojekte für das U-SQL-Skript** sowie die **C#-UDO-Testprojekte** erben von den C#-Komponententestprojekten. Die [Visual Studio-Testaufgabe](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) in Azure DevOps kann diese Testfälle ausführen. 

### <a name="run-u-sql-test-cases-in-azure-devops"></a>Ausführen von U-SQL-Testfällen in Azure DevOps

Stellen Sie für den U-SQL-Test sicher, dass Sie `CPPSDK` auf den Buildcomputer laden, und übergeben Sie dann den `CPPSDK`-Pfad an USqlScriptTestRunner(cppSdkFolderFullPath: \\@"").

**Was ist CPPSDK?**

CPPSDK ist ein Paket, das Microsoft Visual C++ 14 und Windows SDK 10.0.10240.0 enthält. Dies ist die für die U-SQL-Runtime erforderliche Umgebung. Sie finden dieses Paket im Installationsordner von Azure Data Lake Tools für Visual Studio:

- Für Visual Studio 2015 befindet es sich unter `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Für Visual Studio 2017 befindet es sich unter `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Vorbereiten von CPPSDK im Azure DevOps-Build-Agent**

Die gängigste Methode für die Vorbereitung dieser CPPSDK-Abhängigkeit in Azure DevOps ist folgende:

1.  Zippen Sie den Ordner, der die CPPSDK-Bibliotheken enthält.
2.  Checken Sie die ZIP-Datei in das Quellcodeverwaltungssystem ein. (Mit der ZIP-Datei wird sichergestellt, dass alle Bibliotheken im CPPSDK-Ordner eingecheckt und keine Dateien durch „.gitignore“ ignoriert werden.)   
3.  Entzippen Sie die ZIP-Datei in die Buildpipeline.
4.  Verweisen Sie `USqlScriptTestRunner` auf diesen entzippten Ordner auf dem Buildcomputer.

### <a name="run-c-udo-test-cases-in-azure-devops"></a>Ausführen von C#-UDO-Testfällen in Azure DevOps

Stellen Sie für den C#-UDO-Test sicher, dass Sie auf die folgenden Assemblys verweisen, die für UDOs erforderlich sind. Wenn Sie über das [Microsoft.Azure.DataLake.USQL.Interfaces-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) auf sie verweisen, stellen Sie sicher, dass Sie eine NuGet-Wiederherstellungsaufgabe in der Buildpipeline hinzufügen.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Ausführen von U-SQL-Skripts auf dem lokalen Computer](data-lake-analytics-data-lake-tools-local-run.md)
- [Entwickeln einer U-SQL-Datenbank mithilfe eines U-SQL-Datenbankprojekts](data-lake-analytics-data-lake-tools-develop-usql-database.md)

