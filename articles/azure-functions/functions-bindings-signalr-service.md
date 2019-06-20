---
title: Bindungen des SignalR-Diensts für Azure Functions
description: Erfahren Sie, wie Bindungen des SignalR-Diensts in Azure Functions verwendet werden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: f0d4a607676285ed4f0f91d8ce8c83ddf1313b89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511133"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Bindungen des SignalR-Diensts für Azure Functions

In diesem Artikel wird erläutert, wie mit dem [Azure SignalR-Dienst](https://azure.microsoft.com/services/signalr-service/) verbundene Clients mithilfe von Bindungen des SignalR-Diensts in Azure Functions authentifiziert und Nachrichten in Echtzeit an sie gesendet werden. Azure Functions unterstützt Eingabe- und Ausgabebindungen für den SignalR-Dienst.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die SignalR Service-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) (Version 1.*) bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java-Anmerkungen

Um die SignalR Service-Anmerkungen in Java-Funktionen verwenden zu können, müssen Sie in „pom.xml“ dem Artefakt *azure-functions-java-library-signalr* (Version 1.0 oder höher) eine Abhängigkeit hinzufügen.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Verwenden Sie mindestens die Version 2.4.419 von Azure Functions Core Tools (Hostversion 2.0.12332), um die SignalR Service-Bindungen in Java nutzen zu können.

## <a name="using-signalr-service-with-azure-functions"></a>Verwenden von SignalR Service mit Azure Functions

Ausführliche Informationen zum Konfigurieren und Verwenden von SignalR Service und Azure Functions finden Sie unter [Azure Functions-Entwicklung und -Konfiguration mit Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>SignalR-Verbindungsinfo-Eingabebindung

Bevor ein Client eine Verbindung mit dem Azure SignalR-Dienst herstellen kann, muss er die Endpunkt-URL des Diensts und ein gültiges Zugriffstoken abrufen. Die *SignalRConnectionInfo*-Eingabebindung erzeugt die Endpunkt-URL des SignalR-Diensts und ein gültiges Token. Beide werden verwendet, um die Verbindung mit dem Dienst herzustellen. Da das Token zeitlich begrenzt ist und für die Authentifizierung eines bestimmten Benutzers gegenüber einer Verbindung verwendet werden kann, sollten Sie das Token nicht zwischenspeichern oder es für mehrere Clients verwenden. Ein HTTP-Trigger, der diese Bindung verwendet, kann von Clients dazu verwendet werden, die Verbindungsinformationen abzurufen.

Sehen Sie sich das sprachspezifische Beispiel an:

* [2.x – C#](#2x-c-input-examples)
* [2.x – JavaScript](#2x-javascript-input-examples)
* [2.x – Java](#2x-java-input-examples)

Weitere Informationen zur Erstellung einer Aushandlungsfunktion mithilfe dieser Bindung, die von einem SignalR-Client-SDK genutzt werden kann, finden Sie in der SignalR Service-Konzeptdokumentation im Artikel [Azure Functions-Entwicklung und -Konfiguration mit Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="2x-c-input-examples"></a>C#-Eingabebeispiele für 2.x

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die SignalR-Verbindungsinformationen mithilfe der Eingabebindung erwirbt und sie über HTTP zurückgibt.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Authentifizierte Token

Wenn die Funktion von einem authentifizierten Client ausgelöst wird, können Sie dem erzeugten Token einen Benutzer-ID-Anspruch hinzufügen. Mithilfe der [App Service-Authentifizierung](../app-service/overview-authentication-authorization.md) können Sie einer Funktions-App problemlos eine Authentifizierung hinzufügen.

App Service-Authentifizierung legt HTTP-Header mit den Namen `x-ms-client-principal-id` und `x-ms-client-principal-name` fest, die die Clientprinzipal-ID bzw. den Namen des authentifizierten Benutzers enthalten. Sie können die `UserId`-Eigenschaft der Bindung mithilfe eines [Bindungsausdrucks](./functions-bindings-expressions-patterns.md) auf den Wert eines der beiden Header festlegen: `{headers.x-ms-client-principal-id}` oder `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-examples"></a>JavaScript-Eingabebeispiele für 2.x

Das folgende Beispiel zeigt eine SignalR-Verbindungsinfo-Eingabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet, um die Verbindungsinformationen zurückzugeben.

Die Bindungsdaten in der Datei *function.json*:

Beispiel für „function.json“:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Authentifizierte Token

Wenn die Funktion von einem authentifizierten Client ausgelöst wird, können Sie dem erzeugten Token einen Benutzer-ID-Anspruch hinzufügen. Mithilfe der [App Service-Authentifizierung](../app-service/overview-authentication-authorization.md) können Sie einer Funktions-App problemlos eine Authentifizierung hinzufügen.

App Service-Authentifizierung legt HTTP-Header mit den Namen `x-ms-client-principal-id` und `x-ms-client-principal-name` fest, die die Clientprinzipal-ID bzw. den Namen des authentifizierten Benutzers enthalten. Sie können die `userId`-Eigenschaft der Bindung mithilfe eines [Bindungsausdrucks](./functions-bindings-expressions-patterns.md) auf den Wert eines der beiden Header festlegen: `{headers.x-ms-client-principal-id}` oder `{headers.x-ms-client-principal-name}`. 

Beispiel für „function.json“:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>Java-Eingabebeispiele für 2.x

Das folgende Beispiel zeigt eine [Java-Funktion](functions-reference-java.md), die SignalR-Verbindungsinformationen mithilfe der Eingabebindung abruft und sie über HTTP zurückgibt.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Authentifizierte Token

Wenn die Funktion von einem authentifizierten Client ausgelöst wird, können Sie dem erzeugten Token einen Benutzer-ID-Anspruch hinzufügen. Mithilfe der [App Service-Authentifizierung](../app-service/overview-authentication-authorization.md) können Sie einer Funktions-App problemlos eine Authentifizierung hinzufügen.

App Service-Authentifizierung legt HTTP-Header mit den Namen `x-ms-client-principal-id` und `x-ms-client-principal-name` fest, die die Clientprinzipal-ID bzw. den Namen des authentifizierten Benutzers enthalten. Sie können die `UserId`-Eigenschaft der Bindung mithilfe eines [Bindungsausdrucks](./functions-bindings-expressions-patterns.md) auf den Wert eines der beiden Header festlegen: `{headers.x-ms-client-principal-id}` oder `{headers.x-ms-client-principal-name}`.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>SignalR-Ausgabebindung

Verwenden Sie die *SignalR*-Ausgabebindung, um eine oder mehrere Nachrichten mithilfe des Azure SignalR-Diensts zu senden. Sie können eine Nachricht als Broadcast an alle verbundenen Clients senden oder den Broadcast auf verbundene Clients beschränken, die für einem bestimmten Benutzer authentifiziert wurden.

Darüber hinaus können Sie damit die Gruppen verwalten, denen ein Benutzer angehört.

Sehen Sie sich das sprachspezifische Beispiel an:

* [2.x – C#](#2x-c-send-message-output-examples)
* [2.x – JavaScript](#2x-javascript-send-message-output-examples)
* [2.x – Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>Senden einer Nachricht: C#-Ausgabebeispiele für 2.x

#### <a name="broadcast-to-all-clients"></a>Broadcast an alle Clients

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Nachricht mithilfe der Ausgabebindung an alle verbundenen Clients sendet. Das `Target` ist der Name der Methode, die auf den einzelnen Clients aufgerufen werden soll. Die `Arguments`-Eigenschaft ist ein Array von null oder mehr Objekten, die an die Clientmethode übergeben werden sollen.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Senden an einen Benutzer

Sie können eine Nachricht ausschließlich an Verbindungen senden, die für einen Benutzer authentifiziert wurden, indem Sie die `UserId`-Eigenschaft der SignalR-Nachricht festlegen.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Senden an eine Gruppe

Sie können eine Nachricht ausschließlich an Verbindungen senden, die einer Gruppe hinzugefügt wurden, indem Sie die Eigenschaft `GroupName` der SignalR-Nachricht festlegen.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>Gruppenverwaltung: C#-Ausgabebeispiele für 2.x

SignalR Service ermöglicht das Hinzufügen von Benutzern zu Gruppen. Nachrichten können dann an eine Gruppe gesendet werden. Sie können die Klasse `SignalRGroupAction` mit der Ausgabebindung `SignalR` verwenden, um die Gruppenmitgliedschaft eines Benutzers zu verwalten.

#### <a name="add-user-to-a-group"></a>Hinzufügen eines Benutzers zu einer Gruppe

Im folgenden Beispiel wird ein Benutzer einer Gruppe hinzugefügt:

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Entfernen eines Benutzers aus einer Gruppe

Im folgenden Beispiel wird ein Benutzer aus einer Gruppe entfernt:

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>Senden einer Nachricht: JavaScript-Ausgabebeispiele für 2.x

#### <a name="broadcast-to-all-clients"></a>Broadcast an alle Clients

Das folgende Beispiel zeigt eine SignalR-Ausgabebindung in einer Datei vom Typ *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet, um mit dem Azure SignalR-Dienst eine Nachricht zu senden. Legen Sie die Ausgabebindung auf ein Array von einer oder mehreren SignalR-Nachrichten fest. Eine SignalR-Nachricht besteht aus einer `target`-Eigenschaft, die den Namen der auf jedem Client aufzurufenden Methode angibt, und einer `arguments`-Eigenschaft, bei der es sich um ein Array von Objekten handelt, die der Clientmethode als Argumente übergeben werden sollen.

Die Bindungsdaten in der Datei *function.json*:

Beispiel für „function.json“:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Senden an einen Benutzer

Sie können eine Nachricht ausschließlich an Verbindungen senden, die für einen Benutzer authentifiziert wurden, indem Sie die `userId`-Eigenschaft der SignalR-Nachricht festlegen.

*function.json* bleibt unverändert. Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Senden an eine Gruppe

Sie können eine Nachricht ausschließlich an Verbindungen senden, die einer Gruppe hinzugefügt wurden, indem Sie die Eigenschaft `groupName` der SignalR-Nachricht festlegen.

*function.json* bleibt unverändert. Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>Gruppenverwaltung: JavaScript-Ausgabebeispiele für 2.x

SignalR Service ermöglicht das Hinzufügen von Benutzern zu Gruppen. Nachrichten können dann an eine Gruppe gesendet werden. Sie können die Ausgabebindung `SignalR` verwenden, um die Gruppenmitgliedschaft eines Benutzers zu verwalten.

#### <a name="add-user-to-a-group"></a>Hinzufügen eines Benutzers zu einer Gruppe

Im folgenden Beispiel wird ein Benutzer einer Gruppe hinzugefügt:

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Entfernen eines Benutzers aus einer Gruppe

Im folgenden Beispiel wird ein Benutzer aus einer Gruppe entfernt:

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>Senden einer Nachricht: Java-Ausgabebeispiele für 2.x

#### <a name="broadcast-to-all-clients"></a>Broadcast an alle Clients

Das folgende Beispiel zeigt eine [Java-Funktion](functions-reference-java.md), die eine Nachricht mithilfe der Ausgabebindung an alle verbundenen Clients sendet. Das `target` ist der Name der Methode, die auf den einzelnen Clients aufgerufen werden soll. Die `arguments`-Eigenschaft ist ein Array von null oder mehr Objekten, die an die Clientmethode übergeben werden sollen.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Senden an einen Benutzer

Sie können eine Nachricht ausschließlich an Verbindungen senden, die für einen Benutzer authentifiziert wurden, indem Sie die `userId`-Eigenschaft der SignalR-Nachricht festlegen.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Senden an eine Gruppe

Sie können eine Nachricht ausschließlich an Verbindungen senden, die einer Gruppe hinzugefügt wurden, indem Sie die Eigenschaft `groupName` der SignalR-Nachricht festlegen.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>Gruppenverwaltung: Java-Ausgabebeispiele für 2.x

SignalR Service ermöglicht das Hinzufügen von Benutzern zu Gruppen. Nachrichten können dann an eine Gruppe gesendet werden. Sie können die Klasse `SignalRGroupAction` mit der Ausgabebindung `SignalROutput` verwenden, um die Gruppenmitgliedschaft eines Benutzers zu verwalten.

#### <a name="add-user-to-a-group"></a>Hinzufügen eines Benutzers zu einer Gruppe

Im folgenden Beispiel wird ein Benutzer einer Gruppe hinzugefügt:

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Entfernen eines Benutzers aus einer Gruppe

Im folgenden Beispiel wird ein Benutzer aus einer Gruppe entfernt:

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>Konfiguration

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `SignalRConnectionInfo` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type**|| Muss auf `signalRConnectionInfo` festgelegt sein.|
|**direction**|| Muss auf `in` festgelegt sein.|
|**name**|| Variablenname, der im Funktionscode für das Verbindungsinfoobjekt verwendet wird. |
|**hubName**|**HubName**| Dieser Wert muss auf den Namen des SignalR-Hubs festgelegt werden, für den die Verbindungsinformationen erzeugt werden.|
|**userId**|**UserId**| Optional: Der Wert des Benutzer-ID-Anspruchs, der im Zugriffsschlüsseltoken festgelegt werden soll. |
|**connectionStringSetting**|**ConnectionStringSetting**| Der Name der Anwendungseinstellung, die die Verbindungszeichenfolge des SignalR-Diensts enthält (standardmäßig „AzureSignalRConnectionString“) |

### <a name="signalr"></a>SignalR

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `SignalR` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type**|| Muss auf `signalR` festgelegt sein.|
|**direction**|| Muss auf `out` festgelegt sein.|
|**name**|| Variablenname, der im Funktionscode für das Verbindungsinfoobjekt verwendet wird. |
|**hubName**|**HubName**| Dieser Wert muss auf den Namen des SignalR-Hubs festgelegt werden, für den die Verbindungsinformationen erzeugt werden.|
|**connectionStringSetting**|**ConnectionStringSetting**| Der Name der Anwendungseinstellung, die die Verbindungszeichenfolge des SignalR-Diensts enthält (standardmäßig „AzureSignalRConnectionString“) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions-Entwicklung und -Konfiguration mit Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
