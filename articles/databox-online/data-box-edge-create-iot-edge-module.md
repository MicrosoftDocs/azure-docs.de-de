---
title: C#-IoT Edge-Modul für Azure Data Box Edge | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein C#-IoT Edge-Modul entwickeln, das in Data Box Edge bereitgestellt werden kann.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: c2803ba598895834bb197f4a06ff0635354fcaca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680894"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Entwickeln eines C#-IoT Edge-Moduls zum Verschieben von Dateien in Data Box Edge

In diesem Artikel wird Schritt für Schritt erläutert, wie Sie ein IoT Edge-Modul zur Bereitstellung mit Ihrem Data Box Edge-Gerät erstellen. Azure Data Box Edge ist eine Speicherlösung, mit der Sie Daten verarbeiten und über ein Netzwerk an Azure senden können.

Sie können Azure IoT Edge-Module mit Data Box Edge verwenden, um die Daten beim Verschieben in Azure zu transformieren. Mit dem in diesem Artikel verwendeten Modul wird die Logik zum Kopieren einer Datei von einer lokalen Freigabe in eine Cloudfreigabe auf Ihrem Data Box Edge-Gerät implementiert.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen einer Containerregistrierung zum Speichern und Verwalten Ihrer Module (Docker-Images)
> * Erstellen eines IoT Edge-Moduls zur Bereitstellung auf Ihrem Data Box Edge-Gerät


## <a name="about-the-iot-edge-module"></a>Informationen zum IoT Edge-Modul

Auf dem Data Box Edge-Gerät können IoT Edge-Module bereitgestellt und ausgeführt werden. Edge-Module sind im Wesentlichen Docker-Container, die eine bestimmte Aufgabe ausführen, z.B. Erfassen einer Nachricht von einem Gerät, Transformieren einer Nachricht oder Senden einer Nachricht an einen IoT Hub. In diesem Artikel erstellen Sie ein Modul, mit dem Dateien von einer lokalen Freigabe in eine Cloudfreigabe auf Ihrem Data Box Edge-Gerät kopiert werden.

1. Die Dateien werden in die lokale Freigabe auf dem Data Box Edge-Gerät geschrieben.
2. Der Dateiereignisgenerator erstellt ein Dateiereignis für jede in die lokale Freigabe geschriebene Datei. Die Dateiereignisse werden auch generiert, wenn eine Datei geändert wird. Die Dateiereignisse werden dann an den IoT Edge-Hub gesendet (in der IoT Edge-Runtime).
3. Das benutzerdefinierte IoT Edge-Modul verarbeitet das Dateiereignis und erstellt ein Dateiereignisobjekt, das auch einen relativen Pfad für die Datei enthält. Das Modul generiert anhand des relativen Dateipfads einen absoluten Pfad und kopiert die Datei von der lokalen Freigabe in die Cloudfreigabe. Das Modul löscht dann die Datei aus der lokalen Freigabe.

