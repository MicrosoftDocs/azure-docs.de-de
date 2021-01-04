---
title: Authentifizieren nachgeschalteter Geräte – Azure IoT Edge | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie nachgeschaltete Geräten oder Blattgeräte bei IoT Hub authentifizieren und ihre Verbindung über Azure IoT Edge-Gatewaygeräte weiterleiten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2dd7cac8370c261f24f5587e801bd621fbdb0f0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016997"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Authentifizieren eines nachgeschalteten Geräts bei Azure IoT Hub

In einem Szenario mit transparenten Gateways benötigen nachgeschaltete Geräte (auch als Blattgeräte oder untergeordnete Geräte bezeichnet) wie alle anderen Geräte Identitäten in IoT Hub. In diesem Artikel werden die Optionen für die Authentifizierung von nachgeschalteten Geräten bei IoT Hub beschrieben. Außerdem wird gezeigt, wie Sie die Gatewayverbindung deklarieren.

Es gibt drei allgemeine Schritte zum Einrichten einer erfolgreichen Verbindung mit einem transparenten Gateway. In diesem Artikel wird der zweite Schritt behandelt:

1. Konfigurieren Sie das Gatewaygerät als Server, sodass sich nachgeschaltete Geräte mit ihm sicher verbinden können. Richten Sie das Gateway so ein, dass es Nachrichten von nachgeschalteten Geräten empfängt und an das richtige Ziel weiterleitet. Die dazu erforderlichen Schritte finden Sie unter [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md).
2. **Erstellen Sie eine Geräteidentität für das nachgeschaltete Gerät, damit es sich bei IoT Hub authentifizieren kann. Konfigurieren Sie das nachgeschaltete Gerät zum Senden von Nachrichten über das Gatewaygerät.**
3. Verbinden Sie das nachgeschaltete Gerät mit dem Gatewaygerät, und beginnen Sie mit dem Senden von Nachrichten. Die dazu erforderlichen Schritte finden Sie unter [Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway](how-to-connect-downstream-device.md).

Nachgeschaltete Geräte können sich bei IoT Hub mithilfe einer von drei Methoden authentifizieren: symmetrische Schlüssel (auch als SAS-Schlüssel bezeichnet), selbst signierte X.509-Zertifikate oder von einer Zertifizierungsstelle signierte X.509-Zertifikate. Die Authentifizierungsschritte ähneln den Schritten zum Einrichten anderer IoT Edge-Geräte mit IoT Hub. Kleinere Unterschiede bestehen bei der Deklaration der Gatewaybeziehung.

Die automatische Bereitstellung von nachgeschalteten Geräten mit dem Azure IoT Hub-Gerätebereitstellungsdienst (Device Provisioning Service, DPS) wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte in [Konfigurieren eines IoT Edge-Geräts als transparentes Gateway](how-to-create-transparent-gateway.md) aus.

Wenn Sie die X.509-Authentifizierung verwenden, generieren Sie Zertifikate für Ihr nachgeschaltetes Gerät. Halten Sie dasselbe Zertifikat der Stammzertifizierungsstelle und das Zertifikat zum Generieren des Skripts, das Sie für den Artikel zum transparenten Gateway verwendet haben, für erneute Verwendung zur Verfügung.

Im Artikel wird an mehreren Stellen auf den *Gatewayhostnamen* verwiesen. Der Gatewayhostname wird im **hostname**-Parameter der Datei „config.yaml“ auf dem IoT Edge-Gatewaygerät deklariert. In der Verbindungszeichenfolge des nachgeschalteten Geräts wird darauf verwiesen. Der Gatewayhostname muss in eine IP-Adresse aufgelöst werden können – entweder mithilfe von DNS oder einem Eintrag in der Hostdatei auf dem nachgeschalteten Gerät.

## <a name="register-device-with-iot-hub"></a>Registrieren des Geräts bei IoT Hub

Wählen Sie aus, wie sich Ihr nachgeschaltetes Gerät bei IoT Hub authentifizieren soll:

