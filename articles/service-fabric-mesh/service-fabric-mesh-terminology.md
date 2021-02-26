---
title: Terminologie für Azure Service Fabric Mesh
description: In diesem Artikel wird die von Azure Service Fabric Mesh verwendete Terminologie erläutert, damit Sie die in der Dokumentation verwendeten Begriffe besser verstehen.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626755"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh-Terminologie

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Vorhandene Bereitstellungen werden noch bis zum 28. April 2021 unterstützt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh ist ein vollständig verwalteter Dienst, der es Entwicklern ermöglicht, Microservicesanwendungen zu implementieren, ohne virtuelle Computer, Speicher oder Netzwerke verwalten zu müssen. In diesem Artikel wird die von Azure Service Fabric Mesh verwendete Terminologie erläutert, damit Sie die in der Dokumentation verwendeten Begriffe besser verstehen.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) ist eine Open Source-Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht. Service Fabric ist der Orchestrator für Service Fabric Mesh. Service Fabric bietet Optionen für das Erstellen und Ausführen Ihrer Microservicesanwendungen. Sie können ein beliebiges Framework verwenden, um Ihre Dienste zu programmieren, und aus verschiedenen Umgebungen auszuwählen, in denen die Anwendung ausgeführt werden soll.

## <a name="application-and-service-concepts"></a>Anwendungs- und Dienstkonzepte

**Service Fabric Mesh-Anwendung**: Service Fabric Mesh-Anwendungen werden durch das [Ressourcenmodell](./service-fabric-mesh-service-fabric-resources.md) (YAML- und JSON-Ressourcendateien) beschrieben und können in jeder Umgebung bereitgestellt werden, in der Service Fabric ausgeführt wird.

**Native Service Fabric-Anwendung**: Native Service Fabric-Anwendungen werden durch das [native Anwendungsmodell](../service-fabric/service-fabric-application-model.md) (XML-basierte Anwendungs- und Dienstmanifeste) beschrieben.  Native Service Fabric-Anwendungen können nicht in Service Fabric Mesh ausgeführt werden.

**Anwendung**: Eine Service Fabric Mesh-Anwendung ist die Einheit von Bereitstellung, Versionsverwaltung und Lebensdauer einer Mesh-Anwendung. Der Lebenszyklus jeder Anwendungsinstanz kann unabhängig voneinander verwaltet werden.  Anwendungen bestehen aus mindestens einem Dienstcodepaket und Einstellungen. Eine Anwendung wird mit dem Azure-Ressourcenmodellschema (RM-Schema) definiert.  Dienste werden in einer RM-Vorlage als Eigenschaften der Anwendungsressource beschrieben.  Auf die von der Anwendung verwendeten Netzwerke und Volumes wird von der Anwendung verwiesen.  Beim Erstellen einer Anwendung werden die Anwendung, die Dienste, das Netzwerk und die Volumes mithilfe des Service Fabric-Ressourcenmodells modelliert.

**Dienst**: Ein Dienst stellt in einer Anwendung einen Microservice dar und führt eine vollständige und eigenständige Funktion aus. Jeder Dienst besteht aus mindestens einem Codepaket, das alles beschreibt, was zum Ausführen des dem Codepaket zugeordneten Containerimages erforderlich ist.  Die Anzahl von Dienstreplikaten in einer Anwendung kann zentral hoch- oder herunterskaliert werden.

**Codepaket**: Codepakete beschreiben alles, was benötigt wird, um das mit dem Codepaket verbundene Containerimage auszuführen, beispielsweise:

* Name, Version und Registrierung des Containers
* Für jeden Container erforderliche CPU- und Speicherressourcen
* Netzwerkendpunkte
* Im Container einzubindende Volumes, die auf eine separate Volumeressource verweisen

## <a name="deployment-and-application-models"></a>Bereitstellungs- und Anwendungsmodelle 

Für die Bereitstellung Ihrer Dienste müssen Sie beschreiben, wie sie ausgeführt werden sollen. Service Fabric unterstützt drei Bereitstellungsmodelle:

