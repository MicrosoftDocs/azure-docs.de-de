---
title: Problembehandlung – Azure IoT Hub-Fehler „401003 IoTHubUnauthorized“
description: Grundlegendes zum Beheben des Fehlers „401003 IoTHubUnauthorized“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357248"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

In diesem Artikel werden die Ursachen des Fehlers **401003 IoTHubUnauthorized** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

### <a name="symptom-1"></a>Symptom 1

In Protokollen wird ein Muster von Geräten angezeigt, bei denen gerade mit der Meldung **401003 IoTHubUnauthorized** die Verbindung getrennt wird, gefolgt von **404104 DeviceConnectionClosedRemotely** und kurz danach der erfolgreichen Herstellung einer Verbindung.

### <a name="symptom-2"></a>Symptom 2

Anforderungen an IoT Hub schlagen mit einer der folgenden Meldungen fehl:

* Autorisierungsheader fehlt
* IotHub „\*“ enthält das angegebene Gerät „\*“ nicht.
* Die Autorisierungsregel „\*“ lässt keinen Zugriff für „\*“ zu.
* Fehler bei der Authentifizierung für dieses Gerät, beim Erneuern von Token oder Zertifikat und beim erneuten Herstellen einer Verbindung
* Der Fingerabdruck entspricht nicht der Konfiguration: Fingerabdruck: SHA1Hash=\*, SHA2Hash=\*; Konfiguration: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Ursache

### <a name="cause-1"></a>Ursache 1

Bei MQTT verlassen sich einige SDKs darauf, dass IoT Hub beim Ablauf des Tokens eine Trennung der Verbindung auslöst, um zu wissen, wann es aktualisiert werden muss. Also:

1. Das SAS-Token läuft ab.
1. IoT Hub bemerkt den Ablauf und trennt die Geräteverbindung mit **401003 IoTHubUnauthorized**.
1. Das Gerät schließt die Trennung der Verbindung mit **404104 DeviceConnectionClosedRemotely** ab.
1. Das IoT SDK generiert ein neues SAS-Token.
1. Das Gerät stellt die Verbindung mit IoT Hub erfolgreich wieder her.

### <a name="cause-2"></a>Ursache 2

IoT Hub konnte den Autorisierungsheader, die Regel oder den Schlüssel nicht authentifizieren. Dies kann auf einen der in den Symptomen genannten Gründe zurückzuführen sein.

## <a name="solution"></a>Lösung

### <a name="solution-1"></a>Lösung 1

Wenn das IoT SDK – unter Angabe der Geräteverbindungszeichenfolge – für die Verbindung verwendet wird, ist keine Aktion erforderlich. Das IoT SDK generiert das neue Token erneut, um bei SAS-Tokenablauf die Verbindung wiederherzustellen.

Die standardmäßige Tokenlebensdauer beträgt 60 Minuten für SDKs. Bei einigen SDKs können die Tokenlebensdauer und der Schwellenwert für die Tokenerneuerung jedoch konfiguriert werden. Außerdem unterscheiden sich bei jedem SDK die Fehler, die generiert werden, wenn die Verbindung eines Geräts bei der Tokenerneuerung getrennt und wiederhergestellt wird. Weitere entsprechende Einzelheiten sowie Informationen dazu, wie Sie feststellen können, welches SDK das Gerät in Protokollen verwendet, finden Sie unter [Verbindungstrennungsverhalten von MQTT-Geräten bei Azure IoT-SDKs](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Wenn die Fehlermenge für Geräteentwickler ein Problem darstellt, wechseln Sie zum C SDK, über das das SAS-Token vor dem Ablauf erneuert wird. Bei AMQP kann das SAS-Token ohne eine Trennung der Verbindung aktualisiert werden.

### <a name="solution-2"></a>Lösung 2

Im Allgemeinen sollte in der angezeigten Fehlermeldung erläutert werden, wie der Fehler behoben werden kann. Wenn Sie aus irgendeinem Grund auf die Details der Fehlermeldung nicht zugreifen können, stellen Sie Folgendes sicher:

- Die SAS (Shared Access Signature) oder ein anderes verwendetes Sicherheitstoken ist nicht abgelaufen.
- Bei der X.509-Zertifikatsauthentifizierung ist das mit dem Gerät verbundene Geräte- oder Zertifizierungsstellenzertifikat nicht abgelaufen. Wie Sie X.509-Zertifizierungsstellenzertifikate bei IoT Hub registrieren können, erfahren Sie unter [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](iot-hub-security-x509-get-started.md).
- Bei der X.509-Authentifizierung per Fingerabdruck wird der Fingerabdruck des Gerätezertifikats bei IoT Hub registriert.
- Die Autorisierungsanmeldeinformationen sind für das verwendete Protokoll richtig formatiert. Weitere Informationen finden Sie unter [Steuern des Zugriffs auf IoT Hub](iot-hub-devguide-security.md).
- Die verwendete Autorisierungsregel verfügt über die Berechtigung für den angeforderten Vorgang.

## <a name="next-steps"></a>Nächste Schritte

- Zur Vereinfachung der Authentifizierung bei IoT Hub empfehlen wir, [Azure IoT SDKs](iot-hub-devguide-sdks.md) zu verwenden.
- Details zur Authentifizierung bei IoT Hub finden Sie unter [Verwalten des Zugriffs auf IoT Hub](iot-hub-devguide-security.md).
