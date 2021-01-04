---
title: Sicherheitsendpunkte im IoT Device Provisioning-Dienst | Microsoft-Dokumentation
description: Konzepte für die Steuerung des Zugriffs auf IoT Device Provisioning Service (DPS) für Back-End-Apps. Enthält Informationen zu Sicherheitstoken.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954323"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Steuern des Zugriffs auf den Azure IoT Hub Device Provisioning-Dienst

In diesem Artikel werden die Optionen für den Schutz Ihres IoT Device Provisioning-Diensts beschrieben. Der Bereitstellungsdienst verwendet *Berechtigungen*, um Zugriff auf die einzelnen Endpunkte zu gewähren. Berechtigungen beschränken den Zugriff auf eine Dienstinstanz auf der Grundlage von Funktionen.

Dieser Artikel beschreibt Folgendes:

* Die verschiedenen Berechtigungen, die Sie einer Back-End-App für den Zugriff auf Ihren Bereitstellungsdienst gewähren können
* Der Authentifizierungsvorgangs und die verwendeten Token zum Überprüfen der Berechtigungen.

### <a name="when-to-use"></a>Verwendung

Sie benötigen entsprechende Berechtigungen für den Zugriff auf Bereitstellungsdienst-Endpunkte. Beispiel: Eine Back-End-App muss jede Nachricht, die sie an den Dienst sendet, mit einem Token mit Sicherheitsanmeldeinformationen versehen.

## <a name="access-control-and-permissions"></a>Access Control und Berechtigungen