* [Authentifizierung mit symmetrischem Schlüssel](#symmetric-key-authentication): IoT Hub erstellt einen Schlüssel, den Sie auf dem nachgeschalteten Gerät speichern. Bei der Authentifizierung des Geräts überprüft IoT Hub, ob die beiden Schlüssel übereinstimmen. Sie müssen keine zusätzlichen Zertifikate erstellen, wenn Sie die Authentifizierung mit symmetrischem Schlüssel verwenden möchten.

  Mit dieser Methode können Sie schneller starten, wenn Sie Gateways in einem Entwicklungs- oder Testszenario testen.

* [Selbstsignierte X.509-Zertifizierung](#x509-self-signed-authentication): Wird manchmal als „Authentifizierung per Fingerabdruck“ bezeichnet, weil Sie den Fingerabdruck aus dem X.509-Zertifikat des Geräts bei IoT Hub freigeben.

  Zertifikatauthentifizierung wird für Geräte in Produktionsszenarien empfohlen.

* [Authentifizierung mit einem X.509-Zertifikat, das von einer Zertifizierungsstelle signiert wurde](#x509-ca-signed-authentication): Laden Sie das Zertifikat der Stammzertifizierungsstelle zu IoT Hub hoch. Wenn das X.509-Zertifikat für die Authentifizierung von einem Gerät angezeigt wird, überprüft IoT Hub, ob es zu einer Vertrauenskette gehört, die von demselben Zertifikat der Stammzertifizierungsstelle signiert wurde.

  Zertifikatauthentifizierung wird für Geräte in Produktionsszenarien empfohlen.

### <a name="symmetric-key-authentication"></a>Authentifizierung mit symmetrischen Schlüsseln

Die Authentifizierung mit symmetrischen Schlüsseln (oder SAS-Schlüsselauthentifizierung) ist die einfachste Möglichkeit für die Authentifizierung bei IoT Hub. Bei der Authentifizierung mit symmetrischen Schlüsseln wird der ID Ihres IoT-Gerätes in IoT Hub ein Base64-Schlüssel zugeordnet. Sie fügen diesen Schlüssel in Ihre IoT-Anwendungen ein, sodass Ihr Gerät diesen beim Herstellen einer Verbindung mit IoT Hub vorweisen kann.

Fügen Sie Ihrem IoT-Hub ein neues IoT-Gerät hinzu. Verwenden Sie dazu entweder das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder die IoT-Erweiterung für Visual Studio Code. Denken Sie daran, dass nachgeschaltete Geräte in IoT Hub als reguläre IoT-Geräte – nicht als IoT Edge-Geräte – angegeben werden müssen.

Geben Sie beim Erstellen der neuen Geräteidentität die folgenden Informationen an:

* Erstellen Sie eine ID für Ihr Gerät.

* Wählen Sie als Authentifizierungstyp **Symmetrischer Schlüssel** aus.

* Wählen Sie **Übergeordnetes Gerät festlegen** und dann das IoT Edge-Gatewaygerät aus, über das dieses nachgeschaltete Gerät verbunden werden soll. Sie können das übergeordnete Gerät später jederzeit ändern.

   ![Erstellen der Geräte-ID bei der Authentifizierung mit symmetrischem Schlüssel im Portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Sie können auch die [IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension) verwenden, um diesen Vorgang abzuschließen. Im folgenden Beispiel wird mithilfe des Befehls [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) ein neues IoT-Gerät mit Authentifizierung mit symmetrischem Schlüssel erstellt und ein übergeordnetes Gerät zugewiesen:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Als Nächstes müssen Sie [die Verbindungszeichenfolge abrufen und ändern](#retrieve-and-modify-connection-string), damit Ihr Gerät weiß, dass es eine Verbindung über sein Gateway herstellen soll.

### <a name="x509-self-signed-authentication"></a>Selbstsignierte X.509-Authentifizierung

Für die selbstsignierte X.509-Authentifizierung (manchmal auch als „Authentifizierung per Fingerabdruck bezeichnet) müssen Sie Zertifikate erstellen und auf Ihrem nachgeschalteten Gerät speichern. Diese Zertifikate verfügen über einen Fingerabdruck, den Sie für die Authentifizierung an IoT Hub übergeben.

1. Erstellen Sie mithilfe Ihres Zertifizierungsstellenzertifikats zwei Gerätezertifikate (primär und sekundär) für das nachgeschaltete Gerät.

   Wenn Sie über keine Zertifizierungsstelle zum Erstellen von X.509-Zertifikaten verfügen, können Sie die IoT Edge-Demozertifikatskripts zum [Erstellen von Zertifikaten für nachgeschaltete Geräte](how-to-create-test-certificates.md#create-downstream-device-certificates) verwenden. Führen Sie die folgenden Schritte zum Erstellen von selbstsignierten Zertifikaten aus. Verwenden Sie dasselbe Zertifikat der Stammzertifizierungsstelle, das die Zertifikate für Ihr Gatewaygerät generiert hat.

   Wenn Sie Ihre eigenen Zertifikate erstellen, sorgen Sie dafür, dass der Antragstellername des Gerätezertifikats auf die Geräte-ID festgelegt ist, die Sie bei der Registrierung des IoT-Geräts im Azure IoT Hub verwenden. Diese Einstellung ist für die Authentifizierung erforderlich.

2. Rufen Sie den SHA1-Fingerabdruck aller Zertifikate ab. Es handelt sich dabei um eine hexadezimale Zeichenfolge mit 40 Zeichen. Verwenden Sie den folgenden OpenSSL-Befehl, um das Zertifikat anzuzeigen und den Fingerabdruck zu suchen:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Führen Sie diesen Befehl zweimal aus – einmal für das primäre Zertifikat und einmal für das sekundäre Zertifikat. Sie geben Fingerabdrücke für beide Zertifikate an, wenn Sie ein neues IoT-Gerät mithilfe von selbst signierten X.509-Zertifikaten registrieren.

3. Navigieren Sie im Azure-Portal zu Ihrem IoT-Hub, und erstellen Sie eine neue IoT-Geräteidentität mit den folgenden Werten:

   * Geben Sie die **Geräte-ID** an, die mit dem Antragstellernamen Ihrer Gerätezertifikate übereinstimmt.
   * Wählen Sie als Authentifizierungstyp **X.509, selbstsigniert** aus.
   * Fügen Sie die hexadezimalen Zeichenfolgen ein, die Sie aus den primären und sekundären Zertifikaten Ihres Geräts kopiert haben.
   * Wählen Sie **Übergeordnetes Gerät festlegen** und dann das IoT Edge-Gatewaygerät aus, über das dieses nachgeschaltete Gerät verbunden wird. Sie können das übergeordnete Gerät später jederzeit ändern.

   ![Erstellen der Geräte-ID bei der Authentifizierung mit selbstsignierten X.509-Zertifikaten im Portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopieren Sie sowohl die primären als auch die sekundären Gerätezertifikate und deren Schlüssel an einen beliebigen Speicherort auf dem nachgeschalteten Gerät. Verschieben Sie außerdem eine Kopie des freigegebenen Stammzertifizierungsstellenzertifikats, mit dem sowohl das Gatewaygerätezertikat als auch die Zertifikate für das nachgeschaltete Gerät generiert wurden.

   Sie werden auf diese Zertifikatdateien in allen Anwendungen auf dem nachgeschalteten Gerät verweisen, die eine Verbindung mit IoT Hub herstellen. Sie können einen Dienst wie [Azure Key Vault](../key-vault/index.yml) oder eine Funktion wie [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) zum Verschieben der Zertifikatsdateien verwenden.

5. Sehen Sie sich je nach Ihrer bevorzugten Sprache Beispiele dafür an, wie in IoT-Anwendungen auf X.509-Zertifikate verwiesen werden kann:

   * C#: [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Sie können auch die [IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension) verwenden, um den Vorgang zur Geräteerstellung abzuschließen. Im folgenden Beispiel wird mithilfe des Befehls [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) ein neues IoT-Gerät mit selbstsignierter X.509-Zertifizierung erstellt und ein übergeordnetes Gerät zugewiesen:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Als Nächstes müssen Sie [die Verbindungszeichenfolge abrufen und ändern](#retrieve-and-modify-connection-string), damit Ihr Gerät weiß, dass es eine Verbindung über sein Gateway herstellen soll.

### <a name="x509-ca-signed-authentication"></a>Authentifizierung mit einem X.509-Zertifikat, das von einer Zertifizierungsstelle signiert wurde

Für die Authentifizierung mit einem X.509-Zertifikat, das von einer Zertifizierungsstelle (ZS) signiert wurde, muss ein Zertifikat einer Stammzertifizierungsstelle in IoT Hub registriert sein, mit dem Sie die Zertifikate für Ihr nachgeschaltetes Gerät signieren. Jedes Gerät mit einem Zertifikat, das durch das Zertifikat der Stammzertifizierungsstelle oder einer seiner Zwischenzertifizierungsstellen ausgegeben wurde, wird zur Authentifizierung zugelassen.

Dieser Abschnitt baut auf den Anweisungen im IoT Hub-Artikel [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md) auf.

1. Erstellen Sie mithilfe Ihres Zertifizierungsstellenzertifikats zwei Gerätezertifikate (primär und sekundär) für das nachgeschaltete Gerät.

   Wenn Sie über keine Zertifizierungsstelle zum Erstellen von X.509-Zertifikaten verfügen, können Sie die IoT Edge-Demozertifikatskripts zum [Erstellen von Zertifikaten für nachgeschaltete Geräte](how-to-create-test-certificates.md#create-downstream-device-certificates) verwenden. Führen Sie die Schritte zum Erstellen von Zertifikaten aus, die von einer Zertifizierungsstelle signiert wurden. Verwenden Sie dasselbe Zertifikat der Stammzertifizierungsstelle, das die Zertifikate für Ihr Gatewaygerät generiert hat.

2. Befolgen Sie die Anweisungen im Abschnitt [Registrieren der X.509-Zertifizierungsstellenzertifikate bei IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) unter *Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub*. In diesem Abschnitt führen Sie die folgenden Schritte aus:

   1. Laden Sie ein Zertifikat der Stammzertifizierungsstelle hoch. Wenn Sie die Demozertifikate verwenden, lautet die Stammzertifizierungsstelle **\<path>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Stellen Sie sicher, dass Sie Besitzer des Zertifikats der Stammzertifizierungsstelle sind.

3. Befolgen Sie die Anweisungen im Abschnitt [Erstellen eines X.509-Geräts für Ihren IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) unter *Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub*. In diesem Abschnitt führen Sie die folgenden Schritte aus:

   1. Fügen Sie ein neues Gerät hinzu. Geben Sie in **Geräte-ID** einen Namen in Kleinbuchstaben an, und wählen Sie als Authentifizierungstyp **X.509, durch Zertifizierungsstelle signiert** aus.

   2. Legen Sie ein übergeordnetes Gerät fest. Wählen Sie **Übergeordnetes Gerät festlegen** und dann das IoT Edge-Gatewaygerät aus, das die Verbindung mit IoT Hub bereitstellt.

4. Erstellen Sie eine Vertrauenskette für das nachgeschaltete Gerät. Verwenden Sie dasselbe Zertifikat der Stammzertifizierungsstelle, das Sie zum Erstellen dieser Kette in IoT Hub hochgeladen haben. Verwenden Sie dieselbe Geräte-ID in Kleinbuchstaben, die Sie der Geräteidentität im Portal zugewiesen haben.

5. Kopieren Sie das Gerätezertifikat und die Schlüssel an einen beliebigen Speicherort auf dem nachgeschalteten Gerät. Verschieben Sie außerdem eine Kopie des freigegebenen Stammzertifizierungsstellenzertifikats, mit dem sowohl das Gatewaygerätezertikat als auch die Zertifikate für das nachgeschaltete Gerät generiert wurden.

   Sie werden auf diese Dateien in allen Anwendungen auf dem nachgeschalteten Gerät verweisen, die eine Verbindung mit IoT Hub herstellen. Sie können einen Dienst wie [Azure Key Vault](../key-vault/index.yml) oder eine Funktion wie [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) zum Verschieben der Zertifikatsdateien verwenden.

6. Sehen Sie sich je nach Ihrer bevorzugten Sprache Beispiele dafür an, wie in IoT-Anwendungen auf X.509-Zertifikate verwiesen werden kann:

   * C#: [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

Sie können auch die [IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension) verwenden, um den Vorgang zur Geräteerstellung abzuschließen. Im folgenden Beispiel wird mithilfe des Befehls [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) ein neues IoT-Gerät mit Authentifizierung mit einem X.509-Zertifikat, das von einer Zertifizierungsstelle signiert wurde, erstellt und ein übergeordnetes Gerät zugewiesen:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Als Nächstes müssen Sie [die Verbindungszeichenfolge abrufen und ändern](#retrieve-and-modify-connection-string), damit Ihr Gerät weiß, dass es eine Verbindung über sein Gateway herstellen soll.

## <a name="retrieve-and-modify-connection-string"></a>Abrufen und Ändern der Verbindungszeichenfolge

Nachdem Sie im Portal eine IoT-Geräteidentität erstellt haben, können Sie die zugehörigen primären oder sekundären Schlüssel abrufen. Einer dieser Schlüssel muss in der Verbindungszeichenfolge enthalten sein, über die Anwendungen mit IoT Hub kommunizieren. IoT Hub stellt der Einfachheit halber für die Authentifizierung mit symmetrischen Schlüsseln in den Gerätedetails eine vollständige Verbindungszeichenfolge bereit. Sie müssen der Verbindungszeichenfolge zusätzliche Informationen über das Gatewaygerät hinzufügen.

Verbindungszeichenfolgen für nachgeschaltete Geräte müssen folgende Komponenten enthalten:

* Den IoT-Hub, mit dem das Gerät eine Verbindung herstellt: `Hostname={iothub name}.azure-devices.net`
* Die beim Hub registrierte Geräte-ID: `DeviceID={device ID}`
* Die Authentifizierungsmethode (symmetrischer Schlüssel oder X.509-Zertifikate)
  * Wenn Sie die Authentifizierung mit symmetrischem Schlüssel verwenden, geben Sie den Primär- oder Sekundärschlüssel an: `SharedAccessKey={key}`.
  * Bei Verwendung der Authentifizierung mit X.509-Zertifikat geben Sie ein Flag an: `x509=true`.
* Das Gatewaygerät, über das das Gerät die Verbindung herstellt. Sie finden den Wert für **hostname** in der Datei „config.yaml“ des IoT Edge-Gatewaygeräts: `GatewayHostName={gateway hostname}`

Insgesamt sieht eine vollständige Verbindungszeichenfolge wie folgt aus:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Oder:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Dank der Beziehung zwischen über- und untergeordneten Geräten können Sie die Verbindungszeichenfolge vereinfachen, indem Sie das Gateway direkt als Verbindungshost aufrufen. Beispiel:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Sie werden diese geänderte Verbindungszeichenfolge im nächsten Artikel der Serie zum transparenten Gateway verwenden.

## <a name="next-steps"></a>Nächste Schritte

Sie verfügen jetzt über ein IoT Edge-Gerät, das bei Ihrem IoT Hub registriert und als transparentes Gateway konfiguriert wurde. Außerdem haben Sie ein nachgeschaltetes Gerät bei Ihrem IoT Hub registriert, das auf sein Gatewaygerät verweist.

Als Nächstes müssen Sie Ihre nachgeschalteten Geräte so konfigurieren, dass sie dem Gatewaygerät vertrauen und eine sichere Verbindung damit herstellen. Lesen Sie dann den nächsten Artikel in der Serie zum transparenten Gateway, [Verbinden eines nachgeschalteten Geräts mit einem Azure IoT Edge-Gateway](how-to-connect-downstream-device.md).