---
title: Verbinden von Geräten mit X.509-Zertifikaten in einer Azure IoT Central-Anwendung
description: Informationen zum Verbinden von Geräten mit X.509-Zertifikaten mithilfe des Node.js-Geräte-SDKs für IoT Central-Anwendung
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: bffff099e8df2b944cbef50a074ef625267ed238
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944624"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>Informationen zum Verbinden von Geräten mit X.509-Zertifikaten mithilfe des Node.js-Geräte-SDKs für IoT Central-Anwendung

IoT Central unterstützt sowohl SAS (Shared Access Signature) als auch X.509-Zertifikate zum Absichern der Kommunikation zwischen einem Gerät und Ihrer Anwendung. Im Tutorial [Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) wird SAS verwendet. In diesem Artikel erfahren Sie, wie Sie das Codebeispiel so ändern können, dass X.509 verwendet wird.  In Produktionsumgebungen werden X.509-Zertifikate empfohlen. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](./concepts-get-connected.md).

In diesem Artikel werden zwei Möglichkeiten zur Verwendung von X.509 gezeigt – [Gruppenregistrierungen](how-to-connect-devices-x509.md#use-a-group-enrollment), die normalerweise in einer Produktionsumgebung verwendet werden, und [individuelle Registrierungen](how-to-connect-devices-x509.md#use-an-individual-enrollment), die hilfreich bei Tests sind.

## <a name="prerequisites"></a>Voraussetzungen

- Durcharbeiten des Tutorials [Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung (JavaScript)](./tutorial-connect-device.md).
- [Git](https://git-scm.com/download/).
- Laden Sie [OpenSSL](https://www.openssl.org/) herunter, und installieren Sie es. Wenn Sie Windows verwenden, können Sie die Binärdateien von der [Seite „OpenSSL“ auf SourceForge](https://sourceforge.net/projects/openssl/) verwenden.

## <a name="use-a-group-enrollment"></a>Verwenden einer Gruppenregistrierung

Verwenden Sie X.509-Zertifikate bei einer Gruppenregistrierung in einer Produktionsumgebung. In einer Gruppenregistrierung fügen Sie Ihrer IoT Central-Anwendung ein X.509-Stamm- oder -Zwischenzertifikat hinzu. Geräte mit Blattzertifikaten, die vom Stamm- oder Zwischenzertifikat abgeleitet wurden, können eine Verbindung mit Ihrer Anwendung herstellen.

## <a name="generate-root-and-device-cert"></a>Generieren von Stamm- und Gerätezertifikaten

In diesem Abschnitt verwenden Sie ein X.509-Zertifikat zum Verbinden eines Geräts mit einem vom Zertifikat der Registrierungsgruppe abgeleiteten Zertifikat, das eine Verbindung mit Ihrer IoT Central-Anwendung herstellen kann.

> [!WARNING]
> Diese Methode zum Erstellen von X.509-Zertifikaten dient nur zu Testzwecken. Für eine Produktionsumgebung sollten Sie Ihren offiziellen, sicheren Mechanismus für Zertifikatgenerierung verwenden.

1. Öffnen Sie eine Eingabeaufforderung. Klonen Sie das GitHub-Repository für die Skripts zur Zertifikatgenerierung:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

1. Navigieren Sie zum Zertifikatgeneratorskript, und installieren Sie die erforderlichen Pakete:

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

1. Erstellen Sie ein Stammzertifikat, und leiten Sie dann ein Gerätezertifikat durch Ausführen des Skripts ab:

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device sample-device-01 mytestrootcert
    ```

    > [!TIP]
    > Eine Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten.

Mit diesen Befehlen werden jeweils drei Dateien für das Stamm- und das Gerätezertifikat erzeugt.

filename | Inhalt
-------- | --------
\<name\>_cert.pem | Der öffentliche Teil des X.509-Zertifikats
\<name\>_key.pem | Der private Schlüssel für das X.509-Zertifikat
\<name\>_fullchain.pem | Die gesamte Keychain (Schlüsselbund) für das X.509-Zertifikat

## <a name="create-a-group-enrollment"></a>Erstellen einer Gruppenregistrierung

1. Öffnen Sie Ihre IoT Central Anwendung, navigieren Sie im linken Bereich zu **Verwaltung**, und wählen Sie **Geräteverbindung** aus.

1. Wählen Sie **+ Registrierungsgruppe erstellen** aus, und erstellen Sie die neue Registrierungsgruppe _MyX509Group_ mit dem Nachweistyp **Zertifikate (X.509)** .

1. Öffnen Sie die von Ihnen erstellte Registrierungsgruppe, und wählen Sie **Primäre verwalten** aus.

1. Wählen Sie „Dateioption“ aus, und laden Sie die von Ihnen zuvor generierte Stammzertifikatdatei _mytestrootcert_cert.pem_ hoch:

    ![Zertifikatupload](./media/how-to-connect-devices-x509/certificate-upload.png)

1. Zum Abschluss der Überprüfung generieren Sie den Prüfcode, kopieren Sie ihn, und erstellen Sie dann damit an der Eingabeaufforderung ein X.509-Verifizierungszertifikat:

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

1. Laden Sie das signierte Verifizierungszertifikat _verification_cert.pem_ hoch, um die Überprüfung abzuschließen:

    ![Überprüftes Zertifikat](./media/how-to-connect-devices-x509/verified.png)

Jetzt können Sie Geräte verbinden, die ein X.509-Zertifikat haben, das von diesem primären Stammzertifikat abgeleitet wurde.

Nachdem Sie die Registrierungsgruppe gespeichert haben, notieren Sie sich den ID-Bereich.

## <a name="run-sample-device-code"></a>Ausführen des Beispiels für Gerätecode

1. Kopieren Sie die Dateien **sampleDevice01_key.pem** und **sampleDevice01_cert.pem** in den Ordner _azure-iot-sdk-node/device/samples/pnp_, der die Anwendung **simple_thermostat.js** enthält. Sie haben diese Anwendung beim Durcharbeiten des Tutorials [Verbinden eines Geräts (JavaScript)](./tutorial-connect-device.md) verwendet.

1. Navigieren Sie zum Ordner _azure-iot-sdk-node/device/samples/pnp_, der die Anwendung **simple_thermostat.js** enthält, und führen Sie den folgenden Befehl aus, um das X.509-Paket zu installieren:

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

1. Öffnen Sie die Datei **simple_thermostat.js** in einem Text-Editor.

1. Bearbeiten Sie die `require`-Anweisungen wie folgt:

    ```javascript
    const fs = require('fs');
    const X509Security = require('azure-iot-security-x509').X509Security;
    ```

1. Fügen Sie dem Abschnitt „DPS connection information“ die folgenden vier Zeilen hinzu, um die Variable `deviceCert` zu initialisieren:

    ```javascript
    const deviceCert = {
      cert: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_CERT).toString(),
      key: fs.readFileSync(process.env.IOTHUB_DEVICE_X509_KEY).toString()
    };
    ```

1. Bearbeiten Sie die Funktion `provisionDevice`, die den Client erstellt, indem Sie die erste Zeile durch Folgendes ersetzen:

    ```javascript
    var provSecurityClient = new X509Security(registrationId, deviceCert);
    ```

1. Ändern Sie in derselben Funktion die Zeile, in der die Variable `deviceConnectionString` festgelegt wird, wie folgt:

    ```javascript
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    ```

1. Fügen Sie in der Funktion `main` nach der Zeile, über die `Client.fromConnectionString` aufgerufen wird, die folgende Zeile hinzu:

    ```javascript
    client.setOptions(deviceCert);
    ```

1. Legen Sie in der Shellumgebung die folgenden zwei Umgebungsvariablen fest:

    ```cmd/sh
    set IOTHUB_DEVICE_X509_CERT=sampleDevice01_cert.pem
    set IOTHUB_DEVICE_X509_KEY=sampleDevice01_key.pem
    ```

    > [!TIP]
    > Die anderen erforderlichen Umgebungsvariablen haben Sie beim Durcharbeiten des Tutorials [ Erstellen einer Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung](./tutorial-connect-device.md) festgelegt.

1. Führen Sie das Skript aus, und überprüfen Sie, ob das Gerät erfolgreich bereitgestellt wurde:

    ```cmd/sh
    node simple_thermostat.js
    ```

    Sie können auch überprüfen, ob Telemetriedaten auf dem Dashboard angezeigt werden.

    ![Überprüfen der Gerätetelemetrie](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>Verwenden einer individuellen Registrierung

Verwenden Sie X.509-Zertifikate mit einer individuellen Registrierung zum Testen Ihres Geräts und Ihrer Lösung. Bei einer individuellen Registrierung gibt es in Ihrer IoT Central-Anwendung kein X.509-Stamm- oder -Zwischenzertifikat. Geräte verwenden ein selbstsigniertes X.509-Zertifikat zum Herstellen einer Verbindung mit Ihrer Anwendung.

## <a name="generate-self-signed-device-cert"></a>Generieren eines selbstsignierten Zertifikats

In diesem Abschnitt verwenden Sie ein selbstsigniertes X.509-Zertifikat zum Verbinden von Geräten für individuelle Registrierung, die zum Registrieren eines einzelnen Geräts verwendet werden. Selbstsignierte Zertifikate dienen nur zu Testzwecken.

Erstellen Sie ein selbstsigniertes X.509-Gerätezertifikat durch Ausführen des Skripts. Verwenden Sie für den Zertifikatnamen unbedingt nur Kleinbuchstaben und Bindestriche:

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>Erstellen einer individuellen Registrierung

1. Wählen Sie in der Azure IoT Central-Anwendung die Option **Geräte** aus, und erstellen Sie aus der Gerätevorlage „Thermostat“ ein neues Gerät mit der **Geräte-ID** als _mytestselfcertprimary_. Notieren Sie sich den **ID-Bereich**. Sie werden ihn später verwenden.

1. Öffnen Sie das erstellte Gerät, und wählen Sie **Verbinden** aus.

1. Wählen Sie **Einzelne Registrierungen** als **Connect-Methode** und **Zertifikate (X.509)** als Mechanismus aus:

    ![Individuelle Registrierung](./media/how-to-connect-devices-x509/individual-device-connect.png)

1. Wählen Sie unter „primär“ die Option „Datei“ aus, und laden Sie die Zertifikatdatei _mytestselfcertprimary_cert.pem_ hoch, die Sie zuvor generiert haben.

1. Wählen Sie die Dateioption für das sekundäre Zertifikat aus, und laden Sie die Zertifikatdatei _mytestselfcertsecondary_cert.pem_ hoch. Wählen Sie dann **Speichern** aus:

    ![Individuelle Registrierung – Zertifikatupload](./media/how-to-connect-devices-x509/individual-enrollment.png)

Das Gerät wird jetzt mit dem X.509-Zertifikat bereitgestellt.

## <a name="run-a-sample-individual-enrollment-device"></a>Ausführen eines Beispiels für ein Gerät mit individueller Registrierung

1. Kopieren Sie die Dateien _mytestselfcertprimary_key.pem_ und _mytestselfcertprimary_cert.pem_ in den Ordner _azure-iot-sdk-node/device/samples/pnp_, der die Anwendung **simple_thermostat.js** enthält. Sie haben diese Anwendung beim Durcharbeiten des Tutorials [Verbinden eines Geräts (JavaScript)](./tutorial-connect-device.md) verwendet.

1. Ändern Sie die im Beispiel oben verwendeten Umgebungsvariablen wie folgt:

    ```cmd/sh
    set IOTHUB_DEVICE_DPS_DEVICE_ID=mytestselfcertprimary
    set IOTHUB_DEVICE_X509_CERT=mytestselfcertprimary_cert.pem
    set IOTHUB_DEVICE_X509_KEY=mytestselfcertprimary_key.pem
    ```

1. Führen Sie das Skript aus, und überprüfen Sie, ob das Gerät erfolgreich bereitgestellt wurde:

    ```cmd/sh
    node environmentalSensor.js
    ```

    Sie können auch überprüfen, ob Telemetriedaten auf dem Dashboard angezeigt werden.

    ![Telemetrie – Individuelle Registrierung](./media/how-to-connect-devices-x509/telemetry-primary.png)

Sie können die vorstehenden Schritte auch für das Zertifikat _mytestselfcertsecondary_ wiederholen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Geräte mithilfe von X.509-Zertifikaten verbinden können, sollten Sie sich als Nächstes zum Thema [Überwachen der Gerätekonnektivität per Azure CLI](howto-monitor-devices-azure-cli.md) informieren.
