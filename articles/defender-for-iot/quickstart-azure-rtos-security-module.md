---
title: 'Schnellstart: Konfigurieren und Aktivieren des Sicherheitsmoduls für Azure RTOS'
description: Hier erfahren Sie, wie Sie das Sicherheitsmodul für den Azure RTOS-Dienst in Ihren Azure IoT Hub integrieren und darin aktivieren.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2020
ms.author: rkarlin
ms.openlocfilehash: 321c8d2b9e58aba943c5bf19adf54d6359c5be96
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351775"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Schnellstart: Sicherheitsmodul für Azure RTOS (Vorschau)

Dieser Artikel enthält eine Erläuterung der Voraussetzungen vor den ersten Schritten, und zwar wird darin erläutert, wie Sie das Sicherheitsmodul für den Azure RTOS-Dienst auf einer IoT Hub-Instanz aktivieren. Besitzen Sie derzeit keine IoT Hub-Instanz, lesen Sie zum Einstieg die Informationen unter [Erstellen eines IoT Hubs über das Portal](../iot-hub/iot-hub-create-through-portal.md).

> [!NOTE]
> Das Sicherheitsmodul für Azure RTOS wird nur in IoT Hubs des Standardtarifs unterstützt.

## <a name="prerequisites"></a>Voraussetzungen 

### <a name="supported-devices"></a>Unterstützte Geräte

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Mikrochip SAM E54 Xplained Pro EVK

Laden Sie eine der ZIP-Dateien für das jeweilige Board und Tool (IAR, die semi-IDE oder den PC) Ihrer Wahl aus der [GitHub-Ressource des Sicherheitsmoduls für Azure RTOS](https://github.com/azure-rtos/azure-iot-preview/releases) herunter, kompilieren Sie sie, und führen Sie sie aus.

### <a name="azure-resources"></a>Azure-Ressourcen

Die nächste Phase bei den ersten Schritten ist die Vorbereitung Ihrer Azure-Ressourcen. Sie benötigen einen IoT Hub, und wir empfehlen einen Log Analytics-Arbeitsbereich. Für IoT Hub benötigen Sie Ihre IoT Hub-Verbindungszeichenfolge, um eine Verbindung mit Ihrem Gerät herzustellen. 
  
### <a name="iot-hub-connection"></a>IoT Hub-Verbindung

Für die ersten Schritte ist eine IoT Hub-Verbindung ist erforderlich. 

1. Öffnen Sie im Azure-Portal Ihre Instanz von **IoT Hub**.
1. Kopieren Sie die IoT-Verbindungszeichenfolge in die [Konfigurationsdatei](how-to-azure-rtos-security-module.md).


Die Verbindungsanmeldeinformationen werden aus der Benutzeranwendungskonfiguration **HOST_NAME**, **DEVICE_ID** und **DEVICE_SYMMETRIC_KEY** übernommen.

Das Sicherheitsmodul für Azure RTOS verwendet Azure IoT Middleware-Verbindungen basierend auf dem **MQTT**-Protokoll.


### <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich

Die Log Analytics-Datenerfassung in IoT Hub ist in der Defender für IoT-Lösung standardmäßig deaktiviert. Wenn Sie sie zum Arbeiten mit dem Sicherheitsmodul für Azure RTOS aktivieren möchten, führen Sie die folgenden Schritte aus: 
1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub.
1. Wählen Sie im Menü **Sicherheit** die Option **Einstellungen** aus.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Option „Access Data Collection“ (Zugreifen auf Datensammlung) für Azure RTOS"::: 
1. Klicken Sie auf **Datensammlung**. 
1. Legen Sie in der Option **Arbeitsbereichskonfiguration** den Umschalter auf **Ein** fest. 
1. Erstellen Sie einen neuen Log Analytics-Arbeitsbereich, oder fügen Sie einen bereits vorhandenen an. Stellen Sie sicher, dass die Option **Access raw security data (Zugreifen auf Sicherheitsrohdaten)** ausgewählt ist. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Azure RTOS-Konfiguration mit den zwei ausgewählten Optionen „Datensammlung“ und „Sicherheitsrohdaten“":::
1. Wählen Sie **Speichern** aus.
1. Kehren Sie zur Liste Ihrer Azure-Ressourcen zurück, und überprüfen Sie, ob der von Ihnen erstellte und angefügte Log Analytics-Arbeitsbereich für den IoT Hub als aktiviert angezeigt wird.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="In der Liste Ihrer Azure-Ressourcen überprüfen, ob der richtige Log Analytics-Arbeitsbereich für eine IoT Hub-Instanz hinzugefügt wurde"::: 

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den nächsten Artikel, um die Konfiguration und Anpassung Ihrer Lösung abschließen zu können.

> [!div class="nextstepaction"]
> [Konfigurieren des Sicherheitsmoduls für Azure RTOS](how-to-azure-rtos-security-module.md)