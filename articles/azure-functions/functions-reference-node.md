---
title: JavaScript-Entwicklerreferenz zu Azure Functions | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von JavaScript Funktionen entwickeln können.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, Ereignisverarbeitung, Webhooks, dynamisches Compute, serverlose Architektur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 635e72a8e8a70b8885afea282511fbfaf24d2f94
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957339"
---
# <a name="azure-functions-javascript-developer-guide"></a>JavaScript-Entwicklerhandbuch für Azure Functions

Dieses Handbuch enthält Informationen zu den Feinheiten des Schreibens von Azure Functions mit JavaScript.

Eine JavaScript-Funktion ist eine exportierte `function`, die ausgeführt wird, wenn sie ausgelöst wird ([Trigger werden in „function.json“ konfiguriert](functions-triggers-bindings.md)). Das erste Argument, das an jede Funktion übergeben wird, ist ein `context`-Objekt, das zum Empfangen und Senden von Bindungsdaten, für die Protokollierung und für die Kommunikation mit der Runtime verwendet wird.

In diesem Artikel wird davon ausgegangen, dass Sie bereits die [Entwicklerreferenz zu Azure Functions](functions-reference.md)gelesen haben. Führen Sie den Schnellstart zu Azure Functions durch, um Ihre erste Funktion mit [Visual Studio Code](functions-create-first-function-vs-code.md) oder [im Portal](functions-create-first-azure-function.md) zu erstellen.

