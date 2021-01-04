---
title: Bewährte Methoden für Schedulerfunktionen
titleSuffix: Azure Kubernetes Service
description: Lernen Sie die Best Practices des Clusteroperators für die Verwendung erweiterter Schedulerfunktionen kennen, wie z.B. Taints und Toleranzen, Knotenselektoren und Affinität oder Pod-interne Affinität und Antiaffinität in Azure Kubernetes Service (AKS) kennen.
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: c0c1f587b4e52607e9466300f976a52874c9e5ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993702"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Best Practices für erweiterte Schedulerfunktionen in Azure Kubernetes Service (AKS)

Beim Verwalten von Clustern in Azure Kubernetes Service (AKS) müssen Sie oft Teams oder Workloads isolieren. Der Kubernetes-Scheduler bietet erweiterte Funktionen, mit denen Sie steuern können, welche Pods auf bestimmten Knoten geplant werden können oder wie Multi-Pod-Anwendungen angemessen über den Cluster verteilt werden können. 

Dieser Artikel zu Best Practices konzentriert sich auf erweiterten Kubernetes-Planungsfunktionen für Clusteroperatoren. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Verwenden von Taints und Toleranzen, um einzuschränken welche Pods auf Knoten geplant werden können
> * Festlegen, dass Pods auf bestimmten Knoten mit Knotenselektoren oder Knotenaffinität ausgeführt werden
> * Trennen oder Gruppieren von Pods mit Pod-interner Affinität oder Antiaffinität

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Bereitstellen dedizierter Knoten mit Taints und Toleranzen

**Best Practice-Anleitung** – Beschränken Sie den Zugriff für ressourcenintensive Anwendungen, wie z.B. Eingangskontrollen, auf bestimmte Knoten. Halten Sie Knotenressourcen für Workloads verfügbar, die sie benötigen, und erlauben Sie keine Planung anderer Workloads auf den Knoten.

Wenn Sie Ihren AKS-Cluster erstellen, können Sie Knoten mit GPU-Unterstützung oder einer großen Anzahl leistungsstarker CPUs bereitstellen. Diese Knoten werden häufig für große Datenverarbeitungsworkloads wie Machine Learning (ML) oder Künstliche Intelligenz (KI) verwendet. Da diese Art von Hardware in der Regel eine teure Knotenressource ist, die bereitgestellt werden muss, begrenzen Sie die Workloads, die auf diesen Knoten geplant werden können. Möglicherweise möchten Sie stattdessen einige Knoten im Cluster für die Ausführung von Eingangsdiensten und die Verhinderung anderer Workloads verwenden.

Diese Unterstützung für verschiedene Knoten erhalten Sie durch die Verwendung von mehrerer Knotenpools. Ein AKS-Cluster bietet einen oder mehrere Knotenpools.

Der Kubernetes-Planer kann Taints und Toleranzen verwenden, um einzuschränken, welche Workloads auf Knoten ausgeführt werden können.

* Ein **Taint** wird auf einen Knoten angewendet, der anzeigt, dass nur bestimmte Pods darauf geplant werden können.
* Für den Pod wird anschließend eine **Toleranz** angewendet, damit dieser den Taint des Knotens *tolerieren* kann.

Wenn Sie einen Pod in einem AKS-Cluster bereitstellen, plant Kubernetes nur Pods auf Knoten, bei denen dem Taint eine Toleranz zugewiesen ist. Stellen Sie sich als Beispiel vor, dass Sie einen Knotenpool in Ihrem AKS-Cluster für Knoten mit GPU-Unterstützung besitzen. Sie definieren einen Namen, z.B. *gpu* und dann einen Wert für die Planung. Wenn Sie diesen Wert auf *NoSchedule* festlegen, kann der Kubernetes-Scheduler keine Pods auf dem Knoten planen, wenn der Pod nicht die entsprechende Toleranz definiert.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Wenn für Knoten ein Taint angewendet ist, definieren Sie in den Podspezifikationen eine Toleranz, die eine Planung auf den Knoten ermöglicht. Im folgenden Beispiel werden `sku: gpu` und `effect: NoSchedule` so definiert, dass der im vorherigen Schritt für den Knoten angewendete Taint toleriert wird.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Wenn dieser Pod bereitgestellt wird, z.B. mit `kubectl apply -f gpu-toleration.yaml`, kann Kubernetes den Pod erfolgreich auf den Knoten mit dem angewandten Taint planen. Mit dieser logischen Isolation können Sie den Zugriff auf Ressourcen innerhalb eines Clusters steuern.

