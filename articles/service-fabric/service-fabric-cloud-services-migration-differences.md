---
title: Unterschiede zwischen Cloud Services und Service Fabric
description: Ein konzeptioneller Überblick für die Migration von Anwendungen von Cloud Services zu Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: c7e7d346b5a39a262d1d55265becadb1c718cc04
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575770"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Lernen Sie die Unterschiede zwischen Cloud Services und Service Fabric kennen, bevor Sie Anwendungen migrieren
Microsoft Azure Service Fabric ist die neueste Generation der Cloudanwendungsplattformen für stark skalierbare, sehr zuverlässige verteilte Anwendungen. Es führt viele neue Funktionen zum Verpacken, Bereitstellen, Aktualisieren und Verwalten von verteilten Cloudanwendungen ein. 

Dieser Text ist eine Einführung zur Migration von Anwendungen von Cloud Services zu Service Fabric. Er befasst sich vor allem mit den architektonischen und Designunterschieden zwischen Cloud Services und Service Fabric.

## <a name="applications-and-infrastructure"></a>Anwendungen und Infrastruktur
Ein grundlegender Unterschied zwischen Cloud Services und Service Fabric ist die Beziehung zwischen virtuellen Computern, Workloads und Anwendungen. Eine Workload wird hier als der Code definiert, den Sie schreiben, um eine spezifische Aufgabe durchzuführen oder einen Dienst bereitzustellen.

* **Cloud Services dient dazu, Anwendungen als VMs bereitzustellen.** Der Code, den Sie schreiben, ist eng an eine VM-Instanz, beispielsweise eine Web- oder eine Workerrolle, gebunden. Eine Workload in Cloud Services bereitzustellen, heißt, mindestens eine VM-Instanz, welche die Workload ausführt, bereitzustellen. Es gibt keine Trennung zwischen Anwendungen und VMs daher gibt es keine formelle Definition einer Anwendung. Eine Anwendung kann als ein Satz von Web- oder Workerrolleninstanzen innerhalb einer Cloud Services-Bereitstellung oder als eine komplette Cloud Services-Bereitstellung gesehen werden. In diesem Beispiel wird eine Anwendung als ein Satz Rolleninstanzen dargestellt.

![Cloud Services-Anwendungen und -Topologie][1]

* **Service Fabric dient zur Bereitstellung von Anwendungen in vorhandenen VMs und Computern, die Service Fabric unter Windows oder Linux ausführen.** Die Dienste, die Sie schreiben, sind komplett von der zugrunde liegenden Infrastruktur entkoppelt, welche durch die Service Fabric-Anwendungsplattform wegabstrahiert wird, damit eine Anwendung in mehreren Umgebungen bereitgestellt werden kann. Eine Workload wird in Service Fabric als „Dienst“ bezeichnet, und ein oder mehrere Dienste werden in eine formell definierte Anwendung, die auf der Service Fabric-Anwendungsplattform läuft, gruppiert. Mehrere Anwendungen können auf einem einzelnen Service Fabric-Cluster bereitgestellt werden.

![Service Fabric-Anwendungen und -Topologie][2]

Service Fabric ist eine Anwendungsplattformschicht, die unter Windows oder Linux ausgeführt wird, während Cloud Services ein System für die Bereitstellung von virtuellen Computern mit angefügten Workloads ist, die durch Azure verwaltet werden.
Das Service Fabric-Anwendungsmodell bietet viele Vorteile:

* Kurze Bereitstellungszeit – Die Erstellung von VM-Instanzen kann zeitaufwendig sein. In Service Fabric werden VMs nur einmal bereitgestellt, um einen Cluster zu bilden, der die Service Fabric-Anwendungsplattform hostet. Ab diesem Zeitpunkt können Anwendungspakte sehr schnell zum Cluster bereitgestellt werden.
* Hohe Hostingdichte – In Service Fabric sind die Anwendungen von den VMs, die sie ausführen, getrennt. Das bedeutet, dass Sie eine hohe Anzahl an Anwendungen in einer geringen Anzahl VMs bereitstellen können, was die Gesamtkosten für große Bereitstellungen verringern kann.
* Die Service Fabric-Plattform kann in jeder Umgebung ausgeführt werden, in der Windows Server- oder Linux-Computer vorhanden sind, unabhängig davon, ob es sich um eine Azure-Umgebung oder eine lokale Umgebung handelt. Die Plattform bietet eine Abstraktionsschicht über der zugrunde liegenden Infrastruktur; daher kann Ihre Anwendung in verschiedenen Umgebungen laufen. 
* Verwaltung von verteilten Anwendungen – Service Fabric ist eine Plattform, die nicht nur verteilte Anwendungen hostet, sondern auch hilft, deren Lebenszyklus unabhängig von dem Lebenszyklus der hostenden VM oder des Computers zu verwalten.

