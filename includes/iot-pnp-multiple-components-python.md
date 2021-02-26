---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5e0fa474412815a1ce830267ac2f09830a6353d3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834163"
---
In diesem Tutorial erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung mit Komponenten erstellen, mit Ihrem IoT-Hub verbinden und die an den Hub gesendeten Informationen mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung wurde in Python geschrieben und ist im Azure IoT-Geräte-SDK für Python enthalten. Ein Lösungsentwickler kann mithilfe von Azure IoT-Explorer mehr über die Funktionen eines IoT Plug & Play-Geräts erfahren, ohne Gerätecode anzeigen zu müssen.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Laden Sie den Beispielcode herunter.
> * Führen Sie die Beispielgeräteanwendung aus, und überprüfen Sie, ob sie eine Verbindung mit Ihrem IoT-Hub herstellt.
> * Überprüfen Sie den Quellcode.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Für dieses Tutorial muss sich Python 3.7 auf Ihrem Entwicklungscomputer befinden. Die neueste empfohlene Version kann für mehrere Plattformen von [python.org](https://www.python.org/) heruntergeladen werden. Sie können Ihre Python-Version mit dem folgenden Befehl überprüfen:  

```cmd/sh
python --version
```

Die neueste empfohlene Version kann für mehrere Plattformen von [python.org](https://www.python.org/) heruntergeladen werden.

## <a name="download-the-code"></a>Laden Sie den Code herunter.

Das **azure-iot-device**-Paket wird als PIP veröffentlicht.

Installieren Sie das Paket wie folgt in Ihrer lokalen Python-Umgebung:

```cmd/sh
pip install azure-iot-device
```

Wenn Sie [Schnellstart: Verbinden einer unter Windows ausgeführten IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub (Python)](../articles/iot-pnp/quickstart-connect-device.md) abgeschlossen haben, wurde das Repository bereits geklont.

So klonen Sie das Python SDK IoT-Repository:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>Überprüfen des Codes

In diesem Beispiel wird ein IoT Plug & Play-Temperaturregler implementiert. Das in diesem Beispiel implementierte Modell umfasst [mehrere Komponenten](../articles/iot-pnp/concepts-components.md). In der [DTDL-Modelldatei (Digital Twins Definition Language) für das Temperaturgerät](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) werden die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle definiert.

Der Ordner *azure-iot-sdk-python\azure-iot-device\samples\pnp* enthält den Beispielcode für das IoT Plug & Play-Gerät. Das Beispiel mit dem Temperaturregler umfasst folgende Dateien:

- temp_controller_with_thermostats.py
- pnp_helper.py

Der Temperaturregler hat mehrere Komponenten und eine Standardkomponente, die auf dem DTDL-Modell des Temperaturreglers basiert.

Öffnen Sie die Datei *temp_controller_with_thermostats.py* in einem Editor Ihrer Wahl. Der Code in dieser Datei bewirkt Folgendes:

1. Er importiert `pnp_helper.py`, um Zugriff auf Hilfsmethoden zu erhalten.

1. Er definiert zwei Modellbezeichner für digitale Zwillinge (Digital Twin Model Identifiers, DTMIs), um zwei Schnittstellen, die im DTDL-Modell definiert sind, eindeutig darzustellen. Von den Komponenten in einem echten Temperaturregler müssen die beiden folgenden Schnittstellen implementiert werden. Die beiden Schnittstellen sind bereits in einem zentralen Repository veröffentlicht. Die DTMIs müssen dem Benutzer bekannt sein und unterscheiden sich je nach Geräteimplementierungsszenario. Im vorliegenden Beispiel stellen die beiden Schnittstellen Folgendes dar:

    - Einen Thermostat
    - Von Azure entwickelte Geräteinformationen

1. Er definiert die DTMI-Modell-ID (`model_id`) für das zu implementierende Gerät. Der DTMI ist benutzerdefiniert und muss dem DTMI in der DTDL-Modelldatei entsprechen.

1. Er definiert die Namen, die für die Komponenten in der DTDL-Datei vergeben wurden. Die DTDL enthält zwei Thermostate und eine Geräteinformationskomponente. In der Standardkomponente wird außerdem die Konstante `serial_number` definiert. Die Seriennummer (`serial_number`) eines Geräts ist unveränderlich.

1. Er definiert Befehlshandlerimplementierungen. Dadurch wird definiert, wie das Gerät auf eingehende Befehlsanforderungen reagiert.

1. Er definiert Funktionen zum Erstellen einer Befehlsantwort. Dadurch wird definiert, wie das Gerät auf Befehlsanforderungen antwortet. Sie erstellen Befehlsantwortfunktionen, wenn von einem Befehl eine benutzerdefinierte Antwort an den IoT-Hub zurückgesendet werden muss. Wurde für einen Befehl keine Antwortfunktion bereitgestellt, wird eine generische Antwort gesendet. In diesem Beispiel ist nur für den Befehl **getMaxMinReport** eine benutzerdefinierte Antwort vorhanden.

1. Er definiert eine Funktion, um Telemetriedaten von diesem Gerät zu senden. Telemetriedaten werden von den beiden Thermostaten und der Standardkomponente gesendet. Von dieser Funktion wird ein optionaler Komponentennamenparameter akzeptiert, um die Komponente zu identifizieren, von der die Telemetriedaten gesendet wurden.

1. Er definiert einen Listener für Befehlsanforderungen.

1. Er definiert einen Listener für die Aktualisierung gewünschter Eigenschaften.

1. Er enthält eine Funktion vom Typ `main`, für die Folgendes gilt:

    - Sie verwendet das Geräte-SDK, um einen Geräteclient zu erstellen und eine Verbindung mit Ihrem IoT-Hub herzustellen. Vom Gerät wird die Modell-ID (`model_id`) gesendet, damit es vom IoT-Hub als IoT Plug & Play-Gerät identifiziert werden kann.

    - Sie verwendet die Funktion `create_reported_properties` in der Hilfsdatei, um die Eigenschaften zu erstellen. Übergeben Sie den Komponentennamen und die Eigenschaften als Schlüssel-Wert-Paare an diese Funktion.

    - Sie aktualisiert die lesbaren Eigenschaften für die zugehörigen Komponenten durch Aufrufen von `patch_twin_reported_properties`.

    - Sie beginnt mit dem Lauschen auf Befehlsanforderungen unter Verwendung der Funktion `execute_command_listener`. Von der Funktion wird ein Listener eingerichtet, um auf Anforderungen des Diensts zu lauschen. Beim Einrichten des Listeners wird Folgendes als Parameter angegeben: `method_name`, `user_command_handler` und `create_user_response_handler`.
        - `method_name` dient zum Definieren der Befehlsanforderung. In diesem Beispiel werden durch das Modell die Befehle **reboot** und **getMaxMinReport** definiert.
        - Die Funktion `user_command_handler` definiert, was das Gerät tun soll, wenn es einen Befehl empfängt.
        - Die Funktion `create_user_response_handler` erstellt eine Antwort, die an Ihren IoT-Hub gesendet wird, wenn ein Befehl erfolgreich ausgeführt wird. Sie können diese Antwort im Portal anzeigen. Ohne Angabe dieser Funktion wird eine generische Antwort an den Dienst gesendet.

    - Sie verwendet `execute_property_listener`, um auf Eigenschaftsaktualisierungen zu lauschen.

    - Sie beginnt mit dem Senden von Telemetriedaten unter Verwendung von `send_telemetry`. Im Beispielcode wird eine Schleife verwendet, um drei Funktionen zum Senden von Telemetriedaten aufzurufen. Sie werden jeweils alle acht Sekunden aufgerufen.

    - Sie deaktiviert alle Listener und Aufgaben und beendet die Schleife, wenn Sie **Q** oder **q** drücken.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

Verwenden Sie den folgenden Befehl zum Ausführen des Beispiels:

```cmd/sh
python temp_controller_with_thermostats.py
```

Das Beispielgerät sendet im Abstand von wenigen Sekunden Telemetrienachrichten an Ihren IoT-Hub.

Die Ausgabe sieht wie folgt aus und gibt an, dass das Gerät Telemetriedaten an den Hub sendet und nun zum Empfangen von Befehlen und Eigenschaftenaktualisierungen bereit ist:

![Bestätigungsmeldungen des Geräts](media/iot-pnp-multiple-components-python/multiple-component.png)

Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
