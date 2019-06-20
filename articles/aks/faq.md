---
title: Häufig gestellte Fragen zu Azure Kubernetes Service (AKS)
description: Finden Sie Antworten auf einige der häufig gestellten Fragen zu Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 1cc03cbcffc5253e8b357b6702cd21c45740ff81
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514489"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Häufig gestellte Fragen zu Azure Kubernetes Service (AKS)

Dieser Artikel behandelt häufig gestellte Fragen zu Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>In welchen Azure-Regionen wird AKS derzeit zur Verfügung gestellt?

Eine vollständige Liste der verfügbaren Regionen finden Sie unter [AKS-Regionen und Verfügbarkeit][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Unterstützt AKS die automatische Skalierung von Knoten?

Ja, die automatische Skalierung ist ab Kubernetes 1.10 über den [Kubernetes Autoscaler][auto-scaler] verfügbar. Informationen zum manuellen Konfigurieren und Verwenden der automatischen Skalierungsfunktion für Cluster finden Sie unter [Cluster Autoscaler in AKS][aks-cluster-autoscale].

Sie können auch die integrierte automatischen Skalierungsfunktion für Cluster verwenden (derzeit in der Vorschau in AKS), um die Skalierung von Knoten zu verwalten. Weitere Informationen hierzu finden Sie unter [Automatisches Skalieren eines Clusters zur Erfüllung von Anwendungsanforderungen in Azure Kubernetes Service (AKS)][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-rbac"></a>Unterstützt AKS Kubernetes RBAC?

Ja, die rollenbasierte Zugriffssteuerung von Kubernetes (Role-Based Access Control, RBAC) ist standardmäßig aktiviert, wenn Cluster mit der Azure-Befehlszeilenschnittstelle erstellt werden. Sie können RBAC für Cluster aktivieren, die über das Azure-Portal oder mithilfe von Vorlagen erstellt wurden.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Kann ich AKS in meinem vorhandenen virtuellen Netzwerk bereitstellen?

Ja, Sie können einen AKS-Cluster in einem vorhandenen virtuellen Netzwerk mithilfe des [erweiterten Netzwerkfeatures][aks-advanced-networking] bereitstellen.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kann ich den Kubernetes-API-Server nur innerhalb meines virtuellen Netzwerks zugänglich machen?

Derzeit leider nicht. Der Kubernetes-API-Server wird als öffentlicher vollqualifizierter Domänenname (FQDN) verfügbar gemacht. Den Zugriff auf Ihren Cluster können Sie mit [Kubernetes RBAC und Azure Active Directory (Azure AD)][aks-rbac-aad] steuern.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Werden Sicherheitsupdates auf AKS-Agent-Knoten angewendet?

Azure wendet automatisch Sicherheitspatches auf die Linux-Knoten in Ihrem Cluster an, und zwar gemäß einem nächtlichen Zeitplan. Sie sind jedoch dafür verantwortlich, dass diese Linux-Knoten bei Bedarf neu gestartet werden. Sie haben mehrere Möglichkeiten für Neustarts von Knoten:

- Manuell über das Azure-Portal oder die Azure-CLI.
- Durch ein Upgrade des AKS-Clusters. Durch Clusterupgrades [werden Knoten automatisch abgesperrt und ausgeglichen][cordon-drain] und anschließend neue Knoten mit dem neuesten Ubuntu-Image und einer neuen Patchversion oder einer Kubernetes-Nebenversion online geschaltet. Weitere Informationen finden Sie unter [Aktualisieren eines AKS-Clusters][aks-upgrade].
- Mit [Kured](https://github.com/weaveworks/kured), einem Open Source-Neustartdaemon für Kubernetes. Kured wird als [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) ausgeführt und überwacht jeden Knoten auf das Vorhandensein einer Datei, die angibt, dass ein Neustart erforderlich ist. Neustarts des Betriebssystems werden clusterübergreifend verwaltet, wobei derselbe [Vorgang des Absperrens und Ausgleichens][cordon-drain] wie bei einem Clusterupgrade angewandt wird.

Weitere Informationen zur Verwendung von kured finden Sie unter [Apply security and kernel updates to nodes in Azure Kubernetes Service (AKS)][node-updates-kured] (Anwenden von Sicherheits- und Kernelupdates auf Knoten in Azure Kubernetes Service (AKS)).

### <a name="windows-server-nodes"></a>Windows Server-Knoten

Für Windows Server-Knoten (derzeit in der Vorschauversion in AKS) werden die neuesten Updates von Windows Update nicht automatisch ausgeführt und angewendet. Sie sollten in regelmäßigen Abständen ein Upgrade für die Windows Server-Knotenpools in Ihrem AKS-Cluster durchführen, passend zum Windows Update-Freigabezyklus und Ihrem eigenen Validierungsprozess. Während dieses Upgrades werden Knoten erstellt, die das neueste Windows Server-Image und die neuesten Windows Server-Patches ausführen und die älteren Knoten entfernen. Weitere Informationen zu diesem Prozess finden Sie unter [Upgrade a node pool in AKS (Durchführen eines Upgrades für einen Knotenpool in AKS)][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Warum werden zwei Ressourcengruppen mit AKS erstellt?

Jede AKS-Bereitstellung umfasst zwei Ressourcengruppen:

1. Die erste Ressourcengruppe wird von Ihnen erstellt. Diese Gruppe enthält nur die Kubernetes-Dienstressource. Der AKS-Ressourcenanbieter erstellt während der Bereitstellung automatisch die zweite Ressourcengruppe. *MC_myResourceGroup_myAKSCluster_eastus* ist ein Beispiel für die zweite Ressourcengruppe. Informationen dazu, wie Sie den Namen dieser zweiten Ressourcengruppe angeben, finden Sie im nächsten Abschnitt.
1. Die zweite Ressourcengruppe, z. B. *MC_myResourceGroup_myAKSCluster_eastus*, enthält alle Infrastrukturressourcen für den Cluster. Diese Ressourcen umfassen die virtuellen Computer des Kubernetes-Knotens, virtuelle Netzwerke und Speicher. Zweck dieser Ressourcengruppe ist die Vereinfachung der Ressourcenbereinigung.

Wenn Sie Ressourcen erstellen, die mit Ihrem AKS-Cluster verwendet werden sollen (z. B. Speicherkonten oder reservierte öffentliche IP-Adressen), platzieren Sie diese in der automatisch generierten Ressourcengruppe.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Kann ich meinen eigenen Namen für die AKS-Infrastrukturressourcengruppe angeben?

Ja. Standardmäßig erstellt der AKS-Ressourcenanbieter während der Bereitstellung automatisch eine sekundäre Ressourcengruppe, z. B. *MC_myResourceGroup_myAKSCluster_eastus*. Sie können Ihren eigenen Namen für diese verwaltete (*MC_* -)Clusterressourcengruppe angeben, um die Unternehmensrichtlinie zu erfüllen.

Installieren Sie die [aks-preview][aks-preview-cli]-Erweiterungsversion *0.3.2* oder höher für die Azure CLI, um Ihren eigenen Ressourcengruppennamen anzugeben. Verwenden Sie bei der Erstellung eines AKS-Clusters mit dem Befehl [az aks create][az-aks-create] den Parameter *--node-resource-group*, und geben Sie einen Namen für die Ressourcengruppe an. Wenn Sie [eine Azure Resource Manager-Vorlage verwenden][aks-rm-template], um einen AKS-Cluster bereitzustellen, können Sie die *nodeResourceGroup*-Eigenschaft verwenden, um den Namen der Ressourcengruppe zu definieren.

* Die sekundäre Ressourcengruppe wird automatisch vom Azure-Ressourcenanbieter in Ihrem eigenen Abonnement erstellt.
* Sie können nur einen benutzerdefinierten Namen für die Ressourcengruppe angeben, wenn Sie den Cluster erstellen.

Beachten Sie beim Verwenden der *MC_* -Ressourcengruppe, dass Folgendes nicht möglich ist:

* Angeben einer vorhandenen Ressourcengruppe für die *MC_* -Gruppe
* Angeben eines anderen Abonnements für die *MC_* -Ressourcengruppe
* Ändern des Namens der *MC_* -Ressourcengruppe, nachdem der Cluster erstellt wurde
* Angeben von Namen für die verwalteten Ressourcen innerhalb der *MC_* -Ressourcengruppe
* Ändern oder Löschen von Tags von verwalteten Ressourcen innerhalb der *MC_* -Ressourcengruppe (Weitere Informationen finden Sie im nächsten Abschnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Kann ich Tags und andere Eigenschaften der AKS-Ressourcen in der „MC_“-Ressourcengruppe ändern?

Wenn Sie die in Azure erstellten Tags und anderen Ressourceneigenschaften in der *MC_* -Ressourcengruppe ändern oder löschen, kann dies zu unerwarteten Ergebnissen wie Skalierungs- und Aktualisierungsfehlern führen. In AKS können Sie benutzerdefinierte Tags erstellen und ändern. Sie können benutzerdefinierte Tags erstellen oder ändern, um beispielsweise eine Geschäftseinheit oder eine Kostenstelle zuzuweisen. Durch Ändern der Ressourcen unter der *MC_* -Gruppe im AKS-Cluster wird das Servicelevelziel (SLO) unterbrochen. Weitere Informationen finden Sie unter [Bietet AKS eine Vereinbarung zum Servicelevel?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Welche Kubernetes-Zugangssteuerungen werden von AKS unterstützt? Können Zulassungscontroller hinzugefügt oder entfernt werden?

AKS unterstützt die folgenden [Zugangssteuerungen][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Derzeit können Sie die Liste der Zugriffssteuerungen in AKS nicht ändern.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Ist Azure Key Vault in AKS integriert?

AKS ist derzeit nicht nativ mit Azure Key Vault integriert. Das Projekt [Azure Key Vault FlexVolume für Kubernetes][keyvault-flexvolume] ermöglicht jedoch die direkte Integration von Kubernetes-Pods in Key Vault-Geheimnisse.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kann ich Windows Server-Container unter AKS ausführen?

Ja, Windows Server-Container sind in der Vorschauversion verfügbar. Erstellen Sie einen Knotenpool, der Windows Server als Gastbetriebssystem ausführt, um Windows Server-Container in AKS auszuführen. Windows Server-Container können nur Windows Server 2019 verwenden. Informationen zu den ersten Schritten finden Sie unter [Erstellen eines AKS-Clusters mit einem Windows Server-Knotenpool][aks-windows-cli].

Die Windows Server-Unterstützung für Knotenpools umfasst einige Einschränkungen, die Teil des Upstreamprojekts „Windows Server in Kubernetes“ sind. Weitere Informationen zu diesen Einschränkungen finden Sie unter [Windows Server containers in AKS limitations (Einschränkungen für Windows Server-Container in AKS)][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Bietet AKS eine Vereinbarung zum Servicelevel?

In einer Vereinbarung zum Servicelevel (Service Level Agreement, SLA) sichert der Anbieter zu, dem Kunden die Kosten des Diensts zu erstatten, falls das veröffentliche Servicelevel nicht erfüllt wird. Da AKS kostenlos ist, können keine Kosten erstattet werden. Daher verfügt AKS über keine formale SLA. In AKS wird jedoch angestrebt, für den Kubernetes-API-Server eine Verfügbarkeit von mindestens 99,5 % zu bieten.

## <a name="why-cant-i-set-maxpods-below-30"></a>Warum kann ich für maxPods keinen Wert unter 30 festlegen?

In AKS können Sie den Wert für `maxPods` bei der Erstellung des Clusters über die Azure-Befehlszeilenschnittstelle und über Azure Resource Manager-Vorlagen festlegen. Für Kubenet und Azure CNI ist jedoch ein *Mindestwert* erforderlich (wird zum Erstellungszeitpunkt überprüft):

| Netzwerk | Minimum | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Da es sich bei AKS um einen verwalteten Dienst handelt, werden Add-Ons und Pods als Teil des Clusters bereitgestellt und verwaltet. In der Vergangenheit konnten Benutzer einen `maxPods`-Wert definieren, der kleiner war als der Wert, der für die Ausführung der verwalteten Pods erforderlich ist (z. B. 30). AKS berechnet jetzt die Mindestanzahl der Pods mit folgender Formel: ((maxPods oder (maxPods * VM_Anzahl)) > Mindestanzahl verwalteter Add-On-Pods.

Benutzer können die minimale `maxPods`-Validierung nicht außer Kraft setzen.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kann ich Rabatte für Azure-Reservierungen auf meine AKS-Agent-Knoten anwenden?

AKS-Agent-Knoten werden als standardmäßige Azure-VMs abgerechnet. Wenn Sie also [Azure-Reservierungen][reservation-discounts] für die von Ihnen in AKS verwendete VM-Größe erworben haben, werden diese Rabatte automatisch angewendet.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
