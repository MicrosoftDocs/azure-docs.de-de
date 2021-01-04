---
title: 'Installieren und Ausführen des Containers für räumliche Analyse: Maschinelles Sehen'
titleSuffix: Azure Cognitive Services
description: Mit dem Container für räumliche Analyse können Sie Personen und Entfernungen erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 2f03746a6a5afc388db2beeff84b3ab4cbd393b5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014593"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Installieren und Ausführen des Containers für räumliche Analyse (Vorschau)

Mit dem Container für räumliche Analyse können Sie in Echtzeit gestreamte Videodaten analysieren, um räumliche Bezüge zwischen Personen, ihre Bewegungen und ihre Interaktionen mit Objekten in der physischen Umgebung zu verstehen. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank"> im Azure-Portal eine Ressource für maschinelles Sehen <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der Ressource, die Sie erstellen, um den Container für räumliche Analyse auszuführen. Sie benötigen Ihren Schlüssel und den Endpunkt zu einem späteren Zeitpunkt des Prozesses.


### <a name="spatial-analysis-container-requirements"></a>Anforderungen an einen Container für räumliche Analyse

Zum Ausführen des Containers für räumliche Analyse benötigen Sie ein Computegerät mit einer [NVIDIA Tesla T4-GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). Wir empfehlen Ihnen die Verwendung von [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) mit GPU-Beschleunigung. Der Container kann aber auch auf allen anderen Desktopcomputern ausgeführt werden, die die Mindestanforderungen erfüllen. Dieses Gerät wird als Hostcomputer bezeichnet.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)

