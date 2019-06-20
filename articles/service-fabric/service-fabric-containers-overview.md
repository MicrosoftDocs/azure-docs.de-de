---
title: Übersicht über Service Fabric und Container | Microsoft-Dokumentation
description: Der Artikel enthält eine Übersicht über Service Fabric und die Nutzung von Containern zur Bereitstellung von Microserviceanwendungen. Dieser Artikel enthält eine Übersicht über die Verwendungsweise von Containern sowie über die verfügbaren Funktionen in Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: aljo
ms.openlocfilehash: 5a45f14e5ac1da5152f320bd92b1ebb42be1d214
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60881407"
---
# <a name="service-fabric-and-containers"></a>Service Fabric und Container

## <a name="introduction"></a>Einführung

Azure Service Fabric ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices und Container vereinfacht.

Service Fabric ist der [Containerorchestrator](service-fabric-cluster-resource-manager-introduction.md) von Microsoft, der Microservices für einen Cluster von Computern bereitstellt. Service Fabric profitiert von den umfangreichen Erfahrungen, die im Zuge der Ausführung zahlloser Dienste bei Microsoft gesammelt wurden.

Microservices können auf vielerlei Arten entwickelt werden – von der Nutzung von [Service Fabric-Programmiermodellen](service-fabric-choose-framework.md), [ASP:NET Core](service-fabric-reliable-services-communication-aspnetcore.md), bis zur Bereitstellung von [jedem Code](service-fabric-guest-executables-introduction.md). Auch wenn Sie lediglich [Container bereitstellen und verwalten](service-fabric-containers-overview.md) möchten, ist Service Fabric eine gute Wahl.

Standardmäßig werden diese Dienste von Service Fabric als Prozesse bereitgestellt und aktiviert. Prozesse ermöglichen die schnellste Aktivierung und Nutzung von Ressourcen in einem Cluster mit der höchsten Dichte. Service Fabric kann Dienste auch in Containerimages bereitstellen. Sie können in derselben Anwendung auch Dienste in Prozessen und Dienste in Containern mischen.

Wenn Sie direkt loslegen und Container in Service Fabric ausprobieren möchten, sehen Sie sich eine Schnellstartanleitung, ein Tutorial oder ein Beispiel an:  

