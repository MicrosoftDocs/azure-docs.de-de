---
title: Senden von Sicherheitsmeldungen für Defender für IoT-Geräte
description: Hier erfahren Sie, wie Sie Ihre Sicherheitsmeldungen unter Verwendung von Defender für IoT senden.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/8/2021
ms.author: shhazam
ms.custom: devx-track-js
ms.openlocfilehash: d59121f2dbae208ba045a9c2e6d66245296537a0
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820686"
---
# <a name="send-security-messages-sdk"></a>Senden von Sicherheitsmeldungen (SDK)

In dieser Schrittanleitung werden die Funktionen von Defender für IoT erläutert, die zur Verfügung stehen, wenn Sie die Sicherheitsmeldungen Ihres Geräts ohne Verwendung eines Defender für IoT-Agents erfassen und senden möchten. Außerdem erfahren Sie, wie Sie dazu vorgehen müssen.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Senden von Sicherheitsmeldungen mithilfe des Azure IoT C SDK
> * Senden von Sicherheitsmeldungen mithilfe des Azure IoT C# SDK
> * Senden von Sicherheitsmeldungen mithilfe des Azure IoT Python SDK
> * Senden von Sicherheitsmeldungen mithilfe des Azure IoT Node.js SDK
> * Senden von Sicherheitsmeldungen mithilfe des Azure IoT Java SDK

## <a name="defender-for-iot-capabilities"></a>Defender für IoT-Funktionen

Defender für IoT kann beliebige Sicherheitsmeldungsdaten verarbeiten und analysieren, solange die gesendeten Daten dem [Defender für IoT-Schema](https://aka.ms/iot-security-schemas) entsprechen und die Meldung als Sicherheitsmeldung festgelegt ist.

## <a name="security-message"></a>Sicherheitsmeldung

Defender für IoT definiert eine Sicherheitsmeldung anhand folgender Kriterien:

- Ob die Meldung mit dem Azure IoT SDK gesendet wurde
- Ob die Meldung dem [Sicherheitsmeldungsschema](https://aka.ms/iot-security-schemas) entspricht
- Ob die Meldung vor dem Senden als Sicherheitsmeldung festgelegt wurde

Jede Sicherheitsmeldung enthält die Metadaten des Senders, z. B. `AgentId`, `AgentVersion` oder `MessageSchemaVersion`, und eine Liste der Sicherheitsereignisse.
Das Schema definiert die gültigen und erforderlichen Eigenschaften der Sicherheitsmeldung, einschließlich der Ereignistypen.

> [!NOTE]
> Meldungen, die dem Schema nicht entsprechen, werden ignoriert. Überprüfen Sie das Schema, bevor Sie das Senden von Daten initiieren, da ignorierte Meldungen derzeit nicht gespeichert werden.

> [!NOTE]
> Gesendete Meldungen, die nicht unter Verwendung des Azure IoT SDK als Sicherheitsmeldung festgelegt wurden, werden nicht an die Defender für IoT-Pipeline weitergeleitet.

## <a name="valid-message-example"></a>Beispiel für eine gültige Meldung

Das folgende Beispiel zeigt ein gültiges Sicherheitsmeldungsobjekt. Das Beispiel enthält die Metadaten der Meldung und ein `ProcessCreate`-Sicherheitsereignis.

Nachdem sie als Sicherheitsmeldung festgelegt und gesendet wurde, wird diese Meldung in Defender für IoT verarbeitet.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Senden von Sicherheitsmeldungen

Sie können Sicherheitsmeldungen *ohne* Verwendung des Defender für IoT-Agents mithilfe des [Azure IoT C-Geräte-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), des [Azure IoT C#-Geräte-SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), des [Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), des [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) oder des [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java) senden.

Wenn Sie Daten Ihrer Geräte zur Verarbeitung an Defender für IoT senden möchten, verwenden Sie eine der folgenden APIs, um Meldungen für die korrekte Weiterleitung an die Verarbeitungspipeline von Defender für IoT-Verarbeitungspipeline zu markieren.

Alle gesendeten Daten müssen dem [Meldungsschema von Defender für IoT](https://aka.ms/iot-security-schemas) entsprechen. Das gilt auch für Daten, die mit dem korrekten Header markiert wurden.

### <a name="send-security-message-api"></a>API zum Senden von Sicherheitsmeldungen

Die API zum **Senden von Sicherheitsmeldungen** ist derzeit in C, C#, Python, Node.js und Java verfügbar.

#### <a name="c-api"></a>C-API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C#-API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js-API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python-API

Wenn Sie die Python-API verwenden möchten, müssen Sie das Paket [azure-iot-device](https://pypi.org/project/azure-iot-device/) installieren.

Bei Verwendung der Python-API können Sie die Sicherheitsnachricht entweder über das Modul oder über das Gerät mithilfe der eindeutigen Geräte- oder Modulverbindungszeichenfolge senden. Wenn Sie das folgende Beispiel für ein Python-Skript ausführen, verwenden Sie bei einem Gerät **IoTHubDeviceClient** und bei einem Modul **IoTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java-API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen der [Übersicht](overview.md) über den Defender für IoT-Dienst
- Weitere Informationen zur Defender für IoT-[Architektur](architecture.md).
- Aktivieren Sie den [Dienst](quickstart-onboard-iot-hub.md).
- Lesen Sie die [häufig gestellten Fragen](resources-frequently-asked-questions.md).
- Informieren Sie sich, wie Sie auf [Sicherheitsrohdaten](how-to-security-data-access.md) zugreifen.
- Machen Sie sich mit [Empfehlungen](concept-recommendations.md) vertraut.
- Machen Sie sich mit [Warnungen](concept-security-alerts.md) vertraut.
