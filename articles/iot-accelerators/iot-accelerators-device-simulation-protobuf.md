---
title: Verwenden von Protocol Buffers mit Gerätesimulation – Azure | Microsoft-Dokumentation
description: In dieser Schrittanleitung erfahren Sie, wie Sie mithilfe von Protocol Buffers die vom Solution Accelerator für Gerätesimulation gesendeten Telemetriedaten serialisieren.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852149"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialisieren von Telemetriedaten mithilfe von Protocol Buffers

Protocol Buffers (Protobuf) ist ein binäres Serialisierungsformat für strukturierte Daten. Wesentliche Entwurfskriterien von Protobuf sind Einfachheit und Leistung mit dem Ziel, kleiner und schneller zu sein als XML.

Die Gerätesimulation unterstützt Version **proto3** der Protocol Buffers-Sprache.

Da für Protobuf kompilierter Code zum Serialisieren der Daten erforderlich ist, müssen Sie eine benutzerdefinierte Version der Gerätesimulation erstellen.

Dies wird in den Schritten in dieser Anleitung erläutert:

1. Vorbereiten einer Entwicklungsumgebung
1. Angeben der Verwendung des Protobuf-Formats in einem Gerätemodell
1. Definieren des Protobuf-Formats
1. Generieren von Protobuf-Klassen
1. Lokales Testen

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Schritte in dieser Anleitung ist Folgendes erforderlich:

