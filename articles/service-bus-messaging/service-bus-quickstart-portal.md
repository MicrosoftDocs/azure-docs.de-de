---
title: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit dem Azure-Portal einen Service Bus-Namespace und darin eine Warteschlange erstellen.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95799226"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Verwenden des Azure-Portals zum Erstellen eines Service Bus-Namespace und einer Warteschlange
In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit dem [Azure-Portal][Azure portal] einen Service Bus-Namespace und eine Warteschlange erstellen. Darüber hinaus wird beschrieben, wie Sie Anmeldeinformationen für die Autorisierung abrufen, die von einer Clientanwendung zum Senden bzw. Empfangen von Nachrichten für eine Warteschlange genutzt werden können. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][] erstellen, bevor Sie beginnen.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie einen Service Bus-Namespace und darin dann eine Warteschlange erstellt. Informationen zum Senden bzw. Empfangen von Nachrichten für die Warteschlange finden Sie in einer der folgenden Schnellstartanleitungen im Abschnitt **Senden und Empfangen von Nachrichten**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[kostenloses Konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
