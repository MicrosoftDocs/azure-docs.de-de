---
title: Hinzufügen eines Spot-Knotenpools zu einem Azure Kubernetes Service-Cluster (AKS)
description: Erfahren Sie, wie Sie einen Spot-Knotenpool zu einem Azure Kubernetes Service-Cluster (AKS) hinzufügen.
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 5fd97560c3a6e41b49beb957c7b8d79369799c21
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078950"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Hinzufügen eines Spot-Knotenpools zu einem Azure Kubernetes Service-Cluster (AKS)

Ein Spot-Knotenpool ist ein Knotenpool, der von einer [Spot-VM-Skalierungsgruppe][vmss-spot] unterstützt wird. Durch die Verwendung von Spot-VMs für Knoten mit Ihrem AKS-Cluster können Sie die ungenutzte Kapazität in Azure zu erheblichen Kosteneinsparungen nutzen. Die Menge der verfügbaren, nicht genutzten Kapazität hängt von vielen Faktoren ab, einschließlich der Größe der Knotenpunkte, der Region und der Tageszeit.

Bei der Bereitstellung eines Spot-Knotenpools weist Azure die Spot-Knoten zu, wenn Kapazität verfügbar ist. Aber es gibt keine SLA für die Spot-Knoten. Eine Spot-Skalierungsgruppe, die den Spot-Knotenpool unterstützt, wird in einer einzelnen Fehlerdomäne bereitgestellt und gewährleistet keine Hochverfügbarkeit. Wenn die Kapazität von Azure wieder benötigt wird, werden die Spot-Knoten durch die Azure-Infrastruktur entfernt.

Spot-Knotenpunkte eignen sich hervorragend für Workloads, die mit Unterbrechungen, vorzeitigen Beendigungen oder Entfernungen umgehen können. Workloads wie Batchverarbeitungsaufträge, Entwicklungs- und Testumgebungen und große Computeworkloads können z. B. gute Kandidaten sein, die in einem Spot-Knotenpool eingeplant werden können.

In diesem Artikel fügen Sie einem bestehenden Azure Kubernetes Service-Cluster (AKS) einen sekundären Spot-Knotenpool hinzu.

Für diesen Artikel werden Grundkenntnisse im Zusammenhang mit Kubernetes und Azure Load Balancer vorausgesetzt. Weitere Informationen finden Sie unter [Grundlegende Kubernetes-Konzepte für Azure Kubernetes Service (AKS)][kubernetes-concepts].

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie einen Cluster für die Verwendung eines Spot-Knotenpools erstellen, muss dieser Cluster auch VM-Skalierungsgruppen für Knotenpools und den Lastenausgleich mit *Standard* -SKU verwenden. Sie müssen auch einen zusätzlichen Knotenpool hinzufügen, nachdem Sie Ihren Cluster erstellt haben, um einen Spot-Knotenpool zu verwenden. Das Hinzufügen eines zusätzlichen Knotenpools wird in einem späteren Schritt behandelt.

In diesem Artikel wird vorausgesetzt, dass Sie mindestens die Version 2.14 der Azure-Befehlszeilenschnittstelle verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

### <a name="limitations"></a>Einschränkungen

Die folgenden Einschränkungen gelten für die Erstellung und Verwaltung von AKS-Clustern mit einem Spot-Knotenpool:

* Ein Spot-Knotenpool kann nicht der Standardknotenpool des Clusters sein. Ein Spot-Knotenpool kann nur für einen sekundären Pool verwendet werden.
* Sie können kein Upgrade für einen Spot-Knotenpool durchführen, da Spot-Knotenpools nicht das Absperren und Ausgleichen garantieren können. Sie müssen Ihren bestehenden Spot-Knotenpool durch einen neuen ersetzen, um Vorgänge wie das Upgrade der Kubernetes-Version durchzuführen. Um einen Spot-Knotenpool zu ersetzen, erstellen Sie einen neuen Spot-Knotenpool mit einer anderen Version von Kubernetes. Warten Sie dann, bis sein Status *Bereit* lautet, und entfernen Sie dann den alten Knotenpool.
* Die Steuerungsebene und die Knotenpools können nicht gleichzeitig aktualisiert werden. Sie müssen die Upgrades separat durchführen oder den Spot-Knotenpool entfernen, um die Steuerungsebene und die übrigen Knotenpools gleichzeitig zu aktualisieren.
* Ein Spot-Knotenpool muss VM-Skalierungsgruppen verwenden.
* Sie können „ScaleSetPriority“ oder „SpotMaxPrice“ nach der Erstellung nicht mehr ändern.
* Beim Festlegen von „SpotMaxPrice“ muss der Wert -1 oder ein positiver Wert mit bis zu fünf Dezimalstellen sein.
* Ein Spot-Knotenpool hat die Bezeichnung *kubernetes.azure.com/scalesetpriority:spot* , den Taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* , und Systempods weisen die Antiaffinität auf.
* Sie müssen eine [entsprechende Toleranz][spot-toleration] hinzufügen, um Workloads auf einem Spot-Knotenpool zu planen.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Hinzufügen eines Spot-Knotenpools zu einem AKS-Cluster

Sie müssen einen Spot-Knotenpool zu einem bestehenden Cluster hinzufügen, bei dem mehrere Knotenpools aktiviert sind. Weitere Details zum Erstellen eines AKS-Clusters mit mehreren Knotenpools finden Sie [hier][use-multiple-node-pools].

