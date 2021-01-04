---
title: 'Gewusst wie: Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics'
description: Erfahren Sie, wie Sie Continuous Integration und Continuous Deployment für Azure Data Lake Analytics einrichten.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015263"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Gewusst wie: Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics  

In diesem Artikel erfahren Sie, wie Sie eine CI/CD-Pipeline (Continuous Integration und Continuous Deployment) für U-SQL-Aufträge und U-SQL-Datenbanken einrichten können.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Verwenden von CI/CD für U-SQL-Aufträge

Azure Data Lake Tools für Visual Studio umfasst den U-SQL-Projekttyp, mit dem Sie U-SQL-Skripts organisieren können. Wenn Sie den U-SQL-Code mithilfe des U-SQL-Projekts verwalten, gestalten sich die weiteren CI/CD-Szenarien einfach.

## <a name="build-a-u-sql-project"></a>Erstellen eines U-SQL-Projekts

Ein U-SQL-Projekt kann mit der Microsoft-Build-Engine (MSBuild) erstellt werden, indem die entsprechenden Parameter übergeben werden. Führen Sie die Schritte in diesem Artikel zum Einrichten eines Buildprozesses für ein U-SQL-Projekt aus.

### <a name="project-migration"></a>Projektmigration

Vergewissern Sie sich vor dem Einrichten eines Buildtasks für ein U-SQL-Projekt, dass Sie die neueste Version des U-SQL-Projekts verwenden. Öffnen Sie die U-SQL-Projektdatei im Editor, und überprüfen Sie, ob Sie über folgende Importelemente verfügen:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Wenn dies nicht der Fall ist, stehen zwei Optionen zur Verfügung, um das Projekt zu migrieren:

- Option 1: Ändern Sie das alte Importelement in das vorherige Importelement.
- Option 2: Öffnen Sie das alte Projekt in Azure Data Lake Tools für Visual Studio. Verwenden Sie eine neuere Version als 2.3.3000.0. Die alte Projektvorlage wird automatisch auf die neueste Version aktualisiert. Neue Projekte, die mit höheren Versionen als 2.3.3000.0 erstellt wurden, verwenden die neue Vorlage.

### <a name="get-nuget"></a>Abrufen von NuGet

MSBuild enthält keine integrierte Unterstützung für U-SQL-Projekte. Um von dieser Unterstützung profitieren zu können, benötigen Sie einen Verweis für Ihre Projektmappe im NuGet-Paket [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), mit dem der erforderliche Sprachdienst hinzugefügt wird.

Um den NuGet-Paketverweis hinzuzufügen, klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete verwalten** aus. Sie können auch die Datei `packages.config` im Projektmappenordner auswählen und in dieser Datei die folgenden Inhalte einfügen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Verwalten von U-SQL-Datenbankverweisen

U-SQL-Skripts in einem U-SQL-Projekt enthalten möglicherweise Abfrageanweisungen für U-SQL-Datenbankobjekte. In diesem Fall müssen Sie vor der Erstellung des U-SQL-Projekts auf das entsprechende U-SQL-Datenbankprojekt verweisen, das die Objektdefinition enthält. Ein Beispiel ist, wenn Sie eine U-SQL-Tabelle abfragen oder auf eine Assembly verweisen.

