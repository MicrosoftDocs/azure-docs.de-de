---
title: 'Tutorial: Entwickeln eines C#-Moduls für Windows mithilfe von Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie ein IoT Edge-Modul mit C#-Code erstellen und auf einem IoT Edge-Gerät unter Windows bereitstellen.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 46d3a9e961be0717aba75c1f5845b97b52092510
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931961"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Entwickeln eines C#-IoT Edge-Moduls für Windows-Geräte

Verwenden Sie Visual Studio zum Entwickeln von C#-Code, und stellen Sie ihn auf einem Windows-Gerät bereit, auf dem Azure IoT Edge ausgeführt wird.

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, der Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Verwenden von Visual Studio zum Erstellen eines IoT Edge-Moduls auf der Grundlage des C# SDK
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Es leitet Nachrichten nur dann an nachgeschaltete Geräte weiter, wenn die Temperatur einen angegebenen Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial erfahren Sie, wie Sie ein Modul in **C#** mit **Visual Studio 2019** entwickeln und auf einem **Windows-Gerät** bereitstellen. Wenn Sie Module für Linux-Geräte entwickeln, lesen Sie stattdessen [Entwickeln eines C#-IoT Edge-Moduls für Linux-Geräte](tutorial-csharp-module.md).

Informieren Sie sich anhand der nachstehenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von C#-Modulen für Windows-Geräte:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64-Entwicklung** | ![Entwickeln von C#-Modulen für WinAMD64 in VS Code](./media/tutorial-c-module/green-check.png) | ![Entwickeln von C#-Modulen für WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64-Debugging** |   | ![Debuggen von C#-Modulen für WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung einzurichten: [Tutorial: Develop IoT Edge modules for Windows devices](tutorial-develop-for-windows.md) (Tutorial: Entwickeln von IoT Edge-Modulen für Windows-Geräte). Nach Abschluss dieses Tutorials sollten Sie bereits über die folgenden Komponenten verfügen:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Windows-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart.md)
* Eine Containerregistrierung wie [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), mit der Erweiterung [Azure IoT Edge-Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) konfiguriert
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), zur Ausführung von Windows-Containern konfiguriert

> [!TIP]
> Wenn Sie Visual Studio 2017 (ab Version 15.7) verwenden, laden Sie die [Azure IoT Edge-Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) für Visual Studio 2017 aus Visual Studio Marketplace herunter, und installieren Sie sie.

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts

Die folgenden Schritte dienen zum Erstellen eines IoT Edge-Modulprojekts unter Verwendung von Visual Studio und der Erweiterung „Azure IoT Edge-Tools“. Sobald Sie eine Projektvorlage erstellt haben, fügen Sie ihr neuen Code hinzu, damit das Modul Nachrichten aufgrund der darin gemeldeten Eigenschaften herausfiltert.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Azure IoT Edge Tools bietet Projektvorlagen für alle unterstützten IoT Edge-Modulsprachen in Visual Studio. Diese Vorlagen verfügen über alle erforderlichen Dateien und den Code zum Bereitstellen eines funktionierenden Moduls für einen IoT Edge-Test. Darüber hinaus dienen Sie Ihnen als Ausgangspunkt zum Anpassen der Vorlage mit Ihrer eigenen Geschäftslogik.

1. Starten Sie Visual Studio 2019, und wählen Sie **Neues Projekt erstellen** aus.

2. Suchen Sie nach **IoT Edge**, und wählen Sie das Projekt **Azure IoT Edge (Windows amd64)** aus. Klicken Sie auf **Weiter**.

   ![Erstellen eines neuen Azure IoT Edge-Projekts](./media/tutorial-csharp-module-windows/new-project.png)

3. Geben Sie dem Projekt und der Projektmappe einen aussagekräftigen Namen, etwa **CSharpTutorialApp**. Klicken Sie auf **Erstellen** , um das Projekt zu erstellen.

   ![Konfigurieren eines neuen Azure IoT Edge-Projekts](./media/tutorial-csharp-module-windows/configure-project.png)

