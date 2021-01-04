---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028416"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

[Consumergruppen](../articles/event-hubs/event-hubs-features.md#event-consumers) bieten unabhängige Ansichten des Ereignisstreams, die es Apps und Azure-Diensten ermöglichen, unabhängig voneinander Daten desselben Event Hub-Endpunkts zu nutzen. In diesem Abschnitt fügen Sie dem integrierten Endpunkt Ihres IoT-Hubs eine Consumergruppe hinzu, die später in diesem Tutorial zum Pullen von Daten vom Endpunkt verwendet wird.

Gehen Sie folgendermaßen vor, um Ihrem IoT Hub eine Consumergruppe hinzuzufügen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihren IoT-Hub.

2. Wählen Sie im linken Bereich **Integrierte Endpunkte** aus. Wählen Sie anschließend im rechten Bereich **Ereignisse** aus, und geben Sie unter **Consumergruppen** einen Namen ein. Wählen Sie **Speichern** aus.

   ![Erstellen einer Consumergruppe in Ihrem IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)