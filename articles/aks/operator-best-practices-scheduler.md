---
title: Best Practices für Operator – Grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)
description: Lernen Sie die Best Practices des Clusteroperators für die Verwendung grundlegender Schedulerfunktionen wie Ressourcenkontingente und Budgets für die Unterbrechung von Pods im Azure Kubernetes Service (AKS) kennen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: f6e370442c9c359a38025762fb90269119ec0ea6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074122"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)

Beim Verwalten von Clustern in Azure Kubernetes Service (AKS) müssen Sie oft Teams oder Workloads isolieren. Der Kubernetes-Scheduler bietet Funktionen, mit denen Sie die Verteilung von Rechenressourcen steuern oder die Auswirkungen von Wartungsereignissen begrenzen können.

Dieser Artikel zu Best Practices konzentriert sich auf grundlegende Kubernetes-Planungsfunktionen für Clusteroperatoren. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Verwenden von Ressourcenkontingenten, um eine feste Menge an Ressourcen für Teams oder Workloads bereitzustellen
> * Beschränken der Auswirkungen der geplanten Wartung mithilfe von Budgets für die Unterbrechung von Pods
> * Überprüfen fehlender Podressourcenanforderungen und -grenzwerte mithilfe des `kube-advisor`-Tools

## <a name="enforce-resource-quotas"></a>Durchsetzen von Ressourcenkontingenten

**Best Practice-Anleitung** – Planen Sie Ressourcenkontingenten auf Namespaceebene und wenden Sie diese an. Wenn Pods keine Ressourcenanforderungen und -grenzwerte definieren, lehnen Sie die Bereitstellung ab. Überwachen Sie die Ressourcennutzung, und passen Sie Kontingente nach Bedarf an.

Ressourcenanforderungen und -grenzwerte werden in der Podspezifikation hinterlegt. Diese Grenzwerte werden vom Kubernetes-Scheduler zum Zeitpunkt der Bereitstellung verwendet, um einen verfügbaren Knoten im Cluster zu finden. Diese Grenzwerte und Anforderungen gelten jeweils für den einzelnen Pod. Weitere Informationen zum Definieren dieser Werte finden Sie unter [Definieren von Ressourcenanforderungen und -grenzwerten][resource-limits].

Um eine Möglichkeit zu bieten, Ressourcen innerhalb eines Entwicklungsteams oder Projekts zu reservieren und zu begrenzen, sollten Sie *Ressourcenkontingente* verwenden. Diese Kontingente werden in einem Namespace definiert und können verwendet werden, um Kontingente auf der folgenden Grundlage festzulegen:

* **Computeressourcen**, z.B. CPU und Arbeitsspeicher oder GPUs.
* **Speicherressourcen** umfasst die Gesamtanzahl der Volumes oder Speicherplatz für eine angegebene Speicherklasse.
* **Objektanzahl**, z.B die maximale Anzahl von Geheimnissen, Diensten oder der Aufträge, die erstellt werden können.

Kubernetes weist nicht zu viele Ressourcen zu. Sobald die kumulierte Summe der Ressourcenanforderungen oder -grenzwerte das zugewiesene Kontingent überschreitet, sind keine weiteren Bereitstellungen möglich.

Wenn Sie Ressourcenkontingente definieren, müssen alle im Namespace erstellten Pods in ihren Podspezifikationen Grenzwerte oder Anforderungen enthalten. Wenn diese Werte nicht angeben werden, können Sie die Bereitstellung ablehnen. Stattdessen können Sie [Standardanforderungen und Grenzwerte für einen Namespace definieren][configure-default-quotas].

Das folgende Beispiel eines YAML-Manifests mit dem Namen *dev-app-team-quotas.yaml* legt einen festen Grenzwert von insgesamt *10* CPUs, *20Gi* Arbeitsspeicher und *10* Pods fest:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Dieses Ressourcenkontingent kann durch das Angeben des Namespaces angewendet werden, z.B. *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Arbeiten Sie mit Ihren Anwendungsentwicklern und -besitzern zusammen, um deren Anforderungen zu verstehen und die entsprechenden Ressourcenkontingente anzuwenden.

