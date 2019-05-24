---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 57a5de4c590ff98429aa1d4a0b96cebed6084a0f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146426"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

Consumergruppen werden von Anwendungen verwendet, um Daten aus Azure IoT Hub abzurufen. In diesem Tutorial erstellen Sie eine Consumergruppe, die von einem zukünftigen Azure-Dienst zum Lesen von Daten in Ihrem IoT Hub verwendet wird.

Gehen Sie folgendermaßen vor, um Ihrem IoT Hub eine Consumergruppe hinzuzufügen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihren IoT-Hub.

2. Klicken Sie im linken Bereich auf **Integrierte Endpunkte**. Wählen Sie anschließend im oberen Bereich **Ereignisse** aus, und geben Sie im rechten Bereich unter **Consumergruppen** einen Namen ein. Klicken Sie nach dem Ändern des Werts von **Standard-TTL** auf **Speichern**, und setzen Sie „Standard-TTL“ auf den ursprünglichen Wert zurück.

   ![Erstellen einer Consumergruppe in Ihrem IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
