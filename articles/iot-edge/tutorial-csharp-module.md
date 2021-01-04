---
title: 'Tutorial: Entwickeln eines C#-Moduls für Linux-Geräte mithilfe von Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit C#-Code erstellen und auf einem IoT Edge-Gerät unter Linux bereitstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 71bfc84eb50521aef72f78b482bddda112c00c6c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964370"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Tutorial: Entwickeln eines C#-IoT Edge-Moduls für Linux-Geräte

Verwenden Sie Visual Studio Code zum Entwickeln von C#-Code, und stellen Sie ihn auf einem Linux-Gerät bereit, auf dem Azure IoT Edge ausgeführt wird.

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen. Sie verwenden das simulierte IoT Edge-Gerät, das Sie in den Schnellstartanleitungen zum Bereitstellen von Azure IoT Edge auf einem simulierten Gerät unter [Windows](quickstart.md) oder [Linux](quickstart-linux.md) erstellt haben. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen Sie ein IoT Edge-Modul auf der Grundlage des .NET Core 2.1 SDK unter Verwendung von Visual Studio Code.
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio Code und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial erfahren Sie, wie Sie ein Modul in **C#** mit **Visual Studio Code** entwickeln und auf einem **Linux-Gerät** bereitstellen. Wenn Sie Module für Windows-Geräte entwickeln, lesen Sie stattdessen [Develop a C# IoT Edge module for Windows devices (Entwickeln eines C#-IoT Edge-Moduls für Windows-Geräte)](tutorial-csharp-module-windows.md).

Informieren Sie sich anhand der nachstehenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von C#-Modulen für Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#-Module für LinuxAMD64 in VS Code](./media/tutorial-c-module/green-check.png) | ![C#-Module für LinuxAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#-Module für LinuxARM32 in VS Code](./media/tutorial-c-module/green-check.png) | ![C#-Module für LinuxARM32 in Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Unterstützung für Linux ARM64-Geräte ist in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verfügbar. Weitere Informationen finden Sie unter [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Entwickeln und Debuggen von ARM64 IoT Edge-Modulen in Visual Studio Code (Vorschauversion))](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung einzurichten: [Entwickeln eines IoT Edge-Moduls für ein Linux-Gerät](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie bereits über die folgenden Komponenten verfügen:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

Bereiten Sie zum Abschluss dieser Tutorials die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer vor:

* [Erweiterung C# for Visual Studio Code mit Unterstützung von OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts

Die folgenden Schritte dienen zum Erstellen eines IoT Edge-Modulprojekts für C# unter Verwendung von Visual Studio Code und der Azure IoT Tools-Erweiterung. Sobald Sie eine Projektvorlage erstellt haben, fügen Sie ihr neuen Code hinzu, damit das Modul Nachrichten aufgrund der darin gemeldeten Eigenschaften herausfiltert.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Erstellen Sie eine C#-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Klicken Sie in Visual Studio Code auf **Ansicht** > **Befehlspalette**, um die VS Code-Befehlspalette zu öffnen.

2. Geben Sie in der Befehlspalette den Befehl **Azure: Sign in** ein, und führen Sie ihn aus, und befolgen Sie die Anweisungen zum Anmelden bei Ihrem Azure-Konto. Falls Sie bereits angemeldet sind, können Sie diesen Schritt überspringen.

3. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Folgen Sie den Anweisungen in der Befehlspalette, um Ihre Projektmappe zu erstellen.

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein, oder übernehmen Sie den Standardnamen **EdgeSolution**. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **C#-Modul** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **CSharpModule**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für das Containerimage wird der von Ihnen im vorherigen Schritt angegebene Name übernommen. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br>Das endgültige Imagerepository sieht wie folgt aus: \<registry name\>.azurecr.io/csharpmodule. |

   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen. Verwenden Sie die Anmeldeinformationen aus dem Abschnitt **Zugriffsschlüssel** Ihrer Azure-Containerregistrierung.

Die IoT Edge-Erweiterung versucht, Ihre Anmeldeinformationen für die Containerregistrierung per Pullvorgang aus Azure abzurufen und in die Umgebungsdatei einzufügen. Überprüfen Sie, ob Ihre Anmeldeinformationen bereits enthalten sind. Fügen Sie sie jetzt hinzu, wenn dies nicht der Fall ist:

1. Öffnen Sie im VS Code-Explorer die Datei vom Typ **.env**.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort** aus Ihrer Azure-Containerregistrierung.
3. Speichern Sie diese Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit C#-Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“.

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus.

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei.

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

1. Öffnen Sie im VS Code-Explorer **Module** > **CSharpModule** > **Program.cs**.

2. Fügen Sie oben im Namespace **CSharpModule** drei **using**-Anweisungen für Typen hinzu, die im weiteren Verlauf verwendet werden:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Fügen Sie der Klasse **Program** die Variable **temperatureThreshold** hinzu. Diese Variable legt den Wert fest, den die gemessene Temperatur übersteigen muss, damit die Daten an die IoT Hub-Instanz gesendet werden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Fügen Sie der Klasse **Program** die Klassen **MessageBody**, **Machine** und **Ambient** hinzu. Diese Klassen definieren das erwartete Schema für den Textkörper eingehender Nachrichten.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. Suchen Sie die Funktion **Init**. Diese Funktion erstellt und konfiguriert ein **ModuleClient**-Objekt, das es dem Modul ermöglicht, eine Verbindung mit der lokalen Azure IoT Edge-Runtime zum Senden und Empfangen von Nachrichten herzustellen. Nach dem Erstellen von **ModuleClient** liest der Code den Wert **temperatureThreshold** aus den gewünschten Eigenschaften des Modulzwillings. Der Code registriert einen Rückruf, um über den Endpunkt **input1** Nachrichten von einer IoT Edge Hub-Instanz zu empfangen. Ersetzen Sie die Methode **SetInputMessageHandlerAsync** durch eine neue, und fügen Sie den gewünschten Eigenschaften eine Methode vom Typ **SetDesiredPropertyUpdateCallbackAsync** hinzu. Zu diesem Zweck ersetzen Sie die letzte Zeile der **Init**-Methode durch folgenden Code:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Fügen Sie der Klasse **Program** die Methode **onDesiredPropertiesUpdate** hinzu. Diese Methode empfängt Aktualisierungen der gewünschten Eigenschaften aus dem Modulzwilling und aktualisiert die Variable **temperatureThreshold** entsprechend. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in einem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Ersetzen Sie die Methode **PipeMessage** durch die Methode **FilterMessages**. Diese Methode wird aufgerufen, wenn das Modul eine Nachricht vom IoT Edge-Hub empfängt. Sie filtert Nachrichten heraus, die Temperaturen unter dem über den Modulzwilling festgelegten Temperaturschwellenwert melden. Sie fügt der Nachricht außerdem die **MessageType**-Eigenschaft hinzu, deren Wert auf **Alert** festgelegt ist.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Speichern Sie die Datei "Program.cs".

9. Öffnen Sie im VS Code-Explorer die Datei **deployment.template.json** im Arbeitsbereich für Ihre IoT Edge-Projektmappe.

10. Fügen Sie dem Bereitstellungsmanifest den Modulzwilling **CSharpModule** hinzu. Fügen Sie am Ende des Abschnitts **modulesContent** nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-csharp-module/module-twin.png)

