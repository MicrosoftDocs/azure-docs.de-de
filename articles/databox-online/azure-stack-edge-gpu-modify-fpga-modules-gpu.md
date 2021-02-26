---
title: Bearbeiten der IoT Edge-Module auf FPGA-Geräten zur Ausführung auf einem Azure Stack Edge Pro-GPU-Gerät
description: In diesem Artikel wird beschrieben, welche Änderungen auf vorhandenen FPGA-Geräten für vorhandene IoT Edge-Module durchgeführt werden müssen, damit die Ausführung auf Ihrem Azure Stack Edge Pro-GPU-Gerät möglich ist.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6e9e1319b90ab859c63c022e478bc26c4b8aedeb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550217"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Ausführen vorhandener IoT Edge-Module auf Azure Stack Edge Pro-FPGA-Geräten auf einem Azure Stack Edge Pro-GPU-Gerät

In diesem Artikel finden Sie die Details zu erforderlichen Änderungen an einem Docker-basierten IoT Edge-Modul, das in einem Azure Stack Edge Pro-FPGA ausgeführt wird, damit die Ausführung auf einem Azure Stack Edge Pro-GPU-Gerät auf einer Kubernetes-basierten IoT Edge-Plattform möglich ist. 

## <a name="about-iot-edge-implementation"></a>Informationen zur IoT Edge-Implementierung 

Die IoT Edge-Implementierung unterscheidet sich auf Azure Stack Edge Pro-FPGA-Geräten und auf Azure Stack Edge Pro-GPU-Geräten. Für GPU-Geräte wird Kubernetes als Hostingplattform für IoT Edge verwendet. Für IoT Edge auf FPGA-Geräten wird eine Docker-basierte Plattform verwendet. Das Docker-basierte Anwendungsmodell für IoT Edge wird automatisch in das für Kubernetes native Anwendungsmodell übersetzt. Manche Änderung sind jedoch möglicherweise weiterhin erforderlich, da nur eine kleine Teilmenge des Kubernetes-Anwendungsmodells unterstützt wird.

Wenn Sie Ihre Workloads von einem FPGA-Gerät auf ein GPU-Gerät migrieren, müssen Sie Änderungen an den vorhandenen IoT Edge-Modulen vornehmen, damit diese erfolgreich auf der Kubernetes-Plattform ausgeführt werden können. Möglicherweise müssen Sie Ihre Anforderungen an Speicher, Netzwerke, Ressourcenauslastung und Webproxy unterschiedlich angeben. 

## <a name="storage"></a>Storage

Berücksichtigen Sie die folgenden Informationen, wenn Sie Speicher für die IoT Edge-Module angeben.

- Speicher für Container in Kubernetes wird mithilfe von Volumeeinbindungen angegeben.
- Bei Bereitstellungen in Kubernetes können dazugehöriger permanenter Speicher oder Hostpfade keine Bindungen aufweisen.
    - Verwenden Sie für permanenten Speicher `Mounts` mit dem Typ `volume`.
    - Verwenden Sie für Hostpfade `Mounts` mit dem Typ `bind`.
- Bei IoT Edge für Kubernetes funktionieren Bindungen über `Mounts` nur für das Verzeichnis, nicht für Dateien.

#### <a name="example---storage-via-volume-mounts"></a>Beispiel: Speicher über Volumeeinbindungen 

Bei IoT Edge in Docker werden Hostpfadbindungen verwendet, um die Dateifreigaben auf dem Gerät Pfaden innerhalb des Containers zuzuordnen. Hier finden Sie die container create-Optionen, die für FPGA-Geräte verfügbar sind:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Ein Beispiel für die Verwendung von `Mounts` mit dem Typ `bind` für Hostpfade für IoT Edge in Kubernetes finden Sie hier:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Für GPU-Geräte, die in IoT Edge in Kubernetes ausgeführt werden, werden Volumeeinbindungen verwendet, um Speicher anzugeben. Der Wert für `Mounts.Source` wäre der Name der SMB- oder NFS-Dateifreigabe, die auf Ihrem GPU-Gerät bereitgestellt wurde, um Speicher mithilfe von Dateifreigaben anzugeben. `/home/input` ist der Pfad, über den innerhalb des Containers auf das Volume zugegriffen werden kann. Hier finden Sie die container create-Optionen, die für GPU-Geräte verfügbar sind:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Netzwerk

