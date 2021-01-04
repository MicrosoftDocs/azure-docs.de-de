---
title: Ausführen von Durable Functions als WebJobs – Azure
description: Erfahren Sie, wie Sie Durable Functions so codieren und konfigurieren, dass diese in WebJobs mit dem WebJobs SDK ausgeführt werden.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009499"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Ausführen von Durable Functions als WebJobs

Standardmäßig verwendet Durable Functions die Azure Functions-Runtime zum Hosten von Orchestrierungen. Allerdings gibt es möglicherweise Szenarios, in denen Sie den Code, der auf Ereignisse lauscht, stärker steuern müssen. In diesem Artikel erfahren Sie, wie Sie Ihre Orchestrierung mithilfe des WebJobs SDK implementieren. Einen ausführlicheren Vergleich zwischen Functions und WebJobs finden Sie unter [Vergleichen von Functions und WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) und die Erweiterung [Durable Functions](durable-functions-overview.md) bauen auf dem [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) auf. Der Auftragshost im WebJobs SDK ist die Runtime in Azure Functions. Wenn Sie das Verhalten auf eine Weise steuern müssen, die in Azure Functions nicht möglich ist, können Sie Durable Functions entwickeln und ausführen, indem Sie das WebJobs SDK selbst verwenden.

In Version 3.x des WebJobs SDK ist der Host eine Implementierung von `IHost`, und in Version 2.x verwenden Sie das Objekt `JobHost`.