11. Speichern Sie die Datei „deployment.template.json“.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und dem Modul „CSharpModule“ Code hinzugefügt. Der neue Code filtert Nachrichten heraus, bei denen die gemeldete Computertemperatur innerhalb der zulässigen Grenzwerte liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

1. Öffnen Sie das in VS Code integrierte Terminal über **Ansicht** > **Terminal**.

1. Melden Sie sich bei Docker an. Geben Sie dazu den nachstehenden Befehl im Terminal ein. Melden Sie sich mit dem Benutzernamen, Kennwort und Anmeldeserver aus Ihrer Azure-Containerregistrierung an. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

1. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und wählen Sie **Build and Push IoT Edge Solution** (IoT Edge-Projektmappe erstellen und übertragen) aus.

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller.

## <a name="deploy-and-run-the-solution"></a>Bereitstellen und Ausführen der Projektmappe

Verwenden Sie den Visual Studio Code-Explorer und die Azure IoT Tools-Erweiterung, um das Modulprojekt auf Ihrem IoT Edge-Gerät bereitzustellen. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.amd64.json** im Ordner „config“. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie im Visual Studio Code-Explorer im Abschnitt **Azure IoT Hub** den Bereich **Geräte**, um Ihre IoT-Geräteliste anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und klicken Sie dann auf **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen).

3. Wählen Sie im Konfigurationsordner **(config)** die Datei **deployment.amd64.json** aus, und klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). Verwenden Sie nicht die Datei „deployment.template.json“.

4. Erweitern Sie unter Ihrem Gerät den Bereich **Module**, um eine Liste mit bereitgestellten und ausgeführten Modulen anzuzeigen. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollte das neue **CSharpModule**-Modul zusammen mit dem **SimulatedTemperatureSensor**-Modul sowie mit **$edgeAgent** und **$edgeHub** ausgeführt werden.

    Es dauert ggf. einige Minuten, bis die Module gestartet werden. Die IoT Edge-Runtime muss das neue Bereitstellungsmanifest empfangen, die Modulimages per Pullvorgang aus der Containerruntime abrufen und dann jedes neue Modul starten.

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet.

1. Klicken Sie im Visual Studio Code-Explorer mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

2. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Möglicherweise dauert es eine Weile, bis die Nachrichten eintreffen, weil das IoT Edge-Gerät erst seine neue Bereitstellung empfangen und alle Module starten muss. Danach wird bei den am Code von „CModule“ vorgenommenen Änderungen gewartet, bis die Maschinentemperatur 25 Grad erreicht hat, bevor Nachrichten gesendet werden. Außerdem fügt das Gerät allen Nachrichten, die diesen Temperaturschwellenwert erreichen, den Nachrichtentyp **Alert** (Warnung) hinzu.

   ![Anzeigen von Nachrichten, die in der IoT Hub-Instanz eingehen](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „CSharpModule“ im Bereitstellungsmanifest verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Erweitern Sie in Visual Studio Code die Details unter Ihrem IoT Edge-Gerät, um die derzeit ausgeführten Module anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf **CSharpModule**, und wählen Sie **Modulzwilling bearbeiten** aus.

3. Suchen Sie **TemperatureThreshold** in den gewünschten Eigenschaften. Ändern Sie den Wert auf eine neue Temperatur, die 5 bis 10 Grad höher als die zuletzt gemeldete Temperatur ist.

4. Speichern Sie die Modulzwillingsdatei.

5. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle im Bearbeitungsbereich für Modulzwillinge, und wählen Sie **Modulzwilling aktualisieren** aus.

6. Überwachen Sie die eingehenden D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud). Jetzt sollten die Nachrichten so lange gestoppt werden, bis der neue Temperaturschwellenwert erreicht wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul erstellt, das Code zum Filtern von Rohdaten enthält, die von Ihrem IoT Edge-Gerät generiert wurden.

Sie können die nächsten Tutorials durcharbeiten, um zu erfahren, wie Ihnen Azure IoT Edge bei der Bereitstellung von Azure-Clouddiensten helfen kann, um Daten auf Edge-Ebene zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Funktionen](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)