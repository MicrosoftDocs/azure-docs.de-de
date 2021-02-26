---
title: Reagieren auf Azure Media Services-Ereignisse
description: In diesem Artikel wird das Abonnieren von Media Services-Ereignissen mithilfe von Azure Event Grid erläutert.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0d4479914ffee6cf667a5f6db2fd665baf2b857c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895644"
---
# <a name="handling-event-grid-events"></a>Behandeln von Event Grid-Ereignissen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services-Ereignisse ermöglichen es Anwendungen, mithilfe moderner serverloser Architekturen auf verschiedene Ereignisse zu reagieren (z.B. auf Statusänderungsereignisse für Aufträge). Dies geschieht ohne komplizierten Code oder teure und ineffiziente Abrufdienste. Stattdessen werden die Ereignisse per Push über [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) an Ereignishandler wie [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) oder sogar Ihren eigenen Webhook übertragen, und Sie zahlen nur für das, was Sie tatsächlich nutzen. Informationen zu den Preisen finden Sie unter [Event Grid – Preise](https://azure.microsoft.com/pricing/details/event-grid/).

Die Verfügbarkeit von Media Services-Ereignissen ist an die [Verfügbarkeit](../../event-grid/overview.md) von Event Grid gebunden und wird in anderen Regionen verfügbar, wenn dort Event Grid verfügbar ist.  

## <a name="media-services-events-and-schemas"></a>Media Services-Ereignisse und Schemata

Event Grid verwendet [Ereignisabonnements](../../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten. Media Services-Ereignisse enthalten alle Informationen, die Sie zur Reaktion auf Änderungen in Ihren Daten benötigen. Sie können ein Media Services-Ereignis daran erkennen, dass die eventType-Eigenschaft mit „Microsoft.Media“ beginnt.

Weitere Informationen finden Sie unter [Media Services-Ereignisschemas](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Methoden zum Nutzen von Ereignissen

Anwendungen, die Media Services-Ereignisse behandeln, sollten einige empfohlene Methoden verwenden:

* Da mehrere Abonnements zum Weiterleiten von Ereignissen an den gleichen Ereignishandler konfiguriert werden können, ist es wichtig, nicht davon auszugehen, dass Ereignisse aus einer bestimmten Quelle stammen, sondern das Thema der Nachricht zu überprüfen, um sicherzustellen, dass es aus dem Speicherkonto stammt, das Sie erwarten.
* Überprüfen Sie auf ähnliche Weise, ob Sie auf die Verarbeitung des eventType vorbereitet sind, und gehen Sie nicht davon aus, dass alle Ereignisse, die Sie empfangen, den von Ihnen erwarteten Typen entsprechen.
* Ignorieren Sie Felder, die Sie nicht verstehen.  So müssen Sie sich nicht mit neuen Features auseinandersetzen, die in der Zukunft hinzugefügt werden könnten.
* Verwenden Sie Präfix- und Suffixübereinstimmungen für „subject“, um Ereignisse auf ein bestimmtes Ereignis zu beschränken.

> [!NOTE]
> Ereignisse unterliegen der [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) von Event Grid. Wenn Sie Ereignisbenachrichtigungen mithilfe von APIs erhalten möchten, finden Sie weitere Informationen in den Beispielen zum Verarbeiten von Ereignissen mit dem [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) oder [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von Ereignissen: Portal](monitor-events-portal-how-to.md)
* [Überwachen von Ereignissen: Befehlszeilenschnittstelle](job-state-events-cli-how-to.md)
