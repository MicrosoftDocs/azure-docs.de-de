---
title: Schemareferenz zu Trigger- und Aktionstypen
description: Schemareferenzhandbuch zu Trigger- und Aktionstypen in der Workflowdefinitionssprache für Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3dbfcacb6ea5922a01d52dfe39189f09f48d4b4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006080"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Schemareferenzhandbuch zu Trigger- und Aktionstypen für Azure Logic Apps

In dieser Referenz werden die allgemeinen Typen beschrieben, die zum Identifizieren von Triggern und Aktionen in der Ihrer Logik-App zugrunde liegenden Workflowdefinition verwendet werden, die durch die [Definitionssprache für Workflows](../logic-apps/logic-apps-workflow-definition-language.md) beschrieben und validiert werden. Um bestimmte Connector-Trigger und -Aktionen aufzufinden, die Sie in Ihren Logik-Apps verwenden können, sehen Sie in der Liste unter der [Connectorübersicht](/connectors/) nach.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Übersicht über Trigger

Jeder Workflow enthält einen Trigger, mit dem die Aufrufe definiert werden, die den Workflow instanziieren und starten. Hier sind die allgemeinen Triggerkategorien angegeben:

* Ein *Abruftrigger*, der den Endpunkt eines Diensts in regelmäßigen Abständen überprüft.

* Ein *Pushtrigger*, mit dem ein Abonnement für einen Endpunkt erstellt und eine *Rückruf-URL* bereitgestellt wird, damit der Endpunkt den Trigger benachrichtigen kann, wenn das angegebene Ereignis eintritt oder Daten verfügbar sind. Der Trigger wartet dann auf die Antwort des Endpunkts, bevor er ausgelöst wird.

Trigger besitzen auf oberster Ebene die folgenden Elemente (einige davon sind optional):  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*trigger-name*> | String | Name des Triggers | 
| <*trigger-type*> | String | Triggertyp, z.B. „Http“ oder „ApiConnection“ | 
| <*trigger-inputs*> | JSON-Objekt | Eingaben, die das Verhalten des Triggers definieren | 
| <*time-unit*> | String | Die Zeiteinheit für die Häufigkeit der Triggerauslösung: „Second“, „Minute“, „Hour“, „Day“, „Week“, „Month“ | 
| <*number-of-time-units*> | Integer | Wert, mit dem angegeben wird, wie oft der Trigger basierend auf der Häufigkeit ausgelöst wird (Anzahl von Zeiteinheiten der Wartezeit, bis der Trigger erneut ausgelöst wird) <p>Zulässige Mindest- und Maximalintervalle: <p>– Monat: 1–16 Monate </br>– Tag: 1–500 Tage </br>– Stunde: 1–12.000 Stunden </br>– Minute: 1–72.000 Minuten </br>- Sekunde: 1–9.999.999 Sekunden<p>Wenn das Intervall also beispielsweise auf „6“ und die Häufigkeit auf „Month“ festgelegt ist, erfolgt die Wiederholung alle sechs Monate. | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*array-with-conditions*> | Array | Array mit mindestens einer [Bedingung](#trigger-conditions), die bestimmt, ob der Workflow ausgeführt wird. Derzeit nur für Trigger verfügbar. | 
| <*runtime-config-options*> | JSON-Objekt | Sie können das Verhalten der Triggerlaufzeit ändern, indem Sie die Eigenschaften für `runtimeConfiguration` festlegen. Weitere Informationen finden Sie unter den [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options). | 
| <*splitOn-expression*> | String | Für Trigger, die ein Array zurückgeben, können Sie einen Ausdruck angeben, mit dem Arrayelemente für die Verarbeitung in mehrere Workflowinstanzen [aufgeteilt bzw. *aufgelöst*](#split-on-debatch) werden. | 
| <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Liste mit Triggertypen

Jeder Triggertyp verfügt über eine andere Schnittstelle sowie über Eingaben, die sein Verhalten definieren. 

### <a name="built-in-triggers"></a>Integrierte Trigger

| Triggertyp | BESCHREIBUNG | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Überprüft alle Endpunkte bzw. *fragt diese ab*. Der Endpunkt muss einem bestimmten Triggervertrag entsprechen, und zwar durch Verwendung eines asynchronen Musters (`202`) oder durch Rückgabe eines Arrays. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Erstellt einen aufrufbaren Endpunkt für Ihre Logik-App, aber ruft die angegebene URL auf, um die Registrierung bzw. die Aufhebung der Registrierung durchzuführen. |
| [**Recurrence**](#recurrence-trigger) | Wird auf der Grundlage eines definierten Zeitplans ausgelöst. Sie können ein Datum und eine Uhrzeit in der Zukunft festlegen, um diesen Trigger auszulösen. Je nach Häufigkeit können Sie auch Zeiten und Tage für die Ausführung Ihres Workflows angeben. | 
| [**Request**](#request-trigger)  | Erstellt einen aufrufbaren Endpunkt für Ihre Logik-App und wird auch als „manueller“ Trigger bezeichnet. Weitere Informationen finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Verwaltete API-Trigger

| Triggertyp | BESCHREIBUNG | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Überprüft einen Endpunkt mithilfe der [von Microsoft verwalteten APIs](../connectors/apis-list.md) bzw. *fragt diesen ab*. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Erstellt einen aufrufbaren Endpunkt für Ihre Logik-App, indem [von Microsoft verwaltete APIs](../connectors/apis-list.md) aufgerufen werden, um das Abonnieren bzw. das Kündigen des Abonnements durchzuführen. | 
||| 

## <a name="triggers---detailed-reference"></a>Trigger – Detaillierte Referenz

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection-Trigger  

Mit diesem Trigger wird ein Endpunkt überprüft bzw.*abgefragt*, indem [von Microsoft verwaltete APIs](../connectors/apis-list.md) verwendet werden. Die Parameter für diesen Trigger können sich also basierend auf dem Endpunkt unterscheiden. Viele Abschnitte dieser Triggerdefinition sind optional. Das Verhalten des Triggers hängt davon ab, ob bestimmte Abschnitte enthalten sind.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | String | Name des Triggers |
| <*connection-name*> | String | Name für die Verbindung mit der verwalteten API, die vom Workflow verwendet wird |
| <*method-type*> | String | HTTP-Methode für die Kommunikation mit der verwalteten API: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ |
| <*api-operation*> | String | Aufzurufender API-Vorgang |
| <*time-unit*> | String | Die Zeiteinheit für die Häufigkeit der Triggerauslösung: „Second“, „Minute“, „Hour“, „Day“, „Week“, „Month“ |
| <*number-of-time-units*> | Integer | Wert, mit dem angegeben wird, wie oft der Trigger basierend auf der Häufigkeit ausgelöst wird (Anzahl von Zeiteinheiten der Wartezeit, bis der Trigger erneut ausgelöst wird) <p>Zulässige Mindest- und Maximalintervalle: <p>– Monat: 1–16 Monate </br>– Tag: 1–500 Tage </br>– Stunde: 1–12.000 Stunden </br>– Minute: 1–72.000 Minuten </br>- Sekunde: 1–9.999.999 Sekunden<p>Wenn das Intervall also beispielsweise auf „6“ und die Häufigkeit auf „Month“ festgelegt ist, erfolgt die Wiederholung alle sechs Monate. |
||||

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in den API-Aufruf einbezogen werden sollen. Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird dem Aufruf beispielsweise `?api-version=2018-01-01` hinzugefügt. | 
| <*max-runs*> | Integer | Standardmäßig werden Workflowinstanzen gleichzeitig (oder parallel) bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Wenn Ihr Workflow bereits auf der maximalen Anzahl von Instanzen ausgeführt wird (die Sie basierend auf der `runtimeConfiguration.concurrency.runs`-Eigenschaft ändern können), werden alle neuen Ausführungen bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) in diese Warteschlange eingereiht. Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). | 
| <*splitOn-expression*> | String | Für Trigger, die Arrays zurückgeben, verweist dieser Ausdruck auf das zu verwendende Array, damit Sie für jedes Arrayelement eine Workflowinstanz erstellen und ausführen können, anstatt eine „for each“-Schleife zu verwenden. <p>Dieser Ausdruck stellt beispielsweise ein Element im Array dar, das mit dem Inhalt des Triggers zurückgegeben wird: `@triggerbody()?['value']` |
| <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). |
||||

*Ausgaben*
 
| Element | type | BESCHREIBUNG |
|---------|------|-------------|
| headers | JSON-Objekt | Header aus der Antwort |
| body | JSON-Objekt | Text aus der Antwort |
| status code | Integer | Der Statuscode aus der Antwort |
|||| 

*Beispiel*

Diese Triggerdefinition prüft den Posteingang eines Geschäfts-, Schul- oder Unikontos jeden Tag auf E-Mails:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook-Trigger

Dieser Trigger sendet eine Abonnementanforderung an einen Endpunkt, indem eine [von Microsoft verwaltete API](../connectors/apis-list.md) verwendet wird, gibt eine *Rückruf-URL* an, an die der Endpunkt eine Antwort senden kann, und wartet auf die Antwort des Endpunkts. Weitere Informationen finden Sie unter [Endpunktabonnements](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*connection-name*> | String | Name für die Verbindung mit der verwalteten API, die vom Workflow verwendet wird | 
| <*body-content*> | JSON-Objekt | Nachrichteninhalt, der als Nutzlast an die verwaltete API gesendet wird | 
||||

*Optional*

| Wert | type | BESCHREIBUNG |
|-------|------|-------------|
| <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in den API-Aufruf einbezogen werden sollen. <p>Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird dem Aufruf beispielsweise `?api-version=2018-01-01` hinzugefügt. |
| <*max-runs*> | Integer | Standardmäßig werden Workflowinstanzen gleichzeitig (oder parallel) bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Wenn Ihr Workflow bereits auf der maximalen Anzahl von Instanzen ausgeführt wird (die Sie basierend auf der `runtimeConfiguration.concurrency.runs`-Eigenschaft ändern können), werden alle neuen Ausführungen bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) in diese Warteschlange eingereiht. Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). | 
| <*splitOn-expression*> | String | Für Trigger, die Arrays zurückgeben, verweist dieser Ausdruck auf das zu verwendende Array, damit Sie für jedes Arrayelement eine Workflowinstanz erstellen und ausführen können, anstatt eine „for each“-Schleife zu verwenden. <p>Dieser Ausdruck stellt beispielsweise ein Element im Array dar, das mit dem Inhalt des Triggers zurückgegeben wird: `@triggerbody()?['value']` |
| <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). | 
|||| 

*Beispiel*

Diese Triggerdefinition abonniert die Office 365-Outlook-API, gibt eine Rückruf-URL für den API-Endpunkt an und wartet darauf, dass der Endpunkt antwortet, wenn eine neue E-Mail eintrifft.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP-Trigger

Dieser Trigger sendet eine Anforderung an den angegebenen HTTP- oder HTTPS-Endpunkt, basierend auf dem angegebenen Wiederholungszeitplan. Dieser Trigger überprüft dann die Antwort, um zu bestimmen, ob der Workflow ausgeführt wird. Weitere Informationen finden Sie unter [Aufrufen von Dienstendpunkten per HTTP oder HTTPS aus Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Erforderlich*

| Eigenschaft | Wert | type | BESCHREIBUNG |
|----------|-------|------|-------------|
| `method` | <*method-type*> | String | Die zum Senden der ausgehenden Anforderung zu verwendende Methode: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ |
| `uri` | <*HTTP-or-HTTPS-endpoint-URL*> | String | Die HTTP- oder HTTPS-Endpunkt-URL, an die die ausgehende Anforderung gesendet werden soll. Maximal zulässige Zeichenfolgengröße: 2 KB <p>Für einen Azure-Dienst oder eine Azure-Ressource enthält diese URI-Syntax die Ressourcen-ID und den Pfad zu der Ressource, auf die Sie zugreifen möchten. |
| `frequency` | <*time-unit*> | String | Die Zeiteinheit für die Häufigkeit der Triggerauslösung: „Second“, „Minute“, „Hour“, „Day“, „Week“, „Month“ |
| `interval` | <*number-of-time-units*> | Integer | Wert, mit dem angegeben wird, wie oft der Trigger basierend auf der Häufigkeit ausgelöst wird (Anzahl von Zeiteinheiten der Wartezeit, bis der Trigger erneut ausgelöst wird) <p>Zulässige Mindest- und Maximalintervalle: <p>– Monat: 1–16 Monate </br>– Tag: 1–500 Tage </br>– Stunde: 1–12.000 Stunden </br>– Minute: 1–72.000 Minuten </br>- Sekunde: 1–9.999.999 Sekunden<p>Wenn das Intervall also beispielsweise auf „6“ und die Häufigkeit auf „Month“ festgelegt ist, erfolgt die Wiederholung alle sechs Monate. |
|||||

*Optional*

| Eigenschaft | Wert | type | BESCHREIBUNG |
|----------|-------|------|-------------|
| `headers` | <*header-content*> | JSON-Objekt | Alle Kopfzeilen, die in die Anforderung einbezogen werden müssen. <p>Verwenden Sie beispielsweise Folgendes, um Sprache und Typ festzulegen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in der Anforderung verwendet werden müssen. <p>Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird der Anforderung beispielsweise `?api-version=2018-01-01` hinzugefügt. |
| `body` | <*body-content*> | JSON-Objekt | Nachrichteninhalt, der als Nutzlast mit der Anforderung gesendet wird |
| `authentication` | <*authentication-type-and-property-values*> | JSON-Objekt | Das Authentifizierungsmodell, das von der Anforderung zum Authentifizieren ausgehender Anforderungen verwendet wird. Weitere Informationen finden Sie unter [Hinzufügen der Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Über den Scheduler hinaus wird die `authority`-Eigenschaft unterstützt. Ohne Angabe wird standardmäßig der Wert `https://management.azure.com/` verwendet. Sie können aber einen anderen Wert verwenden. |
| `retryPolicy` > `type` | <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*max-runs*> | Integer | Standardmäßig werden Workflowinstanzen gleichzeitig (oder parallel) bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*max-runs-queue*> | Integer | Wenn Ihr Workflow bereits auf der maximalen Anzahl von Instanzen ausgeführt wird (die Sie basierend auf der `runtimeConfiguration.concurrency.runs`-Eigenschaft ändern können), werden alle neuen Ausführungen bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) in diese Warteschlange eingereiht. Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). |
| `operationOptions` | <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). |
|||||

