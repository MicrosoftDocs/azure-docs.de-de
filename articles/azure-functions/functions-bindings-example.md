---
title: Trigger- und Bindungsbeispiel für Azure Functions
description: Informationen zum Konfigurieren von Azure Functions-Bindungen
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8372becd614e35bead9e98e405e2694f3f16db4a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472332"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Trigger- und Bindungsbeispiel für Azure Functions

In diesem Artikel wird das Konfigurieren von [Triggern und Bindungen](./functions-triggers-bindings.md) in einer Azure-Funktion veranschaulicht.

Ein Beispiel: Sie möchten immer dann eine neue Zeile in Azure Table Storage schreiben, wenn in Azure Queue Storage eine neue Nachricht eintrifft. Dieses Szenario kann mit einem Azure Queue Storage-Trigger und einer Azure Table Storage-Ausgabebindung implementiert werden. 

Hier sehen Sie eine *function.json*-Datei für dieses Szenario. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Das erste Element im Array `bindings` ist der Queue Storage-Trigger. Die Eigenschaften `type` und `direction` identifizieren den Trigger. Die Eigenschaft `name` identifiziert den Funktionsparameter, der den Inhalt der Warteschlangennachricht empfängt. Der Name der zu überwachenden Warteschlange befindet sich in `queueName`, und die Verbindungszeichenfolge befindet sich in der App-Einstellung, die durch `connection` festgelegt ist.

Das zweite Element im Array `bindings` ist die Azure Table Storage-Ausgabebindung. Die Eigenschaften `type` und `direction` identifizieren die Bindung. Die Eigenschaft `name` gibt an, wie die Funktion die neue Tabellenzeile bereitstellt – in diesem Fall mit dem Rückgabewert der Funktion. Der Name der Tabelle befindet sich in `tableName`, und die Verbindungszeichenfolge befindet sich in der App-Einstellung, die durch `connection` festgelegt ist.

Wenn Sie den Inhalt von *function.json* im Azure-Portal anzeigen und bearbeiten möchten, klicken Sie für die Funktion auf der Registerkarte **Integrieren** auf die Option **Erweiterter Editor**.

> [!NOTE]
> Beim Wert von `connection` handelt es sich nicht um die Verbindungszeichenfolge selbst, sondern um den Namen einer App-Einstellung, die die Verbindungszeichenfolge enthält. In Bindungen werden Verbindungszeichenfolgen in App-Einstellungen gespeichert, damit sichergestellt ist, dass *function.json* keine Geheimnisse eines Diensts enthält.

## <a name="c-script-example"></a>C#-Skriptbeispiel

Folgender C#-Skriptcode arbeitet mit Triggern und Bindungen. Beachten Sie, dass der Parameter `order` den Inhalt der Warteschlangennachricht bereitstellt. Dieser Name ist erforderlich, da der Eigenschaftswert `name` in *function.json* `order` lautet. 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript-Beispiel

Die gleiche *function.json*-Datei kann mit einer JavaScript-Funktion verwendet werden:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Klassenbibliotheksbeispiel

In einer Klassenbibliothek werden die gleichen Trigger- und Bindungsinformationen (Warteschlangen- und Tabellennamen, Speicherkonten sowie Funktionsparameter für Ein- und Ausgabe) durch Attribute anstatt über eine function.json-Datei bereitgestellt: Hier sehen Sie ein Beispiel:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Sie haben nun eine funktionierende Funktion, die von einer Azure-Warteschlange ausgelöst wird und Daten in Azure Table Storage ausgibt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure Functions-Muster für Bindungsausdrücke](./functions-bindings-expressions-patterns.md)
