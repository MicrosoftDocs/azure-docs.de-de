---
title: Übersicht über Kubernetes mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Dieser Artikel enthält eine Übersicht über Kubernetes mit Azure Arc-Unterstützung.
keywords: Kubernetes, Arc, Azure, Container
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560277"
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

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit einem Cluster](./connect-cluster.md)
