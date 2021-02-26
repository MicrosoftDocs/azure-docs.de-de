---
title: 'Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über die Azure-Befehlszeilenschnittstelle mit Confidential Computing-Knoten'
description: Hier erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle einen AKS-Cluster mit vertraulichen Knoten erstellen und eine Hello World-App bereitstellen.
author: agowdamsft
ms.service: container-service
ms.topic: quickstart
ms.date: 2/5/2020
ms.author: amgowda
ms.openlocfilehash: b6fe8f4fe34799a71d59b7487d96217b4ac6a429
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833202"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-with-confidential-computing-nodes-dcsv2-using-azure-cli-preview"></a>Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) mit Confidential Computing-Knoten (DCsv2) über die Azure-Befehlszeilenschnittstelle (Vorschauversion)

Diese Schnellstartanleitung richtet sich an Entwickler oder Clusteroperatoren, die schnell einen AKS-Cluster erstellen und eine Anwendung bereitstellen möchten, um Anwendungen mithilfe des verwalteten Kubernetes-Diensts in Azure zu überwachen.

## <a name="overview"></a>Übersicht

In dieser Schnellstartanleitung erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle einen AKS-Cluster (Azure Kubernetes Service) mit Confidential Computing-Knoten bereitstellen und eine Hello World-Anwendung in einer Enklave ausführen. AKS ist ein Managed Kubernetes-Dienst, mit dem Sie Cluster schnell bereitstellen und verwalten können. Weitere Informationen zu AKS finden Sie [hier](../aks/intro-kubernetes.md).

> [!NOTE]
> Virtuelle Confidential Computing-Computer der DCsv2-Serie nutzen spezielle Hardware, für die höhere Preise gelten und die möglicherweise nicht in allen Regionen verfügbar ist. Weitere Informationen zu verfügbaren SKUs und unterstützten Regionen für virtuelle Computer finden Sie [hier](virtual-machine-solutions.md).

> Bei DCsv2 werden in Azure virtuelle Computer der 2. Generation genutzt. VMs der 2. Generation sind eine Previewfunktion von AKS. 

### <a name="deployment-pre-requisites"></a>Voraussetzungen für die Bereitstellung
Bei dieser Bereitstellungsanleitung wird Folgendes vorausgesetzt:

1. Ein aktives Azure-Abonnement. Sollten Sie über kein Azure-Abonnement verfügen, können Sie [ein kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
1. Auf dem Bereitstellungscomputer muss mindestens die Azure CLI-Version 2.0.64 installiert und konfiguriert sein. (Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).
1. Die Erweiterung [aks-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview) (Mindestversion: 0.4.62). 
1. Verfügbarkeit des VM-Kernkontingents. In Ihrem Abonnement müssen mindestens sechs **DC<x>s-v2**-Kerne für die Verwendung verfügbar sein. Das VM-Kernkontingent für Confidential Computing liegt standardmäßig bei acht Kernen pro Abonnement. Wenn Sie einen Cluster bereitstellen möchten, der mehr als acht Kerne erfordert, gehen Sie wie [hier](../azure-portal/supportability/per-vm-quota-requests.md) beschrieben vor, um ein Ticket für die Kontingenterhöhung zu erstellen.

### <a name="confidential-computing-node-features-dcxs-v2"></a>Features von Confidential Computing-Knoten (DC<x>s-v2)

1. Von Linux-Workerknoten werden nur Linux-Container unterstützt.
1. VM der 2. Generation mit Ubuntu 18.04-VM-Knoten
1. Intel SGX-basierte CPU mit Encrypted Page Cache Memory (EPC). Weitere Informationen finden Sie [hier](./faq.md).
1. Unterstützung der Kubernetes-Version 1.16 +
1. Der Intel SGX DCAP-Treiber ist auf den AKS-Knoten vorinstalliert. Weitere Informationen finden Sie [hier](./faq.md).
1. Unterstützung der CLI-basierten Bereitstellung während der Vorschauphase und der portalbasierten Bereitstellung nach der Phase „Allgemeine Verfügbarkeit“.


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
### <a name="generation-2-vms-feature-registration-on-azure"></a>Featureregistrierung der VM der 2. Generation in Azure
„Gen2VMPreview“ wird für das Azure-Abonnement registriert. Mit diesem Feature können Sie VMs der 2. Generation als AKS-Knotenpools bereitstellen:

```azurecli-interactive
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```
Es kann einige Minuten dauern, bis der Status als Registriert angezeigt wird. Der Registrierungsstatus kann mit dem Befehl „az feature list“ überprüft werden. Diese Featureregistrierung wird nur einmal pro Abonnement durchgeführt. Falls diese Registrierung schon erfolgt ist, können Sie den obigen Schritt überspringen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```
Wenn der Zustand „registered“ (Registriert) lautet, aktualisieren Sie die Registrierung des Ressourcenanbieters „Microsoft.ContainerService“ mithilfe des Befehls „az provider register“:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="azure-confidential-computing-feature-registration-on-azure-optional-but-recommended"></a>Azure Confidential Computing-Featureregistrierung in Azure (optional, aber empfohlen)
„AKS-ConfidentialComputingAddon“ wird für das Azure-Abonnement registriert. Mit diesem Feature werden zwei DaemonSets hinzugefügt ([hier](./confidential-nodes-aks-overview.md#aks-provided-daemon-sets-addon) ausführlich beschrieben):
1. SGX-Gerätetreiber-Plug-In
2. SGX-Nachweisangebot-Hilfsprogramm

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Es kann einige Minuten dauern, bis der Status als Registriert angezeigt wird. Der Registrierungsstatus kann mit dem Befehl „az feature list“ überprüft werden. Diese Featureregistrierung wird nur einmal pro Abonnement durchgeführt. Falls diese Registrierung schon erfolgt ist, können Sie den obigen Schritt überspringen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
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

Erstellen Sie nun mit dem Befehl az aks create einen AKS-Cluster.

```azurecli-interactive
# Create a new AKS cluster with  system node pool with Confidential Computing addon enabled
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom
```
Mit dem obigen Code wird ein neuer AKS-Cluster mit dem Systemknotenpool erstellt. Fahren Sie nun mit dem Hinzufügen eines Benutzerknotens mit dem Confidential Computing-Knotenpooltyp in AKS (DCsv2) fort.

Im folgenden Beispiel wird ein Benutzerknotenpool mit drei Knoten mit der Größe `Standard_DC2s_v2` hinzugefügt. Sie können [hier](../virtual-machines/dcv2-series.md) eine andere unterstützte Liste mit DCsv2-SKUs und -Regionen auswählen:

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2 --aks-custom-headers usegen2vm=true
```
Mit dem obigen Befehl sollte ein neuer Knotenpool mit **DC<x>s-v2** und automatischer Ausführung von zwei DaemonSets für den Knotenpool hinzugefügt werden ([SGX-Geräte-Plug-In](confidential-nodes-aks-overview.md#sgx-plugin) & [SGX-Angebotshilfsprogramm](confidential-nodes-aks-overview.md#sgx-quote)).

Rufen Sie die Anmeldeinformationen für Ihren AKS-Cluster mit dem Befehl az aks get-credentials ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```
Vergewissern Sie sich, dass die Knoten richtig erstellt wurden und dass die SGX-bezogenen DaemonSets in **DC<x>s-v2**-Knotenpools ausgeführt werden. Verwenden Sie hierfür die folgenden kubectl-Befehle zum Abrufen von Pods und Knoten:

```console
$ kubectl get pods --all-namespaces

output
kube-system     sgx-device-plugin-xxxx     1/1     Running
kube-system     sgx-quote-helper-xxxx      1/1     Running
```
Wenn die Ausgabe wie oben aussieht, ist Ihr AKS-Cluster bereit für die Ausführung vertraulicher Anwendungen.

Navigieren Sie zum Bereitstellungsabschnitt [Hello World aus isolierter Enklavenanwendung](#hello-world), um eine App in einer Enklave zu testen. Alternativ können Sie wie folgt vorgehen, um weitere Knotenpools in AKS hinzuzufügen. (AKS unterstützt die parallele Verwendung von SGX-Knotenpools SGX-fremden Knotenpools.)

## <a name="adding-confidential-computing-node-pool-to-existing-aks-cluster"></a>Hinzufügen eines Confidential Computing-Knotenpools zu einem vorhandenen AKS-Cluster<a id="existing-cluster"></a>

In diesem Abschnitt wird davon ausgegangen, dass Sie bereits über einen aktiven AKS-Cluster verfügen, der die im Abschnitt „Voraussetzungen“ aufgeführten Kriterien erfüllt.

Zuerst fügen Sie das Feature dem Azure-Abonnement hinzu.

```azurecli-interactive
az feature register --name AKS-ConfidentialComputingAddon --namespace Microsoft.ContainerService
```
Es kann einige Minuten dauern, bis der Status als Registriert angezeigt wird. Der Registrierungsstatus kann mit dem Befehl „az feature list“ überprüft werden. Diese Featureregistrierung wird nur einmal pro Abonnement durchgeführt. Falls diese Registrierung schon erfolgt ist, können Sie den obigen Schritt überspringen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ConfidentialComputingAddon')].{Name:name,State:properties.state}"
```
Wenn der Zustand „registered“ (Registriert) lautet, aktualisieren Sie die Registrierung des Ressourcenanbieters „Microsoft.ContainerService“ mithilfe des Befehls „az provider register“:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Aktivieren Sie als Nächstes die Confidential Computing-bezogenen AKS-Add-Ons für den vorhandenen Cluster:

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```
Fügen Sie dem Cluster dann einen **DC<x>s-v2**-Benutzerknotenpool hinzu:
    
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
Erstellen Sie eine Datei mit dem Namen *hello-world-enclave.yaml*, und fügen Sie das folgende YAML-Manifest ein. Diesen Open Enclave-basierten Beispielanwendungscode finden Sie im [Open Enclave-Projekt](https://github.com/openenclave/openenclave/tree/master/samples/helloworld). Für die unten angegebene Bereitstellung wird angenommen, dass Sie das Add-On „confcom“ bereitgestellt haben.

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
