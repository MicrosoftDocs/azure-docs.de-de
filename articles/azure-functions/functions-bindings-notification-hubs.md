---
title: Notification Hubs-Bindungen für Azure Functions
description: Erfahren Sie, wie Azure Notification Hub-Bindungen in Azure Functions verwendet werden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 79ea9455fec7d31f800b2b5d36df6a2a53f502c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61438182"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs-Ausgabebindung für Azure Functions

Dieser Artikel erläutert das Senden von Pushbenachrichtigungen mithilfe von [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md)-Bindungen in Azure Functions. Azure Functions unterstützt Ausgabebindungen für Notification Hubs.

Azure Notification Hubs muss für den Plattformbenachrichtigungsdienst (PNS) konfiguriert sein, den Sie verwenden möchten. Informationen zum Empfang von Pushbenachrichtigungen von Notification Hubs in Ihrer Client-App finden Sie unter [Erste Schritte mit Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Wählen Sie dort in der Dropdownliste oben auf der Seite die Zielplattform für den Client aus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> [Google Cloud Messaging (GCM) wurde von Google eingestellt und durch Firebase Cloud Messaging (FCM) ersetzt](https://developers.google.com/cloud-messaging/faq). Diese Ausgabebindung unterstützt FCM nicht. Verwenden Sie die [Firebase-API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) direkt in Ihrer Funktion, oder nutzen Sie [Vorlagenbenachrichtigungen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), um Benachrichtigungen mithilfe von FCM zu senden.

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die Notification Hubs-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs), Version 1.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Diese Bindung ist nicht verfügbar in Functions 2.x.

## <a name="example---template"></a>Beispiel: Vorlage

Die von Ihnen gesendeten Benachrichtigungen können native Benachrichtigungen oder [Vorlagenbenachrichtigungen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) sein. Native Benachrichtigungen sind für eine spezifische Clientplattform bestimmt. Dies wird in der `platform`-Eigenschaft der Ausgabebindung konfiguriert. Eine Vorlagenbenachrichtigung kann für mehrere Plattformen verwendet werden.   

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript: Ausgabeparameter](#c-script-template-example---out-parameter)
* [C#-Skript: asynchron](#c-script-template-example---asynchronous)
* [C#-Skript: JSON](#c-script-template-example---json)
* [C#-Skript: Bibliothekstypen](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C#-Skriptvorlagenbeispiel: Ausgabeparameter

In diesem Beispiel wird eine Benachrichtigung für eine [Vorlagenregistrierung](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) gesendet, bei der in der Vorlage ein `message`-Platzhalter enthalten ist.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C#-Skriptvorlagenbeispiel: asynchron

Wenn Sie asynchronen Code verwenden, sind out-Parameter nicht zulässig. Verwenden Sie in diesem Fall `IAsyncCollector`, um die Vorlagenbenachrichtigung zurückzugeben. Der folgende Code ist ein asynchrones Beispiel des obigen Codes. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C#-Skriptvorlagenbeispiel: JSON

In diesem Beispiel wird eine Benachrichtigung für eine [Vorlagenregistrierung](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md), die den Platzhalter `message` in der Vorlage enthält, über eine gültige JSON-Zeichenfolge gesendet.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C#-Skriptvorlagenbeispiel: Bibliothekstypen

Dieses Beispiel zeigt, wie Sie in der [Bibliothek für Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) definierte Typen verwenden. 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F#-Vorlagenbeispiel

In diesem Beispiel wird eine Benachrichtigung für eine [Vorlagenregistrierung](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) gesendet, die `location` und `message` enthält.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript-Vorlagenbeispiel

In diesem Beispiel wird eine Benachrichtigung für eine [Vorlagenregistrierung](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) gesendet, die `location` und `message` enthält.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Beispiel: APNS nativ

Dieses C#-Skriptbeispiel zeigt das Senden einer nativen APNS-Benachrichtigung. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Beispiel: WNS nativ

In diesem C#-Skriptbeispiel wird gezeigt, wie Sie in der [Microsoft Azure Notification Hubs-Bibliothek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) definierte Typen zum Senden einer nativen WNS-Popupbenachrichtigung verwenden. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs).

Die Konstruktorparameter und Eigenschaften des Attributs werden im Abschnitt [Konfiguration](#configuration) beschrieben.

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im `NotificationHub`-Attribut festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type** |–| Muss auf „notificationHub“ festgelegt werden. |
|**direction** |–| Muss auf „out“ festgelegt werden. | 
|**name** |–| Variablenname, der im Funktionscode für die Notification Hub-Nachricht verwendet wird |
|**tagExpression** |**TagExpression** | Mit Tagausdrücken können Sie Benachrichtigungen an eine Gruppe von Geräten übermitteln, die sich für den Empfang von Benachrichtigungen, die dem Tagausdruck entsprechen, registriert haben.  Weitere Informationen finden Sie unter [Weiterleitung und Tagausdrücke](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Name der Notification Hub-Ressource im Azure-Portal |
|**Verbindung** | **ConnectionStringSetting** | Der Name einer App-Einstellung, die eine Notification Hubs-Verbindungszeichenfolge enthält.  Die Verbindungszeichenfolge muss auf den Wert *DefaultFullSharedAccessSignature* für Ihren Notification Hub festgelegt werden. Weitere Informationen finden Sie unter [Verbindungszeichenfolgen-Setup](#connection-string-setup) weiter unten in diesem Artikel.|
|**platform** | **Plattform** | Mit der platform-Eigenschaft wird die Clientplattform Ihrer Benachrichtigungsziele angegeben. Wenn die Plattformeigenschaft aus der Ausgabebindung weggelassen wird, können Vorlagenbenachrichtigungen für alle Zielplattformen verwendet werden, die auf dem Azure Notification Hub konfiguriert sind. Weitere Informationen zur allgemeinen Verwendung von Vorlagen zum Senden von plattformübergreifenden Benachrichtigungen mit einem Azure Notification Hub finden Sie unter [Vorlagen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Wenn **platform** festgelegt wird, muss einer der folgenden Werte verwendet werden: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Weitere Informationen zur Konfiguration des Notification Hubs für APNS und zum Empfang der Benachrichtigung in einer Client-App finden Sie unter [Senden von Pushbenachrichtigungen an iOS mit Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Weitere Informationen zur Konfiguration des Notification Hubs für ADM und zum Empfang der Benachrichtigung in einer Kindle-App finden Sie unter [Erste Schritte mit Notification Hubs für Kindle-Apps](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Windows-Pushbenachrichtigungsdienst](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) für Windows-Plattformen. Windows Phone 8.1 und höher wird vom Windows-Pushbenachrichtigungsdienst (WNS) ebenfalls unterstützt. Weitere Informationen finden Sie unter [Erste Schritte mit Notification Hubs für Apps für die universelle Windows-Plattform](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft-Pushbenachrichtigungsdienst](/previous-versions/windows/apps/ff402558(v=vs.105)). Diese Plattform unterstützt Windows Phone 8 und frühere Windows Phone-Plattformen. Weitere Informationen finden Sie unter [Senden von Pushbenachrichtigungen mit Azure Notification Hubs unter Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Beispiel für die Datei „function.json“

Hier sehen Sie ein Beispiel für eine Notification Hubs-Bindung in einer Datei *function.json*.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Verbindungszeichenfolgen-Setup

Um eine Notification Hubs-Ausgabebindung zu verwenden, müssen Sie die Verbindungszeichenfolge für den Hub konfigurieren. Sie können einen vorhandenen Notification Hub auswählen oder über die Registerkarte *Integrieren* im Azure-Portal einen neuen erstellen. Sie können die Verbindungszeichenfolge auch manuell konfigurieren. 

So konfigurieren Sie die Verbindungszeichenfolge für einen vorhandenen Notification Hub:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum Notification Hub, wählen Sie **Zugriffsrichtlinien**, und klicken Sie auf die Kopierschaltfläche neben der Richtlinie **DefaultFullSharedAccessSignature**. Dadurch wird die Verbindungszeichenfolge für die Richtlinie *DefaultFullSharedAccessSignature* in Ihren Notification Hub kopiert. Mit dieser Verbindungszeichenfolge kann Ihre Funktion Benachrichtigungen an den Hub senden.
    ![Kopieren der Notification Hub-Verbindungszeichenfolge](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navigieren Sie im Azure-Portal zu Ihrer Funktions-App, wählen Sie **Anwendungseinstellungen** aus, und fügen Sie einen Schlüssel (etwa **MyHubConnectionString**) hinzu. Fügen Sie die kopierte Richtlinie *DefaultFullSharedAccessSignature* für Ihren Notification Hub als Wert ein, und klicken Sie anschließend auf **Speichern**.

Der Name der Anwendungseinstellung wird in die Verbindungseinstellung der Ausgabebindung in *function.json* oder im .NET-Attribut übernommen. Weitere Informationen finden Sie im [Konfigurationsabschnitt](#configuration) weiter oben in diesem Artikel.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Ausnahmen und Rückgabecodes

| Bindung | Verweis |
|---|---|
| Notification Hub | [Betriebsleitfaden](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)