Erstellen Sie einen Knotenpool mit dem Befehl [az aks nodepool add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Standardmäßig erstellen Sie in Ihrem AKS-Cluster einen Knotenpool mit einer *Priorität* vom Typ *Regulär* , wenn Sie einen Cluster mit mehreren Knotenpools erstellen. Der obige Befehl fügt einem vorhandenen AKS-Cluster einen Hilfsknotenpool mit einer *Priorität* vom Typ *Spot* hinzu. Die *Priorität* vom Typ *Spot* macht den Knotenpool zu einem Spot-Knotenpool. Der Parameter *eviction-policy* ist im obigen Beispiel auf *Löschen* festgelegt, was dem Standardwert entspricht. Wenn Sie die [Entfernungsrichtlinie][eviction-policy] auf *Löschen* festlegen, werden Knoten in der zugrunde liegenden Skalierungsgruppe des Knotenpools bei der Entfernung gelöscht. Sie können die Entfernungsrichtlinie auch auf *Zuordnung aufheben* festlegen. Wenn Sie die Entfernungsrichtlinie auf *Zuordnung aufheben* festlegen, werden die Knoten in der zugrunde liegenden Skalierungsgruppe bei der Entfernung auf den Zustand „Beendet/Zuordnung aufgehoben“ festgelegt. Knoten im Zustand „Beendet/Zuordnung aufgehoben“ werden auf Ihr Computekontingent angerechnet und können Probleme bei der Skalierung oder dem Upgrade von Clustern verursachen. Die Werte für *Priorität* und *eviction-policy* (Entfernungsrichtlinie) können nur während der Erstellung des Knotenpools festgelegt werden. Diese Werte können später nicht mehr aktualisiert werden.

Der Befehl aktiviert auch die [Automatische Clusterskalierung][cluster-autoscaler], die für die Verwendung mit Spot-Knotenpools empfohlen wird. Auf der Grundlage der in Ihrem Cluster ausgeführten Workloads skaliert die automatische Clusterskalierung die Anzahl der Knoten im Knotenpool zentral hoch und herunter. Bei Spot-Knotenpools skaliert die automatische Clusterskalierung die Anzahl der Knoten nach einer Entfernung hoch, falls noch weitere Knoten erforderlich sind. Wenn Sie die maximale Anzahl von Knoten ändern, die ein Knotenpool aufweisen kann, müssen Sie auch den mit der automatischen Clusterskalierung verbundenen `maxCount`-Wert anpassen. Wenn Sie keine automatische Clusterskalierung verwenden, wird der Spot-Pool nach der Entfernung schließlich auf Null sinken und eine manuelle Bedienung erfordern, um weitere Spot-Knoten zu erhalten.

> [!Important]
> Planen Sie Workloads nur für Spot-Knotenpools, die Unterbrechungen verarbeiten können, z. B. Batchverarbeitungsaufträge und Testumgebungen. Es wird empfohlen, dass Sie in Ihrem Spot-Knotenpool [Taints und Toleranzen][taints-tolerations] einrichten, um sicherzustellen, dass in einem Spot-Knotenpool nur Workloads geplant werden, die mit Knotenentfernungen umgehen können. Beispielsweise fügt der obige Befehl „ny“ standardmäßig einen Taint von *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* hinzu, sodass nur Pods mit einer entsprechenden Toleranz auf diesem Knoten geplant werden.

## <a name="verify-the-spot-node-pool"></a>Überprüfen des Spot-Knotenpools

So überprüfen Sie, ob Ihr Knotenpool als Spot-Knotenpool hinzugefügt wurde

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Bestätigen Sie, dass *scaleSetPriorität* auf *Spot* festgelegt ist.

Um einen Pod für die Ausführung auf einem Spot-Knoten zu planen, fügen Sie eine Toleranz hinzu, die dem auf Ihren Spot-Knoten angewendeten Taint entspricht. Das folgende Beispiel zeigt einen Teil einer YAML-Datei, der eine Toleranz definiert, die einem im vorherigen Schritt verwendeten *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* -Taint entspricht.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Wenn ein Pod mit dieser Toleranz bereitgestellt wird, kann Kubernetes den Pod erfolgreich auf den Knoten mit dem angewendeten Taint planen.

## <a name="max-price-for-a-spot-pool"></a>Maximaler Preis für einen Spot-Pool
[Die Preise für Spot-Instanzen variieren][pricing-spot] je nach Region und SKU. Weitere Informationen finden Sie unter den Preisen für [Linux][pricing-linux] und [Windows][pricing-windows].

Bei der variablen Preisgestaltung können Sie einen maximalen Preis in US-Dollar (USD) mit bis zu 5 Dezimalstellen festlegen. Der Wert *0,98765* würde beispielsweise einem maximalen Preis von 0,98765 US-Dollar pro Stunde entsprechen. Wenn Sie den maximalen Preis auf *-1* festlegen, wird die Instanz nicht basierend auf dem Preis entfernt. Der Preis für die Instanz entspricht dem aktuellen Preis für Spot-Instanzen oder dem Preis für eine Standardinstanz, je nachdem, welcher Preis niedriger ist, solange Kapazität und Kontingente verfügbar sind.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen Spot-Knotenpool zu einem AKS-Cluster hinzufügen können. Weitere Informationen zum knotenpoolübergreifenden Steuern von Pods finden Sie unter [Best Practices für erweiterte Scheduler-Funktionen in Azure Kubernetes Service (AKS)][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md