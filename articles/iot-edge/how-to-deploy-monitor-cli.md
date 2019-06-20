---
title: Erstellen automatischer Bereitstellungen über die Befehlszeile – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden der IoT-Erweiterung für Azure CLI zum Erstellen automatischer Bereitstellungen für Gruppen von IoT Edge-Geräten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f93d9eaefe18dd012a639cd26636b56b9eb09249
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595143"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe der Azure CLI

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Mit Azure IoT Edge können Sie Analysen in die Edge-Ebene verlagern und erhalten eine Cloudschnittstelle, um Ihre IoT Edge-Geräte remote zu verwalten und zu überwachen. Die Möglichkeit zur Remoteverwaltung von Geräten wird immer wichtiger, weil Lösungen für das Internet der Dinge immer umfangreicher und komplexer werden. Azure IoT Edge wurde entwickelt, um Ihre Unternehmensziele unabhängig davon zu unterstützen, wie viele Geräte Sie hinzufügen.

Sie können einzelne Geräte verwalten und die dafür erforderlichen Modelle einzeln bereitstellen. Wenn Sie jedoch Änderungen an Geräten in großem Umfang vornehmen möchten, können Sie eine **automatische IoT Edge-Bereitstellung** erstellen, das Teil der automatischen Geräteverwaltung in IoT Hub ist. Bereitstellungen sind dynamische Prozesse, mit denen Sie mehrere Module gleichzeitig auf mehreren Geräten bereitstellen, Status und Integrität der Module nachverfolgen und bei Bedarf Änderungen vornehmen können. 

In diesem Artikel richten Sie die Azure CLI und die IoT-Erweiterung ein. Anschließend wird beschrieben, wie Sie mit den verfügbaren CLI-Befehlen Module auf IoT Edge-Geräten bereitstellen und den Status überwachen.

## <a name="cli-prerequisites"></a>Voraussetzungen für die Befehlszeilenschnittstelle

