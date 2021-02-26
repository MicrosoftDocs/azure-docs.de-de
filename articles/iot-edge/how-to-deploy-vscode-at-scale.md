---
title: Bedarfsabhängiges Bereitstellen von Modulen mithilfe von Visual Studio Code – Azure IoT Edge
description: Verwenden Sie die IoT-Erweiterung für Visual Studio Code zum Erstellen automatischer Bereitstellungen für Gruppen von IoT Edge-Geräten.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9ae7009468b548cb9386fd405abfbe110d62f9a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377515"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Bedarfsabhängiges Bereitstellen von IoT Edge-Modulen mithilfe Visual Studio Code

Sie können eine **automatische IoT Edge-Bereitstellung** mithilfe von Visual Studio Code erstellen, um laufende Bereitstellungen für viele Geräte gleichzeitig zu verwalten. Automatische Bereitstellungen für IoT Edge sind Teil des Features [Automatische Geräteverwaltung](../iot-hub/iot-hub-automatic-device-management.md) von IoT Hub. Bereitstellungen sind dynamische Prozesse, mit denen Sie mehrere Module auf mehreren Geräten bereitstellen können. Sie können auch den Status und die Integrität der Module nachverfolgen und bei Bedarf Änderungen vornehmen.

Weitere Informationen finden Sie unter [Grundlegendes zu automatischen IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf](module-deployment-monitoring.md).

In diesem Artikel richten Sie Visual Studio Code und die IoT-Erweiterung ein. Anschließend erfahren Sie, wie Sie Module auf einer Reihe von IoT Edge-Geräten bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement.
* Mindestens ein IoT Edge-Gerät.

  Wenn Sie kein IoT Edge-Gerät eingerichtet haben, können Sie eines in einem virtuellen Azure-Computer erstellen. Führen Sie die Schritte in einem der Schnellstarts zum [Erstellen eines virtuellen Linux-Geräts](quickstart-linux.md) oder [Erstellen eines virtuellen Windows-Geräts](quickstart.md) aus.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) für Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihrem Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und den IoT Hub, auf dem Sie arbeiten, auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.

1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub**.

1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header.

1. Wählen Sie **Select IoT Hub** (IoT Hub auswählen) aus.

1. Wenn Sie bei Ihrem Azure-Konto nicht angemeldet sind, befolgen Sie die angezeigten Aufforderungen.

1. Wählen Sie Ihr Azure-Abonnement.

