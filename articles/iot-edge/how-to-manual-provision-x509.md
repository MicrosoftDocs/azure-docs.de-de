---
title: 'Bereitstellen mit X.509-Zertifikaten: Azure IoT Edge | Microsoft-Dokumentation'
description: Stellen Sie nach der Installation ein IoT Edge-Gerät mit den Geräteidentitätszertifikaten bereit, und führen Sie die Authentifizierung in IoT Hub durch.
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: abb3aa9ca7c9697fef1cf456964154249f0d69f3
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913975"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Einrichten eines Azure IoT Edge-Geräts mit der Authentifizierung mit X.509-Zertifikat

Dieser Artikel enthält die Schritte zum Registrieren eines neuen IoT Edge-Geräts in IoT Hub und zum Konfigurieren des Geräts für die Authentifizierung mit X.509-Zertifikaten.

Die Schritte in diesem Artikel führen Sie durch die sogenannte manuelle Bereitstellung, bei der jedes Gerät manuell mit dem entsprechenden IoT-Hub verbunden wird. Die Alternative ist die automatische Bereitstellung mithilfe von IoT Hub Device Provisioning Service. Diese ist hilfreich, wenn Sie viele Geräte bereitstellen müssen.

<!--TODO: Add auto-provision info/links-->

Bei der manuellen Bereitstellung haben Sie zwei Optionen zum Authentifizieren von IoT Edge-Geräten:

* **Symmetrische Schlüssel** : Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, erstellt der Dienst zwei Schlüssel. Ein Schlüssel befindet sich auf dem Gerät, den es bei der Authentifizierung in IoT Hub vorzeigt.

  Diese Authentifizierungsmethode kann schneller eingerichtet werden, ist dafür aber weniger sicher.

* **X.509, selbstsigniert** : Sie erstellen zwei X. 509-Identitätszertifikate und bewahren diese auf dem Gerät auf. Wenn Sie in IoT Hub eine neue Geräteidentität erstellen, geben Sie Fingerabdrücke aus beiden Zertifikaten an. Wenn sich das Gerät bei IoT Hub authentifiziert, werden die Zertifikate vorgezeigt, und IoT Hub kann überprüfen, ob sie den Fingerabdrücken entsprechen.

  Diese Authentifizierungsmethode ist sicherer und wird in Produktionsszenarios empfohlen.