4. Konfigurieren Sie Ihr Projekt mit den folgenden Werten:

   | Feld | Wert |
   | ----- | ----- |
   | Vorlage auswählen | Wählen Sie die Option **C#-Modul** aus. |
   | Modulprojektname | Nennen Sie das Modul **CSharpModule**. |
   | Docker-Imagerepository | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für Ihr Containerimage ist der Wert des Modulprojektnamens bereits vorhanden. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br> Das endgültige Imagerepository sieht wie folgt aus: \<registry name\>.azurecr.io/csharpmodule. |

   ![Konfigurieren von Zielgerät, Modultyp und Containerregistrierung für Ihr Projekt](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Wählen Sie **Hinzufügen** aus, um das Projekt zu erstellen.

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Das Bereitstellungsmanifest gibt die Anmeldeinformationen für Ihre Containerregistrierung an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen. Verwenden Sie die Anmeldeinformationen aus dem Abschnitt **Zugriffsschlüssel** Ihrer Azure-Containerregistrierung.

1. Öffnen Sie im Visual Studio-Projektmappen-Explorer die Datei **deployment.template.json**.

2. Suchen Sie unter den gewünschten Eigenschaften von „$edgeAgent“ nach der **registryCredentials**-Eigenschaft. Die Registrierungsadresse sollte automatisch anhand der Informationen eingefügt werden, die Sie beim Erstellen des Projekts angegeben haben. Anschließend sollten die Felder für Benutzername und Kennwort Variablennamen enthalten. Beispiel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Öffnen Sie in Ihrer Modulprojektmappe die **.env**-Datei. (Sie ist im Projektmappen-Explorer standardmäßig ausgeblendet, daher müssen Sie unter Umständen auf die Schaltfläche **Alle Dateien anzeigen** klicken, um sie anzuzeigen.) Die ENV-Datei muss die gleichen Variablen für Benutzername und Kennwort enthalten wie die Datei „deployment.template.json“.

4. Fügen Sie die Werte für **Benutzername** und **Kennwort** aus Ihrer Azure-Containerregistrierung hinzu.

5. Speichern Sie die Änderungen an der ENV-Datei.

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

Der Standardmodulcode empfängt Nachrichten in einer Eingabewarteschlange und leitet sie über eine Ausgabewarteschlange weiter. Fügen Sie einigen zusätzlichen Code hinzu, damit das Modul die Nachrichten im Edge-Bereich verarbeitet, bevor es sie an IoT Hub weiterleitet. Aktualisieren Sie das Modul, sodass es die Temperaturdaten in jeder Nachricht analysiert und die Nachricht nur dann an IoT Hub sendet, wenn die Temperatur einen bestimmten Schwellenwert überschreitet.

1. Öffnen Sie in Visual Studio **CSharpModule** > **Program.cs**.

2. Fügen Sie oben im Namespace **CSharpModule** drei **using**-Anweisungen für Typen hinzu, die im weiteren Verlauf verwendet werden:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Fügen Sie der Klasse **Program** nach der Indikatorvariablen die Variable **temperatureThreshold** hinzu. Die Variable „temperatureThreshold“ legt den Wert fest, den die gemessene Temperatur übersteigen muss, damit die Daten an die IoT Hub-Instanz gesendet werden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Fügen Sie der Klasse **Program** nach den Variablendeklarationen die Klassen **MessageBody**, **Machine** und **Ambient** hinzu. Diese Klassen definieren das erwartete Schema für den Textkörper eingehender Nachrichten.

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

5. Suchen Sie die Methode **Init**. Diese Methode erstellt und konfiguriert ein **ModuleClient**-Objekt, das es dem Modul ermöglicht, eine Verbindung mit der lokalen Azure IoT Edge-Runtime zum Senden und Empfangen von Nachrichten herzustellen. Der Code registriert außerdem einen Rückruf, um über den Endpunkt **input1** Nachrichten von einem IoT Edge-Hub zu empfangen.

   Ersetzen Sie die gesamte Init-Methode durch den folgenden Code:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Diese aktualisierte Init-Methode stellt immer noch mit „ModuleClient“ die Verbindung mit der IoT Edge-Runtime her, bietet jedoch auch neue Funktionen. Sie liest die gewünschten Eigenschaften des Modulzwillings zum Abrufen des Werts **temperatureThreshold**. Anschließend erstellt sie einen Rückruf, der auf alle zukünftigen Aktualisierungen der gewünschten Eigenschaften des Modulzwillings lauscht. Mit diesem Rückruf können Sie den Temperaturschwellenwert im Modulzwilling remote aktualisieren, und die Änderungen werden in das Modul integriert.

   Die aktualisierte Init-Methode ändert darüber hinaus die vorhandene Methode **SetInputMessageHandlerAsync**. Im Beispielcode werden eingehende Nachrichten für *input1* mit der Funktion *PipeMessage* verarbeitet. Der Code soll jedoch so geändert werden, dass stattdessen die Funktion *FilterMessages* verwendet wird, die wir in den nächsten Schritten erstellen.

6. Fügen Sie der Klasse **Program** die neue Methode **onDesiredPropertiesUpdate** hinzu. Diese Methode empfängt Aktualisierungen der gewünschten Eigenschaften aus dem Modulzwilling und aktualisiert die Variable **temperatureThreshold** entsprechend. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in einem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

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

7. Entfernen Sie die Beispielmethode **PipeMessage**, und ersetzen Sie sie durch die neue Methode **FilterMessages**. Diese Methode wird aufgerufen, wenn das Modul eine Nachricht vom IoT Edge-Hub empfängt. Sie filtert Nachrichten heraus, die Temperaturen unter dem über den Modulzwilling festgelegten Temperaturschwellenwert melden. Sie fügt der Nachricht außerdem die **MessageType**-Eigenschaft hinzu, deren Wert auf **Alert** festgelegt ist.

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
                using(var filteredMessage = new Message(messageBytes))
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

9. Öffnen Sie die Datei **deployment.template.json** in Ihrer IoT Edge-Projektmappe. Über diese Datei erhält der IoT Edge-Agent die Informationen dazu, welche Module bereitgestellt werden sollen (hier: **SimulatedTemperatureSensor** und **CSharpModule**). Außerdem wird der IoT Edge-Hub angewiesen, wie die Nachrichten zwischen ihnen weitergeleitet werden sollen.

10. Fügen Sie dem Bereitstellungsmanifest den Modulzwilling **CSharpModule** hinzu. Fügen Sie am Ende des Abschnitts **modulesContent** nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-csharp-module-windows/module-twin.png)

