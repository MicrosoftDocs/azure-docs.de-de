---
title: include file
description: include file
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022138"
---
Die Option AMQP-over-WebSockets-Protokoll wird über TCP-Port 443 wie die HTTP-/REST-API ausgeführt, ist aber ansonsten funktionell identisch mit einfachem AMQP. Diese Option weist eine etwas höhere anfängliche Verbindungslatenz auf, weil zusätzliche Handshakeroundtrips auftreten und der Mehraufwand als Kompromisse bei der gemeinsamen Verwendung des HTTPS-Ports geringfügig größer ist. Wenn dieser Modus ausgewählt ist, ist TCP-Port 443 für die Kommunikation ausreichend. Mit den folgenden Optionen können Sie den einfachen AMQP- oder den AMQP-WebSockets-Modus auswählen:

| Sprache | Option   |
| -------- | ----- |
| .NET     | [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet)-Eigenschaft mit [TransportType.Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) oder [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) mit [com.microsoft.azure.servicebus.primitives.TransportType.AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) oder [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Node  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) verfügt über ein `webSocket`-Konstruktorargument. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) mit [TransportType.Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) oder [TransportType.AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |