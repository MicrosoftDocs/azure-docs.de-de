---
title: Synchronisieren des Gerätestatus über Azure IoT Hub | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Gerätezwillinge zum Konfigurieren Ihrer Geräte aus der Cloud verwenden und den Status und Konformitätsdaten von Ihren Geräten empfangen.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 9ec2c51f01d6b13f33bc2d537a8f73a6721967d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572523"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Tutorial: Konfigurieren Ihrer Geräte über einen Back-End-Dienst

Zusätzlich zum Empfangen von Telemetriedaten von Ihren Geräten müssen Sie die Geräte ggf. über Ihren Back-End-Dienst konfigurieren. Wenn Sie eine gewünschte Konfiguration an Ihre Geräte senden, kann es sein, dass Sie auch Status- und Konformitätsaktualisierungen von diesen Geräten erhalten möchten. Beispielsweise können Sie einen Zieltemperaturbereich für den Betrieb eines Geräts festlegen oder Informationen zur Firmwareversion von Ihren Geräten erfassen.

Zum Synchronisieren von Zustandsinformationen zwischen einem Gerät und einem IoT Hub verwenden Sie _Gerätezwillinge_. Ein [Gerätezwilling](iot-hub-devguide-device-twins.md) ist ein JSON-Dokument, das einem bestimmten Gerät zugeordnet ist und von IoT Hub in der Cloud gespeichert wird, wo es [abgefragt](iot-hub-devguide-query-language.md) werden kann. Ein Gerätezwilling enthält _gewünschte Eigenschaften_, _gemeldete Eigenschaften_ und _Tags_. Eine gewünschte Eigenschaft wird von einer Back-End-Anwendung festgelegt und von einem Gerät gelesen. Eine gemeldete Eigenschaft wird von einem Gerät festgelegt und von einer Back-End-Anwendung gelesen. Ein Tag wird von einer Back-End-Anwendung festgelegt und niemals an ein Gerät gesendet. Sie verwenden Tags, um Ihre Geräte zu organisieren. In diesem Tutorial wird veranschaulicht, wie Sie gewünschte und gemeldete Eigenschaften zum Synchronisieren von Statusinformationen verwenden:

![Zusammenfassung zu Zwillingen](media/tutorial-device-twins/DeviceTwins.png)

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * Erstellen eines IoT Hub und Hinzufügen eines Testgeräts zur Identitätsregistrierung
> * Verwenden von gewünschten Eigenschaften zum Senden von Statusinformationen an Ihr simuliertes Gerät
> * Verwenden von gemeldeten Eigenschaften zum Empfangen von Statusinformationen von Ihrem simulierten Gerät

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

Damit Sie dieses Tutorial durcharbeiten können, muss Ihr Azure-Abonnement einen IoT Hub mit einem Gerät enthalten, das der Geräteidentitätsregistrierung hinzugefügt wurde. Mit dem Eintrag in der Geräteidentitätsregistrierung kann für das simulierte Gerät, das Sie in diesem Tutorial ausführen, eine Verbindung mit Ihrem Hub hergestellt werden.

Falls Sie in Ihrem Abonnement noch keinen IoT Hub eingerichtet haben, können Sie hierfür das folgende CLI-Skript verwenden. In diesem Skript wird der Name **tutorial-iot-hub** für den IoT Hub verwendet. Ersetzen Sie diesen Namen durch Ihren eigenen eindeutigen Namen, wenn Sie es ausführen. Mit dem Skript werden die Ressourcengruppe und der Hub in der Region **Central US** (USA, Mitte) erstellt. Sie können dies auch in eine Region ändern, die in Ihrer Nähe liegt. Das Skript ruft Ihre IoT Hub-Dienstverbindungszeichenfolge ab, die Sie im Back-End-Beispiel verwenden, um eine Verbindung mit Ihrem IoT Hub herzustellen:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-iot

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --name $hubname --policy-name service -o table

```

In diesem Tutorial wird ein simuliertes Gerät mit dem Namen **MyTwinDevice** verwendet. Mit dem folgenden Skript wird dieses Gerät Ihrer Identitätsregistrierung hinzugefügt und die Verbindungszeichenfolge abgerufen:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Senden von Statusinformationen

Sie nutzen die gewünschten Eigenschaften, um Statusinformationen von einer Back-End-Anwendung an ein Gerät zu senden. In diesem Abschnitt wird Folgendes veranschaulicht:

* Empfangen und Verarbeiten von gewünschten Eigenschaften auf einem Gerät
* Senden von gewünschten Eigenschaften von einer Back-End-Anwendung

Navigieren Sie zum Anzeigen des Beispielcodes für das simulierte Gerät, über den die gewünschten Eigenschaften empfangen werden, im heruntergeladenen Node.js-Beispielprojekt zum Ordner **iot-hub/Tutorials/DeviceTwins**. Öffnen Sie anschließend die Datei „SimulatedDevice.js“ in einem Text-Editor.

In den folgenden Abschnitten wird der Code beschrieben, der auf dem simulierten Gerät ausgeführt und mit dem auf die Änderungen der gewünschten Eigenschaften geantwortet wird, die von der Back-End-Anwendung gesendet werden:

### <a name="retrieve-the-device-twin-object"></a>Abrufen des Gerätezwillingsobjekts

Mit dem folgenden Code wird eine Verbindung mit Ihrem IoT Hub über eine Geräteverbindungszeichenfolge hergestellt:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

Mit dem folgenden Code wird ein Zwilling aus dem Clientobjekt abgerufen:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Beispiel für gewünschte Eigenschaften

Sie können die gewünschten Eigenschaften so strukturieren, wie dies für Ihre Anwendung am besten geeignet ist. In diesem Beispiel wird eine Eigenschaft der obersten Ebene mit dem Namen **fanOn** verwendet, und die übrigen Eigenschaften werden zu separaten **Komponenten** gruppiert. Im folgenden JSON-Codeausschnitt ist die Struktur der gewünschten Eigenschaften in diesem Tutorial dargestellt:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Erstellen von Handlern

Sie können Handler für Aktualisierungen von gewünschten Eigenschaften erstellen, die auf unterschiedlichen Ebenen der JSON-Hierarchie auf Aktualisierungen antworten. Dieser Handler erkennt beispielsweise alle Änderungen gewünschter Eigenschaften, die über eine Back-End-Anwendung an das Gerät gesendet werden. Die Variable **delta** enthält die gewünschten Eigenschaften, die vom Back-End der Lösung gesendet werden:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Der folgende Handler reagiert nur auf Änderungen, die an der gewünschten Eigenschaft **fanOn** vorgenommen werden:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Handler für mehrere Eigenschaften

In den obigen Beispielen für den JSON-Code von gewünschten Eigenschaften enthält der Knoten **climate** unter **components** die beiden Eigenschaften **minTemperature** und **maxTemperature**.

Im lokalen Zwillingsobjekt (**twin**) eines Geräts wird ein vollständiger Satz mit gewünschten und gemeldeten Eigenschaften gespeichert. Mit den vom Back-End gesendeten **delta**-Daten wird unter Umständen nur eine Teilmenge der gewünschten Eigenschaften aktualisiert. Im folgenden Codeausschnitt wird – wenn das simulierte Gerät nur eine Aktualisierung für eines der beiden Elemente **minTemperature** und **maxTemperature** erhält – der Wert im lokalen Zwilling für den anderen Wert verwendet, um das Gerät zu konfigurieren:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

Im lokalen Zwillingsobjekt (**twin**) wird ein vollständiger Satz mit gewünschten und gemeldeten Eigenschaften gespeichert. Mit den vom Back-End gesendeten **delta**-Daten wird unter Umständen nur eine Teilmenge der gewünschten Eigenschaften aktualisiert.

### <a name="handle-insert-update-and-delete-operations"></a>Verarbeiten von Einfüge-, Aktualisierungs- und Löschvorgängen

Mit den vom Back-End gesendeten gewünschten Eigenschaften wird nicht angegeben, welcher Vorgang für eine bestimmte gewünschte Eigenschaft durchgeführt wird. In Ihrem Code muss der Vorgang aus dem aktuellen Satz mit den lokal gespeicherten gewünschten Eigenschaften und den vom Hub gesendeten Änderungen abgeleitet werden.

Der folgende Codeausschnitt zeigt, wie das simulierte Gerät Einfüge-, Aktualisierungs- und Löschvorgänge in der Liste mit den **Komponenten** in den gewünschten Eigenschaften verarbeitet. Sie sehen, wie Sie **NULL**-Werte verwenden, um anzugeben, dass eine Komponente gelöscht werden sollte:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Senden von gewünschten Eigenschaften an ein Gerät vom Back-End

Sie haben gesehen, wie ein Gerät Handler für den Empfang von Aktualisierungen für gewünschte Eigenschaften implementiert. In diesem Abschnitt wird veranschaulicht, wie Sie Änderungen gewünschter Eigenschaften von einer Back-End-Anwendung an ein Gerät senden.

Navigieren Sie zum Anzeigen des Beispielcodes für das simulierte Gerät, über den die gewünschten Eigenschaften empfangen werden, im heruntergeladenen Node.js-Beispielprojekt zum Ordner **iot-hub/Tutorials/DeviceTwins**. Öffnen Sie anschließend die Datei „ServiceClient.js“ in einem Text-Editor.

Im folgenden Codeausschnitt wird gezeigt, wie Sie eine Verbindung mit der Geräteidentitätsregistrierung herstellen und auf den Zwilling für ein bestimmtes Gerät zugreifen:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Im folgenden Codeausschnitt sind verschiedene *Patches* für gewünschte Eigenschaften dargestellt, die von der Back-End-Anwendung an das Gerät gesendet werden:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Im folgenden Codeausschnitt ist zu sehen, wie die Back-End-Anwendung die Aktualisierung einer gewünschten Eigenschaft an ein Gerät sendet:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Ausführen der Anwendungen

In diesem Abschnitt führen Sie zwei Beispielanwendungen aus, um zu verfolgen, wie eine Back-End-Anwendung Aktualisierungen für gewünschte Eigenschaften an eine Anwendung zur Simulation eines Geräts sendet.

Zum Ausführen der Anwendung zur Simulation eines Geräts und der Back-End-Anwendung benötigen Sie die Verbindungszeichenfolgen für das Gerät und den Dienst. Sie haben sich die Verbindungszeichenfolgen notiert, als Sie am Anfang dieses Tutorials die Ressourcen erstellt haben.

Öffnen Sie zum Ausführen der Anwendung zur Simulation eines Geräts ein Shell- oder Befehlseingabefenster, und navigieren Sie im heruntergeladenen Node.js-Projekt zum Ordner **iot-hub/Tutorials/DeviceTwins**. Führen Sie anschließend die folgenden Befehle aus:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Öffnen Sie ein weiteres Shell- oder Befehlseingabefenster, um die Back-End-Anwendung auszuführen. Navigieren Sie anschließend im heruntergeladenen Node.js-Projekt zum Ordner **iot-hub/Tutorials/DeviceTwins**. Führen Sie anschließend die folgenden Befehle aus:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Im folgenden Screenshot ist die Ausgabe der Anwendung zur Simulation eines Geräts dargestellt, und die Verarbeitung einer Aktualisierung der gewünschten Eigenschaft **maxTemperature** ist hervorgehoben. Sie sehen, wie der Handler der obersten Ebene und die Komponente „climate“ ausgeführt werden:

![Screenshot, der zeigt, wie der Handler der obersten Ebene und die Handler der Komponente „climate“ ausgeführt werden](./media/tutorial-device-twins/SimulatedDevice1.png)

Im folgenden Screenshot ist die Ausgabe der Back-End-Anwendung dargestellt, und das Senden einer Aktualisierung der gewünschten Eigenschaft **maxTemperature** ist hervorgehoben:

![Screenshot, der die Ausgabe der Back-End-Anwendung zeigt und auf dem das Senden einer Aktualisierung hervorgehoben ist](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Empfangen von Statusinformationen

Ihre Back-End-Anwendung empfängt Statusinformationen von einem Gerät als gemeldete Eigenschaften. Ein Gerät legt die gemeldeten Eigenschaften fest und sendet sie an Ihren Hub. Eine Back-End-Anwendung kann die aktuellen Werte der gemeldeten Eigenschaften vom Gerätezwilling lesen, der in Ihrem Hub gespeichert ist.

### <a name="send-reported-properties-from-a-device"></a>Senden von gemeldeten Eigenschaften von einem Gerät

Sie können Aktualisierungen für Werte von gemeldeten Eigenschaften als Patch senden. Der folgende Codeausschnitt enthält eine Vorlage für den Patch, der vom simulierten Gerät gesendet wird. Das simulierte Gerät aktualisiert die Felder im Patch, bevor dieser an den Hub gesendet wird:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

Für das simulierte Gerät wird die folgende Funktion verwendet, um den Patch mit den gemeldeten Eigenschaften an den Hub zu senden:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Verarbeiten von gemeldeten Eigenschaften

Eine Back-End-Anwendung greift über den Gerätezwilling auf die aktuellen Werte der gemeldeten Eigenschaften für ein Gerät zu. Im folgenden Codeausschnitt ist dargestellt, wie die Back-End-Anwendung die Werte der gemeldeten Eigenschaften für das simulierte Gerät liest:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Ausführen der Anwendungen

In diesem Abschnitt führen Sie zwei Beispielanwendungen aus, um zu verfolgen, wie eine Anwendung zur Simulation eines Geräts Aktualisierungen für gemeldete Eigenschaften an eine Back-End-Anwendung sendet.

Sie führen dieselben beiden Beispielanwendungen wie bei dem Vorgang aus, mit dem Sie das Senden von gewünschten Eigenschaften an ein Gerät getestet haben.

Zum Ausführen der Anwendung zur Simulation eines Geräts und der Back-End-Anwendung benötigen Sie die Verbindungszeichenfolgen für das Gerät und den Dienst. Sie haben sich die Verbindungszeichenfolgen notiert, als Sie am Anfang dieses Tutorials die Ressourcen erstellt haben.

Öffnen Sie zum Ausführen der Anwendung zur Simulation eines Geräts ein Shell- oder Befehlseingabefenster, und navigieren Sie im heruntergeladenen Node.js-Projekt zum Ordner **iot-hub/Tutorials/DeviceTwins**. Führen Sie anschließend die folgenden Befehle aus:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Öffnen Sie ein weiteres Shell- oder Befehlseingabefenster, um die Back-End-Anwendung auszuführen. Navigieren Sie anschließend im heruntergeladenen Node.js-Projekt zum Ordner **iot-hub/Tutorials/DeviceTwins**. Führen Sie anschließend die folgenden Befehle aus:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

Im folgenden Screenshot ist die Ausgabe der Anwendung zur Simulation eines Geräts dargestellt, und es ist hervorgehoben, wie eine Aktualisierung für gemeldete Eigenschaften an Ihren Hub gesendet werden:

![Simuliertes Gerät](./media/tutorial-device-twins/SimulatedDevice2.png)

Im folgenden Screenshot ist die Ausgabe der Back-End-Anwendung zu sehen, und es ist hervorgehoben, wie eine Aktualisierung für gemeldete Eigenschaften von einem Gerät empfangen und verarbeitet wird:

![Back-End-Anwendung](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das nächste Tutorial ausführen möchten, können Sie die Ressourcengruppe und die IoT Hub-Instanz beibehalten und später erneut verwenden.

Falls Sie die IoT Hub-Instanz nicht mehr benötigen, löschen Sie die Ressourcengruppe über das Portal. Wählen Sie hierzu die Ressourcengruppe **tutorial-iot-hub-rg** aus, die Ihre IoT Hub-Instanz enthält, und klicken Sie auf **Löschen**.

Verwenden Sie alternativ hierzu die CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie Statusinformationen zwischen Ihren Geräten und Ihrem IoT Hub synchronisieren. Fahren Sie mit dem nächsten Tutorial fort, in dem beschrieben wird, wie Sie Gerätezwillinge zum Implementieren eines Prozesses zur Aktualisierung der Firmware verwenden.

> [!div class="nextstepaction"]
> [Implementieren eines Updateprozesses für die Gerätefirmware](tutorial-firmware-update.md)
