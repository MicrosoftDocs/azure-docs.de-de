---
title: Visualisierung von Echtzeitdaten Ihrer IoT Hub-Daten in einer Web-App
description: Verwenden Sie eine Webanwendung, um Daten zur Temperatur und Luftfeuchtigkeit zu visualisieren, die von einem Sensor erfasst und an Ihren IoT-Hub gesendet werden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 3218df4601ef7a5e1b1e04c20c89eefc367b720b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746453"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualisieren von Echtzeit-Sensordaten aus Azure IoT Hub in einer Webanwendung

![Lückenloses Diagramm](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Lerninhalt

In diesem Tutorial erfahren Sie, wie Sie vom IoT-Hub empfangene Echtzeit-Sensordaten mit einer Node.js-Web-App visualisieren, die auf Ihrem lokalen Computer ausgeführt wird. Nachdem Sie die Web-App lokal ausgeführt haben, können Sie optional weitere Schritte befolgen, um die Web-App in Azure App Service zu hosten. Wenn Sie versuchen möchten, die Daten in IoT Hub mit Power BI zu visualisieren, lesen Sie den Artikel [Verwenden von Power BI zum Visualisieren von Echtzeit-Sensordaten in Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Aufgaben

* Hinzufügen einer Consumergruppe zum IoT-Hub, den die Webanwendung zum Lesen von Sensordaten verwendet
* Herunterladen des Web-App-Codes von GitHub
* Überprüfen des Web-App-Codes
* Konfigurieren der Umgebungsvariablen zum Speichern der von der Web-App benötigten IoT-Hub-Artefakte
* Ausführen der Web-App auf dem Entwicklungscomputer
* Öffnen einer Webseite zum Anzeigen von Temperatur- und Luftfeuchtigkeitsdaten aus dem IoT-Hub in Echtzeit
* (Optional) Verwenden der Azure CLI zum Hosten Ihrer Web-App in Azure App Service

## <a name="what-you-need"></a>Voraussetzungen

* Sie müssen das Tutorial [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-web-simulator-get-started.md) oder eines der gerätespezifischen Tutorials wie [Verbinden von Raspberry Pi mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md) abgeschlossen haben. In diesen werden folgende Anforderungen beschrieben:

  * Ein aktives Azure-Abonnement
  * IoT Hub in Ihrem Abonnement
  * Eine Clientanwendung, die Nachrichten an IoT Hub sendet

* [Herunterladen von Git](https://www.git-scm.com/downloads)

* Bei den Schritten in diesem Artikel wird ein Windows-Entwicklungscomputer vorausgesetzt. Allerdings können Sie diese Schritte auch problemlos auf einem Linux-System in Ihrer bevorzugten Shell ausführen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

[Consumergruppen](../event-hubs/event-hubs-features.md#event-consumers) bieten unabhängige Ansichten des Ereignisstreams, die es Apps und Azure-Diensten ermöglichen, unabhängig voneinander Daten desselben Event Hub-Endpunkts zu nutzen. In diesem Abschnitt fügen Sie dem integrierten IoT-Hub-Endpunkt, aus dem die Web-App Daten liest, eine Consumergruppe hinzu.

Führen Sie den folgenden Befehl aus, um dem integrierten Endpunkt Ihres IoT-Hubs eine Consumergruppe hinzuzufügen:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Notieren Sie sich den verwendeten Namen, da Sie ihn später in diesem Tutorial benötigen.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Abrufen einer Dienst-Verbindungszeichenfolge für den IoT-Hub

IoT-Hubs werden mit verschiedenen Standardzugriffsrichtlinien erstellt. Eine dieser Richtlinien ist die **Dienstrichtlinie**. Sie gewährt Diensten ausreichende Berechtigungen, um Lese- und Schreibvorgänge in den Endpunkten des IoT-Hubs auszuführen. Führen Sie den folgenden Befehl aus, um eine Verbindungszeichenfolge für den IoT-Hub abzurufen, die die Bedingungen der Dienstrichtlinie erfüllt:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Ihre Verbindungszeichenfolge sollte in etwa folgendermaßen aussehen:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Notieren Sie sich die Dienst-Verbindungszeichenfolge, da Sie sie später in diesem Tutorial benötigen.

## <a name="download-the-web-app-from-github"></a>Herunterladen der Web-App von GitHub

Öffnen Sie ein Befehlsfenster, und geben Sie die folgenden Befehle ein, um das Beispiel von GitHub herunterzuladen und in das Beispielverzeichnis zu wechseln:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Überprüfen des Web-App-Codes

Öffnen Sie die Web-App im Verzeichnis „web-apps-node-iot-hub-data-visualization“ in Ihrem bevorzugten Editor. Im Folgenden ist die Dateistruktur in VS Code zu sehen:

![Struktur der Web-App-Datei](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Hier die Aufgaben der folgenden Dateien in einem kurzen Überblick:

* **Server.js** ist ein dienstseitiges Skript, das den Websocket und die Event Hub-Wrapperklasse initialisiert. Es bietet eine Rückruffunktion für die Event Hub-Wrapperklasse, die die Klasse zur Übertragung eingehender Nachrichten an den Websocket verwendet.

* **Event-hub-reader.js** ist ein dienstseitiges Skript, das unter Verwendung der angegebenen Verbindungszeichenfolge und Consumergruppe eine Verbindung mit dem integrierten Endpunkt des IoT-Hubs herstellt. Das Skript extrahiert „DeviceId“ und „EnqueuedTimeUtc“ aus den Metadaten eingehender Nachrichten und vermittelt die Nachricht anschließend mit der von Server.js registrierten Rückrufmethode.

* **Chart-device-data.js** ist ein clientseitiges Skript, das auf dem Websocket lauscht, jede „DeviceId“ verfolgt und die letzten 50 eingehenden Datenpunkte für jedes Gerät speichert. Anschließend werden die ausgewählten Gerätedaten an das Diagrammobjekt gebunden.

* **Index.html** verwaltet das Benutzeroberflächenlayout für die Webseite und verweist auf die erforderlichen Skripts für die clientseitige Logik.

## <a name="configure-environment-variables-for-the-web-app"></a>Konfigurieren von Umgebungsvariablen für die Web-App

Um Daten aus dem IoT-Hub zu lesen, benötigt die Web-App die Verbindungszeichenfolge Ihres IoT-Hubs und den Namen der Consumergruppe, über die sie lesen soll. Die App ruft diese Zeichenfolgen aus der Prozessumgebung in den folgenden Zeilen in Server.js ab:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Legen Sie die Umgebungsvariablen im Befehlsfenster mit den folgenden Befehlen fest. Ersetzen Sie die Platzhalterwerte durch die Dienst-Verbindungszeichenfolge für Ihren IoT-Hub und den Namen der zuvor erstellten Consumergruppe. Die Zeichenfolgen dürfen nicht in Anführungszeichen stehen.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Ausführen der Web-App

1. Stellen Sie sicher, dass Ihr Gerät ausgeführt wird und Daten sendet.

2. Führen Sie folgende Zeilen im Befehlsfenster aus, um die referenzierten Pakete herunterzuladen und zu installieren und die Website zu starten:

   ```cmd
   npm install
   npm start
   ```

3. An der Ausgabe in der Konsole sollten Sie erkennen, dass die Web-App erfolgreich eine Verbindung mit dem IoT-Hub hergestellt hat und an Port 3000 lauscht:

   ![Web-App wurde in der Konsole gestartet.](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Öffnen einer Webseite, um Daten aus dem IoT-Hub anzuzeigen

Öffnen Sie einen Browser unter `http://localhost:3000`.

Wählen Sie in der Liste **Gerät auswählen** Ihr Gerät aus. Sie sehen einen Verlaufsplot mit den letzten 50 Temperatur- und Luftfeuchtigkeits-Datenpunkten, die vom Gerät an den IoT-Hub gesendet wurden.

![Web-App-Seite mit Anzeige von Temperatur und Luftfeuchtigkeit in Echtzeit](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

In der Konsolenausgabe sollten auch die Nachrichten angezeigt werden, die die Web-App an den Browserclient überträgt:  

![Ausgabe der Web-App-Übertragung in der Konsole](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hosten der Web-App in App Service

Die [Web-Apps-Funktion des Azure App Service](../app-service/overview.md) bietet ein PAAS (Platform-as-a-Service)-Feature zum Hosten von Webanwendungen. In Azure App Service gehostete Webanwendungen profitieren von leistungsstarken Azure-Features wie zusätzlicher Sicherheit, Lastenausgleich und Skalierbarkeit sowie von DevOps-Lösungen von Azure und Partnern wie Continuous Deployment, Paketverwaltung usw. Azure App Service unterstützt Webanwendungen in zahlreichen gängigen Programmiersprachen, die in einer Windows- oder Linux-Infrastruktur bereitgestellt werden.

In diesem Abschnitt stellen Sie eine Web-App in App Service bereit und implementieren den Code für die App mithilfe von Azure CLI-Befehlen. Details zu den verwendeten Befehlen finden Sie in der [az webapp](/cli/azure/webapp?view=azure-cli-latest)-Dokumentation. Stellen Sie zu Beginn sicher, dass Sie die folgenden Schritte ausgeführt haben: [Hinzufügen einer Ressourcengruppe zum IoT-Hub](#add-a-consumer-group-to-your-iot-hub), [Abrufen einer Dienst-Verbindungszeichenfolge für den IoT-Hub](#get-a-service-connection-string-for-your-iot-hub) und [Herunterladen der Web-App von GitHub](#download-the-web-app-from-github).

1. Mit einem [App Service-Plan](../app-service/overview-hosting-plans.md) wird eine Gruppe von Computeressourcen definiert, die für eine in App Service gehostete App ausgeführt werden. In diesem Tutorial verwenden wir den Developer-/Free-Tarif zum Hosten der Web-App. Bei Nutzung des Free-Tarifs wird Ihre Web-App auf freigegebenen Windows-Ressourcen zusammen mit anderen App Service-Apps ausgeführt, einschließlich Apps anderer Kunden. Azure bietet außerdem App Service-Pläne zum Bereitstellen von Web-Apps auf Linux-Computeressourcen. Sie können diesen Schritt überspringen, wenn Sie bereits einen App Service-Plan verfügen, den Sie verwenden möchten.

   Führen Sie den folgenden Befehl aus, um einen App Service-Plan mit dem Free-Tarif von Windows zu erstellen. Verwenden Sie dieselbe Ressourcengruppe, in der der IoT-Hub enthalten ist. Der Name des App Service-Plans darf Groß- und Kleinbuchstaben, Ziffern und Bindestriche enthalten.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Stellen Sie jetzt eine Web-App im App Service-Plan bereit. Durch den `--deployment-local-git`-Parameter kann der Code der Web-App hochgeladen und aus einem Git-Repository auf Ihrem lokalen Computer bereitgestellt werden. Der Name der Web-App muss global eindeutig sein und darf Groß- und Kleinbuchstaben, Ziffern und Bindestriche enthalten. Stellen Sie sicher, dass Sie für den Parameter `--runtime` mindestens Node Version 10.6 angeben, je nach der von Ihnen verwendeten Version der Node.js-Runtime. Sie können mit dem Befehl `az webapp list-runtimes` eine Liste der unterstützten Runtimes abrufen.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Fügen Sie jetzt Anwendungseinstellungen für die Umgebungsvariablen hinzu, die die IoT-Hub-Verbindungszeichenfolge und die Event Hub-Consumergruppe angeben. Einzelne Einstellungen sind im `-settings`-Parameter durch Leerzeichen getrennt. Verwenden Sie die Dienst-Verbindungszeichenfolge für Ihren IoT-Hub und die zuvor in diesem Tutorial erstellte Consumergruppe. Die Werte dürfen nicht in Anführungszeichen stehen.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Aktivieren Sie das WebSocket-Protokoll für die Web-App, und konfigurieren Sie die Web-App so, dass sie ausschließlich HTTPS-Anforderungen empfängt (HTTP-Anforderungen werden zu HTTPS umgeleitet).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Um den Code für App Service bereitzustellen, verwenden Sie Ihre [Anmeldeinformationen für die Bereitstellung auf Benutzerebene](../app-service/deploy-configure-credentials.md). Ihre Anmeldeinformationen für die Bereitstellung auf Benutzerebene unterscheiden sich von Ihren Azure-Anmeldeinformationen und werden für lokale Git- und FTP-Bereitstellungen für eine Web-App verwendet. Nachdem sie festgelegt wurden, gelten sie für alle App Service-Apps in allen Abonnements unter Ihrem Azure-Konto. Wenn Sie zuvor bereits Anmeldeinformationen für die Bereitstellung auf Benutzerebene festgelegt haben, können Sie diese verwenden.

   Wenn Sie zuvor noch keine Anmeldeinformationen für die Bereitstellung auf Benutzerebene festgelegt oder Ihr Kennwort vergessen haben, führen Sie den folgenden Befehl aus. Ihr Benutzername für die Bereitstellung muss in Azure eindeutig sein und darf bei lokalen Git-Pushes kein „\@“-Symbol enthalten. Geben Sie Ihr neues Kennwort ein, und bestätigen Sie es, sobald Sie dazu aufgefordert werden. Das Kennwort muss mindestens acht Zeichen lang sein und zwei der folgenden drei Elemente enthalten: Buchstaben, Zahlen und Symbole.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Rufen Sie die Git-URL ab, die Sie zum Pushen des Codes zum App Service verwenden.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Fügen Sie dem Klon einen Remotespeicherort hinzu, der auf das Git-Repository für die Web-App in App Service verweist. Verwenden Sie als \<Git clone URL\> die im vorherigen Schritt zurückgegebene URL. Führen Sie folgenden Befehl im Befehlsfenster aus.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Um den Code für App Service bereitzustellen, geben Sie den folgenden Befehl im Befehlsfenster ein. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie die Anmeldeinformationen für die Bereitstellung auf Benutzerebene ein, die Sie in Schritt 5 erstellt haben. Stellen Sie sicher, dass der Pushvorgang zum Masterbranch des App Service-Remotespeicherorts erfolgt.

    ```cmd
    git push webapp master:master
    ```

9. Der Status der Bereitstellung wird im Befehlsfenster aktualisiert. Bei einer erfolgreichen Bereitstellung werden am Ende in etwa folgende Zeilen ausgegeben:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Führen Sie den folgenden Befehl aus, um den Status Ihrer Web-App abzufragen und sicherzustellen, dass sie ausgeführt wird:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Navigieren Sie in einem Browser zu `https://<your web app name>.azurewebsites.net`. Sie sehen eine Webseite, die mit der vergleichbar ist, die bei der lokalen Ausführung der Web-App angezeigt wurde. Unter der Voraussetzung, dass Ihr Gerät funktioniert und Daten sendet, sollten Sie einen Verlaufsplot mit den letzten 50 Ablesungen der Temperatur und Luftfeuchtigkeit sehen, die das Gerät gesendet hat.

## <a name="troubleshooting"></a>Problembehandlung

Falls bei der Ausführung dieses Beispiels Probleme auftreten, versuchen Sie, die Schritte in den folgenden Abschnitten auszuführen. Wenn weiterhin Probleme auftreten, senden Sie uns am Ende dieses Themas Ihr Feedback.

### <a name="client-issues"></a>Clientprobleme

* Wenn ein Gerät nicht in der Liste angezeigt oder kein Diagramm gezeichnet wird, stellen Sie sicher, dass der Gerätecode auf Ihrem Gerät ausgeführt wird.

* Öffnen Sie im Browser die Entwicklertools (in den meisten Browsern über die Taste F12), und suchen Sie die Konsole. Stellen Sie fest, ob Warnungen oder Fehler ausgegeben wurden.

* Sie können das clientseitige Skript in „/js/chat-device-data.js“ debuggen.

### <a name="local-website-issues"></a>Probleme mit der lokalen Website

* Sehen Sie sich die Ausgabe in dem Fenster an, in dem Sie den Knoten für die Konsolenausgabe gestartet haben.

* Debuggen Sie insbesondere den Servercode in „server.js“ und „/scripts/event-hub-reader.js“.

### <a name="azure-app-service-issues"></a>Probleme mit Azure App Service

* Navigieren Sie im Azure-Portal zu Ihrer Web-App. Wählen Sie unter **Überwachung** im linken Bereich **App Service-Protokolle** aus. Aktivieren Sie **Anwendungsprotokollierung (Dateisystem)** , legen Sie **Ebene** auf „Fehler“ fest, und wählen Sie dann **Speichern** aus. Öffnen Sie dann (unter **Überwachung**) **Protokollstream**.

* Wählen Sie im Azure-Portal für Ihre Web-App unter **Entwicklungstools** die Option **Konsole** aus, und validieren Sie die node-Version und npm-Version mit `node -v` bzw. `npm -v`.

* Wenn ein Fehler angezeigt wird, dass ein Paket nicht gefunden wurde, haben Sie die Schritte möglicherweise in der falschen Reihenfolge ausgeführt. Wenn die Website (mit `git push`) bereitgestellt wurde, führt der App-Dienst `npm install` aus, das basierend auf der aktuellen, konfigurierten node-Version ausgeführt wird. Wenn dieser Wert später in der Konfiguration geändert wird, müssen Sie eine bedeutungslose Änderung am Code vornehmen und erneut pushen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben mithilfe Ihrer Web-App erfolgreich Sensordaten in Echtzeit in IoT Hub visualisiert.

Eine weitere Möglichkeit zur Visualisierung von Daten in Azure IoT Hub finden Sie im Artikel [Verwenden von Power BI zum Visualisieren von Echtzeit-Sensordaten in IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
