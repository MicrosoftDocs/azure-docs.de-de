---
title: Azure IoT-Geräteverwaltung mit Azure IoT-Tools für Visual Studio Code | Microsoft-Dokumentation
description: Verwenden Sie die Azure IoT-Tools für Visual Studio Code für die Geräteverwaltung mit Azure IoT Hub, indem direkte Methoden und gewünschte Eigenschaften von Gerätezwillingen verwendet werden.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 03df2ceb2df4d857e48f1790703a1d87647e43d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60401169"
---
# <a name="use-azure-iot-tools-for-visual-studio-code-for-azure-iot-hub-device-management"></a>Verwenden der Azure IoT-Tools für Visual Studio Code für die Geräteverwaltung mit Azure IoT Hub

![Lückenloses Diagramm](media/iot-hub-get-started-e2e-diagram/2.png)

Die [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) sind eine nützliche Visual Studio Code-Erweiterung, die die IoT Hub-Verwaltung und die IoT-Anwendungsentwicklung vereinfachen. Es bietet Verwaltungsoptionen, die Sie zum Ausführen verschiedener Aufgaben verwenden können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Verwaltungsoption          | Aufgabe                    |
|----------------------------|--------------------------------|
| Direkte Methoden             | Lassen Sie ein Gerät beispielsweise mit dem Senden von Nachrichten beginnen oder dies beenden, oder starten Sie es neu.                                        |
| Lesen des Gerätezwillings           | Rufen Sie den berichteten Status eines Geräts ab. Das Gerät meldet z.B., das die LED jetzt blinkt.                                    |
| Aktualisieren des Gerätezwillings         | Setzen Sie ein Gerät in bestimmte Status, stellen Sie z.B. das Leuchten einer grünen LED ein, oder legen Sie das Telemetriesendeintervall auf 30 Minuten fest.         |
| C2D-Nachrichten   | Senden Sie Benachrichtigungen an ein Gerät. Beispiel: „Heute wird es sehr wahrscheinlich regnen. Vergessen Sie Ihren Schirm nicht.“              |

Eine ausführlichere Erläuterung zu den Unterschieden und Anweisungen zur Verwendung dieser Optionen finden Sie im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md) und [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

Gerätezwillinge sind JSON-Dokumente, in denen Gerätestatusinformationen (Metadaten, Konfigurationen und Bedingungen) gespeichert werden. Von IoT Hub wird für jedes Gerät, das eine Verbindung herstellt, dauerhaft ein Gerätezwilling gespeichert. Weitere Informationen zu Gerätezwillingen finden Sie unter [Erste Schritte mit Gerätezwillingen (Node)](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-you-learn"></a>Lerninhalt

Es wird beschrieben, wie Sie die Azure IoT-Tools für Visual Studio Code mit unterschiedlichen Verwaltungsoptionen auf Ihrem Entwicklungscomputer verwenden.

## <a name="what-you-do"></a>Aufgaben

Führen Sie die Azure IoT-Tools für Visual Studio Code mit unterschiedlichen Verwaltungsoptionen aus.

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

## <a name="direct-methods"></a>Direkte Methoden

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und wählen Sie **Invoke Direct Method** (Direkte Methode aufrufen). 

2. Geben Sie den Methodennamen und die Nutzlast im Eingabefeld ein.

3. Die Ergebnisse werden in der Ansicht **AUSGABE** > **Azure IoT Hub Toolkit** angezeigt.

## <a name="read-device-twin"></a>Lesen des Gerätezwillings

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und wählen Sie **Gerätezwilling bearbeiten**. 

2. Die Datei **azure-iot-device-twin.json** mit dem Inhalt des Gerätezwillings wird geöffnet.

## <a name="update-device-twin"></a>Aktualisieren des Gerätezwillings

1. Nehmen Sie Änderungen an **Tags** oder am Feld **properties.desired** vor.

2. Klicken Sie mit der rechten Maustaste auf die Datei **azure-iot-device-twin.json**.

3. Wählen Sie **Update Device Twin** (Gerätezwilling aktualisieren), um den Gerätezwilling zu aktualisieren.

## <a name="send-cloud-to-device-messages"></a>Senden von C2D-Nachrichten.

Um eine Nachricht von Ihrem IoT Hub zu Ihrem Gerät zu senden, gehen Sie folgendermaßen vor:
 
1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und klicken Sie auf **C2D-Nachricht an Gerät senden**. 

2. Geben Sie die Nachricht in das Eingabefeld ein.

3. Die Ergebnisse werden in der Ansicht **AUSGABE** > **Azure IoT Hub Toolkit** angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Es wurde beschrieben, wie Sie die Azure IoT-Tools für Visual Studio Code mit unterschiedlichen Verwaltungsoptionen verwenden.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
