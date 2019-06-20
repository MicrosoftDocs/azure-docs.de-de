---
title: SendGrid-Bindungen in Azure Functions
description: Referenz für SendGrid-Bindungen in Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 663283c4dff16194dea78afbf5dd393f1fa833b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511940"
---
# <a name="azure-functions-sendgrid-bindings"></a>SendGrid-Bindungen in Azure Functions

Dieser Artikel erläutert das Senden von E-Mail-Nachrichten mithilfe von [SendGrid](https://sendgrid.com/docs/User_Guide/index.html)-Bindungen in Azure Functions. Azure Functions bietet eine Ausgabebindung für SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die SendGrid-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), Version 2.x, bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die SendGrid-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), Version 3.x, bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#c-example)
* [C#-Skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die einen Service Bus-Warteschlangentrigger und eine SendGrid-Ausgabebindung verwendet.

#### <a name="synchronous-c-example"></a>Synchrones C#-Beispiel:

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
#### <a name="asynchronous-c-example"></a>Asynchrones C#-Beispiel:

```cs
[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Sie müssen die `ApiKey`-Eigenschaft des Attributs nicht festlegen, wenn Ihr API-Schlüssel in einer App-Einstellung mit dem Namen „AzureWebJobsSendGridApiKey“ enthalten ist.

### <a name="c-script-example"></a>C#-Skriptbeispiel

Das folgende Beispiel zeigt eine SendGrid-Ausgabebindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet.

Bindungsdaten in der Datei *function.json*:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der C#-Skriptcode sieht wie folgt aus:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

### <a name="java-example"></a>Java-Beispiel

Im folgenden Beispiel wird die `@SendGridOutput`-Anmerkung aus der [Java-Funktions-Laufzeitbibliothek](/java/api/overview/azure/functions/runtime) zum Senden einer E-Mail mithilfe der SendGrid-Ausgabebindung verwendet.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>JavaScript-Beispiel

Das folgende Beispiel zeigt eine SendGrid-Ausgabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet.

Bindungsdaten in der Datei *function.json*:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs).

Weitere Informationen zu Attributeigenschaften, die Sie konfigurieren können, finden Sie unter [Konfiguration](#configuration). Hier ist ein Beispiel für ein `SendGrid`-Attribut in einer Methodensignatur:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [C#-Beispiel](#c-example).

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `SendGrid` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**type**|| Erforderlich – muss auf `sendGrid` festgelegt sein.|
|**direction**|| Erforderlich – muss auf `out` festgelegt sein.|
|**name**|| Erforderlich – der Variablenname, der im Funktionscode für die Anforderung oder den Anforderungstext verwendet wird. Dieser Wert ist ```$return```, wenn es nur einen Rückgabewert gibt. |
|**apiKey**|**ApiKey**| Der Name einer App-Einstellung, die Ihren API-Schlüssel enthält. Wenn sie nicht festgelegt wird, lautet der Standardname der App-Einstellung „AzureWebJobsSendGridApiKey“.|
|**to**|**An**| E-Mail-Adresse des Empfängers |
|**from**|**From**| E-Mail-Adresse des Absenders |
|**subject**|**Betreff**| Betreff der E-Mail |
|**text**|**Text**| Inhalt der E-Mail |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Einstellungen für „host.json“

In diesem Abschnitt werden die verfügbaren globalen Konfigurationseinstellungen für diese Bindung in Version 2.x beschrieben. Die nachfolgende Beispieldatei „host.json“ enthält nur die Einstellungen für Version 2.x für diese Bindung. Weitere Informationen zu globalen Konfigurationseinstellungen in Version 2.x finden Sie unter [host.json-Referenz für Azure Functions 2.x](functions-host-json.md).

> [!NOTE]
> Eine Referenz für „host.json“ in Functions 1.x finden Sie unter [host.json-Referenz für Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|from|–|E-Mail-Adresse des Absenders für alle Funktionen.| 


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
