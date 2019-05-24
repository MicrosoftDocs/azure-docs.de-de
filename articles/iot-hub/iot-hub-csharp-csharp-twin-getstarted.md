---
title: Erste Schritte mit Azure IoT Hub-Gerätezwillingen (.NET/.NET) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure IoT Hub-Gerätezwillingen Tags hinzufügen und eine IoT Hub-Abfrage verwenden. Sie verwenden das Azure IoT-Geräte-SDK für .NET, um eine simulierte Geräte-App zu implementieren, und das Azure IoT-Dienst-SDK für .NET, um eine Dienst-App zu implementieren, die die Tags hinzufügt und die IoT Hub-Abfrage ausführt.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: 9d5d5bdc569fec1df20c7729285cc462d5af4ffb
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873235"
---
# <a name="get-started-with-device-twins-netnet"></a>Erste Schritte mit Gerätezwillingen (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Am Ende dieses Tutorials verfügen Sie über diese .NET-Konsolen-Apps:

* **CreateDeviceIdentity** ist eine .NET-App, mit der eine Geräteidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden mit Ihrer simulierten Geräte-App erstellt werden.

* **AddTagsAndQuery** ist eine .NET-Back-End-App, die Tags hinzufügt und Gerätezwillinge abfragt.

* **ReportConnectivity** ist eine .NET-Geräte-App, die ein Gerät simuliert, das eine Verbindung mit Ihrer IoT Hub-Instanz mit der zuvor erstellten Geräteidentität herstellt und seine Verbindungsbedingung meldet.

> [!NOTE]
> Im Artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) finden Sie Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Geräte- als auch Back-End-Apps erstellen können.
> 

Für dieses Tutorial benötigen Sie Folgendes:

* Visual Studio.
* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Abrufen der Verbindungszeichenfolge für den IoT-Hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Erstellen der Dienst-App

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App (in C#), mit der dem Gerätezwilling, der **myDeviceId** zugeordnet ist, Standortmetadaten hinzugefügt werden. Anschließend werden die in dem IoT Hub, der die Geräte in den USA auswählt, gespeicherten Gerätezwillinge abgefragt und dann diejenigen, die eine Mobilfunkverbindung gemeldet haben.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **AddTagsAndQuery**.
   
    ![Neues Visual C#-Projekt für den klassischen Windows-Desktop](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AddTagsAndQuery**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen** aus, und suchen Sie dann nach **Microsoft.Azure.Devices**. Wählen Sie **Installieren** aus, um das Paket **Microsoft.Azure.Devices** zu installieren, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket [Azure IoT-Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.
   
    ![Fenster „NuGet-Paket-Manager“](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im vorherigen Abschnitt erstellt haben.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```
   
    Die **Registry**-Klasse macht alle Methoden verfügbar, die für die Interaktion mit Gerätezwillingen des Diensts erforderlich sind. Mit dem vorherigen Code werden zunächst das **registryManager**-Objekt initialisiert, dann der Gerätezwilling für **myDeviceId** abgerufen und schließlich die zugehörigen Tags mit den gewünschten Standortinformationen aktualisiert.
   
    Nach der Aktualisierung werden zwei Abfragen ausgeführt: Mit der ersten werden nur die Gerätezwillinge von Geräten in der Anlage **Redmond43** ausgewählt. Mit der zweiten wird die Abfrage so angepasst, dass nur die Geräte ausgewählt werden, die über ein Mobilfunknetz verbunden sind.
   
    Beachten Sie, dass mit diesem Code beim Erstellen des **query**-Objekts eine maximale Anzahl von zurückgegebenen Dokumenten angegeben wird. Das **query**-Objekt enthält die boolesche Eigenschaft **HasMoreResults**, mit der Sie die **GetNextAsTwinAsync**-Methoden mehrmals aufrufen können, um alle Ergebnisse abzurufen. Die **GetNextAsJson**-Methode ist für Ergebnisse verfügbar, die keine Gerätezwillinge sind, z.B. Ergebnisse von Aggregationsabfragen.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. Öffnen Sie im Projektmappen-Explorer **Startprojekte festlegen**, und vergewissern Sie sich, dass als **Aktion** für **AddTagsAndQuery** **Starten** festgelegt ist. Erstellen Sie die Projektmappe.

9. Führen Sie diese Anwendung aus, indem Sie mit der rechten Maustaste auf das Projekt **AddTagsAndQuery** klicken und **Debuggen** und anschließend **Neue Instanz starten** auswählen. In den Ergebnissen für die Abfrage, mit der alle Geräte in der Anlage **Redmond43** abgefragt werden, sollte ein Gerät angezeigt werden und keines für die Abfrage, mit der die Ergebnisse auf die über ein Mobilfunknetz verbundenen Geräte beschränkt werden.
   
    ![Abfrageergebnisse im Fenster](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Im nächsten Abschnitt erstellen Sie eine Geräte-App, mit der die Verbindungsinformationen gemeldet und das Ergebnis der im vorherigen Abschnitt ausgeführten Abfrage geändert wird.

## <a name="create-the-device-app"></a>Erstellen der Geräte-App

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, die als **myDeviceId** eine Verbindung mit dem Hub herstellt und dann die gemeldeten Eigenschaften so aktualisiert, dass sie die Informationen über die Verbindung mit einem Mobilfunknetz enthalten.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **ReportConnectivity**.
   
    ![Neue klassische Visual C#-App für Windows](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ReportConnectivity**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen** aus, und suchen Sie dann nach **Microsoft.Azure.Devices.Client**. Wählen Sie **Installieren** aus, um das Paket **Microsoft.Azure.Devices.Client** zu installieren, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket [Azure IoT-Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.
   
    ![Fenster „NuGet-Paket-Manager“ mit Client-App](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Geräteverbindungszeichenfolge, die Sie sich im vorherigen Abschnitt notiert haben.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Das **Client**-Objekt macht alle Methoden verfügbar, die für die Interaktion mit Gerätezwillingen des Geräts erforderlich sind. Der oben gezeigte Code initialisiert das **Client**-Objekt und ruft dann den Gerätezwilling für **myDeviceId** ab.

7. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");
            
            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Der Code oben aktualisiert die für **myDeviceId** gemeldete Eigenschaft mit den Konnektivitätsinformationen.

8. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

9. Öffnen Sie im Projektmappen-Explorer **Startprojekte festlegen**, und vergewissern Sie sich, dass als **Aktion** für **ReportConnectivity** die Option **Starten** festgelegt ist. Erstellen Sie die Projektmappe.

10. Sie führen diese Anwendung aus, indem Sie mit der rechten Maustaste auf das Projekt **ReportConnectivity** klicken und dann **Debuggen** und anschließend **Neue Instanz starten** auswählen. Sie sollten sehen, wie die Zwillingsinformationen abgerufen und die Konnektivität anschließend als *gemeldete Eigenschaft* gesendet wird.
   
    ![Ausführen der Geräte-App zum Melden der Konnektivität](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Da das Gerät nun die Verbindungsinformationen gemeldet hat, sollten diese in beiden Abfragen angezeigt werden. Führen Sie die .NET-App **AddTagsAndQuery** aus, um die Abfragen erneut durchzuführen. Nun sollte **myDeviceId** in beiden Abfrageergebnisse angezeigt werden.
   
    ![Gerätekonnektivität erfolgreich gemeldet](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie im Azure-Portal einen neuen IoT-Hub konfiguriert und anschließend in der Identitätsregistrierung des IoT-Hubs eine Geräteidentität erstellt. Sie haben Gerätemetadaten aus einer Back-End-App als Tags hinzugefügt und eine simulierte Geräte-App zum Melden von Geräteverbindungsinformationen im Gerätezwilling geschrieben. Sie haben zudem erfahren, wie diese Informationen mithilfe der SQL-ähnlichen IoT Hub-Abfragesprache abgefragt werden können.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* Senden von Telemetriedaten von Geräten: [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (C#)](quickstart-send-telemetry-dotnet.md)

* Konfigurieren von Geräten mit den gewünschten Eigenschaften des Gerätezwillings: [Tutorial: Konfigurieren Ihrer Geräte über einen Back-End-Dienst](tutorial-device-twins.md)

* Interaktives Steuern von Geräten (z.B. Einschalten eines Lüfters über eine benutzergesteuerte App): [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Node.js)](quickstart-control-device-dotnet.md)