11. Speichern Sie die Datei „deployment.template.json“.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und **CSharpModule** Code hinzugefügt, der Nachrichten herausfiltert, deren gemeldete Computertemperatur unter dem zulässigen Schwellenwert liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

1. Verwenden Sie den folgenden Befehl, um sich auf Ihrem Entwicklungscomputer bei Docker anzumelden. Verwenden Sie den Benutzernamen, das Kennwort und den Anmeldeserver aus Ihrer Azure-Containerregistrierung. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

2. Klicken Sie im Visual Studio-Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, den Sie erstellen möchten. Der Standardname lautet **AzureIotEdgeApp1**. Da Sie ein Windows-Modul erstellen, sollte die Erweiterung **Windows.Amd64** lauten.

3. Wählen Sie **Build and Push IoT Edge Modules** (IoT Edge-Module erstellen und pushen) aus.

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller.

## <a name="deploy-modules-to-device"></a>Bereitstellen von Modulen auf dem Gerät

Verwenden Sie Visual Studio Cloud-Explorer und die Erweiterung „Azure IoT Edge Tools“ zum Bereitstellen des Modulprojekts auf Ihrem IoT Edge-Gerät. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei **deployment.windows-amd64.json** im Ordner „config“. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie in Visual Studio Cloud-Explorer die Ressourcen, um die Liste der IoT-Geräte anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf den Namen des IoT Edge-Geräts, das die Bereitstellung erhalten soll.

3. Wählen Sie **Bereitstellung erstellen** aus.

4. Wählen Sie im Datei-Explorer im Ordner „config“ Ihrer Projektmappe die Datei **deployment.windows-amd64.json** aus.

5. Aktualisieren Sie Cloud-Explorer zum Anzeigen der bereitgestellten Module, die unter Ihrem Gerät aufgeführt sind.

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sobald Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Module, die auf dem Gerät fehlen, werden gestartet.

Mit der Erweiterung „IoT Edge-Tools“ können Sie die bei Ihrer IoT Hub-Instanz eingehenden Nachrichten anzeigen.

1. Wählen Sie in Visual Studio Cloud-Explorer den Namen Ihres IoT Edge-Geräts aus.

2. Wählen Sie in der Liste **Aktionen** die Option **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

3. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Es kann eine Weile dauern, bis die Nachrichten eingehen, da bei den am Code von „CSharpModule“ vorgenommenen Änderungen gewartet wird, bis die Maschinentemperatur 25 Grad erreicht hat, bevor Nachrichten gesendet werden. Außerdem fügt das Gerät allen Nachrichten, die diesen Temperaturschwellenwert erreichen, den Nachrichtentyp **Alert** (Warnung) hinzu.

   ![Anzeigen von Nachrichten, die in der IoT Hub-Instanz eingehen](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „CSharpModule“ verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Öffnen Sie in Visual Studio die Datei **deployment.windows-amd64.json**. (Öffnen Sie nicht die Datei „deployment.template“. Wird das Bereitstellungsmanifest nicht in der Datei „config“ im Projektmappen-Explorer angezeigt, wählen Sie auf der Explorer-Symbolleiste das Symbol **Alle Dateien anzeigen** aus.)

2. Suchen Sie den Zwilling „CSharpModule“, und ändern Sie den Wert des Parameters **temperatureThreshold** in eine neue Temperatur, die fünf bis zehn Grad über der zuletzt gemeldeten Temperatur liegt.

3. Speichern Sie die Datei **deployment.windows-amd64.json**.

4. Führen Sie erneut die Bereitstellungsschritte aus, um das aktualisierte Bereitstellungsmanifest auf Ihr Gerät anzuwenden.

5. Überwachen Sie die eingehenden D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud). Jetzt sollten die Nachrichten so lange gestoppt werden, bis der neue Temperaturschwellenwert erreicht wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die in diesem Artikel verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul mit Code zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert werden.

Sie können die nächsten Tutorials durcharbeiten, um zu erfahren, wie Ihnen Azure IoT Edge bei der Bereitstellung von Azure-Clouddiensten helfen kann, um Daten auf Edge-Ebene zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Funktionen](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