![Funktionsweise des Azure IoT Edge-Moduls in Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Sobald sich die Datei in der Cloudfreigabe befindet, wird sie automatisch in Ihr Azure Storage-Konto hochgeladen.

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie über Folgendes verfügen:

- Ein Data Box Edge-Gerät wird ausgeführt.

    - Das Gerät verfügt auch über eine zugeordnete IoT Hub-Ressource.
    - Auf dem Gerät ist die Rolle „Edgecomputing“ konfiguriert.
    Weitere Informationen zu Data Box Edge finden Sie unter [Konfigurieren von Compute](data-box-edge-deploy-configure-compute.md#configure-compute).

- Folgende Entwicklungsressourcen sind installiert:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Erweiterung C# for Visual Studio Code mit Unterstützung von OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
    - [Azure IoT Edge-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Möglicherweise müssen Sie ein Konto erstellen, um die Software herunterzuladen und zu installieren.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Eine Azure Container Registry-Instanz ist eine private Docker-Registrierung in Azure, in der Sie Ihre privaten Docker-Containerimages speichern und verwalten können. Azure Container Registry und Docker Hub sind zwei gängige Docker-Registrierungsdienste, die in der Cloud verfügbar sind. In diesem Artikel wird Container Registry verwendet.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie **Ressource erstellen > Container > Containerregistrierung** aus. Klicken Sie auf **Create**.
3. Stellen Sie Folgendes bereit:

   1. Einen eindeutigen **Registrierungsnamen** in Azure, der 5 bis 50 alphanumerische Zeichen umfasst.
   2. Wählen Sie ein **Abonnement**aus.
   3. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe.
   4. Wählen Sie einen **Speicherort**aus. Es wird empfohlen, den gleichen Speicherort zu verwenden, der der Data Box Edge-Ressource zugeordnet ist.
   5. Legen Sie **Administratorbenutzer** auf **Aktivieren** fest.
   6. Legen Sie die SKU auf **Basic** fest.

      ![Erstellen einer Containerregistrierung](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Klicken Sie auf **Erstellen**.
5. Navigieren Sie nach der Erstellung der Containerregistrierung zu dieser Registrierung, und klicken Sie auf **Zugriffsschlüssel**.

    ![Abrufen der Zugriffsschlüssel](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopieren Sie die Werte für **Anmeldeserver**, **Benutzername** und **Kennwort**. Sie verwenden diese Werte später, um das Docker-Image in Ihrer Registrierung zu veröffentlichen und um die Registrierungsanmeldeinformationen der Azure IoT Edge-Runtime hinzuzufügen.


## <a name="create-an-iot-edge-module-project"></a>Erstellen eines IoT Edge-Modulprojekts

Mit den folgenden Schritten wird ein IoT Edge-Modulprojekt basierend auf dem .NET Core 2.1 SDK erstellt. In diesem Projekt werden Visual Studio Code und die Azure IoT Edge-Erweiterung verwendet.

### <a name="create-a-new-solution"></a>Erstellen einer neuen Lösung

Erstellen Sie eine C#-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Wählen Sie in Visual Studio Code die Optionen **Ansicht > Befehlspalette** aus, um die VS Code-Befehlspalette zu öffnen.
2. Geben Sie in der Befehlspalette den Befehl **Azure: Sign in** ein, und führen Sie ihn aus, und befolgen Sie die Anweisungen zum Anmelden bei Ihrem Azure-Konto. Falls Sie bereits angemeldet sind, können Sie diesen Schritt überspringen.
3. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Geben Sie in der Befehlspalette die folgenden Informationen an, um die Projektmappe zu erstellen:

    1. Wählen Sie den Ordner aus, in dem die Projektmappe erstellt werden soll.
    2. Geben Sie einen Namen für Ihre Projektmappe ein, oder übernehmen Sie den Standardnamen **EdgeSolution**.
    
        ![Erstellen der neuen Projektmappe, 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Wählen Sie **C#-Modul** als Modulvorlage aus.
    4. Ersetzen Sie den Modulstandardnamen durch den gewünschten Namen, in diesem Fall durch **FileCopyModule**.
    
        ![Erstellen der neuen Projektmappe, 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Geben Sie die Containerregistrierung an, die Sie im vorherigen Abschnitt als Imagerepository für das erste Modul erstellt haben. Ersetzen Sie **localhost:5000** durch den kopierten Wert für den Anmeldeserver.

        Die endgültige Zeichenfolge sieht wie folgt aus: `<Login server name>/<Module name>`. In diesem Beispiel ist dies `mycontreg2.azurecr.io/filecopymodule`.

        ![Erstellen der neuen Projektmappe, 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Navigieren Sie zu **Datei > Ordner öffnen**.

    ![Erstellen der neuen Projektmappe, 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Öffnen Sie den Ordner **EdgeSolution**, den Sie zuvor erstellt haben. Im VS Code-Fenster wird der Arbeitsbereich für Ihre IoT Edge-Projektmappe mit den fünf Komponenten der obersten Ebene geladen. In diesem Artikel werden der Ordner **.vscode** und die Dateien **.gitignore**, **.env** und **deployment.template.json** nicht bearbeitet.
    
    Als einzige Komponente ändern Sie nur den Ordner „modules“. Dieser Ordner enthält den C#-Code für Ihr Modul und die Docker-Dateien zum Erstellen des Moduls als Containerimage.

    ![Erstellen der neuen Projektmappe, 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

1. Öffnen Sie im VS Code-Explorer **Module > FileCopyModule > Program.cs**.
2. Fügen Sie oben im Namespace **FileCopyModule** die folgenden using-Anweisungen für Typen hinzu, die im weiteren Verlauf verwendet werden. **Microsoft.Azure.Devices.Client.Transport.Mqtt** ist ein Protokoll zum Senden von Nachrichten an den IoT Edge-Hub.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Fügen Sie der Program-Klasse die Variablen **InputFolderPath** und **OutputFolderPath** hinzu.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Fügen Sie der Program-Klasse die **MessageBody**-Klasse hinzu. Diese Klassen definieren das erwartete Schema für den Textkörper eingehender Nachrichten.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. In der **Init**-Methode erstellt und konfiguriert der Code ein **ModuleClient**-Objekt. Mit diesem Objekt kann das Modul über das MQTT-Protokoll die Verbindung mit der lokalen Azure IoT Edge-Runtime herstellen, um Nachrichten zu senden und zu empfangen. Die in der Init-Methode verwendete Verbindungszeichenfolge wird durch die IoT Edge-Runtime für das Modul bereitgestellt. Der Code registriert einen FileCopy-Rückruf, um über den Endpunkt **input1** Nachrichten von einem IoT Edge-Hub zu empfangen.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Fügen Sie den Code für **FileCopy** ein.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Speichern Sie diese Datei.

## <a name="build-your-iot-edge-solution"></a>Erstellen Ihrer IoT Edge-Projektmappe

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und Code in FileCopyModule hinzugefügt, um Dateien von der lokalen Freigabe in die Cloudfreigabe zu kopieren. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

1. Wechseln Sie in VSCode zu „Terminal“ > „Neues Terminal“, um ein neues integriertes Terminal von Visual Studio Code zu öffnen.
2. Melden Sie sich bei Docker an. Geben Sie dazu den folgenden Befehl im integrierten Terminal ein:

    `docker login <ACR login server> -u <ACR username>`

    Verwenden Sie den Anmeldeserver und den Benutzernamen, die Sie aus Ihrer Containerregistrierung kopiert haben. 

    ![Erstellen und Pushen der IoT Edge-Projektmappe](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Geben Sie das Kennwort an, wenn Sie dazu aufgefordert werden. Sie können die Werte für den Anmeldeserver, den Benutzernamen und das Kennwort auch über die **Zugriffsschlüssel** in Ihrer Containerregistrierung im Azure-Portal abrufen.
 
3. Nach der Angabe der Anmeldeinformationen können Sie Ihr Modulimage an die Azure-Containerregistrierung pushen. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **module.json**, und wählen Sie **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und pushen) aus.

    ![Erstellen und Pushen der IoT Edge-Projektmappe](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Wenn Sie in Visual Studio Code festlegen, dass die Projektmappe erstellt werden soll, werden zwei Befehle im integrierten Terminal ausgeführt: „docker build“ und „docker push“. Diese beiden Befehle erstellen Ihren Code, packen „CSharpModule.dll“ in Container und pushen den Code anschließend an die Containerregistrierung, die Sie beim Initialisieren der Projektmappe angegeben haben.

    Sie werden aufgefordert, die Modulplattform auszuwählen. Wählen Sie *amd64* für Linux aus.

    ![Plattform auswählen](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Es werden nur die Linux-Module unterstützt.

    Möglicherweise wird die folgende Warnung angezeigt, die Sie jedoch ignorieren können:

    *Program.cs(77,44): Warnung CS1998: Bei dieser asynchronen Methode fehlen „await“-Operatoren. Die Methode wird synchron ausgeführt. Ziehen Sie die Verwendung des „Await“-Operators zum Warten auf nicht blockierende API-Aufrufe in Betracht, oder verwenden Sie „Await Task.Run(...)“, um die CPU-gebundene Verarbeitung in einem Hintergrundthread auszuführen.*

4. Sie können die vollständige Adresse des Containerimages mit Tag im integrierten VS Code-Terminal anzeigen. Die Imageadresse wird auf der Grundlage der Informationen in der Datei „module.json“ mit dem Format `<repository>:<version>-<platform>` erstellt. In diesem Artikel sollte sie wie folgt aussehen: `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen und Ausführen dieses Moduls in Data Box Edge finden Sie in den Schritten unter [Hinzufügen eines Moduls](data-box-edge-deploy-configure-compute.md#add-a-module).
