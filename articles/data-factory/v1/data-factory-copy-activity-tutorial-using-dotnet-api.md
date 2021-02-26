---
title: 'Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe der .NET-API '
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mit Kopieraktivität mithilfe der .NET-API.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8c2baf4da4e7893b650e1f885fb66be835c4b736
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377073"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe der .NET-API
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 

In diesem Artikel erfahren Sie, wie Sie mithilfe von [.NET API](https://portal.azure.com) eine Data Factory mit einer Pipeline erstellen, die Daten aus einem Azure Blob Storage in Azure SQL-Datenbank kopiert. Wenn Sie mit Azure Data Factory nicht vertraut sind, lesen Sie vor der Durchführung dieses Tutorials den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).   

In diesem Tutorial erstellen Sie eine Pipeline mit nur einer Aktivität: die Kopieraktivität. Die Kopieraktivität kopiert die Daten aus einem unterstützten Datenspeicher in einen unterstützten Senkendatenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Eine vollständige Dokumentation zu .NET API für Data Factory finden Sie in der [.NET API-Referenz für Data Factory](/dotnet/api/overview/azure/data-factory).
> 
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Verschaffen Sie sich unter [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Überblick über das Tutorial, und führen Sie die Schritte aus, die zur Erfüllung der **Voraussetzungen** erforderlich sind.
* Visual Studio 2012, 2013 oder 2015
* Herunterladen und Installieren des [Azure .NET SDK](https://azure.microsoft.com/downloads/)
* Azure PowerShell. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) zum Installieren von Azure PowerShell auf Ihrem Computer. Azure PowerShell wird zum Erstellen einer Azure Active Directory-Anwendung benötigt.

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
    $azureAdApplication = New-AzADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
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
4. Fügen Sie der Datei **App.config** den folgenden **appSettings**-Abschnitt hinzu. Diese Einstellungen werden von der Hilfsmethode **GetAuthorizationHeader** verwendet.

    Ersetzen Sie die Werte für **&lt;Anwendungs-ID&gt;** , **&lt;Kennwort&gt;** , **&lt;Abonnement-ID&gt;** und **&lt;Mandanten-ID&gt;** durch Ihre eigenen Werte.

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

5. Fügen Sie die folgenden **using** -Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.

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
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Ersetzen Sie den Wert von **ResourceGroupName** durch den Namen Ihrer Azure-Ressourcengruppe.
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

    Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. In diesem Schritt erstellen wir zunächst die Data Factory.
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

    Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Tutorial werden keine Compute Services wie Azure HDInsight oder Azure Data Lake Analytics verwendet. Sie verwenden zwei Datenspeicher vom Typ „Azure Storage“ (Quelle) und „Azure SQL-Datenbank“ (Ziel). 

    Aus diesem Grund erstellen Sie zwei verknüpfte Dienste mit dem Namen „AzureStorageLinkedService“ und „AzureSqlLinkedService“ vom Typ „AzureStorage“ und „AzureSqlDatabase“.  

    Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Dieses Speicherkonto ist das Konto, in dem Sie im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und die Daten hochgeladen haben.
9. Fügen Sie der **Main**-Methode den folgenden Code hinzu, um einen **verknüpften Azure SQL-Dienst** zu erstellen:

   > [!IMPORTANT]
   > Ersetzen Sie **servername**, **databasename**, **username** und **password** durch die Namen Ihres Servers, Ihrer Datenbank, Ihren Benutzernamen und Ihr Kennwort.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    „AzureSqlLinkedService“ verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) die Tabelle „emp“ in dieser Datenbank erstellt.
10. Fügen Sie den folgenden Code, mit dem **Eingabe- und Ausgabedatasets** erstellt werden, der **Main**-Methode hinzu.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
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

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
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
    
    Im vorherigen Schritt haben Sie verknüpfte Dienste erstellt, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. In diesem Schritt definieren Sie die beiden Datasets „InputDataset“ und „OutputDataset“. Sie stellen Eingabe- und Ausgabedaten dar, die in den Datenspeichern gespeichert werden, auf die mit „AzureStorageLinkedService“ und „AzureSqlLinkedService“ verwiesen wird.

    Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Das Eingabeblobdataset („InputDataset“) gibt den Container und den Ordner an, der die Eingabedaten enthält.  

    Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrer Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset („OututDataset“) die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden.

    In diesem Schritt erstellen Sie ein Dataset namens „InputDataset“, das auf eine Blobdatei (emp.txt) im Stammordner eines Blobcontainers („adftutorial“) in Azure Storage (dargestellt durch den verknüpften Dienst „AzureStorageLinkedService“) verweist. Wenn Sie keinen Wert für „fileName“ festlegen (oder diesen überspringen), werden Daten aus allen Blobs im Eingabeordner in das Ziel kopiert. In diesem Tutorial legen Sie einen Wert für „fileName“ fest.    

    In diesem Schritt erstellen Sie ein Ausgabedataset namens **OutputDataset**. Dieses Dataset verweist auf eine SQL-Tabelle in der durch **AzureSqlLinkedService** dargestellten Datenbank.
11. Fügen Sie den folgenden Code, mit dem eine **Pipeline erstellt und aktiviert** wird, der **Main**-Methode hinzu. In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, für die **InputDataset** als Eingabe und **OutputDataset** als Ausgabe verwendet wird.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

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
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
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
                    }
                }
            }
        });
    ```

    Beachten Sie folgende Punkte:
   
    - Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). In Data Factory-Lösungen können Sie auch [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) verwenden.
    - Die Eingabe für die Aktivität ist auf **InputDataset** und die Ausgabe für die Aktivität ist auf **OutputDataset** festgelegt. 
    - Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben. Eine vollständige Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Um Informationen zum Verwenden eines bestimmten unterstützten Datenspeichers als Quelle/Senke zu erhalten, klicken Sie auf den Link in der Tabelle.  
   
    Derzeit steuert das Ausgabedataset den Zeitplan. In diesem Tutorial wird ein Ausgabedataset konfiguriert, um einmal pro Stunde einen Slice zu erzeugen. Für die Pipeline ist eine Start- und Endzeit festgelegt, die einen Tag, d.h. 24 Stunden, auseinander liegen. Aus diesem Grund werden 24 Ausgabedatasetslices von der Pipeline erzeugt.
12. Fügen Sie den folgenden Code zur Methode **Main** hinzu, um den Status eines Datenslices des Ausgabedatasets abzurufen. Es gibt nur ein Slice in diesem Beispiel erwartet.

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

13. Fügen Sie der Methode **Main** den folgenden Code hinzu, um Ausführungsdetails für einen Datenslice abzurufen:

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
            }
        );

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

14. Fügen Sie die folgende Hilfsmethode, die von der **Main**-Methode verwendet wird, der **Program**-Klasse hinzu.

    > [!NOTE] 
    > Stellen Sie beim Kopieren und Einfügen des folgenden Codes sicher, dass sich der kopierte Code auf der gleichen Ebene wie die Main-Methode befindet.

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

15. Erweitern Sie im Projektmappen-Explorer das Projekt (DataFactoryAPITestApp), klicken Sie mit der rechten Maustaste auf **Verweise**, und klicken Sie auf **Verweis hinzufügen**. Aktivieren Sie das Kontrollkästchen für die Assembly **System.Configuration**, und klicken Sie auf **OK**.
16. Erstellen Sie die Konsolenanwendung. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.
17. Vergewissern Sie sich, dass sich in Azure Blob Storage mindestens eine Datei im Container **adftutorial** befindet. Erstellen Sie andernfalls im Editor die Datei **Emp.txt** mit folgendem Inhalt, und laden Sie sie anschließend in den Container „adftutorial“ hoch:

    ```
    John, Doe
    Jane, Doe
    ```
18. Führen Sie das Beispiel aus, indem Sie im Menü auf **Debuggen** -> **Debuggen starten** klicken. Wenn angezeigt wird, dass die **Ausführungsdetails für einen Datenslice abgerufen werden**, warten Sie einige Minuten, und drücken Sie dann die **EINGABETASTE**.
19. Vergewissern Sie sich mithilfe des Azure-Portals, dass die Data Factory **APITutorialFactory** mit folgenden Artefakten erstellt wird:
    * Verknüpfter Dienst: **LinkedService_AzureStorage**
    * Dataset: **InputDataset** und **OutputDataset**.
    * Pipeline: **PipelineBlobSample**
20. Vergewissern, dass die zwei Mitarbeiterdatensätze in der Tabelle **emp** in der angegebenen Datenbank erstellt werden.

## <a name="next-steps"></a>Nächste Schritte
Eine vollständige Dokumentation zu .NET API für Data Factory finden Sie in der [.NET API-Referenz für Data Factory](/dotnet/api/overview/azure/data-factory).

In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zum Kopieren von Daten in einen bzw. aus einem Datenspeicher zu erhalten, klicken Sie in der Tabelle auf den Link für den Datenspeicher.

