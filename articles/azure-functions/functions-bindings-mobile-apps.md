---
title: Mobile Apps-Bindungen für Azure Functions
description: Erfahren Sie, wie Azure Mobile Apps-Bindungen in Azure Functions verwendet werden.
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
ms.openlocfilehash: 5fd220f15f363c1987f1576009519e4b2feae6b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61438124"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps-Bindungen für Azure Functions 

> [!NOTE]
> Azure Mobile Apps-Bindungen sind nur für Azure Functions 1.x verfügbar. In Azure Functions 2.x werden sie nicht unterstützt.

Dieser Artikel erläutert das Arbeiten mit [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md)-Bindungen in Azure Functions. Azure Functions unterstützt Eingabe- und Ausgabebindungen für Mobile Apps.

Mithilfe von Mobile Apps-Bindungen können Sie Datentabellen in mobilen Apps lesen und aktualisieren.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Mobile Apps-Bindungen werden im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps), Version 1.x bereitgestellt. Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Eingabe

Die Mobile Apps-Eingabebindung lädt einen Datensatz aus einem mobilen Tabellenendpunkt und übergibt ihn an die Funktion. In C#- und F#-Funktionen werden alle Änderungen am Datensatz automatisch an die Tabelle zurückgesendet, wenn die Funktion erfolgreich beendet wird.

## <a name="input---example"></a>Eingabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Eingabe: C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Mobile Apps-Eingabebindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion wird durch eine Warteschlangennachricht, die eine Datensatz-ID enthält, ausgelöst. Die Funktion liest den angegebenen Datensatz und ändert dessen `Text`-Eigenschaft.

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Eingabe: JavaScript

Das folgende Beispiel zeigt eine Mobile Apps-Eingabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion wird durch eine Warteschlangennachricht, die eine Datensatz-ID enthält, ausgelöst. Die Funktion liest den angegebenen Datensatz und ändert dessen `Text`-Eigenschaft.

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#input---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Eingabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs).

