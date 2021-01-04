---
title: Verwenden der automatischen Bereitstellung des Azure IoT Hub Device Provisioning Service zum Registrieren des MXChip IoT DevKit bei IoT Hub | Microsoft-Dokumentation
description: Verwenden der automatischen Bereitstellung des Azure IoT Hub Device Provisioning Service (DPS) zum Registrieren des MXChip IoT DevKit bei IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d6b6649d03da319171b24baa24983972bf270679
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954544"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Verwenden der automatischen Bereitstellung des Azure IoT Hub Device Provisioning Service zum Registrieren des MXChip IoT DevKit bei IoT Hub

In diesem Artikel wird beschrieben, wie der Azure IoT Hub Device Provisioning Service zur [Bereitstellung](about-iot-dps.md#provisioning-process) des MXChip IoT DevKit bei einem Azure IoT Hub verwendet wird. In diesem Tutorial lernen Sie Folgendes:

* Konfigurieren des globalen Endpunkts des Device Provisioning-Diensts auf einem Gerät
* Verwenden eines eindeutigen geheimen Geräteschlüssels (Unique Device Secret, UDS) zum Generieren eines X.509-Zertifikats
* Registrieren eines einzelnen Geräts
* Überprüfen, ob das Gerät registriert ist

Das [MXChip IoT DevKit](https://aka.ms/iot-devkit) ist ein mit Arduino kompatibles All-in-One-Board mit umfangreichen Peripheriegeräten und Sensoren. Mit den Erweiterungspaketen [Azure IoT Device Workbench](https://aka.ms/iot-workbench) und [Azure IoT Tools](https://aka.ms/azure-iot-tools) können Sie in Visual Studio Code dafür entwickeln. Im Lieferumfang des DevKits enthalten ist ein wachsender [Projektkatalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/), der Sie durch das Erstellen eines Prototyps von Lösungen für das Internet der Dinge (Internet of Things, IoT) führt, die Azure-Dienste nutzen.

## <a name="before-you-begin"></a>Voraussetzungen

Um die Schritte in diesem Tutorial auszuführen, erledigen Sie zuerst die folgenden Aufgaben:

* Konfigurieren Sie das WLAN Ihres DevKits, und bereiten Sie Ihre Entwicklungsumgebung vor, indem Sie die in [Verbinden von IoT DevKit AZ3166 mit Azure IoT Hub in der Cloud](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment) im Abschnitt „Vorbereiten der Entwicklungsumgebung“ beschriebenen Schritte ausführen.
* Führen Sie ein Upgrade auf die neueste Firmware (1.3.0 oder höher) mit dem Tutorial [Update der DevKit-Firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) durch.
* Erstellen und Verknüpfen Sie einen IoT-Hub mit einer Device Provisioning-Dienstinstanz, indem Sie die Schritte in [Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md) ausführen.

## <a name="open-sample-project"></a>Öffnen eines Beispielprojekts

1. Stellen Sie sicher, dass Ihr IoT-DevKit **nicht** mit Ihrem Computer verbunden ist. Starten Sie zuerst Visual Studio Code, und stellen Sie dann eine Verbindung von DevKit mit Ihrem Computer her.

1. Drücken Sie `F1`, um die Befehlspalette zu öffnen, und geben Sie **Azure IoT Device Workbench: Open Examples...** (Azure IoT Device Workbench: Beispiele öffnen) ein. Wählen Sie dann **IoT DevKit** als Board aus.

1. Suchen Sie auf der Seite mit IoT Workbench-Beispielen nach **Device Registration with DPS** (Geräteregistrierung bei DPS), und klicken Sie auf **Beispiel öffnen**. Wählen Sie dann den Standardpfad zum Herunterladen des Beispielcodes aus.
    ![Beispiel öffnen](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Speichern eines eindeutigen Gerätegeheimnisses auf dem Gerätesicherheitsspeicher

Die automatische Bereitstellung kann auf einem Gerät basierend auf dem [Nachweismechanismus](concepts-service.md#attestation-mechanism) des Geräts konfiguriert werden. Das MXChip IoT DevKit verwendet die [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) der [Trusted Computing Group](https://trustedcomputinggroup.org). Ein eindeutiger **geheimer Geräteschlüssel** (Unique Device Secret, UDS), der auf einem STSAFE-Sicherheitschip ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) im DevKit gespeichert ist, wird zum Generieren des eindeutigen [X.509-Zertifikats](concepts-x509-attestation.md) für das Gerät verwendet. Das Zertifikat wird später für den Registrierungsprozess im Device Provisioning-Dienst und während der Registrierung zur Laufzeit verwendet.

Ein typischer UDS besteht aus einer Zeichenfolge mit 64 Zeichen, wie im folgenden Beispiel zu sehen:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

So speichern Sie einen UDS auf dem DevKit:

1. Klicken Sie in VS Code auf die Statusleiste, um den COM-Port für das DevKit auszuwählen.
  ![COM-Port auswählen](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Halten Sie im DevKit die **Taste A** gedrückt, und lassen Sie **Rücksetztaste**  los. Lassen Sie dann die **Taste A** los. Ihr DevKit tritt in den Konfigurationsmodus ein.

1. Drücken Sie `F1`, um die Befehlspalette zu öffnen, und geben Sie **Azure IoT Device Workbench: Configure Device Settings... > Config Unique Device String (UDS)** (Azure IoT Device Workbench: Geräteeinstellungen konfigurieren...> Eindeutige Gerätezeichenfolge (UDS) konfigurieren).
  ![UDS konfigurieren](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Notieren Sie sich die generierte UDS-Zeichenfolge. Sie benötigen sie zur Erstellung des X.509-Zertifikats. Drücken Sie anschließend `Enter`.
  ![UDS kopieren](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Vergewissern Sie sich in der Benachrichtigung, dass UDS erfolgreich auf dem STSAFE konfiguriert wurde.
  ![Erfolgsmeldung für die UDS-Konfiguration](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternativ können Sie UDS mithilfe von Hilfsprogrammen wie Putty über einen seriellen Anschluss konfigurieren. Befolgen Sie dazu die Anweisungen unter [Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Verwenden des Konfigurationsmodus).

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualisieren von globalem Geräteendpunkt und ID-Bereich

Im Gerätecode müssen Sie den [Endpunkt für die Gerätebereitstellung](./concepts-service.md#device-provisioning-endpoint) und den ID-Bereich angeben, um die Mandantenisolation sicherzustellen.

1. Wählen Sie im Azure-Portal den Bereich **Übersicht** Ihres Device Provisioning-Diensts aus, und notieren Sie sich die Werte von **Globaler Geräteendpunkt** und **ID-Bereich**.
  ![Globaler Endpunkt und ID-Bereich von Device Provisioning Service](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Öffnen Sie **DevKitDPS.ino**. Suchen und ersetzen Sie `[Global Device Endpoint]` und `[ID Scope]` durch die Werte, die Sie sich gerade notiert hatten.
  ![Endpunkt des Gerätebereitstellungsdiensts](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Füllen Sie die `registrationId`-Variable im Code aus. Es sind nur Kombinationen aus alphanumerischen Kleinbuchstaben und Bindestrichen mit einer maximalen Länge von 128 Zeichen zulässig. Notieren Sie sich auch diesen Wert.
  ![Registrierungs-ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klicken Sie auf `F1`, geben Sie **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Gerätecode hochladen) ein, und wählen Sie den angezeigten Befehl aus. VS Code beginnt dann, den Code zu compilieren und auf Ihr DevKit hochzuladen.
  ![Geräteupload](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generieren eines X.509-Zertifikats

Der von diesem Beispiel verwendete [Nachweismechanismus](./concepts-service.md#attestation-mechanism) ist ein X.509-Zertifikat. Zu dessen Generierung müssen Sie ein Hilfsprogramm verwenden.

1. Klicken Sie in VS Code auf `F1`, geben Sie **Open New Terminal** (Neues Terminal öffnen) ein, und wählen Sie den Befehl aus, um ein Terminalfenster zu öffnen.

1. Führen Sie `dps_cert_gen.exe` im Ordner `tool` aus.

1. Geben Sie den Dateispeicherort der kompilierten Binärdatei als `..\.build\DevKitDPS` an. Fügen Sie dann den **UDS** und die **registrationId** ein, die Sie soeben notiert hatten. 
  ![X.509 generieren](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Ein `.pem`-X.509-Zertifikat wird im gleichen Ordner generiert.
  ![X.509-Datei](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Erstellen eines Geräteregistrierungseintrags

1. Öffnen Sie im Azure-Portal Ihren Gerätebereitstellungsdienst, navigieren Sie zum Abschnitt „Manage Enrollments“ (Registrierungen verwalten), und klicken Sie auf **Add individual enrollment** (Individuelle Registrierung hinzufügen).
  ![Individuelle Registrierung hinzufügen](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klicken Sie auf das Dateisymbol neben **Primäres Zertifikat (PEM- oder CER-Datei)** , um die generierte `.pem`-Datei hochzuladen.
  ![PEM hochladen](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Überprüfen der Registrierung des DevKits bei Azure IoT Hub

Drücken Sie auf Ihrem DevKit die **Reset**-Taste. Daraufhin sollte **DPS verbunden** auf dem DevKit-Bildschirm angezeigt werden. Nach dem Neustart des Geräts werden die folgenden Aktionen ausgeführt:

1. Das Gerät sendet eine Registrierungsanforderung an Ihren Device Provisioning-Dienst.
1. Der Device Provisioning-Dienst sendet eine Registrierungsaufforderung zurück, auf die Ihr Gerät reagiert.
1. Nach erfolgreicher Registrierung sendet der Device Provisioning-Dienst den IoT Hub-URI, die Geräte-ID und den verschlüsselten Schlüssel an das Gerät zurück.
1. Die IoT Hub-Clientanwendung auf dem Gerät stellt eine Verbindung mit Ihrem Hub her.
1. Nachdem eine Verbindung mit dem Hub erfolgreich hergestellt wurde, wird das Gerät im Device Explorer von IoT Hub angezeigt.
  ![Registriertes Gerät](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Probleme und Feedback

Wenn Probleme auftreten, lesen Sie die [häufig gestellten Fragen](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) (FAQs) zum IoT DevKit, oder wenden Sie sich über folgende Kanäle an uns:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie ein Gerät mithilfe von Device Identity Composition Engine sicher beim Device Provisioning-Dienst registriert wird, sodass sich das Gerät automatisch beim Azure IoT Hub registrieren kann. 

Zusammengefasst haben Sie die folgenden Verfahren kennengelernt:

> [!div class="checklist"]
> * Konfigurieren des globalen Endpunkts des Device Provisioning-Diensts auf einem Gerät
> * Verwenden eines eindeutigen geheimen Geräteschlüssels (Unique Device Secret) zum Generieren eines X.509-Zertifikats
> * Registrieren eines einzelnen Geräts
> * Überprüfen, ob das Gerät registriert ist

Informationen zum [Erstellen und Bereitstellen eines simulierten Geräts](./quick-create-simulated-device.md)