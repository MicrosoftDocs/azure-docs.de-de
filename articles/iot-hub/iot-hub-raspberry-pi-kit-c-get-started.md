---
title: Verbinden von Raspberry Pi mit Azure IoT Hub über C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Raspberry Pi einrichten und eine Verbindung mit Azure IoT Hub herstellen, damit Raspberry Pi Daten an die Azure-Cloudplattform senden kann.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 6a895d7978f1af3914bbb9dee3594dbfffd9f317
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569840"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Verbinden von Raspberry Pi mit Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

In diesem Tutorial erlernen Sie die Grundlagen der Verwendung von Raspberry Pi 3 und Raspbian. Anschließend erfahren Sie, wie Sie Ihre Geräte mithilfe von [Azure IoT Hub](about-iot-hub.md) nahtlos mit der Cloud verbinden. Beispiele für Windows 10 IoT Core finden Sie im [Windows Dev Center](https://www.windowsondevices.com/).

Sie haben noch kein Kit? Probieren Sie den [Raspberry Pi-Onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) aus. Oder erwerben Sie [hier](https://azure.microsoft.com/develop/iot/starter-kits) ein neues Kit.

## <a name="what-you-do"></a>Aufgaben

* Erstellen Sie einen IoT Hub.

* Registrieren Sie ein Gerät für Pi in Ihrem IoT Hub.

* Richten Sie Raspberry Pi ein.

* Führen Sie eine Beispielanwendung auf Pi aus, um Sensordaten an Ihren IoT Hub zu senden.

Verbinden Sie Raspberry Pi mit einem von Ihnen erstellten IoT Hub. Führen Sie anschließend eine Beispielanwendung auf Pi aus, um Temperatur- und Feuchtigkeitsdaten eines BME280-Sensors zu erfassen. Senden Sie abschließend die Sensordaten an Ihren IoT Hub.

## <a name="what-you-learn"></a>Lerninhalt

* Erstellen eines Azure IoT Hubs und Abrufen der Verbindungszeichenfolge für Ihr neues Gerät

* Herstellen der Verbindung von Pi mit einem BME280-Sensor

* Erfassen von Sensordaten durch Ausführen einer Beispielanwendung auf Pi

* Senden von Sensordaten an Ihren IoT Hub

## <a name="what-you-need"></a>Voraussetzungen

![Voraussetzungen](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2- oder Raspberry Pi 3-Platine.

* Ein aktives Azure-Abonnement. Wenn Sie kein Azure-Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Azure-Testkonto](https://azure.microsoft.com/free/) erstellen.

* Monitor, USB-Tastatur und Maus, die mit Pi verbunden werden.

* Mac oder PC, auf dem Windows oder Linux ausgeführt wird

* Eine Internetverbindung.

* microSD-Karte mit mindestens 16 GB

* USB-SD-Adapter oder microSD-Karte, um das Betriebssystemimage auf die microSD-Karte zu kopieren

* Netzteil (5 V, 2 A) mit Micro-USB-Kabel (1,8 m)

Die folgenden Elemente sind optional:

* Ein zusammengebauter Adafruit BME280-Sensor für Temperatur, Luftdruck und Luftfeuchtigkeit

* Eine Steckplatine

* 6 F/M-Jumperdrähte

* 1 LED, diffus, 10 mm

> [!NOTE]
> Diese Elemente sind optional, weil das Codebeispiel simulierte Sensordaten unterstützt.
>

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Einrichten von Raspberry Pi

Richten Sie jetzt das Raspberry Pi ein.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installieren des Betriebssystems Raspbian für Pi

Bereiten Sie die microSD-Karte für die Installation des Raspbian-Image vor.

1. Laden Sie Raspbian herunter.

   1. [Laden Sie Raspbian Stretch mit Desktop herunter](https://www.raspberrypi.org/downloads/raspbian/) (die ZIP-Datei).

   2. Entpacken Sie das Raspbian-Image in einen Ordner auf Ihrem Computer.

2. Installieren Sie Raspbian auf der microSD-Karte.

   1. [Laden Sie das SD-Kartenbrennprogramm Etcher herunter, und installieren Sie es](https://etcher.io/).

   2. Führen Sie Etcher aus, und wählen Sie das Raspbian-Image, das Sie in Schritt 1 entpackt haben.

   3. Wählen Sie das microSD-Kartenlaufwerk. Hinweis: Etcher hat unter Umständen bereits das richtige Laufwerk ausgewählt.

   4. Klicken Sie auf „Flash“, um Raspbian auf der microSD-Karte zu installieren.

   5. Entfernen Sie die microSD-Karte aus dem Computer, wenn die Installation abgeschlossen ist. Es ist sicher, die microSD-Karte direkt zu entfernen, da Etcher die microSD-Karte nach Abschluss des Vorgangs automatisch auswirft bzw. die Bereitstellung aufhebt.

   6. Legen Sie die microSD-Karte in den Raspberry Pi ein.

### <a name="enable-ssh-and-spi"></a>Aktivieren von SSH und SPI

1. Verbinden Sie Pi mit dem Monitor, der Tastatur und Maus. Starten Sie Pi, und melden Sie sich bei Raspbian mit `pi` als Benutzername und `raspberry` als Kennwort an.
 
2. Klicken Sie auf das Raspberry-Symbol und dann auf **Preferences** > **Raspberry Pi Configuration**.

   ![Das Raspbian-Menü „Preferences“](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Legen Sie auf der Registerkarte **Interfaces** die Einstellungen **SPI** und **SSH** auf **Enable** fest, und klicken Sie dann auf **OK**. Wenn Sie keine physischen Sensoren besitzen und simulierte Sensordaten verwenden möchten, ist dieser Schritt optional.

   ![Aktivieren von SPI und SSH auf Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Weitere Referenzdokumente zum Aktivieren von SSH und SPI finden Sie auf[raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) und unter [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Verbinden des Sensors mit Pi

Verwenden Sie die Steckplatine und Jumperdrähte, um eine LED und einen BME280-Sensor wie folgt zu verbinden. Wenn Sie keinen Sensor haben, [überspringen Sie diesen Abschnitt](#connect-pi-to-the-network).

![Die Raspberry Pi- und Sensorverbindung](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Mit dem BME280-Sensor können Daten zur Temperatur und Luftfeuchtigkeit erfasst werden. Die LED blinkt, wenn der Kommunikationsvorgang zwischen dem Gerät und der Cloud aktiv ist.

Verwenden Sie für Sensorstifte die folgende Verkabelung:

| Start (Sensor und LED)     | Ende (Board)            | Kabelfarbe   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Stift 5G)         | GPIO 4 (Stift 7)         | Weißes Kabel   |
| LED GND (Stift 6G)         | GND (Stift 6)            | Schwarzes Kabel   |
| VDD (Stift 18F)            | 3,3 V PWR (Stift 17)      | Weißes Kabel   |
| GND (Stift 20F)            | GND (Stift 20)           | Schwarzes Kabel   |
| SCK (Stift 21F)            | SPI0 SCLK (Stift 23)     | Oranges Kabel  |
| SDO (Stift 22F)            | SPI0 MISO (Stift 21)     | Gelbes Kabel  |
| SDI (Stift 23F)            | SPI0 MOSI (Stift 19)     | Grünes Kabel   |
| CS (Stift 24F)             | SPI0 CS (Stift 24)       | Blaues Kabel    |

Klicken Sie hier, um die [Raspberry Pi 2 und 3-Stiftzuordnungen](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) zur Referenz anzuzeigen.

Nachdem Sie den BME280 erfolgreich mit Ihrem Raspberry Pi verbunden haben, sollte das Gerät wie in der nachstehenden Abbildung aussehen.

![Verbindung zwischen Pi und BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Verbindung zwischen Pi und dem Netzwerk

Verbinden Sie den Raspberry Pi mit dem Micro-USB-Kabel mit der Stromversorgung. Verwenden Sie das Ethernet-Kabel zum Verbinden von Pi mit Ihrem verkabelten Netzwerk, oder befolgen Sie die [Anweisungen der Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/), um Pi mit Ihrem WLAN zu verbinden. Notieren Sie sich die [IP-Adresse von Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address), nachdem es eine Verbindung zum Netzwerk hergestellt hat.

![Mit verkabeltem Netzwerk verbunden](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Ausführen einer Beispielanwendung auf Pi

### <a name="sign-into-your-raspberry-pi"></a>Anmelden am Raspberry Pi

1. Verwenden Sie einen der folgenden SSH-Clients auf Ihrem Hostcomputer, um die Verbindung mit Ihrem Raspberry Pi herzustellen.
   
   **Windows-Benutzer**
   1. Laden Sie [PuTTY](https://www.putty.org/) für Windows herunter, und installieren Sie es. 
   1. Kopieren Sie die IP-Adresse von Pi, fügen Sie sie in den Abschnitt „Hostname (oder IP-Adresse)“ ein, und wählen Sie „SSH“ als Verbindungstyp.
   
   ![PuTTY](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- und Ubuntu-Benutzer**

   Verwenden Sie den integrierten SSH-Client unter Ubuntu oder macOS. Möglicherweise müssen Sie `ssh pi@<ip address of pi>` ausführen, um eine SSH-Verbindung mit Pi herzustellen.
   > [!NOTE]
   > Der Standardbenutzername ist `pi`, und das Kennwort ist `raspberry`.


### <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

1. Klonen Sie die Beispielanwendung durch Ausführen des folgenden Befehls:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Führen Sie das Setupskript aus:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Wenn Sie **keinen BME280-Sensor besitzen**, können Sie mit dem Befehlszeilenparameter „--simulated-data“ Temperatur- und Feuchtigkeitsdaten simulieren. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Erstellen und Ausführen der Beispielanwendung

1. Erstellen Sie die Beispielanwendung durch Ausführen des folgenden Befehls:

   ```bash
   cmake . && make
   ```
   
   ![Erstellen der Ausgabe](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Führen Sie die Beispielanwendung durch Aufrufen des folgenden Befehls aus:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Stellen Sie sicher, dass Sie die Verbindungszeichenfolge des Geräts kopieren und zwischen einfachen Anführungszeichen einfügen.
   >

Die folgende Ausgabe sollte angezeigt werden, die die Sensordaten und Nachrichten zeigt, die an Ihren IoT Hub gesendet werden.

![Ausgabe: Von Raspberry Pi an Ihren IoT Hub gesendete Sensordaten](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Lesen der von Ihrem Hub empfangenen Nachrichten

Wenn Sie die von Ihrem IoT-Hub empfangenen Nachrichten von Ihrem Gerät aus überwachen möchten, können Sie dafür die Azure IoT-Tools für Visual Studio Code verwenden. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten zwischen Ihrem Gerät und IoT Hub mithilfe der Azure IoT-Tools für Visual Studio Code](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Weitere Informationen zum Verarbeiten von Daten, die von Ihrem Gerät gesendet wurden, finden Sie im nächsten Abschnitt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Beispielanwendung ausgeführt, die Sensordaten sammelt und an Ihren IoT Hub sendet.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