Wenn Sie Taints anwenden, arbeiten Sie mit Ihren Anwendungsentwicklern und -besitzern zusammen, damit sie die erforderlichen Toleranzen in ihren Bereitstellungen definieren können.

Weitere Informationen zur Verwendung mehrerer Knotenpools in AKS finden Sie unter [Erstellen und Verwalten mehrerer Knotenpools für einen Cluster in AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Verhalten von Taints und Toleranzen in AKS

Wenn Sie ein Upgrade für einen Knotenpool in AKS durchführen, folgen Taints und Toleranzen einem festen Muster, da sie für neue Knoten angewendet werden:

- **Standardcluster, die VM-Skalierungsgruppen verwenden**
  - Sie können einen Knotenpool über die AKS-API [mit Taints versehen][taint-node-pool], damit neu aufskalierte Knoten die von der API angegebenen Knotentaints erhalten.
  - Stellen Sie sich vor, Sie haben einen Cluster mit zwei Knoten: *node1* und *node2*. Sie führen ein Upgrade für den Knotenpool durch.
  - Zwei zusätzliche Knoten werden erstellt, *node3* und *node4*, und die Taints werden entsprechend übergeben.
  - Die ursprünglichen *node1* und *node2* werden gelöscht.

- **Cluster ohne Unterstützung für VM-Skalierungsgruppen**
  - Stellen Sie sich noch einmal vor, Sie haben einen Cluster mit zwei Knoten: *node1* und *node2*. Wenn Sie ein Upgrade durchführen, wird ein zusätzlicher Knoten (*node3*) erstellt.
  - Die Taints aus *node1* gelten für *node3*. *node1* wird gelöscht.
  - Ein weiterer neuer Knoten wird erstellt (mit dem Namen *node1*, da der vorherige *node1* gelöscht wurde), und die Taints aus *node2* gelten nun für den neuen *node1*. *node2* wird gelöscht.
  - Zusammengefasst wird *node1* zu *node3*, und *node2* wird zu *node1*.

Wenn Sie einen Knotenpool in AKS skalieren, werden Taints und Toleranzen absichtlich nicht übertragen.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Steuern der Podplanung mit Knotenselektoren und Affinität

**Best Practice-Anleitung** – Steuern Sie die Planung von Pods auf Knoten mithilfe von Knotenelektoren, Knotenaffinität oder Pod-interner Affinität. Diese Einstellungen ermöglichen es dem Kubernetes-Scheduler, Workloads logisch zu isolieren, z.B. durch Hardware im Knoten.

Taints und Toleranzen werden verwendet, um Ressourcen mit einem harten Cut-off logisch zu isolieren – wenn der Pod den Taint eines Knotens nicht toleriert, wird er nicht auf dem Knoten geplant. Ein alternativer Ansatz ist die Verwendung von Knotenselektoren. Sie bezeichnen Knoten, z.B. um einen lokal angefügten SSD-Speicher oder eine große Menge an Speicher anzuzeigen, und definieren dann in der Podspezifikation einen Knotenselektor. Anschließend plant Kubernetes die Pods in einem übereinstimmenden Knoten. Im Gegensatz zu Toleranzen können Pods ohne einen übereinstimmenden Knotenselektor auf bezeichneten Knoten geplant werden. Dieses Verhalten ermöglicht es, ungenutzte Ressourcen auf den Knoten zu verbrauchen, gibt aber den Pods Priorität, die den passenden Knotenselektor definieren.

Sehen wir uns ein Beispiel für Knoten mit einer hohe Menge an Arbeitsspeicher an. Diese Knoten können Pods bevorzugen, für die viel Speicher erforderlich ist. Um sicherzustellen, dass die Ressourcen nicht ungenutzt bleiben, können sie auch andere Pods ausführen.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

Eine Podspezifikation fügt dann die Eigenschaft `nodeSelector` hinzu, um einen Knotenselektor zu definieren, der mit der auf einem Knoten festgelegten Bezeichnung übereinstimmt:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Wenn Sie diese Scheduleroptionen verwenden, arbeiten Sie mit Ihren Anwendungsentwicklern und -besitzern zusammen, damit sie ihre Podspezifikationen korrekt definieren können.

Weitere Informationen zur Verwendung von Knotenselektoren finden Sie unter [Zuweisen von Pods zu Knoten][k8s-node-selector].

### <a name="node-affinity"></a>Knotenaffinität

Ein Knotenselektor ist eine grundlegende Methode, um einem bestimmten Knoten Pods zuzuordnen. Mehr Flexibilität ist anhand von *Knotenaffinität* möglich. Mit Knotenaffinität definieren Sie, was passiert, wenn der Pod nicht mit einem Knoten abgeglichen werden kann. Sie können *anfordern*, dass der Kubernetes-Scheduler einem Pod mit einem bezeichneten Host entspricht. Oder Sie können *eine Übereinstimmung bevorzugen*, aber erlauben, dass der Pod auf einem anderen Host geplant wird, wenn keine Übereinstimmung verfügbar ist.

Im folgenden Beispiel wird die Knotenaffinität auf *RequiredDuringSchedulingIgnoredDuringExecution* festgelegt. Aufgrund dieser Affinität muss der Kubernetes-Scheduler einen Knoten mit einer übereinstimmenden Bezeichnung verwenden. Wenn kein Knoten verfügbar ist, muss der Pod warten, bis die Planung fortgesetzt wird. Damit der Pod auf einem anderen Knoten geplant werden kann, können Sie den Wert stattdessen auf *preferredDuringSchedulingIgnoreDuringExecution* festlegen:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

Der *IgnoredDuringExecution*-Teil der Einstellung gibt an, dass der Pod aus dem Knoten entfernt werden soll, wenn sich die Bezeichnung des Knotens ändert. Der Kubernetes-Scheduler verwendet nur die aktualisierten Knotenbezeichnungen für neue Pods, die geplant werden, nicht für bereits geplante Pods auf den Knoten.

Weitere Informationen finden Sie unter [Affinität und Antiaffinität][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Pod-interne Affinität und Antiaffinität

Ein letzter Ansatz für den Kubernetes-Scheduler zur logischen Isolation von Workloads ist die Verwendung von Pod-interner Affinität oder Antiaffinität. Diese Einstellungen definieren, dass Pods *nicht* auf einem Knoten mit einem übereinstimmenden Pod geplant werden sollten, oder dass sie geplant werden *sollten*. Standardmäßig versucht der Kubernetes-Scheduler mehrere Pods knotenübergreifend in einer Replikatgruppe zu planen. Sie können für dieses Verhalten spezifischer Regeln definieren.

Ein gutes Beispiel ist eine Webanwendung, die auch eine Azure Cache for Redis-Instanz verwendet. Sie können Regeln für die Pod-Antiaffinität verwenden, um anzufordern, dass der Kubernetes-Scheduler Replikate über Knoten verteilt. Sie können dann Affinitätsregeln verwenden, um sicherzustellen, dass jede Komponente der Webanwendung auf dem gleichen Host wie ein entsprechender Cache geplant ist. Die Verteilung der Pods auf die Knoten sieht beispielsweise wie folgt aus:

| **Knoten 1** | **Knoten 2** | **Knoten 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Dieses Beispiel ist eine komplexere Bereitstellung als die Verwendung von Knotenselektoren oder Knotenaffinität. Mit der Bereitstellung können Sie kontrollieren, wie Kubernetes Pods auf Knoten plant und Ressourcen logisch isolieren kann. Ein vollständiges Beispiel dieser Webanwendung mit Azure Cache for Redis-Beispiel finden Sie unter [Zusammenstellen von Pods auf demselben Knoten][k8s-pod-affinity].

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel konzentriert sich auf erweiterte Funktionen des Kubernetes-Schedulers. Weitere Informationen zu Clustervorgängen in AKS finden Sie in den folgenden Best Practices:

* [Best Practices für Clusterisolierung in Azure Kubernetes Service (AKS)][aks-best-practices-scheduler]
* [Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-scheduler]
* [Authentifizierung und Autorisierung][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
