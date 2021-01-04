---
title: Bewährte Methoden für die Clusterisolierung
titleSuffix: Azure Kubernetes Service
description: Lernen Sie die Best Practices für die Isolierung in Azure Kubernetes Service (AKS) für Clusteroperator kennen.
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011369"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Best Practices für Clusterisolierung in Azure Kubernetes Service (AKS)

Beim Verwalten von Clustern in Azure Kubernetes Service (AKS) müssen Sie oft Teams oder Workloads isolieren. AKS bietet Flexibilität bei der Art, in der Sie mehrinstanzenfähige Cluster ausführen und Ressourcen isolieren können. Um den Nutzen Ihrer Investition in Kubernetes zu maximieren, sollten Sie mit diesen Funktionen für Mehrinstanzenfähigkeit und Isolation vertraut sein und sie implementiert haben.

Dieser Best Practices-Artikel konzentriert sich auf die Isolierung für Clusteroperator. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Planen für mehrinstanzenfähige Cluster und Trennung von Ressourcen
> * Verwenden der logischen oder physischen Isolierung in Ihren AKS-Clustern

## <a name="design-clusters-for-multi-tenancy"></a>Entwerfen von Clustern für die Mehrinstanzenfähigkeit

Kubernetes bietet Features, mit denen Sie Teams und Workloads im selben Cluster logisch isolieren können. Das Ziel sollte sein, die geringste Anzahl von Berechtigungen bereitzustellen, beschränkt auf die Ressourcen, die jedes Team benötigt. Ein [Namespace][k8s-namespaces] in Kubernetes erstellt eine logische Isolationsgrenze. Zusätzliche Kubernetes-Funktionen und Überlegungen zu Isolierung und Mehrinstanzenfähigkeit umfassen die folgenden Bereiche:

* **Planung** umfasst die Verwendung der grundlegenden Funktionen, wie z.B. Ressourcenkontingente und Podunterbrechungsbudgets. Weitere Informationen zu diesen Features finden Sie unter [Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-scheduler].
  * Erweiterte Planerfeatures sind Makel und Toleranzen, Selektoren auf Knoten sowie Affinität zwischen Knoten und Pod bzw. Antiaffinität. Weitere Informationen zu diesen Features finden Sie unter [Best Practices für erweiterte Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-advanced-scheduler].
* **Netzwerk** umfasst die Verwendung von Netzwerkrichtlinien, um den ein- und ausgehenden Fluss des Datenverkehrs des Pods zu steuern.
* **Authentifizierung und Autorisierung** umfasst die Nutzung der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) und Azure Active Directory-Integration (AD), Podidentitäten und Geheimnisse in Azure Key Vault. Weitere Informationen zu diesen Features finden Sie unter [Best Practices für die Authentifizierung und Autorisierung in Azure Kubernetes Service (AKS)][aks-best-practices-identity].
* **Container** enthalten das Azure Policy-Add-On für AKS, um die Podsicherheit, die Verwendung von Podsicherheitskontexten und die Überprüfung von Images und Runtimes auf Sicherheitsrisiken zu erzwingen. Hierunter fällt auch App Armor oder Seccomp (Secure Computing, sicheres Computing) zum Einschränken des Zugriffs auf den Container auf den zugrunde liegenden Knoten.

## <a name="logically-isolate-clusters"></a>Logisches Isolieren von Clustern

**Best Practice-Anleitung**: Trennen Sie Teams und Projekte mithilfe einer logischen Isolierung. Versuchen Sie, die Anzahl der physischen AKS-Cluster zu minimieren, die Sie bereitstellen, um Teams oder Anwendungen zu isolieren.

Mit logischer Isolation kann ein einzelner AKS-Cluster für mehrere Workloads, Teams oder Umgebungen verwendet werden. Kubernetes-[Namespaces][k8s-namespaces] bilden die logische Isolationsgrenze für Workloads und Ressourcen.

