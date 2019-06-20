---
title: Bereitstellen von Modulen über die Befehlszeile – Azure IoT Edge | Microsoft-Dokumentation
description: Stellen Sie mithilfe der IoT-Erweiterung für die Azure CLI Module auf einem IoT Edge-Gerät bereit.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 766b51f208e7e8f4a49109e32864f2726b8ccd63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126391"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Bereitstellen von Azure IoT Edge-Modulen mit der Azure CLI

Nachdem Sie IoT Edge-Module mit Ihrer Geschäftslogik erstellen, sollten Sie sie auf Ihren Geräten für den Betrieb im Edge-Bereich bereitstellen. Wenn bei Ihnen mehrere Module gemeinsam Daten erfassen und verarbeiten, können Sie alle auf einmal bereitstellen und die Routingregeln, mit denen sie verbunden werden, deklarieren.

Die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) ist ein plattformübergreifendes Open-Source-Befehlszeilentool zum Verwalten von Azure-Ressourcen wie IoT Edge. Mit ihm können Sie Azure IoT Hub-Ressourcen, Instanzen des Device Provisioning Service und verknüpfte Hubs direkt ohne weitere Tools verwalten. Mit der neuen IoT-Erweiterung wird die Azure-Befehlszeilenschnittstelle um Features wie die Geräteverwaltung und um umfassende IoT Edge-Funktionen erweitert.

In diesem Artikel wird gezeigt, wie Sie ein JSON-Bereitstellungsmanifest erstellen und anschließend mithilfe dieser Datei die Bereitstellung per Push an ein IoT Edge-Gerät übertragen. Informationen zum Erstellen einer Bereitstellung, die basierend auf freigegebenen Tags auf mehrere Geräte ausgerichtet ist, finden Sie unter [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-monitor-cli.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement.
* Ein [IoT Edge-Gerät](how-to-register-device-cli.md) mit installierter IoT Edge-Runtime.
* Die [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.24 lauten. Verwenden Sie `az –-version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.
* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist vorhanden.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).

Wenn Sie Module mithilfe der Azure CLI bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als JSON-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

Hier sehen Sie ein Beispiel für ein grundlegendes Bereitstellungsmanifest mit einem Modul:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
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
   ```

## <a name="deploy-to-your-device"></a>Bereitstellen auf Ihrem Gerät

Zur Bereitstellung von Modulen auf Ihrem Gerät wenden Sie das Bereitstellungsmanifest an, das Sie mit den Modulinformationen konfiguriert haben.

Wechseln Sie in den Ordner, in dem das Bereitstellungsmanifest gespeichert ist. Wenn Sie eine der IoT Edge-Vorlagen für VS Code verwendet haben, verwenden Sie die Datei `deployment.json` im Ordner **config** im Projektmappenverzeichnis und nicht die Datei `deployment.template.json`.

Wenden Sie die Konfiguration mit dem folgenden Befehl auf ein IoT Edge-Gerät an:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

Beim Parameter für die Geräte-ID muss die Groß-/Kleinschreibung beachtet werden. Der content-Parameter verweist auf die gespeicherte Bereitstellungsmanifestdatei.

   ![az iot edge set-modules-Ausgabe](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Anzeigen von Modulen auf dem Gerät

Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie mit dem folgenden Befehl anzeigen:

Zeigen Sie die Module auf Ihrem IoT Edge-Gerät an:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

Beim Parameter für die Geräte-ID muss ist Groß-/Kleinschreibung beachtet werden.

   ![az iot hub module-identity list-Ausgabe](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [IoT Edge-Module im großen Maßstab bereitstellen und überwachen](how-to-deploy-monitor.md).
