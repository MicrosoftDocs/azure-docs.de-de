---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/25/2020
ms.openlocfilehash: 3668d4e5164ad731058f944feaef66029c2ed2a9
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126091"
---
## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Eine Azure IoT Central-Anwendung, die mit der Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](../articles/iot-central/core/quick-deploy-iot-central.md). Die Anwendung muss am 14. Juli 2020 oder nach diesem Datum erstellt worden sein.
* Einen Entwicklungscomputer mit [Visual Studio (Community, Professional oder Enterprise)](https://visualstudio.microsoft.com/downloads/)
* Eine lokale Kopie des GitHub-Repositorys mit [Microsoft Azure IoT-Beispielen für C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp), das den Beispielcode enthält. Laden Sie über den folgenden Link eine Kopie des Repositorys herunter: [ZIP herunterladen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Entzippen Sie anschließend die Datei an einem geeigneten Speicherort auf Ihrem lokalen Computer.

## <a name="review-the-code"></a>Überprüfen des Codes

Öffnen Sie in der Kopie des zuvor heruntergeladenen Repositorys mit Microsoft Azure IoT-Beispielen für C# die Projektdatei *azure-iot-samples-csharp-master\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* in Visual Studio. Öffnen Sie im Projekt **Thermostat** die Dateien *Program.cs* und *ThermostatSample.cs*, um den Code für dieses Beispiel anzuzeigen.

Wenn Sie das Beispiel ausführen, um eine Verbindung mit IoT Central herzustellen, wird Device Provisioning Service (DPS) zum Registrieren des Geräts und zum Generieren einer Verbindungszeichenfolge verwendet. Das Beispiel ruft die erforderlichen DPS-Verbindungsinformationen aus der Umgebung ab.

In *Program.cs* ruft die `main`-Methode `SetupDeviceClientAsync` zu folgenden Zwecken auf:

* Verwenden der Modell-ID `dtmi:com:example:Thermostat;1` beim Bereitstellen des Geräts mit DPS
* Erstellen einer **DeviceClient**-Instanz zum Herstellen einer Verbindung mit IoT Central

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      s_logger.LogDebug($"Initializing via DPS");
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
        // ...
        break;

    default:
        // ...
  }

  return deviceClient;
}
```

Die main-Methode erstellt dann eine **ThermostatSample**-Instanz und ruft die `PerformOperationsAsync`-Methode auf, um die Interaktionen mit IoT Central zu verarbeiten.

In *ThermostatSample.cs* führt die Methode `PerformOperationsAsync` folgende Aktionen aus:

* Festlegen eines Handlers zum Empfangen gewünschter Eigenschaftsaktualisierungen für die Zieltemperatur
* Festlegen eines Handlers für den Befehl **getMaxMinReport**
* Senden von Temperaturtelemetriedaten in regelmäßigen Abständen
* Senden der Höchsttemperatur seit dem letzten Neustart, wenn eine neue Höchsttemperatur erreicht wird

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(TargetTemperatureUpdateCallbackAsync, _deviceClient, cancellationToken);

  await _deviceClient.SetMethodHandlerAsync("getMaxMinReport", HandleMaxMinReportCommand, _deviceClient, cancellationToken);

  bool temperatureReset = true;
  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
      _temperature = Math.Round(_random.NextDouble() * 40.0 + 5.0, 1);
      temperatureReset = false;
    }

    await SendTemperatureAsync();
    await Task.Delay(5 * 1000);
  }
}
```

Die `SendTemperatureAsync`-Methode zeigt, wie das Gerät die Temperaturtelemetriedaten an IoT Central sendet:

```csharp
private async Task SendTemperatureAsync()
{
  await SendTemperatureTelemetryAsync();

  double maxTemp = _temperatureReadingsDateTimeOffset.Values.Max<double>();
  if (maxTemp > _maxTemp)
  {
    _maxTemp = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync();
  }
}
```

