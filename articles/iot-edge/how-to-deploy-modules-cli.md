---
title: Bereitstellen von Modulen über die Azure CLI-Befehlszeile – Azure IoT Edge
description: Über die Azure CLI mit der IoT-Erweiterung können Sie – entsprechend der Konfiguration durch ein Bereitstellungsmanifest – ein IoT Edge-Modul per Push von Ihrem IoT Hub auf Ihr IoT Edge-Gerät übertragen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 6e4782ad25886c4121742634e9e73f62ff4f0a2c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370239"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Bereitstellen von Azure IoT Edge-Modulen mit der Azure CLI

Nachdem Sie IoT Edge-Module mit Ihrer Geschäftslogik erstellen, sollten Sie sie auf Ihren Geräten für den Betrieb im Edge-Bereich bereitstellen. Wenn bei Ihnen mehrere Module gemeinsam Daten erfassen und verarbeiten, können Sie alle auf einmal bereitstellen und die Routingregeln, mit denen sie verbunden werden, deklarieren.

Die [Azure-Befehlszeilenschnittstelle](/cli/azure) ist ein plattformübergreifendes Open-Source-Befehlszeilentool zum Verwalten von Azure-Ressourcen wie IoT Edge. Mit ihm können Sie Azure IoT Hub-Ressourcen, Instanzen des Device Provisioning Service und verknüpfte Hubs direkt ohne weitere Tools verwalten. Mit der neuen IoT-Erweiterung wird die Azure-Befehlszeilenschnittstelle um Features wie die Geräteverwaltung und um umfassende IoT Edge-Funktionen erweitert.

In diesem Artikel wird gezeigt, wie Sie ein JSON-Bereitstellungsmanifest erstellen und anschließend mithilfe dieser Datei die Bereitstellung per Push an ein IoT Edge-Gerät übertragen. Informationen zum Erstellen einer Bereitstellung, die basierend auf freigegebenen Tags auf mehrere Geräte ausgerichtet ist, finden Sie unter [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-cli-at-scale.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement.
* Ein IoT Edge Gerät

  Wenn Sie kein IoT Edge-Gerät eingerichtet haben, können Sie eines in einem virtuellen Azure-Computer erstellen. Führen Sie die Schritte in einem der Schnellstartartikel zu [Erstellen eines virtuellen Linux-Geräts](quickstart-linux.md) oder [Erstellen eines virtuellen Windows-Geräts](quickstart.md) aus.

* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.
* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist vorhanden.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).

Wenn Sie Module mithilfe der Azure CLI bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als JSON-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

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
              "version": "1.0",
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
          "schemaVersion": "1.1",
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

## <a name="deploy-to-your-device"></a>Bereitstellen auf Ihrem Gerät

Zur Bereitstellung von Modulen auf Ihrem Gerät wenden Sie das Bereitstellungsmanifest an, das Sie mit den Modulinformationen konfiguriert haben.

Wechseln Sie in den Ordner, in dem das Bereitstellungsmanifest gespeichert ist. Wenn Sie eine der IoT Edge-Vorlagen für VS Code verwendet haben, verwenden Sie die Datei `deployment.json` im Ordner **config** im Projektmappenverzeichnis und nicht die Datei `deployment.template.json`.

Wenden Sie die Konfiguration mit dem folgenden Befehl auf ein IoT Edge-Gerät an:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Beim Parameter für die Geräte-ID wird die Groß-/Kleinschreibung berücksichtigt. Der content-Parameter verweist auf die gespeicherte Bereitstellungsmanifestdatei.

   ![az iot edge set-modules-Ausgabe](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Anzeigen von Modulen auf dem Gerät

Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie mit dem folgenden Befehl anzeigen:

Zeigen Sie die Module auf Ihrem IoT Edge-Gerät an:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Beim Parameter für die Geräte-ID wird die Groß-/Kleinschreibung berücksichtigt.

   ![az iot hub module-identity list-Ausgabe](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [IoT Edge-Module im großen Maßstab bereitstellen und überwachen](how-to-deploy-at-scale.md).