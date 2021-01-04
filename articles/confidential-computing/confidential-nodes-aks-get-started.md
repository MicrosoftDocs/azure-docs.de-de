---
title: 'Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über die Azure-Befehlszeilenschnittstelle mit Confidential Computing-Knoten'
description: Hier erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle einen AKS-Cluster mit vertraulichen Knoten erstellen und eine Hello World-App bereitstellen.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 95626836afb09ada286cf7e171f97db450167999
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564343"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-using-azure-cli-preview"></a>Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) mit Confidential Computing-Knoten über die Azure-Befehlszeilenschnittstelle (Vorschauversion)

Diese Schnellstartanleitung richtet sich an Entwickler oder Clusteroperatoren, die schnell einen AKS-Cluster erstellen und eine Anwendung bereitstellen möchten, um Anwendungen mithilfe des verwalteten Kubernetes-Diensts in Azure zu überwachen.

## <a name="overview"></a>Übersicht

In dieser Schnellstartanleitung erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle einen AKS-Cluster (Azure Kubernetes Service) mit Confidential Computing-Knoten bereitstellen und eine Hello World-Anwendung in einer Enklave ausführen. AKS ist ein Managed Kubernetes-Dienst, mit dem Sie Cluster schnell bereitstellen und verwalten können. Weitere Informationen zu AKS finden Sie [hier](../aks/intro-kubernetes.md).

> [!NOTE]
> Virtuelle Confidential Computing-Computer der DCsv2-Serie nutzen spezielle Hardware, für die höhere Preise gelten und die möglicherweise nicht in allen Regionen verfügbar ist. Weitere Informationen zu verfügbaren SKUs und unterstützten Regionen für virtuelle Computer finden Sie [hier](virtual-machine-solutions.md).

### <a name="deployment-pre-requisites"></a>Voraussetzungen für die Bereitstellung