Sie können [Berechtigungen](#device-provisioning-service-permissions) auf folgende Weise gewähren:

* **SAS-Autorisierungsrichtlinien:** SAS-Richtlinien können eine beliebige Kombination von [Berechtigungen](#device-provisioning-service-permissions) gewähren. Sie können Richtlinien im [Azure-Portal][lnk-management-portal] oder programmgesteuert mithilfe der [REST-APIs von Device Provisioning Service][lnk-resource-provider-apis] definieren. Ein neu erstellter Bereitstellungsdienst verfügt über folgende Standardrichtlinie:

* **provisioningserviceowner**: Richtlinie mit sämtlichen Berechtigungen.

> [!NOTE]
> Weitere Informationen finden Sie unter [Berechtigungen](#device-provisioning-service-permissions).

## <a name="authentication"></a>Authentication

Der Azure IoT Hub Device Provisioning-Dienst überprüft ein Token anhand der SAS-Richtlinien, um Zugriff auf Endpunkte zu gewähren. Sicherheitsanmeldeinformationen, beispielsweise symmetrische Schlüssel, werden niemals über eine physische Verbindung gesendet.

> [!NOTE]
> Der Device Provisioning Service-Ressourcenanbieter wird über Ihr Azure-Abonnement geschützt – ebenso wie alle Anbieter in [Azure Resource Manager][lnk-azure-resource-manager].

Weitere Informationen zum Erstellen und Verwenden von Sicherheitstoken finden Sie im nächsten Abschnitt.

HTTP ist das einzige unterstützte Protokoll. Zur Implementierung der Authentifizierung wird ein gültiges Token in den Anforderungsheader **Authorization** eingeschlossen.

#### <a name="example"></a>Beispiel
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Die [Azure IoT Device Provisioning Service SDKs][lnk-sdks] generieren automatisch Token, wenn eine Verbindung mit dem Dienst hergestellt wird.

## <a name="security-tokens"></a>Sicherheitstoken

Der Device Provisioning-Dienst verwendet Sicherheitstoken zum Authentifizieren von Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. Darüber hinaus sind Sicherheitstoken im Hinblick auf Gültigkeitszeitraum und Bereich beschränkt. [Azure IoT Device Provisioning Service SDKs][lnk-sdks] generieren Token automatisch (also ohne spezielle Konfiguration). In einigen Szenarien müssen Sie Sicherheitstoken allerdings direkt generieren und verwenden. Zu solchen Szenarien zählt auch die direkte Verwendung der HTTP-Oberfläche.

### <a name="security-token-structure"></a>Struktur von Sicherheitstoken

Sie verwenden Sicherheitstoken, um für Dienste zeitlich begrenzten Zugriff auf bestimmte Funktionen des IoT Device Provisioning-Diensts zu gewähren. Zur Autorisierung der Verbindungsherstellung mit dem Bereitstellungsdienst müssen Dienste Sicherheitstoken senden, die entweder mit einem freigegebenen Zugriffsschlüssel oder mit einem symmetrischen Schlüssel signiert sind.

Ein mit einem Schlüssel für den gemeinsamen Zugriff signiertes Token gewährt Zugriff auf alle Funktionen, die den SAS-Richtlinienberechtigungen zugeordnet sind. 

Das Sicherheitstoken weist das folgende Format auf:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier sind die erwarteten Werte:

| value | BESCHREIBUNG |
| --- | --- |
| {signature} |Eine HMAC-SHA256-Signaturzeichenfolge in folgendem Format: `{URL-encoded-resourceURI} + "\n" + expiry`. **Wichtig:** Der Schlüssel wird aus Base64 decodiert und als Schlüssel für die HMAC-SHA256-Berechnung verwendet.|
| {expiry} |UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC. |
| {URL-encoded-resourceURI} | URL-Codierung des Ressourcen-URI (beides in Kleinbuchstaben). URI-Präfix (nach Segment) der Endpunkte, auf die mit diesem Token zugegriffen werden kann – beginnend mit dem Hostnamen des IoT Device Provisioning-Diensts (kein Protokoll). Beispiel: `mydps.azure-devices-provisioning.net`. |
| {policyName} |Der Name der gemeinsam genutzten Zugriffsrichtlinie, auf die dieses Token verweist. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Hier ist zum Vergleich der entsprechende Python-Code zum Generieren eines Sicherheitstokens:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Da die zeitliche Gültigkeit des Tokens auf Computern mit dem IoT Device Provisioning-Dienst überprüft wird, darf die Uhr des Computers, auf dem das Token generiert wird, nur minimal abweichen.

### <a name="use-security-tokens-from-service-components"></a>Verwenden der Sicherheitstoken von Dienstkomponenten

Dienstkomponenten können nur Sicherheitstoken mithilfe von SAS-Richtlinien generieren, die die entsprechenden Berechtigungen erteilen, wie bereits erläutert.

Hier sind die Dienstfunktionen, die für die Endpunkte verfügbar gemacht werden:

| Endpunkt | Funktionalität |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Ermöglicht Geräteregistrierungsvorgänge mit dem Device Provisioning-Dienst |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Ermöglicht Vorgänge für die Verwaltung von Geräteregistrierungsgruppen |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Ermöglicht Vorgänge zum Abrufen und Verwalten des Status von Geräteregistrierungen |


Beispielsweise würde ein Dienst, der mit einer vorab erstellten SAS-Richtlinie namens **enrollmentread** generiert wurde, ein Token mit den folgenden Parametern erstellen:

* Ressourcen-URI: `{mydps}.azure-devices-provisioning.net`
* Signaturschlüssel: einer der Schlüssel der `enrollmentread` -Richtlinie
* Richtlinienname: `enrollmentread`
* Eine Ablaufzeit

![Erstellen einer SAS-Richtlinie für Ihre Instanz von Device Provisioning Service im Portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Das Ergebnis, das Lesezugriff auf alle Registrierungsdatensätze gewähren würde, sähe wie folgt aus:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referenzthemen:

Die folgenden Referenzthemen enthalten weitere Informationen zum Steuern des Zugriffs auf Ihren IoT Device Provisioning-Dienst.

### <a name="device-provisioning-service-permissions"></a>Berechtigungen des Device Provisioning-Diensts

Die folgende Tabelle enthält die Berechtigungen, die Sie zum Steuern des Zugriffs auf den IoT Device Provisioning-Dienst verwenden können:

| Berechtigung | Notizen |
| --- | --- |
| **ServiceConfig** |Gewährt Zugriff zum Ändern der Dienstkonfigurationen. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **EnrollmentRead** |Gewährt Lesezugriff auf die Geräteregistrierungen und Registrierungsgruppen. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **EnrollmentWrite** |Gewährt Schreibzugriff auf die Geräteregistrierungen und Registrierungsgruppen. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **RegistrationStatusRead** |Gewährt Lesezugriff auf den Status der Geräteregistrierung. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |
| **RegistrationStatusWrite**  |Gewährt Schreibzugriff auf den Status der Geräteregistrierung. <br/>Diese Berechtigung wird von Back-End-Clouddiensten verwendet. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/