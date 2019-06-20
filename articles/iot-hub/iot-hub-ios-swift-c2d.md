---
title: Cloud-zu-Gerät-Nachrichten mit Azure IoT Hub (iOS) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für iOS Cloud-zu-Gerät-Nachrichten von einer Azure IoT Hub-Instanz an ein Gerät senden.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: e7e8d12af92a566753d8f3d7baf5019bae44de2c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60398897"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Im Artikel [Send telemetry from a device to an IoT hub (Swift)](quickstart-send-telemetry-ios.md) (Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz (Swift)) erfahren Sie, wie Sie eine IoT Hub-Instanz erstellen, eine Geräteidentität darin bereitstellen und eine simulierte Geräte-App programmieren, die D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud) sendet.

In diesem Artikel erfahren Sie Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.

* Empfangen von C2D-Nachrichten auf einem Gerät.

* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu C2D-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Artikels führen Sie zwei Swift-iOS-Projekte aus:

* **simulated-device:** Die App, die in [Send telemetry from a device to an IoT hub (Swift)](quickstart-send-telemetry-ios.md) (Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz (Swift)) erstellt wird. Diese App stellt eine Verbindung mit Ihrer IoT Hub-Instanz her und empfängt C2D-Nachrichten.

* **sample-service:** Dieses Projekt sendet eine C2D-Nachricht über IoT Hub an die simulierte Geräte-App und empfängt die entsprechende Übermittlungsbestätigung.

