---
title: Übersicht über Service Fabric in Azure | Microsoft-Dokumentation
description: Eine Übersicht über Service Fabric und das Konzept der Anwendungen, die sich aus Microservices zusammensetzen, um Skalierung und Resilienz zu bieten. Service Fabric ist eine Plattform für verteilte Systeme und ermöglicht das Erstellen skalierbarer, zuverlässiger und einfach zu verwaltender Anwendungen für die Cloud.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 55bcce3c5a4b0dd00d9217d6cb3cc2cfbe6d5828
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753807"
---
# <a name="overview-of-azure-service-fabric"></a>Übersicht über Azure Service Fabric
Azure Service Fabric ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices und Container vereinfacht. Service Fabric bietet außerdem einfache Lösungen für die komplexen Herausforderungen bei der Entwicklung und Verwaltung von nativer Cloudanwendungen. Entwickler und Administratoren können komplexe Infrastrukturprobleme vermeiden und sich auf das Implementieren geschäftskritischer, anspruchsvoller Workloads konzentrieren, die skalierbar, zuverlässig und einfach zu verwalten sind. Service Fabric ist eine Plattform der nächsten Generation zum Erstellen und Verwalten leistungsstarker Tier-1-Anwendungen mit Cloudskalierung, die in Container ausgeführt werden.

