---
title: Entwickeln ohne ein Azure IoT SDK | Microsoft-Dokumentation
description: Entwicklerhandbuch – Informationen und Links zu Themen, die Sie zum Erstellen von Geräte-Apps und Back-End-Apps ohne Verwendung eines Azure IoT SDKs nutzen können.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461712"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Entwickeln ohne Verwendung eines Azure IoT Hub SDKs

Dieses Thema enthält hilfreiche Informationen und Links für Entwickler, die Geräte- oder Back-End-Apps ohne Verwendung der Azure IoT SDKs entwickeln möchten.

Microsoft rät allerdings dringend zur Verwendung eines Azure IoT SDKs. Die Azure IoT-Geräte- und -Dienst-SDKs werden auf vielen gängigen Plattformen veröffentlicht. Die SDKs stellen eine komfortable Ebene bereit, die einen Großteil der Komplexität des zugrunde liegenden Kommunikationsprotokolls, darunter Geräteverbindung und erneute Verbindung, und die Wiederholungsrichtlinie handhabt. Die SDKs werden regelmäßig aktualisiert, damit sie die neuesten von IoT Hub zur Verfügung gestellten Features und Sicherheitsupdates bereitstellen. Mithilfe der SDKs können Sie die Entwicklungszeit und die Zeit für die Codewartung verkürzen. Weitere Informationen zu den Azure IoT SDKs finden Sie unter [Azure IoT-Geräte- und -Dienst-SDKs](iot-hub-devguide-sdks.md). Ausführlichere Informationen zu den Vorteilen der Verwendung eines Azure IoT SDKs finden Sie im Blogbeitrag [Benefits of using the Azure IoT SDKs and pitfalls to avoid if you don’t](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) (Vorteile der Verwendung der Azure IoT SDKs und vermeidbare Fehler, wenn Sie sie nicht verwenden).

Obwohl IoT Hub AMQP, AMQP über WebSockets, HTTPS, MQTT und MQTT über WebSockets für die Kommunikation mit Geräten unterstützt, empfehlen wir die Verwendung von MQTT, wenn Ihr Gerät dies unterstützt.

## <a name="development-prerequisites"></a>Entwicklungsvoraussetzungen

Bevor Sie mit der Entwicklung beginnen, sollten Sie umfassende Kenntnisse über IoT Hub und die Features haben, die Ihre Geräte- oder Back-End-App implementieren soll. Hier ist eine stark gekürzte Liste von Themen, mit denen Sie vertraut sein sollten:

* Sorgen Sie dafür, dass Sie die von IoT Hub zur Verfügung gestellten Endpunkte und die für jeden Endpunkt unterstützten Protokolle verstehen. Weitere Informationen finden Sie unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md).

* Wenn für Geräte-Apps ein Protokoll ausgewählt werden muss, empfehlen wir dringend die Verwendung von MQTT. Vor der Auswahl eines Protokolls müssen Sie aber unbedingt die damit jeweils verbundenen Einschränkungen verstehen. Weitere Informationen finden Sie unter [Auswählen eines Kommunikationsprotokolls](iot-hub-devguide-protocols.md).

* Grundlegendes zur Authentifizierung bei IoT Hub finden Sie unter [Verwalten des Zugriffs auf IoT Hub](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Hilfe zu verschiedenen Protokollen

Hilfe bei Verwendung der folgenden Protokolle ohne ein Azure IoT SDK:

* Geräte- oder Back-End-Apps zu **AMQP** finden Sie unter [AMQP-Unterstützung](iot-hub-amqp-support.md).

* Geräte-Apps zu **MQTT** finden Sie unter [MQTT-Unterstützung](iot-hub-mqtt-support.md). Ein Großteil dieses Themas befasst sich mit der direkten Verwendung des MQTT-Protokolls. Das Thema enthält aber auch Informationen zur Verwendung des [IoT MQTT-Beispielrepositorys](https://github.com/Azure-Samples/IoTMQTTSample). Dieses Repository enthält C-Beispiele, in denen die Bibliothek „Eclipse Mosquitto“ zum Senden von Nachrichten an IoT Hub verwendet wird.

* Geräte- oder Back-End-Apps für **HTTPS** finden Sie unter den [Azure IoT Hub REST-APIs](/rest/api/iothub/). Wie in [Entwicklungsvoraussetzungen](#development-prerequisites) erwähnt, denken Sie daran, dass Sie die Authentifizierung per X.509-Zertifizierungsstellenzertifikat (Certificate Authority, CA) nicht verwenden können.

Für Geräte empfehlen wir dringend die Verwendung von MQTT, wenn Ihr Gerät dies unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* [MQTT-Unterstützung](iot-hub-mqtt-support.md)