---
title: Azure IoT-Geräteverwaltung mit dem Cloud-Explorer für Visual Studio | Microsoft-Dokumentation
description: Verwenden Sie den Cloud Explorer für Visual Studio für die Geräteverwaltung mit Azure IoT Hub, indem direkte Methoden und gewünschte Eigenschaften von Gerätezwillingen verwendet werden.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399555"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Verwenden des Cloud-Explorers für Visual Studio für die Geräteverwaltung mit Azure IoT Hub

![Lückenloses Diagramm](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud-Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) ist eine nützliche Erweiterung für Visual Studio, mit der Sie Ihre Azure-Ressourcen anzeigen, deren Eigenschaften überprüfen und wichtige Entwickleraktionen in Visual Studio ausführen können. Es bietet Verwaltungsoptionen, die Sie zum Ausführen verschiedener Aufgaben verwenden können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Verwaltungsoption          | Aufgabe                    |
|----------------------------|--------------------------------|
| Direkte Methoden             | Lassen Sie ein Gerät beispielsweise mit dem Senden von Nachrichten beginnen oder dies beenden, oder starten Sie es neu.                                        |
| Lesen des Gerätezwillings           | Rufen Sie den berichteten Status eines Geräts ab. Das Gerät meldet z.B., das die LED jetzt blinkt.                                    |
| Aktualisieren des Gerätezwillings         | Setzen Sie ein Gerät in bestimmte Status, stellen Sie z.B. das Leuchten einer grünen LED ein, oder legen Sie das Telemetriesendeintervall auf 30 Minuten fest.         |
| C2D-Nachrichten   | Senden Sie Benachrichtigungen an ein Gerät. Beispiel: „Heute wird es sehr wahrscheinlich regnen. Vergessen Sie Ihren Schirm nicht.“              |

Eine ausführlichere Erläuterung zu den Unterschieden und Anweisungen zur Verwendung dieser Optionen finden Sie im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md) und [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

Gerätezwillinge sind JSON-Dokumente, in denen Gerätestatusinformationen (Metadaten, Konfigurationen und Bedingungen) gespeichert werden. Von IoT Hub wird für jedes Gerät, das eine Verbindung herstellt, dauerhaft ein Gerätezwilling gespeichert. Weitere Informationen zu Gerätezwillingen finden Sie unter [Erste Schritte mit Gerätezwillingen (Node)](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Lerninhalt

Sie erfahren, wie Sie den Cloud-Explorer für Visual Studio mit unterschiedlichen Verwaltungsoptionen auf Ihrem Entwicklungscomputer verwenden.

## <a name="what-you-do"></a>Aufgaben

Führen Sie den Cloud-Explorer für Visual Studio mit verschiedenen Verwaltungsoptionen aus.

## <a name="what-you-need"></a>Voraussetzungen

- Ein aktives Azure-Abonnement
- Eine Azure IoT Hub-Instanz in Ihrem Abonnement
- Microsoft Visual Studio 2017 Update 8 oder höher
- Cloud-Explorer-Komponente von Visual Studio-Installer (standardmäßig mit Azure-Workload ausgewählt)

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualisieren von Cloud-Explorer auf die neueste Version

Die Cloud-Explorer-Komponente von Visual Studio-Installer unterstützt nur die Überwachung von Gerät-zu-Cloud und Cloud-zu-Gerät-Nachrichten. Sie müssen die neueste [Cloud-Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)-Version herunterladen und installieren, um auf die Verwaltungsoptionen zuzugreifen.

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihre IoT Hub-Instanz

1. Klicken Sie im Fenster **Cloud-Explorer** in Visual Studio auf das Symbol „Kontoverwaltung“. Sie können das Cloud-Explorer-Fenster über das Menü **Ansicht** > **Cloud-Explorer** öffnen.

    ![Klicken Sie auf „Kontoverwaltung“.](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Klicken Sie in Cloud-Explorer auf **Konten verwalten**.
1. Klicken Sie auf **Konto hinzufügen...** im neuen Fenster, um sich zum ersten Mal bei Azure anzumelden.
1. Nach der Anmeldung wird Ihre Liste der Azure-Abonnements angezeigt. Wählen Sie das anzuzeigende Azure-Abonnement aus, und klicken Sie auf **Anwenden**.
1. Erweitern Sie **Ihr Abonnement** > **IoT Hubs** > **Ihr IoT Hub**. Ihre Geräteliste wird unter Ihrem IoT Hub-Knoten angezeigt. Klicken Sie mit der rechten Maustaste auf ein Gerät, um auf die Verwaltungsoptionen zuzugreifen.

    ![Verwaltungsoptionen](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>Direkte Methoden

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und wählen Sie **Direkte Gerätemethode aufrufen** aus.
1. Geben Sie den Methodennamen und die Nutzlast im Eingabefeld ein.
1. Die Ergebnisse werden im Ausgabebereich **IoT Hub** angezeigt.

## <a name="read-device-twin"></a>Lesen des Gerätezwillings

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und wählen Sie **Gerätezwilling bearbeiten**.
1. Die Datei **azure-iot-device-twin.json** mit dem Inhalt des Gerätezwillings wird geöffnet.

## <a name="update-device-twin"></a>Aktualisieren des Gerätezwillings

1. Bearbeiten Sie das **tags**- oder **properties.desired**-Feld der **azure-iot-device-twin.json**-Datei.
1. Drücken Sie **STRG+S**, um den Gerätezwilling zu aktualisieren.
1. Die Ergebnisse werden im Ausgabebereich **IoT Hub** angezeigt.

## <a name="send-cloud-to-device-messages"></a>Senden von C2D-Nachrichten.

Um eine Nachricht von Ihrem IoT Hub zu Ihrem Gerät zu senden, gehen Sie folgendermaßen vor:

1. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und klicken Sie auf **C2D-Nachricht senden**.
1. Geben Sie die Nachricht in das Eingabefeld ein.
1. Die Ergebnisse werden im Ausgabebereich **IoT Hub** angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun gelernt, den Cloud-Explorer für Visual Studio mit verschiedenen Verwaltungsoptionen auszuführen.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]