Berücksichtigen Sie die folgenden Informationen, wenn Sie Netzwerke für die IoT Edge-Module angeben. 

- Eine `HostPort`-Spezifikation ist erforderlich, um einen Dienst sowohl innerhalb als auch außerhalb des Clusters verfügbar zu machen.
    - Mithilfe der K8s-experimental-Optionen kann festgelegt werden, dass der Dienst nur für den Cluster verfügbar gemacht wird.
- Für die Kommunikation zwischen den Modulen ist eine `HostPort`-Spezifikation erforderlich. Außerdem muss die Verbindung mithilfe des zugeordneten Ports und nicht mithilfe des für den Container verfügbar gemachten Ports hergestellt werden.
- Hostnetzwerke können mit `dnsPolicy = ClusterFirstWithHostNet` verwendet werden. So ist es nicht erforderlich, dass sich alle Container (insbesondere `edgeHub`) auch im Hostnetzwerk befinden. <!--Need further clarifications on this one-->
- Das Hinzufügen von Portzuordnungen für TCP und UDP in derselben Anforderung ist nicht möglich.

#### <a name="example---external-access-to-modules"></a>Beispiel: Externer Zugriff auf Module 

Für alle IoT Edge-Module, die Portbindungen angeben, wird mithilfe des externen Dienst-IP-Adressbereichs von Kubernetes eine IP-Adresse zugewiesen. Der Bereich wurde über die lokale Benutzeroberfläche des Geräts angegeben. Wie im folgenden Beispiel ersichtlich wird, gibt es keine Änderungen an den container create-Optionen zwischen IoT Edge in Docker und IoT Edge in Kubernetes.  

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

Wenn Sie jedoch die Ihrem Modul zugewiesene IP-Adresse abfragen möchten, können Sie das Kubernetes-Dashboard wie unter [Abrufen der IP-Adresse für Dienste oder Module](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules) beschrieben verwenden. 

Alternativ haben Sie die Möglichkeit zum [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface), und Sie können den `iotedge`-Auflistungsbefehl verwenden, um alle Module aufzuführen, die auf dem Gerät ausgeführt werden. Die [Befehlsausgabe](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) weist auch die externen IP-Adressen auf, die dem Modul zugeordnet sind.


## <a name="resource-usage"></a>Ressourcenauslastung 

Mit den Kubernetes-basierten IoT Edge-Setups auf GPU-Geräten werden die Ressourcen wie Hardwarebeschleunigung, Arbeitsspeicher und CPU-Anforderungen anders als auf den FPGA-Geräten angegeben. 

#### <a name="compute-acceleration-usage"></a>Nutzung der Computebeschleunigung

Verwenden Sie für eine FPGA-Bereitstellung von Modulen die container create-Optionen. <!--with Device Bindings--> Sehen Sie sich dazu die folgende Konfiguration an: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Verwenden Sie für GPU Ressourcenanforderungsspezifikationen anstatt Gerätebindungen. Sehen Sie sich dazu die folgende minimale Konfiguration an. Sie fordern NVIDIA-Ressourcen anstatt Catapult-Ressourcen an, und Sie müssen `wireserver` nicht angeben. 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Arbeitsspeicherauslastung und CPU-Auslastung
 