Weitere Informationen zu Attributeigenschaften, die Sie konfigurieren können, finden Sie im [folgenden Konfigurationsabschnitt](#input---configuration).

## <a name="input---configuration"></a>Eingabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `MobileTable` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
| **type**|| Muss auf „mobileTable“ festgelegt werden|
| **direction**||Muss auf „in“ festgelegt werden|
| **name**|| Der Name des Eingabeparameters in der Funktionssignatur|
|**tableName** |**TableName**|Der Name der Datentabelle der mobilen App|
| **id**| **Id** | Der Bezeichner des abzurufenden Datensatzes. Kann statisch sein oder auf dem Trigger, der die Funktion aufruft, basieren. Wenn Sie beispielsweise einen Warteschlangentrigger für Ihre Funktion verwenden, nutzt `"id": "{queueTrigger}"` den Zeichenfolgenwert der Warteschlangennachricht als abzurufende Datensatz-ID.|
|**Verbindung**|**Connection**|Der Name einer App-Einstellung mit der URL der mobilen App. Die Funktion konstruiert anhand dieser URL die erforderlichen REST-Vorgänge für Ihre mobile App. Erstellen Sie eine App-Einstellung in Ihrer Funktions-App, die die URL Ihrer mobilen App enthält. Anschließend geben Sie den Namen der App-Einstellung in der `connection`-Eigenschaft Ihrer Eingabebindung an. Die URL sieht wie folgt aus: `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|Der Name einer App-Einstellung mit dem API-Schlüssel Ihrer mobilen App. Sie müssen den API-Schlüssel angeben, wenn Sie [einen API-Schlüssel in Ihrer mobilen Node.js-App](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) oder [einen API-Schlüssel in Ihrer mobilen .NET-App](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) implementieren. Für die Angabe des Schlüssels erstellen Sie eine App-Einstellung in Ihrer Funktions-App, die den API-Schlüssel enthält, und fügen dann die `apiKey`-Eigenschaft in der Eingabebindung mit dem Namen der App-Einstellung hinzu. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Geben Sie den API-Schlüssel nicht für Clients Ihrer mobilen App frei. Er sollte nur sicher an dienstseitige Clients wie Azure Functions verteilt werden. Azure Functions speichert die Verbindungsinformationen und API-Schlüssel als App-Einstellungen, sodass sie nicht ins Repository der Quellcodeverwaltung eingecheckt werden. Dadurch werden vertrauliche Daten geschützt.

## <a name="input---usage"></a>Eingabe: Verwendung

Wenn in C#-Funktionen der Datensatz mit der angegebenen ID gefunden wird, wird er an den benannten [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm)-Parameter übergeben. Wenn der Datensatz nicht gefunden wird, hat der Parameter den Wert `null`. 

In JavaScript-Funktionen wird der Datensatz an das `context.bindings.<name>`-Objekt übergeben. Wenn der Datensatz nicht gefunden wird, hat der Parameter den Wert `null`. 

In C#- und F#-Funktionen werden alle Änderungen am Eingabedatensatz (Eingabeparameter) automatisch wieder an die Tabelle zurückgesendet, wenn die Funktion erfolgreich beendet wird. Sie können einen Datensatz nicht in JavaScript-Funktionen ändern.

## <a name="output"></a>Output

Schreiben Sie unter Verwendung der Mobile Apps-Ausgabebindung einen neuen Datensatz in eine Mobile Apps-Tabelle.  

## <a name="output---example"></a>Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#](#output---c-example)
* [C#-Skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Ausgabe: C#-Beispiel

Das folgende Beispiel zeigt eine [C#-Funktion](functions-dotnet-class-library.md), die durch eine Warteschlangennachricht ausgelöst wird und einen Datensatz in einer Mobile Apps-Tabelle erstellt.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Ausgabe: C#-Skriptbeispiel

Das folgende Beispiel zeigt eine Mobile Apps-Ausgabebindung in einer Datei *function.json* sowie eine [C#-Skriptfunktion](functions-reference-csharp.md), die die Bindung verwendet. Die Funktion wird durch eine Warteschlangennachricht ausgelöst und erstellt einen neuen Datensatz mit einem hartcodierten Wert für die `Text`-Eigenschaft.

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der C#-Skriptcode sieht wie folgt aus:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Ausgabe: JavaScript-Beispiel

Das folgende Beispiel zeigt eine Mobile Apps-Ausgabebindung in einer Datei *function.json* sowie eine [JavaScript-Funktion](functions-reference-node.md), die die Bindung verwendet. Die Funktion wird durch eine Warteschlangennachricht ausgelöst und erstellt einen neuen Datensatz mit einem hartcodierten Wert für die `Text`-Eigenschaft.

Bindungsdaten in der Datei *function.json*:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Weitere Informationen zu diesen Eigenschaften finden Sie im Abschnitt [Konfiguration](#output---configuration).

Der JavaScript-Code sieht wie folgt aus:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Ausgabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs).

Weitere Informationen zu Attributeigenschaften, die Sie konfigurieren können, finden Sie unter [Ausgabe: Konfiguration](#output---configuration). Hier ist ein Beispiel für ein `MobileTable`-Attribut in einer Methodensignatur:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Ein vollständiges Beispiel finden Sie unter [Ausgabe: C#-Beispiel](#output---c-example).

## <a name="output---configuration"></a>Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `MobileTable` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
| **type**|| Muss auf „mobileTable“ festgelegt werden|
| **direction**||Muss auf „out“ festgelegt werden|
| **name**|| Der Name des Ausgabeparameters in der Funktionssignatur|
|**tableName** |**TableName**|Der Name der Datentabelle der mobilen App|
|**Verbindung**|**MobileAppUriSetting**|Der Name einer App-Einstellung mit der URL der mobilen App. Die Funktion konstruiert anhand dieser URL die erforderlichen REST-Vorgänge für Ihre mobile App. Erstellen Sie eine App-Einstellung in Ihrer Funktions-App, die die URL Ihrer mobilen App enthält. Anschließend geben Sie den Namen der App-Einstellung in der `connection`-Eigenschaft Ihrer Eingabebindung an. Die URL sieht wie folgt aus: `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|Der Name einer App-Einstellung mit dem API-Schlüssel Ihrer mobilen App. Sie müssen den API-Schlüssel angeben, wenn Sie [in Ihrem Node.js-Mobile App-Back-End](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) oder [in Ihrem .NET-Mobile App-Back-End](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) einen API-Schlüssel implementieren. Für die Angabe des Schlüssels erstellen Sie eine App-Einstellung in Ihrer Funktions-App, die den API-Schlüssel enthält, und fügen dann die `apiKey`-Eigenschaft in der Eingabebindung mit dem Namen der App-Einstellung hinzu. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Geben Sie den API-Schlüssel nicht für Clients Ihrer mobilen App frei. Er sollte nur sicher an dienstseitige Clients wie Azure Functions verteilt werden. Azure Functions speichert die Verbindungsinformationen und API-Schlüssel als App-Einstellungen, sodass sie nicht ins Repository der Quellcodeverwaltung eingecheckt werden. Dadurch werden vertrauliche Daten geschützt.

## <a name="output---usage"></a>Ausgabe: Verwendung

Verwenden Sie in C#-Skriptfunktionen einen benannten Ausgabeparameter vom Typ `out object`, um auf den Ausgabedatensatz zuzugreifen. In C#-Klassenbibliotheken kann das `MobileTable`-Attribut mit den folgenden Typen verwendet werden:

* `ICollector<T>` oder `IAsyncCollector<T>`, wobei `T` entweder `JObject` oder ein Typ mit einer `public string Id`-Eigenschaft ist.
* `out JObject`
* `out T` oder `out T[]`, wobei `T` ein beliebiger Typ mit einer `public string Id`-Eigenschaft ist.

Verwenden Sie in Node.js-Funktionen `context.bindings.<name>` für den Zugriff auf den Ausgabedatensatz.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
