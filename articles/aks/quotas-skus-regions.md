---
title: Kontingente, SKUs und regionale Verfügbarkeit von Azure Kubernetes Service (AKS)
description: Erfahren Sie mehr über Standardkontingente, eingeschränkte SKU-Größen für Knoten-VMs und regionale Verfügbarkeit von Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: 8d4ed8f791858747814972bcf16a9672a7f12610
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65901457"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)

Für die Ressourcen und Funktionen aller Azure-Dienste gelten festgelegte Standardlimits und -kontingente. Die Verwendung bestimmter SKUs für virtuelle Computer (VM) ist ebenfalls eingeschränkt.

In diesem Artikel werden die Standardressourcenlimits für AKS-Ressourcen (Azure Kubernetes Service) und die Verfügbarkeit von AKS in Azure-Regionen ausführlich erläutert.

## <a name="service-quotas-and-limits"></a>Dienstkontingente und Limits

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Bereitgestellte Infrastruktur

Alle anderen Netzwerk-, Compute- und Speichereinschränkungen gelten für die bereitgestellte Infrastruktur. Die relevanten Grenzwerte finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Wenn Sie für einen AKS-Cluster ein Upgrade vornehmen, werden vorübergehend zusätzliche Ressourcen genutzt. Diese Ressourcen umfassen verfügbare IP-Adressen in einem Subnetz eines virtuellen Netzwerks oder ein vCPU-Kontingent für virtuelle Computer. Wenn Sie Windows Server-Container verwenden (derzeit in der Vorschau in AKS), besteht der einzige unterstützte Ansatz zum Anwenden der neuesten Updates auf die Knoten darin, einen Upgradevorgang durchzuführen. Ein fehlgeschlagener Upgradevorgang für einen Cluster kann darauf hindeuten, dass Sie nicht über den verfügbaren IP-Adressraum oder das vCPU-Kontingent verfügen, um diese temporären Ressourcen zu verarbeiten. Weitere Informationen zum Upgradevorgang eines Windows Server-Knotens finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Eingeschränkte VM-Größen

Jeder Knoten in einem AKS-Cluster umfasst eine feste Menge von Computeressourcen wie vCPU- und Arbeitsspeicher. Wenn ein AKS-Knoten unzureichende Computeressourcen aufweist, werden Pods möglicherweise nicht ordnungsgemäß ausgeführt. Um sicherzustellen, dass die erforderlichen *kube-system*-Pods und Ihre Anwendungen zuverlässig geplant werden können, verwenden Sie die folgenden VM-SKUs nicht in AKS:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Weitere Informationen zu VM-Typen und ihren Computeressourcen finden Sie unter [Größen für virtuelle Computer in Azure][vm-skus].

## <a name="region-availability"></a>Regionale Verfügbarkeit

Die aktuelle Liste der Regionen, in denen Sie Cluster bereitstellen und ausführen können, finden Sie unter [Regionale Verfügbarkeit von AKS][region-availability].

## <a name="next-steps"></a>Nächste Schritte

Bestimmte Standardlimits und Kontingente können erhöht werden. Wenn Ihre Ressource eine Erhöhung unterstützt, fordern Sie die Erhöhung über eine [Azure-Supportanfrage][azure-support] an (als**Problemtyp** wählen Sie **Kontingent**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
