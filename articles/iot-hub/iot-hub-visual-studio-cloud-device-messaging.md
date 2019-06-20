---
title: Verwalten von Azure IoT Hub-Cloudgerätemessaging mit Cloud-Explorer für Visual Studio | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mit Cloud-Explorer für Visual Studio in Azure IoT Hub Gerät-zu-Cloud-Nachrichten überwachen und Cloud-zu-Gerät-Nachrichten senden.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440630"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Senden und Empfangen von Nachrichten zwischen Ihrem Gerät und IoT Hub mithilfe von Cloud-Explorer für Visual Studio

![Lückenloses Diagramm](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud-Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) ist eine nützliche Erweiterung für Visual Studio, mit der Sie Ihre Azure-Ressourcen anzeigen, deren Eigenschaften überprüfen und wichtige Entwickleraktionen in Visual Studio ausführen können. Der Schwerpunkt dieses Artikels liegt auf dem Verwenden von Cloud-Explorer zum Senden und Empfangen von Nachrichten zwischen Ihrem Gerät und IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Lernziele

Sie erfahren, wie Sie mit Cloud-Explorer für Visual Studio Gerät-zu-Cloud-Nachrichten (Device to Cloud, D2C) überwachen und Cloud-zu-Gerät-Nachrichten (Cloud to Device, C2D) senden. Gerät-zu-Cloud-Nachrichten können Sensordaten sein, die Ihr Gerät erfasst und dann an Ihren IoT Hub sendet. Cloud-zu-Gerät-Nachrichten können Befehle sein, die Ihr IoT Hub an Ihr Gerät sendet. Zum Beispiel um eine LED blinken zu lassen, die mit Ihrem Gerät verbunden ist.

## <a name="what-you-will-do"></a>Aufgaben

- Überwachen Sie Gerät-zu-Cloud-Nachrichten mithilfe von Cloud-Explorer für Visual Studio.
- Überwachen Sie Gerät-zu-Cloud-Nachrichten mithilfe von Cloud-Explorer für Visual Studio.

## <a name="what-you-need"></a>Voraussetzungen

- Ein aktives Azure-Abonnement.
- Eine Azure IoT Hub-Instanz in Ihrem Abonnement.
- Microsoft Visual Studio 2017 Update 8 oder höher
- Cloud-Explorer-Komponente von Visual Studio-Installer (standardmäßig mit Azure-Workload ausgewählt)

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualisieren von Cloud-Explorer auf die neueste Version

Die Cloud-Explorer-Komponente von Visual Studio-Installer unterstützt nur die Überwachung von Gerät-zu-Cloud und Cloud-zu-Gerät-Nachrichten. Um Nachrichten an das Gerät oder die Cloud zu senden, laden Sie den neuesten [Cloud-Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) herunter und installieren ihn.

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

1. Klicken Sie im Fenster **Cloud-Explorer** in Visual Studio auf das Symbol „Kontoverwaltung“. Sie können das Cloud-Explorer-Fenster über das Menü **Ansicht** > **Cloud-Explorer** öffnen.

    ![Klicken Sie auf „Kontoverwaltung“.](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Klicken Sie in Cloud-Explorer auf **Konten verwalten**.

3. Klicken Sie auf **Konto hinzufügen...** im neuen Fenster, um sich zum ersten Mal bei Azure anzumelden.

4. Nach der Anmeldung wird Ihre Liste der Azure-Abonnements angezeigt. Wählen Sie das anzuzeigende Azure-Abonnement aus, und klicken Sie auf **Anwenden**.

5. Erweitern Sie **Ihr Abonnement** > **IoT Hubs** > **Ihr IoT Hub**. Ihre Geräteliste wird unter Ihrem IoT Hub-Knoten angezeigt.

    ![Geräteliste](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Überwachen von Gerät-zu-Cloud-Nachrichten

Um Nachrichten zu überwachen, die von Ihrem Gerät an Ihren IoT Hub gesendet werden, gehen Sie folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste auf Ihren IoT Hub oder Ihr Gerät, und klicken Sie auf **Überwachung von D2C-Nachrichten starten**.

    ![Starten der Überwachung von D2C-Nachrichten](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. Die überwachten Nachrichten werden im Ausgabebereich **IoT Hub** angezeigt.

    ![Überwachung von D2C-Nachrichten – Ergebnis](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Um die Überwachung anzuhalten, klicken Sie mit der rechten Maustaste auf einen IoT Hub oder ein Gerät, und wählen Sie **Überwachung von D2C-Nachrichten beenden** aus.

## <a name="send-cloud-to-device-messages"></a>Senden von C2D-Nachrichten.

Um eine Nachricht von Ihrem IoT Hub zu Ihrem Gerät zu senden, gehen Sie folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und klicken Sie auf **C2D-Nachricht senden**.

    ![Senden einer C2D-Nachricht](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Geben Sie die Nachricht in das Eingabefeld ein.

3. Die Ergebnisse werden im Ausgabebereich **IoT Hub** angezeigt.

    ![Senden einer C2D-Nachricht – Ergebnis](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, Gerät-zu-Cloud-Nachrichten zu überwachen und Cloud-zu-Gerät-Nachrichten zwischen dem IoT-Gerät und Azure IoT Hub zu senden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]