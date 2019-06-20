---
title: include file
description: include file
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178744"
---
## <a name="create-a-module-identity"></a>Erstellen einer Modulidentität

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, mit der eine Geräte- und eine Modulidentität in der Identitätsregistrierung Ihres IoT Hubs erstellt wird. Ein Gerät oder Modul kann nur eine Verbindung mit IoT Hub herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch im Abschnitt zur Identitätsregistrierung](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Wenn Sie diese Konsolen-App ausführen, generiert sie eine eindeutige ID und einen eindeutigen Schlüssel für das Gerät und das Modul. Ihr Gerät und Ihr Modul verwenden diese Werte, um sich beim Senden von D2C-Nachrichten an IoT Hub zu identifizieren. Bei den IDs gilt Groß-/Kleinschreibung.


1. **Erstellen eines Visual Studio-Projekts**: Fügen Sie in Visual Studio einer neuen Projektmappe mithilfe der Projektvorlage **Konsolen-App (.NET Framework)** ein Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Stellen Sie sicher, dass .NET Framework-Version 4.6.1 oder höher verwendet wird. Nennen Sie das Projekt **CreateIdentities** und die Projektmappe **IoTHubGetStarted**.

    ![Erstellen einer Visual Studio-Projektmappe](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Installieren des Azure IoT Hub-.NET-Dienst-SDKs V1.16.0-preview-001**: Modulidentität und Modulzwilling sind als Public Preview verfügbar. Sie sind nur in der Vorabversion der IoT Hub-Dienst-SDKs verfügbar. Navigieren Sie in Visual Studio zu „Tools“ > „NuGet-Paket-Manager“ > „NuGet-Pakete für Projektmappe verwalten“. Suchen Sie „Microsoft.Azure.Devices“. Vergewissern Sie sich, dass das Kontrollkästchen „Vorabversion einbeziehen“ aktiviert ist. Wählen Sie Version „1.16.0-preview-001“ aus, und installieren Sie sie. Sie haben nun Zugriff auf alle Modulfeatures. 

    ![Installieren des Azure IoT Hub-.NET-Dienst-SDKs V1.16.0-preview-001](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die IoT Hub-Verbindungszeichenfolge für den Hub, den Sie im vorherigen Abschnitt erstellt haben.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Fügen Sie den folgenden Code der **Main**-Klasse hinzu.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Fügen Sie der **Program**-Klasse die folgenden Methoden hinzu:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Mit der AddDeviceAsync()-Methode wird eine Geräteidentität mit der ID **myFirstDevice** erstellt. (Falls diese Geräte-ID in der Identitätsregistrierung bereits vorhanden ist, werden mit dem Code lediglich die vorhandenen Geräteinformationen abgerufen.) Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel in der simulierten Geräte-App, um eine Verbindung mit Ihrem IoT-Hub herzustellen.

    Die AddModuleAsync()-Methode erstellt eine Modulidentität mit der ID **myFirstModule** unter der Geräte-ID **myFirstDevice**. (Falls diese Modul-ID in der Identitätsregistrierung bereits vorhanden ist, werden mit dem Code lediglich die vorhandenen Modulinformationen abgerufen.) Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel in der simulierten Modul-App, um eine Verbindung mit Ihrem IoT Hub herzustellen.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Führen Sie diese Anwendung aus, und notieren Sie den Geräte- und Modulschlüssel.

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräte- und Modulidentitäten, um einen sicheren Zugriff auf IoT Hub zu ermöglichen. In der Identitätsregistrierung werden Geräte-IDs und -schlüssel für die Verwendung als Sicherheitsanmeldeinformationen gespeichert. Darüber hinaus wird in der Identitätsregistrierung ein Flag für den Aktivierungszustand des jeweiligen Geräts gespeichert, mit dem Sie den Zugriff für das betreffende Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Es gibt keinen Flag „Aktiviert/deaktiviert“ für Modulidentitäten. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
