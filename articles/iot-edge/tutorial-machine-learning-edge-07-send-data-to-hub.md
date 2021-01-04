---
title: 'Tutorial: Senden von Gerätedaten per transparentem Gateway: Machine Learning in Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie Ihren Entwicklungscomputer als simuliertes IoT Edge-Gerät zum Senden von Daten an die IoT Hub-Instanz verwenden, indem Sie ein als transparentes Gateway konfiguriertes Gerät verwenden.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 50df3424892594a6817d481aa4a3d540a342854f
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932318"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Tutorial: Senden von Daten per transparentem Gateway

In diesem Artikel verwenden wir wieder die Entwicklungs-VM als simuliertes Gerät. Anstatt Daten direkt an IoT Hub zu senden, sendet das Gerät aber Daten an das IoT Edge-Gerät, das als transparentes Gateway konfiguriert ist.

Wir überwachen den Vorgang auf dem IoT Edge-Gerät, während das simulierte Gerät Daten sendet. Nachdem die Ausführung des Geräts abgeschlossen ist, sehen wir uns die Daten in unserem Speicherkonto an, um sicherzustellen, dass alles wie erwartet funktioniert hat.

Dieser Schritt wird normalerweise von einem Cloud- oder Geräteentwickler ausgeführt.

In diesem Abschnitt des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen und Ausführen eines Blattgeräts
> * Überprüfen, ob generierte Daten in Azure Blob Storage gespeichert werden
> * Überprüfen, ob die Gerätedaten vom Machine Learning-Modell klassifiziert wurden

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist Teil einer Tutorialreihe zur Verwendung von Azure Machine Learning für IoT Edge. Jeder Artikel in der Reihe baut auf der Arbeit im vorherigen Artikel auf. Wenn Sie diesen Artikel direkt aufgerufen haben, wechseln Sie zum [ersten Artikel](tutorial-machine-learning-edge-01-intro.md) in der Reihe.

## <a name="review-device-harness"></a>Überprüfen der Geräteumgebung

Verwenden Sie wieder das [DeviceHarness-Projekt](tutorial-machine-learning-edge-03-generate-data.md), um das nachgeschaltete Gerät (Blattgerät) zu simulieren. Zum Herstellen einer Verbindung mit dem transparenten Gateway sind zwei weitere Dinge erforderlich:

* Registrieren Sie das Zertifikat, damit das nachgeschaltete IoT-Gerät der Zertifizierungsstelle vertraut, die von der IoT Edge-Runtime verwendet wird. In unserem Fall ist das nachgeschaltete Gerät die Entwicklungs-VM.
* Fügen Sie den vollqualifizierten Domänennamen (FQDN) des Edge-Gateways der Geräteverbindungszeichenfolge hinzu.

Sehen Sie sich den Code an, um zu ermitteln, wie diese beiden Elemente implementiert werden.

1. Öffnen Sie Visual Studio Code auf Ihrem Entwicklungscomputer.

1. Verwenden Sie **Datei** > **Ordner öffnen...** , um „C:\\source\\IoTEdgeAndMlSample\\DeviceHarness“ zu öffnen.

1. Sehen Sie sich in „Program.cs“ die InstallCertificate()-Methode an.

1. Beachten Sie Folgendes: Wenn der Code den Zertifikatpfad findet, wird die CertificateManager.InstallCACert-Methode aufgerufen, um das Zertifikat auf dem Computer zu installieren.

1. Sehen Sie sich nun die GetIotHubDevice-Methode in der TurbofanDevice-Klasse an.

1. Wenn der Benutzer den FQDN des Gateways mit der Option „-g“ angibt, wird dieser Wert als Variable `gatewayFqdn` an diese Methode übergeben, damit er an die Geräteverbindungszeichenfolge angefügt werden kann.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Erstellen und Ausführen eines Blattgeräts

1. Kompilieren Sie das Projekt, während das Projekt „DeviceHarness“ in Visual Studio Code noch geöffnet ist. Wählen Sie im Menü **Terminal** die Option **Buildtask ausführen** und dann **Build** aus.

1. Suchen Sie nach dem vollqualifizierten Domänennamen (FQDN) für Ihr Edge-Gateway, indem Sie im Azure-Portal zu Ihrem IoT Edge-Gerät (Linux-VM) navigieren und den Wert für **DNS-Name** von der Übersichtsseite kopieren.

1. Starten Sie Ihr IoT-Gerät (Linux-VM), falls es nicht bereits ausgeführt wird.

1. Öffnen Sie das Visual Studio Code-Terminal. Wählen Sie im Menü **Terminal** die Option **Neues Terminal** aus, und führen Sie den folgenden Befehl aus. Ersetzen Sie hierbei `<edge_device_fqdn>` durch den DNS-Namen, den Sie vom IoT Edge-Gerät (Linux-VM) kopiert haben:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. Die Anwendung versucht, das Zertifikat auf Ihrem Entwicklungscomputer zu installieren. Akzeptieren Sie den Sicherheitshinweis, wenn dieser Vorgang durchgeführt wird.

1. Klicken Sie bei Aufforderung zur Eingabe der IoT Hub-Verbindungszeichenfolge im Azure IoT Hub-Gerätebereich auf die Auslassungspunkte ( **...** ), und wählen Sie **Copy IoT Hub Connection String** (IoT Hub-Verbindungszeichenfolge kopieren) aus. Fügen Sie den Wert in das Terminal ein.

1. Die folgende Ausgabe wird angezeigt:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Beachten Sie den Zusatz „GatewayHostName“ der Geräteverbindungszeichenfolge, mit dem für das Gerät die Kommunikation per IoT-Hub über das transparente IoT Edge-Gateway ermöglicht wird.

## <a name="check-output"></a>Überprüfen der Ausgabe

### <a name="iot-edge-device-output"></a>Ausgabe des IoT Edge-Geräts

Sie können die Ausgabe des avroFileWriter-Moduls über das IoT Edge-Gerät verfolgen.

1. Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen IoT Edge-Computer her.

1. Suchen Sie nach Dateien, die auf Datenträger geschrieben werden.

   ```bash
   find /data/avrofiles -type f
   ```

1. Die Ausgabe des Befehls sieht wie im folgenden Beispiel aus:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Je nach Zeitpunkt der Ausführung ist ggf. mehr als eine Datei vorhanden.

1. Achten Sie auf die Zeitstempel. Das avroFileWriter-Modul lädt die Dateien in die Cloud hoch, sobald der letzte Änderungszeitpunkt länger als zehn Minuten zurückliegt (siehe „MODIFIED\_FILE\_TIMEOUT“ in der Datei „uploader.py“ im avroFileWriter-Modul).

1. Nach Ablauf von zehn Minuten sollten die Dateien vom Modul hochgeladen werden. Wenn der Upload erfolgreich ist, werden die Dateien vom Datenträger gelöscht.

### <a name="azure-storage"></a>Azure Storage

Wir können die Ergebnisse des Blattgeräts verfolgen, von dem die Daten gesendet werden, indem wir uns die Speicherkonten ansehen, an die die Daten voraussichtlich geleitet werden.

1. Öffnen Sie Visual Studio Code auf dem Entwicklungscomputer.

1. Suchen Sie im Explorer-Fenster in der Struktur unter „AZURE STORAGE“ nach Ihrem Speicherkonto.

1. Erweitern Sie den Knoten **Blobcontainer**.

1. Aufgrund der Schritte, die wir im vorherigen Teil des Tutorials ausgeführt haben, ist zu erwarten, dass der Container **ruldata** Nachrichten zur Restlebensdauer (RUL) enthält. Erweitern Sie den Knoten **ruldata**.

1. Es wird mindestens eine Blobdatei mit einem Namen im folgenden Format angezeigt: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

1. Klicken Sie mit der rechten Maustaste auf eine der Dateien, und wählen Sie **Blob herunterladen**, um die Datei auf Ihrem Entwicklungscomputer zu speichern.

1. Erweitern Sie als Nächstes den Knoten **uploadturbofanfiles**. Im vorherigen Artikel haben wir diesen Speicherort als Ziel für Dateien festgelegt, die vom avroFileWriter-Modul hochgeladen werden.

1. Klicken Sie mit der rechten Maustaste auf die Dateien, und wählen Sie **Blob herunterladen**, um sie auf Ihrem Entwicklungscomputer zu speichern.

### <a name="read-avro-file-contents"></a>Lesen von Avro-Dateiinhalten

Wir haben ein einfaches Befehlszeilenprogramm zum Lesen einer Avro-Datei und Zurückgeben einer JSON-Zeichenfolge der Nachrichten in der Datei bereitgestellt. In diesem Abschnitt installieren wir es und führen es anschließend aus.

1. Öffnen Sie in Visual Studio Code ein Terminal (**Terminal** > **Neues Terminal**).

1. Installation von „hubavroreader“:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Verwenden Sie „hubavroreader“, um die Avro-Datei zu lesen, die Sie von **ruldata** heruntergeladen haben.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Sie sehen, dass der Text der Nachricht wie erwartet aussieht und über eine Geräte-ID und die vorhergesagte Restlebensdauer (RUL) verfügt.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Führen Sie denselben Befehl aus, um die Avro-Datei zu übergeben, die Sie von **uploadturbofanfiles** heruntergeladen haben.

1. Erwartungsgemäß enthalten diese Nachrichten alle Sensordaten und Betriebseinstellungen aus der ursprünglichen Nachricht. Diese Daten können verwendet werden, um das RUL-Modell auf unserem Edgegerät zu verbessern.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie die in diesem umfassenden Tutorial verwendeten Ressourcen weiter erkunden möchten, sollten Sie mit dem Bereinigen der von Ihnen erstellten Ressourcen warten, bis Sie dies getan haben. Führen Sie andernfalls die folgenden Schritte aus, um sie zu löschen:

1. Löschen Sie die erstellten Ressourcengruppen für den virtuellen Entwicklungscomputer, den virtuellen IoT Edge-Computer, das Speicherkonto und den Machine Learning Service-Arbeitsbereich (und die erstellten Ressourcen „Containerregistrierung“, „Application Insights“, „Schlüsseltresor“ und „Speicherkonto“).

1. Löschen Sie das Machine Learning-Projekt in [Azure Notebooks](https://notebooks.azure.com).

1. Gehen Sie wie folgt vor, wenn Sie das Repository lokal geklont haben: Schließen Sie alle PowerShell- oder VS Code-Fenster, in denen auf das lokale Repository verwiesen wird, und löschen Sie anschließend das Repositoryverzeichnis.

1. Wenn Sie die Zertifikate lokal erstellt haben, müssen Sie den Ordner „C:\\edgeCertificates“ löschen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir unsere Entwicklungs-VM verwendet, um ein Blattgerät zu simulieren, das Sensor- und Betriebsdaten an unser IoT Edge-Gerät sendet. Wir haben überprüft, ob die Module auf dem Gerät die Daten weitergeleitet, klassifiziert, gespeichert und hochgeladen haben, indem wir uns den Echtzeitbetrieb des Edge-Geräts und die Dateien angesehen haben, die in das Speicherkonto hochgeladen wurden.

Wenn Sie mehr über IoT Edge-Funktionen erfahren möchten, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
> [Erstellen einer Hierarchie für IoT Edge-Geräte (Vorschauversion)](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)