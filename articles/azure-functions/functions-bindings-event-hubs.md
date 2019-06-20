---
title: Azure Event Hubs-Bindungen für Azure Functions
description: Erfahren Sie, wie Azure Event Hubs-Bindungen in Azure Functions verwendet werden.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 7ca3ab8e3becf884fdba460f1981430e0c66ef99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511780"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs-Bindungen für Azure Functions

Dieser Artikel erläutert das Arbeiten mit [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)-Bindungen für Azure Functions. Azure Functions unterstützt Trigger- und Ausgabebindungen für Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakete: Functions 1.x

Für Azure Functions Version 1.x werden die Event Hubs-Bindungen im NuGet-Paket [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus), Version 2.x bereitgestellt.
Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakete: Functions 2.x

Verwenden Sie für Functions 2.x das Paket [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs), Version 3.x.
Den Quellcode für das Paket finden Sie im GitHub-Repository [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)
