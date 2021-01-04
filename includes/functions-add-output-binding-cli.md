---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904068"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>Hinzufügen einer Ausgabebindungsdefinition zur Funktion

Eine Funktion kann zwar nur über einen Trigger verfügen, aber über mehrere Eingabe- und Ausgabebindungen. Hiermit können Sie eine Verbindung mit anderen Azure-Diensten und -Ressourcen herstellen, ohne benutzerdefinierten Integrationscode zu schreiben. 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Sie deklarieren diese Bindungen in der Datei *function.json* in Ihrem Funktionsordner. In der vorherigen Schnellstartanleitung hat die Datei *function.json* im Ordner *HttpExample* zwei Bindungen in der Sammlung `bindings` enthalten:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Jede Bindung verfügt mindestens über einen Typ, eine Richtung und einen Namen. Im obigen Beispiel hat die erste Bindung den Typ `httpTrigger` und die Richtung `in`. Für die Richtung `in` wird mit `name` der Name eines Eingabeparameters angegeben, der an die Funktion gesendet wird, wenn sie vom Trigger aufgerufen wird.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Die zweite Bindung in der Sammlung hat den Namen `res`. Diese `http`-Bindung ist eine Ausgabebindung (`out`), die zum Schreiben der HTTP-Antwort verwendet wird. 

Fügen Sie zum Schreiben in eine Azure Storage-Warteschlange aus dieser Funktion eine `out`-Bindung vom Typ `queue` mit dem Namen `msg` hinzu. Dies ist im Code unten veranschaulicht:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Die zweite Bindung der Sammlung hat den Typ `http` mit der Richtung `out`. In diesem Fall wird mit dem speziellen `name`-Element von `$return` angegeben, dass für diese Bindung der Rückgabewert der Funktion verwendet wird, anstatt einen Eingabeparameter bereitzustellen.

Fügen Sie zum Schreiben in eine Azure Storage-Warteschlange aus dieser Funktion eine `out`-Bindung vom Typ `queue` mit dem Namen `msg` hinzu. Dies ist im Code unten veranschaulicht:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Die zweite Bindung in der Sammlung hat den Namen `res`. Diese `http`-Bindung ist eine Ausgabebindung (`out`), die zum Schreiben der HTTP-Antwort verwendet wird. 

Fügen Sie zum Schreiben in eine Azure Storage-Warteschlange aus dieser Funktion eine `out`-Bindung vom Typ `queue` mit dem Namen `msg` hinzu. Dies ist im Code unten veranschaulicht:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In diesem Fall wird `msg` als Ausgabeargument für die Funktion bereitgestellt. Für einen `queue`-Typ müssen Sie auch den Namen der Warteschlange in `queueName` und den Namen (*name*) der Azure Storage-Verbindung (aus *local.settings.json*) in `connection` angeben. 
::: zone-end  
