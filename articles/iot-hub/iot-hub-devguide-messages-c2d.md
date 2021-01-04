---
title: Grundlegendes zum Azure IoT Hub-C2D-Messaging | Microsoft-Dokumentation
description: In diesem Entwicklerhandbuch wird die Verwendung des C2D-Messagings mit Ihrem IoT-Hub beschrieben. Es enthält Informationen über den Lebenszyklus von Nachrichten und die Konfigurationsoptionen.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: ba58f7897827cf7ce7f6156df1434733d89d7f42
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844453"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Senden von C2D-Nachrichten von einem IoT-Hub

Für das Senden von unidirektionalen Benachrichtigungen von Ihrem Lösungs-Back-End an die Geräte-App senden Sie C2D-Nachrichten von Ihrem IoT-Hub an Ihr Gerät. Eine Erläuterung anderer C2D-Optionen, die von Azure IoT Hub unterstützt werden, finden Sie im [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Sie senden Cloud-zu-Gerät-Nachrichten (Cloud-to-Device, C2D) über einen dienstseitigen Endpunkt, */messages/devicebound*. Ein Gerät empfängt die Nachrichten dann über einen gerätespezifischen Endpunkt, */devices/{Geräte-ID}/messages/devicebound*.

Um jede C2D-Nachricht einem bestimmten Gerät zuzuordnen, legt Ihr IoT-Hub die **to**-Eigenschaft auf */devices/{Geräte-ID}/messages/devicebound* fest.

Jede Gerätewarteschlange kann maximal 50 C2D-Nachrichten enthalten. Der Versuch, eine größere Anzahl von Nachrichten an das gleiche Gerät zu senden, führt zu einem Fehler.

## <a name="the-cloud-to-device-message-life-cycle"></a>Der Lebenszyklus von C2D-Nachrichten

Um die Nachrichtenübermittlung mindestens einmal zu gewährleisten, werden C2D-Nachrichten in Ihrem IoT-Hub in Warteschlangen auf Gerätebasis beibehalten. Damit der IoT-Hub Geräte aus der Warteschlange entfernt, müssen die Geräte explizit den *Abschluss* bestätigen. Auf diese Weise wird Resilienz bei Verbindungs- und Gerätefehlern gewährleistet.

Die Lebenszyklus-Zustandsgrafik wird im folgenden Diagramm dargestellt:

![Lebenszyklus von C2D-Nachrichten](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Wenn der IoT-Hub-Dienst eine Nachricht an ein Gerät sendet, legt der Dienst den Nachrichtenstatus auf *In Warteschlange eingereiht* fest. Wenn ein Gerät eine Nachricht *empfangen* möchte, *sperrt* der IoT-Hub die Nachricht, indem er den Status auf *Unsichtbar* setzt. Dieser Zustand ermöglicht, dass andere Threads auf dem Gerät andere Nachrichten empfangen können. Wenn ein Gerätethread die Verarbeitung einer Nachricht abschließt, wird der IoT-Hub hierüber durch den *Abschluss* der Nachricht benachrichtigt. Der IoT-Hub legt dann den Status auf *Abgeschlossen* fest.

Ein Gerät kann auch Folgendes durchführen:

* *Ablehnen* der Nachricht: Der IoT-Hub versetzt sie in den Status *Unzustellbar*. Geräte, die über das MQTT-Protokoll (Message Queuing Telemetry Transport) eine Verbindung herstellen, können C2D-Nachrichten nicht ablehnen.

* *Verwerfen* der Nachricht: Der IoT-Hub platziert die Nachricht wieder in der Warteschlange mit dem Status *Zur Warteschlange hinzugefügt*. Geräte, die über das MQTT-Protokoll eine Verbindung herstellen, können C2D-Nachrichten nicht ablehnen.

Bei der Nachrichtenverarbeitung durch den Thread könnte ein Fehler auftreten, ohne dass de IoT-Hub hierüber benachrichtigt wird. In diesem Fall werden Nachrichten automatisch vom Status *Nicht sichtbar* zurück in den Status *Zur Warteschlange hinzugefügt* versetzt, wenn ein Timeout für die *Sichtbarkeit* (oder *Sperrung*) abgelaufen ist. Der Wert dieses Timeouts ist auf eine Minute festgelegt und kann nicht geändert werden.

Die Eigenschaft *Anzahl maximaler Zustellungen* im IoT-Hub bestimmt, wie oft eine Nachricht zwischen den Statuswerten **In Warteschlange eingereiht** und *Nicht sichtbar* wechseln kann. Nachdem diese Anzahl überschritten wurde, legt der IoT-Hub den Status der Nachricht auf *Unzustellbar* fest. Ebenso kennzeichnet der IoT-Hub eine Nachricht nach deren Ablaufzeitpunkt als *Unzustellbar*. Weitere Informationen finden Sie unter [Nachrichtenablauf (Gültigkeitsdauer)](#message-expiration-time-to-live).

Im Artikel [Senden von C2D-Nachrichten mit IoT Hub](iot-hub-csharp-csharp-c2d.md) erfahren Sie, wie Sie C2D-Nachrichten über IoT Hub aus der Cloud senden und auf einem Gerät empfangen.

Normalerweise werden C2D-Nachrichten immer dann vom Gerät abgeschlossen, wenn der Verlust der Nachricht keine Auswirkung auf die Anwendungslogik hat. Dies wäre z.B. der Fall, wenn das Gerät den Nachrichteninhalt lokal gespeichert hat oder ein Vorgang erfolgreich ausgeführt wurde. Die Nachricht könnte auch vorübergehende Informationen übermitteln, deren Verlust die Funktionalität der Anwendung nicht beeinträchtigen würde. Für Aufgaben mit langer Ausführungszeit können Sie:

* Die C2D-Nachricht nach Speichern der Aufgabenbeschreibung im lokalen Speicher des Geräts abschließen.

* Das Lösungs-Back-End mithilfe von D2C-Nachrichten in verschiedenen Phasen des Aufgabenverlaufs benachrichtigen.

## <a name="message-expiration-time-to-live"></a>Nachrichtenablauf (Gültigkeitsdauer)

Jede C2D-Nachricht verfügt über eine Gültigkeitsdauer. Diese Zeit wird auf eine der folgenden Arten festgelegt:

* Die **ExpiryTimeUtc**-Eigenschaft im Dienst
* Den IoT-Hub durch Verwendung der als IoT-Hub-Eigenschaft angegebenen standardmäßigen *Gültigkeitsdauer*

Siehe [Optionen für die C2D-Konfiguration](#cloud-to-device-configuration-options).

Eine gängige Methode, den Vorteil eines Nachrichtenablaufs zu nutzen und zu vermeiden, dass Nachrichten an getrennte Geräte gesendet werden, ist die Festlegung einer kurzen *Gültigkeitsdauer* für Werte. Bei diesem Ansatz wird das gleiche Ergebnis erzielt wie beim Aufrechterhalten des Geräteverbindungsstatus, er ist aber effizienter. Wenn Sie Nachrichtenbestätigungen anfordern, teilt der IoT-Hub Ihnen mit, für welche Geräte Folgendes zutrifft:

* In der Lage, Nachrichten zu empfangen.
* Nicht online oder fehlerhaft.

## <a name="message-feedback"></a>Nachrichtenfeedback

Beim Senden einer C2D-Nachricht kann der Dienst das Übermitteln von Feedback auf Nachrichtenbasis anfordern, um über den finalen Status dieser Nachricht informiert zu werden. Dies geschieht, indem Sie die Anwendungseigenschaft **iothub-ack** in der C2D-Nachricht festlegen, die an einen der folgenden vier Werte gesendet wird:

| Ack-Eigenschaftswert | Verhalten |
| ------------ | -------- |
| none     | Der IoT-Hub generiert keine Feedbacknachricht (Standardverhalten). |
| Positiv | Wenn die C2D-Nachricht den Status *Abgeschlossen* erreicht, generiert der IoT-Hub eine Feedbacknachricht. |
| Negativ | Wenn die C2D-Nachricht den Status *Unzustellbar* erreicht, generiert der IoT-Hub eine Feedbacknachricht. |
| Voll     | Der IoT-Hub generiert in beiden Fällen eine Feedbacknachricht. |

Wenn der **Ack**-Wert auf *Voll* festgelegt ist und Sie keine Feedbacknachricht erhalten, bedeutet dies, dass die Feedbacknachricht abgelaufen ist. Der Dienst kann nicht wissen, was mit der ursprünglichen Nachricht geschehen ist. In der Praxis sollte ein Dienst sicherstellen, dass Feedback verarbeitet werden kann, bevor es abläuft. Die maximale Ablaufzeit beträgt zwei Tage, sodass ausreichend Zeit verbleibt, um den Dienst wieder zu starten, wenn ein Fehler auftritt.

Wie im Abschnitt [Endpunkte](iot-hub-devguide-endpoints.md) erläutert, übermittelt der IoT-Hub Feedback in Form von Nachrichten über einen dienstseitigen Endpunkt ( */messages/servicebound/feedback*). Die Semantik für den Empfang von Feedback entspricht der Semantik für C2D-Nachrichten. Nachrichtenfeedback wird nach Möglichkeit in einer einzigen Nachricht zusammengefasst, die das folgende Format aufweist:

| Eigenschaft     | BESCHREIBUNG |
| ------------ | ----------- |
| EnqueuedTime | Ein Zeitstempel, der angibt, wann die Feedbacknachricht vom Hub empfangen wurde |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Der Nachrichtenkörper ist ein serialisiertes JSON-Array aus Datensätzen, von denen jeder die folgenden Eigenschaften aufweist:

| Eigenschaft           | BESCHREIBUNG |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Ein Zeitstempel, der angibt, wann das Ergebnis der Nachricht zustande gekommen ist (z.B. hat der Hub die Feedbacknachricht erhalten, oder die ursprüngliche Nachricht ist abgelaufen) |
| OriginalMessageId  | Die *MessageId* der C2D-Nachricht, auf die sich das Feedback bezieht |
| StatusCode         | Eine in Feedbacknachrichten verwendete erforderliche Zeichenfolge, die vom IoT-Hub generiert wird: <br/> *Erfolgreich* <br/> *Abgelaufen* <br/> *DeliveryCountExceeded* <br/> *Abgelehnt* <br/> *Gelöscht* |
| BESCHREIBUNG        | Zeichenfolgenwerte für *StatusCode* |
| deviceId           | Die *DeviceId* des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht |
| DeviceGenerationId | Die *DeviceGenerationId* des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht |

Damit die C2D-Nachricht das Feedback mit der ursprünglichen Nachricht korrelieren kann, muss der Dienst eine *MessageId* angeben.

Der folgende Code zeigt den Text einer Feedbacknachricht:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Ausstehendes Feedback für gelöschte Geräte**

Wird ein Gerät gelöscht, wird auch das gesamte ausstehende Feedback gelöscht. Gerätefeedback wird in Batches gesendet. Wird ein Gerät in dem kurzen Zeitraum (häufig weniger als eine Sekunde) zwischen der Bestätigung des Nachrichteneingangs durch das Gerät und der Vorbereitung des nächsten Feedbackbatches gelöscht, wird das Feedback nicht gesendet.

Sie können dies umgehen, indem Sie warten, bis das ausstehende Feedback eingegangen ist, bevor Sie das Gerät löschen. Nach dem Löschen eines Geräts sollte zugehöriges Nachrichtenfeedback als verloren betrachtet werden.

## <a name="cloud-to-device-configuration-options"></a>Optionen für die C2D-Konfiguration

Jede IoT Hub-Instanz legt die folgenden Konfigurationsoptionen für das C2D-Messaging offen:

| Eigenschaft                  | BESCHREIBUNG | Bereich und Standardwert |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Standardmäßige Gültigkeitsdauer für C2D-Nachrichten | ISO_8601-Intervall bis zu 2 Tage (mindestens 1 Minute); Standardwert: 1 Stunde |
| maxDeliveryCount          | Maximale Zustellungsanzahl für C2D-Gerätewarteschlangen pro Gerät | 1 bis 100; Standard: 10 |
| feedback.ttlAsIso8601     | Aufbewahrungsdauer für dienstgebundene Feedbacknachrichten | ISO_8601-Intervall bis zu 2 Tage (mindestens 1 Minute); Standardwert: 1 Stunde |
| feedback.maxDeliveryCount | Maximale Zustellungsanzahl für die Feedbackwarteschlange | 1 bis 100; Standard: 10 |
| feedback.lockDurationAsIso8601 | Maximale Zustellungsanzahl für die Feedbackwarteschlange | ISO_8601-Intervall von 5 bis 300 Sekunden (mindestens 5 Sekunden). Standard: 60 Sekunden. |

Sie können die Konfigurationsoptionen auf eine der folgenden Weisen festlegen:

* **Azure-Portal**: Wählen Sie auf Ihrem IoT-Hub unter **Einstellungen** die Option **Integrierten Endpunkte** aus, und erweitern Sie **Cloud-zu-Gerät-Nachrichten**. (Das Festlegen der Eigenschaften **feedback.maxDeliveryCount** und **feedback.lockDurationAsIso8601** im Azure-Portal wird derzeit nicht unterstützt.)

    ![Festlegen von Konfigurationsoptionen für Cloud-zu-Gerät-Nachrichten im Portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Verwenden Sie den Befehl [az iot hub update](/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update):

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den SDKs, die Sie für den Empfang von C2D-Nachrichten verwenden können, finden Sie unter [Azure IoT-SDKs](iot-hub-devguide-sdks.md).

Wenn Sie den Empfang von C2D-Nachrichten testen möchten, absolvieren Sie das Tutorial [Senden von C2D-Nachrichten](iot-hub-csharp-csharp-c2d.md).
