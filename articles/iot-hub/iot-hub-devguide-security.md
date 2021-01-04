---
title: Grundlegendes zur Sicherheit von Azure IoT Hub | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Steuern des Zugriffs auf IoT Hub für Geräte-Apps und Back-End-Apps. Enthält Informationen zu Sicherheitstoken und zur Unterstützung von X.509-Zertifikaten.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
- devx-track-azurecli
ms.openlocfilehash: 8627681d843d15658882529424375486a4cdb1b9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845168"
---
# <a name="control-access-to-iot-hub"></a>Verwalten des Zugriffs auf IoT Hub

In diesem Artikel werden die Optionen zum Sichern Ihres IoT Hubs beschrieben. IoT Hub verwendet *Berechtigungen*, um Zugriff auf den Endpunkt jedes IoT Hubs zu gewähren. Berechtigungen beschränkten den Zugriff auf einen IoT Hub basierend auf der Funktionalität.

Dieser Artikel bietet eine Einführung zu Folgendem:

* Die verschiedenen Berechtigungen, die Sie einem Gerät oder einer Back-End-App für den Zugriff auf Ihre IoT Hubs gewähren können.
* Der Authentifizierungsvorgangs und die verwendeten Token zum Überprüfen der Berechtigungen.
* Festlegen des Geltungsbereichs für Anmeldeinformationen zum Begrenzen des Zugriffs auf bestimmte Ressourcen.
* IoT Hub-Unterstützung für X.509-Zertifikate.
* Benutzerdefinierte Authentifizierungsmechanismen, die auf vorhandene Geräteidentitätsregistrierungen Authentifizierungsschemas zurückgreifen.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Sie müssen über Berechtigungen für den Zugriff auf IoT Hub-Endpunkte verfügen. Beispielsweise muss ein Gerät ein Token mit den Anmeldeinformationen zusammen mit jeder an IoT Hub gesendeten Nachricht enthalten.

## <a name="access-control-and-permissions"></a>Access Control und Berechtigungen

