---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 13d0bdf82052ff2c61c5b2c6010956c8fb27574d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122455"
---
In diesem Schnellstart erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung erstellen, mit Ihrem IoT-Hub verbinden und die von ihm gesendeten Telemetriedaten mithilfe des Tools Azure IoT-Explorer anzeigen. Die Beispielanwendung wurde in C# geschrieben und ist den Azure IoT-Beispielen für C# enthalten. Ein Lösungsentwickler kann mithilfe des Tools Azure IoT-Explorer mehr über die Funktionen eines IoT Plug & Play-Geräts erfahren, ohne Gerätecode anzeigen zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Zur Ausführung dieser Schnellstartanleitung unter Windows müssen Sie auf Ihrem Entwicklungscomputer die folgende Software installiert haben:

* [Visual Studio (Community, Professional oder Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Laden Sie den Code herunter.

In dieser Schnellstartanleitung bereiten Sie eine Entwicklungsumgebung vor, die Sie zum Klonen und Erstellen aus dem Repository mit den Azure IoT-Beispielen für C# verwenden können.

Öffnen Sie eine Eingabeaufforderung in einem Ordner Ihrer Wahl. Führen Sie den folgenden Befehl aus, um das GitHub-Repository [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) an diesem Speicherort zu klonen:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Erstellen des Codes

Jetzt können Sie das Beispiel in Visual Studio erstellen und im Debugmodus ausführen.

1. Öffnen Sie in Visual Studio 2019 die Projektdatei *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj*.

1. Navigieren Sie in Visual Studio zu **Projekt > Thermostateigenschaften > Debuggen**. Fügen Sie dem Projekt dann die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Der Wert, den Sie sich beim Abschließen von [Einrichten Ihrer Umgebung](../articles/iot-pnp/set-up-environment.md) notiert haben |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Der Wert, den Sie sich beim Abschließen von [Einrichten Ihrer Umgebung](../articles/iot-pnp/set-up-environment.md) notiert haben |

Jetzt können Sie das Beispiel in Visual Studio erstellen und im Debugmodus ausführen.

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels

Wenn Sie die Codeausführung in Visual Studio unter Windows nachverfolgen möchten, fügen Sie der Funktion `main` in der Datei „program.cs“ einen Breakpoint hinzu.

Das Gerät ist jetzt zum Empfangen von Befehlen und Eigenschaftsaktualisierungen bereit und hat damit begonnen, Telemetriedaten an den Hub zu senden. Behalten Sie die Ausführung des Beispiels während der nächsten Schritte bei.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In diesem Beispiel wird ein einfacher IoT Plug & Play-Thermostat implementiert. Das in diesem Beispiel implementierte Modell verwendet keine IoT Plug & Play-[Komponenten](../articles/iot-pnp/concepts-components.md). Die [DTDL-Modelldatei (Digital Twins Definition Language) für das Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiert die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle.

Der Gerätecode stellt mithilfe der Standardmethode `CreateFromConnectionString` eine Verbindung mit Ihrem IoT-Hub her. Das Gerät sendet die Modell-ID des in der Verbindungsanforderung implementierten DTDL-Modells. Ein Gerät, das eine Modell-ID sendet, ist ein IoT Plug & Play-Gerät:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

Die Modell-ID wird im Code gespeichert, wie im folgenden Codeausschnitt zu sehen ist:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Der Code, der Eigenschaften aktualisiert, Befehle verarbeitet und Telemetriedaten sendet, ist identisch mit dem Code für ein Gerät, das keine IoT Plug & Play Konventionen verwendet.

Im Beispiel werden JSON-Objekte mithilfe einer JSON-Bibliothek in den von Ihrem IoT-Hub gesendeten Nutzlasten analysiert:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
