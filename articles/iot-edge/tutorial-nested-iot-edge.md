---
title: 'Tutorial: Erstellen einer Hierarchie für IoT Edge-Geräte – Azure IoT Edge'
description: In diesem Tutorial wird veranschaulicht, wie Sie eine hierarchische Struktur für IoT Edge-Geräte mit Gateways erstellen.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: c1dba383f259e35b143688b2db68f05f1a67def6
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938196"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Tutorial: Erstellen einer Hierarchie für IoT Edge-Geräte (Vorschauversion)

Stellen Sie netzwerkübergreifend Azure IoT Edge-Knoten bereit, die in hierarchischen Ebenen angeordnet sind. Jede Ebene einer Hierarchie entspricht einem Gatewaygerät, mit dem Nachrichten und Anforderungen von den Geräten der direkt untergeordneten Ebene verarbeitet werden.

>[!NOTE]
>Für dieses Feature ist die IoT Edge-Version 1.2, die sich in der öffentlichen Vorschauphase befindet, und die Ausführung von Linux-Containern erforderlich.

Sie können eine Hierarchie mit Geräten so strukturieren, dass nur die oberste Ebene über Konnektivität mit der Cloud verfügt und die untergeordneten Ebenen nur mit der Ebene direkt darüber bzw. darunter kommunizieren können. Diese Netzwerkebenen sind die Grundlage der meisten Unternehmensnetzwerke, die auf der [ISA-95-Norm](https://en.wikipedia.org/wiki/ANSI/ISA-95) basieren.

Das Ziel dieses Tutorials ist die Erstellung einer Hierarchie mit IoT Edge-Geräten, mit der eine Produktionsumgebung simuliert wird. Zum Schluss stellen Sie das [Simulated Temperature Sensor-Modul](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) auf einem Gerät einer untergeordneten Ebene ohne Internetzugriff bereit, indem Sie Containerimages über die Hierarchie herunterladen.

Zur Erreichung dieses Ziels werden in diesem Tutorial Schritt für Schritt die Erstellung einer Hierarchie mit IoT Edge-Geräten, die Bereitstellung von IoT Edge-Runtimecontainern auf Ihren Geräten und die lokale Konfiguration der Geräte beschrieben. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen und Definieren der Beziehungen in einer Hierarchie mit IoT Edge-Geräten
> * Konfigurieren der IoT Edge-Runtime auf den Geräten in Ihrer Hierarchie
> * Installieren von einheitlichen Zertifikaten in Ihrer gesamten Gerätehierarchie
> * Hinzufügen von Workloads zu den Geräten Ihrer Hierarchie
> * Verwenden eines API-Proxymoduls zum sicheren Weiterleiten von HTTP-Datenverkehr über einen einzelnen Port von Ihren Geräten auf den unteren Ebenen.

In diesem Tutorial werden die folgenden Netzwerkebenen definiert:

* **Oberste Ebene**: IoT Edge-Geräte auf dieser Ebene können eine direkte Verbindung mit der Cloud herstellen.

* **Untergeordnete Ebene**: IoT Edge-Geräte auf dieser Ebene können keine direkte Verbindung mit der Cloud herstellen. Zum Senden und Empfangen von Daten müssen sie den Weg über ein oder mehrere zwischengeschaltete IoT Edge-Geräte nehmen.

In diesem Tutorial wird der Einfachheit halber eine Hierarchie mit nur zwei Geräten verwendet. Ein Gerät (**topLayerDevice**) befindet sich auf der obersten Ebene der Hierarchie und kann eine direkte Verbindung mit der Cloud herstellen. Dieses Gerät wird auch als **übergeordnetes Gerät** bezeichnet. Das andere Gerät (**lowerLayerDevice**) befindet sich auf der untergeordneten Ebene der Hierarchie und kann keine direkte Verbindung mit der Cloud herstellen. Dieses Gerät wird auch als **untergeordnetes Gerät** bezeichnet. Sie können weitere untergeordnete Geräte hinzufügen, um Ihre Produktionsumgebung darzustellen. Die Konfiguration aller zusätzlichen untergeordneten Geräte basiert auf der Konfiguration von **lowerLayerDevice**.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um eine Hierarchie mit IoT Edge-Geräten zu erstellen:

* Einen Computer (Windows oder Linux) mit Internetverbindung.
* Ein Azure-Konto mit einem gültigen Abonnement. Wenn Sie kein [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.
* Eine [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md) in Azure mit dem Tarif „Free“ oder „Standard“.
* Azure CLI v2.3.1 mit einer Installation der Azure IoT-Erweiterung v0.10.6 oder höher. In diesem Tutorial wird [Azure Cloud Shell](../cloud-shell/overview.md) verwendet. Falls Sie mit Azure Cloud Shell noch nicht vertraut sein sollten, helfen Ihnen die [Informationen in der Schnellstartanleitung](./quickstart-linux.md#prerequisites) weiter.
* Zwei Linux-Geräte, die als IoT Edge-Geräte konfiguriert werden. Sind keine Geräte verfügbar, können Sie zwei virtuelle Azure-Computer erstellen, indem Sie den Platzhaltertext im folgenden Befehl ersetzen und diesen zweimal ausführen:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

Sie können dieses Szenario auch ausprobieren, indem Sie das Skript für das [Beispiel „Azure IoT Edge for Industrial IoT“](https://aka.ms/iotedge-nested-sample) verwenden. Hiermit werden virtuelle Azure-Computer als vorkonfigurierte Geräte bereitgestellt, um eine Produktionsumgebung zu simulieren.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurieren Ihrer Hierarchie für IoT Edge-Geräte

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Erstellen einer Hierarchie für IoT Edge-Geräte

Sie können den ersten Schritt (Erstellung Ihrer IoT Edge-Geräte) über das Azure-Portal oder die Azure CLI ausführen. In diesem Tutorial wird eine Hierarchie mit zwei IoT Edge-Geräten erstellt: **topLayerDevice** und das untergeordnete Gerät **lowerLayerDevice**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer IoT Hub-Instanz.

1. Wählen Sie im Menü im linken Bereich unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.

1. Wählen Sie **+ IoT Edge-Gerät hinzufügen** aus. Da dies das Gerät der obersten Ebene ist, sollten Sie eine geeignete eindeutige Geräte-ID eingeben. Wählen Sie **Speichern** aus.

1. Wählen Sie erneut die Option **+ IoT Edge-Gerät hinzufügen** aus. Da dies das untergeordnete Gerät ist, sollten Sie eine geeignete eindeutige Geräte-ID eingeben.

1. Wählen Sie die Option **Übergeordnetes Gerät festlegen** und dann in der Liste mit den Geräten Ihr Gerät der obersten Ebene und **OK** aus. Wählen Sie **Speichern** aus.

   ![Festlegen des übergeordneten Geräts für das untergeordnete Gerät](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Geben Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl ein, um auf Ihrem Hub ein IoT Edge-Gerät zu erstellen. Da dies das Gerät der obersten Ebene ist, sollten Sie eine geeignete eindeutige Geräte-ID eingeben:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Geben Sie den folgenden Befehl ein, um Ihr untergeordnetes IoT Edge-Gerät zu erstellen und die hierarchische Beziehung zwischen den Geräten einzurichten:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Notieren Sie sich jeweils die Verbindungszeichenfolgen der IoT Edge-Geräte. Diese werden später noch benötigt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zum Abschnitt **IoT Edge** Ihrer IoT Hub-Instanz.

1. Klicken Sie in der Liste mit den Geräten auf die Geräte-ID eines Geräts.

1. Wählen Sie im Feld **Primäre Verbindungszeichenfolge** die Option **Kopieren** aus, und fügen Sie sie an einem Ort Ihrer Wahl ein.

1. Wiederholen Sie diesen Schritt für alle anderen Geräte.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Geben Sie in [Azure Cloud Shell](https://shell.azure.com/) für jedes Gerät den folgenden Befehl ein, um die Verbindungszeichenfolge Ihres Geräts abzurufen, und fügen Sie sie an einem Ort Ihrer Wahl ein:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Erstellen von Zertifikaten

Für alle Geräte eines [Gatewayszenarios](iot-edge-as-gateway.md) ist ein gemeinsames Zertifikat erforderlich, um dafür sichere Verbindungen einzurichten. Führen Sie die folgenden Schritte aus, um in diesem Szenario Demozertifikate für beide Geräte zu erstellen.

Zum Erstellen von Demozertifikaten auf einem Linux-Gerät müssen Sie die Generierungsskripts klonen und für die lokale Ausführung in Bash einrichten.

1. Klonen Sie das IoT Edge-Git-Repository, das Skripts zum Generieren von Demozertifikaten enthält.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Navigieren Sie zu dem Verzeichnis, in dem Sie arbeiten möchten. Alle Zertifikat- und Schlüsseldateien werden in diesem Verzeichnis erstellt.

1. Kopieren Sie die Konfigurations- und Skriptdateien aus dem geklonten IoT Edge-Repository in Ihr Arbeitsverzeichnis.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Erstellen Sie das Zertifikat der Stammzertifizierungsstelle und ein Zwischenzertifikat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt, aber wir verwenden die folgende Datei als **Zertifikat der Stammzertifizierungsstelle** für die Gatewayhierarchie:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Erstellen Sie mit dem folgenden Befehl zwei Gruppen mit IoT Edge-Zertifizierungsstellenzertifikaten und privaten Schlüsseln: eine für das Gerät der obersten Ebene und eine für das untergeordnete Gerät. Geben Sie für die Zertifizierungsstellenzertifikate aussagekräftige Namen an, damit sie gut voneinander unterschieden werden können.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Mit diesem Skriptbefehl werden mehrere Zertifikat- und Schlüsseldateien erstellt, aber wir verwenden das folgende Zertifikat und Schlüsselpaar auf jedem IoT Edge-Gerät (Verweis in der Datei „config.yaml“):

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Für jedes Gerät werden eine Kopie des Zertifikats der Stammzertifizierungsstelle sowie Kopien des eigenen Zertifizierungsstellenzertifikats und privaten Schlüssels für das Gerät benötigt. Sie können ein USB-Laufwerk oder [Secure File Copy](https://www.ssh.com/ssh/scp/) verwenden, um die Zertifikate auf die einzelnen Geräte zu verschieben.

1. Installieren Sie nach Abschluss der Zertifikatübertragung jeweils das Zertifikat der Stammzertifizierungsstelle für die Geräte.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Mit diesem Befehl sollte ausgegeben werden, dass unter „/etc/ssl/certs“ ein Zertifikat hinzugefügt wurde.

### <a name="install-iot-edge-on-the-devices"></a>Installieren von IoT Edge auf den Geräten

Installieren Sie IoT Edge, indem Sie die unten angegebenen Schritte auf beiden Geräten ausführen.

1. Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

   ```bash
   sudo apt-get update
   ```

1. Installieren Sie die Moby-Engine.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Installieren Sie hsmlib und den IoT Edge-Daemon. Wenn Sie die Ressourcen für andere Linux-Distributionen anzeigen möchten, sehen Sie sich das [GitHub-Release](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) an. <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0.rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Konfigurieren der IoT Edge-Runtime

Konfigurieren Sie die IoT Edge-Runtime, indem Sie auf beiden Geräten die unten angegebenen Schritte ausführen. Die Konfiguration der IoT Edge-Runtime für Ihre Geräte umfasst vier Schritte, in denen jeweils die IoT Edge-Konfigurationsdatei bearbeitet wird:

1. Stellen Sie die Geräte jeweils manuell bereit, indem Sie die Verbindungszeichenfolge des Geräts der Konfigurationsdatei hinzufügen.

1. Beenden Sie die Einrichtung der Zertifikate Ihres Geräts, indem Sie in der Konfigurationsdatei auf das Zertifizierungsstellenzertifikat des Geräts, den privaten Schlüssel der Zertifizierungsstelle für das Gerät und das Zertifikat der Stammzertifizierungsstelle verweisen.

1. Führen Sie für das System einen Bootstrapvorgang durch, indem Sie den IoT Edge-Agent verwenden.

1. Aktualisieren Sie den Parameter **hostname** für Ihr Gerät der **obersten Ebene**, und aktualisieren Sie sowohl den Parameter **hostname** als auch den Parameter **parent_hostname** für Ihre **untergeordneten** Geräte.

Führen Sie die folgenden Schritte aus, und starten Sie den IoT Edge-Dienst neu, um Ihre Geräte zu konfigurieren.

1. Öffnen Sie auf jedem Gerät die IoT Edge-Konfigurationsdatei.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Suchen Sie nach den Bereitstellungskonfigurationen in der Datei, und heben Sie die Auskommentierung des Abschnitts **Manual provisioning configuration using a connection string** auf.

1. Aktualisieren Sie den Wert von **device_connection_string** durch die Verbindungszeichenfolge Ihres IoT Edge-Geräts. Stellen Sie sicher, dass alle anderen Bereitstellungsabschnitte auskommentiert sind. Stellen Sie sicher, dass der Zeile **provisioning:** kein Leerzeichen vorangestellt ist und dass geschachtelte Elemente jeweils um zwei Leerzeichen eingerückt sind.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Verwenden Sie zum Einfügen des Inhalts der Zwischenablage in Nano `Shift+Right Click`, oder drücken Sie `Shift+Insert`.

1. Suchen Sie nach dem Abschnitt **certificates**. Heben Sie die Auskommentierung auf, und aktualisieren Sie die drei Zertifikatfelder so, dass sie auf die Zertifikate verweisen, die Sie im vorherigen Abschnitt erstellt und auf das IoT Edge-Gerät verschoben haben. Geben Sie die URI-Pfade im Format `file:///<path>/<filename>` an.

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Suchen Sie für Ihr Gerät der **obersten Ebene** nach dem Parameter **hostname**. Aktualisieren Sie den Wert auf den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse des IoT Edge-Geräts. Verwenden Sie den von Ihnen gewählten Wert einheitlich auf allen Geräten Ihrer Hierarchie.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Aktualisieren Sie für Ihre **untergeordneten** IoT Edge-Geräte die Konfigurationsdatei so, dass darin auf den FQDN oder die IP-Adresse des übergeordneten Geräts verwiesen wird (Verwendung des Inhalts im Feld **hostname** des übergeordneten Geräts). Lassen Sie diesen Parameter für IoT Edge-Geräte der **obersten Ebene** auskommentiert.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Aktualisieren Sie bei Hierarchien mit mehr als einer untergeordneten Ebene das Feld *parent_hostname* mit dem FQDN des Geräts auf der direkt übergeordneten Ebene.

1. Suchen Sie für Ihr Gerät der **obersten Ebene** nach dem YAML-Abschnitt **agent**, und aktualisieren Sie den Imagewert auf die richtige Version des IoT Edge-Agents. In diesem Fall verweisen wir für den IoT Edge-Agent der obersten Ebene auf Azure Container Registry mit der öffentlichen Vorschauversion des verfügbaren Images für den IoT Edge-Agent.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Aktualisieren Sie für IoT Edge-Geräte auf den **untergeordneten Ebenen** den Domänennamen des Imagewerts so, dass auf den FQDN oder die IP-Adresse des übergeordneten Geräts verwiesen wird, gefolgt vom API-Proxyport 8000. Sie fügen das API-Proxymodul im nächsten Abschnitt hinzu.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Speichern und schließen Sie die Datei.

   `CTRL + X`, `Y`, `Enter`

1. Starten Sie nach Eingabe der Bereitstellungsinformationen in der Konfigurationsdatei den Daemon neu:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Bereitstellen von Modulen auf dem Gerät der obersten Ebene

Die restlichen Schritte für die Konfiguration der IoT Edge-Runtime und die Bereitstellung von Workloads werden aus der Cloud entweder über das Azure-Portal oder die Azure CLI ausgeführt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Im [Azure-Portal](https://ms.portal.azure.com/):

1. Navigieren Sie zu Ihrer IoT Hub-Instanz.

1. Wählen Sie im Menü im linken Bereich unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.

1. Klicken Sie in der Liste mit den Geräten auf die Geräte-ID Ihres Edgegeräts der **obersten Ebene**.

1. Wählen Sie in der oberen Leiste **Module festlegen** aus.

1. Wählen Sie neben dem Zahnradsymbol die Option **Runtimeeinstellungen** aus.

1. Geben Sie unter **Edge Hub** im Imagefeld die Zeichenfolge `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` ein.

   ![Bearbeiten des Edge-Hub-Images](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Fügen Sie Ihrem Edge-Hubmodul die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Bearbeiten der Umgebungsvariablen für den Edge-Hub](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Geben Sie unter **Edge-Agent** im Imagefeld die Zeichenfolge `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2` ein. Wählen Sie **Speichern** aus.

1. Fügen Sie das Docker-Registrierungsmodul Ihrem Gerät der obersten Ebene hinzu. Wählen Sie **+ Hinzufügen** und in der Dropdownliste die Option **IoT Edge-Modul** aus. Geben Sie den Namen `registry` für Ihr Docker-Registrierungsmodul und `registry:latest` für den Image-URI ein. Fügen Sie als Nächstes Umgebungsvariablen hinzu, und erstellen Sie Optionen, um für Ihr lokales Registrierungsmodul auf die Microsoft-Containerregistrierung zu verweisen, damit hierüber Containerimages heruntergeladen und unter registry:5000 bereitgestellt werden können.

1. Geben Sie unter der Registerkarte mit den Umgebungsvariablen das folgende Name/Wert-Paar als Umgebungsvariable ein:

    | Name | Wert |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Geben Sie auf der Registerkarte mit den Optionen für die Containererstellung den folgenden JSON-Code ein:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Fügen Sie als Nächstes das API-Proxymodul Ihrem Gerät der obersten Ebene hinzu. Wählen Sie **+ Hinzufügen** und in der Dropdownliste die Option **Marketplace-Modul** aus. Suchen Sie nach `IoT Edge API Proxy`, und wählen Sie das Modul aus. Vom IoT Edge-API-Proxy wird Port 8000 verwendet, und er ist standardmäßig für die Nutzung Ihres Registrierungsmoduls mit dem Namen `registry` an Port 5000 konfiguriert.

1. Wählen Sie die Option **Überprüfen und erstellen** und dann **Erstellen** aus, um die Bereitstellung durchzuführen. Von der IoT Edge-Runtime Ihres Geräts der obersten Ebene, die über Internetzugriff verfügt, werden die **öffentlichen Vorschauversionen** der Konfigurationen für IoT Edge-Hub und den IoT Edge-Agent gepullt und ausgeführt.

   ![Vollständige Bereitstellung mit Edge-Hub, Edge-Agent, Registrierungsmodul und API-Proxymodul](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Geben Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl ein, um die Datei „deployment.json“ zu erstellen:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Kopieren Sie den unten angegebenen JSON-Code in die Datei „deployment.json“, und speichern und schließen Sie sie.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Geben Sie den folgenden Befehl ein, um eine Bereitstellung auf Ihrem Edgegerät der obersten Ebene zu erstellen:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Bereitstellen von Modulen auf dem Gerät der untergeordneten Ebene

Sie können sowohl das Azure-Portal als auch die Azure CLI verwenden, um Workloads aus der Cloud auf Ihren Geräten der **untergeordneten Ebenen** bereitzustellen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Im [Azure-Portal](https://ms.portal.azure.com/):

1. Navigieren Sie zu Ihrer IoT Hub-Instanz.

1. Wählen Sie im Menü im linken Bereich unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.

1. Klicken Sie in der Liste mit den IoT Edge-Geräten auf die Geräte-ID Ihres Geräts auf der untergeordneten Ebene.

1. Wählen Sie in der oberen Leiste **Module festlegen** aus.

1. Wählen Sie neben dem Zahnradsymbol die Option **Runtimeeinstellungen** aus.

1. Geben Sie unter **Edge Hub** im Imagefeld die Zeichenfolge `$upstream:8000/azureiotedge-hub:1.2.0-rc2` ein.

1. Fügen Sie Ihrem Edge-Hubmodul die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Geben Sie unter **Edge-Agent** im Imagefeld die Zeichenfolge `$upstream:8000/azureiotedge-agent:1.2.0-rc2` ein. Wählen Sie **Speichern** aus.

1. Fügen Sie das Temperatursensormodul hinzu. Wählen Sie **+ Hinzufügen** und in der Dropdownliste die Option **Marketplace-Modul** aus. Suchen Sie nach `Simulated Temperature Sensor`, und wählen Sie das Modul aus.

1. Wählen Sie unter **IoT Edge-Module** das eben hinzugefügte Modul `Simulated Temperature Sensor` aus, und aktualisieren Sie den Image-URI so, dass er auf `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` verweist.

1. Wählen Sie **Speichern**, **Überprüfen und erstellen** und **Erstellen** aus, um die Bereitstellung durchzuführen.

   ![Vollständige Bereitstellung mit Edge-Hub, Edge-Agent und Simulated Temperature Sensor](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Geben Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl ein, um die Datei „deployment.json“ zu erstellen:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Kopieren Sie den unten angegebenen JSON-Code in die Datei „deployment.json“, und speichern und schließen Sie sie.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Geben Sie den folgenden Befehl ein, um eine „set-modules“-Bereitstellung auf Ihrem Edgegerät der untergeordneten Ebene zu erstellen:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden.

So löschen Sie die Ressourcen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.

2. Wählen Sie den Namen der Ressourcengruppe aus, die Ihre IoT Edge-Testressourcen enthält. 

3. Überprüfen Sie die Liste der Ressourcen in Ihrer Ressourcengruppe. Wenn Sie alle löschen möchten, klicken Sie auf **Ressourcengruppe löschen**. Wenn Sie nur einige Ressourcen löschen möchten, klicken Sie auf die jeweiligen Ressourcen, um sie einzeln zu löschen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie zwei IoT Edge-Geräte als Gateways konfiguriert und eins als übergeordnetes Gerät des anderen festgelegt. Anschließend haben Sie gezeigt, wie Sie ein Containerimage über ein Gateway auf das untergeordnete Gerät pullen.

Sie können nun die anderen Tutorials bearbeiten, um noch mehr darüber zu erfahren, wie sich mit Azure IoT Edge weitere Lösungen für Ihr Unternehmen erstellen lassen.

> [!div class="nextstepaction"]
> [Bereitstellen eines Azure Machine Learning-Modells als Modul](tutorial-deploy-machine-learning.md)