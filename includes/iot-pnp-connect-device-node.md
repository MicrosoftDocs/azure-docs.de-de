---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 43ba9abc17217eea399ed4614002b001534da859
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511646"
---
In diesem Schnellstart erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung erstellen, mit Ihrem IoT-Hub verbinden und die von ihm gesendeten Telemetriedaten mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung wurde in Node.js geschrieben und ist im Azure IoT-Geräte-SDK für Node.js enthalten. Ein Lösungsentwickler kann mithilfe des Tools Azure IoT-Explorer mehr über die Funktionen eines IoT Plug & Play-Geräts erfahren, ohne Gerätecode anzeigen zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Um diesen Schnellstart abzuschließen, benötigen Sie Node.js auf Ihrem Entwicklungscomputer. Sie können die neueste empfohlene Version für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Laden Sie den Code herunter.

In dieser Schnellstartanleitung bereiten Sie eine Entwicklungsumgebung vor, die Sie zum Klonen und Erstellen des Azure IoT Hub-Geräte-SDK für Node.js verwenden können.

Öffnen Sie eine Eingabeaufforderung in einem Verzeichnis Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) an diesem Speicherort zu klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installieren der erforderlichen Bibliotheken

Sie verwenden das Geräte-SDK, um den enthaltenen Beispielcode zu erstellen. Die von Ihnen erstellte Anwendung simuliert ein Gerät, mit dem eine Verbindung mit einem IoT-Hub hergestellt wird. Die Anwendung sendet Telemetriedaten und Eigenschaften und empfängt Befehle.

1. Wechseln Sie in einem lokalen Terminalfenster zum Ordner Ihres geklonten Repositorys, und navigieren Sie zum Ordner */azure-iot-sdk-node/device/samples/pnp*. Führen Sie dann den folgenden Befehl aus, um die erforderlichen Bibliotheken zu installieren:

    ```cmd/sh
    npm install
    ```

1. Konfigurieren Sie die Umgebungsvariable mit der Geräteverbindungszeichenfolge, die Sie sich zuvor notiert haben:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Ausführen des Beispielgeräts

In diesem Beispiel wird ein einfacher IoT Plug & Play-Thermostat implementiert. Das in diesem Beispiel implementierte Modell verwendet keine IoT Plug & Play-[Komponenten](../articles/iot-pnp/concepts-components.md). Die [DTDL-Modelldatei für den Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiert die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle.

Öffnen Sie die Datei _simple_thermostat.js_. In dieser Datei können Sie folgende Aktionen sehen:

1. Importieren der erforderlichen Schnittstellen
1. Schreiben eines Updatehandlers für Eigenschaften und eines Befehlshandlers
1. Verarbeiten gewünschter Eigenschaftenpatches und Senden von Telemetriedaten
1. Optional können Sie Ihr Gerät mithilfe des Azure Device Provisioning-Diensts (DPS) bereitstellen.

In der Hauptfunktion können Sie sehen, wie alles zusammengestellt wird:

1. Erstellen Sie das Gerät aus Ihrer Verbindungszeichenfolge, oder stellen Sie es mithilfe von DPS bereit.)
1. Verwenden Sie die Option **modelID**, um das IoT Plug & Play-Gerätemodell anzugeben.
1. Aktivieren Sie den Befehlshandler.
1. Senden Sie Telemetriedaten vom Gerät an Ihren Hub.
1. Rufen Sie den Gerätezwilling ab, und aktualisieren Sie die gemeldeten Eigenschaften.
1. Aktivieren Sie den Updatehandler für gewünschte Eigenschaften.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Führen Sie die Beispielanwendung zum Simulieren eines IoT Plug & Play-Geräts aus, das Telemetriedaten an Ihren IoT-Hub sendet. Um die Beispielanwendung auszuführen, verwenden Sie den folgenden Befehl:

```cmd\sh
node simple_thermostat.js
```

Sie sehen die folgende Ausgabe. Das bedeutet, dass das Gerät mit dem Senden von Telemetriedaten an den Hub begonnen hat und jetzt zum Empfangen von Befehlen und Eigenschaftenaktualisierungen bereit ist.

![Bestätigungsmeldungen des Geräts](media/iot-pnp-connect-device-node/device-confirmation-node.png)

Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
