---
title: Ausführliche Informationen zu Azure SignalR Service
description: Eine Übersicht der ausführlichen Informationen über den Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cbcdfccfdca1dbed3b766b3f50295b1d355b3478
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401751"
---
# <a name="azure-signalr-service-internals"></a>Ausführliche Informationen zu Azure SignalR Service

Azure SignalR Service basiert auf dem ASP.NET Core SignalR-Framework. Es unterstützt ASP.NET SignalR auch als Previewfunktion.

> Zur Unterstützung von ASP.NET SignalR implementiert Azure SignalR Service das Datenprotokoll von ASP.NET SignalR erneut über das ASP.NET Core-Framework

Sie können eine lokale ASP.NET Core SignalR-Anwendung problemlos migrieren, damit Sie mit SignalR Service funktioniert, indem Sie nur ein paar Zeilen Code ändern.

Das folgende Diagramm beschreibt die typische Architektur, wenn Sie den SignalR Service mit Ihrem Anwendungsserver verwenden.

Die Unterschiede zu einer selbst gehosteten ASP.NET Core SignalR-Anwendung werden ebenfalls erläutert.

![Architecture](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Serververbindungen

Ein selbst gehosteter ASP.NET Core SignalR-Anwendungsserver überwacht Clients direkt und stellt auch eine direkte Verbindung mit diesen her.

Mit SignalR Service akzeptiert der Anwendungsserver keine permanenten Clientverbindungen mehr, stattdessen:

1. Wird ein `negotiate`-Endpunkt von Azure SignalR Service-SDK für jeden Hub verfügbar gemacht.
1. Dieser Endpunkt reagiert auf die Aushandlungsanforderungen von Clients und leitet Clients an SignalR Service um.
1. Schließlich werden Clients mit SignalR Service verbunden.

Weitere Informationen finden Sie unter [Clientverbindungen](#client-connections).

Sobald der Anwendungsserver gestartet ist: 
- Für ASP.NET Core SignalR öffnet Azure SignalR Service-SDK 5 WebSocket-Verbindungen pro Hub mit SignalR Service. 
- Für ASP.NET SignalR öffnet Azure SignalR Service-SDK 5 WebSocket-Verbindungen pro Hub mit SignalR Service und eine WebSocket-Verbindung pro Anwendung.

5 WebSocket-Verbindungen ist der Standardwert, der in [Konfiguration](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount) geändert werden kann.

Meldungen an und von Clients werden in diesen Verbindungen per Multiplexing verarbeitet.

Diese Verbindungen mit dem SignalR Service bleiben immer bestehen. Wenn eine Serververbindung wegen Netzwerkproblemen getrennt wird,
- werden alle Clients, die von dieser Serververbindung versorgt werden, getrennt (Weitere Informationen dazu finden Sie unter [Datenübertragung zwischen Client und Server](#data-transmit-between-client-and-server));
- Die Serververbindung beginnt automatisch, die Verbindung neu herzustellen.

## <a name="client-connections"></a>Clientverbindungen

Wenn Sie den SignalR Service verwenden, verbinden sich Clients mit SignalR Service anstatt mit dem Anwendungsserver.
Das Einrichten von permanenten Verbindungen zwischen dem Client und dem SignalR Service besteht aus zwei Schritten.

1. Der Client sendet eine Aushandlungsanforderung an den Anwendungsserver. Mit Azure SignalR Service SDK gibt der Anwendungsserver eine Umleitungsantwort mit der URL und dem Zugriffstoken des SignalR Service zurück.

- Für ASP.NET Core SignalR sieht eine typische Umleitungsantwort wie folgt aus:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Für ASP.NET SignalR sieht eine typische Umleitungsantwort wie folgt aus:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Nach dem Empfang der Umleitungsantwort verwendet der Client die neue URL und das Zugriffstoken, um den normalen Prozess zum Verbinden mit SignalR Service zu starten.

Erfahren Sie mehr über die [Transportprotokolle](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md) von ASP.NET Core SignalR.

## <a name="data-transmit-between-client-and-server"></a>Datenübertragung zwischen Client und Server

Wenn ein Client mit dem SignalR Service verbunden ist, sucht die Dienstlaufzeit eine Serververbindung, um diesen Client zu bedienen.
- Dieser Schritt erfolgt nur einmal, und er stellt eine 1: 1-Zuordnung zwischen dem Client und Serververbindungen dar.
- Die Zuordnung wird SignalR Service aufrechterhalten, bis die Verbindung vom Client oder Server getrennt wird.

An diesem Punkt empfängt der Anwendungsserver ein Ereignis mit Informationen von dem neuen Client. Eine logische Verbindung mit dem Client wird im Anwendungsserver erstellt. Der Datenkanal wird vom Client zum Anwendungsserver über SignalR Service eingerichtet.

Der SignalR Service überträgt Daten vom Client an den gekoppelten Anwendungsserver. Und Daten werden vom Anwendungsserver an die zugeordneten Clients gesendet.

Wie Sie sehen können, ist der Azure SignalR Service im Wesentlichen eine logische Transportschicht zwischen Anwendungsserver und Clients. Alle permanente Verbindungen werden auf SignalR Service ausgelagert.
Der Anwendungsserver muss nur die Geschäftslogik in der Hub-Klasse verarbeiten, ohne sich dabei um Clientverbindungen kümmern zu müssen.