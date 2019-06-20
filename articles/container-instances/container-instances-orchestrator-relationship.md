---
title: Azure Container Instances und Containerorchestrierung
description: Hier erfahren Sie, wie Azure Container Instances mit Containerorchestratoren interagiert.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: df9c3ecbec6dccd9ba8db2b375cfab3276005098
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072990"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances und Containerorchestratoren

Aufgrund ihrer kleinen Größe und Anwendungsorientierung sind Container gut für agile Delivery-Umgebungen und auf Microservice basierende Architekturen geeignet. Die Automatisierung und Verwaltung einer großen Anzahl von Containern und deren Interaktion wird als *Orchestrierung* bezeichnet. Beliebte Containerorchestratoren sind Kubernetes, DC/OS und Docker Swarm.

Azure Container Instances bietet einige grundlegende Planungsfunktionen für Orchestrierungsplattformen. Azure Container Instances unterstützt zwar nicht die höherwertigen Dienste, die diese Plattformen bieten, kann jedoch ergänzend genutzt werden. In diesem Artikel werden die Funktionen von Azure Container Instances beschrieben und wie vollständige Containerorchestratoren damit interagieren können.

## <a name="traditional-orchestration"></a>Herkömmliche Orchestrierung

Die Standarddefinition der Orchestrierung umfasst die folgenden Aufgaben:

- **Zeitplanung**: Findet für ein Containerimage und eine Ressourcenanforderung einen passenden Computer, auf dem der Container ausgeführt werden kann.
- **Affinität/Anti-Affinität**: Gibt an, dass mehrere Container in der Nähe voneinander (für Leistung) oder ausreichend weit voneinander (für Verfügbarkeit) ausgeführt werden sollen.
- **Systemüberwachung**: Bemerkt Containerfehler und plant diese automatisch neu.
- **Failover**: Verfolgt nach, was auf jedem Computer ausgeführt wird, und plant Container von Computern mit Fehlern auf intakte Knoten um.
- **Skalierung**: Fügt Containerinstanzen nach Bedarf manuell oder automatisch hinzu oder entfernt sie.
- **Netzwerk**: Bietet ein Überlagerungsnetzwerk für die Koordinierung der Kommunikation von Containern auf mehreren Hostcomputern.
- **Dienstermittlung**: Ermöglicht, dass sich Container automatisch finden, auch wenn sie den Hostcomputer wechseln oder die IP-Adresse ändern.
- **Koordinierte Anwendungsupgrades**: Verwaltet Containerupgrades zur Vermeidung von Ausfallzeiten der Anwendung und ermöglicht Rollbacks, falls etwas schief geht.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestrierung mit Azure Container Instances: Ein mehrstufiger Ansatz

Azure Container Instances ermöglicht einen mehrstufigen Ansatz für die Orchestrierung, bietet alle Planungs- und Verwaltungsfunktionen für das Ausführen eines einzelnen Containers, und lässt außerdem zu, dass Orchestrierungsplattformen Aufgaben mit mehreren Containern verwalten.

Da die zugrundeliegende Infrastruktur von Container Instances von Azure verwaltet wird, muss sich eine Orchestrierungsplattform nicht darum kümmern, einen geeigneten Hostcomputer zu finden, auf dem ein einzelner Container ausgeführt werden kann. Die Flexibilität der Cloud stellt sicher, dass immer einer verfügbar ist. Stattdessen kann sich der Orchestrator auf die Aufgaben konzentrieren, die die Entwicklung von Architekturen mit mehreren Containern vereinfachen, darunter das Skalieren und Koordinieren von Upgrades.

## <a name="scenarios"></a>Szenarien

Die Orchestratorintegration in Azure Container Instances steckt noch in der Entwicklungsphase, aber es ist davon auszugehen, dass einige andere Umgebungen bald verfügbar sein werden:

### <a name="orchestration-of-container-instances-exclusively"></a>Ausschließliche Orchestrierung von Container Instances

Aufgrund des schnellen Starts und der sekundengenauen Abrechnung bietet eine ausschließlich auf Azure Container Instances basierende Umgebung die schnellste Möglichkeit, um einzusteigen und mit sehr variablen Workloads umzugehen.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombination aus Container Instances und Containern in Virtual Machines

Für stabile Workloads mit langer Ausführungszeit ist es normalerweise günstiger, Container in einem Cluster dedizierter virtueller Computer zu orchestrieren als dieselben Container mit Azure Container Instances auszuführen. Allerdings bietet Container Instances eine tolle Lösung für die schnelle Erweiterung oder Verringerung Ihrer Gesamtkapazität, um auf unerwartete oder vorübergehende Spitzen bei der Auslastung zu reagieren.

Anstatt die Anzahl der virtuellen Computer in Ihrem Cluster horizontal hochzuskalieren und dann zusätzliche Container auf diesen Computern bereitzustellen, kann der Orchestrator einfach in Azure Container Instances zusätzliche Container einplanen und wieder entfernen, wenn sie nicht mehr benötigt werden.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Beispiel für die Implementierung: virtuelle Knoten für Azure Kubernetes Service (AKS)

Um Anwendungsworkloads in einem [Azure Kubernetes Service-Cluster](../aks/intro-kubernetes.md) (AKS) schnell zu skalieren, können Sie *virtuelle Knoten* verwenden, die dynamisch in Azure Container Instances erstellt wurden. Virtuelle Knoten ermöglichen die Netzwerkkommunikation zwischen Pods, die in ACI und dem AKS-Cluster ausgeführt werden. 

Aktuell werden Linux-Containerinstanzen von virtuellen Knoten unterstützt. Verwenden Sie für erste Schritte mit virtuellen Knoten die [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) oder das [Azure-Portal](https://go.microsoft.com/fwlink/?linkid=2047545).

Virtuelle Knoten verwenden das Open Source-Kubelet [Virtual Kubelet][aci-connector-k8s], um das [Kubelet][kubelet-doc] von Kubernetes durch Registrierung als Knoten mit unbegrenzter Kapazität nachzuahmen. Das Virtual Kubelet veranlasst die Erstellung von [Pods][pod-doc] als Containergruppen in Azure Container Instances.

Weitere Beispiele zum Erweitern der Kubernetes-API in serverlosen Containerplattformen finden Sie im [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet)-Projekt.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihren ersten Container mit Azure Container Instances mithilfe der [Schnellstartanleitung](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/