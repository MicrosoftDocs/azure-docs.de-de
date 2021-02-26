---
title: 'Bereitstellen von bedarfsgerechten Modulen mithilfe der Azure CLI: Azure IoT Edge'
description: Verwenden der IoT-Erweiterung für Azure CLI zum Erstellen automatischer Bereitstellungen für Gruppen von IoT Edge-Geräten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 4ecb1c3dc0e72523b19e3183e17306774b3ce164
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370375"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe der Azure CLI

Erstellen Sie eine **automatische IoT Edge-Bereitstellung** mithilfe der Azure-Befehlszeilenschnittstelle zum gleichzeitigen Verwalten laufender Bereitstellungen vieler Geräte. Automatische Bereitstellungen für IoT Edge sind Teil des Features [Automatische Geräteverwaltung](../iot-hub/iot-hub-automatic-device-management.md) von IoT Hub. Bereitstellungen sind dynamische Prozesse, mit denen Sie mehrere Module auf mehreren Geräten bereitstellen, Status und Integrität der Module nachverfolgen und bei Bedarf Änderungen vornehmen können.

Weitere Informationen finden Sie unter [Grundlegendes zu automatischen IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf](module-deployment-monitoring.md).

In diesem Artikel richten Sie die Azure CLI und die IoT-Erweiterung ein. Anschließend wird beschrieben, wie Sie mit den verfügbaren CLI-Befehlen Module auf IoT Edge-Geräten bereitstellen und den Status überwachen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement.
* Mindestens ein IoT Edge-Gerät.

  Wenn Sie kein IoT Edge-Gerät eingerichtet haben, können Sie eines in einem virtuellen Azure-Computer erstellen. Führen Sie die Schritte in einem der Schnellstartartikel zu [Erstellen eines virtuellen Linux-Geräts](quickstart-linux.md) oder [Erstellen eines virtuellen Windows-Geräts](quickstart.md) aus.

* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.
* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist vorhanden.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen finden Sie unter [Informationen zum Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](module-composition.md).

Wenn Sie Module mithilfe der Azure CLI bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als TXT-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

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

## <a name="layered-deployment"></a>Mehrstufige Bereitstellung

Bei der mehrstufigen Bereitstellung handelt es sich um eine Art automatischer Bereitstellung, die übereinander geschichtet werden kann. Weitere Informationen zu mehrstufigen Bereitstellungen finden Sie unter [Grundlegendes zu automatischen IoT Edge-Bereitstellungen für einzelne Geräte oder nach Bedarf](module-deployment-monitoring.md).

Mehrstufige Bereitstellungen können wie jede automatische Bereitstellung über die Azure-Befehlszeilenschnittstelle erstellt und verwaltet werden. Dabei gibt es nur wenige Unterschiede. Nach dem Erstellen verwenden Sie die Azure-Befehlszeilenschnittstelle für eine mehrstufige Bereitstellung genauso wie für jede andere Bereitstellung. Um eine mehrstufige Bereitstellung zu erstellen, fügen Sie dem Erstellungsbefehl das Flag `--layered` hinzu.

Der zweite Unterschied besteht im Aufbau des Bereitstellungsmanifests. Während standardmäßige automatische Bereitstellungen zusätzlich zu allen Benutzermodulen auch die Systemruntimemodule enthalten müssen, können mehrstufige Bereitstellungen nur Benutzermodule enthalten. Stattdessen ist für mehrstufige Bereitstellungen zusätzlich eine standardmäßige automatische Bereitstellung auf einem Gerät notwendig, um die erforderlichen Komponenten jedes IoT Edge-Geräts bereitzustellen, z. B. die Systemruntimemodule.

Hier sehen Sie ein Beispiel für ein grundlegendes Manifest einer mehrstufigen Bereitstellung mit einem Modul:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

