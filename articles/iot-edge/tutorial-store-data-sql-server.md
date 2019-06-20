---
title: 'Tutorial: Speichern von Daten mit dem SQL-Modul in Azure IoT Edge | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mit einem SQL Server-Modul Daten lokal auf Ihrem IoT Edge-Gerät speichern.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 86aab19eb0203e75fb8586adbdeb3f6fff9d14bd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575449"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Speichern von Daten im Edge-Bereich mit SQL Server-Datenbanken

Stellen Sie ein SQL Server-Modul zum Speichern von Daten auf einem Linux-Gerät bereit, auf dem Azure IoT Edge ausgeführt wird.

Verwenden Sie Azure IoT Edge und SQL Server, um Daten im Edge-Bereich zu speichern und abzufragen. Azure IoT Edge verfügt über grundlegende Speicherfunktionen, die Nachrichten zwischenspeichern, wenn ein Gerät offline geht, und weiterleiten, wenn die Verbindung wiederhergestellt wurde. Sie benötigen jedoch unter Umständen komplexere Speicherfunktionen, beispielsweise für die lokale Datenabfrage. Ihre IoT Edge-Geräte können lokale Datenbanken verwenden, um komplexere Computevorgänge auszuführen, ohne dass eine Verbindung mit IoT Hub bestehen muss. 

Dieser Artikel enthält Anweisungen zum Bereitstellen einer SQL Server-Datenbank auf einem IoT Edge-Gerät. Mithilfe von Azure Functions, das auf dem IoT Edge-Gerät ausgeführt wird, werden die eingehenden Daten strukturiert und dann an die Datenbank gesendet. Die Schritte in diesem Artikel können auch auf andere Datenbanken angewendet werden, die in Containern ausgeführt werden, z. B. MySQL oder PostgreSQL.

