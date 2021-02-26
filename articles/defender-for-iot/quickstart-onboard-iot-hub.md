---
title: Durchführen des Onboardings für eine Agent-basierte Lösung für Azure Defender für IoT
description: Hier erfahren Sie, wie Sie in Ihrer Azure IoT Hub-Instanz das Onboarding für den Sicherheitsdienst „Defender für IoT“ durchführen und den Dienst aktivieren.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809132"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Durchführen des Onboardings für eine Agent-basierte Lösung für Azure Defender für IoT

In diesem Artikel erfahren Sie, wie Sie den Dienst „Defender für IoT“ in Ihrer IoT Hub-Instanz aktivieren. Besitzen Sie derzeit keine IoT Hub-Instanz, lesen Sie zum Einstieg die Informationen unter [Erstellen eines IoT Hubs über das Portal](../iot-hub/iot-hub-create-through-portal.md).

Sie können Ihre IoT-Sicherheit über die IoT Hub-Instanz in Defender für IoT verwalten. Über das Verwaltungsportal in der IoT Hub-Instanz können Sie folgende Aufgaben ausführen: 

- Verwalten der IoT Hub-Sicherheit

- Grundlegende Verwaltung der Sicherheit eines IoT-Geräts ohne Installation eines Agents basierend auf der IoT Hub-Telemetrie 

- Erweiterte Verwaltung für die Sicherheit eines IoT-Geräts basierend auf dem Micro-Agent

> [!NOTE]
> Von Defender für IoT werden derzeit nur IoT Hub-Instanzen im Standard-Tarif unterstützt.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Durchführen des Onboardings für Defender für IoT in IoT Hub

Für alle neuen IoT-Hubs ist Defender für IoT standardmäßig auf **Ein** festgelegt. Sie können überprüfen, ob Defender für IoT während des IoT Hub Erstellungsvorgangs auf **Ein** festgelegt wird.

So überprüfen Sie, ob die Umschaltfläche auf **Ein** festgelegt ist:

1. Navigieren Sie zum Azure-Portal.

1. Wählen Sie in der Liste der Azure-Dienste **IoT Hub** aus.

1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Auswählen der Schaltfläche „Erstellen“ auf der oberen Symbolleiste" lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Wählen Sie die Registerkarte **Verwaltung** aus, und überprüfen Sie, ob die Umschaltfläche **Defender für IoT** auf **Ein** festgelegt ist.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Sicherstellen, dass die Umschaltfläche „Defender für IoT“ auf „Ein“ festgelegt ist":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Durchführen des Onboardings für Defender für IoT in eine vorhandene IoT Hub-Instanz

Sie können die Identitätsverwaltung Ihres Geräts und die Kommunikationsmuster zwischen Gerät und Cloud sowie zwischen Cloud und Gerät überwachen. Führen Sie dazu die folgenden Schritte zum Starten des Diensts aus: 

1. Navigieren Sie zu IoT Hub. 

1. Wählen Sie das Menü  **Sicherheitsübersicht** aus. 

1. Klicken Sie auf „Schützen Ihrer IoT-Lösung“, und füllen Sie das Formular zum Onboarding aus. 


## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Ihre Lösung konfigurieren:

> [!div class="nextstepaction"]
> [Erstellen eines IoT-Micro-Agent-Modulzwillings für Defender (Vorschau)](quickstart-create-micro-agent-module-twin.md)
