---
title: Erstellen eines Windows Server-Containers in einem AKS-Cluster mithilfe der Azure CLI
description: Hier erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle schnell einen Kubernetes-Cluster erstellen und eine Anwendung in einem Windows Server-Container in Azure Kubernetes Service (AKS) bereitstellen.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: a14659b64bbc86cfc50cbf8a377c0245fba25065
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886242"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Erstellen eines Windows Server-Containers auf einem Azure Kubernetes Service (AKS)-Cluster mit der Azure-Befehlszeilenschnittstelle

Azure Kubernetes Service (AKS) ist ein verwalteter Kubernetes-Dienst, mit dem Sie schnell Cluster bereitstellen und verwalten können. In diesem Artikel stellen Sie einen AKS-Cluster über die Azure-Befehlszeilenschnittstelle bereit. Sie stellen auch eine ASP.NET-Beispielanwendung in einem Windows Server-Container für den Cluster bereit.

![Abbildung der Navigation zur ASP.NET-Beispielanwendung](media/windows-container/asp-net-sample-app.png)

Für diesen Artikel werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Einschränkungen

Die folgenden Einschränkungen gelten für die Erstellung und Verwaltung von AKS-Clustern, die mehrere Knotenpools unterstützen:

* Sie können den ersten Knotenpool nicht löschen.

Die folgenden zusätzlichen Einschränkungen gelten für Windows Server-Knotenpools:

* Der AKS-Cluster kann maximal 10 Knotenpools umfassen.
* Der AKS-Cluster kann maximal 100 Knoten in den einzelnen Knotenpools umfassen.
* Der Name des Windows Server-Knotenpools ist auf 6 Zeichen begrenzt.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, müssen Sie einen Speicherort angeben. An diesem Speicherort werden die Metadaten der Ressourcengruppe gespeichert. Darüber hinaus werden dort die Ressourcen in Azure ausgeführt, wenn Sie während der Ressourcenerstellung keine andere Region angeben. Erstellen Sie mit dem Befehl [az group create][az-group-create] eine Ressourcengruppe.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

> [!NOTE]
> In diesem Artikel wird die Bash-Syntax für die Befehle des Tutorials verwendet.
> Stellen Sie bei Verwendung von Azure Cloud Shell sicher, dass die Dropdownliste oben links im Cloud Shell-Fenster auf **Bash** festgelegt ist.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Um einen AKS-Cluster auszuführen, der Knotenpools für Windows Server-Container unterstützt, muss Ihr Cluster eine Netzwerkrichtlinie verwenden, die das [Azure CNI][azure-cni-about]-Netzwerk-Plug-In (Erweitert) verwendet. Detaillierte Informationen zur Planung der erforderlichen Subnetzbereiche sowie Netzwerküberlegungen finden Sie unter [Konfigurieren von Azure CNI-Netzwerken][use-advanced-networking]. Erstellen Sie mithilfe des Befehls [az aks create][az-aks-create] einen AKS-Cluster namens *myAKSCluster*. Dieser Befehl erstellt die erforderlichen Netzwerkressourcen, wenn sie nicht vorhanden sind.

* Der Cluster wird mit zwei Knoten konfiguriert.
* Die Parameter *windows-admin-password* und *windows-admin-username* legen die Anmeldeinformationen für alle Windows Server-Container fest, die auf dem Cluster erstellt wurden, und müssen die [Kennwortanforderungen von Windows Server][windows-server-password] erfüllen.
* Der Knotenpool verwendet `VirtualMachineScaleSets`.

> [!NOTE]
> Um zu gewährleisten, dass Ihr Cluster zuverlässig funktioniert, sollten Sie mindestens zwei Knoten im Standardknotenpool ausführen.

Geben Sie Ihr eigenes sicheres Kennwort (*PASSWORD_WIN*) an (für diesen Artikel werden Befehle in eine BASH-Shell eingegeben):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Wenn ein Kennwortvalidierungsfehler zurückgegeben wird, überprüfen Sie, ob der Parameter *windows-admin-password* die [Kennwortanforderungen von Windows Server][windows-server-password] erfüllt. Wenn das Kennwort die Anforderungen erfüllt, versuchen Sie, Ihre Ressourcengruppe in einer anderen Region zu erstellen. Versuchen Sie anschließend, den Cluster mit der neuen Ressourcengruppe zu erstellen.

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster im JSON-Format zurückgegeben. Gelegentlich kann die Bereitstellung des Clusters länger als ein paar Minuten dauern. Warten Sie in diesen Fällen bis zu 10 Minuten.

