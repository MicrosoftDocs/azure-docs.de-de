---
title: Verwalten von Azure Data Lake Analytics mithilfe des Azure .NET SDK
description: In diesem Artikel erfahren Sie, wie Sie das Azure .NET SDK verwenden, um Apps zu schreiben, die Data Lake Analytics-Aufträge, -Datenquellen und -Benutzer verwalten.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 0a10af73d754596e9b5bb34b2974d7f1647d06f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617706"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Verwalten von Azure Data Lake Analytics mithilfe einer .NET-App

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In diesem Artikel erfahren Sie, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mit einer App verwalten, die unter Verwendung des Azure .NET SDK geschrieben wurde. 

## <a name="prerequisites"></a>Voraussetzungen

* **Visual Studio 2015, Visual Studio 2013 Update 4 oder Visual Studio 2012 mit Installation von Visual C++** .
* **Microsoft Azure SDK für .NET-Version 2.5 oder höher**.  Führen Sie die Installation mit dem [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx)durch.
* **Erforderliche NuGet-Pakete**

### <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

|Paket|Version|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-preview|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-preview|

Sie können diese Pakete mit den folgenden Befehlen über die NuGet-Befehlszeile installieren:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Allgemeine Variablen

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Authentication

Sie haben mehrere Optionen für das Anmelden bei Azure Data Lake Analytics. Der folgende Codeausschnitt ist ein Beispiel für die interaktive Benutzerauthentifizierung mit einem Popupelement.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

Den Quellcode für **GetCreds_User_Popup** und den Code für andere Optionen für die Authentifizierung finden Sie unter [Azure Data Lake authentication options for .NET](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options) (Azure Data Lake-Authentifizierungsoptionen für .NET).


## <a name="create-the-client-management-objects"></a>Erstellen der Clientverwaltungsobjekte

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Konten verwalten

### <a name="create-an-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

Sie benötigen eine Azure-Ressourcengruppe, um Ihre Data Lake Analytics-Komponenten erstellen zu können. Sie benötigen Ihre Authentifizierungsanmeldeinformationen, eine Abonnement-ID und einen Standort. Der folgende Code zeigt, wie Sie eine Ressourcengruppe erstellen:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

Weitere Informationen finden Sie unter „Azure-Ressourcengruppen und Data Lake Analytics“.

### <a name="create-a-data-lake-store-account"></a>Erstellen eines Data Lake-Speicherkontos

Für jedes ADLA-Konto ist ein ADLS-Konto erforderlich. Wenn Sie noch über keines verfügen, können Sie es mit dem folgenden Code erstellen:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Erstellen eines Data Lake Analytics-Kontos

Mit dem folgenden Code wird ein ADLS-Konto erstellt.

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Auflisten der Data Lake Store-Konten

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Auflisten der Data Lake Analytics-Konten

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Überprüfen, ob ein Konto vorhanden ist

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Abrufen von Informationen zu einem Konto

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Löschen eines Kontos

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Abrufen des Data Lake Store-Standardkontos

Für jedes Data Lake Analytics-Konto ist ein Data Lake Store-Standardkonto erforderlich. Verwenden Sie den folgenden Code, um das Store-Standardkonto für ein Analytics-Konto zu ermitteln.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Verwalten von Datenquellen

Data Lake Analytics unterstützt derzeit die folgenden Datenquellen:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage-Konto](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Link zu einem Azure Storage-Konto

Sie können Links zu Azure Storage-Konten erstellen.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Auflisten von Azure Storage-Datenquellen

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Auflisten von Data Lake Store-Datenquellen

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Hochladen und Herunterladen von Ordnern und Dateien

Sie können das Clientverwaltungsobjekt des Data Lake Store-Dateisystems zum Hoch- und Herunterladen einzelner Dateien oder Ordner von Azure auf Ihren lokalen Computer verwenden. Dafür gibt es folgende Methoden:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

Der erste Parameter für diese Methoden ist der Name des Data Lake Store-Kontos, gefolgt von Parametern für den Quellpfad und den Zielpfad.

Im folgenden Beispiel wird gezeigt, wie ein Ordner im Data Lake Store heruntergeladen wird.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Erstellen einer Datei in einem Data Lake Store-Konto

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Überprüfen von Pfaden eines Azure Storage-Kontos

Der folgende Code überprüft, ob ein Azure Storage-Konto (StorageAccntName) in einem Data Lake Analytics-Konto (AnalyticsAccountName) vorhanden ist und ob ein Container (ContainerName) im Azure Storage-Konto vorhanden ist.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Verwalten von Katalog und Aufträgen

Das DataLakeAnalyticsCatalogManagementClient-Objekt enthält Methoden zum Verwalten der SQL-Datenbank, die für jedes Azure Data Lake Analytics-Konto bereitgestellt wird. Das DataLakeAnalyticsJobManagementClient-Objekt bietet Methoden zum Übermitteln und Verwalten von Aufträgen, die für die Datenbank ausgeführt werden (mit U-SQL-Skripts).

### <a name="list-databases-and-schemas"></a>Auflisten von Datenbanken und Schemas

Sie können zwar verschiedene Dinge auflisten, am häufigsten werden jedoch Datenbanken und das dazugehörige Schema aufgelistet. Der folgende Code ruft eine Sammlung von Datenbanken ab und zählt dann das Schema für die einzelnen Datenbank auf.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Auflisten von Tabellenspalten

Der folgende Code zeigt, wie Sie mit einem Data Lake Analytics-Katalogverwaltungsclient auf die Datenbank zugreifen, um die Spalten einer bestimmten Tabelle aufzulisten.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>Senden eines U-SQL-Auftrags

Der folgende Code stellt dar, wie ein Data Lake Analytics-Auftragverwaltungsclient zum Senden eines Auftrags verwendet wird.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Auflisten nicht erfolgreicher Aufträge

Der folgende Code listet Informationen zu nicht erfolgreichen Aufträgen auf.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Auflisten von Pipelines

Mit dem folgenden Code werden Informationen zu jeder Pipeline von Aufträgen aufgelistet, die an das Konto übermittelt werden.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Auflisten von Wiederholungen

Mit dem folgenden Code werden Informationen zu jeder Wiederholung von Aufträgen aufgelistet, die an das Konto übermittelt werden.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Allgemeine Graphszenarien

### <a name="look-up-user-in-the-aad-directory"></a>Suchen eines Benutzers im AAD-Verzeichnis

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Abrufen der ObjectId eines Benutzers im AAD-Verzeichnis

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Verwalten von Computerichtlinien

Das DataLakeAnalyticsAccountManagementClient-Objekt enthält Methoden zum Verwalten der Computerichtlinien für ein Data Lake Analytics-Konto.

### <a name="list-compute-policies"></a>Auflisten von Computerichtlinien

Mit dem folgenden Code wird eine Liste von Computerichtlinien für ein Data Lake Analytics-Konto abgerufen.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Erstellen einer Computerichtlinie

Mit dem folgenden Code wird eine neue Computerichtlinie für ein Data Lake Analytics-Konto erstellt, mit der die maximal verfügbaren Analytics-Einheiten für den angegebenen Benutzer auf 50 und die minimale Auftragspriorität auf 250 festgelegt wird.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md)
* [Überwachung und Problembehandlung von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)