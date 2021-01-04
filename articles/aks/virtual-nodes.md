---
title: Verwenden von virtuellen Knoten
titleSuffix: Azure Kubernetes Service
description: Übersicht über die Verwendung virtueller Knoten mit Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 2dd91e5c506f229d653fdf98bc0549c173cec793
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351839"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Erstellen und Konfigurieren eines AKS-Clusters zur Verwendung von virtuellen Knoten

Um Anwendungsworkloads in einem AKS-Cluster schnell zu skalieren, können Sie virtuelle Knoten verwenden. Mit virtuellen Knoten lassen sich Pods schnell bereitstellen, und Sie zahlen für die Ausführungsdauer nur auf Sekundenbasis. Sie müssen nicht warten, bis die Autoskalierung des Kubernetes-Clusters VM-Computeknoten bereitstellt, um die zusätzlichen Pods auszuführen. Virtuelle Knoten werden nur mit Linux-Pods und -Knoten unterstützt.

Das Add-On für virtuelle Knoten für AKS basiert auf dem Open-Source-Projekt [Virtual Kubelet][virtual-kubelet-repo].

In diesem Artikel finden Sie eine Übersicht über die regionale Verfügbarkeit und die Netzwerkanforderungen für die Verwendung virtueller Knoten sowie über die bekannten Einschränkungen.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

In allen Regionen, in denen ACI VNET-SKUs unterstützt, werden Bereitstellungen virtueller Knoten unterstützt.

Informationen zu den verfügbaren CPU- und Arbeitsspeicher-SKUs in den einzelnen Regionen finden Sie unter [Azure Container Instances: Ressourcenverfügbarkeit für Azure Container Instances in Azure-Regionen: Linux-Containergruppen](../container-instances/container-instances-region-availability.md#linux-container-groups).

## <a name="network-requirements"></a>Netzwerkanforderungen

Die virtuellen Knoten ermöglichen die Netzwerkkommunikation zwischen Pods, die in Azure Container Instances (ACI) und dem AKS-Cluster ausgeführt werden. Um diese Kommunikation bereitzustellen, wird ein virtuelles Subnetz erstellt, und delegierte Berechtigungen werden zugewiesen. Virtuelle Knoten funktionieren nur in AKS-Clustern, die mit *erweiterten* Netzwerkfunktionen (Azure CNI) erstellt wurden. Standardmäßig werden AKS-Cluster mit *grundlegenden* Netzwerkfunktionen (kubenet) erstellt.

Pods, die in Azure Container Instances (ACI) ausgeführt werden, benötigen Zugriff auf den AKS-API-Serverendpunkt, um das Netzwerk zu konfigurieren.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Die Funktionalität der virtuellen Knoten ist stark abhängig von den ACI-Features. Neben den [Kontingenten und Limits für Azure Container Instances](../container-instances/container-instances-quotas.md) werden die folgenden Szenarien für virtuelle Knoten noch nicht unterstützt:

* Verwenden des Dienstprinzipals zur Übertragung von ACR-Images mithilfe von Pull. Eine [Problemumgehung](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) ist mithilfe von [Kubernetes-Geheimnissen](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) möglich.
* [Einschränkungen für virtuelle Netzwerke](../container-instances/container-instances-vnet.md), darunter für VNET-Peering, für Kubernetes-Netzwerkrichtlinien und für ausgehenden Internetdatenverkehr im Zusammenhang mit Netzwerksicherheitsgruppen.
* Initialisierungscontainer.
* [Hostaliase](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/).
* [Argumente](../container-instances/container-instances-exec.md#restrictions) für „exec“ in ACI.
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) stellen keine Pods auf den virtuellen Knoten bereit.
* Virtuelle Knoten unterstützen die Planung von Linux-Pods. Sie können den Open-Source-ACI-Anbieter [Virtual Kubelet](https://github.com/virtual-kubelet/azure-aci) manuell installieren, um Windows Server-Container für ACI zu planen.
* Virtuelle Knoten erfordern AKS-Cluster mit einem Azure CNI-Netzwerk.
* Virtuelle Knoten mit privaten Clustern
* Verwenden von vom API-Server autorisierten IP-Adressbereichen für AKS
* Bei der Volumeeinbindung von Azure Files-Freigaben wird [Universell V1](../storage/common/storage-account-overview.md#types-of-storage-accounts) unterstützt. Befolgen Sie die Anweisungen zum Einbinden [eines Volumes mit einer Azure Files-Freigabe](azure-files-volume.md).

## <a name="next-steps"></a>Nächste Schritte

Konfigurieren virtueller Knoten für Ihre Cluster:

- [Erstellen von virtuellen Knoten mithilfe der Azure CLI](virtual-nodes-cli.md)
- [Erstellen von virtuellen Knoten mit dem Portal in Azure Kubernetes Service (AKS)](virtual-nodes-portal.md)

Virtuelle Knoten sind oft eine Komponente einer Skalierungslösung in AKS. Weitere Informationen zu Skalierungslösungen finden Sie in den folgenden Artikeln:

- [Verwenden der horizontalen automatischen Kubernetes-Podskalierung][aks-hpa]
- [Verwenden der automatischen Kubernetes-Clusterskalierung][aks-cluster-autoscaler]
- [Sehen Sie sich das Beispiel für die automatische Skalierung für virtuelle Knoten an][virtual-node-autoscale]
- [Erfahren Sie mehr über die Virtual Kubelet Open Source-Bibliothek][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
