---
title: Erstellen eines transparenten Gatewaygeräts mit Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden eines Azure IoT Edge-Geräts als transparentes Gateway, das Informationen von nachgeschalteten Geräten verarbeiten kann
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 722ee6197b467454818026c960e1ce0e5b39efb4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717195"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurieren eines IoT Edge-Geräts als transparentes Gateway

Dieser Artikel enthält detaillierte Anweisungen zur Konfiguration von IoT Edge-Geräten, die als transparentes Gateway für andere Geräte zur Kommunikation mit IoT Hub dienen. In diesem Artikel bezieht sich der Begriff *IoT Edge-Gateway* auf ein IoT Edge-Gerät, das als transparentes Gateway verwendet wird. Weitere Informationen finden Sie unter [Verwendung eines IoT Edge-Geräts als Gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Derzeit gilt Folgendes:
> * Wenn das Gateway vom IoT Hub getrennt wird, können sich nachgeschaltete Geräte nicht am Gateway authentifizieren.
> * Edge-fähige Geräte können keine Verbindung mit IoT Edge-Gateways herstellen. 
> * Nachgeschaltete Geräte können keinen Dateiupload verwenden.

Damit ein Gerät als Gateway fungieren kann, muss es in der Lage sein, sich sicher mit nachgeschalteten Geräten zu verbinden. Mit Azure IoT Edge können Sie mithilfe der Public Key-Infrastruktur sichere Verbindungen zwischen Geräten einrichten. In diesem Fall lassen wir zu, dass ein nachgeschaltetes Gerät eine Verbindung mit einem IoT Edge-Gerät, das als transparentes Gateway fungiert, herstellt. Um eine angemessene Sicherheit zu gewährleisten, sollte das nachgeschaltete Gerät die Identität des IoT Edge-Geräts bestätigen. Ihre Geräte sollen Verbindungen nämlich nur mit Ihren Gateways und nicht mit potenziell schädlichen Gateways herstellen.

Ein nachgeschaltetes Gerät kann eine beliebige Anwendung oder Plattform sein, deren Identität mit dem [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub)-Clouddienst erstellt wurde. Oft verwenden diese Anwendungen das [Azure IoT-Geräte-SDK](../iot-hub/iot-hub-devguide-sdks.md). In der Praxis kann ein nachgeschaltetes Gerät sogar eine Anwendung sein, die auf dem IoT Edge-Gatewaygerät selbst ausgeführt wird. 

Sie können eine beliebige Zertifikatinfrastruktur erstellen, die die für Ihre Gerät-zu-Gateway-Topologie erforderliche Vertrauensstellung ermöglicht. In diesem Artikel wird von der gleichen Zertifikateinrichtung ausgegangen, die Sie auch zum Aktivieren der [X.509 ZS-Sicherheit](../iot-hub/iot-hub-x509ca-overview.md) in IoT Hub verwenden. Hierbei ist ein X.509-Zertifizierungsstellenzertifikat einem bestimmten IoT Hub zugeordnet (der IoT Hub-Besitzerzertifizierungsstelle), und es ist eine Reihe von Zertifikaten, die mit dieser Zertifizierungsstelle signiert werden, sowie eine Zertifizierungsstelle für die IoT Edge-Geräte vorhanden.

![Gatewayzertifikateinrichtung](./media/how-to-create-transparent-gateway/gateway-setup.png)

Das Gateway legt dem nachgeschalteten Gerät während der Initiierung der Verbindung das Zertifizierungsstellenzertifikat des eigenen IoT Edge-Geräts vor. Das nachgeschaltete Gerät überprüft, ob das Zertifizierungsstellenzertifikat des IoT Edge-Geräts mit dem Zertifizierungsstellenzertifikat des Besitzers signiert wurde. Durch diesen Vorgang kann das nachgeschaltete Gerät bestätigen, dass das Gateway aus einer vertrauenswürdigen Quelle stammt.

In den folgenden Schritten werden Sie durch den Prozess zum Erstellen der Zertifikate und zum Installieren an den richtigen Orten geführt.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure IoT Edge-Gerät, das als Gateway konfiguriert wurde. Verwenden Sie die IoT Edge-Installationsschritte für die folgenden Betriebssysteme:
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Sie können mit jedem beliebigen Computer Zertifikate generieren und sie dann auf Ihr IoT Edge-Gerät kopieren.

>[!NOTE]
>Der „Gatewayname“, der zum Erstellen der Zertifikate in dieser Anweisung verwendet wird, muss der gleiche Name sein, den Sie als Hostnamen in Ihrer IoT Edge-config.yaml-Datei und als GatewayHostName in der Verbindungszeichenfolge des Downstreamgeräts verwendet haben. Der „Gatewayname“ muss sich zu einer IP-Adresse auflösen lassen, entweder mithilfe von DNS oder einem Eintrag in der Hostdatei. Auf der Grundlage des verwendeten Protokolls (MQTTS:8883/AMQPS:5671/HTTPS:433) muss Kommunikation zwischen dem Downstreamgerät und dem transparenten IoT Edge möglich sein. Wenn sich dazwischen eine Firewall befindet, muss der entsprechende Port geöffnet sein.

## <a name="generate-certificates-with-windows"></a>Generieren von Zertifikaten unter Windows

Verwenden Sie die Schritte in diesem Abschnitt, um Testzertifikate auf einem Windows-Gerät zu erstellen. Sie können diese Schritte zum Generieren der Zertifikate auf einem Windows IoT Edge-Gerät verwenden. Oder Sie können die Zertifikate auf Ihrem eigenen Windows-Entwicklungscomputer generieren und dann auf ein beliebiges IoT Edge-Gerät kopieren. 

Die in diesem Abschnitt generierten Zertifikate sind nur für Testzwecke bestimmt. 

### <a name="install-openssl"></a>Installieren von OpenSSL

Installieren Sie OpenSSL für Windows auf dem Computer, den Sie zum Generieren der Zertifikate verwenden. Es gibt zwei Möglichkeiten für die Installation von OpenSSL:

   >[!NOTE]
   >Wenn Sie OpenSSL bereits auf Ihrem Windows-Gerät installiert haben, können Sie diesen Schritt überspringen. Stellen Sie jedoch sicher, dass „openssl.exe“ in Ihrer PATH-Umgebungsvariablen verfügbar ist.

* **Einfacher:** Laden Sie beliebige [Binärdateien von OpenSSL-Drittanbietern](https://wiki.openssl.org/index.php/Binaries) herunter und installieren diese, z.B. von [diesem Projekt auf SourceForge](https://sourceforge.net/projects/openssl/). Fügen Sie „openssl.exe“ den vollständigen Pfad zu Ihrer PATH-Umgebungsvariablen hinzu. 
   
* **Empfohlen.** Laden Sie den OpenSSL-Quellcode herunter, und erstellen Sie die Binärdateien entweder selbst oder mithilfe von [vcpkg](https://github.com/Microsoft/vcpkg) auf Ihrem Computer. In den nachfolgend aufgeführten Anweisungen wird in benutzerfreundlichen Schritten „vcpkg“ zum Herunterladen des Quellcodes sowie zum Kompilieren und Installieren von OpenSSL auf Ihrem Windows-Computer verwendet.

   1. Navigieren Sie zu einem Verzeichnis, in dem Sie „vcpkg“ installieren möchten. Wir bezeichnen das Verzeichnis als *\<VCPKGDIR>* . Folgen Sie den Anweisungen zum Herunterladen und Installieren von [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Führen Sie nach der Installation von „vcpkg“ über eine PowerShell-Eingabeaufforderung den folgenden Befehl für die Installation des OpenSSL-Pakets für Windows x64 aus. Diese Installation dauert in der Regel 5 Minuten.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Fügen Sie `<VCPKGDIR>\installed\x64-windows\tools\openssl` Ihrer PATH-Umgebungsvariablen hinzu, damit die Datei „openssl.exe“ aufgerufen werden kann.

### <a name="prepare-creation-scripts"></a>Vorbereiten von Erstellungsskripts

Das Azure IoT-Geräte-SDK für C enthält Skripts, mit denen Sie Testzertifikate erstellen können. In diesem Abschnitt klonen Sie das SDK und konfigurieren PowerShell.

1. Öffnen Sie ein PowerShell-Fenster im Administratormodus. 

2. Klonen Sie das Git-Repository, das Skripts zum Generieren von Zertifikaten enthält, die nicht in der Produktion eingesetzt werden. Diese Skripts helfen Ihnen dabei, die erforderlichen Zertifikate zum Einrichten eines transparenten Gateways zu erstellen. Verwenden Sie den Befehl `git clone`, oder [laden Sie die ZIP-Datei herunter](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Navigieren Sie zu dem Verzeichnis, in dem Sie arbeiten möchten. Wir bezeichnen das Verzeichnis als *\<WRKDIR>* .  Alle Dateien werden in diesem Verzeichnis erstellt.

4. Kopieren Sie die Konfigurations- und Skriptdateien in Ihr Arbeitsverzeichnis. 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Wenn Sie das SDK als ZIP-Datei heruntergeladen haben, lautet der Ordnername `azure-iot-sdk-c-master` und der Rest des Pfads ist identisch. 

5. Legen Sie für die Umgebungsvariable „OPENSSL_CONF“ die Verwendung der Konfigurationsdatei „openssl_root_ca.cnf“ fest.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Aktivieren Sie PowerShell zum Ausführen der Skripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Fügen Sie die Funktionen, die von den Skripts verwendet werden, in den globalen Namespace von PowerShell ein.
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. Überprüfen Sie, ob OpenSSL richtig installiert wurde und keine Namenskonflikte mit vorhandenen Zertifikaten entstehen. Wenn Probleme auftreten, müsste das Skript Informationen darüber enthalten, wie diese auf Ihrem System behoben werden können.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Erstellen von Zertifikaten

In diesem Abschnitt erstellen Sie drei Zertifikate und verbinden sie dann als Kette. Wenn Sie die Zertifikate in einer Kettendatei platzieren, können Sie sie einfach auf Ihrem IoT Edge-Gatewaygerät und auf beliebigen nachgeschalteten Geräten installieren.  

1. Erstellen Sie das Besitzer-Zertifizierungsstellenzertifikat, das ein Zwischenzertifikat signiert. Die Zertifikate werden alle in *\<WRKDIR>* platziert.

      ```powershell
      New-CACertsCertChain rsa
      ```

2. Erstellen Sie mit dem folgenden Befehl das Zertifizierungsstellenzertifikat und den privaten Schlüssel für das IoT Edge-Gerät. Geben Sie einen Namen für das Gatewaygerät an, das zur Benennung der Dateien und bei der Zertifikatsgenerierung verwendet wird. 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Erstellen Sie mit dem folgenden Befehl eine Zertifikatkette aus dem Besitzer-Zertifizierungsstellenzertifikat, dem Zwischenzertifikat und dem Zertifizierungsstellenzertifikat des IoT Edge-Geräts. 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   Das Skript erstellt die folgenden Zertifikate und Schlüssel:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Generieren von Zertifikaten unter Linux

Verwenden Sie die Schritte in diesem Abschnitt, um Testzertifikate auf einem Linux-Gerät zu erstellen. Sie können die Zertifikate auf Ihrem IoT Edge-Gerät selbst generieren oder einen separaten Computer verwenden und die endgültigen Zertifikate auf ein beliebiges IoT Edge-Gerät mit einem unterstützten Betriebssystem kopieren. 

### <a name="prepare-creation-scripts"></a>Vorbereiten von Erstellungsskripts

1. Klonen Sie das Git-Repository, das Skripts zum Generieren von Zertifikaten enthält, die nicht in der Produktion eingesetzt werden. Diese Skripts helfen Ihnen dabei, die erforderlichen Zertifikate zum Einrichten eines transparenten Gateways zu erstellen. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Navigieren Sie zu dem Verzeichnis, in dem Sie arbeiten möchten. Wir bezeichnen das Verzeichnis als *\<WRKDIR>* .  Alle Dateien werden in diesem Verzeichnis erstellt.
  
3. Kopieren Sie die Konfigurations- und Skriptdateien in Ihr Arbeitsverzeichnis.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Konfigurieren Sie OpenSSL, um Zertifikate mithilfe des bereitgestellten Skripts zu generieren. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Erstellen von Zertifikaten

In diesem Abschnitt erstellen Sie drei Zertifikate und verbinden sie dann als Kette. Wenn Sie die Zertifikate in einer Kettendatei platzieren, können Sie sie einfacher auf Ihrem IoT Edge-Gatewaygerät und auf allen nachgeschalteten Geräten installieren.  

1. Erstellen Sie das Zertifizierungsstellenzertifikat für den Eigentümer und ein Zwischenzertifikat. Die Zertifikate werden alle in *\<WRKDIR>* platziert.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Das Skript erstellt die folgenden Zertifikate und Schlüssel:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. Erstellen Sie mit dem folgenden Befehl das Zertifizierungsstellenzertifikat und den privaten Schlüssel für das IoT Edge-Gerät. Geben Sie einen Namen für das Gatewaygerät an, das zur Benennung der Dateien und bei der Zertifikatsgenerierung verwendet wird. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   Das Skript erstellt die folgenden Zertifikate und Schlüssel:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Erstellen Sie eine Zertifikatkette mit dem Namen **new-edge-device-full-chain.cert.pem** aus dem Besitzer-Zertifizierungsstellenzertifikat, dem Zwischenzertifikat und dem Zertifizierungsstellenzertifikat des IoT Edge-Geräts.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Installieren von Zertifikaten auf dem Gateway

Nachdem Sie nun eine Zertifikatkette erstellt haben, müssen Sie diese auf dem IoT Edge-Gateway installieren und die IoT Edge-Runtime so konfigurieren, dass sie auf die neuen Zertifikate verweist. 

1. Kopieren Sie die folgenden Dateien aus *\<WRKDIR>* . Speichern Sie sie auf Ihrem IoT Edge-Gerät. Wir bezeichnen das Zielverzeichnis auf Ihrem IoT Edge-Gerät als *\<CERTDIR>* . 

   Wenn Sie die Zertifikate auf dem IoT Edge-Gerät selbst erstellt haben, können Sie diesen Schritt überspringen und den Pfad für das Arbeitsverzeichnis verwenden.

   * Zertifizierungsstellenzertifikat des Geräts – `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Privater Zertifizierungsstellenschlüssel des Geräts – `<WRKDIR>\private\new-edge-device.key.pem`
   * Zertifizierungsstelle des Besitzers – `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Öffnen Sie die Konfigurationsdatei des IoT Edge-Sicherheitsdaemons. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Legen Sie die Eigenschaften des **Zertifikats** in der Datei „config.yaml“ auf den Pfad fest, in dem Sie die Zertifikat- und Schlüsseldateien für das IoT Edge-Gerät platziert haben.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Stellen Sie auf Linux-Geräten sicher, dass der Benutzer **iotedge** Leseberechtigungen für das Verzeichnis mit den Zertifikaten hat. 

## <a name="deploy-edgehub-to-the-gateway"></a>Bereitstellen von Edge Hub für das Gateway

Wenn Sie IoT Edge zum ersten Mal auf einem Gerät installieren, startet nur ein Systemmodul automatisch: der IoT Edge-Agent. Damit Ihr Gerät als Gateway fungieren kann, benötigen Sie beide Systemmodule. Wenn Sie noch keine Module auf Ihrem Gatewaygerät bereitgestellt haben, erstellen Sie eine Bereitstellung für Ihr Gerät, um das zweite Systemmodul zu starten: den IoT Edge-Hub. Die Bereitstellung sieht leer aus, weil Sie keine Module im Assistenten hinzufügen, doch sie wird beide Systemmodule bereitstellen. 

Mit dem Befehl `iotedge list` können Sie überprüfen, welche Module auf einem Gerät ausgeführt werden.

1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub.

2. Wechseln Sie zu **IoT Edge**, und wählen Sie das IoT Edge-Gerät aus, das Sie als Gateway verwenden möchten.

3. Wählen Sie **Module festlegen** aus.

4. Klicken Sie auf **Weiter**.

5. Auf der Seite **Routen angeben** sollte eine Standardroute aufgeführt sein, über die alle Nachrichten von allen Modulen an IoT Hub gesendet werden. Ist das nicht der Fall, können Sie den folgenden Code eingeben und dann **Weiter** auswählen.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Wählen Sie auf der Seite **Vorlage überprüfen** die Option **Senden** aus.

## <a name="open-ports-on-gateway-device"></a>Öffnen von Ports auf dem Gatewaygerät

Standard IoT Edge-Geräte benötigen keine eingehende Verbindung, um zu funktionieren, da die gesamte Kommunikation mit dem IoT Hub über ausgehende Verbindungen erfolgt. Gatewaygeräte unterscheiden sich jedoch dadurch, dass sie in der Lage sein müssen, Nachrichten von ihren nachgeschalteten Geräten zu empfangen.

Damit ein Gatewayszenario funktioniert, muss mindestens eines der unterstützten Protokolle des IoT Edge-Hubs für den eingehenden Datenverkehr von nachgeschalteten Geräten offen sein. Die unterstützten Protokolle sind MQTT, AMQP und HTTPS.

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Weiterleiten von Nachrichten von nachgeschalteten Geräten
Die IoT Edge-Runtime kann Nachrichten für nachgeschaltete Geräte genau wie Nachrichten von Modulen weiterleiten. Dieses Features ermöglicht es Ihnen, die Analyse in einem auf dem Gateway ausgeführten Modul durchzuführen, bevor Daten an die Cloud gesendet werden. 

Zurzeit werden von nachgeschalteten Geräten gesendete Nachrichten weitergeleitet, indem sie von den von Modulen gesendeten Nachrichten unterschieden werden. Im Gegensatz zu Nachrichten, die von nachgeschalteten Geräten gesendet werden, enthalten von Modulen gesendete Nachrichten eine Systemeigenschaft namens **connectionModuleId**. Sie können die WHERE-Klausel der Route verwenden, um Nachrichten mit dieser Systemeigenschaft auszuschließen. 

Die nachstehende Route wird zum Senden von Nachrichten von einem nachgeschalteten Gerät an ein Modul mit dem Namen `ai_insights` verwendet.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Weitere Informationen zum Routing von Nachrichten finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Nächste Schritte

Da nun ein IoT Edge-Gerät als transparentes Gateway fungiert, müssen Sie Ihre nachgeschalteten Geräte so konfigurieren, dass sie dem Gateway vertrauen und Nachrichten daran senden. Weitere Informationen finden Sie unter [Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway](how-to-connect-downstream-device.md).