### <a name="resource-model"></a>Ressourcenmodell
Service Fabric-Ressourcen sind Ressourcen, die in Service Fabric einzeln bereitgestellt werden können, z.B. Anwendungen, Dienste, Netzwerke und Volumes. Ressourcen werden mithilfe einer JSON-Datei definiert, die für einen Clusterendpunkt bereitgestellt werden kann.  Für Service Fabric Mesh wird das Azure-Ressourcenmodellschema verwendet. Ein YAML-Dateischema kann ebenfalls verwendet werden, um Definitionsdateien einfacher zu erstellen. Ressourcen können überall bereitgestellt werden, wo Service Fabric ausgeführt wird. Das Ressourcenmodell ist die einfachste Möglichkeit zum Beschreiben Ihrer Service Fabric-Anwendungen. Sein Hauptaugenmerk liegt auf der einfachen Bereitstellung und Verwaltung von Diensten in Containern. Weitere Informationen finden Sie unter [Einführung in das Service Fabric-Ressourcenmodell](./service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Natives Modell
Das native Anwendungsmodell bietet Ihren Anwendungen eine Low-level-Vollzugriff auf Service Fabric. Anwendungen und Dienste werden als registrierte Typen in XML-Manifestdateien definiert.

Das native Datenmodell unterstützt das Reliable Services-Framework, das Zugriff auf die Service Fabric-Laufzeit-APIs und Clusterverwaltungs-APIs in C# und Java bietet. Das native Modell unterstützt auch beliebige Container und ausführbare Dateien.

Es wird nicht in der Service Fabric Mesh-Umgebung unterstützt.  Weitere Informationen finden Sie unter [Übersicht über Programmiermodelle](../service-fabric/service-fabric-choose-framework.md).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) ist Teil des Docker-Projekts. Service Fabric bietet eingeschränkte Unterstützung für die Bereitstellung von Anwendungen mit dem Docker Compose-Modell.

## <a name="environments"></a>Umgebungen

Service Fabric ist eine Open-Source-Plattformtechnologie, auf der mehrere Dienste und Produkte basieren. Microsoft bietet die folgenden Optionen:

 - **Service Fabric Mesh**: Ein vollständig verwalteter Dienst für die Ausführung von Service Fabric-Anwendungen in Microsoft Azure.
 - **Azure Service Fabric**: Das in Azure gehostete Angebot für Service Fabric-Cluster. Es bietet eine Integration zwischen Service Fabric und der Azure-Infrastruktur sowie Upgrades und eine Konfigurationsverwaltung für Service Fabric-Cluster.
 - **Eigenständiges Service Fabric**: Eine Zusammenstellung von Installations- und Konfigurationstools, um [Service Fabric-Cluster überall bereitzustellen](../service-fabric/service-fabric-deploy-anywhere.md) (lokal oder über einen Cloudanbieter). Nicht von Azure verwaltet.
 - **Service Fabric-Entwicklungscluster**: Eine lokale Entwicklungsumgebung unter Windows, Linux oder Mac für die Entwicklung von Service Fabric-Anwendungen.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrix für die Unterstützung von Umgebungen, Frameworks und Bereitstellungsmodellen
Je nach Umgebung werden unterschiedliche Frameworks und Bereitstellungsmodelle unterstützt. In der folgenden Tabelle werden die unterstützten Kombinationen aus Framework und Bereitstellungsmodell beschrieben.

| Typ der Anwendung | Beschrieben von | Azure Service Fabric Mesh | Azure Service Fabric-Cluster (beliebiges Betriebssystem)| Lokaler Cluster | Eigenständiger Cluster |
|---|---|---|---|---|---|
| Service Fabric Mesh-Anwendungen | Ressourcenmodell (YAML und JSON) | Unterstützt |Nicht unterstützt | Windows – unterstützt, Linux und Mac – nicht unterstützt | Windows – nicht unterstützt |
|Native Service Fabric-Anwendungen | Natives Anwendungsmodell (XML) | Nicht unterstützt| Unterstützt|Unterstützt|Windows – unterstützt|

In der folgenden Tabelle werden die verschiedenen Anwendungsmodelle und Tools beschrieben, die sie für Service Fabric bieten.

| Typ der Anwendung | Beschrieben von | Visual Studio | Eclipse | SFCTL | Azure CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh-Anwendungen | Ressourcenmodell (YAML und JSON) | VS 2017 |Nicht unterstützt |Nicht unterstützt | Unterstützt: Nur Mesh-Umgebung | Nicht unterstützt|
|Native Service Fabric-Anwendungen | Natives Anwendungsmodell (XML) | VS 2017 und VS 2015| Unterstützt|Unterstützt|Unterstützt|Unterstützt|

## <a name="next-steps"></a>Nächste Schritte

In der [Übersicht über Azure Service Fabric Mesh](service-fabric-mesh-overview.md) erfahren Sie mehr über Service Fabric Mesh.

Antworten auf [häufig gestellte Fragen](service-fabric-mesh-faq.md)
