---
title: Übersicht über Azure-Cluster und eigenständige Service Fabric-Cluster | Microsoft-Dokumentation
description: Sie können Service Fabric-Cluster auf virtuellen oder physischen Computern erstellen, auf denen Windows Server oder Linux ausgeführt wird. Dadurch lassen sich Service Fabric-Anwendungen in jeder lokalen oder cloudbasierten Umgebung oder in einer Microsoft Azure-Umgebung bereitstellen und ausführen, in der sich miteinander verbundene Computer mit Windows Server oder Linux befinden.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 6d5169d8ea4480e95e09228f9eb02bd78fdd0be8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393502"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Azure-Cluster und eigenständige Service Fabric-Cluster unter Windows Server und Linux
Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein Computer oder ein virtueller Computer, der Teil eines Clusters ist, wird als Clusterknoten bezeichnet. Cluster können auf Tausende von Knoten skaliert werden. Wenn Sie dem Cluster neue Knoten hinzufügen, verteilt Service Fabric Partitionsreplikate und Instanzen des Diensts auf die erhöhte Anzahl von Knoten neu, um sie auszugleichen. Die Gesamtleistung der Anwendung verbessert sich, und Konflikte beim Speicherzugriff werden reduziert. Wenn die Knoten im Cluster nicht effizient genutzt werden, können Sie die Anzahl der Knoten im Cluster verringern. Service Fabric gleicht die Partitionsreplikate und Instanzen erneut auf die verringerte Anzahl von Knoten aus, um die Hardware der einzelnen Knoten besser zu nutzen.

Service Fabric ermöglicht die Erstellung von Service Fabric-Clustern auf VMs oder Computern mit Windows Server oder Linux. Dadurch lassen sich Service Fabric-Anwendungen in jeder lokalen oder cloudbasierten Umgebung oder in einer Microsoft Azure-Umgebung bereitstellen und ausführen, in der sich miteinander verbundene Computer mit Windows Server oder Linux befinden.

## <a name="benefits-of-clusters-on-azure"></a>Vorteile von Clustern in Azure
Unter Azure profitieren Sie von der Integration anderer Azure-Features und -Dienste, die den Betrieb und die Verwaltung des Clusters vereinfachen und zuverlässiger machen.

* **Azure-Portal:** Erleichtert das Erstellen und Verwalten von Clustern.
* **Azure Resource Manager:** Vereinfacht die Verwaltung aller vom Cluster genutzten Ressourcen als Einheit sowie die Abrechnung und sorgt für Kostentransparenz.
* **Service Fabric-Cluster als Azure-Ressource** Ein Service Fabric-Cluster ist eine Azure-Ressource, sodass Sie ihn genau wie andere Azure-Ressourcen in Azure modellieren können.
* **Integration in Azure-Infrastruktur** Service Fabric wird mit der zugrunde liegenden Azure-Infrastruktur für Betriebssystem, Netzwerk und andere Upgrades koordiniert, um die Verfügbarkeit und Zuverlässigkeit Ihrer Anwendungen zu verbessern.  
* **Diagnose:** In Azure wird Integration von Azure-Diagnose und Azure Monitor-Protokollen bereitgestellt.
* **Automatische Skalierung**: Integrierte automatische Skalierungsfunktion für in Azure gehostete Cluster aufgrund von Virtual Machines-Skalierungsgruppen. In lokalen und anderen Cloudumgebungen müssen Sie eine eigene automatische Skalierungsfunktion entwickeln oder eine manuelle Skalierung mithilfe der APIs vornehmen, die Service Fabric für die Skalierung von Clustern verfügbar macht.

## <a name="benefits-of-standalone-clusters"></a>Vorteile von eigenständigen Clustern
* Sie können einen beliebigen Cloudanbieter zum Hosten des Clusters auswählen.
* Einmal entwickelte Service Fabric-Anwendungen können mit minimalen oder sogar ganz ohne Änderungen in mehreren Hostingumgebungen ausgeführt werden.
* Kenntnisse der Erstellung von Service Fabric-Anwendungen lassen sich in andere Hostingumgebungen übertragen.
* Erfahrungen mit dem Betrieb und der Verwaltung von Service Fabric-Clustern lassen sich von einer Umgebung in eine andere Umgebung übertragen.
* Die Kundenreichweite wird nicht durch Einschränkungen bei der Hostingumgebung begrenzt.
* Die Möglichkeit, Dienste im Falle eines Ausfalls in einem Rechenzentrum oder bei einem Cloudanbieter in eine andere Bereitstellungsumgebung zu verlagern, sorgt für ein zusätzliches Maß an Zuverlässigkeit und schützt vor weitreichenden Ausfällen.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die Übersicht über [Service Fabric-Cluster in Azure](service-fabric-azure-clusters-overview.md).
* Lesen Sie die Übersicht über [eigenständige Service Fabric-Cluster](service-fabric-standalone-clusters-overview.md).
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).