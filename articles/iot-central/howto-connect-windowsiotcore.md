---
title: Herstellen einer Verbindung zwischen einem Windows IoT Core-Gerät und Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein MXChip IoT DevKit-Gerät mit Ihrer Azure IoT Central-Anwendung verbinden.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e8d4ab46c598580a3a87f4344202f2700926bf5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510324"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Herstellen einer Verbindung zwischen einem Windows IoT Core-Gerät und Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler eine Verbindung zwischen einem Windows IoT Core-Gerät und Ihrer Microsoft Azure IoT Central-Anwendung herstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Eine Azure IoT Central-Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).

- Ein Gerät, auf dem das Betriebssystem Windows 10 IoT Core ausgeführt wird. Weitere Informationen finden Sie unter [Einrichten Ihres Windows 10 IoT Core-Geräts](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Einen Entwicklungscomputer mit installierter [Node.js](https://nodejs.org/)-Version 8.0.0 oder höher. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Node.js ist für eine Vielzahl von Betriebssystemen verfügbar.

## <a name="the-sample-devkits-application"></a>Die Anwendung Beispiel-DevKits

Eine Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde, enthält eine **Windows IoT Core**-Gerätevorlage mit folgenden Merkmalen:

- Telemetriemessungen für das Gerät: **Luftfeuchtigkeit**, **Temperatur** und **Luftdruck**.
- Einstellung zum Steuern der **Lüfterdrehzahl**.
- Eine Geräteeigenschaft **Nummer** und eine Cloudeigenschaft **Speicherort**.

Vollständige Informationen zur Konfiguration der Gerätevorlage finden Sie unter [Details zur Windows IoT Core-Gerätevorlage](#device-template-details).

## <a name="add-a-real-device"></a>Hinzufügen eines echten Geräts

In Ihrer Azure IoT Central-Anwendung können Sie über die Seite **Geräte-Explorer** ein echtes Gerät aus der Gerätevorlage **Windows 10 IoT Core** hinzufügen. Notieren Sie sich die Geräteverbindungsdetails (**Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**). Weitere Informationen finden Sie unter [Abrufen der Verbindungsinformationen](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Damit das Gerät eine Verbindung mit IoT Central herstellen kann, benötigt es eine Verbindungszeichenfolge.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Damit der Gerätecode auf die Verbindungszeichenfolge zugreifen kann, speichern Sie ihn auf Ihrem Windows 10 IoT Core-Gerät in der Datei **connection.string.iothub** im Ordner `C:\Data\Users\DefaultAccount\Documents\`.

Zum Kopieren der Datei **connection.string.iothub** von Ihrem Desktopcomputer in den Ordner `C:\Data\Users\DefaultAccount\Documents\` auf Ihrem Gerät können Sie das [Windows-Geräteportal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) nutzen:

1. Navigieren Sie in Ihrem Webbrowser zum Windows-Geräteportal auf Ihrem Gerät.
1. Um die Dateien auf Ihrem Gerät zu durchsuchen, wählen Sie **Apps > Datei-Explorer** aus.
1. Navigieren Sie zu **Benutzerordner\Dokumente**. Laden Sie dann die Datei **connection.string.iothub** hoch:

    ![Hochladen der Verbindungszeichenfolge](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Bereitstellen und Ausführen

Zum Bereitstellen und Ausführen der Beispielanwendung auf Ihrem Gerät können Sie das [Windows-Geräteportal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) nutzen:

1. Navigieren Sie in Ihrem Webbrowser zum Windows-Geräteportal auf Ihrem Gerät.
1. Wählen Sie zum Bereitstellen und Ausführen der Anwendung **Azure IoT Hub-Client** im Portal **Apps > Beispiele für die schnelle Ausführung** aus. Wählen Sie danach **Azure IoT Hub-Client** aus.
1. Wählen Sie abschließend **Bereitstellen und Ausführen** aus.

    ![Bereitstellen und Ausführen](media/howto-connect-windowsiotcore/quick-run.png)

Nach ein paar Minuten können Sie in Ihrer IoT Central-Anwendung die Telemetriedaten von Ihrem Gerät anzeigen.

Das [Windows-Geräteportal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) enthält Tools, die Sie für die Problembehandlung auf Ihrem Gerät verwenden können:

- Über die Seite **Apps Manager** können Sie die auf Ihrem Gerät ausgeführten Apps steuern.
- Wenn Sie keinen Monitor mit dem Gerät verbunden haben, können Sie über die Seite **Geräteeinstellungen** Screenshots von Ihrem Gerät erfassen. Beispiel:

    ![Screenshot der App](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Herunterladen des Quellcodes

Wenn Sie den Quellcode für die Clientanwendung untersuchen und bearbeiten möchten, können Sie ihn aus dem GitHub-Repository [Windows-iotcore-samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients) herunterladen.

## <a name="device-template-details"></a>Details zur Gerätevorlage

Eine Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde, enthält eine **Windows IoT Core**-Gerätevorlage mit folgenden Merkmalen:

### <a name="telemetry-measurements"></a>Telemetriemessungen

| Feldname     | Units  | Minimum | Maximum | Dezimalstellen |
| -------------- | ------ | ------- | ------- | -------------- |
| Luftfeuchtigkeit       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Einstellungen

Numerische Einstellungen

| `Display name` | Feldname | Units | Dezimalstellen | Minimum | Maximum | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Lüfterdrehzahl    | fanSpeed   | U/Min   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Eigenschaften

| Type            | `Display name` | Feldname | Datentyp |
| --------------- | ------------ | ---------- | --------- |
| Geräteeigenschaft | Nummer   | dieNumber  | number    |
| Text            | Location     | location   | –       |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt erfahren haben, wie Sie ein Windows IoT Core-Gerät mit Ihrer Azure IoT Central-Anwendung verbinden können, umfasst der nächste empfohlene Schritt Informationen zum [Einrichten einer benutzerdefinierten Gerätevorlage](howto-set-up-template.md) für Ihr eigenes IoT-Gerät.
