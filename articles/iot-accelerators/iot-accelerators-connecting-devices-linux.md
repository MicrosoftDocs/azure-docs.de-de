---
title: Bereitstellen von Linux-Geräten für die Remoteüberwachung in C – Azure | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie ein Gerät mit dem Solution Accelerator für die Remoteüberwachung verbinden, indem Sie eine in C geschriebene Anwendung unter Linux ausführen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61454492"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Herstellen einer Verbindung zwischen Ihrem Gerät und dem Solution Accelerator für die Remoteüberwachung (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In diesem Tutorial wird gezeigt, wie Sie eine Verbindung zwischen einem realen Gerät und dem Solution Accelerator für die Remoteüberwachung herstellen.

Wie bei den meisten eingebetteten Anwendungen, die auf eingeschränkten Geräten ausgeführt werden, wird der Clientcode für die Geräteanwendung in C geschrieben. In diesem Tutorial erstellen Sie die Anwendung auf einem Computer, auf dem Ubuntu (Linux) ausgeführt wird.

Wenn Sie lieber ein Gerät simulieren möchten, finden Sie weitere Informationen unter [Erstellen und Testen eines neuen simulierten Geräts](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die Schritte in dieser Schrittanleitung benötigen Sie ein Gerät mit der Ubuntu-Version 15.04 oder höher. Bevor Sie fortfahren, [richten Sie Ihre Linux-Entwicklungsumgebung ein](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
