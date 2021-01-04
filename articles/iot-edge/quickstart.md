---
title: 'Schnellstart: Erstellen eines Azure IoT Edge-Geräts unter Windows | Microsoft-Dokumentation'
description: In diesem Schnellstart erfahren Sie, wie ein IoT Edge-Geräte erstellen und anschließend vordefinierten Code remote über das Azure-Portal bereitstellen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/30/2020
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 547bf111e73813c939caa917c0117dac6c8989e9
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922460"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Windows-Gerät

In dieser Schnellstartanleitung können Sie Azure IoT Edge ausprobieren, indem Sie Code in Containern auf einem virtuellen Windows-IoT Edge-Gerät bereitstellen. IoT Edge ermöglicht Ihnen die Remoteverwaltung von Code auf Ihren Geräten, damit Sie noch mehr Workloads an den Edgebereich senden können. Im Rahmen dieser Schnellstartanleitung wird die Verwendung eines virtuellen Azure-Computers als IoT Edge-Gerät empfohlen. Bei Verwendung eines virtuellen Computers können Sie schnell einen Testcomputer erstellen, die erforderlichen Komponenten installieren und den Computer anschließend löschen, wenn Sie ihn nicht mehr benötigen.

In dieser Schnellstartanleitung wird Folgendes vermittelt:

* Erstellen Sie einen IoT Hub.
* Registrieren eines IoT Edge-Geräts für Ihren IoT Hub
* Installieren und Starten der IoT Edge-Runtime auf Ihrem virtuellen Gerät
* Durchführen der Remotebereitstellung eines Moduls für ein IoT Edge-Gerät und Senden von Telemetriedaten an den IoT Hub

![Diagramm: Schnellstart-Architektur für Geräte und Cloud](./media/quickstart/install-edge-full.png)

In dieser Schnellstartanleitung erfahren Sie Schritt für Schritt, wie Sie einen virtuellen Windows-Computer erstellen und als IoT Edge-Gerät konfigurieren. Anschließend stellen Sie ein Modul über das Azure-Portal auf Ihrem Gerät bereit. Das in dieser Schnellstartanleitung verwendete Modul ist ein simulierter Sensor, mit dem Daten zu Temperatur, Luftfeuchtigkeit und Luftdruck generiert werden. Die anderen Tutorials zu Azure IoT Edge bauen hierauf auf und erläutern die Bereitstellung von zusätzlichen Modulen, mit denen die simulierten Daten analysiert werden, um geschäftliche Erkenntnisse zu gewinnen.

Wenn Sie über kein aktives Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bereiten Sie die Umgebung für die Azure CLI vor.

