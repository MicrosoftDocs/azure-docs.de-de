---
title: 'Tutorial: Erstellen einer IoT Edge-Instanz für die Videoanalyse in Azure IoT Central (Intel NUC)'
description: 'In diesem Tutorial wird veranschaulicht, wie Sie eine IoT Edge-Instanz für die Videoanalyse erstellen, die mit der Anwendungsvorlage „Videoanalyse: Objekt- und Bewegungserkennung“ verwendet werden kann.'
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: e2e97b857f648390ec017a529115b23c4f17a68d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427988"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>Tutorial: Erstellen einer IoT Edge-Instanz für die Videoanalyse (Intel NUC)

Azure IoT Edge ist ein vollständig verwalteter Dienst für lokale Cloud Intelligence. Hierfür wird Folgendes bereitgestellt und ausgeführt:

* Benutzerdefinierte Logik
* Azure-Dienste
* Künstliche Intelligenz

In IoT Edge werden diese Dienste direkt auf plattformübergreifenden IoT-Geräten ausgeführt, damit Sie Ihre IoT-Lösung in der Cloud oder offline sicher und bedarfsgesteuert ausführen können.

In diesem Tutorial wird veranschaulicht, wie Sie die IoT Edge-Runtime auf einem Intel NUC-Gerät installieren und konfigurieren.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> * Aktualisieren und Konfigurieren von IoT Edge
> * Einrichten des IoT Edge-Gateways
> * Verbinden einer lokalen ONVIF-kompatiblen Kamera mit Ihrem Intel NUC-Gerät

## <a name="prerequisites"></a>Voraussetzungen

* Bevor Sie beginnen, sollten Sie das vorgeschaltete Tutorial [Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (YOLO v3)](./tutorial-video-analytics-create-app-yolo-v3.md) bzw. [Erstellen einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“ in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) durcharbeiten.
* Ein Gerät, z. B. vom Typ Intel NUC, mit Linux, auf dem Docker-Container ausgeführt werden können und das über eine ausreichende Verarbeitungsleistung zum Durchführen von Videoanalysen verfügt.
* [Installation der IoT Edge-Runtime](../../iot-edge/how-to-install-iot-edge.md), die auf dem Gerät ausgeführt wird.
* Für die Verbindungsherstellung mit dem IoT Edge-Gerät von Ihrem Windows-Computer aus benötigen Sie den [PuTTY SSH-Client](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) oder ein vergleichbares Hilfsprogramm.
* Darüber hinaus benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) kostenlos eines erstellen.

## <a name="configure-the-iot-edge-device"></a>Konfigurieren des IoT Edge-Geräts

Falls Sie die IoT Edge-Runtime nicht auf Ihrem Intel NUC-Computer installiert haben, hilft Ihnen die Anleitung unter [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../../iot-edge/how-to-install-iot-edge.md) weiter.

Aktualisieren Sie die IoT Edge-Runtime wie folgt:

1. Verwenden Sie das PuTTY-Hilfsprogramm, um eine Verbindung mit dem IoT Edge-Gerät herzustellen.

1. Führen Sie die folgenden Befehle aus, um die Version der IoT Edge-Runtime zu aktualisieren und zu überprüfen. Zum Zeitpunkt der Artikelerstellung lautet die Version 1.0.9:

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. Verwenden Sie die folgenden Befehle, um die von der Bereitstellung genutzten Ordner mit den erforderlichen Berechtigungen zu erstellen:

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

Gehen Sie wie folgt vor, um die Konfigurationsdatei *state.json* dem Ordner */data/storage* Ihres IoT Edge-Geräts hinzuzufügen:

1. Verwenden Sie einen Text-Editor, um auf Ihrem lokalen Computer im Ordner *lva-configuration* die Datei *state.json* zu öffnen.

1. Aktualisieren Sie die Platzhalter `system` und `iotCentral > properties` mit Zeichenfolgenwerten, die Ihr Gatewaygerät beschreiben. Sie können diese Werte später im Dashboard der IoT Central-Anwendung anzeigen.

