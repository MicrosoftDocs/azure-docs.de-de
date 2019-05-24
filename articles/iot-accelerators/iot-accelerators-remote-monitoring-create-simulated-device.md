---
title: Gerätesimulation mit IoT-Remoteüberwachung – Azure | Microsoft-Dokumentation
description: In dieser Anleitung wird gezeigt, wie Sie den Gerätesimulator mit dem Solution Accelerator für die Remoteüberwachung verwenden.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5044f8b85e59911633a4ffab509efc000948144a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832582"
---
# <a name="create-and-test-a-new-simulated-device"></a>Erstellen und Testen eines neuen simulierten Geräts

Mithilfe des Solution Accelerators für die Remoteüberwachung können Sie Ihre eigenen simulierten Geräte definieren. In diesem Artikel wird gezeigt, wie Sie ein neues simuliertes "Lighbulb"-Gerät (Leuchtmittel) definieren und anschließend lokal testen. Der Solution Accelerator enthält simulierte Geräte, z. B. Chillers (Kältemaschinen) und LKWs. Sie können jedoch Ihre eigenen simulierten Geräte definieren, um Ihre IoT-Lösungen zu testen, bevor Sie Echtgeräte bereitstellen.

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie simulierte Geräte verwenden, die im Gerätesimulationsdienst gehostet werden. Wenn Sie ein echtes Gerät erstellen möchten, finden Sie weitere Informationen unter [Herstellen einer Verbindung zwischen Ihrem Gerät und dem Solution Accelerator für die Remoteüberwachung](iot-accelerators-connecting-devices.md).

Diese Anleitung zeigt Ihnen, wie Sie den Microservice "Gerätesimulation" anpassen. Dieser Microservice ist ein Bestandteil des Solution Accelerators für die Remoteüberwachung. Zur Veranschaulichung der Gerätesimulationsfunktionen werden in dieser Anleitung zwei Szenarien in der Contoso IoT-Anwendung verwendet:

Im ersten Szenario fügen Sie dem vorhandenen Gerätetyp **Chiller** (Kältemaschine) von Contoso einen neuen Telemetrietyp hinzu.

Im zweiten Szenario möchte Contoso das neue intelligente Gerät "Lightbulb" (Leuchtmittel) testen. Zum Ausführen der Tests erstellen Sie ein neues simuliertes Gerät mit den folgenden Merkmalen:

*Properties*

| NAME                     | Werte                      |
| ------------------------ | --------------------------- |
| Farbe                    | White, Red, Blue            |
| Brightness               | 0 bis 100                    |
| Estimated remaining life | Countdown ab 10.000 Stunden |

*Telemetrie*

In der folgenden Tabelle sind die Daten aufgeführt, die das Leuchtmittel als Datenstrom an die Cloud sendet:

| NAME   | Werte      |
| ------ | ----------- |
| Status | „on“, „off“ |
| Temperatur | Grad F |
| online | true, false |

> [!NOTE]
> Der Telemetriewert **online** ist für alle simulierten Typen erforderlich.

*Methoden*

In der folgenden Tabelle sind die Aktionen aufgeführt, die das neue Gerät unterstützt:

| NAME        |
| ----------- |
| Switch on   |
| Switch off  |

*Anfangszustand*

In der folgenden Tabelle ist der Anfangszustand des Geräts aufgeführt:

| NAME                     | Werte |
| ------------------------ | -------|
| Initial color            | Weiß  |
| Initial brightness       | 75     |
| Initial remaining life   | 10.000 |
| Initial telemetry status | „on“   |
| Anfängliche Telemetrietemperatur | 200   |

Um die in dieser Anleitung aufgeführten Schritte ausführen zu können, benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen dieser Anleitung benötigen Sie Folgendes:

