---
title: Programmgesteuertes Überwachen und Verwalten von Azure Stream Analytics-Aufträgen
description: Dieser Artikel beschreibt, wie Sie Stream Analytics-Aufträge, die mit REST-APIs, dem Azure SDK oder PowerShell erstellt wurden, programmgesteuert überwachen.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c746e409964b4625a4c9921d9f02ca0c9730a35
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123445"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programmgesteuertes Überwachen von Stream Analytics-Aufträgen

Dieser Artikel erläutert, wie die Überwachung für einen Stream Analytics-Auftrag aktiviert wird. Für Stream Analytics-Aufträge, die mit REST-APIs, Azure SDK oder PowerShell erstellt wurden, ist in der Standardeinstellung keine Überwachung aktiviert. Sie können diese manuell im Azure-Portal aktivieren, indem Sie zur Seite „Überwachen“ des Auftrags wechseln und auf die Schaltfläche „Aktivieren“ klicken. Sie können diesen Prozess auch automatisieren, indem Sie die in diesem Artikel beschriebenen Schritte ausführen. Die Überwachungsdaten werden im Azure-Portal im Bereich „Metriken“ für den Stream Analytics-Auftrag angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit den Anleitungen in diesem Prozess beginnen, müssen folgende Voraussetzungen erfüllt sein:

* Visual Studio 2019 oder 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) (heruntergeladen und installiert)
* Einen vorhandener Stream Analytics-Auftrag, für den die Überwachung aktiviert werden muss

## <a name="create-a-project"></a>Erstellen eines Projekts

1. Erstellen Sie eine Visual Studio C# .NET-Konsolenanwendung.
2. Führen Sie in der Paket-Manager-Konsole die folgenden Befehle zum Installieren der NuGet-Pakete aus. Das erste ist das Azure Stream Analytics Management .NET SDK. Das zweite ist das Azure Monitor SDK, mit dem die Überwachung aktiviert wird. Das letzte ist der Azure Active Directory-Client, der für die Authentifizierung verwendet wird.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Fügen Sie der Datei "App.config" den folgenden appSettings-Abschnitt hinzu.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Ersetzen Sie die Werte für *SubscriptionId* und *ActiveDirectoryTenantId* durch die IDs Ihres Azure-Abonnements und -Mandanten. Sie können diese Werte durch Ausführen des folgenden PowerShell-Cmdlets abrufen:
   
   ```powershell
   Get-AzureAccount
   ```
4. Fügen Sie die folgenden using-Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Fügen Sie eine Authentifizierungshilfsmethode hinzu.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Erstellen von Verwaltungsclients

Mit dem folgenden Code werden die erforderlichen Variablen und Verwaltungsclients eingerichtet.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Aktivieren der Überwachung für einen vorhandenen Stream Analytics-Auftrag

Mit dem folgenden Code wird die Überwachung für einen **vorhandenen** Stream Analytics-Auftrag aktiviert. Der erste Teil des Codes führt eine GET-Anforderung an den Stream Analytics-Dienst aus, um Daten zum jeweiligen Stream Analytics-Auftrag abzurufen. Hierbei wird die (mit der GET-Anforderung abgerufene) *ID* -Eigenschaft als Parameter für die Put-Methode in der zweiten Hälfte des Codes verwendet, die eine PUT-Anforderung an den Insights-Dienst sendet, um die Überwachung des Stream Analytics-Auftrags zu aktivieren.

> [!WARNING]
> Wenn Sie die Überwachung bereits für einen anderen Stream Analytics-Auftrag aktiviert haben, entweder über das Azure-Portal oder programmgesteuert über den folgenden Code, **wird empfohlen, dass Sie den gleichen Speicherkontonamen angeben wie bei der zuvor aktivierten Überwachung.**
> 
> Das Speicherkonto ist mit der Region verknüpft, in der Sie Ihren Stream Analytics-Auftrag erstellt haben, und nicht mit dem Auftrag selbst.
> 
> Alle Stream Analytics-Aufträge (und alle anderen Azure-Ressourcen) in derselben Region nutzen dieses Speicherkonto gemeinsam, um Überwachungsdaten zu speichern. Wenn Sie ein anderes Speicherkonto angeben, führt dies möglicherweise zu unbeabsichtigten Nebenwirkungen auf die Überwachung Ihrer anderen Stream Analytics-Aufträge oder anderen Azure-Ressourcen.
> 
> Der Speicherkontoname, der zum Ersetzen von `<YOUR STORAGE ACCOUNT NAME>` im folgenden Code verwendet wird, muss sich auf ein Speicherkonto beziehen, das zum gleichen Abonnement gehört wie der Stream Analytics-Auftrag, für den Sie die Überwachung aktivieren.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Support

Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)