Das Beispiel für die Verkettung von Durable Functions ist in einer WebJobs SDK 2.x-Version verfügbar: Laden Sie das [Durable Functions-Repository](https://github.com/azure/azure-functions-durable-extension/) herunter. Führen Sie einen Check-Out für den Branch *v1* durch, und navigieren Sie zum Ordner *samples\\webjobssdk\\chaining*.

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie mit den Grundlagen des WebJobs SDK, der Entwicklung von C#-Klassenbibliotheken für Azure Functions und Durable Functions vertraut sind. Wenn Sie eine Einführung in diese Themen benötigen, lesen Sie die folgenden Artikel:

* [Erste Schritte mit dem WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Erstellen Ihrer ersten Funktion mit Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Zur Durchführung der in diesem Artikel aufgeführten Schritte ist Folgendes erforderlich:

* [Installieren Sie Visual Studio 2019](/visualstudio/install/) mit der Workload **Azure-Entwicklung**.

  Wenn Sie bereits über Visual Studio, jedoch nicht über diese Workload verfügen, müssen Sie die Workload hinzufügen, indem Sie **Tools** > **Tools und Features abrufen** auswählen.

  (Sie können stattdessen [Visual Studio Code](https://code.visualstudio.com/) verwenden, aber einige der Anweisungen sind spezifisch für Visual Studio.)

* Installieren Sie den [Azure-Speicheremulator](../../storage/common/storage-use-emulator.md) Version 5.2 oder höher, und führen Sie ihn aus. Alternativ können Sie die Datei *App.config* mit einer Azure Storage-Verbindungszeichenfolge aktualisieren.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-Versionen

In diesem Artikel wird erläutert, wie man ein WebJobs SDK 2.x-Projekt (entspricht Azure Functions Version 1.x) entwickelt. Informationen zu Version 3.x finden Sie weiter unten in diesem Artikel unter [WebJobs SDK 3.x](#webjobs-sdk-3x).

## <a name="create-a-console-app"></a>Erstellen einer Konsolen-App

Sie müssen zuerst eine Konsolen-App erstellen, um Durable Functions als WebJobs ausführen zu können. Ein WebJobs SDK-Projekt ist lediglich ein Konsolen-App-Projekt, für das die entsprechenden NuGet-Pakete installiert sind.

Wählen Sie im Visual Studio-Dialogfeld **Neues Projekt** die Option **Klassischer Windows-Desktop** > **Konsolen-App (.NET Framework)** aus. In der Projektdatei sollte die `TargetFrameworkVersion``v4.6.1` sein.

Visual Studio bietet auch eine WebJob-Projektvorlage, die Sie verwenden können, indem Sie **Cloud** > **Azure WebJob (.NET Framework)** auswählen. Diese Vorlage installiert viele Pakete, von denen einige möglicherweise nicht benötigt werden.

## <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

Sie benötigen NuGet-Pakete für das WebJobs SDK, Hauptbindungen, das Protokollierungsframework und die Durable Task-Erweiterung. Hier sind Befehle der **Paket-Manager-Konsole** für diese Pakete mit den neuesten stabilen Versionsnummern zum Zeitpunkt der Erstellung dieses Artikels:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Darüber hinaus benötigen Sie Protokollanbieter. Die folgenden Befehle installieren den Azure Application Insights-Anbieter und den `ConfigurationManager`. Mit dem `ConfigurationManager` können Sie den Application Insights-Instrumentierungsschlüssel aus den App-Einstellungen abrufen.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Über die folgenden Befehle wird der Konsolenanbieter installiert:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost-Code

Wenn die Konsolen-App erstellt und die erforderlichen NuGet-Pakete installiert sind, können Sie Durable Functions verwenden. Dazu verwenden Sie JobHost-Code.

Um die Erweiterung Durable Functions zu verwenden, rufen Sie `UseDurableTask` für das `JobHostConfiguration`-Objekt in Ihrer `Main`-Methode auf:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Eine Liste der Eigenschaften, die Sie im `DurableTaskExtension`-Objekt festlegen können, finden Sie unter [host.json](../functions-host-json.md#durabletask).

Über die `Main`-Methode richten Sie auch den Protokollanbieter ein. In dem folgenden Beispiel werden die Konsole und Application Insights-Anbieter konfiguriert.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Durable Functions im Kontext von WebJobs unterscheidet sich etwas von Durable Functions im Kontext von Azure Functions. Wenn Sie Ihren Code schreiben, ist es wichtig, diese Unterschiede zu berücksichtigen.

Die folgenden Azure Functions werden vom WebJobs SDK nicht unterstützt:

* [FunctionName-Attribut](#functionname-attribute)
* [HTTP-Trigger](#http-trigger)
* [Durable Functions-HTTP-Verwaltungs-API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName-Attribut

In einem WebJobs-SDK-Projekt ist der Methodenname einer Funktion der Funktionsname. Das Attribut `FunctionName` wird nur in Azure Functions verwendet.

### <a name="http-trigger"></a>HTTP-Trigger

Das WebJobs SDK verfügt über keinen HTTP-Trigger. Der Orchestrierungsclient des Beispielprojekts verwendet einen Zeitgebertrigger:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP-Verwaltungs-API

Da für das WebJobs SDK kein HTTP-Trigger vorhanden ist, verfügt es auch über keine [HTTP-Verwaltungs-API](durable-functions-http-api.md).

In einem WebJobs SDK-Projekt können Sie Methoden für das Orchestrierungsclientobjekt aufrufen, anstatt HTTP-Anforderungen zu senden. Die folgenden Methoden entsprechen den drei Aufgaben, die Sie mit der HTTP-Verwaltungs-API ausführen können:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Die Orchestrationsclientfunktion im Beispielprojekt startet die Orchestratorfunktion und wird dann in einer Schleife ausgeführt, die `GetStatusAsync` alle 2 Sekunden aufruft:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Ausführen des Beispiels

Sie haben Durable Functions für die Ausführung als WebJob-Dienst eingerichtet, und Sie verstehen, wie sich diese Ausführung von der Ausführung als eigenständiger Azure Functions-Dienst unterscheidet. An diesem Punkt kann es hilfreich sein, die Funktion in einem Beispiel zu sehen.

Dieser Abschnitt bietet eine Übersicht über die Ausführung des [Beispielprojekts](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining). Ausführliche Anweisungen, wie Sie ein WebJobs SDK-Projekt lokal ausführen und in einem Azure WebJob bereitstellen, finden Sie unter [Erste Schritte mit dem WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Lokales Ausführen

1. Stellen Sie sicher, dass der Speicheremulator ausgeführt wird (siehe [Voraussetzungen](#prerequisites)).

1. Wenn Sie beim lokalen Ausführen des Projekts Protokolle in Application Insights anzeigen möchten:

    a. Erstellen Sie eine Application Insights-Ressource, und verwenden Sie für die Ressource den App-Typ **Allgemein**.

    b. Speichern Sie den Instrumentierungsschlüssel in der Datei *App.config*.

1. Führen Sie das Projekt aus.

### <a name="run-in-azure"></a>Ausführen in Azure

1. Erstellen Sie eine Web-App und eine Speicherkonto.

1. Speichern Sie in der Web-App die Verbindungszeichenfolge des Speichers in einer App-Einstellung namens `AzureWebJobsStorage`.

1. Erstellen Sie eine Application Insights-Ressource, und verwenden Sie für die Ressource den App-Typ **Allgemein**.

1. Speichern Sie den Instrumentierungsschlüssel in einer App-Einstellung namens `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Führen Sie die Bereitstellung als WebJob aus.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

In diesem Artikel wird erläutert, wie man ein WebJobs SDK 2.x-Projekt entwickelt. Wenn Sie ein [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md)-Projekt entwickeln, hilft Ihnen dieser Abschnitt dabei, die Unterschiede zu verstehen.

Die wichtigste eingeführte Änderung ist die Verwendung von .NET Core anstelle von .NET Framework. Zum Erstellen eines WebJobs SDK 3.x-Projekts sind die Anweisungen identisch, mit diesen Ausnahmen:

1. Erstellen Sie eine .NET Core-Konsolen-App. Wählen Sie im Visual Studio-Dialogfeld **Neues Projekt** die Option **.NET Core** > **Konsolen-App (.NET Core)** aus. Die Projektdatei gibt an, dass `TargetFramework``netcoreapp2.x` ist.

1. Wählen Sie die Releaseversion WebJobs SDK 3.x der folgenden Pakete:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Legen Sie die Speicherverbindungszeichenfolge und den Application Insights-Instrumentierungsschlüssel in einer *appsettings.json*-Datei mithilfe des .NET Core-Konfigurationsframeworks fest. Hier sehen Sie ein Beispiel:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Ändern Sie den `Main`-Methodencode, um dies zu tun. Hier sehen Sie ein Beispiel:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu dem WebJobs SDK finden Sie unter [How to use the WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) (Gewusst wie: Verwenden des WebJobs SDK).