* Visual Studio Code. Sie können [Visual Studio Code für Mac, Linux und Windows](https://code.visualstudio.com/download) herunterladen.
* .NET Core. Sie können [.NET Core für Mac, Linux und Windows](https://www.microsoft.com/net/download) herunterladen.
* Postman. Sie können [Postman für Mac, Windows oder Linux](https://www.getpostman.com/apps) herunterladen.
* Einen [IoT Hub, der für Ihr Azure-Abonnement bereitgestellt wurde](../iot-hub/iot-hub-create-through-portal.md). Sie benötigen die Verbindungszeichenfolge des IoT Hubs zum Ausführen der Schritte in dieser Anleitung. Sie können diese Verbindungszeichenfolge aus dem Azure-Portal abrufen.
* Eine [in Ihrem Azure-Abonnement bereitgestellte Cosmos DB-Datenbank](../cosmos-db/create-sql-api-dotnet.md#create-account), die die SQL-API verwendet und für [starke Konsistenz](../cosmos-db/how-to-manage-database-account.md) konfiguriert wurde. Sie benötigen die Verbindungszeichenfolge der Cosmos DB-Datenbank zum Ausführen der Schritte in dieser Anleitung. Sie können diese Verbindungszeichenfolge aus dem Azure-Portal abrufen.
* Ein [in Ihrem Azure-Abonnement bereitgestelltes Azure Storage-Konto](../storage/common/storage-account-create.md). Sie benötigen die Verbindungszeichenfolge des Speicherkontos zum Ausführen der Schritte in dieser Anleitung. Sie können diese Verbindungszeichenfolge aus dem Azure-Portal abrufen.

## <a name="prepare-your-development-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung

Führen Sie die folgenden Aufgaben aus, um Ihre Entwicklungsumgebung vorzubereiten:

* Laden Sie die Quelle für den Microservice "Gerätesimulation" herunter.
* Laden Sie die Quelle für den Microservice "Speicheradapter" herunter.
* Führen Sie den Microservice "Speicheradapter" lokal aus.

Bei den Anweisungen in diesem Artikel wird angenommen, dass Sie Windows verwenden. Wenn Sie ein anderes Betriebssystem verwenden, müssen Sie möglicherweise einige der Dateipfade und Befehle an Ihre Umgebung anpassen.

### <a name="download-the-microservices"></a>Herunterladen der Microservices

Laden Sie die [Microservices für die Remoteüberwachung](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) von GitHub an einen geeigneten Speicherort auf dem lokalen Computer herunter, und entzippen Sie sie dort. Dieses Repository enthält den für diese Anleitung benötigten Microservice für Speicheradapter.

Laden Sie den [Microservice "Gerätesimulation"](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) von GitHub an einen geeigneten Speicherort auf Ihrem lokalen Computer herunter, und entzippen Sie ihn dort.

### <a name="run-the-storage-adapter-microservice"></a>Ausführen des Microservices "Speicheradapter"

Öffnen Sie in Visual Studio Code den Ordner **remote-monitoring-services-dotnet-master\storage-adapter**. Klicken Sie auf eine der Schaltflächen **Wiederherstellen**, um nicht aufgelöste Abhängigkeiten zu beheben.

Öffnen Sie die Datei **.vscode/launch.json**, und weisen Sie Ihre Cosmos DB-Verbindungszeichenfolge der Umgebungsvariablen **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zu.

> [!NOTE]
> Auch wenn Sie den Microservice lokal auf Ihrem Computer ausführen, ist für eine ordnungsgemäße Ausführung dennoch eine Cosmos DB-Instanz in Azure erforderlich.

Um den Microservice für Speicheradapter lokal auszuführen, klicken Sie auf **Debuggen \> Debuggen starten**.

Im Fenster **Terminal** in Visual Studio Code wird die Ausgabe aus dem ausgeführten Microservice angezeigt, einschließlich einer URL für die Webdienst-Integritätsprüfung: <http://127.0.0.1:9022/v1/status>. Wenn Sie zu dieser Adresse navigieren, sollte der Status „OK: aktiv und gut“ lauten.

Führen Sie den Microservice für Speicheradapter weiterhin in dieser Instanz von Visual Studio Code aus, während Sie die folgenden Schritte ausführen.

## <a name="define-your-device-model"></a>Definieren des Gerätemodells

Öffnen Sie den von GitHub heruntergeladenen Ordner **device-simulation-dotnet-master** in einer neuen Instanz von Visual Studio Code. Klicken Sie auf eine der **Wiederherstellen**-Schaltflächen, um nicht aufgelöste Abhängigkeiten zu beheben.

In dieser Schrittanleitung erstellen Sie ein neues Gerätemodell für „Asset Tracker“:

1. Erstellen Sie die neue Gerätemodelldatei **assettracker-01.json** im Ordner **Services\data\devicemodels**.

1. Definieren Sie die Gerätefunktionen in der Gerätemodelldatei **assettracker-01.json**. Für den Abschnitt „Telemetry“ eines Protobuf-Gerätemodells gilt Folgendes:

   * Er muss den Namen der Protobuf-Klasse enthalten, den Sie für das Gerät generieren. Im folgenden Abschnitt wird erläutert, wie Sie diese Klasse generieren.
   * In diesem Abschnitt muss Protobuf als Nachrichtenformat angegeben werden.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Erstellen des Verhaltensskripts für das Gerät

Schreiben Sie das Verhaltensskript, das das Verhalten des Geräts definiert. Weitere Informationen finden Sie unter [Erstellen eines erweiterten simulierten Geräts](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definieren des Protobuf-Formats

Wenn Sie ein Gerätemodell definiert und das Nachrichtenformat festgelegt haben, können Sie eine **proto**-Datei erstellen. In der **proto**-Datei fügen Sie Folgendes hinzu:

* Einen `csharp_namespace`, der mit der Eigenschaft **ClassName** in Ihrem Gerätemodell übereinstimmt.
* Eine Nachricht für jede zu serialisierende Datenstruktur.
* Einen Namen und Typ für jedes Feld in der Nachricht.

1. Erstellen Sie eine neue Datei mit dem Namen **assettracker.proto** im Ordner **Services\Models\Protobuf\proto**.

1. Definieren Sie in der **proto**-Datei wie folgt die Syntax, den Namespace und das Nachrichtenschema:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

Die Marker `=1` und `=2` für jedes Element geben ein eindeutiges Tag an, das im Feld in der binären Codierung verwendet wird. Die Zahlen 1 bis 15 erfordern ein Byte weniger für die Codierung als höhere Zahlen.

## <a name="generate-the-protobuf-class"></a>Generieren der Protobuf-Klasse

Nachdem Sie eine **proto**-Datei erstellt haben, generieren Sie im nächsten Schritt die zum Lesen und Schreiben von Nachrichten erforderlichen Klassen. Für diesen Schritt benötigen Sie den Protobuf-Compiler **Protoc**.

1. [Laden Sie den Protobuf-Compiler von GitHub herunter](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip).

1. Führen Sie den Compiler aus, und geben Sie dabei das Quellverzeichnis, das Zielverzeichnis und den Namen Ihrer **proto**-Datei an. Beispiel:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Mit diesem Befehl wird die Datei **Assettracker.cs** im Ordner **Services\Models\Protobuf** generiert.

## <a name="test-protobuf-locally"></a>Lokales Testen von Protobuf

In diesem Abschnitt testen Sie das in den vorherigen Abschnitten erstellte Gerät „Asset Tracker“ lokal.

### <a name="run-the-device-simulation-microservice"></a>Ausführen des Microservices „Gerätesimulation“

Öffnen Sie die Datei **.vscode/launch.json**, und nehmen Sie folgende Zuweisungen vor:

* Weisen Sie die IoT Hub-Verbindungszeichenfolge der Umgebungsvariable **PCS\_IOTHUB\_CONNSTRING** zu.
* Weisen Sie die Verbindungszeichenfolge des Speicherkontos der Umgebungsvariable **PCS\_AZURE\_STORAGE\_ACCOUNT** zu.
* Weisen Sie die Cosmos DB-Verbindungszeichenfolge der Umgebungsvariable **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zu.

Öffnen Sie die Datei **WebService/Properties/launchSettings.json**, und nehmen Sie folgende Zuweisungen vor:

* Weisen Sie die IoT Hub-Verbindungszeichenfolge der Umgebungsvariable **PCS\_IOTHUB\_CONNSTRING** zu.
* Weisen Sie die Verbindungszeichenfolge des Speicherkontos der Umgebungsvariable **PCS\_AZURE\_STORAGE\_ACCOUNT** zu.
* Weisen Sie die Cosmos DB-Verbindungszeichenfolge der Umgebungsvariable **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zu.

Öffnen Sie die Datei **WebService\appsettings.ini**, und ändern Sie die Einstellungen wie folgt:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurieren der Lösung mit den neuen Gerätemodelldateien

Standardmäßig werden die JSON- und JS-Dateien des neuen Gerätemodells nicht automatisch in die erstellte Lösung kopiert. Sie müssen sie explizit einfügen.

Fügen Sie der Datei **services\services.csproj** einen Eintrag für jede Datei hinzu, die enthalten sein soll. Beispiel:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Um den Microservice lokal auszuführen, klicken Sie auf **Debuggen \> Debuggen starten**.

Das Fenster **Terminal** in Visual Studio Code zeigt die Ausgabe aus dem laufenden Microservice.

Lassen Sie den Microservice "Gerätesimulation" weiterhin in dieser Instanz von Visual Studio Code ausführen, während Sie die nächsten Schritte erledigen.

### <a name="set-up-a-monitor-for-device-events"></a>Einrichten einer Überwachung für Geräteereignisse

In diesem Abschnitt verwenden Sie die Azure-Befehlszeilenschnittstelle, um eine Ereignisüberwachung zum Anzeigen der Telemetriedaten einzurichten, die von den mit Ihrem IoT Hub verbundenen Geräten gesendet wurden.

Bei dem nachstehenden Skript wird angenommen, dass der Name Ihres Hubs **device-simulation-test** lautet.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lassen Sie die Ereignisüberwachung weiterhin ausführen, während Sie die simulierten Geräte testen.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Erstellen einer Simulation mit dem Gerätetyp „Asset Tracker“

In diesem Abschnitt verwenden Sie das Tool Postman, um den Microservice für Gerätesimulation zum Ausführen einer Simulation mit dem Gerätetyp „Asset Tracker“ anzufordern. Bei Postman handelt es sich um ein Tool, mit dem Sie REST-Anforderungen an einen Webdienst senden können.

So richten Sie Postman ein:

1. Öffnen Sie Postman auf Ihrem lokalen Computer.

1. Klicken Sie auf **File (Datei) \> Import (Importieren)** . Klicken Sie dann auf **Dateien auswählen**.

1. Wählen Sie **Azure IoT Device Simulation solution accelerator.postman\_collection** und **Azure IoT Device Simulation solution accelerator.postman\_environment** aus, und klicken Sie auf **Open** (Öffnen).

1. Erweitern Sie **Azure IoT Device Simulation solution accelerator** (Solution Accelerator für Azure IoT-Gerätesimulation), um die Anforderungen anzuzeigen, die Sie senden können.

1. Klicken Sie auf **No Environment** (Keine Umgebung), und wählen Sie **Azure IoT Device Simulation solution accelerator** (Solution Accelerator für Azure IoT-Gerätesimulation) aus.

Nun haben Sie eine Sammlung und eine Umgebung in Ihren Postman-Arbeitsbereich geladen, die Sie zum Interagieren mit dem Microservice "Gerätesimulation" verwenden können.

So können Sie die Simulation konfigurieren und ausführen:

1. Wählen Sie in der Postman-Sammlung **Create asset tracker simulation** (Asset Tracker-Simulation erstellen) aus, und klicken Sie auf **Send** (Senden). Mit dieser Anforderung werden vier Instanzen des simulierten Gerätetyps „Asset Tracker“ erstellt.

1. In der Ausgabe der Ereignisüberwachung im Azure CLI-Fenster werden die Telemetriedaten aus den simulierten Geräten angezeigt.

Um die Simulation zu beenden, wählen Sie in Postman die Anforderung **Simulation beenden** aus, und klicken Sie auf **Senden**.

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die beiden lokal ausgeführten Microservices in den zugehörigen Visual Studio Code-Instanzen beenden (**Debuggen \> Debuggen beenden**).

Wenn Sie die Instanzen von IoT Hub und Cosmos DB nicht mehr benötigen, löschen Sie sie aus Ihrem Azure-Abonnement, um unnötige Gebühren zu vermeiden.

## <a name="iot-hub-support"></a>IoT Hub-Unterstützung

Viele IoT Hub-Features unterstützen Protobuf oder andere binäre Formate nicht nativ. Beispielsweise können Sie keine Weiterleitung basierend auf der Nachrichtennutzlast durchführen, da die Nachrichtennutzlast in IoT Hub nicht verarbeitet werden kann. Eine Weiterleitung basierend auf Nachrichtenheadern ist dagegen möglich.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie gelernt haben, wie Sie die Gerätesimulation so anpassen, dass Telemetriedaten mithilfe von Protobuf gesendet werden, informieren Sie sich im nächsten Schritt im GitHub-Repository über weitere [Gerätesimulationen](https://github.com/Azure/device-simulation-dotnet).
