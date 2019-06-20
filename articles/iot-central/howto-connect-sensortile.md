---
title: Herstellen einer Verbindung zwischen einem SensorTile.box-Gerät und Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein SensorTile.box-Gerät mit Ihrer Azure IoT Central-Anwendung verbinden.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 8c1b4a4ab834b2203a7e0b6e4e9e366c3fc38774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472166"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>Verbinden eines SensorTile.box-Geräts mit Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler eine Verbindung zwischen einem SensorTile.box-Gerät und Ihrer Microsoft Azure IoT Central-Anwendung herstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Zum Ausführen der Schritte in diesem Artikel benötigen Sie folgende Ressourcen:

* Ein SensorTile.box-Gerät. Weitere Informationen finden Sie unter [SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html).
* Die auf Ihrem Android-Gerät installierte ST BLE Sensor-App. Diese können Sie [hier herunterladen](https://play.google.com/store/apps/details?id=com.st.bluems). Weitere Informationen finden Sie unter: [ST BLE-Sensor](https://www.st.com/stblesensor)
* Eine Azure IoT Central-Anwendung, die mit der **DevKits**-Anwendungsvorlage erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
* Fügen Sie die Gerätevorlage **SensorTile.box** zu Ihrer IoT Central-Anwendung hinzu. Navigieren Sie dazu zur Seite **Gerätevorlagen**, klicken Sie auf **+ Neu**, und wählen Sie die Vorlage **SensorTile.box** aus.

### <a name="get-your-device-connection-details"></a>Abrufen der Geräteverbindungsdetails

Fügen Sie in Ihrer Azure IoT Central-Anwendung ein reales Gerät aus der **SensorTile.box**-Gerätevorlage hinzu, und notieren Sie sich die Verbindungsdetails des Geräts: **Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**:

1. Fügen Sie ein Gerät aus Device Explorer hinzu. Wählen Sie **+ Neu > Real** aus, um ein reales Gerät hinzuzufügen.

    * Geben Sie eine **Geräte-ID** (in Kleinbuchstaben) ein, oder verwenden Sie die vorgeschlagene **Geräte-ID**.
    * Geben Sie einen **Gerätenamen** ein, oder verwenden Sie den vorgeschlagenen Namen.

    ![Gerät hinzufügen](media/howto-connect-sensortile/real-device.png)

1. Wählen Sie zum Abrufen der Geräteverbindungsdetails (**Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**) auf der Geräteseite die Option **Verbinden** aus.

    ![Verbindungsdetails](media/howto-connect-sensortile/connect-device.png)

1. Notieren Sie sich die Verbindungsdetails. Bei der Vorbereitung des DevKit-Geräts im nächsten Schritt sind Sie vorübergehend nicht mit dem Internet verbunden.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Einrichten der SensorTile.box mit der mobilen Anwendung

In diesem Abschnitt erfahren Sie, wie Sie die Anwendungsfirmware auf das Gerät übertragen. Anschließend können Sie die Gerätedaten über die mobile ST BLE Sensor App mit Bluetooth Low Energy (BLE)-Konnektivität an IoT Central senden.

1. Öffnen Sie die App ST BLE Sensor, und wählen Sie die Schaltfläche **Neue App erstellen** aus.

    ![Erstellen einer App](media/howto-connect-sensortile/create-app.png)

1. Wählen Sie die Anwendung **Barometer** aus.
1. Wählen Sie die Schaltfläche zum Hochladen aus.

    ![Hochladen des Barometers](media/howto-connect-sensortile/barometer-upload.png)

1. Wählen Sie die Wiedergabeschaltfläche aus, die Ihrer SensorTile.box zugeordnet werden soll.
1. Wenn der Prozess abgeschlossen ist, streamt die SensorTile.box Temperatur, Luftdruck und Luftfeuchtigkeit über BLE.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>Verbinden der SensorTile.box mit der Cloud

In diesem Abschnitt erfahren Sie, wie Sie die SensorTile.box mit der mobilen Anwendung und die mobile Anwendung wiederum mit der Cloud verbinden.

1. Wählen Sie im linken Menü die Schaltfläche **Cloud Logging** (Cloud-Protokollierung) aus.

    ![Cloud-Protokollierung](media/howto-connect-sensortile/cloud-logging.png)

1. Wählen Sie **Azure IoT Central** als Cloudanbieter aus.
1. Geben Sie die Geräte-ID und die Bereichs-ID ein, die Sie sich zuvor notiert haben.

    ![Anmeldeinformationen](media/howto-connect-sensortile/credentials.png)

1. Wählen Sie das Optionsfeld **Anwendungsschlüssel** aus.
1. Klicken Sie auf **Verbinden**, und wählen Sie die Telemetriedaten aus, die Sie hochladen möchten.
1. Nach einigen Sekunden werden die Daten auf dem Dashboard der IoT Central-Anwendung angezeigt.

## <a name="sensortilebox-device-template-details"></a>Details zur SensorTile.box-Gerätevorlage

Eine mit der SensorTile.box-Gerätevorlage erstellte Anwendung mit den folgenden Merkmalen:

### <a name="telemetry"></a>Telemetrie

| Feldname     | Units  | Minimum | Maximum | Dezimalstellen |
| -------------- | ------ | ------- | ------- | -------------- |
| Luftfeuchtigkeit       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie eine SensorTile.box mit Ihrer Azure IoT Central-Anwendung verbunden wird, empfiehlt sich als nächster Lernschritt das [Einrichten einer benutzerdefinierten Gerätevorlage](howto-set-up-template.md) für Ihr eigenes IoT-Gerät.
