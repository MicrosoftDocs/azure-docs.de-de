---
title: '(VERALTET) Schnellstart: Azure Kubernetes-Cluster für Windows'
description: Hier lernen Sie schnell, einen Kubernetes-Cluster für Windows-Container in Azure Container Service über die Azure-Befehlszeilenschnittstelle zu erstellen.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: d7ce702bb726fb89780d251f31023c9490112c36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66148788"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-windows-containers"></a>(VERALTET) Bereitstellen eines Kubernetes-Clusters für Windows-Container

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Anleitung wird die Bereitstellung eines [Kubernetes](https://kubernetes.io/docs/home/)-Clusters in [Azure Container Service](../container-service-intro.md) über die Azure-Befehlszeilenschnittstelle beschrieben. Nach dem Bereitstellen des Clusters verbinden Sie diesen mit dem Kubernetes-Befehlszeilentool `kubectl` und stellen dann den ersten Windows-Container bereit.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Die Unterstützung für Windows-Container in Kubernetes in Azure Container Service befindet sich in der Vorschauphase. 
>

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Erstellen eines Kubernetes-Clusters
Erstellen Sie mit dem Befehl [az acs create](/cli/azure/acs#az-acs-create) einen Kubernetes-Cluster in Azure Container Service. 

Im folgenden Beispiel wird ein Cluster mit dem Namen *myK8sCluster* mit einem Linux-Masterknoten und zwei Windows-Agentknoten erstellt. In diesem Beispiel werden die für die Verbindung mit dem Linux-Masterknoten erforderlichen SSH-Schlüssel erstellt. In dem Beispiel wird *azureuser* als Administratorbenutzername und *myPassword12* als Kennwort für die Windows-Knoten verwendet. Aktualisieren Sie diese Werte auf andere, für Ihre Umgebung geeignete Werte. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Nach einigen Minuten ist der Befehl abgeschlossen, und es werden Informationen zu der Bereitstellung angezeigt.

## <a name="install-kubectl"></a>Installieren von kubectl

Zum Herstellen der Verbindung mit dem Kubernetes-Cluster auf Ihrem Clientcomputer verwenden Sie [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) (den Kubernetes-Befehlszeilenclient). 

Bei Verwendung von Azure Cloud Shell ist `kubectl` bereits installiert. Wenn Sie ihn lokal installieren möchten, verwenden Sie den Befehl [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Im folgenden Azure CLI-Beispiel wird `kubectl` in Ihrem System installiert. Führen Sie diesen Befehl unter Windows als Administrator aus.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Verbinden mit kubectl

Führen Sie den Befehl [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) aus, um `kubectl` für die Verbindung mit dem Kubernetes-Cluster zu konfigurieren. Im folgenden Beispiel wird die Clusterkonfiguration für den Kubernetes-Cluster heruntergeladen.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Um die Verbindung zwischen dem Cluster und Ihrem Computer zu überprüfen, führen Sie folgenden Befehl aus:

```azurecli-interactive
kubectl get nodes
```

`kubectl` listet den Master- und die Agentknoten auf.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Bereitstellen eines Windows IIS-Containers

Sie können einen Docker-Container in einem Kubernetes-*Pod* ausführen, der einen oder mehrere Container enthält. 

In diesem einfachen Beispiel wird mithilfe einer JSON-Datei ein Microsoft-Internetinformationsdienst-Container (IIS) angegeben und dann mit dem Befehl `kubctl apply` der Pod erstellt. 

Erstellen Sie eine lokale Datei mit dem Namen `iis.json`, und kopieren Sie den folgenden Text. Diese Datei weist Kubernetes an, IIS unter Windows Server 2016 Nano Server mithilfe eines öffentlichen Containerimages von [Docker Hub](https://hub.docker.com/r/microsoft/iis/) auszuführen. Der Container verwendet Port 80. Zunächst kann auf ihn aber nur innerhalb des Clusternetzwerks zugegriffen werden.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Geben Sie zum Starten des Pods Folgendes ein:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Geben Sie zum Nachverfolgen des Bereitstellungstyps Folgendes ein:
  
```azurecli-interactive
kubectl get pods
```

Während der Bereitstellung des Pods lautet der Status `ContainerCreating`. Es kann einige Minuten dauern, bis der Container in den Status `Running` wechselt.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Anzeigen der IIS-Willkommensseite

Geben Sie den folgenden Befehl ein, um den Pod mit einer öffentlichen IP-Adresse global verfügbar zu machen:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Dieser Befehl bewirkt, dass Kubernetes einen Dienst und eine Azure Load Balancer-Regel mit einer öffentlichen IP-Adresse für den Dienst erstellt. 

Führen Sie den folgenden Befehl aus, um den Status des Diensts anzuzeigen.

```azurecli-interactive
kubectl get svc
```

Zunächst wird die IP-Adresse als `pending` angezeigt. Nach ein paar Minuten ist die externe IP-Adresse des `iis`-Pods festgelegt:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Sie können die IIS-Standardwillkommensseite unter der externen IP-Adresse in einem Webbrowser Ihrer Wahl anzeigen:

![Abbildung: Navigieren zu IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Löschen von Clustern
Wenn der Cluster nicht mehr benötigt wird, können Sie mit dem Befehl [az group delete](/cli/azure/group#az-group-delete) die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen entfernen.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen Kubernetes-Cluster bereitgestellt, mit `kubectl` verbunden und einen Pod mit einem IIS-Container bereitgestellt. Weitere Informationen zu Azure Container Service erhalten Sie, wenn Sie mit dem Kubernetes-Tutorial fortfahren.

> [!div class="nextstepaction"]
> [Verwalten eines ACS Kubernetes-Clusters](container-service-tutorial-kubernetes-prepare-app.md)
