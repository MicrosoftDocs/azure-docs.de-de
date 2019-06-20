---
title: Verwalten von Azure IoT Hub-Cloudgerätemessaging mit Azure IoT-Tools für Visual Studio Code | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mit der Azure IoT-Tools für Visual Studio Code in Azure IoT Hub Gerät-zu-Cloud-Nachrichten überwachen und Cloud-zu-Gerät-Nachrichten senden.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/18/2019
ms.author: junhan
ms.openlocfilehash: 5b74524a05317cf22160561a4a001e88f9215953
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440069"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Senden und Empfangen von Nachrichten zwischen Ihrem Gerät und IoT Hub mithilfe der Azure IoT-Tools für Visual Studio Code.

![Lückenloses Diagramm](./media/iot-hub-vscode-iot-toolkit-cloud-device-messaging/e-to-e-diagram.png)

Die [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) sind eine nützliche Visual Studio Code-Erweiterung, die die IoT Hub-Verwaltung und die IoT-Anwendungsentwicklung vereinfachen. In diesem Artikel wird beschrieben, wie Nachrichten zwischen Ihrem Gerät und IoT Hub mithilfe der Azure IoT-Tools für Visual Studio Code gesendet und empfangen werden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Lernziele

Sie erfahren, wie Sie mit den Azure IoT-Tools für Visual Studio Code Gerät-zu-Cloud-Nachrichten (Device to Cloud, D2C) überwachen und Cloud-zu-Gerät-Nachrichten (Cloud to Device, C2D) senden. Gerät-zu-Cloud-Nachrichten können Sensordaten sein, die Ihr Gerät erfasst und dann an Ihren IoT Hub sendet. Cloud-zu-Gerät-Nachrichten können Befehle sein, die Ihr IoT Hub an Ihr Gerät sendet, um eine LED blinken zu lassen, die mit Ihrem Gerät verbunden ist.

## <a name="what-you-will-do"></a>Aufgaben

* Überwachen Sie Gerät-zu-Cloud-Nachrichten mithilfe der Azure IoT-Tools für Visual Studio Code.

* Senden Sie Cloud-zu-Gerät-Nachrichten mithilfe der Azure IoT-Tools für Visual Studio Code.

## <a name="what-you-need"></a>Voraussetzungen

* Ein aktives Azure-Abonnement.

* Ein Azure IoT Hub in Ihrem Abonnement.

* [Visual Studio Code](https://code.visualstudio.com/)

* [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), oder [öffnen Sie diesen Link in Visual Studio Code](vscode:extension/vsciot-vscode.azure-iot-tools).

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

1. Erweitern Sie in der Ansicht **Explorer** von VS Code in der unteren linken Ecke den Abschnitt **Azure IoT Hub-Geräte**.

2. Klicken Sie im Kontextmenü auf **IoT Hub auswählen**.

3. In der unteren rechten Ecke wird ein Popupfenster angezeigt, mit der Sie sich zum ersten Mal bei Azure anmelden können.

4. Nachdem Sie sich angemeldet haben, wird Ihre Liste der Azure-Abonnements angezeigt. Wählen Sie dann „Azure-Abonnement“ und „IoT Hub“ aus.

5. In der Geräteliste wird nach wenigen Sekunden die Registerkarte **Azure IoT Hub-Geräte** angezeigt.

   > [!Note]
   > Sie können die Einrichtung auch abschließen, indem Sie auf **Set IoT Hub Connection String** (IoT Hub-Verbindungszeichenfolge festlegen) klicken. Geben Sie in das Popupfenster die Verbindungszeichenfolge für den IoT Hub ein, mit dem Ihr IoT-Gerät eine Verbindung herstellt.

## <a name="monitor-device-to-cloud-messages"></a>Überwachen von Gerät-zu-Cloud-Nachrichten

Um Nachrichten zu überwachen, die von Ihrem Gerät an Ihren IoT Hub gesendet werden, gehen Sie folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und klicken Sie auf **Überwachung von D2C-Nachrichten starten**.

2. Die überwachten Nachrichten werden in der Ansicht **AUSGABE** > **Azure IoT Hub Toolkit** angezeigt.

3. Um die Überwachung anzuhalten, klicken Sie mit der rechten Maustaste auf die Ansicht **AUSGABE**, und klicken Sie auf **Überwachung von D2C-Nachrichten beenden**.

## <a name="send-cloud-to-device-messages"></a>Senden von C2D-Nachrichten.

Um eine Nachricht von Ihrem IoT Hub zu Ihrem Gerät zu senden, gehen Sie folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und klicken Sie auf **C2D-Nachricht an Gerät senden**.

2. Geben Sie die Nachricht in das Eingabefeld ein.

3. Die Ergebnisse werden in der Ansicht **AUSGABE** > **Azure IoT Hub Toolkit** angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, Gerät-zu-Cloud-Nachrichten zu überwachen und Cloud-zu-Gerät-Nachrichten zwischen dem IoT-Gerät und Azure IoT Hub zu senden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]