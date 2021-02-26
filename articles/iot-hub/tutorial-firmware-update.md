---
title: 'Tutorial: Aktualisieren der Gerätefirmware per Azure IoT Hub | Microsoft-Dokumentation'
description: 'Tutorial: Hier wird beschrieben, wie Sie einen Updateprozess für die Gerätefirmware implementieren, der über eine mit Ihrem IoT-Hub verbundene Back-End-Anwendung ausgelöst werden kann.'
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 3fc257ff192ccb1bb05b233c6ac802696ece0054
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575720"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Tutorial: Implementieren eines Updateprozesses für die Gerätefirmware

Unter Umständen müssen Sie die Firmware auf den Geräten aktualisieren, die mit Ihrem IoT Hub verbunden sind. Es kann beispielsweise sein, dass Sie der Firmware neue Features hinzufügen oder Sicherheitspatches anwenden möchten. In vielen IoT-Szenarien ist es unpraktisch, physisch vor Ort manuell Firmwareupdates auf Ihre Geräte anzuwenden. In diesem Tutorial wird veranschaulicht, wie Sie den Prozess für Firmwareupdates per Remotezugriff über eine Back-End-Anwendung, die mit Ihrem Hub verbunden ist, starten und überwachen können.

Zum Erstellen und Überwachen des Prozesses für Firmwareupdates erstellt die Back-End-Anwendung in diesem Tutorial eine _Konfiguration_ auf Ihrem IoT Hub. Für die [automatische IoT Hub-Geräteverwaltung](./iot-hub-automatic-device-management.md) wird diese Konfiguration verwendet, um eine Gruppe mit gewünschten _Eigenschaften von Gerätezwillingen_ auf Ihren gesamten Kältemaschinen zu aktualisieren. Über die gewünschten Eigenschaften werden die Details zum erforderlichen Firmwareupdate angegeben. Während die Kältemaschinen den Prozess für das Firmwareupdate ausführen, wird der Status zurück an die Back-End-Anwendung gemeldet, indem _gemeldete Eigenschaften von Gerätezwillingen_ verwendet werden. Die Back-End-Anwendung kann die Konfiguration nutzen, um die vom Gerät gesendeten gemeldeten Eigenschaften zu überwachen und den Prozess für das Firmwareupdate bis zum Abschluss nachzuverfolgen:

![Prozess für das Firmwareupdate](media/tutorial-firmware-update/Process.png)

In diesem Tutorial führen Sie die folgenden Aufgaben durch:

> [!div class="checklist"]
> * Erstellen eines IoT Hub und Hinzufügen eines Testgeräts zur Identitätsregistrierung für das Gerät
> * Erstellen einer Konfiguration zum Definieren des Firmwareupdates
> * Simulieren des Prozesses für Firmwareupdates auf einem Gerät
> * Empfangen von Statusaktualisierungen vom Gerät während der Durchführung des Firmwareupdates

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Die beiden in dieser Schnellstartanleitung ausgeführten Beispielanwendungen sind in Node.js geschrieben. Sie benötigen mindestens Node.js v10.x.x auf Ihrem Entwicklungscomputer.

