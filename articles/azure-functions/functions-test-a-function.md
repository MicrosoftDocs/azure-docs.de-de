---
title: Testen von Azure Functions
description: Erstellen automatisierter Tests für eine C# -Funktion in Visual Studio und JavaScript-Funktion in VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Functions, Ereignisverarbeitung, Webhooks, dynamisches Compute, serverlose Architektur, Tests
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 7489f42719223dbd7f9cc2908f666dca53fe7c04
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496388"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategien zum Testen Ihres Codes in Azure Functions

In diesem Artikel wird veranschaulicht, wie Sie automatisierte Tests für Azure Functions erstellen. 

Das Testen des gesamten Codes wird empfohlen, jedoch erhalten Sie möglicherweise die besten Ergebnisse, wenn Sie die Logik einer Funktion umschließen und Tests außerhalb der Funktion erstellen. Das Abstrahieren von Logik begrenzt immer die Codezeilen einer Funktion und gestattet es der Funktion, allein für das Aufrufen anderer Klassen oder Module verantwortlich zu sein. In diesem Artikel wird jedoch das Erstellen automatisierter Tests für eine HTTP- und per Zeitgeber ausgelöste Funktion veranschaulicht.

Der Inhalt, der folgt, ist in zwei verschiedene Abschnitte unterteilt, die sich auf verschiedene Sprachen und Umgebungen beziehen. Sie können das Erstellen von Tests in folgenden Sprachen/Umgebungen erlernen:

