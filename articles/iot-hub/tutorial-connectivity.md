---
title: Überprüfen der Gerätekonnektivität mit Azure IoT Hub
description: Verwenden Sie die IoT Hub-Tools, um während der Entwicklung Probleme mit der Konnektivität für Ihre IoT Hub-Instanz zu behandeln.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom:
- mvc
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: 1eead9bb93fe8b753ace518cde18b240ab1a3cd4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572676"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Tutorial: Verwenden eines simulierten Geräts zum Testen der Konnektivität mit Ihrem IoT Hub

In diesem Tutorial verwenden Sie die Tools des Azure IoT Hub-Portals und die Azure CLI-Befehle, um die Gerätekonnektivität zu testen. In diesem Tutorial wird auch ein einfacher Gerätesimulator verwendet, den Sie auf Ihrem Desktopcomputer ausführen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Überprüfen Ihrer Geräteauthentifizierung
> * Überprüfen der Gerät-zu-Cloud-Konnektivität
> * Überprüfen der Cloud-zu-Gerät-Konnektivität
> * Überprüfen der Synchronisierung von Gerätezwillingen

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Die Gerätesimulatoranwendung, die Sie in diesem Tutorial ausführen, wurde mit Node.js geschrieben. Sie benötigen mindestens Node.js v10.x.x auf Ihrem Entwicklungscomputer.

Sie können Node.js für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```cmd/sh
node --version
```

Laden Sie das Node.js-Beispielprojekt für den Gerätesimulator von https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip herunter, und extrahieren Sie das ZIP-Archiv.

Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispielgerät in diesem Tutorial wird das MQTT-Protokoll verwendet, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Wenn Sie in einer vorherigen Schnellstartanleitung oder einem Tutorial einen IoT Hub mit dem Tarif „Free“ oder „Standard“ erstellt haben, können Sie diesen Schritt überspringen.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Überprüfen der Geräteauthentifizierung

Ein Gerät muss für Ihren Hub authentifiziert werden, bevor es Daten mit dem Hub austauschen kann. Sie können das Tool **IoT-Geräte** im Abschnitt **Geräteverwaltung** des Portals verwenden, um Ihre Geräte zu verwalten und die genutzten Authentifizierungsschlüssel zu überprüfen. In diesem Abschnitt des Tutorials fügen Sie ein neues Testgerät hinzu, rufen den dazugehörigen Schlüssel ab und stellen sicher, dass das Testgerät eine Verbindung mit dem Hub herstellen kann. Später setzen Sie den Authentifizierungsschlüssel zurück, um zu verfolgen, was passiert, wenn für ein Gerät ein veralteter Schlüssel verwendet wird. In diesem Abschnitt des Tutorials wird das Azure-Portal verwendet, um ein Gerät zu erstellen, zu verwalten und zu überwachen, und außerdem wird das Beispiel für einen Node.js-Gerätesimulator genutzt.

Melden Sie sich am Portal an, und navigieren Sie zu Ihrem IoT Hub. Navigieren Sie anschließend zum Tool **IoT-Geräte**:

![Tool „IoT-Geräte“](media/tutorial-connectivity/iot-devices-tool.png)

Klicken Sie zum Registrieren eines neuen Geräts auf **+ Hinzufügen**, legen Sie die **Geräte-ID** auf **MyTestDevice** fest, und klicken Sie auf **Speichern**:

![Hinzufügen eines neuen Geräts](media/tutorial-connectivity/add-device.png)

Klicken Sie zum Abrufen der Verbindungszeichenfolge für **MyTestDevice** in der Liste mit den Geräten darauf, und kopieren Sie anschließend den Wert von **Verbindungszeichenfolge – Primärschlüssel**. Die Verbindungszeichenfolge enthält den *Freigegebenen Zugriffsschlüssel* für das Gerät.

![Abrufen der Geräte-Verbindungszeichenfolge](media/tutorial-connectivity/copy-connection-string.png)

Führen Sie zum Simulieren des Sendens von Telemetriedaten an Ihren IoT Hub per **MyTestDevice** die Node.js-Anwendung für die Gerätesimulation aus, die Sie zuvor heruntergeladen haben.

Navigieren Sie in einem Terminalfenster auf Ihrem Entwicklungscomputer zum Stammordner des Node.js-Beispielprojekts, das Sie heruntergeladen haben. Navigieren Sie anschließend zum Ordner **iot-hub\Tutorials\ConnectivityTests**.

Führen Sie im Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die Anwendung zur Simulation eines Geräts auszuführen. Verwenden Sie die Verbindungszeichenfolge des Geräts, die Sie beim Hinzufügen des Geräts im Portal notiert haben.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Im Terminalfenster werden Informationen angezeigt, während versucht wird, eine Verbindung mit Ihrem Hub herzustellen:

