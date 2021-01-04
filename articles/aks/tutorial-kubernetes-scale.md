---
title: Tutorial zu Kubernetes in Azure – Skalieren von Anwendungen
description: In diesem Azure Kubernetes Service-Tutorial (AKS) erfahren Sie, wie Knoten und Pods im Kubernetes skaliert und die horizontale automatische Pod-Skalierung implementiert werden.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e700934a965f836456458cb33dc46125bef4ab72
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746998"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Tutorial: Skalieren von Anwendungen in Azure Kubernetes Service (AKS)

Wenn Sie die Tutorials durchgearbeitet haben, verfügen Sie über einen funktionsfähigen Kubernetes-Cluster in AKS und haben die Azure Voting-Beispiel-App bereitgestellt. In diesem Tutorial – Teil 5 von 7 – skalieren Sie die Pods in der App auf und testen das Feature der automatischen Skalierung von Pods. Sie erfahren auch, wie Sie die Anzahl von Azure-VM-Knoten skalieren, um die Clusterkapazität für das Hosten von Workloads zu ändern. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Skalieren der Kubernetes-Knoten
> * Manuelles Skalieren von Kubernetes-Pods, die Ihre Anwendung ausführen
> * Konfigurieren der automatischen Skalierung von Pods, die das App-Front-End ausführen

In den wieteren Tutorials wird die Anwendung Azure Vote auf eine neue Version aktualisiert.

## <a name="before-you-begin"></a>Voraussetzungen

In den vorherigen Tutorials wurde eine Anwendung als Containerimage verpackt. Dieses Image wurde in Azure Container Registry hochgeladen, und Sie haben einen AKS-Cluster erstellt. Die Anwendung wurde dann für den AKS-Cluster bereitgestellt. Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, beginnen Sie mit [Tutorial 1: Erstellen von Containerimages][aks-tutorial-prepare-app].

Für dieses Tutorial müssen Sie mindestens Version 2.0.53 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="manually-scale-pods"></a>Manuelles Skalieren von Pods

Wenn in vorhergehenden Tutorials das Azure Vote-Front-End und eine Redis-Instanz bereitgestellt wurden, wurde ein einzelnes Replikat erstellt. Verwenden Sie den Befehl [kubectl get][kubectl-get] wie folgt, um Anzahl und Status der Pods in Ihrem Cluster anzuzeigen:

```console
kubectl get pods
```

Die folgende Beispielausgabe zeigt einen Front-End-Pod und einen Back-End-Pod:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ändern Sie die Anzahl von Pods in der Bereitstellung *azure-vote-front* manuell mithilfe des Befehls [kubectl scale][kubectl-scale]. Im folgenden Beispiel wird die Anzahl von Front-End-Pods auf *5* erhöht:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Führen Sie [kubectl get pods][kubectl-get] erneut aus, um sicherzustellen, dass AKS die zusätzlichen Pods erstellt. Nach ca. einer Minute sind die zusätzlichen Pods in Ihrem Cluster verfügbar:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatisches Skalieren von Pods

Kubernetes unterstützt [die automatische horizontale Skalierung von Pods][kubernetes-hpa], um die Anzahl von Pods in einer Bereitstellung je nach CPU-Nutzung und anderen ausgewählten Metriken anzupassen. Der [Metrikserver][metrics-server] wird verwendet, um die Ressourcenverwendung für Kubernetes bereitzustellen, und in AKS-Clustern der Version 1.10 und höher wird er automatisch bereitgestellt. Verwenden Sie zum Anzeigen der Version Ihres AKS-Clusters den Befehl [az aks show][az-aks-show]. Dies ist im folgenden Beispiel dargestellt:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Hat Ihr AKS-Cluster eine ältere Version als *1.10* , wird der Metrikserver nicht automatisch installiert. Installationsmanifeste für Metrikserver sind als Ressource vom Typ `components.yaml` in Metrikserverversionen verfügbar. Das bedeutet, dass sie über eine URL installiert werden können. Weitere Informationen zu diesen YAML-Definitionen finden Sie in der Infodatei im Abschnitt [Bereitstellung][metrics-server-github].
> 
> Beispielinstallation:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

Um die automatische Skalierungsfunktion zu verwenden, müssen für alle Container in Ihren Pods sowie für Ihre Pods CPU-Anforderungen und -Grenzwerte definiert sein. In der `azure-vote-front`-Bereitstellung fordert der Front-End-Container bereits 0,25 CPU an, und es gilt ein Grenzwert von 0,5 CPU. Diese Ressourcenanforderungen und -grenzwerte sind so definiert, wie im folgenden Beispielcodeausschnitt gezeigt:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Das folgende Beispiel verwendet den Befehl [kubectl autoscale][kubectl-autoscale], um die Anzahl von Pods in der Bereitstellung *azure-vote-front* automatisch zu skalieren. Wenn die durchschnittliche CPU-Auslastung aller Pods 50 % der angeforderten Nutzung überschreitet, erhöht die Autoskalierung die Pods auf maximal *10*  Instanzen. Mindestens *3* Instanzen werden dann für die Bereitstellung festgelegt:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Alternativ können Sie eine Manifestdatei erstellen, um das Verhalten der Autoskalierung und Ressourcengrenzwerte zu definieren. Es folgt ein Beispiel für eine Manifestdatei namens `azure-vote-hpa.yaml`.

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

Verwenden Sie `kubectl apply`, um die in der Manifestdatei `azure-vote-hpa.yaml` definierte Autoskalierung anzuwenden.

```
kubectl apply -f azure-vote-hpa.yaml
```

Führen Sie den `kubectl get hpa`-Befehl wie folgt aus, um den Status der automatischen Skalierungsfunktion anzuzeigen:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Nach einigen Minuten mit minimaler Last in der Azure Vote-App sinkt die Anzahl von Podreplikaten automatisch auf drei. Sie können `kubectl get pods` erneut ausführen, um zu sehen, wie die nicht benötigten Pods entfernt werden.

## <a name="manually-scale-aks-nodes"></a>Manuelles Skalieren von AKS-Knoten

Wenn Sie Ihren Kubernetes-Cluster mithilfe der Befehle im vorherigen Tutorial erstellt haben, verfügt er über zwei Knoten. Sie können die Anzahl der Knoten manuell anpassen, wenn Sie größere oder kleinere Containerworkloads in Ihrem Cluster planen.

Im folgenden Beispiel wird die Anzahl von Knoten im Kubernetes-Cluster *myAKSCluster* auf drei erhöht. Diese Ausführung dieses Befehls dauert einige Minuten.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Wenn der Cluster erfolgreich skaliert wurde, sieht die Ausgabe so wie im folgenden Beispiel aus:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie verschiedene Skalierungsfunktionen in Ihrem Kubernetes-Cluster verwendet. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Manuelles Skalieren von Kubernetes-Pods, die Ihre Anwendung ausführen
> * Konfigurieren der automatischen Skalierung von Pods, die das App-Front-End ausführen
> * Manuelles Skalieren der Kubernetes-Knoten

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Anwendungen in Kubernetes aktualisiert werden.

> [!div class="nextstepaction"]
> [Aktualisieren einer Anwendung in Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