Sie können Node.js für mehrere Plattformen von [nodejs.org](https://nodejs.org) herunterladen.

Mit dem folgenden Befehl können Sie die aktuelle Node.js-Version auf Ihrem Entwicklungscomputer überprüfen:

```cmd/sh
node --version
```

Laden Sie das Node.js-Beispielprojekt von https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip herunter, und extrahieren Sie das ZIP-Archiv.

Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispielgerät in diesem Tutorial wird das MQTT-Protokoll verwendet, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

Damit Sie dieses Tutorial durcharbeiten können, muss Ihr Azure-Abonnement über einen IoT Hub mit einem Gerät verfügen, das der Geräteidentitätsregistrierung hinzugefügt wurde. Mit dem Eintrag in der Geräteidentitätsregistrierung kann für das simulierte Gerät, das Sie in diesem Tutorial ausführen, eine Verbindung mit Ihrem Hub hergestellt werden.

Falls Sie in Ihrem Abonnement noch keinen IoT Hub eingerichtet haben, können Sie hierfür das folgende CLI-Skript verwenden. In diesem Skript wird der Name **tutorial-iot-hub** für den IoT Hub verwendet. Ersetzen Sie diesen Namen durch Ihren eigenen eindeutigen Namen, wenn Sie es ausführen. Mit dem Skript werden die Ressourcengruppe und der Hub in der Region **Central US** (USA, Mitte) erstellt. Sie können dies auch in eine Region ändern, die in Ihrer Nähe liegt. Das Skript ruft Ihre IoT Hub-Dienstverbindungszeichenfolge ab, die Sie in der Back-End-Beispielanwendung verwenden, um eine Verbindung mit Ihrem IoT Hub herzustellen:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create a free-tier IoT Hub. You can have only one free IoT Hub per subscription. Free tier hubs can have only 2 partitions.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later
az iot hub connection-string show --name $hubname --policy-name service -o table

```

In diesem Tutorial wird ein simuliertes Gerät mit dem Namen **MyFirmwareUpdateDevice** verwendet. Mit dem folgenden Skript wird dieses Gerät Ihrer Geräteidentitätsregistrierung hinzugefügt, ein Tagwert festgelegt und die Verbindungszeichenfolge abgerufen:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"device type":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity connection-string show --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Wenn Sie diese Befehle an einer Windows-Befehlseingabeaufforderung oder PowerShell-Eingabeaufforderung ausführen, helfen Ihnen die Informationen auf der Seite mit den [Tipps zu azure-iot-cli-extension](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips) beim Zitieren von JSON-Zeichenfolgen weiter.

## <a name="start-the-firmware-update"></a>Starten des Firmwareupdates

Sie erstellen eine [Konfiguration für die automatische Geräteverwaltung](iot-hub-automatic-device-management.md#create-a-configuration) in der Back-End-Anwendung, um den Prozess für das Firmwareupdate auf allen Geräten zu starten, für die als **Gerätetyp** die Option „Chiller“ (Kältemaschine) angegeben ist. In diesem Abschnitt wird Folgendes veranschaulicht:

* Erstellen einer Konfiguration aus einer Back-End-Anwendung
* Überwachen des Auftrags bis zu seinem Abschluss

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Verwenden von gewünschten Eigenschaften zum Starten des Firmwareupgrades über die Back-End-Anwendung

Navigieren Sie zum Anzeigen des Back-End-Anwendungscodes, mit dem die Konfiguration erstellt wird, zum Ordner **iot-hub/Tutorials/FirmwareUpdate** im heruntergeladenen Node.js-Beispielprojekt. Öffnen Sie anschließend die Datei „ServiceClient.js“ in einem Text-Editor.

Die Back-End-Anwendung erstellt die folgende Konfiguration:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

Die Konfiguration enthält die folgenden Abschnitte:

* `content` gibt die gewünschten Eigenschaften für die Firmware an, die an die ausgewählten Geräte gesendet werden.
* `metrics` gibt die auszuführenden Abfragen an, mit denen der Status des Firmwareupdates gemeldet wird.
* Mit `targetCondition` werden die Geräte ausgewählt, die das Firmwareupdate erhalten sollen.
* Mit `priorty` wird die relative Priorität dieser Konfiguration gegenüber anderen Konfigurationen festgelegt.

Die Back-End-Anwendung nutzt den folgenden Code, um die Konfiguration zum Festlegen der gewünschten Eigenschaften zu erstellen:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Nachdem die Konfiguration erstellt wurde, überwacht die Anwendung das Firmwareupdate:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Eine Konfiguration meldet zwei Arten von Metriken:

* Systemmetriken, die melden, wie viele Geräte als Ziel angegeben sind und auf wie viele Geräte das Update angewendet wurde.
* Benutzerdefinierte Metriken: Diese werden über die Abfragen generiert, die Sie der Konfiguration hinzufügen. In diesem Tutorial melden die Abfragen, wie viele Geräte sich in den einzelnen Phasen des Updateprozesses befinden.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Antworten auf die Anforderung des Firmwareupgrades auf dem Gerät

Navigieren Sie zum Ordner **iot-hub/Tutorials/FirmwareUpdate** im heruntergeladenen Node.js-Beispielprojekt. Hier können Sie den simulierten Gerätecode anzeigen, mit dem die gewünschten Eigenschaften der Firmware verarbeitet werden, die von der Back-End-Anwendung gesendet werden. Öffnen Sie anschließend die Datei „SimulatedDevice.js“ in einem Text-Editor.

Die simulierte Geräteanwendung erstellt einen Handler für Updates der gewünschten Eigenschaften (**properties.desired.firmware**) auf dem Gerätezwilling. Im Handler werden einige grundlegende Überprüfungen der gewünschten Eigenschaften durchgeführt, bevor der Updateprozesses gestartet wird:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Aktualisieren der Firmware

Die Funktion **initiateFirmwareUpdateFlow** führt das Update aus. Für diese Funktion wird die Funktion **waterfall** verwendet, um die Phasen des Updateprozesses nacheinander auszuführen. In diesem Beispiel hat das Firmwareupdate vier Phasen. In der ersten Phase wird das Image heruntergeladen, in der zweiten Phase wird das Image anhand einer Prüfsumme überprüft, in der dritten Phase wird das Image angewendet, und in der letzten Phase wird das Gerät neu gestartet:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Während des Updateprozesses meldet das simulierte Gerät den Status mithilfe von gemeldeten Eigenschaften:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Der folgende Codeausschnitt veranschaulicht die Implementierung der Downloadphase. Während der Downloadphase verwendet das simulierte Gerät gemeldete Eigenschaften, um Statusinformationen zurück an die Back-End-Anwendung zu senden:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Ausführen des Beispiels

In diesem Abschnitt führen Sie zwei Beispielanwendungen aus, die nachverfolgt werden, während eine Back-End-Anwendung die Konfiguration zum Verwalten des Prozesses für das Firmwareupdate auf dem simulierten Gerät erstellt.

Zum Ausführen der Anwendung zur Simulation eines Geräts und der Back-End-Anwendung benötigen Sie die Verbindungszeichenfolgen für das Gerät und den Dienst. Sie haben sich die Verbindungszeichenfolgen notiert, als Sie am Anfang dieses Tutorials die Ressourcen erstellt haben.

Öffnen Sie zum Ausführen der Anwendung zur Simulation eines Geräts ein Shell- oder Befehlseingabefenster, und navigieren Sie im heruntergeladenen Node.js-Projekt zum Ordner **iot-hub/Tutorials/FirmwareUpdate**. Führen Sie anschließend die folgenden Befehle aus:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Öffnen Sie ein weiteres Shell- oder Befehlseingabefenster, um die Back-End-Anwendung auszuführen. Navigieren Sie anschließend im heruntergeladenen Node.js-Projekt zum Ordner **iot-hub/Tutorials/FirmwareUpdate**. Führen Sie anschließend die folgenden Befehle aus:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Im folgenden Screenshot ist die Ausgabe der simulierten Geräteanwendung und ihre Reaktion auf das Update der gewünschten Eigenschaften für die Firmware von der Back-End-Anwendung dargestellt:

![Simuliertes Gerät](./media/tutorial-firmware-update/SimulatedDevice.png)

Im folgenden Screenshot ist die Ausgabe der Back-End-Anwendung dargestellt und hervorgehoben, wie damit die Konfiguration zum Aktualisieren der gewünschten Eigenschaften für die Firmware erstellt wird:

![Screenshot: Ausgabe der Back-End-Anwendung](./media/tutorial-firmware-update/BackEnd1.png)

Im folgenden Screenshot ist die Ausgabe der Back-End-Anwendung dargestellt und hervorgehoben, wie die Metriken des Firmwareupdates vom simulierten Gerät überwacht werden:

![Back-End-Anwendung](./media/tutorial-firmware-update/BackEnd2.png)

Da automatische Gerätekonfigurationen zum Zeitpunkt der Erstellung und dann alle fünf Minuten ausgeführt werden, wird möglicherweise nicht jede Statusaktualisierung an die Back-End-Anwendung gesendet. Sie können die Metriken auch im Portal im Abschnitt **Automatic device management > IoT device configuration** (Automatische Geräteverwaltung > IoT-Gerätekonfiguration) für Ihren IoT Hub anzeigen:

![Anzeigen der Konfiguration im Portal](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das nächste Tutorial ausführen möchten, können Sie die Ressourcengruppe und die IoT Hub-Instanz beibehalten und später erneut verwenden.

Falls Sie die IoT Hub-Instanz nicht mehr benötigen, löschen Sie die Ressourcengruppe über das Portal. Wählen Sie hierzu die Ressourcengruppe **tutorial-iot-hub-rg** aus, die Ihre IoT Hub-Instanz enthält, und klicken Sie auf **Löschen**.

Verwenden Sie alternativ hierzu die CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie einen Firmwareupdateprozess für Ihre verbundenen Geräte implementieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie die Tools des Azure IoT Hub-Portals und die Azure CLI-Befehle verwenden, um die Gerätekonnektivität zu testen.

> [!div class="nextstepaction"]
> [Verwenden eines simulierten Geräts zum Testen der Konnektivität mit Ihrem IoT Hub](tutorial-connectivity.md)
