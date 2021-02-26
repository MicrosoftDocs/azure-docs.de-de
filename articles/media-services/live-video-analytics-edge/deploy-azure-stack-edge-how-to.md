---
title: Bereitstellen von Live Video Analytics in Azure Stack Edge
description: In diesem Artikel werden die Schritte aufgelistet, die Sie bei der Bereitstellung von Live Video Analytics in Azure Stack Edge unterstützen.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: cc3dcfaa96034e807d3d82e75eedc0f6a82eff08
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551007"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Bereitstellen von Live Video Analytics in Azure Stack Edge

In diesem Artikel werden die Schritte aufgelistet, die Sie bei der Bereitstellung von Live Video Analytics in Azure Stack Edge unterstützen. Nachdem das Gerät eingerichtet und aktiviert wurde, ist es für die Bereitstellung von Live Video Analytics bereit. 

Wir stellen Live Video Analytics über IoT Hub bereit. Die Azure Stack Edge-Ressourcen machen jedoch eine Kubernetes-API verfügbar, die dem Kunden das Bereitstellen zusätzlicher nicht IoT Hub-fähiger Ressourcen ermöglicht, die mit Live Video Analytics verbunden werden können. 

> [!TIP]
> Die Verwendung der Kubernetes (K8s)-API für die benutzerdefinierte Bereitstellung ist ein komplexerer Fall. Es wird empfohlen, statt Verwendung der Kubernetes-API Edge-Module zu erstellen und die Bereitstellung in jeder Azure Stack Edge-Ressource über IoT Hub auszuführen. In diesem Artikel werden die Schritte zum Bereitstellen des Live Video Analytics-Moduls mit IoT Hub erläutert.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement, für das Sie über [Besitzerrechte](../../role-based-access-control/built-in-roles.md#owner) verfügen
* Eine [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)-Ressource
   
* Ein [IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* Ein [Dienstprinzipal](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) für das Live Video Analytics-Modul.

   Verwenden Sie eine dieser Regionen, in denen IoT Hub verfügbar ist: USA, Osten 2, USA, Mitte; USA, Norden-Mitte; Japan, Osten; USA, Westen 2; USA, Westen-Mitte, Kanada, Osten, Vereinigtes Königreich, Süden, Frankreich, Mitte, Frankreich, Süden, Schweiz, Norden, Schweiz, Westen und Japan, Westen.
* Speicherkonto

    Es wird empfohlen, Speicherkonten vom Typ „Allgemein V2“ (GPv2) zu verwenden.  
    Weitere Informationen über ein [Speicherkonto vom Typ „Allgemein v2“](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)
* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
* Vergewissern Sie sich, dass für das Netzwerk, mit dem der Entwicklungscomputer verbunden ist, Advanced Message Queueing Protocol über Port 5671 zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Konfigurieren von Azure Stack Edge für die Verwendung von Live Video Analytics

Azure Stack Edge ist eine Hardware-as-a-Service-Lösung und ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Erfahren Sie mehr über [Azure Stack Edge und ausführliche Setupanweisungen ](../../databox-online/azure-stack-edge-deploy-prep.md). Folgen Sie für den Einstieg den Anweisungen unter den nachstehenden Links:

* [Erstellung von Azure Stack Edge-/Data Box Gateway-Ressourcen](../../databox-online/azure-stack-edge-deploy-prep.md)
* [Installation und Einrichtung](../../databox-online/azure-stack-edge-deploy-install.md)
* [Verbindung und Aktivierung](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [Anfügen von IoT Hub an Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-configure-compute#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Aktivieren von Computevoraussetzungen auf der lokalen Benutzeroberfläche von Azure Stack Edge

Stellen Sie Folgendes sicher, bevor Sie fortfahren:

* Sie haben die Azure Stack Edge-Ressource aktiviert.
* Sie haben Zugriff auf ein Windows-Clientsystem mit PowerShell 5.0 oder höher, um auf die Azure Stack Edge-Ressource zuzugreifen.
* Zum Bereitstellen eines Kubernetes-Clusters müssen Sie die Azure Stack Edge-Ressource über die [lokale Webbenutzeroberfläche](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) konfigurieren. 
    
    * Wechseln Sie auf der lokalen Webbenutzeroberfläche des Geräts zur Seite „Compute“, um Compute zu aktivieren.
    
        * Wählen Sie eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten. Wählen Sie „Aktivieren“ aus. Das Aktivieren des Computevorgangs führt zur Erstellung eines virtuellen Switches auf Ihrem Gerät für diese Netzwerkschnittstelle.
        * Lassen Sie die IP-Adressen der Kubernetes-Testknoten und der externen Kubernetes-Dienste leer.
        * Wählen Sie „Anwenden“ aus – dieser Vorgang dauert ca. 2 Minuten.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Computevoraussetzungen auf der lokalen Benutzeroberfläche von Azure Stack Edge":::

        * Wenn für die Kubernetes-API und Azure Stack Edge-Ressource kein DNS konfiguriert ist, können Sie die Windows-Hostdatei aktualisieren.
        
            * Öffnen Sie als Administrator einen Text-Editor.
            * Öffnen Sie die Datei „C:\Windows\System32\Drivers\etc\hosts“.
            * Fügen Sie der Datei die IPv4-Adresse für den Kubernetes-API-Gerätenamen und den Hostnamen hinzu. (Diese Informationen finden Sie im Azure Stack Edge-Portal im Abschnitt „Geräte“.)
            * Speichern und schließen

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Bereitstellen des Live Video Analytics-Edge-Moduls mit dem Azure-Portal

Hierfür werden nur bestimmte Schritte aus dem Artikel zum [Bereitstellen von Live Video Analytics über IoT Hub](deploy-iot-edge-device.md) durchgeführt.

1. Überspringen Sie die Schritte für die Erstellung von Benutzern und Gruppen, und wechseln Sie zu [Bereitstellen des Live Video Analytics Edge-Moduls](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module). Führen Sie die hier beschriebenen Schritte durch.
1. In „Optionen für Containererstellung“ müssen Sie keine Umgebungsvariablen festlegen. Überspringen Sie also diesen Schritt.
1. Öffnen Sie die Registerkarte Optionen für Containererstellung.

   * Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in das Feld ein, um die Größe der vom Modul erzeugten Protokolldateien einzuschränken.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Wenn das gRPC-Protokoll mit Übertragung von gemeinsam genutztem Speicher verwendet wird, verwenden Sie für den Zugriff auf gemeinsam genutzten Speicher zwischen Live Video Analytics und Rückschlusslösungen den Host-IPC-Modus.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > Der Abschnitt „Binds“ im JSON-Code enthält zwei Einträge. Die im obigen Abschnitt „Binds“ erwähnten Verzeichnisse werden automatisch von LVA erstellt.  
        Sie können die Edgegerätebindungen aktualisieren, müssen dabei jedoch sicherstellen, dass diese Verzeichnisse auf dem Gerät vorhanden sind.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": Hiermit werden die persistenten Anwendungskonfigurationsdaten aus dem Container gebunden und auf dem Edge-Gerät gespeichert.
    * "/var/media:/var/media": Dadurch werden die Medienordner zwischen dem Edge-Gerät und dem Container gebunden. Dies wird zum Speichern der Videoaufzeichnungen verwendet, wenn Sie eine Mediengraphtopologie ausführen, die das Speichern von Videoclips auf dem Edge-Gerät unterstützt.
        
1. Fahren Sie mit den Schritten in der Dokumentation fort, und legen Sie die „Einstellungen für Modulzwilling“ fest.
1. Klicken Sie auf **Weiter**: „Routen“, um mit dem Abschnitt über Routen fortzufahren. Geben Sie Routen an.

    Behalten Sie die Standardrouten bei, und klicken Sie auf Weiter: Überprüfen und erstellen, um mit dem Abschnitt zur Überprüfung fortzufahren.
1. [Überprüfen Ihrer Bereitstellung](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>(Optional) Einrichten von Docker-Volumebereitstellungen

Wenn Sie die Daten in den Arbeitsverzeichnissen anzeigen möchten, führen Sie die folgenden Schritte aus, um vor der Bereitstellung die Docker-Volumebereitstellungen einzurichten. 

Diese Schritte umfassen das Erstellen eines Gatewaybenutzers und das Einrichten von Dateifreigaben zum Anzeigen der Inhalte des Live Video Analytics-Arbeitsverzeichnisses und des Live Video Analytics-Medienordners.

> [!NOTE]
> Bindungsbereitstellungen werden unterstützt, mit Volumebereitstellungen können jedoch die Daten angezeigt und ggf. remote kopiert werden. Es ist möglich, sowohl Bindungs- als auch Volumebereitstellungen zu verwenden, diese können jedoch nicht auf denselben Containerpfad zeigen.

1. Öffnen Sie das Azure-Portal, und navigieren Sie zu Ihrer Azure Stack Edge-Ressource.
1. Erstellen Sie einen **Gateway-Benutzer**, der auf Freigaben zugreifen kann.
    
    1. Klicken Sie im linken Navigationsbereich auf **Cloudspeichergateway**.
    1. Klicken Sie im linken Navigationsbereich auf **Benutzer**.
    1. Klicken Sie auf **+ Benutzer hinzufügen**, um den Benutzernamen und das Kennwort festzulegen. (Empfohlen: `lvauser`).
    1. Klicken Sie auf **Hinzufügen**.
    
1. Erstellen Sie eine **lokale Freigabe** für die Persistenz von Live Video Analytics.

    1. Klicken Sie auf **Cloudspeichergateway > Freigaben**.
    1. Klicken Sie auf **+ Add Shares**  (+ Freigaben hinzufügen).
    1. Legen Sie einen Freigabenamen fest. (Empfohlen: `lva`).
    1. Behalten Sie den Freigabetyp SMB bei.
    1. Stellen Sie sicher, dass **Freigabe mit Edgecomputing verwenden** aktiviert ist.
    1. Stellen Sie sicher, dass **Als lokale Edge-Freigabe konfigurieren** aktiviert ist.
    1. Gewähren Sie unter „Benutzerdetails“ dem zuletzt erstellten Benutzer Zugriff auf die Freigabe.
    1. Klicken Sie auf **Erstellen**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Lokale Freigabe":::  

    > [!TIP]
    > Stellen Sie mithilfe des mit Azure Stack Edge verbundenen Windows-Clients eine Verbindung mit den SMB-Freigaben her, indem Sie [die in diesem Dokument beschriebenen Schritte](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) ausführen.    

1. Erstellen Sie eine Remotefreigabe für den Dateisynchronisierungsspeicher.

    1. Erstellen Sie zunächst ein Blob Storage-Konto in derselben Region, indem Sie auf **Cloudspeichergateway > Speicherkonten** klicken.
    1. Klicken Sie auf **Cloudspeichergateway > Freigaben**.
    1. Klicken Sie auf **+ Add Shares**  (+ Freigaben hinzufügen).
    1. Legen Sie einen Freigabenamen fest. (Empfohlen: „media“).
    1. Behalten Sie den Freigabetyp SMB bei.
    1. Stellen Sie sicher, dass **Freigabe mit Edgecomputing verwenden** aktiviert ist.
    1. Stellen Sie sicher, dass **Als lokale Edge-Freigabe konfigurieren** nicht aktiviert ist.
    1. Wählen Sie das zuletzt erstellte Speicherkonto aus.
    1. Legen Sie einen Containernamen fest.
    1. Legen Sie den Speichertyp auf Blockblob fest.
    1. Gewähren Sie unter „Benutzerdetails“ dem zuletzt erstellten Benutzer Zugriff auf die Freigabe.
    1. Klicken Sie auf **Erstellen**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Remotefreigabe":::
    
    
1. Aktualisieren Sie die „Optionen für die Containererstellung“ des Live Video Analytics Edge-Moduls (siehe Punkt 4 in [Hinzufügen von Modulen](deploy-iot-edge-device.md#add-modules)), um Volumebereitstellungen zu verwenden.

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>Überprüfen, ob das Modul ausgeführt wird

Im letzten Schritt wird sichergestellt, dass das Modul verbunden ist und wie erwartet ausgeführt wird. Der Laufzeitstatus des Moduls sollte für Ihr IoT Edge-Gerät auf der IoT Hub-Ressource „Wird ausgeführt“ lauten.

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob das Modul ausgeführt wird:

1. Kehren Sie im Azure-Portal zur Azure Stack Edge-Ressource zurück.
1. Wählen Sie die Kachel „Module“ aus. Das Blatt Module wird geöffnet. Identifizieren Sie in der Liste mit den Modulen das von Ihnen bereitgestellte Modul. Der Laufzeitstatus des hinzugefügten Moduls sollte Wird ausgeführt lauten.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Benutzerdefiniertes Modul":::

### <a name="configure-the-azure-iot-tools-extension"></a>Konfigurieren der Azure IoT Tools-Erweiterung

Befolgen Sie die unten angegebene Anleitung, um mit der Azure IoT Tools-Erweiterung eine Verbindung mit Ihrem IoT-Hub herzustellen.

1. Wählen Sie in Visual Studio „Ansicht“ > „Explorer“ aus. Oder drücken Sie STRG+UMSCHALT+E.
1. Wählen Sie unten links auf der Registerkarte Explorer die Option Azure IoT Hub aus.
1. Wählen Sie das Symbol Weitere Optionen aus, um das Kontextmenü anzuzeigen. Wählen Sie anschließend IoT Hub-Verbindungszeichenfolge festlegen aus.
1. Geben Sie Ihre IoT Hub-Verbindungszeichenfolge ein, wenn das entsprechende Eingabefeld angezeigt wird. 

   * Um die Verbindungszeichenfolge abzurufen, navigieren Sie im Azure-Portal zu IoT Hub, und klicken Sie im linken Navigationsbereich auf „Richtlinien für gemeinsamen Zugriff“.
   * Klicken Sie auf „iothubowner“, um die Schlüssel für gemeinsamen Zugriff abzurufen.
   * Kopieren Sie den Primärschlüssel der Verbindungszeichenfolge, und fügen Sie ihn in Visual Studio Code in das Eingabefeld ein.

   Die Verbindungszeichenfolge lautet:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Wenn die Verbindung erfolgreich hergestellt wird, wird die Liste mit den Edgegeräten angezeigt. Azure Stack Edge wird angezeigt. Sie können nun über das Kontextmenü Ihre IoT Edge-Geräte verwalten und mit Azure IoT Hub interagieren. Um die auf dem Edgegerät bereitgestellten Module anzuzeigen, erweitern Sie unter dem Azure Stack-Gerät den Knoten „Module“.
    
## <a name="troubleshooting"></a>Problembehandlung

* Kubernetes-API-Zugriff (kubectl).

    * Gehen Sie gemäß der Dokumentation vor, um Ihren Computer für den [Zugriff auf den Kubernetes-Cluster](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues) zu konfigurieren.
    * Alle bereitgestellten IoT Edge Module verwenden den `iotedge`-Namespace. Schließen Sie ihn ein, wenn Sie kubectl verwenden.
* Modulprotokolle

    Auf das Tool `iotedge` kann nicht zugegriffen werden, um Protokolle abzurufen. Sie müssen [kubectl-Protokolle](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) verwenden, um die Protokolle anzuzeigen oder an eine Datei zu übergeben. Beispiel: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Pod- und Knotenmetriken

    Verwenden Sie den Befehl [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top), um Pod- und Knotenmetriken anzuzeigen. (Diese Funktion ist in der nächsten Azure Stack Edge-Version verfügbar – > v2007.)<br/>`kubectl top pods -n iotedge`
* Modulnetzwerke Zur Modulermittlung in Azure Stack Edge muss für das Modul in „createOptions“ Hostportbindung festgelegt sein. Das Modul ist dann über `moduleName:hostport` adressierbar.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Volumebereitstellung

    Ein Modul kann nicht gestartet werden, wenn der Container versucht, ein Volume in ein vorhandenes und nicht leeres Verzeichnis einzubinden.
* Shared Memory

    Gemeinsam genutzter Arbeitsspeicher in Azure Stack Edge-Ressourcen wird mit Host-IPC podübergreifend in jedem Namespace unterstützt.
    Konfigurieren von gemeinsam genutztem Speicher in einem Edgemodul für die Bereitstellung über IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* (Erweitert) Gemeinsame Anordnung von Pods

    Wenn Sie mit K8s benutzerdefinierte Rückschlusslösungen bereitstellen, die über gRPC mit Live Video Analytics kommunizieren, müssen Sie sicherstellen, dass die Pods auf denselben Knoten wie Live Video Analytics-Module bereitgestellt werden.

    * Option 1: Verwenden von Knotenaffinität und integrierten Knotenbezeichnungen für die gemeinsame Anordnung.

    Die benutzerdefinierte NodeSelector-Konfiguration scheint derzeit keine Option zu sein, da die Benutzer über keinen Zugriff zum Festlegen von Bezeichnungen für die Knoten verfügen. Abhängig von der Topologie und den Benennungskonventionen des Kunden können sie jedoch möglicherweise [integrierte Knotenbezeichnungen](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels) verwenden. Um eine gemeinsame Anordnung zu erreichen, kann dem Rückschlusspodmanifest der Abschnitt „nodeAffinity“ hinzugefügt werden, der auf Azure Stack Edge-Ressourcen mit Live Video Analytics verweist.
    * Option 2: Verwenden von Podaffinität für die gemeinsame Anordnung (empfohlen).
Kubernetes unterstützt [Podaffinität](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity), sodass Pods auf demselben Knoten geplant werden können. Um eine gemeinsame Anordnung zu erreichen, kann dem Rückschlusspodmanifest der Abschnitt „podAffinity“ hinzugefügt werden, der auf das Live Video Analytics-Modul verweist.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Nächste Schritte

Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie direkte Methoden aufrufen. [Rufen Sie die direkten Methoden](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) für das Modul auf.