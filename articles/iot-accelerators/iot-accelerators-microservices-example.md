---
title: Ändern und erneutes Bereitstellen eines Microservice – Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie einen Microservice in der Remoteüberwachungslösung ändern und erneut bereitstellen.
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447623"
---
# <a name="customize-and-redeploy-a-microservice"></a>Anpassen und erneutes Bereitstellen eines Microservice

In diesem Tutorial erfahren Sie, wie Sie einen der [Microservices](https://azure.com/microservices) in der Remoteüberwachungslösung bearbeiten, ein Image Ihres Microservice erstellen, das Image in Ihrem Docker-Hub bereitstellen und es anschließend in der Remoteüberwachungslösung verwenden. Dieses Konzept wird im vorliegenden Tutorial anhand eines einfachen Szenarios veranschaulicht, in dem Sie eine Microservice-API aufrufen und die Statusmeldung von „Alive and Well“ in „New Edits Made Here!“ ändern.

Die in der Remoteüberwachungslösung verwendeten Microservices basieren auf Docker-Images, die aus einem Docker-Hub gepullt werden. 

In diesem Tutorial lernen Sie Folgendes:

>[!div class="checklist"]
> * Bearbeiten und Erstellen eines Microservice in der Remoteüberwachungslösung
> * Erstellen eines Docker-Images
> * Pushen eines Docker-Images an Ihren Docker-Hub
> * Pullen des neuen Docker-Images
> * Visualisieren der Änderungen 

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

>[!div class="checklist"]
> * [Lokales Bereitstellen des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Ein Docker-Konto](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) (zum Anzeigen der API-Antwort)

## <a name="call-the-api-and-view-response-status"></a>Aufrufen der API und Anzeigen des Antwortstatus

In diesem Teil rufen Sie die Standard-API des IoT Hub-Manager-Microservice auf. Die API gibt eine Statusmeldung zurück, die Sie später ändern, indem Sie den Microservice anpassen.

1. Vergewissern Sie sich, dass die Remoteüberwachungslösung lokal auf Ihrem Computer ausgeführt wird.
2. Navigieren Sie zu dem Speicherort, an den Sie Postman heruntergeladen haben, und öffnen Sie die Anwendung.
3. Geben Sie in Postman die folgende GET-Anforderung ein: `http://localhost:8080/iothubmanager/v1/status`.
4. In der Rückgabe sollte Folgendes angezeigt werden: "Status": "OK:Alive and Well".

    ![Postman-Meldung „Alive and Well“](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Ändern des Status und Erstellen des Images

Ändern Sie nun die Statusmeldung des Iot Hub-Manager-Microservice in „New Edits Made Here!“, und erstellen Sie das Docker-Image mit diesem neuen Status neu. Informationen zur Vorgehensweise bei Problemen finden Sie im Abschnitt zur [Problembehandlung](#Troubleshoot).

1. Öffnen Sie bei Bedarf Ihr Terminal, und wechseln Sie zu dem Verzeichnis, in dem Sie die Remoteüberwachungslösung geklont haben. 
1. Wechseln Sie zum Verzeichnis „azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services“.
1. Öffnen Sie „StatusService.cs“ in einem beliebigen Text-Editor oder in einer beliebigen IDE. 
1. Suchen Sie den folgenden Code:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    Ändern Sie ihn in den folgenden Code, und speichern Sie die Datei.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Kehren Sie zu Ihrem Terminal zurück, und wechseln Sie zu folgendem Verzeichnis: „azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker“.
6. Geben Sie Folgendes ein, um Ihr neues Docker-Image zu erstellen:

    ```sh
    sh build
    ```
    
    Für Windows benötigen Sie folgende Eingabe:
    
    ```cmd
    ./build.cmd
    ```

7. Geben Sie Folgendes ein, um sich zu vergewissern, dass das neue Image erfolgreich erstellt wurde:

    ```cmd/sh
    docker images 
    ```

Das Repository sollte „azureiotpcs/iothub-manager-dotnet“ lauten.

![Erfolgreiche Erstellung des Docker-Images](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Markieren und Pushen des Images
Bevor Sie Ihr neues Docker-Image an einen Docker-Hub pushen, erwartet Docker, dass Sie Ihre Images markieren. Informationen zur Vorgehensweise bei Problemen finden Sie im Abschnitt zur [Problembehandlung](#Troubleshoot).

1. Geben Sie Folgendes ein, um die Image-ID des erstellten Docker-Images zu suchen:

    ```cmd/sh
    docker images
    ```

2. Geben Sie Folgendes ein, um Ihr Image mit dem Typ „testing“ zu markieren:

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Geben Sie Folgendes ein, um Ihr soeben markiertes Image an Ihren Docker-Hub zu pushen:

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Navigieren Sie in Ihrem Internetbrowser zu Ihrem [Docker Hub](https://hub.docker.com/), und melden Sie sich an.
5. Ihr soeben gepushtes Docker-Image sollte nun in Ihrem Docker-Hub angezeigt werden.
![Docker-Image im Docker-Hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aktualisieren Ihrer Remoteüberwachungslösung
Nun müssen Sie Ihre lokale Datei „docker-compose.yml“ aktualisieren, um Ihr neues Docker-Image aus Ihrem Docker-Hub zu pullen. Informationen zur Vorgehensweise bei Problemen finden Sie im Abschnitt zur [Problembehandlung](#Troubleshoot).

1. Kehren Sie zum Terminal zurück, und wechseln Sie zu folgendem Verzeichnis: „azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local“.
2. Öffnen Sie „docker-compose.yml“ in einem beliebigen Text-Editor oder in einer beliebigen IDE.
3. Suchen Sie den folgenden Code:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    Ändern Sie ihn wie in der folgenden Abbildung gezeigt, und speichern Sie die Datei.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Anzeigen des neuen Antwortstatus
Stellen Sie abschließend erneut eine lokale Instanz der Remoteüberwachungslösung bereit, und sehen Sie sich die neue Statusantwort in Postman an.

1. Kehren Sie zu Ihrem Terminal zurück, und wechseln Sie zu folgendem Verzeichnis: „azure-iot-pcs-remote-monitoring-dotnet/scripts/local“.
2. Geben Sie den folgenden Befehl in das Terminal ein, um Ihre lokale Instanz der Remoteüberwachungslösung zu starten:

    ```cmd/sh
    docker-compose up
    ```

3. Navigieren Sie zu dem Speicherort, an den Sie Postman heruntergeladen haben, und öffnen Sie die Anwendung.
4. Geben Sie in Postman die folgende GET-Anforderung ein: `http://localhost:8080/iothubmanager/v1/status`. Nun sollte Folgendes angezeigt werden: "Status": "OK: New Edits Made Here!".

![Postman-Meldung: „New Edits Made Here!“](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Problembehandlung

Probleme lassen sich unter Umständen durch Entfernen der Docker-Images und -Container auf dem lokalen Computer beheben.

1. Wenn Sie alle Container entfernen möchten, müssen Sie zunächst alle aktiven Container beenden. Öffnen Sie Ihr Terminal, und geben Sie Folgendes ein:

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Öffnen Sie zum Entfernen aller Images Ihr Terminal, und geben Sie Folgendes ein: 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Geben Sie Folgendes ein, um zu prüfen, ob auf dem Computer noch Container vorhanden sind:

    ```cmd/sh
    docker ps -aq 
    ```

    Wenn Sie alle Container erfolgreich entfernt haben, wird nichts angezeigt.

4. Geben Sie Folgendes ein, um zu prüfen, ob auf dem Computer noch Images vorhanden sind:

    ```cmd/sh
    docker images
    ```

    Wenn Sie alle Images erfolgreich entfernt haben, wird nichts angezeigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Bearbeiten und Erstellen eines Microservice in der Remoteüberwachungslösung
> * Erstellen eines Docker-Images
> * Pushen eines Docker-Images an Ihren Docker-Hub
> * Pullen des neuen Docker-Images
> * Visualisieren der Änderungen 

Versuchen Sie als Nächstes, [den Gerätesimulator-Microservice in der Remoteüberwachungslösung anzupassen](iot-accelerators-microservices-example.md).

Weitere Entwicklerinformationen zur Remoteüberwachungslösung finden Sie hier:

* [Referenzhandbuch für Entwickler](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

