---
title: C#-Skriptentwicklerreferenz zu Azure Functions
description: Erfahren Sie, wie Azure Functions mithilfe von C#-Skript entwickelt wird.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, Ereignisverarbeitung, Webhooks, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 44a9368f82e95641d3df893ba0958c6bf8cf696f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724962"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>C#-Skriptentwicklerreferenz (C#-Skript, CSX) zu Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Dieser Artikel ist eine Einführung in die Entwicklung von Azure Functions mithilfe von C#-Skript (*CSX*).

Azure Functions unterstützt die Programmiersprachen C# und C#-Skript. Wenn Sie Anleitungen zum [Verwenden von C# in einem Visual Studio-Klassenbibliotheksprojekt](functions-develop-vs.md) suchen, sollten Sie zur [C#-Entwicklerreferenz](functions-dotnet-class-library.md) wechseln.

In diesem Artikel wird davon ausgegangen, dass Sie das [Azure Functions: Entwicklerhandbuch](functions-reference.md) bereits gelesen haben.

## <a name="how-csx-works"></a>Funktionsweise von CSX

Die C#-Skriptoberfläche für Azure Functions basiert auf dem [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Daten fließen über Methodenargumente in Ihre C#-Funktion ein. Argumentnamen werden in einer `function.json`-Datei angegeben, und es gibt vordefinierte Namen für den Zugriff auf Elemente wie die Funktionsprotokollierung und Abbruchtoken.

Dank des *CSX*-Formats müssen Sie weniger Textbausteine schreiben und können sich ganz auf das Schreiben einer C#-Funktion konzentrieren. Anstatt sämtliche Informationen in einem Namespace und einer Klasse zu umschließen, definieren Sie einfach eine `Run`-Methode. Schließen Sie wie gewohnt alle Assemblyverweise und Namespaces am Anfang der Datei ein.

Die *CSX*-Dateien einer Funktions-App werden kompiliert, wenn eine Instanz initialisiert wird. Dieser Kompilierungsschritt bedeutet, dass bestimmte Dinge, etwa ein Kaltstart, für C#-Skriptfunktionen im Vergleich zu C#-Klassenbibliotheken länger dauern. Dieser Kompilierungsschritt ist auch der Grund, warum C#-Skriptfunktionen im Azure-Portal bearbeitet werden können, während dies für C#-Klassenbibliotheken nicht möglich ist.

## <a name="folder-structure"></a>Ordnerstruktur

Die Ordnerstruktur für ein C#-Skriptprojekt sieht wie folgt aus:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Sie können die freigegebene Datei [host.json](functions-host-json.md) zum Konfigurieren der Funktions-App verwenden. Jede Funktion verfügt über eine eigene Codedatei (CSX-Datei) sowie über eine eigene Bindungskonfigurationsdatei (function.json).

Die in [Version 2.x](functions-versions.md) der Functions-Laufzeit erforderlichen Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, die eigentlichen Bibliotheksdateien befinden sich im Ordner `bin`. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Wenn Sie Funktionen im Azure-Portal entwickeln, wird diese Registrierung für Sie ausgeführt.

## <a name="binding-to-arguments"></a>Binden an Argumente

Eingabe- oder Ausgabedaten werden über die `name`-Eigenschaft in der *function.json*-Konfigurationsdatei an dienen C#-Skriptfunktionsparameter gebunden. Im folgenden Beispiel werden eine *function.json*-Datei und eine *run.csx*-Datei für eine über die Warteschlange ausgelöste Funktion veranschaulicht. Der Parameter, der die Daten aus der Warteschlangennachricht empfängt, hat den Namen `myQueueItem`, weil dies der Wert der `name`-Eigenschaft ist.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