![Verbindungsherstellung für simuliertes Gerät](media/tutorial-connectivity/sim-1-connected.png)

Sie haben sich jetzt erfolgreich über ein Gerät authentifiziert, indem Sie einen von Ihrem IoT Hub generierten Geräteschlüssel genutzt haben.

### <a name="reset-keys"></a>Zurücksetzen von Schlüsseln

In diesem Abschnitt setzen Sie den Geräteschlüssel zurück und sehen dann den Fehler, wenn für das simulierte Gerät versucht wird, eine Verbindung herzustellen.

Führen Sie die folgenden Befehle aus, um den primären Geräteschlüssel für **MyTestDevice** zurückzusetzen:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-iot

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

Führen Sie im Terminalfenster auf Ihrem Entwicklungscomputer erneut die Anwendung für die Gerätesimulation aus:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Hierbei sehen Sie jetzt einen Authentifizierungsfehler, wenn die Anwendung versucht, eine Verbindung herzustellen:

![Verbindungsfehler](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Generieren eines SAS-Tokens (Shared Access Signature)

Wenn Ihr Gerät eines der IoT Hub-Geräte-SDKs nutzt, generiert der SDK-Bibliothekscode das SAS-Token, das zum Authentifizieren mit dem Hub verwendet wird. Ein SAS-Token wird aus dem Namen Ihres Hub, dem Namen Ihres Geräts und dem Geräteschlüssel generiert.

Bei einigen Szenarien, z.B. in einem Cloudprotokollgateway oder als Teil eines benutzerdefinierten Authentifizierungsschemas, müssen Sie das SAS-Token ggf. selbst generieren. Zum Behandeln von Problemen mit Ihrem SAS-Generierungscode ist es nützlich, ein als funktionierend bekanntes SAS-Token zur Verwendung beim Testen zu generieren.

> [!NOTE]
> Das Beispiel „SimulatedDevice-2.js“ enthält Beispiele zum Generieren eines SAS-Tokens jeweils mit und ohne SDK.

Führen Sie den folgenden Befehl aus, um mit der CLI ein als funktionierend bekanntes SAS-Token zu generieren:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Notieren Sie sich den gesamten Text des generierten SAS-Tokens. Ein SAS-Token sieht wie folgt aus: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

Navigieren Sie in einem Terminalfenster auf Ihrem Entwicklungscomputer zum Stammordner des Node.js-Beispielprojekts, das Sie heruntergeladen haben. Navigieren Sie anschließend zum Ordner **iot-hub\Tutorials\ConnectivityTests**.

Führen Sie im Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die Anwendung zur Simulation eines Geräts auszuführen:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

Im Terminalfenster werden Informationen angezeigt, während versucht wird, mit dem SAS-Token eine Verbindung mit Ihrem Hub herzustellen:

![Verbindungsherstellung für simuliertes Gerät mit Token](media/tutorial-connectivity/sim-2-connected.png)

Sie haben sich jetzt von einem Gerät aus erfolgreich authentifiziert, indem Sie ein mit einem CLI-Befehl generiertes SAS-Testtoken verwendet haben. Die Datei **SimulatedDevice-2.js** enthält Beispielcode, mit dem veranschaulicht wird, wie Sie im Code ein SAS-Token generieren.

### <a name="protocols"></a>Protokolle

Für ein Gerät kann eines der folgenden Protokolle verwendet werden, um eine Verbindung mit Ihrem IoT Hub herzustellen:

| Protocol | Ausgehender Port |
| --- | --- |
| MQTT |8883 |
| MQTT über WebSockets |443 |
| AMQP |5671 |
| AMQP über WebSockets |443 |
| HTTPS |443 |

Wenn der ausgehende Port durch eine Firewall blockiert ist, kann das Gerät keine Verbindung herstellen:

![Blockierter Port](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Überprüfen der Gerät-zu-Cloud-Konnektivität

Nachdem für ein Gerät die Verbindung hergestellt wurde, wird normalerweise versucht, Telemetriedaten an Ihren IoT Hub zu senden. In diesem Abschnitt wird veranschaulicht, wie Sie überprüfen können, ob die vom Gerät gesendeten Telemetriedaten Ihren Hub erreichen.

Rufen Sie zunächst mit dem folgenden Befehl die aktuelle Verbindungszeichenfolge für das simulierte Gerät ab:

```azurecli-interactive
az iot hub device-identity show-connection-string --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Navigieren Sie zum Ausführen eines simulierten Geräts, mit dem Nachrichten gesendet werden, im heruntergeladenen Code zum Ordner **iot-hub\Tutorials\ConnectivityTests**.

Führen Sie im Terminalfenster die folgenden Befehle aus, um die erforderlichen Bibliotheken zu installieren und die Anwendung zur Simulation eines Geräts auszuführen:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

Im Terminalfenster werden Informationen angezeigt, während Telemetriedaten an Ihren Hub gesendet werden:

![Senden von Nachrichten vom simulierten Gerät](media/tutorial-connectivity/sim-3-sending.png)

Sie können die Option **Metriken** im Portal nutzen, um sicherzustellen, dass die Telemetrienachrichten Ihren IoT Hub erreichen. Wählen Sie Ihren IoT Hub in der Dropdownliste **Ressourcen** aus, wählen Sie **Telemetry messages sent** (Gesendete Telemetrienachrichten) als Metrik aus, und legen Sie den Zeitraum auf **Letzte Stunde** fest. Im Diagramm wird die Aggregatanzahl von Nachrichten angezeigt, die vom simulierten Gerät gesendet werden:

![Anzeigen von IoT Hub-Metriken](media/tutorial-connectivity/metrics-portal.png)

Es dauert einige Minuten, bis die Metriken nach dem Starten des simulierten Geräts zur Verfügung stehen.

## <a name="check-cloud-to-device-connectivity"></a>Überprüfen der Cloud-zu-Gerät-Konnektivität

In diesem Abschnitt wird gezeigt, wie Sie einen direkten Test-Methodenaufruf für ein Gerät durchführen können, um die Cloud-zu-Gerät-Konnektivität zu testen. Sie führen ein simuliertes Gerät auf Ihrem Entwicklungscomputer aus, um auf direkte Methodenaufrufe von Ihrem Hub zu lauschen.

Verwenden Sie in einem Terminalfenster den folgenden Befehl, um die simulierte Geräteanwendung auszuführen:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Verwenden Sie einen CLI-Befehl, um auf dem Gerät eine direkte Methode aufzurufen:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

Das simulierte Gerät gibt eine Meldung auf der Konsole aus, wenn es einen direkten Methodenaufruf empfängt:

![Simuliertes Gerät empfängt direkten Methodenaufruf](media/tutorial-connectivity/receive-method-call.png)

Wenn das simulierte Gerät den direkten Methodenaufruf ohne Fehler empfangen hat, sendet es eine Bestätigung zurück an den Hub:

![Bestätigung des empfangenen direkten Methodenaufrufs](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Überprüfen der Zwillingssynchronisierung

Für Geräte werden Zwillinge verwendet, um den Zustand zwischen dem Gerät und dem Hub zu synchronisieren. In diesem Abschnitt verwenden Sie CLI-Befehle zum Senden von _gewünschten Eigenschaften_ an ein Gerät und zum Lesen der vom Gerät gesendeten _gemeldeten Eigenschaften_.

Das in diesem Abschnitt verwendete simulierte Gerät sendet bei jedem Start gemeldete Eigenschaften an den Hub und gibt die gewünschten Eigenschaften jeweils in der Konsole aus, wenn diese empfangen werden.

Verwenden Sie in einem Terminalfenster den folgenden Befehl, um die simulierte Geräteanwendung auszuführen:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Verwenden Sie den folgenden CLI-Befehl, um sicherzustellen, dass der Hub die gemeldeten Eigenschaften vom Gerät empfangen hat:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

In der Ausgabe des Befehls wird die **devicelaststarted**-Eigenschaft im Abschnitt mit den gemeldeten Eigenschaften angezeigt. Mit dieser Eigenschaft werden das Datum und die Uhrzeit des Zeitpunkts angezeigt, zu dem Sie das simulierte Gerät zum letzten Mal gestartet haben.

![Anzeigen von gemeldeten Eigenschaften](media/tutorial-connectivity/reported-properties.png)

Verwenden Sie den folgenden CLI-Befehl, um sicherzustellen, dass der Hub Werte für gewünschte Eigenschaften an das Gerät senden kann:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Das simulierte Gerät gibt eine Meldung aus, wenn es ein Update mit gewünschten Eigenschaften vom Hub empfängt:

![Empfangen von gewünschten Eigenschaften](media/tutorial-connectivity/desired-properties.png)

Zusätzlich zum Empfangen der vorgenommenen Änderungen von gewünschten Eigenschaften führt das simulierte Gerät beim Starten eine automatische Überprüfung auf gewünschte Eigenschaften durch.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie die IoT Hub-Instanz nicht mehr benötigen, löschen Sie die Ressourcengruppe über das Portal. Wählen Sie hierzu die Ressourcengruppe **tutorials-iot-hub-rg** aus, die Ihre IoT Hub-Instanz enthält, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie Folgendes überprüfen: Ihre Geräteschlüssel, die Gerät-zu-Cloud-Konnektivität, die Cloud-zu-Gerät-Konnektivität und die Synchronisierung der Gerätezwillinge. Weitere Informationen zur Überwachung Ihres IoT Hub finden Sie im Artikel mit der Anleitung für die IoT Hub-Überwachung.

> [!div class="nextstepaction"]
> [Überwachen von IoT Hub](monitor-iot-hub.md)
