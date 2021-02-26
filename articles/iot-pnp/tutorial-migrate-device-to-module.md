---
title: 'Tutorial: Verbinden eines generischen Azure IoT Plug & Play-Moduls | Microsoft-Dokumentation'
description: 'Tutorial: Verwenden von C#-Beispielcode für ein IoT Plug & Play-Gerät in einem generischen Modul'
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831202"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Tutorial: Verbinden eines IoT Plug & Play-Moduls (C#)

In diesem Tutorial wird veranschaulicht, wie Sie eine Verbindung für ein generisches IoT Plug & Play-[Modul](../iot-hub/iot-hub-devguide-module-twins.md) herstellen.

Bei einem Gerät handelt es sich um ein IoT Plug & Play-Gerät, wenn es bei der Verbindungsherstellung mit einem IoT-Hub seine Modell-ID veröffentlicht und die Eigenschaften und Methoden implementiert, die im DTDL-Modell (Digital Twins Definition Language) beschrieben sind. Die Identifizierung erfolgt hierbei über die Modell-ID. Weitere Informationen dazu, wie Geräte die DTDL und eine Modell-ID nutzen, finden Sie im [Entwicklerhandbuch für IoT Plug & Play](./concepts-developer-guide-device.md). Für Module werden Modell-IDs und DTDL-Modelle auf die gleiche Weise verwendet.

In diesem Tutorial wird Folgendes gezeigt, um die Implementierung eines IoT Plug & Play-Moduls zu veranschaulichen:

> [!div class="checklist"]
> * Hinzufügen eines Geräts mit einem Modul zu Ihrem IoT-Hub
> * Konvertieren des C#-Gerätebeispiels mit einem Thermostat in ein generisches Modul
> * Interagieren mit dem Modul über das Dienst-SDK

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um dieses Tutorial unter Windows ausführen zu können:

