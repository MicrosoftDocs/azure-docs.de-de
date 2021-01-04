---
title: Verwenden des .NET SDK für Microsoft Azure StorSimple Data Manager-Aufträge
description: Erfahren Sie, wie Sie das .NET SDK im StorSimple Data Manager-Dienst verwenden, um StorSimple-Gerätedaten zu transformieren.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015535"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Verwenden des .NET SDK zum Initiieren einer Datentransformation

## <a name="overview"></a>Übersicht

In diesem Artikel wird erläutert, wie Sie die Datentransformationsfunktion innerhalb des StorSimple Data Manager-Diensts verwenden, um StorSimple-Gerätedaten zu transformieren. Die transformierten Daten können dann von anderen Azure-Diensten in der Cloud verwendet werden.

Sie können einen Datentransformationsauftrag auf zwei Arten starten:

- Verwenden des .NET SDK
- Verwenden des Azure Automation-Runbooks
 
  In diesem Artikel finden Sie Hinweise zum Erstellen einer .NET-Beispielskonsolenanwendung zum Initiieren eines Datentransformationsauftrags und zum Nachverfolgen der Fertigstellung. Weitere Informationen zum Initiieren der Datentransformation über Automation finden Sie unter [Verwenden des Azure Automation-Runbooks zum Initiieren von Datentransformationsaufträgen](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen stellen Sie sicher, dass Sie über Folgendes verfügen:
*   Ein Computer, auf dem Folgendes ausgeführt wird:

    - Visual Studio 2012, 2013, 2015 oder 2017

    - Azure PowerShell. [Azure PowerShell herunterladen](/powershell/azure/).
*   Eine ordnungsgemäß konfigurierte Auftragsdefinition in StorSimple Data Manager innerhalb einer Ressourcengruppe
*   Alle erforderlichen DLLS. Laden Sie diese DLLS aus dem [GitHub-Repository](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) herunter.
*   Skript [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) aus dem GitHub-Repository

## <a name="step-by-step-procedure"></a>Ausführliche Anleitung

Führen Sie die folgenden Schritte aus, um .NET für das Starten eines Datentransformationsauftrags zu verwenden.

1. Führen Sie die folgenden Schritte aus, um die Konfigurationsparameter abzurufen:
    1. Laden Sie `Get-ConfigurationParams.ps1` über das GitHub-Repository-Skript unter `C:\DataTransformation` herunter.
    1. Führen Sie das Skript `Get-ConfigurationParams.ps1` aus dem GitHub-Repository heraus aus. Geben Sie folgenden Befehl ein:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Sie können beliebige Werte für ActiveDirectoryKey und AppName übergeben.

2. Dieses Skript gibt die folgenden Werte aus:
    * Client-ID
    * Mandanten-ID
    * Active Directory-Schlüssel (identisch mit der Eingabe für „ActiveDirectoryKey“)
    * Abonnement-ID

        ![Ausgabe des Konfigurationsparameterskripts](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Erstellen Sie mithilfe von Visual Studio 2012, 2013 oder 2015 eine C# .NET-Konsolenanwendung.

    1. Starten Sie **Visual Studio 2012/2013/2015**.
    1. Wählen Sie **Datei > Neu > Projekt** aus.

        ![Erstellen eines Projekts 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Wählen Sie **Installiert > Vorlagen > Visual C# > Konsolenanwendung** aus.
    3. Geben Sie bei **Name** **DataTransformationApp** ein.
    4. Wählen Sie **C:\DataTransformation** als **Speicherort** aus.
    6. Klicken Sie auf **OK**, um das Projekt zu erstellen.

        ![Erstellen eines Projekts 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Fügen Sie nun alle DLLs, die sich im Ordner [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) befinden, dem erstellten Projekt als **Verweise** hinzu. Um die DLL-Dateien hinzuzufügen, führen Sie folgende Schritte aus:

   1. Wechseln Sie in Visual Studio zu **Ansicht > Projektmappen-Explorer**.
   2. Klicken Sie auf den Pfeil auf der linken Seite des Data Transformation-App-Projekts. Klicken Sie auf **Verweise**, und klicken Sie dann mit der rechten Maustaste auf **Verweis hinzufügen**.
    
       ![Hinzufügen von DLLs 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Suchen Sie den Speicherort des Paketordners, wählen Sie alle DLLs aus, und klicken Sie auf **Hinzufügen**. Klicken Sie anschließend auf **OK**.

       ![Hinzufügen von DLLs 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Fügen Sie die folgenden **using** -Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Der folgende Code initialisiert die Instanz des Datentransformationsauftrags. Fügen Sie ihn in **Main-Methode** hinzu. Ersetzen Sie die Werte der Konfigurationsparameter, die Sie zuvor erhalten haben. Geben Sie die Werte für **ResourceGroupName** und **ResourceName** ein. **ResourceGroupName** ist mit dem StorSimple Data Manager verknüpft, auf dem die Auftragsdefinition konfiguriert wurde. **ResourceName** ist der Name Ihres StorSimple Data Manager-Diensts.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Geben Sie die Parameter an, mit denen die Auftragsdefinition ausgeführt werden muss.

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (ODER)

    Wenn Sie die Parameter der Auftragsdefinition während der Laufzeit ändern möchten, fügen Sie den folgenden Code hinzu:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Fügen Sie nach der Initialisierung den folgenden Code ein, um einen Datentransformationsauftrag für die Auftragsdefinition auszulösen. Geben Sie den passenden **Job Definition Name (Name der Auftragsdefinition)** ein.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Nachdem der Code eingefügt wurde, erstellen Sie die Lösung. Hier ist ein Screenshot des Codeausschnitts zum Initialisieren der Datentransformationsauftrags-Instanz.

   ![Codeausschnitt zum Initialisieren des Datentransformationsauftrags](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Dieser Auftrag transformiert die Daten, die dem Stammverzeichnis und den Dateifiltern im StorSimple-Volume entsprechen, und schreibt sie in den angegebenen Container bzw. die Dateifreigabe. Beim Transformieren einer Datei wird einer Speicherwarteschlange (im selben Speicherkonto wie der Container) mit dem gleichen Namen wie die Auftragsdefinition eine Nachricht hinzugefügt. Sie können diese Nachricht verwenden, um eine weitere Verarbeitung der Datei auszulösen.

10. Sie können mit dem folgenden Code die Fertigstellung des Auftrags überwachen, nachdem der Auftrag ausgelöst wurde. Es ist nicht erforderlich, diesen Code bei der Ausführung des Auftrags hinzuzufügen.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Hier ist ein Screenshot des gesamten Codebeispiels zum Auslösen des Auftrags mithilfe von .NET.

    ![Vollständiger Codeausschnitt zum Auslösen eines .NET-Auftrags](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Nächste Schritte

[Use StorSimple Data Manager UI to transform your data (Verwenden der StorSimple Data Manager-UI zum Transformieren von Daten) ](storsimple-data-manager-ui.md).