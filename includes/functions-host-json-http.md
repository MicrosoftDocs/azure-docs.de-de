---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131606"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Eigenschaft  |Standard | BESCHREIBUNG |
|---------|---------|---------| 
|routePrefix|api|Das Routenpräfix, das für alle Routen gilt. Verwenden Sie eine leere Zeichenfolge, um das Standardpräfix zu entfernen. |
|maxOutstandingRequests|200*|Die maximale Anzahl ausstehender Anforderungen, die zu einem beliebigen Zeitpunkt gespeichert werden. Dieser Grenzwert umfasst Anforderungen in der Warteschlange, deren Ausführung aber noch nicht gestartet ist, sowie alle laufenden Ausführungen. Alle eingehenden Anforderungen über diesem Grenzwert werden mit der Antwort 429 „Ausgelastet“ zurückgewiesen. Das ermöglicht es dem Aufrufer zeitbasierte Strategien für Wiederholungsversuche einzusetzen, und Sie erhalten damit die Möglichkeit, die maximalen Wartezeiten für Anforderungen zu steuern. Damit wird nur das Queuing gesteuert, das innerhalb des Ausführungspfads des Skripthosts auftritt. Andere Warteschlangen, z.B. die ASP.NET-Anforderungswarteschlange, sind von dieser Einstellung nicht betroffen und werden weiterhin verwendet. *Der Standardwert für Version 1.x ist nicht beschränkt. Der Standardwert für Version 2.x in einem Verbrauchstarif beträgt 200. Der Standardwert für Version 2.x in einem dedizierten Tarif ist nicht beschränkt.|
|maxConcurrentRequests|100*|Die maximale Anzahl von HTTP-Funktionen, die parallel ausgeführt werden. Dadurch können Sie die Parallelität steuern und somit die Verwaltung der Ressourcenverwendung vereinfachen. Beispielsweise könnten Sie über eine HTTP-Funktion verfügen, die viele Systemressourcen (Speicher/CPU/Sockets) verbraucht und daher Probleme verursacht, wenn die Parallelität zu hoch ist. Oder eine Funktion führt ausgehende Anforderungen an einen Dienst eines Drittanbieters durch, und die Rate dieser Aufrufe muss eingeschränkt werden. In diesen Fällen kann eine Drosselung hilfreich sein. *Der Standardwert für Version 1.x ist nicht beschränkt. Der Standardwert für Version 2.x in einem Verbrauchstarif beträgt 100. Der Standardwert für Version 2.x in einem dedizierten Tarif ist nicht beschränkt.|
|dynamicThrottlesEnabled|true*|Bei einer Aktivierung dieser Einstellung überprüft die Pipeline zur Anforderungsverarbeitung regelmäßig Leistungsindikatoren zur Systemleistung wie Verbindungen/Threads/Prozesse/Speicher/CPU usw., und wenn einer dieser Leistungsindikatoren einen integrierten Schwellenwert (80 %) übersteigt, werden Anforderungen mit der Antwort „429 – Ausgelastet“ zurückgewiesen, bis die Leistungsindikatoren wieder ein normales Niveau erreichen. *Der Standardwert für Version 1.x lautet „false“. Der Standardwert für Version 2.x in einem Verbrauchstarif lautet „true“. Der Standardwert für Version 2.x in einem dedizierten Tarif lautet „false“.|