Dieser Artikel eignet sich auch für die [App-Entwicklung mit TypeScript](#typescript).

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

Die in [Version 2.x](functions-versions.md) der Functions-Runtime erforderlichen Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, die eigentlichen Bibliotheksdateien befinden sich im Ordner `bin`. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Wenn Sie Funktionen im Azure-Portal entwickeln, wird diese Registrierung für Sie ausgeführt.

## <a name="exporting-a-function"></a>Exportieren einer Funktion

JavaScript-Funktionen müssen über [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (oder [`exports`](https://nodejs.org/api/modules.html#modules_exports)) exportiert werden. Die exportierte Funktion muss eine JavaScript-Funktion sein, die ausgeführt wird, wenn sie ausgelöst wird.

Standardmäßig sucht die Functions-Runtime in `index.js` nach Ihrer Funktion, wobei sich `index.js` im gleichen übergeordneten Verzeichnis befindet wie die entsprechende Datei `function.json`. Im Standardfall sollte Ihre exportierte Funktion der einzige Export aus der zugehörigen Datei oder der Export mit dem Namen `run` oder `index` sein. Um den Dateispeicherort zu konfigurieren und den Namen Ihrer Funktion zu exportieren, lesen Sie weiter unten die Beschreibung zum [Konfigurieren des Einstiegspunkts Ihrer Funktion](functions-reference-node.md#configure-function-entry-point).

An die exportierte Funktion wird bei der Ausführung eine Reihe von Argumenten übergeben. Das erste angenommene Argument ist immer ein `context`-Objekt. Wenn Ihre Funktion synchron ist (d.h. keine Zusage zurückgibt), muss das `context`-Objekt übergeben werden, da der Aufruf von `context.done` zur korrekten Verwendung erforderlich ist.

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
   
 - **Als Eingaben unter Verwendung des JavaScript-Objekts [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx).** Dies entspricht im Wesentlichen dem Übergeben von Eingaben als Parameter, ermöglicht jedoch die dynamische Verarbeitung der Eingaben.
 
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
Die Laufzeit verwendet ein `context`-Objekt, um Daten an Ihre und von Ihrer Funktion zu übergeben und Ihnen die Kommunikation mit der Laufzeit zu ermöglichen. Das context-Objekt kann zum Lesen und Festlegen von Daten von Bindungen, zum Schreiben von Protokollen und für den `context.done`-Rückruf verwendet werden, wenn die exportierte Funktion asynchron ist.

Das `context`-Objekt ist immer der erste Parameter in einer Funktion. Es sollte angegeben werden, da es wichtige Methoden wie `context.done` und `context.log` enthält. Sie können dem Objekt einen beliebigen Namen geben (also etwa `ctx` oder `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
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

Gibt ein benanntes Objekt zurück, das Triggermetadaten und Funktionsaufrufdaten (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`) enthält. Ein Beispiel für Triggermetadaten finden Sie in diesem [Event Hubs-Beispiel](functions-bindings-event-hubs.md#trigger---javascript-example).

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

Ermöglicht das Schreiben in die Streamingfunktionsprotokolle auf Standard-Ablaufverfolgungsebene. Es sind zusätzliche Protokollierungsmethoden in `context.log` verfügbar, mit denen Sie auf anderen Ablaufverfolgungsebenen in das Funktionsprotokoll schreiben können:


| Methode                 | BESCHREIBUNG                                |
| ---------------------- | ------------------------------------------ |
| **Fehler(_Meldung_)**   | Schreibt in Protokollierung auf Fehlerebene oder niedriger.   |
| **warn(_Meldung_)**    | Schreibt in Protokollierung auf Warnungsebene oder niedriger. |
| **info(_Meldung_)**    | Schreibt in Protokollierung auf Informationsebene oder niedriger.    |
| **verbose(_Meldung_)** | Schreibt in Protokollierung auf ausführlicher Ebene.           |

Das folgende Beispiel schreibt auf Warnungs-Ablaufverfolgungsebene ein Protokoll:

```javascript
context.log.warn("Something has happened."); 
```

Sie können den [Ablaufverfolgungsebenen-Schwellenwert für die Protokollierung](#configure-the-trace-level-for-console-logging) in der Datei „host.json“ konfigurieren. Weitere Informationen zum Schreiben von Protokollen finden Sie unter [Schreiben von Ablaufverfolgungsausgaben ](#writing-trace-output-to-the-console) weiter unten.

Lesen Sie [Überwachen von Azure Functions](functions-monitoring.md), um weitere Informationen zum Anzeigen und Abfragen von Funktionsprotokollen zu erhalten.

## <a name="writing-trace-output-to-the-console"></a>Schreiben der Ablaufverfolgungsausgabe in die Konsole 

In Functions verwenden Sie die `context.log`-Methoden, um die Ablaufverfolgungsausgabe in die Konsole zu schreiben. In Functions v2.x werden Ablaufverfolgungsausgaben über `console.log` auf der Ebene der Funktionen-App erfasst. Dies bedeutet, dass Ausgaben von `console.log` nicht an einen bestimmten Funktionsaufruf gebunden sind und nicht in den Protokollen einer bestimmten Funktion angezeigt werden. Sie werden jedoch an Application Insights weitergegeben. In Functions v1.x können Sie `console.log` nicht verwenden, um in die Konsole zu schreiben.

Beim Aufruf von `context.log()` wird Ihre Meldung auf der Standard-Ablaufverfolgungsebene, also der _info_-Ablaufverfolgungsebene, in die Konsole geschrieben. Das folgende Beispiel schreibt auf der „info“-Ablaufverfolgungsebene in die Konsole:

```javascript
context.log({hello: 'world'});  
```

Dieser Code entspricht dem Code oben:

```javascript
context.log.info({hello: 'world'});  
```

Dieser Code schreibt auf der Fehlerebene in die Konsole:

```javascript
context.log.error("An error has occurred.");  
```

Da _error_ die höchste Ablaufverfolgungsebene ist, wird diese Ablaufverfolgung auf allen Ablaufverfolgungsebenen in die Ausgabe geschrieben, solange die Protokollierung aktiviert ist.

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

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurieren der Ablaufverfolgungsebene für die Konsolenprotokollierung

Mit Functions 1.x können Sie den Ablaufverfolgungsebenen-Schwellenwert zum Schreiben in die Konsole definieren, sodass Sie mühelos mit Ihrer Funktion steuern können, wie Ablaufverfolgungen in die Konsole geschrieben werden. Legen Sie mit der `tracing.consoleLevel`-Eigenschaft in der Datei „host.json“ den Schwellenwert für alle Ablaufverfolgungen fest, die in die Konsole geschrieben werden. Diese Einstellung gilt für alle Funktionen in Ihrer Funktionen-App. Im folgenden Beispiel wird der Schwellenwert für die Ablaufverfolgung festgelegt, um die ausführliche Protokollierung zu aktivieren:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Die Werte von **consoleLevel** entsprechen den Namen der `context.log`-Methoden. Um die gesamte Ablaufverfolgungsprotokollierung in der Konsole zu deaktivieren, setzen Sie **consoleLevel** auf _off_. Weitere Informationen finden Sie in der [host.json-Referenz](functions-host-json-v1.md).

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

### <a name="accessing-the-request-and-response"></a>Zugreifen auf Anforderung und Antwort 

Beim Arbeiten mit HTTP-Triggern bestehen verschiedene Möglichkeiten, auf die HTTP-Anforderungsobjekte und -Antwortobjekte zuzugreifen:

+ **Über die `req`- und `res`-Eigenschaft des `context`-Objekts.** Auf diese Weise können Sie die herkömmlichen Muster für den Zugriff auf HTTP-Daten über das context-Objekt verwenden, anstatt das gesamte `context.bindings.name`-Muster verwenden zu müssen. Das folgende Beispiel veranschaulicht den Zugriff auf das `req`- und `res`-Objekt des `context`-Objekts:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
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

+ **_[Nur Antwort]_ Durch Aufrufen von `context.done()`.** Eine besondere Art der HTTP-Bindung gibt die Antwort zurück, die der `context.done()`-Methode übergeben wird. Die folgende HTTP-Ausgabebindung definiert einen `$return`-Ausgabeparameter:

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

## <a name="node-version"></a>Node-Version

Die folgende Tabelle zeigt die jeweilige von den Hauptversionen von Functions Runtime verwendete Node.js-Version:

| Functions-Version | Node.js-Version | 
|---|---|
| 1.x | 6.11.2 (durch die Laufzeit gesperrt) |
| 2.x  | Node.js.-Version _Active LTS_ und geradzahlige Node.js-Version _Current_ (8.11.1 und 10.14.1 empfohlen). Legen Sie die Version mithilfe der [App-Einstellung](functions-how-to-use-azure-function-app-settings.md#settings) „WEBSITE_NODE_DEFAULT_VERSION“ fest.|

Sie können die aktuelle Version anzeigen, die die Laufzeit verwendet, indem Sie die oben gezeigte App-Einstellung überprüfen oder `process.version` aus einer beliebigen Funktion ausgeben.

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
1. Wechseln Sie zur Adresse `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicken Sie auf **Debugkonsole** > **CMD**.

3. Gehen Sie zu `D:\home\site\wwwroot`, und ziehen Sie dann die Datei „package.json“ auf den **wwwroot**-Ordner in der oberen Hälfte der Seite.  
    Es gibt auch andere Möglichkeiten, Dateien in Ihre Funktionen-App hochzuladen. Weitere Informationen finden Sie unter [Aktualisieren von Funktionen-App-Dateien](functions-reference.md#fileupdate). 

4. Sobald die Datei „package.json“ hochgeladen ist, führen Sie den `npm install`-Befehl in der **Kudu-Remoteausführungskonsole** aus.  
    Mit dieser Aktion werden die in der Datei „package.json“ angegebenen Pakete heruntergeladen und die Funktionen-App neu gestartet.

## <a name="environment-variables"></a>Umgebungsvariablen

In Functions werden [App-Einstellungen](functions-app-settings.md), z.B. Dienstverbindungszeichenfolgen, während der Ausführung als Umgebungsvariablen verfügbar gemacht. Auf diese Einstellungen können Sie über `process.env` zugreifen, wie dies hier in der `GetEnvironmentVariable`-Funktion gezeigt wird:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Wenn App-Einstellungen lokal ausgeführt werden, werden sie über die Projektdatei [local.settings.json](functions-run-local.md#local-settings-file) gelesen.

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

Fügen Sie unter `Values` in der Datei [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) `"languageWorkers:node:arguments": "--inspect=5858"` hinzu, und fügen Sie einen Debugger an Port 5858 an, um lokal zu debuggen.

Wenn Sie mit VS Code debuggen, wird der Parameter `--inspect` automatisch mit dem Wert `port` in der Datei „launch.json“ des Projekts hinzugefügt.

In Version 1.x funktioniert die Einstellung `languageWorkers:node:arguments` nicht. Sie können den Debugport mit dem Parameter [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) in den Azure Functions Core Tools festlegen.

## <a name="typescript"></a>TypeScript

Wenn Sie Version 2.x der Azure Functions-Runtime als Ziel verwenden, können Sie mit der [Azure Functions-Erweiterung für Visual Studio Code](functions-create-first-function-vs-code.md) und den [Azure Functions Core Tools](functions-run-local.md) Funktions-Apps mit Vorlagen erstellen, die Funktions-App-Projekte in TypeScript unterstützen. Diese Vorlage generiert `package.json`- und `tsconfig.json`-Projektdateien, mit denen Sie JavaScript-Funktionen aus TypeScript-Code leichter mithilfe dieser Tools transpilieren, ausführen und veröffentlichen können.

Die generierte `.funcignore`-Datei wird verwendet, um anzugeben, welche Dateien ausgeschlossen werden sollen, wenn ein Projekt in Azure veröffentlicht wird.  

TypeScript-Dateien (.ts) werden im Ausgabeverzeichnis `dist` in JavaScript-Dateien (.js) transpiliert. TypeScript-Dateien verwenden in `function.json` den [Parameter `scriptFile`](#using-scriptfile), um den Speicherort der entsprechenden JS-Datei im Ordner `dist` anzugeben. Der Ausgabespeicherort wird von der Vorlage mit dem Parameter `outDir` in der Datei `tsconfig.json` festgelegt. Wenn Sie diese Einstellung oder den Namen des Ordners ändern, kann die Runtime den auszuführenden Code nicht finden.

> [!NOTE]
> In Version 1.x der Azure Functions-Runtime ist die experimentelle Unterstützung von TypeScript vorhanden. Die experimentelle Version transpiliert TypeScript-Dateien in JavaScript-Dateien, wenn die Funktion aufgerufen wird. In Version 2.x wurde diese experimentelle Unterstützung durch eine toolgesteuerte Methode ersetzt, die die Transpilierung vor der Initialisierung des Hosts und während der Bereitstellung durchführt.

Die Art der lokalen Entwicklung und Bereitstellung aus einem TypeScript-Projekt hängen von Ihrem Entwicklungstool ab.

### <a name="visual-studio-code"></a>Visual Studio Code

Mit der [Azure Functions-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) können Sie Ihre Funktionen mit TypeScript entwickeln. Für die Azure Functions-Erweiterung sind die Azure Functions Core Tools erforderlich.

Wählen Sie einfach `TypeScript` aus, wenn Sie eine Funktions-App erstellen und die Sprache festlegen, um eine TypeScript-Funktions-App in Visual Studio Code zu erstellen.

Wenn Sie auf **F5** drücken, um die App lokal auszuführen, wird die Transpilierung durchgeführt, bevor der Host („func.exe“) initialisiert wird. 

Wenn Sie Ihre Funktions-App mit **Deploy to function app...** (In Funktions-App bereitstellen...) in Azure bereitstellen, generiert die Azure Functions-Erweiterung zunächst aus den TypeScript-Quelldateien einen produktionsbereiten Build aus JavaScript-Dateien.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Wenn Sie ein TypeScript-Funktions-App-Projekt mit den Core Tools erstellen möchten, müssen Sie die Sprache auf TypeScript festlegen, wenn Sie die Funktions-App erstellen. Wählen Sie dazu eine der folgenden Methoden:

- Führen Sie den Befehl `func init` aus, wählen Sie `node` als Sprachstapel, und wählen Sie dann `typescript`.

- Führen Sie den Befehl `func init --worker-runtime typescript` aus.

Wenn Sie den Code Ihrer Funktions-App lokal mit den Core Tools ausführen möchten, verwenden Sie den Befehl `npm start` statt `func host start`. Der Befehl `npm start` entspricht den folgenden Befehlen:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Bevor Sie den Befehl [`func azure functionapp publish`] für die Bereitstellung in Azure verwenden, müssen Sie zunächst den Befehl `npm run build:production` ausführen. Dieser Befehl erstellt aus den TypeScript-Quelldateien einen produktionsbereiten Build aus JavaScript-Dateien, der mit [`func azure functionapp publish`] bereitgestellt werden kann.

## <a name="considerations-for-javascript-functions"></a>Überlegungen zu JavaScript-Funktionen

Beachten Sie beim Arbeiten mit JavaScript-Funktionen die Überlegungen in den folgenden Abschnitten.

### <a name="choose-single-vcpu-app-service-plans"></a>Auswählen von App Service-Plänen mit einzelner vCPU

Wenn Sie eine Funktions-App erstellen, die den App Service-Plan verwendet, sollten Sie statt eines Plans mit mehreren vCPUs einen Plan mit einer einzelnen vCPU auswählen. Derzeit führt Functions JavaScript-Funktionen auf virtuellen Computern mit einer einzelnen vCPU effizienter aus. Die Verwendung größerer virtueller Computer führt nicht zu den erwarteten Leistungsverbesserungen. Bei Bedarf können Sie manuell horizontal hochskalieren, indem Sie weitere Instanzen virtueller Computer mit einer einzelnen vCPU hinzufügen. Sie können aber auch die automatische Skalierung aktivieren. Weitere Informationen finden Sie unter [Manuelles oder automatisches Skalieren der Instanzenzahl](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Kaltstart

Bei der Entwicklung von Azure Functions im serverlosen Hostingmodell sind Kaltstarts Realität. Der Begriff *Kaltstart* bezieht sich auf die Tatsache, dass es beim ersten Start Ihrer Funktions-App nach einer Zeit der Inaktivität länger dauert, bis sie gestartet wird. Insbesondere bei JavaScript-Funktionen mit großen Abhängigkeitsbäumen kann dies erheblich länger dauern. Nach Möglichkeit sollten Sie [die Funktionen als Paketdatei ausführen](run-functions-from-deployment-package.md), um den Prozess des Kaltstarts zu beschleunigen. Bei vielen Bereitstellungsmethoden erfolgt die Ausführung standardmäßig über das Paketmodell. Wenn aber umfangreiche Kaltstarts durchgeführt werden und die Ausführung nicht auf diese Weise erfolgt, kann diese Änderung eine wesentliche Verbesserung bewirken.

### <a name="connection-limits"></a>Verbindungsbeschränkungen

Wenn Sie einen dienstabhängigen Client in einer Azure Functions-Anwendung verwenden, erstellen Sie nicht bei jedem Funktionsaufruf einen neuen Client. Erstellen Sie stattdessen einen einzelnen, statischen Client im globalen Bereich. Weitere Informationen finden Sie unter [Verwalten von Verbindungen in Azure Functions](manage-connections.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

+ [Bewährte Methoden für Azure Functions](functions-best-practices.md)
+ [Entwicklerreferenz zu Azure Functions](functions-reference.md)
+ [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)

[Arbeiten mit Azure Functions Core Tools]: functions-run-local.md#project-file-deployment
