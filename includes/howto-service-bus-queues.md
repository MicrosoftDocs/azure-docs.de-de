---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: d3d33c87dc1adf65a53b71cc4c833e7f4a191670
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171285"
---
## <a name="what-are-service-bus-queues"></a>Was sind Service Bus-Warteschlangen?
Service Bus-Warteschlangen unterstützen ein Kommunikationsmodell namens **Brokermessaging** . Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe (Broker) fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort. Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)** -Verfahren an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

* Kommunikation zwischen Web- und Workerrollen in Azure-Anwendungen mit mehreren Ebenen
* Kommunikation zwischen lokalen Apps und von Azure gehosteten Apps in einer Hybridlösung
* Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen unterstützen Sie bei der einfacheren Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.


