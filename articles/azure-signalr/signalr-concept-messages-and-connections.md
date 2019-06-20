---
title: Nachrichten und Verbindungen in Azure SignalR Service
description: Eine Übersicht über die Hauptkonzepte im Zusammenhang mit Nachrichten und Verbindungen in Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401201"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Nachrichten und Verbindungen in Azure SignalR Service

Das Abrechnungsmodell für Azure SignalR Service basiert auf der Anzahl von Verbindungen und Nachrichten. In diesem Artikel erfahren Sie, wie Nachrichten und Verbindungen definiert und für Abrechnungszwecke gezählt werden.


## <a name="message-formats"></a>Nachrichtenformate 

Azure SignalR Service unterstützt die gleichen Formate wie ASP.NET Core SignalR: [JSON](https://www.json.org/) und [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Nachrichtengröße

Die Nachrichtengröße ist bei Azure SignalR Service nicht beschränkt.

Große Nachrichten werden in kleinere Nachrichten mit einer Größe von jeweils maximal 2 KB aufgeteilt und separat übertragen. Das Aufteilen und Zusammensetzen der Meldungen wird von SDKs erledigt. Hierfür ist kein Entwickleraufwand erforderlich.

Große Nachrichten wirken sich negativ auf die Leistung der Nachrichtenübermittlung aus. Verwenden Sie nach Möglichkeit kleinere Nachrichten, und bestimmen Sie anhand von Tests die optimale Nachrichtengröße für das jeweilige Szenario.

## <a name="how-messages-are-counted-for-billing"></a>Zählung von Nachrichten für die Abrechnung

Für die Abrechnung werden nur ausgehende Nachrichten von Azure SignalR Service gezählt. Pingnachrichten zwischen Clients und Servern werden ignoriert.

Nachrichten mit einer Größe von mehr als 2 KB werden als mehrere Nachrichten mit jeweils 2 KB gezählt. Das Diagramm mit der Nachrichtenanzahl im Azure-Portal wird hubspezifisch alle 100 Nachrichten aktualisiert.

Beispiel: Angenommen, Sie verfügen über drei Clients und einen Anwendungsserver. Ein Client sendet eine 4 KB große Nachricht, damit der Server einen Broadcast an alle Clients durchführt. In diesem Fall werden acht Nachrichten gezählt: eine Nachricht vom Dienst an den Anwendungsserver und drei Nachrichten vom Dienst an die Clients. Jede Nachricht wird als zwei Nachrichten mit jeweils 2 KB gezählt.

Im Azure-Portal werden weiterhin null Nachrichten angezeigt, bis mehr als 100 Nachrichten zusammengekommen sind.

## <a name="how-connections-are-counted"></a>Zählung von Verbindungen

Es gibt Serververbindungen und Clientverbindungen. Standardmäßig verfügt jeder Anwendungsserver pro Hub über fünf Verbindungen mit Azure SignalR Service, und jeder Client verfügt über eine Clientverbindung mit Azure SignalR Service.

Die im Azure-Portal angezeigte Verbindungsanzahl umfasst sowohl Server- als auch Clientverbindungen.

Ein Beispiel: Angenommen, Sie verfügen über zwei Anwendungsserver und definieren fünf Hubs im Code. Dadurch ergeben sich 50 Serververbindungen: 2 App-Server × 5 Hubs × 5 Verbindungen pro Hub

Bei ASP.NET SignalR werden Serververbindungen anders berechnet. In diesem Fall ist zusätzlich zu den definierten Hubs ein einzelner Standardhub enthalten. Jeder Anwendungsserver benötigt standardmäßig fünf weitere Serververbindungen. Die Verbindungsanzahl für den Standardhub bleibt mit der Anzahl der anderen Hubs konsistent.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Berücksichtigung von ein-/ausgehendem Datenverkehr

Die Unterscheidung zwischen eingehendem und ausgehendem Datenverkehr basiert auf der Perspektive von Azure SignalR Service. Datenverkehr wird in Bytes berechnet. Genau wie bei der Nachrichtenanzahl gilt auch beim Datenverkehr eine Samplingrate. Das Diagramm für ein-/ausgehenden Datenverkehr im Azure-Portal wird hubspezifisch alle 100 KB aktualisiert.

## <a name="related-resources"></a>Zugehörige Ressourcen

- [Unterstützte Metriken von Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-Konfiguration](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)