*Ausgaben*

| Element | type | BESCHREIBUNG |
|---------|------|-------------|
| `headers` | JSON-Objekt | Header aus der Antwort |
| `body` | JSON-Objekt | Text aus der Antwort |
| `status code` | Integer | Der Statuscode aus der Antwort |
||||

*Erforderliche Voraussetzungen für eingehende Anforderungen*

Damit der Endpunkt gut mit Ihrer Logik-App funktioniert, muss er einem bestimmten Triggermuster oder -vertrag entsprechen und diese Antworteigenschaften erkennen:

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| Statuscode | Ja | Der Statuscode „200 OK“ startet eine Ausführung. Alle anderen Statuscodes starten keine Ausführung. |
| Retry-After-Header | Nein | Die Anzahl von Sekunden bis zur erneuten Abfrage des Endpunkts durch Ihre Logik-App. |
| Adressheader | Nein | Die URL, die im nächsten Abfrageintervall aufgerufen werden soll. Ohne Angabe wird die ursprüngliche URL verwendet. |
|||| 

*Beispielverhalten für unterschiedliche Anforderungen*

| Statuscode | Wiederholungsintervall | Verhalten | 
|-------------|-------------|----------|
| 200 | {keine} | Führt den Workflow aus und sucht erst nach der definierten Wiederholung wieder nach weiteren Inhalten. | 
| 200 | 10 Sekunden | Führt den Workflow aus und sucht nach zehn Sekunden nach weiteren Inhalten. |  
| 202 | 60 Sekunden | Der Workflow wird nicht ausgelöst. Der nächste Versuch erfolgt gemäß der definierten Wiederholung in einer Minute. Wenn die definierte Wiederholung weniger als eine Minute beträgt, hat der Retry-After-Header Vorrang. Andernfalls wird die definierte Wiederholung verwendet. | 
| 400 | {keine} | Ungültige Anforderung. Der Workflow wird nicht ausgeführt. Wenn keine `retryPolicy` definiert ist, wird die Standardrichtlinie verwendet. Wenn die Anzahl von Wiederholungen erreicht ist, sucht der Trigger nach der definierten Wiederholung erneut nach Inhalten. | 
| 500 | {keine}| Serverfehler. Der Workflow wird nicht ausgeführt. Wenn keine `retryPolicy` definiert ist, wird die Standardrichtlinie verwendet. Wenn die Anzahl von Wiederholungen erreicht ist, sucht der Trigger nach der definierten Wiederholung erneut nach Inhalten. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook-Trigger  

Mit diesem Trigger wird Ihre Logik-App aufrufbar, indem ein Endpunkt erstellt wird, der durch das Aufrufen der angegebenen Endpunkt-URL ein Abonnement registrieren kann. Wenn Sie diesen Trigger in Ihrem Workflow erstellen, wird bei einer ausgehenden Anforderung der Aufruf für die Registrierung des Abonnements durchgeführt. Auf diese Weise kann der Trigger mit dem Lauschen auf Ereignisse beginnen. Wenn ein Vorgang diesen Trigger ungültig macht, führt eine ausgehende Anforderung automatisch den Aufruf zum Kündigen des Abonnements durch. Weitere Informationen finden Sie unter [Endpunktabonnements](#subscribe-unsubscribe).

Sie können auch [asynchrone Limits](#asynchronous-limits) für einen **HTTPWebhook**-Trigger angeben. Das Verhalten des Triggers hängt davon ab, welche Abschnitte Sie verwenden bzw. entfernen.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Einige Werte, z.B. <*method-type*>, sind sowohl für das Objekt `"subscribe"` als auch für `"unsubscribe"` verfügbar.

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*method-type*> | String | HTTP-Methode für die Abonnementanforderung: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ | 
| <*endpoint-subscribe-URL*> | String | Endpunkt-URL, an die die Abonnementanforderung gesendet werden soll | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*method-type*> | String | HTTP-Methode für die Kündigungsanforderung: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ | 
| <*endpoint-unsubscribe-URL*> | String | Endpunkt-URL, an die die Kündigungsanforderung gesendet werden soll | 
| <*body-content*> | String | Beliebiger Nachrichteninhalt, der in der Abonnement- oder Kündigungsanforderung gesendet werden soll | 
| <*authentication-type*> | JSON-Objekt | Das Authentifizierungsmodell, das von der Anforderung zum Authentifizieren ausgehender Anforderungen verwendet wird. Weitere Informationen finden Sie unter [Hinzufügen der Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Integer | Standardmäßig werden alle Workflowinstanzen gleichzeitig (oder parallel) bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Wenn Ihr Workflow bereits auf der maximalen Anzahl von Instanzen ausgeführt wird (die Sie basierend auf der `runtimeConfiguration.concurrency.runs`-Eigenschaft ändern können), werden alle neuen Ausführungen bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) in diese Warteschlange eingereiht. Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). | 
| <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). | 
|||| 

*Ausgaben* 

| Element | type | BESCHREIBUNG |
|---------|------|-------------| 
| headers | JSON-Objekt | Header aus der Antwort | 
| body | JSON-Objekt | Text aus der Antwort | 
| status code | Integer | Der Statuscode aus der Antwort | 
|||| 

*Beispiel*

Dieser Trigger erstellt ein Abonnement mit dem angegebenen Endpunkt, stellt eine eindeutige Rückruf-URL bereit und wartet auf neu veröffentlichte Technologieartikel.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Recurrence-Trigger  

Dieser Trigger wird basierend auf dem angegebenen Wiederholungszeitplan ausgeführt und ist eine einfache Möglichkeit zum Erstellen eines Workflows, der regelmäßig ausgeführt wird.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*time-unit*> | String | Die Zeiteinheit für die Häufigkeit der Triggerauslösung: „Second“, „Minute“, „Hour“, „Day“, „Week“, „Month“ | 
| <*number-of-time-units*> | Integer | Wert, mit dem angegeben wird, wie oft der Trigger basierend auf der Häufigkeit ausgelöst wird (Anzahl von Zeiteinheiten der Wartezeit, bis der Trigger erneut ausgelöst wird) <p>Zulässige Mindest- und Maximalintervalle: <p>– Monat: 1–16 Monate </br>– Tag: 1–500 Tage </br>– Stunde: 1–12.000 Stunden </br>– Minute: 1–72.000 Minuten </br>- Sekunde: 1–9.999.999 Sekunden<p>Wenn das Intervall also beispielsweise auf „6“ und die Häufigkeit auf „Month“ festgelegt ist, erfolgt die Wiederholung alle sechs Monate. | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | String | Das Startdatum und die -uhrzeit im folgenden Format: <p>JJJJ-MM-TTTHH:mm:ss (bei Angabe einer Zeitzone; beachten Sie dabei, dass „TT“ den Tag bezeichnet, während das dritte „T“ wie in den nachfolgenden Beispielen gezeigt nicht verändert werden darf) <p>Oder <p>JJJJ-MM-TTTHH:mm:ssZ (ohne Angabe einer Zeitzone) <p>Für „18. September 2017, 14:00 Uhr“ müssten Sie also beispielsweise „2017-09-18T14:00:00“ und eine Zeitzone (etwa „Pacific Standard Time“) angeben oder „2017-09-18T14:00:00Z“ ohne eine Zeitzone. <p>**Hinweis:** Diese Startzeit kann maximal 49 Jahre in der Zukunft liegen und muss dem [ISO 8601-Format für Datums-/Uhrzeitangaben](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) entsprechen und im [UTC-Datums-/Zeitformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) angegeben werden, jedoch ohne [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset). Wenn Sie keine Zeitzone angeben, müssen Sie den Buchstaben „Z“ ohne Leerzeichen anhängen. „Z“ bezieht sich auf die entsprechende [nautische Zeit](https://en.wikipedia.org/wiki/Nautical_time). <p>Bei einfachen Zeitpläne ist die Startzeit das erste Vorkommen. Bei komplexeren Zeitplänen wird der Trigger nicht vor der Startzeit ausgelöst. Weitere Informationen zu Startdatum und -uhrzeit finden Sie unter [Schedule tasks and workflows that run regularly with logic apps](../connectors/connectors-native-recurrence.md) (Planen von regelmäßig ausgeführten Aufgaben und Workflows mit Logik-Apps). | 
| <*time-zone*> | String | Nur relevant, wenn Sie eine Startzeit angeben, da dieser Trigger keine [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset) akzeptiert. Geben Sie die anzuwendende Zeitzone an. | 
| <*one-or-more-hour-marks*> | Ganze Zahl oder Ganzzahlarray | Wenn Sie für `frequency` die Option „Day“ oder „Week“ angeben, können Sie eine ganze Zahl oder eine kommagetrennte Liste mit ganzen Zahlen von 0 bis 23 als die Stunden des Tages angeben, zu denen der Workflow ausgeführt werden soll. <p>Wenn Sie also etwa „10“, „12“ und „14“ angeben, erhalten Sie die vollen Stunden „10 Uhr“, „12 Uhr“ und „14 Uhr“. | 
| <*one-or-more-minute-marks*> | Ganze Zahl oder Ganzzahlarray | Wenn Sie für `frequency` die Option „Day“ oder „Week“ angeben, können Sie eine ganze Zahl oder eine kommagetrennte Liste mit ganzen Zahlen von 0 bis 59 als die Minuten der Stunde angeben, zu denen der Workflow ausgeführt werden soll. <p>Wenn Sie also beispielsweise „30“ als Minutenwert angeben und das vorherige Beispiel für Stunden des Tages verwenden, erhalten Sie „10:30 Uhr“, „12:30 Uhr“ und „14:30 Uhr“. | 
| weekDays | Zeichenfolge oder Zeichenfolgenarray | Wenn Sie für `frequency` die Option „Week“ angeben, können Sie einen Tag oder eine durch Trennzeichen getrennte Liste mit Tagen für die Workflowausführung angeben: „Monday“, „Tuesday“, „Wednesday“, „Thursday“, „Friday“, „Saturday“, „Sunday“ | 
| <*max-runs*> | Integer | Standardmäßig werden alle Workflowinstanzen gleichzeitig (oder parallel) bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Wenn Ihr Workflow bereits auf der maximalen Anzahl von Instanzen ausgeführt wird (die Sie basierend auf der `runtimeConfiguration.concurrency.runs`-Eigenschaft ändern können), werden alle neuen Ausführungen bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) in diese Warteschlange eingereiht. Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). | 
| <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). | 
|||| 

*Beispiel 1*

Dieser grundlegende Wiederholungstrigger wird täglich ausgeführt:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Beispiel 2*

Sie können ein Startdatum und eine -uhrzeit für die Auslösung des Triggers festlegen. Dieser Wiederholungstrigger wird am angegebenen Datum gestartet und anschließend täglich ausgelöst:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Beispiel 3*

Dieser Wiederholungstrigger startet am 9. September 2017 um 14:00 Uhr und wird wöchentlich jeden Montag um 10:30 Uhr, 12:30 Uhr und 14:30 Uhr Pacific Standard Time ausgelöst:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Weitere Informationen zu diesem Trigger sowie Beispiele finden Sie unter [Erstellen und Planen der regelmäßigen Ausführung von Aufgaben](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Anforderungstrigger

Durch diesen Trigger wird Ihre Logik-App aufrufbar, indem ein Endpunkt erstellt wird, der eingehende Anforderungen annehmen kann. Geben Sie für diesen Trigger ein JSON-Schema an, das die Nutzlast oder die Eingaben, die der Trigger aus der eingehenden Anforderung empfängt, beschreibt und überprüft. Mit dem Schema kann für spätere Aktionen des Workflows außerdem leichter auf Triggereigenschaften verwiesen werden.

Um diesen Trigger aufrufen zu können, müssen Sie die `listCallbackUrl`-API verwenden, die in der [Workflow Service-REST-API](/rest/api/logic/workflows) beschrieben ist. Informationen zum Verwenden dieses Triggers als HTTP-Endpunkt finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Logik-Apps](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*property-name*> | String | Name einer Eigenschaft im JSON-Schema, mit der die Nutzlast beschrieben wird | 
| <*property-type*> | String | Typ der Eigenschaft | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*method-type*> | String | Methode, die eingehende Anforderungen verwenden müssen, um Ihre Logik-App aufzurufen: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ |
| <*relative-path-for-accepted-parameter*> | String | Relativer Pfad für den Parameter, der von der URL Ihres Endpunkts akzeptiert werden kann | 
| <*required-properties*> | Array | Mindestens eine Eigenschaft, die Werte erfordert. | 
| <*max-runs*> | Integer | Standardmäßig werden alle Workflowinstanzen gleichzeitig (oder parallel) bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Wenn Ihr Workflow bereits auf der maximalen Anzahl von Instanzen ausgeführt wird (die Sie basierend auf der `runtimeConfiguration.concurrency.runs`-Eigenschaft ändern können), werden alle neuen Ausführungen bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) in diese Warteschlange eingereiht. Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). | 
| <*operation-option*> | String | Sie können das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). | 
|||| 

