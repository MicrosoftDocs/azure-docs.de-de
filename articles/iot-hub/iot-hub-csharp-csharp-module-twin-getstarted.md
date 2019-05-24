---
title: Erste Schritte mit der Modulidentität und dem Modulzwilling von Azure IoT Hub (.NET) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit IoT SDKs für .NET eine Modulidentität erstellen und den Modulzwilling aktualisieren.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 5421423441d03a7375feea2ca0dfe289993145ee
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873221"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub unter Verwendung eines .NET-Back-Ends und eines .NET-Geräts

> [!NOTE]
> [Modulidentitäten und Modulzwillinge](iot-hub-devguide-module-twins.md) ähneln den Geräteidentitäten und Gerätezwillingen von Azure IoT Hub, ermöglichen jedoch eine feinere Granularität. Azure IoT Hub-Geräteidentitäten und -Gerätezwillinge ermöglichen der Back-End-Anwendung die Konfiguration eines Geräts und geben Aufschluss über den Gerätezustand. Modulidentitäten und Modulzwillinge bieten diese Funktionalität hingegen für einzelne Komponenten eines Geräts. Auf Geräten mit mehreren Komponenten (etwa auf betriebssystembasierten Geräten oder Firmwaregeräten) ermöglichen sie isolierte Konfigurationen und Zustände für die einzelnen Komponenten.

Am Ende dieses Tutorials verfügen Sie über zwei .NET-Konsolen-Apps:

* **CreateIdentities**: Hiermit werden eine Geräteidentität, eine Modulidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden Ihrer Geräte- und Modulclients erstellt.

* **UpdateModuleTwinReportedProperties**: Hiermit werden aktualisierte vom Modulzwilling gemeldete Eigenschaften an Ihren IoT Hub sendet.

> [!NOTE]
> Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Anwendungen für Geräte als auch das zugehörige Lösungs-Back-End erstellen können, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Für dieses Tutorial benötigen Sie Folgendes:

* Visual Studio.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Sie haben nun Ihren IoT-Hub erstellt und verfügen über den Hostnamen und die IoT Hub-Verbindungszeichenfolge, die Sie für die weiteren Schritte in diesem Tutorial benötigen.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualisieren des Modulzwillings mithilfe des .NET-Geräte-SDKs

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App auf Ihrem simulierten Gerät, die die vom Modulzwilling gemeldeten Eigenschaften aktualisiert.

1. **Erstellen Sie ein Visual Studio-Projekt:** Fügen Sie in Visual Studio in der vorhandenen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung (.NET Framework)** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Stellen Sie sicher, dass .NET Framework-Version 4.6.1 oder höher verwendet wird. Nennen Sie das Projekt **UpdateModuleTwinReportedProperties**.

    ![Erstellen eines Visual Studio-Projekts](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **Installieren Sie das neueste Azure IoT Hub-.NET-Geräte-SDK:** Die Modulidentität und der Modulzwilling befinden sich in der Public Preview. Sie sind nur in der Vorabversion der IoT Hub-Geräte-SDKs verfügbar. Navigieren Sie in Visual Studio zu „Tools“ > „NuGet-Paket-Manager“ > „NuGet-Pakete für Projektmappe verwalten“. Suchen Sie nach „Microsoft.Azure.Devices.Client“. Vergewissern Sie sich, dass das Kontrollkästchen „Vorabversion einbeziehen“ aktiviert ist. Wählen Sie die neueste Version aus, und installieren Sie sie. Sie haben nun Zugriff auf alle Modulfeatures. 

    ![Installieren des Azure IoT Hub-.NET-Dienst-SDKs V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Abrufen der Modulverbindungszeichenfolge**: Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Navigieren Sie zu Ihrer IoT Hub-Instanz, und klicken Sie auf „IoT-Geräte“. Suchen Sie nach „myFirstDevice“, und öffnen Sie den Eintrag. Sie sehen, dass „myFirstModule“ erfolgreich erstellt wurde. Kopieren Sie die Modulverbindungszeichenfolge. Sie wird im nächsten Schritt benötigt.

    ![Moduldetails im Azure-Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **Erstellen der Konsolen-App „UpdateModuleTwinReportedProperties“**

    Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Verbindungszeichenfolge des Moduls.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Fügen Sie die folgende Methode **OnDesiredPropertyChanged** der Klasse **Program** hinzu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    In diesem Codebeispiel wird gezeigt, wie Sie den Modulzwilling abrufen und die gemeldeten Eigenschaften mit dem AMQP-Protokoll aktualisieren. In der öffentlichen Vorschau wird nur AMQP für Modulzwillingsvorgänge unterstützt.

5. Neben der oben genannten **Main**-Methode können Sie den folgenden Codeblock hinzufügen, um Ereignisse von Ihrem Modul an IoT Hub zu senden:

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können die Apps nun ausführen. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und klicken Sie dann auf **Startprojekte festlegen**. Wählen Sie **Mehrere Startprojekte** und dann **Starten** als Aktion für die Konsolen-App aus. Drücken Sie F5, um die App zu starten.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den weiteren ersten Schritten mit IoT Hub und zum Kennenlernen anderer IoT-Szenarien finden Sie in den folgenden Artikeln:

* [Erste Schritte mit der Geräteverwaltung](iot-hub-node-node-device-management-get-started.md)
* [Erste Schritte mit IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)