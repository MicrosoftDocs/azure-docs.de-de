---
title: Azure Event Grid-Trigger für Azure Functions
description: Erfahren Sie, wie Sie Code ausführen, wenn Event Grid-Ereignisse in Azure Functions versendet werden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 0e2e09bc72991330ccdec7a35400460cbeba26fc
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327031"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid-Trigger für Azure Functions

Der Funktionstrigger kann verwendet werden, um auf ein Ereignis zu reagieren, das an ein Event Grid-Thema gesendet wird.

Informationen zu Setup- und Konfigurationsdetails finden Sie in der [Übersicht](./functions-bindings-event-grid.md).

## <a name="example"></a>Beispiel

# <a name="c"></a>[C#](#tab/csharp)

Ein Beispiel für einen HTTP-Trigger finden Sie unter [Empfangen von Ereignissen an einem HTTP-Endpunkt](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C# (ab 2.x)

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md) für die Bindung an `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Weitere Informationen finden Sie unter Pakete, [Attribute](#attributes-and-annotations), [Konfiguration](#configuration) und [Verwendung](#usage).

### <a name="version-1x"></a>Version 1.x

Das folgende Beispiel zeigt eine Functions 1.x-[C#-Funktion](functions-dotnet-class-library.md) für die Bindung an `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Das folgende Beispiel zeigt eine Triggerbindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet.

Bindungsdaten in der Datei *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Version 2.x und höher

Hier sehen Sie ein Beispiel mit einer Bindung an `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Weitere Informationen finden Sie unter Pakete, [Attribute](#attributes-and-annotations), [Konfiguration](#configuration) und [Verwendung](#usage).

### <a name="version-1x"></a>Version 1.x

Functions 1.x-C#-Skriptcode für die Bindung an `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Beispiel zeigt eine Triggerbindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet.

Bindungsdaten in der Datei *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Das folgende Beispiel zeigt eine Triggerbindung in einer Datei *function.json* sowie eine [Python-Funktion](functions-reference-python.md), die die Bindung verwendet.

