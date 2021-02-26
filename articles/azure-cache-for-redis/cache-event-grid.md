---
title: Azure Cache for Redis Event Grid – Übersicht
description: Verwenden Sie Azure Event Grid zum Veröffentlichen von Azure Cache for Redis-Ereignissen.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056877"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Azure Cache for Redis Event Grid – Übersicht 

Azure Cache for Redis-Ereignisse wie Patchen, Skalieren Import/Export (RDB)-Ereignisse werden mithilfe von [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) an Abonnenten wie Azure Functions, Azure Logic Apps oder sogar an Ihren eigenen HTTP-Listener gepusht. Event Grid sorgt über umfassende Wiederholungsrichtlinien und unzustellbare Nachrichten für eine zuverlässige Ereignisübermittlung an Ihre Anwendungen.

Im Artikel zum [Azure Cache for Redis-Ereignisschema ](../event-grid/event-schema-azure-cache.md) finden Sie eine vollständige Liste der Ereignisse, die von Azure Cache for Redis unterstützt werden.

Wenn Sie Azure Cache for Redis-Ereignisse ausprobieren möchten, informieren Sie sich in folgenden Schnellstartanleitungen:

|Gewünschtes Tool:    |Siehe in dieser Schnellstartanleitung: |
|--|-|
|Azure-Portal    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit dem Azure-Portal](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit PowerShell](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit der Azure CLI](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Ereignismodell

Event Grid verwendet [Ereignisabonnements](../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten. Diese Abbildung veranschaulicht die Beziehung zwischen Ereignisherausgebern, Ereignisabonnements und Ereignishandlern.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Event Grid-Modell.":::

Abonnieren Sie zunächst einen Endpunkt für ein Ereignis. Sobald ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an den Endpunkt.

Im Artikel zum [Azure Cache for Redis-Ereignisschema](../event-grid/event-schema-azure-cache.md) finden Sie:

> [!div class="checklist"]
> * Eine vollständige Liste der Azure Cache for Redis-Ereignisse und Informationen dazu, wie jedes Ereignis ausgelöst wird.
> * Ein Beispiel dafür, welche Daten von Event Grid zu den einzelnen Ereignissen gesendet werden
> * Angaben zum Zweck der jeweiligen Schlüssel-Wert-Paare, die in den Daten enthalten sind


## <a name="best-practices-for-consuming-events"></a>Bewährte Methoden zum Nutzen von Ereignissen

Anwendungen, die Azure Cache for Redis-Ereignisse verwenden, sollten ein paar empfohlene Methoden nutzen:
> [!div class="checklist"]
> * Da mehrere Abonnements zum Weiterleiten von Ereignissen an den gleichen Ereignishandler konfiguriert werden können, ist es wichtig, nicht davon auszugehen, dass Ereignisse von einer bestimmten Quelle stammen, sondern das Thema der Nachricht zu überprüfen, um sicherzugehen, dass es aus der erwarteten Azure Cache for Redis-Instanz stammt.
> * Überprüfen Sie auf ähnliche Weise, ob Sie auf die Verarbeitung des eventType vorbereitet sind, und gehen Sie nicht davon aus, dass alle Ereignisse, die Sie empfangen, den von Ihnen erwarteten Typen entsprechen.
> * Azure Cache for Redis-Ereignisse gewährleisten eine mindestens einmalige Zustellung (At-Least-Once) an Abonnenten, wodurch sichergestellt wird, dass alle Nachrichten ausgegeben werden. Aufgrund von Wiederholungen oder abhängig von der Verfügbarkeit von Abonnements kommt es jedoch möglicherweise gelegentlich zu doppelten Nachrichten. Weitere Informationen zur Übermittlung und Wiederholung von Nachrichten finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Event Grid, und probieren Sie Azure Cache for Redis-Ereignisse aus:

- [Einführung in Azure Event Grid](../event-grid/overview.md)
- [Azure Cache for Redis-Ereignisschema](../event-grid/event-schema-azure-cache.md)
- [Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit der Azure CLI](cache-event-grid-quickstart-cli.md)
- [Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit dem Azure-Portal](cache-event-grid-quickstart-portal.md)
- [Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit PowerShell](cache-event-grid-quickstart-powershell.md)
