---
title: Erstellen von Testzertifikaten – Azure IoT Edge | Microsoft-Dokumentation
description: Erstellen Sie Testzertifikate, und erfahren Sie, wie Sie diese zur Vorbereitung der Produktionsbereitstellung auf einem Azure IoT Edge-Gerät installieren.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c5af77da0ed2c579a478c8ebaaa924882d9a15c6
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927701"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Erstellen von Demozertifikaten zum Testen der Features von IoT Edge-Geräten

IoT Edge-Geräte benötigen Zertifikate für die sichere Kommunikation zwischen der Runtime, den Modulen und etwaigen Downstreamgeräten.
Wenn Sie nicht über eine Zertifizierungsstelle verfügen, um die erforderlichen Zertifikate zu erstellen, können Sie mithilfe von Demozertifikaten IoT Edge-Features in Ihrer Testumgebung testen.
In diesem Artikel werden die Funktionen der Zertifikatgenerierungsskripts beschrieben, die IoT Edge zu Testzwecken bereitstellt.

Diese Zertifikate laufen nach 30 Tagen ab und sollten nicht in Produktionsszenarien verwendet werden.

Sie können auf jedem beliebigen Computer Zertifikate erstellen und sie dann auf Ihr IoT Edge-Gerät kopieren.
Es ist einfacher, die Zertifikate auf dem primären Computer zu erstellen, anstatt sie auf dem eigentlichen IoT Edge-Gerät zu generieren.
Wenn Sie Ihren primären Computer verwenden, können Sie die Skripts einmalig einrichten und dann verwenden, um Zertifikate für mehrere Geräte zu erstellen.

Führen Sie die folgenden Schritte aus, um Demozertifikate zum Testen Ihres IoT Edge-Szenarios zu erstellen:

