---
title: include file
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509433"
---
## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein [Azure-Abonnement](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

## <a name="create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespace
Befolgen Sie die Anleitungen in diesem Tutorial: [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md), um folgende Aufgaben durchzuführen:

- Erstellen eines Service Bus **Premium**-Namespace. 
- Abrufen der Verbindungszeichenfolge. 
- Erstellen eines Service Bus-Themas.
- Erstellen eines Abonnements für das Thema. In diesem Tutorial wird nur ein einzelnes Abonnement benötigt. Die Abonnements S2 und S3 müssen also nicht erstellt werden. 

## <a name="send-messages-to-the-service-bus-topic"></a>Senden von Nachricht an das Service Bus-Thema
In diesem Schritt wird eine Beispielanwendung verwendet, um Nachrichten an das Service Bus-Thema zu senden, das Sie im vorherigen Schritt erstellt haben. 

1. Klonen Sie das [GitHub-Repository „azure-service-bus“](https://github.com/Azure/azure-service-bus/).
2. Navigieren Sie in Visual Studio zum Ordner *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration*, und öffnen Sie die Datei *SBEventGridIntegration.sln*.
3. Erweitern Sie im Fenster des Projektmappen-Explorers das Projekt **MessageSender**, und wählen Sie **Program.cs** aus.
4. Ersetzen Sie `<SERVICE BUS NAMESPACE - CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace und `<TOPIC NAME>` durch den Namen des Themas. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Erstellen Sie das Programm, und führen Sie es aus, um fünf Testnachrichten (`const int numberOfMessages = 5;`) an das Service Bus-Thema zu senden. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Ausgabe der Konsolen-App":::
