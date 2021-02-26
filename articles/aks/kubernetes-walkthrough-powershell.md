---
title: 'Schnellstart: Bereitstellen eines AKS-Clusters mithilfe von PowerShell'
description: Hier erfahren Sie, wie Sie über PowerShell schnell einen Kubernetes-Cluster erstellen, eine Anwendung bereitstellen und die Leistung in Azure Kubernetes Service (AKS) überwachen.
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 131469a955190561d8854aad4a7f77c8ca15a222
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578776"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Schnellstart: Bereitstellen eines Azure Kubernetes Service-Clusters mit PowerShell

In dieser Schnellstartanleitung stellen Sie einen AKS-Cluster (Azure Kubernetes Service) über PowerShell bereit. AKS ist ein Managed Kubernetes-Dienst, mit dem Sie Cluster schnell bereitstellen und verwalten können. In dem Cluster wird eine Anwendung mit mehreren Containern ausgeführt, die ein Web-Front-End und eine Redis-Instanz enthält. Sie erfahren dann, wie Sie den Zustand des Clusters und der Pods überwachen können, in denen Ihre Anwendung ausgeführt wird.

Weitere Informationen zum Erstellen eines Windows Server-Knotenpools finden Sie unter [Erstellen eines AKS-Clusters, der Windows Server-Container unterstützt][windows-container-powershell].

![In Azure Kubernetes Service bereitgestellte Abstimmungs-App](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Für diese Schnellstartanleitung werden Grundkenntnisse in Bezug auf die Kubernetes-Konzepte vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) eine bestimmte Abonnement-ID aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Wenn Sie eine Ressourcengruppe erstellen, müssen Sie einen Speicherort angeben. An diesem Speicherort werden die Metadaten der Ressourcengruppe gespeichert. Darüber hinaus werden dort die Ressourcen in Azure ausgeführt, wenn Sie während der Ressourcenerstellung keine andere Region angeben. Erstellen Sie mit dem Cmdlet [New-AzResourceGroup][new-azresourcegroup] eine neue Ressourcengruppe.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen **myResourceGroup** in der Region **eastus** erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Die folgende Beispielausgabe zeigt, dass die Ressourcengruppe erfolgreich erstellt wurde:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Erstellen eines ACS-Clusters

Verwenden Sie das Befehlszeilenprogramm `ssh-keygen`, um ein SSH-Schlüsselpaar zu generieren. Weitere Informationen finden Sie unter [Kurzanleitung: Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Verwenden Sie das Cmdlet [New-AzAks][new-azaks], um einen AKS-Cluster zu erstellen. Im folgenden Beispiel wird ein Cluster mit dem Namen **myAKSCluster** mit einem Knoten erstellt. Azure Monitor für Container ist ebenfalls standardmäßig aktiviert. Dies dauert mehrere Minuten.

> [!NOTE]
> Beim Erstellen eines AKS-Clusters wird automatisch eine zweite Ressourcengruppe erstellt, um die AKS-Ressourcen zu speichern. Weitere Informationen finden Sie unter [Warum werden zwei Ressourcengruppen mit AKS erstellt?](./faq.md#why-are-two-resource-groups-created-with-aks).

```azurepowershell-interactive
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

Nach wenigen Minuten ist die Ausführung des Befehls abgeschlossen, und es werden Informationen zum Cluster zurückgegeben.

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Um `kubectl` lokal zu installieren, verwenden Sie das Cmdlet `Install-AzAksKubectl`:

```azurepowershell
Install-AzAksKubectl
```

Verwenden Sie das Cmdlet [Import-AzAksCredential][import-azakscredential], um `kubectl` für die Verbindung mit Ihrem Kubernetes-Cluster zu konfigurieren. Das folgende Beispiel lädt Anmeldeinformationen herunter, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert:

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get][kubectl-get], um eine Liste der Clusterknoten zurückzugeben.

```azurepowershell-interactive
.\kubectl get nodes
```

Die folgende Beispielausgabe zeigt den in den vorherigen Schritten erstellten Knoten. Vergewissern Sie sich, dass der Knoten den Status **Bereit** hat:

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Eine Kubernetes-Manifestdatei definiert einen gewünschten Zustand (Desired State) für den Cluster – also beispielsweise, welche Containerimages ausgeführt werden sollen. In dieser Schnellstartanleitung wird ein Manifest verwendet, um alle Objekte zu erstellen, die zum Ausführen der Azure Vote-Anwendung benötigt werden. Dieses Manifest umfasst zwei [Kubernetes-Bereitstellungen][kubernetes-deployment]: eine für die Azure Vote-Python-Beispielanwendungen und eine für eine Redis-Instanz. Außerdem werden zwei Kubernetes-Dienste erstellt: ein interner Dienst für die Redis-Instanz und ein externer Dienst, über den aus dem Internet auf die Azure Vote-Anwendung zugegriffen wird.

Erstellen Sie eine Datei namens `azure-vote.yaml`, und fügen Sie die folgende YAML-Definition ein. Bei Verwendung von Azure Cloud Shell kann diese Datei mit `vi` oder `nano` erstellt werden – genau wie bei der Verwendung eines virtuellen oder physischen Systems:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Stellen Sie die Anwendung über den Befehl [kubectl apply][kubectl-apply] bereit, und geben Sie den Namen Ihres YAML-Manifests an:

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

In der folgenden Beispielausgabe sind die erfolgreich erstellten Bereitstellungen und Dienste aufgeführt:

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>Testen der Anwendung

Wenn die Anwendung ausgeführt wird, macht ein Kubernetes-Dienst das Anwendungs-Front-End im Internet verfügbar.
Dieser Vorgang kann einige Minuten dauern.

Verwenden Sie zum Überwachen des Fortschritts den Befehl [kubectl get service][kubectl-get] mit dem Argument `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Die externe IP-Adresse (**EXTERNAL-IP**) für den Dienst **azure-vote-front** wird zunächst als **ausstehend** angezeigt.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (**EXTERNAL-IP**) von pending (**ausstehend**) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Azure-Abstimmungs-App in Aktion zu sehen.

![In Azure Kubernetes Service bereitgestellte Abstimmungs-App](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Beim Erstellen des AKS-Clusters wurde [Azure Monitor für Container](../azure-monitor/containers/container-insights-overview.md) aktiviert, um Integritätsmetriken für die Clusterknoten und die Pods zu erfassen. Diese Integritätsmetriken sind im Azure-Portal verfügbar.

## <a name="delete-the-cluster"></a>Löschen des Clusters

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen. Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung][sp-delete]. Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="get-the-code"></a>Abrufen des Codes

In dieser Schnellstartanleitung wurden vorab erstellte Containerimages verwendet, um eine Kubernetes-Bereitstellung zu erstellen. Der dazugehörige Anwendungscode, die Dockerfile-Datei und die Kubernetes-Manifestdatei sind auf GitHub verfügbar.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Kubernetes-Cluster und eine Anwendung mit mehreren Containern dafür bereitgestellt. Sie können auch auf das [Kubernetes-Webdashboard][kubernetes-dashboard] für Ihren AKS-Cluster zugreifen.

Weitere Informationen zu Azure Container Service sowie ein vollständiges Beispiel vom Code bis zur Bereitstellung finden Sie im Kubernetes-Clustertutorial.

> [!div class="nextstepaction"]
> [AKS-Tutorial][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
