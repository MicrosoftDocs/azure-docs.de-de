---
title: Verwenden von GPUs in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie GPUs für Hochleistungscompute- oder grafikintensive Workloads in Azure Kubernetes Service (AKS) verwendet werden können.
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929139"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Verwenden von GPUs für computeintensive Workloads in Azure Kubernetes Service (AKS)

GPUs (Graphical Processing Units) werden häufig für computeintensive Workloads (also etwa für grafikintensive und visualisierungsorientierte Workloads) verwendet. AKS unterstützt die Erstellung von GPU-fähigen Knotenpools, um diese computrintensiven Workloads in Kubernetes auszuführen. Weitere Informationen zu verfügbaren GPU-fähigen virtuellen Computern finden Sie unter [GPU-optimierte VM-Größen in Azure][gpu-skus]. Für die AKS-Knoten empfehlen wir eine Mindestgröße von *Standard_NC6*.

> [!NOTE]
> GPU-fähige virtuelle Computer verfügen über spezielle Hardware, für die höhere Preise gelten und die möglicherweise nicht in allen Regionen verfügbar ist. Weitere Informationen finden Sie in der [Preisübersicht][azure-pricing] sowie auf der [Website zur regionalen Verfügbarkeit][azure-availability].

Die Verwendung GPU-fähiger Knotenpools steht aktuell nur für Linux-Knotenpools zur Verfügung.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über einen AKS-Cluster mit Knoten verfügen, die AKS unterstützen. Ihr AKS-Cluster muss Kubernetes 1.10 oder höher ausführen. Wenn Sie einen AKS-Cluster benötigen, der diese Anforderungen erfüllt, lesen Sie den ersten Abschnitt dieses Artikels zum [Erstellen eines AKS-Clusters](#create-an-aks-cluster).

Außerdem muss mindestens die Version 2.0.64 der Azure-Befehlszeilenschnittstelle installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Wenn Sie einen AKS-Cluster benötigen, der die Mindestanforderungen erfüllt (GPU-fähiger Knoten und Kubernetes Version 1.10 oder höher), führen Sie die folgenden Schritte aus. Wenn Sie bereits über einen AKS-Cluster verfügen, der diese Anforderungen erfüllt, [fahren Sie mit dem nächsten Abschnitt fort](#confirm-that-gpus-are-schedulable).

Erstellen Sie zunächst mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe für den Cluster. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az aks create][az-aks-create] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit einem einzelnen Knoten der Größe `Standard_NC6` erstellt:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Rufen Sie die Anmeldeinformationen für Ihren AKS-Cluster mit dem Befehl [az aks get-credentials][az-aks-get-credentials] ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Installieren des NVIDIA-Geräte-Plug-Ins

Um die GPUs in den Knoten verwenden zu können, muss zunächst ein DaemonSet für das NVIDIA-Geräte-Plug-In bereitgestellt werden. Diese DaemonSet führt einen Pod für jeden Knoten aus, um die erforderlichen Treiber für die GPUs bereitzustellen.

Erstellen Sie zunächst einen Namespace mit dem Befehl [kubectl create namespace][kubectl-create], z.B. *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Erstellen Sie eine Datei mit dem Namen *nvidia-device-plugin-ds.yaml*, und fügen Sie das folgende YAML-Manifest ein. Dieses Manifest wird im Rahmen des Projekts [NVIDIA device plugin for Kubernetes][nvidia-github] (NVIDIA-Geräte-Plug-In für Kubernetes) bereitgestellt.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Erstellen Sie als Nächstes mithilfe des Befehls [kubectl apply][kubectl-apply] das DaemonSet, und vergewissern Sie sich, dass das NVIDIA-Geräte-Plug-In erfolgreich erstellt wurde, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Verwenden des speziellen AKS-GPU-Images (Vorschau)

Als Alternative zu diesen Schritten stellt AKS ein vollständig konfiguriertes AKS-Image bereit, das schon das [NVIDIA-Geräte-Plug-In für Kubernetes][nvidia-github] enthält.

> [!WARNING]
> Sie sollten das NVIDIA-Geräte-Plug-In-DaemonSet für Cluster nicht manuell mit dem neuen speziellen AKS-GPU-Image installieren.


Registrieren Sie das Feature `GPUDedicatedVHDPreview`:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Es kann einige Minuten dauern, bis der Status als **Registriert** angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list](/cli/azure/feature#az-feature-list) überprüfen:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Wenn der Status als registriert angezeigt wird, können Sie die Registrierung des `Microsoft.ContainerService`-Ressourcenanbieters mit dem Befehl [az provider register](/cli/azure/provider#az-provider-register) aktualisieren:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Verwenden Sie zum Installieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension add --name aks-preview
```

Verwenden Sie zum Aktualisieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Verwenden des speziellen AKS-GPU-Images für neue Cluster (Vorschau)    

Konfigurieren Sie den Cluster beim Erstellen so, dass das spezielle AKS-GPU-Image verwendet wird. Verwenden Sie das `--aks-custom-headers`-Flag für die GPU-Agent-Knoten im neuen Cluster, um das spezielle AKS-GPU-Image zu verwenden.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Wenn Sie einen Cluster mit regulären AKS-Images erstellen möchten, lassen Sie das benutzerdefinierte Tag `--aks-custom-headers` weg. Sie können auch weitere spezielle GPU-Knotenpools hinzufügen, wie im Anschluss beschrieben.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Verwenden des speziellen AKS-GPU-Images für vorhandene Cluster (Vorschau)

Konfigurieren Sie einen neuen Knotenpool, um das spezielle AKS-GPU-Image zu verwenden. Verwenden Sie das `--aks-custom-headers`-Flag für die GPU-Agent-Knoten im neuen Knotenpool, um das spezielle AKS-GPU-Image zu verwenden.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Wenn Sie einen Knotenpool mit regulären AKS-Images erstellen möchten, lassen Sie das benutzerdefinierte Tag `--aks-custom-headers` weg. 

> [!NOTE]
> Wenn Ihre GPU-SKU virtuelle Computer der 2. Generation erfordert, können Sie Folgendes erstellen:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Bestätigen, dass GPUs planbar sind

Nachdem Ihr AKS-Cluster erstellt wurde, bestätigen Sie, dass GPUs in Kubernetes planbar sind. Listen Sie zunächst die Knoten in Ihrem Cluster mit dem Befehl [kubectl get nodes][kubectl-get] auf:

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Verwenden Sie nun den Befehl [kubectl describe node][kubectl-describe], um zu bestätigen, dass die GPUs planbar sind. Unter dem Abschnitt *Capacity* (Kapazität) sollte die GPU als `nvidia.com/gpu:  1` aufgelistet werden.

Das folgende verkürzte Beispiel zeigt, dass eine GPU für den Knoten *aks-nodepool1-18821093-0* verfügbar ist:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Ausführen einer GPU-fähigen Workload

Um die GPU in Aktion zu sehen, planen Sie eine GPU-fähige Workload mit der entsprechenden Ressourcenanforderung. In diesem Beispiel wird ein [TensorFlow](https://www.tensorflow.org/)-Auftrag für das [MNIST-Dataset](http://yann.lecun.com/exdb/mnist/) ausgeführt.

Erstellen Sie eine Datei mit dem Namen *samples-tf-mnist-demo.yaml*, und fügen Sie das folgende YAML-Manifest ein. Das folgende Auftragsmanifest enthält ein Ressourcenlimit von `nvidia.com/gpu: 1`:

> [!NOTE]
> Wenn Sie beim Aufruf von Treibern einen Versionsfehler erhalten (z. B., dass die CUDA-Treiberversion für die CUDA-Laufzeitversion nicht ausreicht), überprüfen Sie das NVIDIA-Treibermatrix-Kompatibilitätsdiagramm: [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Führen Sie den Auftrag mithilfe des Befehls [kubectl apply][kubectl-apply] aus. Dieser Befehl analysiert die Manifestdatei und erstellt die definierten Kubernetes-Objekte:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Anzeigen des Status und der Ausgabe der GPU-fähigen Workload

Überwachen Sie den Status des Auftrags mithilfe des Befehls [kubectl get jobs][kubectl-get] mit dem Argument `--watch`. Es kann einige Minuten dauern, um zunächst das Image zu pullen und das Dataset zu verarbeiten. Wenn die Spalte *ABSCHLÜSSE* die Angabe *1/1* enthält, wurde der Auftrag erfolgreich abgeschlossen. Beenden Sie den Befehl `kubetctl --watch` durch Drücken von *STRG+C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Um die Ausgabe der GPU-fähigen Workload anzuzeigen, rufen Sie zunächst mithilfe des Befehls [kubectl get pods][kubectl-get] den Namen des Pods ab:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Nun verwenden Sie den Befehl [kubectl logs][kubectl-logs], um die Podprotokolle anzuzeigen. Die folgenden Beispielpodprotokolle bestätigen, dass das entsprechende GPU-Gerät ermittelt wurde: `Tesla K80`. Geben Sie den Namen für Ihren eigenen Pod an:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie zum Entfernen der zugehörigen Kubernetes-Objekte, die in diesem Artikel erstellt wurden, den Befehl [kubectl delete job][kubectl delete] wie folgt:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Nächste Schritte

Um Apache Spark-Aufträge auszuführen, lesen Sie [Ausführen von Apache Spark-Aufträgen in AKS][aks-spark].

Weitere Informationen zum Ausführen von Workloads für Machine Learning (ML) in Kubernetes finden Sie unter [Kubeflow-Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
