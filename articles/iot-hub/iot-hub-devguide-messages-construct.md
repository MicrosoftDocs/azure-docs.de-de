---
title: Grundlegendes zum Azure IoT Hub-Nachrichtenformat | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Beschreibung des Formats und des erwarteten Inhalts von IoT Hub-Nachrichten.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: e80fb136220330ddc53d513b22ebcfa19a35117b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252801"
---
# <a name="create-and-read-iot-hub-messages"></a>Erstellen und Lesen von IoT Hub-Nachrichten

Um eine nahtlose Interoperabilität zwischen Protokollen zu gewährleisten, definiert IoT Hub ein gemeinsames Nachrichtenformat für alle geräteseitigen Protokolle. Dieses Nachrichtenformat wird sowohl für [D2C-Routing](iot-hub-devguide-messages-d2c.md)- als auch für [C2D](iot-hub-devguide-messages-c2d.md)-Nachrichten verwendet. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementiert das D2C-Messaging anhand eines Streaming-Messagingmusters. Die D2C-Nachrichten von IoT Hub sind eher mit [Event Hubs](/azure/event-hubs/)-*Ereignissen* vergleichbar als mit [Service Bus](/azure/service-bus-messaging/)-*Nachrichten*, da der Dienst von einem größeren Volumen von Ereignissen durchlaufen wird, die von mehreren Lesern gelesen werden können.

Eine IoT Hub-Nachricht enthält Folgendes:

* Einen vordefinierten Satz von *Systemeigenschaften* wie unten aufgeführt.

* Einen Satz an *Anwendungseigenschaften*. Ein Wörterbuch mit Zeichenfolgeneigenschaften. Die Anwendung kann diese definieren und darauf zugreifen, ohne den Nachrichtentext deserialisieren zu müssen. IoT Hub ändert diese Eigenschaften nie.

* Ein nicht lesbarer binärer Textkörper.

