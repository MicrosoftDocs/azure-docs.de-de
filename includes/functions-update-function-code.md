---
title: Includedatei
description: Includedatei
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132147"
---
## <a name="update-the-function"></a>Aktualisieren der Funktion

Standardmäßig erstellt die Vorlage eine Funktion, die bei Anforderungen einen Funktionsschlüssel benötigt. Um das Testen der Funktion in Azure zu vereinfachen, müssen Sie die Funktion aktualisieren, um anonymen Zugriff zuzulassen. Wie Sie diese Änderung vornehmen, hängt von Ihrer Funktionenprojektsprache ab.

### <a name="c"></a>C\#

Öffnen Sie die MyHttpTrigger.cs-Codedatei, die Ihre neue Funktion ist, aktualisieren Sie das **AuthorizationLevel**-Attribut in der Funktionsdefinition mit dem Wert `Anonymous`, und speichern Sie die Änderungen.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Öffnen Sie die Datei „function.json“ für Ihre neue Funktion in einem Text-Editor, aktualisieren Sie die **authLevel**-Eigenschaft in **bindings** in `anonymous`, und speichern Sie die Änderungen.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Jetzt können Sie die Funktion in Azure aufrufen, ohne den Funktionsschlüssel anzugeben. Der Funktionsschlüssel ist bei lokaler Ausführung nie erforderlich.