> [!NOTE]
> IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, Java und JavaScript). Im [Azure IoT Developer Center](https://www.azure.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.

Für dieses Tutorial benötigen Sie Folgendes:

- Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

- Eine aktive IoT Hub-Instanz in Azure. 

- Das Codebeispiel aus den [Azure-Beispielen](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

- Die neueste Version von [Xcode](https://developer.apple.com/xcode/) mit der neuesten Version des iOS SDKs. Diese Schnellstartanleitung wurde mit Xcode 9.3 und iOS 11.3 getestet.

- Die neueste Version von [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="simulate-an-iot-device"></a>Simulieren eines IoT-Geräts

In diesem Abschnitt simulieren Sie ein iOS-Gerät, das eine Swift-Anwendung ausführt, um C2D-Nachrichten von der IoT Hub-Instanz zu empfangen. 

Dabei handelt es sich um das Beispielgerät, das Sie im Artikel [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz (iOS)](quickstart-send-telemetry-ios.md) erstellt haben. Falls die Simulation bereits aktiv ist, können Sie diesen Abschnitt überspringen.

### <a name="install-cocoapods"></a>Installieren von CocoaPods

CocoaPods verwalten Abhängigkeiten für iOS-Projekte, die Bibliotheken von Drittanbietern verwenden.

Navigieren Sie in einem Terminalfenster zum Ordner „Azure-IoT-Samples-iOS“, den Sie im Rahmen der Vorbereitung heruntergeladen haben. Navigieren Sie dann zum Beispielprojekt:

```sh
cd quickstart/sample-device
```

Vergewissern Sie sich, dass Xcode geschlossen ist, und führen Sie den folgenden Befehl aus, um die in der Datei **Podfile** deklarierten CocoaPods zu installieren:

```sh
pod install
```

Der Installationsbefehl installiert nicht nur die erforderlichen Pods für Ihr Projekt, sondern erstellt auch eine Xcode-Arbeitsbereichsdatei, die bereits zur Verwendung der Pods für Abhängigkeiten konfiguriert ist. 

### <a name="run-the-sample-device-application"></a>Ausführen der Beispielgeräteanwendung 

1. Rufen Sie die Verbindungszeichenfolge für Ihr Gerät ab. Diese Zeichenfolge können Sie entweder im [Azure-Portal](https://portal.azure.com) auf dem Blatt mit den Gerätedetails kopieren oder mithilfe des folgenden CLI-Befehls abrufen: 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Öffnen Sie den Beispielarbeitsbereich in Xcode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Erweitern Sie das Projekt **MQTT Client Sample** und anschließend den Ordner mit dem gleichen Namen.  

3. Öffnen Sie **ViewController.swift** zur Bearbeitung in Xcode. 

4. Suchen Sie nach der Variablen **connectionString**, und aktualisieren Sie den Wert mit der Geräteverbindungszeichenfolge aus dem ersten Schritt.

5. Speichern Sie die Änderungen. 

6. Klicken Sie auf die Schaltfläche **Erstellen und Ausführen**, oder drücken Sie die Tastenkombination **BEFEHL+R**, um das Projekt im Geräteemulator auszuführen. 

   ![Ausführen des Projekts](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="simulate-a-service-device"></a>Simulieren eines Dienstgeräts

In diesem Abschnitt simulieren Sie ein zweites iOS-Gerät mit einer Swift-App, die C2D-Nachrichten über die IoT Hub-Instanz sendet. Diese Konfiguration ist in IoT-Szenarien hilfreich, in denen ein iPhone oder iPad als Controller für andere iOS-Geräte fungiert, die mit einer IoT Hub-Instanz verbunden sind. 

### <a name="install-cocoapods"></a>Installieren von CocoaPods

CocoaPods verwalten Abhängigkeiten für iOS-Projekte, die Bibliotheken von Drittanbietern verwenden.

Navigieren Sie zum Ordner „Azure IoT iOS Samples“, den Sie im Rahmen der Vorbereitung heruntergeladen haben. Navigieren Sie dann zum Beispieldienstprojekt:

```sh
cd quickstart/sample-service
```

Vergewissern Sie sich, dass Xcode geschlossen ist, und führen Sie den folgenden Befehl aus, um die in der Datei **Podfile** deklarierten CocoaPods zu installieren:

```sh
pod install
```

Der Installationsbefehl installiert nicht nur die erforderlichen Pods für Ihr Projekt, sondern erstellt auch eine Xcode-Arbeitsbereichsdatei, die bereits zur Verwendung der Pods für Abhängigkeiten konfiguriert ist.

### <a name="run-the-sample-service-application"></a>Ausführen der Beispieldienstanwendung

1. Rufen Sie die Dienstverbindungszeichenfolge für Ihre IoT Hub-Instanz ab. Diese Zeichenfolge können Sie entweder im [Azure-Portal](https://portal.azure.com) auf dem Blatt **Richtlinien für gemeinsamen Zugriff** aus der Richtlinie **iothubowner** kopieren oder mithilfe des folgenden CLI-Befehls abrufen:  

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --output table
    ```

2. Öffnen Sie den Beispielarbeitsbereich in Xcode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. Erweitern Sie das Projekt **AzureIoTServiceSample** und anschließend den Ordner mit dem gleichen Namen.  

4. Öffnen Sie **ViewController.swift** zur Bearbeitung in Xcode. 

5. Suchen Sie nach der Variablen **connectionString**, und aktualisieren Sie den Wert mit der zuvor kopierten Dienstverbindungszeichenfolge.

6. Speichern Sie die Änderungen. 

7. Legen Sie die Emulatoreinstellungen in Xcode auf ein iOS-Gerät fest, bei dem es sich nicht um das Gerät handelt, das Sie zum Ausführen des IoT-Geräts verwendet haben. Xcode kann nicht mehrere Emulatoren vom gleichen Typ ausführen. 

   ![Ändern des Emulatorgeräts](media/iot-hub-ios-swift-c2d/change-device.png)

8. Klicken Sie auf die Schaltfläche **Erstellen und Ausführen**, oder drücken Sie die Tastenkombination **BEFEHL+R**, um das Projekt im Geräteemulator auszuführen. 

   ![Ausführen des Projekts](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

Nun können Sie mit den beiden Anwendungen C2D-Nachrichten senden und empfangen.

1. Klicken Sie in der App **iOS App Sample**, die auf dem simulierten IoT-Gerät ausgeführt wird, auf **Start**. Die Anwendung beginnt damit, D2C-Nachrichten zu senden und auf C2D-Nachrichten zu lauschen. 

   ![Anzeigen der Beispiel-IoT-Geräte-App](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Geben Sie in der App **IoTHub Service Client Sample**, die auf dem simulierten Dienstgerät ausgeführt wird, die ID für das IoT-Gerät ein, an das Sie eine Nachricht senden möchten. 

3. Schreiben Sie eine Klartextnachricht, und klicken Sie auf **Send** (Senden). 

    Daraufhin werden mehrere Aktionen ausgeführt: Das Dienstbeispiel sendet die Nachricht an Ihre IoT Hub-Instanz, auf die die App dank der angegebenen Dienstverbindungszeichenfolge Zugriff hat. Die IoT Hub-Instanz überprüft die Geräte-ID und sendet die Nachricht an das Zielgerät und eine Bestätigung an das Quellgerät. Die auf dem simulierten IoT-Gerät ausgeführte App prüft, ob Nachrichten von der IoT Hub-Instanz vorliegen, und gibt den Text der neuesten Nachricht auf dem Bildschirm aus.

    Die Ausgabe sollte wie im folgenden Beispiel aussehen:

   ![Anzeigen von C2D-Nachrichten](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden. 

Beispiele für vollständige End-to-End-Lösungen, die IoT Hub nutzen, finden Sie in der [Dokumentation zu Azure IoT Solution Accelerators](https://azure.microsoft.com/documentation/suites/iot-suite/).

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).
