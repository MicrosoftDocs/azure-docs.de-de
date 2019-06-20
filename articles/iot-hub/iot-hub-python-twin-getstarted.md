---
title: Erste Schritte mit Azure IoT Hub-Gerätezwillingen (Python) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure IoT Hub-Gerätezwillingen Tags hinzufügen und eine IoT Hub-Abfrage verwenden. Sie verwenden die Azure IoT SDKs für Python, um die simulierte Geräte-App und eine Dienst-App zu implementieren, die die Tags hinzufügt und die IoT Hub-Abfrage ausführt.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 3e703c999d57cf62064291cf91059a17a959a2c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569251"
---
# <a name="get-started-with-device-twins-python"></a>Erste Schritte mit Gerätezwillingen (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Am Ende dieses Tutorials verfügen Sie über zwei Python-Konsolen-Apps:

* **AddTagsAndQuery.py**, eine Python-Back-End-App, die Tags hinzufügt und Gerätezwillinge abfragt.

* **ReportConnectivity.py** ist eine Python-Geräte-App, die ein Gerät simuliert, das eine Verbindung mit Ihrer IoT Hub-Instanz mit der zuvor erstellten Geräteidentität herstellt und seine Verbindungsbedingung meldet.

> [!NOTE]
> Im Artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) finden Sie Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Geräte- als auch Back-End-Apps erstellen können.

Für dieses Tutorial benötigen Sie Folgendes:

