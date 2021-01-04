---
title: Verbinden von IoT DevKit AZ3166 mit einem Azure IoT Hub
description: In diesem Tutorial erfahren Sie, wie Sie IoT DevKit AZ3166 einrichten und eine Verbindung mit Azure IoT Hub herstellen, um Daten an die Azure-Cloudplattform zu senden.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 3e5ab1667ee0cda459785efa624bd7f4fc6818b8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562955"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Verbinden von IoT DevKit AZ3166 mit Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Sie können das [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)-Board zur Entwicklung von IoT-Lösungen (Internet of Things, Internet der Dinge), die Microsoft Azure-Dienste nutzen, sowie zum Erstellen eines Prototyps dieser Lösungen verwenden. Es enthält ein Arduino-kompatibles Board mit umfangreichen Peripheriegeräten und Sensoren, ein Open-Source-Boardpaket und einen umfassenden [Beispielkatalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Lerninhalt

* Erstellen einer IoT Hub-Instanz und Registrieren eines Geräts für das MXChip IoT DevKit.
* Verbinden des IoT DevKit mit dem WLAN und Konfigurieren der IoT Hub-Verbindungszeichenfolge.
* Senden der DevKit-Sensortelemetriedaten an Ihren IoT-Hub.
* Vorbereiten der Entwicklungsumgebung und Entwickeln von Anwendungen für das IoT DevKit.

