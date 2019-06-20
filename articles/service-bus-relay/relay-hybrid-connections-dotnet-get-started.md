---
title: Erste Schritte mit WebSockets von Azure Relay Hybrid Connections in .NET | Microsoft-Dokumentation
description: Schreiben Sie eine C#-Konsolenanwendung für WebSockets von Azure Relay Hybrid Connections.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 6ad1d5415feefcf30ebae860bc8f4d8a3e2261d5
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428352"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Erste Schritte mit WebSockets von Relay Hybrid Connections in .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In dieser Schnellstartanleitung erstellen Sie Sender- und Empfängeranwendungen in .NET, die mithilfe von Hybrid Connections WebSockets in Azure Relay Nachrichten senden und empfangen. Allgemeine Informationen zu Azure Relay finden Sie unter [Was ist Azure Relay?](relay-what-is-it.md). 

Diese Schnellstartanleitung umfasst folgende Schritte:

1. Erstellen eines Relay-Namespace über das Azure-Portal
2. Erstellen einer Hybridverbindung in diesem Namespace über das Azure-Portal
3. Erstellen einer Serverkonsolenanwendung (Listener) zum Empfangen von Nachrichten
4. Erstellen einer Clientkonsolenanwendung (Absender) zum Senden von Nachrichten
5. Ausführen von Anwendungen 

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

* [Visual Studio 2015 oder eine höhere Version.](https://www.visualstudio.com) Für die Beispiele in diesem Tutorial wird Visual Studio 2017 verwendet.
* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="create-a-namespace"></a>Erstellen eines Namespace
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Hybridverbindung erstellen
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Erstellen einer Serveranwendung (Listener)
Schreiben Sie in Visual Studio eine C#-Konsolenanwendung, um auf Nachrichten des Relays zu lauschen und sie zu empfangen.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Erstellen einer Clientanwendung (Absender)
Schreiben Sie in Visual Studio eine C#-Konsolenanwendung, um Nachrichten an das Relay zu senden.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Ausführen der Anwendungen
1. Führen Sie die Serveranwendung aus.
2. Führen Sie die Clientanwendung aus, und geben Sie Text ein.
3. Vergewissern Sie sich, dass in der Konsole der Serveranwendung der Text angezeigt wird, der in die Clientanwendung eingegeben wurde.

    ![Ausführen von Anwendungen](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Glückwunsch! Sie haben eine vollständige Hybrid Connections-Anwendung erstellt!

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie Client- und Serveranwendungen in .NET erstellt, die mithilfe von WebSockets Nachrichten senden und empfangen. Das Hybrid Connections-Feature von Azure Relay unterstützt auch die Verwendung von HTTP zum Senden und Empfangen von Nachrichten. Informationen zur Verwendung von HTTP mit Hybrid Connections von Azure Relay finden Sie unter [Erste Schritte mit HTTP-Anforderungen von Relay Hybrid Connections in .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).

In dieser Schnellstartanleitung haben Sie .NET Framework zum Erstellen von Client- und Serveranwendungen verwendet. Informationen zum Schreiben von Client- und Serveranwendungen mithilfe von Node.js finden Sie unter [Erste Schritte mit WebSockets von Relay Hybrid Connections in Node](relay-hybrid-connections-node-get-started.md) oder [Erste Schritte mit HTTP-Anforderungen von Relay Hybrid Connections in .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).

