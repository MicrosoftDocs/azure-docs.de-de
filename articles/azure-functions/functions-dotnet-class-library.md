---
title: C#-Entwicklerreferenz zu Azure Functions
description: Erfahren Sie, wie Azure Functions mithilfe von C# entwickelt wird.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, Ereignisverarbeitung, Webhooks, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: 2a6d670ba9f2f496cc94d2790eb6f66d46305746
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872801"
---
# <a name="azure-functions-c-developer-reference"></a>C#-Entwicklerreferenz zu Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Dieser Artikel ist eine Einführung in die Entwicklung von Azure Functions durch Verwenden von C# in .NET-Klassenbibliotheken.

Azure Functions unterstützt die Programmiersprachen C# und C#-Skript. Wenn Sie nach Anleitungen zum [Verwenden von C# im Azure-Portal](functions-create-function-app-portal.md) suchen, lesen Sie [C#-Skriptentwicklerreferenz (C#-Skript, CSX) zu Azure Functions](functions-reference-csharp.md).

In diesem Artikel wird davon ausgegangen, dass Sie die folgenden Artikel bereits gelesen haben:

* [Azure Functions: Entwicklerhandbuch](functions-reference.md)
* [Azure Functions Visual Studio 2019 Tools](functions-develop-vs.md)

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

Dieses Verzeichnis wird in Ihrer Funktions-App in Azure bereitgestellt. Die in [Version 2.x](functions-versions.md) der Functions-Runtime erforderlichen Bindungserweiterungen werden [dem Projekt als NuGet-Pakete hinzugefügt](./functions-bindings-register.md#c-class-library-with-visual-studio-2019).

> [!IMPORTANT]
> Im Buildprozess wird für jede Funktion eine Datei vom Typ *function.json* erstellt. Die Datei *function.json* ist nicht für die direkte Bearbeitung vorgesehen. Sie können weder die Bindungskonfiguration ändern noch die Funktion deaktivieren, indem Sie diese Datei bearbeiten. Informationen zum Deaktivieren einer Funktion finden Sie unter [Gewusst wie: Deaktivieren von Funktionen](disable-function.md#functions-2x---c-class-libraries).

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

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Unter den `Sdk`-Paketabhängigkeiten befinden sich Auslöser und Bindungen. Ein 1.x-Projekt verweist auf 1.x-Auslöser und -Bindungen, da diese Auslöser und Bindungen für das .NET Framework ausgelegt sind, während 2.x-Auslöser und -Bindungen für .NET Core ausgelegt sind.

Das `Sdk`-Paket hängt außerdem von [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) und indirekt von [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage) ab. Diese Abhängigkeiten stellen sicher, dass Ihr Projekt die Versionen dieser Pakete verwendet, die mit der Functions-Runtime-Version funktionieren, auf die das Projekt ausgelegt ist. Beispiel: `Newtonsoft.Json` weist Version 11 für .NET Framework 4.6.1 auf, die Functions-Runtime, die auf .NET Framework 4.6.1 ausgelegt ist, ist jedoch nur mit `Newtonsoft.Json` 9.0.1 kompatibel. Daher muss Ihr Funktionscode in diesem Projekt auch `Newtonsoft.Json` 9.0.1 verwenden.

Der Quellcode für `Microsoft.NET.Sdk.Functions` ist im GitHub-Repository [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) verfügbar.

## <a name="runtime-version"></a>Laufzeitversion

Visual Studio verwendet [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) zum Ausführen von Functions-Projekten. Core Tools ist eine Befehlszeilenschnittstelle für die Functions-Runtime.

Wenn Sie Core Tools mithilfe von npm installieren, wirkt sich dies nicht auf die von Visual Studio verwendete Core Tools-Version aus. Für die Functions-Runtime-Version 1.x speichert Visual Studio Core Tools-Versionen in *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* und verwendet die aktuelle dort gespeicherte Version. Für Function 2.x ist Core Tools in der Erweiterung **Azure Functions und WebJobs Tools** enthalten. Sie können sowohl für 1.x als auch 2.x anzeigen, welche Version in der Konsolenausgabe verwendet wird, wenn Sie ein Functions-Projekt ausführen:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Unterstützte Typen für Bindungen

Jede Bindung hat ihre eigenen unterstützten Typen. Beispielsweise kann ein Blobtriggerattribut auf einen Zeichenfolgeparameter, einen POCO-Parameter, einen `CloudBlockBlob`-Parameter oder einen von mehreren anderen unterstützten Typen angewendet werden. Im [Bindungsreferenzartikel für Blobbindungen](functions-bindings-storage-blob.md#trigger---usage) sind alle unterstützten Parametertypen aufgelistet. Weitere Informationen hierzu finden Sie unter [Trigger und Bindungen](functions-triggers-bindings.md) und in den [Bindungsreferenzdokumenten für jeden Bindungstyp](functions-triggers-bindings.md#next-steps).

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

## <a name="logging"></a>Protokollierung

Um eine Ausgabe in C# in Ihren Streamingprotokollen zu dokumentieren, fügen Sie ein Argument vom Typ [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) ein. Es wird empfohlen, diesen wie im folgenden Beispiel `log` zu nennen:  

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

Vermeiden Sie die Verwendung von `Console.Write` in Azure Functions. Weitere Informationen finden Sie unter [Schreiben von Protokollen in C#-Funktionen](functions-monitoring.md#write-logs-in-c-functions) im Artikel **Überwachen von Azure Functions**.

## <a name="async"></a>Async

Um eine Funktion [asynchron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/) auszuführen, verwenden Sie das `async`-Schlüsselwort, und geben Sie ein `Task`-Objekt zurück.

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

Die Eigenschaft [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) ist eine alternative API zum Abrufen von Werten einer App-Einstellung, jedoch wird die hier gezeigte Verwendung von `GetEnvironmentVariable` empfohlen.

## <a name="binding-at-runtime"></a>Binden zur Laufzeit

In C# und anderen .NET-Sprachen können Sie ein [imperatives](https://en.wikipedia.org/wiki/Imperative_programming) Bindungsmuster verwenden, im Gegensatz zu den [*deklarativen* ](https://en.wikipedia.org/wiki/Declarative_programming) Bindungen in Attributen. Imperative Bindung eignet sich, wenn Bindungsparameter zur Laufzeit statt zur Entwurfszeit berechnet werden müssen. Mit diesem Muster ist die Bindung an unterstützte Eingabe- und Ausgabebindungen direkt im Funktionscode möglich.

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

  `BindingTypeAttribute` ist das .NET-Attribut, das die Bindung definiert, und `T` ist ein Eingabe- oder Ausgabetyp, der von diesem Bindungstyp unterstützt wird. `T` darf kein `out`-Parametertyp sein (wie etwa `out JObject`). Die ausgehende Bindung für die Tabelle „Mobile Apps“ unterstützt z. B. [sechs Ausgabetypen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), Sie können aber nur [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) oder [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) mit imperativer Bindung verwenden.

### <a name="single-attribute-example"></a>Beispiel mit einem einzigen Attribut

Mit dem folgenden Beispielcode wird eine [ausgehende Speicherblob-Bindung](functions-bindings-storage-blob.md#output) mit einem Blobpfad erstellt, der zur Laufzeit definiert wird. Dann wird eine Zeichenfolge in das Blob geschrieben.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiert die Eingabe- oder Ausgabebindung für den [Speicherblob](functions-bindings-storage-blob.md), und [TextWriter](/dotnet/api/system.io.textwriter) ist ein unterstützter Ausgabenbindungstyp.

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