1. Ein aktives Azure-Abonnement. Sollten Sie über kein Azure-Abonnement verfügen, können Sie [ein kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
1. Auf dem Bereitstellungscomputer muss mindestens die Azure CLI-Version 2.0.64 installiert und konfiguriert sein. (Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. Die Erweiterung [aks-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) (Mindestversion: 0.4.62). 
1. In Ihrem Abonnement müssen mindestens sechs **DC<x>s-v2**-Kerne für die Verwendung verfügbar sein. Das VM-Kernkontingent für Confidential Computing liegt standardmäßig bei acht Kernen pro Abonnement. Wenn Sie einen Cluster bereitstellen möchten, der mehr als acht Kerne erfordert, gehen Sie wie [hier](../azure-portal/supportability/per-vm-quota-requests.md) beschrieben vor, um ein Ticket für die Kontingenterhöhung zu erstellen.

### <a name="confidential-computing-node-features-dcxs-v2"></a>Features von Confidential Computing-Knoten (DC<x>s-v2)

1. Von Linux-Workerknoten werden nur Linux-Container unterstützt.
1. Virtuelle Ubuntu-Computer der zweiten Generation (18.04)
1. Intel SGX-basierte CPU mit Encrypted Page Cache Memory (EPC). Weitere Informationen finden Sie [hier](./faq.md).
1. Kubernetes-Version 1.16 oder höher
1. Vorinstallierter Intel SGX DCAP-Treiber. Weitere Informationen finden Sie [hier](./faq.md).
1. CLI-basierte Bereitstellung während der Vorschau


## <a name="installing-the-cli-pre-requisites"></a>Installieren der CLI-Voraussetzungen

Verwenden Sie die folgenden Azure CLI-Befehle, um mindestens die Version 0.4.62 der Erweiterung „aks-preview“ zu installieren:

```azurecli-interactive
az extension add --name aks-preview
az extension list
```
Verwenden Sie zum Aktualisieren der aks-preview-CLI-Erweiterung die folgenden Azure CLI-Befehle:

```azurecli-interactive
az extension update --name aks-preview
```

Registrieren Sie „Gen2VMPreview“:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Es kann einige Minuten dauern, bis der Status als Registriert angezeigt wird. Der Registrierungsstatus kann mithilfe des Befehls „az feature list“ überprüft werden:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Wenn der Zustand „registered“ (Registriert) lautet, aktualisieren Sie die Registrierung des Ressourcenanbieters „Microsoft.ContainerService“ mithilfe des Befehls „az provider register“:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="creating-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Wenn Sie bereits über einen AKS-Cluster verfügen, der die oben genannten Anforderungen erfüllt, können Sie [direkt mit dem Abschnitt für einen vorhandenen Cluster fortfahren](#existing-cluster), um einen neuen Pool mit Confidential Computing-Knoten hinzuzufügen.

Erstellen Sie zunächst mit dem Befehl az group create eine Ressourcengruppe für den Cluster. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *westus2* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Erstellen Sie nun mit dem Befehl az aks create einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit einem einzelnen Knoten der Größe `Standard_DC2s_v2` erstellt. [Hier](../virtual-machines/dcv2-series.md) können Sie eine andere unterstützte Liste von DCsv2-SKUs auswählen:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_DC2s_v2 \
    --node-count 3 \
    --enable-addon confcom \
    --network-plugin azure \
    --vm-set-type VirtualMachineScaleSets \
    --aks-custom-headers usegen2vm=true
```
Der obige Befehl stellt einen neuen AKS-Cluster mit **DC<x>s-v2**-Knotenpools bereit und installiert automatisch zwei Daemonsets: [SGX-Geräte-Plug-In](confidential-nodes-aks-overview.md#sgx-plugin) und [SGX-Angebotshilfe](confidential-nodes-aks-overview.md#sgx-quote).

Rufen Sie die Anmeldeinformationen für Ihren AKS-Cluster mit dem Befehl az aks get-credentials ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Vergewissern Sie sich, dass die Knoten ordnungsgemäß erstellt wurden und dass die SGX-bezogenen Daemonsets in **DC<x>s-v2**-Knotenpools ausgeführt werden. Verwenden Sie hierzu die folgenden kubectl-Befehle zum Abrufen von Pods und Knoten:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
```
Wenn die Ausgabe wie oben aussieht, ist Ihr AKS-Cluster bereit für die Ausführung vertraulicher Anwendungen.

Navigieren Sie zum Bereitstellungsabschnitt [Hello World aus isolierter Enklavenanwendung](#hello-world), um eine App in einer Enklave zu testen. Alternativ können Sie wie folgt vorgehen, um weitere Knotenpools in AKS hinzuzufügen. (AKS unterstützt die parallele Verwendung von SGX-Knotenpools SGX-fremden Knotenpools.)

>Sollten die SGX-bezogenen Daemonsets nicht in Ihren DCSv2-Knotenpools installiert sein, führen Sie den folgenden Befehl aus:

```azurecli-interactive
az aks update --enable-addons confcom --resource-group myResourceGroup --name myAKSCluster
```

![DCSv2 AKS-Clustererstellung](./media/confidential-nodes-aks-overview/CLIAKSProvisioning.gif)

## <a name="adding-confidential-computing-node-to-existing-aks-cluster"></a>Hinzufügen eines Confidential Computing-Knotens zu einem vorhandenen AKS-Cluster<a id="existing-cluster"></a>

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits über einen aktiven AKS-Cluster verfügen, der die im Abschnitt „Voraussetzungen“ aufgeführten Kriterien erfüllt.

Aktivieren Sie zunächst die Confidential Computing-bezogenen AKS-Add-Ons für den vorhandenen Cluster:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Fügen Sie dem Cluster als Nächstes einen **DC<x>s-v2**-Knotenpool hinzu:
    
> [!NOTE]
> Damit Sie die Confidential Computing-Funktion nutzen können, muss der vorhandene AKS-Cluster über mindestens einen Knotenpool verfügen, der auf der SKU für virtuelle **DC<x>s-v2**-Computer basiert. Weitere Informationen zur SKU für virtuelle Confidential Computing-Computer der DCsv2-Serie finden Sie [hier](virtual-machine-solutions.md).
    
  ```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2 --aks-custom-headers usegen2vm=true

output node pool added

Verify

az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

```console
kubectl get nodes
```
Die Ausgabe sollte den neu hinzugefügten Pool „confcompool1“ im AKS-Cluster enthalten.

```console
$ kubectl get pods --all-namespaces

output (you may also see other daemonsets along SGX daemonsets as below)
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Wenn die Ausgabe wie oben aussieht, ist Ihr AKS-Cluster bereit für die Ausführung vertraulicher Anwendungen.

## <a name="hello-world-from-isolated-enclave-application"></a>Hello World aus isolierter Enklavenanwendung <a id="hello-world"></a>
Erstellen Sie eine Datei mit dem Namen *hello-world-enclave.yaml*, und fügen Sie das folgende YAML-Manifest ein. Diesen Open Enclave-basierten Beispielanwendungscode finden Sie im [Open Enclave-Projekt](https://github.com/openenclave/openenclave/tree/master/samples/helloworld).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 5 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
  ```

Erstellen Sie nun mithilfe des Befehls „kubectl apply“ einen Beispielauftrag, der in einer sicheren Enklave gestartet wird, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

Durch Ausführen der folgenden Befehle können Sie sich vergewissern, dass von der Workload erfolgreich eine vertrauenswürdige Ausführungsumgebung (Enklave) erstellt wurde:

```console
$ kubectl get jobs -l app=sgx-test
```

```console
$ kubectl get jobs -l app=sgx-test
NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test
```

```console
$ kubectl get pods -l app=sgx-test
NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test
```

```console
$ kubectl logs -l app=sgx-test
Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie die folgenden Befehle, um die zugeordneten Knotenpools zu entfernen oder den AKS-Cluster zu löschen:

Löschen des AKS-Clusters
``````azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```
Removing the confidential computing node pool

``````azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name myNodePoolName --resource-group myResourceGroup
``````

## <a name="next-steps"></a>Nächste Schritte

Führen Sie Python-Anwendungen, Node-Anwendungen usw. vertraulich über vertrauliche Container aus, indem Sie sich [Beispiele für vertrauliche Container](https://github.com/Azure-Samples/confidential-container-samples) ansehen.

Führen Sie Enclave-fähige Anwendungen aus, indem Sie sich [Beispiele für Enclave-fähige Azure-Container](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/) ansehen.