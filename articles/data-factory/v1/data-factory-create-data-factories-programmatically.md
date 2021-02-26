---
title: Erstellen von Datenpipelines mithilfe des Azure .NET SDK
description: Erfahren Sie, wie Sie Azure Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen, überwachen und verwalten.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b7f5d1ad671b6d97bd6597386c714b026c02232
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376852"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Azure Data Factory .NET SDK
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 

## <a name="overview"></a>Übersicht
Sie können Azure Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen, überwachen und verwalten. Dieser Artikel enthält eine exemplarische Vorgehensweise, die Sie befolgen können, um eine .NET-Beispielkonsolenanwendung zu erstellen, die eine Data Factory erstellt und überwacht. 

> [!NOTE]
> In diesem Artikel wird nicht die gesamte Data Factory-.NET-API behandelt. In der [.NET-API-Referenz für die Data Factory](/dotnet/api/overview/azure/data-factory) finden Sie eine umfassende Dokumentation zur .NET-API für die Data Factory. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012, 2013 oder 2015
* Laden Sie das [Azure .NET SDK](https://azure.microsoft.com/downloads/)herunter, und installieren Sie es.
* Azure PowerShell. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) zum Installieren von Azure PowerShell auf Ihrem Computer. Azure PowerShell wird zum Erstellen einer Azure Active Directory-Anwendung benötigt.

### <a name="create-an-application-in-azure-active-directory"></a>Erstellen einer Anwendung in Azure Active Directory
Erstellen Sie eine Azure Active Directory-Anwendung, erstellen Sie einen Dienstprinzipal für die Anwendung, und weisen Sie ihn der Rolle **Data Factory-Mitwirkender** zu.

1. Starten Sie **PowerShell**.
2. Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:

    ```powershell
    Connect-AzAccount
    ```
3. Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```
4. Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **&lt;NameOfAzureSubscription**&gt; durch den Namen Ihres Azure-Abonnements.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Notieren Sie sich **SubscriptionId** und **TenantId** aus der Ausgabe dieses Befehls.

5. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** , indem Sie in PowerShell den folgenden Befehl ausführen.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Falls die Ressourcengruppe bereits vorhanden ist, können Sie angeben, ob sie aktualisiert (J) oder beibehalten (N) werden soll.

    Bei Verwendung einer anderen Ressourcengruppe müssen Sie den Namen Ihrer Ressourcengruppe anstelle von ADFTutorialResourceGroup in diesem Tutorial verwenden.
6. Erstellen Sie eine Azure Active Directory-Anwendung.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    Sollte der folgende Fehler auftreten, wiederholen Sie den Befehl mit einer anderen URL:
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. Erstellen Sie den AD-Dienstprinzipal.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Fügen Sie den Dienstprinzipal der Rolle **Data Factory-Mitwirkender** hinzu.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Rufen Sie die Anwendungs-ID ab.

    ```powershell
    $azureAdApplication    
    ```
    Notieren Sie sich die Anwendungs-ID (applicationID) aus der Ausgabe.

Nach diesen Schritten sollten Sie über vier Werte verfügen:

* Mandanten-ID
* Abonnement-ID
* Anwendungs-ID
* Kennwort (angegeben im ersten Befehl)

## <a name="walkthrough"></a>Exemplarische Vorgehensweise
In der exemplarischen Vorgehensweise erstellen Sie eine Data Factory mit einer Pipeline, die eine Kopieraktivität beinhaltet. Bei der Kopieraktivität werden Daten aus einem Ordner in Azure Blob Storage in einen anderen, ebenfalls in Blob Storage befindlichen Ordner kopiert. 

Die Kopieraktivität dient zum Verschieben von Daten in Azure Data Factory. Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) .

1. Erstellen Sie mithilfe von Visual Studio 2012/2013/2015 eine C# .NET-Konsolenanwendung.
   1. Starten Sie **Visual Studio** (2012/2013/2015).
   2. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**.
   3. Erweitern Sie **Vorlagen**, und wählen Sie **Visual C#** aus. In dieser exemplarischen Vorgehensweise verwenden Sie C#, aber Sie können jede .NET-Sprache verwenden.
   4. Wählen Sie in der Liste der Projekttypen auf der rechten Seite **Konsolenanwendung** aus.
   5. Geben Sie als Name **DataFactoryAPITestApp** ein.
   6. Wählen Sie als Speicherort **C:\ADFGetStarted** aus.
   7. Klicken Sie auf **OK**, um das Projekt zu erstellen.
2. Klicken Sie auf **Extras**, zeigen Sie auf **NuGet-Paket-Manager**, und klicken Sie auf **Paket-Manager-Konsole**.
3. Führen Sie in der **Paket-Manager-Konsole** die folgenden Schritte aus:
   1. Führen Sie den folgenden Befehl zum Installieren des Data Factory-Pakets aus: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Führen Sie den folgenden Befehl zum Installieren des Azure Active Directory-Pakets aus (Sie verwenden die Active Directory-API im Code): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Ersetzen Sie die Inhalte der Datei **App.config** im Projekt durch den folgenden Inhalt: 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. Ersetzen Sie in der Datei „App.Config“ die Werte für **&lt;Anwendungs-ID&gt;** , **&lt;Kennwort&gt;** , **&lt;Abonnement-ID&gt;** und **&lt;Mandanten-ID&gt;** durch Ihre eigenen Werte.
6. Fügen Sie die folgenden **using**-Anweisungen der Datei **Program.cs** im Projekt hinzu.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. Fügen Sie folgenden Code, mit dem eine Instanz der **DataPipelineManagementClient**-Klasse erstellt wird, der **Main**-Methode hinzu. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Eingabe- und Ausgabedatasets sowie eine Pipeline zu erstellen. Außerdem dient das Objekt zum Überwachen von Datenslices eines Datasets zur Laufzeit.

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Ersetzen Sie den Wert von **ResourceGroupName** durch den Namen Ihrer Azure-Ressourcengruppe. Mit dem Cmdlet [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) können Sie eine Ressourcengruppe erstellen.
   >
   > Legen Sie den Namen der Data Factory (dataFactoryName) auf einen eindeutigen Namen fest. Der Name der Data Factory muss global eindeutig sein. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .
7. Fügen Sie den folgenden Code, der eine **Data Factory** erstellt, der **Main**-Methode hinzu.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. Fügen Sie der **Main**-Methode den folgenden Code hinzu, um einen **verknüpften Azure Storage-Dienst** zu erstellen:

   > [!IMPORTANT]
   > Ersetzen Sie **storageaccountname** und **accountkey** durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```
