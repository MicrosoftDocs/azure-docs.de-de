---
title: 'Bereitstellen von Modulen aus Visual Studio Code: Azure IoT Edge'
description: Verwenden Sie Visual Studio Code mit den Azure IoT Tools, um ein IoT Edge-Modul entsprechend der Konfiguration durch ein Bereitstellungsmanifest per Push von Ihrer IoT Hub-Instanz auf Ihr IoT Edge-Gerät zu übertragen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a6415aa55a67b37d9564398eb77dacb48cf16f0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378110"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Bereitstellen von Azure IoT Edge-Modulen mithilfe von Visual Studio Code

Nachdem Sie IoT Edge-Module mit Ihrer Geschäftslogik erstellen, sollten Sie sie auf Ihren Geräten für den Betrieb im Edge-Bereich bereitstellen. Wenn bei Ihnen mehrere Module gemeinsam Daten erfassen und verarbeiten, können Sie alle auf einmal bereitstellen und die Routingregeln, mit denen sie verbunden werden, deklarieren.

In diesem Artikel wird gezeigt, wie Sie ein JSON-Bereitstellungsmanifest erstellen und anschließend mithilfe dieser Datei die Bereitstellung per Push an ein IoT Edge-Gerät übertragen. Informationen zum Erstellen einer Bereitstellung für mehrere Geräte – basierend auf deren freigegebenen Tags – finden Sie unter [Bereitstellen von IoT Edge-Modulen im großen Maßstab mithilfe von Visual Studio Code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement.
* Ein IoT Edge Gerät

  Wenn Sie kein IoT Edge-Gerät eingerichtet haben, können Sie eines in einem virtuellen Azure-Computer erstellen. Führen Sie die Schritte in einem der Schnellstarts zum [Erstellen eines virtuellen Linux-Geräts](quickstart-linux.md) oder [Erstellen eines virtuellen Windows-Geräts](quickstart.md) aus.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) für Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).

Wenn Sie Module mithilfe von Visual Studio Code bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als JSON-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

Hier sehen Sie ein Beispiel für ein grundlegendes Bereitstellungsmanifest mit einem Modul:

>[!NOTE]
>In diesem Beispielbereitstellungsmanifest wird die Schemaversion 1.1 für den IoT Edge-Agent und den Hub verwendet. Die Schemaversion 1.1 wurde zusammen mit der IoT Edge Version 1.0.10 veröffentlicht und ermöglicht Features wie Startreihenfolge für Module und Priorisierung von Routen.

   ```json
   {
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
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
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
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihren IoT Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und den IoT Hub, auf dem Sie arbeiten, auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.

1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub**.

   ![Erweitern des Azure IoT Hub-Abschnitts](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header.

1. Wählen Sie **Select IoT Hub** (IoT Hub auswählen) aus.

1. Wenn Sie nicht bei Ihrem Azure-Konto angemeldet sind, befolgen Sie die angezeigten Aufforderungen.

1. Wählen Sie Ihr Azure-Abonnement.

1. Wählen Sie Ihren IoT Hub aus.

## <a name="deploy-to-your-device"></a>Bereitstellen auf Ihrem Gerät

Zur Bereitstellung von Modulen auf Ihrem Gerät wenden Sie das Bereitstellungsmanifest an, das Sie mit den Modulinformationen konfiguriert haben.

1. Erweitern Sie in der Visual Studio Code-Exploreransicht den Abschnitt **Azure IoT Hub**, und erweitern Sie dann den Knoten **Geräte**.

1. Klicken Sie mit der rechten Maustaste auf das IoT Edge-Gerät, das Sie mit dem Bereitstellungsmanifest konfigurieren möchten.

    > [!TIP]
    > Vergewissern Sie sich, dass es sich bei dem ausgewählten Gerät um ein IoT Edge-Gerät handelt. Wählen Sie dazu das Gerät aus, um die Liste mit den Modulen zu erweitern, und vergewissern Sie sich, dass **$edgeHub** und **$edgeAgent** vorhanden sind. Jedes IoT Edge-Gerät verfügt über diese beiden Module.

1. Klicken Sie auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

1. Navigieren Sie zur gewünschten JSON-Datei mit dem Bereitstellungsmanifest, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen).

   ![Auswählen des Edge-Bereitstellungsmanifests](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Die Ergebnisse Ihrer Bereitstellung werden in VS Code ausgegeben. Erfolgreiche Bereitstellungen werden innerhalb weniger Minuten angewendet, sofern das Zielgerät ausgeführt wird und mit dem Internet verbunden ist.

## <a name="view-modules-on-your-device"></a>Anzeigen von Modulen auf dem Gerät

Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie im Abschnitt **Azure IoT Hub** anzeigen. Klicken Sie zum Erweitern auf den Pfeil neben dem IoT Edge-Gerät. All derzeit ausgeführten Module werden angezeigt.

Falls Sie vor Kurzem neue Module auf einem Gerät bereitgestellt haben, zeigen Sie auf den Abschnittsheader **Azure IoT Hub Devices** (Azure IoT Hub-Geräte), und klicken Sie auf das Aktualisierungssymbol, um die Ansicht zu aktualisieren.

Klicken Sie mit der rechten Maustaste auf den Namen eines Moduls, um den Modulzwilling anzuzeigen und zu bearbeiten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im Artikel [Bedarfsgerechtes Bereitstellen und Überwachen von IoT Edge-Modulen mithilfe des Azure-Portals](how-to-deploy-at-scale.md).
