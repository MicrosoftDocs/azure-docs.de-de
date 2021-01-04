---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: ff1041c941a994784c68d779bd96dec9070ce89a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511327"
---
In diesem Tutorial erfahren Sie, wie Sie eine IoT Plug & Play-Beispielgeräteanwendung mit Komponenten erstellen, mit Ihrem IoT-Hub verbinden und die an den Hub gesendeten Informationen mithilfe des Azure IoT-Explorer-Tools anzeigen. Die Beispielanwendung wurde in C# geschrieben und ist im Azure IoT-Geräte-SDK für C# enthalten. Ein Lösungsentwickler kann mithilfe von Azure IoT-Explorer mehr über die Funktionen eines IoT Plug & Play-Geräts erfahren, ohne Gerätecode anzeigen zu müssen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Installieren Sie die folgende Software in Ihrer lokalen Windows-Umgebung, um dieses Tutorial unter Windows ausführen zu können:

* [Visual Studio (Community, Professional oder Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonen des SDK-Repositorys mit dem Beispielcode

Wenn Sie [Schnellstart: Verbinden einer unter Windows ausgeführten IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md) abgeschlossen haben, wurde das Repository bereits geklont.

Klonen Sie die Beispiele aus dem GitHub-Repository mit den Azure IoT-Beispielen für C#. Öffnen Sie eine Eingabeaufforderung in einem Ordner Ihrer Wahl. Führen Sie den folgenden Befehl zum Klonen des GitHub-Repositorys mit [Microsoft Azure IoT-Beispielen für .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) aus:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Ausführen des Beispielgeräts

In dieser Schnellstartanleitung verwenden Sie einen in C# geschriebenen Beispieltemperaturregler als IoT Plug & Play-Gerät. So führen Sie das Beispielgerät aus:

1. Öffnen Sie in Visual Studio 2019 die Projektdatei *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj*.

1. Navigieren Sie in Visual Studio zu **Projekt > TemperatureController-Eigenschaften > Debuggen**. Fügen Sie dem Projekt dann die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Der Wert, den Sie sich beim Abschließen von [Einrichten Ihrer Umgebung](../articles/iot-pnp/set-up-environment.md) notiert haben |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Der Wert, den Sie sich beim Abschließen von [Einrichten Ihrer Umgebung](../articles/iot-pnp/set-up-environment.md) notiert haben |


1. Jetzt können Sie das Beispiel in Visual Studio erstellen und im Debugmodus ausführen.

1. Es werden Nachrichten darüber angezeigt, dass das Gerät Informationen gesendet und sich selbst als „online“ gemeldet hat. Diese Nachrichten weisen darauf hin, dass das Gerät damit begonnen hat, Telemetriedaten an den Hub zu senden und jetzt bereit zum Empfangen von Befehlen und Eigenschaftsaktualisierungen ist. Schließen Sie diese Instanz von Visual Studio nicht. Sie benötigen sie zur Bestätigung, dass das Dienstbeispiel funktioniert.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Überprüfen des Codes mithilfe von Azure IoT-Explorer

Überprüfen Sie nach dem Start des Geräteclientbeispiels mithilfe von Azure IoT-Explorer, ob das Beispiel funktioniert.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

In diesem Beispiel wird ein IoT Plug & Play-Temperaturregler implementiert. Das in diesem Beispiel implementierte Modell umfasst [mehrere Komponenten](../articles/iot-pnp/concepts-components.md). In der [DTDL-Modelldatei (Digital Twins Definition Language) für das Temperaturgerät](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) werden die vom Gerät implementierten Telemetriedaten, Eigenschaften und Befehle definiert.

Der Gerätecode stellt mithilfe der Standardmethode `CreateFromConnectionString` eine Verbindung mit Ihrem IoT-Hub her. Das Gerät sendet die Modell-ID des in der Verbindungsanforderung implementierten DTDL-Modells. Ein Gerät, das eine Modell-ID sendet, ist ein IoT Plug & Play-Gerät:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

Die Modell-ID wird im Code gespeichert, wie im folgenden Codeausschnitt zu sehen:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Nachdem das Gerät eine Verbindung mit Ihrem IoT-Hub hergestellt hat, registriert der Code die Befehlshandler. Der Befehl `reboot` wird in der Standardkomponente definiert. Der Befehl `getMaxMinReport` wird in den beiden Thermostatkomponenten definiert:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Es gibt separate Handler für die Aktualisierung gewünschter Eigenschaften der beiden Thermostatkomponenten:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

Durch den Beispielcode werden Telemetriedaten von beiden Thermostatkomponenten gesendet:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

Von der Methode `SendTemperatureTelemetryAsync` wird die Klasse `PnpHhelper` verwendet, um Nachrichten für die jeweilige Komponente zu erstellen:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

Die Klasse `PnpHelper` enthält weitere Beispielmethoden, die Sie mit einem Modell mit mehreren Komponenten verwenden können.

Verwenden Sie Azure IoT-Explorer, um die Telemetriedaten und Eigenschaften der beiden Thermostatkomponenten anzuzeigen:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Gerät mit mehreren Komponenten in Azure IoT-Explorer":::

Mit dem Tool Azure IoT-Explorer können Sie auch Befehle in einer der beiden Thermostatkomponenten oder in der Standardkomponente aufrufen.