In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]
> * Erstellen einer Azure-Funktion mit Visual Studio Code
> * Bereitstellen einer SQL-Datenbank auf Ihrem IoT Edge-Gerät
> * Erstellen und Bereitstellen von Modulen auf Ihrem IoT Edge-Gerät mithilfe von Visual Studio Code
> * Anzeigen generierter Daten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie das vorhergehende Tutorial durchgearbeitet haben, um Ihre Entwicklungsumgebung für die Entwicklung von Linux-Containern einzurichten: [Entwickeln von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md). Nach Abschluss dieses Tutorials sollten Sie die folgenden Voraussetzungen eingerichtet haben: 

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein [Linux-Gerät, auf dem Azure IoT Edge ausgeführt wird](quickstart-linux.md).
* Eine Containerregistrierung wie [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/), der mit den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) konfiguriert wurde.
* [Docker CE](https://docs.docker.com/install/), das zur Ausführung von Linux-Containern konfiguriert wurde.

In diesem Tutorial wird ein Azure Functions-Modul verwendet, um Daten an den SQL Server zu senden. Um ein IoT Edge-Modul mit Azure Functions zu entwickeln, installieren Sie die folgenden zusätzlichen Voraussetzungen auf Ihrem Entwicklungscomputer: 

* [C#-Erweiterung für Visual Studio Code (unterstützt von OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Erstellen eines Funktionsprojekts

Zum Senden von Daten an eine Datenbank benötigen Sie ein Modul, das die Daten entsprechend strukturieren kann und dann in einer Tabelle speichert. 

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Die folgenden Schritte zeigen, wie Sie mithilfe von Visual Studio Code und der Azure IoT Edge-Tools eine IoT Edge-Funktion erstellen.

1. Öffnen Sie Visual Studio Code.

2. Öffnen Sie die VS Code-Befehlspalette, indem Sie auf **Ansicht** > **Befehlspalette** klicken.

3. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge: New IoT Edge Solution** ein, und führen Sie ihn aus. Geben Sie in der Befehlspalette die folgenden Informationen an, um die Projektmappe zu erstellen: 

   | Feld | Wert |
   | ----- | ----- |
   | Ordner auswählen | Wählen Sie den Speicherort auf Ihrem Entwicklungscomputer aus, an dem Visual Studio Code die Projektmappendateien erstellen soll. |
   | Provide a solution name (Projektmappennamen angeben) | Geben Sie für Ihre Projektmappe einen aussagekräftigen Namen ein (beispielsweise **SqlSolution**), oder übernehmen Sie den Standardnamen. |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Azure Functions – C#** aus. |
   | Provide a module name (Modulname angeben) | Nennen Sie das Modul **sqlFunction**. |
   | Provide Docker image repository for the module (Docker-Imagerepository für das Modul angeben) | Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Ihr Containerimage wird aus dem vorherigen Schritt übernommen. Ersetzen Sie **localhost:5000** durch den Anmeldeserverwert aus Ihrer Azure-Containerregistrierung. Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln. <br><br>Die endgültige Zeichenfolge sieht wie folgt aus: „\<Registrierungsname\>.azurecr.io/sqlFunction“. |

   Das VS Code-Fenster lädt den Arbeitsbereich für Ihre IoT Edge-Projektmappe. 
   
### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Die Umgebungsdatei speichert die Anmeldeinformationen für Ihre Containerregistrierung und gibt sie an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen.

1. Öffnen Sie im VS Code-Explorer die ENV-Datei.
2. Aktualisieren Sie die Felder mit den Werten für **Benutzername** und **Kennwort**, die Sie aus der Azure-Containerregistrierung kopiert haben.
3. Speichern Sie diese Datei.

### <a name="select-your-target-architecture"></a>Auswählen Ihrer Zielarchitektur

Mit Visual Studio Code können derzeit C#-Module für Linux AMD64- und Linux ARM32v7-Geräte entwickelt werden. Sie müssen bei jeder Projektmappe auswählen, welche Architektur Sie als Ziel verwenden möchten, weil der Container für jeden Architekturtyp unterschiedlich erstellt und ausgeführt wird. Der Standardwert ist „Linux AMD64“. 

1. Öffnen Sie die Befehlspalette, und suchen Sie nach **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Standardzielplattform für Edge-Projektmappe festlegen), oder wählen Sie das Verknüpfungssymbol in der Seitenleiste unten im Fenster aus. 

2. Wählen Sie in der Befehlspalette die Zielarchitektur aus der Liste mit Optionen aus. Weil in diesem Tutorial ein virtueller Ubuntu-Computer als IoT Edge-Gerät verwendet wird, behalten Sie den Standardwert **amd64** bei. 

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

1. Öffnen Sie im VS Code-Explorer **Module** > **sqlFunction** > **sqlFunction.cs**.

2. Ersetzen Sie den gesamten Inhalt der Datei durch den folgenden Code:

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
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

3. Ersetzen Sie in Zeile 35 die Zeichenfolge **\<sql connection string\>** durch die folgende Zeichenfolge. Die Eigenschaft **Data Source** verweist auf den SQL Server-Container. Dieser ist noch nicht vorhanden, wird aber im nächsten Abschnitt mit dem Namen **SQL** erstellt. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Speichern Sie die Datei **sqlFunction.cs**. 

5. Öffnen Sie die Datei **sqlFunction.csproj**.

6. Suchen Sie nach der Gruppe der Paketverweise, und fügen Sie einen neuen Verweis hinzu, um „SqlClient“ einzuschließen. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Speichern Sie die Datei **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Hinzufügen des SQL Server-Containers

In einem [Bereitstellungsmanifest](module-composition.md) wird deklariert, welche Module von der IoT Edge-Runtime auf dem IoT Edge-Gerät installiert werden. Im vorherigen Abschnitt haben Sie den Code zum Erstellen eines angepassten Functions-Moduls bereitgestellt, das SQL Server-Modul wurde jedoch bereits erstellt und steht im Azure Marketplace zur Verfügung. Sie müssen lediglich die IoT Edge-Runtime anweisen, es aufzunehmen, und es dann auf Ihrem Gerät konfigurieren. 

1. Öffnen Sie in Visual Studio Code die Befehlspalette, indem Sie **Ansicht** > **Befehlspalette** auswählen.

2. Geben Sie in der Befehlspalette den Befehl **Azure IoT Edge ein: IoT Edge-Modul hinzufügen** ein. Geben Sie in der Befehlspalette folgende Informationen an, um ein neues Modul hinzuzufügen: 

   | Feld | Wert | 
   | ----- | ----- |
   | Select deployment template file (Bereitstellungsvorlagendatei auswählen) | Die Befehlspalette hebt die Datei „deployment.template.json“ in Ihrem aktuellen Projektmappenordner hervor. Wählen Sie diese Datei aus.  |
   | Select module template (Modulvorlage auswählen) | Wählen Sie **Module from Azure Marketplace** (Modul aus dem Azure Marketplace) aus. |

3. Suchen Sie im Marketplace für Azure IoT Edge-Module nach dem **SQL Server-Modul**, und wählen Sie es aus. 

4. Ändern Sie den Namen des Moduls in **sql** (klein geschrieben). Dieser Name entspricht dem Containernamen, der in der Verbindungszeichenfolge in der Datei „sqlFunction.cs“ deklariert wird. 

5. Wählen Sie **Importieren** aus, um das Modul Ihrer Projektmappe hinzuzufügen. 

6. Öffnen Sie in Ihrem Projektmappenordner die Datei **deployment.template.json**. 

7. Suchen Sie nach dem Abschnitt **modules**. Es sollten drei Module angezeigt werden. Das Modul *tempSensor* ist standardmäßig in neuen Projektmappen enthalten und stellt Testdaten für Ihre anderen Module bereit. Das Modul *sqlFunction* ist das Modul, das Sie erstellt und mit neuem Code aktualisiert haben. Das Modul *sql* wurde aus dem Azure Marketplace importiert. 

   >[!Tip]
   >Das SQL Server-Modul verfügt über ein Standardkennwort, das in den Umgebungsvariablen des Bereitstellungsmanifests festgelegt ist. Bei jedem Erstellen eines SQL Server-Containers in einer Produktionsumgebung sollten Sie [das Standardkennwort für den Systemadministrator ändern](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Schließen Sie die Datei **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Erstellen Ihrer IoT Edge-Projektmappe

In den vorherigen Abschnitten haben Sie eine Projektmappe mit einem Modul erstellt und anschließend ein weiteres Modul zur Bereitstellungsmanifestvorlage hinzugefügt. Das SQL Server-Modul wird öffentlich von Microsoft gehostet. Sie müssen den Code jedoch in Containern im Functions-Modul platzieren. In diesem Abschnitt erstellen Sie die Projektmappe sowie die Containerimages für das Modul „sqlFunction“ und übertragen die Images per Push an Ihre Containerregistrierung. 

1. Öffnen Sie das in Visual Studio Code integrierte Terminal, indem Sie **Ansicht** > **Terminal** auswählen.  

1. Melden Sie sich bei der Containerregistrierung in Visual Studio Code an, sodass Sie die Images per Push an die Registrierung übertragen können. Verwenden Sie die ACR-Anmeldeinformationen (Azure Container Registry), die Sie auch der ENV-Datei hinzugefügt haben. Geben Sie den folgenden Befehl in das integrierte Terminal ein:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Unter Umständen wird eine Sicherheitswarnung angezeigt, in der die Verwendung des Parameters „--password-stdin“ empfohlen wird. Obwohl in diesem Artikel nicht auf dessen Verwendung eingegangen werden kann, wird empfohlen, diese bewährte Methode anzuwenden. Weitere Informationen finden Sie in der [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)-Befehlsreferenz. 

2. Klicken Sie im VS Code-Explorer mit der rechten Maustaste auf die Datei **deployment.template.json**, und klicken Sie anschließend auf **Build and Push IoT Edge solution** (IoT Edge-Projektmappe erstellen und übertragen). 

Wenn Sie Visual Studio Code anweisen, die Projektmappe zu erstellen, wird zunächst basierend auf den Informationen in der Bereitstellungsvorlage eine Datei vom Typ „deployment.json“ in einem neuen Ordner namens **config** erstellt. Anschließend werden zwei Befehle im integrierten Terminal ausgeführt: `docker build` und `docker push`. Diese beiden Befehle erstellen Ihren Code, packen das Modul in Container und pushen den Code anschließend in die Containerregistrierung, die Sie beim Initialisieren der Projektmappe angegeben haben. 

Sie können sich vergewissern, dass das Modul „sqlFunction“ erfolgreich per Push an Ihre Containerregistrierung übertragen wurde. Navigieren Sie im Azure-Portal wieder zu Ihrer Containerregistrierung. Wählen Sie **Repositorys** aus, und suchen Sie nach **sqlFunction**. Die beiden anderen Module („tempSensor“ und „sql“) werden nicht per Push an Ihre Containerregistrierung übertragen, da Sie in den Microsoft-Registrierungen bereits auf deren Repositorys verweisen.

## <a name="deploy-the-solution-to-a-device"></a>Bereitstellen der Projektmappe auf einem Gerät

Sie können über IoT Hub Module auf einem Gerät festlegen, können jedoch auch über Visual Studio Code auf IoT Hub und Geräte zugreifen. In diesem Abschnitt richten Sie den Zugriff auf Ihre IoT Hub-Instanz ein und stellen dann mithilfe von VS Code Ihre Projektmappe auf dem IoT Edge-Gerät bereit. 

1. Erweitern Sie im VS Code-Explorer den Abschnitt **Azure IoT Hub-Geräte**. 

2. Klicken Sie mit der rechten Maustaste auf das Gerät, das als Ziel für die Bereitstellung festgelegt werden soll, und klicken Sie auf **Create deployment for single device** (Bereitstellung für einzelnes Gerät erstellen). 

3. Navigieren Sie im Datei-Explorer zum Ordner **config** der Projektmappe, und klicken Sie auf **deployment.amd64**. Klicken Sie auf **Select Edge Deployment Manifest** (Edge-Bereitstellungsmanifest auswählen). 

   Verwenden Sie die Datei „deployment.template.json“ nicht als Bereitstellungsmanifest.

War die Bereitstellung erfolgreich, wird in VS Code eine Bestätigungsnachricht ausgegeben. 

Aktualisieren Sie den Status Ihres Geräts im Abschnitt „Azure IoT Hub Devices“ (Azure IoT Hub-Geräte) von VS Code. Die neuen Module werden aufgeführt und in den nächsten Minuten als ausgeführt gemeldet, während die Container installiert und gestartet werden. Sie können auch überprüfen, ob alle Module auf dem Gerät ausgeführt werden. Führen Sie auf dem IoT Edge-Gerät den folgenden Befehl aus, um den Status der Module anzuzeigen. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Erstellen der SQL-Datenbank

Wenn Sie das Bereitstellungsmanifest auf Ihr Gerät anwenden, werden drei Module ausgeführt. Das Modul „tempSensor“ generiert simulierte Umgebungsdaten. Das Modul „sqlFunction“ formatiert die Daten für eine Datenbank. Dieser Abschnitt führt Sie durch das Einrichten der SQL-Datenbank zum Speichern der Temperaturdaten. 

Führen Sie auf Ihrem IoT Edge-Gerät die folgenden Befehle aus. Diese Befehle stellen eine Verbindung mit dem **sql**-Modul her, das auf Ihrem Gerät ausgeführt wird, und erstellt eine Datenbank und eine Tabelle zum Speichern der gesendeten Temperaturdaten. 

1. Stellen Sie in einem Befehlszeilentool auf Ihrem IoT Edge-Gerät eine Verbindung mit Ihrer Datenbank her. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Öffnen Sie das SQL-Befehlstool.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Erstellen Sie die Datenbank: 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Definieren Sie die Tabelle.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Sie können die SQL Server-Docker-Datei so anpassen, dass SQL Server automatisch für die Bereitstellung auf mehreren IoT Edge-Geräten eingerichtet wird. Weitere Informationen finden Sie im [Demoprojekt für einen Microsoft SQL Server-Container](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Anzeigen der lokalen Daten

Nach der Erstellung der Tabelle beginnt das Modul „sqlFunction“ mit dem Speichern von Daten in einer lokalen SQL Server 2017-Datenbank auf dem IoT Edge-Gerät. 

Führen Sie im SQL-Befehlstool den folgenden Befehl aus, um die formatierten Tabellendaten anzuzeigen: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Inhalt der lokalen Datenbank anzeigen](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen. 

Andernfalls können Sie die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Azure Functions-Modul erstellt, das Code zum Filtern von Rohdaten enthält, die von Ihrem IoT Edge-Gerät generiert wurden. Wenn Sie eigene Module erstellen möchten, finden Sie entsprechende Informationen unter [Use Visual Studio Code to develop and debug Azure Functions for Azure IoT Edge](how-to-develop-csharp-function.md) (Entwickeln und Debuggen von Azure Functions für Azure IoT Edge mithilfe von Visual Studio Code). 

Wenn Sie eine andere Speichermethode im Edge-Bereich testen möchten, lesen Sie Informationen zur Verwendung von Azure Blob Storage in IoT Edge. 

> [!div class="nextstepaction"]
> [Speichern von Daten im Edge-Bereich mit Azure Blob Storage in IoT Edge](how-to-store-data-blob.md)