In diesem Artikel wird der Registrierungs- und Bereitstellungsprozess mit der Authentifizierung mit dem X.509-Zertifikat erläutert. Informationen zum Einrichten eines Geräts mit symmetrischen Schlüsseln finden Sie unter [Einrichten eines Azure IoT Edge-Geräts mit Authentifizierung mit symmetrischem Schlüssel ](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Schritte in diesem Artikel durchführen, sollten Sie über ein Gerät verfügen, auf dem die IoT Edge-Runtime installiert ist. Wenn Sie kein Gerät mit der IoT Edge-Runtime besitzen, führen Sie die in [Installieren oder Deinstallieren der Azure IoT Edge-Runtime](how-to-install-iot-edge.md) beschriebenen Schritte durch.

Die manuelle Bereitstellung mit X.509-Zertifikaten erfordert die IoT Edge-Version 1.0.10 oder höher.

## <a name="create-certificates-and-thumbprints"></a>Erstellen von Zertifikaten und Fingerabdrücken

Das Geräteidentitätszertifikat ist ein Blattzertifikat, das über eine Zertifikatvertrauenskette mit dem obersten X.509-Zertifikat der Zertifizierungsstelle (Certificate Authority, CA) verbunden ist. Für das Geräteidentitätszertifikat muss der allgemeine Name (Common Name, CN) auf die Geräte-ID festgelegt sein, die das Gerät in Ihrem IoT-Hub haben soll.

Geräteidentitätszertifikate werden nur für die Bereitstellung des IoT Edge Geräts und die Authentifizierung des Geräts bei Azure IoT Hub verwendet. Im Gegensatz zu den Zertifizierungsstellenzertifikaten, die das IoT Edge-Gerät Modulen oder Blattgeräten zur Überprüfung präsentiert, signieren sie keine Zertifikate. Weitere Informationen finden Sie unter [Details zur Verwendung von Azure IoT Edge-Zertifikaten](iot-edge-certs.md).

Nachdem Sie das Geräteidentitätszertifikat erstellt haben, sollten Sie zwei Dateien haben: eine CER- oder PEM-Datei, die den öffentlichen Teil des Zertifikats enthält, und eine CER- oder PEM-Datei mit dem privaten Schlüssel des Zertifikats.

Für die manuelle Bereitstellung mit X.509 ist Folgendes erforderlich:

* Zwei Gruppen von Geräteidentitätszertifikaten und privaten Schlüsselzertifikaten. Eine Gruppe von Zertifikat-/Schlüsseldateien wird für die IoT Edge-Runtime bereitgestellt.
* Fingerabdrücke von beiden Geräteidentitätszertifikaten. Die Fingerabdruckwerte bestehen aus 40 Hexadezimalzeichen für SHA-1-Hashes oder 64 Hexadezimalzeichen für SHA-256-Hashes. Beide Fingerabdrücke werden zum Zeitpunkt der Geräteregistrierung für IoT Hub bereitgestellt.

Sollten Sie über keine Zertifikate verfügen, können Sie [Demozertifikate zum Testen von IoT Edge-Gerätefeatures erstellen](how-to-create-test-certificates.md). Gehen Sie wie in dem Artikel beschrieben vor, um Zertifikaterstellungsskripts einzurichten und ein Zertifikat der Stammzertifizierungsstelle sowie zwei IoT Edge-Geräteidentitätszertifikate zu erstellen.

Der Fingerabdruck kann beispielsweise mithilfe des folgenden OpenSSL-Befehls aus einem Zertifikat abgerufen werden:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-a-new-device"></a>Registrieren eines neuen Geräts

Jedes Gerät, das eine Verbindung mit einem IoT-Hub herstellt, verfügt über eine Geräte-ID, die zum Nachverfolgen der Cloud-zu-Gerät- oder Gerät-zu-Cloud-Kommunikation verwendet wird. Sie konfigurieren ein Gerät mit den Verbindungsinformationen, einschließlich des IoT Hub-Hostnamens, der Geräte-ID und der Informationen, die das Gerät zum Authentifizieren bei IoT Hub verwendet.

Bei der Authentifizierung mit dem X.509-Zertifikat werden diese Informationen in Form eines *Fingerabdrucks* bereitgestellt, der den Geräteidentitätszertifikaten entnommen wurde. Diese Fingerabdrücke werden IoT Hub zum Zeitpunkt der Geräteregistrierung bereitgestellt, damit der Dienst das Gerät erkennen kann, wenn es eine Verbindung herstellt.

Sie können verschiedene Tools verwenden, um ein neues IoT Edge-Gerät in IoT Hub zu registrieren und seine Zertifikatfingerabdrücke hochzuladen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Voraussetzungen für das Azure-Portal

Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Erstellen eines IoT Edge-Geräts im Azure-Portal

In Ihrem IoT Hub im Azure-Portal werden IoT Edge-Geräte separat von IoT-Geräten erstellt und verwaltet, die nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Klicken Sie im linken Bereich auf **IoT Edge** , und klicken Sie dann auf **IoT Edge Geräte hinzufügen**.

   ![Hinzufügen eines IoT Edge-Geräts im Azure-Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Geben Sie auf der Seite **Gerät erstellen** die folgenden Informationen ein:

   * Erstellen Sie eine eindeutige Geräte-ID. Notieren Sie sich die Geräte-ID, da Sie diese im nächsten Abschnitt verwenden.
   * Wählen Sie als Authentifizierungstyp **X.509, selbstsigniert** aus.
   * Geben Sie die primären und sekundären Identitätszertifikatfingerabdrücke an. Die Fingerabdruckwerte bestehen aus 40 Hexadezimalzeichen für SHA-1-Hashes oder 64 Hexadezimalzeichen für SHA-256-Hashes.

1. Wählen Sie **Speichern**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Anzeigen von IoT Edge-Geräten im Azure-Portal

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Voraussetzungen für die Azure-Befehlszeilenschnittstelle

* Ein [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) in Ihrem Azure-Abonnement.
* Die [Azure CLI](/cli/azure/install-azure-cli) ist in Ihrer Umgebung vorhanden. Ihre Azure CLI-Version muss mindestens 2.0.70 lauten. Verwenden Sie `az --version`, um dies zu überprüfen. Diese Version unterstützt az-Erweiterungsbefehle, und das Framework für Knack-Befehle wird eingeführt.
* Die [IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension) ist vorhanden.

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Erstellen eines IoT Edge-Geräts mit der Azure-Befehlszeilenschnittstelle

Verwenden Sie den Befehl [az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create), um eine neue Geräteidentität in Ihrem IoT-Hub zu erstellen. Beispiel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Dieser Befehl umfasse mehrere Parameter:

* `--device-id` oder `-d`: Geben Sie einen beschreibenden Namen an, der für Ihren IoT Hub eindeutig ist. Notieren Sie sich die Geräte-ID, da Sie diese im nächsten Abschnitt verwenden.
* `hub-name` oder `-n`: Geben Sie den Namen Ihres IoT Hub an.
* `--edge-enabled` oder `--ee`: Deklarieren Sie, dass das Gerät ein IoT Edge-Gerät ist.
* `--auth-method` oder `--am`: Deklarieren Sie den vom Gerät verwendeten Autorisierungstyp. In diesem Fall verwenden Sie X.509-Zertifikatfingerabdrücke.
* `--primary-thumbprint` oder `--ptp`: Geben Sie einen X.509-Zertifikatfingerabdruck an, der als Primärschlüssel verwendet werden soll.
* `--secondary-thumbprint` oder `--stp`: Geben Sie einen X.509-Zertifikatfingerabdruck an, der als Sekundärschlüssel verwendet werden soll.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Anzeigen von IoT Edge-Geräten mit der Azure-Befehlszeilenschnittstelle

Verwenden Sie den Befehl [az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list), um alle Geräte in Ihrem IoT-Hub anzuzeigen. Beispiel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Fügen Sie das Flag `--edge-enabled` oder `--ee` hinzu, um nur die IoT Edge-Geräte in Ihrem IoT-Hub aufzulisten.

Alle als IoT Edge-Gerät registrierten Geräte weisen die **capabilities.iotEdge** -Eigenschaft mit dem Wert **true** auf.

--- 

## <a name="configure-an-iot-edge-device"></a>Konfigurieren eines IoT Edge-Geräts

Wenn das IoT Edge-Gerät über eine Identität in IoT Hub verfügt, müssen Sie das Gerät mit seiner Cloudidentität und seinen Identitätszertifikaten konfigurieren.

Auf einem Linux-Gerät geben Sie diese Informationen an, indem Sie eine config.yaml-Datei bearbeiten. Auf einem Windows-Gerät geben Sie diese Informationen durch Ausführen eines PowerShell-Skripts an.

# <a name="linux"></a>[Linux](#tab/linux)

1. Öffnen Sie die Konfigurationsdatei auf dem IoT Edge-Gerät.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie den Abschnitt zu Bereitstellungskonfigurationen für die Datei. 

1. Kommentieren Sie den Abschnitt **Manual provisioning configuration using a connection string** (Manuelle Bereitstellungskonfiguration mithilfe einer Verbindungszeichenfolge) aus.

1. Heben Sie die Auskommentierung des Abschnitts **Manual provisioning configuration using an X.509 identity certificate** (Manuelle Bereitstellungskonfiguration mithilfe eines X.509-Identitätszertifikats) auf. Stellen Sie sicher, dass der Zeile **provisioning:** kein Leerzeichen vorangestellt ist und dass geschachtelte Elemente jeweils um zwei Leerzeichen eingerückt sind.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Aktualisieren Sie die folgenden Felder:

   * **iothub_hostname** : Dies ist der Hostname des IoT-Hubs, mit dem das Gerät eine Verbindung herstellt. Beispiel: `{IoT hub name}.azure-devices.net`.
   * **device_id** : Dies ist die ID, die Sie bei der Registrierung des Geräts angegeben haben.
   * **identity_cert** : Dies ist der URI für ein Identitätszertifikat auf dem Gerät. Beispiel: `file:///path/identity_certificate.pem`.
   * **identity_pk** : Dies ist der URI für die Datei mit dem privaten Schlüssel für das bereitgestellte Identitätszertifikat. Beispiel: `file:///path/identity_key.pem`.

1. Speichern und schließen Sie die Datei.

   `CTRL + X`, `Y`, `Enter`

1. Starten Sie nach Eingabe der Bereitstellungsinformationen in der Konfigurationsdatei den Daemon neu:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. Führen Sie PowerShell auf dem IoT Edge-Gerät als Administrator aus.

2. Verwenden Sie den Befehl [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge), um die IoT Edge-Runtime auf Ihrem Computer zu konfigurieren.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Wenn Sie Linux-Container verwenden, fügen Sie dem Flag den `-ContainerOs`-Parameter hinzu. Wählen Sie dieselbe Containeroption wie beim Befehl `Deploy-IoTEdge`, den Sie zuvor ausgeführt haben.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Wenn Sie das Skript „IoTEdgeSecurityDaemon.ps1“ auf Ihr Gerät heruntergeladen haben, um eine Offlineinstallation durchzuführen oder eine bestimmte Version zu installieren, müssen Sie auf die lokale Kopie des Skripts verweisen.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Geben Sie bei entsprechender Aufforderung die folgenden Informationen ein:

   * **IotHubHostName** : Dies ist der Hostname des IoT-Hubs, mit dem das Gerät eine Verbindung herstellt. Beispiel: `{IoT hub name}.azure-devices.net`.
   * **DeviceId** : Dies ist die ID, die Sie bei der Registrierung des Geräts angegeben haben.
   * **X509IdentityCertificate** : Dies ist der absolute Pfad zu einem Identitätszertifikat auf dem Gerät. Beispiel: `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey** : Dies ist der absolute Pfad zu der Datei mit dem privaten Schlüssel für das bereitgestellte Identitätszertifikat. Beispiel: `C:\path\identity_key.pem`.

Wenn Sie ein Gerät manuell bereitstellen, können Sie zusätzliche Parameter verwenden, um den Prozess folgendermaßen anzupassen:

* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Sie können ein bestimmtes edgeAgent-Containerimage deklarieren und Anmeldeinformationen bereitstellen, wenn dieses sich in einer privaten Registrierung befindet.

Weitere Informationen zu diesen zusätzlichen Parametern finden Sie unter [PowerShell-Skripts für IoT Edge unter Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.