1. Aktualisieren Sie die Platzhalter vom Typ `iotCentral > appKeys` mit den Werten, die Sie sich im vorherigen Tutorial in der Datei *scratchpad.txt* notiert haben. Speichern Sie die Änderungen.

1. Nutzen Sie das PuTTY-Hilfsprogramm `scp` an der Eingabeaufforderung, um die gerade bearbeitete Datei *state.json* in den Ordner */data/storage* auf Ihrem IoT Edge-Gerät zu kopieren. In diesem Beispiel wird `192.168.0.144` als IP-Beispieladresse verwendet. Ersetzen Sie sie durch die IP-Adresse Ihres IoT Edge-Geräts:

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

Konfigurieren Sie IoT Edge für die Registrierung und Verbindungsherstellung mit Ihrer IoT Central-Anwendung:

1. Verwenden Sie das PuTTY-Hilfsprogramm, um eine Verbindung mit dem IoT Edge-Gerät herzustellen.

1. Nutzen Sie einen Text-Editor, z. B. `nano`, um die IoT Edge-Datei „config.yaml“ zu öffnen.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > Bei YAML-Dateien können keine Tabstoppzeichen für Einzüge verwendet werden. Geben Sie stattdessen zwei Leerzeichen ein. Elemente der obersten Ebene dürfen keine führenden Leerzeichen enthalten.

1. Scrollen Sie nach unten zu `# Manual provisioning configuration`. Kommentieren Sie die nächsten drei Zeilen aus, wie im folgenden Codeausschnitt gezeigt:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Scrollen Sie nach unten zu `# DPS symmetric key provisioning configuration`. Heben Sie die Auskommentierung der nächsten acht Zeilen aus, wie im folgenden Codeausschnitt gezeigt:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. Ersetzen Sie `{scope_id}` durch den **ID-Bereich**, den Sie sich im vorherigen Tutorial in der Datei *scratchpad.txt* notiert haben.

1. Ersetzen Sie `{registration_id}` durch das Gerät *gateway-001*, das Sie im vorherigen Tutorial erstellt haben.

1. Ersetzen Sie `{symmetric_key}` durch den **Primärschlüssel** für das Gerät **gateway-001**, den Sie sich im vorherigen Tutorial in der Datei *scratchpad.txt* notiert haben.

1. Führen Sie den folgenden Befehl aus, um den IoT Edge-Daemon neu zu starten:

    ```bash
    sudo systemctl restart iotedge
    ```

1. Führen Sie den folgenden Befehl aus, um den Status der IoT Edge-Module zu überprüfen:

    ```bash
    iotedge list
    ```

    In der Ausgabe des obigen Befehls sind fünf ausgeführte Module enthalten. Sie können den Status der ausgeführten Module auch in Ihrer IoT Central-Anwendung anzeigen.

    > [!TIP]
    > Sie können diesen Befehl erneut ausführen, um den Status zu überprüfen. Unter Umständen müssen Sie warten, bis alle Module gestartet wurden.

Falls die IoT Edge-Module nicht richtig gestartet werden, helfen Ihnen die Informationen unter [Behandeln von Problemen bei Ihrem IoT Edge-Gerät](../../iot-edge/troubleshoot.md) weiter.

## <a name="collect-the-rtsp-stream-from-your-camera"></a>Erfassen des RTSP-Datenstroms von Ihrer Kamera

Identifizieren Sie die RTSP-Datenstrom-URLs für die Kameras, die mit Ihrem IoT Edge-Gerät verbunden sind, z. B.:

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> Versuchen Sie, den Kameradatenstrom auf dem IoT Edge-Computer mit einem Media Player, z. B. VLC, anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die Bereitstellung der IoT Edge-Runtime und die LVA-Module auf dem Intel NUC-Gatewaygerät jetzt abgeschlossen.

Informationen zum Verwalten der Kameras erhalten Sie beim Durcharbeiten des nächsten Tutorials:

> [!div class="nextstepaction"]
> [Überwachen und Verwalten einer Anwendung vom Typ „Videoanalyse: Objekt- und Bewegungserkennung“](./tutorial-video-analytics-manage.md)