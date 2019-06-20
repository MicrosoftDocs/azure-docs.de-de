---
title: Informationen zu direkten Azure IoT Hub-Methoden | Microsoft Docs
description: Entwicklerhandbuch – Verwenden von direkten Methoden zum Aufrufen von Code auf Ihren Geräten von einer Service-App
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: d7c63ffe5a318507053f59bf3a18242ee8c327a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61327753"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Verstehen und Aufrufen direkter Methoden von IoT Hub

IoT Hub gibt Ihnen die Möglichkeit, direkte Methoden auf Geräten von der Cloud aus aufzurufen. Direkte Methoden stellen eine Anforderung-Antwort-Interaktion mit einem Gerät dar, die einem HTTP-Aufruf darin ähnelt, dass sie unverzüglich (nach einem vom Benutzer angegebenen Timeout) zu einem Erfolg oder Fehler führt. Dieser Ansatz eignet sich für Szenarien, in denen die Vorgehensweise bei sofortigen Aktionen unterschiedlich ist, je nachdem, ob das Gerät reagieren konnte oder nicht.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Jedes Gerätemethode hat ein einzelnes Gerät als Ziel. [Aufträge auf mehreren Geräten planen](iot-hub-devguide-jobs.md) bietet eine Möglichkeit zum Aufrufen von direkten Methoden auf mehreren Geräten und zum Planen von Methodenaufrufen für nicht verbundene Geräte.

Jeder Benutzer mit der Berichtigung **Dienstverbindung** für IoT Hub kann eine Methode auf einem Gerät aufrufen.

Direkte Methoden entsprechen einem Anforderung/Antwort-Schema und sind für Kommunikation bestimmt, die sofortige Bestätigung ihres Ergebnisses erfordert. Ein Beispiel hierfür ist die interaktive Steuerung des Geräts, wie das Einschalten eines Lüfters.

Falls Sie weitere Informationen dazu benötigen, was die Verwendung von gewünschten Eigenschaften, direkten Methoden oder C2D-Nachrichten betrifft, hilft Ihnen der [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md) weiter.

## <a name="method-lifecycle"></a>Methodenlebenszyklus

Direkte Methoden werden auf dem Gerät implementiert und können für eine ordnungsgemäße Instanziierung null oder mehr Eingaben in der Methodennutzlast erfordern. Sie rufen eine direkte Methode über einen dienstseitigen URI (`{iot hub}/twins/{device id}/methods/`) auf. Ein Gerät empfängt direkte Methoden über ein gerätespezifisches MQTT-Thema (`$iothub/methods/POST/{method name}/`) oder über AMQP-Links (die Anwendungseigenschaften `IoThub-methodname` und `IoThub-status`). 

> [!NOTE]
> Wenn Sie eine direkte Methode auf einem Gerät aufrufen, können Eigenschaftennamen und -werte nur druckbare alphanumerische US-ASCII-Zeichen mit Ausnahme der folgenden enthalten: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Direkte Methoden sind synchron und werden nach dem Zeitlimit (Standardwert: 30 Sekunden, einstellbar bis 300 Sekunden) erfolgreich oder mit einem Fehler abgeschlossen. Direkte Methoden sind hilfreich bei interaktiven Szenarios, in denen ein Gerät genau dann agieren soll, wenn es online ist und Befehle empfängt. Beispiel: Einschalten von Beleuchtung über ein Telefon. In diesen Szenarios soll der Erfolg oder Misserfolg unmittelbar erkennbar sein, damit der Clouddienst so schnell wie möglich auf das Ergebnis reagieren kann. Das Gerät kann einen Nachrichtentext als Ergebnis der Methode zurückgeben, dies ist für die Methode aber nicht erforderlich. Es gibt keine Garantie für die Sortierung oder eine Parallelitätssemantik für Methodenaufrufe.

Direkte Methoden erfolgen von der Cloudseite nur über HTTPS und von der Geräteseite über MQTT oder AMQP.

Die Nutzlast für Methodenanforderungen und -antworten ist ein JSON-Dokument mit bis zu 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Aufrufen einer direkten Methode aus einer Back-End-App

Rufen Sie jetzt eine direkte Methode aus einer Back-End-App auf.

### <a name="method-invocation"></a>Methodenaufruf

Direkte Methodenaufrufe auf einem Gerät sind HTTPS-Aufrufe, die aus den folgenden Elementen bestehen:

* Der *Anforderungs-URI* für das Gerät neben der [API-Version](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* Die POST-*Methode*

* *Headern*, die Autorisierung, Anforderungs-ID, Inhaltstyp und Inhaltscodierung enthalten

* Einen transparenten JSON-*Haupttext* im folgenden Format:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Timeout in Sekunden. Wenn kein Timeout festgelegt ist, lautet der Standardwert 30 Sekunden.

#### <a name="example"></a>Beispiel

Nachfolgend finden Sie ein Barebonebeispiel unter Verwendung von `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>response

Die Back-End-App empfängt eine Antwort, die aus den folgenden Elementen besteht:

* *HTTP-Statuscode*, der für Fehler von IoT Hub verwendet wird. Dazu gehören z.B. 404-Fehler für Geräte, die derzeit nicht verbunden sind.

* *Headern*, die ETag, Anforderungs-ID, Inhaltstyp und Inhaltscodierung enthalten.

* Einen JSON-*Haupttext* im folgenden Format:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` und `body` werden vom Gerät bereitgestellt und für die Antwort mit dem Statuscode und/oder der Beschreibung des Geräts verwendet.

### <a name="method-invocation-for-iot-edge-modules"></a>Methodenaufruf für IoT Edge-Module

Der Aufruf von direkten Methoden mithilfe einer Modul-ID wird im [IoT Service Client C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) unterstützt.

Zu diesem Zweck verwenden Sie die `ServiceClient.InvokeDeviceMethodAsync()`-Methode und übergeben `deviceId` und `moduleId` als Parameter.

## <a name="handle-a-direct-method-on-a-device"></a>Behandeln einer direkten Methode auf einem Gerät

Sehen wir uns an, wie eine direkte Methode auf einem IoT-Gerät behandelt wird.

### <a name="mqtt"></a>MQTT

Der nachstehende Abschnitt erläutert das MQTT-Protokoll.

#### <a name="method-invocation"></a>Methodenaufruf

Geräte empfangen direkte Methodenanforderungen zum MQTT-Thema: `$iothub/methods/POST/{method name}/?$rid={request id}`. Die Anzahl der Abonnements pro Gerät ist auf 5 begrenzt. Deshalb wird empfohlen, nicht jede direkte Methode einzeln zu abonnieren. Erwägen Sie stattdessen das Abonnieren von `$iothub/methods/POST/#`. Filtern Sie dann die übermittelten Nachrichten, basierend auf den gewünschten Methodennamen.

Der vom Gerät empfangene Text weist das folgende Format auf:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Methodenanforderungen sind QoS 0.

#### <a name="response"></a>response

Das Gerät sendet Antworten an `$iothub/methods/res/{status}/?$rid={request id}`, wobei Folgendes gilt:

* Die `status`-Eigenschaft ist der vom Gerät bereitgestellte Status der Methodenausführung.

* Die `$rid`-Eigenschaft ist die Anforderungs-ID des von IoT Hub empfangenen Methodenaufrufs.

Der Haupttext wird durch das Gerät festgelegt und kann jeden beliebigen Status aufweisen.

### <a name="amqp"></a>AMQP

Der nachstehende Abschnitt erläutert das AMQP-Protokoll.

#### <a name="method-invocation"></a>Methodenaufruf

Das Gerät empfängt Anforderungen direkter Methoden durch Erstellen eines Empfangslinks für die Adresse `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Die AMQP-Nachricht geht bei dem Empfangslink ein, der die Methodenanforderung darstellt. Sie enthält die folgenden Abschnitte:

* Die Korrelations-ID-Eigenschaft mit einer Anforderungs-ID, die mit der entsprechenden Methodenantwort zurückgegeben werden soll

* Eine Anwendungseigenschaft namens `IoThub-methodname`, die den Namen der aufgerufenen Methode enthält

* Den AMQP-Nachrichtentext mit der Methodennutzlast im JSON-Format

#### <a name="response"></a>response

Das Gerät erstellt einen Sendelink, um die Methodenantwort an der Adresse `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` zurückzugeben.

Die Antwort der Methode wird über den Sendelink zurückgegeben und umfasst Folgendes:

* Die Korrelations-ID-Eigenschaft mit der Anforderungs-ID, die in der Anforderungsnachricht der Methode übergeben wurde

* Eine Anwendungseigenschaft namens `IoThub-status`, die den vom Benutzer angegebenen Methodenstatus enthält

* Den AMQP-Nachrichtentext mit der Methodenantwort im JSON-Format

## <a name="additional-reference-material"></a>Weiteres Referenzmaterial

Weitere Referenzthemen im IoT Hub-Entwicklerhandbuch:

* Unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT Hub für Laufzeit- und Verwaltungsvorgänge verfügbar macht.

* Unter [Einschränkung und Kontingente](iot-hub-devguide-quotas-throttling.md) werden die Kontingente und das Einschränkungsverhalten beschrieben, die bei Verwendung von IoT Hub zu erwarten sind.

* Unter [Azure IoT-SDKs für Geräte und Dienste](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.

* Unter [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md) wird die IoT Hub-Abfragesprache beschrieben, mit der Sie von IoT Hub Informationen zu Gerätezwillingen und Aufträgen abrufen können.

* [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md) enthält weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie direkte Methoden verwenden, sind möglicherweise die folgenden Themen im IoT Hub-Entwicklerhandbuch für Sie interessant:

* [Planen von Aufträgen auf mehreren Geräten](iot-hub-devguide-jobs.md)

Wenn Sie einige der in diesem Artikel beschriebenen Konzepte ausprobieren möchten, ist möglicherweise das folgende IoT Hub-Tutorial für Sie interessant:

* [Verwenden von direkten Methoden](quickstart-control-device-node.md)
* [Geräteverwaltung mit Azure IoT-Tools für VS Code](iot-hub-device-management-iot-toolkit.md)
