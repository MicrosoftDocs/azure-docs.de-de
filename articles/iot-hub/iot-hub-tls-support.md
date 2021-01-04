---
title: Azure IoT Hub TLS-Unterstützung
description: Informationen zur Verwendung von sicheren TLS-Verbindungen für Geräte und Dienste, die mit IoT Hub kommunizieren
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jlian
ms.openlocfilehash: f4438aebcb81d665a19a595ac7ade4fea27fc43f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621007"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Transport Layer Security (TLS)-Unterstützung in IoT Hub

IoT Hub verwendet Transport Layer Security (TLS) zum Sichern von Verbindungen mit IoT-Geräten und -Diensten. Derzeit werden drei Versionen des TLS-Protokolls unterstützt, nämlich die Versionen 1.0, 1.1 und 1.2.

TLS 1.0 und 1.1 werden als Legacy betrachtet und in Kürze eingestellt. Weitere Informationen finden Sie unter [Deprecating TLS 1.0 and 1.1 for IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md) (Einstellung von TLS 1.0 and 1.1 für IoT Hub). Verwenden Sie zur Vermeidung zukünftiger Probleme TLS 1.2 als einzige TLS-Version, wenn Sie eine Verbindung mit IoT Hub herstellen.

## <a name="iot-hubs-server-tls-certificate"></a>TLS-Zertifikat des IoT Hub-Servers

Während eines TLS-Handshakes präsentiert IoT Hub RSA-schlüsselgebundene Serverzertifikate zum Verbinden von Clients. Sein Stamm ist die Baltimore Cybertrust-Stammzertifizierungsstelle (Stamm-ZS). Vor kurzem haben wir eine Änderung am TLS-Serverzertifikat eingeführt, sodass es jetzt von neuen Zwischenzertifizierungsstellen (Intermediate Certificate Authorities, ICA) ausgestellt wird. Weitere Informationen finden Sie unter [IoT Hub TLS certificate update](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/) (Aktualisierung des TLS-Zertifikats für IoT Hub).

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>TLS-Zertifikat von ECC-Server (Elliptic Curve Cryptography) (Vorschau)

Das TLS-Zertifikat von ECC-Server für IoT Hub steht für die öffentliche Vorschau zur Verfügung. Während die ECC-Zertifikatüberprüfung (mit Nur-ECC-Verschlüsselungssammlungen) eine ähnliche Sicherheit für RSA-Zertifikate bietet, nutzt sie bis zu 40 % weniger Compute, Arbeitsspeicher und Bandbreite. Diese Einsparungen sind wichtig für IoT-Geräte aufgrund ihrer kleineren Profile und des kleineren Arbeitsspeichers und zur Unterstützung von Anwendungsfällen in Umgebungen mit eingeschränkter Netzwerkbandbreite. 

So können Sie die Vorschau des ECC-Serverzertifikats für IoT Hub anzeigen:

1. [Erstellen Sie einen neuen IoT-Hub mit aktiviertem Vorschaumodus](iot-hub-preview-mode.md).
1. [Konfigurieren Sie Ihren Client](#tls-configuration-for-sdk-and-iot-edge) so, dass er *nur* ECDSA-Verschlüsselungssammlungen enthält und RSA-Verschlüsselungssammlungen *ausschließt*. Dies sind die unterstützten Verschlüsselungssammlungen für die öffentliche Vorschau des ECC-Zertifikats:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Verbinden Sie Ihren Client mit dem Vorschau-IoT Hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Erzwingen von TLS 1.2 in ausgewählten Regionen verfügbar

Wenn Sie die Sicherheit erhöhen möchten, konfigurieren Sie Ihre IoT Hubs so, dass *nur* Clientverbindungen zugelassen werden, die TLS-Version 1.2 verwenden, und dass die Verwendung von [Verschlüsselungssammlungen](#cipher-suites) erzwungen wird. Diese Funktion wird nur in diesen Regionen unterstützt:

* East US
* USA Süd Mitte
* USA, Westen 2
* US Gov Arizona
* US Government, Virginia

Stellen Sie zu diesem Zweck einen neuen IoT Hub in einer der unterstützten Regionen bereit, und legen Sie die `minTlsVersion`-Eigenschaft in der IoT Hub-Ressourcenspezifikation Ihrer Azure Resource Manager-Vorlage auf `1.2` fest:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Die erstellte IoT Hub-Ressource, die diese Konfiguration verwendet, wird Geräte- und Dienstclients ablehnen, die versuchen, eine Verbindung mit den TLS-Versionen 1.0 und 1.1 herzustellen. Analog dazu wird der TLS-Handshake abgelehnt, wenn die `ClientHello`-Nachricht keine der [empfohlenen Verschlüsselungen](#cipher-suites) enthält.

> [!NOTE]
> Die Eigenschaft `minTlsVersion` ist schreibgeschützt und kann nach Erstellung Ihrer IoT Hub-Ressource nicht mehr geändert werden. Daher ist es von entscheidender Bedeutung, dass Sie im Voraus ordnungsgemäß testen und überprüfen, ob *alle* Ihre IoT-Geräte und -Dienste mit TLS 1.2 und den [empfohlenen Verschlüsselungen](#cipher-suites) kompatibel sind.
> 
> Nach einem Failover bleibt die Eigenschaft `minTlsVersion` Ihres IoT Hubs in der geografisch gekoppelten Region wirksam.

## <a name="cipher-suites"></a>Verschlüsselungssammlungen

IoT Hubs, die so konfiguriert wurden, dass nur TLS 1.2 akzeptiert wird, erzwingen auch die Verwendung der folgenden empfohlenen Verschlüsselungssammlungen:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Bei IoT Hubs, die nicht zum Erzwingen von TLS 1.2 konfiguriert wurden, funktioniert TLS 1.2 weiterhin bei den folgenden Verschlüsselungssammlungen:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

Ein Client kann eine Liste von höheren Verschlüsselungssammlungen vorschlagen, die während `ClientHello` verwendet werden sollen. Einige davon werden jedoch möglicherweise nicht von IoT Hub unterstützt (z. B. `ECDHE-ECDSA-AES256-GCM-SHA384`). In diesem Fall versucht IoT Hub, die bevorzugte Einstellung des Clients einzuhalten, aber die Verschlüsselungssammlung letztendlich mit `ServerHello` auszuhandeln.

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>TLS-Konfiguration für SDK und IoT Edge

Verwenden Sie die nachstehenden Links zum Konfigurieren von TLS 1.2 und zulässigen Verschlüsselungen in IoT Hub-Client-SDKs.

| Sprache | Versionen mit Unterstützung von TLS 1.2 | Dokumentation |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 oder höher            | [Link](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 oder höher             | [Link](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 oder höher            | [Link](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 oder höher            | [Link](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 oder höher            | [Link](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge-Geräte können für die Verwendung von TLS 1.2 bei der Kommunikation mit IoT Hub konfiguriert werden. Verwenden Sie dazu die [IoT Edge Dokumentationsseite](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Geräte-Authentifizierung

Nach einem erfolgreichen TLS-Handshake kann IoT Hub ein Gerät mithilfe eines symmetrischen Schlüssels oder eines X.509-Zertifikats authentifizieren. Bei der zertifikatbasierten Authentifizierung kann dies ein beliebiges X.509-Zertifikat sein, einschließlich ECC. IoT Hub überprüft das Zertifikat mit dem Fingerabdruck oder der Zertifizierungsstelle (Certificate Authority, CA), den bzw. die Sie bereitstellen. Weitere Informationen finden Sie unter [Unterstützte X.509-Zertifikate](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Aushandlung der maximalen TLS-Fragmentlänge (Vorschau)

IoT Hub unterstützt auch die Aushandlung der maximalen TLS-Fragmentlänge, die manchmal als „Aushandlung der TLS-Framegröße“ bezeichnet wird. Dieses Feature befindet sich in der Phase der öffentlichen Vorschau. 

Mithilfe dieses Features können Sie für die maximale Fragmentlänge des Klartexts einen Wert angeben, der kleiner als der Standardwert von „2^14 Bytes“ ist. Nach dem Aushandeln beginnen IoT Hub und der Client mit der Fragmentierung von Nachrichten, um sicherzustellen, dass alle Fragmente kleiner als die ausgehandelte Länge sind. Dieses Verhalten ist hilfreich für die Berechnung oder den Speicher eingeschränkter Geräte. Weitere Informationen finden Sie in der [offiziellen TLS-Erweiterungsspezifikation](https://tools.ietf.org/html/rfc6066#section-4).

Die offizielle SDK-Unterstützung für dieses Feature der öffentlichen Vorschau steht noch nicht zur Verfügung. Einführung

1. [Erstellen Sie einen neuen IoT-Hub mit aktiviertem Vorschaumodus](iot-hub-preview-mode.md).
1. Wenn Sie OpenSSL verwenden, rufen Sie [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) auf, um die Fragmentgröße anzugeben.
1. Verbinden Sie Ihren Client mit dem Vorschau-IoT Hub.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur IoT Hub-Sicherheit und -Zugriffssteuerung finden Sie unter [Steuern des Zugriffs auf IoT Hub](iot-hub-devguide-security.md).
- Weitere Informationen zum Verwenden des X.509-Zertifikats für die Geräteauthentifizierung finden Sie unter [Geräteauthentifizierung mit X.509-ZS-Zertifikaten](iot-hub-x509ca-overview.md).