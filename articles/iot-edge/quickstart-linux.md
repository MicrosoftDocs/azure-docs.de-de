---
title: 'Schnellstart: Erstellen eines Azure IoT Edge-Geräts unter Linux | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein IoT Edge-Geräte unter Linux erstellen und anschließend vordefinierten Code remote über das Azure-Portal bereitstellen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/02/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9ba73e71e4525fe56a3cbb54626030f57e990b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920802"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät

In dieser Schnellstartanleitung können Sie Azure IoT Edge ausprobieren, indem Sie Code in Containern auf einem virtuellen Linux-IoT Edge-Gerät bereitstellen. IoT Edge ermöglicht Ihnen die Remoteverwaltung von Code auf Ihren Geräten, damit Sie noch mehr Workloads an den Edgebereich senden können. Im Rahmen dieser Schnellstartanleitung wird die Verwendung eines virtuellen Azure-Computers als IoT Edge-Gerät empfohlen. So können Sie schnell einen Testcomputer mit dem IoT Edge-Dienst erstellen und anschließend löschen, wenn Sie ihn nicht mehr benötigen.

In dieser Schnellstartanleitung wird Folgendes vermittelt:

* Erstellen Sie einen IoT Hub.
* Registrieren eines IoT Edge-Geräts für Ihren IoT Hub
* Installieren und Starten der IoT Edge-Runtime auf Ihrem virtuellen Gerät
* Durchführen der Remotebereitstellung eines Moduls auf einem IoT Edge-Gerät

![Diagramm: Schnellstart-Architektur für Geräte und Cloud](./media/quickstart-linux/install-edge-full.png)

In dieser Schnellstartanleitung wird Schritt für Schritt beschrieben, wie Sie einen virtuellen Linux-Computer erstellen, der als IoT Edge-Gerät konfiguriert ist. Anschließend stellen Sie ein Modul über das Azure-Portal auf Ihrem Gerät bereit. Das in dieser Schnellstartanleitung verwendete Modul ist ein simulierter Sensor, mit dem Daten zu Temperatur, Luftfeuchtigkeit und Luftdruck generiert werden. Die anderen Tutorials zu Azure IoT Edge bauen hierauf auf und erläutern die Bereitstellung von zusätzlichen Modulen, mit denen die simulierten Daten analysiert werden, um geschäftliche Erkenntnisse zu gewinnen.

Wenn Sie über kein aktives Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bereiten Sie die Umgebung für die Azure CLI vor.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cloudressourcen:

- Eine Ressourcengruppe zum Verwalten aller Ressourcen, die Sie in dieser Schnellstartanleitung verwenden. Wir verwenden in dieser Schnellstartanleitung und den folgenden Tutorials durchgängig die Beispielressourcengruppe mit dem Namen **IoTEdgeResources**.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Beginnen Sie den Schnellstart, indem Sie an der Azure-Befehlszeilenschnittstelle einen IoT-Hub erstellen.

![Diagramm: Erstellen einer IoT Hub-Instanz in der Cloud](./media/quickstart-linux/create-iot-hub.png)

Der kostenlose IoT Hub kann für diesen Schnellstart verwendet werden. Wenn Sie IoT Hub schon einmal genutzt und bereits einen Hub erstellt haben, können Sie diesen IoT-Hub verwenden.

Mit dem folgenden Code wird ein kostenloser **F1**-Hub in der Ressourcengruppe **IoTEdgeResources** erstellt. Ersetzen Sie `{hub_name}` durch einen eindeutigen Namen für Ihren IoT-Hub. Die Erstellung einer IoT Hub-Instanz kann einige Minuten dauern.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Wenn Sie eine Fehlermeldung erhalten, da bereits ein kostenloser Hub in Ihrem Abonnement vorhanden ist, ändern Sie die SKU auf **S1**. Jedes Abonnement kann nur über einen kostenlosen IoT Hub verfügen. Sollten Sie eine Fehlermeldung mit dem Hinweis erhalten, dass der IoT Hub-Name nicht verfügbar ist, ist bereits ein Hub mit diesem Namen vorhanden. Probieren Sie einen neuen Namen aus.

## <a name="register-an-iot-edge-device"></a>Registrieren eines IoT Edge-Geräts

Registrieren Sie ein IoT Edge-Gerät bei Ihrem neu erstellten IoT Hub.

![Diagramm – Registrieren eines Geräts mit einer IoT Hub-Identität](./media/quickstart-linux/register-device.png)

