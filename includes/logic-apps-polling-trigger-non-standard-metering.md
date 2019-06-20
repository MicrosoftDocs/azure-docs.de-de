---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178684"
---
Um genauere Verbrauchskosten zu schätzen, berücksichtigen Sie die mögliche Anzahl von Nachrichten oder Ereignissen, die an einem beliebigen Tag eingehen, statt Ihre Berechnungen nur das Abrufintervall zu stützen. Wenn ein Ereignis oder eine Nachricht die Auslösekriterien erfüllt, versuchen viele Trigger sofort, alle anderen wartenden Ereignisse oder Nachrichten, die die Kriterien erfüllen, zu lesen. Dieses Verhalten bedeutet, dass auch bei Auswahl eines längeren Abrufintervalls der Trigger aufgrund der Anzahl der wartenden Ereignisse oder Nachrichten, die für das Starten von Workflows qualifiziert sind, ausgelöst wird. Zu den Triggern, die diesem Verhalten folgen, zählen Azure Service Bus und Azure Event Hub.

Nehmen Sie beispielsweise an, dass Sie einen Trigger eingerichtet haben, der täglich einen Endpunkt überprüft. Wenn der Trigger den Endpunkt überprüft und 15 Ereignisse findet, die die Kriterien erfüllen, wird der Trigger ausgelöst und führt den entsprechenden Workflow 15 mal aus. Logik-Apps messen alle Aktionen, die diese 15 Workflows ausführen, einschließlich der Trigger-Anforderungen. Zum Berechnen der möglichen Kosten können Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) ausprobieren.