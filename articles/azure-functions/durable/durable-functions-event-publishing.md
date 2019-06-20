---
title: Veröffentlichungen von Durable Functions in Azure Event Grid (Vorschau)
description: Erfahren Sie, wie Sie eine automatische Veröffentlichung in Azure Event Grid für Durable Functions konfigurieren.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: glenga
ms.openlocfilehash: c07a42349fbd81a46b1b7cd9bcad1978f891a6b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733779"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Veröffentlichungen von Durable Functions in Azure Event Grid (Vorschau)

In diesem Artikel wird gezeigt, wie Sie Durable Functions so einrichten, dass Lebenszyklusereignisse einer Orchestrierung (z.B. Erstellen, Abschließen und Fehlschlagen) in einem benutzerdefinierten [Azure Event Grid-Thema](https://docs.microsoft.com/azure/event-grid/overview) veröffentlicht werden.

Es folgen einige Szenarien, in denen diese Funktion hilfreich ist:

* **DevOps-Szenarien wie Blau/Grün-Bereitstellungen**: Vor der Implementierung einer [parallelen Bereitstellungsstrategie](durable-functions-versioning.md#side-by-side-deployments) möchten Sie möglicherweise wissen, ob irgendeine Aufgabe ausgeführt wird.

* **Erweiterte Unterstützung für Überwachung und Diagnose:** Sie können die Orchestrierungsstatusinformationen in einem externen, für Abfragen optimierten Speicher (z. B. SQL-Datenbank oder CosmosDB) verfolgen.

* **Lang andauernde Hintergrundaktivität:** Bei Verwendung von Durable Functions für eine lang andauernde Hintergrundaktivität erleichtert diese Funktion Ihnen die Ermittlung des aktuellen Status.

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 1.3.0-rc oder höher in Ihrem Durable Functions-Projekt.
* Installieren Sie den [Azure-Speicheremulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator).
* Installieren Sie [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), oder verwenden Sie die [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="create-a-custom-event-grid-topic"></a>Erstellen eines benutzerdefinierten Event Grid-Themas

Erstellen Sie ein Event Grid-Thema zum Senden von Ereignissen aus Durable Functions. In den folgenden Anweisungen wird gezeigt, wie Sie ein Thema mithilfe der Azure-Befehlszeilenschnittstelle (CLI) erstellen. Informationen zur Vorgehensweise mit PowerShell oder im Azure-Portal finden Sie in folgenden Artikeln:

* [EventGrid-Schnellstarts: Erstellen eines benutzerdefinierten Ereignisses – PowerShell](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-powershell)
* [EventGrid-Schnellstarts: Erstellen eines benutzerdefinierten Ereignisses – Azure-Portal](https://docs.microsoft.com/azure/event-grid/custom-event-quickstart-portal)

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl `az group create` eine Ressourcengruppe. Derzeit unterstützt Azure Event Grid nicht alle Regionen. Informationen zu den unterstützten Regionen finden Sie unter [Azure Event Grid – Übersicht](https://docs.microsoft.com/azure/event-grid/overview).

```bash
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>Erstellen eines benutzerdefinierten Themas

Ein Event Grid-Thema stellt einen benutzerdefinierten Endpunkt für die Veröffentlichung Ihrer Ereignisse bereit. Ersetzen Sie `<topic_name>` durch einen eindeutigen Namen für Ihr Thema. Der Name des Themas muss eindeutig sein, da er als DNS-Eintrag verwendet wird.

```bash
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>Abrufen von Endpunkt und Schlüssel

Rufen Sie den Endpunkt des Themas ab. Ersetzen Sie `<topic_name>` durch den von Ihnen ausgewählten Namen.

```bash
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

Rufen Sie den Schlüssel für das Thema ab. Ersetzen Sie `<topic_name>` durch den von Ihnen ausgewählten Namen.

```bash
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

Jetzt können Sie Ereignisse an das Thema senden.

## <a name="configure-azure-event-grid-publishing"></a>Konfigurieren der Veröffentlichung in Azure Event Grid

Suchen Sie in Ihrem Durable Functions-Projekt nach der Datei `host.json`.

Fügen Sie `eventGridTopicEndpoint` und `eventGridKeySettingName` einer `durableTask`-Eigenschaft hinzu.

```json
{
    "durableTask": {
        "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
        "eventGridKeySettingName": "EventGridKey"
    }
}
```

Die möglichen Konfigurationseigenschaften von Azure Event Grid finden Sie in der [host.json-Dokumentation](../functions-host-json.md#durabletask). Nach dem Konfigurieren der Datei `host.json` sendet die Funktions-App Lebenszyklusereignisse an das Event Grid-Thema. Dies funktioniert, wenn Sie die Funktions-App lokal und in Azure ausführen.

Legen Sie die App-Einstellung für den Schlüssel des Themas in der Funktions-App und in der Datei `local.setting.json` fest. Die folgende JSON-Datei dient als Beispiel für die Datei `local.settings.json` für lokales Debuggen. Ersetzen Sie `<topic_key>` durch den Schlüssel für das Thema.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

Stellen Sie sicher, dass der [Speicheremulator](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) ausgeführt wird. Es ist ratsam, vor der Ausführung den Befehl `AzureStorageEmulator.exe clear all` auszuführen.

## <a name="create-functions-that-listen-for-events"></a>Erstellen von Funktionen, die auf Ereignisse warten

Erstellen Sie eine Funktions-App. Es wird empfohlen, sie in der gleichen Region wie das Event Grid-Thema anzusiedeln.

### <a name="create-an-event-grid-trigger-function"></a>Erstellen einer Event Grid-Triggerfunktion

Erstellen Sie eine Funktion zum Empfangen der Lebenszyklusereignisse. Wählen Sie **Benutzerdefinierte Funktion** aus.

![Wählen Sie „Erstellen einer benutzerdefinierten Funktion“ aus.](./media/durable-functions-event-publishing/functions-portal.png)

Wählen Sie „Event Grid-Trigger“ und dann `C#` aus.

![Wählen Sie den Event Grid-Trigger aus.](./media/durable-functions-event-publishing/eventgrid-trigger.png)

Geben Sie den Namen der Funktion ein, und wählen Sie dann `Create` aus.

![Erstellen Sie den Event Grid-Trigger.](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

Es wird eine Funktion mit folgendem Code erstellt:

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

Wählen Sie `Add Event Grid Subscription` aus. Durch diesen Vorgang wird dem erstellten Event Grid-Thema ein Event Grid-Abonnement hinzugefügt. Weitere Informationen finden Sie unter [Begriffe in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/concepts).

![Wählen Sie den Link zum Event Grid-Trigger aus.](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

Wählen Sie `Event Grid Topics` als **Thementyp** aus. Wählen Sie die Ressourcengruppe aus, die Sie für das Event Grid-Thema erstellt haben. Wählen Sie dann die Instanz des Event Grid-Themas aus. Drücken Sie `Create`.

![Erstellen Sie ein Event Grid-Abonnement.](./media/durable-functions-event-publishing/eventsubscription.png)

Sie können nun Lebenszyklusereignisse empfangen.

## <a name="create-durable-functions-to-send-the-events"></a>Erstellen Sie langlebige Funktionen (Durable Functions), um die Ereignisse zu senden.

Starten Sie in Ihrem Durable Functions-Projekt das Debuggen auf Ihrem lokalen Computer.  Der folgende Code entspricht dem Beispielcode für die langlebigen Funktionen. `host.json` und `local.settings.json` haben Sie bereits auf dem lokalen Computer konfiguriert.

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace LifeCycleEventSpike
{
    public static class Sample
    {
        [FunctionName("Sample")]
        public static async Task<List<string>> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var outputs = new List<string>();

            // Replace "hello" with the name of your Durable Activity Function.
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("Sample_Hello", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("Sample_Hello")]
        public static string SayHello([ActivityTrigger] string name, ILogger log)
        {
            log.LogInformation($"Saying hello to {name}.");
            return $"Hello {name}!";
        }

        [FunctionName("Sample_HttpStart")]
        public static async Task<HttpResponseMessage> HttpStart(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
            [OrchestrationClient]DurableOrchestrationClient starter,
            ILogger log)
        {
            // Function input comes from the request content.
            string instanceId = await starter.StartNewAsync("Sample", null);
            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

Wenn Sie `Sample_HttpStart` mit Postman oder in Ihrem Browser aufrufen, beginnt Durable Functions mit dem Senden von Lebenszyklusereignissen. `http://localhost:7071/api/Sample_HttpStart` ist in der Regel der Endpunkt für das lokale Debuggen.

Überprüfen Sie die Protokolle der Funktion, die Sie im Azure-Portal erstellt haben.

```
2018-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2018-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2018-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2018-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2018-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2018-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2018-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>Ereignisschema

In der folgenden Liste wird das Schema für Lebenszyklusereignisse erläutert:

* **`id`** : Eindeutiger Bezeichner für das Event Grid-Ereignis.
* **`subject`** : Pfad zum Ereignisbetreff. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}` kann `Running`, `Completed`, `Failed` und `Terminated` sein.  
* **`data`** : Spezielle Durable Functions-Parameter.
  * **`hubName`** : Name des [TaskHub](durable-functions-task-hubs.md).
  * **`functionName`** : Name der Orchestratorfunktion.
  * **`instanceId`** : ID der Durable Functions-Instanz.
  * **`reason`** : Zusätzliche Daten zum Nachverfolgungsereignis. Weitere Informationen finden Sie unter [Diagnose in Durable Functions (Azure Functions)](durable-functions-diagnostics.md).
  * **`runtimeStatus`** : Laufzeitstatus der Orchestrierung. „Running“, „Completed“, „Failed“, „Canceled“.
* **`eventType`** : "orchestratorEvent"
* **`eventTime`** : Ereigniszeit (UTC).
* **`dataVersion`** : Version des Lebenszyklus-Ereignisschemas.
* **`metadataVersion`** :  Version der Metadaten.
* **`topic`** : Ressource des Event Grid-Themas.

## <a name="how-to-test-locally"></a>Lokales Testen

Verwenden Sie [Ngrok](../functions-bindings-event-grid.md#local-testing-with-ngrok) zum lokalen Testen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Instanzverwaltung in Durable Functions](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Informationen zur Versionsverwaltung in Durable Functions](durable-functions-versioning.md)
