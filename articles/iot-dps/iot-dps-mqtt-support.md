---
title: Grundlegendes zur MQTT-Unterstützung für den Azure IoT-Gerätebereitstellungsdienst | Microsoft-Dokumentation
description: Entwicklerhandbuch – Unterstützung für Geräte, die über das MQTT-Protokoll eine Verbindung mit dem geräteseitigen Endpunkt des Azure IoT-Gerätebereitstellungsdiensts (Device Provisioning Service, DPS) herstellen.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967124"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Kommunikation mit Ihrer DPS-Instanz über das MQTT-Protokoll

DPS ermöglicht Geräten die Kommunikation mit dem DPS-Geräteendpunkt über:

* [MQTT v3.1.1](https://mqtt.org/) an Port 8883
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) über WebSocket an Port 443

DPS ist kein voll funktionsfähiger MQTT-Broker und unterstützt nicht alle im MQTT v3.1.1-Standard angegebenen Verhaltensweisen. In diesem Artikel wird beschrieben, wie Geräte unterstützte MQTT-Verhaltensweisen für die Kommunikation mit DPS verwenden können.

Die gesamte Gerätekommunikation mit DPS muss mithilfe von TLS/SSL gesichert werden. Daher unterstützt DPS keine unsicheren Verbindungen über Port 1883.

 > [!NOTE] 
 > DPS unterstützt derzeit keine Geräte, die den TPM-[Nachweismechanismus](./concepts-service.md#attestation-mechanism) über das MQTT-Protokoll nutzen.

## <a name="connecting-to-dps"></a>Verbinden mit DPS

Ein Gerät kann das MQTT-Protokoll zum Herstellen einer Verbindung mit einem DPS über eine der folgenden Optionen verwenden.

* Bibliotheken in den [Azure IoT-Bereitstellungs-SDKs](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks)
* Das MQTT-Protokoll direkt

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Direktes Verwenden des Protokolls MQTT (als Gerät)

Wenn ein Gerät die SDKs von Geräten nicht verwenden kann, lässt es sich dennoch mithilfe des Protokolls MQTT über den Port 8883 mit den öffentlichen Geräteendpunkten verbinden. Das Gerät sollte im **CONNECT**-Paket die folgenden Werte verwenden:

* Verwenden Sie im Feld **ClientId** den Wert **registrationId**.

* Verwenden Sie im Feld **Benutzername** den Wert `{idScope}/registrations/{registration_id}/api-version=2019-03-31`. Dabei ist `{idScope}` der [idScope](./concepts-service.md#id-scope) des DPS.

* Verwenden Sie im Feld **Kennwort** ein SAS-Token. Das Format des SAS-Tokens ist das gleiche wie das für die Protokolle HTTPS und AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` Der „resourceURI“ sollte das Format `{idScope}/registrations/{registration_id}`haben. Der Richtlinienname sollte `registration` lauten.

  > [!NOTE]
  > Bei Verwendung der X.509-Zertifikatauthentifizierung sind keine SAS-Tokenkennwörter erforderlich.

  Weitere Informationen zum Generieren von SAS-Token finden Sie im Abschnitt „Sicherheitstoken“ von [Control access to DPS](how-to-control-access.md#security-tokens) (Steuern des Zugriffs auf DPS).

Hier ist eine Liste mit den spezifischen Verhaltensweisen bei der DPS-Implementierung:

 * DPS unterstützt nicht die Funktionalität des Flags **CleanSession**, das auf **0** festgelegt wird.

 * Wenn eine Geräte-App ein Thema mit **QoS 2** abonniert, gewährt DPS im Paket **SUBACK** maximal die QoS-Ebene 1. Danach übermittelt DPS Nachrichten mithilfe von QoS 1 an das Gerät.

## <a name="tlsssl-configuration"></a>TLS/SSL-Konfiguration

Damit Sie das MQTT-Protokoll direkt verwenden können, *muss* Ihr Client die Verbindung über TLS 1.2 herstellen. Wenn Sie versuchen, diesen Schritt zu überspringen, treten Verbindungsfehler auf.


## <a name="registering-a-device"></a>Registrieren eines Geräts

Zum Registrieren eines Geräts über DPS sollte es abonnieren und dazu `$dps/registrations/res/#` als **Themenfilter** verwenden. Der Platzhalter mit mehreren Ebenen `#` im Themenfilter wird nur verwendet, um dem Gerät das Empfangen zusätzlicher Eigenschaften im Themennamen zu erlauben. DPS lässt die Verwendung des Platzhalters `#` oder `?` zum Filtern von Unterthemen nicht zu. Da DPS kein allgemeiner Nachrichtenbrokerdienst für das Veröffentlichen und Abonnieren ist, werden nur die dokumentierten Themennamen und -filter unterstützt.

Das Gerät sollte eine Registrierungsmeldung an DPS veröffentlichen und dabei `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` als **Themennamen** verwenden. Die Nutzlast sollte das Objekt [Geräteregistrierung](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) im JSON-Format enthalten.
In einem erfolgreichen Szenario erhält das Gerät eine Antwort auf den Themennamen `$dps/registrations/res/202/?$rid={request_id}&retry-after=x`. Dabei ist „x“ der Retry-After-Wert in Sekunden. Die Nutzlast der Antwort enthält das Objekt [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) im JSON-Format.

## <a name="polling-for-registration-operation-status"></a>Abrufen des Registrierungsvorgangsstatus

Das Gerät muss den Dienst in regelmäßigen Abständen abrufen, um das Ergebnis des Geräteregistrierungsvorgangsstatus zu erhalten. Vorausgesetzt, dass das Gerät – wie oben angegeben – das Thema `$dps/registrations/res/#` bereits abonniert hat, kann es eine Nachricht vom Typ „GetOperationStatus“ im Themennamen `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` veröffentlichen. Die Vorgangs-ID in dieser Nachricht sollte der Wert sein, der im vorherigen Schritt in der Antwortnachricht „RegistrationOperationStatus“ empfangen wurde. Im Erfolgsfall antwortet der Dienst auf das Thema `$dps/registrations/res/200/?$rid={request_id}`. Die Nutzlast der Antwort enthält das Objekt „RegistrationOperationStatus“. Nach einer Verzögerung gleich dem Retry-After-Zeitraum sollte das Gerät den Dienst weiterhin abrufen, wenn der Antwortcode „202“ lautet. Der Geräteregistrierungsvorgang ist erfolgreich, wenn der Dienst den Statuscode „200 “zurückgibt.

## <a name="connecting-over-websocket"></a>Herstellen einer Verbindung über WebSocket
Geben Sie beim Herstellen einer Verbindung über WebSocket das Unterprotokoll als `mqtt` an. Befolgen Sie [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum MQTT-Protokoll finden Sie in der [MQTT-Dokumentation](https://mqtt.org/).

Weitere Informationen zu den Funktionen von DPS finden Sie unter:

* [Informationen zu IoT DPS](about-iot-dps.md)