* Ein [IoT-Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement 
* [IoT Edge-Geräte](how-to-register-device-cli.md) mit installierter IoT Edge-Runtime
* Die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.24 lauten. Verwenden Sie `az –-version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt. 
* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist vorhanden.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).

Wenn Sie Module mithilfe der Azure CLI bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als TXT-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen. 

Hier sehen Sie ein Beispiel für ein grundlegendes Bereitstellungsmanifest mit einem Modul:

   ```json
   {
        "content": {
         "modulesContent": {
           "$edgeAgent": {
             "properties.desired": {
               "schemaVersion": "1.0",
               "runtime": {
                 "type": "docker",
                 "settings": {
                   "minDockerVersion": "v1.25",
                   "loggingOptions": "",
                   "registryCredentials": {
                     "registryName": {
                       "username": "",
                       "password": "",
                       "address": ""
                     }
                   }
               },
               "systemModules": {
                 "edgeAgent": {
                   "type": "docker",
                   "settings": {
                     "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                     "createOptions": "{}"
                   }
                 },
                 "edgeHub": {
                   "type": "docker",
                   "status": "running",
                   "restartPolicy": "always",
                   "settings": {
                     "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                     "createOptions": "{}"
                   }
                 }
               },
               "modules": {
                 "tempSensor": {
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
               "schemaVersion": "1.0",
               "routes": {
                   "route": "FROM /* INTO $upstream"
               },
               "storeAndForwardConfiguration": {
                 "timeToLiveSecs": 7200
               }
             }
           },
           "tempSensor": {
             "properties.desired": {}
           }
         }
       }
   }
   ```

## <a name="identify-devices-using-tags"></a>Identifizieren von Geräten mithilfe von Tags

Bevor Sie eine Bereitstellung erstellen können, müssen Sie angeben können, welche Geräte Sie ansprechen möchten. Azure IoT Edge erkennt Geräte anhand von **Tags** im Gerätezwilling. Jedes Gerät kann mehrere Tags aufweisen, und Sie können diese auf jede beliebige Weise definieren, die für Ihre Lösung sinnvoll erscheint. Wenn Sie beispielsweise einen Campus mit intelligenten Gebäuden verwalten, können Sie einem Gerät etwa die folgenden Tags hinzufügen:

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

Verwenden Sie den folgenden Befehl, um eine Bereitstellung zu erstellen:

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
   ```

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub erstellt wird. Geben Sie Ihrer Bereitstellung einen eindeutigen Namen, der bis zu 128 Kleinbuchstaben umfasst. Verwenden Sie dabei weder Leerzeichen noch die folgenden ungültigen Zeichen: `& ^ [ ] { } \ | " < > /`.
* **--hub-name**: Name des IoT-Hubs, in dem die Bereitstellung erstellt wird. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.
* **--content**: Dateipfad zur JSON-Datei mit dem Bereitstellungsmanifest 
* **--labels**: Fügen Sie Bezeichnungen hinzu, um Ihre Bereitstellungen im Blick zu behalten. Bezeichnungen sind Name-Wert-Paare, die Ihre Bereitstellung beschreiben. Bezeichnungen verwenden JSON-Formatierung für die Namen und Werte. Zum Beispiel, `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition**: Geben Sie eine Zielbedingung ein, um festzulegen, auf welche Geräte diese Bereitstellung ausgerichtet werden soll. Die Bedingung basiert auf den Gerätezwillingstags oder auf den gemeldeten Gerätezwillingseigenschaften und muss dem Ausdrucksformat entsprechen. Beispiel: `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** – ein positiver Integer. Wenn mindestens zwei Bereitstellungen auf dasselbe Gerät ausgerichtet sind, wird die Bereitstellung mit dem höchsten numerischen Wert für die Priorität angewendet.

## <a name="monitor-a-deployment"></a>Überwachen einer Bereitstellung

Sie können den Inhalt einer Bereitstellung mit dem folgenden Befehl anzeigen:

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub vorhanden ist
* **--hub-name**: Der Name des IoT-Hubs, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

Überprüfen Sie die Bereitstellung im Befehlsfenster. Die Eigenschaft **metrics** enthält eine Anzahl für jede Metrik, die von den einzelnen Hubs ausgewertet wird:

* **targetedCount**: Eine Systemmetrik, die die Anzahl von Gerätezwillingen in IoT Hub angibt, die die Zielbedingung erfüllen.
* **appliedCount**: Eine Systemmetrik, die die Anzahl von Geräten angibt, auf deren Modulzwillinge in IoT Hub die Bereitstellungsinhalte angewendet wurden.
* **reportedSuccessfulCount**: Eine Gerätemetrik, die die Anzahl von Edge-Geräten in der Bereitstellung angibt, für die von der IoT Edge-Clientruntime eine Erfolgsmeldung ausgegeben wurde.
* **reportedFailedCount**: Eine Gerätemetrik, die die Anzahl von Edge-Geräten in der Bereitstellung angibt, für die von der IoT Edge-Clientruntime eine Fehlermeldung ausgegeben wurde.

Sie können mit dem folgenden Befehl eine Liste der Geräte-IDs oder Objekte für jede der Metriken anzeigen:

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub vorhanden ist
* **--metric-id**: Der Name der Metrik, für die Sie eine Liste der Geräte-IDs anzeigen möchten, z.B. `reportedFailedCount`.
* **--hub-name** – Name des IoT Hub, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

## <a name="modify-a-deployment"></a>Ändern einer Bereitstellung

Wenn Sie Änderungen an einer Bereitstellung vornehmen, werden diese sofort auf alle Zielgeräte repliziert. 

Wenn Sie die Zielbedingung ändern, erfolgen die nachfolgend aufgeführten Anpassungen:

* Wenn ein Gerät die alte Zielbedingung nicht erfüllt, wohl aber die neue, und diese Bereitstellung die höchste Priorität für das Gerät hat, wird diese Bereitstellung auf das Gerät angewendet. 
* Wenn ein Gerät, auf dem diese Bereitstellung gegenwärtig ausgeführt wird, die Zielbedingung nicht mehr erfüllt, wird diese Bereitstellung deinstalliert, und die Bereitstellung mit der nächsthöheren Priorität wird angewendet. 
* Wenn ein Gerät, auf dem diese Bereitstellung gegenwärtig ausgeführt wird, diese Zielbedingung wie auch die Zielbedingungen aller anderen Bereitstellungen nicht erfüllt, erfolgt keine Änderung auf dem Gerät. Das Gerät führt die aktuellen Module im aktuellen Zustand weiter aus, wird aber nicht mehr als Teil dieser Bereitstellung verwaltet. Sobald es die Zielbedingung einer anderen Bereitstellung erfüllt, wird diese Bereitstellung deinstalliert, und die neue Bereitstellung wird angewendet. 

Verwenden Sie den folgenden Befehl, um eine Bereitstellung zu aktualisieren:

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub vorhanden ist
* **--hub-name**: Der Name des IoT-Hubs, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.
* **--set**: Aktualisieren einer Eigenschaft in der Bereitstellung. Sie können die folgenden Eigenschaften aktualisieren:
  * targetCondition – z.B. `targetCondition=tags.location.state='Oregon'`
  * Bezeichnungen 
  * priority


## <a name="delete-a-deployment"></a>Löschen einer Bereitstellung

Wenn Sie eine Bereitstellung löschen, übernehmen alle Geräte die Bereitstellung mit der jeweils nächsthöheren Priorität. Wenn Ihre Geräte die Zielbedingung keiner anderen Bereitstellung erfüllen, werden die Module beim Löschen der Bereitstellung nicht entfernt. 

Verwenden Sie zum Löschen einer Bereitstellung den folgenden Befehl:

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```

* **--deployment-id**: Der Name der Bereitstellung, die im IoT-Hub vorhanden ist
* **--hub-name**: Der Name des IoT-Hubs, in dem die Bereitstellung vorhanden ist. Der Hub muss aus dem aktuellen Abonnement stammen. Wechseln Sie mit dem Befehl `az account set -s [subscription name]` zum gewünschten Abonnement.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über das [Bereitstellen von Modulen auf Edge-Geräten](module-deployment-monitoring.md).
