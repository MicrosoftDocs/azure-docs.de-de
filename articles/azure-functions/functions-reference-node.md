---
title: JavaScript-Entwicklerreferenz für Azure Functions
description: Erfahren Sie, wie Sie mithilfe von JavaScript Funktionen entwickeln können.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 11/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 21ab58095fa919e6302251c16e474b02f1445993
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301995"
---
# <a name="azure-functions-javascript-developer-guide"></a>JavaScript-Entwicklerhandbuch für Azure Functions

Dieser Leitfaden enthält ausführliche Informationen, die Sie bei der erfolgreichen Entwicklung von Azure Functions mit JavaScript unterstützen.

Wenn Sie noch nicht mit Azure Functions vertraut sind, sollten Sie als Express.js-, Node.js- oder JavaScript-Entwickler zunächst einen der folgenden Artikel lesen:

| Erste Schritte | Konzepte| Geführte Tutorials |
| -- | -- | -- | 
| <ul><li>[Node.js-Funktion unter Verwendung von Visual Studio Code](./create-first-function-vs-code-node.md)</li><li>[Node.js-Funktion mit Terminal/Eingabeaufforderung](./create-first-function-cli-java.md)</li></ul> | <ul><li>[Entwicklerhandbuch](functions-reference.md)</li><li>[Hostingoptionen](functions-scale.md)</li><li>[TypeScript-Funktionen](#typescript)</li><li>[Überlegungen&nbsp; zur Leistung](functions-best-practices.md)</li></ul> | <ul><li>[Erstellen serverloser Anwendungen](/learn/paths/create-serverless-applications/)</li><li>[Umgestalten von Node js- und Express-APIs auf serverlose APIs](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript-Funktionsgrundlagen

Eine JavaScript-Funktion (bzw. Node.js-Funktion) ist eine exportierte `function`, die ausgeführt wird, wenn sie ausgelöst wird ([Trigger werden in „function.json“ konfiguriert](functions-triggers-bindings.md)). Das erste Argument, das an jede Funktion übergeben wird, ist ein `context`-Objekt, das zum Empfangen und Senden von Bindungsdaten, für die Protokollierung und für die Kommunikation mit der Runtime verwendet wird.

## <a name="folder-structure"></a>Ordnerstruktur

Die erforderlichen Ordnerstruktur für ein JavaScript-Projekt sieht folgendermaßen aus. Diese Standardeinstellung kann geändert werden. Weitere Informationen finden Sie im Abschnitt zu [scriptFile](#using-scriptfile) weiter unten.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Im Stammverzeichnis des Projekts befindet sich eine freigegebene Datei [host.json](functions-host-json.md), die zum Konfigurieren der Funktions-App verwendet werden kann. Jede Funktion verfügt über einen Ordner mit einer eigenen Codedatei (JS-Datei) und Bindungskonfigurationsdatei („function.json“). Der Name des übergeordneten Verzeichnisses von `function.json` ist immer der Name Ihrer Funktion.

Die in [Version 2.x](functions-versions.md) der Functions-Runtime erforderlichen Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, die eigentlichen Bibliotheksdateien befinden sich im Ordner `bin`. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](./functions-bindings-register.md#extension-bundles). Wenn Sie Funktionen im Azure-Portal entwickeln, wird diese Registrierung für Sie ausgeführt.

## <a name="exporting-a-function"></a>Exportieren einer Funktion

JavaScript-Funktionen müssen über [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (oder [`exports`](https://nodejs.org/api/modules.html#modules_exports)) exportiert werden. Die exportierte Funktion muss eine JavaScript-Funktion sein, die ausgeführt wird, wenn sie ausgelöst wird.

Standardmäßig sucht die Functions-Runtime in `index.js` nach Ihrer Funktion, wobei sich `index.js` im gleichen übergeordneten Verzeichnis befindet wie die entsprechende Datei `function.json`. Im Standardfall sollte Ihre exportierte Funktion der einzige Export aus der zugehörigen Datei oder der Export mit dem Namen `run` oder `index` sein. Um den Dateispeicherort zu konfigurieren und den Namen Ihrer Funktion zu exportieren, lesen Sie weiter unten die Beschreibung zum [Konfigurieren des Einstiegspunkts Ihrer Funktion](functions-reference-node.md#configure-function-entry-point).

An die exportierte Funktion wird bei der Ausführung eine Reihe von Argumenten übergeben. Das erste angenommene Argument ist immer ein `context`-Objekt. Wenn Ihre Funktion synchron ist (also keine Zusage zurückgibt), muss das `context`-Objekt übergeben werden, da der Aufruf von `context.done` zur korrekten Verwendung erforderlich ist.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportieren einer Async-Funktion
Bei der Verwendung der Deklaration [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) oder einfacher JavaScript-[Zusagen](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) in Version 2.x der Functions-Runtime müssen Sie den [`context.done`](#contextdone-method)-Rückruf nicht explizit aufrufen, um zu signalisieren, dass Ihre Funktion abgeschlossen wurde. Ihre Funktion wird abgeschlossen, wenn die exportierte asynchrone Funktion/Zusage abgeschlossen wird. Bei Funktionen für Version 1.x der Runtime müssen Sie jedoch [`context.done`](#contextdone-method) aufrufen, wenn die Ausführung des Codes abgeschlossen wurde.

Beim folgenden Beispiel handelt es sich um eine einfache Funktion, die ihre Auslösung protokolliert und dann die Ausführung abschließt.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Beim Exportieren einer asynchronen Funktion können Sie zudem eine Ausgabebindung für die Annahme des `return`-Werts konfigurieren. Dies wird empfohlen, wenn Sie nur eine Ausgabebindung definiert haben.

Um eine Ausgabe mithilfe von `return` zuzuweisen, ändern Sie die `name`-Eigenschaft in `function.json` in `$return`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

In diesem Fall sollte die Funktion wie das folgende Beispiel aussehen:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bindungen 
In JavaScript werden [Bindungen](functions-triggers-bindings.md) in der Datei „function.json“ einer Funktion konfiguriert und definiert. Funktionen interagieren auf verschiedene Weise mit Bindungen.

### <a name="inputs"></a>Eingaben
Eingaben werden in Azure Functions in zwei Kategorien unterteilt: die Triggereingabe und die zusätzliche Eingabe. Trigger und andere Eingabebindungen (Bindungen des Typs `direction === "in"`) können von einer Funktion auf drei Arten gelesen werden:
 - **_[Empfohlen]_ Als an die Funktion übergebene Parameter.** Sie werden in der Reihenfolge, in der sie in *function.json* definiert sind, an die Funktion übergeben. Die in *function.json* definierte `name`-Eigenschaft muss nicht mit dem Namen des Parameters übereinstimmen, obwohl dies empfehlenswert ist.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Als Member des [`context.bindings`](#contextbindings-property)-Objekts.** Jeder Member wird durch die in *function.json* definierte `name`-Eigenschaft benannt.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Als Eingaben unter Verwendung des JavaScript-Objekts [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments).** Dies entspricht im Wesentlichen dem Übergeben von Eingaben als Parameter, ermöglicht jedoch die dynamische Verarbeitung der Eingaben.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Ausgaben
Ausgaben (Bindungen des Typs `direction === "out"`) können von einer Funktion auf verschiedene Arten geschrieben werden. In allen Fällen entspricht die in *function.json* definierte `name`-Eigenschaft der Bindung dem Namen des Objektmembers, der in die Funktion geschrieben wird. 

Sie können Ausgabebindungen mit einer der folgenden Methoden Daten zuweisen. Achten Sie darauf, dass Sie nicht beide Methoden verwenden.

- **_[Empfohlen für mehrere Ausgaben]_ Zurückgeben eines Objekts.** Bei Verwendung einer asynchronen Funktion (mit Rückgabe einer Zusage) kann ein Objekt mit zugewiesenen Ausgabedaten zurückgegeben werden. Im folgenden Beispiel werden die Ausgabebindungen in *function.json* mit „httpResponse“ und „queueOutput“ benannt.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Bei Verwendung einer synchronen Funktion kann dieses Objekt mithilfe von [`context.done`](#contextdone-method) zurückgegeben werden (siehe Beispiel).
- **_[Empfohlen für eine einzelne Ausgabe]_ Direktes Zurückgeben eines Werts und Verwenden des Bindungsnamens „$return“.** Dies ist nur bei asynchronen Funktionen (mit Rückgabe einer Zusage) möglich. Siehe dazu das Beispiel unter [Exportieren einer Async-Funktion](#exporting-an-async-function). 
- **Zuweisen von Werten zu `context.bindings`.** Sie können „context.bindings“ direkt Werte zuweisen.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Datentyp für Bindungen

Verwenden Sie zum Definieren des Datentyps für eine Eingabebindung die `dataType`-Eigenschaft in der Bindungsdefinition. Um z.B. den Inhalt einer HTTP-Anforderung im Binärformat zu lesen, verwenden Sie den Typ `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Optionen für `dataType` sind `binary`, `stream` und `string`.

## <a name="context-object"></a>context-Objekt

Die Laufzeit verwendet ein `context`-Objekt, um Daten an Ihre Funktion und die Laufzeit und von Ihrer Funktion und der Laufzeit zu übergeben. Das zum Lesen und Festlegen von Daten aus Bindungen und zum Schreiben in Protokolle verwendete `context`-Objekt wird immer als erster Parameter an eine Funktion übergeben.

Für Funktionen mit synchronem Code schließt das Kontextobjekt den `done`-Rückruf ein, den Sie durchführen, wenn die Verarbeitung der Funktion abgeschlossen ist. `done` muss beim Schreiben von asynchronem Code nicht explizit aufgerufen werden. Der `done`-Rückruf wird implizit durchgeführt.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

Der Kontext, der an die Funktion übertragen wird, macht eine `executionContext`-Eigenschaft verfügbar. Dieses Objekt hat folgende Eigenschaften:

| Eigenschaftenname  | type  | BESCHREIBUNG |
|---------|---------|---------|
| `invocationId` | String | Stellt einen eindeutigen Bezeichner für den jeweiligen Funktionsaufruf bereit. |
| `functionName` | String | Gibt den Namen der Funktion an, die ausgeführt wird. |
| `functionDirectory` | String | Stellt das Funktionen-App-Verzeichnis bereit. |

Im folgenden Beispiel wird gezeigt, wie die `invocationId` zurückgegeben wird.

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>context.bindings-Eigenschaft

```js
context.bindings
```

Gibt ein benanntes Objekt zurück, das zum Lesen oder Zuweisen von Bindungsdaten verwendet wird. Auf Eingabe- und Triggerbindungsdaten kann durch Lesen der Eigenschaften in `context.bindings` zugegriffen werden. Auf Ausgabebindungsdaten kann durch Hinzufügen von Daten zu `context.bindings` zugegriffen werden.

Mit den folgenden Bindungsdefinitionen in der Datei „function.json“ können Sie beispielsweise auf den Inhalt einer Warteschlange von `context.bindings.myInput` zugreifen und Ausgaben mit `context.bindings.myOutput` einer Warteschlange zuweisen.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Sie können die Ausgabebindungsdaten mit der `context.done`-Methode anstelle des `context.binding`-Objekts definieren (siehe unten).

### <a name="contextbindingdata-property"></a>context.bindingData-Eigenschaft

```js
context.bindingData
```

Gibt ein benanntes Objekt zurück, das Triggermetadaten und Funktionsaufrufdaten (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`) enthält. Ein Beispiel für Triggermetadaten finden Sie in diesem [Event Hubs-Beispiel](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>context.Done-Methode

```js
context.done([err],[propertyBag])
```

Gibt für die Runtime an, dass der Code abgeschlossen wurde. Wenn die Funktion die [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)-Deklaration verwendet, muss `context.done()` nicht verwendet werden. Der Rückruf `context.done` wird implizit aufgerufen. Asynchrone Funktionen sind in Node 8 oder einer höheren Version verfügbar, für die Version 2.x der Functions-Runtime erforderlich ist.

Wenn Ihre Funktion keine asynchrone Funktion ist, **müssen Sie `context.done` aufrufen**, um der Laufzeit mitzuteilen, dass Ihre Funktion vollständig ist. Wenn diese Methode fehlt, tritt bei der Ausführung ein Timeout auf.

Mit der `context.done`-Methode können Sie sowohl einen benutzerdefinierten Fehler an die Laufzeit als auch ein JSON-Objekt mit Ausgabebindungsdaten zurückgeben. Eigenschaften, die an `context.done` übergeben werden, überschreiben alles, was für das `context.bindings`-Objekt festgelegt wurde.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log-Methode  

```js
context.log(message)
```

Ermöglicht das Schreiben in die Streamingfunktionsprotokolle auf Standard-Ablaufverfolgungsebene, wobei auch noch andere Protokolliergrade verfügbar sind. Die Ablaufverfolgungsprotokollierung wird im nächsten Abschnitt ausführlich beschrieben. 

## <a name="write-trace-output-to-logs"></a>Schreiben der Ausgabe der Ablaufverfolgung in Protokolle

In Functions werden die Methoden vom Typ `context.log` verwendet, um die Ausgabe der Ablaufverfolgung in die Protokolle und in die Konsole zu schreiben. Wenn Sie `context.log()` aufrufen, wird Ihre Meldung auf der Standard-Ablaufverfolgungsebene (_info_) in die Konsole geschrieben. Die Integration von Functions und Azure Application Insights ermöglicht eine bessere Erfassung Ihrer Funktions-App-Protokolle. Application Insights ist eine Komponente von Azure Monitor und bietet Funktionen für die Erfassung, das visuelle Rendering und die Analyse von Anwendungstelemetriedaten sowie Ihrer Ausgaben der Ablaufverfolgung. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md).

Im folgenden Beispiel wird ein Protokoll auf der Ablaufverfolgungsebene „info“ geschrieben (einschließlich der Aufruf-ID):

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Alle `context.log`-Methoden unterstützen das gleiche Parameterformat, das auch von der [util.format](https://nodejs.org/api/util.html#util_util_format_format)-Methode in Node.js unterstützt wird. Beachten Sie den folgenden Code, der auf der standardmäßigen Ablaufverfolgungsebene Funktionsprotokolle schreibt:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Sie können den gleichen Code auch im folgenden Format schreiben:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Verwenden Sie nicht `console.log`, um Ausgaben der Ablaufverfolgung zu schreiben. Da die Ausgabe von `console.log` auf der Ebene der Funktions-App erfasst wird, ist sie nicht an einen bestimmten Funktionsaufruf gebunden und wird nicht in den Protokollen einer bestimmten Funktion angezeigt. Außerdem ist es in der Version 1.x der Functions-Runtime nicht möglich, `console.log` für die Ausgabe in der Konsole zu verwenden.

### <a name="trace-levels"></a>Ablaufverfolgungsebenen

Neben der Standardebene stehen auch folgende Protokollierungsmethoden zur Verfügung, mit denen Funktionsprotokolle auf bestimmten Ablaufverfolgungsebenen geschrieben werden können:

| Methode                 | BESCHREIBUNG                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Schreibt ein Ereignis auf Fehlerebene in die Protokolle.   |
| **warn(_message_)**    | Schreibt ein Ereignis auf Warnungsebene in die Protokolle. |
| **info(_message_)**    | Schreibt in Protokollierung auf Informationsebene oder niedriger.    |
| **verbose(_message_)** | Schreibt in Protokollierung auf ausführlicher Ebene.           |

Im folgenden Beispiel wird das gleiche Protokoll auf der Ablaufverfolgungsebene „warning“ geschrieben (anstatt auf der Ebene „info“):

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Da _error_ die höchste Ablaufverfolgungsebene ist, wird diese Ablaufverfolgung auf allen Ablaufverfolgungsebenen in die Ausgabe geschrieben, solange die Protokollierung aktiviert ist.

### <a name="configure-the-trace-level-for-logging"></a>Konfigurieren der Ablaufverfolgungsebene für die Protokollierung

In Functions können Sie den Schwellenwert der Ablaufverfolgungsebene für das Schreiben in die Protokolle oder in die Konsole definieren. Die spezifischen Schwellenwerteinstellungen hängen von Ihrer Version der Functions-Runtime ab.

# <a name="v2x"></a>[Ab v2.x](#tab/v2)

Verwenden Sie die Eigenschaft `logging.logLevel` in der Datei „host.json“, um den Schwellenwert für Ablaufverfolgungen festzulegen, die in die Protokolle geschrieben werden. Mit diesem JSON-Objekt können Sie einen Standardschwellenwert für alle Funktionen in Ihrer Funktions-App sowie spezifische Schwellenwerte für einzelne Funktionen definieren. Weitere Informationen finden Sie unter [Konfigurieren der Überwachung für Azure Functions](configure-monitoring.md).

# <a name="v1x"></a>[v1.x](#tab/v1)

Verwenden Sie die Eigenschaft `tracing.consoleLevel` in der Datei „host.json“, um den Schwellenwert für alle Ablaufverfolgungen festzulegen, die in Protokolle und in die Konsole geschrieben werden. Diese Einstellung gilt für alle Funktionen in Ihrer Funktionen-App. Im folgenden Beispiel wird der Schwellenwert für die Ablaufverfolgung festgelegt, um die ausführliche Protokollierung zu aktivieren:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Die Werte von **consoleLevel** entsprechen den Namen der `context.log`-Methoden. Um die gesamte Ablaufverfolgungsprotokollierung in der Konsole zu deaktivieren, setzen Sie **consoleLevel** auf _off_. Weitere Informationen finden Sie in der [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Protokollieren benutzerdefinierter Telemetriedaten

Ausgaben werden von Functions standardmäßig als Ablaufverfolgungen in Application Insights geschrieben. Sollten Sie mehr Steuerungsmöglichkeiten benötigen, können Sie stattdessen das [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) verwenden, um benutzerdefinierte Telemetriedaten an Ihre Application Insights-Instanz zu senden. 

# <a name="v2x"></a>[Ab v2.x](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

Mit dem Parameter `tagOverrides` wird die `operation_Id` auf die Aufrufkennung der Funktion festgelegt. Mithilfe dieser Einstellung können Sie die gesamte automatisch generierte und benutzerdefinierte Telemetrie für einen bestimmten Funktionsaufruf korrelieren.

## <a name="http-triggers-and-bindings"></a>HTTP: Trigger und Bindungen

HTTP- und Webhooktrigger und HTTP-Ausgabebindungen verwenden Request- und Response-Objekte, um das HTTP-Messaging darzustellen.  

### <a name="request-object"></a>Anforderungsobjekt

Das `context.req`-Objekt (Anforderungsobjekt) weist die folgenden Eigenschaften auf:

| Eigenschaft      | BESCHREIBUNG                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Ein Objekt, das den Hauptteil der Anforderung enthält.               |
| _headers_     | Ein Objekt, das die Header der Anforderung enthält.                   |
| _method_      | Die HTTP-Methode der Anforderung.                                |
| _originalUrl_ | Die URL der Anforderung.                                        |
| _params_      | Ein Objekt, das die Routingparameter der Anforderung enthält. |
| _query_       | Ein Objekt, das die Abfrageparameter enthält.                  |
| _rawBody_     | Der Hauptteil der Meldung als Zeichenfolge.                           |


### <a name="response-object"></a>Antwortobjekt

Das `context.res`-Objekt (Antwortobjekt) weist die folgenden Eigenschaften auf:

| Eigenschaft  | BESCHREIBUNG                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Ein Objekt, das den Hauptteil der Antwort enthält.         |
| _headers_ | Ein Objekt, das die Header der Antwort enthält.             |
| _isRaw_   | Gibt an, dass die Formatierung für die Antwort übersprungen wird.    |
| _status_  | Der HTTP-Statuscode der Antwort.                     |
| _cookies_ | Ein Array von HTTP-Cookieobjekten, die in der Antwort festgelegt sind. Ein HTTP-Cookieobjekt verfügt über einen Namen (`name`) und einen Wert (`value`) sowie über andere Cookieeigenschaften wie etwa `maxAge` oder `sameSite`. |

### <a name="accessing-the-request-and-response"></a>Zugreifen auf Anforderung und Antwort 

Beim Arbeiten mit HTTP-Triggern bestehen verschiedene Möglichkeiten, auf die HTTP-Anforderungsobjekte und -Antwortobjekte zuzugreifen:

+ **Über die `req`- und `res`-Eigenschaft des `context`-Objekts.** Auf diese Weise können Sie die herkömmlichen Muster für den Zugriff auf HTTP-Daten über das context-Objekt verwenden, anstatt das gesamte `context.bindings.name`-Muster verwenden zu müssen. Das folgende Beispiel veranschaulicht den Zugriff auf das `req`- und `res`-Objekt des `context`-Objekts:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Über die benannten Eingabe- und Ausgabebindungen.** Hierbei funktionieren die HTTP-Trigger und -Bindungen genauso wie jede andere Bindung. Im folgenden Beispiel wird das Antwortobjekt mit einer als `response` benannten Bindung festgelegt: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Nur Antwort]_ Durch Aufrufen von `context.res.send(body?: any)`.** Eine HTTP-Antwort wird mit der Eingabe `body` als Antworttext erstellt. `context.done()` wird implizit aufgerufen.

+ **_[Nur Antwort]_ Durch Aufrufen von `context.done()`.** Mit einer besonderen Art von HTTP-Bindung wird die Antwort zurückgegeben, die an die `context.done()`-Methode übergeben wird. Die folgende HTTP-Ausgabebindung definiert einen `$return`-Ausgabeparameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Skalierung und Parallelität

Standardmäßig überwacht Azure Functions automatisch die Auslastung Ihrer Anwendung und erstellt bei Bedarf zusätzliche Hostinstanzen für Node.js. Functions verwendet integrierte (nicht vom Benutzer konfigurierbare) Schwellenwerte für verschiedene Triggertypen, um zu entscheiden, wann Instanzen hinzugefügt werden sollen, z. B. Alter von Nachrichten und Warteschlangengröße für Warteschlangentrigger. Weitere Informationen finden Sie unter [Funktionsweise von Verbrauchsplan (Verbrauchstarif) und Premium-Plan](functions-scale.md#how-the-consumption-and-premium-plans-work).

Dieses Skalierungsverhalten ist für zahlreiche Node.js-Anwendungen ausreichend. Für CPU-gebundene Anwendungen können Sie die Leistung durch Verwendung mehrerer Sprachworkerprozesse weiter verbessern.

Standardmäßig verfügt jede Functions-Hostinstanz über einen einzigen Sprachworkerprozess. Sie können die Anzahl der Workerprozesse pro Host erhöhen (bis zu 10), indem Sie die Anwendungseinstellung [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) verwenden. Azure Functions versucht dann, gleichzeitige Funktionsaufrufe gleichmäßig auf diese Worker zu verteilen. 

Die FUNCTIONS_WORKER_PROCESS_COUNT gilt für jeden Host, der von Functions erstellt wird, wenn Ihre Anwendung horizontal skaliert wird, um die Anforderungen zu erfüllen. 

## <a name="node-version"></a>Node-Version

Die folgende Tabelle zeigt die aktuell von den jeweiligen Hauptversionen der Functions Runtime unterstützte Node.js-Version nach Betriebssystem:

| Functions-Version | Node-Version (Windows) | Node-Version (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (durch die Laufzeit gesperrt) | – |
| 2.x  | `~8`<br/>`~10` (empfohlen)<br/>`~12` | `node|8`<br/>`node|10` (empfohlen)  |
| 3.x | `~10`<br/>`~12` (empfohlen)<br/>`~14` (Vorschau)  | `node|10`<br/>`node|12` (empfohlen)<br/>`node|14` (Vorschau) |

Die aktuell von der Laufzeit verwendete Version ermitteln Sie, indem Sie `process.version` aus einer beliebigen Funktion protokollieren.

### <a name="setting-the-node-version"></a>Festlegen der Node-Version

Legen Sie für Windows-Funktions-Apps die Zielversion in Azure fest, indem Sie die [App-Einstellung](functions-how-to-use-azure-function-app-settings.md#settings) `WEBSITE_NODE_DEFAULT_VERSION` auf eine unterstützte LTS-Version wie `~12` festlegen.

Führen Sie für Linux-Funktions-Apps den folgenden Azure CLI-Befehl aus, um die Node-Version zu aktualisieren.

```bash
az functionapp config set --linux-fx-version "node|12" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Verwaltung von Abhängigkeiten
Um Communitybibliotheken in Ihrem JavaScript-Code zu verwenden (wie im folgenden Beispiel gezeigt), müssen Sie sicherstellen, dass alle Abhängigkeiten für Ihre Funktions-App in Azure installiert sind.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Sie sollten eine `package.json`-Datei im Stammverzeichnis Ihrer Funktions-App definieren. Wenn Sie die Datei definieren, nutzen alle Funktionen in der App gemeinsam die gleichen zwischengespeicherten Pakete, was die Leistung optimiert. Wenn ein Versionskonflikt auftritt, können Sie ihn beheben, indem Sie eine `package.json`-Datei im Ordner einer bestimmten Funktion hinzufügen.  

Bei der Bereitstellung von Funktions-Apps aus der Quellcodeverwaltung lösen alle in Ihrem Repository vorhandenen `package.json`-Dateien während der Bereitstellung ein `npm install` im eigenen Ordner aus. Bei einer Bereitstellung über das Portal oder die CLI müssen Sie die Pakete jedoch manuell installieren.

Es gibt zwei Möglichkeiten zum Installieren von Paketen für Ihre Funktions-App: 

### <a name="deploying-with-dependencies"></a>Bereitstellen mit Abhängigkeiten
1. Installieren Sie alle erforderlichen Pakete lokal, indem Sie `npm install` ausführen.

2. Stellen Sie Ihren Code bereit, und stellen sicher, dass der Ordner `node_modules` in der Bereitstellung enthalten ist. 


### <a name="using-kudu"></a>Verwenden von Kudu
1. Gehe zu `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicken Sie auf **Debugkonsole** > **CMD**.

3. Gehen Sie zu `D:\home\site\wwwroot`, und ziehen Sie dann die Datei „package.json“ auf den **wwwroot**-Ordner in der oberen Hälfte der Seite.  
    Es gibt auch andere Möglichkeiten, Dateien in Ihre Funktionen-App hochzuladen. Weitere Informationen finden Sie unter [Aktualisieren von Funktionen-App-Dateien](functions-reference.md#fileupdate). 

4. Sobald die Datei „package.json“ hochgeladen ist, führen Sie den `npm install`-Befehl in der **Kudu-Remoteausführungskonsole** aus.  
    Mit dieser Aktion werden die in der Datei „package.json“ angegebenen Pakete heruntergeladen und die Funktionen-App neu gestartet.

## <a name="environment-variables"></a>Umgebungsvariablen

Fügen Sie einer Funktionsanwendung in Ihrer lokalen und in der Cloudumgebung Ihre eigenen Umgebungsvariablen hinzu, z. B. Betriebsgeheimnisse (Verbindungszeichenfolgen, Schlüssel und Endpunkte) oder Umgebungseinstellungen (z. B. Profilerstellungsvariablen). Greifen Sie mithilfe von `process.env` in Ihrem Funktionscode auf diese Einstellungen zu.

### <a name="in-local-development-environment"></a>In der lokalen Entwicklungsumgebung

Wenn das Funktionsprojekt lokal ausgeführt wird, enthält es eine [`local.settings.json`-Datei](/azure/azure-functions/functions-run-local), in der Sie die Umgebungsvariablen im `Values`-Objekt speichern. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>In der Azure-Cloudumgebung

Wenn die Ausführung in Azure erfolgt, können Sie mithilfe der Funktions-App [Anwendungseinstellungen](functions-app-settings.md) festlegen, z. B. Dienstverbindungszeichenfolgen. Diese Einstellungen werden während der Ausführung als Umgebungsvariablen bereitgestellt. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Zugreifen auf Umgebungsvariablen im Code

Greifen Sie auf Anwendungseinstellungen als Umgebungsvariablen mit `process.env` zu, wie hier in den zweiten und dritten Aufrufen von `context.log()` gezeigt, in denen die Umgebungsvariablen `AzureWebJobsStorage` und `WEBSITE_SITE_NAME` protokolliert werden:

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="configure-function-entry-point"></a>Konfigurieren des Funktionseinstiegspunkts

Die `function.json`-Eigenschaften `scriptFile` und `entryPoint` können verwendet werden, um den Speicherort und den Namen Ihrer exportierten Funktion zu konfigurieren. Diese Eigenschaften können wichtig sein, wenn Ihr JavaScript transpiliert ist.

### <a name="using-scriptfile"></a>Verwenden von `scriptFile`

Standardmäßig wird eine JavaScript-Funktion aus der Datei `index.js` ausgeführt, einer Datei, die sich das gleiche übergeordnete Verzeichnis mit der entsprechenden Datei `function.json` teilt.

`scriptFile` kann verwendet werden, um eine Ordnerstruktur zu erhalten, die wie im folgenden Beispiel aussieht:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Die Datei `function.json` für `myNodeFunction` sollte eine `scriptFile`-Eigenschaft enthalten, die auf Datei mit der exportierten Funktion verweist, die ausgeführt werden soll.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Verwenden von `entryPoint`

In `scriptFile` (oder `index.js`) muss eine Funktion mit `module.exports` exportiert werden, um gefunden und ausgeführt zu werden. Standardmäßig ist die Funktion, die ausgeführt wird, wenn sie ausgelöst wird, der einzige Export aus dieser Datei, der Export mit dem Namen `run` oder der Export mit dem Namen `index`.

Dies kann wie im folgenden Beispiel mit `entryPoint` in `function.json` konfiguriert werden:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In Functions v2.x wird der Parameter `this` in Benutzerfunktionen unterstützt. Der Funktionscode kann dann wie im folgenden Beispiel aussehen:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Beachten Sie in diesem Beispiel besonders, dass es keine Garantie dafür gibt, dass der Zustand zwischen den Ausführungen erhalten bleibt, auch wenn ein Objekt exportiert wird.

## <a name="local-debugging"></a>Lokales Debugging

Wenn ein Node.js-Prozess mit dem Parameter `--inspect` gestartet wird, lauscht er auf einen Debugclient auf dem angegebenen Port. Sie können in Azure Functions 2.x Argumente angeben, die an den Node.js-Prozess übergeben werden, der Ihren Code ausführt, indem Sie die Umgebungsvariable oder die App-Einstellung `languageWorkers:node:arguments = <args>` hinzufügen. 

Fügen Sie unter `Values` in der Datei [local.settings.json](./functions-run-local.md#local-settings-file)`"languageWorkers:node:arguments": "--inspect=5858"` hinzu, und fügen Sie einen Debugger an Port 5858 an, um lokal zu debuggen.

Wenn Sie mit VS Code debuggen, wird der Parameter `--inspect` automatisch mit dem Wert `port` in der Datei „launch.json“ des Projekts hinzugefügt.

In Version 1.x funktioniert die Einstellung `languageWorkers:node:arguments` nicht. Sie können den Debugport mit dem Parameter [`--nodeDebugPort`](./functions-run-local.md#start) in den Azure Functions Core Tools festlegen.

## <a name="typescript"></a>TypeScript

Wenn Sie Version 2.x der Azure Functions-Runtime als Ziel verwenden, können Sie mit der [Azure Functions-Erweiterung für Visual Studio Code](./create-first-function-cli-typescript.md) und den [Azure Functions Core Tools](functions-run-local.md) Funktions-Apps mit Vorlagen erstellen, die Funktions-App-Projekte in TypeScript unterstützen. Diese Vorlage generiert `package.json`- und `tsconfig.json`-Projektdateien, mit denen Sie JavaScript-Funktionen aus TypeScript-Code leichter mithilfe dieser Tools transpilieren, ausführen und veröffentlichen können.

Die generierte `.funcignore`-Datei wird verwendet, um anzugeben, welche Dateien ausgeschlossen werden sollen, wenn ein Projekt in Azure veröffentlicht wird.  

TypeScript-Dateien (.ts) werden im Ausgabeverzeichnis `dist` in JavaScript-Dateien (.js) transpiliert. TypeScript-Dateien verwenden in `function.json` den [Parameter `scriptFile`](#using-scriptfile), um den Speicherort der entsprechenden JS-Datei im Ordner `dist` anzugeben. Der Ausgabespeicherort wird von der Vorlage mit dem Parameter `outDir` in der Datei `tsconfig.json` festgelegt. Wenn Sie diese Einstellung oder den Namen des Ordners ändern, kann die Runtime den auszuführenden Code nicht finden.

Die Art der lokalen Entwicklung und Bereitstellung aus einem TypeScript-Projekt hängen von Ihrem Entwicklungstool ab.

### <a name="visual-studio-code"></a>Visual Studio Code

Mit der [Azure Functions-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) können Sie Ihre Funktionen mit TypeScript entwickeln. Für die Azure Functions-Erweiterung sind die Azure Functions Core Tools erforderlich.

Wählen Sie beim Erstellen einer Funktions-App `TypeScript` als Sprache, um in Visual Studio Code eine TypeScript-Funktions-App zu erstellen.

Wenn Sie auf **F5** drücken, um die App lokal auszuführen, wird die Transpilierung durchgeführt, bevor der Host („func.exe“) initialisiert wird. 

Wenn Sie Ihre Funktions-App mit **Deploy to function app...** (In Funktions-App bereitstellen...) in Azure bereitstellen, generiert die Azure Functions-Erweiterung zunächst aus den TypeScript-Quelldateien einen produktionsbereiten Build aus JavaScript-Dateien.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Bei der Verwendung der Core Tools unterscheidet sich ein TypeScript-Projekt auf vielfältige Weise von einem JavaScript-Projekt.

#### <a name="create-project"></a>Projekt erstellen

Wenn Sie ein TypeScript-Funktions-App-Projekt mit den Core Tools erstellen möchten, müssen Sie bei der Erstellung der Funktions-App die Sprache auf „TypeScript“ festlegen. Wählen Sie dazu eine der folgenden Methoden:

- Führen Sie den Befehl `func init` aus, wählen Sie `node` als Sprachstapel, und wählen Sie dann `typescript`.

- Führen Sie den Befehl `func init --worker-runtime typescript` aus.

#### <a name="run-local"></a>Lokale Ausführung

Wenn Sie den Code Ihrer Funktions-App lokal mit den Core Tools ausführen möchten, verwenden Sie statt `func host start` die folgenden Befehle: 

```command
npm install
npm start
```

Der Befehl `npm start` entspricht den folgenden Befehlen:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Veröffentlichen in Azure

Bevor Sie den Befehl [`func azure functionapp publish`] zur Bereitstellung in Azure verwenden, erstellen Sie aus den TypeScript-Quelldateien einen produktionsbereiten Build aus JavaScript-Dateien. 

Mit den folgenden Befehlen können Sie Ihr TypeScript-Projekt mithilfe der Core Tools vorbereiten und veröffentlichen: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Ersetzen Sie in diesem Befehl `<APP_NAME>` durch den Namen Ihrer Funktions-App.

## <a name="considerations-for-javascript-functions"></a>Überlegungen zu JavaScript-Funktionen

Beachten Sie beim Arbeiten mit JavaScript-Funktionen die Überlegungen in den folgenden Abschnitten.

### <a name="choose-single-vcpu-app-service-plans"></a>Auswählen von App Service-Plänen mit einzelner vCPU

Wenn Sie eine Funktions-App erstellen, die den App Service-Plan verwendet, sollten Sie statt eines Plans mit mehreren vCPUs einen Plan mit einer einzelnen vCPU auswählen. Derzeit führt Functions JavaScript-Funktionen auf virtuellen Computern mit einer einzelnen vCPU effizienter aus. Die Verwendung größerer virtueller Computer führt nicht zu den erwarteten Leistungsverbesserungen. Bei Bedarf können Sie manuell aufskalieren, indem Sie weitere Instanzen virtueller Computer mit einer einzelnen vCPU hinzufügen. Sie können aber auch die automatische Skalierung aktivieren. Weitere Informationen finden Sie unter [Manuelles oder automatisches Skalieren der Instanzenzahl](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Kaltstart

Bei der Entwicklung von Azure Functions im serverlosen Hostingmodell sind Kaltstarts Realität. Der Begriff *Kaltstart* bezieht sich auf die Tatsache, dass es beim ersten Start Ihrer Funktions-App nach einer Zeit der Inaktivität länger dauert, bis sie gestartet wird. Insbesondere bei JavaScript-Funktionen mit großen Abhängigkeitsbäumen kann dies erheblich länger dauern. Nach Möglichkeit sollten Sie [die Funktionen als Paketdatei ausführen](run-functions-from-deployment-package.md), um den Prozess des Kaltstarts zu beschleunigen. Bei vielen Bereitstellungsmethoden erfolgt die Ausführung standardmäßig über das Paketmodell. Wenn aber umfangreiche Kaltstarts durchgeführt werden und die Ausführung nicht auf diese Weise erfolgt, kann diese Änderung eine wesentliche Verbesserung bewirken.

### <a name="connection-limits"></a>Verbindungsbeschränkungen

Wenn Sie einen dienstabhängigen Client in einer Azure Functions-Anwendung verwenden, erstellen Sie nicht bei jedem Funktionsaufruf einen neuen Client. Erstellen Sie stattdessen einen einzelnen, statischen Client im globalen Bereich. Weitere Informationen finden Sie unter [Verwalten von Verbindungen in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Verwenden von `async` und `await`

Beim Schreiben von Azure Functions in JavaScript sollten Sie Code schreiben, indem Sie die Schlüsselwörter `async` und `await` verwenden. Wenn Sie zum Schreiben von Code `async` und `await` anstelle von Rückrufen oder `.then` und `.catch` mit Zusagen verwenden, können Sie zwei häufige Probleme vermeiden:
 - Das Auslösen von nicht abgefangenen Ausnahmen, die zu einem [Absturz des Node.js-Prozesses](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly) führen und sich unter Umständen auf die Ausführung anderer Funktionen auswirken.
 - Unerwartetes Verhalten, z. B. fehlende Protokolle aus „context.log“, die durch nicht korrekt erwartete asynchrone Aufrufe verursacht werden.

Im Beispiel unten wird die asynchrone `fs.readFile`-Methode mit einer Error-First-Rückruffunktion als zweitem Parameter aufgerufen. Durch diesen Code werden die beiden oben erwähnten Probleme verursacht. Eine Ausnahme, die nicht explizit im richtigen Bereich abgefangen wird, hat zum Absturz des gesamten Prozesses geführt (erstes Problem). Das Aufrufen von `context.done()` außerhalb des Bereichs der Rückruffunktion bedeutet, dass der Funktionsaufruf ggf. endet, bevor die Datei gelesen wird (zweites Problem). Bei diesem Beispiel führt ein zu frühes Aufrufen von `context.done()` zu fehlenden Protokolleinträgen, die mit `Data from file:` beginnen.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Beide Fehler können vermieden werden, indem die Schlüsselwörter `async` und `await` verwendet werden. Sie sollten die Node.js-Hilfsfunktion [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) verwenden, um Error-First-Funktionen im Rückrufstil in „Awaitable“-Funktionen zu verwandeln.

Im folgenden Beispiel führen alle Ausnahmefehler, die während der Funktionsausführung ausgelöst werden, nur zu einem Fehler für den individuellen Aufruf, der eine Ausnahme ausgelöst hat. Das Schlüsselwort `await` bedeutet, dass Schritte, die auf `readFileAsync` folgen, erst nach Abschluss von `readFile` ausgeführt werden. Bei Verwendung von `async` und `await` ist es auch nicht erforderlich, den Rückruf `context.done()` aufzurufen.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

+ [Bewährte Methoden für Azure Functions](functions-best-practices.md)
+ [Entwicklerreferenz zu Azure Functions](functions-reference.md)
+ [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)

[Arbeiten mit Azure Functions Core Tools]: functions-run-local.md#project-file-deployment