Erstellen Sie eine Geräteidentität für das IoT Edge-Gerät, sodass es mit dem IoT Hub kommunizieren kann. Die Geräteidentität befindet sich in der Cloud, und Sie verwenden eine eindeutige Geräte-Verbindungszeichenfolge, um einem physischen Gerät eine Geräteidentität zuzuordnen.

Da IoT Edge-Geräte sich von typischen IoT-Geräten unterscheiden und auf unterschiedliche Weise verwaltet werden können, deklarieren Sie diese Identität mit dem Flag `--edge-enabled` für ein IoT Edge-Gerät.

1. Geben Sie in Azure Cloud Shell den folgenden Befehl ein, um in Ihrem Hub ein Gerät mit dem Namen **myEdgeDevice** zu erstellen.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Falls Sie eine Fehlermeldung zu iothubowner-Richtlinienschlüsseln erhalten, überprüfen Sie, ob in Ihrer Cloud Shell-Instanz die aktuelle Version der Erweiterung „azure-iot“ ausgeführt wird.

2. Zeigen Sie die Verbindungszeichenfolge für Ihr Gerät an, über die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft wird. Sie enthält den Namen Ihres IoT-Hubs, den Namen Ihres Geräts und einen gemeinsam verwendeten Schlüssel, mit dem die Verbindungen zwischen diesen Komponenten authentifiziert werden. Diese Verbindungszeichenfolge wird auch im nächsten Abschnitt verwendet, wenn Sie Ihr IoT Edge-Gerät einrichten.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![Anzeigen der Verbindungszeichenfolge in der CLI-Ausgabe](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>Konfigurieren Ihres IoT Edge-Geräts

Erstellen Sie einen virtuellen Computer, auf dem die Azure IoT Edge-Runtime vorhanden ist.

![Diagramm – Starten der Runtime auf einem Gerät](./media/quickstart-linux/start-runtime.png)

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Sie besteht aus drei Komponenten. Der *Daemon für die IoT Edge-Sicherheit* wird jedes Mal gestartet, wenn ein IoT Edge-Gerät gestartet wird. Hierbei wird der IoT Edge-Agent gestartet, um einen Bootstrapvorgang durchzuführen. Der *IoT Edge-Agent* erleichtert die Bereitstellung und Überwachung von Modulen auf dem IoT Edge-Gerät, einschließlich des IoT Edge-Hubs. Der *IoT Edge-Hub* verwaltet die Kommunikation zwischen Modulen auf dem IoT Edge-Gerät sowie zwischen dem Gerät und IoT Hub.

Bei der Konfiguration der Runtime geben Sie eine Geräte-Verbindungszeichenfolge ein. Dies ist die Zeichenfolge, die Sie über die Azure CLI abgerufen haben. Diese Zeichenfolge ordnet Ihr physisches Gerät der IoT Edge-Geräteidentität in Azure zu.

### <a name="deploy-the-iot-edge-device"></a>Bereitstellen des IoT Edge-Geräts

In diesem Abschnitt wird eine Azure Resource Manager-Vorlage verwendet, um einen neuen virtuellen Computer zu erstellen und darauf die IoT Edge-Runtime zu installieren. Falls Sie stattdessen Ihr eigenes Linux-Gerät verwenden möchten, können Sie die Installationsschritte unter [Installieren der Azure IoT Edge-Runtime](how-to-install-iot-edge.md) ausführen und dann zu dieser Schnellstartanleitung zurückkehren.

Verwenden Sie den folgenden CLI-Befehl, um Ihr IoT Edge-Gerät basierend auf der vordefinierten Vorlage [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) zu erstellen.

* Bash- oder Cloud Shell-Benutzer: Kopieren Sie den folgenden Befehl in einen Text-Editor, ersetzen Sie den Platzhaltertext durch Ihre Informationen, und kopieren Sie ihn dann in Ihr Bash- bzw. Cloud Shell-Fenster:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name
   <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* PowerShell-Benutzer: Kopieren Sie den folgenden Befehl in Ihr PowerShell-Fenster, und ersetzen Sie den Platzhaltertext dann durch Ihre eigenen Informationen:

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

Für diese Vorlage werden die folgenden Parameter verwendet:

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| **resource-group** | Die Ressourcengruppe, in der die Ressourcen erstellt werden. Verwenden Sie die Standardressourcengruppe **IoTEdgeResources**, die in diesem Artikel bereits verwendet wurde, oder geben Sie den Namen einer Ressourcengruppe an, die unter Ihrem Abonnement vorhanden ist. |
| **template-uri** | Ein Zeiger auf die Resource Manager-Vorlage, die wir verwenden. |
| **dnsLabelPrefix** | Eine Zeichenfolge, die zum Erstellen des Hostnamens des virtuellen Computers verwendet wird. Ersetzen Sie den Platzhaltertext durch einen Namen für Ihren virtuellen Computer. |
| **adminUsername** | Ein Benutzername für das Administratorkonto des virtuellen Computers. Verwenden Sie das Beispiel **azureUser**, oder geben Sie einen neuen Benutzernamen an. |
| **deviceConnectionString** | Die Verbindungszeichenfolge aus der Geräteidentität in IoT Hub, die zum Konfigurieren der IoT Edge-Runtime auf dem virtuellen Computer verwendet wird. Mit dem CLI-Befehl in diesem Parameter wird die Verbindungszeichenfolge für Sie abgerufen. Ersetzen Sie den Platzhaltertext durch den Namen Ihres IoT-Hubs. |
| **authenticationType** | Die Authentifizierungsmethode für das Administratorkonto. In dieser Schnellstartanleitung wird die Authentifizierung vom Typ **password** verwendet, aber Sie können diesen Parameter auch auf **sshPublicKey** festlegen. |
| **adminPasswordOrKey** | Das Kennwort oder der Wert des SSH-Schlüssels für das Administratorkonto. Ersetzen Sie den Platzhaltertext durch ein sicheres Kennwort. Ihr Kennwort muss mindestens zwölf Zeichen lang sein und drei von vier der folgenden Zeichenarten abdecken: Kleinbuchstaben, Großbuchstaben, Ziffern und Sonderzeichen. |

Nach Abschluss der Bereitstellung sollten Sie in der CLI eine Ausgabe im JSON-Format erhalten, die die SSH-Informationen zum Herstellen der Verbindung mit dem virtuellen Computer enthält. Kopieren Sie den Wert des Eintrags **Öffentlicher SSH-Schlüssel** im Abschnitt **outputs**:

   ![Abrufen des öffentlichen SSH-Werts aus der Ausgabe](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>Anzeigen des Status der IoT Edge-Runtime

Die verbleibenden Befehle in dieser Schnellstartanleitung werden auf Ihrem IoT Edge-Gerät ausgeführt, Sie können also sehen, was auf dem Gerät geschieht. Gehen Sie wie folgt vor, wenn Sie einen virtuellen Computer verwenden: Stellen Sie mit diesem Computer nun eine Verbindung her, indem Sie den von Ihnen eingerichteten Administratorbenutzernamen und den DNS-Namen verwenden, der vom Bereitstellungsbefehl ausgegeben wurde. Den DNS-Namen finden Sie auch auf der Seite „Übersicht“ Ihres virtuellen Computers im Azure-Portal. Verwenden Sie den folgenden Befehl, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. Ersetzen Sie `{admin username}` und `{DNS name}` durch Ihre eigenen Werte.

   ```console
   ssh {admin username}@{DNS name}
   ```

Nachdem die Verbindung mit Ihrem virtuellen Computer hergestellt wurde, sollten Sie überprüfen, ob die Runtime auf Ihrem IoT Edge-Gerät erfolgreich installiert und konfiguriert wurde.

1. Überprüfen Sie, ob der Daemon für die IoT Edge-Sicherheit als Systemdienst ausgeführt wird.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Anzeigen der Ausführung des IoT Edge-Daemons als Systemdienst](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >Sie benötigen erhöhte Rechte zum Ausführen von `iotedge`-Befehlen. Nachdem Sie sich bei Ihrem Computer abgemeldet und sich nach der Installation der IoT Edge-Runtime zum ersten Mal erneut angemeldet haben, werden Ihre Berechtigungen automatisch aktualisiert. Verwenden Sie bis dahin `sudo` vor den Befehlen.

2. Sollte eine Problembehandlung für den Dienst erforderlich sein, rufen Sie die Dienstprotokolle ab.

   ```bash
   journalctl -u iotedge
   ```

3. Zeigen Sie alle Module an, die auf Ihrem IoT Edge-Gerät ausgeführt werden. Da der Dienst gerade zum ersten Mal gestartet wurde, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul edgeAgent wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von zusätzlichen Modulen, die Sie auf Ihrem Gerät bereitstellen.

   ```bash
   sudo iotedge list
   ```

   ![Anzeigen eines Moduls auf Ihrem Gerät](./media/quickstart-linux/iotedge-list-1.png)

Ihr IoT Edge-Gerät ist jetzt konfiguriert. Es kann nun zum Ausführen von in der Cloud bereitgestellten Modulen verwendet werden.

## <a name="deploy-a-module"></a>Bereitstellen eines Moduls

Verwalten Sie Ihr Azure IoT Edge-Gerät über die Cloud, um ein Modul bereitzustellen, das Telemetriedaten an die IoT Hub-Instanz sendet.

![Diagramm: Bereitstellen eines Moduls aus der Cloud für das Gerät](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Anzeigen generierter Daten

In diesem Schnellstart haben Sie ein neues IoT Edge-Gerät erstellt und die IoT Edge-Runtime darauf installiert. Anschließend haben Sie das Azure-Portal verwendet, um ein IoT Edge-Modul zur Ausführung auf dem Gerät bereitzustellen, ohne Änderungen am Gerät selbst vornehmen zu müssen.

In diesem Fall werden von dem Modul, das Sie gepusht haben, Daten für die Beispielumgebung generiert, die Sie später zum Testen verwenden können. Der simulierte Sensor überwacht sowohl einen Computer als auch seine Umgebung. Beispielsweise kann sich dieser Sensor in einem Serverraum, in einer Fabrik oder in einer Windturbine befinden. Er meldet die Umgebungstemperatur und Luftfeuchtigkeit, die Computertemperatur und den Druck sowie einen Zeitstempel. In den IoT Edge-Tutorials werden diese vom Modul erstellten Daten als Testdaten für die Analyse verwendet.

Öffnen Sie die Eingabeaufforderung erneut auf Ihrem IoT Edge-Gerät, oder verwenden Sie die SSH-Verbindung über die Azure CLI. Vergewissern Sie sich, dass das über die Cloud bereitgestellte Modul auf dem IoT Edge-Gerät ausgeführt wird:

   ```bash
   sudo iotedge list
   ```

   ![Anzeigen von drei Modulen auf dem Gerät](./media/quickstart-linux/iotedge-list-2.png)

Anzeigen der Nachrichten, die vom Temperatursensormodul gesendet werden:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >Bei IoT Edge-Befehlen werden beim Verweis auf Modulnamen Groß- und Kleinschreibung unterschieden.

   ![Anzeigen der Daten von Ihrem Modul](./media/quickstart-linux/iotedge-logs.png)

Sie können die bei Ihrem IoT-Hub eingehenden Nachrichten auch mit der [Azure IoT Hub-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) anzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit den IoT Edge-Tutorials fortfahren möchten, können Sie das Gerät verwenden, das Sie in dieser Schnellstartanleitung registriert und eingerichtet haben. Andernfalls können Sie die erstellten Azure-Ressourcen löschen, um Kosten zu vermeiden.

Wenn Sie Ihren virtuellen Computer und Azure IoT Hub in einer neuen Ressourcengruppe erstellt haben, können Sie diese Gruppe und alle zugehörigen Ressourcen löschen. Überprüfen Sie den Inhalt der Ressourcengruppe, um sicherzustellen, dass sie keine Elemente enthält, die Sie behalten möchten. Wenn Sie nicht die gesamte Gruppe löschen möchten, können Sie stattdessen einzelne Ressourcen löschen.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden.

Entfernen Sie die Gruppe **IoTEdgeResources**. Das Löschen einer Ressourcengruppe kann einige Minuten dauern.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Sie können sich vergewissern, dass die Ressourcengruppe entfernt wurde, indem Sie die Liste der Ressourcengruppen anzeigen.

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein IoT Edge-Gerät erstellt und die Azure IoT Edge-Cloudschnittstelle zum Bereitstellen von Code auf dem Gerät verwendet. Sie verfügen nun über ein Testgerät, das Rohdaten zu seiner Umgebung generiert.

Im nächsten Schritt richten Sie Ihre lokale Entwicklungsumgebung ein, damit Sie mit der Entwicklung von IoT Edge-Modulen für Ihre Geschäftslogik beginnen können.

> [!div class="nextstepaction"]
> [Einstieg in die Entwicklung von IoT Edge-Modulen für Linux-Geräte](tutorial-develop-for-linux.md)
