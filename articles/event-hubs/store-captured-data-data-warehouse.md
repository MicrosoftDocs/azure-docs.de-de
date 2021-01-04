---
title: 'Tutorial: Migrieren von Ereignisdaten zu Azure Synapse Analytics – Azure Event Hubs'
description: Hier wird erläutert, wie erfasste Event Hubs-Daten mithilfe von Azure Event Grid und Functions zu Azure Synapse Analytics migriert werden.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854139"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Tutorial: Migrieren erfasster Event Hubs-Daten zu Azure Synapse Analytics mithilfe von Event Grid und Azure Functions
Mit Azure Event Hubs [Capture](./event-hubs-capture-overview.md) können Sie die Streamingdaten in Event Hubs automatisch in einer Azure Blob Storage- oder Azure Data Lake Store-Instanz erfassen. In diesem Tutorial erfahren Sie, wie Sie erfasste Event Hubs-Daten aus Storage mithilfe einer durch eine [Event Grid](../event-grid/overview.md)-Instanz ausgelösten Azure-Funktion zu Azure Synapse Analytics migrieren.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Nächste Schritte 
Sie können leistungsstarke Datenvisualisierungstools für Ihr Data Warehouse nutzen, um verwertbare Erkenntnisse zu gewinnen.

In [diesem Artikel](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) erfahren Sie, wie Sie Power BI mit Azure Synapse Analytics verwenden.