*Beispiel*

Dieser Trigger gibt an, dass eine eingehende Anforderung die HTTP POST-Methode verwenden muss, um den Trigger aufzurufen. Außerdem enthält er ein Schema, mit dem die Eingabe aus der eingehenden Anforderung überprüft wird:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Triggerbedingungen

Für jeden Trigger – und nur für Trigger – können Sie ein Array mit Ausdrücken für Bedingungen einbinden, mit denen bestimmt wird, ob der Workflow ausgeführt werden soll. Öffnen Sie Ihre Logik-App im Codeansicht-Editor, um die `conditions`-Eigenschaft einem Trigger in Ihrem Workflow hinzuzufügen.

Sie können beispielsweise angeben, dass ein Trigger nur dann ausgeführt wird, wenn eine Website einen internen Serverfehler zurückgibt. Hierzu verweisen Sie in der `conditions`-Eigenschaft auf den Statuscode des Triggers:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Standardmäßig wird ein Trigger nur ausgelöst, wenn die Antwort „200 OK“ empfangen wird. Wenn ein Ausdruck auf den Statuscode eines Triggers verweist, wird das Standardverhalten des Triggers ersetzt. Falls der Trigger bei mehr als einem Statuscode ausgelöst werden soll, z.B. „200“ und „201“, müssen Sie diesen Ausdruck als Ihre Bedingung einfügen:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Mehrere Ausführungen des Triggers