- [C# in Visual Studio mit xUnit](#c-in-visual-studio)
- [JavaScript in VS Code mit Jest](#javascript-in-vs-code)

Das Beispielrepository ist auf [GitHub](https://github.com/Azure-Samples/azure-functions-tests) verfügbar.

## <a name="c-in-visual-studio"></a>C# in Visual Studio
Im folgenden Beispiel wird beschrieben, wie Sie eine C#-Funktions-App in Visual Studio erstellen und mit [xUnit](https://xunit.github.io) ausführen und testen.

![Testen von Azure Functions mit C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Einrichtung

Um Ihre Umgebung einzurichten, erstellen Sie eine Funktion und Test-App. Die folgenden Schritte helfen Ihnen beim Erstellen der zur Unterstützung der Tests erforderlichen Apps und Funktionen:

1. [Erstellen Sie eine neue Functions-App](./functions-create-first-azure-function.md), und nennen Sie sie *Functions*
2. [Erstellen Sie eine HTTP-Funktion aus der Vorlage](./functions-create-first-azure-function.md), und nennen Sie sie *HttpTrigger*.
3. [Erstellen Sie eine Zeitgeberfunktion aus der Vorlage](./functions-create-scheduled-function.md), und nennen Sie sie *TimerTrigger*.
4. [Eine xUnit-Test-App erstellen Sie](https://xunit.github.io/docs/getting-started-dotnet-core) in Visual Studio, indem Sie auf **Datei > Neu > Projekt > Visual C# > .NET Core > xUnit-Testprojekt** klicken und sie *Functions.Test* nennen. 
5. Fügen Sie mithilfe von Nuget einen Verweis von der Test-App [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) hinzu.
6. [Verweisen Sie aus der *Functions.Test*-App auf die *Functions*-App](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017).

### <a name="create-test-classes"></a>Erstellen von Testklassen

Nachdem die Anwendungen nun erstellt sind, können Sie die Klassen erstellen, die zum Ausführen der automatisierten Tests verwendet werden.

Jede Funktion nimmt eine Instanz von [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger), um die Nachrichtenprotokollierung zu behandeln. Einige Tests protokollieren entweder keine Nachrichten oder ignorieren, wie die Protokollierung implementiert wird. Andere Tests müssen protokollierte Nachrichten auswerten, um zu bestimmen, ob ein Test bestanden wird.

Die `ListLogger`-Klasse dient der Implementierung der `ILogger`-Schnittstelle sowie der Aufnahme einer internen Liste von Nachrichten für die Evaluierung während eines Tests.

**Klicken Sie mit der rechten Maustaste** auf die *Functions.Test*-Anwendung, und wählen Sie **Hinzufügen > Klasse** aus, nennen Sie dies **NullScope.cs**, und geben Sie den folgenden Code ein:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

**Klicken Sie anschließend mit der rechten Maustaste** auf die *Functions.Test*-Anwendung, und wählen Sie **Hinzufügen > Klasse** aus, nennen Sie dies **ListLogger.cs**, und geben Sie den folgenden Code ein:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

Die `ListLogger`-Klasse implementiert die folgenden Elemente, wie durch die `ILogger`-Schnittstelle vereinbart:

- **BeginScope**: Bereiche fügen Ihrer Protokollierung Kontext hinzu. In diesem Fall verweist der Test einfach auf die statische Instanz in der `NullScope`-Klasse, damit der Test funktionieren kann.

- **IsEnabled**: Ein Standardwert von `false` wird bereitgestellt.

- **Log**: Diese Methode verwendet die angegebene `formatter`-Funktion, um die Nachricht zu formatieren, und fügt dann den resultierenden Text der `Logs`-Auflistung hinzu.

Die `Logs`-Auflistung ist eine Instanz von `List<string>` und wird im Konstruktor initialisiert.

Als Nächstes **klicken Sie mit der rechten Maustaste** auf die *Functions.Test*-Anwendung, und wählen Sie **Hinzufügen > Klasse** aus, nennen Sie dies **LoggerTypes.cs**, und geben Sie den folgenden Code ein:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
Diese Enumeration gibt den Typ der Protokollierung an, die von den Tests verwendet wird. 

Als Nächstes **klicken Sie mit der rechten Maustaste** auf die *Functions.Test*-Anwendung, und wählen Sie **Hinzufügen > Klasse** aus, nennen Sie dies **TestFactory.cs**, und geben Sie den folgenden Code ein:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
Die `TestFactory`-Klasse implementiert die folgenden Elemente:

- **Data**: Diese Eigenschaft gibt eine [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable)-Auflistung von Beispieldaten zurück. Die Schlüssel-/Wertpaare stellen Werte dar, die in eine Abfragezeichenfolge übergeben werden.

- **CreateDictionary**: Diese Methode akzeptiert ein Schlüssel-/Wertpaar als Argumente und gibt ein neues `Dictionary` zurück, das verwendet wird, um `QueryCollection` zu erstellen, um Abfragezeichenfolgenwerte darzustellen.

- **CreateHttpRequest**: Diese Methode erstellt eine HTTP-Anforderung, die mit den angegebenen Abfragezeichenfolgenparametern initialisiert wird.

- **CreateLogger**: Basierend auf dem Typ des Protokollierungstools gibt diese Methode eine Protokollierungsklasse zurück, die zum Testen verwendet wird. `ListLogger` verfolgt die protokollierten Nachrichten, die zur Evaluierung in Tests zur Verfügung stehen.

Als Nächstes **klicken Sie mit der rechten Maustaste** auf die *Functions.Test*-Anwendung, und wählen Sie **Hinzufügen > Klasse** aus, nennen Sie dies **FunctionsTests.cs**, und geben Sie den folgenden Code ein:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Die in dieser Klasse implementierten Elemente sind:

- **Http_trigger_should_return_known_string**: Dieser Test erstellt eine Anforderung mit den Abfragezeichenfolgenwerten von `name=Bill` an eine HTTP-Funktion und überprüft, ob die erwartete Antwort zurückgegeben wird.

- **Http_trigger_should_return_string_from_member_data**: Dieser Test verwendet xUnit-Attribute, um der HTTP-Funktion Beispieldaten bereitzustellen.

- **Timer_should_log_message**: Dieser Test erstellt eine Instanz von `ListLogger` und übergibt sie an eine Zeitgeberfunktion. Sobald die Funktion ausgeführt wird, wird das Protokoll überprüft, um sicherzustellen, dass die erwartete Nachricht vorhanden ist.

Wenn Sie in Ihren Tests auf Anwendungseinstellungen zugreifen möchten, können Sie [System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables) verwenden.

### <a name="run-tests"></a>Ausführen von Tests

Um die Tests auszuführen, navigieren Sie zum **Test-Explorer**, und klicken Sie auf **Alle ausführen**.

![Testen von Azure Functions mit C# in Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Debuggen von Tests

Um die Tests zu debuggen, legen Sie bei einem Test einen Breakpoint fest, navigieren Sie zum **Test-Explorer**, und klicken Sie auf **Ausführen > Letzte Ausführung debuggen**.

## <a name="javascript-in-vs-code"></a>JavaScript in VS Code

Im folgenden Beispiel wird beschrieben, wie Sie eine JavaScript-Funktions-App in VS Code erstellen und mit [Jest](https://jestjs.io) ausführen und testen. Dieses Verfahren verwendet die [VS Code-Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions), um Azure Functions zu erstellen.

![Testen von Azure Functions mit JavaScript in VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Einrichtung

Um Ihre Umgebung einzurichten, initialisieren Sie eine neue Node.js-App in einem leeren Ordner, indem Sie `npm init` ausführen.

```bash
npm init -y
```
Als Nächstes installieren Sie Jest, indem Sie den folgenden Befehl ausführen:

```bash
npm i jest
```
Jetzt aktualisieren Sie _package.json_, um den vorhandenen Testbefehl durch den folgenden Befehl zu ersetzen:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Erstellen von Testmodulen
Nachdem das Projekt initialisiert wurde, können Sie die Module erstellen, die zum Ausführen der automatisierten Tests verwendet werden. Beginnen Sie damit, einen neuen Ordner namens *testing* zu erstellen, der die Unterstützungsmodule aufnehmen soll.

Fügen Sie im Ordner *testing* eine neue Datei hinzu, nennen Sie sie **defaultContext.js**, und fügen Sie den folgenden Code hinzu:

```javascript
module.exports = {
    log: jest.fn()
};
```
Dieses Modul simuliert die *log*-Funktion, um den Standardausführungskontext darzustellen.

Als Nächstes fügen Sie eine neue Datei hinzu, nennen Sie sie **defaultTimer.js**, und fügen Sie den folgenden Code hinzu:

```javascript
module.exports = {
    IsPastDue: false
};
```
Dieses Modul implementiert die `IsPastDue`-Eigenschaft so, dass sie als Fake-Zeitgeberinstanz fungiert.

Verwenden Sie als Nächstes die VS Code-Functions-Erweiterungen, um [eine neue JavaScript-HTTP-Funktion zu erstellen](https://docs.microsoft.com/azure/azure-functions/tutorial-javascript-vscode-get-started), und nennen Sie sie *HttpTrigger*. Nachdem die Funktion erstellt wurde, fügen Sie im selben Ordner eine neue Datei namens **index.test.js** hinzu, und fügen Sie den folgenden Code hinzu:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
Die HTTP-Funktion aus der Vorlage gibt die Zeichenfolge „Hello“ zurück, verkettet mit dem in der Abfragezeichenfolge angegebenen Namen. Dieser Test erstellt eine Instanz einer Anforderung und übergibt sie an eine HTTP-Funktion. Der Test überprüft, ob die *log*-Methode einmal aufgerufen wird, und ob der zurückgegebene Text „Hello Bill“ lautet.

Verwenden Sie als Nächstes die VS Code-Functions-Erweiterungen, um eine neue JavaScript-Zeitgeberfunktion zu erstellen, und nennen Sie sie *TimerTrigger*. Nachdem die Funktion erstellt wurde, fügen Sie im selben Ordner eine neue Datei namens **index.test.js** hinzu, und fügen Sie den folgenden Code hinzu:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Die Zeitgeberfunktion aus der Vorlage protokolliert eine Meldung am Ende des Hauptteils der Funktion. Dieser Test stellt sicher, dass die *log*-Funktion einmal aufgerufen wird.

### <a name="run-tests"></a>Ausführen von Tests
Um die Tests auszuführen, drücken Sie **STRG+~** , um das Befehlsfenster zu öffnen, und führen Sie `npm test` aus:

```bash
npm test
```

![Testen von Azure Functions mit JavaScript in VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Debuggen von Tests

Um Ihre Tests zu debuggen, fügen Sie die folgende Konfiguration Ihrer Datei *launch.json* hinzu:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Legen Sie als Nächstes einen Breakpoint in Ihrem Test fest, und drücken Sie **F5**.

## <a name="next-steps"></a>Nächste Schritte

Wo Sie jetzt wissen, wie Sie automatisierte Tests für Ihre Funktionen schreiben, fahren Sie mit diesen Ressourcen fort:
- [Manuelles Ausführen einer Funktion ohne HTTP-Trigger](./functions-manually-run-non-http.md)
- [Azure Functions – Fehlerbehandlung](./functions-bindings-error-pages.md)
- [Lokales Debuggen von Azure-Funktionen mit Event Grid-Trigger](./functions-debug-event-grid-trigger-local.md)
