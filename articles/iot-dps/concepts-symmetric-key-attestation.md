---
title: Azure IoT Hub Device Provisioning Service – Nachweis des symmetrischen Schlüssels
description: Dieser Artikel bietet eine konzeptionelle Übersicht über den Nachweis des symmetrischen Schlüssels mit IoT Hub Device Provisioning Service.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 2f6e1e1a27e32e567cf0eaa8ff7a99046ed81bbe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746111"
---
# <a name="symmetric-key-attestation"></a>Nachweis des symmetrischen Schlüssels

In diesem Artikel wird der Prozess des Identitätsnachweises bei der Verwendung von symmetrischen Schlüsseln mit Device Provisioning Service beschrieben. 

Der Nachweis des symmetrischen Schlüssels ist eine einfache Methode zum Authentifizieren eines Geräts mit einer Device Provisioning Service-Instanz. Diese Nachweismethode stellt eine „Hallo Welt“-Umgebung für Entwickler bereit, die noch nicht mit der Gerätebereitstellung vertraut sind oder keine strengen Sicherheitsanforderungen haben. Die Gerätebestätigung bzw. der Nachweis mithilfe eines [TPM](concepts-tpm-attestation.md) (Trusted Platform Module) oder [x.509-Zertifikats](concepts-security.md#x509-certificates) ist sicherer und sollte verwendet werden, wenn striktere Sicherheitsanforderungen gelten.

Registrierungen von symmetrischen Schlüsseln eignen sich zudem hervorragend, um Legacygeräte mit eingeschränkten Sicherheitsfunktionen über Azure IoT in der Cloud zu starten. Weitere Informationen zum Nachweis des symmetrischen Schlüssels mit Legacygeräten finden Sie unter [How to use symmetric keys with legacy devices](how-to-legacy-device-symm-key.md) (Verwenden von symmetrischen Schlüsseln mit Legacygeräten).


## <a name="symmetric-key-creation"></a>Erstellung von symmetrischen Schlüsseln

Device Provisioning Service erstellt standardmäßig neue symmetrische Schlüssel mit einer Standardlänge von 32 Byte, wenn neue Registrierungen mit aktivierter Option **Schlüssel automatisch generieren** gespeichert werden.

![Automatisches Generieren von symmetrischen Schlüsseln](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Sie können auch Ihre eigenen symmetrischen Schlüssel für Registrierungen verwenden, indem Sie diese Option deaktivieren. Wenn Sie eigene symmetrische Schlüssel angeben, müssen diese eine Schlüssellänge von 16 bis 64 Byte aufweisen. Symmetrische Schlüssel müssen darüber hinaus in einem gültigen Base64-Format angegeben werden.



## <a name="detailed-attestation-process"></a>Detaillierter Nachweisprozess

Der Nachweis des symmetrischen Schlüssels mit Device Provisioning Service wird mit den gleichen [Sicherheitstoken](../iot-hub/iot-hub-devguide-security.md#security-token-structure) ausgeführt, die von IoT-Hubs zum Identifizieren von Geräten unterstützt werden. Diese Sicherheitstoken sind [SAS-Token (Shared Access Signature)](../service-bus-messaging/service-bus-sas.md). 

SAS-Token haben eine *Hashsignatur*, die mit dem symmetrischen Schlüssel erstellt wird. Die Signatur wird von Device Provisioning Service neu erstellt, um zu überprüfen, ob ein während der Bestätigung angegebenes Sicherheitstoken authentisch ist.

SAS-Token weisen folgendes Format auf:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Jedes Token enthält die folgenden Komponenten:

| Wert | BESCHREIBUNG |
| --- | --- |
| {signature} |Eine HMAC-SHA256-Signaturzeichenfolge. Für individuelle Registrierungen wird diese Signatur erstellt, indem der symmetrische Schlüssel (primär oder sekundär) zum Ausführen des Hashvorgangs verwendet wird. Für Registrierungsgruppen wird ein aus dem Registrierungsgruppenschlüssel abgeleiteter Schlüssel zum Ausführen des Hashvorgangs verwendet. Der Hashvorgang wird für eine Nachricht im folgenden Format ausgeführt: `URL-encoded-resourceURI + "\n" + expiry`. **Wichtig**: Der Schlüssel muss aus Base64 decodiert werden, bevor er zum Ausführen der HMAC-SHA256-Berechnung verwendet wird. Zudem muss das Signaturergebnis URL-codiert sein. |
| {resourceURI} |Der URI des Registrierungsendpunkts, auf den mit diesem Token zugegriffen werden kann. Der URI beginnt mit der Bereichs-ID für die Device Provisioning Service-Instanz. Zum Beispiel, `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC. |
| {URL-encoded-resourceURI} |URL-Codierung des Ressourcen-URI (beides in Kleinbuchstaben) |
| {policyName} |Der Name der gemeinsam genutzten Zugriffsrichtlinie, auf die dieses Token verweist. Der Richtlinienname, der bei der Bereitstellung mit Nachweis des symmetrischen Schlüssels verwendet wird, ist **registration**. |

Wenn ein Gerät die Bestätigung mit einer individuellen Registrierung vornimmt, verwendet es den im individuellen Registrierungseintrag definierten symmetrischen Schlüssel zum Erstellen der Hashsignatur für das SAS-Token.

Codebeispiele für die Erstellung eines SAS-Tokens finden Sie unter [Sicherheitstoken](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Die Erstellung von Sicherheitstoken für den Nachweis des symmetrischen Schlüssels wird vom Azure IoT C SDK unterstützt. Ein Beispiel zur Verwendung des Azure IoT C SDK für die Gerätebestätigung mit einer individuellen Registrierung finden Sie unter [Bereitstellen eines simulierten Geräts mit symmetrischen Schlüsseln](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Gruppenregistrierungen

Die symmetrischen Schlüssel für Gruppenregistrierungen werden bei der Bereitstellung nicht direkt von Geräten verwendet. Stattdessen verwenden Geräte, die zu einer Registrierungsgruppe gehören, einen abgeleiteten Geräteschlüssel zur Bereitstellung. 

Zunächst wird eine eindeutige Registrierungs-ID für jedes Gerät definiert, das die Bestätigung mit einer Registrierungsgruppe vornimmt. Gültige Zeichen für die Registrierungs-ID sind Kleinbuchstaben und Bindestriche (-). Bei dieser Registrierungs-ID sollte es sich um eine eindeutige Bezeichnung handeln, die das Gerät identifiziert. Beispielsweise kann ein Legacygerät nur beschränkte Sicherheitsfunktionen unterstützen. Das Legacygerät verfügt möglicherweise nur über eine MAC-Adresse oder Seriennummer zur eindeutigen Identifizierung des Geräts. In diesem Fall kann eine Registrierungs-ID wie folgt aus der MAC-Adresse und der Seriennummer zusammengesetzt werden:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Dieses Beispiel wird im Artikel [How to provision legacy devices using symmetric keys](how-to-legacy-device-symm-key.md) (Bereitstellen von Legacygeräten mit symmetrischen Schlüsseln) verwendet.

Nachdem eine Registrierungs-ID für das Gerät definiert wurde, wird der symmetrische Schlüssel für die Registrierungsgruppe zum Berechnen eines [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Hashes der Registrierungs-ID verwendet, um einen abgeleiteten Geräteschlüssel zu erstellen. Der Hashvorgang für die Registrierungs-ID kann mit dem folgenden C#-Code ausgeführt werden:

```C#
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Der resultierende Geräteschlüssel wird dann verwendet, um ein SAS-Token für die Bestätigung zu generieren. Jedes Gerät in einer Registrierungsgruppe muss die Bestätigung mit einem Sicherheitstoken ausführen, das aus einem eindeutigen abgeleiteten Schlüssel generiert wurde. Der symmetrische Schlüssel der Registrierungsgruppe kann nicht direkt für die Bestätigung verwendet werden.

#### <a name="installation-of-the-derived-device-key"></a>Installation des abgeleiteten Geräteschlüssels

Im Idealfall werden die Geräteschlüssel werkseitig abgeleitet und installiert. Dadurch wird sichergestellt, dass der Gruppenschlüssel nie in einer auf dem Gerät bereitgestellten Software enthalten ist. Wenn dem Gerät eine MAC-Adresse oder Seriennummer zugewiesen ist, kann der Schlüssel abgeleitet und wie vom Hersteller gewünscht auf dem Gerät gespeichert werden.

Das folgende Diagramm zeigt eine Tabelle mit Geräteschlüsseln, die werkseitig durch Hashing jeder Geräteregistrierungs-ID mit dem Gruppenregistrierungsschlüssel generiert wurden (**K**). 

![Werkseitig zugewiesene Geräteschlüssel](./media/concepts-symmetric-key-attestation/key-diversification.png)

Die Identität jedes Geräts wird durch die Registrierungs-ID und den werkseitig installierten abgeleiteten Geräteschlüssel dargestellt. Der Geräteschlüssel wird nie an einen anderen Speicherort kopiert, und der Gruppenschlüssel wird nie auf einem Gerät gespeichert.

Falls die Geräteschlüssel nicht werkseitig installiert wurden, sollte ein [Hardwaresicherheitsmodul (HSM)](concepts-security.md#hardware-security-module) verwendet werden, um die Identität des Geräts sicher zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Nachweis des symmetrischen Schlüssels vertraut gemacht haben, können Sie nun die folgenden Artikel lesen, um mehr zu erfahren:

* [Schnellstart: Bereitstellen eines simulierten Geräts mit symmetrischen Schlüsseln](quick-create-simulated-device-symm-key.md)
* [Konzepte für die automatische Bereitstellung](./concepts-auto-provisioning.md)
* [Erste Schritte mit der automatischen Bereitstellung](./quick-setup-auto-provision.md) 