Sie können [Berechtigungen](#iot-hub-permissions) auf folgende Weise gewähren:

* **Richtlinien für den gemeinsamen Zugriff auf IoT Hub-Ebene**. SAS-Richtlinien können eine beliebige Kombination von [Berechtigungen](#iot-hub-permissions) gewähren. Sie können Richtlinien im [Azure-Portal](https://portal.azure.com) programmgesteuert mithilfe von [IoT Hub-Ressourcenanbieter-REST-APIs](/rest/api/iothub/iothubresource) oder mit dem CLI-Befehl [az iot hub policy](/cli/azure/iot/hub/policy) definieren. Ein neu erstellter IoT Hub verfügt über die folgenden Standardrichtlinien:
  
  | SAS-Richtlinie | Berechtigungen |
  | -------------------- | ----------- |
  | iothubowner | Alle Berechtigungen |
  | Dienst | **ServiceConnect**-Berechtigung |
  | device | **DeviceConnect**-Berechtigung |
  | registryRead | **RegistryRead**-Berechtigung |
  | registryReadWrite | **RegistryReadWrite**- und **RegistryWrite**-Berechtigungen |

* **Sicherheitsanmeldeinformationen auf Gerätebasis**. Jeder IoT Hub enthält eine [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). Sie können für jedes Gerät in dieser Identitätsregistrierung Sicherheitsanmeldeinformationen konfigurieren, die **DeviceConnect**-Berechtigungen erteilen, deren Gültigkeitsbereich auf die entsprechenden Geräteendpunkte festgelegt ist.

Beispielsweise In einer normalen IoT-Lösung:

* Die Geräteverwaltungskomponente verwendet die Richtlinie *registryReadWrite* .
* Die Ereignisprozessorkomponente verwendet die Richtlinie *service* .
* Die Laufzeit-Geschäftslogikkomponente für Geräte verwendet die Richtlinie *service*.
* Einzelne Geräte stellen unter Verwendung von Anmeldeinformationen eine Verbindung her, die in der IoT Hub-Identitätsregistrierung gespeichert sind.

> [!NOTE]
> Weitere Informationen finden Sie unter [Berechtigungen](#iot-hub-permissions).

## <a name="authentication"></a>Authentifizierung

Azure IoT Hub gewährt Zugriff auf Endpunkte, indem ein Token zur Verifizierung mit gemeinsam genutzten Zugriffsrichtlinien und Sicherheitsanmeldeinformationen für die Identitätsregistrierung verglichen wird.

Sicherheitsanmeldeinformationen, beispielsweise symmetrische Schlüssel, werden niemals über eine physische Verbindung gesendet.

> [!NOTE]
> Der Azure IoT Hub-Ressourcenanbieter wird über Ihr Azure-Abonnement geschützt – ebenso wie alle Anbieter im [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Weitere Informationen zur Erstellung und Verwendung von Sicherheitstoken finden Sie im Artikel zu den [IoT Hub-Sicherheitstoken](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokolldetails

Jedes unterstützte Protokoll, z.B. MQTT, AMQP und HTTPS, transportiert Tokens auf unterschiedliche Weise.

Bei Verwendung von MQTT weist das CONNECT-Paket die Geräte-ID als Client-ID, `{iothubhostname}/{deviceId}` im Feld „Benutzername“ und ein SAS-Token im Feld „Kennwort“ auf. `{iothubhostname}` sollte der vollständige CNAME des IoT Hubs (z.B. „contoso.azure-devices.net“) sein.

Bei Verwendung von [AMQP](https://www.amqp.org/) unterstützt IoT Hub [SASL PLAIN](https://tools.ietf.org/html/rfc4616) und die [auf AMQP-Ansprüchen basierte Sicherheit](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Wenn Sie eine auf AMQP-Ansprüchen beruhende Sicherheit verwenden, gibt der Standard vor, wie die oben aufgeführten Token übertragen werden.

Für SASL PLAIN kann der **Benutzername** Folgendes sein:

* `{policyName}@sas.root.{iothubName}`, wenn Token auf IoT Hub-Ebene verwendet werden.
* `{deviceId}@sas.{iothubname}`, wenn Token für Geräte verwendet werden.

In beiden Fällen enthält das Kennwortfeld das Token gemäß der Beschreibung unter [IoT Hub-Sicherheitstoken](iot-hub-devguide-security.md#security-tokens).

HTTPS implementiert die Authentifizierung, indem ein gültiges Token in den Anforderungsheader **Authorization** eingeschlossen wird.

#### <a name="example"></a>Beispiel

Benutzername (bei der Geräte-ID wird Groß-/Kleinschreibung berücksichtigt): `iothubname.azure-devices.net/DeviceId`

Kennwort (Sie können ein SAS-Token mit dem CLI-Erweiterungsbefehl [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) oder den [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) generieren):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Die [Azure IoT SDKs](iot-hub-devguide-sdks.md) generieren automatisch Token, wenn eine Verbindung mit dem Dienst hergestellt wird. In einigen Fällen unterstützen die Azure IoT SDKs nicht alle Protokolle oder Authentifizierungsmethoden.

### <a name="special-considerations-for-sasl-plain"></a>Besonderheiten für SASL PLAIN

Bei Verwendung von SASL PLAIN mit AMQP kann ein Client, der eine Verbindung mit einem IoT Hub herstellt, ein einziges Token für jede TCP-Verbindung verwenden. Wenn das Token abläuft, wird die TCP-Verbindung mit dem Dienst getrennt, und es wird ein Versuch zur erneuten Verbindungsherstellung ausgelöst. Dieses Verhalten ist für eine Back-End-App unproblematisch, für eine Geräte-App jedoch schädlich. Dies hat folgende Gründe:

* Gateways stellen eine Verbindung üblicherweise für eine Vielzahl von Geräten her. Bei Verwendung von SASL PLAIN muss für jedes Gerät, das eine Verbindung mit einem IoT Hub herstellen möchte, eine eigene TCP-Verbindung erstellt werden. Bei diesem Szenario erhöht sich der Verbrauch von Rechen- und Netzwerkressourcen erheblich, und es kommt zu einer höheren Latenz für jede Geräteverbindung.

* Der erhöhte Ressourcenverbrauch bei der erneuten Verbindungsherstellung nach jedem Tokenablauf wirkt sich negativ auf Geräte mit Ressourceneinschränkungen aus.

## <a name="scope-iot-hub-level-credentials"></a>Gültigkeitsbereich für Anmeldeinformationen auf IoT Hub-Ebene

Sie können den Bereich für Sicherheitsrichtlinien auf IoT Hub-Ebene festlegen, indem Sie Token mit eingeschränktem Ressourcen-URI erstellen. Beispielsweise ist der Endpunkt zum Senden von Gerät-zu-Cloud-Nachrichten von einem Gerät **/devices/{deviceId}/messages/events**. Sie können eine SAS-Richtlinie auf IoT Hub-Ebene mit **DeviceConnect**-Berechtigungen auch verwenden, um ein Token zu signieren, dessen resourceURI-Element **/devices/{deviceId}** ist. Bei diesem Ansatz wird ein Token erstellt, das nur zum Senden von Nachrichten im Namen der des Geräts **deviceId** verwendet werden kann.

Dieser Mechanismus ist mit einer [Event Hubs-Herausgeberrichtlinie](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) vergleichbar und ermöglicht die Implementierung von benutzerdefinierten Authentifizierungsmethoden.

## <a name="security-tokens"></a>Sicherheitstoken

IoT Hub verwendet Sicherheitstoken zum Authentifizieren von Geräten und Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. Darüber hinaus sind Sicherheitstoken im Hinblick auf Gültigkeitszeitraum und Bereich beschränkt. [Azure IoT SDKs](iot-hub-devguide-sdks.md) generieren Token automatisch, ohne dass eine spezielle Konfiguration erforderlich ist. In einigen Szenarien müssen Sie Sicherheitstoken allerdings direkt generieren und verwenden. Zu diesen Szenarien zählen:

* Die direkte Verwendung von MQTT-, AMQP- oder HTTPS-Oberflächen

* Die Implementierung des Tokendienstmusters, wie unter [Benutzerdefinierte Geräteauthentifizierung](iot-hub-devguide-security.md#custom-device-and-module-authentication) beschrieben.

IoT Hub ermöglicht Geräten darüber hinaus die Authentifizierung bei IoT Hub mithilfe von [X.509-Zertifikaten](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktur von Sicherheitstoken

Sie verwenden Sicherheitstoken, um zeitlich begrenzten Zugriff auf Geräte und Dienste für bestimmte Funktionen in IoT Hub zu gewähren. Zur Autorisierung der Verbindungsherstellung mit IoT Hub müssen Geräte und Dienste Sicherheitstoken senden, die entweder mit einem freigegebenen Zugriffsschlüssel oder mit einem symmetrischen Schlüssel signiert sind. Diese Schlüssel werden mit einer Geräteidentität in der Identitätsregistrierung gespeichert.

Ein mit einem Schlüssel für den gemeinsamen Zugriff signiertes Token gewährt Zugriff auf alle Funktionen, die den SAS-Richtlinienberechtigungen zugeordnet sind. Ein Token, das mit einem symmetrischen Schlüssel der Geräteidentität signiert ist, erteilt nur die Berechtigung **DeviceConnect** für die zugeordnete Geräteidentität.

Das Sicherheitstoken weist das folgende Format auf:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier sind die erwarteten Werte:

| Wert | BESCHREIBUNG |
| --- | --- |
| {signature} |Eine HMAC-SHA256-Signaturzeichenfolge in folgendem Format: `{URL-encoded-resourceURI} + "\n" + expiry`. **Wichtig**: Der Schlüssel wird aus Base64 decodiert und als Schlüssel für die HMAC-SHA256-Berechnung verwendet. |
| {resourceURI} |Das URI-Präfix (nach Segment) der Endpunkte, auf die mit diesem Token zugegriffen werden kann – beginnend mit dem Hostnamen des IoT Hubs (kein Protokoll). Zum Beispiel, `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC. |
| {URL-encoded-resourceURI} |URL-Codierung des Ressourcen-URI (beides in Kleinbuchstaben) |
| {policyName} |Der Name der gemeinsam genutzten Zugriffsrichtlinie, auf die dieses Token verweist. Nicht vorhanden, wenn das Token auf Geräteregistrierungs-Anmeldeinformationen verweist. |

**Hinweis zum Präfix**: Das URI-Präfix wird nach Segment, nicht nach Zeichen berechnet. Beispielsweise ist `/a/b` ein Präfix für `/a/b/c`, aber nicht für `/a/bc`.

Der folgende „Node.js“-Codeausschnitt zeigt eine Funktion namens **GenerateSasToken**, die das Token aus den Eingaben `resourceUri, signingKey, policyName, expiresInMins` berechnet. In den nächsten Abschnitten wird erläutert, wie die verschiedenen Eingaben für die verschiedenen Anwendungsfälle für Token initialisiert werden.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Hier ist zum Vergleich der entsprechende Python-Code zum Generieren eines Sicherheitstokens:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Die Funktion in C# zum Generieren eines Sicherheitstokens ist wie folgt:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```

Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Da der Gültigkeitszeitraum des Tokens auf dem IoT Hub-Computer überprüft wird, muss die Abweichung der Uhr des Computers, auf dem das Token generiert wird, minimal sein.

### <a name="use-sas-tokens-in-a-device-app"></a>Verwenden von SAS-Token in einer Geräte-App

Es gibt zwei Methoden zum Abrufen von **DeviceConnect**-Berechtigungen mit IoT Hub über Sicherheitstoken: mit einem [symmetrischen Identitätsschlüssel aus der Identitätsregistrierung](#use-a-symmetric-key-in-the-identity-registry) oder mit einem [Schlüssel für den gemeinsamen Zugriff](#use-a-shared-access-policy).

Denken Sie daran, dass alle Funktionen, auf die auf Geräten zugegriffen werden kann, für Endpunkte mit dem Präfix `/devices/{deviceId}` absichtlich verfügbar gemacht werden.

> [!IMPORTANT]
> Die einzige Möglichkeit, dass IoT Hub ein bestimmtes Gerät authentifiziert, ist durch die Verwendung des symmetrischen Schlüssels der Geräteidentität. Wenn eine SAS-Richtlinie verwendet wird, um auf Gerätefunktionen zuzugreifen, muss die Lösung die Komponente, die das Sicherheitstoken ausgibt, als vertrauenswürdige Unterkomponente ansehen.

Die geräteseitigen Endpunkte sind (unabhängig vom Protokoll):

| Endpunkt | Funktionalität |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Senden von Gerät-an-Cloud-Nachrichten. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Empfangen von Cloud-an-Gerät-Nachrichten. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Verwenden eines symmetrischen Schlüssels in der Identitätsregistrierung

Wenn Sie den symmetrischen Schlüssel einer Geräteidentität zum Generieren eines Tokens verwenden, wird das policyName-Element (`skn`) des Tokens nicht angegeben.

Beispielsweise muss ein Token, das für den Zugriff auf alle Gerätefunktionen erstellt wird, die folgenden Parameter aufweisen:

* Ressourcen-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* Signaturschlüssel: ein symmetrischer Schlüssel für die `{device id}` -Identität
* Kein Richtlinienname
* Eine Ablaufzeit

Ein Beispiel mit der oben genannten Node.js-Funktion:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Das Ergebnis, das Zugriff auf alle Funktionen für „device1“ gewährt wird, wäre:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Sie können ein SAS-Token mit dem CLI-Erweiterungsbefehl [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) oder den [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) generieren.

### <a name="use-a-shared-access-policy"></a>Verwenden einer SAS-Richtlinie

Wenn Sie ein Token auf der Grundlage einer SAS-Richtlinie erstellen, legen Sie das Feld `skn` auf den Namen der Richtlinie fest. Diese Richtlinie muss die Berechtigung **DeviceConnect** erteilen.

Die beiden wichtigsten Szenarien für die Verwendung von SAS-Richtlinien für den Zugriff auf Gerätefunktionen sind:

* [Protokollgateways für Clouds](iot-hub-devguide-endpoints.md),
* [Tokendienste](iot-hub-devguide-security.md#custom-device-and-module-authentication), die verwendet werden, um benutzerdefinierte Authentifizierungsschemas zu implementieren.

Da die SAS-Richtlinie potenziell Zugriff gewähren kann, um als ein beliebiges Gerät eine Verbindung herzustellen, ist es wichtig, beim Erstellen von Sicherheitstoken den richtigen Ressourcen-URI zu verwenden. Diese Einstellung ist besonders wichtig für Tokendienste, die das Token mit dem Ressourcen-URI auf ein bestimmtes Gerät festlegen müssen. Dieser Punkt ist weniger relevant für Protokollgateways, da sie Datenverkehr bereits für alle Geräte vermitteln.

Beispielsweise würde ein Tokendienst, der die vorab erstellte SAS-Richtlinie mit dem Namen **device** verwendet, ein Token mit den folgenden Parametern erstellen:

* Ressourcen-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* Signaturschlüssel: einer der Schlüssel der `device` -Richtlinie
* Richtlinienname: `device`
* Eine Ablaufzeit

Ein Beispiel mit der oben genannten Node.js-Funktion:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Das Ergebnis, das Zugriff auf alle Funktionen für „device1“ gewährt wird, wäre:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Ein Protokollgateway könnte das gleiche Token für alle Geräte verwenden, indem einfach der Ressourcen-URI auf `myhub.azure-devices.net/devices`festgelegt wird.

### <a name="use-security-tokens-from-service-components"></a>Verwenden der Sicherheitstoken von Dienstkomponenten

Dienstkomponenten können nur Sicherheitstoken mithilfe von SAS-Richtlinien generieren, die die entsprechenden Berechtigungen erteilen, wie bereits erläutert.

Hier sind die Dienstfunktionen, die für die Endpunkte verfügbar gemacht werden:

| Endpunkt | Funktionalität |
| --- | --- |
| `{iot hub host name}/devices` |Erstellen, Aktualisieren, Abrufen und Löschen von Geräteidentitäten. |
| `{iot hub host name}/messages/events` |Empfangen von Gerät-an-Cloud-Nachrichten. |
| `{iot hub host name}/servicebound/feedback` |Empfangen von Feedback für Cloud-an-Gerät-Nachrichten. |
| `{iot hub host name}/devicebound` |Senden von Cloud-an-Gerät-Nachrichten. |

Beispielsweise würde ein Dienst, der zum Generieren die vorab erstellte SAS-Richtlinie mit dem Namen **registryRead** verwendet, ein Token mit den folgenden Parametern erstellen:

* Ressourcen-URI: `{IoT hub name}.azure-devices.net/devices`
* Signaturschlüssel: einer der Schlüssel der `registryRead` -Richtlinie
* Richtlinienname: `registryRead`
* Eine Ablaufzeit

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Das Ergebnis, das Lesezugriff für alle Geräteidentitäten gewähren würde, wäre:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Unterstützte X.509-Zertifikate

Sie können ein X.509-Zertifikat verwenden, um ein Gerät bei IoT Hub zu authentifizieren. Laden Sie hierzu entweder einen Zertifikatfingerabdruck oder eine Zertifizierungsstelle (Certificate Authority, CA) in Azure IoT Hub hoch. Bei der Authentifizierung mittels Zertifikatfingerabdruck wird überprüft, ob der verwendete Fingerabdruck dem konfigurierten Fingerabdruck entspricht. Bei der Authentifizierung per Zertifizierungsstelle wird die Vertrauenskette überprüft. In beiden Fällen erfordert der TLS-Handshake, dass das Gerät über ein gültiges Zertifikat und einen privaten Schlüssel verfügt. Weitere Informationen finden Sie in der TLS-Spezifikation, z. B.: [RFC 5246 – Die Transport Layer Security (TLS)-Protokollversion 1.2](https://tools.ietf.org/html/rfc5246/).

Unterstützte Zertifikate:

* **Ein vorhandenes X.509-Zertifikat**. Einem Gerät ist möglicherweise bereits ein X.509-Zertifikat zugeordnet. Das Gerät kann dieses Zertifikat für die Authentifizierung bei IoT Hub verwenden. Diese Option ist sowohl für die Authentifizierung per Fingerabdruck als auch für die Authentifizierung per Zertifizierungsstelle geeignet. 

* **Ein von einer Zertifizierungsstelle signiertes X.509-Zertifikat**. Sie können auch ein von einer Zertifizierungsstelle generiertes und signiertes X.509-Zertifikat verwenden, um ein Gerät zu identifizieren und bei IoT Hub zu authentifizieren. Diese Option ist sowohl für die Authentifizierung per Fingerabdruck als auch für die Authentifizierung per Zertifizierungsstelle geeignet.

* **Ein selbst generiertes und selbstsigniertes X.509-Zertifikat**. Ein Gerätehersteller oder interner Bereitsteller kann diese Zertifikate generieren und den entsprechenden privaten Schlüssel (und das Zertifikat) auf dem Gerät speichern. Sie können dafür Tools wie etwa [OpenSSL](https://www.openssl.org/) und das Windows-Hilfsprogramm [SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) verwenden. Diese Option kann nur für die Authentifizierung per Fingerabdruck verwendet werden.

Ein Gerät verwendet entweder ein X.509-Zertifikat oder ein Sicherheitstoken für die Authentifizierung, aber nicht beides. Stellen Sie bei der X.509-Zertifikatsauthentifizierung sicher, dass Sie über eine Strategie zur Handhabung des Rollovers von Zertifikaten verfügen, wenn ein vorhandenes Zertifikat abläuft.

Die folgenden Funktionen werden bei Geräten, die die Authentifizierung per X.509-Zertifizierungsstelle verwenden, nicht unterstützt:

* HTTPS, MQTT über WebSockets und AMQP über WebSockets-Protokolle.
* Dateiuploads (alle Protokolle).

Weitere Informationen zur Authentifizierung per Zertifizierungsstelle finden Sie unter [Geräteauthentifizierung mit X.509-Zertifikaten](iot-hub-x509ca-overview.md). Informationen zum Hochladen und Überprüfen einer Zertifizierungsstelle bei Ihrem IoT Hub finden Sie unter [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrieren eines X.509-Zertifikats für ein Gerät

Das [Azure IoT-Dienst-SDK für C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (mindestens Version 1.0.8) unterstützt die Registrierung von Geräten, die ein X.509-Zertifikat für die Authentifizierung verwenden. Andere APIs wie beispielsweise für den Import/Export von Geräten unterstützen ebenfalls X.509-Zertifikate.

Sie können auch den CLI-Erweiterungsbefehl [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) verwenden, um X.509-Zertifikate für Geräte zu konfigurieren.

### <a name="c-support"></a>C\#-Unterstützung

Die **RegistryManager** -Klasse stellt eine programmgesteuerte Methode zum Registrieren eines Geräts bereit. Insbesondere die Methoden **AddDeviceAsync** und **UpdateDeviceAsync** ermöglichen Ihnen die Registrierung und Aktualisierung eines Geräts in der IoT Hub-Identitätsregistrierung. Diese beiden Methoden nutzen eine **Device** -Instanz als Eingabe. Die **Device**-Klasse enthält eine **Authentication**-Eigenschaft, die Ihnen die Angabe primärer und sekundärer X.509-Zertifikatfingerabdrücke ermöglicht. Der Fingerabdruck stellt einen SHA256-Hash des X.509-Zertifikats dar (gespeichert mithilfe binärer DER-Codierung). Sie haben die Möglichkeit, einen primären Fingerabdruck oder einen sekundären Fingerabdruck oder beides anzugeben. Primäre und sekundäre Fingerabdrücke werden unterstützt, um Szenarien mit Zertifikat-Rollover zu behandeln.

Hier sehen Sie einen C\#-Beispielcodeausschnitt für die Registrierung eines Geräts mithilfe eines X.509-Zertifikatfingerabdrucks:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Verwenden eines X.509-Zertifikats bei Laufzeitvorgängen

Das [Azure IoT-Geräte-SDK für .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (mindestens Version 1.0.11) unterstützt die Verwendung von X.509-Zertifikaten.

### <a name="c-support"></a>C\#-Unterstützung

Die Klasse **DeviceAuthenticationWithX509Certificate** unterstützt die Erstellung von **DeviceClient**-Instanzen mithilfe eines X.509-Zertifikats. Das X.509-Zertifikat muss im PFX-Format (auch als „PKCS #12“ bezeichnet) vorliegen, das den privaten Schlüssel enthält.

Hier sehen Sie ein Beispiel für einen Codeausschnitt:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Benutzerdefinierte Geräte- und Modulauthentifizierung

Mit der [Identitätsregistrierung](iot-hub-devguide-identity-registry.md) von IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung pro Gerät/Modul mithilfe von [Token](iot-hub-devguide-security.md#security-tokens) konfigurieren. Wenn eine IoT-Lösung bereits über eine benutzerdefinierte Identitätsregistrierung und/oder über ein Authentifizierungsschema verfügt, können Sie diese Infrastruktur durch Erstellen eines *Tokendiensts* in IoT Hub integrieren. Auf diese Weise können Sie andere IoT-Features in der Lösung nutzen.

Ein Tokendienst ist ein benutzerdefinierter Clouddienst. Er verwendet eine *SAS-Richtlinie* von IoT Hub mit **DeviceConnect**- oder **ModuleConnect**-Berechtigungen, um Token mit *Gerätebereich* bzw. *Modulbereich* zu erstellen. Mit diesen Token kann ein Gerät oder ein Modul eine Verbindung mit Ihrer IoT Hub-Instanz herstellen.

![Schritte des Tokendienstmusters](./media/iot-hub-devguide-security/tokenservice.png)

Hier sind die wichtigsten Schritte des Tokendienstmusters:

1. Erstellen Sie eine IoT Hub-SAS-Richtlinie mit **DeviceConnect**- oder **ModuleConnect**-Berechtigungen für Ihre IoT Hub-Instanz. Sie können diese Richtlinie im [Azure-Portal](https://portal.azure.com) oder programmgesteuert erstellen. Diese Richtlinie wird vom Tokendienst zum Signieren der von ihm erstellten Token verwendet.

2. Wenn ein Gerät/Modul auf IoT Hub zugreifen muss, fordert es von Ihrem Tokendienst ein signiertes Token an. Das Gerät kann mit Ihrer benutzerdefinierten Identitätsregistrierung bzw. mit dem Authentifizierungsschema authentifiziert werden, um die Geräte- oder Modulidentität zu ermitteln, die der Tokendienst zum Erstellen des Tokens verwendet.

3. Der Tokendienst gibt ein Token zurück. Das Token wird mit `/devices/{deviceId}` oder `/devices/{deviceId}/module/{moduleId}` als `resourceURI` erstellt, wobei `deviceId` das authentifizierte Gerät bzw. `moduleId` das authentifizierte Modul ist. Der Tokendienst verwendet die SAS-Richtlinie, um das Token zu erstellen.

4. Das Gerät/Modul nutzt das Token direkt mit IoT Hub.

> [!NOTE]
> Sie können die .NET-Klasse [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) oder die Java-Klasse [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) zum Erstellen eines Tokens im Tokendienst verwenden.

Der Tokendienst kann die Gültigkeitsdauer für das Token wie gewünscht festlegen. Wenn das Token abläuft, trennt IoT Hub die Geräte-/Modulverbindung. Das Gerät/Modul muss dann ein neues Token vom Tokendienst anfordern. Eine kurze Ablaufzeit erhöht die Last für das Gerät/Modul und den Tokendienst gleichermaßen.

Damit ein Gerät/Modul eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie es der IoT Hub-Identitätsregistrierung hinzufügen – auch wenn es für die Verbindung ein Token und keinen Schlüssel verwendet. Aus diesem Grund können Sie weiterhin die geräte-/modulspezifische Zugriffssteuerung nutzen, indem Sie Geräte-/Modulidentitäten in der [Identitätsregistrierung](iot-hub-devguide-identity-registry.md) aktivieren oder deaktivieren. Dieser Ansatz verringert die Risiken der Verwendung von Token mit langen Ablaufzeiten.

### <a name="comparison-with-a-custom-gateway"></a>Vergleich mit einem benutzerdefinierten Gateway

Das Tokendienstmuster ist der empfohlene Weg zur Implementierung einer benutzerdefinierten Identitätsregistrierung bzw. eines Authentifizierungsschemas mit IoT Hub. Dieses Muster wird empfohlen, da der größte Teil des Lösungsdatenverkehrs weiterhin über IoT Hub abgewickelt wird. Wenn das benutzerdefinierte Authentifizierungsschema allerdings so eng mit dem Protokoll verknüpft ist, muss der gesamte Datenverkehr unter Umständen durch ein *benutzerdefiniertes Gateway* verarbeitet werden. Ein Beispiel eines solchen Szenarios ist die Verwendung von [Transport Layer Security (TLS) und vorinstallierten Schlüsseln (PSKs)](https://tools.ietf.org/html/rfc4279). Weitere Informationen finden Sie im Artikel zum [Protokollgateway](iot-hub-protocol-gateway.md).

## <a name="reference-topics"></a>Referenzthemen:

Die folgenden Referenzthemen enthalten weitere Informationen zum Steuern des Zugriffs auf Ihren IoT Hub.

## <a name="iot-hub-permissions"></a>IoT Hub-Berechtigungen

In der folgenden Tabelle werden die Berechtigungen aufgeführt, die Sie zum Steuern des Zugriffs auf den IoT Hub verwenden können.

| Berechtigung | Notizen |
| --- | --- |
| **RegistryRead** |Diese Berechtigung gewährt Lesezugriff auf die Identitätsregistrierung. Weitere Informationen finden Sie unter [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). <br/>Diese Berechtigung wird von Back-End Cloud Services verwendet. |
| **RegistryReadWrite** |Diese Berechtigung gewährt Lese- und Schreibzugriff auf die Identitätsregistrierung. Weitere Informationen finden Sie unter [Identitätsregistrierung](iot-hub-devguide-identity-registry.md). <br/>Diese Berechtigung wird von Back-End Cloud Services verwendet. |
| **ServiceConnect** |Diese Berechtigung gewährt Zugriff auf die clouddienstseitigen Endpunkte für Kommunikation und Überwachung. <br/>Clouddiensten wird die Berechtigung erteilt, D2C-Nachrichten zu empfangen, C2D-Nachrichten zu senden und die zugehörigen Übermittlungsbestätigungen zu empfangen. <br/>Erteilt die Berechtigung zum Abrufen der Übermittlungsbestätigungen für Dateiuploads. <br/>Gewährt die Berechtigung zum Zugriff auf Zwillinge zum Aktualisieren von Tags und gewünschten Eigenschaften, zum Abrufen gemeldeter Eigenschaften und zum Ausführen von Abfragen. <br/>Diese Berechtigung wird von Back-End Cloud Services verwendet. |
| **DeviceConnect** |Diese Berechtigung gewährt Zugriff auf die geräteseitigen Endpunkte. <br/>Gewährt die Berechtigung zum Senden von D2C-Nachrichten und zum Empfangen von C2D-Nachrichten. <br/>Gewährt die Berechtigung zum Ausführen eines Dateiupload von einem Gerät. <br/>Gewährt die Berechtigung zum Abrufen von gewünschten Eigenschaftsbenachrichtigungen für Gerätezwillinge und zum Aktualisieren von gemeldeten Eigenschaften. <br/>Gewährt die Berechtigung zum Ausführen eines Dateiupload. <br/>Diese Berechtigung wird von Geräten verwendet. |

## <a name="additional-reference-material"></a>Weiteres Referenzmaterial

Weitere Referenzthemen im IoT Hub-Entwicklerhandbuch:

* Unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT Hub für Laufzeit- und Verwaltungsvorgänge verfügbar macht.

* Unter [Drosselung und Kontingente](iot-hub-devguide-quotas-throttling.md) werden die Kontingente und das Drosselungsverhalten des IoT Hub-Diensts beschrieben.

* Unter [Verstehen und Verwenden von Azure IoT Hub SDKs](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.

* Unter [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md) wird die Abfragesprache beschrieben, mit der Sie Informationen zu Gerätezwillingen und Aufträgen aus IoT Hub abrufen können.

* [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md) enthält weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll.

* [RFC 5246 – Die Transport Layer Security (TLS)-Protokollversion 1.2](https://tools.ietf.org/html/rfc5246/) enthält weitere Informationen zur TLS-Authentifizierung.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie gelernt haben, wie Sie den Zugriff auf IoT Hub steuern, sind möglicherweise die folgenden Themen im IoT Hub-Entwicklerhandbuch für Sie interessant:

* [Verwenden von Gerätezwillingen zum Synchronisieren von Status und Konfigurationen](iot-hub-devguide-device-twins.md)
* [Aufrufen einer direkten Methode auf einem Gerät](iot-hub-devguide-direct-methods.md)
* [Planen von Aufträgen auf mehreren Geräten](iot-hub-devguide-jobs.md)

Wenn Sie einige der in diesem Artikel beschriebenen Konzepte ausprobieren möchten, sehen Sie sich die folgenden IoT Hub-Tutorials an:

* [Erste Schritte mit Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Verarbeiten von Gerät-zu-Cloud-Nachrichten mit IoT Hub](tutorial-routing.md)
