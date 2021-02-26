---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 43a8600a288a9797edcd326a1cb5dbcc8688d61f
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834148"
---
In diesem Tutorial erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung mit Komponenten erstellen, mit Ihrem IoT-Hub verbinden und die an den Hub gesendeten Informationen mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung wurde für Node.js geschrieben und ist im Azure IoT Hub-Geräte-SDK für Node.js enthalten. Ein Lösungsentwickler kann mithilfe von Azure IoT-Explorer mehr über die Funktionen eines IoT Plug & Play-Geräts erfahren, ohne Gerätecode anzeigen zu müssen.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Laden Sie den Beispielcode herunter.
> * Führen Sie die Beispielgeräteanwendung aus, und überprüfen Sie, ob sie eine Verbindung mit Ihrem IoT-Hub herstellt.
> * Überprüfen Sie den Quellcode.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Für dieses Tutorial muss sich Node.js auf Ihrem Entwicklungscomputer befinden. Sie können die neueste empfohlene Version für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Laden Sie den Code herunter.

Wenn Sie [Schnellstart: Verbinden einer unter Windows ausgeführten IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub (Node.js)](../articles/iot-pnp/quickstart-connect-device.md) abgeschlossen haben, wurde das Repository bereits geklont.

Öffnen Sie eine Eingabeaufforderung in einem Verzeichnis Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) an diesem Speicherort zu klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Installieren der erforderlichen Bibliotheken

Sie verwenden das Geräte-SDK, um den enthaltenen Beispielcode zu erstellen. Durch die von Ihnen erstellte Anwendung wird ein Plug & Play-Gerät mit mehreren Komponenten simuliert, das eine Verbindung mit einem IoT-Hub herstellt. Die Anwendung sendet Telemetriedaten und Eigenschaften und empfängt Befehle.

1. Wechseln Sie in einem lokalen Terminalfenster zum Ordner Ihres geklonten Repositorys, und navigieren Sie zum Ordner */azure-iot-sdk-node/device/samples/pnp*. Führen Sie dann den folgenden Befehl aus, um die erforderlichen Bibliotheken zu installieren:

```cmd/sh
npm install
```

Dadurch werden die relevanten npm-Dateien installiert, die zum Ausführen der Beispiele im Ordner erforderlich sind.

## <a name="review-the-code"></a>Überprüfen des Codes

Navigieren Sie zum Ordner *azure-iot-sdk-node\device\samples\pnp*.

Der Ordner *azure-iot-sdk-node\device\samples\pnp* enthält den Beispielcode für den IoT Plug & Play-Temperaturregler.

Der Code in der Datei *pnpTemperatureController.js* dient zum Implementieren eines IoT Plug & Play-Temperaturreglers. Das in diesem Beispiel implementierte Modell umfasst [mehrere Komponenten](../articles/iot-pnp/concepts-components.md). In der [DTDL-Modelldatei (Digital Twins Definition Language) für das Temperaturgerät](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) werden die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle definiert.

Öffnen Sie die Datei *pnpTemperatureController.js* in einem Code-Editor Ihrer Wahl. Mithilfe des Beispielcodes wird Folgendes gezeigt:

- Definieren der Modell-ID (`modelId`). Hierbei handelt es sich um den DTMI für das zu implementierende Gerät. Dieser DTMI ist benutzerdefiniert und muss dem DTMI im [DTDL-Modell des Temperaturreglers](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) entsprechen.

- Implementieren der im DTDL-Modell des Temperaturreglers definierten Komponenten. Von den Komponenten in einem echten Temperaturregler müssen die beiden folgenden Schnittstellen implementiert werden. Die beiden Schnittstellen sind bereits in einem zentralen Repository veröffentlicht. In diesem Beispiel werden die beiden folgenden Schnittstellen verwendet:

  - Thermostat
  - Von Azure entwickelte Geräteinformationen

- Definieren von Komponentennamen. Dieses Beispiel umfasst zwei Thermostate und eine Geräteinformationskomponente.

- Definieren von Befehlsnamen. Hierbei handelt es sich um die Befehle, auf die das Gerät antwortet.

- Definieren der Konstante `serialNumber`. Die Seriennummer (`serialNumber`) ist für jedes Gerät fest vorgegeben.

- Definieren der Befehlshandler

- Definieren der Funktionen zum Senden von Befehlsantworten

- Definieren von Hilfsfunktionen zum Protokollieren von Befehlsanforderungen

- Definieren einer Hilfsfunktion zum Erstellen der Eigenschaften

- Definieren eines Listeners für Eigenschaftsaktualisierungen

- Definieren einer Funktion, um Telemetriedaten von diesem Gerät zu senden. Telemetriedaten werden von beiden Thermostaten und der Standardkomponente gesendet. Diese Funktion empfängt den Komponentennamen als Parameter.

- Definieren einer Funktion vom Typ `main`, für die Folgendes gilt:

  - Sie verwendet das Geräte-SDK, um einen Geräteclient zu erstellen und eine Verbindung mit Ihrem IoT-Hub herzustellen. Vom Gerät wird die Modell-ID (`modelId`) bereitgestellt, damit es vom IoT-Hub als IoT Plug & Play-Gerät identifiziert werden kann.

  - Sie beginnt mit dem Lauschen auf Befehlsanforderungen unter Verwendung der Funktion `onDeviceMethod`. Von der Funktion wird ein Listener eingerichtet, um auf Anforderungen des Diensts zu lauschen:

    - Durch die DTDL des Geräts werden die Befehle `reboot` und `getMaxMinReport` definiert.
    - Durch die Funktion `commandHandler` wird definiert, wie das Gerät auf einen Befehl antwortet.

  - Sie beginnt mit dem Senden von Telemetriedaten unter Verwendung von `setInterval` und `sendTelemetry`.

  - Sie verwendet die Funktion `helperCreateReportedPropertiesPatch`, um die Eigenschaften zu erstellen, und die Funktion `updateComponentReportedProperties`, um die Eigenschaften zu aktualisieren.

  - Sie verwendet `desiredPropertyPatchListener`, um auf Eigenschaftsaktualisierungen zu lauschen.

  - Sie deaktiviert alle Listener und Aufgaben und beendet die Schleife, wenn Sie **Q** oder **q** drücken.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Nachdem Sie nun mit dem Code vertraut sind, verwenden Sie den folgenden Befehl, um das Beispiel auszuführen:

```cmd\sh
node pnpTemperatureController.js
```

Sie sehen die folgende Ausgabe. Das bedeutet, dass das Gerät mit dem Senden von Telemetriedaten an den Hub begonnen hat und jetzt zum Empfangen von Befehlen und Eigenschaftenaktualisierungen bereit ist.

![Bestätigungsmeldungen des Geräts](media/iot-pnp-multiple-components-node/multiple-component.png)

Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