- Verwenden Sie [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell) in der PowerShell-Umgebung.

   [![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)   
- Wenn Sie möchten, können Sie auch die Azure CLI [installieren](/cli/azure/install-azure-cli), um CLI-Verweisbefehle auszuführen.
   - Wenn Sie eine lokale Installation verwenden, melden Sie sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei der Azure CLI an.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.  Weitere Anmeldeoptionen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).
  - Installieren Sie die Azure CLI-Erweiterungen bei der ersten Verwendung, wenn Sie dazu aufgefordert werden.  Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Führen Sie [az version](/cli/azure/reference-index?#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index?#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

Cloudressourcen:

- Eine Ressourcengruppe zum Verwalten aller Ressourcen, die Sie in dieser Schnellstartanleitung verwenden.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-Gerät:

- Ein virtueller Windows-Computer, der als IoT Edge-Gerät fungiert. Sie können diesen virtuellen Computer mit dem folgenden Befehl erstellen, wobei Sie `{password}` durch ein sicheres Kennwort ersetzen:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  Das Erstellen und Starten des neuen virtuellen Computers kann ein paar Minuten in Anspruch nehmen.

  Nach dem Starten des virtuellen Computers können Sie eine RDP-Datei herunterladen, die Sie beim Herstellen einer Verbindung mit Ihrem virtuellen Computer verwenden:

  1. Navigieren Sie zu Ihrem neuen virtuellen Windows-Computer im Azure-Portal.
  1. Wählen Sie **Verbinden**.
  1. Wählen Sie auf der Registerkarte **RDP** **RDP-Datei herunterladen** aus.

  Öffnen Sie diese Datei mit der Remotedesktopverbindung, um eine Verbindung mit Ihrem virtuellen Windows-Computer unter Verwendung des Administratornamens und Kennworts herzustellen, die Sie mit dem Befehl `az vm create` angegeben haben.

> [!NOTE]
> Ihr virtueller Windows-Computer startet mit der Windows-Version 1809 (Build 17763). Dabei handelt es sich um den aktuellen [Windows-Build mit langfristigem Support](/windows/release-information/). Windows sucht standardmäßig alle 22 Stunden automatisch nach Updates. Nach einer Überprüfung Ihres virtuellen Computers überträgt Windows ein Versionsupdate, das nicht mit IoT Edge für Windows kompatibel ist. Dadurch wird die weitere Verwendung der Features von IoT Edge für Windows verhindert. Es wird empfohlen, die Verwendung des virtuellen Computers auf eine Dauer von maximal 22 Stunden zu beschränken oder [Windows-Updates vorübergehend anzuhalten](https://support.microsoft.com/help/4028233/windows-10-manage-updates).
>
> In dieser Schnellstartanleitung wird aus Gründen der Einfachheit ein virtueller Windows-Desktopcomputer verwendet. Weitere Informationen darüber, welche Windows-Betriebssysteme für Produktionsszenarien allgemein verfügbar sind, finden Sie unter [Von Azure IoT Edge unterstützte Systeme](support.md).
>
> Wenn Sie Ihr eigenes Windows-Gerät für IoT Edge (einschließlich Geräten mit IoT Core) konfigurieren möchten, führen Sie die Schritte unter [Installieren der Azure IoT Edge-Runtime](how-to-install-iot-edge.md) aus.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Beginnen Sie den Schnellstart, indem Sie an der Azure-Befehlszeilenschnittstelle einen IoT-Hub erstellen.

![Diagramm: Erstellen einer IoT Hub-Instanz in der Cloud](./media/quickstart/create-iot-hub.png)

Der kostenlose IoT Hub kann für diesen Schnellstart verwendet werden. Wenn Sie IoT Hub schon einmal genutzt und bereits einen Hub erstellt haben, können Sie diesen IoT-Hub verwenden.

Mit dem folgenden Code wird ein kostenloser **F1**-Hub in der Ressourcengruppe `IoTEdgeResources` erstellt. Ersetzen Sie `{hub_name}` durch einen eindeutigen Namen für Ihren IoT-Hub. Die Erstellung einer IoT Hub-Instanz kann einige Minuten dauern.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Wenn Sie eine Fehlermeldung erhalten, da bereits ein kostenloser Hub in Ihrem Abonnement vorhanden ist, ändern Sie die SKU auf **S1**. Sollten Sie eine Fehlermeldung mit dem Hinweis erhalten, dass der IoT Hub-Name nicht verfügbar ist, ist bereits ein Hub mit diesem Namen vorhanden. Probieren Sie einen neuen Namen aus.

## <a name="register-an-iot-edge-device"></a>Registrieren eines IoT Edge-Geräts

Registrieren Sie ein IoT Edge-Gerät bei Ihrem neu erstellten IoT Hub.
![Diagramm: Registrieren eines Geräts mit einer IoT Hub-Identität](./media/quickstart/register-device.png)

Erstellen Sie eine Geräteidentität für das simulierte Gerät, sodass es mit dem IoT Hub kommunizieren kann. Die Geräteidentität befindet sich in der Cloud, und Sie verwenden eine eindeutige Geräte-Verbindungszeichenfolge, um einem physischen Gerät eine Geräteidentität zuzuordnen.

Da IoT Edge-Geräte sich von typischen IoT-Geräten unterscheiden und auf unterschiedliche Weise verwaltet werden können, deklarieren Sie diese Identität mit dem Flag `--edge-enabled` für ein IoT Edge-Gerät.

1. Geben Sie in Azure Cloud Shell den folgenden Befehl ein, um in Ihrem Hub ein Gerät mit dem Namen **myEdgeDevice** zu erstellen.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Falls Sie eine Fehlermeldung zu iothubowner-Richtlinienschlüsseln erhalten, überprüfen Sie, ob in Ihrer Cloud Shell-Instanz die aktuelle Version der Erweiterung „azure-iot“ ausgeführt wird.

2. Zeigen Sie die Verbindungszeichenfolge für Ihr Gerät an, über die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft wird. Sie enthält den Namen Ihres IoT-Hubs, den Namen Ihres Geräts und einen gemeinsam verwendeten Schlüssel, mit dem die Verbindungen zwischen diesen Komponenten authentifiziert werden.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Kopieren Sie den Wert des Schlüssels `connectionString` aus der JSON-Ausgabe, und speichern Sie ihn. Dieser Wert ist die Verbindungszeichenfolge des Geräts. Sie verwenden ihn im nächsten Abschnitt zum Konfigurieren der IoT Edge-Runtime.

   ![Abrufen der Verbindungszeichenfolge aus der CLI-Ausgabe](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installieren und Starten der IoT Edge-Runtime

Installieren Sie die Azure IoT Edge-Runtime auf Ihrem IoT Edge-Gerät, und konfigurieren Sie es mit einer Geräte-Verbindungszeichenfolge.
![Diagramm: Starten der Runtime auf einem Gerät](./media/quickstart/start-runtime.png)

Die IoT Edge-Runtime wird auf allen IoT Edge-Geräten bereitgestellt. Sie besteht aus drei Komponenten. Der *Daemon für die IoT Edge-Sicherheit* wird jedes Mal gestartet, wenn ein IoT Edge-Gerät gestartet wird. Hierbei wird der IoT Edge-Agent gestartet, um einen Bootstrapvorgang durchzuführen. Mit dem *IoT Edge-Agent* wird die Bereitstellung und Überwachung von Modulen auf dem IoT Edge-Gerät, einschließlich des IoT Edge-Hubs, verwaltet. Der *IoT Edge-Hub* verarbeitet die Kommunikation zwischen Modulen auf dem IoT Edge-Gerät sowie zwischen dem Gerät und IoT Hub.

Das Installationsskript beinhaltet auch ein Containermodul namens Moby, das die Containerimages auf Ihrem IoT Edge-Gerät verwaltet.

Bei der Installation der Runtime werden Sie zur Eingabe der Geräte-Verbindungszeichenfolge aufgefordert. Verwenden Sie die Zeichenfolge, die Sie über die Azure CLI abgerufen haben. Diese Zeichenfolge ordnet Ihr physisches Gerät der IoT Edge-Geräteidentität in Azure zu.

### <a name="connect-to-your-iot-edge-device"></a>Herstellen einer Verbindung mit Ihrem IoT Edge-Gerät

Die Schritte in diesem Abschnitt erfolgen alle auf Ihrem IoT Edge-Gerät, weshalb Sie eine Verbindung mit diesem virtuellen Computer über den Remotedesktop herstellen sollten.

### <a name="install-and-configure-the-iot-edge-service"></a>Installieren und Konfigurieren des IoT Edge-Diensts

Verwenden Sie PowerShell zum Herunterladen und Installieren der IoT Edge-Runtime. Verwenden Sie die aus IoT Hub abgerufene Geräte-Verbindungszeichenfolge zum Konfigurieren Ihres Geräts.

1. Führen Sie PowerShell auf dem virtuellen Computer als Administrator aus.

   >[!NOTE]
   >Verwenden Sie eine AMD64-PowerShell-Sitzung, um IoT Edge zu installieren, nicht PowerShell (x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

2. Durch den Befehl **Deploy-IoTEdge** wird überprüft, ob Ihr Windows-Computer über eine unterstützte Version verfügt. Außerdem aktiviert der Befehl das Containerfeature und lädt die Moby-Runtime und danach die IoT Edge-Runtime herunter.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

3. Ihr Computer wird möglicherweise automatisch neu gestartet. Sobald Sie vom Befehl „Deploy-IoTEdge“ zum Neustart aufgefordert werden, führen Sie diesen Schritt aus.

4. Führen Sie PowerShell erneut als Administrator aus.

5. Der Befehl **Initialize-IoTEdge** konfiguriert die IoT Edge-Runtime auf Ihrem Computer. Standardmäßig wird für den Befehl die manuelle Bereitstellung mit Windows-Containern verwendet.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

6. Wenn Sie zur Eingabe von **DeviceConnectionString** aufgefordert werden, geben Sie die Zeichenfolge ein, die Sie im vorherigen Abschnitt kopiert haben. Nehmen Sie die Anführungszeichen um die Verbindungszeichenfolge nicht mit auf.

### <a name="view-the-iot-edge-runtime-status"></a>Anzeigen des Status der IoT Edge-Runtime

Vergewissern Sie sich, dass die Runtime erfolgreich installiert und konfiguriert wurde. Es dauert einige Minuten, bis die Installation abgeschlossen ist und das IoT Edge-Agent-Modul gestartet wird.

1. Überprüfen Sie den Status des IoT Edge-Diensts.

   ```powershell
   Get-Service iotedge
   ```

2. Sollte eine Problembehandlung für den Dienst erforderlich sein, rufen Sie die Dienstprotokolle ab.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Zeigen Sie alle Module an, die auf Ihrem IoT Edge-Gerät ausgeführt werden. Da der Dienst gerade zum ersten Mal gestartet wurde, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul edgeAgent wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von zusätzlichen Modulen, die Sie auf Ihrem Gerät bereitstellen.

    ```powershell
    iotedge list
    ```

   ![Anzeigen eines Moduls auf Ihrem Gerät](./media/quickstart/iotedge-list-1.png)

Ihr IoT Edge-Gerät ist jetzt konfiguriert. Es kann nun zum Ausführen von in der Cloud bereitgestellten Modulen verwendet werden.

## <a name="deploy-a-module"></a>Bereitstellen eines Moduls

Verwalten Sie Ihr Azure IoT Edge-Gerät über die Cloud, um ein Modul bereitzustellen, das Telemetriedaten an die IoT Hub-Instanz sendet.

![Diagramm: Bereitstellen eines Moduls aus der Cloud für das Gerät](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Anzeigen generierter Daten

In diesem Schnellstart haben Sie ein neues IoT Edge-Gerät erstellt und die IoT Edge-Runtime darauf installiert. Anschließend haben Sie das Azure-Portal verwendet, um ein IoT Edge-Modul zur Ausführung auf dem Gerät bereitzustellen, ohne Änderungen am Gerät selbst vornehmen zu müssen.

In diesem Fall werden von dem Modul, das Sie gepusht haben, Daten für die Beispielumgebung generiert, die Sie später zum Testen verwenden können. Der simulierte Sensor überwacht sowohl einen Computer als auch seine Umgebung. Beispielsweise kann sich dieser Sensor in einem Serverraum, in einer Fabrik oder in einer Windturbine befinden. Er meldet die Umgebungstemperatur und Luftfeuchtigkeit, die Computertemperatur und den Druck sowie einen Zeitstempel. In den IoT Edge-Tutorials werden diese vom Modul erstellten Daten als Testdaten für die Analyse verwendet.

Vergewissern Sie sich, dass das über die Cloud bereitgestellte Modul auf dem IoT Edge-Gerät ausgeführt wird.

```powershell
iotedge list
```

   ![Anzeigen von drei Modulen auf dem Gerät](./media/quickstart/iotedge-list-2.png)

Zeigen Sie die vom Temperatursensormodul an die Cloud gesendeten Nachrichten an.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >Bei IoT Edge-Befehlen werden beim Verweis auf Modulnamen Groß- und Kleinschreibung unterschieden.

   ![Anzeigen der Daten von Ihrem Modul](./media/quickstart/iotedge-logs.png)

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
> [Einstieg in die Entwicklung von IoT Edge-Modulen für Windows-Geräte](tutorial-develop-for-windows.md)