Die `#r`-Anweisung ist[ weiter unten in diesem Artikel](#referencing-external-assemblies) erläutert.

## <a name="supported-types-for-bindings"></a>Unterstützte Typen für Bindungen

Jede Bindung hat ihre eigenen unterstützten Typen. Beispielsweise kann ein Blobtrigger mit einem Zeichenfolgeparameter, einem POCO-Parameter, einem `CloudBlockBlob`-Parameter oder einem von mehreren anderen unterstützten Typen verwendet werden. Im [Bindungsreferenzartikel für Blobbindungen](functions-bindings-storage-blob.md#trigger---usage) sind alle unterstützten Parametertypen für Blobtrigger aufgelistet. Weitere Informationen hierzu finden Sie unter [Trigger und Bindungen](functions-triggers-bindings.md) und in den [Bindungsreferenzdokumenten für jeden Bindungstyp](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Verweisen auf benutzerdefinierte Klassen

Wenn Sie eine benutzerdefinierte POCO-Klasse (Plain Old CLR Object) verwenden müssen, können Sie die Klassendefinition in dieselbe Datei einfügen oder in eine separate Datei setzen.

Im folgenden Beispiel wird ein *run.csx*-Beispiel veranschaulicht, das eine POCO-Klassendefinition enthält.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Bei einer POCO-Klasse müssen für jede Eigenschaft ein Getter und ein Setter definiert sein.

## <a name="reusing-csx-code"></a>Wiederverwenden von CSX-Code

Sie können in der Datei *run.csx* Klassen und Methoden verwenden, die in anderen *CSX* -Dateien definiert sind. Verwenden Sie zu diesem Zweck `#load`-Anweisungen in der Datei *run.csx*. Im folgenden Beispiel wird die Protokollierungsroutine `MyLogger` in *myLogger.csx* freigegeben und mit der `#load`-Anweisung in *run.csx* geladen:

Beispiel für *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Beispiel für *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Die Verwendung einer freigegebenen *CSX*-Datei ist ein häufiges Verfahren, wenn die Daten, die über ein POCO-Objekt zwischen Funktionen übertragen werden, stark typisiert werden sollen. Im folgenden vereinfachten Beispiel verwenden ein HTTP-Trigger und ein Warteschlangentrigger gemeinsam das POCO-Objekt `Order`, um die Bestelldaten stark zu typisieren:

Beispiel: *run.csx* für HTTP-Trigger:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Beispiel: *run.csx* für Warteschlangentrigger:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Beispiel: *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Mit der `#load` -Direktive können Sie einen relativen Pfad verwenden:

* `#load "mylogger.csx"` : Lädt eine Datei, die sich im Funktionsordner befindet.
* `#load "loadedfiles\mylogger.csx"` : Lädt eine Datei, die sich in einem Ordner im Funktionsordner befindet.
* `#load "..\shared\mylogger.csx"` : Lädt eine Datei, die sich in einem Ordner auf der gleichen Ebene befindet wie der Funktionsordner (also direkt unter *wwwroot*).

Die `#load`-Direktive kann nur mit *CSX*-Dateien verwendet werden, nicht mit *CS*-Dateien.

## <a name="binding-to-method-return-value"></a>Binden an den Rückgabewert einer Methode

Sie können für eine Ausgabebindung den Rückgabewert einer Methode verwenden, indem Sie den Namen `$return` in der Datei *function.json* verwenden. Beispiele finden Sie unter [Konzepte für Azure Functions-Trigger und -Bindungen](./functions-bindings-return-value.md).

Verwenden Sie den Rückgabewert nur dann, wenn eine erfolgreiche Ausführung der Funktion immer einen Rückgabewert ergibt, der an die Ausgabebindung übergeben werden soll. Verwenden Sie andernfalls, wie im folgenden Abschnitt gezeigt, `ICollector` oder `IAsyncCollector`.

## <a name="writing-multiple-output-values"></a>Schreiben von mehreren Ausgabewerten

Verwenden Sie die Typen [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) oder [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) in folgenden Fällen: 1. um mehrere Werte in eine Ausgabebindung zu schreiben oder 2. wenn ein erfolgreicher Funktionsaufruf möglicherweise keinen übergebbaren Wert für die Ausgabebindung ergibt. Diese Typen stellen lesegeschützte Sammlungen dar, die nach Durchführung der Methode in die Ausgabebindung geschrieben werden.

In diesem Beispiel werden mehrere Warteschlangennachrichten mit `ICollector` in die gleiche Warteschlange geschrieben:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Protokollierung

Um eine Ausgabe in C# in Ihren Streamingprotokollen zu dokumentieren, fügen Sie ein Argument vom Typ [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) ein. Verwenden Sie hierzu am besten den Namen `log`. Vermeiden Sie die Verwendung von `Console.Write` in Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Informationen zu einem neueren Protokollierungsframework, das Sie anstelle von `TraceWriter` verwenden können, finden Sie unter [Schreiben von Protokollen in C#-Funktionen](functions-monitoring.md#write-logs-in-c-functions) im Artikel **Überwachen von Azure Functions**.

## <a name="async"></a>Async

Um eine Funktion [asynchron](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/) auszuführen, verwenden Sie das `async`-Schlüsselwort, und geben Sie ein `Task`-Objekt zurück.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Sie können keine `out`-Parameter in asynchronen Funktionen verwenden. Für Ausgabebindungen verwenden Sie stattdessen [Funktionsrückgabewert ](#binding-to-method-return-value) oder [Sammlerobjekt](#writing-multiple-output-values).

## <a name="cancellation-tokens"></a>Abbruchtoken

Eine Funktion kann einen [CancellationToken](/dotnet/api/system.threading.cancellationtoken)-Parameter annehmen, der es dem Betriebssystem ermöglicht, den Code vor dem Beenden der Funktion zu benachrichtigen. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die die Daten in einem inkonsistenten Zustand hinterlässt.

Das folgende Beispiel zeigt, wie Sie nach einer bevorstehenden Beendigung einer Funktion suchen.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
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
```

## <a name="importing-namespaces"></a>Importieren von Namespaces

Wenn Sie Namespaces importieren müssen, ist dies wie üblich über die `using` -Klausel möglich.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Die folgenden Namespaces werden automatisch importiert und sind daher optional:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Verweise auf externe Assemblys

Bei Frameworkassemblys können Sie Verweise über die `#r "AssemblyName"` -Anweisung hinzufügen.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Die folgenden Assemblys werden automatisch von der Azure Functions-Hostumgebung hinzugefügt:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Von einem einfachen Namen kann auf folgende Assemblys verwiesen (z.B. `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Verweisen von benutzerdefinierten Assemblys

Um eine benutzerdefinierte Assembly zu verweisen, können Sie entweder eine *freigegebene* Assembly oder eine *private* Assembly verwenden:

* Freigegebene Assemblys werden für alle Funktionen innerhalb einer Funktionen-App freigegeben. Um auf eine benutzerdefinierte Assembly zu verweisen, laden Sie die Assembly in einen Ordner namens `bin` im [Stammverzeichnis der Funktions-App](functions-reference.md#folder-structure) (wwwroot) hoch.

* Private Assemblys sind Bestandteil eines Kontexts einer bestimmten Funktion und unterstützen das Querladen von verschiedenen Versionen. Private Assemblys sollten in einen `bin`-Ordner im Funktionsverzeichnis hochgeladen werden. Verweisen Sie auf die Assemblys, indem Sie den Dateinamen verwenden, etwa `#r "MyAssembly.dll"`.

Informationen zum Hochladen von Dateien in Ihren Funktionenordner finden Sie im Abschnitt zur [Paketverwaltung](#using-nuget-packages).

### <a name="watched-directories"></a>Überwachte Verzeichnisse

Das Verzeichnis, das die Skriptdatei für die Funktion enthält, wird automatisch im Hinblick auf Änderungen an Assemblys überwacht. Um Änderungen an Assemblys in anderen Verzeichnissen zu überwachen, fügen Sie sie der Liste `watchDirectories` in der Datei [host.json](functions-host-json.md) hinzu.

## <a name="using-nuget-packages"></a>Verwenden von NuGet-Paketen
Um NuGet-Pakete in einer 2.x C#-Funktion zu verwenden, laden Sie die Datei *function.proj* in den Ordner der Funktion im Dateisystem der Funktions-App hoch. Hier sehen Sie ein Beispiel für die Datei *function.proj*, die einen Verweis auf *Microsoft.ProjectOxford.Face* (Version *1.1.0*) hinzufügt:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    
    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Um einen benutzerdefinierten NuGet-Feed zu verwenden, geben Sie den Feed in der Datei *NuGet.config* im Stammverzeichnis der Funktionen-App an. Weitere Informationen finden Sie unter [Konfigurieren des NuGet-Verhaltens](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> In 1.x C#-Funktionen wird auf NuGet-Pakete mit einer *project.json*-Datei anstelle einer *function.proj*-Datei verwiesen.

Verwenden Sie für 1.x-Funktionen stattdessen eine *project.json*-Datei. Hier folgt eine *project.json*-Beispieldatei: 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Verwenden einer function.proj-Datei

1. Öffnen Sie die Funktion im Azure-Portal. Auf der Registerkarte „Protokolle“ wird die Ausgabe der Paketinstallation angezeigt.
2. Verwenden Sie zum Hochladen der Datei *function.proj* eine der Methoden, die im Abschnitt [Vorgehensweise zum Aktualisieren von Funktions-App-Dateien](functions-reference.md#fileupdate) des Themas „Azure Functions-Entwicklerreferenz“ beschrieben werden.
3. Nach dem Hochladen der Datei *.proj* wird im Streamingprotokoll Ihrer Funktion in etwa folgende Ausgabe angezeigt:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Umgebungsvariablen

Verwenden Sie `System.Environment.GetEnvironmentVariable`zum Abrufen einer Umgebungsvariablen oder zum Abrufen des Werts einer App-Einstellung, wie im folgenden Codebeispiel zu sehen:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
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
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Binden zur Laufzeit

In C# und anderen .NET-Sprachen können Sie ein [imperatives](https://en.wikipedia.org/wiki/Imperative_programming) Bindungsmuster verwenden, im Gegensatz zu den [ *deklarativen* ](https://en.wikipedia.org/wiki/Declarative_programming) Bindungen in *function.json*. Imperative Bindung eignet sich, wenn Bindungsparameter zur Laufzeit statt zur Entwurfszeit berechnet werden müssen. Mit diesem Muster ist die Bindung an unterstützte Eingabe- und Ausgabebindungen direkt im Funktionscode möglich.

Definieren Sie eine imperative Bindung wie folgt:

- Schließen Sie für die gewünschten imperativen Bindungen **keinen** Eintrag in *function.json* ein.
- Übergeben Sie den Eingabeparameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) oder [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Verwenden Sie das folgende C#-Muster, um die Datenbindung auszuführen.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` ist das .NET-Attribut, das die Bindung definiert, und `T` ist ein Eingabe- oder Ausgabetyp, der von diesem Bindungstyp unterstützt wird. `T` darf kein `out`-Parametertyp sein (wie etwa `out JObject`). Die ausgehende Bindung für die Tabelle „Mobile Apps“ unterstützt z.B. [sechs Ausgabetypen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), Sie können aber nur [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) oder [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) für `T` verwenden.

### <a name="single-attribute-example"></a>Beispiel mit einem einzigen Attribut

Mit dem folgenden Beispielcode wird eine [ausgehende Speicherblob-Bindung](functions-bindings-storage-blob.md#output) mit einem Blobpfad erstellt, der zur Laufzeit definiert wird. Dann wird eine Zeichenfolge in das Blob geschrieben.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiert die Eingabe- oder Ausgabebindung für den [Speicherblob](functions-bindings-storage-blob.md), und [TextWriter](/dotnet/api/system.io.textwriter) ist ein unterstützter Ausgabenbindungstyp.

### <a name="multiple-attribute-example"></a>Beispiel mit mehreren Attributen

Im vorherigen Beispiel wird die App-Einstellung für die Verbindungszeichenfolge (`AzureWebJobsStorage`) des Hauptspeicherkontos der Funktions-App abgerufen. Sie können eine benutzerdefinierte App-Einstellung angeben, die für das Storage-Konto verwendet werden soll, indem Sie [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) hinzufügen und das Attributarray an `BindAsync<T>()` übergeben. Verwenden Sie einen `Binder`-Parameter, nicht `IBinder`.  Beispiel:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

In der folgenden Tabelle sind die .NET-Attribute für jeden Bindungstyp und die Pakete aufgelistet, in denen sie definiert sind.

> [!div class="mx-codeBreakAll"]
> | Bindung | Attribut | Hinzuzufügender Verweis |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Speicherwarteschlange | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Speicherblob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Speichertabelle | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Triggern und Bindungen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Weitere Informationen zu bewährten Methoden für Azure Functions](functions-best-practices.md)
