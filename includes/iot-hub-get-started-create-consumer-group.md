---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66249072"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

[Consumergruppen](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) bieten unabhängige Ansichten des Ereignisstreams, die es Apps und Azure-Diensten ermöglichen, unabhängig voneinander Daten desselben Event Hub-Endpunkts zu nutzen. In diesem Abschnitt fügen Sie dem integrierten Endpunkt Ihres IoT-Hubs eine Consumergruppe hinzu, die später in diesem Tutorial zum Pullen von Daten vom Endpunkt verwendet wird.

Gehen Sie folgendermaßen vor, um Ihrem IoT Hub eine Consumergruppe hinzuzufügen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihren IoT-Hub.

2. Wählen Sie im linken Bereich **Integrierte Endpunkte** aus. Wählen Sie anschließend im rechten Bereich **Ereignisse** aus, und geben Sie unter **Consumergruppen** einen Namen ein. Wählen Sie **Speichern** aus.

   ![Erstellen einer Consumergruppe in Ihrem IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
