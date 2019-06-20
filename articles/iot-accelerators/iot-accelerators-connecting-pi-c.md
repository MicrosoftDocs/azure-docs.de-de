---
title: Bereitstellen von Raspberry Pi für die Remoteüberwachung mit C – Azure | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie mittels einer in C geschriebenen Anwendung die Verbindung eines Raspberry Pi-Geräts mit dem Solution Accelerator für die Remoteüberwachung herstellen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61454497"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Herstellen einer Verbindung zwischen Ihrem Raspberry Pi-Gerät und dem Solution Accelerator für die Remoteüberwachung (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In diesem Tutorial wird gezeigt, wie Sie eine Verbindung zwischen einem realen Gerät und dem Solution Accelerator für die Remoteüberwachung herstellen. Wie bei den meisten eingebetteten Anwendungen, die auf eingeschränkten Geräten ausgeführt werden, wird der Clientcode für die Raspberry Pi-Geräteanwendung in C geschrieben. In diesem Tutorial erstellen Sie die Anwendung auf einem Raspberry Pi mit dem Raspbian-Betriebssystem.

Wenn Sie lieber ein Gerät simulieren möchten, finden Sie weitere Informationen unter [Erstellen und Testen eines neuen simulierten Geräts](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Erforderliche Hardware

Ein Desktopcomputer, über den Sie eine Remoteverbindung mit der Befehlszeile auf dem Raspberry Pi herstellen können.

[Microsoft IoT Starter Kit für Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) oder entsprechende Komponenten. In diesem Tutorial wird Folgendes aus dem Kit verwendet:

- Raspberry Pi 3
- MicroSD-Karte (mit NOOBS)
- Ein Mini-USB-Kabel
- Ein Ethernetkabel

### <a name="required-desktop-software"></a>Erforderliche Desktopsoftware

Sie benötigen einen SSH-Client auf Ihrem Desktopcomputer, um per Remotezugriff auf die Befehlszeile des Raspberry Pi zugreifen zu können.

- In Windows ist kein SSH-Client enthalten. Wir empfehlen die Verwendung von [PuTTY](https://www.putty.org/).
- In den meisten Linux-Distributionen sowie in Mac OS ist ein SSH-Befehlszeilenprogramm enthalten. Weitere Informationen finden Sie unter [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH unter Linux oder Mac OS).

### <a name="required-raspberry-pi-software"></a>Erforderliche Raspberry Pi-Software

In diesem Artikel wird vorausgesetzt, dass Sie die neueste Version des [Raspbian-Betriebssystems auf Ihrem Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/) installiert haben.

Die folgenden Schritte zeigen, wie Sie Ihren Raspberry Pi für die Erstellung einer C-Anwendung vorbereiten, die eine Verbindung mit dem Solution Accelerator herstellt:

1. Stellen Sie mithilfe von **SSH** eine Verbindung mit Ihrem Raspberry Pi her. Weitere Informationen finden Sie auf der [Raspberry Pi-Website](https://www.raspberrypi.org/) unter [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md).

1. Verwenden Sie den folgenden Befehl aus, um Ihr Raspberry Pi zu aktualisieren:

    ```sh
    sudo apt-get update
    ```

1. Führen Sie für diese Schrittanleitung die Schritte zum [Einrichten der Linux-Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aus, um Raspberry Pi die erforderlichen Entwicklungstools und -bibliotheken hinzuzufügen.

## <a name="view-the-code"></a>Anzeigen des Codes

Der in dieser Anleitung verwendete [Beispielcode](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) ist im GitHub-Repository mit den Azure IoT C SDKs verfügbar.

### <a name="download-the-source-code-and-prepare-the-project"></a>Herunterladen des Quellcodes und Vorbereiten des Projekts

Klonen Sie zum Vorbereiten des Projekts das [Repository mit Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) auf GitHub, oder laden Sie es herunter.

Das Beispiel befindet sich im Ordner **samples/solutions/remote_monitoring_client**.

Öffnen Sie die Datei **remote_monitoring.c** im Ordner **samples/solutions/remote_monitoring_client** in einem Text-Editor.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Erstellen und Ausführen der Anwendung

Die folgenden Schritte beschreiben, wie Sie *CMake* zum Erstellen der Clientanwendung verwenden. Die Clientanwendung für die Remoteüberwachung wird im Rahmen des Buildprozesses für das SDK erstellt.

1. Bearbeiten Sie die Datei **remote_monitoring.c**. Ersetzen Sie `<connectionstring>` durch die Geräte-Verbindungszeichenfolge, die Sie zu Beginn dieser Schrittanleitung notiert haben, als Sie ein Gerät zum Solution Accelerator hinzugefügt haben.

1. Navigieren Sie zum Stamm der geklonten Kopie des [Repositorys mit Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c), und führen Sie die folgenden Befehle zum Erstellen der Clientanwendung aus:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Führen Sie die Clientanwendung aus, und senden Sie die Telemetriedaten an IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Die Konsole zeigt Meldungen an wie:

    - Die Anwendung sendet Beispieltelemetriedaten an den Solution Accelerator.
    - Reagiert auf Methoden, die über das Lösungsdashboard aufgerufen werden.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