9. Fügen Sie den folgenden Code, mit dem **Eingabe- und Ausgabedatasets** erstellt werden, der **Main**-Methode hinzu.

    Für den **FolderPath** des Eingabeblobs ist der Wert **adftutorial/** festgelegt, wobei **adftutorial** dem Namen des Containers im Blobspeicher entspricht. Wenn dieser Container nicht in Azure Blob Storage enthalten ist, erstellen Sie einen Container mit dem Namen **adftutorial** und laden eine Textdatei in den Container hoch.

    Für den FolderPath des Ausgabeblobs ist **adftutorial/apifactoryoutput/{Slice}** festgelegt, wobei **Slice** auf Basis des Werts von **SliceStart** (Startdatum und -zeit für jeden Slice) dynamisch berechnet wird.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. Fügen Sie den folgenden Code, mit dem eine **Pipeline erstellt und aktiviert** wird, der **Main**-Methode hinzu. Diese Pipeline verfügt über eine **CopyActivity**, die **BlobSource** als Quelle und **BlobSink** als Senke verwendet.

    Die Kopieraktivität dient zum Verschieben von Daten in Azure Data Factory. Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) .

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
12. Fügen Sie den folgenden Code zur Methode **Main** hinzu, um den Status eines Datenslices des Ausgabedatasets abzurufen. In diesem Beispiel wird nur ein Slice erwartet.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(optional)** Fügen Sie der **Main**-Methode den folgenden Code hinzu, um Ausführungsdetails für einen Datenslice abzurufen.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. Fügen Sie die folgende Hilfsmethode, die von der **Main**-Methode verwendet wird, der **Program**-Klasse hinzu. Diese Methode öffnet ein Dialogfeld, in dem Sie den **Benutzernamen** und das **Kennwort** bereitstellen können, mit denen Sie sich beim Azure-Portal anmelden.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. Erweitern Sie das Projekt im Projektmappen-Explorer: Wählen Sie **DataFactoryAPITestApp**, klicken Sie mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **Verweis hinzufügen**. Aktivieren Sie das Kontrollkästchen für das `System.Configuration` -Assembly, und klicken Sie auf **OK**.
15. Erstellen Sie die Konsolenanwendung. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.
16. Vergewissern Sie sich, dass sich mindestens eine Datei im adftutorial-Container in Azure Blob Storage befindet. Ist dies nicht der Fall, erstellen Sie die Datei "Emp.txt" mit folgendem Inhalt im Editor, und laden Sie sie anschließend in den "adftutorial"-Container hoch.

    ```
    John, Doe
    Jane, Doe
    ```
17. Führen Sie das Beispiel aus, indem Sie im Menü auf **Debuggen** -> **Debuggen starten** klicken. Wenn angezeigt wird, dass die **Ausführungsdetails für einen Datenslice abgerufen werden**, warten Sie einige Minuten, und drücken Sie dann die **EINGABETASTE**.
18. Vergewissern Sie sich mithilfe des Azure-Portals, dass die Data Factory **APITutorialFactory** mit folgenden Artefakten erstellt wird:
    * Verknüpfter Dienst: **AzureStorageLinkedService**
    * Dataset: **DatasetBlobSource** und **DatasetBlobDestination**.
    * Pipeline: **PipelineBlobSample**
19. Vergewissern Sie sich, dass im Ordner **apifactoryoutput** im Container **adftutorial** eine Ausgabedatei erstellt wird.

## <a name="get-a-list-of-failed-data-slices"></a>Abrufen einer Liste von fehlerhaften Datenslices 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich das folgende Beispiel für die Erstellung einer Pipeline mithilfe des .NET-SDK an, das Daten aus einem Azure-Blobspeicher in Azure SQL-Datenbank kopiert: 

- [Erstellen einer Pipeline zum Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-activity-tutorial-using-dotnet-api.md)
