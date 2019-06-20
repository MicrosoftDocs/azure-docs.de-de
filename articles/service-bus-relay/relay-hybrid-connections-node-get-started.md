---
title: Erste Schritte mit WebSockets von Azure Relay Hybrid Connections in Node | Microsoft-Dokumentation
description: Schreiben einer Node.js-Konsolenanwendung für WebSockets von Azure Relay Hybrid Connections
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: b4864673e25ba4f5a1f2e8629e0889863051bc07
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60553880"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Erste Schritte mit WebSockets von Relay Hybrid Connections in Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In dieser Schnellstartanleitung erstellen Sie Sender- und Empfängeranwendungen in Node.js, die mithilfe von Hybrid Connections WebSockets in Azure Relay Nachrichten senden und empfangen. Allgemeine Informationen zu Azure Relay finden Sie unter [Was ist Azure Relay?](relay-what-is-it.md). 

Diese Schnellstartanleitung umfasst folgende Schritte: 

1. Erstellen eines Relay-Namespace über das Azure-Portal
2. Erstellen einer Hybridverbindung in diesem Namespace über das Azure-Portal
3. Erstellen einer Serverkonsolenanwendung (Listener) zum Empfangen von Nachrichten
4. Erstellen einer Clientkonsolenanwendung (Absender) zum Senden von Nachrichten
5. Ausführen von Anwendungen 

## <a name="prerequisites"></a>Voraussetzungen

- [Node.js](https://nodejs.org/en/).
- Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="create-a-namespace"></a>Erstellen eines Namespace
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Hybridverbindung erstellen
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Erstellen einer Serveranwendung (Listener)
Schreiben Sie eine Node.js-Konsolenanwendung, um auf Nachrichten des Relays zu lauschen und sie zu empfangen.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Erstellen einer Clientanwendung (Absender)
Schreiben Sie eine Node.js-Konsolenanwendung, um Nachrichten an das Relay zu senden.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Ausführen der Anwendungen

1. Führen Sie die Serveranwendung aus: Geben Sie in einer Node.js-Eingabeaufforderung `node listener.js` ein.
2. Führen Sie die Clientanwendung aus: Geben Sie in einer Node.js-Eingabeaufforderung `node sender.js` und einen beliebigen Text ein.
3. Stellen Sie sicher, dass von der Konsole der Serveranwendung der Text ausgegeben wird, der in die Clientanwendung eingegeben wurde.

    ![Ausführen von Anwendungen](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Glückwunsch! Sie haben mithilfe von Node.js eine Anwendung für End-to-End-Hybridverbindungen erstellt.

## <a name="next-steps"></a>Nächste Schritte
In dieser Schnellstartanleitung haben Sie Client- und Serveranwendungen in Node.js erstellt, die mithilfe von WebSockets Nachrichten senden und empfangen. Das Hybrid Connections-Feature von Azure Relay unterstützt auch die Verwendung von HTTP zum Senden und Empfangen von Nachrichten. Informationen zur Verwendung von HTTP mit Hybrid Connections von Azure Relay finden Sie unter [Erste Schritte mit HTTP-Anforderungen von Relay Hybrid Connections in Node](relay-hybrid-connections-http-requests-node-get-started.md).

In dieser Schnellstartanleitung haben Sie Node.js zum Erstellen von Client- und Serveranwendungen verwendet. Informationen zum Schreiben von Client- und Serveranwendungen mithilfe von .NET Framework finden Sie unter [Erste Schritte mit WebSockets von Relay Hybrid Connections in .NET](relay-hybrid-connections-dotnet-get-started.md) oder [Erste Schritte mit WebSockets von Relay Hybrid Connections in Node](relay-hybrid-connections-http-requests-dotnet-get-started.md).


