---
title: Herstellen der Verbindung eines Raspberry Pi-Geräts mit Ihrer Azure IoT Central-Anwendung (C#) | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Geräteentwickler ein Raspberry Pi-Gerät mit Ihrer Azure IoT Central-Anwendung über C# verbinden.
author: viv-liu
ms.author: viviali
ms.date: 04/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3e77494eacaf16ac23a531cb7a16fe8bf6117006
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64714416"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Verbinden eines Raspberry Pi-Geräts mit Ihrer Azure IoT Central-Anwendung (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

In diesem Artikel wird beschrieben, wie Sie als Geräteentwickler ein Raspberry Pi-Gerät mithilfe der Programmiersprache C# mit Ihrer Microsoft Azure IoT Central-Anwendung verbinden.

## <a name="before-you-begin"></a>Voraussetzungen

Um die in diesem Artikel aufgeführten Schritte auszuführen, benötigen Sie die folgenden Komponenten:

* Eine Azure IoT Central-Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
* Sie verfügen über ein Raspberry Pi-Gerät, auf dem das Raspbian-Betriebssystem ausgeführt wird. Das Raspberry Pi-Gerät muss eine Verbindung mit dem Internet herstellen können. Weitere Informationen finden Sie unter [Einrichten Ihres Raspberry Pi-Geräts](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>Anwendungsvorlage **Beispiel-DevKits**

Eine Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde, enthält eine Gerätevorlage **Raspberry Pi** mit den folgenden Eigenschaften:

- Telemetriedaten, darunter die folgenden vom Gerät gesammelten Messungen:
  - Luftfeuchtigkeit
  - Temperatur
  - Pressure
  - Magnetometer (X, Y, Z)
  - Beschleunigungsmesser (X, Y, Z)
  - Gyroskop (X, Y, Z)
- Einstellungen
  - Spannung
  - Aktuell
  - Lüfterdrehzahl
  - IR-Umschalter
- Eigenschaften
  - Geräteeigenschaft „Nummer“
  - Cloudeigenschaft „Standort“

Vollständige Informationen zur Konfiguration der Gerätevorlage finden Sie unter [Details zur Raspberry Pi-Gerätevorlage](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Hinzufügen eines echten Geräts

Fügen Sie in Ihrer Azure IoT Central-Anwendung ein echtes Gerät aus der Gerätevorlage **Raspberry Pi** hinzu. Notieren Sie sich die Geräteverbindungsdetails (**Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**). Weitere Informationen finden Sie unter [Hinzufügen eines echten Geräts zu Ihrer Azure IoT Central-Anwendung](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Erstellen Ihrer .NET-Anwendung

Sie erstellen und testen die Geräteanwendung auf dem Desktopcomputer.

Für die folgenden Schritte können Sie Visual Studio Code verwenden. Weitere Informationen finden Sie unter [Arbeiten mit C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Wenn Sie möchten, können Sie die folgenden Schritte mit einem anderen Code-Editor ausführen.

1. Um Ihr .NET-Projekt zu initialisieren und die benötigten NuGet-Pakete hinzuzufügen, führen Sie die folgenden Befehle aus:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Öffnen Sie in Visual Studio Code den Ordner `pisample`. Dann öffnen Sie die Projektdatei **pisample.csproj**. Fügen Sie das im folgenden Codeausschnitt gezeigte `<RuntimeIdentifiers>`-Tag hinzu:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Ihre **Microsoft.Azure.Devices.Client**-Paketversionsnummer ist möglicherweise höher als die angezeigte.

1. Speichern Sie **pisample.csproj**. Wenn Sie in Visual Studio Code aufgefordert werden, den Wiederherstellungsbefehl auszuführen, wählen Sie **Wiederherstellen**.

1. Öffnen Sie **Program.cs**, und ersetzen Sie den Inhalt durch den folgenden Code:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Sie aktualisieren den Platzhalter `{your device connection string}` im nächsten Schritt.

## <a name="run-your-net-application"></a>Ausführen Ihrer .NET-Anwendung

Fügen Sie dem Code Ihre gerätespezifische Verbindungszeichenfolge zur Authentifizierung bei Azure IoT Central hinzu. Führen Sie diese Anleitungen zum [Generieren der Verbindungszeichenfolge für das Gerät](howto-generate-connection-string.md) aus, und verwenden Sie dazu die zuvor notierten Werte für **Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**.

1. Ersetzen Sie `{your device connection string}` in der Datei **Program.cs** durch die generierte Verbindungszeichenfolge.

1. Führen Sie den folgenden Befehl in Ihrer Befehlszeilenumgebung aus:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Kopieren Sie den Ordner `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` auf Ihr Raspberry Pi-Gerät. Die Dateien können Sie z.B. mit dem Befehl **scp** kopieren:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Weitere Informationen finden Sie unter [Raspberry Pi-Remotezugriff](https://www.raspberrypi.org/documentation/remote-access/).

1. Melden Sie sich bei Ihrem Raspberry Pi-Gerät an, und führen Sie die folgenden Befehle in einer Shell aus:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Führen Sie auf Ihrem Raspberry Pi-Gerät die folgenden Befehle aus:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Das Programm beginnt](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. In Ihrer Azure IoT Central-Anwendung können Sie sehen, wie der auf dem Raspberry Pi-Gerät ausgeführte Code mit der Anwendung interagiert:

   * Auf der Seite **Messungen** für Ihr echtes Gerät sehen Sie die Telemetrie.
   * Auf der Seite **Eigenschaften** sehen Sie den Wert der gemeldeten **Nummer**-Eigenschaft.
   * Auf der Seite **Einstellungen** können Sie verschiedene Einstellungen am Raspberry Pi-Gerät ändern, wie z.B. Spannung und Lüfterdrehzahl.

     Der folgende Screenshot zeigt das Raspberry Pi-Gerät, das gerade die Einstellungsänderung empfängt:

     ![Raspberry Pi empfängt Einstellungsänderung](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Details zur Raspberry Pi-Gerätevorlage

Eine Anwendung, die mit der Anwendungsvorlage **Beispiel-Entwickler-Kits** erstellt wurde, enthält eine Gerätevorlage **Raspberry Pi** mit den folgenden Eigenschaften:

### <a name="telemetry-measurements"></a>Telemetriemessungen

| Feldname     | Units  | Minimum | Maximum | Dezimalstellen |
| -------------- | ------ | ------- | ------- | -------------- |
| Luftfeuchtigkeit       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Einstellungen

Numerische Einstellungen

| `Display name` | Feldname | Units | Dezimalstellen | Minimum | Maximum | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spannung      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | Ampere  | 0              | 0       | 100     | 0       |
| Lüfterdrehzahl    | fanSpeed   | U/Min   | 0              | 0       | 1000    | 0       |

Einstellungen zum Ein-/Ausschalten

| `Display name` | Feldname | Text, wenn „eingeschaltet“ | Text, wenn „ausgeschaltet“ | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | EIN      | OFF      | Aus     |

### <a name="properties"></a>Eigenschaften

| Type            | `Display name` | Feldname | Datentyp |
| --------------- | ------------ | ---------- | --------- |
| Geräteeigenschaft | Nummer   | dieNumber  | number    |
| Text            | Location     | location   | –       |

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie ein Raspberry Pi-Gerät mit Ihrer Azure IoT Central-Anwendung verbunden wird, empfiehlt sich als nächster Schritt das [Einrichten einer benutzerdefinierten Gerätevorlage](howto-set-up-template.md) für Ihr eigenes IoT-Gerät.
