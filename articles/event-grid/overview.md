---
title: Was ist Azure Event Grid?
description: Senden Sie Ereignisdaten mit Azure Event Grid aus einer Quelle an Handler. Erstellen Sie ereignisbasierte Anwendungen, und integrieren Sie sie in Azure-Dienste.
ms.topic: overview
ms.date: 01/28/2021
ms.openlocfilehash: e53665c88c3860d37b3512c6498ab626b02a6400
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055132"
---
# <a name="what-is-azure-event-grid"></a>Was ist Azure Event Grid?

Mit Azure Event Grid können Sie mühelos Anwendungen mit ereignisbasierten Architekturen erstellen. Wählen Sie zuerst die Azure-Ressource aus, die Sie abonnieren möchten, und geben Sie dann den Ereignishandler oder WebHook-Endpunkt an, an den das Ereignis gesendet werden soll. Event Grid verfügt über integrierte Unterstützung für Ereignisse, die aus Azure-Diensten stammen, wie Speicherblobs und Ressourcengruppen. Event Grid bietet auch Unterstützung für Ihre eigenen Ereignisse mit benutzerdefinierten Themen. 

Sie können Filter zum Weiterleiten bestimmter Ereignisse an verschiedene Endpunkte verwenden, Multicasts zu mehreren Endpunkten ausführen und sicherstellen, dass Ihre Ereignisse zuverlässig übermittelt werden.

Azure Event Grid wird zum Maximieren der Verfügbarkeit durch eine native Verteilung auf mehrere Fehlerdomänen in jeder Region sowie auf Verfügbarkeitszonen (in Regionen, in denen diese unterstützt werden) bereitgestellt. Eine Liste der Regionen, die von Event Grid unterstützt werden, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Dieser Artikel enthält eine Übersicht zu Azure Event Grid. Wenn Sie in die Verwendung von Event Grid einsteigen möchten, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="Event Grid-Modell von Quellen und Handlern" lightbox="./media/overview/functional-model-big.png":::

Diese Abbildung zeigt, wie Event Grid Quellen und Handler verbindet, stellt jedoch keine vollständige Liste der unterstützten Integrationen dar.

## <a name="event-sources"></a>Ereignisquellen

Derzeit unterstützen die folgenden Azure-Dienste das Senden von Ereignissen an Event Grid. Weitere Informationen zu einer Quelle in der Liste erhalten Sie, indem Sie den Link auswählen.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure-Ressourcengruppen](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure-Abonnements](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="event-handlers"></a>Ereignishandler

Ausführliche Informationen zu den Funktionen der einzelnen Handler sowie entsprechende Artikel finden Sie unter [Ereignishandler](event-handlers.md). Derzeit unterstützen die folgenden Azure-Dienste das Verarbeiten von Ereignissen von Event Grid: 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure-Funktionen](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Relay-Hybridverbindungen](handler-relay-hybrid-connections.md)
* [Logik-Apps](handler-webhooks.md#logic-apps)
* [Power Automate (ehemals „Microsoft Flow“)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [Webhooks](handler-webhooks.md)

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
* **Hoher Durchsatz**: Erstellen Sie in Event Grid Workloads mit hohem Volumen.
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

Mit Event Grid können Sie die Automatisierung beschleunigen und die Richtlinienerzwingung vereinfachen. Nutzen Sie Event Grid z. B., um Azure Automation zu benachrichtigen, wenn ein virtueller Computer oder eine Datenbank in Azure SQL erstellt wird. Verwenden Sie diese Ereignisse, um automatisch die Kompatibilität von Dienstkonfigurationen zu überprüfen, Metadaten in Betriebstools einzulesen, virtuelle Computer zu kennzeichnen oder Arbeitselemente abzulegen.

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
  Ein Tutorial, das mithilfe von Azure Functions Daten aus Event Hubs in Azure Synapse Analytics streamt.
* [Event Grid-REST-API-Referenz](/rest/api/eventgrid)  
  Enthält Referenzinformationen für die Verwaltung von Ereignisabonnements, Routing und Filterung.