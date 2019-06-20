---
title: Bereitstellen eines benutzerdefinierten Images der Gerätesimulation – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung erfahren Sie, wie Sie ein benutzerdefiniertes Docker-Image der Gerätesimulationslösung in Azure bereitstellen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61448397"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Erstellen eines benutzerdefinierten Docker-Images der Gerätesimulation

Sie können die Gerätesimulationslösung ändern und benutzerdefinierte Features hinzufügen. Im Artikel [Serialisieren von Telemetriedaten mithilfe von Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) erfahren Sie beispielsweise, wie Sie der Lösung ein benutzerdefiniertes Gerät hinzufügen, das Protocol Buffers (Protobuf) verwendet, um Telemetriedaten zu senden. Nachdem Sie die Änderungen lokal getestet haben, stellen Sie sie im nächsten Schritt in Ihrer Gerätesimulationsinstanz in Azure bereit. Um diese Aufgabe abzuschließen, müssen Sie ein Docker-Image erstellen und bereitstellen, das den geänderten Dienst enthält.

Dies wird in den Schritten in dieser Anleitung erläutert:

1. Vorbereiten einer Entwicklungsumgebung
1. Generieren eines neuen Docker-Images
1. Konfigurieren der Gerätesimulation zur Verwendung des neuen Docker-Images
1. Ausführen einer Simulation mithilfe des neuen Images

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Schritte in dieser Anleitung ist Folgendes erforderlich:

* Eine bereitgestellte Instanz der [Gerätesimulation](quickstart-device-simulation-deploy.md)
* Docker. Laden Sie [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) für Ihre Plattform herunter.
* Ein [Docker-Hub-Konto](https://hub.docker.com/), in das Sie Ihre Docker-Images hochladen können. Erstellen Sie in Ihrem Docker-Hub-Konto ein öffentliches Repository mit dem Namen **device-simulation**.
* Eine geänderte und getestete [Gerätesimulationslösung](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) auf dem lokalen Computer. Sie können die Lösung z.B. so ändern, dass [Telemetriedaten mithilfe von Protocol Buffers serialisiert werden](iot-accelerators-device-simulation-protobuf.md).
* Eine Shell, die SSH ausführen kann. Wenn Sie Git für Windows installieren, können Sie die **Bash**-Shell verwenden, die in der Installation enthalten ist. Sie können ebenso [Azure Cloud Shell](https://shell.azure.com/) verwenden.

Bei den Anweisungen in diesem Artikel wird angenommen, dass Sie Windows verwenden. Wenn Sie ein anderes Betriebssystem verwenden, müssen Sie möglicherweise einige der Dateipfade und Befehle an Ihre Umgebung anpassen.

## <a name="create-a-new-docker-image"></a>Erstellen eines neuen Docker-Images

Um Ihre am Gerätesimulationsdienst vorgenommenen Änderungen bereitzustellen, müssen Sie die Build- und Bereitstellungsskripts im Ordner **scripts\docker** bearbeiten, um die Container in Ihr Docker-Hub-Konto hochzuladen.

### <a name="modify-the-docker-scripts"></a>Ändern der Docker-Skripts

Ändern Sie die Docker-Skripts **build.cmd**, **publish.cmd** und **run.cmd** im Ordner **scripts\docker** mit den Informationen Ihres Docker-Hub-Repositorys. Bei diesen Schritten wird vorausgesetzt, dass Sie das öffentliche Repository **device-simulation** erstellt haben:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Aktualisieren Sie die Datei **docker-compose.yml** wie folgt:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Konfigurieren der Lösung mit allen neuen Dateien

Wenn Sie neue Gerätemodelldateien hinzugefügt haben, müssen Sie diese explizit in der Lösung einfügen. Fügen Sie der Datei **services/services.csproj** einen Eintrag für jede zusätzliche Datei hinzu, die enthalten sein soll. Wenn Sie beispielsweise die Schrittanleitung [Serialisieren von Telemetriedaten mithilfe von Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) befolgt haben, fügen Sie die folgenden Einträge hinzu:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generieren neuer Docker-Images und Übertragen an Docker-Hub mithilfe von Push

Veröffentlichen Sie das neue Docker-Image über Ihr Docker-Hub-Konto in Docker-Hub:

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu der lokalen Kopie des Repositorys für die Gerätesimulation.

1. Navigieren Sie zum Ordner **docker**:

    ```cmd
    cd scripts\docker
    ```

1. Führen Sie den folgenden Befehl aus, um das Docker-Image zu erstellen:

    ```cmd
    build.cmd
    ```

1. Führen Sie den folgenden Befehl aus, um das Docker-Image in Ihrem Docker-Hub-Repository zu veröffentlichen. Melden Sie sich mit Ihren Docker-Hub-Anmeldeinformationen bei Docker an:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualisieren des Diensts

Führen Sie die folgenden Schritte aus, um den Gerätesimulationscontainer so zu aktualisieren, dass das benutzerdefinierte Image verwendet wird:

* Stellen Sie über SSH eine Verbindung mit dem virtuellen Computer her, der Ihre Gerätesimulationsinstanz hostet. Verwenden Sie die IP-Adresse und das Kennwort, die Sie im vorherigen Abschnitt notiert haben:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navigieren Sie zum Verzeichnis **/app**:

    ```sh
    cd /app
    ```

* Bearbeiten Sie die Datei **docker-compose.yml**:

    ```sh
    sudo nano docker-compose.yml
    ```

    Ändern Sie das **Image** so, dass es auf das benutzerdefinierte Image **device-simulation** verweist, das Sie in Ihr Docker-Hub-Repository hochgeladen haben:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Speichern Sie die Änderungen.

* Führen Sie den folgenden Befehl aus, um die Microservices neu zu starten:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Ausführen der Simulation

Nun können Sie eine Simulation mithilfe Ihrer benutzerdefinierten Gerätesimulationslösung ausführen:

1. Starten Sie die Webbenutzeroberfläche für die Gerätesimulation aus [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Konfigurieren Sie eine Simulation, und führen Sie sie auf der Webbenutzeroberfläche aus. Wenn Sie zuvor [Serialisieren von Telemetriedaten mithilfe von Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) abgeschlossen haben, können Sie das benutzerdefinierte Gerätemodell verwenden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie gelernt haben, wie Sie ein benutzerdefiniertes Image der Gerätesimulation bereitstellen, können Sie nun mit [Verwenden eines vorhandenen IoT Hubs mit dem Solution Accelerator für Gerätesimulation](iot-accelerators-device-simulation-choose-hub.md) fortfahren.