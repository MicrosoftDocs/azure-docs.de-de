---
title: Planen von Aufträgen mit Azure IoT Hub (.NET/.NET) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure IoT Hub-Auftrag planen, um eine direkte Methode auf mehreren Geräten aufzurufen. Sie verwenden die Azure IoT-Geräte-SDKs für .NET, um die simulierten Geräte-Apps und eine Dienst-App für die Auftragsausführung zu implementieren.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: robinsh
ms.openlocfilehash: f21f1eed6babee52f30c6eccc79f88dc7bee5d58
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864479"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Planen und Übertragen von Aufträgen (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Verwenden Sie Azure IoT Hub zum Planen und Nachverfolgen von Aufträgen, die Millionen von Geräte aktualisieren. Verwenden Sie Aufträge zum:

* Aktualisieren gewünschter Eigenschaften
* Aktualisieren von Tags
* Aufrufen direkter Methoden

Ein Auftrag umschließt eine dieser Aktionen und verfolgt die Ausführung für eine Gruppe von Geräten nach, die anhand einer Gerätezwillingsabfrage definiert wird. Eine Back-End-App kann z.B. einen Auftrag verwenden, um eine direkte Methode auf 10.000 Geräten aufzurufen, die die Geräte neu startet. Sie geben die Gruppe von Geräten mit einer Gerätezwillingsabfrage ein und planen die Ausführung des Auftrags zu einem späteren Zeitpunkt. Der Auftrag verfolgt den Fortschritt nach, während jedes der Geräte die Neustart-Direktmethode empfängt und ausführt.

Weitere Informationen zu diesen Funktionen finden Sie unter:

* Gerätezwillinge und Eigenschaften: [Erste Schritte mit Gerätezwillingen](iot-hub-csharp-csharp-twin-getstarted.md) und [Tutorial: Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)

* Direkte Methoden: [Verstehen und Aufrufen direkter Methoden von IoT Hub](iot-hub-devguide-direct-methods.md) und [Tutorial: Verwenden von direkten Methoden](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Erstellen Sie eine Geräte-App, die eine direkte Methode namens **LockDoor** implementiert, die von der Back-End-App aufgerufen werden kann.

* Erstellen Sie eine Back-End-App, die einen Auftrag zum Aufrufen der direkten **LockDoor**-Methode auf mehreren Geräten erstellt. Ein anderer Auftrag sendet die Updates der gewünschten Eigenschaft an mehrere Geräte.

Am Ende dieses Tutorials verfügen Sie über zwei .NET-Konsolen-Apps (C#):

**SimulateDeviceMethods** verbindet Ihren IoT Hub und implementiert die direkte **LockDoor**-Methode.

**ScheduleJob** verwendet Aufträge zum Aufrufen der direkten **LockDoor**-Methode und Aktualisieren der gewünschten Gerätezwillingseigenschaften auf mehreren Geräten.

Für dieses Tutorial benötigen Sie Folgendes:

* Visual Studio.
* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, die auf eine vom Lösungs-Back-End aufgerufene direkte Methode antwortet.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **SimulateDeviceMethods**.
   
    ![Neue klassische Visual C#-App für Windows](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das **SimulateDeviceMethods**-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten...**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen** aus, und suchen Sie dann nach **Microsoft.Azure.Devices.Client**. Wählen Sie **Installieren** aus, um das Paket **Microsoft.Azure.Devices.Client** zu installieren, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket [Azure IoT-Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.
   
    ![Fenster „NuGet-Paket-Manager“ mit Client-App](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Geräteverbindungszeichenfolge, die Sie sich im vorherigen Abschnitt notiert haben:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Fügen Sie Folgendes hinzu, um die direkte Methode auf dem Gerät zu implementieren:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

7. Fügen Sie Folgendes hinzu, um den Listener für Gerätezwillinge auf dem Gerät zu implementieren:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Fügen Sie abschließend der **Main**-Methode folgenden Code hinzu, um die Verbindung mit dem IoT Hub zu öffnen und den Listener der Methode zu initialisieren:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
9. Speichern Sie Ihre Arbeit, und erstellen Sie Ihre Lösung.         

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. einen erneuten Verbindungsversuch), wie im Artikel zum [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) beschrieben.
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planen von Aufträgen zum Aufrufen einer direkten Methode und Senden der Gerätezwillingsupdates

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App (mithilfe von C#), die Aufträge zum Aufrufen der direkten **LockDoor**-Methode und Senden der Aktualisierungen der gewünschten Eigenschaft an mehrere Geräte verwendet.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Geben Sie dem Projekt den Namen **ScheduleJob**.

    ![Neues Visual C#-Projekt für den klassischen Windows-Desktop](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ScheduleJob**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen**, suchen Sie nach **Microsoft.Azure.Devices**, wählen Sie zum Installieren des Pakets **Microsoft.Azure.Devices** die Option **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. In diesem Schritt wird das NuGet-Paket [Azure IoT-Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

    ![Fenster „NuGet-Paket-Manager“](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Fügen Sie die folgende `using`-Anweisung in den Standardanweisungen hinzu, sofern sie noch nicht vorhanden ist.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie die Platzhalter durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im vorherigen Abschnitt erstellt haben, und den Namen Ihres Geräts.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Fügen Sie der **Program**-Klasse eine weitere Methode hinzu:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Weitere Informationen zur Abfragesyntax finden Sie unter [IoT Hub-Abfragesprache](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

11. Speichern Sie Ihre Arbeit, und erstellen Sie Ihre Lösung. 

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können die Apps nun ausführen.

1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und klicken Sie dann auf **Erstellen**. **Mehrere Startprojekte**. Stellen Sie sicher, dass `SimulateDeviceMethods` ganz oben in der Liste angezeigt wird, gefolgt von `ScheduleJob`. Legen Sie die Aktionen für beide auf **Starten** fest, und klicken Sie auf **OK**.

2. Führen Sie die Projekte aus, indem Sie auf **Starten** klicken oder in das Menü **Debuggen** wechseln und auf **Debugging starten** klicken.

3. Sie sehen die Ausgabe von Geräte- und Back-End-App.

    ![Führen Sie die Apps aus, um Aufträge zu planen.](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Auftrag zum Planen einer direkten Methode für ein Gerät und eines Updates der Eigenschaften eines Gerätezwillings verwendet.

Informationen zu den weiteren ersten Schritten mit IoT Hub und Geräteverwaltungsmustern, z.B. drahtloses Firmware-Remoteupdate, finden Sie im [Tutorial: Durchführen eines Firmwareupdates](tutorial-firmware-update.md).

Informationen zum Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge finden Sie unter [Schnellstart: Bereitstellen des ersten IoT Edge-Moduls auf einem Linux-basierten x64-Gerät](../iot-edge/tutorial-simulate-device-linux.md).