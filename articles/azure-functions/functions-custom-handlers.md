---
title: Benutzerdefinierte Azure Functions-Handler
description: Erfahren Sie, wie Sie Azure Functions mit einer beliebigen Sprache oder Laufzeitversion verwenden.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: 099f90ba8c5d9dabb6c4c505e50d8c077e3eaf0f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746028"
---
# <a name="azure-functions-custom-handlers"></a>Benutzerdefinierte Azure Functions-Handler

Jede Functions-App wird von einem sprachspezifischen Handler ausgeführt. Von Azure Functions werden zwar standardmäßig zahlreiche [Sprachhandler](./supported-languages.md) unterstützt, es gibt jedoch Fälle, in denen Sie möglicherweise andere Sprachen oder Runtimes verwenden möchten.

Benutzerdefinierte Handler sind schlanke Webserver, die Ereignisse vom Functions-Host empfangen. Jede Sprache, die HTTP-Primitive unterstützt, kann einen benutzerdefinierten Handler implementieren.

Benutzerdefinierte Handler eignen sich am besten für Situationen, in denen Sie Folgendes ausführen möchten:

- Implementieren einer Funktions-App in einer derzeit nicht unterstützten Sprache wie Go oder Rust.
- Implementieren einer Funktions-App in einer derzeit nicht unterstützten Runtime wie Deno

Mit benutzerdefinierten Handlern können Sie alle [Trigger sowie Ein- und Ausgabebindungen](./functions-triggers-bindings.md) über [Erweiterungspakete](./functions-bindings-register.md) verwenden.

Machen Sie sich mit den ersten Schritten mit benutzerdefinierten Azure Functions-Handlern mit [Schnellstarts in Go und Rust](create-first-function-vs-code-other.md) vertraut.

## <a name="overview"></a>Übersicht

Die folgende Abbildung zeigt die Beziehung zwischen dem Functions-Host und einem Webserver, der als benutzerdefinierter Handler implementiert ist.