Wenn der Trigger ein Array zum Verarbeiten durch die Logik-App zurückgibt, könnte eine „for each“-Schleife in einigen Fällen für die Verarbeitung der einzelnen Arrayelemente zu lange dauern. Stattdessen können Sie die **SplitOn**-Eigenschaft in Ihrem Trigger zur *debatch*-Ausführung für das Array verwenden. Bei der Batchauflösung werden das Array in seine Elemente aufgeteilt und eine neue Logik-App-Instanz gestartet, die für jedes Arrayelement ausgeführt wird. Dieser Ansatz ist beispielsweise hilfreich, wenn Sie einen Endpunkt abfragen möchten, der zwischen Abfrageintervallen mehrere neue Elemente zurückgeben könnte. Informationen über die maximale Anzahl der Arrayelemente, die **SplitOn** in einer einzelnen Logik-App-Ausführung verarbeiten kann, finden Sie unter [Logic Apps-Grenzwerte und -Konfiguration](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Es ist nicht möglich, **SplitOn** mit einem synchronen Antwortmuster zu verwenden. Jeder Workflow, der **SplitOn** verwendet und eine Antwortaktion enthält, wird asynchron ausgeführt und sendet sofort eine `202 ACCEPTED`-Antwort.
>
> Wenn Trigger-Parallelität aktiviert ist, wird das [SplitOn-Limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) erheblich reduziert. Wenn die Anzahl der Elemente dieses Limit überschreitet, wird die SplitOn-Funktion deaktiviert.
 
Wenn die Swagger-Datei des Triggers eine Nutzlast beschreibt, die ein Array ist, wird die **SplitOn**-Eigenschaft automatisch dem Trigger hinzugefügt. Fügen Sie diese Eigenschaft andernfalls in der Antwortnutzlast hinzu, die das Array enthält, für das Sie die Batchauflösung ausführen möchten.

*Beispiel*

Angenommen, Sie verfügen über eine API, die folgende Antwort zurückgibt: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

Ihre Logik-App benötigt lediglich den Inhalt des Arrays von `Rows`, damit Sie einen Trigger wie im folgenden Beispiel erstellen können:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Mit dem `SplitOn`-Befehl können Sie keine Eigenschaften außerhalb des Arrays abrufen. In diesem Beispiel können Sie also nicht die `status`-Eigenschaft in der von der API zurückgegebenen Antwort abrufen.
> 
> Um einen Fehler zu vermeiden, wenn die `?`-Eigenschaft nicht vorhanden ist, wird in diesem Beispiel der `Rows`-Operator verwendet.

Ihre Workflowdefinition kann nun `@triggerBody().name` verwenden, um die `name`-Werte abzurufen. Dies sind `"customer-name-one"` aus der ersten Ausführung und `"customer-name-two"` aus der zweiten Ausführung. Ihre Triggerausgaben sehen nun wie diese Beispiele aus:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Übersicht über Aktionen

Für Azure Logic Apps werden verschiedene Aktionstypen bereitgestellt. Diese verfügen jeweils über unterschiedliche Eingaben, mit denen das eindeutige Verhalten einer Aktion definiert wird. Aktionen verfügen über diese allgemeinen Elemente, von denen einige aber optional sind:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------|
| <*action-name*> | String | Name für die Aktion | 
| <*action-type*> | String | Aktionstyp, z.B. „Http“ oder „ApiConnection“| 
| <*input-name*> | String | Name für eine Eingabe, mit der das Verhalten der Aktion definiert wird | 
| <*input-value*> | Verschiedene | Eingabewert, bei dem es sich um eine Zeichenfolge, eine Ganzzahl, ein JSON-Objekt usw. handeln kann | 
| <*previous-trigger-or-action-status*> | JSON-Objekt | Name und sich ergebender Status für den Trigger oder die Aktion, die sofort ausgeführt werden muss, bevor diese aktuelle Aktion ausgeführt werden kann | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------|
| <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter „Wiederholungsrichtlinien“. | 
| <*runtime-config-options*> | JSON-Objekt | Für einige Aktionen können Sie das Verhalten der Aktion zur Laufzeit ändern, indem Sie die `runtimeConfiguration`-Eigenschaften festlegen. Weitere Informationen finden Sie unter den [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options). | 
| <*operation-option*> | String | Für einige Aktionen können Sie das Standardverhalten ändern, indem Sie die `operationOptions`-Eigenschaft festlegen. Weitere Informationen finden Sie unter [Optionen für Vorgänge](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Liste mit Aktionstypen

Hier sind einige häufig verwendete Aktionstypen angegeben: 

* [Integrierte Aktionstypen](#built-in-actions), z.B. diese Beispiele und mehr: 

  * [**HTTP**](#http-action) zum Aufrufen von Endpunkten per HTTP oder HTTPS

  * [**Antwort**](#response-action) zum Beantworten von Anforderungen

  * [**Ausführen von JavaScript-Code**](#run-javascript-code) für die Ausführung von JavaScript-Codeausschnitten

  * [**Funktion**](#function-action) zum Aufrufen von Azure Functions

  * Datenvorgangsaktionen, z.B. [**Join**](#join-action), [**Compose**](#compose-action), [**Table**](#table-action), [**Select**](#select-action) und andere, mit denen Daten aus unterschiedlichen Eingaben erstellt oder transformiert werden

  * [**Workflow**](#workflow-action) zum Aufrufen eines anderen Logik-App-Workflows

* [Aktionstypen mit verwalteter API](#managed-api-actions), z.B. [**ApiConnection**](#apiconnection-action) und [**ApiConnectionWebHook**](#apiconnectionwebhook-action), mit denen verschiedene von Microsoft verwaltete Connectors und APIs aufgerufen werden, z.B. Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub und mehr

* [Aktionstypen zur Workflowsteuerung](#control-workflow-actions), z.B. [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action) und [**Until**](#until-action), die andere Aktionen enthalten und Sie beim Organisieren der Workflowausführung unterstützen

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Integrierte Aktionen

| Aktionstyp | BESCHREIBUNG | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Erstellt aus Eingaben, die verschiedene Typen aufweisen können, eine einzelne Ausgabe. | 
| [**Ausführen von JavaScript-Code**](#run-javascript-code) | Führen Sie JavaScript-Codeausschnitte aus, die bestimmten Kriterien entsprechen. Informationen zu Codeanforderungen und weitere Informationen finden Sie unter [Hinzufügen und Ausführen von Codeausschnitten mit Inlinecode](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Function**](#function-action) | Ruft eine Azure-Funktion auf. | 
| [**HTTP**](#http-action) | Ruft einen HTTP-Endpunkt auf. | 
| [**Join**](#join-action) | Erstellt eine Zeichenfolge aus allen Elementen in einem Array und trennt diese Elemente mit einem angegebenen Trennzeichen. | 
| [**Parse JSON**](#parse-json-action) | Erstellt benutzerfreundliche Token aus Eigenschaften in JSON-Inhalten. Sie können dann auf diese Eigenschaften verweisen, indem Sie die Token in Ihre Logik-App einfügen. | 
| [**Query**](#query-action) | Erstellt basierend auf einer Bedingung oder einem Filter ein Array aus den Elementen eines anderen Arrays. | 
| [**Response**](#response-action) | Erstellt eine Antwort auf einen eingehenden Aufruf oder eine Anforderung. | 
| [**Select**](#select-action) | Erstellt ein Array mit JSON-Objekten, indem Elemente aus einem anderen Array basierend auf der angegebenen Zuordnung transformiert werden. | 
| [**Table**](#table-action) | Erstellt aus einem Array eine CSV- oder HTML-Tabelle. | 
| [**Terminate**](#terminate-action) | Beendet einen aktiv ausgeführten Workflow. | 
| [**Wait**](#wait-action) | Hält Ihren Workflow für einen angegebenen Zeitraum bzw. bis zum angegebenen Zeitpunkt (Datum und Uhrzeit) an. | 
| [**Workflow**](#workflow-action) | Schachtelt einen Workflow innerhalb eines anderen Workflows. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Aktionen mit verwalteter API

| Aktionstyp | BESCHREIBUNG | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Ruft einen HTTP-Endpunkt auf, indem eine [von Microsoft verwaltete API](../connectors/apis-list.md) verwendet wird. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Vergleichbar mit „HTTP Webhook“, aber es wird eine [von Microsoft verwaltete API](../connectors/apis-list.md) verwendet. | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Aktionen zur Workflowsteuerung

Mit diesen Aktionen können Sie die Workflowausführung steuern und andere Aktionen einbinden. Von außerhalb einer Aktion zur Workflowsteuerung können Sie direkt auf Aktionen innerhalb der jeweiligen Aktion zur Workflowsteuerung verweisen. Wenn Sie beispielsweise eine `Http`-Aktion innerhalb eines Bereichs verwenden, können Sie von überall im Workflow aus auf den Ausdruck `@body('Http')` verweisen. Aktionen, die innerhalb einer Aktion zur Workflowsteuerung vorhanden sind, können nur nach anderen Aktionen ausgeführt werden, die sich in derselben Struktur für die Workflowsteuerung befinden.

| Aktionstyp | BESCHREIBUNG | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Führt die gleichen Aktionen für jedes Element eines Arrays als Schleife aus. | 
| [**If**](#if-action) | Führt Aktionen in Abhängigkeit davon aus, ob die angegebene Bedingung wahr oder falsch ist. | 
| [**Scope**](#scope-action) | Führt Aktionen basierend auf dem Gruppenstatus eines Satzes mit Aktionen aus. | 
| [**Switch**](#switch-action) | Führt Aktionen aus, die zu Fällen zusammengefasst sind, wenn Werte aus Ausdrücken, Objekten oder Token mit den für jeden Fall angegebenen Werten übereinstimmen. | 
| [**Until**](#until-action) | Führt Aktionen in einer Schleife aus, bis die angegebene Bedingung wahr ist. | 
|||  

## <a name="actions---detailed-reference"></a>Aktionen – Detaillierte Referenz

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection-Aktion

Diese Aktion sendet eine HTTP-Anforderung an eine [von Microsoft verwaltete API](../connectors/apis-list.md). Hierfür sind Informationen zur API und zu den Parametern sowie ein Verweis auf eine gültige Verbindung erforderlich. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*action-name*> | String | Name der vom Connector bereitgestellten Aktion | 
| <*api-name*> | String | Name der von Microsoft verwalteten API, die für die Verbindung verwendet wird | 
| <*method-type*> | String | HTTP-Methode zum Aufrufen der API: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ | 
| <*api-operation*> | String | Aufzurufender API-Vorgang | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | JSON-Objekt | Alle anderen Eingabeeigenschaften, die für die spezifische Aktion gelten | 
| <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in den API-Aufruf einbezogen werden sollen. <p>Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird dem Aufruf beispielsweise `?api-version=2018-01-01` hinzugefügt. | 
| <*other-action-specific-properties*> | JSON-Objekt | Alle anderen Eigenschaften, die für die spezifische Aktion gelten | 
|||| 

*Beispiel*

Mit dieser Definition wird die Aktion **Send an email** für den Office 365-Outlook-Connector beschrieben, bei dem es sich um eine von Microsoft verwaltete API handelt: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook-Aktion

Diese Aktion sendet eine Abonnementanforderung per HTTP an einen Endpunkt, indem eine [von Microsoft verwaltete API](../connectors/apis-list.md) verwendet wird, gibt eine *Rückruf-URL* an, an die der Endpunkt eine Antwort senden kann, und wartet auf die Antwort des Endpunkts. Weitere Informationen finden Sie unter [Endpunktabonnements](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Einige Werte, z.B. <*method-type*>, sind sowohl für das Objekt `"subscribe"` als auch für `"unsubscribe"` verfügbar.

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*action-name*> | String | Name der vom Connector bereitgestellten Aktion | 
| <*method-type*> | String | HTTP-Methode zum Abonnieren oder Kündigen des Abonnements über einen Endpunkt: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ | 
| <*api-subscribe-URL*> | String | URI, der zum Abonnieren der API verwendet wird | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | String | URI, der zum Kündigen des API-Abonnements verwendet wird | 
| <*header-content*> | JSON-Objekt | Alle Header, die in der Anforderung gesendet werden sollen <p>Verwenden Sie beispielsweise Folgendes, um Sprache und Typ für eine Anforderung festzulegen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-Objekt | Beliebiger Nachrichteninhalt, der in der Anforderung gesendet wird | 
| <*authentication-type*> | JSON-Objekt | Das Authentifizierungsmodell, das von der Anforderung zum Authentifizieren ausgehender Anforderungen verwendet wird. Weitere Informationen finden Sie unter [Hinzufügen der Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in den API-Aufruf einbezogen werden sollen. <p>Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird dem Aufruf beispielsweise `?api-version=2018-01-01` hinzugefügt. | 
| <*other-action-specific-input-properties*> | JSON-Objekt | Alle anderen Eingabeeigenschaften, die für die spezifische Aktion gelten | 
| <*other-action-specific-properties*> | JSON-Objekt | Alle anderen Eigenschaften, die für die spezifische Aktion gelten | 
|||| 

Sie können auch Grenzwerte für eine **ApiConnectionWebhook**-Aktion angeben, wie Sie dies für [asynchrone Grenzwerte für HTTP](#asynchronous-limits) tun.

<a name="compose-action"></a>

### <a name="compose-action"></a>Compose-Aktion

Diese Aktion erstellt eine einzelne Ausgabe aus mehreren Eingaben, z.B. Ausdrücken. Sowohl die Ausgabe als auch die Eingaben können einen beliebigen Typ aufweisen, der von Azure Logic Apps nativ unterstützt wird, z.B. Arrays, JSON-Objekte, XML und Binärdateien. Sie können die Ausgabe der Aktion dann in anderen Aktionen verwenden. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Erforderlich* 

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*inputs-to-compose*> | Any | Eingaben für die Erstellung einer einzelnen Ausgabe | 
|||| 

*Beispiel 1*

<!-- markdownlint-disable MD038 -->
Bei dieser Aktionsdefinition werden `abcdefg ` mit einer nachgestellten Leerstelle und der Wert `1234` zusammengeführt:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Hier ist die Ausgabe angegeben, die mit dieser Aktion erstellt wird:

`abcdefg 1234`

*Beispiel 2*

Bei dieser Aktionsdefinition werden eine Zeichenfolgenvariable, die `abcdefg` enthält, und eine Integer-Variable zusammengeführt, die `1234` enthält:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Hier ist die Ausgabe angegeben, die mit dieser Aktion erstellt wird:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Aktion „JavaScript-Code ausführen“

Diese Aktion führt einen JavaScript-Codeausschnitt aus und gibt die Ergebnisse über ein `Result`-Token zurück, auf das nachfolgende Aktionen verweisen können.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG |
|-------|------|-------------|
| <*JavaScript-Codeausschnitt*> | Varies | Der JavaScript-Code, der ausgeführt werden soll. Informationen zu Codeanforderungen und weitere Informationen finden Sie unter [Hinzufügen und Ausführen von Codeausschnitten mit Inlinecode](../logic-apps/logic-apps-add-run-inline-code.md). <p>Im `code`-Attribut kann Ihr Codeausschnitt das schreibgeschützte `workflowContext`-Objekt als Eingabe verwenden. Dieses Objekt weist untergeordnete Eigenschaften auf, die dem Code Zugriff auf die Ergebnisse des Triggers und früherer Aktionen im Workflow gewähren. Weitere Informationen zum `workflowContext`-Objekt finden Sie unter [Verweisen auf die Ergebnisse von Triggern und Aktionen in Ihrem Code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*In einigen Fällen erforderlich*

Das `explicitDependencies`-Attribut gibt an, dass die Ergebnisse des Triggers, Ergebnisse vorheriger Aktionen oder beides als Abhängigkeiten explizit für Ihren Codeausschnitt enthalten sein sollen. Weitere Informationen für das Hinzufügen dieser Abhängigkeiten finden Sie unter [Hinzufügen von Parametern für Inlinecode](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Für das `includeTrigger`-Attribut können Sie `true`- oder `false`-Werte angeben.

| Wert | type | BESCHREIBUNG |
|-------|------|-------------|
| <*previous-actions*> | Zeichenfolgenarray | Ein Array mit den von Ihren angegebenen Aktionsnamen. Verwenden Sie die Aktionsnamen, die in Ihrer Workflowdefinition angezeigt werden, wobei Aktionsnamen Unterstriche (_) und keine Leerzeichen („ “) verwenden. |
||||

*Beispiel 1*

Diese Aktion führt Code aus, der den Namen Ihrer Logik-App abruft und den Text „Hello World from \<logic-app-name>“ als Ergebnis zurückgibt. In diesem Beispiel verweist der Code auf den Namen des Workflows, indem mithilfe des schreibgeschützten `workflowContext`-Objekts auf die Eigenschaft `workflowContext.workflow.name` zugegriffen wird. Weitere Informationen zur Verwendung des `workflowContext`-Objekts finden Sie unter [Reference trigger and action results in your code (Verweisen auf die Ergebnisse von Triggern und Aktionen in Ihrem Code)](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Beispiel 2*

Diese Aktion führt Code in einer Logik-App aus, die ausgelöst wird, wenn eine neue E-Mail in einem Geschäfts-, Schul- oder Unikonto eingeht. Die Logik-App verwendet auch eine Aktion für das Senden einer Genehmigungs-E-Mail, die den Inhalt der empfangenen E-Mail zusammen mit einer Genehmigungsanforderung weiterleitet.

Der Code extrahiert die E-Mail-Adressen aus der `Body`-Eigenschaft des Triggers und gibt die Adressen zusammen mit dem `SelectedOption`-Eigenschaftswert aus der Genehmigungsaktion zurück. Die Aktion schließt explizit die Aktion zum Senden der Genehmigungs-E-Mail als Abhängigkeit im Attribut `explicitDependencies` > `actions` mit ein.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Function-Aktion

Mit dieser Aktion wird eine zuvor erstellte [Azure-Funktion](../azure-functions/functions-create-first-azure-function.md) aufgerufen.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | Die Ressourcen-ID für die Azure-Funktion, die Sie aufrufen möchten. Das Format für diesen Wert lautet wie folgt:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*method-type*> | String | HTTP-Methode zum Aufrufen der Funktion: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ <p>Wenn nichts angegeben wird, wird standardmäßig die Methode „POST“ verwendet. | 
||||

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------|  
| <*header-content*> | JSON-Objekt | Alle Header, die mit dem Aufruf gesendet werden sollen <p>Verwenden Sie beispielsweise Folgendes, um Sprache und Typ für eine Anforderung festzulegen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON-Objekt | Beliebiger Nachrichteninhalt, der in der Anforderung gesendet wird | 
| <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in den API-Aufruf einbezogen werden sollen. <p>Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird dem Aufruf beispielsweise `?api-version=2018-01-01` hinzugefügt. | 
| <*other-action-specific-input-properties*> | JSON-Objekt | Alle anderen Eingabeeigenschaften, die für die spezifische Aktion gelten | 
| <*other-action-specific-properties*> | JSON-Objekt | Alle anderen Eigenschaften, die für die spezifische Aktion gelten | 
||||

Beim Speichern Ihrer Logik-App führt die Logic Apps-Engine diese Überprüfungen der referenzierten Funktion durch:

* Ihr Workflow muss auf die Funktion zugreifen können.

* Ihr Workflow kann nur einen HTTP-Standardtrigger oder generischen JSON-Webhooktrigger verwenden. 

  Die Logic Apps-Engine ruft die zur Laufzeit verwendete Trigger-URL ab und speichert sie zwischen. Sollte die zwischengespeicherte URL aufgrund eines Vorgangs ungültig werden, tritt bei der Aktion **Function** zur Laufzeit aber ein Fehler auf. Speichern Sie die Logik-App erneut, damit diese die Trigger-URL wieder abruft und zwischenspeichert, um dieses Problem zu beheben.

* Für die Funktion kann keine Route definiert werden.

* Als Autorisierungsebenen sind nur „function“ und „anonymous“ zulässig. 

*Beispiel*

Mit dieser Aktionsdefinition wird die zuvor erstellte Funktion „GetProductID“ aufgerufen:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP-Aktion

Diese Aktion sendet eine Anforderung an den angegebenen HTTP- oder HTTPS-Endpunkt und überprüft anhand der Antwort, ob der Workflow ausgeführt wird. Weitere Informationen finden Sie unter [Aufrufen von Dienstendpunkten per HTTP oder HTTPS aus Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Erforderlich*

| Eigenschaft | Wert | type | BESCHREIBUNG |
|----------|-------|------|-------------|
| `method` | <*method-type*> | String | Die zum Senden der ausgehenden Anforderung zu verwendende Methode: „GET“, „PUT“, „POST“, „PATCH“, „DELETE“ |
| `uri` | <*HTTP-or-HTTPS-endpoint-URL*> | String | Die HTTP- oder HTTPS-Endpunkt-URL, an die die ausgehende Anforderung gesendet werden soll. Maximal zulässige Zeichenfolgengröße: 2 KB <p>Für einen Azure-Dienst oder eine Azure-Ressource enthält diese URI-Syntax die Ressourcen-ID und den Pfad zu der Ressource, auf die Sie zugreifen möchten. |
|||||

*Optional*

| Eigenschaft | Wert | type | BESCHREIBUNG |
|----------|-------|------|-------------|
| `headers` | <*header-content*> | JSON-Objekt | Alle Kopfzeilen, die in die Anforderung einbezogen werden müssen. <p>Verwenden Sie beispielsweise Folgendes, um Sprache und Typ festzulegen: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*query-parameters*> | JSON-Objekt | Alle Abfrageparameter, die in der Anforderung verwendet werden müssen. <p>Mit dem `"queries": { "api-version": "2018-01-01" }`-Objekt wird dem Aufruf beispielsweise `?api-version=2018-01-01` hinzugefügt. |
| `body` | <*body-content*> | JSON-Objekt | Nachrichteninhalt, der als Nutzlast mit der Anforderung gesendet wird |
| `authentication` | <*authentication-type-and-property-values*> | JSON-Objekt | Das Authentifizierungsmodell, das von der Anforderung zum Authentifizieren ausgehender Anforderungen verwendet wird. Weitere Informationen finden Sie unter [Hinzufügen der Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Über den Scheduler hinaus wird die `authority`-Eigenschaft unterstützt. Ohne Angabe wird standardmäßig der Wert `https://management.azure.com/` verwendet. Sie können aber einen anderen Wert verwenden. |
| `retryPolicy` > `type` | <*retry-behavior*> | JSON-Objekt | Passt das Wiederholungsverhalten für vorübergehende Fehler, die über den Statuscode 408, 429 und 5XX verfügen, und alle Verbindungsausnahmen an. Weitere Informationen finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*other-action-specific-input-properties*> | <*input-property*> | JSON-Objekt | Alle anderen Eingabeeigenschaften, die für die spezifische Aktion gelten |
| <*other-action-specific-properties*> | <*property-value*> | JSON-Objekt | Alle anderen Eigenschaften, die für die spezifische Aktion gelten |
|||||

*Beispiel*

Mit dieser Aktionsdefinition werden die aktuellen Neuigkeiten abgerufen, indem eine Anforderung an den angegebenen Endpunkt gesendet werden:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Join-Aktion

Diese Aktion erstellt eine Zeichenfolge aus allen Elementen in einem Array und trennt diese Elemente mit dem angegebenen Trennzeichen. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*array*> | Array | Das Array oder der Ausdruck, mit dem die Quellelemente bereitgestellt werden. Setzen Sie diesen Ausdruck in doppelte Anführungszeichen, wenn Sie einen Ausdruck angeben. | 
| <*delimiter*> | Zeichenfolge mit nur einem Zeichen | Das Zeichen, mit dem die Elemente in der Zeichenfolge getrennt werden | 
|||| 

*Beispiel*

Angenommen, Sie verfügen über die zuvor erstellte Variable „myIntegerArray“, die dieses Integer-Array enthält: 

`[1,2,3,4]` 

Mit dieser Aktionsdefinition werden die Werte aus der Variablen abgerufen, indem die Funktion `variables()` in einem Ausdruck verwendet und die folgende Zeichenfolge mit per Komma getrennten Werten erstellt wird: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Aktion „Parse JSON“

Mit dieser Aktion werden benutzerfreundliche Felder oder *Token* aus den Eigenschaften im JSON-Inhalt erstellt. Sie können dann in Ihrer Logik-App auf diese Eigenschaften zugreifen, indem Sie stattdessen die Token verwenden. Wenn Sie beispielsweise die JSON-Ausgabe von Diensten verwenden möchten, z.B. Azure Service Bus und Azure Cosmos DB, können Sie diese Aktion in Ihre Logik-App einfügen, damit Sie einfacher auf die Daten in dieser Ausgabe verweisen können.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*JSON-source*> | JSON-Objekt | JSON-Inhalt, den Sie analysieren möchten | 
| <*JSON-schema*> | JSON-Objekt | JSON-Schema, mit dem der zugrunde liegende JSON-Inhalt beschrieben wird, der von der Aktion zum Analysieren des JSON-Quellinhalts verwendet wird. <p>**Tipp**: Im Logik-App-Designer können Sie das Schema bereitstellen oder eine Beispielnutzlast angeben, damit die Aktion das Schema generieren kann. | 
|||| 

*Beispiel*

Mit dieser Aktionsdefinition werden diese Token erstellt, die Sie in Ihrem Workflow verwenden können. Dies gilt aber nur für Aktionen, die nach der Aktion **Parse JSON** ausgeführt werden:

`FirstName`, `LastName` und `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

In diesem Beispiel gibt die „content“-Eigenschaft den JSON-Inhalt für die zu analysierende Aktion an. Sie können diesen JSON-Inhalt auch als Beispielnutzlast zum Generieren des Schemas angeben.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Mit der „schema“-Eigenschaft wird das JSON-Schema angegeben, das zum Beschreiben des JSON-Inhalts verwendet wird:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Abfrageaktion

Mit dieser Aktion wird basierend auf einer angegebenen Bedingung oder einem Filter ein Array aus den Elementen eines anderen Arrays erstellt.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*array*> | Array | Das Array oder der Ausdruck, mit dem die Quellelemente bereitgestellt werden. Setzen Sie diesen Ausdruck in doppelte Anführungszeichen, wenn Sie einen Ausdruck angeben. |
| <*condition-or-filter*> | String | Bedingung, die zum Filtern von Elementen im Quellarray verwendet wird <p>**Hinweis**: Falls keine Werte die Bedingung erfüllen, erstellt die Aktion ein leeres Array. |
|||| 

*Beispiel*

Mit dieser Aktionsdefinition wird ein Array mit Werten erstellt, die größer als der angegebene Wert (2) sind:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Antwortaktion  

Mit dieser Aktion wird die Nutzlast für die Antwort auf eine HTTP-Anforderung erstellt. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*response-status-code*> | Integer | Der HTTP-Statuscode, der an die eingehende Anforderung gesendet wird. Der Standardcode lautet „200 OK“, aber es kann ein beliebiger gültiger Statuscode sein, der mit 2xx, 4xx oder 5xx beginnt (aber nicht mit 3xxx). | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*response-headers*> | JSON-Objekt | Einer oder mehrere Header, die in die Antwort eingefügt werden sollen | 
| <*response-body*> | Verschiedene | Antworttext, bei dem es sich um eine Zeichenfolge, ein JSON-Objekt oder auch Binärinhalt aus einer vorherigen Aktion handelt | 
|||| 

*Beispiel*

Mit dieser Aktionsdefinition wird eine Antwort auf eine HTTP-Anforderung mit dem angegebenen Statuscode, Nachrichtentext und den Nachrichtenheadern erstellt:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Einschränkungen*

Im Gegensatz zu anderen Aktionen verfügt die Aktion **Response** über spezielle Einschränkungen: 

* Ihr Workflow kann die Aktion **Response** nur verwenden, wenn der Workflow mit einem HTTP-Anforderungstrigger beginnt. Dies bedeutet, dass Ihr Workflow von einer HTTP-Anforderung ausgelöst werden muss.

* Ihr Workflow kann die Aktion **Response** überall verwenden, *mit Ausnahme* innerhalb von **Foreach**-Schleifen, **Until**-Schleifen, z.B. sequenziellen Schleifen, und parallelen Branches. 

* Die ursprüngliche HTTP-Anforderung ruft die Antwort Ihres Workflows nur ab, wenn alle für die Aktion **Response** erforderlichen Aktionen innerhalb des [Timeoutlimits für HTTP-Anforderungen](../logic-apps/logic-apps-limits-and-config.md#request-limits) beendet wurden.

  Wenn Ihr Workflow aber eine andere Logik-App als geschachtelten Workflow aufruft, wartet der übergeordnete Workflow, bis der geschachtelte Workflow abgeschlossen ist. Hierbei spielt es keine Rolle, wie viel Zeit vergeht, bis der geschachtelte Workflow abgeschlossen ist.

* Wenn für Ihren Workflow die Aktion **Response** und ein synchrones Antwortmuster verwendet werden, kann der Workflow auch den Befehl **splitOn** nicht in der Triggerdefinition verwenden, da mit diesem Befehl mehrere Ausführungen erstellt werden. Achten Sie bei Verwendung der PUT-Methode auf diesen Fall, und sorgen Sie dafür, dass eine Antwort vom Typ „Fehlerhafte Anforderung“ zurückgegeben wird.

  Wenn für Ihren Workflow dagegen der Befehl **splitOn** und die Aktion **Response** verwendet werden, wird der Workflow asynchron ausgeführt, und es wird sofort die Antwort „202 AKZEPTIERT“ zurückgegeben.

* Wenn die Ausführung Ihres Workflows die Aktion **Response** erreicht, aber die eingehende Anforderung bereits eine Antwort erhalten hat, wird die Aktion **Response** aufgrund des Konflikts als „Failed“ (Fehler) gekennzeichnet. Ihre Logik-App-Ausführung erhält somit ebenfalls den Status „Failed“.

<a name="select-action"></a>

### <a name="select-action"></a>Aktion select

Diese Aktion erstellt ein Array mit JSON-Objekten, indem Elemente aus einem anderen Array basierend auf der angegebenen Zuordnung transformiert werden. Das Ausgabearray und das Quellarray verfügen immer über die gleiche Anzahl von Elementen. Sie können zwar nicht die Anzahl von Objekten im Ausgabearray ändern, aber Sie können Eigenschaften und die zugehörigen Werte für diese Objekte hinzufügen oder entfernen. Mit der `select`-Eigenschaft wird mindestens ein Schlüssel-Wert-Paar angegeben, mit dem die Zuordnung zum Transformieren von Elementen im Quellarray definiert wird. Ein Schlüssel-Wert-Paar steht für eine Eigenschaft und den zugehörigen Wert für alle Objekte im Ausgabearray.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Erforderlich* 

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*array*> | Array | Das Array oder der Ausdruck, mit dem die Quellelemente bereitgestellt werden. Stellen Sie sicher, dass Sie einen Ausdruck in doppelte Anführungszeichen setzen. <p>**Hinweis**: Wenn das Quellarray leer ist, wird mit der Aktion ein leeres Array erstellt. | 
| <*key-name*> | String | Eigenschaftenname, der dem Ergebnis über <*expression*>  zugewiesen wird<p>Geben Sie einen Schlüsselnamen (<*key-name*>) für diese Eigenschaft und einen Ausdruck (<*expression*>) für den Eigenschaftswert an, um für alle Objekte im Ausgabearray eine neue Eigenschaft hinzuzufügen. <p>Lassen Sie <*key-name*> für diese Eigenschaft weg, um eine Eigenschaft aus allen Objekten im Array zu entfernen. | 
| <*expression*> | String | Ausdruck, mit dem das Element im Quellarray transformiert und das Ergebnis <*key-name*> zugewiesen wird | 
|||| 

Mit der Aktion **Select** wird ein Array als Ausgabe erstellt. Alle Aktionen, für die diese Ausgabe verwendet werden soll, müssen also entweder ein Array akzeptieren, oder Sie müssen das Array in den Typ konvertieren, der von der Consumeraktion akzeptiert wird. Um beispielsweise das Ausgabearray in eine Zeichenfolge zu konvertieren, können Sie dieses Array an die Aktion **Compose** übergeben und dann über die Aktion **Compose** in Ihren anderen Aktionen auf die Ausgabe verweisen.

*Beispiel*

Mit dieser Aktionsdefinition wird ein JSON-Objektarray aus einem Integer-Array erstellt. Die Aktion durchläuft das Quellarray, ruft die einzelnen ganzzahligen Werte ab, indem der Ausdruck `@item()` verwendet wird, und weist jeden Wert der Eigenschaft „`number`“ in den einzelnen JSON-Objekten zu:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Hier ist das Array angegeben, das mit dieser Aktion erstellt wird:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Übergeben Sie diese Ausgabe in eine **Compose**-Aktion, um die Arrayausgabe in anderen Aktionen zu verwenden:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Sie können dann die Ausgabe der Aktion **Compose** in Ihren anderen Aktionen verwenden, z.B. der Aktion **Office 365 Outlook – Send an email**:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Aktion table

Mit dieser Aktion wird aus einer CSV- oder HTML-Tabelle ein Array erstellt. Für Arrays mit JSON-Objekten erstellt diese Aktion aus den Eigenschaftennamen der Objekte automatisch die Spaltenüberschriften. Für Arrays mit anderen Datentypen müssen Sie die Spaltenüberschriften und -werte angeben. Dieses Array enthält beispielsweise die Eigenschaften „ID“ und „Product_Name“, die von dieser Aktion für die Namen von Spaltenüberschriften verwendet werden können:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Erforderlich* 

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| \<CSV *or* HTML>| String | Format für die Tabelle, die Sie erstellen möchten | 
| <*array*> | Array | Array oder Ausdruck, mit dem die Quellelemente für die Tabelle bereitgestellt werden <p>**Hinweis**: Wenn das Quellarray leer ist, wird mit der Aktion eine leere Tabelle erstellt. | 
|||| 

*Optional*

Verwenden Sie das Array `columns`, um Spaltenüberschriften und -werte anzugeben bzw. anzupassen. Wenn `header-value`-Paare über den gleichen Überschriftennamen verfügen, werden die Werte in derselben Spalte unter diesem Überschriftennamen angezeigt. Andernfalls wird mit jedem eindeutigen Header eine eindeutige Spalte definiert.

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*column-name*> | String | Headername für eine Spalte | 
| <*column-value*> | Any | Wert in dieser Spalte | 
|||| 

*Beispiel 1*

Angenommen, Sie verfügen über die zuvor erstellte Variable „myItemArray“, die derzeit dieses Array enthält:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Mit dieser Aktionsdefinition wird eine CSV-Tabelle aus der Variablen „myItemArray“ erstellt. Der von der `from`-Eigenschaft verwendete Ausdruck ruft das Array aus „myItemArray“ ab, indem die Funktion `variables()` verwendet wird:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Hier ist die CSV-Tabelle angegeben, die mit dieser Aktion erstellt wird: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Beispiel 2*

Mit dieser Aktionsdefinition wird eine HTML-Tabelle aus der Variablen „myItemArray“ erstellt. Der von der `from`-Eigenschaft verwendete Ausdruck ruft das Array aus „myItemArray“ ab, indem die Funktion `variables()` verwendet wird:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Hier ist die HTML-Tabelle angegeben, die mit dieser Aktion erstellt wird: 

<table><thead><tr><th>id</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Äpfel</td></tr><tr><td>1</td><td>Oranges</td></tr></tbody></table>

*Beispiel 3*

Mit dieser Aktionsdefinition wird eine HTML-Tabelle aus der Variablen „myItemArray“ erstellt. In diesem Beispiel werden die Standardnamen der Spaltenüberschriften aber durch „Stock_ID“ und „Description“ überschrieben, und in der Spalte „Description“ wird den Werten das Wort „Organic“ hinzugefügt.

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Hier ist die HTML-Tabelle angegeben, die mit dieser Aktion erstellt wird: 

<table><thead><tr><th>Stock_ID</th><th>BESCHREIBUNG</th></tr></thead><tbody><tr><td>0</td><td>Organic Apples</td></tr><tr><td>1</td><td>Organic Oranges</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Terminate-Aktion

Mit dieser Aktion wird die Ausführung für eine Workflowinstanz beendet, alle aktiven Aktionen werden abgebrochen, alle verbleibenden Aktionen werden übersprungen und der angegebene Status wird zurückgegeben. Sie können die **Terminate**-Aktion beispielsweise verwenden, wenn Ihre Logik-App nach einem Fehlerzustand vollständig beendet werden muss. Diese Aktion wirkt sich nicht auf bereits abgeschlossene Aktionen aus und kann nicht in **Foreach**- und **Until**-Schleifen, einschließlich sequenziellen Schleifen, enthalten sein.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*status*> | String | Der zurückzugebende Status für die Ausführung: „Failed“, „Cancelled“, „Succeeded“ |
|||| 

*Optional*

Die Eigenschaften für das Objekt „runStatus“ gelten nur, wenn die „runStatus“-Eigenschaft auf den Status „Failed“ festgelegt ist.

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*error-code-or-name*> | String | Code oder Name für den Fehler |
| <*error-message*> | String | Nachricht oder Text, mit der bzw. dem der Fehler und alle Aktionen beschrieben werden, die vom Benutzer durchgeführt werden können | 
|||| 

*Beispiel*

Mit dieser Aktion wird eine Workflowausführung beendet und der Status der Ausführung auf „Failed“ festgelegt, und außerdem werden der Status, ein Fehlercode und eine Fehlermeldung zurückgegeben:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Wait-Aktion

Mit dieser Aktion wird die Workflowausführung für den angegebenen Zeitraum oder bis zum angegebenen Zeitpunkt (aber nicht beides) unterbrochen.

*Angegebener Zeitraum*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Angegebener Zeitpunkt*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*number-of-units*> | Integer | Für die Aktion **Delay** die Anzahl von Zeiteinheiten für die Wartedauer | 
| <*interval*> | String | Das zu wartende Intervall für die Aktion **Delay**: „Second“, „Minute“, „Hour“, „Day“, „Week“, „Month“ | 
| <*date-time-stamp*> | String | Für die Aktion **Delay Until** das Datum und die Uhrzeit zum Fortsetzen der Ausführung. Für diesen Wert muss das [UTC-Format für Datum und Uhrzeit](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) verwendet werden. | 
|||| 

*Beispiel 1*

Mit dieser Aktionsdefinition wird der Workflow 15 Minuten lang angehalten:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Beispiel 2*

Mit dieser Aktionsdefinition wird der Workflow bis zum angegebenen Zeitpunkt angehalten:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Workflow-Aktion

Mit dieser Aktion wird eine andere zuvor erstellte Logik-App aufgerufen. Dies bedeutet, dass Sie andere Logik-App-Workflows einfügen und wiederverwenden können. Sie können auch die Ausgaben aus der untergeordneten oder *geschachtelten* Logik-App in Aktionen verwenden, die auf die geschachtelte Logik-App folgen, solange die untergeordnete Logik-App eine Antwort zurückgibt.

Die Logic Apps-Engine überprüft den Zugriff auf den Trigger, den Sie aufrufen möchten. Stellen Sie also sicher, dass Sie auf diesen Trigger zugreifen können. Darüber hinaus muss die geschachtelte Logik-App die folgenden Kriterien erfüllen:

* Ein Trigger macht die geschachtelte Logik-App aufrufbar, z.B. ein [Anforderungstrigger](#request-trigger) oder [HTTP-Trigger](#http-trigger).

* Es wird dasselbe Azure-Abonnement wie für Ihre übergeordnete Logik-App verwendet.

* Die geschachtelte Logik-App muss über eine [Response](#response-action)-Aktion verfügen, damit die Ausgaben der geschachtelten Logik-App in Ihrer übergeordneten Logik-App verwendet werden können.

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | String | Name für die Logik-App, die Sie aufrufen möchten | 
| <*trigger-name*> | String | Name für den Trigger in der geschachtelten Logik-App, die Sie aufrufen möchten | 
| <*Azure-subscription-ID*> | String | Azure-Abonnement-ID für die geschachtelte Logik-App |
| <*Azure-resource-group*> | String | Name der Azure-Ressourcengruppe für die geschachtelte Logik-App |
| <*nested-logic-app-name*> | String | Name für die Logik-App, die Sie aufrufen möchten |
||||

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------|  
| <*header-content*> | JSON-Objekt | Alle Header, die mit dem Aufruf gesendet werden sollen | 
| <*body-content*> | JSON-Objekt | Beliebiger Nachrichteninhalt, der mit dem Aufruf gesendet werden soll | 
||||

*Ausgaben*

Die Ausgaben dieser Aktion variieren je nach der Aktion „Response“ der geschachtelten Logik-App. Falls die geschachtelte Logik-App keine Aktion „Response“ enthält, sind die Ausgaben leer.

*Beispiel*

Nachdem die Aktion „Start_search“ erfolgreich abgeschlossen wurde, wird mit dieser Workflow-Aktionsdefinition eine andere Logik-App mit dem Namen „Get_product_information“ aufgerufen, mit der die angegebenen Eingaben übergeben werden:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Details zu den Aktionen zur Steuerung des Workflows

<a name="foreach-action"></a>

### <a name="foreach-action"></a>ForEach-Aktion

Diese Schleifenaktion durchläuft ein Array und führt Aktionen für die einzelnen Arrayelemente aus. Standardmäßig wird die „for each“-Schleife bis zu einer maximalen Schleifenanzahl parallel ausgeführt. Informationen zu dieser maximalen Anzahl finden Sie unter [Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Informieren Sie sich darüber, [wie Sie „for each“-Schleifen erstellen](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Erforderlich* 

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*action-1...n*> | String | Namen der Aktionen, die für jedes Arrayelement ausgeführt werden | 
| <*action-definition-1...n*> | JSON-Objekt | Definitionen der ausgeführten Aktionen | 
| <*for-each-expression*> | String | Ausdruck, mit dem im angegebenen Array auf die einzelnen Elemente verwiesen wird | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*count*> | Integer | Standardmäßig werden die Iterationen für „for each“-Schleifen gleichzeitig (oder parallel) ausgeführt, bis das [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) erreicht ist. Informationen dazu, wie Sie dieses Limit ändern, indem Sie einen neuen <*count*>-Wert festlegen, finden Sie unter [Ändern der Parallelität von „for each“-Schleifen](#change-for-each-concurrency). | 
| <*operation-option*> | String | Wenn Sie eine „for each“-Schleife nicht parallel, sondern sequenziell durchführen möchten, legen Sie entweder <*operation-option*> auf `Sequential` oder <*count*> auf `1` fest (aber nicht beides). Weitere Informationen finden Sie unter [Sequenzielles Ausführen von „for each“-Schleifen](#sequential-for-each). | 
|||| 

*Beispiel*

Diese „for each“-Schleife sendet eine E-Mail für jedes Element des Arrays, das Anlagen einer eingehenden E-Mail enthält. Die Schleife sendet eine E-Mail, einschließlich Anlage, an eine Person, die die Anlage überprüft.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Um nur ein Array anzugeben, das als Ausgabe des Triggers übergeben wird, ruft dieser Ausdruck das Array <*array-name*> aus dem Triggertext ab. Im Ausdruck wird der Operator `?` verwendet, um einen Ausfall zu vermeiden, falls das Array nicht vorhanden ist:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>If-Aktion

Mit dieser Aktion, bei der es sich um eine *bedingte Anweisung* handelt, wird ein Ausdruck ausgewertet. Dieser stellt eine Bedingung dar und führt in Abhängigkeit davon, ob die Bedingung wahr oder falsch ist, jeweils eine andere Branch aus. Wenn die Bedingung wahr ist, erhält die Bedingung den Status „Succeeded“. Informieren Sie sich darüber, [wie Sie bedingte Anweisungen erstellen](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*condition*> | JSON-Objekt | Auszuwertende Bedingung, bei der es sich auch um einen Ausdruck handeln kann | 
| <*action-1*> | JSON-Objekt | Aktion, die ausgeführt werden soll, wenn <*condition*> als „true“ ausgewertet wird | 
| <*action-definition*> | JSON-Objekt | Definition für die Aktion | 
| <*action-2*> | JSON-Objekt | Aktion, die ausgeführt werden soll, wenn <*condition*> als „false“ ausgewertet wird | 
|||| 

Die Aktionen in den Objekten `actions` oder `else` erhalten die folgenden Status:

* „Erfolgreich“, wenn sie erfolgreich ausgeführt wurden
* „Fehlerhaft“, wenn sie nicht erfolgreich ausgeführt wurden
* „Übersprungen“, wenn die entsprechende Verzweigung nicht ausgeführt wird

*Beispiel*

Mit dieser Bedingung wird angegeben, dass der Workflow eine Website überprüft, wenn der Wert der Integer-Variable größer als null ist. Wenn die Variable null oder kleiner ist, überprüft der Workflow eine andere Website.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Verwendung von Ausdrücken durch Bedingungen

Die folgenden Beispiele veranschaulichen die Verwendung von Ausdrücken in Bedingungen:
  
| JSON | Ergebnis | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | Ausschließlich für boolesche Ausdrücke wird die Bedingung für alle Werte übergeben, die als „true“ ausgewertet werden. <p>Andere Typen können mithilfe der Funktion `empty()` oder `equals()` in boolesche Werte konvertiert werden. | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | Für Vergleichsfunktionen wird die Aktion nur ausgeführt, wenn die Ausgabe von <*action*> größer als der <*threshold*>-Wert ist. | 
| "expression": "@or(greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | Für Logikfunktionen und die Erstellung von geschachtelten booleschen Ausdrücken wird die Aktion ausgeführt, wenn die Ausgabe von <*action*> größer als der <*threshold*>-Wert bzw. kleiner als 100 ist. | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | Sie können Arrayfunktionen verwenden, um zu überprüfen, ob das Array über Elemente verfügt. Die Aktion wird ausgeführt, wenn das Array `errors` leer ist. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Scope-Aktion

Mit dieser Aktion werden Aktionen logisch zu *Bereichen* (Scopes) gruppiert, die einen eigenen Status erhalten, nachdem die Ausführung der Aktionen in diesem Bereich beendet ist. Sie können den Status des Bereichs dann verwenden, um zu ermitteln, ob andere Aktionen ausgeführt werden. Informieren Sie sich darüber, [wie Sie Bereiche erstellen](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------|  
| <*inner-action-1...n*> | JSON-Objekt | Eine oder mehrere Aktionen, die innerhalb des Bereichs ausgeführt werden |
| <*action-inputs*> | JSON-Objekt | Eingaben für jede Aktion |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Switch-Aktion

Mit dieser Aktion, die auch als *switch-Anweisung* bezeichnet wird, werden andere Aktionen als *Fälle* organisiert. Jedem Fall, mit Ausnahme des Standardfalls (sofern vorhanden), wird ein Wert zugewiesen. Wenn Ihr Workflow ausgeführt wird, vergleicht die Aktion **Switch** den Wert eines Ausdrucks, Objekts oder Tokens mit den Werten, die für jeden Fall angegeben wurden. Falls die Aktion **Switch** einen übereinstimmenden Fall findet, führt Ihr Workflow nur die Aktionen für diesen Fall aus. Bei jeder Ausführung der Aktion **Switch** ist entweder nur ein übereinstimmender Fall vorhanden, oder es sind keine Übereinstimmungen vorhanden. Falls keine Übereinstimmungen vorhanden sind, werden mit der Aktion **Switch** die Standardaktionen ausgeführt. Erfahren Sie, [wie Sie switch-Anweisungen erstellen](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Varies | Ausdruck, JSON-Objekt oder Token für die Auswertung | 
| <*action-name*> | String | Name der Aktion, die für den übereinstimmenden Fall ausgeführt werden soll | 
| <*action-definition*> | JSON-Objekt | Definition für die Aktion, die für den übereinstimmenden Fall ausgeführt werden soll | 
| <*matching-value*> | Varies | Wert, der mit dem Auswertungsergebnis verglichen werden soll | 
|||| 

*Optional*

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*default-action-name*> | String | Name der Standardaktion, die ausgeführt wird, wenn keine übereinstimmenden Fälle vorhanden sind | 
| <*default-action-definition*> | JSON-Objekt | Definition für die Aktion, die ausgeführt wird, wenn kein übereinstimmender Fall vorhanden ist | 
|||| 

*Beispiel*

Mit dieser Aktionsdefinition wird ausgewertet, ob die Person, die auf die E-Mail mit der Genehmigungsanforderung die Option „Approve“ (Genehmigen) oder „Reject“ (Ablehnen) ausgewählt hat. Basierend auf dieser Auswahl führt die **Switch**-Aktion die Aktionen für den übereinstimmenden Fall aus. Hierbei geht es um das Senden einer weiteren E-Mail an die antwortende Stelle, die aber jedes Mal einen anderen Text enthält. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Until-Aktion

Diese Schleifenaktion enthält Aktionen, die so lange ausgeführt werden, bis die angegebene Bedingung „true“ ist. Mit der Schleife wird die Bedingung als letzter Schritt überprüft, nachdem alle anderen Aktionen ausgeführt wurden. Sie können mehr als eine Aktion in das `"actions"`-Objekt einfügen, und mit der Aktion muss mindestens ein Limit definiert werden. Erfahren Sie, [wie Sie „until“-Schleifen erstellen](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Wert | type | BESCHREIBUNG | 
|-------|------|-------------| 
| <*action-name*> | String | Name für die Aktion, die Sie in der Schleife ausführen möchten | 
| <*action-type*> | String | Aktionstyp, den Sie ausführen möchten | 
| <*action-inputs*> | Verschiedene | Eingaben für die auszuführende Aktion | 
| <*condition*> | String | Die Bedingung oder der Wert, die bzw. der ausgewertet wird, nachdem die Ausführung aller Aktionen der Schleife beendet ist | 
| <*loop-count*> | Integer | Der Grenzwert für die maximale Anzahl von Schleifen, die von der Aktion ausgeführt werden können. Weitere Informationen zum Standardgrenzwert und Maximalgrenzwert finden Sie unter [Grenzwerte und Konfiguration für Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*loop-timeout*> | String | Der Grenzwert für längste Ausführungsdauer, die für die Schleife möglich ist. Der Standardwert für `timeout` lautet `PT1H`. Dies ist das erforderliche [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

> [!NOTE]
> Wenn der Ausdruck von der Ausgabe einer Aktion innerhalb der Until-Schleife abhängig ist, stellen Sie sicher, dass Sie alle Fehler berücksichtigen, die aus dieser Aktion resultieren.

*Beispiel*

Mit dieser Schleifenaktionsdefinition wird eine HTTP-Anforderung an die angegebene URL gesendet, bis eine dieser Bedingungen erfüllt ist:

* Die Anforderung erhält eine Antwort mit dem Statuscode „200 OK“.
* Die Schleife wurde sechzig Mal ausgeführt.
* Die Schleife wurde eine Stunde lang ausgeführt.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks und Abonnements

Webhook-basierte Trigger und Aktionen führen keine regelmäßigen Überprüfungen von Endpunkten durch, sondern warten stattdessen auf bestimmte Ereignisse oder Daten an diesen Endpunkten. Diese Trigger und Aktionen *abonnieren* die Endpunkte, indem sie eine *Rückruf-URL* bereitstellen, an die der Endpunkt Antworten senden kann.

Der `subscribe`-Aufruf wird bei jeder Veränderung des Workflows ausgeführt (beispielsweise bei einer Erneuerung der Anmeldeinformationen oder bei einer Änderung der Eingabeparameter für einen Trigger oder eine Aktion). Für diesen Aufruf werden die gleichen Parameter wie bei HTTP-Standardaktionen verwendet. 

Der `unsubscribe`-Aufruf erfolgt automatisch, wenn ein Vorgang dazu führt, dass der Trigger oder die Aktion ungültig wird, z.B.:

* Löschen oder Deaktivieren des Triggers 
* Löschen oder Deaktivieren des Workflows 
* Löschen oder Deaktivieren des Abonnements 

Zur Unterstützung dieser Aufrufe gibt der `@listCallbackUrl()`-Ausdruck eine eindeutige „Rückruf-URL“ für den Trigger oder die Aktion zurück. Diese URL stellt einen eindeutigen Bezeichner für die Endpunkte dar, die die REST-API des Diensts verwenden. Die Parameter für diese Funktion entsprechen denen des Webhook-Triggers bzw. der -Aktion.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Ändern der asynchronen Dauer

Sowohl für Trigger als auch für Aktionen können Sie die Dauer für das asynchrone Muster auf ein bestimmtes Zeitintervall begrenzen, indem Sie die `limit.timeout`-Eigenschaft hinzufügen. Auf diese Weise wird der Status der Aktion als `Cancelled` mit dem Code `ActionTimedOut` gekennzeichnet, falls die Aktion nach Ablauf des Intervalls noch nicht beendet war. Für die `timeout`-Eigenschaft wird das [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) verwendet.

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Einstellungen für Laufzeitkonfiguration

Sie können das Standardlaufzeitverhalten für Trigger und Aktionen ändern, indem Sie diese `runtimeConfiguration`-Eigenschaften in der Trigger- oder Aktionsdefinition hinzufügen.

| Eigenschaft | type | BESCHREIBUNG | Trigger oder Aktion | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Ändern Sie das [*Standardlimit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) für die Anzahl von Workflowinstanzen, die gleichzeitig (oder parallel) ausgeführt werden können. Das Anpassen dieses Werts kann die Anzahl von Anforderungen reduzieren, die auf Back-End-Systemen eingehen. <p>Das Festlegen der `runs`-Eigenschaft auf `1` funktioniert genauso wie das Festlegen der `operationOptions`-Eigenschaft auf `SingleInstance`. Sie können jeweils eine Eigenschaft festlegen, aber nicht beide. <p>Informationen zum Ändern des Standardlimits finden Sie unter [Ändern der Triggerparallelität](#change-trigger-concurrency) oder [Sequenzielles Auslösen von Instanzen](#sequential-trigger). | Alle Trigger | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Ändern Sie das [*Standardlimit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) für die Anzahl von Workflowinstanzen, die auf die Ausführung warten müssen, wenn für Ihre Logik-App bereits die maximale Anzahl von gleichzeitigen Instanzen ausgeführt wird. <p>Informationen zum Ändern des Standardlimits finden Sie unter [Ändern des Limits für wartende Ausführungen](#change-waiting-runs). | Alle Trigger | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Ändern Sie das [*Standardlimit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) für die Anzahl von Iterationen der „for each“-Schleife, die gleichzeitig (oder parallel) ausgeführt werden können. <p>Das Festlegen der `repetitions`-Eigenschaft auf `1` funktioniert genauso wie das Festlegen der `operationOptions`-Eigenschaft auf `SingleInstance`. Sie können jeweils eine Eigenschaft festlegen, aber nicht beide. <p>Informationen zum Ändern des Standardlimits finden Sie unter [Ändern der „for each“-Parallelität](#change-for-each-concurrency) oder [Sequenzielles Ausführen von „for each“-Schleifen](#sequential-for-each). | Aktion: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | Für bestimmte Aktionen, die die Paginierung unterstützen und diese aktiviert haben, gibt dieser Wert die *minimale* Anzahl der abzurufenden Ergebnisse an. <p>Informationen für das Aktivieren der Paginierung finden Sie unter [Get bulk data, items, or results by using pagination (Abrufen von Massendaten, Elementen oder Ergebnissen mithilfe der Paginierung)](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). | Aktion: Verschiedene |
| `runtimeConfiguration.secureData.properties` | Array | Bei vielen Triggern und Aktionen blenden diese Einstellungen Eingaben, Ausgaben oder beides im Ausführungsverlauf der Logik-App aus. <p>Informationen zum Schützen dieser Daten finden Sie unter [Ausblenden von Ein- und Ausgaben im Ausführungsverlauf](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Gilt für die meisten Trigger und Aktionen |
| `runtimeConfiguration.staticResult` | JSON-Objekt | Für Aktionen, die die Einstellung [statisches Ergebnis](../logic-apps/test-logic-apps-mock-data-static-results.md) unterstützen und für die diese aktiviert ist, hat das `staticResult`-Objekt diese Attribute: <p>- `name`, das auf den Definitionsnamen des statischen Ergebnisses der aktuelle Aktion verweist, der innerhalb des `staticResults`-Attributs im `definition`-Attribut des Workflows Ihrer Logik-App vorkommt. Weitere Informationen finden Sie unter [Statische Ergebnisse – Schemareferenz zur Definitionssprache für Workflows](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, das angibt, ob statische Ergebnisse `Enabled` sind oder nicht für die aktuelle Aktion. <p>Informationen zum Aktivieren statischer Ergebnisse finden Sie unter [Testen von Logik-Apps mit Simulationsdaten durch die Einrichtung von statischen Ergebnissen](../logic-apps/test-logic-apps-mock-data-static-results.md). | Aktion: Verschiedene |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Optionen für den Vorgang

Sie können das Standardverhalten für Trigger und Aktionen mit der `operationOptions`-Eigenschaft in der Trigger- oder Aktionsdefinition ändern.

| Vorgangsoption | type | BESCHREIBUNG | Trigger oder Aktion | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | Dient zum synchronen Ausführen von HTTP-basierten Aktionen (anstatt asynchron). <p><p>Informationen zum Festlegen dieser Option finden Sie unter [Synchrones Ausführen von Aktionen](#disable-asynchronous-pattern). | Aktionen: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Antwort](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | String | Schließen Sie für Logik-Apps, die [Azure Active Directory Open Authentication (Azure AD OAuth) aktivieren](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth), um den Zugriff für eingehende Aufrufe an einen anforderungsbasierten Triggerendpunkt zu autorisieren, den `Authorization`-Header aus dem OAuth-Zugriffstoken in die Triggerausgaben ein. Weitere Informationen finden Sie unter [Einschließen des „Authorization“-Headers in Anforderungstriggerausgaben](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Trigger: <p>[Anforderung](#request-trigger), <br>[HTTP Webhook](#http-webhook-trigger) | 
| `Sequential` | String | Dient zum einzelnen Ausführen der Iterationen von „for each“-Schleifen, anstatt alle auf einmal parallel. <p>Diese Option funktioniert genauso wie das Festlegen der `runtimeConfiguration.concurrency.repetitions`-Eigenschaft auf `1`. Sie können jeweils eine Eigenschaft festlegen, aber nicht beide. <p><p>Informationen zum Festlegen dieser Option finden Sie unter [Sequenzielles Ausführen von „for each“-Schleifen](#sequential-for-each).| Aktion: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | Dient zum sequenziellen Ausführen des Triggers für jede Logik-App-Instanz. Es wird gewartet, bis die zuvor aktive Ausführung beendet ist, bevor die nächste Logik-App-Instanz ausgelöst wird. <p><p>Diese Option funktioniert genauso wie das Festlegen der `runtimeConfiguration.concurrency.runs`-Eigenschaft auf `1`. Sie können jeweils eine Eigenschaft festlegen, aber nicht beide. <p>Informationen zum Festlegen dieser Option finden Sie unter [Sequenzielles Auslösen von Instanzen](#sequential-trigger). | Alle Trigger | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Ändern der Triggerparallelität

Standardmäßig werden alle Logik-App-Workflowinstanzen gleichzeitig (oder parallel) ausgeführt. Dieses Verhalten bedeutet, dass jede Triggerinstanz ausgelöst wird, bevor die Ausführung der zuvor aktiven Workflowinstanz beendet ist. Die Anzahl der gleichzeitig ausgeführten Instanzen hat jedoch ein [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wenn die Anzahl von gleichzeitig ausgeführten Workflowinstanzen dieses Limit erreicht, müssen alle anderen neuen Instanzen auf ihre Ausführung warten. Mit diesem Limit wird die Anzahl von Anforderungen gesteuert, die auf Back-End-Systemen eingehen.

Wenn Sie die Parallelitätssteuerung des Auslösers aktivieren, werden Auslöserinstanzen parallel bis zum [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) ausgeführt. Zum Ändern des Standardparallelitätslimits können Sie entweder den Codeansicht-Editor oder den Logik-App-Designer nutzen, da durch das Ändern der Parallelitätseinstellung über den Designer die `runtimeConfiguration.concurrency.runs`-Eigenschaft in der zugrunde liegenden Triggerdefinition hinzugefügt bzw. aktualisiert wird (und umgekehrt). Diese Eigenschaft steuert die maximale Anzahl von neuen Workflowinstanzen, die parallel ausgeführt werden können.

Hier finden Sie einige Erwägungen für den Fall, dass Sie die Parallelität auf einem Auslöser aktivieren möchten:

* Wenn Parallelität aktiviert ist, wird das [SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)-Limit für das [Auflösen von Arraybatches](#split-on-debatch) wesentlich reduziert. Wenn die Anzahl der Elemente dieses Limit überschreitet, wird die SplitOn-Funktion deaktiviert.

* Die Parallelität kann nicht deaktiviert werden, nachdem Sie die Parallelitätssteuerung aktiviert haben.

* Wenn Parallelität aktiviert ist, könnte eine Logik-App-Instanz mit langer Ausführungszeit dazu führen, dass neue Logik-App-Instanzen in den Wartezustand wechseln. Dieser Zustand verhindert, dass Azure Logic Apps neue Instanzen erstellt, und er tritt auch dann ein, wenn die Anzahl gleichzeitiger Ausführungen kleiner als die angegebene maximale Anzahl gleichzeitiger Ausführungen ist.

  * Um diesen Zustand zu unterbrechen, brechen Sie die frühesten Instanzen ab, die *noch ausgeführt werden*.

    1. Wählen Sie im Menü Ihrer Logik-App die Option **Übersicht** aus.

    1. Wählen Sie im Abschnitt **Ausführungsverlauf** die früheste Instanz aus, die noch ausgeführt wird, z. B.:

       ![Auswählen der frühesten ausgeführten Instanz](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Um nur Instanzen anzuzeigen, die noch ausgeführt werden, öffnen Sie die Liste **Alle**, und wählen Sie **Wird ausgeführt** aus.

    1. Wählen Sie unter **Logik-App-Ausführung** die Option **Ausführung abbrechen** aus.

       ![Auffinden der frühesten ausgeführten Instanz](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Um diese Möglichkeit zu umgehen, fügen Sie jeder Aktion einen Timeout hinzu, der diese Ausführungen aufhalten könnte. Wenn Sie im Code-Editor arbeiten, finden Sie weitere Informationen unter [Ändern der asynchronen Dauer](#asynchronous-limits). Andernfalls, wenn Sie den Designer verwenden, führen Sie diese Schritte aus:

    1. Wählen Sie in Ihrer Logik-App bei der Aktion, der Sie einen Timeout hinzufügen möchten, in der oberen rechten Ecke die Auslassungspunkte ( **...** ) aus, und wählen Sie dann **Einstellungen** aus.

       ![Öffnen der Aktionseinstellungen](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Geben Sie unter **Timeout** die Timeoutdauer im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) an.

       ![Angeben der Timeoutdauer](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Um Ihre Logik-App sequenziell auszuführen, legen Sie die Parallelität des Triggers entweder mithilfe des Codeansicht-Editors oder des Designers auf `1` fest. Stellen Sie sicher, dass Sie nicht auch die `operationOptions`-Eigenschaft des Triggers im Codeansicht-Editor auf `SingleInstance` festlegen. Andernfalls erhalten Sie einen Validierungsfehler. Weitere Informationen finden Sie unter [Sequenzielles Auslösen von Instanzen](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Bearbeiten in der Codeansicht 

In der zugrunde liegenden Triggerdefinition fügen Sie die Eigenschaft `runtimeConfiguration.concurrency.runs` hinzu, die einen Wert zwischen `1` und `50` besitzen kann.

Hier ist ein Beispiel angegeben, in dem gleichzeitige Ausführungen auf zehn Instanzen begrenzt werden:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Weitere Informationen finden Sie unter den [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Bearbeiten im Logik-App-Designer

1. Wählen Sie in der oberen rechten Ecke des Triggers die Ellipsenschaltfläche ( **...** ) und dann **Einstellungen** aus.

1. Legen Sie unter **Gleichzeitigkeitssteuerung** die Option **Grenzwert** auf **Ein** fest. 

1. Ziehen Sie den Schieberegler **Parallelitätsgrad** auf den gewünschten Wert. Um Ihre Logik-App sequenziell auszuführen, ziehen Sie den Schieberegler auf **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Ändern der „for each“-Parallelität

Standardmäßig werden alle Iterationen für „for each“-Schleifen gleichzeitig (oder parallel) ausgeführt. Dieses Verhalten bedeutet, dass die Ausführung jeder Iteration gestartet wird, bevor die Ausführung der vorherigen Iteration abgeschlossen ist. Die Anzahl der gleichzeitig ausgeführten Iterationen hat jedoch ein [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wenn die Anzahl von gleichzeitig ausgeführten Iterationen dieses Limit erreicht, müssen alle anderen Iterationen auf ihre Ausführung warten.

Zum Ändern des Standardlimits können Sie entweder den Codeansicht-Editor oder den Logik-App-Designer nutzen, da durch das Ändern der Parallelitätseinstellung über den Designer die `runtimeConfiguration.concurrency.repetitions`-Eigenschaft in der zugrunde liegenden „for each“-Aktionsdefinition hinzugefügt bzw. aktualisiert wird (und umgekehrt). Diese Eigenschaft steuert die maximale Anzahl von Iterationen, die parallel ausgeführt werden können.

> [!NOTE] 
> Wenn Sie für die „for each“-Aktion die sequenzielle Ausführung festlegen, indem Sie entweder den Designer oder den Codeansicht-Editor verwenden, sollten Sie die `operationOptions`-Eigenschaft der Aktion im Codeansicht-Editor nicht auf `Sequential` festlegen. Andernfalls erhalten Sie einen Validierungsfehler. Weitere Informationen finden Sie unter [Sequenzielles Ausführen von „for each“-Schleifen](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Bearbeiten in der Codeansicht 

In der zugrunde liegenden „for each“-Definition fügen Sie die Eigenschaft `runtimeConfiguration.concurrency.repetitions` hinzu, die einen Wert zwischen `1` und `50` besitzen kann, oder aktualisieren sie.

Hier ist ein Beispiel angegeben, in dem gleichzeitige Ausführungen auf zehn Iterationen begrenzt werden:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Weitere Informationen finden Sie unter den [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Bearbeiten im Logik-App-Designer

1. Wählen Sie in der Aktion **For each** oben rechts die Schaltfläche mit den Auslassungspunkten ( **...** ) und dann **Einstellungen** aus.

1. Legen Sie unter **Gleichzeitigkeitssteuerung** die Option **Gleichzeitigkeitssteuerung** auf **Ein** fest.

1. Ziehen Sie den Schieberegler **Parallelitätsgrad** auf den gewünschten Wert. Um Ihre Logik-App sequenziell auszuführen, ziehen Sie den Schieberegler auf **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Ändern des Limits für wartende Ausführungen

Standardmäßig werden alle Logik-App-Workflowinstanzen gleichzeitig (oder parallel) ausgeführt. Dieses Verhalten bedeutet, dass jede Triggerinstanz ausgelöst wird, bevor die Ausführung der zuvor aktiven Workflowinstanz beendet ist. Die Anzahl der gleichzeitig ausgeführten Instanzen hat jedoch ein [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wenn die Anzahl von gleichzeitig ausgeführten Workflowinstanzen dieses Limit erreicht, müssen alle anderen neuen Instanzen auf ihre Ausführung warten.

Die Anzahl der wartenden Ausführungen besitzt ebenfalls ein [Standardlimit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Wenn die Anzahl der wartenden Ausführungen dieses Limit erreicht, akzeptiert die Logic Apps-Engine keine neuen Ausführungen mehr. Anforderungs- und Webhook-Trigger geben Fehler vom Typ 429 zurück, und sich wiederholende Trigger beginnen damit, Abfrageversuche zu überspringen.

Sie können nicht nur das [Standardlimit für die Triggerparallelität ändern](#change-trigger-concurrency), sondern auch das Standardlimit für wartende Ausführungen. In der zugrunde liegenden Triggerdefinition fügen Sie die Eigenschaft `runtimeConfiguration.concurrency.maximumWaitingRuns` hinzu, die einen Wert zwischen `1` und `100` besitzen kann.

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Weitere Informationen finden Sie unter den [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Sequenzielles Auslösen von Instanzen

Legen Sie für den Trigger die sequenzielle Ausführung fest, damit jede Logik-App-Workflowinstanz erst nach Beendigung der vorherigen Instanz ausgeführt wird. Sie können entweder den Codeansicht-Editor oder den Logik-App-Designer nutzen, da durch das Ändern der Parallelitätseinstellung über den Designer auch die `runtimeConfiguration.concurrency.runs`-Eigenschaft in der zugrunde liegenden Triggerdefinition hinzugefügt bzw. aktualisiert wird (und umgekehrt).

> [!NOTE] 
> Wenn Sie für den Trigger die sequenzielle Ausführung festlegen, indem Sie entweder den Designer oder den Codeansicht-Editor verwenden, sollten Sie die `operationOptions`-Eigenschaft des Triggers im Codeansicht-Editor nicht auf `Sequential` festlegen. Andernfalls erhalten Sie einen Validierungsfehler. 

#### <a name="edit-in-code-view"></a>Bearbeiten in der Codeansicht

Legen Sie in der Triggerdefinition eine dieser Eigenschaften fest, aber nicht beide. 

Legen Sie die `runtimeConfiguration.concurrency.runs`-Eigenschaft auf `1` fest:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*-oder-*

Legen Sie die `operationOptions`-Eigenschaft auf `SingleInstance` fest:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Weitere Informationen finden Sie unter [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options) und [Vorgangsoptionen](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Bearbeiten im Logik-App-Designer

1. Wählen Sie in der oberen rechten Ecke des Triggers die Ellipsenschaltfläche ( **...** ) und dann **Einstellungen** aus.

1. Legen Sie unter **Gleichzeitigkeitssteuerung** die Option **Grenzwert** auf **Ein** fest. 

1. Ziehen Sie den Schieberegler **Parallelitätsgrad** auf den Wert `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Sequenzielles Ausführen von „for each“-Schleifen

Legen Sie für die Aktion „for each“ die sequenzielle Ausführung fest, damit die Iteration einer „for each“-Schleife erst nach Abschluss der vorherigen Iteration ausgeführt wird. Sie können entweder den Codeansicht-Editor oder den Logik-App-Designer nutzen, da durch das Ändern der Parallelität der Aktion über den Designer auch die `runtimeConfiguration.concurrency.repetitions`-Eigenschaft in der zugrunde liegenden Aktionsdefinition hinzugefügt bzw. aktualisiert wird (und umgekehrt).

> [!NOTE] 
> Wenn Sie für eine „for each“-Aktion die sequenzielle Ausführung festlegen, indem Sie entweder den Designer oder den Codeansicht-Editor verwenden, sollten Sie die `operationOptions`-Eigenschaft der Aktion im Codeansicht-Editor nicht auf `Sequential` festlegen. Andernfalls erhalten Sie einen Validierungsfehler. 

#### <a name="edit-in-code-view"></a>Bearbeiten in der Codeansicht

Legen Sie in der Aktionsdefinition eine dieser Eigenschaften fest, aber nicht beide. 

Legen Sie die `runtimeConfiguration.concurrency.repetitions`-Eigenschaft auf `1` fest:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*-oder-*

Legen Sie die `operationOptions`-Eigenschaft auf `Sequential` fest:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Weitere Informationen finden Sie unter [Einstellungen für die Laufzeitkonfiguration](#runtime-config-options) und [Vorgangsoptionen](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Bearbeiten im Logik-App-Designer

1. Wählen Sie in der Aktion **For each** oben rechts die Schaltfläche mit den Auslassungspunkten ( **...** ) und dann **Einstellungen** aus.

1. Legen Sie unter **Gleichzeitigkeitssteuerung** die Option **Gleichzeitigkeitssteuerung** auf **Ein** fest.

1. Ziehen Sie den Schieberegler **Parallelitätsgrad** auf den Wert `1`.

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Ausführen von Aktionen in einem synchronen Vorgangsmuster

Standardmäßig folgen die HTTP-Aktion und die APIConnection-Aktionen in Azure Logic Apps dem standardmäßigen [*asynchronen Vorgangsmuster*](/azure/architecture/patterns/async-request-reply), während die Response-Aktion (Antwort) dem *synchronen Vorgangsmuster* folgt. Das asynchrone Muster gibt an, dass der Empfänger, nachdem eine Aktion eine Anforderung aufgerufen oder an den Endpunkt, Dienst, das System oder die API gesendet hat, sofort eine [„202 ACCEPTED“](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)-Antwort zurückgibt. Dieser Code bestätigt, dass der Empfänger die Anforderung akzeptiert, aber die Verarbeitung noch nicht abgeschlossen hat. Die Antwort kann einen `location`-Header enthalten, in dem die URL und eine Aktualisierungs-ID angegeben sind, die der Aufrufer zum fortlaufenden Abrufen oder Überprüfen des Status der asynchronen Anforderung verwenden kann, bis der Empfänger die Verarbeitung beendet und eine [„200 OK“](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1)-Erfolgsantwort oder eine andere Nicht-202-Antwort zurückgibt. Weitere Informationen finden Sie unter [Asynchrone Microservice-Integration erzwingt Microservice-Autonomie](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* Im Logik-App-Designer verfügen die HTTP-Aktion, die APIConnection-Aktionen und die Response-Aktion über die Einstellung **Asynchrones Muster**. Wenn diese Einstellung aktiviert ist, gibt sie an, dass der Aufrufer nicht auf den Abschluss der Verarbeitung wartet, sondern mit der nächsten Aktion fortfahren kann, dass er aber weiterhin den Status überprüft, bis die Verarbeitung beendet wird. Wenn diese Einstellung deaktiviert ist, gibt sie an, dass der Aufrufer auf den Abschluss der Verarbeitung wartet, bevor mit der nächsten Aktion fortfährt. Führen Sie die folgenden Schritte aus, um diese Einstellung zu suchen:

  1. Wählen Sie auf der Titelleiste der HTTP-Aktion die Schaltfläche mit den Auslassungspunkten ( **...** ) aus, woraufhin die Einstellungen der Aktion geöffnet werden.

  1. Suchen Sie die Einstellung **Asynchrones Muster**.

     ![Einstellung „Asynchrones Muster“](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* In der JSON-Definition (JavaScript Object Notation), die der Aktion zugrunde liegt, folgen die HTTP-Aktion und die APIConnection-Aktionen implizit dem asynchronen Vorgangsmuster.

In einigen Szenarien möchten Sie eventuell, dass eine Aktion stattdessen dem synchronen Muster folgt. Beispielsweise, wenn Sie die HTTP-Aktion verwenden, möchten Sie möglicherweise Folgendes:

* [Vermeiden von HTTP-Timeouts bei Aufgaben mit langer Ausführungszeit](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Deaktivieren der Überprüfung von Location-Headern](../connectors/connectors-native-http.md#disable-location-header-check)

In diesen Fällen können Sie veranlassen, dass eine Aktion synchron ausgeführt wird, indem Sie diese Optionen verwenden:

* Ersetzen Sie die Abrufversion dieser Aktion durch eine Webhookversion, falls verfügbar.

* Deaktivieren Sie das asynchrone Verhalten der Aktion, indem Sie eine der folgenden Optionen ausführen:

  * [Deaktivieren Sie im Logik-App-Designer die Einstellung **Asynchrone Muster**](#turn-off-asynchronous-pattern-setting).

  * [Fügen Sie in der JSON-Definition, die der Aktion zugrunde liegt, die Vorgangsoption `"DisableAsyncPattern"`](#add-disable-async-pattern-option) hinzu.

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Deaktivieren der Einstellung **Asynchrones Muster**

1. Wählen Sie im Logik-App-Designer auf der Titelleiste der Aktion die Schaltfläche mit den Auslassungspunkten ( **...** ) aus, woraufhin die Einstellungen der Aktion geöffnet werden.

1. Suchen Sie die Einstellung **Asynchrones Muster**, deaktivieren Sie die Einstellung, indem Sie sie auf **Aus** festlegen (falls sie aktiviert ist), und wählen Sie **Fertig** aus.

   ![Deaktivieren der Einstellung „Asynchrones Muster“](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Deaktivieren des asynchronen Musters in der JSON-Definition einer Aktion

Fügen Sie in der JSON-Definition, die der Aktion zugrunde liegt, die Eigenschaft [„operationOptions“](#operation-options) hinzu, und legen Sie sie auf `"DisableAsyncPattern"` im Abschnitt `"inputs"` der Aktion fest, z. B.:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Authentifizieren von Triggern und Aktionen

HTTP- und HTTP-Endpunkte unterstützen verschiedene Arten der Authentifizierung. Basierend auf dem Trigger oder der Aktion, mit dem/der Sie ausgehende Aufrufe oder Anforderungen ausführen, um auf diese Endpunkte zuzugreifen, können Sie aus verschiedenen Bereichen von Authentifizierungstypen auswählen. Weitere Informationen finden Sie unter [Hinzufügen der Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Schema der Definitionssprache für Workflows für Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).
