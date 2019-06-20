---
title: F#-Entwicklerreferenz zu Azure Functions | Microsoft Docs
description: Erfahren Sie, wie in Azure Functions mithilfe von F#-Skripts entwickelt wird.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: Azure Functions, Functions, Ereignisverarbeitung, Webhooks, dynamisches Computing, serverlose Architektur, F#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: fbc5a149e59bff1897d3949185272e9ca664f989
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717828"
---
# <a name="azure-functions-f-developer-reference"></a>F#-Entwicklerreferenz zu Azure Functions

F# für Azure Functions ist eine Lösung, mit der Sie ganz einfach kleinere Codeelemente (Funktionen) in der Cloud ausführen können. Daten fließen über Funktionsargumente in Ihre F#-Funktion. Argumentnamen werden in `function.json`angegeben, und es gibt vordefinierte Namen für den Zugriff auf Elemente wie die Funktionsprotokollierung und Abbruchtoken. 

>[!IMPORTANT]
>F#-Skripts („.fsx“) werden nur von [Version 1.x](functions-versions.md#creating-1x-apps) der Azure Functions-Runtime unterstützt. Wenn Sie F# mit der Runtimeversion 2.x verwenden möchten, müssen Sie ein vorkompiliertes F#-Klassenbibliotheksprojekt („.fs“) verwenden. Sie erstellen, verwalten und veröffentlichen ein F#-Klassenbibliotheksprojekt mithilfe von Visual Studio wie ein [C#-Klassenbibliotheksprojekt](functions-dotnet-class-library.md). Weitere Informationen zu den Versionen von Functions finden Sie unter [Übersicht über die Runtimeversionen von Azure Functions](functions-versions.md).

In diesem Artikel wird davon ausgegangen, dass Sie bereits die [Entwicklerreferenz zu Azure Functions](functions-reference.md)gelesen haben.

## <a name="how-fsx-works"></a>Funktionsweise von „.fsx“
Eine `.fsx` -Datei ist ein F#-Skript. Sie können sich dies wie ein F#-Projekt vorstellen, das in einer einzelnen Datei enthalten ist. Die Datei enthält sowohl den Code für Ihr Programm (in diesem Fall Ihre Azure-Funktion) als auch Direktiven zum Verwalten von Abhängigkeiten.

Bei Verwendung der Endung `.fsx` für eine Azure-Funktion werden die üblicherweise erforderlichen Assemblys automatisch für Sie eingebunden, sodass Sie sich auf die Funktion konzentrieren können und sich nicht mit Codebausteinen beschäftigen müssen.

## <a name="folder-structure"></a>Ordnerstruktur

Die Ordnerstruktur für ein F#-Skriptprojekt sieht wie folgt aus:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Sie können die freigegebene Datei [host.json](functions-host-json.md) zum Konfigurieren der Funktions-App verwenden. Jede Funktion verfügt über eine eigene Codedatei (FSX-Datei) sowie über eine eigene Bindungskonfigurationsdatei (function.json).

Die in [Version 2.x](functions-versions.md) der Functions-Runtime erforderlichen Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, die eigentlichen Bibliotheksdateien befinden sich im Ordner `bin`. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Wenn Sie Funktionen im Azure-Portal entwickeln, wird diese Registrierung für Sie ausgeführt.

## <a name="binding-to-arguments"></a>Binden an Argumente
Jede Bindung unterstützt eine Gruppe von Argumenten. Dies ist in der [Entwicklerreferenz zu Triggern und Bindungen in Azure Functions](functions-triggers-bindings.md) ausführlich beschrieben. Eine Argumentbindung, die von einem Blobtrigger beispielsweise unterstützt wird, ist ein POCO-Element. Dies kann mit einem F#-Eintrag ausgedrückt werden. Beispiel:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Für Ihre F#-Azure-Funktion wird mindestens ein Argument verwendet. Wenn wir von Azure Functions-Argumenten sprechen, geht es um Argumente für die *Eingabe* und Argumente für die *Ausgabe*. Bei einem Eingabeargument geht es genau um das, was der Name vermuten lässt: die Eingabe in Ihre F#-Azure-Funktion. Bei einem *Ausgabe*argument geht es um änderbare Daten oder ein `byref<>`-Argument, das als Möglichkeit zum Zurückgeben von Daten *aus* Ihrer Funktion dient.

Im obigen Beispiel ist `blob` ein Eingabeargument und `output` ein Ausgabeargument. Beachten Sie, dass wir `byref<>` für `output` verwendet haben (das Hinzufügen der Anmerkung `[<Out>]` ist nicht erforderlich). Mit dem Typ `byref<>` kann Ihre Funktion ändern, auf welchen Eintrag oder welches Objekt das Argument verweist.

