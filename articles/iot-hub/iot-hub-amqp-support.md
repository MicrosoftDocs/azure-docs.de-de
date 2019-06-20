---
title: Grundlegendes zur Azure IoT Hub AMQP-Unterstützung | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Unterstützung für Geräte, die unter Verwendung des AMQP-Protokolls eine Verbindung mit einem geräteseitigen und dienstseitigen IoT Hub-Endpunkt herstellen. Enthält Informationen zur integrierten AMQP-Unterstützung der Azure IoT-Geräte-SDKs.'
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: d256faa42161e276e165f95c944b9f58ac4a8927
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297417"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls AMQP

IoT Hub unterstützt [AMQP Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf), um eine Vielzahl von Funktionen durch geräteseitige und dienstseitige Endpunkte bereitzustellen. Dieses Dokument beschreibt die Verwendung des AMQP-Clients, um eine Verbindung mit IoT Hub zur Verwendung der IoT Hub-Funktionalität herzustellen.

## <a name="service-client"></a>Dienstclient

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Herstellen der Verbindung mit und Authentifizierung bei IoT Hub (Dienstclient)
Ein Client kann zum Herstellen der Verbindung mit IoT Hub mithilfe von AMQP die Authentifizierung mit [Claims Based Security (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) – auf Ansprüchen basierende Sicherheit – oder [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) verwenden.

Für den Dienstclient sind die folgenden Informationen erforderlich:

| Information | Wert | 
|-------------|--------------|
| IoT Hub-Hostname | `<iot-hub-name>.azure-devices.net` |
| Schlüsselname | `service` |
| Zugriffsschüssel | Primärer oder sekundärer dem Dienst zugeordneter Schlüssel |
| Shared Access Signature (SAS) | Kurzlebige SAS in folgendem Format: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (den Code zum Generieren dieser Signatur finden Sie [hier](./iot-hub-devguide-security.md#security-token-structure)).


Der Codeausschnitt unten verwendet die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) zum Herstellen der Verbindung mit IoT Hub über einen Senderlink.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Aufrufen von C2D-Nachrichten (Dienstclient)
Der C2D-Nachrichtenaustausch zwischen Dienst und IoT Hub sowie zwischen Gerät und IoT Hub wird [hier](iot-hub-devguide-messages-c2d.md) beschrieben. Der Dienstclient verwendet die beiden unten beschriebenen Links, um Nachrichten zu senden und Feedback für zuvor von Geräten gesendete Nachrichten zu empfangen.

| Erstellt von | Verknüpfungstyp | Verknüpfungspfad | BESCHREIBUNG |
|------------|-----------|-----------|-------------|
| Dienst | Senderlink | `/messages/devicebound` | C2D-Nachrichten an Geräte werden vom Dienst an diesen Link gesendet. Die `To`-Eigenschaft von Nachrichten, die über diesen Link gesendet werden, wird auf den Linkpfad des Empfänger des Zielgeräts gesetzt, d.h. `/devices/<deviceID>/messages/devicebound`. |
| Dienst | Empfängerlink | `/messages/serviceBound/feedback` | Abschluss-, Ablehnungs- und Abbruch-Feedbacknachrichten von Geräten, die über diesen Link vom Dienst empfangen werden. Weitere Informationen zu Feedbacknachrichten finden Sie [hier](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Der folgende Codeausschnitt veranschaulicht, wie eine C2D-Nachricht erstellt und mit der [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) an ein Gerät gesendet wird.

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Um Feedback zu erhalten, erstellt der Dienstclient einen Empfängerlink. Der folgende Codeausschnitt veranschaulicht, wie hierzu die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) verwendet wird.

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Wie oben gezeigt, hat eine C2D-Feedbacknachricht den Inhaltstyp `application/vnd.microsoft.iothub.feedback.json`, und Eigenschaften in ihrem JSON-Text können verwendet werden, um den Zustellungsstatus der ursprünglichen Nachricht abzuleiten:
* Schlüssel `statusCode` im Feedbacktext hat einen der folgenden Werte: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Schlüssel `deviceId` im Feedbacktext enthält die ID des Zielgeräts.
* Schlüssel `originalMessageId` im Feedbacktext enthält die ID der ursprünglichen vom Dienst gesendeten C2D-Nachricht. Diese kann zum Korrelieren des Feedbacks für C2D-Nachrichten verwendet werden.

### <a name="receive-telemetry-messages-service-client"></a>Empfangen von Telemetrienachrichten (Dienstclient)
Standardmäßig werden die erfassten Telemetrienachrichten der Geräte von IoT Hub in einem integrierten Event Hub gespeichert. Ihr Dienstclient kann das AMQP-Protokoll nutzen, um die gespeicherten Ereignisse zu empfangen.

Zu diesem Zweck muss der Dienstclient zuerst eine Verbindung mit dem IoT Hub-Endpunkt herstellen und eine Adresse für die Umleitung zu den integrierten Event Hubs erhalten. Der Dienstclient nutzt die bereitgestellte Adresse dann, um eine Verbindung mit dem integrierten Event Hub herzustellen.

In jedem Schritt muss der Client die folgenden Informationen angeben:
* Gültige Dienstanmeldeinformationen (Dienst-SAS-Token).
* Einen korrekt formatierten Pfad zu der Consumergruppenpartition, von der Nachrichten abgerufen werden sollen. Für eine Consumergruppe und Partitions-ID hat der Pfad das folgende Format: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (die Standardconsumergruppe ist `$Default`).
* Ein optionales Filterungsprädikat zum Angeben eines Startpunkts für die Partition (eine Sequenznummer, eine Versatzangabe oder ein Einreihungszeitstempel).

Im unten angegebenen Codeausschnitt wird die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) verwendet, um die obigen Schritte zu veranschaulichen.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Für eine bestimmte Geräte-ID wird von IoT Hub ein Hashwert der Geräte-ID verwendet, um zu ermitteln, auf welcher Partition die Nachrichten gespeichert werden sollen. Im obigen Codeausschnitt ist dargestellt, wie Ereignisse von einer einzelnen Partition dieser Art empfangen werden. Beachten Sie aber, dass für eine typische Anwendung häufig Ereignisse abgerufen werden müssen, die auf allen Event Hub-Partitionen gespeichert sind.


## <a name="device-client"></a>Geräteclient

### <a name="connection-and-authenticating-to-iot-hub-device-client"></a>Verbindung mit und Authentifizierung beim IoT Hub (Geräteclient)
Ein Gerät kann zum Herstellen der Verbindung mit IoT Hub mithilfe von AMQP die Authentifizierung mit [Claims Based Security (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) (auf Ansprüchen basierende Sicherheit) oder [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) verwenden.

Für den Geräteclient sind die folgenden Informationen erforderlich:

| Information | Wert | 
|-------------|--------------|
| IoT Hub-Hostname | `<iot-hub-name>.azure-devices.net` |
| Zugriffsschüssel | Primärer oder sekundärer dem Gerät zugeordneter Schlüssel |
| Shared Access Signature (SAS) | Kurzlebige SAS in folgendem Format: `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}` (den Code zum Generieren dieser Signatur finden Sie [hier](./iot-hub-devguide-security.md#security-token-structure)).


Der Codeausschnitt unten verwendet die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python) zum Herstellen der Verbindung mit IoT Hub über einen Senderlink.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Die folgenden Linkpfade werden als Gerätevorgänge unterstützt:

| Erstellt von | Verknüpfungstyp | Verknüpfungspfad | BESCHREIBUNG |
|------------|-----------|-----------|-------------|
| Geräte | Empfängerlink | `/devices/<deviceID>/messages/devicebound` | An Geräte gerichtete C2D-Nachrichten werden von jedem Zielgerät über diesen Link empfangen. |
| Geräte | Senderlink | `/devices/<deviceID>messages/events` | Von einem Gerät gesendete D2C-Nachrichten werden über diesen Link gesendet. |
| Geräte | Senderlink | `/messages/serviceBound/feedback` | Von Geräten über diesen Link an den Dienst gesendetes C2D-Nachrichtenfeedback. |


### <a name="receive-c2d-commands-device-client"></a>Empfangen von C2D-Befehlen (Geräteclient)
C2D-Befehle, die an Geräte gesendet werden, werden über den `/devices/<deviceID>/messages/devicebound`-Link empfangen. Geräte können diese Nachrichten in Batches empfangen und bei Bedarf die Nachrichtendatennutzlast, Nachrichteneigenschaften, Anmerkungen oder Anwendungseigenschaften in der Nachricht verwenden.

Der unten angegebene Codeausschnitt verwendet die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python), um C2D-Nachrichten auf einem Gerät zu empfangen.

```python
# ... 
# Create a receive client for the C2D receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in Event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Senden von Telemetrienachrichten (Geräteclient)
Telemetrienachrichten werden von Geräten ebenfalls über AMQP gesendet. Das Gerät kann optional ein Verzeichnis der Anwendungseigenschaften oder verschiedene Nachrichteneigenschaften wie die Nachrichten-ID bereitstellen.

Der unten angegebene Codeausschnitt verwendet die [uAMQP-Bibliothek in Python](https://github.com/Azure/azure-uamqp-python), um D2C-Nachrichten von einem Gerät zu senden.


```python
# ... 
# Create a send client for the D2C send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Zusätzliche Hinweise
* Die AMQP-Verbindungen können aufgrund von Netzwerkstörungen oder Ablauf des (im Code generierten) Authentifizierungstokens unterbrochen werden. Der Dienstclient muss diese Situationen bewältigen und die Verbindung und Links bei Bedarf wiederherstellen. Im Fall des Ablaufs des Authentifizierungstokens kann der Client das Token auch proaktiv vor dessen Ablauf verlängern, um einen Verbindungsausfall zu vermeiden.
* In einigen Fällen muss Ihr Client Linkumleitungen ordnungsgemäß verarbeiten können. Informationen zur Verarbeitung dieses Vorgangs finden Sie in der Dokumentation Ihres AMQP-Clients.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum AMQP-Protokoll finden Sie in der [AMQP 1.0-Spezifikation](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Weitere Informationen zum IoT Hub-Messaging finden Sie unter:

* [Cloud-zu-Gerät-Nachrichten](./iot-hub-devguide-messages-c2d.md)
* [Unterstützen zusätzlicher Protokolle](iot-hub-protocol-gateway.md)
* [Unterstützung für das MQTT-Protokoll](./iot-hub-mqtt-support.md)