Die `UpdateMaxTemperatureSinceLastRebootAsync`-Methode sendet eine Aktualisierung der Eigenschaft `maxTempSinceLastReboot` an IoT Central:

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync()
{
  const string propertyName = "maxTempSinceLastReboot";

  var reportedProperties = new TwinCollection();
  reportedProperties[propertyName] = _maxTemp;

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties);
}
```

Die `TargetTemperatureUpdateCallbackAsync`-Methode behandelt die schreibbare Eigenschaftsaktualisierung für die Zieltemperatur aus IoT Central:

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
    const string propertyName = "targetTemperature";

    (bool targetTempUpdateReceived, double targetTemperature) = GetPropertyFromTwin<double>(desiredProperties, propertyName);
    if (targetTempUpdateReceived)
    {
      string jsonPropertyPending = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.InProgress}, " +
          $"\"av\": {desiredProperties.Version} }} }}";
      var reportedPropertyPending = new TwinCollection(jsonPropertyPending);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedPropertyPending);

      // Update Temperature in 2 steps
      double step = (targetTemperature - _temperature) / 2d;
      for (int i = 1; i <= 2; i++)
      {
        _temperature = Math.Round(_temperature + step, 1);
        await Task.Delay(6 * 1000);
      }

      string jsonProperty = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.Completed}, " +
        $"\"av\": {desiredProperties.Version}, \"ad\": \"Successfully updated target temperature\" }} }}";
      var reportedProperty = new TwinCollection(jsonProperty);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedProperty);
  }
  else
  {
    // ...
  }
}
```

Die `HandleMaxMinReportCommand`-Methode behandelt den über IoT Central aufgerufenen Befehl:

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
  try
  {
    DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
    var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

    Dictionary<DateTimeOffset, double> filteredReadings = _temperatureReadingsDateTimeOffset
      .Where(i => i.Key > sinceInDateTimeOffset)
      .ToDictionary(i => i.Key, i => i.Value);

    if (filteredReadings != null && filteredReadings.Any())
    {
      var report = new
      {
        maxTemp = filteredReadings.Values.Max<double>(),
        minTemp = filteredReadings.Values.Min<double>(),
        avgTemp = filteredReadings.Values.Average(),
        startTime = filteredReadings.Keys.Min(),
        endTime = filteredReadings.Keys.Max(),
      };

      byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
      return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
    }

    return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
  }
  catch (JsonReaderException ex)
  {
    // ...
  }
}
```

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Ausführen des Codes

So führen Sie die Beispielanwendung aus

1. Öffnen Sie in Visual Studio die Projektdatei *azure-iot-samples-csharp-master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat/Thermostat.csproj*.

1. Navigieren Sie in Visual Studio zu **Projekt > Thermostateigenschaften > Debuggen**. Fügen Sie dem Projekt dann die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Der zuvor notierte ID-Bereichswert |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Der generierte Wert für den Geräteschlüssel, den Sie sich zuvor notiert haben |

Sie können das Beispiel jetzt in Visual Studio ausführen und debuggen.

Die folgende Ausgabe zeigt, wie das Gerät registriert und eine Verbindung mit IoT Central hergestellt wird. Das Beispiel beginnt mit dem Senden von Telemetriedaten:

```output
[11/25/2020 11:07:58]info: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Press Control+C to quit the sample.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set up the device client.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Initializing via DPS
[11/25/2020 11:08:11]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler to receive "targetTemperature" updates.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler for "getMaxMinReport" command.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 36.5°C } is Completed.
[11/25/2020 11:08:18]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:23]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:29]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Sie können sehen, wie das Gerät auf Befehle und Eigenschaftsaktualisierungen reagiert:

```output
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 19/11/2020 06:30:00.
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: MaxMinReport since 19/11/2020 06:30:00: maxTemp=36.5, minTemp=36.5, avgTemp=36.5, startTime=25/11/2020 11:08:13, endTime=25/11/2020 11:09:51

...

[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Received - { "targetTemperature": 56°C }.
[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is InProgress.
[11/25/2020 11:14:40]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 56°C } is Completed.
[11/25/2020 11:14:43]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is Completed.
```