Im vorherigen Beispiel wurde eine mehrstufige Bereitstellung gezeigt, bei der `properties.desired` für ein Modul festgelegt wird. Wenn diese mehrstufige Bereitstellung auf ein Gerät abzielt, auf dem dasselbe Modul bereits angewandt wurde, werden alle vorhandenen gewünschten Eigenschaften überschrieben. Um gewünschte Eigenschaften zu aktualisieren anstatt sie zu überschreiben, können Sie einen neuen Unterabschnitt definieren. Beispiel:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Weitere Informationen zum Konfigurieren von Modulzwillingen in mehrstufigen Bereitstellungen finden Sie unter [Mehrstufige Bereitstellung](module-deployment-monitoring.md#layered-deployment).

## <a name="identify-devices-using-tags"></a>Identifizieren von Geräten mithilfe von Tags

Bevor Sie eine Bereitstellung erstellen können, müssen Sie angeben können, welche Geräte Sie ansprechen möchten. Azure IoT Edge erkennt Geräte anhand von **Tags** im Gerätezwilling. Jedes Gerät kann mehrere Tags aufweisen, die Sie auf eine für Ihre Lösung sinnvolle Weise definieren können. Wenn Sie beispielsweise einen Campus mit intelligenten Gebäuden verwalten, können Sie einem Gerät etwa die folgenden Tags hinzufügen:

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

Weitere Informationen zu Gerätezwillingen und Tags finden Sie unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Erstellen einer Bereitstellung

Zum Bereitstellen von Modulen auf Ihren Zielgeräten erstellen Sie eine Bereitstellung, die sich aus dem Bereitstellungsmanifest sowie weiteren Parametern zusammensetzt.

Führen Sie zum Erstellen einer Bereitstellung den Befehl [az group deployment create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) aus:

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Verwenden Sie den gleichen Befehl mit dem Flag `--layered`, um eine mehrstufige Bereitstellung zu erstellen.

Für diesen Befehl werden die folgenden Parameter verwendet:

* **--layered:** ein optionales Flag zum Identifizieren der Bereitstellung als mehrstufige Bereitstellung.
* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub erstellt wird. Geben Sie Ihrer Bereitstellung einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`. Erforderlicher Parameter.
* **--content**: Dateipfad zur JSON-Datei mit dem Bereitstellungsmanifest Erforderlicher Parameter.
* **--hub-name**: Name des IoT-Hubs, in dem die Bereitstellung erstellt wird. Der Hub muss aus dem aktuellen Abonnement stammen. Ändern Sie Ihr aktuelles Abonnement mit dem Befehl `az account set -s [subscription name]`.
* **--labels**: Fügen Sie Bezeichnungen hinzu, um Ihre Bereitstellungen im Blick zu behalten. Bezeichnungen sind Name-Wert-Paare, die Ihre Bereitstellung beschreiben. Bezeichnungen verwenden JSON-Formatierung für die Namen und Werte. Zum Beispiel, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition**: Geben Sie eine Zielbedingung ein, um festzulegen, auf welche Geräte diese Bereitstellung ausgerichtet werden soll.  Die Bedingung basiert auf den Gerätezwillingstags oder auf den gemeldeten Gerätezwillingseigenschaften und muss dem Ausdrucksformat entsprechen. Beispiel: `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--priority** – ein positiver Integer. Wenn mindestens zwei Bereitstellungen auf dasselbe Gerät ausgerichtet sind, wird die Bereitstellung mit dem höchsten numerischen Wert für die Priorität angewendet.
* **--metrics:** Erstellen Sie Metriken, die die gemeldeten edgeHub-Eigenschaften abfragen, um den Status einer Bereitstellung nachzuverfolgen. Metriken nehmen eine JSON-Eingabe oder einen Dateipfad entgegen. Beispiel: `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

Informationen zur Überwachung einer Bereitstellung mit der Azure-Befehlszeilenschnittstelle finden Sie unter [Überwachen von IoT Edge-Bereitstellungen](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli).

## <a name="modify-a-deployment"></a>Ändern einer Bereitstellung

Wenn Sie Änderungen an einer Bereitstellung vornehmen, werden diese sofort auf alle Zielgeräte repliziert.

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Gerät die alte Zielbedingung nicht erfüllt, wohl aber die neue, und diese Bereitstellung die höchste Priorität für das Gerät hat, wird diese Bereitstellung auf das Gerät angewendet.
* Wenn ein Gerät, auf dem diese Bereitstellung gegenwärtig ausgeführt wird, die Zielbedingung nicht mehr erfüllt, wird diese Bereitstellung deinstalliert, und die Bereitstellung mit der nächsthöheren Priorität wird angewendet.
* Wenn ein Gerät, auf dem diese Bereitstellung gegenwärtig ausgeführt wird, diese Zielbedingung wie auch die Zielbedingungen aller anderen Bereitstellungen nicht erfüllt, erfolgt keine Änderung auf dem Gerät. Das Gerät führt die aktuellen Module im aktuellen Zustand weiter aus, wird aber nicht mehr als Teil dieser Bereitstellung verwaltet. Sobald es die Zielbedingung einer anderen Bereitstellung erfüllt, wird diese Bereitstellung deinstalliert, und die neue Bereitstellung wird angewendet.

Sie können den Inhalt einer Bereitstellung nicht aktualisieren. Dies gilt auch für die im Bereitstellungsmanifest definierten Module und Routen. Wenn Sie den Inhalt einer Bereitstellung aktualisieren möchten, erstellen Sie eine neue Bereitstellung, die dieselben Geräte mit höherer Priorität als Ziel hat. Sie können bestimmte Eigenschaften eines vorhandenen Moduls ändern, einschließlich der Zielbedingung, Bezeichnungen, Metriken und Priorität.

Führen Sie zum Aktualisieren einer Bereitstellung den Befehl [az group deployment update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) aus:

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

Für diesen Befehl werden die folgenden Parameter verwendet:

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub vorhanden ist
* **--hub-name** – Name des IoT Hub, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.
* **--set**: Aktualisieren einer Eigenschaft in der Bereitstellung. Sie können die folgenden Eigenschaften aktualisieren:
  * targetCondition – z.B. `targetCondition=tags.location.state='Oregon'`
  * Bezeichnungen
  * priority
* **--add:** Fügen Sie der Bereitstellung eine neue Eigenschaft hinzu, einschließlich Zielbedingungen oder Bezeichnungen.
* **--remove:** Entfernen Sie eine vorhandene Eigenschaft, einschließlich Zielbedingungen oder Bezeichnungen.

## <a name="delete-a-deployment"></a>Löschen einer Bereitstellung

Wenn Sie eine Bereitstellung löschen, übernehmen alle Geräte die Bereitstellung mit der jeweils nächsthöheren Priorität. Wenn Ihre Geräte die Zielbedingung keiner anderen Bereitstellung erfüllen, werden die Module beim Löschen der Bereitstellung nicht entfernt.

Führen Sie zum Löschen einer Bereitstellung den Befehl [az group deployment delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) aus:

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

Für diesen Befehl werden die folgenden Parameter verwendet:

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub vorhanden ist
* **--hub-name** – Name des IoT Hub, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zum [Bereitstellen von Modulen auf IoT Edge-Geräten](module-deployment-monitoring.md).