---
title: Deaktivieren von Funktionen in Azure Functions
description: Erfahren Sie, wie Sie Funktionen in Azure Functions deaktivieren und aktivieren.
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551042"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Deaktivieren von Funktionen in Azure Functions

In diesem Artikel wird erläutert, wie Sie eine Funktion in Azure Functions deaktivieren. Durch das *Deaktivieren* einer Funktion wird die Runtime angewiesen, den für die Funktion definierten automatischen Trigger zu ignorieren. Dadurch können Sie das Ausführen einer bestimmten Funktion verhindern, ohne die gesamte Funktions-App zu beenden.

Es wird empfohlen, eine Funktion mithilfe einer App-Einstellung im Format `AzureWebJobs.<FUNCTION_NAME>.Disabled`, das auf `true` festgelegt ist, zu deaktivieren. Sie können diese Anwendungseinstellung auf verschiedene Weise erstellen und ändern, z. B. über die [Azure-Befehlszeilenschnittstelle](/cli/azure/) und im [Azure-Portal](https://portal.azure.com) auf der Registerkarte **Übersicht** der Funktion. 

> [!NOTE]  
> Wenn Sie eine durch HTTP ausgelöste Funktion mithilfe der in diesem Artikel beschriebenen Methoden deaktivieren, kann der Zugriff auf den Endpunkt bei Ausführung auf dem lokalen Computer weiterhin möglich sein.  

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Verwenden Sie zum Erstellen und Ändern der App-Einstellung den Azure CLI-Befehl [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Der folgende Befehl deaktiviert eine Funktion namens `QueueTrigger`. Hierzu wird eine App-Einstellung namens `AzureWebJobs.QueueTrigger.Disabled` erstellt und auf `true` festgelegt. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Wenn Sie die Funktion wieder aktivieren möchten, führen Sie den gleichen Befehl mit dem Wert `false` aus.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>Verwenden des Portals

Sie können auch die Schaltflächen **Aktivieren** und **Deaktivieren** auf der Seite **Übersicht** der Funktion verwenden. Diese Schaltflächen ändern den Wert der App-Einstellung `AzureWebJobs.<FUNCTION_NAME>.Disabled`. Diese funktionsspezifische Einstellung wird beim ersten Deaktivieren erstellt. 

![Option „Funktionszustand“](media/disable-function/function-state-switch.png)

Auch wenn Sie Ihre Funktions-App aus einem lokalen Projekt veröffentlichen, können Sie weiterhin das Portal verwenden, um Funktionen in der Funktions-App zu deaktivieren. 

> [!NOTE]  
> Die im Portal integrierte Testfunktion ignoriert die Einstellung `Disabled`. Dies bedeutet, dass eine deaktivierte Funktion immer noch ausgeführt wird, wenn sie über das Fenster **Test** im Portal gestartet wird. 

## <a name="localsettingsjson"></a>local.settings.json

Funktionen können bei lokaler Ausführung auf die gleiche Weise deaktiviert werden. Um eine Funktion mit dem Namen `HttpExample` zu deaktivieren, fügen Sie der Values-Sammlung in der Datei „local.settings.json“ einen Eintrag wie folgt hinzu:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Andere Methoden

Obwohl die Methode der Anwendungseinstellung für alle Sprachen und alle Runtimeversionen empfohlen wird, gibt es verschiedene weitere Möglichkeiten, Funktionen zu deaktivieren. Diese Methoden, die je nach Sprache und Runtimeversion variieren, werden aus Gründen der Abwärtskompatibilität beibehalten. 

### <a name="c-class-libraries"></a>C#-Klassenbibliotheken

In einer Klassenbibliotheksfunktion können Sie auch das `Disable`-Attribut verwenden, um das Auslösen der Funktion zu verhindern. Mit diesem Attribut können Sie den Namen der Einstellung anpassen, mit der die Funktion deaktiviert wird. Verwenden Sie die Version des Attributs, mit der Sie wie im folgenden Beispiel gezeigt einen Konstruktorparameter hinzuzufügen, der auf eine boolesche App-Einstellung verweist:

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

Es gibt auch einen Konstruktor für den Parameter, der keine Zeichenfolge für den Einstellungsnamen akzeptiert. Diese Version des Attributs wird nicht empfohlen. Wenn Sie diese Version verwenden, müssen Sie das Projekt erneut kompilieren und bereitstellen, um den deaktivierten Zustand der Funktion zu ändern.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x – Skriptsprachen

In Version 1.x können Sie auch die `disabled`-Eigenschaft in der Datei *function.json* verwenden, um der Runtime mitzuteilen, dass keine Funktion ausgelöst werden soll. Diese Methode funktioniert nur bei Skriptsprachen wie C#-Skripten und JavaScript. Die `disabled`-Eigenschaft kann auf `true` oder auf den Namen einer App-Einstellung festgelegt werden:

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

>[!IMPORTANT]  
>Im Portal werden v1.x-Funktionen über Anwendungseinstellungen deaktiviert. Wenn eine Anwendungseinstellung mit der Datei „function.json“ in Konflikt steht, kann ein Fehler auftreten. Sie sollten die Eigenschaft `disabled` aus der Datei „function.json“ entfernen, um Fehler zu verhindern. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wird das Deaktivieren von automatischen Triggern behandelt. Weitere Informationen zu Triggern finden Sie unter [Trigger und Bindungen](functions-triggers-bindings.md).
