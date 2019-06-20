---
title: Veröffentlichen und Abonnieren von Anwendungsereignissen – Azure Event Grid
description: Senden Sie Ereignisdaten mit Azure Event Grid aus einer Quelle an Handler. Erstellen Sie ereignisbasierte Anwendungen, und integrieren Sie sie in Azure-Dienste.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 05/25/2019
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: bfc8b62c61e2b9d01ffa0c582468b6236c92cccc
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257941"
---
# <a name="what-is-azure-event-grid"></a>Was ist Azure Event Grid?

Mit Azure Event Grid können Sie mühelos Anwendungen mit ereignisbasierten Architekturen erstellen. Wählen Sie zuerst die Azure-Ressource aus, die Sie abonnieren möchten, und geben Sie dann den Ereignishandler oder WebHook-Endpunkt an, an den das Ereignis gesendet werden soll. Event Grid verfügt über integrierte Unterstützung für Ereignisse, die aus Azure-Diensten stammen, wie Speicherblobs und Ressourcengruppen. Event Grid bietet auch Unterstützung für Ihre eigenen Ereignisse mit benutzerdefinierten Themen. 

Sie können Filter zum Weiterleiten bestimmter Ereignisse an verschiedene Endpunkte verwenden, Multicasts zu mehreren Endpunkten ausführen und sicherstellen, dass Ihre Ereignisse zuverlässig übermittelt werden.

Azure Event Grid wird zum Maximieren der Verfügbarkeit durch eine native Verteilung auf mehrere Fehlerdomänen in jeder Region sowie auf Verfügbarkeitszonen (in Regionen, in denen diese unterstützt werden) bereitgestellt. Azure Event Grid ist derzeit in allen öffentlichen Regionen verfügbar. In den Clouds „Azure Deutschland“, „Azure China 21Vianet“ und „Azure Government“ ist es noch nicht verfügbar.

Dieser Artikel enthält eine Übersicht zu Azure Event Grid. Wenn Sie in die Verwendung von Event Grid einsteigen möchten, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md). 

![Event Grid-Modell von Quellen und Handlern](./media/overview/functional-model.png)

Diese Abbildung zeigt, wie Event Grid Quellen und Handler verbindet, stellt jedoch keine vollständige Liste der unterstützten Integrationen dar.

## <a name="event-sources"></a>Ereignisquellen

Ausführliche Informationen zu den Funktionen der einzelnen Quellen sowie entsprechende Artikel finden Sie unter [Ereignisquellen](event-sources.md). Derzeit unterstützen die folgenden Azure-Dienste das Senden von Ereignissen an Event Grid:

