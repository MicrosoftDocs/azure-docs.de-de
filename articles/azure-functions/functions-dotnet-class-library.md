---
title: C#-Entwicklerreferenz zu Azure Functions
description: Erfahren Sie, wie Azure Functions mithilfe von C# entwickelt wird.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 07/24/2020
ms.openlocfilehash: dc08e378d68743ed7906f4dec7c8f31202959880
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608281"
---
# <a name="azure-functions-c-developer-reference"></a>C#-Entwicklerreferenz zu Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Dieser Artikel ist eine Einführung in die Entwicklung von Azure Functions durch Verwenden von C# in .NET-Klassenbibliotheken.

Für C#-Entwickler sind möglicherweise auch folgende Artikel interessant:

| Erste Schritte | Konzepte| Geführte Tutorials/Beispiele |
| -- | -- | -- | 
| <ul><li>[Verwenden von Visual Studio](functions-create-your-first-function-visual-studio.md)</li><li>[Verwendung von Visual Studio Code](create-first-function-vs-code-csharp.md)</li><li>[Verwenden von Befehlszeilentools](create-first-function-cli-csharp.md)</li></ul> | <ul><li>[Hostingoptionen](functions-scale.md)</li><li>[Überlegungen zur &nbsp;Leistung](functions-best-practices.md)</li><li>[Visual Studio-Entwicklung](functions-develop-vs.md)</li><li>[Dependency Injection](functions-dotnet-dependency-injection.md)</li></ul> | <ul><li>[Erstellen serverloser Anwendungen](/learn/paths/create-serverless-applications/)</li><li>[Beispiele für C#](/samples/browse/?products=azure-functions&languages=csharp)</li></ul> |

Azure Functions unterstützt die Programmiersprachen C# und C#-Skript. Wenn Sie nach Anleitungen zum [Verwenden von C# im Azure-Portal](functions-create-function-app-portal.md) suchen, lesen Sie [C#-Skriptentwicklerreferenz (C#-Skript, CSX) zu Azure Functions](functions-reference-csharp.md).

## <a name="supported-versions"></a>Unterstützte Versionen

Versionen der Functions-Laufzeit funktionieren mit bestimmten Versionen von .NET. Die folgende Tabelle zeigt die höchste Ebene von .NET Core und .NET Framework und .NET Core, die mit einer bestimmten Version von Functions in Ihrem Projekt verwendet werden kann. 

| Version der Functions-Laufzeit | Maximale .NET-Version |
| ---- | ---- |
| Functions 3.x | .NET Core 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4.7 |

Weitere Informationen finden Sie unter [Versionen der Azure Functions-Laufzeit: Übersicht](functions-versions.md).

## <a name="functions-class-library-project"></a>Funktionsklassenbibliotheks-Projekt

In Visual Studio wird mit der **Azure Functions**-Projektvorlage ein C#-Klassenbibliotheksprojekt erstellt, das die folgenden Dateien enthält:

* [host.json](functions-host-json.md): Speichert Konfigurationseinstellungen, die sich auf alle Funktionen im Projekt auswirken, wenn es lokal oder in Azure ausgeführt wird.
* [local.settings.json](functions-run-local.md#local-settings-file): Speichert App-Einstellungen und Verbindungszeichenfolgen, die verwendet werden, wenn das Projekt lokal ausgeführt wird. Diese Datei enthält Geheimnisse und wird nicht in Ihrer Funktions-App in Azure veröffentlicht. Gehen Sie stattdessen so vor, dass Sie [Ihrer Funktions-App App-Einstellungen hinzufügen](functions-develop-vs.md#function-app-settings).

Wenn Sie das Projekt erstellen, wird im Buildausgabeverzeichnis eine Ordnerstruktur generiert, die weitgehend so aussieht wie das folgende Beispiel:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Dieses Verzeichnis wird in Ihrer Funktions-App in Azure bereitgestellt. Die in [Version 2.x](functions-versions.md) der Functions-Runtime erforderlichen Bindungserweiterungen werden [dem Projekt als NuGet-Pakete hinzugefügt](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Im Buildprozess wird für jede Funktion eine Datei vom Typ *function.json* erstellt. Die Datei *function.json* ist nicht für die direkte Bearbeitung vorgesehen. Sie können weder die Bindungskonfiguration ändern noch die Funktion deaktivieren, indem Sie diese Datei bearbeiten. Informationen zum Deaktivieren einer Funktion finden Sie unter [Gewusst wie: Deaktivieren von Funktionen](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Methoden, die als Funktionen erkannt werden

In einer Klassenbibliothek ist eine Funktion eine statische Methode mit einem Funktionsnamen (`FunctionName`) und einem Trigger-Attribut, wie im folgenden Beispiel gezeigt:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Das Attribut `FunctionName` kennzeichnet die Methode als Funktionseinstiegspunkt. Der Name muss innerhalb eines Projekts eindeutig sein, mit einem Buchstaben beginnen und darf nur Buchstaben, Ziffern, `_` und `-` enthalten. Bis zu 127 Zeichen sind zulässig. Projektvorlagen erstellen oft eine Methode namens `Run`, aber der Name der Methode kann ein beliebiger gültiger C#-Methodennamen sein.

Das Trigger-Attribut gibt den Triggertyp an und bindet die Eingabedaten an einen Methodenparameter. Die Beispielfunktion wird durch eine Warteschlangennachricht ausgelöst, und die Warteschlangennachricht wird im `myQueueItem`-Parameter an die Methode übergeben.

## <a name="method-signature-parameters"></a>Methodensignaturparameter

Die Methodensignatur kann andere Parameter als den mit dem Triggerattribut verwendeten Parameter enthalten. Hier sind einige weitere Parameter, die Sie verwenden können:

* [Eingabe- und Ausgabebindungen](functions-triggers-bindings.md), die als solche gekennzeichnet werden, indem Sie sie mit Attributen versehen.  
* Ein `ILogger`- oder `TraceWriter`-Parameter ([nur Version 1.x](functions-versions.md#creating-1x-apps)) für die [Protokollierung](#logging).
* Ein `CancellationToken`-Parameter für [ordnungsgemäßes Herunterfahren](#cancellation-tokens).
* [Binden von Ausdrücken](./functions-bindings-expressions-patterns.md) Parameter zum Abrufen von Metadaten für Trigger.

Die Reihenfolge der Parameter in der Funktionssignatur spielt keine Rolle. Beispielsweise können Sie Triggerparameter für Bindungen voran- oder nachstellen und den Parameter „logger“ vor oder nach Trigger- oder Bindungsparametern anordnen.

### <a name="output-binding-example"></a>Beispiel für eine Ausgabebindung

Im folgenden Beispiel wird das vorhergehende geändert, indem eine Ausgabewarteschlangenbindung hinzugefügt wird. Die Funktion schreibt die Warteschlangennachricht, die die Funktion auslöst, in eine neue Warteschlangennachricht in einer anderen Warteschlange.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

In den Artikeln zu den Bindungsverweisen (z. B. [Speicherwarteschlangen](functions-bindings-storage-queue.md)) wird erläutert, welche Parametertypen Sie mit Trigger-, Eingabe- oder Ausgabebindungsattributen verwenden können.

### <a name="binding-expressions-example"></a>Beispiel für Bindungsausdrücke

Mit dem folgenden Code wird der Name der zu überwachenden Warteschlange für eine App-Einstellung abgerufen, und der Erstellungszeitpunkt der Warteschlangennachricht ist im Parameter `insertionTime` enthalten.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatisch generierte function.json-Datei

Im Buildprozess wird eine *function.json*-Datei in einem Funktionsordner im Ordner für Builds erstellt. Wie bereits erwähnt ist diese Datei nicht für die direkte Bearbeitung vorgesehen. Sie können weder die Bindungskonfiguration ändern noch die Funktion deaktivieren, indem Sie diese Datei bearbeiten. 

Der Zweck dieser Datei besteht darin, Informationen für den Skalierungscontroller bereitzustellen, die dieser für [Skalierungsentscheidungen hinsichtlich des Verbrauchsplans](functions-scale.md#how-the-consumption-and-premium-plans-work) verwenden kann. Aus diesem Grund hat die Datei nur Triggerinformationen und keine Eingabe- oder Ausgabebindungen.

Die generierte *function.json*-Datei enthält eine `configurationSource`-Eigenschaft, die der Runtime mitteilt, dass sie statt der *function.json*-Konfiguration die .NET Attribute für Bindungen verwenden soll. Hier sehen Sie ein Beispiel:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

Die Erstellung der *function.json*-Datei wird mit dem NuGet-Paket [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) ausgeführt. 

Dasselbe Paket wird für die Versionen 1.x und 2.x der Functions-Runtime verwendet. Ein 1.x-Projekt unterscheidet sich durch das Zielframework von einem 2.x-Projekt. Hier sind die relevanten Teile von *CSPROJ*-Dateien, welche verschiedene Zielframeworks und dasselbe `Sdk`-Paket zeigen:

# <a name="v2x"></a>[v2.x+](#tab/v2)

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```
---


Unter den `Sdk`-Paketabhängigkeiten befinden sich Auslöser und Bindungen. Ein 1.x-Projekt verweist auf 1.x-Auslöser und -Bindungen, da diese Auslöser und Bindungen für das .NET Framework ausgelegt sind, während 2.x-Auslöser und -Bindungen für .NET Core ausgelegt sind.

Das `Sdk`-Paket hängt außerdem von [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) und indirekt von [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage) ab. Diese Abhängigkeiten stellen sicher, dass Ihr Projekt die Versionen dieser Pakete verwendet, die mit der Functions-Runtime-Version funktionieren, auf die das Projekt ausgelegt ist. Beispiel: `Newtonsoft.Json` weist Version 11 für .NET Framework 4.6.1 auf, die Functions-Runtime, die auf .NET Framework 4.6.1 ausgelegt ist, ist jedoch nur mit `Newtonsoft.Json` 9.0.1 kompatibel. Daher muss Ihr Funktionscode in diesem Projekt auch `Newtonsoft.Json` 9.0.1 verwenden.

Der Quellcode für `Microsoft.NET.Sdk.Functions` ist im GitHub-Repository [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) verfügbar.

## <a name="runtime-version"></a>Laufzeitversion

Visual Studio verwendet [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) zum Ausführen von Functions-Projekten. Core Tools ist eine Befehlszeilenschnittstelle für die Functions-Runtime.

Wenn Sie Core Tools mithilfe von npm installieren, wirkt sich dies nicht auf die von Visual Studio verwendete Core Tools-Version aus. Für die Functions-Runtime-Version 1.x speichert Visual Studio Core Tools-Versionen in *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* und verwendet die aktuelle dort gespeicherte Version. Für Function 2.x ist Core Tools in der Erweiterung **Azure Functions und WebJobs Tools** enthalten. Sie können sowohl für 1.x als auch 2.x anzeigen, welche Version in der Konsolenausgabe verwendet wird, wenn Sie ein Functions-Projekt ausführen:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="readytorun"></a>ReadyToRun

Sie können ihre Funktions-App als [ReadyToRun-Binärdateien](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images) kompilieren. ReadyToRun ist eine Form der Vorabkompilierung, die zur Optimierung der Startleistung beitragen und die Auswirkungen eines [Kaltstarts](functions-scale.md#cold-start) bei Ausführung in einem [Verbrauchstarif](functions-scale.md#consumption-plan) reduzieren kann.

ReadyToRun ist in .NET 3.0 verfügbar und setzt [Azure Functions Runtimeversion 3.0](functions-versions.md) voraus.

Um Ihr Projekt als „ReadyToRun“ zu kompilieren, aktualisieren Sie die Projektdatei, indem Sie die Elemente `<PublishReadyToRun>` und `<RuntimeIdentifier>` hinzufügen. Im Folgenden finden Sie die Konfiguration für die Veröffentlichung in einer Windows 32-Bit-Funktions-App.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun unterstützt derzeit keine Kreuzkompilierung. Sie müssen Ihre App auf derselben Plattform erstellen, auf der sich auch das Bereitstellungsziel befindet. Achten Sie auch auf die „Bitanzahl“, die in ihrer Funktions-App konfiguriert wird. Wenn Ihre Funktions-App in Azure z. B. mit Windows 64-Bit konfiguriert wird, müssen Sie Ihre App unter Windows mit `win-x64` als [Runtimebezeichner](/dotnet/core/rid-catalog) kompilieren.

Sie können Ihre App mit ReadyToRun auch über die Befehlszeile erstellen. Weitere Informationen finden Sie unter der Option `-p:PublishReadyToRun=true` in [`dotnet publish`](/dotnet/core/tools/dotnet-publish).

## <a name="supported-types-for-bindings"></a>Unterstützte Typen für Bindungen

Jede Bindung hat ihre eigenen unterstützten Typen. Beispielsweise kann ein Blobtriggerattribut auf einen Zeichenfolgeparameter, einen POCO-Parameter, einen `CloudBlockBlob`-Parameter oder einen von mehreren anderen unterstützten Typen angewendet werden. Im [Bindungsreferenzartikel für Blobbindungen](functions-bindings-storage-blob-trigger.md#usage) sind alle unterstützten Parametertypen aufgelistet. Weitere Informationen hierzu finden Sie unter [Trigger und Bindungen](functions-triggers-bindings.md) und in den [Bindungsreferenzdokumenten für jeden Bindungstyp](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Binden an den Rückgabewert einer Methode

Sie können einen Rückgabewert einer Methode für eine Ausgabebindung nutzen, indem Sie das Attribut auf den Rückgabewert einer Methode anwenden. Beispiele finden Sie unter [Konzepte für Azure Functions-Trigger und -Bindungen](./functions-bindings-return-value.md). 

Verwenden Sie den Rückgabewert nur dann, wenn eine erfolgreiche Ausführung der Funktion immer einen Rückgabewert ergibt, der an die Ausgabebindung übergeben werden soll. Verwenden Sie andernfalls, wie im folgenden Abschnitt gezeigt, `ICollector` oder `IAsyncCollector`.

## <a name="writing-multiple-output-values"></a>Schreiben von mehreren Ausgabewerten

Verwenden Sie die Typen [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) oder [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) in folgenden Fällen: 1. um mehrere Werte in eine Ausgabebindung zu schreiben oder 2. wenn ein erfolgreicher Funktionsaufruf möglicherweise keinen übergebbaren Wert für die Ausgabebindung ergibt. Diese Typen stellen lesegeschützte Sammlungen dar, die nach Durchführung der Methode in die Ausgabebindung geschrieben werden.

In diesem Beispiel werden mehrere Warteschlangennachrichten mit `ICollector` in die gleiche Warteschlange geschrieben:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="async"></a>Async

Um eine Funktion [asynchron](/dotnet/csharp/programming-guide/concepts/async/) auszuführen, verwenden Sie das `async`-Schlüsselwort, und geben Sie ein `Task`-Objekt zurück.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Sie können keine `out`-Parameter in asynchronen Funktionen verwenden. Für Ausgabebindungen verwenden Sie stattdessen [Funktionsrückgabewert ](#binding-to-method-return-value) oder [Sammlerobjekt](#writing-multiple-output-values).

## <a name="cancellation-tokens"></a>Abbruchtoken

Eine Funktion kann einen [CancellationToken](/dotnet/api/system.threading.cancellationtoken)-Parameter annehmen, der es dem Betriebssystem ermöglicht, den Code vor dem Beenden der Funktion zu benachrichtigen. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die die Daten in einem inkonsistenten Zustand hinterlässt.

Das folgende Beispiel zeigt, wie Sie nach einer bevorstehenden Beendigung einer Funktion suchen.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="logging"></a>Protokollierung

In Ihrem Funktionscode können Sie die Ausgabe in Protokolle schreiben, die in Application Insights als Ablaufverfolgungen angezeigt werden. Die empfohlene Vorgehensweise zum Schreiben in die Protokolle besteht darin, einen Parameter vom Typ [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) hinzuzufügen, der in der Regel `log` heißt. In Version 1.x der Functions-Runtime wurde `TraceWriter` verwendet. Dadurch wurde ebenfalls in Application Insights geschrieben, aber es wurde keine strukturierte Protokollierung unterstützt. Verwenden Sie nicht `Console.Write` zum Schreiben der Protokolle, da diese Daten von Application Insights nicht aufgezeichnet werden. 

### <a name="ilogger"></a>ILogger

Fügen Sie in Ihrer Funktionsdefinition den Parameter [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) ein, der [strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) unterstützt.

Mit einem `ILogger`-Objekt rufen Sie `Log<level>`-[Erweiterungsmethoden in ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) auf, um Protokolle zu erstellen. Mit dem folgenden Code werden Protokolle vom Typ `Information` mit der Kategorie `Function.<YOUR_FUNCTION_NAME>.User.` geschrieben.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

Weitere Informationen zur Implementierung von `ILogger` in Functions finden Sie unter [Sammeln von Telemetriedaten](functions-monitoring.md#collecting-telemetry-data). Bei Kategorien mit dem Präfix `Function` wird angenommen, dass Sie eine `ILogger`-Instanz verwenden. Wenn Sie stattdessen eine `ILogger<T>`-Instanz verwenden, kann der Kategoriename stattdessen auf `T` basieren.  

### <a name="structured-logging"></a>Strukturierte Protokollierung

Die Reihenfolge der Platzhalter, nicht der Namen, bestimmt, welche Parameter in der Protokollnachricht verwendet werden. Angenommen, Sie verwenden den folgenden Code:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Wenn Sie die gleiche Nachrichtenzeichenfolge beibehalten und die Reihenfolge der Parameter umkehren, befinden sich die Werte im resultierenden Nachrichtentext an den falschen Stellen.

Platzhalter werden auf diese Weise verarbeitet, damit Sie die strukturierte Protokollierung durchführen können. Application Insights speichert die Name/Wert-Paare für Parameter und die Nachrichtenzeichenfolge. Das Ergebnis ist, dass die Nachrichtenargumente zu Feldern werden, anhand denen Sie Abfragen durchführen können.

Wenn Ihr Methodenaufruf für die Protokollierung wie im vorherigen Beispiel aussieht, können Sie das Feld `customDimensions.prop__rowKey` abfragen. Durch das Hinzufügen des Präfix `prop__` soll sichergestellt werden, dass es zwischen den Feldern, die von der Runtime hinzugefügt werden, und Feldern, die von Ihrem Funktionscode hinzugefügt werden, nicht zu Konflikten kommt.

Sie können auch die ursprüngliche Nachrichtenzeichenfolge abfragen, indem Sie auf das Feld `customDimensions.prop__{OriginalFormat}` verweisen.  

Hier ist eine JSON-Beispieldarstellung von `customDimensions`-Daten angegeben:

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

## <a name="log-custom-telemetry-in-c-functions"></a>Protokollieren von benutzerdefinierter Telemetrie in C#-Funktionen

Es gibt eine Functions-spezifische Version des Application Insights SDK, mit der Sie benutzerdefinierte Telemetriedaten von ihren Funktionen an Application Insights senden können: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Verwenden Sie den folgenden Befehl in der Eingabeaufforderung, um das folgende Paket zu installieren:

# <a name="command"></a>[Befehl](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

Ersetzen `<VERSION>` in diesem Befehl durch eine Version dieses Pakets, die Ihre installierte Version von [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/) unterstützt. 

Im folgenden C#-Beispiel wird die [benutzerdefinierte Telemetrie-API](../azure-monitor/app/api-custom-events-metrics.md) verwendet. Das Beispiel gilt für eine .NET-Klassenbibliothek, aber der Application Insights-Code ist für C#-Skript identisch.

# <a name="v2x"></a>[v2.x+](#tab/v2)

Die Runtime ab Version 2.x verwendet neuere Features in Application Insights, um die Telemetrie automatisch mit dem aktuellen Vorgang zu korrelieren. Es ist nicht erforderlich, für den Vorgang die Felder `Id`, `ParentId` oder `Name` festzulegen.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Write an event to the customEvents table.
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Generate a custom metric, in this case let's use ContentLength.
            this.telemetryClient.GetMetric("contentLength").TrackValue(req.ContentLength);

            // Log a custom dependency in the dependencies table.
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

In diesem Beispiel werden die benutzerdefinierten Metrikdaten vom Host aggregiert, bevor sie an die Tabelle „customMetrics“ gesendet werden. Weitere Informationen finden Sie in der Dokumentation zu [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) in Application Insights. 

Bei der lokalen Ausführung müssen Sie die Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY` mit dem Application Insights-Schlüssel zur Datei [local.settings.json](functions-run-local.md#local-settings-file) hinzufügen.


# <a name="v1x"></a>[v1.x](#tab/v1)

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```
---

Vermeiden Sie es, `TrackRequest` oder `StartOperation<RequestTelemetry>` aufzurufen, da in diesem Fall für einen Funktionsaufruf doppelte Anforderungen angezeigt werden.  Mit der Functions-Laufzeit werden Anforderungen automatisch nachverfolgt.

Legen Sie nicht `telemetryClient.Context.Operation.Id` fest. Diese globale Einstellung führt zu falschen Korrelationen, wenn viele Funktionen gleichzeitig ausgeführt werden. Erstellen Sie stattdessen eine neue Telemetrieinstanz (`DependencyTelemetry`, `EventTelemetry`), und ändern Sie die `Context`-Eigenschaft. Übergeben Sie in der Telemetrie-Instanz dann die entsprechende `Track`-Methode `TelemetryClient` (`TrackDependency()`, `TrackEvent()`, `TrackMetric()`). Durch diese Methode wird sichergestellt, dass die Telemetrie die richtigen Korrelationsdetails für den aktuellen Funktionsaufruf enthält.


## <a name="environment-variables"></a>Umgebungsvariablen

Verwenden Sie `System.Environment.GetEnvironmentVariable`zum Abrufen einer Umgebungsvariablen oder zum Abrufen des Werts einer App-Einstellung, wie im folgenden Codebeispiel zu sehen:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

App-Einstellungen können sowohl beim lokalen Entwickeln als auch bei der Ausführung unter Azure aus Umgebungsvariablen gelesen werden. Beim lokalen Entwickeln kommen App-Einstellungen aus der `Values`-Sammlung in der Datei *local.settings.json*. In beiden Umgebungen, lokal und Azure, ruft `GetEnvironmentVariable("<app setting name>")` den Wert der benannten App-Einstellung ab. Bei der lokalen Ausführung würde beispielsweise „My Site Name“ zurückgegeben, wenn Ihre *local.settings.json*-Datei `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` enthält.

Die Eigenschaft [System.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) ist eine alternative API zum Abrufen von Werten einer App-Einstellung, jedoch wird die hier gezeigte Verwendung von `GetEnvironmentVariable` empfohlen.

## <a name="binding-at-runtime"></a>Binden zur Laufzeit

In C# und anderen .NET-Sprachen können Sie ein [imperatives](https://en.wikipedia.org/wiki/Imperative_programming) Bindungsmuster verwenden, im Gegensatz zu den [*deklarativen*](https://en.wikipedia.org/wiki/Declarative_programming) Bindungen in Attributen. Imperative Bindung eignet sich, wenn Bindungsparameter zur Laufzeit statt zur Entwurfszeit berechnet werden müssen. Mit diesem Muster ist die Bindung an unterstützte Eingabe- und Ausgabebindungen direkt im Funktionscode möglich.

Definieren Sie eine imperative Bindung wie folgt:

- Schließen Sie für die gewünschten imperativen Bindungen **kein** Attribut in die Funktionssignatur ein.
- Übergeben Sie den Eingabeparameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) oder [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Verwenden Sie das folgende C#-Muster, um die Datenbindung auszuführen.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` ist das .NET-Attribut, das die Bindung definiert, und `T` ist ein Eingabe- oder Ausgabetyp, der von diesem Bindungstyp unterstützt wird. `T` darf kein `out`-Parametertyp sein (wie etwa `out JObject`). Die ausgehende Bindung für die Tabelle „Mobile Apps“ unterstützt z. B. [sechs Ausgabetypen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), Sie können aber nur [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) oder [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) mit imperativer Bindung verwenden.

### <a name="single-attribute-example"></a>Beispiel mit einem einzigen Attribut

Mit dem folgenden Beispielcode wird eine [ausgehende Speicherblob-Bindung](functions-bindings-storage-blob-output.md) mit einem Blobpfad erstellt, der zur Laufzeit definiert wird. Dann wird eine Zeichenfolge in das Blob geschrieben.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiert die Eingabe- oder Ausgabebindung für den [Speicherblob](functions-bindings-storage-blob.md), und [TextWriter](/dotnet/api/system.io.textwriter) ist ein unterstützter Ausgabenbindungstyp.

### <a name="multiple-attribute-example"></a>Beispiel mit mehreren Attributen

Im vorherigen Beispiel wird die App-Einstellung für die Verbindungszeichenfolge (`AzureWebJobsStorage`) des Hauptspeicherkontos der Funktions-App abgerufen. Sie können eine benutzerdefinierte App-Einstellung angeben, die für das Storage-Konto verwendet werden soll, indem Sie [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) hinzufügen und das Attributarray an `BindAsync<T>()` übergeben. Verwenden Sie einen `Binder`-Parameter, nicht `IBinder`.  Beispiel:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Trigger und Bindungen 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Triggern und Bindungen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Weitere Informationen zu bewährten Methoden für Azure Functions](functions-best-practices.md)
