---
title: Übersicht über die Service Fabric-Programmiermodelle
description: 'Service Fabric bietet zwei Frameworks zum Erstellen von Diensten: das Actors-Framework und das Services-Framework. Sie bieten unterschiedliche Kompromisse im Hinblick auf Einfachheit und Steuerung.'
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 523c9e0064d8b78698f1a0dd3544bda58436800c
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575719"
---
# <a name="service-fabric-programming-model-overview"></a>Übersicht über die Service Fabric-Programmiermodelle

Service Fabric bietet verschiedene Methoden zum Schreiben und Verwalten von Diensten. Dienste können die Service Fabric-APIs verwenden, um Features und Anwendungsframeworks der Plattform umfassend zu nutzen. Ein Dienst kann auch ein kompiliertes ausführbares Programm sein, das in einer beliebigen Sprache oder in einem beliebigen Code geschrieben wurde und in einem Container ausgeführt wird, der in einem Service Fabric-Cluster gehostet wird.

## <a name="guest-executables"></a>Ausführbare Gastanwendungsdateien

Eine [ausführbare Gastdatei](service-fabric-guest-executables-introduction.md) ist eine vorhandene, beliebig ausführbare Datei, die in einer beliebigen Sprache geschrieben sein kann, die als Service in Ihrer Anwendung ausgeführt werden kann. Ausführbare Gastdateien rufen die APIs der Service Fabric SDK nicht direkt auf. Dennoch profitieren sie von den angebotenen Features der Plattform. Hierzu zählen beispielsweise die Auffindbarkeit von Diensten und benutzerdefinierte Integritäts- und Auslastungsberichte durch Aufrufen von REST-APIs, die über Service Fabric verfügbar gemacht werden. Außerdem wird der Anwendungslebenszyklus vollständig unterstützt.

Beginnen Sie damit, die erste [ausführbare Gastanwendungsdatei](service-fabric-deploy-existing-app.md)bereitzustellen.

## <a name="containers"></a>Container

Standardmäßig werden Dienste von Service Fabric als Prozesse bereitgestellt und aktiviert. Service Fabric kann Dienste auch in [Containern](service-fabric-containers-overview.md) bereitstellen. Service Fabric unterstützt die Bereitstellung von Linux-Containern sowie von Windows-Containern unter Windows Server 2016 und höher. Containerimages können von jedem Containerrepository abgerufen und auf dem Computer bereitgestellt werden. Sie können vorhandene Anwendungen als ausführbare Gastdateien bereitstellen, als zustandslose oder zustandsbehaftete zuverlässige Service Fabric-Dienste oder als Reliable Actors in Containern. Außerdem können Sie Dienste in Prozessen und Dienste in Containern in derselben Anwendung mischen.

[Erfahren Sie mehr über das Ausführen Ihrer Dienste in Containern unter Windows oder Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services ist ein schlankes Framework zum Schreiben von Diensten, die sich in die Service Fabric-Plattform integrieren lassen und von allen Features der Plattform profitieren. Reliable Services stellen einen minimalen Satz von APIs bereit, die der Service Fabric-Runtime ermöglichen, den Lebenszyklus Ihrer Dienste zu verwalten, und Ihren Diensten ermöglichen, mit der Runtime zu interagieren. Das Anwendungsframework ist sehr schlank, bietet vollständige Kontrolle über Entwurfs- und Implementierungsoptionen und kann zum Hosten jedes anderen Anwendungsframeworks (beispielsweise ASP.NET Core) verwendet werden.

Reliable Services können ähnlich wie die meisten Dienstplattformen zustandslos sein. Ein Beispiel wären Webserver, bei denen jede Instanz des Diensts gleichartig erstellt und der Zustand in einer externen Lösung (beispielsweise Azure DB oder Azure Table Storage) gespeichert wird.

Ein exklusives Feature von Service Fabric: Reliable Services können auch zustandsbehaftet sein, da der Status mithilfe von Reliable Collections direkt im Dienst gespeichert wird. Der Status wird durch Replikation als „hochverfügbar“ festgelegt und durch Partitionierung verteilt. Die Verwaltung erfolgt automatisch durch Service Fabric.

[Erfahren Sie mehr über Reliable Services](service-fabric-reliable-services-introduction.md) oder legen Sie los, indem Sie [Ihren ersten eigenen Reliable Service schreiben](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core ist ein neues plattformübergreifendes Open-Source-Framework zur Erstellung moderner cloudbasierter Anwendungen mit Internetverbindung. Hierzu zählen beispielsweise Web-Apps, IoT-Apps und mobile Back-Ends. Service Fabric wird mit ASP.NET Core integriert, damit Sie jeweils zustandslose und zustandsbehaftete ASP.NET Core-Anwendungen schreiben können, die die Vorteile zuverlässiger Auflistungen und erweiterter Orchestrierungsfunktionen von Service Fabric nutzen.

[Erfahren Sie mehr zu ASP.NET Core in Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md), oder beginnen Sie, indem Sie [Ihre erste ASP.NET Core Service Fabric-Anwendung schreiben](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Das Reliable Actor-Anwendungsframework setzt auf Reliable Services auf und implementiert das [Virtual Actor](https://research.microsoft.com/en-us/projects/orleans/)-Muster, basierend auf dem computerbasierten [Akteurmodell](https://en.wikipedia.org/wiki/Actor_model). Das Reliable Actor-Framework verwendet unabhängige Compute- und Statuseinheiten mit Singlethreadausführung, die als *Akteure* bezeichnet werden. Das Reliable Actor-Framework bietet integrierte Kommunikation für Akteure sowie voreingestellte Statuspersistenz und horizontal hochskalierbare Konfigurationen.

Da es sich bei Reliable Actors um ein Anwendungsframework handelt, das auf Reliable Services basiert, ist es vollständig in die Service Fabric-Plattform integriert und profitiert von allen Features, die die Plattform bietet.

[Erfahren Sie mehr über Reliable Actors](service-fabric-reliable-actors-introduction.md), oder legen Sie direkt los, indem Sie [Ihren ersten Reliable Actor-Dienst schreiben](service-fabric-reliable-actors-get-started.md).

[Erstellen Sie einen Front-End-Dienst mithilfe von ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Service Fabric und Container](service-fabric-containers-overview.md)

[Übersicht über Reliable Services](service-fabric-reliable-services-introduction.md)

[Übersicht über Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric und ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