Sie haben noch kein DevKit-Board? Probieren Sie den [DevKit-Simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) aus, oder [kaufen Sie ein DevKit](https://aka.ms/iot-devkit-purchase).

Den Quellcode für alle DevKit-Tutorials finden Sie im [Katalog mit Codebeispielen](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Voraussetzungen

- Ein MXChip IoT DevKit-Board mit einem Micro-USB-Kabel. [Jetzt kaufen](https://aka.ms/iot-devkit-purchase).
- Ein Computer mit Windows 10, macOS 10.10+ oder Ubuntu 18.04+.
- Ein aktives Azure-Abonnement. [Kostenloses Microsoft Azure-Testkonto für 30 Tage](https://azureinfo.microsoft.com/us-freetrial.html) aktivieren.
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Vorbereiten der Hardware

Schließen Sie die folgende Hardware an Ihren Computer an:

* DevKit-Board
* Micro-USB-Kabel

![Erforderliche Hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Führen Sie diese Schritte aus, um das DevKit mit Ihrem Computer zu verbinden:

1. Stellen Sie eine Verbindung der USB-Seite mit Ihrem Computer her.

2. Stellen Sie eine Verbindung der Micro-USB-Seite mit dem DevKit her.

3. Die grüne Betriebs-LED bestätigt die Verbindung.

   ![Hardwareverbindungen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Schnellstart: Senden von Telemetriedaten vom DevKit an einen IoT-Hub

In diesem Schnellstart wird die vorkompilierte DevKit-Firmware zum Senden der Telemetriedaten an IoT Hub verwendet. Vor dem Ausführen erstellen Sie einen IoT-Hub und registrieren ein Gerät bei diesem Hub.

### <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrieren eines Geräts

Ein Gerät muss bei Ihrer IoT Hub-Instanz registriert sein, um eine Verbindung herstellen zu können. In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell, um ein simuliertes Gerät zu registrieren.

1. Führen Sie in Azure Cloud Shell den folgenden Befehl aus, um die Geräteidentität zu erstellen.

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

   **MyNodeDevice**: Der Name des Geräts, das Sie registrieren. Verwenden Sie **MyNodeDevice** wie gezeigt. Wenn Sie für Ihr Gerät einen anderen Namen wählen, müssen Sie diesen innerhalb des gesamten Artikels verwenden und den Gerätenamen in den Beispielanwendungen aktualisieren, bevor Sie sie ausführen.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Wenn bei der Ausführung von `device-identity` eine Fehlermeldung angezeigt wird, installieren Sie die [Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Führen Sie den folgenden Befehl aus, um Ihrer Cloud Shell-Instanz die Microsoft Azure IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle hinzuzufügen. Die IoT-Erweiterung fügt der Azure-Befehlszeilenschnittstelle spezifische Befehle für IoT Hub, IoT Edge und IoT Device Provisioning Service (DPS) hinzu.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Führen Sie die folgenden Befehle in Azure Cloud Shell aus, um die _Geräteverbindungszeichenfolge_ für das soeben registrierte Gerät abzurufen:

   **YourIoTHubName**: Ersetzen Sie diesen Platzhalter unten durch den Namen, den Sie für Ihren IoT-Hub wählen.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Notieren Sie sich die Geräteverbindungszeichenfolge, die wie folgt aussieht:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Dieser Wert wird später in der Schnellstartanleitung benötigt.

### <a name="send-devkit-telemetry"></a>Senden von DevKit-Telemetriedaten

Das DevKit stellt eine Verbindung mit einem gerätespezifischen Endpunkt in Ihrem IoT-Hub her und sendet Telemetriedaten für Temperatur und Luftfeuchtigkeit.

1. Laden Sie die aktuelle Version der [GetStarted-Firmware](https://aka.ms/devkit/prod/getstarted/latest) für das IoT DevKit herunter.

1. Achten Sie darauf, das IoT DevKit per USB mit Ihrem Computer zu verbinden. Öffnen Sie den Datei-Explorer. Es wird ein USB-Massenspeichergerät mit dem Namen **AZ3166** angezeigt.

    ![Öffnen von Windows-Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Ziehen Sie die soeben heruntergeladene Firmware in das Massenspeichergerät, und legen Sie sie dort ab. Der Flashvorgang findet automatisch statt.

    ![Kopieren von Firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Halten Sie im DevKit die Taste **B** gedrückt, drücken Sie die **Rücksetztaste** , und lassen Sie sie wieder los. Lassen Sie dann die Taste **B** los. Das DevKit wechselt in den AP-Modus. Auf dem Display werden zur Bestätigung die SSID (Service Set-ID) von DevKit und die IP-Adresse des Konfigurationsportals angezeigt.

    ![Resettaste, Taste B und SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Festlegen des AP-Modus](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Verwenden Sie einen Webbrowser an einem anderen WLAN-fähigen Gerät (Computer oder Mobiltelefon), um eine Verbindung mit der im vorherigen Schritt angezeigten SSID des IoT DevKit herzustellen. Wenn Sie zur Eingabe eines Kennworts aufgefordert werden, lassen Sie es leer.

    ![Verbinden mit der SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Öffnen Sie **192.168.0.1** im Browser. Wählen Sie das WLAN aus, mit dem das IoT DevKit eine Verbindung herstellen soll, geben Sie das WLAN-Kennwort ein, und fügen dann die Verbindungszeichenfolge des Geräts ein, die Sie zuvor notiert haben. Klicken Sie anschließend auf „Speichern“.

    ![Konfigurationsoberfläche](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Das IoT DevKit unterstützt nur 2,4-GHz-Netzwerke. Weitere Informationen finden Sie in den [häufig gestellten Fragen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration).

1. Die WLAN-Informationen und die Verbindungszeichenfolge des Geräts wurden im IoT DevKit gespeichert, wenn die Ergebnisseite angezeigt wird.

    ![Konfigurationsergebnis](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Nach der WLAN-Konfiguration werden Ihre Anmeldeinformationen für diese Verbindung auf dem Gerät beibehalten, auch wenn es nicht angeschlossen ist.

1. Das IoT DevKit wird in einigen Sekunden neu gestartet. Auf dem DevKit-Bildschirm wird die IP-Adresse für das DevKit angezeigt, gefolgt von den Telemetriedaten, einschließlich Werten für Temperatur und Luftfeuchtigkeit mit der Anzahl der an Azure IoT Hub gesendeten Nachrichten.

    ![WLAN-IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Senden von Daten](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Führen Sie den folgenden Befehl in Azure Cloud Shell aus, um die an Azure gesendeten Telemetriedaten zu bestätigen:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

Führen Sie die folgenden Schritte durch, um die Entwicklungsumgebung für das DevKit vorzubereiten:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installieren von Visual Studio Code mit dem Azure IoT Tools-Erweiterungspaket

1. Installieren Sie die [Arduino-IDE](https://www.arduino.cc/en/Main/Software). Sie stellt die erforderliche Toolkette für das Kompilieren und Hochladen von Arduino-Code bereit.
    * **Windows**: Verwenden Sie die Windows Installer-Version. Führen Sie die Installation nicht aus dem App Store durch.
    * **macOS**: Ziehen Sie die extrahierte Datei **Arduino.app** in den Ordner `/Applications`.
    * **Ubuntu**: Entpacken Sie die Datei in einem Ordner wie `$HOME/Downloads/arduino-1.8.8`.

2. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/), einen plattformübergreifenden Quellcode-Editor mit leistungsstarken Entwicklertools wie IntelliSense, Codevervollständigung und Debugging sowie zahlreichen Erweiterungen, die über den Marketplace installiert werden können.

3. Starten Sie VS Code, suchen Sie im Erweiterungs-Marketplace nach **Arduino**, und installieren Sie es. Diese Erweiterung bietet erweiterte Funktionen für die Entwicklung auf der Arduino-Plattform.

    ![Installieren von Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Suchen Sie im Erweiterungs-Marketplace nach [Azure IoT Tools](https://aka.ms/azure-iot-tools), und installieren Sie es.

    ![Screenshot mit Azure IoT Tools im Erweiterungs-Marketplace](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Oder kopieren Sie diese URL, und fügen Sie sie in ein Browserfenster ein: `vscode:extension/vsciot-vscode.azure-iot-tools`.

    > [!NOTE]
    > Das Erweiterungspaket Azure IoT Tools enthält die [Azure IoT Device Workbench](https://aka.ms/iot-workbench), mit der das Entwickeln und Debuggen auf verschiedenen IoT Devkit-Geräten durchgeführt werden kann. Die [Azure IoT Hub-Erweiterung](https://aka.ms/iot-toolkit), die auch im Azure IoT Tools-Erweiterungspaket enthalten ist, wird zur Verwaltung und Interaktion mit Azure IoT Hubs verwendet.

5. Konfigurieren Sie VS Code mit Arduino-Einstellungen.

    Klicken Sie in Visual Studio Code auf **Datei > Voreinstellungen > Einstellungen**  (unter macOS auf **Code > Voreinstellungen > Einstellungen**). Klicken Sie dann in der oberen rechten Ecke der Seite **Einstellungen** auf *Einstellungen öffnen (JSON)* .

    ![Installieren von Azure IoT-Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Fügen Sie die folgenden Zeilen hinzu, um Arduino abhängig von Ihrer Plattform zu konfigurieren: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Ersetzen Sie den Platzhalter **{Username}** unten durch Ihren Benutzernamen.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klicken Sie auf `F1`, um die Befehlspalette zu öffnen, geben Sie **Arduino: Board Manager** ein, und wählen Sie den Eintrag aus. Suchen Sie nach **AZ3166**, und installieren Sie die aktuelle Version.

    ![Installieren des DevKit-SDKs](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installieren von ST-Link-Treibern

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) ist die USB-Schnittstelle, die vom IoT-DevKit für die Kommunikation mit Ihrem Entwicklungscomputer verwendet wird. Sie müssen sie unter Windows installieren, damit der kompilierte Gerätecode zum DevKit übertragen werden kann. Führen Sie die Schritte für das jeweilige Betriebssystem aus, um dem Computer Zugriff auf Ihr Gerät zu gewähren.

* **Windows**: Laden Sie den USB-Treiber von der [STMicroelectronics-Website](https://www.st.com/en/development-tools/stsw-link009.html) herunter, und installieren Sie ihn.
* **macOS**: Für macOS ist kein Treiber erforderlich.
* **Ubuntu**: Führen Sie die Befehle im Terminal aus. Melden Sie sich ab und wieder an, damit die Gruppenänderung wirksam wird:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Jetzt sind Sie mit der Vorbereitung und Konfiguration Ihrer Entwicklungsumgebung fertig. Nun erstellen wir das GetStarted-Beispiel, das Sie gerade ausgeführt haben.

## <a name="build-your-first-project"></a>Erstellen Ihres ersten Projekts

### <a name="open-sample-code-from-sample-gallery"></a>Öffnen von Beispielcode aus dem Beispielkatalog

Das IoT DevKit enthält einen umfassenden Katalog mit Beispielen, aus denen Sie lernen können, wie Sie das DevKit mit verschiedenen Azure-Diensten verbinden.

1. Stellen Sie sicher, dass Ihr IoT-DevKit **nicht** mit Ihrem Computer verbunden ist. Starten Sie zuerst Visual Studio Code, und stellen Sie dann eine Verbindung von DevKit mit Ihrem Computer her.

1. Drücken Sie `F1`, um die Befehlspalette zu öffnen, und geben Sie **Azure IoT Device Workbench: Open Examples...** (Azure IoT Device Workbench: Beispiele öffnen) ein. Wählen Sie dann **IoT DevKit** als Board aus.

1. Suchen Sie auf der Seite mit IoT Workbench-Beispielen nach **Erste Schritte**, und klicken Sie auf **Beispiel öffnen**. Wählen Sie dann den Standardpfad zum Herunterladen des Beispielcodes aus.

    ![Beispiel öffnen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Bereitstellen von Azure IoT Hub und dem Gerät

Anstatt Azure IoT Hub und das Gerät über das Azure-Portal bereitzustellen, können Sie dies auch in Visual Studio Code erledigen, ohne die Entwicklungsumgebung verlassen zu müssen.

1. Klicken Sie im neu geöffneten Projektfenster auf `F1`, um die Befehlspalette zu öffnen, geben Sie **Azure IoT Workbench: Provision Azure Services...** (Azure IoT Workbench: Azure-Dienste bereitstellen) ein, und wählen Sie den Eintrag aus. Befolgen Sie die schrittweise Anleitung, um die Bereitstellung Ihres Azure IoT Hubs abzuschließen und das IoT Hub-Gerät zu erstellen.

    ![Bereitstellungsbefehl](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Wenn Sie sich nicht bei Azure angemeldet sind. Befolgen Sie die Popupbenachrichtigung zur Anmeldung.

1. Wählen Sie das Abonnement aus, das Sie verwenden möchten.

    ![Abonnement auswählen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Wählen oder erstellen Sie dann eine neue [Ressourcengruppe](../azure-resource-manager/management/overview.md#terminology).

    ![Auswählen der Ressourcengruppe](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Folgen Sie in der von Ihnen angegebenen Ressourcengruppe der Anleitung, um einen neuen Azure IoT Hub auszuwählen oder zu erstellen.

    ![IoT Hub-Schritte auswählen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![IoT Hub auswählen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Ausgewählter IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Im Ausgabefenster wird der bereitgestellte Azure IoT Hub angezeigt.

    ![Bereitgestellter IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Wählen oder erstellen Sie ein neues Gerät im bereitgestellten Azure IoT Hub.

    ![IoT-Geräteschritte auswählen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Bereitgestelltes IoT-Gerät auswählen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Jetzt haben Sie Azure IoT Hub bereitgestellt und das Gerät darin erstellt. Außerdem wird die Geräteverbindungszeichenfolge in Visual Studio Code zur späteren Konfiguration des IoT DevKit gespeichert.

    ![Bereitstellung abgeschlossen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurieren und Kompilieren des Gerätecodes

1. Überprüfen Sie rechts unten auf der Statusleiste, ob **MXCHIP AZ3166** als ausgewähltes Board angezeigt und ein serieller Port mit **STMicroelectronics** verwendet wird.

    ![Board und COM auswählen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Drücken Sie `F1`, um die Befehlspalette zu öffnen, und geben Sie **Azure IoT Device Workbench: Configure Device Settings...** (Azure IoT Device Workbench: Geräteeinstellungen konfigurieren) ein, wählen Sie den Eintrag aus, und wählen Sie dann **Config Device Connection String > Select IoT Hub Device Connection String** (Geräte-Verbindungszeichenfolge konfigurieren > IoT Hub-Geräte-Verbindungszeichenfolge auswählen) aus.

1. Halten Sie im DevKit die **Taste A** gedrückt, und lassen Sie **Rücksetztaste**  los. Lassen Sie dann die **Taste A** los. Ihr DevKit wechselt in den Konfigurationsmodus und speichert die Verbindungszeichenfolge.

    ![Verbindungszeichenfolge](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klicken Sie erneut auf `F1`, geben Sie **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Gerätecode hochladen) ein, und wählen Sie den angezeigten Befehl aus. Daraufhin wird der Code kompiliert und auf das DevKit hochgeladen.

    ![Arduino-Upload](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Das DevKit startet neu und beginnt mit der Ausführung des Codes.

> [!NOTE]
> Wenn Fehler oder Unterbrechungen auftreten, können Sie den Befehl jederzeit erneut ausführen.

## <a name="test-the-project"></a>Testen des Projekts

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Anzeigen der an Azure IoT Hub gesendeten Telemetriedaten

Klicken Sie in der Statusleiste auf das Symbol für den Netzstecker, um den seriellen Monitor zu öffnen:

![Serieller Monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Die Beispielanwendung wird erfolgreich ausgeführt, wenn folgende Ergebnisse angezeigt werden:

* Der serielle Monitor zeigt die an IoT Hub gesendete Nachricht an.
* Die LED auf dem MXChip IoT DevKit-Board blinkt.

![Ausgabe des seriellen Monitors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Möglicherweise tritt während des Tests ein Fehler auf, bei dem die LED nicht blinkt, das Azure-Portal keine eingehenden Daten vom Gerät anzeigt, aber im OLED-Bildschirm des Geräts **Wird ausgeführt...** angezeigt wird. Wechseln Sie zur Behebung des Problems über das Azure-Portal zum Gerät im IoT-Hub, und senden Sie eine Nachricht an das Gerät. Wenn im seriellen Monitor die folgende Antwort in VS Code angezeigt wird, ist es möglich, dass die direkte Kommunikation vom Gerät auf Routerebene blockiert wird. Überprüfen Sie die für die Verbindungsgeräte konfigurierten Firewall- und Routerregeln. Vergewissern Sie sich außerdem, dass der ausgehende Port 1833 geöffnet ist.
> 
> FEHLER: mqtt_client.c (ln 454): Fehler: Fehler beim Öffnen der Verbindung mit dem Endpunkt  
> INFO: >>>Verbindungsstatus: getrennt  
> FEHLER: tlsio_mbedtls. c (ln 604): Fehler beim Öffnen der zugrunde liegenden E/A  
> ERROR: mqtt_client.c (ln 1042): Fehler: io_open fehlgeschlagen  
> FEHLER: iothubtransport_mqtt_common.c (ln 2283): Fehler beim Herstellen einer Verbindung mit der Adresse atcsliothub.azure-devices.net.  
> INFO: >>>Stellen Sie erneut eine Verbindung her.  
> INFO: IoT Hub-Version: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Anzeigen der von Azure IoT Hub empfangenen Telemetriedaten

Sie können [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verwenden, um D2C-Nachrichten (Gerät-zu-Cloud) in IoT Hub zu überwachen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie die von Ihnen erstellte IoT Hub-Instanz.

    ![Azure-Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Klicken Sie im Bereich **Freigegebene Zugriffsrichtlinien** auf die Richtlinie **iothubowner**, und notieren Sie die Verbindungszeichenfolge Ihrer IoT Hub-Instanz.

    ![Azure IoT Hub-Verbindungszeichenfolge](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Klicken Sie in VS Code auf `F1`, geben Sie **Azure IoT Device Workbench: Set IoT Hub Connection String** (Azure IoT Device Workbench: IoT Hub-Verbindungszeichenfolge festlegen) ein, und wählen Sie den Befehl aus. Kopieren Sie die Verbindungszeichenfolge hinein.

    ![Festlegen der Azure IoT Hub-Verbindungszeichenfolge](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Erweitern Sie den Bereich **AZURE IOT HUB DEVICES** (AZURE IOT HUB-GERÄTE) auf der linken Seite, klicken Sie mit der rechten Maustaste auf den Namen des von Ihnen erstellten Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung von integrierten Ereignisendpunkten starten) aus.

    ![D2C-Nachrichten überwachen](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. Im Bereich **AUSGABE** sehen Sie die eingehenden D2C-Nachrichten an IoT Hub.

    ![Screenshot mit den eingehenden D2C-Nachrichten an IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Überprüfen des Codes

Die `GetStarted.ino` ist die Arduino-Hauptskizzendatei.

![D2C-Nachricht](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Um zu prüfen, wie die Gerätetelemetriedaten an den Azure IoT Hub gesendet werden, öffnen Sie die Datei `utility.cpp` in demselben Ordner. Unter [API-Referenz](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) erfahren Sie, wie Sie Sensoren und Peripheriegeräte mit IoT DevKit verwenden.

Der verwendete `DevKitMQTTClient` ist ein Wrapper des **iothub_client** aus den [Microsoft Azure IoT-SDKs und -Bibliotheken für C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) zur Interaktion mit dem Azure IoT Hub.

## <a name="problems-and-feedback"></a>Probleme und Feedback

Falls Probleme auftreten, können Sie in den [häufig gestellten Fragen zu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nach einer Lösung suchen oder sich über [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) an uns wenden. Sie können uns auch Feedback senden, indem Sie einen Kommentar auf dieser Seite hinterlassen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfolgreich eine Verbindung eines MXChip IoT DevKit-Boards mit IoT Hub hergestellt und die erfassten Sensordaten an IoT Hub gesendet.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