Weitere Informationen zu verfügbaren Ressourcenobjekten, Bereichen und Prioritäten finden Sie unter [Ressourcenkontingente in Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planen der Verfügbarkeit mit Budgets für die Unterbrechung von Pods

**Best Practice-Anleitung** – Um die Verfügbarkeit von Anwendungen aufrechtzuerhalten, definieren Sie Budgets für die Unterbrechung von Pods (Pod Disruption Budgets, PDBs), um sicherzustellen, dass eine Mindestanzahl von Pods im Cluster verfügbar ist.

Es gibt zwei unterbrechende Ereignisse, durch die Pods entfernt werden:

* *Unfreiwillige Unterbrechungen* sind Ereignisse, die außerhalb der typischen Kontrolle des Clusteroperators oder Anwendungsbesitzers liegen.
  * Zu diesen unfreiwilligen Unterbrechungen gehören ein Hardwareausfall auf dem physischen Computer, eine Kernelstörung oder das Löschen einer Knoten-VM.
* *Freiwillige Unterbrechungen* sind Ereignisse, die vom Clusteroperator oder Anwendungsbesitzer angefordert werden.
  * Zu diesen freiwilligen Unterbrechungen gehören Clusterupgrades, eine aktualisierte Bereitstellungsvorlage oder das versehentliche Löschen eines Pods.

Die Auswirkungen der unfreiwilligen Unterbrechungen können durch die Verwendung mehrerer Replikate Ihrer Pods in einer Bereitstellung verringert werden. Das Ausführen mehrerer Knoten im AKS-Cluster verringert ebenfalls die Auswirkungen dieser unfreiwilligen Unterbrechungen. Für freiwillige Unterbrechungen stellt Kubernetes *Budgets für die Unterbrechung von Pods* zur Verfügung, mit denen der Clusteroperator eine minimale verfügbare oder maximal nicht verfügbare Ressourcenanzahl definieren kann. Anhand dieser Budgets können Sie planen, wie Bereitstellungen oder Replikatgruppen bei einem freiwilligen Unterbrechungsereignis reagieren.

Wenn ein Cluster aktualisiert oder eine Bereitstellungsvorlage aktualisiert werden soll, stellt der Kubernetes-Scheduler sicher, dass zusätzliche Pods auf anderen Knoten geplant werden, bevor die freiwilligen Unterbrechungsereignisse fortgesetzt werden können. Der Scheduler wartet, bis die definierte Anzahl von Pods auf anderen Knoten im Cluster erfolgreich geplant ist, bevor ein Knoten neu gestartet wird.

Schauen wir uns ein Beispiel für eine Replikatgruppe mit fünf Pods an, auf denen NGINX ausgeführt wird. Den Pods in der Replikatgruppe ist die Bezeichnung `app: nginx-frontend` zugewiesen. Während eines freiwilligen Unterbrechungsereignisses, wie z.B. eines Clusterupgrades, möchten Sie sicherstellen, dass mindestens drei Pods weiterhin laufen. Das folgende YAML-Manifest für ein *PodDisruptionBudget*-Objekt definiert diese Anforderungen:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

Sie können auch einen Prozentsatz definieren, wie z.B. *60 %* , mit dem Sie automatisch die Erhöhung der Anzahl der Pods durch die Replikatgruppe kompensieren können.

Sie können eine maximale Anzahl von nicht verfügbaren Instanzen in einer Replikatgruppe definieren. Darüber hinaus kann ein Prozentsatz für die maximale Anzahl der nicht verfügbaren Pods definiert werden. Das folgende YAML-Manifest für das Budget für die Unterbrechung von Pods definiert, dass nicht mehr als zwei Pods in der Replikatgruppe nicht verfügbar sein dürfen:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Sobald Sie Ihr Budget definiert haben, können Sie es genauso wie bei jedem anderen Kubernetes-Objekt in Ihrem AKS-Cluster erstellen:

```console
kubectl apply -f nginx-pdb.yaml
```

Arbeiten Sie mit Ihren Anwendungsentwicklern und -besitzern zusammen, um deren Anforderungen zu verstehen und das entsprechende Budget für die Unterbrechung von Pods anzuwenden.

Weitere Informationen zur Verwendung von Budgets für die Unterbrechung von Pods finden Sie unter [Spezifizieren von Budgets für die Unterbrechung für Ihre Anwendung][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Regelmäßiges Überprüfen auf Clusterprobleme mit dem Kube-Advisor

**Best Practices-Anleitung:** Führen Sie regelmäßig die neueste Version des Open-Source-Tools `kube-advisor` aus, um Probleme in Ihrem Cluster zu erkennen. Wenn Sie Ressourcenkontingente auf einen bestehenden AKS-Cluster anwenden, führen Sie zuerst `kube-advisor` aus, um Pods zu finden, die keine Ressourcenanforderungen und -grenzwerte definiert haben.

Das Tool [kube-advisor][kube-advisor] ist ein verwandtes Open-Source-Projekt für AKS, das einen Kubernetes-Cluster scannt und gefundene Probleme meldet. Eine nützliche Überprüfung ist die Identifizierung von Pods, bei denen keine Ressourcenanforderungen und -grenzwerte angegeben sind.

Das kube-advisor-Tool kann Berichte zur Ressourcenanforderung und zu Grenzwerten erstellen, die in PodSpecs für Windows- und Linux-Anwendungen fehlen, das kube-advisor-Tool selbst muss jedoch auf einem Linux-Pod geplant werden. Sie können einen Pod mit einem [Knoten-Selektor][ k8s-node-selector] in der Konfiguration des Pods so planen, dass er auf einem Knotenpool mit einem bestimmten Betriebssystem ausgeführt wird.

In einem AKS-Cluster, der mehrere Entwicklungsteams und Anwendungen hostet, kann es schwierig sein, Pods zu verfolgen, bei denen diese Ressourcenanforderungen und -grenzwerte nicht festgelegt wurden. Als Best Practice sollten Sie `kube-advisor` regelmäßig auf Ihren AKS-Clustern ausführen, insbesondere wenn Sie Namespaces keine Ressourcenkontingente zuweisen.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel konzentriert sich auf grundlegende Funktionen des Kubernetes-Schedulers. Weitere Informationen zu Clustervorgängen in AKS finden Sie die folgenden Best Practices:

* [Mehrinstanzenfähigkeit und Clusterisolation][aks-best-practices-cluster-isolation]
* [Erweiterte Funktionen des Kubernetes-Schedulers][aks-best-practices-advanced-scheduler]
* [Authentifizierung und Autorisierung][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
