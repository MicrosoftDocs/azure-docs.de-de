---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493943"
---
In Visual Studio Code können Sie Ihrer function.json-Datei Bindungen hinzufügen, indem Sie auf eine Folge komfortabler Aufforderungen reagieren. 

Um eine Bindung hinzuzufügen, öffnen Sie die Befehlspalette (F1), und geben Sie **Azure Functions: Bindung hinzufügen...** ein. Wählen Sie die Funktion für die neue Bindung, und folgen Sie dann den Aufforderungen, die je nach Art der Bindung variieren, die der Funktion hinzugefügt wird. 

In der Folge finden Sie Beispielaufforderungen zum Definieren einer neuen Ausgabebindung für den Speicher:

| Eingabeaufforderung | Wert | BESCHREIBUNG |
| -------- | ----- | ----------- |
| **Select binding direction** (Wählen Sie die Bindungsrichtung aus) | `out` | Die Bindung ist eine Ausgabebindung. |
| **Select binding with direction** (Bindung mit Richtung auswählen) | `Azure Queue Storage` | Die Bindung ist eine Azure Storage-Warteschlangenbindung. |
| **Der Name, der zum Identifizieren dieser Bindung in Ihrem Code verwendet wird** | `msg` | Name, der den Bindungsparameter identifiziert, auf den in Ihrem Code verwiesen wird. |
| **Die Warteschlange, an die die Nachricht gesendet wird** | `outqueue` | Der Name der Warteschlange, in den die Bindung schreibt. Wenn der *queueName* nicht vorhanden ist, erstellt die Bindung ihn bei der ersten Verwendung. |
| **Select setting from "local.setting.json"** (Eine Einstellung aus „local.setting.json“ auswählen) | `MyStorageConnection` | Der Name einer Anwendungseinstellung, die die Verbindungszeichenfolge für das Speicherkonto enthält. Die Einstellung `AzureWebJobsStorage` enthält die Verbindungszeichenfolge für das Speicherkonto, das Sie mit der Funktions-App erstellt haben. |

Sie können auch in Ihrem Funktionsordner mit der rechten Maustaste (Strg+Klick unter macOS) direkt auf die Datei **function.json** klicken, **Bindung hinzufügen** auswählen und denselben Aufforderungen folgen.

In diesem Beispiel wird die folgende Bindung dem Array `bindings` in Ihrer function.json-Datei hinzugefügt:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```