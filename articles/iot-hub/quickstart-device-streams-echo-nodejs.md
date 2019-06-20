---
title: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion) | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung führen Sie eine dienstseitige Node.js-Anwendung aus, die über einen Gerätestream mit einem IoT-Gerät kommuniziert.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: d9d083569d648b2bd22438f295a640b4e8d03b77
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733241"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Schnellstart: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub unterstützt derzeit Gerätestreams als [Previewfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Über [IoT Hub-Gerätestreams](./iot-hub-device-streams-overview.md) können Dienst- und Geräteanwendungen sicher und firewallfreundlich kommunizieren. Während der Public Preview-Phase unterstützt das Node.js SDK nur dienstseitige Gerätestreams. Aus diesem Grund wird in dieser Schnellstartanleitung nur das Ausführen der dienstseitigen Anwendung erläutert. Sie sollten eine begleitende geräteseitige Anwendung über einen der folgenden Schnellstarts ausführen:

* [Kommunizieren mit Geräte-Apps in C über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-c.md)

* [Kommunizieren mit Geräte-Apps in C# über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-csharp.md)

Die dienstseitige Node.js-Anwendung in dieser Schnellstartanleitung hat folgende Funktionen:

* Erstellen eines Gerätestreams mit einem IoT-Gerät

* Lesen von Befehlszeileneingaben und senden dieser Eingaben an die Geräteanwendung, die sie anschließend zurückgibt

Der Code veranschaulicht den Initiierungsprozess eines Gerätestreams sowie das Senden und Empfangen von Daten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Die Vorschau der Gerätestreams wird derzeit nur für IoT Hubs unterstützt, die in folgenden Regionen erstellt werden:

*  **USA (Mitte)**

*  **USA, Mitte (EUAP)**

Auf Ihrem Entwicklungscomputer muss sich mindestens Node.js v10.x.x befinden, um die dienstseitige Anwendung in diesem Schnellstart ausführen zu können.

Sie können Node.js für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```
node --version
```

Führen Sie den folgenden Befehl aus, um Ihrer Cloud Shell-Instanz die Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle hinzuzufügen. Die IoT-Erweiterung fügt der Azure-Befehlszeilenschnittstelle spezifische Befehle für IoT Hub, IoT Edge und IoT Device Provisioning Service (DPS) hinzu.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Laden Sie das Node.js-Beispielprojekt von https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip herunter, und extrahieren Sie das ZIP-Archiv (falls Sie dies nicht bereits getan haben).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-node.md) absolviert haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](quickstart-send-telemetry-node.md) absolviert haben, können Sie diesen Schritt überspringen.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyDevice**: Der für das registrierte Gerät angegebene Name. Verwenden Sie „MyDevice“ wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Darüber hinaus benötigen Sie eine *Dienstverbindungszeichenfolge*, damit die Back-End-Anwendung eine Verbindung mit Ihrer IoT Hub-Instanz herstellen und die Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Notieren Sie sich den zurückgegebenen Wert, der in etwa wie folgt aussieht:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunizieren zwischen Gerät und Dienst über Gerätestreams

In diesem Abschnitt führen Sie die geräteseitige Anwendung und die dienstseitige Anwendung aus und stellen eine Kommunikation zwischen beiden her.

### <a name="run-the-device-side-application"></a>Ausführen der geräteseitigen Anwendung

Wie bereits erwähnt, unterstützt das IoT Hub Node.js SDK während der Public Preview-Phase nur dienstseitige Gerätestreams. Verwenden Sie als geräteseitige Anwendung die begleitenden Geräteprogramme, die in diesen Schnellstarts verfügbar sind:

   * [Kommunizieren mit Geräte-Apps in C über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-c.md)

   * [Kommunizieren mit Geräte-Apps in C# über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-csharp.md)

Vergewissern Sie sich, dass die geräteseitige Anwendung ausgeführt wird, bevor Sie mit dem nächsten Schritt fortfahren.

### <a name="run-the-service-side-application"></a>Ausführen der dienstseitigen Anwendung

Nachdem Sie sich vergewissert haben, dass die geräteseitige Anwendung ausgeführt wird, gehen Sie wie folgt vor, um die dienstseitige Anwendung in Node.js auszuführen:

* Geben Sie Ihre Dienstanmeldeinformationen und die Geräte-ID als Umgebungsvariablen an.
 
   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Ändern Sie `MyDevice` in die Geräte-ID, die Sie für Ihr Gerät gewählt haben.

* Navigieren Sie in Ihrem entzippten Projektordner zu `Quickstarts/device-streams-service`, und führen Sie das Beispiel mit dem Knoten aus.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Am Ende des letzten Schritts initiiert das dienstseitige Programm einen Stream an Ihr Gerät und sendet anschließend einen Zeichenfolgenpuffer über den Stream an den Dienst. In diesem Beispiel liest das dienstseitige Programm einfach `stdin` im Terminal und sendet die Daten an das Gerät, das sie daraufhin zurückgibt. Dies veranschaulicht eine erfolgreiche bidirektionale Kommunikation zwischen den beiden Anwendungen.

![Dienstseitige Konsolenausgabe](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Anschließend können Sie das Programm durch erneutes Drücken der EINGABETASTE beenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen IoT-Hub eingerichtet, ein Gerät registriert, einen Gerätestream zwischen geräte- und dienstseitigen Anwendungen eingerichtet und mithilfe des Streams Daten zwischen den Anwendungen hin und her gesendet.

Weitere Informationen zu Gerätestreams finden Sie hier:

> [!div class="nextstepaction"]
> [IoT Hub-Gerätestreams (Vorschau)](./iot-hub-device-streams-overview.md) 