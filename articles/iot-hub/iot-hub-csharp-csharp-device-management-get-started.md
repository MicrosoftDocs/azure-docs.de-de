---
title: Erste Schritte mit der Azure IoT Hub-Geräteverwaltung (.NET/.NET) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT Hub-Geräteverwaltung den Neustart eines Remotegeräts initiieren. Sie verwenden das Azure IoT-Geräte-SDK für .NET, um eine simulierte Geräte-App zu implementieren, die eine direkte Methode enthält, und das Azure IoT-Dienst-SDK für .NET, um eine Dienst-App zu implementieren, die die direkte Methode aufruft.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: fe548b0e8c791d5e7e3bdbc7bd4612a130ff8168
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873285"
---
# <a name="get-started-with-device-management-netnet"></a>Erste Schritte mit der Geräteverwaltung (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Verwenden des Azure-Portals zum Erstellen einer IoT Hub-Instanz und einer Geräteidentität im IoT Hub

* Erstellen Sie eine simulierte Geräte-App, die eine direkte Methode enthält, die das Gerät neu startet. Direkte Methoden werden aus der Cloud aufgerufen.

* Erstellen einer .NET-Konsolenanwendung, die über Ihre IoT Hub-Instanz eine direkte Neustartmethode in der simulierten Geräte-App aufruft

Am Ende dieses Tutorials verfügen Sie über zwei .NET-Konsolen-Apps:

* **SimulateManagedDevice**: Stellt mit der zuvor erstellten Geräteidentität eine Verbindung mit dem IoT Hub her, empfängt eine direkte Methode zum Neustart, simuliert einen physischen Neustart und meldet den Zeitpunkt des letzten Neustarts.

* **TriggerReboot**: Ruft eine direkte Methode auf der simulierten Geräte-App auf und zeigt die Antwort sowie die aktualisierten Eigenschaften an.

Für dieses Tutorial benötigen Sie Folgendes:

* Visual Studio.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Auslösen eines Remoteneustarts auf dem Gerät über eine direkte Methode

In diesem Abschnitt erstellen Sie (mit C#) eine .NET Konsolen-App, die einen Remoteneustart auf einem Gerät über eine direkte Methode auslöst. Die App verwendet Gerätezwillingsabfragen, um den Zeitpunkt des letzten Neustarts bei diesem Gerät zu ermitteln.

1. Fügen Sie in Visual Studio einer neuen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung (.NET Framework** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Stellen Sie sicher, dass .NET-Framework-Version 4.5.1 oder höher verwendet wird. Nennen Sie das Projekt **TriggerReboot**.

    ![Neues Visual C#-Projekt für den klassischen Windows-Desktop](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **TriggerReboot**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen**, suchen Sie nach **Microsoft.Azure.Devices**, wählen Sie zum Installieren des Pakets **Microsoft.Azure.Devices** die Option **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket [Azure IoT-Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

    ![Fenster „NuGet-Paket-Manager“](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im Abschnitt „Erstellen eines IoT Hubs“ erstellt haben. 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu.  Dieser Code ruft den Gerätezwilling für das neu startende Gerät auf und gibt die gemeldeten Eigenschaften aus.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Fügen Sie der **Program**-Klasse die folgende Methode hinzu.  Dieser Code löst den Neustart auf dem Gerät mit einer direkten Methode aus.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

7. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Erstellen Sie die Projektmappe.

> [!NOTE]
> In diesem Tutorial wird nur eine einzelne Abfrage für die gemeldeten Eigenschaften des Geräts durchgeführt. Der Produktionscode sollte einen Abruf enthalten, um die Änderungen in den gemeldeten Eigenschaften des Geräts zu erkennen.

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Erstellen einer .NET-Konsolen-App, die auf eine von der Cloud aufgerufene direkte Methode antwortet

* Auslösen des Neustarts eines simulierten Geräts

* Verwenden der gemeldeten Eigenschaften, um Gerätezwillingsabfragen zu ermöglichen, die Geräte und den Zeitpunkt ihres letzten Neustarts ermitteln

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **SimulateManagedDevice**.
   
    ![Neue klassische Visual C#-App für Windows](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulateManagedDevice**, und klicken Sie dann auf **NuGet-Pakete verwalten...**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen** aus, und suchen Sie dann nach **Microsoft.Azure.Devices.Client**. Wählen Sie **Installieren** aus, um das Paket **Microsoft.Azure.Devices.Client** zu installieren, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket [Azure IoT-Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.
   
    ![Fenster „NuGet-Paket-Manager“ mit Client-App](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Geräteverbindungszeichenfolge, die Sie sich im vorherigen Abschnitt notiert haben.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Fügen Sie Folgendes hinzu, um die direkte Methode auf dem Gerät zu implementieren:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Fügen Sie abschließend der **Main**-Methode folgenden Code hinzu, um die Verbindung mit dem IoT Hub zu öffnen und den Listener der Methode zu initialisieren:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```
        
8. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und klicken Sie dann auf **Startprojekte festlegen...**. Wählen Sie **Einzelnes Startprojekt** und dann im Dropdownmenü das Projekt **SimulateManagedDevice** aus. Erstellen Sie die Projektmappe.       

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können die Apps nun ausführen.

1. Klicken Sie zum Ausführen der .NET-Geräte-App **SimulateManagedDevice** mit der rechten Maustaste auf das **SimulateManagedDevice**-Projekt, wählen Sie **Debuggen** aus, und klicken Sie anschließend auf **Neue Instanz starten**. Es sollte auf die Methodenaufrufe aus Ihrem IoT Hub gelauscht werden. 

2. Jetzt ist das Gerät verbunden und wartet auf Methodenaufrufe, und Sie können die .NET-App **TriggerReboot** ausführen, um die Neustartmethode in der simulierten Geräte-App aufzurufen. Klicken Sie dazu mit der rechten Maustaste auf das **TriggerReboot**-Projekt, wählen Sie **Debuggen** aus, und klicken Sie anschließend auf **Neue Instanz starten**. „Neustart wird ausgeführt!“ sollte auf der **SimulatedManagedDevice**-Konsole angezeigt werden, und die gemeldeten Eigenschaften des Geräts, darunter der Zeitpunkt des letzten Neustarts, sollten auf der **TriggerReboot**-Konsole angezeigt werden.
   
    ![Ausführen der Dienst- und Geräte-App](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
