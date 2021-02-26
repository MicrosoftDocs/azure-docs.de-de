---
title: 'Tutorial: Ändern der Module für die Azure IoT Edge-Livevideoanalyse'
description: 'In diesem Tutorial wird veranschaulicht, wie Sie die Gatewaymodule für die Livevideoanalyse ändern und erstellen, die von der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ verwendet werden.'
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: e5236bb7034ae4d5f86ab2f8f965bdbfbc3d6ccd
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832044"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Tutorial: Ändern und Erstellen der Gatewaymodule für die Livevideoanalyse

In diesem Tutorial wird veranschaulicht, wie Sie den Code der IoT Edge-Module für die LVA-Module (Livevideoanalyse) ändern.

In den vorherigen Tutorials werden vordefinierte Images der Module verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen der Schritte in diesem Tutorial benötigen Sie Folgendes:

* [Node.js](https://nodejs.org/en/download/) v10 oder höher
* [Visual Studio Code](https://code.visualstudio.com/Download) mit installierter [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)-Erweiterung
* [Docker](https://www.docker.com/products/docker-desktop)-Engine
* Eine Instanz von [Azure Container Registry](../../container-registry/index.yml) zum Hosten Ihrer Versionen der Module.
* Ein [Azure Media Services](../../media-services/index.yml)-Konto. Wenn Sie die vorherigen Tutorials durchgearbeitet haben, können Sie das zuvor erstellte Konto wiederverwenden.

## <a name="clone-the-repository"></a>Klonen des Repositorys

Wenn Sie das Repository nicht bereits geklont haben, können Sie den folgenden Befehl verwenden, um es an einem geeigneten Speicherort auf Ihrem lokalen Computer zu klonen:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Öffnen Sie den lokalen Repositoryordner *live-video-analytics* mit VS Code.

## <a name="edit-the-deploymentamd64json-file"></a>Bearbeiten der Datei „deployment.amd64.json“

1. Erstellen Sie den Ordner *ref-apps/lva-edge-iot-central-gateway/storage* in der lokalen Kopie des Repositorys **lva-gateway**, falls Sie dies nicht bereits getan haben. Dieser Ordner wird von Git ignoriert, um zu verhindern, dass Sie versehentlich vertrauliche Informationen einchecken.
1. Kopieren Sie die Datei *deployment.amd64.json* aus dem Ordner *setup* in den Ordner *storage*.
1. Öffnen Sie in VS Code die Datei *storage/deployment.amd64.json*.
1. Bearbeiten Sie den Abschnitt `registryCredentials`, um Ihre Anmeldeinformationen für Azure Container Registry hinzuzufügen.
1. Bearbeiten Sie den Modulabschnitt `LvaEdgeGatewayModule`, um den Namen Ihres Images und den Namen Ihres AMS-Kontos unter `env:amsAccountName:value` hinzuzufügen.
1. Bearbeiten Sie den Modulabschnitt `lvaYolov3`, und fügen Sie den Namen Ihres Images hinzu.
1. Bearbeiten Sie den Modulabschnitt `lvaEdge`, und fügen Sie den Namen Ihres Images hinzu.
1. Weitere Informationen zum Durchführen der Konfiguration finden Sie unter [Erstellen einer Videoanalyseanwendung in Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md).

## <a name="build-the-code"></a>Erstellen des Codes

1. Bevor Sie versuchen, den Code zum ersten Mal zu erstellen, sollten Sie im VS Code-Terminal den Befehl `npm install` ausführen. Mit diesem Befehl werden die erforderlichen Pakete installiert und die Setupskripts ausgeführt.

    > [!TIP]
    > Löschen Sie beim Pullen einer neueren Version des GitHub-Repositorys den Ordner *node_modules*, und führen Sie `npm install` erneut aus.

1. Bearbeiten Sie die Datei *./setup/imageConfig.json*, um das Image zu aktualisieren, das basierend auf dem Namen Ihrer Containerregistrierung benannt ist:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Verwenden Sie das VS Code-Terminal, um den Befehl `docker login [your server].azurecr.io` auszuführen. Verwenden Sie die gleichen Anmeldeinformationen, die Sie im Bereitstellungsmanifest für die Module angegeben haben.

1. Verwenden Sie das VS Code-Terminal, um den Befehl **npm version patch** auszuführen. Mit diesem Buildskript werden die Images in Ihrer Containerregistrierung bereitgestellt. An der Ausgabe im Fenster des VS Code-Terminals können Sie ablesen, ob der Buildvorgang erfolgreich war.

1. Die Version des Images **LvaEdgeGatewayModule** wird jeweils inkrementiert, wenn der Buildvorgang abgeschlossen wurde. Sie müssen diese Version in der Bereitstellungsmanifestdatei verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Anwendung nicht mehr benötigen, können Sie alle erstellten Ressourcen wie folgt entfernen:

1. Navigieren Sie in der IoT Central-Anwendung zur Seite **Ihre Anwendung** im Abschnitt **Verwaltung**. Wählen Sie anschließend die Option **Löschen**.
1. Löschen Sie im Azure-Portal die Ressourcengruppe **lva-rg**.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ und den IoT Edge-LVA-Modulen vertraut gemacht haben, ist es ratsam, sich über das folgende Thema zu informieren:

> [!div class="nextstepaction"]
> [Erstellen von Lösungen für den Einzelhandel mit Azure IoT Central](overview-iot-central-retail.md)