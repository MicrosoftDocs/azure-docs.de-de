---
title: Verwenden des WebJobs SDK
description: Erfahren Sie mehr über das Schreiben von Code für das WebJobs SDK. Erstellen Sie ereignisgesteuerte Hintergrundverarbeitungsaufträge, die auf Daten in Azure und Diensten von Drittanbietern zugreifen.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: f941c394c3dab0e5e6997898a48a248f6a0cfe42
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352438"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Verwenden des WebJobs SDK für die ereignisgesteuerte Hintergrundverarbeitung

Dieser Artikel enthält Anleitungen zum Arbeiten mit dem Azure WebJobs SDK. Wenn Sie sofort mit WebJobs beginnen möchten, lesen Sie [Erste Schnitte mit dem Azure WebJobs SDK für die ereignisgesteuerte Hintergrundverarbeitung](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-Versionen

Dies sind die wichtigsten Unterschiede zwischen Version 3.*x* und Version 2.*x* des WebJobs SDK:

* Version 3. *x* fügt Unterstützung für .NET Core hinzu.
* In Version 3.*x* müssen Sie die Storage-Bindungserweiterung. die für das WebJobs SDK erforderlich ist, explizit installieren. In Version 2.*x* sind die Speicherbindungen im SDK enthalten.
* Visual Studio-Tools für .NET Core (3.*x*)-Projekte unterscheiden sich von Tools für .NET Framework (2.*x*)-Projekte. Weitere Informationen finden Sie unter [Entwickeln und Bereitstellen von WebJobs mit Visual Studio – Azure App Service](webjobs-dotnet-deploy-vs.md).

Wenn möglich werden Beispiele sowohl für Version 3.*x* als auch Version 2.*x* bereitgestellt.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) basiert auf dem WebJobs SDK. Dieser Artikel enthält bei einigen Themen Links zur Azure Functions-Dokumentation. Beachten Sie diese Unterschiede zwischen Functions und dem WebJobs SDK:
> * Azure Functions 2.*x* entspricht dem WebJobs SDK 3.*x*. Azure Functions 1.*x* entspricht dem WebJobs SDK 2.*x*. Quellcoderepositorys verwenden die WebJobs SDK-Nummerierung.
> * Der Beispielcode für Azure Functions C#-Klassenbibliotheken entspricht dem WebJobs SDK-Code – mit der Ausnahme, dass Sie in einem WebJobs SDK-Projekt kein `FunctionName`-Attribut benötigen.
> * Einige Bindungstypen wie HTTP (Webhooks) und Event Grid (der auf HTTP basiert) werden nur in Functions unterstützt.
>
> Weitere Informationen finden Sie unter [Vergleich von WebJobs SDK und Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs-Host

Der Host ist ein Runtimecontainer für Funktionen.  Er lauscht auf Trigger und ruft Funktionen auf. In Version 3. *x* ist der Host eine Implementierung von `IHost`. In Version 2.*x* verwenden Sie das `JobHost`-Objekt. Sie erstellen eine Hostinstanz in Ihrem Code und schreiben Code zum Anpassen des entsprechenden Verhaltens.

Das ist ein entscheidender Unterschied zwischen der direkten Verwendung des WebJobs SDK und der indirekten Verwendung über Azure Functions. In Azure Functions wird der Host durch den Dienst gesteuert und kann nicht mittels Code angepasst werden. In Azure Functions können Sie das Hostverhalten über die Einstellungen in der Datei „host.json“ anpassen. Bei diesen Einstellungen handelt es sich um Zeichenfolgen, nicht um Code. Dadurch werden die Arten von Anpassungen, die Sie vornehmen können, eingeschränkt.

### <a name="host-connection-strings"></a>Hostverbindungszeichenfolgen

Bei lokaler Ausführung sucht das WebJobs SDK in der Datei „local.settings.json“ nach Azure Storage- und Azure Service Bus-Verbindungszeichenfolgen. Bei Ausführung in Azure sucht es in der WebJobs-Umgebung. Standardmäßig ist eine Einstellung für die Speicherverbindungszeichenfolge namens `AzureWebJobsStorage` erforderlich.  

In Version 2.*x* des SDK können Sie eigene Namen für diese Verbindungszeichenfolgen verwenden oder sie an einem anderen Ort speichern. Sie können Namen im Code mit der [`JobHostConfiguration`] festlegen, wie hier gezeigt:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

> [!NOTE]
> Aufgrund der Verwendung der standardmäßigen .NET Core-Konfigurations-APIs steht in Version 3.*x* keine API zum Ändern von Verbindungszeichenfolgennamen zur Verfügung. Siehe [Entwickeln und Bereitstellen von WebJobs mit Visual Studio](webjobs-dotnet-deploy-vs.md).

### <a name="host-development-settings"></a>Hostentwicklungseinstellungen

Sie können den Host im Entwicklungsmodus ausführen, um die lokale Entwicklung effizienter zu machen. Bei der Ausführung im Entwicklungsmodus ändern sich unter anderem folgende Einstellungen:

| Eigenschaft | Entwicklungseinstellung |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` zum Maximieren der Protokollausgabe. |
| `Queues.MaxPollingInterval`  | Ein niedriger Wert, um sicherzustellen, dass die Warteschlangenmethoden sofort ausgelöst werden.  |
| `Singleton.ListenerLockPeriod` | 15 Sekunden, um eine schnelle iterative Entwicklung zu erleichtern. |

Der Prozess zum Aktivieren des Entwicklungsmodus hängt von der SDK-Version ab. 

#### <a name="version-3x"></a>Version 3.*x*

In Version 3.*x* werden die standardmäßigen ASP.NET Core-APIs verwendet. Rufen Sie die [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment)-Methode für die [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)-Instanz auf. Übergeben Sie eine Zeichenfolge namens `development` wie in diesem Beispiel:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Die Klasse `JobHostConfiguration` verfügt zum Aktivieren des Entwicklungsmodus über die Methode `UseDevelopmentSettings`.  Im folgenden Beispiel wird die Verwendung von Entwicklungseinstellungen aufgezeigt. Legen Sie eine lokale Umgebungsvariable namens `AzureWebJobsEnv` mit dem Wert `Development` fest, damit `config.IsDevelopment` bei lokaler Ausführung den Wert `true` zurückgibt.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Verwalten gleichzeitiger Verbindungen (Version 2.*x*)

In Version 3.*x* ist die Verbindungsanzahl standardmäßig nicht beschränkt. Wenn Sie diese Einstellung ändern müssen, können Sie die Eigenschaft [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) der Klasse [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) verwenden.

In Version 2.*x* steuern die Anzahl gleichzeitiger Verbindungen mit einem Host über die API [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit). Es empfiehlt sich, in Version 2.*x* den Standardwert „2“ zu erhöhen, bevor Sie Ihren WebJobs-Host starten.

Alle ausgehenden HTTP-Anforderungen, die Sie über eine Funktion mit `HttpClient` ausführen, durchlaufen `ServicePointManager`. Nachdem Sie den in `DefaultConnectionLimit` festgelegten Wert erreicht haben, startet `ServicePointManager` das Queueing von Anforderungen, bevor er sie sendet. Angenommen, Ihr `DefaultConnectionLimit` ist auf 2 festgelegt, und Ihr Code führt 1.000 HTTP-Anforderungen aus. In diesem Fall werden zunächst nur zwei Anforderungen an das Betriebssystem übermittelt. Die verbleibenden 998 Anforderungen werden in die Warteschlange eingereiht, bis genügend Platz für sie vorhanden ist. Dies bedeutet: Möglicherweise tritt ein Timeout für Ihren `HttpClient` auf, weil es so scheint, als hätte er die Anforderung vorgenommen, die aber nie vom Betriebssystem an den Zielserver gesendet wurde. So tritt möglicherweise ein Verhalten auf, das scheinbar keinen Sinn ergibt: Ihr lokaler `HttpClient` benötigt 10 Sekunden, um eine Anforderung abzuschließen, aber Ihr Dienst gibt jede Anforderung in 200 ms zurück. 

Der Standardwert für ASP.NET-Anwendungen ist `Int32.MaxValue`. Und dies funktioniert wahrscheinlich gut, wenn WebJobs in einem App Service-Plan des Typs „Basic“ oder höher ausgeführt wird. Für WebJobs ist in der Regel die „Always On“-Einstellung erforderlich, und diese wird nur von einem App Service-Plan des Typs „Basic“ oder höher unterstützt.

Bei der Ausführung des WebJobs in einem App Service-Plan des Typs „Free“ oder „Shared“ wird Ihre Anwendung vom App Service-Sandkasten (Sandbox) eingeschränkt, für den aktuell ein [Verbindungslimit von 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits) gilt. Bei einem ungebundenen Verbindungslimit in `ServicePointManager` ist es wahrscheinlicher, dass der Schwellenwert für die Sandbox-Verbindung erreicht und die Website heruntergefahren wird. In diesem Fall kann dies durch Festlegen von `DefaultConnectionLimit` auf einen niedrigeren Wert (wie 50 oder 100) verhindert werden und trotzdem einen ausreichenden Durchsatz ermöglichen.

Die Einstellung muss vor dem Ausführen von HTTP-Anforderungen konfiguriert werden. Aus diesem Grund sollte der WebJobs-Host die Einstellung nicht automatisch anpassen. Es könnte HTTP-Anforderungen geben, die vor dem Start des Hosts erfolgen. Dies könnte zu einem unerwarteten Verhalten führen. Der beste Ansatz besteht darin, den Wert sofort in Ihrer `Main`-Methode festzulegen, bevor Sie den `JobHost` initialisieren, wie hier gezeigt:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Trigger

Funktionen müssen öffentliche Methoden sein und ein Triggerattribut oder das [`NoAutomaticTrigger`](#manual-triggers)-Attribut aufweisen.

### <a name="automatic-triggers"></a>Automatische Trigger

Automatische Trigger rufen eine Funktion als Reaktion auf ein Ereignis auf. Sehen Sie sich dieses Beispiel für eine Funktion an, die durch eine Azure Queue Storage hinzugefügte Nachricht ausgelöst wird. Sie reagiert durch Lesen eines Blobs aus Azure Blob Storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Das `QueueTrigger`-Attribut weist die Runtime an, die Funktion jedes Mal aufzurufen, wenn eine Warteschlangennachricht in der `myqueue-items`-Warteschlange angezeigt wird. Das `Blob`-Attribut weist die Runtime an, die Warteschlangennachricht zum Lesen eines Blobs im Container *Beispielarbeitselemente* zu verwenden. Der Name des Blobelements im `samples-workitems`-Container wird als Bindungsausdruck (`{queueTrigger}`) direkt aus dem Warteschlangentrigger abgerufen.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Manuelle Trigger

Wenn Sie eine Funktion manuell auslösen möchten, verwenden Sie das `NoAutomaticTrigger`-Attribut, wie hier gezeigt:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Der Prozess zum manuellen Auslösen der Funktion hängt von der SDK-Version ab.

#### <a name="version-3x"></a>Version 3.*x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Eingabe- und Ausgabebindungen

Eingabebindungen bieten eine deklarative Möglichkeit, Daten aus Azure oder Diensten von Drittanbietern für Ihren Code verfügbar zu machen. Ausgabebindungen bieten eine Möglichkeit, Daten zu aktualisieren. Im Artikel [Erste Schritte](webjobs-sdk-get-started.md) finden Sie ein Beispiel für die einzelnen Bindungen.

Sie können einen Rückgabewert einer Methode für eine Ausgabebindung nutzen, indem Sie das Attribut auf den Rückgabewert einer Methode anwenden. Siehe dazu das Beispiel in [Verwenden des Rückgabewerts einer Azure-Funktion](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Bindungstypen

Der Prozess zum Installieren und Verwalten von Bindungstypen hängt davon ab, ob Sie Version 3.*x* oder Version 2.*x* des SDK verwenden. Sie finden das für einen bestimmten Bindungstyp zu installierende Paket im Abschnitt „Pakete“ des jeweiligen [Referenzartikels](#binding-reference-information) von Azure Functions für diesen Bindungstyp. Eine Ausnahme ist der Trigger- und Bindungstyp „Dateien“ (für das lokale Dateisystem), der von Azure Functions nicht unterstützt wird.

#### <a name="version-3x"></a>Version 3.*x*

In Version 3.*x* sind die Speicherbindungen im Paket `Microsoft.Azure.WebJobs.Extensions.Storage` enthalten. Rufen Sie die Erweiterungsmethode `AddAzureStorage` in der `ConfigureWebJobs`-Methode auf, wie hier gezeigt:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Wenn Sie andere Trigger- und Bindungstypen verwenden möchten, installieren Sie das NuGet-Paket, das diese Typen enthält, und rufen Sie die in der Erweiterung implementierte Erweiterungsmethode `Add<binding>` auf. Wenn Sie also beispielsweise eine Azure Cosmos DB-Bindung verwenden möchten, installieren Sie `Microsoft.Azure.WebJobs.Extensions.CosmosDB`, und rufen Sie `AddCosmosDB` wie folgt auf:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Wenn Sie den Trigger „Zeitgeber“ und die Bindung „Dateien“ verwenden möchten, die beide Teil der Kerndienste sind, rufen Sie die Erweiterungsmethode `AddTimers` bzw. `AddFiles` auf.

#### <a name="version-2x"></a>Version 2.*x*

In Version 2.*x* des Pakets `Microsoft.Azure.WebJobs` sind folgende Trigger- und Bindungstypen enthalten:

* Blob Storage
* Queue Storage
* Table Storage

Wenn Sie andere Trigger- und Bindungstypen verwenden möchten, installieren Sie das NuGet-Paket, das diese Typen enthält, und rufen Sie eine `Use<binding>`-Methode für das `JobHostConfiguration`-Objekt auf. Wenn Sie beispielsweise einen Zeitgebertrigger verwenden möchten, installieren Sie `Microsoft.Azure.WebJobs.Extensions`, und rufen Sie `UseTimers` in der `Main`-Methode auf, wie hier gezeigt:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Wenn Sie die Bindung „Dateien“ verwenden möchten, installieren Sie `Microsoft.Azure.WebJobs.Extensions`, und rufen Sie `UseFiles` auf.

### <a name="executioncontext"></a>Ausführungskontext (ExecutionContext)

WebJobs ermöglicht Bindungen an einen Ausführungskontext ([`ExecutionContext`]). Mit dieser Bindung können Sie auf den Ausführungskontext ([`ExecutionContext`]) als Parameter in Ihrer Funktionssignatur zugreifen. Im folgenden Code wird das Kontextobjekt beispielsweise verwendet, um auf die Aufruf-ID zuzugreifen, mit der Sie alle von einem bestimmten Funktionsaufruf erzeugten Protokolle korrelieren können.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Der Prozess zum Binden an den [`ExecutionContext`] hängt von Ihrer SDK-Version ab.

#### <a name="version-3x"></a>Version 3.*x*

Rufen Sie die Erweiterungsmethode `AddExecutionContextBinding` in der `ConfigureWebJobs`-Methode auf, wie hier gezeigt:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

In dem zuvor erwähnten `Microsoft.Azure.WebJobs.Extensions`-Paket wird auch ein spezieller Bindungstyp bereitgestellt, den Sie durch Aufrufen der `UseCore`-Methode registrieren können. Mit dieser Bindung können Sie einen [`ExecutionContext`]-Parameter in Ihrer Funktionssignatur definieren und wie folgt aktivieren:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Bindungskonfiguration

Sie können das Verhalten einiger Trigger und Bindungen konfigurieren. Der Prozess für deren Konfiguration hängt von der SDK-Version ab.

* **Version 3.*x*:** Legen Sie die Konfiguration fest, wenn die `Add<Binding>`-Methode in `ConfigureWebJobs` aufgerufen wird.
* **Version 2.*x*:** Legen Sie die Konfiguration fest, indem Sie Eigenschaften in einem Konfigurationsobjekt festlegen, das Sie an `JobHost` übergeben.

Diese bindungsspezifischen Einstellungen sind gleichwertig mit Einstellungen in der [host.json-Projektdatei](../azure-functions/functions-host-json.md) in Azure Functions.

Sie können die folgenden Bindungen konfigurieren:

* [Azure Cosmos DB-Trigger](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs-Trigger](#event-hubs-trigger-configuration-version-3x)
* [Queue Storage-Trigger](#queue-storage-trigger-configuration)
* [SendGrid-Bindung](#sendgrid-binding-configuration-version-3x)
* [Service Bus-Trigger](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfiguration des Azure Cosmos DB-Triggers (Version 3.*x*)

Dieses Beispiel zeigt, wie der Azure Cosmos DB-Trigger konfiguriert wird:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Weitere Informationen finden Sie im Artikel [Azure Cosmos DB-Bindung](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings).

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfiguration des Event Hubs-Triggers (Version 3.*x*)

Dieses Beispiel zeigt, wie der Event Hubs-Trigger konfiguriert wird:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Weitere Informationen finden Sie im Artikel [Event Hubs-Bindung](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json).

### <a name="queue-storage-trigger-configuration"></a>Konfiguration des Queue Storage-Triggers

Diese Beispiele zeigen, wie der Queue Storage-Trigger konfiguriert wird:

#### <a name="version-3x"></a>Version 3.*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Weitere Informationen finden Sie im Artikel [Queue Storage-Bindung](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties).

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Weitere Informationen finden Sie in der [host.json v1.x-Referenz](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfiguration der SendGrid-Bindung (Version 3.*x*)

In diesem Beispiel wird gezeigt, wie die SendGrid-Ausgabebindung konfiguriert wird:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Weitere Informationen finden Sie im Artikel [SendGrid-Bindung](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings).

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfiguration des Service Bus-Triggers (Version 3.*x*)

Dieses Beispiel zeigt, wie der Service Bus-Trigger konfiguriert wird:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Weitere Informationen finden Sie im Artikel [Service Bus-Bindung](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings).

### <a name="configuration-for-other-bindings"></a>Konfiguration für andere Bindungen

Einige Trigger- und Bindungstypen definieren ihre eigenen benutzerdefinierten Konfigurationstypen. Mit dem Dateitrigger können Sie beispielsweise den zu überwachenden Stammpfad angeben, wie in diesen Beispielen gezeigt wird:

#### <a name="version-3x"></a>Version 3.*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Bindungsausdrücke

In Attributkonstruktorparametern können Sie Ausdrücke verwenden, die in Werte aus verschiedenen Quellen aufgelöst werden. Im folgenden Code erstellt beispielsweise der Pfad für das `BlobTrigger`-Attribut einen Ausdruck mit dem Namen `filename`. Bei Verwendung für die Ausgabebindung wird `filename` in den Namen des auslösenden Blobs aufgelöst.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Weitere Informationen zu Bindungsausdrücken finden Sie unter [Bindungsausdrücke und Muster](../azure-functions/functions-bindings-expressions-patterns.md) in der Azure Functions-Dokumentation.

### <a name="custom-binding-expressions"></a>Benutzerdefinierte Bindungsausdrücke

Manchmal möchten Sie einen Warteschlangennamen, einen Blobnamen oder Container oder aber einen Tabellennamen im Code angeben, anstatt ihn hart zu codieren. So möchten Sie z. B. den Warteschlangennamen für das `QueueTrigger`-Attribut in einer Konfigurationsdatei oder Umgebungsvariablen angeben.

Sie erreichen dies, indem Sie ein `NameResolver`-Objekt in das `JobHostConfiguration`-Objekt übergeben. Sie schließen Platzhalter in Konstruktorparameter von Trigger- oder Bindungsattributen ein, und Ihr `NameResolver`-Code gibt die tatsächlichen Werte an, die anstelle dieser Platzhalter verwendet werden sollen. Sie identifizieren Platzhalter, indem Sie davor und dahinter ein Prozentzeichen (%) eingeben, wie hier gezeigt:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Mit diesem Code können Sie eine Warteschlange namens `logqueuetest` in der Testumgebung und eine Warteschlange namens `logqueueprod` in der Produktionsumgebung verwenden. Anstelle eines hartcodierten Warteschlangennamens geben Sie den Namen eines Eintrags in der `appSettings`-Auflistung an.

Wenn Sie keine benutzerdefinierte Namensauflösung angeben, wird ein Standard-`NameResolver` wirksam. Der Standardwert ruft Werte aus App-Einstellungen oder Umgebungsvariablen ab.

Ihre `NameResolver`-Klasse ruft den Warteschlangennamen aus `appSettings` ab, wie hier gezeigt:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Version 3.*x*

Sie konfigurieren den Resolver mithilfe von Abhängigkeitsinjektion. Für diese Beispiele ist die folgende `using`-Anweisung erforderlich:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Sie fügen den Resolver hinzu, indem Sie die [`ConfigureServices`]-Erweiterungsmethode für [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) wie in diesem Beispiel aufrufen:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

Übergeben Sie Ihre `NameResolver`-Klasse an das `JobHost`-Objekt, wie hier gezeigt:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementiert `INameResolver`, um Werte aus App-Einstellungen abzurufen, wie im Beispiel gezeigt. Wenn Sie das WebJobs SDK direkt verwenden, können Sie eine benutzerdefinierte Implementierung schreiben, die Ersatzwerte für Platzhalter aus einer beliebigen von Ihnen gewünschten Quelle abruft.

## <a name="binding-at-runtime"></a>Binden zur Laufzeit

Wenn Sie vor der Verwendung eines Bindungsattributs wie `Queue`, `Blob` oder `Table` weitere Aufgaben in Ihrer Funktion ausführen müssen, können Sie dazu die `IBinder`-Schnittstelle verwenden.

Im folgenden Beispiel wird aus einer Nachricht der Eingabewarteschlange eine neue Nachricht mit dem gleichen Inhalt in einer Ausgabewarteschlange erstellt. Der Name der Ausgabewarteschlangenname wird durch Code im Text der Funktion festgelegt.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Weitere Informationen finden Sie unter [Binden zur Laufzeit](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) in der Azure Functions-Dokumentation.

## <a name="binding-reference-information"></a>Referenzinformationen für Bindungen

Die Azure Functions-Dokumentation enthält Referenzinformationen zu den einzelnen Bindungstypen. In jedem Bindungsreferenzartikel finden Sie die folgenden Informationen. (Dieses Beispiel basiert auf der Speicherwarteschlange.)

* [Pakete](../azure-functions/functions-bindings-storage-queue.md). Das Paket, das Sie installieren müssen, um Unterstützung für die Bindung in ein WebJobs SDK-Projekt zu integrieren.
* [Beispiele](../azure-functions/functions-bindings-storage-queue-trigger.md). Codebeispiele. Das Beispiel für die C#-Klassenbibliothek bezieht sich auf das WebJobs SDK. Lassen Sie nur das `FunctionName`Attribut weg.
* [Attribute](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Die für den Bindungstyp zu verwendenden Attribute.
* [Konfiguration](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Erläuterungen der Attributeigenschaften und Konstruktorparameter.
* [Verwendung:](../azure-functions/functions-bindings-storage-queue-trigger.md#usage) Die Typen, die Sie binden können, und Informationen zur Funktionsweise der Bindung. Beispiele: Abrufalgorithmus, Verarbeitung der Warteschlange für nicht verarbeitete Nachrichten.
  
Eine Liste der Bindungsreferenzartikel finden Sie im Artikel [Trigger und Bindungen](../azure-functions/functions-triggers-bindings.md#supported-bindings) für Azure Functions unter „Unterstützte Bindungen“. Die in dieser Liste aufgeführten Bindungen „HTTP“, „Webhook“ und „Event Grid“ werden nur von Azure Functions und nicht vom WebJobs SDK unterstützt.

## <a name="disable-attribute"></a>Disable-Attribut 

Mit dem [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)-Attribut können Sie steuern, ob eine Funktion ausgelöst werden kann. 

Wenn die App-Einstellung `Disable_TestJob` im folgenden Beispiel den Wert `1` oder `True` (Groß-/Kleinschreibung nicht beachten) aufweist, wird die Funktion nicht ausgeführt. In diesem Fall erstellt die Runtime die Protokollmeldung *Funktion „Functions.TestJob“ ist deaktiviert*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Wenn Sie im Azure-Portal die Werte einer App-Einstellung ändern, wird der WebJob neu gestartet, um die neue Einstellung zu übernehmen.

Das Attribut kann auf Parameter-, Methoden- oder Klassenebene deklariert werden. Der Einstellungsname kann auch Bindungsausdrücke enthalten.

## <a name="timeout-attribute"></a>Timeout-Attribut

Das [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)-Attribut bewirkt, dass eine Funktion abgebrochen wird, wenn sie nicht innerhalb einer angegebenen Zeit abgeschlossen wurde. Im folgenden Beispiel wird die Funktion ohne das Timeout-Attribut einen Tag lang ausgeführt. Timeout bewirkt, dass die Funktion nach 15 Sekunden abgebrochen werden soll.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Sie können das Timeout-Attribut auf Klassen- oder Methodenebene anwenden, und mit `JobHostConfiguration.FunctionTimeout` können Sie ein globales Zeitlimit angeben. Zeitlimits auf Klassen- oder Methodenebene setzen das globale Zeitlimit außer Kraft.

## <a name="singleton-attribute"></a>Singleton-Attribut

Das [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)-Attribut stellt sicher, dass nur eine Instanz einer Funktion ausgeführt wird, auch wenn mehrere Instanzen der Host-Web-App vorhanden sind. Dies wird mithilfe einer [verteilten Sperre](#viewing-lease-blobs) erreicht.

In diesem Beispiel wird immer nur eine einzige Instanz der `ProcessImage`-Funktion ausgeführt:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

In einige Trigger ist die Unterstützung der Parallelitätsverwaltung integriert:

* **QueueTrigger**. Legen Sie `JobHostConfiguration.Queues.BatchSize` auf `1` fest.
* **ServiceBusTrigger**. Legen Sie `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` auf `1` fest.
* **FileTrigger**. Legen Sie `FileProcessor.MaxDegreeOfParallelism` auf `1` fest.

Sie können diese Einstellungen verwenden, um sicherzustellen, dass Ihre Funktion als Singleton auf einer einzigen Instanz ausgeführt wird. Wenn Sie sicherstellen möchten, dass nur eine Instanz der Funktion ausgeführt wird, wenn die Web-App auf mehrere Instanzen skaliert wird, wenden Sie eine Singleton-Sperre auf Listener-Ebene für die Funktion an (`[Singleton(Mode = SingletonMode.Listener)]`). Listener-Sperren werden beim Starten des JobHosts abgerufen. Wenn drei horizontal skalierte Instanzen zur selben Zeit gestartet werden, erhält nur eine der Instanzen die Sperre, und es wird nur ein Listener gestartet.

> [!NOTE]
> Informationen zur Funktionsweise der „SingletonMode.Function“ finden Sie in diesem [GitHub-Repository](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs).

### <a name="scope-values"></a>Bereichswerte

Sie können einen *Bereichsausdruck/Wert* in einem Singleton angeben. Der Ausdruck/Wert stellt sicher, dass alle Ausführungen der Funktion in einem bestimmten Bereich serialisiert werden. Die Implementierung einer detaillierteren Sperre auf diese Weise kann eine gewisse Parallelität für Ihre Funktion ermöglichen, während andere Aufrufe entsprechend Ihren Anforderungen serialisiert werden. Im folgenden Code wird beispielsweise der Bereichsausdruck an den Wert `Region` der eingehenden Nachricht gebunden. Wenn die Warteschlange drei Nachrichten in den Regionen „East“, „East“ und „West“ enthält, werden die Nachrichten, die die Region „East“ enthalten, seriell ausgeführt, während die Nachricht mit der Region „West“ parallel dazu ausgeführt wird.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

Der Standardbereich für eine Sperre ist `SingletonScope.Function`. Dies bedeutet, dass der Sperrenbereich (der Blob-Leasepfad) an den vollqualifizierten Funktionsnamen gebunden ist. Um funktionenübergreifende Sperren festzulegen, geben Sie `SingletonScope.Host` an, und verwenden Sie eine Bereichs-ID, die für alle Funktionen, die nicht gleichzeitig ausgeführt werden sollen, identisch ist. Im folgenden Beispiel wird jeweils nur eine Instanz von `AddItem` oder `RemoveItem` ausgeführt:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Anzeigen von Lease-Blobs

Das WebJobs SDK verwendet [Azure-Blob-Leases](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) im Hintergrund, um eine verteilte Sperre zu implementieren. Die von Singleton verwendeten Lease-Blobs finden Sie im `azure-webjobs-host`-Container im `AzureWebJobsStorage`-Speicherkonto unter dem Pfad „locks“ (Sperren). Beispielsweise könnte der Lease-Blob-Pfad für das erste zuvor dargestellte `ProcessImage`-Beispiel `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` lauten. Alle Pfade enthalten die JobHost-ID, in diesem Fall „061851c758f04938a4426aa9ab3869c0“.

## <a name="async-functions"></a>Asynchrone Funktionen

Informationen zum Codieren asynchroner Funktionen finden Sie in der [Azure Functions-Dokumentation](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Abbruchtoken

Informationen zum Umgang mit Abbruchtoken finden Sie in der Azure Functions-Dokumentation unter [Abbruchtoken und ordnungsgemäßes Herunterfahren](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Mehrere Instanzen

Bei der Ausführung Ihrer Web-App auf mehreren Instanzen wird ein kontinuierlicher WebJob auf allen Instanzen ausgeführt, der Trigger überwacht und Funktionen aufruft. Die verschiedenen Triggerbindungen sind so konzipiert, dass die Arbeit effizient über mehrere Instanzen hinweg geteilt wird, sodass Sie bei einer Skalierung auf mehrere Instanzen mehr Last bewältigen können.

Während manche Trigger zu einer Doppelverarbeitung führen können, hindern Warteschlangen- und Blob-Speichertrigger eine Funktion automatisch an der mehrmaligen Verarbeitung einer Warteschlangennachricht oder eines Blobs. Weitere Informationen finden Sie unter [Entwerfen für identische Eingaben](../azure-functions/functions-idempotent.md) in der Azure Functions-Dokumentation.

Mit dem Zeitgebertrigger wird automatisch sichergestellt, dass jeweils nur eine Instanz des Zeitgebers ausgeführt wird, damit zu einem geplanten Zeitpunkt nicht mehrere Funktionsinstanzen ausgeführt werden.

Wenn Sie sicherstellen möchten, dass nur eine Instanz einer Funktion ausgeführt wird, auch wenn mehrere Instanzen der Host-Web-App vorhanden sind, können Sie das [`Singleton`](#singleton-attribute)-Attribut verwenden.

## <a name="filters"></a>Filter

Funktionsfilter (Vorschauversion) bieten eine Möglichkeit zum Anpassen der WebJobs-Ausführungspipeline mit Ihrer eigenen Logik. Die Filter sind mit [ASP.NET Core-Filtern](/aspnet/core/mvc/controllers/filters) vergleichbar. Sie können sie als deklarative Attribute implementieren, die auf Ihre Funktionen oder Klassen angewendet werden. Weitere Informationen finden Sie unter [Funktionsfilter](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

Wir empfehlen das für ASP.NET entwickelte Protokollierungsframework. Im Artikel [Erste Schritte](webjobs-sdk-get-started.md) finden Sie ein Beispiel für dessen Verwendung. 

### <a name="log-filtering"></a>Protokollfilterung

Jedem von einer `ILogger`-Instanz erstellten Protokoll ist eine `Category` und eine `Level` zugeordnet. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) ist eine Enumeration, und der Code (eine ganze Zahl) weist auf die relative Bedeutung hin:

|LogLevel    |Code|
|------------|---|
|Trace       | 0 |
|Debuggen       | 1 |
|Information | 2 |
|Warnung     | 3 |
|Fehler       | 4 |
|Kritisch    | 5 |
|Keine        | 6 |

Sie können jede Kategorie unabhängig voneinander auf eine bestimmte [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) filtern. So könnten Sie beispielsweise alle Protokolle für die Verarbeitung des Blob-Triggers, aber nur `Error` und höhere Ebenen für alles andere anzeigen.

#### <a name="version-3x"></a>Version 3.*x*

In Version 3.*x* des SDK wird die in .NET Core-integrierte Filterung verwendet. Mit der Klasse `LogCategories` können Sie Kategorien für bestimmte Funktionen, Trigger oder Benutzer definieren. Sie definiert auch Filter für bestimmte Hostzustände wie `Startup` und `Results`. So können Sie die Protokollausgabe optimieren. Sollte in den definierten Kategorien keine Übereinstimmung gefunden werden, wird der Filter bei der Entscheidung, ob die Nachricht gefiltert werden soll, auf den Wert `Default` zurückgesetzt.

`LogCategories` erfordert die folgende using-Anweisung:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Im folgenden Beispiel wird ein Filter erstellt, der standardmäßig alle Protokolle auf der `Warning`-Ebene filtert. Die Kategorien `Function` und `results` (äquivalent zu `Host.Results` in Version2.*x*) werden auf der Ebene `Error` gefiltert. Der Filter vergleicht die aktuelle Kategorie mit allen registrierten Ebenen in der `LogCategories`-Instanz und wählt die längste Übereinstimmung aus. Dies bedeutet, dass die für `Host.Triggers` registrierte `Debug`-Ebene entweder `Host.Triggers.Queue` oder `Host.Triggers.Blob` entspricht. Dadurch können Sie umfassendere Kategorien steuern, ohne jede einzelne Kategorie hinzufügen zu müssen.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2.*x*

In Version 2.*x* des SDK verwenden Sie die Klasse `LogCategoryFilter` zum Steuern der Filterung. Der `LogCategoryFilter` verfügt über eine `Default`-Eigenschaft mit dem Anfangswert `Information`. Dies bedeutet, dass alle Nachrichten der Ebene `Information`, `Warning`, `Error` oder `Critical` protokolliert, aber Nachrichten der Ebene `Debug` oder `Trace` weggefiltert werden.

Die Eigenschaft `CategoryLevels` ermöglicht genau wie `LogCategories` in Version 3.*x* das Angeben von Protokollebenen für bestimmte Kategorien, sodass Sie die Protokollierungsausgabe optimieren können. Wird im `CategoryLevels`-Wörterbuch keine Übereinstimmung gefunden, wird der Filter bei der Entscheidung, ob die Nachricht gefiltert werden soll, auf den `Default`-Wert zurückgesetzt.

Im folgenden Beispiel wird ein Filter erstellt, der standardmäßig alle Protokolle auf der `Warning`-Ebene filtert. Die Kategorien `Function` und `Host.Results` werden auf der `Error`-Ebene gefiltert. Der `LogCategoryFilter` vergleicht die aktuelle Kategorie mit allen registrierten `CategoryLevels` und wählt die längste Übereinstimmung aus. Deshalb entspricht die für `Host.Triggers` registrierte `Debug`-Ebene entweder `Host.Triggers.Queue` oder `Host.Triggers.Blob`. Dadurch können Sie umfassendere Kategorien steuern, ohne jede einzelne Kategorie hinzufügen zu müssen.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Benutzerdefinierte Telemetrie für Application Insights

Der Prozess zum Implementieren von benutzerdefinierter Telemetrie für [Application Insights](../azure-monitor/app/app-insights-overview.md) hängt von der SDK-Version ab. Informationen zum Konfigurieren von Application Insights finden Sie unter [Hinzufügen der Application Insights-Protokollierung](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Version 3.*x*

Da in Version 3.*x* des WebJobs SDK der generische .NET Core-Host verwendet wird, steht keine Factory für benutzerdefinierte Telemetrie mehr zur Verfügung. Sie können der Pipeline aber mithilfe von Abhängigkeitsinjektion benutzerdefinierte Telemetrie hinzufügen. Für die Beispiele in diesem Abschnitt sind folgende `using`-Anweisungen erforderlich:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Mit der folgenden benutzerdefinierten Implementierung von [`ITelemetryInitializer`] können Sie der standardmäßigen Telemetriekonfiguration ([`TelemetryConfiguration`]) Ihre eigene Telemetrie ([`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry)) hinzufügen:

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Rufen Sie im Generator [`ConfigureServices`] auf, um der Pipeline Ihre benutzerdefinierte Instanz von [`ITelemetryInitializer`] hinzuzufügen:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Beim Konstruieren der Telemetriekonfiguration ([`TelemetryConfiguration`]) werden alle registrierten Typen von [`ITelemetryInitializer`] eingeschlossen. Weitere Informationen finden Sie unter [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](../azure-monitor/app/api-custom-events-metrics.md).

