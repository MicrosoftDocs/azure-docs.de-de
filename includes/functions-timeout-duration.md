---
title: include file
description: include file
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178653"
---
## <a name="timeout"></a>Funktions-App-Timeoutdauer 

Die Timeoutdauer einer Funktions-App wird durch die functionTimeout-Eigenschaft in der [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout)-Projektdatei definiert. Die folgende Tabelle listet die Standard- und Maximalwerte in Minuten für beide Pläne und beide Laufzeitversionen auf:

| Plan | Laufzeitversion | Standard | Maximum |
|------|---------|---------|---------|
| Nutzung | 1.x | 5 | 10 |
| Nutzung | 2.x | 5 | 10 |
| App Service | 1.x | Unbegrenzt | Unbegrenzt |
| App Service | 2.x | 30 | Unbegrenzt |

> [!NOTE] 
> Unabhängig von der Timeouteinstellung der Funktions-App stellen 230 Sekunden die längste Zeit dar, die einer über HTTP ausgelösten Funktion bis zur Reaktion auf eine Anfrage bleibt. Dies hat seinen Grund im [Standard-Leerlauftimeout von Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Erwägen Sie für längere Verarbeitungszeiten die Verwendung des [asynchronen Durable Functions-Musters](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) oder [stellen Sie die eigentliche Arbeit zurück, und geben Sie unmittelbar eine Antwort zurück](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
