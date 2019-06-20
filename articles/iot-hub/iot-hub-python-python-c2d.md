---
title: C2D-Nachrichten mit Azure IoT Hub (Python) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Python C2D-Nachrichten von einem Azure IoT Hub an ein Gerät senden. Sie passen eine simulierte Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 7ac668bdbc3698be3ed2aa50a428cef84e68369a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61441410"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Senden von C2D-Nachrichten mit IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Einführung
Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Im Schnellstart [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md) erfahren Sie, wie ein IoT-Hub erstellt, eine Geräteidentität im Hub bereitgestellt und eine simulierte Geräte-App programmiert wird, die D2C-Nachrichten (Device to Cloud, Gerät zu Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md)auf. Es beschreibt Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.

* Empfangen von C2D-Nachrichten auf einem Gerät.

* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei Python-Konsolen-Apps aus:

* **SimulatedDevice.py**, eine abgewandelte Version der in [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md) erstellten App, die eine Verbindung mit IoT Hub herstellt und C2D-Nachrichten (Cloud-zu-Gerät) empfängt.

* **SendCloudToDeviceMessage.py**, die über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App sendet und dann die zugehörige Übermittlungsbestätigung empfängt.

> [!NOTE]
> IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, Java und JavaScript). Im [Azure IoT Developer Center](https://www.azure.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.
>

Für dieses Tutorial benötigen Sie Folgendes:

* [Python 2.x oder 3.x](https://www.python.org/downloads/). Stellen Sie je nach Einrichtung sicher, dass die 32-Bit- bzw. die 64-Bit-Installation verwendet wird. Fügen Sie Python Ihrer plattformspezifischen Umgebungsvariablen hinzu, wenn Sie während der Installation dazu aufgefordert werden. Bei Verwendung von Python 2.x müssen Sie ggf. [*pip*, das Python-Paketverwaltungssystem, installieren oder upgraden](https://pip.pypa.io/en/stable/installing/).

* Bei Verwendung des Windows-Betriebssystems wird das [Visual C++ Redistributable Package](https://www.microsoft.com/download/confirmation.aspx?id=48145) verwendet, um die Verwendung nativer DLLs aus Python zu ermöglichen.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

> [!NOTE]
> Die *pip*-Pakete für `azure-iothub-service-client` und `azure-iothub-device-client` sind derzeit nur für das Windows-Betriebssystem verfügbar. Pakete für Linux/Mac OS finden Sie in den Abschnitten zu Linux und Mac OS im Beitrag [Prepare your development environment for Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) (Vorbereiten der Entwicklungsumgebung für Python).
>

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App zum Simulieren des Geräts und empfangen C2D-Nachrichten aus dem IoT Hub.

1. Erstellen Sie in einem Text-Editor eine Datei namens **SimulatedDevice.py**.

2. Fügen Sie am Anfang der Datei **SimulatedDevice.py** die folgenden `import`-Anweisungen und -Variablen hinzu:

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. Fügen Sie der Datei **SimulatedDevice.py** dann folgenden Code hinzu. Ersetzen Sie den Platzhalterwert "{deviceConnectionString}" durch die Verbindungszeichenfolge für das Gerät, das Sie im Schnellstart [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md) erstellt haben:

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Fügen Sie die folgende Funktion hinzu, um empfangene Nachrichten an der Konsole auszugeben:

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. Fügen Sie den folgenden Code hinzu, um den Client zu initialisieren und auf den Empfang der C2D-Nachricht zu warten:

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. Fügen Sie die folgende main-Funktion hinzu:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Speichern und schließen Sie die Datei **SimulatedDevice.py**.

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie im Schnellstart [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md) hinzugefügt haben. Sie benötigen auch die IoT Hub-Verbindungszeichenfolge für den Hub, die Sie im [Azure-Portal](https://portal.azure.com) finden.

1. Erstellen Sie in einem Text-Editor eine Datei namens **SendCloudToDeviceMessage.py**.

2. Fügen Sie am Anfang der Datei **SendCloudToDeviceMessage.py** die folgenden `import`-Anweisungen und -Variablen hinzu:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Fügen Sie der Datei **SendCloudToDeviceMessage.py** dann folgenden Code hinzu. Ersetzen Sie den Platzhalterwert "{IoTHubConnectionString}" durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im Schnellstart [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md) erstellt haben. Ersetzen Sie den Platzhalter "{deviceId}" durch die Geräte-ID des Geräts, das Sie im Schnellstart [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md) hinzugefügt haben:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Fügen Sie die folgende Funktion hinzu, um Feedbacknachrichten an der Konsole auszugeben:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Fügen Sie den folgenden Code hinzu, um eine Nachricht an Ihr Gerät zu senden und die Feedbacknachricht zu verarbeiten, wenn das Gerät die C2D-Nachricht bestätigt:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Fügen Sie die folgende main-Funktion hinzu:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Speichern und schließen Sie die Datei **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Öffnen Sie eine Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Device SDK für Python**.

    ```
    pip install azure-iothub-device-client
    ```

2. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um auf C2D-Nachrichten zu lauschen:

    ```shell
    python SimulatedDevice.py
    ```

    ![Ausführen der simulierten Geräte-App](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Öffnen Sie eine neue Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Service SDK für Python**.

    ```
    pip install azure-iothub-service-client
    ```

4. Führen Sie an einer Eingabeaufforderung den folgenden Befehl aus, um eine C2D-Nachricht zu senden und auf das Nachrichtenfeedback zu warten:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Ausführen der App zum Senden des C2D-Befehls](./media/iot-hub-python-python-c2d/send-command.png)

5. Beachten Sie die vom Gerät empfangene Nachricht.

    ![Empfangene Nachricht](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden.

Beispiele vollständiger Lösungen, die IoT Hub nutzen, finden Sie unter [Azure IoT-Solution Accelerator für die Remoteüberwachung](https://azure.microsoft.com/documentation/suites/iot-suite/).

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).