Wenn Sie die Arbeitsspeicherauslastung und CPU-Auslastung festlegen möchten, können Sie Prozessorgrenzwerte für Module im `k8s-experimental`-Abschnitt verwenden. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
Die Spezifikationen für Arbeitsspeicher und CPU sind nicht erforderlich, jedoch empfehlenswert. Wenn `requests` nicht angegeben wird, werden die als Grenzwerte festgelegten Werte als Mindestanforderung verwendet. 

Für die Verwendung von freigegebenem Arbeitsspeicher für Module ist ebenfalls eine andere Herangehensweise erforderlich. Sie können beispielsweise den Host-IPC-Modus für den Zugriff auf gemeinsam genutzten Arbeitsspeicher zwischen Live Video Analytics-Lösungen und Rückschlusslösungen verwenden. Informationen dazu finden Sie unter [Bereitstellen des Live Video Analytics-Edge-Moduls mit dem Azure-Portal](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Webproxy 

Beim Konfigurieren eines Webproxys müssen Sie folgende Informationen beachten:

Wenn in Ihrem Netzwerk ein Webproxy konfiguriert ist, konfigurieren Sie die folgenden Umgebungsvariablen für die `edgeHub`-Bereitstellung in Ihrem Docker-basierten IoT Edge-Setup auf FPGA-Geräten:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (es sei denn, der Webproxy lässt `Amqp`-Datenverkehr zu)

Für Kubernetes-basierte IoT Edge-Setups auf GPU-Geräten müssen Sie diese zusätzliche Variable bei der Bereitstellung konfigurieren:

- `no_proxy`: localhost

- Der IoT Edge-Proxy auf der Kubernetes-Plattform verwendet Port 35000 und 35001. Sorgen Sie dafür, dass Ihr Modul nicht auf diesen Ports ausgeführt wird. Andernfalls könnten Konflikte verursacht werden. 

## <a name="other-differences"></a>Weitere Unterschiede

- **Bereitstellungsstrategie:** Möglicherweise müssen Sie das Bereitstellungsverhalten für Modulaktualisierungen ändern. Das Standardverhalten für IoT Edge-Module sind rollierende Updates. Diese Verhalten verhindert, dass das aktualisierte Modul neu gestartet wird, wenn das Modul Ressourcen wie Hardwarebeschleunigung oder Netzwerkports verwendet. Dieses Verhalten kann unerwartete Effekte haben, insbesondere wenn permanente Volumes auf der Kubernetes-Plattform für die GPU-Geräte verarbeitet werden. Wenn Sie diese Standardverhalten überschreiben möchten, können Sie `Recreate` im `k8s-experimental`-Abschnitt Ihres Moduls angeben.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Modulnamen:** Modulnamen sollten die Namenskonventionen von Kubernetes befolgen. Möglicherweise müssen Sie die Module, die in IoT Edge mit Docker ausgeführt werden, umbenennen, wenn Sie diese Module zu IoT Edge mit Kubernetes verschieben. Weitere Informationen zur Benennung finden Sie unter [Objektnamen und IDs](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Weitere Optionen**: 
    - Bestimmte docker create-Optionen, die für FPGA-Geräte funktioniert haben, funktionieren nicht in der Kubernetes-Umgebung auf Ihren GPU-Geräten. Beispiel: EntryPoint.<!--can we confirm what exactly is required here-->
    - Umgebungsvariablen wie `:` müssen durch `__` ersetzt werden.
    - Der Status **Container Creating** für einen Kubernetes-Pod führt zum Status **backoff** für ein Modul in der IoT Hub-Ressource. Es gibt zwar eine Vielzahl Gründe, warum der Pod diesen Status aufweist, ein häufiger Grund ist jedoch, dass ein großes Containerimage über eine Verbindung mit geringer Netzwerkbandbreite gepullt wird. Wenn der Pod diesen Status aufweist, wird als Status für das Modul **backoff** in IoT Hub angezeigt, obwohl das Modul gerade erst gestartet wird.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zum [Konfigurieren der GPU für die Verwendung eines Moduls](azure-stack-edge-j-series-configure-gpu-modules.md).