* Visual Studio Code. Sie können [Visual Studio Code für Mac, Linux und Windows herunterladen](https://code.visualstudio.com/download).
* .NET Core. Sie können [.NET Core für Mac, Linux und Windows](https://www.microsoft.com/net/download) herunterladen.
* [C# für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. Sie können [Postman für Mac, Windows oder Linux](https://www.getpostman.com/apps) herunterladen.
* Einen [IoT Hub, der für Ihr Azure-Abonnement bereitgestellt wurde](../../articles/iot-hub/iot-hub-create-through-portal.md). Sie benötigen die Verbindungszeichenfolge des IoT Hubs zum Ausführen der Schritte in dieser Anleitung. Sie können diese Verbindungszeichenfolge aus dem Azure-Portal abrufen.
* Eine Cosmos DB-Datenbank, die die SQL-API verwendet und für [starke Konsistenz](../../articles/cosmos-db/how-to-manage-database-account.md) konfiguriert wurde. Sie benötigen die Verbindungszeichenfolge der Cosmos DB-Datenbank zum Ausführen der Schritte in dieser Anleitung. Sie können diese Verbindungszeichenfolge aus dem Azure-Portal abrufen.

## <a name="prepare-your-development-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung

Führen Sie die folgenden Aufgaben aus, um Ihre Entwicklungsumgebung vorzubereiten:

* Laden Sie die Quelle für den Microservice "Gerätesimulation" herunter.
* Laden Sie die Quelle für den Microservice "Speicheradapter" herunter.
* Führen Sie den Microservice "Speicheradapter" lokal aus.

Bei den Anweisungen in diesem Artikel wird angenommen, dass Sie Windows verwenden. Wenn Sie ein anderes Betriebssystem verwenden, müssen Sie möglicherweise einige der Dateipfade und Befehle an Ihre Umgebung anpassen.

### <a name="download-the-microservices"></a>Herunterladen der Microservices

Laden Sie die [Microservices "Remoteüberwachung"](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) von GitHub an einen geeigneten Speicherort auf Ihrem lokalen Computer herunter, und entzippen Sie sie dort. Im Artikel wird vorausgesetzt, der Name des Ordners **remote-monitoring-services-dotnet-master** lautet.

Laden Sie den [Microservice "Gerätesimulation"](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) von GitHub an einen geeigneten Speicherort auf Ihrem lokalen Computer herunter, und entzippen Sie ihn dort. Im Artikel wird vorausgesetzt, der Name des Ordners **device-simulation-dotnet-master** lautet.

### <a name="run-the-storage-adapter-microservice"></a>Ausführen des Microservices "Speicheradapter"

Öffnen Sie den Ordner **remote-monitoring-services-dotnet-master\storage-adapter** in Visual Studio Code. Klicken Sie auf eine der **Wiederherstellen**-Schaltflächen, um nicht aufgelöste Abhängigkeiten zu beheben.

Öffnen Sie die Datei **storage-adapter/WebService/appsettings.ini**, und weisen Sie Ihre Cosmos DB-Verbindungszeichenfolge der Variablen **documentDBConnectionString** zu.

Um den Microservice lokal auszuführen, klicken Sie auf **Debuggen > Debuggen starten**.

Das Fenster **Terminal** in Visual Studio Code zeigt die Ausgabe aus dem laufenden Microservice, einschließlich einer URL für die Webdienst-Integritätsprüfung: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). Wenn Sie zu dieser Adresse navigieren, sollte der Status „OK: aktiv und gut“ lauten.

Lassen Sie den Microservice "Speicheradapter" weiterhin in dieser Instanz von Visual Studio Code ausführen, während Sie die nächsten Schritte erledigen.

## <a name="modify-the-chiller"></a>Ändern des Gerätetyps „Chiller“

In diesem Abschnitt fügen Sie dem vorhandenen Gerätetyp **Chiller**den neuen Telemetrietyp **Internal Temperature** hinzu:

1. Erstellen Sie auf Ihrem lokalen Computer den neuen Ordner **C:\temp\devicemodels**.

1. Kopieren Sie aus der heruntergeladenen Kopie des Microservices "Gerätesimulation" die folgenden Dateien in Ihren neuen Ordner:

    | `Source` | Ziel |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Öffnen Sie die Datei **C:\temp\devicemodels\chiller-01.json**.

1. Fügen Sie im Abschnitt **InitialState** die folgenden beiden Definitionen hinzu:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Fügen Sie im **Telemetry**-Array die folgende Definition hinzu:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Speichern Sie die Datei **C:\temp\devicemodels\chiller-01.json**.

1. Öffnen Sie die Datei **C:\temp\devicemodels\scripts\chiller-01-state.js**.

1. Fügen Sie der **state**-Variable die folgenden Felder hinzu:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Aktualisieren Sie die Funktion **main** wie folgt:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Speichern Sie die Datei **C:\temp\devicemodels\scripts\chiller-01-state.js**.

## <a name="create-the-lightbulb"></a>Erstellen des Gerätetyps "Lightbulb"

In diesem Abschnitt definieren Sie den neuen Gerätetyp **Lightbulb** (Leuchtmittel):

1. Erstellen Sie die Datei **C:\temp\devicemodels\lightbulb-01.json**, und fügen Sie ihr den folgenden Inhalt hinzu:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Speichern Sie die Änderungen in **C:\temp\devicemodels\lightbulb-01.json**.

1. Erstellen Sie die Datei **C:\temp\devicemodels\scripts\lightbulb-01-state.js**, und fügen Sie ihr den folgenden Inhalt hinzu:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Speichern Sie die Änderungen in **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Erstellen Sie die Datei **C:\temp\devicemodels\scripts\SwitchOn-method.js**, und fügen Sie ihr den folgenden Inhalt hinzu:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Speichern Sie die Änderungen in **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Erstellen Sie die Datei **C:\temp\devicemodels\scripts\SwitchOff-method.js**, und fügen Sie ihr den folgenden Inhalt hinzu:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Speichern Sie die Änderungen in **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Sie haben nun eine angepasste Version des Gerätetyps **Chiller** und einen neuen Gerätetyp **Lightbulb** erstellt.

## <a name="test-the-devices"></a>Testen der Geräte

In diesem Abschnitt testen Sie die in den vorherigen Abschnitten erstellten Gerätetypen lokal.

### <a name="run-the-device-simulation-microservice"></a>Ausführen des Microservices „Gerätesimulation“

Öffnen Sie den von GitHub heruntergeladenen Ordner **device-simulation-dotnet-master** in einer neuen Instanz von Visual Studio Code. Klicken Sie auf eine der **Wiederherstellen**-Schaltflächen, um nicht aufgelöste Abhängigkeiten zu beheben.

Öffnen Sie die Datei **WebService/appsettings.ini**, und weisen Sie Ihre Cosmos DB-Verbindungszeichenfolge der Variablen **documentdb_connstring** zu. Ändern Sie außerdem die Einstellungen wie folgt:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Um den Microservice lokal auszuführen, klicken Sie auf **Debuggen > Debuggen starten**.

Das Fenster **Terminal** in Visual Studio Code zeigt die Ausgabe aus dem laufenden Microservice.

Lassen Sie den Microservice "Gerätesimulation" weiterhin in dieser Instanz von Visual Studio Code ausführen, während Sie die nächsten Schritte erledigen.

### <a name="set-up-a-monitor-for-device-events"></a>Einrichten einer Überwachung für Geräteereignisse

In diesem Abschnitt verwenden Sie die Azure-Befehlszeilenschnittstelle, um eine Ereignisüberwachung zum Anzeigen der Telemetriedaten einzurichten, die von den mit Ihrem IoT Hub verbundenen Geräten gesendet wurden.

Bei dem nachstehenden Skript wird angenommen, dass der Name Ihres Hubs **device-simulation-test** lautet.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lassen Sie die Ereignisüberwachung weiterhin ausführen, während Sie die simulierten Geräte testen.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Erstellen einer Simulation mit dem aktualisierten Gerätetyp „Chiller“

In diesem Abschnitt verwenden Sie das Tool Postman, um den Microservice "Gerätesimulation" zum Ausführen einer Simulation mit dem aktualisierten Gerätetyp "Chiller" anzufordern. Bei Postman handelt es sich um ein Tool, mit dem Sie REST-Anforderungen an einen Webdienst senden können. Die benötigten Postman-Konfigurationsdateien sind in Ihrer lokalen Kopie des Repositorys **device-simulation-dotnet** gespeichert.

So richten Sie Postman ein:

1. Öffnen Sie Postman auf Ihrem lokalen Computer.

1. Klicken Sie auf **Datei > Import**. Klicken Sie dann auf **Dateien auswählen**.

1. Navigieren Sie zum Ordner **device-simulation-dotnet-master/docs/postman**. Wählen Sie **Azure IoT Device Simulation solution accelerator.postman_collection** und **Azure IoT Device Simulation solution accelerator.postman_environment** aus, und klicken Sie auf **Öffnen**.

1. Erweitern Sie den **Solution Accelerator für Azure IoT-Gerätesimulation** auf die Anforderungen, die Sie senden können.

1. Klicken Sie auf **Keine Umgebung**, und wählen Sie **Solution Accelerator für Azure IoT-Gerätesimulation** aus.

Nun haben Sie eine Sammlung und eine Umgebung in Ihren Postman-Arbeitsbereich geladen, die Sie zum Interagieren mit dem Microservice "Gerätesimulation" verwenden können.

So können Sie die Simulation konfigurieren und ausführen:

1. Wählen Sie in der Postman-Sammlung **Geänderte Chiller-Simulation erstellen** aus, und klicken Sie auf **Senden**. Diese Anforderung erstellt vier Instanzen des simulierten Gerätetyps „Chiller“.

1. Die Ausgabe der Ereignisüberwachung im „Azure CLI“-Fenster zeigt die Telemetriedaten aus den simulierten Geräten, einschließlich der neuen **internal_temperature**-Werte.

Um die Simulation zu beenden, wählen Sie in Postman die Anforderung **Simulation beenden** aus, und klicken Sie auf **Senden**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Erstellen einer Simulation mit dem Gerätetyp „Lightbulb“

In diesem Abschnitt verwenden Sie das Tool Postman, um den Microservice "Gerätesimulation" zum Ausführen einer Simulation mit dem Gerätetyp "Lightbulb" anzufordern. Bei Postman handelt es sich um ein Tool, mit dem Sie REST-Anforderungen an einen Webdienst senden können.

So können Sie die Simulation konfigurieren und ausführen:

1. Wählen Sie in der Postman-Sammlung **Lightbulb-Simulation erstellen** aus, und klicken Sie auf **Senden**. Diese Anforderung erstellt zwei Instanzen des simulierten Gerätetyps „Lightbulb“.

1. Die Ausgabe der Ereignisüberwachung im „Azure CLI“-Fenster zeigt die Telemetriedaten aus dem simulierten Gerätetyp "Lightbulb".

Um die Simulation zu beenden, wählen Sie in Postman die Anforderung **Simulation beenden** aus, und klicken Sie auf **Senden**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die beiden lokal ausgeführten Microservices in ihren Instanzen von Visual Studio Code beenden (**Debuggen > Debuggen beenden**).

Wenn Sie die Instanzen von IoT Hub und Cosmos DB nicht mehr benötigen, löschen Sie sie aus Ihrem Azure-Abonnement, um unnötige Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung wurde gezeigt, wie Sie benutzerdefinierte simulierte Gerätetypen erstellen und testen, indem Sie den Microservice "Gerätesimulation" lokal ausführen.

Im nächsten empfohlenen Schritt erfahren Sie, wie Sie Ihre benutzerdefinierten simulierten Gerätetypen für den [Solution Accelerator für die Remoteüberwachung](iot-accelerators-remote-monitoring-deploy-simulated-device.md) bereitstellen.
