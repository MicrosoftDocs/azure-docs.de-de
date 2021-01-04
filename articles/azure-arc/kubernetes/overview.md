---
title: Übersicht über Kubernetes mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Dieser Artikel enthält eine Übersicht über Kubernetes mit Azure Arc-Unterstützung.
keywords: Kubernetes, Arc, Azure, Container
ms.custom: references_regions
ms.openlocfilehash: 7e48ebf98f12e79cb154fb50d8e6dbdfaea1cd95
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371306"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Was ist Kubernetes mit Azure Arc-Unterstützung (Vorschauversion)?

Sie können Kubernetes-Cluster innerhalb oder außerhalb von Azure mit der Vorschauversion von Kubernetes mit Azure Arc-Unterstützung anfügen und konfigurieren. Wenn ein Kubernetes-Cluster an Azure Arc angefügt ist, wird er im Azure-Portal angezeigt. Er verfügt dann über eine Azure Resource Manager-ID und eine verwaltete Identität. Cluster sind an Azure-Standardabonnements angefügt, befinden sich in einer Ressourcengruppe und können wie jede andere Azure-Ressource Tags erhalten. 

Zum Herstellen einer Verbindung zwischen einem Kubernetes-Cluster und Azure muss ein Clusteradministrator entsprechende Agents bereitstellen. Diese Agents werden in einem Kubernetes-Namespace namens `azure-arc` ausgeführt und sind Kubernetes-Standardbereitstellungen. Die Agents sind verantwortlich für die Konnektivität mit Azure, die Erfassung von Azure Arc-Protokollen und -Metriken sowie die Überwachung von Konfigurationsanforderungen. 

Kubernetes mit Azure Arc-Unterstützung unterstützt branchenübliches SSL zum Schützen von Daten während der Übertragung. Die Daten werden außerdem im Ruhezustand verschlüsselt in einer Azure Cosmos DB-Datenbank gespeichert, um ihre Vertraulichkeit zu gewährleisten.
 
> [!NOTE]
> Kubernetes mit Azure Arc-Unterstützung (Vorschauversion) Sie sollte nicht für Produktionsworkloads verwendet werden.

## <a name="supported-kubernetes-distributions"></a>Unterstützte Kubernetes-Distributionen

Kubernetes mit Azure Arc-Unterstützung kann mit jedem von Cloud Native Computing Foundation (CNFC) zertifizierten Kubernetes-Cluster verwendet werden, z. B. eine AKS-Engine in Azure oder Azure Stack Hub oder GKE-, EKS- und VMware vSphere-Cluster.

Die Features von Kubernetes mit Azure Arc-Unterstützung wurden vom Arc-Team für folgende Distributionen getestet:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS Engine
* AKS-Engine in Azure Stack Hub
* AKS in Azure Stack HCI
* Cluster-API (Azure-Anbieter)

## <a name="supported-scenarios"></a>Unterstützte Szenarios 

Kubernetes mit Azure Arc-Unterstützung unterstützt diese Szenarien: 

* Verbinden von außerhalb von Azure ausgeführtem Kubernetes für Bestand, Gruppierung und Kennzeichnung

* Bereitstellen von Anwendungen und Anwenden der Konfiguration mithilfe der GitOps-basierten Konfigurationsverwaltung 

* Verwenden von Azure Monitor für Container zum Anzeigen und Überwachen Ihrer Cluster 

* Anwenden von Richtlinien mit Azure Policy für Kubernetes 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Unterstützte Regionen 

Kubernetes mit Azure Arc-Unterstützung wird derzeit in diesen Regionen unterstützt: 

* East US 
* Europa, Westen

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen (FAQs)

* Worin besteht der Unterschied zwischen Azure Arc-fähigem Kubernetes und Azure Kubernetes Service (AKS)?

    Azure Kubernetes Service (AKS) ist das Managed Kubernete-Angebot von Azure. AKS vereinfacht das Bereitstellen eines Managed Kubernetes-Clusters in Azure. AKS verringert die Komplexität und den operativen Mehraufwand für die Kubernetes-Verwaltung, indem ein Großteil dieser Verantwortung an Azure übertragen wird. Die Kubernetes-Master werden von Azure verwaltet. Sie verwalten und warten nur die Agent-Knoten.

    Azure Arc-fähiges Kubernetes ermöglicht es Ihnen, Kubernetes-Cluster mit Azure zu verbinden, um die Verwaltungsfunktionen von Azure wie Azure Monitor und Azure Policy zu erweitern. Die Wartung des zugrunde liegenden Kubernetes-Clusters selbst erfolgt durch Sie.

* Muss ich meine Azure Kubernetes Service-Cluster, die in Azure ausgeführt werden, mit Azure Arc verbinden?

    Nein. Alle Funktionen von Azure Arc-fähigem Kubernetes wie Azure Monitor, Azure Policy (Gatekeeper) sind nativ mit AKS verfügbar, das bereits über eine Ressourcendarstellung in Azure verfügt.
    
* Sollte ich meinen AKS-Cluster auf Azure Stack HCI mit Azure Arc verbinden? Was ist mit Kubernetes-Clustern, die auf Azure Stack Hub oder Azure Stack Edge ausgeführt werden?

    Ja, das Verbinden dieser Cluster mit Azure Arc hat Vorteile. Es stellt eine Ressourcendarstellung für diese Kubernetes-Cluster in Azure Resource Manager bereit. Mithilfe dieser Ressourcendarstellung können Funktionen wie Clusterkonfiguration, Azure Monitor, Azure Policy (Gatekeeper) auf diese Kubernetes-Cluster erweitert werden.

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit einem Cluster](./connect-cluster.md)
