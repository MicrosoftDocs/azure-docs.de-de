---
title: Kommunizieren mit einer Geräte-App in Node.js über Azure IoT Hub-Gerätestreams
description: In dieser Schnellstartanleitung führen Sie eine dienstseitige Node.js-Anwendung aus, die über einen Gerätestream mit einem IoT-Gerät kommuniziert.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 678955970f3eeb87a10c43cd43effc3464db7794
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832006"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Schnellstart: Kommunizieren mit einer Geräteanwendung in Node.js über IoT Hub-Gerätestreams (Vorschauversion)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

In dieser Schnellstartanleitung führen Sie eine dienstseitige Anwendung aus und richten mithilfe von Gerätestreams die Kommunikation zwischen einem Gerät und einem Dienst ein. Über Azure IoT Hub-Gerätestreams können Dienst- und Geräteanwendungen sicher und firewallfreundlich kommunizieren. Während der Public Preview-Phase unterstützt das Node.js SDK nur dienstseitige Gerätestreams. Aus diesem Grund wird in dieser Schnellstartanleitung nur das Ausführen der dienstseitigen Anwendung erläutert.

## <a name="prerequisites"></a>Voraussetzungen

* Abschluss von [Schnellstart: Kommunizieren mit einer Geräteanwendung in C über IoT Hub-Gerätestreams (Vorschauversion)](./quickstart-device-streams-echo-c.md) oder von [Schnellstart: Kommunizieren mit einer Geräteanwendung in C# über IoT Hub-Gerätestreams (Vorschauversion)](./quickstart-device-streams-echo-csharp.md)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10 oder höher](https://nodejs.org)

    Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

    ```cmd/sh
    node --version
    ```

* [Ein Node.js-Beispielprojekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Microsoft Azure IoT Hub unterstützt derzeit Gerätestreams als [Previewfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Die Vorschau der Gerätestreams wird derzeit nur für IoT Hubs unterstützt, die in folgenden Regionen erstellt werden:
>
> * USA (Mitte)
> * USA, Mitte (EUAP)
> * Nordeuropa
> * Asien, Südosten

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](quickstart-send-telemetry-node.md) abgeschlossen haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrieren eines Geräts

Wenn Sie das vorherige Tutorial [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](quickstart-send-telemetry-node.md) abgeschlossen haben, können Sie diesen Schritt überspringen.

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

   **MyDevice**: Dies ist der Name des Geräts, das Sie registrieren. Es empfiehlt sich, **MyDevice** wie gezeigt zu verwenden. Wenn Sie für Ihr Gerät einen anderen Namen auswählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Darüber hinaus benötigen Sie eine *Dienstverbindungszeichenfolge*, damit die Back-End-Anwendung eine Verbindung mit Ihrer IoT Hub-Instanz herstellen und die Nachrichten abrufen kann. Der folgende Befehl ruft die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab:

    **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub ausgewählt haben.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Notieren Sie sich die zurückgegebene Verbindungszeichenfolge des Diensts zur späteren Verwendung in dieser Schnellstartanleitung. Dies sieht in etwa wie im folgenden Beispiel aus:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunizieren zwischen Gerät und Dienst über Gerätestreams

In diesem Abschnitt führen Sie die geräteseitige Anwendung und die dienstseitige Anwendung aus und stellen eine Kommunikation zwischen beiden her.

### <a name="run-the-device-side-application"></a>Ausführen der geräteseitigen Anwendung

Wie bereits erwähnt, unterstützt das IoT Hub Node.js SDK während der Public Preview-Phase nur dienstseitige Gerätestreams. Verwenden Sie für eine geräteseitige Anwendung die zugehörigen Geräteprogramme, die in diesen Schnellstarts verfügbar sind:

* [Kommunizieren mit Geräte-Apps in C über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-c.md)

* [Kommunizieren mit Geräte-Apps in C# über IoT Hub-Gerätestreams](./quickstart-device-streams-echo-csharp.md)

Vergewissern Sie sich, dass die geräteseitige Anwendung ausgeführt wird, bevor Sie mit dem nächsten Schritt fortfahren.

### <a name="run-the-service-side-application"></a>Ausführen der dienstseitigen Anwendung

Die dienstseitige Node.js-Anwendung in dieser Schnellstartanleitung hat folgende Funktionen:

* Erstellen eines Gerätestreams mit einem IoT-Gerät
* Lesen von Befehlszeileneingaben und senden dieser Eingaben an die Geräteanwendung, die sie anschließend zurückgibt

Der Code veranschaulicht den Initiierungsprozess eines Gerätestreams sowie das Senden und Empfangen von Daten.

Nachdem Sie sich vergewissert haben, dass die geräteseitige Anwendung ausgeführt wird, gehen Sie in einem Terminalfenster wie folgt vor, um die dienstseitige Anwendung in Node.js auszuführen:

* Geben Sie Ihre Dienstanmeldeinformationen und die Geräte-ID als Umgebungsvariablen an.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Ändern Sie den ServiceConnectionString-Platzhalter so, dass er mit der Verbindungszeichenfolge des Diensts identisch ist, und **MyDevice** muss mit Ihrer Geräte-ID übereinstimmen, wenn Sie einen anderen Namen vergeben haben.

* Navigieren Sie in Ihrem entzippten Projektordner zu `Quickstarts/device-streams-service`, und führen Sie das Beispiel mit dem Knoten aus.

   ```cmd/sh
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
> [Übersicht über Gerätestreams](./iot-hub-device-streams-overview.md) 
