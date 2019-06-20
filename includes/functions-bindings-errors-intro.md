---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177886"
---
[Trigger und Bindungen](../articles/azure-functions/functions-triggers-bindings.md) von Azure Functions kommunizieren mit verschiedenen Azure-Diensten. Bei der Integration in diese Dienste können Fehler auftreten, deren Ursache in den APIs der zugrunde liegenden Azure-Dienste liegt. Fehler können auch auftreten, wenn Sie versuchen, mit anderen Diensten aus Ihrem Funktionscode über REST oder Clientbibliotheken zu kommunizieren. Zur Vermeidung von Datenverlusten und zum Sicherstellen eines ordnungsgemäßen Verhaltens der Funktionen ist es wichtig, Fehler aus allen Quellen zu behandeln.

Die folgenden Trigger bieten eine integrierte Unterstützung der Wiederholung:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Warteschlange/Thema)](../articles/azure-functions/functions-bindings-service-bus.md)

Standardmäßig werden für diese Trigger bis zu fünf Wiederholungsversuche ausgeführt. Nach dem fünften Wiederholungsversuch schreiben diese Trigger eine Nachricht in eine spezielle [Warteschlange für nicht verarbeitete Nachrichten](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages).

Für die anderen Trigger von Funktionen ist kein integrierter Wiederholungsmechanismus beim Auftreten von Fehlern während der Funktionsausführung vorhanden. Um den Verlust von Triggerinformationen zu verhindern, wenn ein Fehler in Ihrer Funktion auftritt, wird empfohlen, try-catch-Blöcke zum Abfangen von Fehlern in Ihrem Funktionscode zu verwenden. Wenn ein Fehler auftritt, schreiben Sie die Informationen, die vom Trigger an die Funktion übergeben werden, in eine spezielle Warteschlange für „unzustellbare“ Nachrichten. Dieser Ansatz wird auch für den [Blob Storage-Trigger](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs) verwendet.

Auf diese Weise können Sie Triggerereignisse, die aufgrund von Fehlern verloren gehen könnten, erfassen und zu einem späteren Zeitpunkt mit einer anderen Funktion wiederholen, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten mit den gespeicherten Informationen zu verarbeiten.  