Azure Stack Edge ist eine Hardware-as-a-Service-Lösung und ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Eine ausführliche Anleitung zur Vorbereitung und Einrichtung finden Sie in der [Azure Stack Edge-Dokumentation](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Hardwaremindestanforderungen

* 4 GB Systemarbeitsspeicher
* 4 GB GPU-Arbeitsspeicher
* CPU mit acht Kernen
* 1 NVIDIA Tesla T4-GPU
* 20 GB Festplattenspeicher

#### <a name="recommended-hardware"></a>Empfohlene Hardware

* 32 GB Systemarbeitsspeicher
* 16 GB GPU-Arbeitsspeicher
* CPU mit acht Kernen
* 2 NVIDIA Tesla T4-GPUs
* 50 GB SSD-Speicherplatz

In diesem Artikel führen Sie den Download und die Installation der folgenden Softwarepakete durch. Auf dem Hostcomputer muss Folgendes ausgeführt werden können (siehe Anleitung unten):

* [NVIDIA-Grafiktreiber](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) und [NVIDIA CUDA-Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Konfigurationen für [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (Multi-Process Service, Multiprozessdienst)
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) und [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md)-Runtime

---

| Anforderung | Beschreibung |
|--|--|
| Kamera | Der Container für räumliche Analyse ist nicht an eine bestimmte Kameramarke gebunden. Für das Kameragerät müssen die folgenden Bedingungen erfüllt sein: Unterstützung des Real-Time Streaming-Protokolls (RTSP) und der H.264-Codierung, Zugriff auf den Hostcomputer und Streaming mit einer Auflösung von 15 Bilder/Sek. und 1080p. |
| Linux-Betriebssystem | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) muss auf dem Hostcomupter installiert sein.  |


## <a name="request-approval-to-run-the-container"></a>Anfordern der Genehmigung für die Containerausführung

Füllen Sie das [Anforderungsformular](https://aka.ms/csgate) aus, und übermitteln Sie es, um die Genehmigung für die Containerausführung anzufordern.

Im Formular müssen Sie Informationen über Sie selbst, Ihr Unternehmen und das Benutzerszenario eintragen, für das Sie den Container verwenden möchten. Das Azure Cognitive Services-Team überprüft das Formular nach der Übermittlung und sendet Ihnen eine E-Mail mit der Entscheidung.

> [!IMPORTANT]
> * Im Formular müssen Sie eine E-Mail-Adresse angeben, die einer Azure-Abonnement-ID zugeordnet ist.
> * Die Maschinelles Sehen-Ressource, die Sie zum Ausführen des Containers verwenden, muss mit der genehmigten Azure-Abonnement-ID erstellt worden sein.

Nach der Genehmigung können Sie den Container ausführen, nachdem Sie ihn aus Microsoft Container Registry (MCR) heruntergeladen haben (weiter unten in diesem Artikel beschrieben).

Sie können den Container nicht ausführen, wenn Ihr Azure-Abonnement nicht genehmigt wurde.

## <a name="set-up-the-host-computer"></a>Einrichten des Hostcomputers

Wir empfehlen Ihnen, ein Azure Stack Edge-Gerät für Ihren Hostcomputer zu verwenden. Klicken Sie auf **Desktopcomputer**, wenn Sie ein anderes Gerät konfigurieren.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Konfigurieren von Compute im Azure Stack Edge-Portal 
 
Bei der räumlichen Analyse werden die Computefeatures von Azure Stack Edge zum Ausführen einer KI-Lösung verwendet. Stellen Sie Folgendes sicher, um die Computefeatures zu aktivieren: 

* Sie haben Ihr Azure Stack Edge-Gerät [verbunden und aktiviert](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md). 
* Sie verfügen über ein Windows-Clientsystem mit PowerShell 5.0 oder höher, um auf das Gerät zuzugreifen.  
* Zum Bereitstellen eines Kubernetes-Clusters müssen Sie Ihr Azure Stack Edge-Gerät über die **lokale Benutzeroberfläche** im [Azure-Portal](https://portal.azure.com/) konfigurieren: 
  1. Aktivieren Sie das Computefeature auf Ihrem Azure Stack Edge-Gerät. Navigieren Sie zum Aktivieren von Compute auf der Weboberfläche Ihres Geräts zur Seite **Compute**. 
  2. Wählen Sie eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten, und klicken Sie anschließend auf **Aktivieren**. Auf Ihrem Gerät wird für diese Netzwerkschnittstelle ein virtueller Switch erstellt.
  3. Lassen Sie die Felder für die IP-Adressen der Kubernetes-Testknoten und der externen Kubernetes-Dienste leer.
  4. Klicken Sie auf **Übernehmen**. Dieser Vorgang kann ungefähr zwei Minuten dauern. 

![Konfigurieren der Computeumgebung](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Einrichten einer Edgecomputingrolle und Erstellen einer IoT Hub-Ressource

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Azure Stack Edge-Ressource. Klicken Sie auf der Seite **Übersicht** oder in der Navigationsliste auf die Schaltfläche **Erste Schritte**. Klicken Sie auf der Kachel  **Edgecomputing konfigurieren** auf **Konfigurieren**. 

![Link](media/spatial-analysis/configure-edge-compute-tile.png)

Wählen Sie auf der Seite  **Edgecomputing konfigurieren** eine vorhandene IoT Hub-Ressource aus, oder entscheiden Sie sich für die Neuerstellung. Standardmäßig wird zum Erstellen einer IoT Hub-Ressource ein Standard-Tarif (S1) verwendet. Wenn Sie eine IoT Hub-Ressource im Free-Tarif verwenden möchten, müssen Sie sie erstellen und anschließend auswählen. Für die IoT Hub-Ressource wird jeweils dasselbe Abonnement und dieselbe Ressourcengruppe wie für die Azure Stack Edge-Ressource genutzt. 

Klicken Sie auf **Erstellen**. Die Erstellung einer IoT Hub-Ressource kann einige Minuten dauern. Nachdem die IoT Hub-Ressource erstellt wurde, wird die Kachel  **Edgecomputing konfigurieren** aktualisiert, um die neue Konfiguration anzuzeigen. Wählen Sie auf der Kachel  **Compute konfigurieren** die Option  **Konfiguration anzeigen** aus, um sich zu vergewissern, dass die Edgecomputingrolle konfiguriert wurde.

Wenn die Edge-Computerolle auf dem Edge-Gerät eingerichtet ist, werden zwei Geräte erstellt: ein IoT-Gerät und ein IoT Edge-Gerät. Beide Geräte können in der IoT Hub-Ressource angezeigt werden. Die Azure IoT Edge-Runtime wird auf dem IoT Edge-Gerät bereits ausgeführt.            

> [!NOTE]
> * Derzeit wird nur die Linux-Plattform für IoT Edge-Geräte unterstützt. Informationen zur Problembehandlung für das Azure Stack Edge-Gerät finden Sie im Artikel [Protokollierung und Problembehandlung](spatial-analysis-logging.md).
> * Weitere Informationen zum Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](../../iot-edge/how-to-configure-proxy-support.md#azure-portal).

###  <a name="enable-mps-on-azure-stack-edge"></a>Aktivieren von MPS auf Azure Stack Edge 

1. Führen Sie eine Windows PowerShell-Sitzung als Administrator aus. 

2. Stellen Sie sicher, dass der Dienst Windows-Remoteverwaltung auf dem Client ausgeführt wird. Verwenden Sie im PowerShell-Terminal den folgenden Befehl: 
    
    ```powershell
    winrm quickconfig
    ```
    
    Falls Warnungen zu einer Firewallausnahme angezeigt werden, sollten Sie Ihren Netzwerkverbindungstyp überprüfen und in der Dokumentation zur [Windows-Remoteverwaltung](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) nachsehen.

3. Weisen Sie der IP-Adresse des Geräts eine Variable zu. 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. Verwenden Sie den folgenden Befehl, um der Liste mit den vertrauenswürdigen Hosts des Clients die IP-Adresse Ihres Geräts hinzuzufügen: 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. Starten Sie eine Windows PowerShell-Sitzung auf dem Gerät. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. Geben Sie das Kennwort an, wenn Sie dazu aufgefordert werden. Verwenden Sie dasselbe Kennwort wie für die Anmeldung bei der lokalen Webbenutzeroberfläche. Das Standardkennwort für die lokale Webbenutzeroberfläche lautet `Password1`.

Geben Sie `Start-HcsGpuMPS` ein, um den MPS-Dienst auf dem Gerät zu starten. 

Informationen zur Problembehandlung für das Azure Stack Edge-Gerät finden Sie unter [Problembehandlung beim Azure Stack Edge-Gerät](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device). 

#### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)

Befolgen Sie diese Anleitung, falls es sich bei Ihrem Hostcomputer nicht um ein Azure Stack Edge-Gerät handelt.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Installieren des NVIDIA CUDA-Toolkits und der Nvidia-Grafiktreiber auf dem Hostcomputer

Verwenden Sie das folgende Bash-Skript zum Installieren der erforderlichen Nvidia-Grafiktreiber und des CUDA-Toolkits.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Starten Sie den Computer neu, und führen Sie den unten angegebenen Befehl aus.

```bash
nvidia-smi
```

Die folgende Ausgabe wird angezeigt.

![NVIDIA-Treiberausgabe](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Installieren von Docker CE und nvidia-docker2 auf dem Hostcomputer

Installieren Sie Docker CE auf dem Hostcomputer.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Installieren Sie das Softwarepaket *nvidia-docker-2*.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Aktivieren von NVIDIA MPS auf dem Hostcomputer

> [!TIP]
> * Installieren Sie MPS nicht, wenn Ihre GPU-Computefunktion weniger als 7.x (vor Volta) beträgt. Weitere Informationen finden Sie unter [CUDA-Kompatibilität](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title). 
> * Führen Sie die MPS-Anleitung über ein Terminalfenster auf dem Hostcomputer aus. Führen Sie sie nicht auf Ihrer Docker-Containerinstanz aus.

Konfigurieren Sie die GPUs des Hostcomputers für [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (Multi-Process Service, Multiprozessdienst), um die beste Leistung und Auslastung zu erzielen. Führen Sie die MPS-Anleitung über ein Terminalfenster auf dem Hostcomputer aus.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Konfigurieren von Azure IoT Edge auf dem Hostcomputer

Erstellen Sie zum Bereitstellen des Containers für räumliche Analyse auf dem Hostcomputer eine Instanz des Diensts [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md), indem Sie den Standard- (S1) oder Free-Tarif (F0) verwenden. Wenn Ihr Hostcomputer vom Typ „Azure Stack Edge“ ist, sollten Sie dasselbe Abonnement und dieselbe Ressourcengruppe verwenden, das bzw. die von der Azure Stack Edge-Ressource verwendet wird.

Verwenden Sie die Azure CLI, um eine Instanz von Azure IoT Hub zu erstellen. Ersetzen Sie die Parameter nach Bedarf. Alternativ können Sie die Instanz von Azure IoT Hub auch über das [Azure-Portal](https://portal.azure.com/) erstellen.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "test-resource-group" --location "WestUS"

az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

Wenn es sich beim Hostcomputer nicht um ein Azure Stack Edge-Gerät handelt, müssen Sie [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) Version 1.0.9 installieren. Führen Sie die folgenden Schritte aus, um die richtige Version herunterzuladen:

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopieren Sie die generierte Liste.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

```bash
sudo apt-get update
```

Installieren Sie das Release 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registrieren Sie den Hostcomputer als Nächstes als IoT Edge-Gerät auf Ihrer IoT Hub-Instanz, indem Sie eine [Verbindungszeichenfolge](../../iot-edge/how-to-manual-provision-symmetric-key.md?view=iotedge-2018-06) verwenden.

Sie müssen das IoT Edge-Gerät mit Ihrer Azure IoT Hub-Instanz verbinden. Sie müssen die Verbindungszeichenfolge von dem IoT Edge-Gerät kopieren, das Sie weiter oben erstellt haben. Alternativ können Sie auch den unten angegebenen Befehl über die Azure CLI ausführen.

```bash
az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

Öffnen Sie auf dem Hostcomputer `/etc/iotedge/config.yaml` zur Bearbeitung. Ersetzen Sie `ADD DEVICE CONNECTION STRING HERE` durch die Verbindungszeichenfolge. Speichern und schließen Sie die Datei. Führen Sie diesen Befehl aus, um den IoT Edge-Dienst auf dem Hostcomputer neu zu starten.

```bash
sudo systemctl restart iotedge
```

Stellen Sie den Container für räumliche Analyse über das [Azure-Portal](../../iot-edge/how-to-deploy-modules-portal.md) oder die [Azure CLI](../../iot-edge/how-to-deploy-modules-cli.md) auf dem Hostcomputer als IoT-Modul bereit. Legen Sie bei Verwendung des Portals den Image-URI auf den Speicherort Ihrer Azure Container Registry-Instanz fest. 

Führen Sie die unten angegebenen Schritte aus, um den Container mit der Azure CLI bereitzustellen.

---

### <a name="iot-deployment-manifest"></a>IoT-Bereitstellungsmanifest

Zum Optimieren der Containerbereitstellung auf mehreren Hostcomputern können Sie eine Bereitstellungsmanifestdatei erstellen, um die Optionen für die Containererstellung und die Umgebungsvariablen anzugeben. Ein Beispiel für ein Bereitstellungsmanifest [für Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) und [andere Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) finden Sie auf Github.

In der folgenden Tabelle sind die verschiedenen Umgebungsvariablen aufgeführt, die vom IoT Edge-Modul verwendet werden. Sie können sie auch im oben verlinkten Bereitstellungsmanifest festlegen, indem Sie das Attribut `env` in `spatialanalysis` verwenden:

| Einstellungsname | Wert | Beschreibung|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Info, Verbose (Information, Ausführlich) | Protokolliergrad: Wählen Sie einen der beiden Werte aus.|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Nicht ändern|
| ARCHON_PERF_MARKER| false| Legen Sie diese Einstellung auf „true“ fest, wenn Sie die Leistungsprotokollierung verwenden möchten, und andernfalls auf „false“.| 
| ARCHON_NODES_LOG_LEVEL | Info, Verbose (Information, Ausführlich) | Protokolliergrad: Wählen Sie einen der beiden Werte aus.|
| OMP_WAIT_POLICY | PASSIVE | Nicht ändern|
| QT_X11_NO_MITSHM | 1 | Nicht ändern|
| API_KEY | Ihr API-Schlüssel| Ermitteln Sie diesen Wert im Azure-Portal unter Ihrer Maschinelles Sehen-Ressource. Sie finden ihn im Abschnitt **Schlüssel und Endpunkt** für Ihre Ressource. |
| BILLING_ENDPOINT | Ihr Endpunkt-URI| Ermitteln Sie diesen Wert im Azure-Portal unter Ihrer Maschinelles Sehen-Ressource. Sie finden ihn im Abschnitt **Schlüssel und Endpunkt** für Ihre Ressource.|
| ENDBENUTZER-LIZENZVERTRAG | accept (Akzeptieren) | Sie müssen diesen Wert auf *accept* (Akzeptieren) festlegen, damit der Container ausgeführt werden kann. |
| DISPLAY | :1 | Dieser Wert muss der Ausgabe von `echo $DISPLAY` auf dem Hostcomputer entsprechen. Azure Stack Edge-Geräte haben kein Display. Diese Einstellung ist nicht anwendbar.|


> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

Nachdem Sie das Bereitstellungsmanifest für [Azure Stack Edge-Geräte](https://go.microsoft.com/fwlink/?linkid=2142179) oder [einen Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) mit Ihren eigenen Einstellungen und Ihrer Auswahl von Vorgängen aktualisiert haben, können Sie den unten dargestellten [Azure CLI](../../iot-edge/how-to-deploy-modules-cli.md)-Befehl verwenden, um den Container auf dem Hostcomputer als IoT Edge-Modul bereitzustellen.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json --subscription "<subscriptionId>"
```

|Parameter  |Beschreibung  |
|---------|---------|
| `--hub-name` | Der Name Ihrer Azure IoT Hub-Instanz. |
| `--content` | Der Name der Bereitstellungsdatei. |
| `--target-condition` | Der Name Ihres IoT Edge-Geräts für den Hostcomputer. |
| `-–subscription` | Abonnement-ID oder -name. |

Mit diesem Befehl wird die Bereitstellung gestartet. Navigieren Sie im Azure-Portal zur Seite mit Ihrer Azure IoT Hub-Instanz, um den Bereitstellungsstatus anzuzeigen. Als Status wird unter Umständen *417 – Die Bereitstellungskonfiguration des Geräts ist nicht festgelegt* angezeigt, bis das Gerät das Herunterladen der Containerimages abgeschlossen hat und gestartet wird.

## <a name="validate-that-the-deployment-is-successful"></a>Überprüfen der erfolgreichen Bereitstellung

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Container aktiv ist. Suchen Sie im Azure-Portal in den **Einstellungen des IoT Edge-Moduls** für die räumliche Analyse in Ihrer Azure IoT Hub-Instanz nach dem *Laufzeitstatus*. Vergewissern Sie sich, dass **Gewünschter Wert** und **Gemeldeter Wert** für *Laufzeitstatus* auf *Wird ausgeführt* festgelegt sind.

![Beispiel für die Bereitstellungsüberprüfung](./media/spatial-analysis/deployment-verification.png)

Nach Abschluss der Bereitstellung und dem Beginn der Containerausführung beginnt der **Hostcomputer** damit, Ereignisse an Azure IoT Hub zu senden. Wenn Sie die `.debug`-Version der Vorgänge verwendet haben, wird ein Visualisierungsfenster für jede Kamera angezeigt, die Sie im Bereitstellungsmanifest konfiguriert haben. Sie können jetzt die zu überwachenden Linien und Zonen im Bereitstellungsmanifest definieren und die Anleitung zur erneuten Bereitstellung befolgen. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Konfigurieren der von der räumlichen Analyse durchgeführten Vorgänge

Sie müssen die [Vorgänge der räumlichen Analyse](spatial-analysis-operations.md) nutzen, um den Container für die Verwendung von vernetzten Kameras zu konfigurieren, die Vorgänge zu konfigurieren usw. Für jedes von Ihnen konfigurierte Kameragerät wird über die Vorgänge der räumlichen Analyse ein Ausgabestream mit JSON-Nachrichten generiert, die an Ihre Instanz von Azure IoT Hub gesendet werden.

## <a name="redeploy-or-delete-the-deployment"></a>Erneutes Bereitstellen oder Löschen der Bereitstellung

Wenn Sie die Bereitstellung aktualisieren müssen, müssen Sie sicherstellen, dass Ihre vorherigen Bereitstellungen erfolgreich waren. Alternativ müssen Sie die Bereitstellungen von IoT Edge-Geräten löschen, die nicht erfolgreich abgeschlossen werden konnten. Andernfalls werden diese Bereitstellungen fortgesetzt, und das System befindet sich in einem Fehlerzustand. Sie können das Azure-Portal oder die [Azure CLI](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment) verwenden.

## <a name="use-the-output-generated-by-the-container"></a>Verwenden der vom Container generierten Ausgabe

Falls Sie die vom Container generierte Ausgabe nutzen möchten, helfen Ihnen die Informationen in den folgenden Artikeln weiter:

*   Verwenden Sie das Azure Event Hub-SDK für Ihre gewählte Programmiersprache, um eine Verbindung mit dem Azure IoT Hub-Endpunkt herstellen und die Ereignisse empfangen zu können. Weitere Informationen finden Sie unter [Lesen von Nachrichten, die von Geräten an die Cloud gesendet werden, vom integrierten Endpunkt](../../iot-hub/iot-hub-devguide-messages-read-builtin.md). 
*   Richten Sie das Nachrichtenrouting auf Ihrer Azure IoT Hub-Instanz ein, um Ereignisse an andere Endpunkte zu senden, die Ereignisse unter Azure Blob Storage zu speichern usw. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../../iot-hub/iot-hub-devguide-messages-d2c.md). 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Ausführen der räumlichen Analyse mit einer aufgezeichneten Videodatei

Sie können die räumliche Analyse sowohl für aufgezeichnete als auch für Livevideos verwenden. Versuchen Sie, eine Videodatei aufzuzeichnen und als MP4-Datei zu speichern, um die räumliche Analyse für ein aufgezeichnetes Video zu verwenden. Erstellen Sie ein Blobspeicherkonto in Azure, oder verwenden Sie ein vorhandenes Konto. Aktualisieren Sie anschließend im Azure-Portal die folgenden Blobspeichereinstellungen:
    1. Ändern Sie **Sichere Übertragung erforderlich** in **Deaktiviert**.
    2. Ändern Sie **Öffentlichen Blobzugriff gestatten** in **Aktiviert**.

Navigieren Sie zum Abschnitt **Container**, und erstellen Sie entweder einen neuen Container, oder verwenden Sie einen vorhandenen. Laden Sie anschließend die Videodatei in den Container hoch. Erweitern Sie die Dateieinstellungen für die hochgeladene Datei, und wählen Sie die Option **SAS generieren** aus. Achten Sie darauf, dass Sie unter **Ablaufdatum** einen Zeitraum angeben, der den Testzeitraum abdeckt. Legen Sie **Zulässige Protokolle** auf *HTTP* fest (*HTTPS* wird nicht unterstützt).

Klicken Sie auf **SAS-Token und -URL generieren**, und kopieren Sie die Blob-SAS-URL. Ersetzen Sie `https` am Anfang durch `http`, und testen Sie die URL in einem Browser, der die Videowiedergabe unterstützt.

Ersetzen Sie `VIDEO_URL` im Bereitstellungsmanifest für Ihr [Azure Stack Edge-Gerät](https://go.microsoft.com/fwlink/?linkid=2142179) oder einen anderen [Desktopcomputer](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) durch die von Ihnen erstellte URL, und zwar für alle Graphen. Legen Sie `VIDEO_IS_LIVE` auf `false` fest, und stellen Sie den Container für räumliche Analyse mit dem aktualisierten Manifest erneut bereit. Betrachten Sie das folgende Beispiel.

Das Modul für die räumliche Analyse beginnt mit der Nutzung der Videodatei und wird fortlaufend automatisch wiedergegeben.


```json
"zonecrossing": {
  "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
  "version": 1,
  "enabled": true,
  "parameters": {
      "VIDEO_URL": "Replace http url here",
      "VIDEO_SOURCE_ID": "personcountgraph",
      "VIDEO_IS_LIVE": false,
        "VIDEO_DECODE_GPU_INDEX": 0,
      "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
      "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
    }
  },

```

## <a name="troubleshooting"></a>Problembehandlung

Falls beim Starten oder Ausführen des Containers Probleme auftreten, helfen Ihnen die Lösungsschritte für häufige Probleme im Artikel [Telemetrie und Problembehandlung](spatial-analysis-logging.md) weiter. Dieser Artikel enthält auch Informationen zum Generieren und Erfassen von Protokollen und zur Erfassung der Systemintegrität.

## <a name="billing"></a>Abrechnung

Der Container für räumliche Analyse sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource für maschinelles Sehen in Ihrem Azure-Konto. Die Verwendung der räumlichen Analyse im Rahmen der öffentlichen Vorschauversion ist derzeit kostenlos. 

Für die Ausführung von Azure Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht mit dem Endpunkt für Messung/Abrechnung verbunden sind. Sie müssen sicherstellen, dass die Container jederzeit Abrechnungsinformationen an den Abrechnungsendpunkt übermitteln können. Cognitive Services-Container senden keine Kundendaten, z. B. das Video oder das analysierte Bild, an Microsoft.


## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurden die Konzepte und der Workflow zum Herunterladen, Installieren und Ausführen des Containers für die räumliche Analyse beschrieben. Zusammenfassung:

* Bei der räumlichen Analyse handelt es sich um einen Linux-Container für Docker.
* Containerimages werden aus Microsoft Container Registry heruntergeladen.
* Containerimages werden als IoT-Module in Azure IoT Edge ausgeführt.
* Konfigurieren des Containers und Bereitstellen auf einem Hostcomputer

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Webanwendung für die Erfassung der Personenanzahl](spatial-analysis-web-app.md)
* [Konfigurieren von Vorgängen zur räumlichen Analysen](spatial-analysis-operations.md)
* [Protokollierung und Problembehandlung](spatial-analysis-logging.md)
* [Leitfaden zur Kameraplatzierung](spatial-analysis-camera-placement.md)
* [Leitfaden zur Platzierung von Zonen und Linien](spatial-analysis-zone-line-placement.md)
