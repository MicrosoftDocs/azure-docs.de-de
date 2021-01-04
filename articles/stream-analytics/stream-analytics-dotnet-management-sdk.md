---
title: Management .NET SDK für Azure Stream Analytics
description: Führen Sie erste Schritte mit dem Stream Analytics Management .NET SDK durch. Erfahren Sie, wie Analytics-Aufträge eingerichtet und ausgeführt werden. Erstellen Sie ein Projekt, Eingaben, Ausgaben und Transformationen.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 93e10baa5281481b57e945f93b7375060b574de5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126854"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: Einrichten und Ausführen von Analyseaufträgen mit der Azure Stream Analytics-API für .NET
Erfahren Sie, wie Sie mit der Azure Stream Analytics-API für .NET über das Management .NET SDK Analyseaufträge einrichten und ausführen. Richten Sie ein Projekt ein, erstellen Sie Eingabe- und Ausgabequellen sowie Transformationen, und starten und beenden Sie Aufträge. Für Ihre Analyseaufträge können Sie Daten aus dem Blob-Speicher oder einem Event Hub streamen.

Weitere Informationen finden Sie in der [Referenzdokumentation zur Verwaltung für die Stream Analytics-API für .NET](/previous-versions/azure/dn889315(v=azure.100)).

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, Hochverfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Stream Analytics ermöglicht Kunden, Streaming-Aufträge zur Analyse von Datenströmen einzurichten und Analysen nahezu in Echtzeit durchzuführen.  

> [!NOTE]
> Wir haben den Beispielcode in diesem Artikel mit Version v2.x des Azure Stream Analytics Management .NET SDK aktualisiert. Beispielcode mit der älteren SDK-Version (1.x) finden Sie unter [Verwenden des Management .NET SDK v1.x für Stream Analytics]().

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, müssen Sie die folgenden Schritte ausführen:

