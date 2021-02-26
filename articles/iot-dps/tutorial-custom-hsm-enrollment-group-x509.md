---
title: 'Tutorial: Bereitstellen von X.509-Geräten für Azure IoT Hub mit einem benutzerdefinierten Hardwaresicherheitsmodul (HSM)'
description: In diesem Tutorial werden Registrierungsgruppen verwendet. Das Tutorial enthält eine Beschreibung, wie Sie X.509-Geräte mit einem benutzerdefinierten Hardwaresicherheitsmodul (HSM) und dem C-Geräte-SDK für Azure IoT Hub Device Provisioning Service (DPS) bereitstellen.
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b178aa4a524cb7fcc85c7fc68ac5f772747787a3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052362"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Tutorial: Bereitstellen mehrerer X.509-Geräte mit Registrierungsgruppen

In diesem Tutorial wird beschrieben, wie Sie Gruppen mit IoT-Geräten bereitstellen, bei denen X.509-Zertifikate für die Authentifizierung verwendet werden. Beispielgerätecode aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) wird auf Ihrem Entwicklungscomputer ausgeführt, um die Bereitstellung von X.509-Geräten zu simulieren. Auf realen Geräten wird der Gerätecode auf dem IoT-Gerät bereitgestellt und ausgeführt.

Vergewissern Sie sich, dass Sie mindestens die Schritte unter [Schnellstart: Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](quick-setup-auto-provision.md) ausgeführt haben, bevor Sie mit diesem Tutorial fortfahren. Wenn Sie mit der automatischen Bereitstellung nicht vertraut sind, lesen Sie außerdem die Übersicht zur [Bereitstellung](about-iot-dps.md#provisioning-process). 

In Azure IoT Device Provisioning Service werden zwei Registrierungsarten für Bereitstellungsgeräte unterstützt:

* [Registrierungsgruppen:](concepts-service.md#enrollment-group) Für die Registrierung mehrerer verbundener Geräte
* [Individuelle Registrierung:](concepts-service.md#individual-enrollment) Für die Registrierung eines einzelnen Geräts

Dieses Tutorial ähnelt den vorherigen Tutorials, indem demonstriert wird, wie Sie Registrierungsgruppen zum Bereitstellen von Gruppen mit Geräten verwenden. Allerdings werden in diesem Tutorial anstelle von symmetrischen Schlüsseln X.509-Zertifikate verwendet. Sehen Sie in den vorherigen Tutorials dieses Abschnitts nach, falls Sie einen einfachen Ansatz mit [symmetrischen Schlüsseln](./concepts-symmetric-key-attestation.md) benötigen.

In diesem Tutorial wird das [Beispiel mit einem benutzerdefinierten HSM](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) verwendet, bei dem es um eine Stubimplementierung für die Einrichtung von Schnittstellen mit hardwarebasiertem sicheren Speicher geht. Ein [Hardwaresicherheitsmodul (HSM)](./concepts-service.md#hardware-security-module) wird für die sichere, hardwarebasierte Speicherung von Gerätegeheimnissen verwendet. Ein HSM kann mit einem symmetrischen Schlüssel, X.509-Zertifikat oder TPM-Nachweis verwendet werden, um die sichere Speicherung für Geheimnisse zu ermöglichen. Die hardwarebasierte Speicherung von Gerätegeheimnissen ist nicht erforderlich, wird jedoch dringend empfohlen, um zum Schutz vertraulicher Informationen (etwa des privaten Schlüssels Ihres Gerätezertifikats) beizutragen.


In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Erstellen einer Zertifikatvertrauenskette zum Organisieren einer Gruppe von Geräten mit X.509-Zertifikaten
> * Durchführen eines Eigentumsnachweises mit einem Signaturzertifikat, das zusammen mit der Zertifikatkette verwendet wird
> * Erstellen einer neuen Gruppenregistrierung, für die die Zertifikatkette verwendet wird
> * Einrichten der Entwicklungsumgebung für die Bereitstellung eines Geräts mit Code aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Bereitstellen eines Geräts über die Zertifikatkette mit dem Beispiel für ein benutzerdefiniertes Hardwaresicherheitsmodul (HSM) im SDK

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen gelten für eine Windows-Entwicklungsumgebung, die zum Simulieren der Geräte verwendet wird. Informationen zu Linux oder macOS finden Sie in der SDK-Dokumentation im entsprechenden Abschnitt unter [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 mit der aktivierten Workload [„Desktopentwicklung mit C++“](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development). Visual Studio 2015 und Visual Studio 2017 werden ebenfalls unterstützt. 

    In diesem Artikel wird Visual Studio zum Erstellen des Gerätebeispielcodes verwendet, der auf IoT-Geräten bereitgestellt wird.  Das bedeutet nicht, dass Visual Studio auf dem Gerät selbst erforderlich ist.

* Die neueste Version von [Git](https://git-scm.com/download/) ist installiert.

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Vorbereiten der Azure IoT C SDK-Entwicklungsumgebung

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung vor, die zum Erstellen des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet wird. Das SDK enthält Beispielcode und Tools für die X.509-Gerätebereitstellung mit DPS.

1. Laden Sie das [CMake-Buildsystem](https://cmake.org/download/) herunter.

    Wichtig: Die Voraussetzungen für Visual Studio (siehe [Visual Studio](https://visualstudio.microsoft.com/vs/) und die Workload [„Desktopentwicklung mit C++“](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)) müssen **vor** Beginn der Installation von `CMake` auf dem Computer installiert sein. Sobald die Voraussetzungen erfüllt sind und der Download überprüft wurde, installieren Sie das CMake-Buildsystem.

2. Suchen Sie den Tagnamen für das [aktuelle Release](https://github.com/Azure/azure-iot-sdk-c/releases/latest) des Azure IoT C SDK.

3. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle zum Klonen des aktuellen Releases des [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-GitHub-Repositorys aus. Verwenden Sie das im vorherigen Schritt gefundene Tag als Wert für den Parameter `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

4. Erstellen Sie ein `cmake`-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Das von Ihnen erstellte Verzeichnis `cmake` enthält das Beispiel für ein benutzerdefiniertes HSM und den Beispielcode für die Gerätebereitstellung, in dem das benutzerdefinierte HSM für die X.509-Authentifizierung verwendet wird. 

    Führen Sie den folgenden Befehl in Ihrem Verzeichnis `cmake` aus, um eine spezifische Version des SDK für Ihre Entwicklungsplattform zu erstellen. Der Build enthält einen Verweis auf das Beispiel für ein benutzerdefiniertes HSM. 

    Stellen Sie beim Angeben des Pfads, der unten mit `-Dhsm_custom_lib` verwendet wird, sicher, dass es sich um den Pfad relativ zum zuvor erstellten Verzeichnis `cmake` handelt. Der unten angegebene relative Pfad ist nur ein Beispiel.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Falls `cmake` Ihren C++-Compiler nicht findet, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) aus.

    Nachdem der Buildvorgang abgeschlossen ist, wird in Ihrem Verzeichnis `cmake` eine Visual Studio-Projektmappe generiert. Die letzten Ausgabezeilen lauten in etwa wie folgt:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Erstellen einer X.509-Zertifikatkette

In diesem Abschnitt generieren Sie zum Testen der einzelnen Geräte für dieses Tutorial eine X.509-Zertifikatkette mit drei Zertifikaten. Die Zertifikate verfügen über die im Folgenden beschriebene Hierarchie.

![Tutorial: Gerätezertifikatkette](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Stammzertifikat](concepts-x509-attestation.md#root-certificate): Sie führen den Vorgang für den [Eigentumsnachweis](how-to-verify-certificates.md) durch, um das Stammzertifikat zu verifizieren. Anhand dieser Überprüfung kann DPS dieses Zertifikat als vertrauenswürdig einstufen und die damit signierten Zertifikate verifizieren.

[Zwischenzertifikat](concepts-x509-attestation.md#intermediate-certificate): Zwischenzertifikate werden häufig verwendet, um Geräte logisch nach Produktlinien, Unternehmensbereichen oder anderen Kriterien zu gruppieren. In diesem Tutorial wird eine Zertifikatkette mit einem Zwischenzertifikat verwendet. Das Zwischenzertifikat wird vom Stammzertifikat signiert. Dieses Zertifikat wird auch in der in DPS erstellten Registrierungsgruppe verwendet, um Geräte logisch zu gruppieren. Diese Konfiguration ermöglicht die Verwaltung einer gesamten Gruppe mit Geräten, für die die Gerätezertifikate mit demselben Zwischenzertifikat signiert werden. Sie können Registrierungsgruppen erstellen, um eine Gruppe mit Geräten zu aktivieren oder zu deaktivieren. Weitere Informationen zum Deaktivieren einer Gruppe mit Geräten finden Sie unter [Verweigern eines X.509-Zertifikats der Zwischen- oder Stammzertifizierungsstelle mithilfe einer Registrierungsgruppe](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group).

[Gerätezertifikate:](concepts-x509-attestation.md#end-entity-leaf-certificate) Die Gerätezertifikate (untergeordnete Zertifikate) werden vom Zwischenzertifikat signiert und zusammen mit dem privaten Schlüssel auf dem Gerät gespeichert. Im Idealfall werden diese vertraulichen Elemente sicher mit einem HSM gespeichert. Jedes Gerät gibt sein Zertifikat und seinen privaten Schlüssel zusammen mit der Zertifikatkette an, wenn versucht wird, die Bereitstellung durchzuführen. 

#### <a name="create-root-and-intermediate-certificates"></a>Erstellen von Stamm- und Zwischenzertifikaten

So erstellen Sie die Stamm- und Zwischenteile der Zertifikatkette:

1. Öffnen Sie eine Git Bash-Eingabeaufforderung. Führen Sie die Schritte 1 und 2 aus, indem Sie die Anleitung für die Bash-Shell unter [Verwalten von Zertifizierungsstellen-Testzertifikaten für Beispiele und Tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials) verwenden.

    Dadurch wird ein Arbeitsverzeichnis für die Zertifikatskripts erstellt. Außerdem werden die Beispiele für ein Stammzertifikat und ein Zwischenzertifikat für die Zertifikatkette per OpenSSL generiert. 
    
2. In der Ausgabe sehen Sie, dass der Speicherort des selbstsignierten Stammzertifikats angezeigt wird. Dieses Zertifikat durchläuft den [Eigentumsnachweis](how-to-verify-certificates.md), damit später die Eigentümerschaft verifiziert werden kann.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. Beachten Sie, dass in der Ausgabe der Speicherort des Zwischenzertifikats angegeben ist, das vom Stammzertifikat signiert bzw. ausgestellt wird. Dieses Zertifikat wird für die Registrierungsgruppe verwendet, die Sie später erstellen.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>Erstellen von Gerätezertifikaten

So erstellen Sie die Gerätezertifikate, die vom Zwischenzertifikat in der Kette signiert werden:

1. Führen Sie den folgenden Befehl aus, um ein neues Gerätezertifikat bzw. untergeordnetes Zertifikat mit einem Antragstellernamen zu erstellen, den Sie als Parameter angeben. Verwenden Sie das Beispiel für den Antragstellernamen, der für dieses Tutorial vorgegeben ist: `custom-hsm-device-01`. Dieser Antragstellername ist die Geräte-ID für Ihr IoT-Gerät. 

    > [!WARNING]
    > Verwenden Sie keinen Antragstellernamen, der Leerzeichen enthält. Dieser Gerätename ist die Geräte-ID für das bereitzustellende IoT-Gerät. Sie muss den Regeln für eine Geräte-ID entsprechen. Weitere Informationen finden Sie unter [Geräteidentitätseigenschaften](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    In der folgenden Ausgabe ist angegeben, wo sich das neue Gerätezertifikat befindet. Das Gerätezertifikat wird vom Zwischenzertifikat signiert (ausgestellt).

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. Führen Sie den folgenden Befehl aus, um eine PEM-Datei mit der vollständigen Zertifikatkette zu erstellen, die das neue Gerätezertifikat für `custom-hsm-device-01` enthält.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    Verwenden Sie einen Text-Editor, und öffnen Sie die Zertifikatkettendatei *./certs/new-device-01-full-chain.cert.pem*. Der Text der Zertifikatkette enthält die gesamte Kette mit allen drei Zertifikaten. Sie verwenden diesen Text später in diesem Tutorial als Zertifikatkette mit dem Code für das benutzerdefinierte HSM für `custom-hsm-device-01`.

    Der Text für die gesamte Kette hat das folgende Format:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. Beachten Sie, dass der private Schlüssel für das neue Gerätezertifikat in *./private/new-device.key.pem* geschrieben wird. Benennen Sie die Schlüsseldatei *./private/new-device-01.key.pem* für das Gerät `custom-hsm-device-01` um. Der Text für diesen Schlüssel wird vom Gerät während der Bereitstellung benötigt. Der Text wird später dem Beispiel für das benutzerdefinierte HSM hinzugefügt.

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > Der Text für die Zertifikate enthält nur Informationen zum öffentlichen Schlüssel. 
    >
    > Das Gerät muss aber auch über Zugriff auf den privaten Schlüssel für das Gerätezertifikat verfügen. Dies ist erforderlich, weil das Gerät zur Laufzeit, wenn die Bereitstellung durchgeführt werden soll, diesen Schlüssel für die Verifizierung verwenden muss. Die Vertraulichkeit dieses Schlüssels ist einer der Hauptgründe, warum wir Ihnen empfehlen, zum Schützen von privaten Schlüsseln für ein echtes HSM hardwarebasierten Speicher zu verwenden.

4. Wiederholen Sie die Schritte 1 bis 3 für ein zweites Gerät mit der Geräte-ID `custom-hsm-device-02`. Verwenden Sie für dieses Gerät die folgenden Werte:

    |   BESCHREIBUNG                 |  Wert  |
    | :---------------------------- | :--------- |
    | Antragstellername                  | `custom-hsm-device-02` |
    | Vollständige Zertifikatkettendatei   | *./certs/new-device-02-full-chain.cert.pem* |
    | Name der Datei mit dem privaten Schlüssel          | *private/new-device-02.key.pem* |
    

## <a name="verify-ownership-of-the-root-certificate"></a>Überprüfen der Eigentümerschaft des Stammzertifikats

1. Verwenden Sie die Anleitung unter [Registrieren des öffentlichen Teils eines X.509-Zertifikats und Abrufen eines Prüfcodes](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), um das Stammzertifikat (`./certs/azure-iot-test-only.root.ca.cert.pem`) hochzuladen und einen Prüfcode aus DPS abzurufen.

2. Führen Sie nach dem Erhalt eines Prüfcodes von DPS für das Stammzertifikat den folgenden Befehl aus dem Arbeitsverzeichnis Ihres Zertifikatskripts aus, um ein Verifizierungszertifikat zu generieren.
 
    Der hier angegebene Prüfcode ist nur ein Beispiel. Verwenden Sie den von Ihnen generierten Code aus DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Mit diesem Skript wird ein Zertifikat erstellt, das vom Stammzertifikat signiert wird und für das der Antragstellername auf den Prüfcode festgelegt ist. Anhand dieses Zertifikats kann DPS sicherstellen, dass Sie über Zugriff auf den privaten Schlüssel des Stammzertifikats verfügen. Achten Sie in der Ausgabe des Skripts auf den Speicherort des Verifizierungszertifikats. Dieses Zertifikat wird im Format `.pfx` generiert.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Laden Sie wie unter [Hochladen des signierten Verifizierungszertifikats](how-to-verify-certificates.md#upload-the-signed-verification-certificate) beschrieben das Verifizierungszertifikat hoch, und klicken Sie in DPS auf **Überprüfen**, um den Eigentumsnachweis für das Stammzertifikat durchzuführen.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Aktualisieren des Zertifikatspeichers auf Windows-basierten Geräten

Auf anderen Geräten als Windows-Geräten können Sie die Zertifikatkette aus dem Code als Zertifikatspeicher übergeben.

Auf Windows-basierten Geräten müssen Sie die Signaturzertifikate (Stamm- und Zwischenzertifikat) einem Windows-[Zertifikatspeicher](/windows/win32/secauthn/certificate-stores) hinzufügen. Andernfalls werden die Signaturzertifikate nicht über einen sicheren Kanal mit Transport Layer Security (TLS) an DPS übermittelt.

> [!TIP]
> Sie können anstelle eines sicheren Kanals (Schannel) auch OpenSSL mit dem C SDK verwenden. Weitere Informationen zur Verwendung von OpenSSL finden Sie unter [Verwenden von OpenSSL im SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk).

Fügen Sie die Signaturzertifikate dem Zertifikatspeicher auf Windows-basierten Geräten wie folgt hinzu:

1. Navigieren Sie an einer Git Bash-Eingabeaufforderung zum Unterverzeichnis `certs`, in dem Ihre Signaturzertifikate enthalten sind, und konvertieren Sie sie wie folgt in das `.pfx`-Format.

    Stammzertifikat:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    Zwischenzertifikat:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Klicken Sie mit der rechten Maustaste auf die Schaltfläche **Start** von Windows. Klicken Sie dann mit der linken Maustaste auf **Ausführen**. Geben Sie *certmgr.mcs* ein, und klicken Sie auf **OK**, um das MMC-Snap-In für den Zertifikat-Manager zu starten.

3. Klicken Sie im Zertifikat-Manager unter **Zertifikate > Aktueller Benutzer** auf **Vertrauenswürdige Stammzertifizierungsstellen**. Klicken Sie anschließend im Menü auf **Aktion** > **Alle Aufgaben** > **Importieren**, um `root.pfx` zu importieren.

    * Suchen Sie nach **Personal Information Exchange (.pfx)** .
    * Verwenden Sie `1234` als Kennwort.
    * Fügen Sie das Zertifikat in den Zertifikatspeicher **Vertrauenswürdige Stammzertifizierungsstellen** ein.

4. Klicken Sie im Zertifikat-Manager unter **Zertifikate > Aktueller Benutzer** auf **Zwischenzertifizierungsstellen**. Klicken Sie anschließend im Menü auf **Aktion** > **Alle Aufgaben** > **Importieren**, um `intermediate.pfx` zu importieren.

    * Suchen Sie nach **Personal Information Exchange (.pfx)** .
    * Verwenden Sie `1234` als Kennwort.
    * Fügen Sie das Zertifikat in den Zertifikatspeicher **Zwischenzertifizierungsstellen** ein.

Ihre Signaturzertifikate werden auf dem Windows-basierten Gerät jetzt als vertrauenswürdig eingestuft, und die vollständige Kette kann an DPS übermittelt werden.



## <a name="create-an-enrollment-group"></a>Erstellen einer Registrierungsgruppe

1. Melden Sie sich beim Azure-Portal an, wählen Sie im Menü links die Schaltfläche **Alle Ressourcen** aus, und öffnen Sie Ihren Gerätebereitstellungsdienst.

2. Wählen Sie die Registerkarte **Registrierungen verwalten** und dann oben die Schaltfläche **Registrierungsgruppe hinzufügen** aus.

3. Geben Sie im Bereich **Registrierungsgruppe hinzufügen** die folgenden Informationen ein, und wählen Sie die Schaltfläche **Speichern** aus.

      ![Hinzufügen einer Registrierungsgruppe für den X.509-Nachweis im Portal](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Feld        | Wert           |
    | :----------- | :-------------- |
    | **Gruppenname** | Geben Sie für dieses Tutorial **custom-hsm-x509-devices** ein. |
    | **Nachweistyp** | Wählen Sie die Option **Zertifikat** aus. |
    | **IoT Edge-Gerät** | Wählen Sie **False** aus. |
    | **Zertifikattyp** | Wählen Sie **Zwischenzertifikat** aus. |
    | **Primäres Zertifikat (PEM- oder CER-Datei)** | Navigieren Sie zum zuvor erstellten Zwischenzertifikat ( *./certs/azure-iot-test-only.intermediate.cert.pem*). |


## <a name="configure-the-provisioning-device-code"></a>Konfigurieren des Codes für die Bereitstellung des Geräts

In diesem Abschnitt aktualisieren Sie den Beispielcode mit den Informationen zu Ihrer Device Provisioning Service-Instanz. Wenn ein Gerät authentifiziert wurde, wird es einem IoT-Hub zugewiesen, der mit der in diesem Abschnitt konfigurierten Device Provisioning Service-Instanz verknüpft ist.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihren Gerätebereitstellungsdienst, und notieren Sie sich den Wert unter **_ID-Bereich_** .

    ![Extrahieren von Informationen zum Device Provisioning Service-Endpunkt aus dem Portalblatt](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Starten Sie Visual Studio, und öffnen Sie die neue Projektmappendatei, die im Verzeichnis `cmake` erstellt wurde, das Sie im Stammverzeichnis des Git-Repositorys „azure-iot-sdk-c“ erstellt haben. Die Projektmappendatei hat den Namen `azure_iot_sdks.sln`.

3. Navigieren Sie im Projektmappen-Explorer für Visual Studio zu **Provisioning_Samples > prov_dev_client_sample > Source Files**, und öffnen Sie *prov_dev_client_sample.c*.

4. Suchen Sie die Konstante `id_scope`, und ersetzen Sie den Wert durch Ihren **ID-Bereich**-Wert, den Sie zuvor kopiert haben. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. Suchen Sie die Definition für die Funktion `main()` in der gleichen Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten dargestellt auf `SECURE_DEVICE_TYPE_X509` festgelegt ist.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Klicken Sie mit der rechten Maustaste auf das Projekt **prov\_dev\_client\_sample**, und wählen Sie **Als Startprojekt festlegen** aus.


## <a name="configure-the-custom-hsm-stub-code"></a>Konfigurieren des Stubcodes für das benutzerdefinierte HSM

Die Details der Interaktion mit sicherem hardwarebasiertem Speicher in der Praxis variieren je nach Hardware. Daher sind die Zertifikatketten, die in diesem Tutorial von den simulierten Geräten genutzt werden, im Stubcode für das benutzerdefinierte HSM hartcodiert. In einem realen Fall wird die Zertifikatkette auf der eigentlichen HSM-Hardware gespeichert, um eine höhere Sicherheit für vertrauliche Informationen zu erzielen. Anschließend werden Methoden implementiert, die den Stubmethoden dieses Beispiels ähneln, um die Geheimnisse aus dem hardwarebasierten Speicher auszulesen. 

HSM-Hardware ist zwar nicht erforderlich, es wird jedoch empfohlen, vertrauliche Informationen wie etwa den privaten Schlüssel des Zertifikats zu schützen. Wenn ein tatsächliches HSM durch das Beispiel aufgerufen werden würde, wäre der private Schlüssel nicht im Quellcode vorhanden. Ist der Schlüssel im Quellcode enthalten, wird er für jeden Benutzer verfügbar gemacht, der den Code anzeigen kann. Dieser Schritt wird in diesem Artikel nur zu Lernzwecken ausgeführt.

Wenn Sie den Stubcode für das benutzerdefinierte HSM zum Simulieren der Identität des Geräts mit der ID `custom-hsm-device-01` aktualisieren möchten, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Projektmappen-Explorer von Visual Studio zu **Provisioning_Samples > custom_hsm_example > Source Files**, und öffnen Sie *custom_hsm_example.c*.

2. Aktualisieren Sie den Zeichenfolgenwert der Zeichenfolgenkonstante `COMMON_NAME` mit dem allgemeinen Namen (Common Name), den Sie beim Generieren des Gerätezertifikats verwendet haben.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. In derselben Datei müssen Sie den Zeichenfolgenwert der konstanten Zeichenfolge `CERTIFICATE` mithilfe Ihres Zertifikatkettentexts aktualisieren, den Sie nach dem Generieren Ihrer Zertifikate unter *./certs/new-device-01-full-chain.cert.pem* gespeichert haben.

    Die Syntax des Zertifikattexts muss dem unten angegebenen Muster ohne zusätzliche Leerstellen oder eine Analyse durch Visual Studio entsprechen.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    Das ordnungsgemäße Aktualisieren dieses Zeichenfolgenwerts in diesem Schritt kann sehr mühsam sein und zu Fehlern führen. Wenn Sie die richtige Syntax in Ihrer Git Bash-Eingabeaufforderung generieren möchten, kopieren Sie die folgenden Bash-Shell-Befehle, und fügen Sie sie in Ihre Git Bash-Eingabeaufforderung ein. Drücken Sie dann die **EINGABETASTE**. Diese Befehle generieren die Syntax für den Wert der Zeichenfolgenkonstante `CERTIFICATE`.

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Kopieren Sie den ausgegebenen Zertifikattext für den neuen konstanten Wert, und fügen Sie ihn ein. 


4. In derselben Datei muss der Zeichenfolgenwert der Konstante `PRIVATE_KEY` mit dem privaten Schlüssel für Ihr Gerätezertifikat ebenfalls aktualisiert werden.

    Die Syntax des Texts zum privaten Schlüssel muss dem unten angegebenen Muster ohne zusätzliche Leerstellen oder eine Analyse durch Visual Studio entsprechen.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    Das ordnungsgemäße Aktualisieren dieses Zeichenfolgenwerts in diesem Schritt kann ebenfalls sehr mühsam sein und zu Fehlern führen. Wenn Sie die richtige Syntax in Ihrer Git Bash-Eingabeaufforderung generieren möchten, kopieren Sie die folgenden Bash-Shell-Befehle, und fügen Sie sie ein. Drücken Sie dann die **EINGABETASTE**. Diese Befehle generieren die Syntax für den Wert der Zeichenfolgenkonstante `PRIVATE_KEY`.

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    Kopieren Sie den ausgegebenen Text zum privaten Schlüssel für den neuen konstanten Wert, und fügen Sie ihn ein. 

5. Speichern Sie *custom_hsm_example.c*.

6. Wählen Sie im Visual Studio-Menü die Option **Debuggen** > **Starten ohne Debugging** aus, um die Projektmappe auszuführen. Wählen Sie in der Aufforderung zum erneuten Erstellen des Projekts **Ja** aus, um das Projekt vor der Ausführung neu zu erstellen.

    Die folgende Ausgabe ist ein Beispiel dafür, wie das simulierte Gerät `custom-hsm-device-01` erfolgreich hochfährt und sich mit dem Bereitstellungsdienst verbindet. Das Gerät wurde einem IoT-Hub zugewiesen und registriert:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. Navigieren Sie im Portal zu dem mit Ihrem Bereitstellungsdienst verknüpften IoT-Hub, und wählen Sie die Registerkarte **IoT-Geräte** aus. Nach erfolgreicher Bereitstellung des X.509-Geräts auf dem Hub wird die zugehörige Geräte-ID auf dem Blatt **IoT-Geräte** angezeigt, und der *STATUS* lautet **Aktiviert**. Unter Umständen müssen Sie oben auf die Schaltfläche **Aktualisieren** klicken. 

    ![Registrierung des benutzerdefinierten HSM-Geräts beim IoT-Hub](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. Wiederholen Sie die Schritte 1 bis 7 für ein zweites Gerät mit der Geräte-ID `custom-hsm-device-02`. Verwenden Sie für dieses Gerät die folgenden Werte:

    |   BESCHREIBUNG                 |  Wert  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | Vollständige Zertifikatkette        | Generieren des Texts mithilfe von `input="./certs/new-device-02-full-chain.cert.pem"` |
    | Privater Schlüssel                   | Generieren des Texts mithilfe von `input="./private/new-device-02.key.pem"` |

    Die folgende Ausgabe ist ein Beispiel dafür, wie das simulierte Gerät `custom-hsm-device-02` erfolgreich hochfährt und sich mit dem Bereitstellungsdienst verbindet. Das Gerät wurde einem IoT-Hub zugewiesen und registriert:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die unten angegebenen Schritte aus, um alle in diesem Tutorial erstellten Ressourcen zu löschen, nachdem Sie das Testen und Erkunden dieses Geräteclientbeispiels abgeschlossen haben.

1. Schließen Sie auf Ihrem Computer das Ausgabefenster des Geräteclientbeispiels.
1. Wählen Sie im Azure-Portal im Menü links **Alle Ressourcen** und dann Ihren Gerätebereitstellungsdienst aus. Öffnen Sie **Registrierungen verwalten** für Ihren Dienst, und wählen Sie dann die Registerkarte **Registrierungsgruppen** aus. Aktivieren Sie das Kontrollkästchen neben dem *Gruppennamen* der Gerätegruppe, die Sie in diesem Tutorial erstellt haben, und klicken Sie oben im Bereich auf die Schaltfläche **Löschen**. 
1. Klicken Sie in DPS auf **Zertifikate**. Klicken Sie für jedes Zertifikat, das Sie in diesem Tutorial hochgeladen und verifiziert haben, zunächst auf das Zertifikat und dann auf die Schaltfläche **Löschen**, um es zu entfernen.
1. Wählen Sie im linken Menü im Azure-Portal **Alle Ressourcen** und dann Ihren IoT-Hub aus. Öffnen Sie die Option **IoT-Geräte** für Ihren Hub. Aktivieren Sie das Kontrollkästchen neben der *GERÄTE-ID* des Geräts, das Sie in diesem Tutorial registriert haben. Klicken Sie oben im Bereich auf die Schaltfläche **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein X.509-Gerät mit einem benutzerdefinierten HSM für Ihren IoT-Hub bereitgestellt. Informationen zur Bereitstellung von IoT-Geräten auf mehreren Hubs finden Sie im nächsten Tutorial. 

> [!div class="nextstepaction"]
> [Tutorial: Bereitstellen von Geräten für IoT-Hubs mit Lastausgleich](tutorial-provision-multiple-hubs.md)