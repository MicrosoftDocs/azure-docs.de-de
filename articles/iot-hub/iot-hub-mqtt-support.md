---
title: Grundlegendes zur Azure IoT Hub MQTT-Unterstützung | Microsoft-Dokumentation
description: Unterstützung für Geräte, die mithilfe des MQTT-Protokolls eine Verbindung mit einem geräteseitigen IoT Hub-Endpunkt herstellen. Enthält Informationen zur integrierten MQTT-Unterstützung der Azure IoT-Geräte-SDKs.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: d206f40380ddb60a53ec8af2802a65af94f5820d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027797"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT

IoT Hub ermöglicht Geräten die Kommunikation mit den IoT Hub-Geräteendpunkten mithilfe von:

* [MQTT v3.1.1](https://mqtt.org/) an Port 8883
* MQTT v3.1.1 über WebSocket an Port 443

IoT Hub ist kein voll funktionsfähiger MQTT-Broker und unterstützt nicht alle im MQTT 3.1.1-Standard angegebenen Verhaltensweisen. In diesem Artikel wird beschrieben, wie Geräte unterstützte MQTT-Verhaltensweisen für die Kommunikation mit IoT Hub verwenden können.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Die gesamte Gerätekommunikation mit IoT Hub muss mithilfe von TLS/SSL gesichert werden. Daher unterstützt IoT Hub keine unsicheren Verbindungen über Port 1883.

## <a name="connecting-to-iot-hub"></a>Herstellen einer Verbindung mit einem IoT Hub

Ein Gerät kann das MQTT-Protokoll zum Herstellen einer Verbindung mit einem IoT-Hub über die folgenden Optionen verwenden.

* Bibliotheken in den [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks)
* Das MQTT-Protokoll direkt

Der MQTT-Port (8883) wird in vielen Netzwerken von Unternehmen und Bildungseinrichtungen blockiert. Wenn Sie Port 8883 in Ihrer Firewall nicht öffnen können, empfiehlt es sich, MQTT über WebSockets zu verwenden. MQTT über WebSockets kommuniziert über Port 443, der in Netzwerkumgebungen fast immer geöffnet ist. Informationen zum Angeben der Protokolle für MQTT und MQTT über WebSockets bei Verwendung der Azure IoT SDKs finden Sie unter [Verwenden der Geräte-SDKs](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Verwenden der Geräte-SDKs

[Geräte-SDKs](https://github.com/Azure/azure-iot-sdks), die das MQTT-Protokoll unterstützen, stehen für Java, Node.js, C, C# und Python zur Verfügung. Die SDKs für Geräte verwenden die IoT Hub-Standard-Verbindungszeichenfolge zum Herstellen einer Verbindung mit einem IoT Hub. Um das MQTT-Protokoll verwenden zu können, muss der Clientprotokollparameter auf **MQTT** festgelegt werden. Sie können MQTT über WebSockets auch im Parameter für das Clientprotokoll angeben. Standardmäßig verbinden sich die SDKs von Geräten mit einem IoT Hub, indem das **CleanSession**-Flag auf **0** festgelegt und **QoS 1** für den Nachrichtenaustausch mit dem IoT Hub verwendet wird. Es ist möglich, **QoS 0** für einen schnelleren Nachrichtenaustausch zu konfigurieren, dabei sollten Sie jedoch beachten, dass die Übermittlung weder garantiert noch bestätigt wird. Aus diesem Grund wird **QoS 0** oft als „Fire and Forget“ bezeichnet.

Wenn ein Gerät mit einem IoT Hub verbunden ist, werden mit den SDKs von Geräten Methoden bereitgestellt, die dem Gerät den Austausch von Nachrichten mit einem IoT Hub ermöglichen.

Die folgende Tabelle enthält Links zu Codebeispielen für jede unterstützte Sprache und gibt die Parameter zum Herstellen einer Verbindung mit IoT Hub über die Protokolle für MQTT oder MQTT über WebSockets an.

| Sprache | Parameter für das MQTT-Protokoll | Parameter für das Protokoll für MQTT über WebSockets
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt.Mqtt | azure-iot-device-mqtt.MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable).MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet).Mqtt | TransportType.Mqtt greift auf MQTT über WebSockets zurück, wenn bei MQTT ein Fehler auftritt. Um nur MQTT über WebSockets anzugeben, verwenden Sie TransportType.Mqtt_WebSocket_Only. |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Unterstützt standardmäßig MQTT | Fügen Sie `websockets=True` im Befehl hinzu, um den Client zu erstellen. |

Das folgende Fragment zeigt, wie Sie bei Verwendung des Azure IoT SDK für Node.js das Protokoll für MQTT über WebSockets angeben:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Das folgende Fragment zeigt, wie Sie bei Verwendung des Azure IoT SDK für Python das Protokoll für MQTT über WebSockets angeben:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Keep-Alive-Standardtimeout

Um sicherzustellen, dass eine Client/IoT Hub-Verbindung aktiv bleibt, senden sowohl der Dienst als auch der Client einander regelmäßig einen *Keep-Alive*-Ping. Der Client, der das IoT SDK verwendet, sendet in dem in der nachstehenden Tabelle definierten Intervall ein Keep-Alive:

|Sprache  |Keep-Alive-Standardintervall  |Konfigurierbar  |
|---------|---------|---------|
|Node.js     |   180 Sekunden      |     Nein    |
|Java     |    230 Sekunden     |     Nein    |
|C     | 240 Sekunden |  [Ja](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 Sekunden |  [Ja](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 Sekunden |  Nein   |

Entsprechend der [MQTT-Spezifikation](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081) ist das Keep-Alive-Pingintervall von IoT Hub das 1,5-fache des Keep-Alive-Werts für Clients. IoT Hub schränkt jedoch das maximale serverseitige Timeout auf 29,45 Minuten (1.767 Sekunden) ein, weil sämtliche Azure-Dienste an das TCP-Leerlauftimeout von Azure Load Balancer (29,45 Minuten) gebunden sind. 

So sendet beispielsweise ein Gerät, das das Java SDK verwendet, den Keep-Alive-Ping und verliert dann die Netzwerkkonnektivität. 230 Sekunden später verpasst das Gerät den Keep-Alive-Ping, weil es offline ist. Allerdings schließt IoT Hub die Verbindung nicht sofort, sondern wartet weitere `(230 * 1.5) - 230 = 115` Sekunden, bevor es die Geräteverbindung mit der Fehlermeldung [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md) trennt. 

Als maximalen Keep-Alive-Wert für Clients können Sie `1767 / 1.5 = 1177` Sekunden festlegen. Das Keep-Alive wird durch jeglichen Datenverkehr zurückgesetzt. Beispielsweise setzt eine erfolgreiche SAS-Tokenaktualisierung das Keep-Alive zurück.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrieren einer Geräte-App von AMQP zu MQTT

Wie bereits erwähnt, muss bei Verwendung der [Geräte-SDKs](https://github.com/Azure/azure-iot-sdks) bei einem Wechsel von AMQP zu MQTT der Protokollparameter in der Clientinitialisierung geändert werden.

Dabei sollten Sie die folgenden Punkte beachten:

* Bei AMQP werden Fehler für viele Bedingungen zurückgegeben, bei MQTT wird dagegen die Verbindung beendet. Daher müssen an der Ausnahmebehandlungslogik möglicherweise einige Änderungen vorgenommen werden.

* MQTT unterstützt beim Empfang von [Cloud-zu-Gerät-Nachrichten](iot-hub-devguide-messaging.md) keine *reject*-Vorgänge. Wenn Ihre Back-End-App eine Antwort von der Geräte-App erhalten muss, können Sie [direkte Methoden](iot-hub-devguide-direct-methods.md) verwenden.

* AMQP wird im Python SDK nicht unterstützt.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Beispiel in C mit MQTT ohne Azure IoT SDK

Im [IoT MQTT-Beispielrepository](https://github.com/Azure-Samples/IoTMQTTSample) finden Sie einige Demoprojekte für C/C++, die zeigen, wie Sie Telemetrienachrichten senden und Ereignisse mit einem IoT Hub empfangen, ohne das Azure IoT C SDK verwenden zu müssen. 

In diesen Beispielen dient die Eclipse Mosquitto-Bibliothek zum Senden von Nachrichten an den im IoT Hub implementierten MQTT-Broker.

Dieses Repository enthält Folgendes:

**Für Windows:**

* TelemetryMQTTWin32: Enthält Code zum Senden einer Telemetrienachricht an einen Azure IoT Hub, der auf einem Windows-Computer erstellt und ausgeführt wird.

* SubscribeMQTTWin32: Enthält Code zum Abonnieren von Ereignissen eines bestimmten IoT Hubs auf einem Windows-Computer.

* DeviceTwinMQTTWin32: Enthält Code zum Abfragen und Abonnieren der Gerätezwillingsereignisse eines Geräts im Azure IoT Hub auf einem Windows-Computer.

* PnPMQTTWin32: Enthält Code zum Senden einer Telemetrienachricht mit IoT Plug & Play-Gerätefunktionen an einen Azure IoT-Hub, der auf einem Windows-Computer erstellt und ausgeführt wird. Weitere Informationen finden Sie unter [Was ist IoT Plug & Play?](../iot-pnp/overview-iot-plug-and-play.md).

**Für Linux:**

* MQTTLinux: Enthält Code und ein Buildskript zur Ausführung unter Linux (bisher wurden WSL, Ubuntu und Raspbian getestet).

* LinuxConsoleVS2019: Enthält denselben Code, aber in einem VS2019-Projekt für WSL (Windows-Subsystem für Linux). Dieses Projekt ermöglicht Ihnen das schrittweise Debuggen des unter Linux ausgeführten Codes in Visual Studio.

**Für mosquitto_pub:**

Dieser Ordner enthält zwei Beispielbefehle, die bei dem Hilfsprogrammtool „mosquitto_pub“ von Mosquitto.org verwendet werden.

* „Mosquitto_sendmessage“: Dient zum Senden einer einfachen Textnachricht an einen Azure IoT Hub, der als Gerät fungiert.

* „Mosquitto_subscribe“: Dient zum Anzeigen von Ereignissen, die in einem Azure IoT Hub eintreten.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Direktes Verwenden des Protokolls MQTT (als Gerät)

Wenn ein Gerät die SDKs von Geräten nicht verwenden kann, lässt es sich dennoch mithilfe des Protokolls MQTT über den Port 8883 mit den öffentlichen Geräteendpunkten verbinden. Das Gerät sollte im **CONNECT**-Paket die folgenden Werte verwenden:

* Verwenden Sie für das Feld **ClientId** die **deviceId**-Eigenschaft.

* Verwenden Sie `{iothubhostname}/{device_id}/?api-version=2018-06-30` für das Feld **Benutzername**, wobei `{iothubhostname}` der vollständige CNAME für den IoT Hub ist.

    Beispiel: Wenn der Name für den IoT Hub **contoso.azure devices.net** und der Name des Geräts **MyDevice01** lautet, sollte das vollständige Feld **Benutzername** Folgendes enthalten:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Verwenden Sie im Feld **Kennwort** ein SAS-Token. Das Format des SAS-Tokens ist das gleiche wie das für die Protokolle HTTPS und AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Bei Verwendung der X.509-Zertifikatauthentifizierung sind keine SAS-Tokenkennwörter erforderlich. Weitere Informationen finden Sie unter [Einrichten der X.509-Sicherheit in Ihrem Azure IoT Hub](iot-hub-security-x509-get-started.md). Befolgen Sie außerdem die Codeanweisungen im [Abschnitt „TLS/SSL-Konfiguration“](#tlsssl-configuration).

  Weitere Informationen zum Generieren von SAS-Token finden Sie unter [Verwenden von IoT-Hub-Sicherheitstoken](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app) im Abschnitt zu Geräten.

  Beim Testen können Sie auch mithilfe der plattformübergreifenden [Azure IoT Tools für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) oder des CLI-Erweiterungsbefehls [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token) schnell ein SAS-Token generieren, das Sie kopieren und in Ihren eigenen Code einfügen können.

### <a name="for-azure-iot-tools"></a>Für Azure IoT Tools

1. Erweitern Sie in der unteren linken Ecke von Visual Studio Code die Registerkarte **AZURE IOT HUB-GERÄTE**.
  
2. Klicken Sie mit der rechten Maustaste auf Ihr Gerät, und klicken Sie auf **SAS-Token für Gerät generieren**.
  
3. Legen Sie die **Ablaufzeit** fest, und drücken Sie die EINGABETASTE.
  
4. Das SAS-Token wird erstellt und in die Zwischenablage kopiert.

   Das generierte SAS-Token weist die folgende Struktur auf:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Der folgende Teil des Tokens wird im Feld **Kennwort** verwendet, um über MQTT eine Verbindung herzustellen:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Für die MQTT-Pakete CONNECT und DISCONNECT löst IoT Hub ein Ereignis im Kanal **Vorgangsüberwachung** aus. Dieses Ereignis weist zusätzliche Informationen auf, mit deren Hilfe Sie Konnektivitätsprobleme beheben können.

Die Geräte-App kann eine **Will**-Nachricht im **CONNECT**-Paket angeben. Für die Geräte-App sollte `devices/{device_id}/messages/events/` oder `devices/{device_id}/messages/events/{property_bag}` als **Will**-Themenname verwendet werden, um festzulegen, dass **Will**-Nachrichten als Telemetrienachricht weitergeleitet werden sollen. Wenn die Netzwerkverbindung geschlossen ist, aber vorher kein **DISCONNECT**-Paket vom Gerät eingegangen ist, sendet IoT Hub in diesem Fall die im **CONNECT**-Paket bereitgestellte **Will**-Nachricht an den Telemetriekanal. Der Telemetriekanal kann entweder der Standardendpunkt **Ereignisse** oder ein benutzerdefinierter Endpunkt sein, der per IoT Hub-Routing definiert wird. Die Nachricht verfügt über die **iothub-MessageType**-Eigenschaft, der der Wert **Will** zugewiesen ist.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Direktes Verwenden des Protokolls MQTT (als Modul)

Das Herstellen einer Verbindung mit IoT Hub über MQTT mithilfe einer Modulidentität ähnelt dem Gerät (beschrieben [im Abschnitt über die direkte Verwendung des MQTT-Protokolls als Gerät](#using-the-mqtt-protocol-directly-as-a-device)). Sie müssen aber Folgendes verwenden:

* Legen Sie für die Client-ID `{device_id}/{module_id}` fest.

* Bei einer Authentifizierung mit Benutzername und Kennwort legen Sie für den Benutzernamen `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` fest, und verwenden Sie das der Modulidentität zugeordnete SAS-Token als Ihr Kennwort.

* Verwenden Sie `devices/{device_id}/modules/{module_id}/messages/events/` als Thema für die Veröffentlichung von Telemetriedaten.

* Verwenden Sie `devices/{device_id}/modules/{module_id}/messages/events/` als Will-Thema.

* Die Zwillingsthemen GET und PATCH sind bei Modulen und Geräten identisch.

* Das Zwillingsstatusthema ist bei Modulen und Geräten identisch.

## <a name="tlsssl-configuration"></a>TLS/SSL-Konfiguration

Damit Sie das MQTT-Protokoll direkt verwenden können, *muss* Ihr Client die Verbindung über TLS/SSL herstellen. Wenn Sie versuchen, diesen Schritt zu überspringen, treten Verbindungsfehler auf.

Möglicherweise müssen Sie das DigiCert Baltimore-Stammzertifikat herunterladen und darauf verweisen, um eine TLS-Verbindung herstellen zu können. Dieses Zertifikat wird von Azure zum Sichern der Verbindung verwendet. Sie finden dieses Zertifikat im Repository [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c). Weitere Informationen zu diesen Zertifikaten finden Sie auf der [Website von Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Ein Beispiel zur Implementierung mithilfe der Python-Version der [Paho MQTT-Bibliothek](https://pypi.python.org/pypi/paho-mqtt) von der Eclipse Foundation könnte wie folgt aussehen.

Installieren Sie zunächst die Paho-Bibliothek aus Ihrer Befehlszeilenumgebung:

```cmd/sh
pip install paho-mqtt
```

Implementieren Sie anschließend den Client in einem Python-Skript. Ersetzen Sie die Platzhalter wie folgt:

* `<local path to digicert.cer>` ist der Pfad zu einer lokalen Datei, die das DigiCert Baltimore-Stammzertifikat enthält. Sie können diese Datei erstellen, indem Sie die Zertifikatinformationen aus [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) in das Azure IoT SDK für C kopieren. Fügen Sie die Zeilen `-----BEGIN CERTIFICATE-----` und `-----END CERTIFICATE-----` ein, entfernen Sie die `"`-Markierungen am Anfang und Ende jeder Zeile, und entfernen Sie die Zeichen `\r\n` am Ende jeder Zeile.

* `<device id from device registry>` ist die ID eines Geräts, das Sie Ihrem IoT Hub hinzugefügt haben.

* `<generated SAS token>` ist ein SAS-Token für das Gerät, das wie zuvor in diesem Artikel beschrieben erstellt wurde.

* `<iot hub name>` ist der Name Ihres IoT Hubs.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Um sich mit einem Gerätezertifikat zu authentifizieren, aktualisieren Sie den obigen Codeausschnitt mit den folgenden Änderungen (siehe [Abrufen eines X.509-Zertifizierungsstellenzertifikats](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate), um mehr zur Vorbereitung der zertifikatsbasierten Authentifizierung zu erfahren):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Senden von D2C-Nachrichten

Nachdem Sie erfolgreich eine Verbindung hergestellt haben, kann ein Gerät Nachrichten mithilfe von `devices/{device_id}/messages/events/` oder `devices/{device_id}/messages/events/{property_bag}` als **Themenname** an IoT Hub senden. Das `{property_bag}` -Element ermöglicht dem Gerät das Senden von Nachrichten mit zusätzlichen Eigenschaften in einem URL-codierten Format. Beispiel:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Dieses `{property_bag}`-Element verwendet die gleiche Codierung wie Abfragezeichenfolgen im HTTPS-Protokoll.

Hier ist eine Liste mit dem spezifischen Verhalten für die IoT Hub-Implementierung angegeben:

* IoT Hub unterstützt keine QoS 2-Nachrichten. Wenn eine Geräte-App eine Nachricht mit **QoS 2** veröffentlicht, schließt IoT Hub die Netzwerkverbindung.

* IoT Hub speichert Beibehaltungsnachrichten („Retain“) nicht beständig. Wenn ein Gerät eine Nachricht mit auf 1 festgelegtem **RETAIN**-Flag sendet, fügt IoT Hub der Nachricht die Anwendungseigenschaft **mqtt-retain** hinzu. In diesem Fall speichert IoT Hub die Beibehaltungsnachricht nicht beständig, sondern übergibt sie an die Back-End-App.

* IoT Hub unterstützt nur eine aktive MQTT-Verbindung pro Gerät. Jede neue MQTT-Verbindung für dieselbe Geräte-ID bewirkt, dass IoT Hub die vorhandene Verbindung löscht und in IoT Hub-Protokollen **400027 ConnectionForcefullyClosedOnNewConnectionn** protokolliert wird.


Weitere Informationen finden Sie im [Entwicklerhandbuch zum Messaging](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Empfangen von C2D-Nachrichten

Zum Empfangen von Nachrichten von einem IoT Hub muss ein Gerät ein Abonnement unter Verwendung von `devices/{device_id}/messages/devicebound/#` als **Themenfilter** einrichten. Der Platzhalter mit mehreren Ebenen `#` im Themenfilter wird nur verwendet, um dem Gerät das Empfangen zusätzlicher Eigenschaften im Themennamen zu erlauben. IoT Hub lässt die Verwendung des Platzhalters `#` oder `?` für die Filterung von Unterthemen nicht zu. Da IoT Hub kein allgemeiner Nachrichtenbrokerdienst für das Veröffentlichen und Abonnieren ist, werden nur die dokumentierten Themennamen und -filter unterstützt.

Das Gerät empfängt Nachrichten von IoT Hub erst, nachdem es dessen gerätespezifischen Endpunkt erfolgreich abonniert hat, der durch den Themenfilter `devices/{device_id}/messages/devicebound/#` dargestellt wird. Nachdem ein Abonnement eingerichtet wurde, empfängt das Gerät C2D-Nachrichten, die nach dem Zeitpunkt des Abonnements an das Gerät gesendet wurden. Wenn das Gerät eine Verbindung mit auf **0** festgelegtem **CleanSession**-Flag herstellt, behält das Abonnement verschiedene Sitzungen übergreifend bei. In diesem Fall empfängt das Gerät beim nächsten Verbindungsaufbau mit **CleanSession 0** ausstehende Nachrichten, die ihm gesendet wurden, als es vom Netzwerk getrennt war. Wenn das Gerät das auf **1** festgelegte **CleanSession**-Flag verwendet, empfängt es erst dann Nachrichten von der IoT Hub-Instanz, wenn es deren Geräteendpunkt abonniert.

IoT Hub sendet Nachrichten mit dem **Themennamen** `devices/{device_id}/messages/devicebound/` oder `devices/{device_id}/messages/devicebound/{property_bag}`, wenn Nachrichteneigenschaften vorhanden sind. `{property_bag}` enthält URL-codierte Schlüssel-Wert-Paare von Nachrichteneigenschaften. Nur Anwendungseigenschaften und vom Benutzer festlegbare Systemeigenschaften (z.B. **messageId** oder **correlationId**) sind im Eigenschaftenbehälter enthalten. Systemeigenschaftennamen haben das Präfix **$** , Anwendungseigenschaften verwenden den ursprünglichen Eigenschaftennamen ohne Präfix. Weitere Details zum Format des Eigenschaftenbehälters finden Sie unter [Senden von D2C-Nachrichten](#sending-device-to-cloud-messages).

Bei Cloud-zu-Gerät-Nachrichten werden die Werte im Eigenschaftenbehälter wie in folgender Tabelle gezeigt dargestellt:

| Eigenschaftswert | Darstellung | BESCHREIBUNG |
|----|----|----|
| `null` | `key` | Nur der Schlüssel wird im Eigenschaftenbehälter angezeigt |
| Leere Zeichenfolge | `key=` | Der Schlüssel gefolgt von einem Gleichheitszeichen ohne Wert |
| nicht NULL, nicht leerer Wert | `key=value` | Der Schlüssel gefolgt von einem Gleichheitszeichen und dem Wert |

Das folgende Beispiel zeigt einen Eigenschaftenbehälter, der drei Anwendungseigenschaften enthält: **prop1** mit einem Wert von `null`; **prop2**, eine leere Zeichenfolge („“) und **prop3** mit einem Wert „eine Zeichenfolge“.

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Wenn eine Geräte-App ein Thema mit **QoS 2** abonniert, gewährt IoT Hub im **SUBACK**-Paket maximal die QoS-Ebene 1. Danach übermittelt IoT Hub mithilfe von QoS 1 Nachrichten an das Gerät.

## <a name="retrieving-a-device-twins-properties"></a>Abrufen der Eigenschaften eines Gerätezwillings

Als Erstes abonniert ein Gerät `$iothub/twin/res/#`, um die Antworten des Vorgangs zu erhalten. Anschließend wird eine leere Nachricht an das Thema `$iothub/twin/GET/?$rid={request id}` gesendet, wobei der Wert für **request id** ausgefüllt ist. Als Nächstes sendet der Dienst eine Antwortnachricht mit den Daten des Gerätezwillings im Thema `$iothub/twin/res/{status}/?$rid={request id}`, indem die gleiche **request id** wie für die Anforderung verwendet wird.

Die Anforderungs-ID (Request ID) kann ein beliebiger gültiger Wert für den Eigenschaftswert einer Nachricht sein (siehe [Entwicklerhandbuch zum IoT-Hub-Messaging](iot-hub-devguide-messaging.md)). Der Status wird als „Integer“ validiert.

Der Text der Antwort enthält den Abschnitt mit den Eigenschaften des Gerätezwillings, wie im folgenden Antwortbeispiel gezeigt:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Die möglichen Statuscodes lauten:

|Status | Beschreibung |
| ----- | ----------- |
| 200 | Erfolg |
| 429 | Zu viele Anforderungen (gedrosselt), siehe [IoT Hub-Drosselung](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfehler |

Weitere Informationen finden Sie im [Entwicklerhandbuch zu Gerätezwillingen](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualisieren der gemeldeten Eigenschaften des Gerätezwillings

Zum Aktualisieren der gemeldeten Eigenschaften gibt das Gerät eine Anforderung an den IoT Hub in Form einer Veröffentlichung über ein designiertes MQTT-Thema aus. Nach dem Verarbeiten dieser Anforderung antwortet IoT Hub mit dem Erfolgs- oder Fehlerstatus des Aktualisierungsvorgangs in Form einer Veröffentlichung unter einem anderen Thema. Dieses Thema kann vom Gerät abonniert werden, um es über das Ergebnis der Aktualisierungsanforderung seines Gerätezwillings zu benachrichtigen. Um diese Art von Anforderungs-/Antwortinteraktion in MQTT zu implementieren, nutzen wir das Konzept der Anforderungs-ID (`$rid`), die ursprünglich vom Gerät in seiner Aktualisierungsanforderung bereitgestellt wurde. Diese Anforderungs-ID ist auch in der Antwort von IoT Hub enthalten, damit das Gerät die Antwort mit seiner jeweiligen früheren Anforderung korrelieren kann.

Die folgende Sequenz beschreibt, wie ein Gerät die gemeldeten Eigenschaften in dem Gerätezwilling in IoT Hub aktualisiert:

1. Ein Gerät muss zuerst das Thema `$iothub/twin/res/#` abonnieren, um die Antworten des Vorgangs von IoT Hub zu empfangen.

2. Ein Gerät sendet eine Nachricht, die das Gerätezwillingsupdate enthält, an das Thema `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Diese Nachricht enthält einen **request ID**-Wert.

3. Anschließend sendet der Dienst eine Antwortnachricht, die den neuen ETag-Wert für die Sammlung der gemeldeten Eigenschaften enthält, im Thema `$iothub/twin/res/{status}/?$rid={request id}`. Diese Antwortnachricht verwendet den gleichen **request id**-Wert wie die Anforderung.

Der Nachrichtentext der Anforderung enthält ein JSON-Dokument mit neuen Werten für gemeldete Eigenschaften. Jeder Member im JSON-Dokument wird aktualisiert, oder der entsprechende Member wird im Dokument des Gerätezwillings hinzugefügt. Wenn ein Member auf `null` festgelegt wurde, wird er aus dem enthaltenden Objekt gelöscht. Beispiel:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Die möglichen Statuscodes lauten:

|Status | BESCHREIBUNG |
| ----- | ----------- |
| 204 | Erfolg (kein Inhalt) |
| 400 | Ungültige Anforderung; falsch formatierter JSON-Code |
| 429 | Zu viele Anforderungen (gedrosselt), siehe [IoT Hub-Drosselung](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfehler |

Der Python-Codeausschnitt unten veranschaulicht den Aktualisierungsvorgang der vom Gerätezwilling gemeldeten Eigenschaften über MQTT (mithilfe des Paho MQTT-Clients):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Bei erfolgreichem Abschluss des Aktualisierungsvorgangs der vom Gerätezwilling gemeldeten Eigenschaften oben weist die Veröffentlichungsnachricht von IoT Hub das folgende Thema auf: `$iothub/twin/res/204/?$rid=1&$version=6`, wobei `204` der Statuscode ist, der den Erfolg angibt, `$rid=1` der vom Gerät im Code übergebenen Anforderungs-ID und `$version` der Version des Abschnitts der gemeldeten Eigenschaften der Gerätezwillinge nach der Aktualisierung entspricht.

Weitere Informationen finden Sie im [Entwicklerhandbuch zu Gerätezwillingen](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Empfangen von Aktualisierungsbenachrichtigungen für gewünschte Eigenschaften

Wenn die Verbindung für ein Gerät hergestellt wird, sendet IoT Hub Benachrichtigungen an das Thema `$iothub/twin/PATCH/properties/desired/?$version={new version}`. Die Benachrichtigungen enthalten den Inhalt der Aktualisierung, die vom Lösungs-Back-End durchgeführt wird. Beispiel:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Für Aktualisierungen von Eigenschaften bedeutet die Angabe von `null` für die Werte, dass der JSON-Objektmember gelöscht wird. Beachten Sie auch, dass `$version` die neue Version des Abschnitts mit den gewünschten Eigenschaften des Zwillings angibt.

> [!IMPORTANT]
> IoT Hub generiert Änderungsbenachrichtigungen nur, wenn Geräte verbunden sind. Achten Sie darauf, den [Flow zur Wiederherstellung der Geräteverbindung](iot-hub-devguide-device-twins.md#device-reconnection-flow) zu implementieren, um für die gewünschten Eigenschaften die Synchronisierung zwischen dem IoT-Hub und der Geräte-App aufrechtzuerhalten.

Weitere Informationen finden Sie im [Entwicklerhandbuch zu Gerätezwillingen](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Antworten auf eine direkte Methode

Als Erstes muss ein Gerät `$iothub/methods/POST/#` abonnieren. IoT Hub sendet Methodenanforderungen an das Thema `$iothub/methods/POST/{method name}/?$rid={request id}`, die entweder gültigen JSON-Code oder leeren Text enthalten.

Als Antwort sendet das Gerät eine Nachricht mit einem gültigen JSON-Code oder leerem Text an das Thema `$iothub/methods/res/{status}/?$rid={request id}`. In dieser Nachricht muss die **Anforderungs-ID** derjenigen in der Anforderungsnachricht entsprechen, und **Status** muss eine ganze Zahl sein.

Weitere Informationen finden Sie im [Entwicklerhandbuch zu direkten Methoden](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Weitere Überlegungen

Wenn Sie das Verhalten des MQTT-Protokolls auf der Cloudseite anpassen müssen, sollten Sie den Artikel zum [Azure IoT-Protokollgateway](iot-hub-protocol-gateway.md) lesen. Diese Software ermöglicht Ihnen die Bereitstellung eines benutzerdefinierten Hochleistungs-Protokollgateways, das eine direkte Schnittstelle mit IoT Hub bildet. Das Azure IoT-Protokollgateway dient zum Anpassen des Geräteprotokolls zum Unterstützen von Brownfield MQTT-Bereitstellungen oder anderer benutzerdefinierter Protokolle. Dieser Ansatz setzt jedoch voraus, dass Sie ein benutzerdefiniertes Protokollgateway ausführen und betreiben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum MQTT-Protokoll finden Sie in der [MQTT-Dokumentation](https://mqtt.org/).

Weitere Informationen zum Planen Ihrer IoT Hub-Bereitstellung finden Sie unter:

* [Katalog mit Azure Certified for IoT-Geräten](https://catalog.azureiotsolutions.com/)
* [Unterstützen zusätzlicher Protokolle](iot-hub-protocol-gateway.md)
* [Vergleich mit Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalierung, Hochverfügbarkeit und Notfallwiederherstellung](iot-hub-scaling.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)
