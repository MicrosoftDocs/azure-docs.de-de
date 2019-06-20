---
title: HTTP-APIs in Durable Functions – Azure
description: Es wird beschrieben, wie Sie HTTP-APIs in der Erweiterung „Durable Functions“ für Azure Functions implementieren.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 2f0b01601dfb28b2b6b8ee8ca53398ec3dccb803
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787285"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP-APIs in Durable Functions (Azure Functions)

Die Erweiterung „Durable Task“ macht eine Reihe von HTTP-APIs verfügbar, die zum Durchführen der folgenden Aufgaben verwendet werden können:

* Abrufen des Status einer Orchestrierungsinstanz
* Senden eines Ereignisses an eine Orchestrierungsinstanz im Wartezustand
* Beenden einer ausgeführten Orchestrierungsinstanz

Bei all diesen HTTP-APIs handelt es sich um Webhookvorgänge, die direkt von der Erweiterung „Durable Task“ verarbeitet werden. Sie gelten nicht spezifisch für eine Funktion in der Funktionen-App.

> [!NOTE]
> Diese Vorgänge können auch direkt aufgerufen werden, indem die Instanzverwaltungs-APIs der [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse verwendet werden. Weitere Informationen finden Sie unter [Instanzverwaltung](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Ermittlung der HTTP-API-URL

Die [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)-Klasse macht eine [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)-API verfügbar, die zum Generieren einer HTTP-Antwortnutzlast mit Links zu allen unterstützten Vorgängen verwendet werden kann. Hier ist ein Beispiel für eine HTTP-Triggerfunktion angegeben, mit dem die Nutzung dieser API veranschaulicht wird:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Diese Beispielfunktionen erzeugen die folgenden JSON-Antwortdaten. Der Datentyp aller Felder lautet `string`.

| Feld                   |BESCHREIBUNG                           |
|-------------------------|--------------------------------------|
| **`id`**                |ID der Orchestrierungsinstanz |
| **`statusQueryGetUri`** |Status-URL der Orchestrierungsinstanz |
| **`sendEventPostUri`**  |URL der Orchestrierungsinstanz für die „Ereignisauslösung“ |
| **`terminatePostUri`**  |URL der Orchestrierungsinstanz für die „Beendigung“ |
| **`rewindPostUri`**     |URL der Orchestrierungsinstanz für den „Rücklauf“ |

Hier eine Beispielantwort:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Das Format der Webhook-URLs kann in Abhängigkeit davon variieren, welche Version des Azure Functions-Hosts Sie ausführen. Das obige Beispiel gilt für den Azure Functions 2.x-Host.

## <a name="async-operation-tracking"></a>Nachverfolgen von asynchronen Vorgängen

Die oben erwähnte HTTP-Antwort wurde als Hilfe bei der Implementierung von asynchronen HTTP-APIs mit langer Ausführungsdauer per Durable Functions konzipiert. Dies wird auch als *Polling Consumer Pattern* (Consumerabrufmuster) bezeichnet. Der Client/Server-Datenfluss funktioniert wie folgt:

1. Der Client sendet eine HTTP-Anforderung, um einen Prozess mit langer Ausführungsdauer zu starten, z.B. eine Orchestratorfunktion.
2. Der HTTP-Zieltrigger gibt eine HTTP 202-Antwort mit dem Header `Location` und dem Wert `statusQueryGetUri` zurück.
3. Der Client fragt die URL im Header `Location` ab. Er erhält weiterhin die HTTP 202-Antworten mit dem Header `Location`.
4. Wenn die Instanz abgeschlossen (oder fehlgeschlagen) ist, gibt der Endpunkt im Header `Location` die Antwort „HTTP 200“ zurück.

Dieses Protokoll ermöglicht die Koordination von Prozessen mit langer Ausführungsdauer mit externen Clients oder Diensten, die das Abfragen eines HTTP-Endpunkts unterstützen, und das Beachten des Headers `Location`. Die grundlegenden Teile sind bereits in die Durable Functions-HTTP-APIs integriert.

> [!NOTE]
> Standardmäßig unterstützen alle HTTP-basierten Aktionen, die von [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) bereitgestellt werden, das Standardmuster für asynchrone Vorgänge. Diese Funktion ermöglicht das Einbetten eines Durable Functions-Elements als Teil eines Logic Apps-Workflows. Weitere Informationen zur Unterstützung von Logik-Apps für asynchrone HTTP-Muster finden Sie im Artikel [Workflowaktionen und -trigger für Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP-API-Referenz

Alle HTTP-APIs, die von der Erweiterung implementiert werden, verwenden die folgenden Parameter. Alle Parameter haben den Datentyp `string`.

| Parameter        | Parametertyp  | BESCHREIBUNG |
|------------------|-----------------|-------------|
| **`taskHub`**    | Abfragezeichenfolge    | Der Name des [Aufgabenhub](durable-functions-task-hubs.md). Wenn er nicht angegeben ist, wird der Name des Aufgabenhub der aktuellen Funktionen-App verwendet. |
| **`connection`** | Abfragezeichenfolge    | Der **Name** der Verbindungszeichenfolge für das Speicherkonto. Wenn nichts angegeben ist, wird die Standardverbindungszeichenfolge für die Funktionen-App genutzt. |
| **`systemKey`**  | Abfragezeichenfolge    | Der Autorisierungsschlüssel, der zum Aufrufen der API erforderlich ist. |

`systemKey` ist ein Autorisierungsschlüssel, der vom Azure Functions-Host automatisch generiert wird. Er gewährt spezifischen Zugriff auf die APIs der Erweiterung „Durable Task“ und kann genauso wie [andere Autorisierungsschlüssel](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API) verwaltet werden. Die einfachste Methode zum Ermitteln des `systemKey`-Werts ist die Verwendung der oben erwähnten `CreateCheckStatusResponse`-API.

In den nächsten Abschnitten werden die spezifischen HTTP-APIs behandelt, die von der Erweiterung unterstützt werden, und es sind Anwendungsbeispiele vorhanden.

### <a name="get-instance-status"></a>Abrufen des Instanzstatus (Get instance status)

Dient zum Abrufen des Status einer angegebenen Orchestrierungsinstanz.

#### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld                   | Parametertyp  | BESCHREIBUNG |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID der Orchestrierungsinstanz |
| **`showInput`**         | Abfragezeichenfolge    | Dieser Parameter ist optional. Bei Festlegung auf `false` wird die Funktionseingabe nicht die Antwortnutzlast aufgenommen.|
| **`showHistory`**       | Abfragezeichenfolge    | Dieser Parameter ist optional. Wenn diese Option auf `true` gesetzt ist, wird der Ausführungsverlauf der Orchestrierung in die Antwortnutzlast aufgenommen.|
| **`showHistoryOutput`** | Abfragezeichenfolge    | Dieser Parameter ist optional. Wenn diese Option auf `true` festgelegt ist, werden die Funktionsausgaben in den Ausführungsverlauf der Orchestrierung aufgenommen.|
| **`createdTimeFrom`**   | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der zurückgegebenen Instanzen, die am oder nach dem angegebenen ISO8601-Zeitstempel erstellt wurden.|
| **`createdTimeTo`**     | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der zurückgegebenen-Instanzen, die am oder vor dem angegebenen ISO8601-Zeitstempel erstellt wurden.|
| **`runtimeStatus`**     | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der zurückgegebenen Instanzen auf der Grundlage ihres Laufzeitstatus. Die Liste der möglichen Werte für den Laufzeitstatus finden Sie im Thema [Abfragen von Instanzen](durable-functions-instance-management.md). |

#### <a name="response"></a>response

Es können mehrere mögliche Statuscodewerte zurückgegeben werden.

* **HTTP 200 (OK)** : Die angegebene Instanz befindet sich im Status „Completed“ (Abgeschlossen).
* **HTTP 202 (Akzeptiert)** : Die angegebene Instanz befindet sich in Bearbeitung.
* **HTTP 400 (Ungültige Anforderung)** : Bei der angegebenen Instanz ist ein Fehler aufgetreten, oder sie wurde beendet.
* **HTTP 404 (Nicht gefunden)** : Die angegebene Instanz ist nicht vorhanden, oder die Ausführung wurde noch nicht gestartet.
* **HTTP 500 (Interner Serverfehler)** : Bei der angegebenen Instanz ist ein Ausnahmefehler aufgetreten.

Die Antwortnutzlast für die Fälle **HTTP 200** und **HTTP 202** ist ein JSON-Objekt mit den folgenden Feldern:

| Feld                 | Datentyp | BESCHREIBUNG |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | Zeichenfolge    | Der Laufzeitstatus der Instanz. Mögliche Werte sind *Running*, *Pending*, *Failed*, *Canceled*, *Terminated* und *Completed*. |
| **`input`**           | JSON      | Die JSON-Daten, die zum Initialisieren der Instanz verwendet werden. Dieses Feld lautet `null`, wenn der Abfragezeichenfolgenparameter `showInput` auf `false` festgelegt ist.|
| **`customStatus`**    | JSON      | Die für den benutzerdefinierten Orchestrierungsstatus verwendeten JSON-Daten. Dieses Feld ist `null`, sofern nichts anderes festgelegt wurde. |
| **`output`**          | JSON      | Die JSON-Ausgabe der Instanz. Dieses Feld ist `null`, wenn die Instanz nicht den Status „Completed“ (Abgeschlossen) aufweist. |
| **`createdTime`**     | Zeichenfolge    | Der Zeitpunkt, zu dem die Instanz erstellt wurde. Es wird die erweiterte ISO 8601-Notation verwendet. |
| **`lastUpdatedTime`** | Zeichenfolge    | Der Zeitpunkt, zu dem die Instanz zuletzt persistent gemacht wurde. Es wird die erweiterte ISO 8601-Notation verwendet. |
| **`historyEvents`**   | JSON      | Ein JSON-Array, das den Ausführungsverlauf der Orchestrierung enthält. Dieses Feld ist `null`, sofern der Abfragezeichenfolgen-Parameter `showHistory` auf `true` festgelegt ist. |

Hier sehen Sie ein Beispiel für eine Antwortnutzlast mit dem Ausführungsverlauf der Orchestrierung und den Aktivitätsausgaben (zur besseren Lesbarkeit formatiert):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Die Antwort **HTTP 202** enthält auch den Antwortheader **Location**, in dem auf die gleiche URL wie im oben erwähnten Feld `statusQueryGetUri` verwiesen wird.

### <a name="get-all-instances-status"></a>Abrufen aller Instanzstatus

Sie können auch den Status aller Instanzen abfragen, indem Sie die `instanceId` aus der Anforderung „Get instance status“ (Abrufen des Instanzstatus) entfernen. In diesem Fall entsprechen die grundlegenden Parameter der Anforderung „Get instance status“. Abfragezeichenfolgeparameter zum Filtern werden auch unterstützt.

Beachten Sie jedoch, dass `connection` und `code` optional sind. Wenn Sie für die Funktion anonyme Authentifizierung verwenden, ist kein Code erforderlich.
Wenn Sie keine andere Verbindungszeichenfolge für den Speicher als die in der AzureWebJobsStorage-App-Einstellung definierte verwenden möchten, können Sie den Verbindungszeichenfolgen-Parameter unbesorgt ignorieren.

#### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld                   | Parametertyp  | BESCHREIBUNG |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID der Orchestrierungsinstanz |
| **`showInput`**         | Abfragezeichenfolge    | Dieser Parameter ist optional. Bei Festlegung auf `false` wird die Funktionseingabe nicht die Antwortnutzlast aufgenommen.|
| **`showHistory`**       | Abfragezeichenfolge    | Dieser Parameter ist optional. Wenn diese Option auf `true` gesetzt ist, wird der Ausführungsverlauf der Orchestrierung in die Antwortnutzlast aufgenommen.|
| **`showHistoryOutput`** | Abfragezeichenfolge    | Dieser Parameter ist optional. Wenn diese Option auf `true` festgelegt ist, werden die Funktionsausgaben in den Ausführungsverlauf der Orchestrierung aufgenommen.|
| **`createdTimeFrom`**   | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der zurückgegebenen Instanzen, die am oder nach dem angegebenen ISO8601-Zeitstempel erstellt wurden.|
| **`createdTimeTo`**     | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der zurückgegebenen-Instanzen, die am oder vor dem angegebenen ISO8601-Zeitstempel erstellt wurden.|
| **`runtimeStatus`**     | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der zurückgegebenen Instanzen auf der Grundlage ihres Laufzeitstatus. Die Liste der möglichen Werte für den Laufzeitstatus finden Sie im Thema [Abfragen von Instanzen](durable-functions-instance-management.md). |
| **`top`**               | Abfragezeichenfolge    | Dieser Parameter ist optional. Wenn er angegeben wird, begrenzt er die Anzahl von Instanzen, die von der Abfrage zurückgegeben werden. |

#### <a name="response"></a>response

Hier ist ein Beispiel für Antwortnutzlasten einschließlich des Orchestrierungsstatus (für bessere Lesbarkeit formatiert):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Dieser Vorgang kann in Bezug auf Azure Storage-E/A-Vorgänge sehr teuer sein, wenn die Instanztabelle viele Zeilen umfasst. Weitere Informationen zur Instanztabelle finden Sie in der Dokumentation [Leistung und Skalierbarkeit in Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table).
>

Wenn mehr Ergebnisse vorhanden sind, wird ein Fortsetzungstoken im Antwortheader zurückgegeben.  Der Name des Headers lautet `x-ms-continuation-token`.

Wenn Sie den Wert des Fortsetzungstokens im nächsten Anforderungsheader festlegen, können Sie die nächste Seite mit Ergebnissen abrufen. Dieser Name für den Anforderungsheader ist auch `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Löschen des Verlaufs für eine einzelne Instanz

Löscht den Verlauf und zugehörige Artefakte für eine bestimmte Orchestrierungsinstanz.

#### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld             | Parametertyp  | BESCHREIBUNG |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID der Orchestrierungsinstanz |

#### <a name="response"></a>response

Die folgenden HTTP-Statuscodewerte können zurückgegeben werden:

* **HTTP 200 (OK)** : Der Instanzverlauf wurde erfolgreich gelöscht.
* **HTTP 404 (Nicht gefunden)** : Die angegebene Instanz ist nicht vorhanden.

Die Antwortnutzlast für den Fall **HTTP 200** ist ein JSON-Objekt mit dem folgenden Feld:

| Feld                  | Datentyp | BESCHREIBUNG |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Die Anzahl der gelöschten Instanzen. Für eine einzelne Instanz sollte dieser Wert immer `1` sein. |

Hier ist ein Beispiel für eine Antwortnutzlast angegeben (zur besseren Lesbarkeit formatiert):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Löschen des Verlaufs für mehrere Instanzen

Sie können auch den Verlauf und zugehörige Artefakte für mehrere Instanzen in einem Aufgabenhub löschen, indem Sie die `{instanceId}` aus der Anforderung „Purge single instance history“ (Löschen des Verlaufs für eine einzelne Instanz) entfernen. Verwenden Sie die gleichen Filter, die in der Anforderung „Get all instances status“ (Abrufen aller Instanzstatus) beschrieben sind, um selektiv den Verlauf einer Instanz zu löschen.

#### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld                 | Parametertyp  | BESCHREIBUNG |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Abfragezeichenfolge    | Filtert die Liste der gelöschten Instanzen, die am oder nach dem angegebenen ISO8601-Zeitstempel erstellt wurden.|
| **`createdTimeTo`**   | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der gelöschten Instanzen, die am oder vor dem angegebenen ISO8601-Zeitstempel erstellt wurden.|
| **`runtimeStatus`**   | Abfragezeichenfolge    | Dieser Parameter ist optional. Filtert, wenn er angegeben wird, die Liste der gelöschten Instanzen auf der Grundlage ihres Laufzeitstatus. Die Liste der möglichen Werte für den Laufzeitstatus finden Sie im Thema [Abfragen von Instanzen](durable-functions-instance-management.md). |

> [!NOTE]
> Dieser Vorgang kann in Bezug auf Azure Storage-E/A-Vorgänge sehr teuer sein, wenn die Instanztabelle viele Zeilen und/oder Verlaufstabellen umfasst. Weitere Informationen zu diesen Tabellen finden Sie in der Dokumentation [Leistung und Skalierbarkeit in Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table).

#### <a name="response"></a>response

Die folgenden HTTP-Statuscodewerte können zurückgegeben werden:

* **HTTP 200 (OK)** : Der Instanzverlauf wurde erfolgreich gelöscht.
* **HTTP 404 (Nicht gefunden)** : Es wurden keine Instanzen gefunden, die dem Filterausdruck entsprechen.

Die Antwortnutzlast für den Fall **HTTP 200** ist ein JSON-Objekt mit dem folgenden Feld:

| Feld                   | Datentyp | BESCHREIBUNG |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Die Anzahl der gelöschten Instanzen. |

Hier ist ein Beispiel für eine Antwortnutzlast angegeben (zur besseren Lesbarkeit formatiert):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Auslösen eines Ereignisses (Raise event)

Sendet eine Ereignisbenachrichtigung an eine ausgeführte Orchestrierungsinstanz.

#### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld             | Parametertyp  | BESCHREIBUNG |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID der Orchestrierungsinstanz |
| **`eventName`**   | URL             | Der Name des Ereignisses, das die Zielorchestrierungsinstanz erwartet. |
| **`{content}`**   | Inhalt anfordern | Ereignisnutzlast in JSON-Formatierung |

#### <a name="response"></a>response

Es können mehrere mögliche Statuscodewerte zurückgegeben werden.

* **HTTP 202 (Akzeptiert)** : Das ausgelöste Ereignis wurde zur Verarbeitung akzeptiert.
* **HTTP 400 (Ungültige Anforderung)** : Der Anforderungsinhalt war nicht vom Typ `application/json` oder war kein gültiger JSON-Code.
* **HTTP 404 (Nicht gefunden)** : Die angegebene Instanz wurde nicht gefunden.
* **HTTP 410 (Fehlend)** : Die angegebene Instanz wurde abgeschlossen, oder es ist ein Fehler aufgetreten, sodass sie keine ausgelösten Ereignisse verarbeiten kann.

Hier ist eine Beispielanforderung angegeben, bei der die JSON-Zeichenfolge `"incr"` an eine Instanz gesendet wird, die auf ein Ereignis mit dem Namen **operation** wartet:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Die Antworten für diese API enthalten keinen Inhalt.

### <a name="terminate-instance"></a>Beenden der Instanz (Terminate instance)

Dient zum Beenden einer ausgeführten Orchestrierungsinstanz.

#### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld             | Parametertyp  | BESCHREIBUNG |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID der Orchestrierungsinstanz |
| **`reason`**      | Abfragezeichenfolge    | Optional. Gibt den Grund für die Beendigung der Orchestrierungsinstanz an. |

#### <a name="response"></a>response

Es können mehrere mögliche Statuscodewerte zurückgegeben werden.

* **HTTP 202 (Akzeptiert)** : Die Beendigungsanforderung wurde zur Verarbeitung akzeptiert.
* **HTTP 404 (Nicht gefunden)** : Die angegebene Instanz wurde nicht gefunden.
* **HTTP 410 (Fehlend)** : Die angegebene Instanz wurde abgeschlossen, oder es ist ein Fehler aufgetreten.

Hier ist eine Beispielanforderung angegeben, mit der eine ausgeführte Instanz beendet und als Grund **buggy** (fehlerhaft) angegeben wird:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Die Antworten für diese API enthalten keinen Inhalt.

## <a name="rewind-instance-preview"></a>Rewind-Instanz (Vorschau)

Stellt eine fehlerhafte Orchestrierungsinstanz in den Zustand „Wird ausgeführt“ zurück, indem die letzten fehlerhaften Vorgänge erneut durchgeführt werden.

### <a name="request"></a>Anforderung

Für Version 1.x der Azure Functions-Runtime ist die Anforderung wie folgt formatiert (der besseren Übersichtlichkeit halber werden mehrere Zeilen angezeigt):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In Version 2.x der Azure Functions-Runtime hat das URL-Format die gleichen Parameter, jedoch mit einem etwas anderen Präfix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Anforderungsparameter für diese API enthalten den bereits erwähnten Standardsatz sowie die folgenden eindeutigen Parameter:

| Feld             | Parametertyp  | BESCHREIBUNG |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID der Orchestrierungsinstanz |
| **`reason`**      | Abfragezeichenfolge    | Optional. Gibt den Grund für das Zurückspulen der Orchestrierungsinstanz an. |

### <a name="response"></a>response

Es können mehrere mögliche Statuscodewerte zurückgegeben werden.

* **HTTP 202 (Akzeptiert)** : Die Anforderung zum Zurückspulen wurde zur Verarbeitung akzeptiert.
* **HTTP 404 (Nicht gefunden)** : Die angegebene Instanz wurde nicht gefunden.
* **HTTP 410 (Fehlend)** : Die angegebene Instanz wurde abgeschlossen oder beendet.

Hier ist eine Beispielanforderung angegeben, die eine fehlerhafte Instanz zurückspult und einen Grund für **fixed** (behoben) angibt:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Die Antworten für diese API enthalten keinen Inhalt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Fehlerbehandlung](durable-functions-error-handling.md)
