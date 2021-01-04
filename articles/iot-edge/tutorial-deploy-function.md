---
title: 'Tutorial: Bereitstellen von Azure-Funktionen als Module – Azure IoT Edge'
description: In diesem Tutorial entwickeln Sie eine Azure-Funktion als IoT Edge-Modul und stellen sie anschließend auf einem Edgegerät bereit.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 5b5d60a014cf63bd28f3097ac3131ad4c7018208
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621245"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Bereitstellen von Azure-Funktionen als IoT Edge-Module

Mithilfe von Azure Functions können Sie Code bereitstellen, mit dem Ihre Geschäftslogik direkt auf Ihren Azure IoT Edge-Geräten implementiert wird. In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie eine Azure-Funktion erstellen und bereitstellen, die Sensordaten auf dem simulierten IoT Edge-Gerät filtert. Sie verwenden das simulierte IoT Edge-Gerät, das Sie in den Schnellstartanleitungen zum Bereitstellen von Azure IoT Edge auf einem simulierten Gerät unter [Windows](quickstart.md) oder [Linux](quickstart-linux.md) erstellt haben. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer Azure-Funktion mithilfe von Visual Studio Code
> * Erstellen und Veröffentlichen eines Docker-Images in einer Containerregistrierung mithilfe von VS Code und Docker
> * Bereitstellen des Moduls über die Containerregistrierung auf Ihrem IoT Edge-Gerät
> * Anzeigen gefilterter Daten

<center>

![Architekturdiagramm des Tutorials: Staging und Bereitstellung des Funktionsmoduls](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Die Azure-Funktion, die Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Die Funktion sendet Nachrichten nur dann an Azure IoT Hub (Upstream), wenn die Temperatur einen angegebenen Schwellenwert überschreitet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

Um ein IoT Edge-Modul mit Azure Functions zu entwickeln, installieren Sie die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer:

* [Erweiterung C# for Visual Studio Code mit Unterstützung von OmniSharp](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

## <a name="create-a-function-project"></a>Erstellen eines Funktionsprojekts

Die Azure IoT-Tools für Visual Studio Code, die Sie in den Voraussetzungen installiert haben, bietet Verwaltungsfunktionen sowie einige Codevorlagen. In diesem Abschnitt verwenden Sie Visual Studio Code zum Erstellen einer IoT Edge-Projektmappe, die eine Azure-Funktion enthält.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Erstellen Sie eine C# Function-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Öffnen Sie Visual Studio Code auf Ihrem Entwicklungscomputer.

2. Öffnen Sie die VS Code-Befehlspalette, indem Sie auf **Ansicht** > **Befehlspalette** klicken.

3. Geben Sie in der Befehlspalette den folgenden Befehl ein, und führen Sie ihn aus: **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: Neue IoT Edge-Projektmappe) ein, und führen Sie ihn aus. Folgen Sie den Anweisungen in der Befehlspalette, um Ihre Projektmappe zu erstellen.

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein (beispielsweise **FunctionSolution**), oder übernehmen Sie den Standardnamen. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Azure Functions – C#** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **CSharpFunction**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Ihr Containerimage wird aus dem vorherigen Schritt übernommen. Ersetzen Sie **localhost:5000** durch den Wert für **Anmeldeserver** aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. Die endgültige Zeichenfolge sieht wie folgt aus: \<registry name\>.azurecr.io/CSharpFunction. |

   ![Bereitstellen eines Docker-Imagerepositorys](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen.

Die IoT Edge-Erweiterung versucht, Ihre Anmeldeinformationen für die Containerregistrierung per Pullvorgang aus Azure abzurufen und in die Umgebungsdatei einzufügen. Überprüfen Sie, ob Ihre Anmeldeinformationen bereits enthalten sind. Fügen Sie sie jetzt hinzu, wenn dies nicht der Fall ist:

1. Öffnen Sie im VS Code-Explorer die ENV-Datei.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie diese Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit C#-Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“.

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus.

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei.

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

Fügen Sie einigen zusätzlichen Code hinzu, damit das Modul die Nachrichten im Edge-Bereich verarbeitet, bevor es sie an IoT Hub weiterleitet.

1. Öffnen Sie in Visual Studio Code **Module** > **CSharpFunction** > **CSharpFunction.cs**.

1. Ersetzen Sie den Inhalt der Datei **CSharpFunction.cs** durch den folgenden Code. Dieser Code empfängt Telemetriedaten zur Umgebungs- und Gerätetemperatur und leitet die Nachricht nur dann an IoT Hub weiter, wenn die Gerätetemperatur über einem festgelegten Schwellenwert liegt.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Speichern Sie die Datei .

## <a name="build-and-push-your-iot-edge-solution"></a>Erstellen und Pushen Ihrer IoT Edge-Projektmappe

Im vorherigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und **CSharpFunction** geändert, um Nachrichten herauszufiltern, bei denen die gemeldete Gerätetemperatur unter dem zulässigen Schwellenwert liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

1. Öffnen Sie das in VS Code integrierte Terminal über **Ansicht** > **Terminal**.

2. Melden Sie sich bei Docker an. Geben Sie dazu den nachstehenden Befehl im Terminal ein. Melden Sie sich mit dem Benutzernamen, Kennwort und Anmeldeserver aus Ihrer Azure-Containerregistrierung an. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Möglicherweise wird Ihnen in einem Sicherheitshinweis die Verwendung von `--password-stdin` empfohlen. Diese bewährte Methode wird für Produktionsszenarien empfohlen, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Referenz.

3. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und wählen Sie **Build and Push IoT Edge Solution** (IoT Edge-Projektmappe erstellen und übertragen) aus.

   Der Befehl zum Erstellen und Übertragen per Push startet drei Vorgänge. Zuerst erstellt er in der Projektmappe einen neuen Ordner mit dem Namen **config**. Darin ist das vollständige Bereitstellungsmanifest gespeichert, das aus Informationen in der Bereitstellungsvorlage und anderen Projektmappendateien erstellt wurde. Danach führt er `docker build` zum Erstellen des Containerimages aus, das auf der entsprechenden Dockerfile-Datei für Ihre Zielarchitektur basiert. Und schließlich führt er `docker push` aus, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal einige Minuten dauern, aber er ist bei der nächsten Ausführung von Befehlen schon schneller.

## <a name="view-your-container-image"></a>Anzeigen des Containerimages

Visual Studio Code gibt eine Erfolgsmeldung aus, wenn das Containerimage an die Containerregistrierung übertragen wurde. Wenn Sie selbst überprüfen möchten, ob die Ausführung erfolgreich war, können Sie das Image in der Registrierung anzeigen.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure-Containerregistrierung.
2. Wählen Sie **Repositorys** aus.
3. Die Liste sollte das Repository **csharpfunction** enthalten. Wählen Sie dieses Repository aus, um weitere Details anzuzeigen.
4. Im Abschnitt **Tags** sollte das Tag **0.0.1-amd64** angezeigt werden. Dieses Tag gibt die Version und die Plattform des erstellten Images an. Diese Werte sind in der Datei „module.json“ im Ordner „CSharpFunction“ festgelegt.

## <a name="deploy-and-run-the-solution"></a>Bereitstellen und Ausführen der Projektmappe

Sie können das Azure-Portal verwenden, um Ihr Funktionsmodul wie in den Schnellstartanleitungen auf einem IoT Edge-Gerät bereitzustellen. Sie können Module aber auch über Visual Studio Code bereitstellen und überwachen. In den folgenden Abschnitten werden die Azure IoT-Tools für VS Code verwendet, die als erforderliche Komponente aufgeführt sind. Sofern Sie die Erweiterung noch nicht installiert haben, holen Sie dies jetzt nach.

1. Erweitern Sie im Visual Studio Code-Explorer im Abschnitt **Azure IoT Hub** den Bereich **Geräte**, um Ihre IoT-Geräteliste anzuzeigen.

2. Klicken Sie mit der rechten Maustaste auf den Namen Ihres IoT Edge-Geräts, und wählen Sie dann **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen) aus.

3. Navigieren Sie zum Projektmappenordner mit **CSharpFunction**. Öffnen Sie den Konfigurationsordner, und wählen Sie die Datei **deployment.amd64.json** und anschließend **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen) aus.

