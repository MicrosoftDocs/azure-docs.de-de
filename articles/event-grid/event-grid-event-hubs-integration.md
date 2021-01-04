---
title: 'Tutorial: Senden von Event Hubs-Daten an Data Warehouse – Event Grid'
description: Hier wird erläutert, wie erfasste Event Hubs-Daten über Azure Functions und Event Grid-Trigger in Azure Synapse Analytics gespeichert werden.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854715"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutorial: Streamen von Big Data in ein Data Warehouse
Bei Azure [Event Grid](overview.md) handelt es sich um einen intelligenten Ereignisroutingdienst, der es Ihnen ermöglicht, auf Benachrichtigungen oder Ereignisse von Apps und Diensten zu reagieren. Er kann beispielsweise eine Azure-Funktion auslösen, um Event Hubs-Daten zu verarbeiten, die in Blob Storage oder Data Lake Store erfasst wurden. In diesem [Beispiel](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) wird gezeigt, wie erfasste Event Hubs-Daten mithilfe von Event Grid und Azure Functions aus Blobspeicher zu Azure Synapse Analytics – genauer gesagt zu einem dedizierten SQL-Pool – migriert werden.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den Unterschieden in der Azure-Messagingdiensten finden Sie unter [Wählen zwischen Azure-Diensten, die Nachrichten übermitteln](compare-messaging-services.md).
* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Eine Einführung zu Event Hubs Capture finden Sie unter [Aktivieren von Event Hubs Capture über das Azure-Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Weitere Informationen zum Einrichten und Ausführen des Beispiels finden Sie unter [Event Hubs Capture and Event Grid sample](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) (Beispiel für Events Hubs Capture und Event Grid).
