---
title: Deaktivieren von Funktionen in Azure Functions
description: Hier erfahren Sie, wie Sie Funktionen in Azure Functions 1.x und 2.x deaktivieren und aktivieren.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: glenga
ms.openlocfilehash: a32b4815a2716428ceeec034ddc5589e3aa062e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60710564"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Deaktivieren von Funktionen in Azure Functions

In diesem Artikel wird erläutert, wie Sie eine Funktion in Azure Functions deaktivieren. Durch das *Deaktivieren* einer Funktion wird die Runtime angewiesen, den für die Funktion definierten automatischen Trigger zu ignorieren. Die hierzu verwendete Methode hängt von der Runtimeversion und der Programmiersprache ab:

* Functions 1.x
  * Skriptsprachen
  * C#-Klassenbibliotheken
* Functions 2.x
  * Eine Methode für alle Programmiersprachen
  * Optionale Methode für C#-Klassenbibliotheken

## <a name="functions-1x---scripting-languages"></a>Functions 1.x – Skriptsprachen

Für Skriptsprachen wie C#-Skript und JavaScript verwenden Sie die `disabled`-Eigenschaft der Datei *function.json*, um der Runtime mitzuteilen, dass eine Funktion nicht ausgelöst werden soll. Diese Eigenschaft kann auf `true` oder auf den Namen einer App-Einstellung festgelegt werden:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
oder 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

Im zweiten Beispiel wird die Funktion deaktiviert, wenn eine App-Einstellung mit dem Namen „IS_DISABLED“ vorhanden und auf `true` oder 1 festgelegt ist.

Sie können die Datei im Azure-Portal bearbeiten oder die Option **Funktionszustand** auf der Registerkarte **Verwalten** der Funktion verwenden. Die Option im Portal ändert die Datei *function.json*.

![Option „Funktionszustand“](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x – C#-Klassenbibliotheken

In einer Functions 1.x-Klassenbibliothek verwenden Sie ein `Disable`-Attribut, um zu verhindern, dass eine Funktion ausgelöst wird. Sie können das Attribut wie im folgenden Beispiel gezeigt ohne einen Konstruktorparameter verwenden:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Wenn Sie das Attribut ohne Konstruktorparameter verwenden, müssen Sie das Projekt erneut kompilieren und erneut bereitstellen, um den Zustand „Deaktiviert“ der Funktion zu ändern. Eine flexiblere Möglichkeit zur Verwendung des Attributs besteht darin, wie im folgenden Beispiel gezeigt einen Konstruktorparameter hinzuzufügen, der auf eine boolesche App-Einstellung verweist:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Mit dieser Methode können Sie die Funktion durch Ändern der App-Einstellung ohne erneutes Kompilieren oder erneutes Bereitstellen aktivieren und deaktivieren. Das Ändern einer App-Einstellung bewirkt einen Neustart der Funktions-App, sodass die Änderung des Zustands „Deaktiviert“ sofort erkannt wird.

> [!IMPORTANT]
> Das `Disabled`-Attribut ist die einzige Möglichkeit zum Deaktivieren einer Klassenbibliotheksfunktion. Die generierte Datei *function.json* für eine Klassenbibliotheksfunktion ist nicht zur direkten Bearbeitung vorgesehen. Wenn Sie diese Datei bearbeiten, haben Änderungen, die Sie an der `disabled`-Eigenschaft vornehmen, keinerlei Auswirkungen.
>
> Das Gleiche gilt für die Option **Funktionszustand** auf der Registerkarte **Verwalten**, da diese die Datei *function.json* ändert.
>
> Beachten Sie außerdem, dass die Funktion im Portal als deaktiviert angezeigt werden kann, wenn dies nicht der Fall ist.



## <a name="functions-2x---all-languages"></a>Functions 2.x – alle Programmiersprachen

In Functions 2.x verwenden Sie zum Deaktivieren einer Funktion eine App-Einstellung. Um eine Funktion mit dem Namen `QueueTrigger` zu deaktivieren, erstellen Sie beispielsweise eine App-Einstellung namens `AzureWebJobs.QueueTrigger.Disabled` und legen diese auf `true` fest. Zum Aktivieren der Funktion legen Sie die App-Einstellung auf `false` fest. Sie können auch die Option **Funktionszustand** auf der Registerkarte **Verwalten** der Funktion verwenden. Die Option erstellt und löscht die App-Einstellung `AzureWebJobs.<functionname>.Disabled`.

![Option „Funktionszustand“](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x – C#-Klassenbibliotheken

In einer Functions 2.x-Klassenbibliothek empfehlen wir die Verwendung der Methode, die für alle Programmiersprachen funktioniert. Wenn Sie es vorziehen, können Sie jedoch [das Disable-Attribut wie in Functions 1.x verwenden](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wird das Deaktivieren von automatischen Triggern behandelt. Weitere Informationen zu Triggern finden Sie unter [Trigger und Bindungen](functions-triggers-bindings.md).