## <a name="add-a-windows-server-node-pool"></a>Hinzufügen eines Windows Server-Knotenpools

Standardmäßig wird ein AKS-Cluster mit einem Knotenpool erstellt, der Linux-Container ausführen kann. Verwenden Sie den Befehl `az aks nodepool add`, um einen zusätzlichen Knotenpool hinzuzufügen, der Windows Server-Container neben dem Linux-Knotenpool ausführen kann.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Der obige Befehl erstellt einen neuen Knotenpool namens *npwin* und fügt ihn dem *myAKSCluster* hinzu. Wenn Sie einen Knotenpool erstellen, um Windows Server-Container auszuführen, ist der Standardwert für *node-vm-size* *Standard_D2s_v3*. Wenn Sie den Parameter *node-vm-size* festlegen, sollten Sie die Liste mit den [eingeschränkten VM-Größen][restricted-vm-sizes] überprüfen. Die empfohlene Mindestgröße ist *Standard_D2s_v3*. Der obige Befehl verwendet auch das Standardsubnetz im Standard-Vnet, das beim Ausführen von `az aks create` erstellt wurde.

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Verwenden Sie für die lokale Installation von `kubectl` den Befehl [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get][kubectl-get], um eine Liste der Clusterknoten zurückzugeben.

```console
kubectl get nodes
```

Die folgende Beispielausgabe zeigt alle Knoten im Cluster. Vergewissern Sie sich, dass alle Knoten den Status *Bereit* haben:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. In diesem Artikel wird ein Manifest verwendet, um alle Objekte zu erstellen, die zum Ausführen der ASP.NET-Beispielanwendung in einem Windows Server-Container erforderlich sind. Dieses Manifest beinhaltet eine [Kubernetes-Bereitstellung][kubernetes-deployment] für die ASP.NET-Beispielanwendung und einen externen [Kubernetes-Dienst][kubernetes-service] für den Zugriff auf die Anwendung über das Internet.

Die ASP.NET-Beispielanwendung wird als Teil des [.NET Framework-Beispiels][dotnet-samples] bereitgestellt und in einem Windows Server-Container ausgeführt. AKS verlangt, dass Windows Server-Container auf Images von *Windows Server 2019* oder höher basieren. Mit der Kubernetes-Manifestdatei muss auch eine [Knotenauswahl][node-selector] definiert werden. So wird Ihrem AKS-Cluster mitgeteilt, dass der Pod Ihrer ASP.NET-Beispielanwendung auf einem Knoten ausgeführt werden soll, für den die Ausführung von Windows Server-Containern möglich ist.

Erstellen Sie eine Datei namens `sample.yaml`, und fügen Sie die folgende YAML-Definition ein. Bei Verwendung von Azure Cloud Shell kann diese Datei mit `vi` oder `nano` erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Stellen Sie die Anwendung über den Befehl [kubectl apply][kubectl-apply] bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f sample.yaml
```

In der folgende Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar. Dieser Vorgang kann einige Minuten dauern. Gelegentlich kann die Bereitstellung des Diensts länger als ein paar Minuten dauern. Warten Sie in diesen Fällen bis zu 10 Minuten.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`.

```console
kubectl get service sample --watch
```

Die externe IP-Adresse *EXTERNAL-IP* für den *Beispieldienst* wird zunächst als *ausstehend* angezeigt.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Beispielanwendung in Aktion zu sehen.

![Abbildung der Navigation zur ASP.NET-Beispielanwendung](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Wenn beim Versuch, die Seite zu laden, ein Verbindungstimeout auftritt, sollten Sie mit dem folgenden Befehl überprüfen, ob die Beispiel-App bereit ist: [kubectl get pods --watch]. Manchmal wird der Windows-Container nicht gestartet, wenn Ihre externe IP-Adresse verfügbar ist.

## <a name="delete-cluster"></a>Löschen von Clustern

Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Befehl [az group delete][az-group-delete] die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete]. Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Kubernetes-Cluster und eine ASP.NET-Beispielanwendung in einem Windows Server-Container bereitgestellt. [Zugreifen auf das Kubernetes-Webdashboard][kubernetes-dashboard] für den Cluster, den Sie gerade erstellt haben.

Weitere Informationen zu Azure Container Service sowie ein vollständiges Beispiel vom Code bis zur Bereitstellung finden Sie im Kubernetes-Clustertutorial.

> [!div class="nextstepaction"]
> [AKS-Tutorial][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/insights/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference