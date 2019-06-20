---
title: 'Übersetzen von Modbus-Protokollen mithilfe von Gateways: Azure IoT Edge | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie es Geräten mit Verwendung von ModBus-TCP die Kommunikation mit Azure IoT Hub ermöglichen, indem Sie ein IoT Edge-Gatewaygerät erstellen.
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61248014"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Verbinden von ModBus-TCP-Geräten über ein IoT Edge-Gerätegateway

Wenn Sie IoT-Geräte, für die ModBus-TCP- oder -RTU-Protokolle genutzt werden, mit einem Azure IoT Hub verbinden möchten, sollten Sie ein IoT Edge-Gerät als Gateway einsetzen. Das Gatewaygerät liest Daten von Ihren ModBus-Geräten und kommuniziert sie dann über ein unterstütztes Protokoll weiter an die Cloud.

![Verbindung von ModBus-Geräten mit dem IoT-Edge-Gateway](./media/deploy-modbus-gateway/diagram.png)

In diesem Artikel wird beschrieben, wie Sie Ihr eigenes Containerimage für ein ModBus-Modul erstellen (Sie können auch ein vorgefertigtes Beispiel verwenden) und dann auf dem IoT Edge-Gerät bereitstellen, das als Ihr Gateway fungiert.

In diesem Artikel wird davon ausgegangen, dass Sie das ModBus-TCP-Protokoll verwenden. Weitere Informationen zur Konfiguration des Moduls zur Unterstützung von ModBus-RTU finden Sie im Projekt [ModBus-Modul für Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) auf GitHub.

## <a name="prerequisites"></a>Voraussetzungen
* Ein Azure IoT Edge-Gerät. Eine exemplarische Vorgehensweise zur Einrichtung finden Sie unter [Bereitstellen von Azure IoT Edge unter Windows](quickstart.md) oder [Linux](quickstart-linux.md).
* Die Verbindungszeichenfolge des primären Schlüssels für das IoT Edge-Gerät
* Ein physisches oder simuliertes ModBus-Gerät, das ModBus-TCP unterstützt.

## <a name="prepare-a-modbus-container"></a>Vorbereiten eines ModBus-Containers

Wenn Sie die Funktionalität des ModBus-Gateways testen möchten, können Sie ein von Microsoft bereitgestelltes Beispielmodul verwenden. Sie können auf das Modul über Azure Marketplace [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview) oder über den Image-URI **mcr.microsoft.com/azureiotedge/modbus:1.0** zugreifen.

Falls Sie Ihr eigenes Modul erstellen und für Ihre Umgebung anpassen möchten, können Sie das Open-Source-Projekt [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (ModBus-Modul für Azure IoT Edge) auf GitHub nutzen. Befolgen Sie die Anweisungen in diesem Projekt, um Ihr eigenes Containerimage zu erstellen. Wenn Sie ein eigenes Containerimage erstellen, lesen Sie [Entwickeln von C#-Modulen in Visual Studio](how-to-visual-studio-develop-csharp-module.md) oder [Entwickeln von Modulen in Visual Studio Code](how-to-vs-code-develop-module.md). Diese Artikel bieten Anweisungen zum Erstellen neuer Module und zum Veröffentlichen von Containerimages in einer Registrierung.

## <a name="try-the-solution"></a>Testen der Lösung

Dieser Abschnitt führt Sie durch die Bereitstellung des Modbus-Beispielmoduls von Microsoft auf Ihrem IoT Edge-Gerät.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem IoT Hub.

2. Navigieren Sie zu **IoT Edge**, und klicken Sie auf Ihr IoT Edge-Gerät.

3. Wählen Sie **Module festlegen** aus.

4. Fügen Sie das ModBus-Modul hinzu:

   1. Klicken Sie auf **Hinzufügen** und anschließend auf **IoT Edge-Modul**.

   2. Geben Sie im Feld **Name** den Text „modbus“ ein.

   3. Geben Sie im Feld **Image** den Image-URI des Beispielcontainers ein: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Aktivieren Sie das Kontrollkästchen **Aktivieren**, um die gewünschten Eigenschaften des Modulzwillings zu aktualisieren.

   5. Kopieren Sie den folgenden JSON-Code in das Textfeld. Ändern Sie den Wert von **SlaveConnection** in die IPv4-Adresse Ihres ModBus-Geräts.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Wählen Sie **Speichern** aus.

5. Wenn Sie zum Schritt **Module hinzufügen** zurückgekehrt sind, wählen Sie **Weiter** aus.

7. Kopieren Sie im Schritt **Routen angeben** den unten angegebenen JSON-Code in das Textfeld. Für diese Route werden alle Nachrichten, die vom ModBus-Modul gesammelt wurden, an den IoT Hub gesendet. Bei dieser Route ist „modbusOutput“ der Endpunkt, der vom ModBus-Modul zum Ausgeben von Daten verwendet wird, und „upstream“ ist ein spezielles Ziel, das den IoT Edge-Hub zum Senden von Nachrichten an den IoT Hub anweist.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Klicken Sie auf **Weiter**.

9. Klicken Sie im Schritt für die **Bereitstellungsüberprüfung** auf **Übermitteln**.

10. Kehren Sie zur Seite mit Gerätedetails zurück, und wählen Sie **Aktualisieren** aus. Das neue Modul **modbus** sollte nun zusammen mit der IoT Edge-Runtime ausgeführt werden.

## <a name="view-data"></a>Anzeigen von Daten
Zeigen Sie die Daten an, die über das ModBus-Modul eingehen:
```cmd/sh
iotedge logs modbus
```

Die vom Gerät gesendeten Telemetriedaten können Sie auch anzeigen, indem Sie [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (die Azure IoT Hub-Toolkit-Erweiterung für Visual Studio Code, früher: Azure IoT Toolkit-Erweiterung) verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Nutzung von IoT Edge-Geräten als Gateways finden Sie unter [Erstellen eines IoT Edge-Geräts, das als transparentes Gateway fungiert](./how-to-create-transparent-gateway.md).
- Weitere Informationen zur Funktionsweise von IoT Edge-Modulen finden Sie unter [Grundlegendes zu Azure IoT Edge-Modulen](iot-edge-modules.md).