![Benutzerdefinierte Azure Functions-Handler: Übersicht](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Jedes Ereignis löst eine Anforderung aus, die an den Functions-Host gesendet wird. Ein Ereignis ist ein beliebiger von Azure Functions unterstützter Trigger.
1. Der Functions-Host gibt eine [Anforderungsnutzlast](#request-payload) an den Webserver aus. Die Nutzlast enthält Trigger- und Eingabebindungsdaten sowie andere Metadaten für die Funktion.
1. Der Webserver führt die einzelne Funktion aus und gibt eine [Antwortnutzlast](#response-payload) an den Functions-Host zurück.
1. Der Functions-Host übergibt zur Verarbeitung Daten aus der Antwort an die Ausgabebindungen der Funktion.

Eine als benutzerdefinierter Handler implementierte Azure Functions-App muss die Dateien *host.json*, *local.settings.json* und *function.json* unter Berücksichtigung bestimmter Konventionen konfigurieren.

## <a name="application-structure"></a>Anwendungsstruktur

Um einen benutzerdefinierten Handler zu implementieren, benötigen Sie die folgenden Komponenten für Ihre Anwendung:

- Eine Datei *host.json* im Stammverzeichnis Ihrer App.
- Eine Datei namens *local.settings.json* im Stammverzeichnis Ihrer App.
- Eine Datei *function.json* für jede Funktion (in einem Ordner, der mit dem Funktionsnamen übereinstimmt).
- Einen Befehl, ein Skript oder eine ausführbare Datei zum Ausführen eines Webservers.

Das folgende Diagramm zeigt, wie diese Dateien für eine Funktion namens „MyQueueFunction“ und für eine ausführbare Datei eines benutzerdefinierten Handlers (*handler.exe*) im Dateisystem aussehen:

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Konfiguration

Die Anwendung wird über die Dateien *host.json* und *local.settings.json* konfiguriert.

#### <a name="hostjson"></a>host.json

*host.json* teilt dem Functions-Host mit, wohin Anforderungen gesendet werden sollen. Hierzu wird auf einen Webserver verwiesen, der HTTP-Ereignisse verarbeiten kann.

Ein benutzerdefinierter Handler wird definiert, indem die Datei *host.json* mit Details zum Ausführen des Webservers über den Abschnitt `customHandler` konfiguriert wird.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

Der Abschnitt `customHandler` verweist auf ein Ziel (wie durch `defaultExecutablePath`definiert). Das Ausführungsziel kann entweder ein Befehl, eine ausführbare Datei oder eine Datei sein, in der der Webserver implementiert wird.

Verwenden Sie das Array `arguments`, um Argumente an die ausführbare Datei zu übergeben. Argumente unterstützen die Erweiterung von Umgebungsvariablen (Anwendungseinstellungen) per `%%`-Notation.

Mit `workingDirectory` können Sie außerdem das von der ausführbaren Datei verwendete Arbeitsverzeichnis ändern.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Bindungsunterstützung

Standardtrigger sind zusammen mit Eingabe- und Ausgabebindungen verfügbar, indem Sie in der Datei *host.json* auf [Erweiterungspakete](./functions-bindings-register.md) verweisen.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.json* definiert Anwendungseinstellungen, die bei lokaler Ausführung der Funktions-App verwendet werden. Da *local.settings.json* Geheimnisse enthalten kann, sollte die Datei aus der Quellcodeverwaltung ausgeschlossen werden. Verwenden Sie in Azure stattdessen Anwendungseinstellungen.

Legen Sie `FUNCTIONS_WORKER_RUNTIME` in *local.settings.json* für benutzerdefinierte Handler auf `Custom` fest.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` wird in Linux Premium- oder App Service-Plänen möglicherweise nicht als gültige Runtime erkannt. Sollte dies Ihr Bereitstellungsziel sein, legen Sie `FUNCTIONS_WORKER_RUNTIME` auf eine leere Zeichenfolge fest.

### <a name="function-metadata"></a>Funktionsmetadaten

Bei Verwendung mit einem benutzerdefinierten Handler unterscheiden sich die Inhalte von *function.json* nicht von der Definition einer Funktion in einem beliebigen anderen Kontext. Die einzige Voraussetzung besteht darin, dass sich *function.json*-Dateien in einem Ordner befinden müssen, dessen Name mit dem Funktionsnamen übereinstimmt.

In der folgenden Instanz von *function.json* wird eine Funktion konfiguriert, die über einen Warteschlangentrigger und über eine Warteschlangen-Ausgabebindung verfügt. Da sie sich in einem Ordner namens *MyQueueFunction* befindet, definiert sie eine Funktion namens *MyQueueFunction*.

**MyQueueFunction/function.json**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Anforderungsnutzlast

Wenn eine Warteschlangennachricht eingeht, sendet der Functions-Host eine HTTP POST-Anforderung mit einer Nutzlast im Text an den benutzerdefinierten Handler.

Der folgende Code stellt eine Anforderungsbeispielnutzlast dar. Die Nutzlast umfasst eine JSON-Struktur mit zwei Elementen: `Data` und `Metadata`.

Das `Data`-Element enthält Schlüssel, die Eingabe- und Triggernamen entsprechen, wie im Bindungsarray in der Datei *function.json* definiert.

Das `Metadata`-Element umfasst [Metadaten, die aus der Ereignisquelle generiert](./functions-bindings-expressions-patterns.md#trigger-metadata) werden.

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Antwortnutzlast

Gemäß der Konvention werden Funktionsantworten als Schlüssel-Wert-Paare formatiert. Unterstützte Schlüssel sind:

| <nobr>Nutzlastschlüssel</nobr>   | Datentyp | Bemerkungen                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | Objekt (object)    | Enthält Antwortwerte gemäß Definition des `bindings`-Arrays in *function.json*.<br /><br />Ist eine Funktion also beispielsweise mit einer Warteschlangen-Ausgabebindung namens „myQueueOutput“ konfiguriert, enthält `Outputs` einen Schlüssel mit dem Namen `myQueueOutput`, der durch den benutzerdefinierten Handler für die an die Warteschlange gesendeten Nachrichten festgelegt wird. |
| `Logs`        | array     | Meldungen des Funktionsprotokolls werden in den Functions-Aufrufprotokollen angezeigt.<br /><br />Bei Ausführung in Azure werden Meldungen in Application Insights angezeigt. |
| `ReturnValue` | Zeichenfolge    | Wird verwendet, um eine Antwort bereitzustellen, wenn eine Ausgabe als `$return` in der Datei *function.json* konfiguriert wird. |

Hier sehen Sie ein Beispiel für eine Antwortnutzlast:

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Beispiele

Benutzerdefinierte Handler können in jeder Sprache implementiert werden, die den Empfang von HTTP-Ereignissen unterstützt. Die folgenden Beispiele zeigen die Implementierung eines benutzerdefinierten Handlers unter Verwendung der Programmiersprache Go.

### <a name="function-with-bindings"></a>Funktion mit Bindungen

Das in diesem Beispiel implementierte Szenario enthält eine Funktion namens `order`, die eine `POST`-Anforderung mit einer Nutzlast annimmt, die eine Produktbestellung darstellt. Wenn eine Bestellung an die Funktion gesendet wird, wird eine Queue Storage-Nachricht erstellt und eine HTTP-Antwort zurückgegeben.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementierung

In einem Ordner mit dem Namen *order* konfiguriert die Datei *function.json* die durch HTTP ausgelöste Funktion.

**order/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Diese Funktion wird als eine [durch HTTP ausgelöste Funktion](./functions-bindings-http-webhook-trigger.md) definiert, die eine [HTTP-Antwort](./functions-bindings-http-webhook-output.md) zurückgibt und eine [Queue Storage](./functions-bindings-storage-queue-output.md)-Nachricht ausgibt.

Im Stammverzeichnis der App ist die Datei *host.json* für die Ausführung einer ausführbaren Datei namens `handler.exe` (`handler` für Linux oder macOS) konfiguriert.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Hierbei handelt es sich um die an die Functions-Runtime gesendete HTTP-Anforderung.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Von der Functions-Runtime wird daraufhin die folgende HTTP-Anforderung an den benutzerdefinierten Handler gesendet:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Zur besseren Übersichtlichkeit wurden einige Teile der Nutzlast entfernt.

*handler.exe* ist das kompilierte Go-Programm für den benutzerdefinierten Handler, das einen Webserver ausführt und auf Funktionsaufrufanforderungen des Functions-Hosts reagiert.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

In diesem Beispiel führt der benutzerdefinierte Handler einen Webserver aus, um HTTP-Ereignisse zu verarbeiten, und er ist so konfiguriert, dass er an `FUNCTIONS_CUSTOMHANDLER_PORT` auf Anforderungen lauscht.

Obwohl der Functions-Host die ursprüngliche HTTP-Anforderung unter `/api/order` empfangen hat, ruft er den benutzerdefinierten Handler unter Verwendung des Funktionsnamens (zugehöriger Ordnername) auf. In diesem Beispiel ist die Funktion am Pfad `/order` definiert. Der Host sendet dem benutzerdefinierten Handler eine HTTP-Anforderung am Pfad `/order`.

Da Anforderungen vom Typ `POST` an diese Funktion gesendet werden, sind die Triggerdaten und die Funktionsmetadaten über den HTTP-Anforderungstext verfügbar. Auf den ursprünglichen HTTP-Anforderungstext kann im Datenanforderungstext (`Data.req.Body`) der Nutzlast zugegriffen werden.

Die Antwort der Funktion wird in Form von Schlüssel-Wert-Paaren formatiert. Das Element `Outputs` enthält dabei einen JSON-Wert, bei dem die Schlüssel den in der Datei *function.json* definierten Ausgaben entsprechen.

Hier sehen Sie ein Beispiel für eine Nutzlast, die von diesem Handler an den Functions-Host zurückgegeben wird:

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Wenn Sie das Element `message` der Ausgabe auf die Bestelldaten aus der Anforderung festlegen, gibt die Funktion diese Bestelldaten an die konfigurierte Warteschlange aus. Der Functions-Host gibt auch die in `res` konfigurierte HTTP-Antwort an den Aufrufer zurück.

### <a name="http-only-function"></a>Reine HTTP-Funktion

Bei Funktionen mit HTTP-Trigger und ohne zusätzliche Bindungen oder Ausgaben soll Ihr Handler möglicherweise direkt mit der HTTP-Anforderung und -Antwort arbeiten anstatt mit den Nutzlasten von [Anforderung](#request-payload) und [Antwort](#response-payload) des benutzerdefinierten Handlers. Dieses Verhalten kann mithilfe der Einstellung `enableForwardingHttpRequest` in *host.json* konfiguriert werden.

> [!IMPORTANT]
> Der Hauptzweck benutzerdefinierter Handler besteht darin, die Verwendung von Sprachen und Runtimes zu ermöglichen, für die derzeit keine erstklassige Unterstützung in Azure Functions zur Verfügung steht. Webanwendungen können zwar ggf. mit benutzerdefinierten Handlern ausgeführt werden, Azure Functions ist jedoch kein standardmäßiger Reverseproxy. Einige Features wie Antwortstreaming, HTTP/2 und WebSockets stehen nicht zur Verfügung. Manche Komponenten der HTTP-Anforderung wie bestimmte Header und Routen sind möglicherweise eingeschränkt. Bei Ihrer Anwendung kann es auch zu übermäßigen [Kaltstarts](functions-scale.md#cold-start) kommen.
>
> Zur Vermeidung dieser Punkte empfiehlt es sich gegebenenfalls, Ihre Web-Apps in [Azure App Service](../app-service/overview.md) auszuführen.

Im folgenden Beispiel wird veranschaulicht, wie eine durch HTTP ausgelöste Funktion ohne zusätzliche Bindungen oder Ausgaben konfiguriert wird. Das in diesem Beispiel implementierte Szenario enthält eine Funktion namens `hello`, die eine `GET`- oder `POST`-Anforderungen annimmt.

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementierung

In einem Ordner namens *hello* konfiguriert die Datei *function.json* die durch HTTP ausgelöste Funktion.

**hello/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Die Funktion ist so konfiguriert, dass sowohl `GET`- als auch `POST`-Anforderungen akzeptiert werden, und der Ergebniswert wird über ein Argument namens `res` bereitgestellt.

Im Stammverzeichnis der App ist die Datei *host.json* für die Ausführung von `handler.exe` konfiguriert, und `enableForwardingHttpRequest` ist auf `true` festgelegt.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Wenn `enableForwardingHttpRequest` auf `true` festgelegt ist, unterscheidet sich das Verhalten reiner HTTP-Funktionen wie folgt vom Standardverhalten benutzerdefinierter Handler:

* Die [Anforderungsnutzlast](#request-payload) des benutzerdefinierten Handlers ist nicht in der HTTP-Anforderung enthalten. Stattdessen ruft der Functions-Host den Handler mit einer Kopie der ursprünglichen HTTP-Anforderung auf.
* Der Functions-Host ruft den Handler mit dem gleichen Pfad auf wie die ursprüngliche Anforderung (einschließlich aller Abfragezeichenfolgenparameter).
* Der Functions-Host gibt eine Kopie der HTTP-Antwort des Handlers als Antwort auf die ursprüngliche Anforderung zurück.

Im Anschluss sehen Sie eine an den Functions-Host gerichtete POST-Anforderung. Der Functions-Host sendet daraufhin eine Kopie der Anforderung an den benutzerdefinierten Handler am gleichen Pfad.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Die Datei *handler.go* implementiert einen Webserver und eine HTTP-Funktion.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

In diesem Beispiel erstellt der benutzerdefinierte Handler einen Webserver, um HTTP-Ereignisse zu verarbeiten, und er ist so konfiguriert, dass er an `FUNCTIONS_CUSTOMHANDLER_PORT` auf Anforderungen lauscht.

Zur Behandlung von `GET`-Anforderungen wird eine Zeichenfolge zurückgegeben, und `POST`-Anforderungen haben Zugriff auf den Anforderungstext.

Die Route für die Bestellfunktion lautet in diesem Fall `/api/hello` und ist somit identisch mit der ursprünglichen Anforderung.

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT` ist nicht der öffentliche Port, mit dem die Funktion aufgerufen wird. Dieser Port wird vom Functions-Host verwendet, um den benutzerdefinierten Handler aufzurufen.

## <a name="deploying"></a>Wird bereitgestellt

Ein benutzerdefinierter Handler kann für jede Azure Functions-Hostingoption bereitgestellt werden. Falls Ihr Handler benutzerdefinierte Betriebssystem- oder Plattformabhängigkeiten erfordert (etwa eine Sprachruntime), muss ggf. ein [benutzerdefinierter Container](./functions-create-function-linux-custom-image.md) verwendet werden.

Wenn Sie eine Funktions-App in Azure für benutzerdefinierte Handler erstellen, empfiehlt es sich, „.NET Core“ als Stapel auszuwählen. Für benutzerdefinierte Handler wird später noch ein Stapel vom Typ „Benutzerdefiniert“ hinzugefügt.

Zum Bereitstellen einer benutzerdefinierten Handler-App mit den Azure Functions Core Tools führen Sie den folgenden Befehl aus.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Vergewissern Sie sich, dass alle Dateien, die zum Ausführen des benutzerdefinierten Handlers erforderlich sind, im Ordner vorhanden sind und in die Bereitstellung einbezogen werden. Falls Ihr benutzerdefinierter Handler eine ausführbare Binärdatei ist oder plattformspezifische Abhängigkeiten aufweist, stellen Sie sicher, dass diese Dateien der Zielbereitstellungsplattform entsprechen.

## <a name="restrictions"></a>Beschränkungen

- Der Webserver des benutzerdefinierten Handlers muss innerhalb von 60 Sekunden starten.

## <a name="samples"></a>Beispiele

Beispiele für die Implementierung von Funktionen in einer Vielzahl verschiedener Sprachen finden Sie im [GitHub-Repository mit Beispielen für benutzerdefinierte Handler](https://github.com/Azure-Samples/functions-custom-handlers).

## <a name="troubleshooting-and-support"></a>Problembehandlung und Support

### <a name="trace-logging"></a>Ablaufprotokollierung

Falls Ihr benutzerdefinierter Handlerprozess nicht gestartet wird oder Probleme bei der Kommunikation mit dem Functions-Host auftreten, können Sie den Protokolliergrad der Funktions-App auf `Trace` erhöhen, um weitere Diagnosemeldungen des Hosts anzuzeigen.

Konfigurieren Sie zum Ändern des Standardprotokolliergrads der Funktions-App die Einstellung `logLevel` im Abschnitt `logging` der Datei *host.json*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Der Functions-Host gibt zusätzliche Protokollmeldungen mit Informationen zum benutzerdefinierten Handlerprozess aus. Verwenden Sie die Protokolle, um Probleme beim Starten Ihres benutzerdefinierten Handlerprozesses oder beim Aufrufen von Funktionen in Ihrem benutzerdefinierten Handler zu untersuchen.

Lokal werden Protokolle in der Konsole ausgegeben.

In Azure können Sie [Application Insights-Ablaufverfolgungen abfragen](analyze-telemetry-data.md#query-telemetry-data), um die Protokollmeldungen anzuzeigen. Falls von Ihrer App eine große Menge an Protokollen erzeugt wird, wird nur ein Teil der Protokollmeldungen an Application Insights gesendet. [Deaktivieren Sie das Sampling](configure-monitoring.md#configure-sampling), um sicherzustellen, dass alle Meldungen protokolliert werden.

### <a name="test-custom-handler-in-isolation"></a>Isoliertes Testen eines benutzerdefinierten Handlers

Bei Apps mit benutzerdefiniertem Handler handelt es sich um einen Webserverprozess. Daher kann es hilfreich sein, sie separat zu starten und Funktionsaufrufe zu testen, indem Sie mithilfe eines Tools wie [cURL](https://curl.haxx.se/) oder [Postman](https://www.postman.com/) simulierte [HTTP-Anforderungen](#request-payload) senden.

Diese Strategie können Sie auch in Ihren CI/CD-Pipelines verwenden, um automatisierte Tests für Ihren benutzerdefinierten Handler durchzuführen.

### <a name="execution-environment"></a>Ausführungsumgebung

Benutzerdefinierte Handler werden in der gleichen Umgebung ausgeführt wie eine typische Azure Functions-App. Testen Sie Ihren Handler, um sich zu vergewissern, dass die Umgebung alle für die Ausführung erforderlichen Abhängigkeiten enthält. Apps, die zusätzliche Abhängigkeiten erfordern, müssen ggf. unter Verwendung eines [benutzerdefinierten Containerimages](functions-create-function-linux-custom-image.md) ausgeführt werden, das im [Premium-Plan](functions-premium-plan.md) von Azure Functions gehostet wird.

### <a name="get-support"></a>Support aufrufen

Sollten Sie Hilfe im Zusammenhang mit einer Funktions-App mit benutzerdefinierten Handlern benötigen, können Sie eine Anfrage über die reguläre Supportkanäle stellen. Aufgrund des breiten Spektrums an möglichen Sprachen, die zur Erstellung von Apps mit benutzerdefinierten Handlern verwendet werden können, unterliegt der Support jedoch gewissen Einschränkungen.

Support wird bereitgestellt, wenn der Functions-Host Probleme beim Start oder bei der Kommunikation mit dem benutzerdefinierten Handlerprozess hat. Bei spezifischen Problemen im Zusammenhang mit Komponenten Ihres benutzerdefinierten Handlerprozesses (beispielsweise bei Problemen mit der gewählten Sprache oder dem gewählten Framework) kann Ihnen unser Supportteam in diesem Kontext leider nicht weiterhelfen.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte beim Entwickeln einer Azure Functions-App in Go oder Rust mit dem [Schnellstart für benutzerdefinierte Handler](create-first-function-vs-code-other.md).