* [Visual Studio (Community, Professional oder Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Fügen Sie Ihrem IoT-Hub mit dem Azure IoT-Explorer ein neues Gerät mit dem Namen **my-module-device** hinzu.

Fügen Sie **my-module-device** das Modul **my-module** hinzu:

1. Navigieren Sie im Azure IoT-Explorer zum Gerät **my-module-device**.

1. Wählen Sie **Modulkennung** und dann **+ Hinzufügen** aus.

1. Geben Sie **my-module** als Namen der Modulkennung ein, und wählen Sie **Speichern** aus.

1. Wählen Sie in der Liste mit den Modulkennungen den Eintrag **my-module** aus. Kopieren Sie anschließend die primäre Verbindungszeichenfolge. Die Verbindungszeichenfolge des Moduls wird im weiteren Verlauf dieses Tutorials noch benötigt.

1. Wählen Sie die Registerkarte **Modulzwilling** aus. Sie sehen, dass keine gewünschten oder gemeldeten Eigenschaften vorhanden sind:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Laden Sie den Code herunter.

Klonen Sie das GitHub-Repository „Azure IoT Hub Device C# SDK“ auf Ihrem lokalen Computer, falls Sie dies noch nicht getan haben:

Öffnen Sie eine Eingabeaufforderung in einem Ordner Ihrer Wahl. Verwenden Sie den folgenden Befehl, um das GitHub-Repository mit den [Azure IoT-C#-Beispielen](https://github.com/Azure-Samples/azure-iot-samples-csharp) an diesem Speicherort zu klonen:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Vorbereiten des Projekts

Führen Sie das Öffnen und Vorbereiten des Beispielprojekts wie folgt durch:

1. Öffnen Sie in Visual Studio 2019 die Projektdatei *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj*.

1. Navigieren Sie in Visual Studio zu **Projekt > Thermostateigenschaften > Debuggen**. Fügen Sie dem Projekt dann die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | Die Modulverbindungszeichenfolge, die Sie sich zuvor notiert haben. |

    Weitere Informationen zur Beispielkonfiguration finden Sie in der [Beispiel-Infodatei](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Bearbeiten des Codes

Ändern Sie den Code wie folgt, damit er nicht wie ein Gerät, sondern wie ein Modul funktioniert:

1. Öffnen Sie in Visual Studio die Datei *Parameter.cs*, und ändern Sie die Zeile, in der die Variable **PrimaryConnectionString** festgelegt wird, wie folgt:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. Öffnen Sie in Visual Studio die Datei *Program.cs*, und ersetzen Sie die sieben Instanzen der `DeviceClient`-Klasse durch die `ModuleClient`-Klasse.

    > [!TIP]
    > Verwenden Sie die Visual Studio-Funktion zum Suchen/Ersetzen mit Aktivierung von **Groß-/Kleinschreibung beachten** und **Nur ganzes Wort suchen**, um `DeviceClient` durch `ModuleClient` zu ersetzen.

1. Öffnen Sie in Visual Studio die Datei *Thermostat.cs*, und ersetzen Sie beide Instanzen der `DeviceClient`-Klasse wie unten angegeben durch die `ModuleClient`-Klasse.

1. Speichern Sie Dateien, in denen Sie die Änderungen vorgenommen haben.

Wenn Sie den Code ausführen und dann den Azure IoT-Explorer verwenden, um den aktualisierten Modulzwilling anzuzeigen, werden der aktualisierte Gerätezwilling mit der Modell-ID und die gemeldete Eigenschaft des Moduls angezeigt:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interagieren mit einem Gerätemodul

Mit den Dienst-SDKs können Sie die Modell-ID von verbundenen IoT Plug & Play-Geräten und -Modulen abrufen. Sie können die Dienst-SDKs verwenden, um schreibbare Eigenschaften festzulegen und Befehle aufzurufen:

1. Öffnen Sie in einer anderen Instanz von Visual Studio das Projekt *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. Navigieren Sie in Visual Studio zu **Projekt > Thermostateigenschaften > Debuggen**. Fügen Sie dem Projekt dann die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | Der Wert, den Sie sich beim Abschließen von [Einrichten Ihrer Umgebung](set-up-environment.md) notiert haben |

    > [!TIP]
    > Sie können Ihre IoT-Hub-Verbindungszeichenfolge auch im Azure IoT-Explorer ermitteln.

1. Öffnen Sie die Datei *Program.cs*, und ändern Sie die Zeile, in der ein Befehl aufgerufen wird, wie folgt:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. Ändern Sie in der Datei *Program.cs* die Zeile, in der der Gerätezwilling abgerufen wird, wie folgt:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Vergewissern Sie sich, dass das Modulclientbeispiel noch ausgeführt wird, und führen Sie dann dieses Dienstbeispiel aus. In der Ausgabe des Dienstbeispiels werden die Modell-ID vom Gerätezwilling und der Befehlsaufruf angezeigt:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    In der Ausgabe des Modulclients wird die Antwort des Befehlshandlers angezeigt:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Konvertieren in ein IoT Edge-Modul

Um dieses Beispiel so zu konvertieren, dass es wie ein IoT Edge-Modul für IoT Plug & Play funktioniert, müssen Sie die Anwendung in einen Container packen. Darüber hinaus müssen Sie keine weiteren Codeänderungen vornehmen. Die Umgebungsvariable für die Verbindungszeichenfolge wird von der IoT Edge-Runtime beim Starten eingefügt. Weitere Informationen finden Sie unter [Entwickeln und Debuggen von Modulen für Azure IoT Edge mithilfe von Visual Studio 2019](../iot-edge/how-to-visual-studio-develop-module.md).

Informationen zum Bereitstellen Ihres Containermoduls finden Sie unter:

* [Ausführen von virtuellen Computern vom Typ „Azure IoT Edge unter Ubuntu“](../iot-edge/how-to-install-iot-edge-ubuntuvm.md)
* [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../iot-edge/how-to-install-iot-edge.md)

Sie können den Azure IoT-Explorer verwenden, um Folgendes anzuzeigen:

* Die Modell-ID Ihres IoT Edge-Geräts im Modulzwilling.
* Telemetriedaten vom IT Edge-Gerät.
* Eigenschaftsaktualisierungen von IoT Edge-Modulzwillingen, die IoT Plug & Play-Benachrichtigungen auslösen.
* Das IoT Edge-Modul reagiert auf Ihre IoT Plug & Play-Befehle.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie für ein IoT Plug & Play-Gerät mit Modulen eine Verbindung mit einem IoT-Hub herstellen. Weitere Informationen zu IoT Plug & Play-Gerätemodellen finden Sie unter:

> [!div class="nextstepaction"]
> [Anleitung für Entwickler: Modellierung mit IoT Plug & Play](./concepts-developer-guide-device.md)