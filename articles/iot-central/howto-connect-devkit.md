---
title: Herstellen einer Verbindung zwischen einem DevKit-Gerät und Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein MXChip IoT-Entwickler-Kit-Gerät mit Ihrer Azure IoT Central-Anwendung verbinden.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 130ca6bc946d44d80cddba5486d405bfb15523cb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235877"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Herstellen einer Verbindung zwischen einem MXChip IoT DevKit-Gerät und Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler eine Verbindung zwischen einem MXChip IoT DevKit-Gerät (DevKit) und Ihrer Microsoft Azure IoT Central-Anwendung herstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Zum Ausführen der Schritte in diesem Artikel benötigen Sie folgende Ressourcen:

1. Eine Azure IoT Central-Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
1. Ein DevKit-Gerät. Um ein DevKit-Gerät zu erwerben, besuchen Sie [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Anwendung Beispiel-DevKits

Eine mit der Anwendungsvorlage **Beispiel-Devkits** erstellte Anwendung enthält eine **MXChip**-Gerätevorlage, die folgende Geräteeigenschaften definiert:

- Telemetriemessungen für **Luftfeuchtigkeit**, **Temperatur**, **Druck**, **Magnetometer** (entlang der X-, Y- und Z-Achse gemessen), **Beschleunigungsmesser** (entlang der X-, Y- und Z-Achse gemessen) und **Gyroskop** (entlang der X-, Y- und Z-Achse gemessen).
- Zustandsmessung für den **Gerätestatus**.
- Ereignismessung für **Taste B gedrückt**.
- Einstellungen für **Spannung**, **Stromstärke** und **Lüftergeschwindigkeit** sowie eine Umschaltschaltfläche namens **IR**.
- Geräteeigenschaften **Nummer** und **Gerätestandort** (eine Standorteigenschaft).
- Cloudeigenschaft **Hergestellt in**.
- Befehle **Echo** und **Countdown**. Wenn ein echtes Gerät einen **Echo**-Befehl empfängt, zeigt es den gesendeten Wert auf dem Display des Geräts an. Wenn ein echtes Gerät einen **Countdown**-Befehl empfängt, durchläuft die LED ein Muster, und das Gerät sendet Countdownwerte an IoT Central zurück.

Vollständige Informationen zur Konfiguration finden Sie unter [Details zur MXChip-Gerätevorlage](#mxchip-device-template-details).

## <a name="add-a-real-device"></a>Hinzufügen eines echten Geräts

### <a name="get-your-device-connection-details"></a>Abrufen der Geräteverbindungsdetails

Fügen Sie in Ihrer Azure IoT Central-Anwendung ein echtes Gerät aus der **MXChip**-Gerätevorlage hinzu, und notieren Sie sich die Verbindungsdetails des Geräts: **Bereichs-ID, Geräte-ID und Primärschlüssel**:

1. Fügen Sie über den Device Explorer ein **echtes Gerät** hinzu. Wählen Sie **+ Neu > Echt** aus, um ein echtes Gerät hinzuzufügen.

    * Geben Sie eine **Geräte-ID** (in Kleinbuchstaben) ein, oder verwenden Sie die vorgeschlagene **Geräte-ID**.
    * Geben Sie einen **Gerätenamen** ein, oder verwenden Sie den vorgeschlagenen Namen.

    ![Gerät hinzufügen](media/howto-connect-devkit/add-device.png)

1. Wählen Sie zum Abrufen der Geräteverbindungsdetails (**Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**) auf der Geräteseite die Option **Verbinden** aus.

    ![Verbindungsdetails](media/howto-connect-devkit/device-connect.png)

1. Notieren Sie sich die Verbindungsdetails. Bei der Vorbereitung des DevKit-Geräts im nächsten Schritt sind Sie vorübergehend nicht mit dem Internet verbunden.

### <a name="prepare-the-devkit-device"></a>Vorbereiten des DevKit-Geräts

Wenn Sie das Gerät zuvor genutzt haben und es zur Verwendung eines anderen WLAN, einer anderen Verbindungszeichenfolge oder einer anderen Telemetriemessung neu konfigurieren möchten, drücken Sie gleichzeitig die Tasten **A** und **B**. Falls dies nicht funktioniert, drücken Sie die **Rücksetztaste**, und versuchen Sie es noch einmal.

#### <a name="to-prepare-the-devkit-device"></a>So bereiten Sie das DevKit-Gerät vor

1. Laden Sie die neueste vorgefertigte Azure IoT Central-Firmware für den MXChip von der Seite [Releases](https://aka.ms/iotcentral-docs-MXChip-releases) in GitHub herunter.
1. Stellen Sie über ein USB-Kabel eine Verbindung zwischen dem DevKit-Gerät und dem Entwicklungscomputer her. Unter Windows wird ein Datei-Explorer-Fenster für ein Laufwerk geöffnet, das dem Speicher auf dem DevKit-Gerät zugeordnet ist. Das Laufwerk heißt beispielsweise **AZ3166 (D:)** .
1. Ziehen Sie die Datei **iotCentral.bin** auf das Laufwerksfenster. Wenn der Kopiervorgang abgeschlossen ist, wird das Gerät mit der neuen Firmware neu gestartet.

1. Beim Neustart des DevKit-Geräts wird der folgende Bildschirm angezeigt:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Wenn auf dem Bildschirm andere Daten angezeigt werden, setzen Sie das Gerät zurück, und drücken Sie auf dem Gerät gleichzeitig die Tasten **A** und **B**, um das Gerät neu zu starten.

1. Das Gerät befindet sich jetzt im Zugriffspunktmodus (Access Point, AP). Sie können von Ihrem Computer oder Mobilgerät aus eine Verbindung mit diesem WLAN-Zugriffspunkt herstellen.

1. Stellen Sie auf Ihrem Computer, Smartphone oder Tablet eine Verbindung mit dem WLAN-Namen her, der auf dem Bildschirm des Geräts angezeigt wird. Wenn Sie eine Verbindung mit diesem Netzwerk herstellen, haben Sie keinen Internetzugriff. Dies ist so beabsichtigt. Sie werden mit diesem Netzwerk nur für kurze Zeit verbunden, solange Sie das Gerät konfigurieren.

1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zu [http://192.168.0.1/start](http://192.168.0.1/start). Die folgende Webseite wird angezeigt:

    ![Seite „Gerätekonfiguration“](media/howto-connect-devkit/configpage.png)

    Geben Sie auf der Webseite Folgendes ein, und nehmen Sie folgende Einstellungen vor:
    - Name Ihres WLAN
    - WLAN-Kennwort
    - Auf dem Gerätedisplay angezeigter PIN-Code
    - Verbindungsdetails **Bereichs-ID**, **Geräte-ID** und **Primärschlüssel** Ihres Geräts (in den obigen Schritten notiert)
    - Alle verfügbaren Telemetriemessungen auswählen

1. Nachdem Sie **Gerät konfiguriere** ausgewählt haben, wird diese Seite angezeigt:

    ![Gerät konfiguriert](media/howto-connect-devkit/deviceconfigured.png)

1. Drücken Sie an Ihrem Gerät die **Rücksetztaste**.

## <a name="view-the-telemetry"></a>Anzeigen der Telemetrie

Beim Neustart des DevKit-Geräts wird auf dem Gerätebildschirm Folgendes angezeigt:

* Die Anzahl gesendeter Telemetrienachrichten
* Die Fehleranzahl
* Die Anzahl der erhaltenen gewünschten Eigenschaften und die Anzahl der gesendeten gemeldeten Eigenschaften.

> [!NOTE]
> Sollte das Gerät beim Verbindungsaufbau in einer Schleife hängen bleiben, überprüfen Sie, ob es in IoT Central **blockiert** ist, und **heben Sie die Blockierung des Geräts auf**, damit es eine Verbindung mit der App herstellen kann.

Schütteln Sie das Gerät, um eine gemeldete Eigenschaft zu senden. Das Gerät sendet eine zufällige Zahl als Geräteeigenschaft **Nummer**.

Sie können die Telemetriemessungen und die gemeldeten Eigenschaftswerte anzeigen und Einstellungen in Azure IoT Central konfigurieren:

1. Verwenden Sie den **Device Explorer**, um die Seite **Messungen** für das reale MXChip-Gerät aufzurufen, das Sie hinzugefügt haben:

    ![Navigieren zum realen Gerät](media/howto-connect-devkit/realdevicenew.png)

1. Auf der Seite **Messungen** sehen Sie die vom MXChip-Gerät gesendeten Telemetriedaten:

    ![Anzeigen der Telemetrie vom realen Gerät](media/howto-connect-devkit/devicetelemetrynew.png)

1. Auf der Seite **Eigenschaften** sehen Sie die Nummer und den Gerätestandort, die zuletzt vom Gerät gemeldet wurden:

    ![Anzeigen von Geräteeigenschaften](media/howto-connect-devkit/devicepropertynew.png)

1. Auf der Seite **Einstellungen** können Sie die Einstellungen für das MXChip-Gerät aktualisieren:

    ![Anzeigen der Geräteeinstellungen](media/howto-connect-devkit/devicesettingsnew.png)

1. Auf der Seite **Befehle** können Sie die Befehle **Echo** und **Countdown** aufrufen:

    ![Aufrufen von Befehlen](media/howto-connect-devkit/devicecommands.png)

1. Auf der Seite **Dashboard** sehen Sie die Standortzuordnung.

    ![Anzeigen des Gerätedashboards](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Herunterladen des Quellcodes

Wenn Sie den Gerätecode untersuchen und ändern möchten, können Sie ihn von GitHub herunterladen. Wenn Sie den Code ändern möchten, befolgen Sie die Anweisungen zum [Vorbereiten der Entwicklungsumgebung](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) für Ihr Desktopbetriebssystem.

Um den Quellcode herunterzuladen, führen Sie den folgenden Befehl auf dem Desktopcomputer aus:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Mit dem vorherige Befehl wird der Quellcode in einen Ordner namens `iot-central-firmware` heruntergeladen.

> [!NOTE]
> Wenn **Git** in Ihrer Entwicklungsumgebung nicht installiert ist, können Sie es von [https://git-scm.com/download](https://git-scm.com/download) herunterladen.

## <a name="review-the-code"></a>Überprüfen des Codes

Verwenden Sie Visual Studio Code, um den Ordner `MXCHIP/mxchip_advanced` im Ordner `iot-central-firmware` zu öffnen:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Um zu sehen, wie die Telemetriedaten an die Azure IoT Central-Anwendung gesendet werden, öffnen Sie die Datei **telemetry.cpp** im Ordner `src`:

- Die Funktion `TelemetryController::buildTelemetryPayload` erstellt die JSON-Telemetrienutzlast anhand von Daten von den Sensoren des Geräts.

- Die Funktion `TelemetryController::sendTelemetryPayload` ruft `sendTelemetry` in der Datei **AzureIOTClient.cpp** auf, um die JSON-Nutzlast an den IoT-Hub zu senden, der von Ihrer Azure IoT Central-Anwendung verwendet wird.

Um zu sehen, wie Eigenschaftswerte an die Azure IoT Central-Anwendung gemeldet werden, öffnen Sie die Datei **telemetry.cpp** im Ordner `src`:

- Die Funktion `TelemetryController::loop` sendet die gemeldete Eigenschaft **location** ungefähr alle 30 Sekunden. Dazu verwendet sie die Funktion `sendReportedProperty` in der Quelldatei **AzureIOTClient.cpp**.

- Die Funktion `TelemetryController::loop` sendet die gemeldete Eigenschaft **dieNumber**, wenn der Beschleunigungsmesser des Geräts einen Doppeltipp erkennt. Dazu verwendet sie die Funktion `sendReportedProperty` in der Quelldatei **AzureIOTClient.cpp**.

Um zu sehen, wie das Gerät auf Befehle reagiert, die in der IoT Central-Anwendung aufgerufen werden, öffnen Sie die Datei **registeredMethodHandlers.cpp** im Ordner `src`:

- Die **dmEcho**-Funktion ist der Handler für den **echo**-Befehl. Sie zeigt das Feld **displayedValue** in der Nutzlast auf dem Bildschirm des Geräts an.

- Die **dmCountdown**-Funktion ist der Handler für den **countdown**-Befehl. Sie ändert die Farbe der LED des Geräts und verwendet eine gemeldete Eigenschaft, um den Countdownwert zurück an die IoT Central-Anwendung zu senden. Die gemeldete Eigenschaft hat den gleichen Namen wie der Befehl. Die Funktion verwendet die Funktion `sendReportedProperty` in der Quelldatei **AzureIOTClient.cpp**.

Der Code in der Quelldatei **AzureIOTClient.cpp** nutzt Funktionen aus den [Microsoft Azure IoT SDKs und Bibliotheken für C](https://github.com/Azure/azure-iot-sdk-c), um mit IoT Hub zu interagieren.

Informationen zum Ändern, Erstellen und Hochladen des Beispielcodes auf Ihr Gerät finden Sie in der Datei **readme.md** im Ordner `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Details zur MXChip-Gerätevorlage

Eine Anwendung, die mit der Anwendungsvorlage „Beispiel-DevKits“ erstellt wurde, enthält eine MXChip-Gerätevorlage mit den folgenden Eigenschaften:

### <a name="measurements"></a>Messungen

#### <a name="telemetry"></a>Telemetrie

| Feldname     | Units  | Minimum | Maximum | Dezimalstellen |
| -------------- | ------ | ------- | ------- | -------------- |
| Luftfeuchtigkeit       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Zustände 
| NAME          | `Display name`   | NORMAL | VORSICHT | GEFAHR | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Gerätestatus   | Grün  | Orange  | Rot    | 

#### <a name="events"></a>Ereignisse 
| NAME             | `Display name`      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Schaltfläche „B“ gedrückt  | 

### <a name="settings"></a>Einstellungen

Numerische Einstellungen

| `Display name` | Feldname | Units | Dezimalstellen | Minimum | Maximum | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spannung      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | Ampere  | 0              | 0       | 100     | 0       |
| Lüfterdrehzahl    | fanSpeed   | U/Min   | 0              | 0       | 1000    | 0       |

Einstellungen zum Ein-/Ausschalten

| `Display name` | Feldname | Text, wenn „eingeschaltet“ | Text, wenn „ausgeschaltet“ | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | EIN      | OFF      | Aus     |

### <a name="properties"></a>Eigenschaften

| Type            | `Display name` | Feldname | Datentyp |
| --------------- | ------------ | ---------- | --------- |
| Geräteeigenschaft | Nummer   | dieNumber  | number    |
| Geräteeigenschaft | Gerätestandort   | location  | location    |
| Text            | Hergestellt in     | manufacturedIn   | –       |

### <a name="commands"></a>Befehle

| `Display name` | Feldname | Rückgabetyp | Anzeigename des Eingabefelds | Name des Eingabefelds | Typ des Eingabefelds |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | Echo       | text        | value to display (Anzuzeigender Wert)         | displayedValue   | text             |
| Countdown    | countdown  | number      | Count from (Zählen ab)               | countFrom        | number           |

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie ein MXChip IoT DevKit mit Ihrer Azure IoT Central-Anwendung verbunden wird, empfiehlt sich als nächster Lernschritt das [Einrichten einer benutzerdefinierten Gerätevorlage](howto-set-up-template.md) für Ihr eigenes IoT-Gerät.