* Installieren Sie Visual Studio 2019 oder 2015.
* Laden Sie das [Azure .NET SDK](https://azure.microsoft.com/downloads/)herunter, und installieren Sie es.
* Erstellen Sie in Ihrem Abonnement eine Azure-Ressourcengruppe. Nachfolgend ist ein Azure PowerShell-Beispielskript aufgeführt. Informationen zu Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Richten Sie eine Eingabequelle und ein Ausgabeziel für den Auftrag ein, mit dem eine Verbindung hergestellt werden soll.

## <a name="set-up-a-project"></a>Einrichten eines Projekts
Verwenden Sie zum Erstellen eines Analyseauftrags die Stream Analytics-API für .NET, und richten Sie zuerst das Projekt ein.

1. Erstellen Sie eine Visual Studio C# .NET-Konsolenanwendung.
2. Führen Sie in der Paket-Manager-Konsole die folgenden Befehle zum Installieren der NuGet-Pakete aus. Das erste ist das Azure Stream Analytics Management .NET SDK. Das zweite dient zur Authentifizierung des Azure-Clients.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Fügen Sie der Datei "App.config" den folgenden **appSettings** -Abschnitt hinzu.
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Ersetzen Sie die Werte für **SubscriptionId** und **ActiveDirectoryTenantId** durch die IDs Ihres Azure-Abonnements und -Mandanten. Sie können diese Werte durch Ausführen des folgenden Azure PowerShell-Cmdlets abrufen:

   ```powershell
      Get-AzureAccount
   ```

4. Fügen Sie in der CSPROJ-Datei den folgenden Verweis hinzu:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Fügen Sie die folgenden **using** -Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Fügen Sie eine Authentifizierungshilfsmethode hinzu:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Erstellen eines Stream Analytics-Verwaltungsclients
Mithilfe eines **StreamAnalyticsManagementClient** -Objekts können Sie den Auftrag und die Auftragskomponenten wie Eingabe, Ausgabe und Transformation verwalten.

Fügen Sie den folgenden Code am Anfang der Methode **Main** hinzu:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

Der Wert der Variablen **resourceGroupName** sollte mit dem Namen der Ressourcengruppe übereinstimmen, die Sie in den Vorbereitungsschritten erstellt oder ausgewählt haben.

Informationen zum Automatisieren der Darstellung von Anmeldeinformationen bei der Auftragserstellung finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

In den verbleibenden Abschnitten dieses Artikels wird davon ausgegangen, dass dieser Code am Anfang der Methode **Main** enthalten ist.

## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags
Der folgende Code erstellt einen Stream Analytics-Auftrag unter der Ressourcengruppe, die Sie definiert haben. Sie fügen dem Auftrag später eine Eingabe, Ausgabe und Transformation hinzu.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Erstellen einer Stream Analytics-Eingabequelle
Der folgende Code erstellt eine Stream Analytics-Eingabequelle mit dem Blob-Eingabequellentyp und mit CSV-Serialisierung. Verwenden Sie zum Erstellen einer Event Hub-Eingabequelle **EventHubStreamInputDataSource** anstelle von **BlobStreamInputDataSource**. In ähnlicher Weise können Sie den Serialisierungstyp der Eingabequelle anpassen.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Eingabequellen, gleichgültig ob vom Blob-Speicher oder einem Event Hub, sind an einen bestimmten Auftrag gebunden. Um die gleiche Eingabequelle für verschiedene Aufträge zu verwenden, müssen Sie die Methode erneut aufrufen und einen anderen Auftragsnamen angeben.

## <a name="test-a-stream-analytics-input-source"></a>Testen einer Stream Analytics-Eingabequelle
Die Methode **TestConnection** überprüft, ob der Stream Analytics-Auftrag sich mit der Eingabequelle verbinden kann. Ferner werden andere Aspekte getestet, die für die Eingabequelle spezifisch sind. Beispielsweise überprüft die Methode in der Blob-Eingabequelle, die Sie in einem früheren Schritt erstellt haben, ob das Paar aus dem Namen des Storage-Kontos und dem Schlüssel verwendet werden kann, um eine Verbindung mit dem Storage-Konto herzustellen. Zudem wird überprüft, ob der angegebene Container vorhanden ist.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Erstellen eines Stream Analytics-Ausgabeziels
Das Erstellen eines Ausgabeziels ähnelt dem Erstellen einer Stream Analytics-Eingabequelle. Genau wie Eingabequellen sind Ausgabeziele an einen bestimmten Auftrag gebunden. Um dasselbe Ausgabeziel für verschiedene Aufträge zu verwenden, müssen Sie die Methode erneut aufrufen und einen anderen Auftragsnamen angeben.

Der folgende Code erstellt ein Ausgabeziel (Azure SQL-Datenbank). Sie können den Datentyp des Ausgabeziels und/oder den Serialisierungstyp anpassen.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testen eines Stream Analytics-Ausgabeziels
Ein Stream Analytics-Ausgabeziel verfügt auch über die Methode **TestConnection** zum Testen von Verbindungen.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Erstellen einer Stream Analytics-Transformation
Der folgende Code erstellt eine Stream Analytics-Transformation mit der Abfrage "select * from Input" und gibt an, dass eine Streamingeinheit für den Stream Analytics-Auftrag zuordnet werden soll. Weitere Informationen zum Anpassen von Streamingeinheiten finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Wie Eingabe und Ausgabe ist auch eine Transformation an den jeweiligen Stream Analytics-Auftrag gebunden, unter dem Sie erstellt wurde.

## <a name="start-a-stream-analytics-job"></a>Starten eines Stream Analytics-Auftrags
Nach dem Erstellen eines Stream Analytics-Auftrags und seiner Eingabe(n), Ausgabe(n) und Transformation können Sie den Auftrag durch Aufrufen der Methode **Start** starten.

Der folgende Beispielcode startet einen Stream Analytics-Auftrag mit der benutzerdefinierten Ausgabestartzeit "12. Dezember 2012, 12:12:12 UTC":

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Beenden eines Stream Analytics-Auftrags
Sie können einen laufenden Stream Analytics-Auftrag beenden, indem Sie die Methode **Stop** aufrufen.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Löschen eines Stream Analytics-Auftrags
Die Methode **Delete** löscht den Auftrag sowie die zugrunde liegenden Unterressourcen, einschließlich Eingabe(n), Ausgabe(n) und Transformation des Auftrags.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Support
Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte
Sie haben nun die Grundlagen der Verwendung eines .NET SDK zum Erstellen und Ausführen von Analyseaufträgen kennengelernt. Weitere Informationen erhalten Sie in den folgenden Artikeln:

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Verwenden des Azure Stream Analytics Management .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/