In Version 3.*x* muss der [`TelemetryClient`] nicht mehr geleert werden, wenn der Host beendet wird. Das Abhängigkeitsinjektionssystem von .NET Core verwirft automatisch den registrierten Application Insights-Protokollierungsanbieter (`ApplicationInsightsLoggerProvider`), wodurch der Telemetrieclient ([`TelemetryClient`]) geleert wird.

#### <a name="version-2x"></a>Version 2.*x*

In Version 2.*x* verwendet der [`TelemetryClient`], der intern durch den Application Insights-Anbieter für das WebJobs SDK erstellt wurde, [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll). Wenn der Application Insights-Endpunkt nicht verfügbar ist oder eingehende Anforderungen gedrosselt werden, [speichert dieser Kanal die Anforderungen im Dateisystem der Web-App und übermittelt sie später erneut](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/).

Der Telemetrieclient ([`TelemetryClient`]) wird von einer Klasse erstellt, die `ITelemetryClientFactory` implementiert. Das ist standardmäßig [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

Wenn Sie einen Teil der Application Insights-Pipeline ändern möchten, können Sie Ihre eigene Telemetrieclientfactory (`ITelemetryClientFactory`) angeben. Daraufhin verwendet der Host Ihre Klasse, um einen Telemetrieclient ([`TelemetryClient`]) zu konstruieren. Dieser Code überschreibt z. B. `DefaultTelemetryClientFactory`, um eine Eigenschaft von `ServerTelemetryChannel` zu ändern:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Das `SamplingPercentageEstimatorSettings` konfiguriert [adaptive Stichprobenerstellung](../azure-monitor/app/sampling.md). Dies bedeutet, dass Application Insights in bestimmten Szenarien mit hohem Volumen eine ausgewählte Teilmenge von Telemetriedaten an den Server sendet.

Nachdem Sie die Telemetrie-Factory erstellt haben, übergeben Sie sie an den Application Insights-Protokollierungsanbieter:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Nächste Schritte

In diesem Artikel wurden Codeausschnitte bereitgestellt, die zeigen, wie häufige Szenarien für das Arbeiten mit dem WebJobs SDK behandelt werden. Vollständige Beispiele finden Sie unter [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[ITelemetryInitializer]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[TelemetryConfiguration]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[JobHostConfiguration]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