* [Python 2.x oder 3.x](https://www.python.org/downloads/). Stellen Sie je nach Einrichtung sicher, dass die 32-Bit- bzw. die 64-Bit-Installation verwendet wird. Fügen Sie Python Ihrer plattformspezifischen Umgebungsvariablen hinzu, wenn Sie während der Installation dazu aufgefordert werden. Bei Verwendung von Python 2.x müssen Sie ggf. [*pip*, das Python-Paketverwaltungssystem, installieren oder upgraden](https://pip.pypa.io/en/stable/installing/).

* Bei Verwendung des Windows-Betriebssystems wird das [Visual C++ Redistributable Package](https://www.microsoft.com/download/confirmation.aspx?id=48145) verwendet, um die Verwendung nativer DLLs aus Python zu ermöglichen.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

> [!NOTE]
> Die *pip*-Pakete für `azure-iothub-service-client` und `azure-iothub-device-client` sind derzeit nur für das Windows-Betriebssystem verfügbar. Pakete für Linux/Mac OS finden Sie in den Abschnitten zu Linux und Mac OS im Beitrag [Prepare your development environment for Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) (Vorbereiten der Entwicklungsumgebung für Python).
>

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Erstellen der Dienst-App

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App, mit der dem Gerätezwilling, der Ihrer **{Geräte-ID}** zugeordnet ist, Standortmetadaten hinzugefügt werden. Anschließend werden die Gerätezwillinge abgefragt, die in dem für die Auswahl der Geräte in Redmond zuständigen IoT Hub gespeichert sind, und dann diejenigen, die eine Mobilfunkverbindung melden.

1. Öffnen Sie eine Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Service SDK für Python**. Schließen Sie die Eingabeaufforderung, nachdem Sie das SDK installiert haben.

   ```
   pip install azure-iothub-service-client
   ```

2. Erstellen Sie in einem Text-Editor eine neue Datei namens **AddTagsAndQuery.py**.

3. Fügen Sie den folgenden Code hinzu, um die erforderlichen Module aus dem Dienst-SDK zu importieren:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Fügen Sie den folgenden Code hinzu, und ersetzen Sie dabei den Platzhalter für `[IoTHub Connection String]` und `[Device Id]` durch die Verbindungszeichenfolge für den IoT Hub und die Geräte-ID, die Sie in den vorherigen Abschnitten erstellt haben.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Fügen Sie den folgenden Code der Datei **AddTagsAndQuery.py** hinzu:

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    Das **Registry**-Objekt macht alle Methoden verfügbar, die für die Interaktion mit Gerätezwillingen des Diensts erforderlich sind. Der Code initialisiert zuerst das **Registrierungs**-Objekt, aktualisiert dann den Gerätezwilling für **deviceId** und führt schließlich zwei Abfragen aus. Mit der ersten werden nur die Gerätezwillinge von Geräten in der Anlage **Redmond43** ausgewählt. Mit der zweiten wird die Abfrage so angepasst, dass nur die Geräte ausgewählt werden, die über ein Mobilfunknetz verbunden sind.

6. Fügen Sie am Ende der Datei **AddTagsAndQuery.py** den folgenden Code ein, um die Funktion **iothub_service_sample_run** zu implementieren:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Führen Sie die Anwendung mit folgendem Befehl aus:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    In den Ergebnissen für die Abfrage, mit der alle Geräte in der Anlage **Redmond43** abgefragt werden, sollte ein Gerät angezeigt werden und keines für die Abfrage, mit der die Ergebnisse auf die über ein Mobilfunknetz verbundenen Geräte beschränkt werden.

    ![erste Abfrage zeigt alle Geräte in Redmond](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

Im nächsten Abschnitt erstellen Sie eine Geräte-App, mit der die Verbindungsinformationen gemeldet und das Ergebnis der im vorherigen Abschnitt ausgeführten Abfrage geändert wird.

## <a name="create-the-device-app"></a>Erstellen der Geräte-App

In diesem Abschnitt erstellen Sie eine Python-Konsolenanwendung, die als Ihre **{Geräte-ID}** eine Verbindung mit dem Hub herstellt und dann die gemeldeten Eigenschaften des zugehörigen Gerätezwillings so aktualisiert, dass sie die Informationen über die Verbindung mit einem Mobilfunknetz enthalten.

1. Öffnen Sie eine Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Service SDK für Python**. Schließen Sie die Eingabeaufforderung, nachdem Sie das SDK installiert haben.

    ```
    pip install azure-iothub-device-client
    ```

2. Erstellen Sie in einem Text-Editor eine neue Datei namens **ReportConnectivity.py**.

3. Fügen Sie den folgenden Code hinzu, um die erforderlichen Module aus dem Dienst-SDK zu importieren:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Fügen Sie den folgenden Code hinzu, und ersetzen Sie dabei den Platzhalter für `[IoTHub Device Connection String]` durch die Verbindungszeichenfolge für das in den vorherigen Abschnitten erstellte IoT Hub-Gerät.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Fügen Sie folgenden Code der Datei **ReportConnectivity.py** hinzu, um die Gerätezwillingsfunktionalität zu implementieren:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    Das **Client**-Objekt macht alle Methoden verfügbar, die für die Interaktion mit Gerätezwillingen des Geräts erforderlich sind. Mit diesem Code werden nach dem Initialisieren des **Client**-Objekts der Gerätezwilling für Ihr Gerät abgerufen und die zugehörige gemeldete Eigenschaft mit den Verbindungsinformationen aktualisiert.

6. Fügen Sie am Ende der Datei **ReportConnectivity.py** den folgenden Code ein, um die Funktion **iothub_client_sample_run** zu implementieren:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Führen Sie die Geräte-App aus:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Es sollte die Bestätigung angezeigt werden, dass die Gerätezwillinge aktualisiert wurden.

    ![Zwillinge aktualisieren](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Da das Gerät nun die Verbindungsinformationen gemeldet hat, sollten diese in beiden Abfragen angezeigt werden. Gehen Sie zurück, und führen Sie die Abfragen erneut aus:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Nun sollte Ihre **{Geräte-ID}** in beiden Abfrageergebnisse angezeigt werden.

    ![zweite Abfrage](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie im Azure-Portal einen neuen IoT-Hub konfiguriert und anschließend in der Identitätsregistrierung des IoT-Hubs eine Geräteidentität erstellt. Sie haben Gerätemetadaten aus einer Back-End-App als Tags hinzugefügt und eine simulierte Geräte-App zum Melden von Geräteverbindungsinformationen im Gerätezwilling geschrieben. Sie haben zudem erfahren, wie diese Informationen mithilfe der Registrierung abgefragt werden können.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* Senden von Telemetriedaten von Geräten im Tutorial [Erste Schritte mit IoT Hub](quickstart-send-telemetry-python.md).

* Konfigurieren von Geräten mit den gewünschten Eigenschaften des Gerätezwillings im Tutorial [Konfigurieren Ihrer Geräte über einen Back-End-Dienst](tutorial-device-twins.md).

* Interaktives Steuern von Geräten (z.B. Einschalten eines Lüfters über eine benutzergesteuerte App): [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Node.js)](quickstart-control-device-python.md).