Bindungsdaten in der Datei *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Dies ist der Python-Code:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="java"></a>[Java](#tab/java)

Dieser Abschnitt enthält folgende Beispiele:

* [Event Grid-Trigger, String-Parameter](#event-grid-trigger-string-parameter)
* [Event Grid-Trigger, POJO-Parameter](#event-grid-trigger-pojo-parameter)

Die folgenden Beispiele zeigen die Triggerbindung in [Java](functions-reference-java.md), das die Bindung verwendet und ein Ereignis ausgibt. Dabei erhalten sie das Ereignis erst als `String` und dann als POJO.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid-Trigger, String-Parameter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid-Trigger, POJO-Parameter

In diesem Beispiel werden die folgenden POJO verwendet, die die Eigenschaften der obersten Ebene eines Event Grid-Ereignisses darstellen:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Beim Eingang wird die JSON-Nutzlast des Ereignisses in das ```EventSchema```-POJO deserialisiert, damit dieses von der Funktion verwendet werden kann. Durch diesen Prozess kann die Funktion auf die Ereigniseigenschaften auf objektorientierte Weise zugreifen.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Verwenden Sie die `EventGridTrigger`-Anmerkung in der [Laufzeitbibliothek für Java-Funktionen](/java/api/overview/azure/functions/runtime) für Parameter, deren Wert von Event Grid empfangen wird. Parameter mit diesen Anmerkungen führen dazu, dass die Funktion ausgeführt wird, wenn ein Ereignis empfangen wird.  Diese Anmerkung kann mit nativen Java-Typen, POJOs oder Werten mit `Optional<T>`, die NULL-Werte annehmen können, verwendet werden.

---

## <a name="attributes-and-annotations"></a>Attribute und Anmerkungen

# <a name="c"></a>[C#](#tab/csharp)

In [C#-Klassenbibliotheken](functions-dotnet-class-library.md) verwenden Sie das [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)-Attribut.

Dies ist ein `EventGridTrigger`-Attribut in einer Methodensignatur:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter „C#-Beispiel“.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

Attribute werden von C#-Skript nicht unterstützt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribute werden von JavaScript nicht unterstützt.

# <a name="python"></a>[Python](#tab/python)

Attribute werden von Python nicht unterstützt.

# <a name="java"></a>[Java](#tab/java)

Mit der [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java)-Anmerkung können Sie eine Event Grid-Bindung deklarativ konfigurieren, indem Sie Konfigurationswerte bereitstellen. Weitere Details finden Sie in den Abschnitten [Beispiel](#example) und [Konfiguration](#configuration).

---

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* festlegen. Im Attribut `EventGridTrigger` müssen keine Konstruktorparameter oder -eigenschaften festgelegt werden.

|Eigenschaft von „function.json“ |BESCHREIBUNG|
|---------|---------|
| **type** | Erforderlich – muss auf `eventGridTrigger` festgelegt sein. |
| **direction** | Erforderlich – muss auf `in` festgelegt sein. |
| **name** | Erforderlich – der Variablenname, der im Funktionscode für den Parameter verwendet wird, der die Ereignisdaten empfängt. |

## <a name="usage"></a>Verwendung

# <a name="c"></a>[C#](#tab/csharp)

In Azure Functions 1.x können Sie die folgenden Parametertypen für den Event Grid-Trigger verwenden:

* `JObject`
* `string`

In Azure Functions 2.x und höher können Sie optional auch den folgenden Parametertyp für den Event Grid-Trigger verwenden:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` definiert Eigenschaften für die Felder, die für alle Ereignistypen gelten.

> [!NOTE]
> Wenn Sie in Functions v1 versuchen, eine Bindung an `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` herzustellen, zeigt der Compiler eine „Veraltet“-Meldung an, und weist Sie an, stattdessen `Microsoft.Azure.EventGrid.Models.EventGridEvent` zu verwenden. Verweisen Sie zum Verwenden des neueren Typs auf das [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)-NuGet-Paket, und qualifizieren Sie den `EventGridEvent`-Typnamen vollständig, indem Sie ihm das Präfix `Microsoft.Azure.EventGrid.Models` voranstellen.

# <a name="c-script"></a>[C#-Skript](#tab/csharp-script)

In Azure Functions 1.x können Sie die folgenden Parametertypen für den Event Grid-Trigger verwenden:

* `JObject`
* `string`

In Azure Functions 2.x und höher können Sie optional auch den folgenden Parametertyp für den Event Grid-Trigger verwenden:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` definiert Eigenschaften für die Felder, die für alle Ereignistypen gelten.

> [!NOTE]
> Wenn Sie in Functions v1 versuchen, eine Bindung an `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` herzustellen, zeigt der Compiler eine „Veraltet“-Meldung an, und weist Sie an, stattdessen `Microsoft.Azure.EventGrid.Models.EventGridEvent` zu verwenden. Verweisen Sie zum Verwenden des neueren Typs auf das [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)-NuGet-Paket, und qualifizieren Sie den `EventGridEvent`-Typnamen vollständig, indem Sie ihm das Präfix `Microsoft.Azure.EventGrid.Models` voranstellen. Informationen zum Verweisen auf NuGet-Pakete in einer C#-Skriptfunktion finden Sie unter [Verwenden von NuGet-Paketen](functions-reference-csharp.md#using-nuget-packages).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Die Event Grid-Instanz ist über den Parameter verfügbar, der in der `name`-Eigenschaft der *function.json*-Datei konfiguriert ist.

# <a name="python"></a>[Python](#tab/python)

Die Event Grid-Instanz ist über den Parameter verfügbar, der in der als `func.EventGridEvent` typisierten `name`-Eigenschaft der *function.json*-Datei konfiguriert ist.

# <a name="java"></a>[Java](#tab/java)

Die Event Grid-Ereignisinstanz ist über den Parameter verfügbar, der dem als `EventSchema` typisierten `EventGridTrigger`-Attribut zugeordnet ist. Weitere Details finden Sie in dem [Beispiel](#example).

---

## <a name="event-schema"></a>Ereignisschema

Daten für ein Event Grid-Ereignis werden als JSON-Objekt im Text einer HTTP-Anforderung empfangen. Das JSON-Objekt ähnelt dem folgenden Beispiel:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Das Beispiel stellt ein Array mit einem Element dar. Event Grid sendet immer ein Array und kann mehrere Ereignisse im Array senden. Die Runtime ruft die Funktion einmal für jedes Arrayelement auf.

Die Eigenschaften der obersten Ebene in den JSON-Ereignisdaten sind für alle Ereignistypen identisch, die Inhalte der Eigenschaft `data` sind dagegen für jeden Ereignistyp spezifisch. Das Beispiel oben bezieht sich auf ein Blob Storage-Ereignis.

Erläuterungen zu den allgemeinen und ereignisspezifischen Eigenschaften finden Sie unter [Ereigniseigenschaften](../event-grid/event-schema.md#event-properties) in der Event Grid-Dokumentation.

Mit dem Typ `EventGridEvent` werden lediglich die Eigenschaften der obersten Ebene definiert. Bei der `Data`-Eigenschaft handelt es sich um ein `JObject`.

## <a name="create-a-subscription"></a>Erstellen eines Abonnements

Erstellen Sie für den Empfang von HTTP-Anforderungen in Event Grid ein Event Grid-Abonnement, das die Endpunkt-URL angibt, über die die Funktion aufgerufen wird.

### <a name="azure-portal"></a>Azure-Portal

Wählen Sie für Funktionen, die Sie im Azure-Portal mit dem Event Grid-Trigger entwickeln, nacheinander die Optionen **Integration**, **Event Grid-Trigger** und **Event Grid-Abonnement erstellen** aus.

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Verbinden eines neuen Ereignisabonnements mit einem Trigger im Portal":::

Bei Auswahl dieses Links wird das Portal mit der Seite **Ereignisabonnement erstellen** geöffnet, auf der der aktuelle Triggerendpunkt bereits definiert ist.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Erstellen eines Ereignisabonnements mit bereits definiertem Funktionsendpunkt" :::

Weitere Informationen zum Erstellen von Abonnements über das Azure-Portal finden Sie unter [Erstellen eines benutzerdefinierten Ereignisses – Azure-Portal](../event-grid/custom-event-quickstart-portal.md) in der Event Grid-Dokumentation.

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie den Befehl [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create), um ein Abonnement über die [Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli) zu erstellen.

Für den Befehl ist die Endpunkt-URL erforderlich, über die die Funktion aufgerufen wird. Das folgende Beispiel zeigt das versionsabhängige URL-Muster:

#### <a name="version-2x-and-higher-runtime"></a>Runtime ab Version 2.x

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Laufzeit der Version 1.x

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

Der Systemschlüssel ist ein Autorisierungsschlüssel, der in der Endpunkt-URL für einen Event Grid-Trigger eingefügt werden muss. Im folgenden Abschnitt wird erläutert, wie der Systemschlüssel abgerufen wird.

Beispiel, in dem ein Blob Storage-Konto abonniert wird (mit einem Platzhalter für den Systemschlüssel):

#### <a name="version-2x-and-higher-runtime"></a>Runtime ab Version 2.x

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Laufzeit der Version 1.x

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Weitere Informationen zum Erstellen eines Abonnements finden Sie im [Schnellstart für Blob Storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) oder in den anderen Schnellstarts für Event Grid.

### <a name="get-the-system-key"></a>Abrufen des Systemschlüssels

Sie können den Systemschlüssel mithilfe der folgenden API (HTTP GET) abrufen:

#### <a name="version-2x-and-higher-runtime"></a>Runtime ab Version 2.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Laufzeit der Version 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Dabei handelt es sich um eine Admin-API, für die der [Masterschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys) Ihrer Funktion erforderlich ist. Verwechseln Sie den Systemschlüssel (zum Aufrufen einer Event Grid-Triggerfunktion) nicht mit dem Masterschlüssel (zum Ausführen von Verwaltungsaufgaben in der Funktions-App). Verwenden Sie zum Abonnieren eines Event Grid-Themas immer den Systemschlüssel.

Beispiel für die Antwort, die den Systemschlüssel enthält:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Sie erhalten den Hauptschlüssel für Ihre Funktionen-App über die Registerkarte **Funktionen-App-Einstellungen** im Portal.

> [!IMPORTANT]
> Der Hauptschlüssel bietet Administratorzugriff auf Ihre Funktionen-App. Geben Sie diesen Schlüssel nicht an Dritte weiter und verteilen Sie ihn nicht in nativen Clientanwendungen.

Weitere Informationen finden Sie unter [Autorisierungsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys) im Referenzartikel zu HTTP-Triggern.

Alternativ können Sie eine HTTP-PUT-Anforderung senden, um den Schlüsselwert selbst anzugeben.

## <a name="local-testing-with-viewer-web-app"></a>Lokales Testen mit Viewer-Web-App

Zum lokalen Testen eines Event Grid-Triggers müssen Event Grid-HTTP-Anforderungen von ihrem Ursprung in der Cloud an Ihren lokalen Computer gesendet werden. Dies kann erfolgen, indem Anforderungen online erfasst und manuell erneut an Ihren lokalen Computer gesendet werden:

1. [Erstellen Sie eine Viewer-Web-App](#create-a-viewer-web-app), die Ereignisnachrichten erfasst.
1. [Erstellen Sie ein Event Grid-Abonnement](#create-an-event-grid-subscription), mit dem Ereignisse an die Viewer-App gesendet werden.
1. [Generieren Sie eine Anforderung](#generate-a-request), und kopieren Sie den Anforderungstext von der Viewer-App.
1. [Stellen Sie die Anforderung manuell](#manually-post-the-request) für die localhost-URL der Event Grid-Triggerfunktion bereit.

Nach Abschluss der Tests können Sie dasselbe Abonnement für die Produktion verwenden, indem Sie den Endpunkt aktualisieren. Verwenden Sie dazu den Azure CLI-Befehl [az eventgrid event-subscription update](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-update).

### <a name="create-a-viewer-web-app"></a>Erstellen einer Viewer-Web-App

Zum einfachen Erfassen von Ereignisnachrichten können Sie eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereitstellen, die die Ereignisnachrichten anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

Wählen Sie **Deploy to Azure** (In Azure bereitstellen), um die Lösung für Ihr Abonnement bereitzustellen. Geben Sie im Azure-Portal Werte für die Parameter an.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.

Die Website wird angezeigt, aber es wurden noch keine Ereignisse bereitgestellt.

![Anzeigen der neuen Website](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Erstellen eines Event Grid-Abonnements

Erstellen Sie ein Event Grid-Abonnement des Typs, den Sie testen möchten, und geben Sie die URL Ihrer Web-App als Endpunkt für die Ereignisbenachrichtigung an. Der Endpunkt für Ihre Web-App muss das Suffix `/api/updates/` enthalten. Daher lautet die vollständige URL `https://<your-site-name>.azurewebsites.net/api/updates`.

Informationen zum Erstellen von Abonnements über das Azure-Portal finden Sie unter [Erstellen eines benutzerdefinierten Ereignisses – Azure-Portal](../event-grid/custom-event-quickstart-portal.md) in der Event Grid-Dokumentation.

### <a name="generate-a-request"></a>Generieren einer Anforderung

Lösen Sie ein Ereignis aus, mit dem HTTP-Datenverkehr für Ihren Web-App-Endpunkt generiert wird.  Wenn Sie z.B. ein Blob Storage-Abonnement erstellt haben, können Sie ein Blob hochladen oder löschen. Wenn eine Anforderung in Ihrer Web-App angezeigt wird, kopieren Sie den Anforderungstext.

Die Anforderung zur Abonnementüberprüfung wird zuerst empfangen. Ignorieren Sie sämtliche Überprüfungsanforderungen, und kopieren Sie die Ereignisanforderung.

![Kopieren des Anforderungstexts aus Web-App](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Manuelles Bereitstellen der Anforderung

Führen Sie die Event Grid-Funktion lokal aus.

Verwenden Sie ein Tool wie z.B. [Postman](https://www.getpostman.com/) oder [cURL](https://curl.haxx.se/docs/httpscripting.html), um eine HTTP POST-Anforderung zu erstellen:

* Legen Sie einen `Content-Type: application/json`-Header fest.
* Legen Sie einen `aeg-event-type: Notification`-Header fest.
* Fügen Sie die RequestBin-Daten im Anforderungstext ein.
* Stellen Sie die Anforderung für die URL der Event Grid-Triggerfunktion bereit.
  * Verwenden Sie ab der Version 2.x das folgende Muster:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Verwenden Sie für 1.x Folgendes:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

Der `functionName`-Parameter muss der im `FunctionName`-Attribut angegebene Name sein.

In den folgenden Screenshots sind die Header und der Anforderungstext in Postman dargestellt:

![Header in Postman](media/functions-bindings-event-grid/postman2.png)

![Anforderungstext in Postman](media/functions-bindings-event-grid/postman.png)

Mit der Event Grid-Triggerfunktion werden Protokolle ähnlich denen im folgenden Beispiel ausgeführt und angezeigt:

![Beispielprotokolle der Event Grid-Triggerfunktion](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Nächste Schritte

* [Versenden eines Event Grid-Ereignisses](./functions-bindings-event-grid-output.md)