Weitere Informationen zum U-SQL-Datenbankprojekt finden Sie [hier](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> Die DROP-Anweisung kann möglicherweise zu einem versehentlichen Löschvorgang führen. Zum Aktivieren einer DROP-Anweisung müssen Sie die MSBuild-Argumente explizit angeben. **AllowDropStatement** ermöglicht einen nicht datenbezogenen DROP-Vorgang, z.B. das Löschen einer Assembly und das Löschen einer Tabellenwertfunktion. **AllowDataDropStatement** ermöglicht einen datenbezogenen DROP-Vorgang, z.B. das Löschen einer Tabelle und das Löschen eines Schemas. Vor der Verwendung von „AllowDataDropStatement“ müssen Sie „AllowDropStatement“ aktivieren.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Erstellen eines U-SQL-Projekts über die MSBuild-Befehlszeile

Migrieren Sie zunächst das Projekt, und rufen Sie das NuGet-Paket ab. Rufen Sie dann die MSBuild-Standardbefehlszeile mit den folgenden zusätzlichen Argumenten ab, um Ihr U-SQL-Projekt zu erstellen:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

Die Definition und die Werte der Argumente lauten wie folgt:

- **USQLSDKPath=\<U-SQL Nuget package>\build\runtime**. Dieser Parameter verweist auf den Installationspfad des NuGet-Pakets für den U-SQL-Sprachdienst.
- **USQLTargetType=Merge oder SyntaxCheck**:

  - **Merge**. Der Merge-Modus kompiliert CodeBehind-Dateien. Beispiele sind **CS**-, **PY**- und **R**-Dateien. Die resultierende benutzerdefinierte Codebibliothek wird inline im U-SQL-Skript erstellt. Beispiele sind DLL-Binärdateien, Python- oder R-Code.

  - **SyntaxCheck**. Der SyntaxCheck-Modus führt zunächst CodeBehind-Dateien im U-SQL-Skript zusammen. Anschließend wird zur Überprüfung Ihres Codes das U-SQL-Skript kompiliert.

- **DataRoot=\<DataRoot path>** . DataRoot wird nur für den SyntaxCheck-Modus benötigt. Beim Erstellen des Skripts mit dem SyntaxCheck-Modus überprüft MSBuild die Verweise im Skript auf Datenbankobjekte. Richten Sie vor dem Erstellen eine entsprechende lokale Umgebung, die die referenzierten Objekte aus der U-SQL-Datenbank enthält, im DataRoot-Ordner des Buildcomputers ein. Sie können diese Datenbankabhängigkeiten auch durch [Verweisen auf ein U-SQL-Datenbankprojekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project) verwalten. MSBuild überprüft nur Verweise auf Datenbankobjekte und keine Dateien.

- **EnableDeployment=true** oder **false**. EnableDeployment gibt an, ob die Bereitstellung referenzierter U-SQL-Datenbanken während des Buildprozesses zulässig ist. Wenn Sie auf das U-SQL-Datenbankprojekt verweisen und die Datenbankobjekte in Ihrem U-SQL-Skript nutzen, legen Sie für diesen Parameter **true** fest.

### <a name="continuous-integration-through-azure-pipelines"></a>Continuous Integration über Azure Pipelines

Neben der Befehlszeile können Sie auch den Visual Studio Build- oder einen MSBuild-Task verwenden, um U-SQL-Projekte in Azure Pipelines zu erstellen. Fügen Sie zum Einrichten einer Buildpipeline zwei Tasks in der Buildpipeline hinzu: einen NuGet-Wiederherstellungstask und einen MSBuild-Task.

![MSBuild-Task für ein U-SQL-Projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Fügen Sie den NuGet-Wiederherstellungstask hinzu, um das NuGet-Paket mit der referenzierten Projektmappe einschließlich `Azure.DataLake.USQL.SDK` abzurufen, sodass MSBuild die Ziele der U-SQL-Sprache finden kann. Legen Sie für **Erweitert** > **Zielverzeichnis** den Wert `$(Build.SourcesDirectory)/packages` fest, wenn Sie das MSBuild-Argumentbeispiel in Schritt 2 direkt verwenden möchten.

   ![NuGet-Wiederherstellungstask für ein U-SQL-Projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Legen Sie MSBuild-Argumente in Visual Studio Build Tools oder in einem MSBuild-Task fest, wie im folgenden Beispiel gezeigt wird. Alternativ können Sie Variablen für diese Argumente in der Azure Pipelines-Buildpipeline definieren.

   ![Definieren von CI/CD-MSBuild-Variablen für ein U-SQL-Projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Buildausgabe für das U-SQL-Projekt

Nach dem Ausführen eines Builds werden alle Skripts im U-SQL-Projekt erstellt und in einer ZIP-Datei mit dem Namen `USQLProjectName.usqlpack` ausgegeben. Die Ordnerstruktur in Ihrem Projekt wird in der ZIP-Buildausgabe beibehalten.

> [!NOTE]
> Die CodeBehind-Dateien für jedes U-SQL-Skript werden als Inlineanweisung in der Buildausgabe des Skripts zusammengeführt.

## <a name="test-u-sql-scripts"></a>Testen von U-SQL-Skripts

Azure Data Lake enthält Testprojekte für U-SQL-Skripts und C#-UDO/UDAG/UDF:

- Erfahren Sie, wie Sie [Testfälle für U-SQL-Skripts und den erweiterten C#-Code hinzufügen](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

- Erfahren Sie, wie Sie [Testfälle in Azure Pipelines ausführen](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Bereitstellen eines U-SQL-Auftrags

Nach dem Überprüfen des Codes über den Build- und Testprozess können Sie U-SQL-Aufträge direkt aus Azure Pipelines über einen Azure PowerShell-Task übermitteln. Sie können das Skript zudem in Azure Data Lake Storage und Azure Blob Storage bereitstellen und [die geplanten Aufträge über Azure Data Factory ausführen](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Übermitteln von U-SQL-Aufträgen über Azure Pipelines

Die Buildausgabe des U-SQL-Projekts ist eine ZIP-Datei mit dem Namen **USQLProjectName.usqlpack**. Die ZIP-Datei enthält alle U-SQL-Skripts im Projekt. Sie können den [Azure PowerShell-Task](/azure/devops/pipelines/tasks/deploy/azure-powershell) in Pipelines mit dem folgenden PowerShell-Beispielskript verwenden, um U-SQL-Aufträge direkt aus Azure Pipelines zu übermitteln.

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> Die beiden Befehle `Submit-AzDataLakeAnalyticsJob` und `Wait-AzDataLakeAnalyticsJob` sind Azure PowerShell-Cmdlets für Azure Data Lake Analytics im Azure Resource Manager-Framework. Sie benötigen eine Arbeitsstation, auf der Azure PowerShell installiert wurde. Weitere Befehle und Beispiele können Sie in der [Befehlsliste](/powershell/module/Az.DataLakeAnalytics) finden.
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Bereitstellen von U-SQL-Aufträgen über Azure Data Factory

Sie können U-SQL-Aufträge direkt aus Azure Pipelines übermitteln. Alternativ können Sie die Buildskripts in Azure Data Lake Storage und Azure Blob Storage bereitstellen und [die geplanten Aufträge über Azure Data Factory ausführen](../data-factory/transform-data-using-data-lake-analytics.md).

Verwenden Sie den [Azure PowerShell-Task](/azure/devops/pipelines/tasks/deploy/azure-powershell) in Azure Pipelines mit dem folgenden PowerShell-Beispielskript, um die U-SQL-Skripts in ein Azure Data Lake Storage-Konto hochzuladen:

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD für eine U-SQL-Datenbank

Azure Data Lake Tools für Visual Studio enthält Vorlagen für das U-SQL-Datenbankprojekt, mit denen Entwickler U-SQL-Datenbanken entwickeln, verwalten und bereitstellen können. Weitere Informationen zum U-SQL-Datenbankprojekt finden Sie [hier](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Erstellen des U-SQL-Datenbankprojekts

### <a name="get-the-nuget-package"></a>Abrufen des NuGet-Pakets

MSBuild enthält keine integrierte Unterstützung für U-SQL-Datenbankprojekte. Um von dieser Funktion zu profitieren, benötigen Sie einen Verweis für Ihre Projektmappe im NuGet-Paket [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), mit dem der erforderliche Sprachdienst hinzugefügt wird.

Um den NuGet-Paketverweis hinzuzufügen, klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe. Wählen Sie dann **NuGet-Pakete verwalten** aus. Suchen Sie anschließend nach dem NuGet-Paket, und installieren Sie es. Sie können auch die Datei **packages.config** im Projektmappenordner auswählen und in dieser Datei die folgenden Inhalte hinzufügen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Erstellen eines U-SQL-Datenbankprojekts über die MSBuild-Befehlszeile

Um Ihr U-SQL-Datenbankprojekt zu erstellen, rufen Sie die MSBuild-Standardbefehlszeile auf, und übergeben Sie den Verweis auf das U-SQL SDK-NuGet-Paket als zusätzliches Argument. Sehen Sie sich folgendes Beispiel an: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Das Argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` verweist auf den Installationspfad des NuGet-Pakets für den U-SQL-Sprachdienst.

### <a name="continuous-integration-with-azure-pipelines"></a>Continuous Integration mit Azure Pipelines

Neben der Befehlszeile können Sie auch Visual Studio Build oder einen MSBuild-Task verwenden, um U-SQL-Datenbankprojekte in Azure Pipelines zu erstellen. Fügen Sie zum Einrichten eines Buildtasks zwei Tasks in der Buildpipeline hinzu: einen NuGet-Wiederherstellungstask und einen MSBuild-Task.

   ![CI/CD-MSBuild-Task für ein U-SQL-Projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Fügen Sie einen NuGet-Wiederherstellungstask hinzu, um das NuGet-Paket mit der referenzierten Projektmappe einschließlich `Azure.DataLake.USQL.SDK` abzurufen, sodass MSBuild die Ziele der U-SQL-Sprache finden kann. Legen Sie für **Erweitert** > **Zielverzeichnis** den Wert `$(Build.SourcesDirectory)/packages` fest, wenn Sie das MSBuild-Argumentbeispiel in Schritt 2 direkt verwenden möchten.

   ![CI/CD-NuGet-Task für ein U-SQL-Projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Legen Sie MSBuild-Argumente in Visual Studio Build Tools oder in einem MSBuild-Task fest, wie im folgenden Beispiel gezeigt wird. Alternativ können Sie Variablen für diese Argumente in der Azure Pipelines-Buildpipeline definieren.

   ![Definieren von CI/CD-MSBuild-Variablen für ein U-SQL-Datenbankprojekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>Buildausgabe für das U-SQL-Datenbankprojekt

Die Buildausgabe für ein U-SQL-Datenbankprojekt ist ein U-SQL-Datenbankbereitstellungspaket, das mit dem Suffix `.usqldbpack` benannt ist. Bei dem Paket `.usqldbpack` handelt es sich um eine ZIP-Datei, die alle DDL-Anweisungen in einem einzigen U-SQL-Skript in einem DDL-Ordner enthält. Es enthält alle **DLL**-Dateien und zusätzliche Dateien für die Assembly in einem temporären Ordner.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testen von Tabellenwertfunktionen und gespeicherten Prozeduren

Das direkte Hinzufügen von Testfällen für Tabellenwertfunktionen und gespeicherte Prozeduren wird derzeit nicht unterstützt. Um dies zu umgehen, können Sie ein U-SQL-Projekt erstellen, das U-SQL-Skripts zum Aufrufen dieser Funktionen enthält, und Testfälle für die Funktionen schreiben. Führen Sie die folgenden Schritte aus, um Testfälle für Tabellenwertfunktionen und gespeicherte Prozeduren einzurichten, die im U-SQL-Datenbankprojekt definiert sind:

1. Erstellen Sie ein U-SQL-Projekt für Testzwecke, und schreiben Sie U-SQL-Skripts, die die Tabellenwertfunktionen und gespeicherten Prozeduren aufrufen.

1. Fügen Sie einen Datenbankverweis auf dieses U-SQL-Projekt hinzu. Um die Definition der Tabellenwertfunktionen und gespeicherten Prozeduren abzurufen, müssen Sie auf das Datenbankprojekt verweisen, das die DDL-Anweisung enthält. Erfahren Sie mehr über [Datenbankverweise](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Fügen Sie Testfälle für U-SQL-Skripts hinzu, die Tabellenwertfunktionen und gespeicherte Prozeduren aufrufen. Informationen zum Hinzufügen von Testfällen für U-SQL-Skripts finden Sie [hier](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Bereitstellen einer U-SQL-Datenbank über Azure Pipelines

`PackageDeploymentTool.exe` enthält die Programmier- und Befehlszeilenschnittstellen, über die U-SQL-Datenbankbereitstellungspakete **.usqldbpack** bereitgestellt werden können. Das SDK ist im [U-SQL SDK-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) enthalten und befindet sich unter **build/runtime/PackageDeploymentTool.exe**. Durch Verwenden von `PackageDeploymentTool.exe` können Sie U-SQL-Datenbanken in Azure Data Lake Analytics-Konten sowie in lokalen Konten bereitstellen.

> [!NOTE]
>
> Die Unterstützung für die Bereitstellung von U-SQL-Datenbanken über die PowerShell-Befehlszeile und den Azure Pipelines-Freigabetask wird derzeit entwickelt.
>

Führen Sie die folgenden Schritte aus, um einen Task für die Datenbankbereitstellung in Azure Pipelines einzurichten:

1. Fügen Sie einen PowerShell-Skripttask im Build oder der Releasepipeline hinzu, und führen Sie das folgende PowerShell-Skript aus. Mit dieser Aufgabe können Azure SDK-Abhängigkeiten für `PackageDeploymentTool.exe` und `PackageDeploymentTool.exe` abgerufen werden. Sie können die Parameter **-AzureSDK** und **-DBDeploymentTool** festlegen, um die Abhängigkeiten und Bereitstellungstools in bestimmte Ordner zu laden. Übergeben Sie den **-AzureSDK**-Pfad in Schritt 2 als **-AzureSDKPath**-Parameter an `PackageDeploymentTool.exe`.

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. Fügen Sie einen **Befehlszeilentask** in einem Build oder einer Releasepipeline hinzu, und füllen Sie das Skript aus, indem Sie `PackageDeploymentTool.exe` aufrufen. `PackageDeploymentTool.exe` befindet sich unter dem definierten **$DBDeploymentTool**-Ordner. Das Beispielskript sieht wie folgt aus: 

   - Lokales Bereitstellen einer U-SQL-Datenbank:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Verwenden des interaktiven Authentifizierungsmodus zum Bereitstellen einer U-SQL-Datenbank in einem Azure Data Lake Analytics-Konto:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Verwenden der **secrete**-Authentifizierung zum Bereitstellen einer U-SQL-Datenbank in einem Azure Data Lake Analytics-Konto:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Verwenden der **certFile**-Authentifizierung zum Bereitstellen einer U-SQL-Datenbank in einem Azure Data Lake Analytics-Konto:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Beschreibung der Parameter in „PackageDeploymentTool.exe“

#### <a name="common-parameters"></a>Allgemeine Parameter

| Parameter | BESCHREIBUNG | Standardwert | Erforderlich |
|---------|-----------|-------------|--------|
|Paket|Der Pfad des bereitzustellenden U-SQL-Datenbankbereitstellungspakets.|NULL|true|
|Datenbank|Der Name der Datenbank, in der die Bereitstellung erfolgt oder die erstellt wird.|master|false|
|LogFile|Der Pfad der Datei für die Protokollierung, standardmäßig zur Standardausgabe (Konsole).|NULL|false|
|LogLevel|Protokollierungsgrad: „Ausführlich“, „Normal“, „Warnung“ oder „Fehler“.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Parameter für die lokale Bereitstellung

|Parameter|BESCHREIBUNG|Standardwert|Erforderlich|
|---------|-----------|-------------|--------|
|DataRoot|Der Pfad des lokalen Datenstammordners.|NULL|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parameter für die Bereitstellung von Azure Data Lake Analytics

|Parameter|BESCHREIBUNG|Standardwert|Erforderlich|
|---------|-----------|-------------|--------|
|Konto|Gibt per Kontonamen an, in welchem Azure Data Lake Analytics-Konto die Einrichtung erfolgen soll.|NULL|true|
|ResourceGroup|Der Name der Azure-Ressourcengruppe für das Azure Data Lake Analytics-Konto.|NULL|true|
|SubscriptionId|Die Azure-Abonnement-ID für das Azure Data Lake Analytics-Konto.|NULL|true|
|Tenant|Der Mandantenname ist der Azure Active Directory-Domänenname (Azure AD). Diesen finden Sie auf der Abonnementverwaltungsseite im Azure-Portal.|NULL|true|
|AzureSDKPath|Der Pfad zum Suchen von abhängigen Assemblys im Azure SDK.|NULL|true|
|Interactive|Legt fest, ob der interaktive Modus für die Authentifizierung verwendet oder nicht verwendet werden soll.|false|false|
|ClientId|Die Azure AD-Anwendungs-ID ist für die nicht interaktive Authentifizierung erforderlich.|NULL|Für die nicht interaktive Authentifizierung erforderlich.|
|Geheimnis|Das Geheimnis oder Kennwort für die nicht interaktive Authentifizierung. Es sollte nur in einer vertrauenswürdigen und sicheren Umgebung verwendet werden.|NULL|Für die nicht interaktive Authentifizierung erforderlich. Verwenden Sie andernfalls „SecreteFile“.|
|SecreteFile|Die Datei speichert das Geheimnis oder Kennwort für die nicht interaktive Authentifizierung. Stellen Sie sicher, dass sie nur vom aktuellen Benutzer gelesen werden kann.|NULL|Für die nicht interaktive Authentifizierung erforderlich. Verwenden Sie andernfalls „Secrete“.|
|CertFile|Die Datei speichert die X.509-Zertifizierung für die nicht interaktive Authentifizierung. Standardmäßig wird die Authentifizierung mit geheimem Clientschlüssel verwendet.|NULL|false|
| JobPrefix | Das Präfix für die Datenbankbereitstellung eines U-SQL-DDL-Auftrags. | Deploy_ und DateTime.Now | false |

## <a name="next-steps"></a>Nächste Schritte

- [Testen des Azure Data Lake Analytics-Codes](data-lake-analytics-cicd-test.md)
- [Ausführen eines U-SQL-Skripts auf dem lokalen Computer](data-lake-analytics-data-lake-tools-local-run.md)
- [Verwenden eines U-SQL-Datenbankprojekts zum Entwickeln einer U-SQL-Datenbank](data-lake-analytics-data-lake-tools-develop-usql-database.md)