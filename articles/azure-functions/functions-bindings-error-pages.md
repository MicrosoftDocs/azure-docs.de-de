---
title: 'Azure Functions: Leitfaden für Fehlerbehandlung und Wiederholungen'
description: Erfahren Sie, wie Sie Fehler und Wiederholungsereignisse in Azure Functions mit Links zu bestimmten Bindungsfehlern behandeln können.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284453"
---
# <a name="azure-functions-error-handling-and-retries"></a>Fehlerbehandlung und Wiederholungen in Azure Functions

Das Behandeln von Fehlern in Azure Functions ist wichtig, um verlorene Daten und verpasste Ereignisse zu vermeiden sowie um die Integrität Ihrer Anwendung zu überwachen.

Dieser Artikel beschreibt allgemeine Strategien für die Fehlerbehandlung zusammen mit Links zu bindungsspezifischen Fehlern.

## <a name="handling-errors"></a>Behandlung von Fehlern

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>Bindungsfehlercodes

Bei der Integration in Azure-Dienste können Fehler auftreten, deren Ursache in den APIs der zugrunde liegenden Dienste liegt. Informationen zu bindungsspezifischen Fehlern finden Sie im Abschnitt **Ausnahmen und Rückgabecodes** der folgenden Artikel:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Tabellenspeicherung](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