[Schnellstart: Bereitstellen von Linux-Containern in Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Schnellstart: Bereitstellen von Windows-Containern in Service Fabric](service-fabric-quickstart-containers.md)  
[Tutorial: Bereitstellen einer .NET-App in einem Windows-Container in Azure Service Fabric](service-fabric-host-app-in-a-container.md)  
[Service Fabric-Containerbeispiele](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Was sind Container?

Mithilfe von Containern lassen sich Anwendungen zuverlässig in verschiedenen Computingumgebungen ausführen, da sie eine unveränderliche Umgebung für die Anwendungsausführung bereitstellen. Container umschließen eine Anwendung und alle ihre Abhängigkeiten (etwa Bibliotheken und Konfigurationsdateien) in einer eigenen isolierten Box, die alle für die Ausführung der Software im Container erforderlichen Komponenten enthält. Unabhängig vom Ausführungsort des Containers verfügt die Anwendung darin stets über alle für die Ausführung erforderlichen Komponenten, etwa die richtigen Versionen der abhängigen Bibliotheken, Konfigurationsdateien usw.

Container werden direkt oberhalb des Kernels ausgeführt und verfügen über eine isolierte Ansicht des Dateisystems und anderer Ressourcen. Eine Anwendung in einem Container verfügt über keine Informationen zu anderen Anwendungen oder Prozessen außerhalb ihres Containers. Jede Anwendung und ihre Laufzeit, Abhängigkeiten und Systembibliotheken werden in einem Container mit privatem Vollzugriff auf die eigene isolierte Containersicht des Betriebssystems ausgeführt. Die Verwendung von Containern in Service Fabric erleichtert nicht nur die Bereitstellung aller Anwendungsabhängigkeiten, die für die Ausführung in verschiedenen Computingumgebungen erforderlich sind, sondern bietet darüber hinaus Sicherheit und Ressourcenisolierung. (Ohne Container werden Dienste in einem Prozess ausgeführt.)

Im Vergleich zu virtuellen Computern haben Container die folgenden Vorteile:

* **Klein:** Für Container werden ein einzelner Speicherbereich und Ebenenversionen und -Updates verwendet, um die Effizienz zu steigern.
* **Schnell**: Container müssen nicht ein gesamtes Betriebssystem starten. Daher ist ihr Start deutlich schneller und erfolgt üblicherweise innerhalb weniger Sekunden.
* **Portabilität**: Ein in einem Container enthaltenes Anwendungsimage kann so portiert werden, dass es in der Cloud, lokal, auf virtuellen Computern oder direkt auf physischen Computern ausgeführt werden kann.
* **Ressourcenkontrolle**: Ein Container kann die physischen Ressourcen beschränken, die er auf seinem Host nutzen kann.

### <a name="container-types-and-supported-environments"></a>Containertypen und unterstützte Umgebungen

Service Fabric unterstützt Container unter Linux und Windows. Unter Windows wird auch der Hyper-V-Isolationsmodus unterstützt.

#### <a name="docker-containers-on-linux"></a>Docker-Container unter Linux

Docker bietet APIs zum Erstellen und Verwalten von Containern oberhalb von Linux-Kernelcontainern. Docker Hub stellt ein zentrales Repository zum Speichern und Abrufen von Containerimages bereit.
Ein Linux-basiertes Tutorial finden Sie unter [Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Windows Server-Container

Unter Windows Server 2016 werden zwei verschiedene Arten von Containern bereitgestellt, die sich anhand des Isolationsgrads unterscheiden. Windows Server-Container und Docker-Container ähneln sich, da beide über eine Namespace- und Dateisystemisolation verfügen, den Kernel aber gemeinsam mit dem Host nutzen, auf dem sie ausgeführt werden. Unter Linux wurde diese Isolation üblicherweise mit „cgroups“ und Namespaces erzielt. Windows Server-Container verhalten sich ähnlich.

Windows-Container mit Hyper-V-Unterstützung bieten mehr Isolation und Sicherheit, da kein Container den Betriebssystemkernel mit anderen Containern oder mit dem Host gemeinsam verwendet. Aufgrund dieses höheren Grads an Sicherheitsisolation sind für Hyper-V aktivierte Container für potenziell kritischere Szenarios mit mehreren Mandanten geeignet.
Ein Windows-basiertes Tutorial finden Sie unter [Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Windows](service-fabric-get-started-containers.md).

In der folgenden Abbildung sind die unterschiedlichen Arten der Virtualisierung und Isolationsstufen dargestellt, die verfügbar sind.
![Service Fabric-Plattform][Image1]

## <a name="scenarios-for-using-containers"></a>Szenarien für die Verwendung von Containern

In den folgenden typischen Beispielen ist ein Container eine gute Wahl:

* **IIS-Lift und -Shift**: Sie können eine vorhandene [ASP.NET MVC](https://www.asp.net/mvc)-App in einem Container platzieren, statt sie zu ASP.NET Core zu migrieren. Diese ASP.NET MVC-Apps sind von Internetinformationsdiensten (Internet Information Services, IIS) abhängig. Sie können diese Anwendungen in Containerimages aus dem vorab erstellten IIS-Image verpacken und mit Service Fabric bereitstellen. Informationen zu Windows-Containern finden Sie unter [Container Images on Windows Server (Containerimages unter Windows Server)](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server).

* **Mischen Sie Container und Service Fabric-Microservices**: Verwenden Sie ein vorhandenes Containerimage für einen Teil Ihrer Anwendung. Beispielsweise können Sie den [NGINX-Container](https://hub.docker.com/_/nginx/) für das Web-Front-End Ihrer Anwendung und zustandsbehaftete Dienste für die rechenintensiveren Back-End-Vorgänge verwenden.

* **Reduzieren Sie die Auswirkungen der Beeinträchtigung durch andere Dienste („Noisy Neighbors“)** : Sie können die Möglichkeit zur Ressourcenkontrolle von Containern verwenden, um die Ressourcen einzuschränken, die ein Dienst auf einem Host verwendet. Wenn Dienste eine große Menge von Ressourcen nutzen und die Leistung anderer Dienste beeinträchtigen (z.B. ein Abfragevorgang mit langer Ausführungsdauer), können Sie diese Dienste in Containern mit Ressourcenkontrolle anordnen.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-Unterstützung für Container

Service Fabric unterstützt die Bereitstellung von Docker-Containern in Linux- und Windows Server-Containern unter Windows Server 2016 zusammen mit der Unterstützung für den Hyper-V-Isolationsmodus. 

Service Fabric stellt ein [Anwendungsmodell](service-fabric-application-model.md) bereit, in dem ein Container einen Anwendungshost darstellt, in dem mehrere Dienstreplikate angeordnet werden. Service Fabric unterstützt darüber hinaus das [Szenario einer ausführbaren Gastanwendungsdatei](service-fabric-guest-executables-introduction.md), bei dem Sie nicht die integrierten Service Fabric-Programmiermodelle verwenden, sondern stattdessen eine vorhandene in einer beliebigen Sprache oder einem beliebigen Framework geschriebene Anwendung in einem Container packen. Dieses Szenario ist der gängigste Anwendungsfall für Container.

Sie können [Service Fabric-Dienste auch in einem Container](service-fabric-services-inside-containers.md) ausführen. Die Unterstützung für das Ausführen von Service Fabric-Diensten innerhalb von Containern ist derzeit begrenzt.

Service Fabric stellt mehrere Containerfunktionen für die Erstellung von Anwendungen bereit, die sich aus Microservices in Containern zusammensetzen, etwa:

* Bereitstellung und Aktivierung des Containerimage
* Ressourcenkontrolle einschließlich des standardmäßigen Festlegens von Ressourcenwerten auf Azure-Clustern.
* Repositoryauthentifizierung
* Containerport zum Hosten der Portzuordnung
* Container-zu-Container-Ermittlung und -Kommunikation
* Möglichkeit zum Konfigurieren und Festlegen von Umgebungsvariablen
* Möglichkeit zum Festlegen von Sicherheitsanmeldeinformationen für den Container.
* Auswahl verschiedener Netzwerkmodi für Container.

Unter [Azure für Container](https://docs.microsoft.com/azure/containers/) finden Sie eine umfassende Übersicht über die Containerunterstützung in Azure, etwa Informationen zum Erstellen eines Kubernetes-Clusters mit Azure Kubernetes Service und einer privaten Docker-Registrierung in Azure Container Registry und vieles mehr.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über die Unterstützung erfahren, die Service Fabric für die Ausführung von Containern bietet. Als Nächstes arbeiten wir Beispiele für die einzelnen Funktionen durch, um deren Nutzung zu veranschaulichen.

[Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Linux](service-fabric-get-started-containers-linux.md)  
[Erstellen Ihrer ersten Service Fabric-Containeranwendung unter Windows](service-fabric-get-started-containers.md)  
[Weitere Informationen zu Windows-Containern](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png