## <a name="application-architecture"></a>Anwendungsarchitektur
Die Architektur einer Cloud Services-Anwendung umfasst üblicherweise zahlreiche externe Dienstabhängigkeiten wie Service Bus, Azure Table und Blob Storage, SQL, Redis und andere, um den Zustand und die Daten einer Anwendung und die Kommunikation zwischen Web- und Workerrollen in einer Cloud Services-Bereitstellung zu verwalten. Eine vollständige Cloud Services-Anwendung könnte beispielsweise so aussehen:  

![Cloud Services-Architektur][9]

Service Fabric-Anwendungen können auch die gleichen externen Dienste in einer vollständigen Anwendung verwenden. Diese Beispielarchitektur für Cloud Services zeigt den einfachsten Migrationspfad von Cloud Services zu Service Fabric: Es wird lediglich die Cloud Services-Bereitstellung durch eine Service Fabric-Anwendung ersetzt. Die übergeordnete Architektur bleibt dabei gleich. Die Web- und Workerrollen können mit minimalen Codeänderungen an die zustandslosen Service Fabric-Dienste portiert werden.

![Service Fabric-Architektur nach einer einfachen Migration][10]

In dieser Phase sollte das System wie bisher weiterarbeiten. Dank der zustandsbehafteten Funktionen von Service Fabric können externe Zustandsspeicher, wo möglich, als zustandsbehaftete Dienste internalisiert werden. Dies ist etwas komplexer als eine einfache Migration von Web- und Workerrollen zu zustandslosen Service Fabric-Diensten, da es das Schreiben benutzerdefinierter Dienste erfordert. Diese Dienste müssen für Ihre Anwendung dieselbe Funktion erfüllen wie die externen Dienste bisher. Dies bietet die folgenden Vorteile: 

* Entfernen externer Abhängigkeiten 
* Vereinheitlichen der Bereitstellungs-, Verwaltungs- und Upgrademodelle 

Die Architektur, die sich nach dem Internalisieren dieser Dienste ergibt, könnte beispielsweise so aussehen:

![Service Fabric-Architektur nach einer vollständigen Migration][11]

## <a name="communication-and-workflow"></a>Kommunikation und Workflow
Die meisten Clouddienst-Anwendungen bestehen aus mehr als einer Ebene. Eine Service Fabric-Anwendung besteht gleichermaßen aus mehr als einem Dienst (in der Regel aus vielen Diensten). Zwei häufige Kommunikationsmodelle sind die direkte Kommunikation und die Kommunikation über einen externen dauerhaften Speicher.

### <a name="direct-communication"></a>Direkte Kommunikation
Bei der direkten Kommunikation können die Ebenen direkt durch die auf jeder Ebene offengelegten Endpunkte kommunizieren. In zustandslosen Umgebungen wie Cloud Services bedeutet das, dass eine Instanz einer VM-Rolle entweder zufällig oder reihum ausgewählt wird. Dies dient dazu, die Belastung auszugleichen und eine direkte Verbindung mit deren Endpunkt herzustellen.

![Cloud Services direkte Kommunikation][5]

 Direkte Kommunikation ist ein in Service Fabric übliches Kommunikationsmodell. Der Hauptunterschied zwischen Service Fabric und Cloud Services ist, dass Sie in Cloud Services eine Verbindung zu einer VM herstellen, während Sie in Service Fabric eine Verbindung zu einem Dienst aufbauen. Diese Unterscheidung ist aus mehreren Gründen wichtig:

* Dienste sind in Service Fabric nicht an die VMs gebunden, auf der sie gehostet werden, Dienste können sich im Cluster verschieben. Das wird aus verschiedenen Gründen sogar erwartet: Ressourcennutzungsausgleich, Failover, Aktualisierungen von Anwendungen und der Infrastruktur und Einschränkungen der Platzierung oder Belastung. Das bedeutet, dass sich die Dienstinstanzadresse jederzeit ändern kann. 
* Eine VM in Service Fabric kann mehrere Dienste hosten, wobei jeder über einen eindeutigen Endpunkt verfügt.

Service Fabric bietet einen Dienstermittlungsmechanismus namens „Naming Service“, welcher verwendet werden kann, um die Endpunktadressen der Dienste aufzulösen. 

![Diagramm, das zeigt, wie Service Fabric einen Dienstermittlungsmechanismus namens „Naming Service“ bietet, welcher verwendet werden kann, um die Endpunktadressen der Dienste aufzulösen][6]

### <a name="queues"></a>Warteschlangen
Ein üblicher Kommunikationsmechanismus zwischen Ebenen in zustandslosen Umgebungen wie Cloud Services besteht in der Verwendung einer externen Speicherwarteschlange, um Arbeitsaufgaben dauerhaft von einer Ebene auf die andere zu speichern. Ein verbreitetes Szenario ist eine Web-Ebene, die Aufträge an eine Azure-Speicherwarteschlange oder einen Service Bus schickt. Dort können die Workerrolleninstanzen die Aufträge aus der Warteschlange entfernen und bearbeiten.

![Cloud Services-Warteschlangenkommunikation][7]

Das gleiche Kommunikationsmodell kann in Service Fabric verwendet werden. Dies kann bei der Migration einer vorhandenen Cloud Services-Anwendung zu Service Fabric nützlich sein. 

![Service Fabric direkte Kommunikation][8]

## <a name="parity"></a>Parität
[Cloud Services ist in puncto Steuerungsmöglichkeiten und Benutzerfreundlichkeit mit Service Fabric vergleichbar, gilt aber mittlerweile als veraltet, weshalb Service Fabric für Neuentwicklungen empfohlen wird.](/azure/architecture/guide/technology-choices/compute-decision-tree) Im Anschluss finden Sie einen API-Vergleich:


| **Cloud Services-API** | **Service Fabric-API** | **Hinweise** |
| --- | --- | --- |
| RoleInstance.GetID | „FabricRuntime.GetNodeContext.NodeId“ oder „.NodeName“ | ID ist eine Eigenschaft von „NodeName“. |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtern Sie nach „NodeName“, und verwenden Sie die FD-Eigenschaft. |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtern Sie nach „NodeName“, und verwenden Sie die Upgradeeigenschaft. |
| RoleInstance.GetInstanceEndpoints | „FabricRuntime.GetActivationContext“ oder „Naming“ (ResolveService) | „CodePackageActivationContext“ wird sowohl von „FabricRuntime.GetActivationContext“ als auch innerhalb von Replikaten über „ServiceInitializationParameters.CodePackageActivationContext“ bereitgestellt (während „.Initialize“). |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Wenn Sie die gleiche Art von Filterung nach Typ ausführen möchten, können Sie mithilfe von „FabricClient.ClusterManager.GetClusterManifest“ die Liste mit den Knotentypen aus dem Clustermanifest abrufen, um die Rollen-/Knotentypen zu erhalten. |
| RoleEnvironment.GetIsAvailable | Verwenden Sie „Connect-WindowsFabricCluster“, oder erstellen Sie eine FabricRuntime-Instanz mit einem Verweis auf einen bestimmten Knoten. | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | „FabricClient.QueryManager.GetNodeList“ oder „ResolveService“ | * |
| RoleInstanceEndpoint.GetIPEndpoint | „FabricRuntime.GetActivationContext“ oder „Naming“ (ResolveService) | * |

## <a name="next-steps"></a>Nächste Schritte
Der einfachste Migrationspfad von Cloud Services zu Service Fabric besteht darin, schlicht die Cloud Services-Bereitstellung durch eine Service Fabric-Anwendung zu ersetzen. Die übergeordnete Architektur Ihrer Anwendung bleibt dabei annähernd gleich. Der folgende Artikel bietet eine Hilfestellung für das Konvertieren einer Web- oder Workerrolle zu einem zustandslosen Service Fabric-Dienst.

* [Einfache Migration: Konvertieren einer Web- oder Workerrolle in einen zustandslosen Service Fabric-Dienst](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png