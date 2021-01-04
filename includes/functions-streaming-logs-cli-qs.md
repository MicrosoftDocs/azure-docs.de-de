---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424717"
---
Führen Sie den folgenden Befehl aus, um [Streamingprotokolle](../articles/azure-functions/functions-run-local.md#enable-streaming-logs) nahezu in Echtzeit anzuzeigen:

```console
func azure functionapp logstream <APP_NAME> 
```

In einem separaten Terminalfenster oder im Browser rufen Sie die Remotefunktion erneut auf. Ein ausführliches Protokoll der Funktionsausführung in Azure wird im Terminal angezeigt. 