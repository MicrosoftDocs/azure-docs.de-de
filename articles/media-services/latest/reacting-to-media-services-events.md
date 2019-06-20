---
title: Reaktion auf Azure Media Services-Ereignisse | Microsoft-Dokumentation
description: Verwenden Sie Azure Event Grid, um Media Services-Ereignisse zu abonnieren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996149"
---
# <a name="handling-event-grid-events"></a>Behandeln von Event Grid-Ereignissen

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

## <a name="next-steps"></a>Nächste Schritte

[Abrufen von Auftragsstatusereignissen](job-state-events-cli-how-to.md)
