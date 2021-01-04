---
title: Einführung in Azure-Warteschlangen – Azure Storage
description: Hier finden Sie eine Einführung in Azure Queues, ein Dienst für die Speicherung sehr vieler Nachrichten. Ein Warteschlangendienst enthält ein URL-Format, ein Speicherkonto, eine Warteschlange und eine Nachricht.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: cb9d25bc9449c96ec7bf5ba11f8d64d59c8ddb4d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491943"
---
# <a name="what-are-azure-queues"></a>Was sind Azure-Warteschlangen?

Azure Queue Storage ist ein Dienst für die Speicherung großer Nachrichtenmengen. Sie können überall auf der Welt über authentifizierte Aufrufe mithilfe von HTTP oder HTTPS auf Nachrichten zugreifen. Eine Warteschlangennachricht kann bis zu 64 KB groß sein. Eine Warteschlange kann Millionen Nachrichten enthalten, bis die maximale Kapazität eines Speicherkontos erreicht ist. Warteschlangen werden häufig verwendet, um ein Arbeits-Backlog zur asynchronen Verarbeitung zu erstellen.

## <a name="queue-service-concepts"></a>Konzepte des Warteschlangendiensts

Der Warteschlangendienst umfasst die folgenden Komponenten:

![Diagramm, das die Beziehung zwischen einem Speicherkonto, Warteschlangen und Nachrichten zeigt.](./media/storage-queues-introduction/queue1.png)

- **URL-Format**: Warteschlangen sind über das folgende URL-Format adressierbar:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Mit der folgenden URL kann eine der Warteschlangen im Diagramm adressiert werden:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Weitere Informationen zur Speicherkontokapazität finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Warteschlange:** Eine Warteschlange enthält einen Satz von Nachrichten. Der Warteschlangenname **muss** ausschließlich aus Kleinbuchstaben bestehen. Informationen zum Benennen von Warteschlangen finden Sie unter [Benennen von Warteschlangen und Metadaten](/rest/api/storageservices/Naming-Queues-and-Metadata).

- **Nachricht:** Eine Nachricht in einem beliebigen Format und mit einer Größe von bis zu 64 KB. Vor Version 2017-07-29 beträgt die maximale Gültigkeitsdauer sieben Tage. Für Version 2017-07-29 oder höhere Versionen kann die maximale Gültigkeitsdauer eine beliebige positive Zahl sein. Mit -1 wird angegeben, dass die Nachricht nicht abläuft. Wird dieser Parameter ausgelassen, beträgt die Standardgültigkeitsdauer sieben Tage.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Speicherkontos](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Erste Schritte mit Azure Queue Storage mit .NET](storage-dotnet-how-to-use-queues.md)
