---
title: Konzepte – Skalieren von Anwendungen in Azure Kubernetes Service (AKS)
description: Informationen zum Skalieren in Azure Kubernetes Service (AKS) einschließlich der horizontalen automatischen Podskalierung, der automatischen Clusterskalierung und des Azure Container Instances-Connectors.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: b72ed7cefc6a16eb484e1337dbd64e5f069a2201
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686037"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Skalierungsoptionen für Anwendungen in Azure Kubernetes Service (AKS)

Wenn Sie Anwendungen in Azure Kubernetes Service (AKS) ausführen, müssen Sie vielleicht die Anzahl der Computeressourcen herauf- oder herabsetzen. Wenn sich die Anzahl der benötigten Anwendungsinstanzen ändert, muss die Anzahl der zugrunde liegenden Kubernetes-Knoten möglicherweise auch geändert werden. Vielleicht müssen Sie auch schnell eine große Anzahl zusätzlicher Anwendungsinstanzen bereitstellen.

In diesem Artikel werden die wichtigsten Konzepte vorgestellt, mit denen Sie Anwendungen in AKS skalieren können:

- [Manuelles Skalieren](#manually-scale-pods-or-nodes)
- [Horizontale automatische Podskalierung (Horizontal Pod Autoscaler, HPA)](#horizontal-pod-autoscaler)
- [Automatische Clusterskalierung ](#cluster-autoscaler)
- [Integration von Azure Container Instances (ACI) in AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Manuelles Skalieren von Pods oder Knoten

Sie können Replikate (Pods) und Knoten manuell skalieren, um zu testen, wie Ihre Anwendung auf eine Änderung in verfügbaren Ressourcen und Status reagiert. Durch manuelles Skalieren von Ressourcen können Sie auch eine festgelegte Anzahl zu verwendender Ressourcen definieren, wie z.B. die Anzahl der Knoten, um feste Kosten einzuhalten. Zum manuellen Skalieren definieren Sie die Replikat- oder Knotenanzahl. Die Kubernetes-API plant dann das Erstellen zusätzlicher Pods oder Entfernen von Knoten auf Basis dieser Replikat- oder Knotenanzahl.

Beim horizontalen Herunterskalieren von Knoten ruft die Kubernetes-API die relevante Azure-Compute-API auf, die an den von Ihrem Cluster verwendeten Computetyp gebunden ist. Beispielsweise wird für Cluster, die auf VM Scale Sets basieren, die Logik für die Auswahl der zu entfernenden Knoten durch die VM Scale Sets-API bestimmt. Weitere Informationen dazu, wie Knoten beim horizontalen Herunterskalieren für die Entfernung ausgewählt werden, finden Sie in den [Häufig gestellte Fragen zu VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Informationen zu ersten Schritten mit der manuellen Skalierung von Pods und Knoten finden Sie unter [Skalieren von Anwendungen in AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Horizontale automatische Podskalierung

Kubernetes verwendet die horizontale automatische Podskalierung (HPA) zum Überwachen des Ressourcenbedarfs und automatischen Skalieren der Anzahl der Replikate. Standardmäßig überprüft die horizontale automatische Podskalierung die Metriken-API alle 30 Sekunden auf erforderliche Änderungen der Replikatanzahl. Wenn Änderungen erforderlich sind, wird die Anzahl von Replikaten entsprechend herauf- oder herabgesetzt. Die horizontale automatische Podskalierung funktioniert mit AKS-Clustern, die den Metrikserver für Kubernetes 1.8 und höher bereitgestellt haben.

![Automatische horizontale Kubernetes-Podskalierung](media/concepts-scale/horizontal-pod-autoscaling.png)

Wenn Sie die horizontale automatische Podskalierung für eine bestimmte Bereitstellung konfigurieren, definieren Sie die minimale und maximale Anzahl an Replikaten, die ausgeführt werden kann. Außerdem definieren Sie die Metrik zum Überwachen und als Grundlage aller Skalierungsentscheidungen, wie z.B. zur CPU-Auslastung.

Informationen zu den ersten Schritten mit der horizontalen automatischen Podskalierung in AKS finden Sie unter [Automatische Podskalierung in AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Abkühlung der Skalierung von Ereignissen

Da die horizontale automatische Podskalierung die Metriken-API alle 30 Sekunden überprüft, werden vorherige Skalierungsereignisse möglicherweise nicht erfolgreich abgeschlossen, bevor eine andere Überprüfung erfolgt. Dieses Verhalten kann dazu führen, dass die horizontale automatische Podskalierung die Anzahl der Replikate ändert, bevor das vorherige Skalierungsereignis die Anwendungsworkload empfangen und die Ressourcenanforderungen entsprechend anpassen konnte.

Um Raceereignisse zu minimieren, wird ein Verzögerungswert festgelegt. Dieser Wert definiert, wie lange die horizontale automatische Podskalierung nach einem Skalierungsereignis warten muss, bevor ein anderes Skalierungsereignis ausgelöst werden kann. Dieses Verhalten ermöglicht, dass die neue Replikatanzahl wirksam werden und die Metriken-API die verteilte Workload widerspiegeln kann. Es gibt [keine Verzögerung für Hochskalierungsereignisse ab Kubernetes 1.12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), jedoch ist die Verzögerung bei Herunterskalierungsereignissen auf 5 Minuten voreingestellt.

Derzeit können diese Abkühlungsstandardwerte nicht optimiert werden.

## <a name="cluster-autoscaler"></a>Automatische Clusterskalierung

Um auf veränderte Podanforderungen zu reagieren, enthält Kubernetes eine automatische Clusterskalierung, die die Anzahl von Knoten basierend auf den angeforderten Computeressourcen im Knotenpool anpasst. Standardmäßig überprüft die automatische Clusterskalierung den Metrik-API-Server alle 10 Sekunden auf erforderliche Änderungen der Knotenanzahl. Wenn die automatische Clusterskalierung ermittelt, dass eine Änderung erforderlich ist, wird die Anzahl der Knoten im AKS-Cluster entsprechend herauf- oder herabgesetzt. Die automatische Clusterskalierung funktioniert mit Kubernetes RBAC-fähigen AKS-Clustern, die Kubernetes 1.10.x oder höher ausführen.

![Automatische Kubernetes-Clusterskalierung](media/concepts-scale/cluster-autoscaler.png)

Die automatische Clusterskalierung wird normalerweise zusammen mit der horizontalen automatischen Podskalierung verwendet. In der Kombination setzt die horizontale automatische Podskalierung die Anzahl von Pods nach Bedarf der Anwendung herauf oder herab, und die automatische Clusterskalierung passt die Anzahl von Knoten nach Bedarf zur entsprechenden Ausführung dieser zusätzlichen Pods an.

Informationen zu den ersten Schritten mit der automatischen Clusterskalierung in AKS finden Sie unter [Automatische Clusterskalierung in AKS][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Aufskalieren von Ereignissen

Wenn ein Knoten nicht über ausreichende Computeressourcen zum Ausführen eines angeforderten Pods verfügt, kann dieser Pod den Planungsprozess nicht erfüllen. Der Pod kann erst dann gestartet werden, wenn innerhalb des Knotenpools zusätzliche Computeressourcen verfügbar sind.

Wenn die automatische Clusterskalierung Pods erkennt, die aufgrund von Einschränkungen von Knotenpoolressourcen nicht eingeplant werden können, wird die Anzahl von Knoten innerhalb des Knotenpools heraufgesetzt, um die zusätzlichen Computeressourcen bereitzustellen. Wenn diese zusätzlichen Knoten erfolgreich bereitgestellt werden und für die Verwendung innerhalb des Knotenpools verfügbar sind, werden die Pods zur Ausführung darauf eingeplant.

Wenn Ihre Anwendung schnell skalieren muss, bleiben einige Pods möglicherweise in einem Zustand des Wartens darauf, eingeplant zu werden, bis die zusätzlichen, von der automatischen Clusterskalierung bereitgestellten Knoten die eingeplanten Pods annehmen können. Für Anwendungen mit hohen Burstanforderungen können Sie mit virtuellen Knoten und Azure Container Instances skalieren.

### <a name="scale-in-events"></a>Abskalieren von Ereignissen

Die automatische Clusterskalierung überwacht auch den Status der Podplanung für Knoten, die nicht vor kurzem neue Planungsanforderungen empfangen haben. Dieses Szenario zeigt, dass der Knotenpool mehr Computeressourcen als erforderlich besitzt, und dass die Anzahl der Knoten verringert werden kann.

Ein Knoten, der 10 Minuten lang einen Schwellenwert nicht einhält, demzufolge er nicht mehr benötigt wird, wird zum Löschen eingeplant. Wenn diese Situation eintritt, werden Pods zur Ausführung auf anderen Knoten innerhalb des Knotenpools eingeplant, und die automatische Clusterskalierung setzt die Anzahl der Knoten herab.

Bei Ihren Anwendungen kann eine Unterbrechung auftreten, da Pods auf anderen Knoten eingeplant werden, wenn die automatische Clusterskalierung die Anzahl der Knoten herabsetzt. Um Unterbrechungen zu minimieren, vermeiden Sie Anwendungen, die eine einzelne Podinstanz verwenden.

## <a name="burst-to-azure-container-instances"></a>Burst zu Azure Container Instances

Um Ihren AKS-Cluster schnell zu skalieren, können Sie ihn in Azure Container Instances (ACI) integrieren. Kubernetes verfügt über integrierte Komponenten zum Skalieren der Replikat- und Knotenanzahl. Wenn Ihre Anwendung jedoch schnell skalieren muss, kann die horizontale automatische Podskalierung weitere Pods einplanen, die durch die vorhandenen Computeressourcen im Knotenpool bereitgestellt werden können. Sofern konfiguriert, würde dieses Szenario dann die automatische Clusterskalierung zum Bereitstellen zusätzlicher Knoten im Knotenpool auslösen, aber es kann einige Minuten dauern, bis diese Knoten erfolgreich bereitgestellt sind, sodass der Kubernetes-Planer Pods auf ihnen ausführen kann.

![Kubernetes-Burstskalierung in ACI](media/concepts-scale/burst-scaling.png)

Mit ACI können Sie schnell Containerinstanzen ohne zusätzlichen Infrastrukturaufwand bereitstellen. Beim Herstellen der Verbindung mit AKS wird ACI eine sichere logische Erweiterung Ihres AKS-Clusters. Die Komponente zu [virtuellen Knoten][virtual-nodes-cli] (virtual nodes), die unter [Virtual Kubelet][virtual-kubelet] zu finden ist, wird in Ihrem AKS-Cluster installiert, der ACI als virtuellen Kubernetes-Knoten darstellt. Kubernetes kann dann Pods einplanen, die als ACI-Instanzen über virtuelle Knoten ausgeführt werden, nicht direkt in Ihrem AKS-Cluster als Pods auf VM-Knoten.

Die Anwendung erfordert keine Änderung, um virtuelle Knoten zu verwenden. Bereitstellungen können AKS und ACI übergreifend und ohne Verzögerung skalieren, da die automatische Clusterskalierung neue Knoten im AKS-Cluster bereitstellt.

Virtuelle Knoten werden in einem weiteren Subnetz im gleichen virtuellen Netzwerk wie in Ihrem AKS-Cluster bereitgestellt. Diese Konfiguration des virtuellen Netzwerks ermöglicht sicheren Datenverkehr zwischen ACI und AKS. Wie ein AKS-Cluster ist eine ACI-Instanz eine sichere, logische Computeressource, die von anderen Benutzern isoliert ist.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg in das Skalieren von Anwendungen finden Sie in dem [Schnellstart zum Erstellen eines AKS-Clusters mit der Azure CLI][aks-quickstart]. Anschließend können Sie beginnen, Anwendungen manuell oder automatisch im AKS-Cluster zu skalieren:

- Manuelles Skalieren von [Pods][aks-manually-scale-pods] oder [Knoten][aks-manually-scale-nodes]
- Verwenden der [horizontalen automatischen Podskalierung][aks-hpa]
- Verwenden der [automatischen Clusterskalierung][aks-cluster-autoscaler]

Weitere Informationen zu den wesentlichen Konzepten von Kubernetes und AKS finden Sie in den folgenden Artikeln:

- [Kubernetes/AKS: Cluster und Workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS: Zugriff und Identität][aks-concepts-identity]
- [Kubernetes/AKS: Sicherheit][aks-concepts-security]
- [Kubernetes/AKS: virtuelle Netzwerke][aks-concepts-network]
- [Kubernetes/AKS: Speicher][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