![Logische Isolierung eines Kubernetes-Clusters in AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Die logische Trennung der Cluster bietet in der Regel eine höhere Poddichte als physisch isolierte Cluster. Weniger überschüssige Computekapazität befindet sich im Leerlauf im Cluster. In Kombination mit der automatischen Clusterskalierung in Kubernetes können Sie die Anzahl von Knoten gemäß der Anforderungen nach oben oder unten skalieren. Dieser Best Practice-Ansatz zur automatischen Skalierung ermöglicht Ihnen, nur die erforderliche Anzahl von Knoten auszuführen und die Kosten zu minimieren.

Kubernetes-Umgebungen, ob in AKS oder an anderer Stelle, sind nicht völlig sicher vor feindlicher Verwendung mit mehreren Mandanten. In einer Umgebung mit mehreren Mandanten arbeiten mehrere Mandanten in einer gemeinsamen, freigegebenen Infrastruktur. Hieraus resultiert, dass, wenn alle Mandanten nicht vertrauenswürdig sind, Sie eine zusätzliche Planung durchführen müssen, um zu verhindern, dass ein Mandant die Sicherheit und den Dienst eines anderen beeinträchtigt. Zusätzliche Sicherheitsfeatures wie eine *Podsicherheitsrichtlinie* und eine differenziertere rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Knoten erschweren Exploits. Für echte Sicherheit bei der Ausführung feindlicher Workloads mit mehreren Mandanten ist jedoch ein Hypervisor die einzige Sicherheitsstufe, der Sie vertrauen sollten. Die Sicherheitsdomäne für Kubernetes wird zum gesamten Cluster und nicht zu einem einzelnen Knoten. Für diese Art von feindlichen Workloads mit mehreren Mandanten sollten Sie physisch isolierte Cluster verwenden.

## <a name="physically-isolate-clusters"></a>Physisches Isolieren von Clustern

**Best Practice-Anleitung**: Minimieren Sie die Verwendung der physischen Isolierung für jedes einzelne Team oder jede einzelne Anwendungsbereitstellung. Verwenden Sie stattdessen die *logische* Isolierung, wie im vorherigen Abschnitt erläutert.

Ein gängiger Ansatz zum Isolieren von Clustern ist die Verwendung physisch getrennter AKS-Cluster. In diesem Isolationsmodell werden Teams oder Workloads ihre eigenen AKS-Cluster zugewiesen. Dieser Ansatz sieht oft wie die einfachste Möglichkeit zum Isolieren von Workloads oder Teams aus, bringt jedoch zusätzlichen Verwaltungs- und Finanzaufwand mit sich. Sie müssen jetzt diese vielen Cluster verwalten und individuell Zugriff ermöglichen und Berechtigungen zuweisen. Ihnen werden auch die einzelnen Knoten in Rechnung gestellt.

![Physische Isolierung einzelner Kubernetes-Cluster in AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Physisch separate Cluster verfügen in der Regel über eine geringe Poddichte. Da jedes Team oder jede Workload seinen/ihren eigenen AKS-Cluster hat, wird dem Cluster häufig ein Übermaß an Computeressourcen bereitgestellt. Häufig ist eine kleine Anzahl von Pods auf diesen Knoten eingeplant. Nicht verwendete Kapazität auf den Knoten kann nicht für Anwendungen oder Dienste in der Entwicklung anderer Teams verwendet werden. Diese überschüssigen Ressourcen tragen zu den zusätzlichen Kosten in physisch separaten Clustern bei.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel konzentriert sich auf Clusterisolierung. Weitere Informationen zu Clustervorgängen in AKS finden Sie in den folgenden Best Practices:

* [Best Practices für grundlegende Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-scheduler]
* [Best Practices für erweiterte Schedulerfunktionen in Azure Kubernetes Service (AKS)][aks-best-practices-advanced-scheduler]
* [Authentifizierung und Autorisierung][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