In diesem kurzen Video werden Service Fabric und Microservices vorgestellt:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="compliance"></a>Compliance
Der Azure Service Fabric-Ressourcenanbieter ist in allen Azure-Regionen verfügbar und ist mit allen Compliance-Zertifikaten von Azure konform, u. a. SOC, ISO, PCI-DSS, HIPAA und DSGVO. Eine vollständige Liste der Compliance-Zertifikate finden Sie unter [Complianceangebote](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="applications-composed-of-microservices"></a>Aus Microservices bestehende Anwendungen 
Service Fabric ermöglicht das Erstellen und Verwalten skalierbarer und zuverlässiger Anwendungen, die sich aus Microservices zusammensetzen. Diese Microservices werden in einem gemeinsam genutzten Computerpool (als Cluster bezeichnet) mit hoher Dichte ausgeführt. Die Plattform bietet eine ausgereifte leichte Laufzeit zum Erstellen verteilter, skalierbarer zustandsloser und zustandsbehafteter Microservices, die in Container ausgeführt werden. Außerdem bietet sie umfassende Anwendungsverwaltungsfunktionen zum Organisieren, Bereitstellen, Überwachen, Upgraden/Patchen und Löschen bereitgestellter Anwendungen, einschließlich Diensten in Containern.

Service Fabric liegt schon heute zahlreichen Microsoft-Diensten zugrunde. Hierzu zählen unter anderem Azure SQL-Datenbank, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype for Business und viele weitere Azure-Kerndienste.

Service Fabric wurde für die Erstellung cloudbasierter Dienste konzipiert, die zu Beginn klein ausfallen und je nach Bedarf auf Hunderte oder Tausende von Computern skaliert werden können.

Internetdienste setzen sich heutzutage aus Microservices zusammen. Beispiele für Microservices sind etwa Protokollgateways, Benutzerprofile, Einkaufswagen, Bestandsverarbeitungsvorgänge, Warteschlangen und Caches. Service Fabric ist eine Microservices-Plattform, in der jeder Microservice (oder Container) einen eindeutigen Namen erhält und entweder zustandslos oder zustandsbehaftet sein kann.

Service Fabric bietet umfassende Verwaltungsfunktionen für die Laufzeit und den Lebenszyklus der Anwendungen, die sich aus diesen Microservices zusammensetzen. Die in Containern gehosteten Microservices werden im Service Fabric-Cluster bereitgestellt und aktiviert. Eine Umstellung von virtuellen Computern auf Container ermöglicht eine Zunahme der Dichte in größerem Umfang. Eine weitere deutliche Steigerung der Dichte lässt sich dann erzielen, indem die Umstellung von Containern auf Microservices in diesen Containern durchgeführt wird. Ein einzelner Cluster für Azure SQL-Datenbank kann beispielsweise aus Hunderten von Computern bestehen, die Zehntausende von Containern ausführen, die wiederum Hunderttausende von Datenbanken hosten. Jede Datenbank ist dabei ein zustandsbehafteter Service Fabric-Microservice. 

Weitere Informationen zum Microservice-Ansatz finden Sie unter [Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Containerbereitstellung und -orchestrierung
Service Fabric ist der [Container Orchestrator](service-fabric-cluster-resource-manager-introduction.md) von Microsoft, der Microservices für einen Cluster von Computern bereitstellt. Microservices können auf vielerlei Arten entwickelt werden – von der Nutzung von [Service Fabric-Programmiermodellen](service-fabric-choose-framework.md), [ASP:NET Core](service-fabric-reliable-services-communication-aspnetcore.md), bis zur Bereitstellung von [jedem Code](service-fabric-guest-executables-introduction.md). Wichtig ist, dass Sie in derselben Anwendung beide Dienste in Prozessen und Diensten in Containern mischen können. Wenn Sie in einem Cluster mit Computern lediglich [Container bereitstellen und verwalten](service-fabric-containers-overview.md) möchten, ist Service Fabric der ideale Containerorchestrator.

## <a name="any-os-any-cloud"></a>Jedes Betriebssystem, jede Cloud
Service Fabric kann überall ausgeführt werden. Sie können Cluster für Service Fabric in vielen Umgebungen erstellen, z.B. unter Azure oder lokal, unter Windows Server oder unter Linux. Sie können auch Cluster auf anderen öffentlichen Clouds erstellen. Darüber hinaus ist die Entwicklungsumgebung im SDK **identisch** mit der Produktionsumgebung ohne Beteiligung von Emulatoren. Anders ausgedrückt: Die Komponenten, die in Ihrer lokalen Entwicklungsumgebung ausgeführt werden, werden auch in anderen Umgebungen für den Cluster bereitgestellt.

![Service Fabric-Plattform][Image1]

Für die Windows-Entwicklung ist das Service Fabric .NET SDK in Visual Studio und PowerShell integriert. Siehe [Vorbereiten Ihrer Entwicklungsumgebung unter Windows](service-fabric-get-started.md). Für die Linux-Entwicklung ist das Service Fabric Java SDK in Eclipse integriert, und Yeoman wird zum Generieren von Vorlagen für Java, .NET Core und Containeranwendungen verwendet. Siehe [Vorbereiten Ihrer Entwicklungsumgebung unter Linux](service-fabric-get-started-linux.md).

Weitere Informationen zum Erstellen von Clustern finden Sie unter [Erstellen von eigenständigen Service Fabric-Clustern unter Windows Server oder Linux](service-fabric-deploy-anywhere.md). Informationen zum Erstellen von Azure-Clustern finden Sie unter [Erstellen eines Service Fabric-Clusters in Azure über das Azure-Portal](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Zustandslose und zustandsbehaftete Microservices für Service Fabric
Service Fabric ermöglicht es Ihnen, Anwendungen zu erstellen, die aus Microservices oder Containern bestehen. Zustandslose Microservices (z.B. Protokollgateways und Webproxys) behalten über die Anforderung und ihre Antwort vom Dienst hinaus keinen veränderbaren Zustand bei. Ein zustandsloser Dienst ist zum Beispiel die Workerrolle in Azure Cloud Services. Zustandsbehaftete Microservices (z.B. Benutzerkonten, Datenbanken, Geräte, Einkaufswagen und Warteschlangen) behalten einen veränderbaren, autoritativen Zustand über die Anforderung und ihre Antwort hinaus bei. Heutige Internetanwendungen bestehen aus einer Kombination aus zustandslosen und zustandsbehafteten Microservices. 

Ein zentraler Unterschied von Service Fabric ist der starke Fokus auf die Erstellung zustandsbehafteter Dienste. Dies kann entweder über [integrierte Programmiermodelle](service-fabric-choose-framework.md) oder zustandsbehaftete Dienste in Containern erfolgen. Die [Anwendungsszenarios](service-fabric-application-scenarios.md) beschreiben die Szenarios, in denen zustandsbehaftete Dienste verwendet werden.


## <a name="application-lifecycle-management"></a>Anwendungslebenszyklusverwaltung
Service Fabric verfügt über Unterstützung für den vollständigen Lebenszyklus der Anwendung und CI/CD von Cloudanwendungen einschließlich Containern. Der Lebenszyklus reicht von der Entwicklung über die Bereitstellung, die tägliche Verwaltung und die Wartung bis zur endgültigen Außerbetriebnahme.

Dank der Funktionen für die Anwendungslebenszyklusverwaltung von Service Fabric profitieren Anwendungsadministratoren und IT-Operatoren von einfachen Workflows und können Anwendungen so mit geringem Arbeitsaufwand vorbereiten, bereitstellen, patchen und überwachen. Diese integrierten Workflows entlasten IT-Operatoren bei der Gewährleistung der kontinuierlichen Anwendungsverfügbarkeit erheblich.

Die meisten Anwendungen bestehen aus einer Kombination aus zustandslosen und zustandsbehafteten Microservices, Containern und anderen ausführbaren Dateien, die gemeinsam bereitgestellt werden. Dank leistungsstarker Typen für Anwendungen ermöglicht Service Fabric die Bereitstellung mehrerer Anwendungsinstanzen. Jede Instanz kann jeweils unabhängig voneinander verwaltet und aktualisiert werden. Maßgeblich dabei ist, dass Service Fabric jede ausführbare Datei und jeden Container zuverlässig bereitstellen kann. Mit Service Fabric können beispielsweise .NET, ASP.NET Core, Node.js, Windows-Container, Linux-Container, virtuelle Java-Computer, Skripts, Angular und alle anderen Komponenten bereitgestellt werden, aus denen sich Ihre Anwendung zusammensetzt.

Service Fabric ist z.B. in CI/CD-Tools integriert wie z.B. [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html), und [Octopus Deploy](https://octopus.com/) und kann mit jedem anderen beliebten CI/CD-Tool verwendet werden.

Weitere Informationen zur Verwaltung des Anwendungslebenszyklus finden Sie unter [Anwendungslebenszyklus](service-fabric-application-lifecycle.md). Weitere Informationen zum Bereitstellen von Code finden Sie unter [Bereitstellen einer ausführbaren Gastanwendungsdatei](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Wichtige Funktionen
Mit Service Fabric können Sie Folgendes tun:

* Bereitstellung in Azure oder in lokalen Datencentern, die Windows oder Linux ausführen, ohne Änderungen am Code durchführen. Anwendungen einmal schreiben und an beliebiger Stelle in einem beliebigen Service Fabric-Cluster bereitstellen.
* Entwickeln Sie skalierbare Anwendungen, die aus Microservices bestehen, indem Sie das Service Fabric-Programmiermodell, Container oder jeden beliebigen Code verwenden.
* Sehr zuverlässige zustandslose und zustandsbehaftete Microservices entwickeln. Vereinfachen Sie den Anwendungsentwurf durch die Nutzung zustandsbehafteter Microservices. 
* Verwenden Sie das neue Reliable Actors-Programmiermodell zum Erstellen von Cloudobjekten mit eigenständigem Code und Zustand.
* Container bereitstellen und orchestrieren, die Windows-Container und Linux-Container enthalten. Service Fabric ist ein datenfähiger, statusbehafteter Containerorchestrator.
* Stellen Sie Anwendungen in Sekundenschnelle mit hoher Dichte mit hunderten oder tausenden Anwendungen oder Containern pro Computer bereit.
* Verschiedene Versionen derselben Anwendung gleichzeitig bereitstellen und jede Anwendung unabhängig voneinander aktualisieren.
* Verwalten Sie den Lebenszyklus von Anwendungen ohne Ausfallzeiten – einschließlich Upgrades mit und ohne Funktionsbeeinträchtigung.
* Skalieren Sie die Anzahl von Knoten in einem Cluster hoch oder herunter. Beim Skalieren von Knoten wird ihre Anwendung automatisch skaliert.
* Integrität der Anwendungen überwachen und diagnostizieren und Richtlinien für automatische Reparaturen festlegen.
* Überwachen Sie den Resource Balancer beim Organisieren einer erneuten Bereitstellung der Anwendungen im Cluster. Service Fabric führt nach Fehlern eine Wiederherstellung durch und optimiert die Lastenverteilung basierend auf den verfügbaren Ressourcen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen finden Sie unter:
  * [Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen](service-fabric-overview-microservices.md)
  * [Technische Übersicht](service-fabric-technical-overview.md)
* Einrichten der [Windows-Entwicklungsumgebung](service-fabric-get-started.md)  
* Einrichten der [Linux-Entwicklungsumgebung](service-fabric-get-started-linux.md)
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