1. Wählen Sie Ihren IoT Hub aus.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, in dem beschrieben wird, welche Module bereitgestellt werden müssen. Außerdem wird beschrieben, wie der Datenfluss zwischen den Modulen und den gewünschten Eigenschaften der Modulzwillinge verläuft. Weitere Informationen finden Sie unter [Informationen zum Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

Wenn Sie Module mithilfe von Visual Studio Code bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als JSON-Datei. Sie müssen den Speicherort angeben, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

Hier sehen Sie ein Beispiel für ein grundlegendes Bereitstellungsmanifest mit einem Modul:

>[!NOTE]
>In diesem Beispielbereitstellungsmanifest wird die Schemaversion 1.1 für den IoT Edge-Agent und den Hub verwendet. Die Schemaversion 1.1 wurde zusammen mit der IoT Edge Version 1.0.10 veröffentlicht und ermöglicht Features wie Startreihenfolge für Module und Priorisierung von Routen.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Wenn Sie ermitteln müssen, welche IoT Edge-Geräte Sie zurzeit konfigurieren können, führen Sie den Befehl **IoT Edge: Geräteinformationen abrufen** aus.

## <a name="identify-devices-with-target-conditions"></a>Identifizieren von Geräten mit Zielbedingungen

Zum Identifizieren der IoT Edge-Geräte, die die Bereitstellung erhalten sollen, müssen Sie eine Zielbedingung angeben. Eine Zielbedingung ist erfüllt, wenn eine Geräte-ID, ein Tagwert oder ein gemeldeter Eigenschaftswert den angegebenen Kriterien entspricht.

Tags konfigurieren Sie im Gerätezwilling. Hier ist ein Beispiel für einen Gerätezwilling mit Tags:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Dieses Gerät empfängt eine Bereitstellung, wenn die Zielbedingung für die Bereitstellung einen Ausdruck enthält, der mit einem der Tagwerte übereinstimmt, z. B. `tag.location.building = '20'`.

Wenn Sie ein bestimmtes Gerät unabhängig von seinen Tags oder anderen Werten als Ziel festlegen möchten, geben Sie einfach die `deviceId` für die Zielbedingung an.

Hier sind einige weitere Beispiele:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' ODER deviceId = 'linuxprod2' ODER deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' UND tags.location = 'westus2'
* tags.environment = 'prod' ODER tags.location = 'westus2'
* tags.operator = 'John' UND tags.environment = 'prod' UND NICHT deviceId = 'linuxprod1'

Weitere Informationen finden Sie unter [Zielbedingung](module-deployment-monitoring.md#target-condition). Weitere Informationen zu Gerätezwillingen und Tags finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Bearbeiten des Gerätezwillings

Sie können den Gerätezwilling in Visual Studio Code bearbeiten, um die Tags zu konfigurieren. Wählen Sie im Menü **Ansicht** die Option **Befehlspalette** aus, und führen Sie den Befehl **IoT Edge: Gerätezwilling bearbeiten** aus. Wählen Sie Ihr IoT Edge-Gerät aus. Dann wird der Gerätezwilling angezeigt.

In diesem Beispiel wurden keine Tags definiert. Ersetzen Sie den aktuellen leeren Abschnitt `"tags": {}` durch ihre eigene Tagdefinition.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Nachdem Sie die lokale Datei gespeichert haben, führen Sie den Befehl **IoT Edge: Gerätezwilling aktualisieren** aus.

## <a name="create-deployment-at-scale"></a>Erstellen einer bedarfsabhängigen Bereitstellung

Nachdem Sie das Bereitstellungsmanifest und Tags im Gerätezwilling konfiguriert haben, können Sie mit der Bereitstellung beginnen.

1. Wählen Sie im Menü **Ansicht** die Option **Befehlspalette** und dann den Befehl **Azure IoT Edge: Create Deployment at Scale** (Bedarfsgerechte Bereitstellung erstellen) aus.

1. Navigieren Sie zur gewünschten JSON-Datei mit dem Bereitstellungsmanifest, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen).

1. Geben Sie bei der entsprechenden Aufforderung Werte an, beginnend mit der **Bereitstellungs-ID**.

   ![Angeben einer Bereitstellungs-ID](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Geben Sie Werte für die folgenden Parameter an:

  | Parameter | Beschreibung |
  | --- | --- |
  | Bereitstellungs-ID | Der Name der Bereitstellung, die im IoT Hub erstellt werden soll. Geben Sie Ihrer Bereitstellung einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`. |
  | Zielbedingung | Geben Sie eine Zielbedingung ein, um festzulegen, auf welche Geräte diese Bereitstellung angewendet werden soll.  Die Bedingung basiert auf den Gerätezwillingstags oder auf den gemeldeten Gerätezwillingseigenschaften und muss dem Ausdrucksformat entsprechen. Beispiel: `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Priorität |  Eine positive ganze Zahl. Wenn dasselbe Gerät das Ziel für mindestens zwei Bereitstellungen ist, wird die Bereitstellung mit dem höchsten numerischen Wert als Priorität („Priority“) angewendet. |

  Nach Angabe der Priorität sollte die Ausgabe im Terminal ähnlich wie in der nachstehenden Abbildung aussehen:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Überwachen und Ändern von Bereitstellungen

Sie können Bereitstellungen über das [Azure-Portal](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) oder die [Azure-Befehlszeilenschnittstelle](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) (Azure CLI) überwachen, ändern und löschen. Beide liefern Metriken für Ihre Bereitstellungen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zum [Bereitstellen von Modulen auf IoT Edge-Geräten](module-deployment-monitoring.md).