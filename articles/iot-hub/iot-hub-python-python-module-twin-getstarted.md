---
title: Erste Schritte mit der Modulidentität und dem Modulzwilling von Azure IoT Hub (Python) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit IoT-SDKs für Python eine Modulidentität erstellen und den Modulzwilling aktualisieren.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: f887fbd4f82e59c02d6a5b69d0d5b43b426a39bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61441208"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub unter Verwendung eines Python-Back-Ends und eines Python-Geräts

> [!NOTE]
> [Modulidentitäten und Modulzwillinge](iot-hub-devguide-module-twins.md) ähneln den Geräteidentitäten und Gerätezwillingen von Azure IoT Hub, ermöglichen jedoch eine feinere Granularität. Azure IoT Hub-Geräteidentitäten und -Gerätezwillinge ermöglichen der Back-End-Anwendung die Konfiguration eines Geräts und geben Aufschluss über den Gerätezustand. Modulidentitäten und Modulzwillinge bieten diese Funktionalität hingegen für einzelne Komponenten eines Geräts. Auf Geräten mit mehreren Komponenten (etwa auf betriebssystembasierten Geräten oder Firmwaregeräten) ermöglichen sie isolierte Konfigurationen und Zustände für die einzelnen Komponenten.
>

Am Ende dieses Tutorials verfügen Sie über zwei Python-Apps:

* **CreateIdentities**: Hiermit werden eine Geräteidentität, eine Modulidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden Ihrer Geräte- und Modulclients erstellt.

* **UpdateModuleTwinReportedProperties**: Hiermit werden aktualisierte vom Modulzwilling gemeldete Eigenschaften an Ihren IoT Hub sendet.

> [!NOTE]
> Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Anwendungen für Geräte als auch das zugehörige Lösungs-Back-End erstellen können, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).
>

Für dieses Tutorial benötigen Sie Folgendes:

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

* Einen IoT Hub.

* Installieren Sie das neueste [Python SDK](https://github.com/Azure/azure-iot-sdk-python).

Sie haben nun Ihren IoT-Hub erstellt und verfügen über den Hostnamen und die IoT Hub-Verbindungszeichenfolge, die Sie für die weiteren Schritte in diesem Tutorial benötigen.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Erstellen einer Geräteidentität und einer Modulidentität in IoT Hub

In diesem Abschnitt erstellen Sie eine Python-App, mit der eine Geräte- und eine Modulidentität in der Identitätsregistrierung Ihrer IoT Hub-Identität erstellt werden. Ein Gerät oder Modul kann nur eine Verbindung mit IoT Hub herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im Abschnitt zur Identitätsregistrierung des [IoT Hub-Entwicklerhandbuchs](iot-hub-devguide-identity-registry.md). Wenn Sie diese Konsolen-App ausführen, generiert sie eine eindeutige ID und einen eindeutigen Schlüssel für das Gerät und das Modul. Ihr Gerät und Ihr Modul verwenden diese Werte, um sich beim Senden von D2C-Nachrichten an IoT Hub zu identifizieren. Bei den IDs gilt Groß-/Kleinschreibung.

Fügen Sie Ihrer Python-Datei den folgenden Code hinzu:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Diese App erstellt eine Geräteidentität mit der ID **myFirstDevice** und ein Identitätsmodul mit der ID **myFirstModule** unter dem Gerät **myFirstDevice**. (Falls diese Modul-ID in der Identitätsregistrierung bereits vorhanden ist, werden mit dem Code lediglich die vorhandenen Modulinformationen abgerufen.) Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel in der simulierten Modul-App, um eine Verbindung mit Ihrem IoT Hub herzustellen.

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräte- und Modulidentitäten, um einen sicheren Zugriff auf IoT Hub zu ermöglichen. In der Identitätsregistrierung werden Geräte-IDs und -schlüssel für die Verwendung als Sicherheitsanmeldeinformationen gespeichert. Darüber hinaus wird in der Identitätsregistrierung ein Flag für den Aktivierungszustand des jeweiligen Geräts gespeichert, mit dem Sie den Zugriff für das betreffende Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Es gibt keinen Flag „Aktiviert/deaktiviert“ für Modulidentitäten. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>Aktualisieren des Modulzwillings mithilfe des Python-Geräte-SDKs

In diesem Abschnitt erstellen Sie auf Ihrem simulierten Gerät eine Python-App, die die vom Modulzwilling gemeldeten Eigenschaften aktualisiert.

1. **Abrufen der Modulverbindungszeichenfolge**: Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Navigieren Sie zu Ihrer IoT Hub-Instanz, und klicken Sie auf „IoT-Geräte“. Suchen Sie nach „myFirstDevice“, und öffnen Sie den Eintrag. Sie sehen, dass „myFirstModule“ erfolgreich erstellt wurde. Kopieren Sie die Modulverbindungszeichenfolge. Sie wird im nächsten Schritt benötigt.

   ![Moduldetails im Azure-Portal](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **Erstellen der App „UpdateModuleTwinReportedProperties“**

   Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

In diesem Codebeispiel wird gezeigt, wie Sie den Modulzwilling abrufen und die gemeldeten Eigenschaften mit dem AMQP-Protokoll aktualisieren.

## <a name="get-updates-on-the-device-side"></a>Abrufen von Updates auf Geräteseite

Neben dem oben aufgeführten Code können Sie den nachstehenden Codeblock hinzufügen, um die Updatenachricht des Gerätezwillings auf Ihrem Gerät abzurufen.

```python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den weiteren ersten Schritten mit IoT Hub und zum Kennenlernen anderer IoT-Szenarien finden Sie in den folgenden Artikeln:

* [Erste Schritte mit der Geräteverwaltung](iot-hub-node-node-device-management-get-started.md)

* [Erste Schritte mit IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)