Beim Senden von D2C-Nachrichten mit dem HTTPS-Protokoll sowie beim Senden von C2D-Nachrichten dürfen Eigenschaftennamen und Eigenschaftswerte nur alphanumerische ASCII-Zeichen sowie die Zeichen ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` enthalten.

D2C-Messaging mit IoT Hub weist folgende Merkmale auf:

* D2C-Nachrichten sind dauerhafter Art und werden bis zu sieben Tage lang auf dem Standardendpunkt der IoT Hub-Instanz (**messages/events**) aufbewahrt.

* D2C-Nachrichten dürfen maximal 256 KB groß sein. Sie können in Batches gruppiert werden, um den Sendevorgang zu optimieren. Die Batches können maximal 256 KB groß sein.

* IoT Hub erlaubt keine beliebige Partitionierung. D2C-Nachrichten werden gemäß ihrer ursprünglichen **deviceId**partitioniert.

* Wie in [Verwalten des Zugriffs auf IoT Hub](iot-hub-devguide-security.md) erläutert, ermöglicht IoT Hub Authentifizierung und Zugriffssteuerung auf Gerätebasis.

* Sie können Nachrichten mit Informationen stempeln, die in die Anwendungseigenschaften aufgenommen. Weitere Informationen finden Sie unter [Nachrichtenergänzungen](iot-hub-message-enrichments-overview.md).

Weitere Informationen zum Codieren und Decodieren von Nachrichten, die über verschiedene Protokollen gesendet werden, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).

In der folgenden Tabelle werden die Systemeigenschaften in IoT Hub-Nachrichten aufgeführt.

| Eigenschaft | BESCHREIBUNG | Kann der Benutzer festgelegt werden? |
| --- | --- | --- |
| message-id |Eine vom Benutzer festgelegte Kennung für die Nachricht; wird für Anforderung-Antwort-Muster verwendet. Format: Eine Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung (bis zu 128 Zeichen lang), die aus alphanumerischen ASCII-Zeichen (7 Bit) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`besteht. | Ja |
| sequence-number |Eine Nummer (für jede Gerätewarteschlange eindeutig), die jeder C2D-Nachricht von IoT Hub zugewiesen wird | „Nein“ bei C2D-Nachrichten; andernfalls „Ja“. |
| zu |Ein Ziel, das in [C2D](iot-hub-devguide-c2d-guidance.md) -Nachrichten angegeben wird. | „Nein“ bei C2D-Nachrichten; andernfalls „Ja“. |
| absolute-expiry-time |Datum und Uhrzeit des Nachrichtenablaufs. | Ja |
| iothub-enqueuedtime |Datum und Uhrzeit des Empfangs der [C2D](iot-hub-devguide-c2d-guidance.md)-Nachricht durch IoT Hub. | „Nein“ bei C2D-Nachrichten; andernfalls „Ja“. |
| correlation-id |Eine Zeichenfolgeneigenschaft in einer Antwortnachricht, die normalerweise die Nachrichten-ID der Anforderung im Anforderung-Antwort-Muster enthält. | Ja |
| user-id |Eine ID zum Festlegen des Ursprungs von Nachrichten. Wenn IoT Hub Nachrichten generiert, wird diese Eigenschaft auf `{iot hub name}`festgelegt. | Nein |
| iothub-ack |Ein Feedbacknachrichtengenerator. Diese Eigenschaft wird in C2D-Nachrichten verwendet, um IoT Hub anzuweisen, als Ergebnis der Nachrichtenverarbeitung durch das Gerät Feedbacknachrichten zu generieren. Mögliche Werte: **Kein** (Standardeinstellung): Es wird keine Feedbacknachricht generiert. **Positiv**: Es wird eine Feedbacknachricht empfangen, wenn die Nachricht abgeschlossen wurde. **Negativ**: Es wird eine Feedbacknachricht empfangen, wenn die Nachricht ohne vollständige Verarbeitung durch das Gerät abgelaufen ist (oder die maximale Anzahl von Zustellversuchen erreicht wurde). **Voll**: Feedback wird sowohl bei erfolgreicher als auch nicht erfolgreicher Nachrichtenverarbeitung generiert. <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Ja |
| iothub-connection-device-id |Eine ID, die von IoT Hub für D2C-Nachrichten festgelegt wird. Diese Eigenschaft enthält die **deviceId** des Geräts, das die Nachricht sendet. | „Nein“ bei D2C-Nachrichten; andernfalls „Ja“. |
| iothub-connection-auth-generation-id |Eine ID, die von IoT Hub für D2C-Nachrichten festgelegt wird. Diese Eigenschaft enthält die **generationId** (gemäß [Geräteidentitätseigenschaften](iot-hub-devguide-identity-registry.md#device-identity-properties)) des Geräts, das die Nachricht gesendet hat. | „Nein“ bei D2C-Nachrichten; andernfalls „Ja“. |
| iothub-connection-auth-method |Eine von IoT Hub für D2C-Nachrichten festgelegte Authentifizierungsmethode. Diese Eigenschaft enthält Informationen zu der Methode, die zum Authentifizieren des Geräts verwendet wird, das die Nachricht sendet. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | „Nein“ bei D2C-Nachrichten; andernfalls „Ja“. |
| iothub-creation-time-utc | Datum und Uhrzeit der Erstellung der Nachricht auf einem Gerät. Ein Gerät muss diesen Wert explizit festlegen. | Ja |

## <a name="message-size"></a>Nachrichtengröße

IoT Hub misst die Nachrichtengröße auf „protokollagnostische“ Weise, indem nur die tatsächliche Nutzlast berücksichtigt wird. Die Summe für die Größe in Byte wird wie folgt berechnet:

* Text in Byte
* Größe aller Werte der Nachrichtensystemeigenschaften in Byte
* Größe aller Benutzereigenschaftsnamen und -werte in Byte

Die Eigenschaftennamen und -werte sind auf ASCII-Zeichen beschränkt, sodass die Länge der Zeichenfolgen der Größe in Byte entspricht.

## <a name="anti-spoofing-properties"></a>Eigenschaften zum Schutz vor Spoofing

Um ein Gerätespoofing beim D2C-Messaging zu verhindern, versieht IoT Hub alle Nachrichten mit den folgenden Eigenschaften:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Die ersten beiden Eigenschaften enthalten die Werte für **deviceId** und **generationId** des ursprünglichen Geräts, wie unter [Geräteidentitätseigenschaften](iot-hub-devguide-identity-registry.md#device-identity-properties) beschrieben.

Die Eigenschaft **iothub-connection-auth-method** enthält ein serialisiertes JSON-Objekt mit folgenden Eigenschaften:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Größenbeschränkungen für Nachrichten in IoT Hub finden Sie unter [IoT Hub-Kontingente und -Drosselung](iot-hub-devguide-quotas-throttling.md).

* Informationen zum Erstellen und Lesen von IoT Hub-Nachrichten in verschiedenen Programmiersprachen finden Sie in den [Schnellstartanleitungen](quickstart-send-telemetry-node.md).