Wenn ein F#-Eintrag als Eingabetyp verwendet wird, muss die Eintragsdefinition mit `[<CLIMutable>]` gekennzeichnet werden. So kann das Azure Functions-Framework die Felder richtig festlegen, bevor der Eintrag an Ihre Funktion übergeben wird. Im Hintergrund generiert `[<CLIMutable>]` Setter für die Eintragseigenschaften. Beispiel:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Eine F#-Klasse kann auch für in- und out-Argumente verwendet werden. Für eine Klasse benötigen Eigenschaften normalerweise „Getter“ und „Setter“. Beispiel:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Protokollierung
Zum Protokollieren der Ausgabe in Ihren [Streamingprotokollen](../app-service/troubleshoot-diagnostic-logs.md) in F# sollte Ihre Funktion ein Argument vom Typ [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) verwenden. Der Einheitlichkeit halber empfehlen wir Ihnen, diesem Argument den Namen `log`zu geben. Beispiel:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
Der `async`-Workflow kann verwendet werden, aber vom Ergebnis muss ein `Task`-Element zurückgegeben werden. Hierfür können Sie `Async.StartAsTask`verwenden, z.B.:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Abbruchtoken
Wenn Ihre Funktion das ordnungsgemäße Herunterfahren durchführen soll, können Sie sie mit dem Argument [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) versehen. Dies können Sie mit `async`kombinieren, z.B.:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importieren von Namespaces
Namespaces können auf die übliche Weise geöffnet werden:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Die folgenden Namespaces werden automatisch geöffnet:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Verweise auf externe Assemblys
Auf ähnliche Weise werden Framework-Assemblyverweise mit der `#r "AssemblyName"`-Direktive hinzugefügt.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Die folgenden Assemblys werden automatisch von der Azure Functions-Hostumgebung hinzugefügt:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Darüber hinaus stehen die folgenden besonderen Assemblys zur Verfügung, auf die über den einfachen Namen verwiesen werden kann (beispielsweise `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`(Fixierte Verbindung) festgelegt ist(Fixierte Verbindung) festgelegt ist.

Wenn Sie auf eine private Assembly verweisen müssen, können Sie die Assemblydatei in einen `bin`-Ordner relativ zu Ihrer Funktion hochladen und anhand des Dateinamens darauf verweisen (Beispiel: `#r "MyAssembly.dll"`). Informationen zum Hochladen von Dateien in Ihren Funktionenordner finden Sie im folgenden Abschnitt zur Paketverwaltung.

## <a name="editor-prelude"></a>Editor-Einleitung
Ein Editor, der F#-Compilerdienste unterstützt, besitzt keine Informationen zu den Namespaces und Assemblys, die in Azure Functions automatisch enthalten sind. Daher kann es nützlich sein, eine Einleitung einzufügen, die dem Editor als Unterstützung beim Suchen nach den von Ihnen verwendeten Assemblys und beim expliziten Öffnen von Namespaces dient. Beispiel:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Wenn Ihr Code von Azure Functions ausgeführt wird, wird die Quelle mit der Definition `COMPILED` verarbeitet, und die Editor-Einleitung wird ignoriert.

<a name="package"></a>

## <a name="package-management"></a>Paketverwaltung
Um NuGet-Pakete in einer F#-Funktion zu verwenden, fügen Sie dem Funktionsordner im Dateisystem der Funktions-App die Datei `project.json` hinzu. Hier ist eine `project.json`-Beispieldatei angegeben, mit der ein NuGet-Paketverweis auf `Microsoft.ProjectOxford.Face` Version 1.1.0 hinzugefügt wird:

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

Nur .NET Framework 4.6 wird unterstützt. Stellen Sie also sicher, dass für Ihre Datei `project.json` wie hier gezeigt `net46` angegeben wird.

Beim Hochladen der Datei `project.json` erhält die Laufzeit die Pakete und fügt automatisch Verweise auf die Paketassemblys hinzu. Sie müssen keine `#r "AssemblyName"` -Direktiven hinzufügen. Fügen Sie die erforderlichen `open`-Anweisungen einfach der `.fsx`-Datei hinzu.

Es kann ratsam sein, automatische Verweisassemblys in die Editor-Einleitung einzufügen, um die Interaktion des Editors mit den F#-Kompilierdiensten zu verbessern.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Hinzufügen der Datei `project.json` zur Azure-Funktion
1. Stellen Sie zunächst sicher, dass Ihre Funktionen-App ausgeführt wird. Öffnen Sie zu diesem Zweck Ihre Funktion im Azure-Portal. Dies ermöglicht auch Zugriff auf die Streamingprotokolle, in denen die Ausgabe der Paketinstallation angezeigt wird.
2. Verwenden Sie zum Hochladen der Datei `project.json` eines der Verfahren, die unter [Aktualisieren von Funktionen-App-Dateien](functions-reference.md#fileupdate)beschrieben sind. Wenn Sie [Continuous Deployment für Azure Functions](functions-continuous-deployment.md) verwenden, können Sie Ihrer Stagingverzweigung die Datei `project.json` hinzufügen, um damit zu experimentieren, bevor Sie sie der Bereitstellungsverzweigung hinzufügen.
3. Nach dem Hinzufügen der Datei `project.json` wird eine Ausgabe angezeigt, die dem folgenden Beispiel im Streamingprotokoll Ihrer Funktion ähnelt:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Umgebungsvariablen
Verwenden Sie `System.Environment.GetEnvironmentVariable`, um eine Umgebungsvariable oder einen App-Einstellungswert abzurufen, z.B.:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Wiederverwenden von .fsx-Code
Sie können Code aus anderen `.fsx`-Dateien verwenden, indem Sie eine `#load`-Direktive nutzen. Beispiel:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Die Pfade zur `#load`-Direktive gelten relativ zum Speicherort Ihrer `.fsx`-Datei.

* `#load "logger.fsx"` lädt eine Datei, die sich im Funktionsordner befindet.
* `#load "package\logger.fsx"` lädt eine Datei, die sich im Ordner `package` im Funktionsordner befindet.
* `#load "..\shared\mylogger.fsx"` lädt eine Datei, die sich im Ordner `shared` auf derselben Ebene wie der Funktionsordner direkt unter `wwwroot` befindet.

Die `#load`-Direktive funktioniert nur mit `.fsx`-Dateien (F#-Skripts) und nicht mit `.fs`-Dateien.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Ressourcen:

* [F# Guide](/dotnet/articles/fsharp/index)
* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)
* [Azure Functions-Tests](functions-test-a-function.md)
* [Azure Functions-Skalierung](functions-scale.md)