1. [Richten Sie Skripts](#set-up-scripts) für die Generierung von Zertifikaten auf Ihrem Gerät ein.
2. [Erstellen Sie das Zertifikat der Stammzertifizierungsstelle](#create-root-ca-certificate), mit dem Sie alle anderen Zertifikate für das Szenario signieren.
3. Generieren Sie die Zertifikate, die Sie für das Szenario benötigen, das Sie testen möchten:
   * [Erstellen Sie IoT Edge-Geräteidentitätszertifikate](#create-iot-edge-device-identity-certificates) für automatische Bereitstellung mit dem IoT Hub Device Provisioning Service.
   * [Erstellen Sie IoT Edge-Geräte-Zertifizierungsstellenzertifikate](#create-iot-edge-device-ca-certificates) für IoT Edge-Geräte in Gatewayszenarien.
   * [Erstellen Sie Zertifikate für nachgeschaltete Geräte](#create-downstream-device-certificates) zum Authentifizieren von nachgeschalteten Geräten in einem Gatewayszenario.

## <a name="prerequisites"></a>Voraussetzungen

Ein Entwicklungscomputer mit installiertem Git

## <a name="set-up-scripts"></a>Einrichten von Skripts

Das IoT Edge-Repository auf GitHub enthält Skripts zur Zertifikatgenerierung, die Sie zum Erstellen von Demozertifikaten verwenden können.
Dieser Abschnitt enthält Anweisungen zum Vorbereiten der Skripts, damit diese unter Windows oder Linux auf Ihrem Computer ausgeführt werden können.
Wenn Sie sich auf einem Linux-Computer befinden, fahren Sie mit [Einrichten unter Linux](#set-up-on-linux) fort.

### <a name="set-up-on-windows"></a>Einrichten unter Windows

Zum Erstellen von Demozertifikaten auf einem Windows-Gerät müssen Sie OpenSSL installieren und dann die Generierungsskripts klonen und für die lokale Ausführung in PowerShell einrichten.

#### <a name="install-openssl"></a>Installieren von OpenSSL

Installieren Sie OpenSSL für Windows auf dem Computer, den Sie zum Generieren der Zertifikate verwenden.
Wenn Sie OpenSSL auf Ihrem Windows-Gerät bereits installiert haben, stellen Sie sicher, dass „openssl.exe“ in Ihrer PATH-Umgebungsvariablen zur Verfügung steht.

Es gibt mehrere Möglichkeiten zum Installieren von OpenSSL, einschließlich der folgenden Optionen:

* **Einfacher:** Laden Sie beliebige [Binärdateien von OpenSSL-Drittanbietern](https://wiki.openssl.org/index.php/Binaries) herunter (z. B. von [diesem Projekt auf SourceForge](https://sourceforge.net/projects/openssl/)), und installieren Sie sie. Fügen Sie „openssl.exe“ den vollständigen Pfad zu Ihrer PATH-Umgebungsvariablen hinzu.

* **Empfohlen.** Laden Sie den OpenSSL-Quellcode herunter, und erstellen Sie die Binärdateien entweder selbst oder mithilfe von [vcpkg](https://github.com/Microsoft/vcpkg) auf Ihrem Computer. In den nachfolgend aufgeführten Anweisungen wird in benutzerfreundlichen Schritten „vcpkg“ zum Herunterladen des Quellcodes sowie zum Kompilieren und Installieren von OpenSSL auf Ihrem Windows-Computer verwendet.

   1. Navigieren Sie zu einem Verzeichnis, in dem Sie „vcpkg“ installieren möchten. Folgen Sie den Anweisungen zum Herunterladen und Installieren von [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Führen Sie nach der Installation von vcpkg über eine PowerShell-Eingabeaufforderung den folgenden Befehl für die Installation des OpenSSL-Pakets für Windows x64 aus. Diese Installation dauert in der Regel 5 Minuten.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Fügen Sie `<vcpkg path>\installed\x64-windows\tools\openssl` Ihrer PATH-Umgebungsvariablen hinzu, damit die Datei „openssl.exe“ aufgerufen werden kann.

#### <a name="prepare-scripts-in-powershell"></a>Vorbereiten von Skripts in PowerShell

Das Azure IoT Edge-Git-Repository enthält Skripts, mit denen Sie Testzertifikate erstellen können.
In diesem Abschnitt klonen Sie das IoT Edge-Repository und führen die Skripts aus.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus.

2. Klonen Sie das IoT Edge-Git-Repository, das Skripts zum Generieren von Demozertifikaten enthält. Verwenden Sie den Befehl `git clone`, oder [laden Sie die ZIP-Datei herunter](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigieren Sie zu dem Verzeichnis, in dem Sie arbeiten möchten. In diesem Artikel wird dieses Verzeichnis als *\<WRKDIR>* bezeichnet. Alle Zertifikate und Schlüssel werden in diesem Arbeitsverzeichnis erstellt.

4. Kopieren Sie die Konfigurations- und Skriptdateien aus dem geklonten Repository in Ihr Arbeitsverzeichnis.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Wenn Sie das Repository als ZIP-Datei heruntergeladen haben, lautet der Ordnername `iotedge-master`. Der Rest des Pfads ist identisch.

5. Aktivieren Sie PowerShell zum Ausführen der Skripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Fügen Sie die Funktionen, die von den Skripts verwendet werden, in den globalen Namespace von PowerShell ein.

   ```powershell
   . .\ca-certs.ps1
   ```

   Im PowerShell-Fenster wird eine Warnung angezeigt, das die von diesem Skript generierten Zertifikate ausschließlich für Testzwecke vorgesehen sind und nicht in Produktionsszenarien verwendet werden sollten.

7. Überprüfen Sie, ob OpenSSL richtig installiert wurde und keine Namenskonflikte mit vorhandenen Zertifikaten entstehen. Wenn Probleme auftreten, sollte die Skriptausgabe Informationen darüber enthalten, wie diese auf Ihrem System behoben werden können.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Einrichten unter Linux

Zum Erstellen von Demozertifikaten auf einem Linux-Gerät müssen Sie die Generierungsskripts klonen und für die lokale Ausführung in Bash einrichten.

1. Klonen Sie das IoT Edge-Git-Repository, das Skripts zum Generieren von Demozertifikaten enthält.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigieren Sie zu dem Verzeichnis, in dem Sie arbeiten möchten. Dieses Verzeichnis wird im gesamten Artikel als *\<WRKDIR>* bezeichnet. Alle Zertifikat- und Schlüsseldateien werden in diesem Verzeichnis erstellt.
  
3. Kopieren Sie die Konfigurations- und Skriptdateien aus dem geklonten IoT Edge-Repository in Ihr Arbeitsverzeichnis.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Erstellen des Stammzertifizierungsstellen-Zertifikats

Das Stammzertifizierungsstellen-Zertifikat wird verwendet, um alle anderen Demozertifikate zum Testen eines IoT Edge-Szenarios zu erstellen.
Sie können dasselbe Stammzertifizierungsstellen-Zertifikat weiterhin verwenden, um Demozertifikate für mehrere IoT Edge- oder Downstreamgeräte zu erstellen.

Wenn Sie bereits über ein Stammzertifizierungsstellen-Zertifikat in Ihrem Arbeitsordner verfügen, erstellen Sie kein neues.
Das neue Stammzertifizierungsstellen-Zertifikat überschreibt das alte, und alle mit dem alten erstellten Downstreamzertifikate funktionieren nicht mehr.
Wenn Sie mehrere Stammzertifizierungsstellen-Zertifikate benötigen, achten Sie darauf, sie in getrennten Ordnern zu verwalten.

Bevor Sie mit den Schritten in diesem Abschnitt fortfahren, führen Sie die Schritte im Abschnitt [Einrichten von Skripts](#set-up-scripts) aus, um ein Arbeitsverzeichnis mit den Skripts für die Demozertifikatgenerierung vorzubereiten.

### <a name="windows"></a>Windows

1. Navigieren Sie zu dem Arbeitsverzeichnis, in dem Sie die Skripts für die Zertifikatgenerierung gespeichert haben.

1. Erstellen Sie das Zertifikat der Stammzertifizierungsstelle, und signieren Sie damit ein Zwischenzertifikat. Die Zertifikate werden alle im Arbeitsverzeichnis gespeichert.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Dieser Skriptbefehl erstellt mehrere Zertifikat- und Schlüsseldateien. Wenn in Artikeln jedoch das **Stammzertifizierungsstellen-Zertifikat** gefordert wird, verwenden Sie die folgende Datei:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navigieren Sie zu dem Arbeitsverzeichnis, in dem Sie die Skripts für die Zertifikatgenerierung gespeichert haben.

1. Erstellen Sie das Zertifikat der Stammzertifizierungsstelle und ein Zwischenzertifikat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Dieser Skriptbefehl erstellt mehrere Zertifikat- und Schlüsseldateien. Wenn in Artikeln jedoch das **Stammzertifizierungsstellen-Zertifikat** gefordert wird, verwenden Sie die folgende Datei:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Erstellen von Zertifizierungsstellenzertifikaten für IoT Edge-Geräte

Geräteidentitätszertifikate werden zum Bereitstellen von IoT Edge-Geräten über den DPS (Azure IoT Hub Device Provisioning Service) verwendet.

Geräteidentitätszertifikate werden im Abschnitt **Bereitstellung** der config.yaml-Datei auf dem IoT Edge-Gerät gespeichert.

Bevor Sie mit den Schritten in diesem Abschnitt fortfahren, befolgen Sie die Schritte in den Abschnitten [Einrichten von Skripts](#set-up-scripts) und [Erstellen des Stammzertifizierungsstellen-Zertifikats](#create-root-ca-certificate).

### <a name="windows"></a>Windows

Erstellen Sie mit dem folgenden Befehl das IoT Edge-Geräteidentitätszertifikat und den privaten Schlüssel.

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Der Name, den Sie an diesen Befehl übergeben, ist die Geräte-ID für das IoT Edge-Gerät in IoT Hub.

Der neue Geräteidentitätsbefehl erstellt verschiedene Zertifikats- und Schlüsseldateien, u. a. drei Dateien, die Sie zum Erstellen einer individuellen Registrierung in DPS und Installieren der IoT Edge-Runtime verwenden.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Erstellen Sie mit dem folgenden Befehl das IoT Edge-Geräteidentitätszertifikat und den privaten Schlüssel.

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Der Name, den Sie an diesen Befehl übergeben, ist die Geräte-ID für das IoT Edge-Gerät in IoT Hub.

Das Skript erstellt verschiedene Zertifikats- und Schlüsseldateien, u. a. drei Dateien, die Sie zum Erstellen einer individuellen Registrierung in DPS und Installieren der IoT Edge-Runtime verwenden.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>Erstellen von Zertifizierungsstellenzertifikaten für IoT Edge-Geräte

Jedes IoT Edge-Gerät, das in die Produktion versetzt werden soll, benötigt ein Zertifizierungsstellenzertifikat für das Gerät, auf das in der Datei „config.yaml“ verwiesen wird.
Das Zertifizierungsstellenzertifikat ist für das Erstellen von Zertifikaten für Module zuständig, die auf dem Gerät ausgeführt werden.
Es ist auch für Gatewayszenarien erforderlich, weil das IoT Edge-Gerät damit seine Identität gegenüber Downstreamgeräten nachweist.

Zertifizierungsstellenzertifikate für Geräte werden im Abschnitt **Zertifikate** der config.yaml-Datei auf dem IoT Edge-Gerät gespeichert.

Bevor Sie mit den Schritten in diesem Abschnitt fortfahren, befolgen Sie die Schritte in den Abschnitten [Einrichten von Skripts](#set-up-scripts) und [Erstellen des Stammzertifizierungsstellen-Zertifikats](#create-root-ca-certificate).

### <a name="windows"></a>Windows

1. Navigieren Sie zu dem Arbeitsverzeichnis, in dem sich die Skripts für die Zertifikatgenerierung und das Stammzertifizierungsstellen-Zertifikat befinden.

2. Erstellen Sie mit dem folgenden Befehl das Zertifizierungsstellenzertifikat und den privaten Schlüssel für das IoT Edge-Gerät. Geben Sie einen Namen für das Zertifizierungsstellenzertifikat an.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Mit diesem Befehl werden mehrere Zertifikat- und Schlüsseldateien erstellt. Das folgende Paar aus Zertifikat und Schlüssel muss auf ein IoT Edge Gerät kopiert und in der Datei „config.yaml“ referenziert werden:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

Der an den Befehl **New-CACertsEdgeDevice** übergebene Name sollte nicht mit dem Parameter „hostname“ in der Datei „config.yaml“ oder der ID des Geräts in IoT Hub identisch sein.

### <a name="linux"></a>Linux

1. Navigieren Sie zu dem Arbeitsverzeichnis, in dem sich die Skripts für die Zertifikatgenerierung und das Stammzertifizierungsstellen-Zertifikat befinden.

2. Erstellen Sie mit dem folgenden Befehl das Zertifizierungsstellenzertifikat und den privaten Schlüssel für das IoT Edge-Gerät. Geben Sie einen Namen für das Zertifizierungsstellenzertifikat an.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt. Das folgende Paar aus Zertifikat und Schlüssel muss auf ein IoT Edge Gerät kopiert und in der Datei „config.yaml“ referenziert werden:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Der an den Befehl **create_edge_device_ca_certificate** übergebene Name darf nicht mit dem Parameter für den Hostnamen in der Datei „config.yaml“ oder der ID des Geräts in IoT Hub identisch sein.

## <a name="create-downstream-device-certificates"></a>Erstellen von Zertifikaten für nachgeschaltete Geräte

Wenn Sie ein IoT-Downstreamgerät für ein Gatewayszenario einrichten und die X.509-Authentifizierung nutzen möchten, können Sie Demozertifikate für das Downstreamgerät generieren.
Falls Sie die Authentifizierung mit symmetrischem Schlüssel verwenden möchten, müssen Sie keine zusätzlichen Zertifikate für das Downstreamgerät erstellen.
Es gibt zwei Möglichkeiten, ein IoT-Gerät mit X.509-Zertifikaten zu authentifizieren: mit selbst signierten Zertifikaten oder mit von einer Zertifizierungsstelle signierten Zertifikaten.
Für die Authentifizierung mit selbstsignierten X.509-Zertifikaten (auch als Fingerabdruck-Authentifizierung bezeichnet) müssen Sie neue Zertifikate erstellen und auf Ihrem IoT-Gerät speichern.
Diese Zertifikate verfügen über einen Fingerabdruck, den Sie für die Authentifizierung an IoT Hub übergeben.
Für die Authentifizierung mit X.509-Zertifikaten, die von einer Zertifizierungsstelle (ZS) signiert wurden, muss in IoT Hub ein Zertifikat einer Stammzertifizierungsstelle registriert sein, mit dem Sie die Zertifikate für Ihre IoT-Geräte signieren.
Jedes Gerät mit einem Zertifikat, das durch das Stammzertifizierungsstellen-Zertifikat oder ein direkt abgeleitetes Zertifikat ausgegeben wurde, wird zur Authentifizierung zugelassen.

Die Zertifikatgenerierungsskripts können dabei helfen, Demozertifikate zu erstellen, um diese Authentifizierungsszenarien zu testen.

Bevor Sie mit den Schritten in diesem Abschnitt fortfahren, befolgen Sie die Schritte in den Abschnitten [Einrichten von Skripts](#set-up-scripts) und [Erstellen des Stammzertifizierungsstellen-Zertifikats](#create-root-ca-certificate).

### <a name="self-signed-certificates"></a>Selbstsignierte Zertifikate

Wenn Sie ein IoT-Gerät mit selbst signierten Zertifikaten authentifizieren, müssen Sie Gerätezertifikate basierend auf dem Stammzertifizierungsstellen-Zertifikat für Ihre Lösung erstellen.
Anschließend rufen Sie einen hexadezimalen „Fingerabdruck“ aus den Zertifikaten ab, der gegenüber IoT Hub bereitgestellt wird.
Das IoT-Gerät benötigt außerdem eine Kopie seiner Gerätezertifikate, damit die Authentifizierung bei IoT Hub erfolgen kann.

#### <a name="windows"></a>Windows

1. Navigieren Sie zu dem Arbeitsverzeichnis, in dem sich die Skripts für die Zertifikatgenerierung und das Stammzertifizierungsstellen-Zertifikat befinden.

2. Erstellen Sie zwei Zertifikate (primär und sekundär) für das nachgeschaltete Gerät. Eine einfache Namenskonvention ist das Erstellen der Zertifikate mit dem Namen des IoT-Geräts und dann der primären oder sekundären Bezeichnung. Beispiel:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt. Die folgenden Zertifikat-Schlüssel-Paare müssen auf das Downstream-IoT-Gerät kopiert und in den Anwendungen referenziert werden, die eine Verbindung mit IoT Hub herstellen:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Rufen Sie den SHA1-Fingerabdruck (im IoT Hub-Kontext auch als „Thumbprint“ bezeichnet) aus jedem Zertifikat ab. Der Fingerabdruck ist eine hexadezimale Zeichenfolge mit 40 Zeichen. Verwenden Sie den folgenden OpenSSL-Befehl, um das Zertifikat anzuzeigen und den Fingerabdruck zu suchen:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Führen Sie diesen Befehl zweimal aus – einmal für das primäre Zertifikat und einmal für das sekundäre Zertifikat. Sie geben Fingerabdrücke für beide Zertifikate an, wenn Sie ein neues IoT-Gerät mithilfe von selbst signierten X.509-Zertifikaten registrieren.

#### <a name="linux"></a>Linux

1. Navigieren Sie zu dem Arbeitsverzeichnis, in dem sich die Skripts für die Zertifikatgenerierung und das Stammzertifizierungsstellen-Zertifikat befinden.

2. Erstellen Sie zwei Zertifikate (primär und sekundär) für das nachgeschaltete Gerät. Eine einfache Namenskonvention ist das Erstellen der Zertifikate mit dem Namen des IoT-Geräts und dann der primären oder sekundären Bezeichnung. Beispiel:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt. Die folgenden Zertifikat-Schlüssel-Paare müssen auf das Downstream-IoT-Gerät kopiert und in den Anwendungen referenziert werden, die eine Verbindung mit IoT Hub herstellen:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Rufen Sie den SHA1-Fingerabdruck (im IoT Hub-Kontext auch als „Thumbprint“ bezeichnet) aus jedem Zertifikat ab. Der Fingerabdruck ist eine hexadezimale Zeichenfolge mit 40 Zeichen. Verwenden Sie den folgenden OpenSSL-Befehl, um das Zertifikat anzuzeigen und den Fingerabdruck zu suchen:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Sie geben sowohl den primären als auch den sekundären Fingerabdruck an, wenn Sie ein neues IoT-Gerät mithilfe von selbst signierten X.509-Zertifikaten registrieren.

### <a name="ca-signed-certificates"></a>Von einer Zertifizierungsstelle signierte Zertifikate

Wenn Sie ein IoT-Gerät mit selbst signierten Zertifikaten authentifizieren, müssen Sie das Stammzertifizierungsstellen-Zertifikat für Ihre Lösung in IoT Hub hochladen.
Anschließend führen Sie eine Überprüfung durch, um gegenüber IoT Hub zu belegen, dass Sie das Stammzertifizierungsstellen-Zertifikat besitzen.
Schließlich verwenden Sie das gleiche Stammzertifizierungsstellen-Zertifikat zum Erstellen von Gerätezertifikaten, die auf dem IoT-Gerät abgelegt werden, damit es sich bei IoT Hub authentifizieren kann.

Die Zertifikate in diesem Abschnitt sind für die Schritte unter [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md) ausgelegt.

#### <a name="windows"></a>Windows

1. Laden Sie die Stammzertifizierungsstellen-Zertifikatsdatei aus Ihrem Arbeitsverzeichnis (`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`) auf Ihren IoT-Hub hoch.

2. Verwenden Sie den im Azure-Portal bereitgestellten Code, um zu verifizieren, dass Sie das Stammzertifizierungsstellen-Zertifikat besitzen.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Erstellen Sie eine Vertrauenskette für das nachgeschaltete Gerät. Verwenden Sie dieselbe Geräte-ID, mit der das Gerät bei IoT Hub registriert ist.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt. Die folgenden Zertifikat-Schlüssel-Paare müssen auf das Downstream-IoT-Gerät kopiert und in den Anwendungen referenziert werden, die eine Verbindung mit IoT Hub herstellen:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Laden Sie die Stammzertifizierungsstellen-Zertifikatsdatei aus Ihrem Arbeitsverzeichnis (`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`) auf Ihren IoT-Hub hoch.

2. Verwenden Sie den im Azure-Portal bereitgestellten Code, um zu verifizieren, dass Sie das Stammzertifizierungsstellen-Zertifikat besitzen.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Erstellen Sie eine Vertrauenskette für das nachgeschaltete Gerät. Verwenden Sie dieselbe Geräte-ID, mit der das Gerät bei IoT Hub registriert ist.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt. Die folgenden Zertifikat-Schlüssel-Paare müssen auf das Downstream-IoT-Gerät kopiert und in den Anwendungen referenziert werden, die eine Verbindung mit IoT Hub herstellen:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