4. Erweitern Sie unter Ihrem Gerät den Bereich **Module**, um eine Liste mit bereitgestellten und ausgeführten Modulen anzuzeigen. Klicken Sie auf die Schaltfläche „Aktualisieren“. Nun sollte das neue **CSharpFunction**-Modul zusammen mit dem **SimulatedTemperatureSensor**-Modul sowie mit **$edgeAgent** und **$edgeHub** ausgeführt werden.

    Es kann etwas dauern, bis die neuen Module angezeigt werden. Ihr IoT Edge-Gerät muss die neuen Bereitstellungsinformationen von IoT Hub abrufen, die neuen Container starten und dann den Status an IoT Hub zurückmelden.

   ![Anzeigen der bereitgestellten Module in VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Anzeigen der generierten Daten

Sie können alle bei der IoT Hub-Instanz eingehenden Nachrichten anzeigen, indem Sie in der Befehlspalette **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) auswählen.

Sie können die Ansicht auch filtern, um alle Nachrichten anzuzeigen, die von einem bestimmten Gerät an Ihre IoT Hub-Instanz gesendet werden. Klicken Sie im Abschnitt **Azure IoT Hub-Geräte** mit der rechten Maustaste auf das Gerät, und wählen Sie **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

Wenn Sie die Nachrichtenüberwachung beenden möchten, führen Sie in der Befehlspalette den Befehl **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts beenden) aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Azure-Funktionsmodul mit Code zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert werden.

In den nächsten Tutorials erfahren Sie, wie Ihnen Azure IoT Edge noch dabei helfen kann, Daten in geschäftliche Erkenntnisse auf Edge-Ebene zu verwandeln.

> [!div class="nextstepaction"]
> [Bereitstellen von Azure Stream Analytics als IoT Edge-Modul (Vorschauversion)](tutorial-deploy-stream-analytics.md)