* [Azure-Abonnements (Verwaltungsvorgänge)](event-sources.md#azure-subscriptions)
* [Containerregistrierung](event-sources.md#container-registry)
* [Benutzerdefinierte Themen](event-sources.md#custom-topics)
* [Event Hubs](event-sources.md#event-hubs)
* [IoT Hub](event-sources.md#iot-hub)
* [Media Services](event-sources.md#media-services)
* [Ressourcengruppen (Verwaltungsvorgänge)](event-sources.md#resource-groups)
* [Service Bus](event-sources.md#service-bus)
* [Storage Blob](event-sources.md#storage)
* [Azure Maps](event-sources.md#maps)

## <a name="event-handlers"></a>Ereignishandler

Ausführliche Informationen zu den Funktionen der einzelnen Handler sowie entsprechende Artikel finden Sie unter [Ereignishandler](event-handlers.md). Derzeit unterstützen die folgenden Azure-Dienste das Verarbeiten von Ereignissen von Event Grid: 

* [Azure Automation](event-handlers.md#azure-automation)
* [Azure-Funktionen](event-handlers.md#azure-functions)
* [Event Hubs](event-handlers.md#event-hubs)
* [Hybridverbindungen](event-handlers.md#hybrid-connections)
* [Logik-Apps](event-handlers.md#logic-apps)
* [Microsoft Flow](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Queue Storage](event-handlers.md#queue-storage)
* [Webhooks](event-handlers.md#webhooks)

## <a name="concepts"></a>Konzepte

Es gibt fünf Konzepte in Azure Event Grid, die Sie nutzen können:

* **Ereignisse**: Was ist passiert?
* **Ereignisquellen**: Wo das Ereignis stattgefunden hat
* **Themen**: Der Endpunkt, an den Herausgeber Ereignisse senden.
* **Ereignisabonnements**: Der Endpunkt oder integrierte Mechanismus zum Weiterleiten von Ereignissen, manchmal zu mehreren Handlern. Abonnements werden auch vom Handler verwendet, um eingehende Ereignisse intelligent zu filtern.
* **Ereignishandler**: Die App oder der Dienst, die/der auf das Ereignis reagiert.

Weitere Informationen zu diesen Konzepten finden Sie unter [Concepts in Azure Event Grid](concepts.md) (Konzepte in Azure Event Grid).

## <a name="capabilities"></a>Funktionen

Azure Event Grid bietet unter anderem folgende zentrale Features:

* **Einfachheit**: Leiten Sie Ereignisse mit Point-and-Click aus Ihrer Azure-Ressource zu einem Ereignishandler oder Endpunkt.
* **Erweiterte Filterung**: Filtern Sie nach Ereignistyp oder Ereignisveröffentlichungspfad, um sicherzustellen, dass Ereignishandler nur relevante Ereignisse empfangen.
* **Auffächern**: Abonnieren Sie mehrere Endpunkte für das gleiche Ereignis, um Kopien des Ereignisses an so viele Stellen wie erforderlich zu senden.
* **Zuverlässigkeit**: 24-Stunden-Wiederholung mit exponentiellem Backoff, um sicherzustellen, dass Ereignisse übermittelt werden.
* **Bezahlen pro Ereignis**: Zahlen Sie nur für den Umfang, in dem Sie Event Grid nutzen.
* **Hoher Durchsatz**: Erstellen Sie Workloads mit hohem Volumen auf Event Grid mit Unterstützung für Millionen Ereignisse pro Sekunde.
* **Integrierte Ereignisse**: Mit ressourcendefinierten integrierten Ereignissen sind Sie schnell einsatzbereit.
* **Benutzerdefinierte Ereignisse**: Nutzen Sie die Weiterleitungs- und Filterfunktionen von Event Grid, um zuverlässig benutzerdefinierte Ereignisse in Ihre App zu übertragen.

Einen Vergleich von Event Grid, Event Hubs und Service Bus finden Sie unter [Auswählen zwischen Azure-Diensten für die Übermittlung von Nachrichten](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Welche Möglichkeiten bietet Event Grid?

Azure Event Grid bietet verschiedene Funktionen, die die serverlose, Betriebsautomatisierungs- und [Integrationsarbeit](https://azure.com/integration) erheblich verbessern: 

### <a name="serverless-application-architectures"></a>Serverlose Anwendungsarchitekturen

![Serverlose Anwendungsarchitektur](./media/overview/serverless_web_app.png)

Event Grid verknüpft Datenquellen und Ereignishandler. Verwenden Sie Event Grid z.B. zum Auslösen einer serverlosen Funktion, die Images analysiert, wenn sie einem Blobspeichercontainer hinzugefügt werden. 

### <a name="ops-automation"></a>Betriebsautomatisierung

![Betriebsautomatisierung](./media/overview/Ops_automation.png)

Mit Event Grid können Sie die Automatisierung beschleunigen und die Richtlinienerzwingung vereinfachen. Nutzen Sie Event Grid z.B., um Azure Automation zu benachrichtigen, wenn ein virtueller Computer oder eine SQL-Datenbankinstanz erstellt wird. Verwenden Sie diese Ereignisse, um automatisch die Kompatibilität von Dienstkonfigurationen zu überprüfen, Metadaten in Betriebstools einzulesen, virtuelle Computer zu kennzeichnen oder Arbeitselemente abzulegen.

### <a name="application-integration"></a>Anwendungsintegration

![Anwendungsintegration in Azure](./media/overview/app_integration.png)

Event Grid verknüpft Ihre App mit anderen Diensten. Sie können z.B. ein benutzerdefiniertes Thema erstellen, um die Ereignisdaten Ihrer App an Event Grid zu senden und von der zuverlässigen Übermittlung, den erweiterten Routingfunktionen und der direkten Integration in Azure zu profitieren. Alternativ können Sie Event Grid mit Logic Apps verwenden, um Daten überall verarbeiten zu können, ohne Code schreiben zu müssen. 

## <a name="how-much-does-event-grid-cost"></a>Was kostet Event Grid?

Das Preismodell von Azure Event Grid orientiert sich an einer Zahlung pro Ereignis, sodass Sie nur für die tatsächliche Verwendung zahlen. Die ersten 100.000 Vorgänge pro Monat sind kostenlos. Vorgänge sind als Ereigniseingang, Abonnementübermittlungsversuche, Verwaltungsaufrufe und Filterung nach Betreffsuffix definiert. Details finden Sie auf der Seite mit der [Preisübersicht](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Nächste Schritte

* [Routen von Storage Blob-Ereignissen](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Reagieren Sie auf Storage Blob-Ereignisse mithilfe von Event Grid.
* [Erstellen und Abonnieren von benutzerdefinierten Ereignissen](custom-event-quickstart.md)  
  Starten Sie sofort, und senden Sie Ihre eigenen benutzerdefinierten Ereignisse mithilfe des Azure Event Grid-Schnellstarts an einen beliebigen Endpunkt.
* [Verwenden von Logic Apps als Ereignishandler](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Ein Tutorial zum Erstellen einer App mit Logic Apps, um auf Ereignisse zu reagieren, die von Event Grid mithilfe von Push übertragen werden.
* [Streamen von Big Data in ein Data Warehouse](event-grid-event-hubs-integration.md)  
  Ein Tutorial, das mithilfe von Azure Functions Daten von Event Hubs in SQL Data Warehouse streamt.
* [Event Grid-REST-API-Referenz](/rest/api/eventgrid)  
  Enthält Referenzinformationen für die Verwaltung von Ereignisabonnements, Routing und Filterung.
