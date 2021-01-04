---
title: Herstellen einer Verbindung mit und Verwalten von Microsoft Azure Stack Edge Pro-GPU-Geräten über die Windows PowerShell-Schnittstelle | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie über die Windows PowerShell-Schnittstelle eine Verbindung mit einem Azure Stack Edge Pro-GPU-Gerät herstellen und dieses anschließend verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: ba3005b1ec36e4b2406084368a3aabd778c17716
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449440"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Verwalten eines Azure Stack Edge Pro-GPU-Geräts mithilfe von Windows PowerShell

Azure Stack Edge Pro ist eine Lösung, mit der Sie Daten verarbeiten und über ein Netzwerk an Azure senden können. In diesem Artikel werden einige der Konfigurations- und Verwaltungsaufgaben für Ihr Azure Stack Edge Pro-Gerät beschrieben. Sie können das Azure-Portal, die lokale Webbenutzeroberfläche oder die Windows PowerShell-Schnittstelle verwenden, um Ihr Gerät zu verwalten.

Dieser Artikel befasst sich schwerpunktmäßig damit, wie Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts herstellen können und welche Aufgaben Sie mithilfe dieser Schnittstelle ausführen können. 


## <a name="connect-to-the-powershell-interface"></a>Herstellen einer Verbindung mit der PowerShell-Schnittstelle

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Unterstützungspaket erstellen

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

<!--## Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

You can also upload IoT Edge certificates to enable a secure connection between your IoT Edge device and the downstream devices that may connect to it. There are three IoT Edge certificates (*.pem* format) that you need to install:

- Root CA certificate or the owner CA
- Device CA certificate
- Device key certificate

The following example shows the usage of this cmdlet to install IoT Edge certificates:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
When you run this cmdlet, you will be prompted to provide the password for the network share.

For more information on certificates, go to [Azure IoT Edge certificates](../iot-edge/iot-edge-certs.md) or [Install certificates on a gateway](../iot-edge/how-to-create-transparent-gateway.md).-->

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Anzeigen von GPU-Treiberinformationen

Wenn die Computerolle auf Ihrem Gerät konfiguriert ist, können Sie die GPU-Treiberinformationen auch über die PowerShell-Schnittstelle abrufen. 

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Verwenden Sie `Get-HcsGpuNvidiaSmi` zum Abrufen der GPU-Treiberinformationen für Ihr Gerät.

    Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Notieren Sie sich die Treiberinformationen aus der Beispielausgabe dieses Cmdlets.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Aktivieren des Multiprozessdiensts (MPS)

Ein Multiprozessdienst (MPS) auf NVIDIA-GPUs bietet einen Mechanismus, bei dem GPUs von mehreren Aufträgen gemeinsam genutzt werden können, wobei jedem Auftrag ein bestimmter Prozentsatz der GPU-Ressourcen zugewiesen wird. MPS ist eine Previewfunktion auf Ihrem Azure Stack Edge Pro-GPU-Gerät. Führen Sie die folgenden Schritte aus, um MPS auf Ihrem Gerät zu aktivieren:

1. Stellen Sie Folgendes sicher, bevor Sie beginnen: 

    1. Sie haben Ihr [Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-activate.md) mit einer Azure Stack Edge Pro/Data Box Gateway-Ressource in Azure konfiguriert und aktiviert.
    1. Sie haben [auf diesem Gerät Compute im Azure-Portal konfiguriert](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
1. Verwenden Sie den folgenden Befehl, um MPS auf Ihrem Gerät zu aktivieren.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Zurücksetzen Ihres Geräts

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Abrufen von Computeprotokollen

Wenn die Computerolle auf Ihrem Gerät konfiguriert ist, können Sie die Computeprotokolle auch über die PowerShell-Schnittstelle abrufen.

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Verwenden Sie `Get-AzureDataBoxEdgeComputeRoleLogs` zum Abrufen der Computeprotokolle für Ihr Gerät.

    Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Es folgt eine Beschreibung der verwendeten Parameter des Cmdlets:
    - `Path`: Geben Sie einen Netzwerkpfad für die Freigabe an, in der Sie das Computeprotokollpaket erstellen möchten.
    - `Credential`: Geben Sie den Benutzernamen für die Netzwerkfreigabe an. Wenn Sie dieses Cmdlet ausführen, müssen Sie das Freigabekennwort angeben.
    - `FullLogCollection`: Dieser Parameter stellt sicher, dass das Protokollpaket alle Computeprotokolle enthält. Standardmäßig enthält das Protokollpaket nur eine Teilmenge der Protokolle.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Ändern der Pod- und Dienstsubnetze in Kubernetes

Standardmäßig verwendet Kubernetes auf Ihrem Azure Stack Edge-Gerät das Subnetz 172.27.0.0/16 für den Pod und das Subnetz 172.28.0.0/16 für den Dienst. Wenn diese Subnetze bereits in Ihrem Netzwerk verwendet werden, können Sie das Cmdlet `Set-HcsKubeClusterNetworkInfo` ausführen, um diese Subnetze zu ändern.

Sie sollten diese Konfiguration vor dem Konfigurieren von Compute im Azure-Portal ausführen, da in diesem Schritt der Kubernetes-Cluster erstellt wird.

1. [Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her.](#connect-to-the-powershell-interface)
1. Führen Sie an der PowerShell-Schnittstelle des Geräts folgenden Befehl aus:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Ersetzen Sie <subnet details> durch den Subnetzbereich, den Sie verwenden möchten. 

1. Nachdem Sie diesen Befehl ausgeführt haben, können Sie mit dem Befehl `Get-HcsKubeClusterNetworkInfo` überprüfen, ob sich die Pod- und Dienstsubnetze geändert haben.

Nachfolgend finden Sie eine Beispielausgabe für diesen Befehl.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```


## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Debuggen von Kubernetes-Problemen im Zusammenhang mit IoT Edge

<!--When the Kubernetes cluster is created, there are two system namespaces created: `iotedge` and `azure-arc`. --> 

<!--### Create config file for system namespace

To troubleshoot, first create the `config` file corresponding to the `iotedge` namespace with `aseuser`.

Run the `Get-HcsKubernetesUserConfig -AseUser` command and save the output as `config` file (no file extension). Save the file in the `.kube` folder of your user profile on the local machine.

Following is the sample output of the `Get-HcsKubernetesUserConfig` command.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```
-->

Ein Azure Stack Edge Pro-Gerät, für das die Computerolle konfiguriert ist, können Sie mit zwei verschiedenen Befehlen überwachen oder eine Problembehandlung vornehmen.

- Unter Verwendung von `iotedge`-Befehlen. Diese Befehle sind für grundlegende Vorgänge auf dem Gerät vorgesehen.
- Unter Verwendung von `kubectl`-Befehlen. Diese Befehle sind für umfangreiche Vorgänge auf dem Gerät vorgesehen.

Um eine der oben aufgeführten Gruppen von Befehlen auszuführen, müssen Sie [eine Verbindung mit der PowerShell-Schnittstelle herstellen](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Verwenden von `iotedge`-Befehlen

Um eine Liste der verfügbaren Befehle anzuzeigen, [stellen Sie eine Verbindung zur PowerShell-Schnittstelle her](#connect-to-the-powershell-interface), und verwenden Sie die `iotedge`-Funktion.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

Die folgende Tabelle enthält eine kurze Beschreibung der für `iotedge` verfügbaren Befehle:

|command  |BESCHREIBUNG |
|---------|---------|
|`list`     | Module auflisten         |
|`logs`     | Abrufen der Protokolle eines Moduls        |
|`restart`     | Beenden und Neustarten eines Moduls         |


Mit dem Befehl `iotedge list` können Sie alle Module auflisten, die auf Ihrem Gerät ausgeführt werden.

Nachfolgend finden Sie eine Beispielausgabe dieses Befehls. Mit diesem Befehl werden alle Module, die zugehörige Konfiguration und die externen IP-Adressen aufgelistet, die den Modulen zugeordnet sind. Beispielsweise können Sie mit `https://10.128.44.244` auf die **Webserver**-App zugreifen. 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```


### <a name="use-kubectl-commands"></a>Verwenden von kubectl-Befehlen

Auf einem Azure Stack Edge Pro-Gerät, für das die Computerolle konfiguriert wurde, stehen alle `kubectl`-Befehle für die Überwachung oder Problembehandlung von Modulen zur Verfügung. Führen Sie `kubectl --help` im Befehlsfenster aus, um eine Liste der verfügbaren Befehle anzuzeigen.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Eine umfassende Liste der `kubectl`-Befehle finden Sie auf dem [`kubectl`Spickzettel](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>So erhalten Sie eine IP-Adresse für Dienste oder Module, die außerhalb von Kubernetes bereitgestellt werden

Führen Sie den folgenden Befehl aus, um die IP-Adresse von Lastenausgleichsdiensten oder -modulen abzurufen, die außerhalb von Kubernetes bereitgestellt werden:

`kubectl get svc -n iotedge`

Im Folgenden finden Sie eine Beispielausgabe aller Dienste oder Module, die außerhalb des Kubernetes-Clusters bereitgestellt werden. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
Die IP-Adresse in der Spalte „Externe IP“ entspricht dem externen Endpunkt für den Dienst oder das Modul. Sie können [die externe IP-Adresse auch im Kubernetes-Dashboard abrufen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Überprüfen, ob das Modul erfolgreich bereitgestellt wurde

Computingmodule sind Container mit implementierter Geschäftslogik. Ein Kubernetes-Pod kann mehrere Container ausführen. 

Stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her, um zu überprüfen, ob ein Computemodul erfolgreich bereitgestellt wurde.
Führen Sie den `get pods`-Befehl aus, und überprüfen Sie, ob der (dem Computemodul entsprechende) Container ausgeführt wird.

Führen Sie den folgenden Befehl aus, um die Liste aller in einem bestimmten Namespace ausgeführten Pods zu erhalten:

`get pods -n <namespace>`

Führen Sie den folgenden Befehl aus, um die über IOT Edge bereitgestellten Module zu überprüfen:

`get pods -n iotedge`

Im Folgenden finden Sie eine Beispielausgabe aller Pods, die im `iotedge`-Namespace ausgeführt werden.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

Der Status **Status** zeigt an, dass alle Pods im Namespace ausgeführt werden, und der Status **Bereit** gibt die Anzahl der in einem Pod bereitgestellten Container an. Im vorherigen Beispiel werden alle Pods und alle in den Pods bereitgestellten Module ausgeführt. 

Führen Sie den folgenden Befehl aus, um die über Azure Arc bereitgestellten Module zu überprüfen:

`get pods -n azure-arc`

Alternativ können Sie eine [Verbindung mit dem Kubernetes-Dashboard herstellen, um die IOT Edge- oder Azure Arc-Bereitstellungen anzuzeigen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Führen Sie den folgenden Befehl aus, um eine ausführlichere Ausgabe eines bestimmten Pods für einen bestimmten Namespace zu erhalten:

`kubectl describe pod <pod name> -n <namespace>` 

Die Beispielausgabe sieht wie folgt aus:

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Abrufen von Containerprotokollen

Führen Sie den folgenden Befehl über die PowerShell-Schnittstelle des Geräts aus, um die Protokolle für ein Modul abzurufen:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Da das `all-containers`-Flag alle Protokolle für sämtliche Container sichert, lassen sich mit der Option `--tail 10` die zuletzt aufgetretenen Fehler anzeigen.

Im Folgenden finden Sie eine Beispielausgabe: 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

## <a name="connect-to-bmc"></a>Verbinden mit dem BMC

Der Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) wird verwendet, um Ihr Gerät remote zu überwachen und zu verwalten. In diesem Abschnitt werden die Cmdlets beschrieben, die zum Verwalten der BMC-Konfiguration verwendet werden können. Stellen Sie vor dem Ausführen eines dieser Cmdlets [eine Verbindung mit der PowerShell-Schnittstelle des Geräts her](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Verwenden Sie dieses Cmdlet, um die Netzwerk-Konfigurationseigenschaften des BMC abzurufen, z. B. `IPv4Address`, `IPv4Gateway`, `IPv4SubnetMask` und `DhcpEnabled`. 
    
    Hier ist eine Beispielausgabe:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: Sie können dieses Cmdlet auf die folgenden zwei Arten verwenden.

    - Verwenden Sie das Cmdlet, um die DHCP-Konfiguration für den BMC über den entsprechenden Wert für den Parameter `UseDhcp` zu aktivieren oder zu deaktivieren. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Hier ist eine Beispielausgabe: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Verwenden Sie dieses Cmdlet, um die statische Konfiguration für den BMC zu konfigurieren. Sie können Werte für `IPv4Address`, `IPv4Gateway` und `IPv4SubnetMask` angeben. 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Hier ist eine Beispielausgabe: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Verwenden Sie dieses Cmdlet, um das BMC-Kennwort für `EdgeUser` zu ändern. Beim Benutzernamen (`EdgeUser`) wird die Groß-/Kleinschreibung beachtet.

    Hier ist eine Beispielausgabe: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Beenden der Remotesitzung

Schließen Sie das PowerShell-Fenster, um die PowerShell-Remotesitzung zu beenden.

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) im Azure-Portal bereit.