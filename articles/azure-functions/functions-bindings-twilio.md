---
title: Azure Functions – Twilio-Bindung
description: Erfahren Sie, wie Twilio-Bindungen in Azure Functions verwendet werden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc6ca29af1866c5d26d3b73b26121451440c4dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511795"
---
# <a name="twilio-binding-for-azure-functions"></a>Twilio-Bindung für Azure Functions

Dieser Artikel erläutert das Senden von Textnachrichten mithilfe von [Twilio](https://www.twilio.com/)-Bindungen in Azure Functions. Azure Functions unterstützt Ausgabebindungen für Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Die Twilio-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio), Version 1.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Die Twilio-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio), Version 3.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Beispiel: Functions 1.x

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#c-example)
* [C#-Skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Textnachricht sendet, wenn sie durch eine Warteschlangennachricht ausgelöst wird.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Dieses Beispiel verwendet das `TwilioSms`-Attribut mit dem Rückgabewert der Methode. Alternativ können Sie das Attribut auch mit einem `out SMSMessage`-Parameter oder den Parametern `ICollector<SMSMessage>` oder `IAsyncCollector<SMSMessage>` verwenden.

### <a name="c-script-example"></a>C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Twilio-Ausgabebindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion verwendet einen `out`-Parameter, um eine Textnachricht zu senden.

Die Bindungsdaten in der Datei *function.json*:

Beispiel für „function.json“:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Der C#-Skriptcode:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Sie können in synchronem Code keine Ausgabeparameter verwenden. Ein Beispiel für asynchronen C#-Skriptcode:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>JavaScript-Beispiel

Das folgende Beispiel zeigt eine Twilio-Ausgabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet.

Die Bindungsdaten in der Datei *function.json*:

Beispiel für „function.json“:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>Beispiel: Functions 2.x

Sehen Sie sich das sprachspezifische Beispiel an:

* [2.x – C#](#2x-c-example)
* [2.x – C#-Skript (CSX)](#2x-c-script-example)
* [2.x – JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>2.x – C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die eine Textnachricht sendet, wenn sie durch eine Warteschlangennachricht ausgelöst wird.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

Dieses Beispiel verwendet das `TwilioSms`-Attribut mit dem Rückgabewert der Methode. Alternativ können Sie das Attribut auch mit einem `out CreateMessageOptions`-Parameter oder den Parametern `ICollector<CreateMessageOptions>` oder `IAsyncCollector<CreateMessageOptions>` verwenden.

### <a name="2x-c-script-example"></a>2.x – C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Twilio-Ausgabebindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion verwendet einen `out`-Parameter, um eine Textnachricht zu senden.

Die Bindungsdaten in der Datei *function.json*:

Beispiel für „function.json“:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Der C#-Skriptcode:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Sie können in synchronem Code keine Ausgabeparameter verwenden. Ein Beispiel für asynchronen C#-Skriptcode:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>2.x – JavaScript-Beispiel

Das folgende Beispiel zeigt eine Twilio-Ausgabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet.

Die Bindungsdaten in der Datei *function.json*:

Beispiel für „function.json“:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs).

Weitere Informationen zu Attributeigenschaften, die Sie konfigurieren können, finden Sie unter [Konfiguration](#configuration). Hier ist ein Beispiel für ein `TwilioSms`-Attribut in einer Methodensignatur:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Ein vollständiges Beispiel finden Sie unter [C#-Beispiel](#c-example).

## <a name="configuration"></a>Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `TwilioSms` festlegen:

| function.json-Eigenschaft in v1 | function.json-Eigenschaft in v2 | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|---------|----------------------|
|**type**|**type**| auf `twilioSms` festgelegt werden muss.|
|**direction**|**direction**| auf `out` festgelegt werden muss.|
|**name**|**name**| Variablenname, der im Funktionscode für die Twilio-SMS-Textnachricht verwendet wird |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Dieser Wert muss auf den Namen einer App-Einstellung festgelegt werden, die Ihre Twilio-Konto-SID enthält, z.B. „TwilioAccountSid“. Wenn er nicht festgelegt wird, lautet der Standardname der App-Einstellung "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Dieser Wert muss auf den Namen einer App-Einstellung festgelegt werden, die Ihr Twilio-Authentifizierungstoken enthält, z.B. „TwilioAccountAuthToken“. Wenn er nicht festgelegt wird, lautet der Standardname der App-Einstellung „AzureWebJobsTwilioAuthToken“. |
|**to**| N/V – in Code angeben | **An**| Dieser Wert wird auf die Telefonnummer festgelegt, an die die SMS-Textnachricht gesendet wird.|
|**from**|**from** | **From**| Dieser Wert wird auf die Telefonnummer festgelegt, von der die SMS-Textnachricht gesendet wird.|
|**body**|**body** | **Text**| Dieser Wert kann verwendet werden, um die SMS-Textnachricht als vordefinierten Code festzulegen und nicht dynamisch im Code für die Funktion. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
