---
title: Bereitstellen von Modulen aus Visual Studio Code – Azure IoT Edge | Microsoft-Dokumentation
description: Bereitstellen von Modulen auf einem IoT Edge-Gerät mithilfe von Visual Studio Code
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 91a074cf98291b105864a69730314efff3482254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126408"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Bereitstellen von Azure IoT Edge-Modulen mithilfe von Visual Studio Code

Nach der Erstellung von IoT Edge-Modulen mit Ihrer Geschäftslogik sollten Sie die Module auf Ihren Geräten für den Betrieb im Edge-Bereich bereitstellen. Wenn bei Ihnen mehrere Module gemeinsam Daten erfassen und verarbeiten, können Sie alle auf einmal bereitstellen und die Routingregeln deklarieren, über die sie verbunden werden.

In diesem Artikel wird gezeigt, wie Sie ein JSON-Bereitstellungsmanifest erstellen und anschließend mithilfe dieser Datei die Bereitstellung per Push an ein IoT Edge-Gerät übertragen. Informationen zum Erstellen einer Bereitstellung, die basierend auf freigegebenen Tags auf mehrere Geräte ausgerichtet ist, finden Sie unter [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement.
* Ein [IoT Edge-Gerät](how-to-register-device-portal.md) mit installierter IoT Edge-Runtime.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) für Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können – Vorschau](module-composition.md).

Wenn Sie Module mithilfe von Visual Studio Code bereitstellen möchten, speichern Sie das Bereitstellungsmanifest lokal als JSON-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

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

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT-Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihren IoT Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und den IoT Hub, auf dem Sie arbeiten, auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.

1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte).

   ![Erweitern des Abschnitts „Azure IoT Hub Devices“ (Azure IoT Hub-Geräte)](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klicken Sie auf **...** in der Überschrift des Abschnitts **Azure IoT Hub Devices** (Azure IoT Hub-Geräte). Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header.

1. Wählen Sie **Select IoT Hub** (IoT Hub auswählen) aus.

1. Wenn Sie nicht bei Ihrem Azure-Konto angemeldet sind, befolgen Sie die angezeigten Aufforderungen.

1. Wählen Sie Ihr Azure-Abonnement.

1. Wählen Sie Ihren IoT Hub aus.

## <a name="deploy-to-your-device"></a>Bereitstellen auf Ihrem Gerät

Zur Bereitstellung von Modulen auf Ihrem Gerät wenden Sie das Bereitstellungsmanifest an, das Sie mit den Modulinformationen konfiguriert haben.

1. Erweitern Sie im Visual Studio Code-Explorer den Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte).

1. Klicken Sie mit der rechten Maustaste auf das IoT Edge-Gerät, das Sie mit dem Bereitstellungsmanifest konfigurieren möchten.

    > [!TIP]
    > Vergewissern Sie sich, dass es sich bei dem ausgewählten Gerät um ein IoT Edge-Gerät handelt. Wählen Sie dazu das Gerät aus, um die Liste mit den Modulen zu erweitern, und vergewissern Sie sich, dass **$edgeHub** und **$edgeAgent** vorhanden sind. Jedes IoT Edge-Gerät verfügt über diese beiden Module.

1. Klicken Sie auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

1. Navigieren Sie zur gewünschten JSON-Datei mit dem Bereitstellungsmanifest, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen).

   ![Auswählen des Edge-Bereitstellungsmanifests](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Die Ergebnisse Ihrer Bereitstellung werden in VS Code ausgegeben. Erfolgreiche Bereitstellungen werden innerhalb weniger Minuten angewendet, sofern das Zielgerät ausgeführt wird und mit dem Internet verbunden ist.

## <a name="view-modules-on-your-device"></a>Anzeigen von Modulen auf dem Gerät

Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie im Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte) anzeigen. Klicken Sie zum Erweitern auf den Pfeil neben dem IoT Edge-Gerät. All derzeit ausgeführten Module werden angezeigt.

Falls Sie vor Kurzem neue Module auf einem Gerät bereitgestellt haben, zeigen Sie auf den Abschnittsheader **Azure IoT Hub Devices** (Azure IoT Hub-Geräte), und klicken Sie auf das Aktualisierungssymbol, um die Ansicht zu aktualisieren.

Klicken Sie mit der rechten Maustaste auf den Namen eines Moduls, um den Modulzwilling anzuzeigen und zu bearbeiten.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [IoT Edge-Module im großen Maßstab bereitstellen und überwachen](how-to-deploy-monitor.md).
