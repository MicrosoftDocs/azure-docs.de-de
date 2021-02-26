---
title: Einführung in das Azure Service Fabric-Ressourcenmodell
description: Einführung in das Service Fabric-Ressourcenmodell, einem vereinfachten Ansatz zum Definieren von Service Fabric Mesh-Anwendungen
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626908"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Einführung in das Service Fabric-Ressourcenmodell

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Das Service Fabric-Ressourcenmodell beschreibt einen einfachen Ansatz zum Definieren von Ressourcen, die eine Service Fabric Mesh-Anwendung ausmachen. Einzelne Ressourcen können in einer beliebigen Service Fabric-Umgebung bereitgestellt werden.  Das Service Fabric-Ressourcenmodell ist auch mit dem Azure Resource Manager-Modell kompatibel. Derzeit unterstützt das Modell die folgenden Ressourcentypen:

- Anwendungen und Dienste
- Netzwerke
- Gateways
- Geheimnisse und Geheimnisse/Werte
- Volumes

Jede Ressource wird deklarativ in einer Ressourcendatei angegeben, die ein einfaches YAML- oder JSON-Dokument ist, das die Mesh-Anwendung beschreibt, und von der Service Fabric-Plattform bereitgestellt.

## <a name="applications-and-services"></a>Anwendungen und Dienste

Eine Anwendungsressource ist die Einheit von Bereitstellung, Versionsverwaltung und Lebensdauer einer Mesh-Anwendung. Sie besteht aus mindestens einer Dienstressource, die einen Microservice darstellt. Jede Dienstressource besteht wiederum aus mindestens einem Codepakete, in dem alle erforderlichen Informationen zum Ausführen des mit dem Codepaket verbundenen Containerimages angegeben sind.

![Apps und Dienste][Image1]

Eine Dienstressource deklariert Folgendes:

- Name, Version und Registrierung des Containers
- Für jeden Container erforderliche CPU- und Speicherressourcen
- Netzwerkendpunkte
- Verweise auf andere Ressourcen wie Netzwerke, Volumes und Geheimnisse 

Alle Codepakete, die als Teil einer Dienstressource definiert sind, werden zusammen als Gruppe bereitgestellt und aktiviert. Die Dienstressource beschreibt auch, wie viele Dienstinstanzen ausgeführt werden, und verweist außerdem auf andere Ressourcen von denen sie abhängt (z. B. Netzwerkressource).

Wenn eine Mesh-Anwendung aus mehreren Diensten besteht, können diese nicht unbedingt zusammen auf demselben Knoten ausgeführt werden. Wenn bei einem Anwendungsupgrade die Aktualisierung eines einzigen Diensts fehlschlägt, werden alle Dienste auf eine frühere Version zurückgesetzt.

Wie bereits erwähnt, kann der Lebenszyklus jeder Anwendungsinstanz separat verwaltet werden. So kann eine Anwendungsinstanz beispielsweise unabhängig von den anderen Anwendungsinstanzen aktualisiert werden. Die Anzahl von Diensten in einer Anwendung sollte relativ klein gehalten werden. Denn je mehr Dienste Sie einer Anwendung hinzufügen, umso schwieriger ist es, jeden Dienst separat zu verwalten.

## <a name="networks"></a>Netzwerke

Die Netzwerkressource ist eine separat bereitstellbare Ressource. Es besteht keine Abhängigkeit zwischen ihr und anderen Anwendungen oder Dienstressourcen. Sie wird verwendet, um ein Netzwerk für Ihre Anwendungen zu erstellen. Mehrere Dienste verschiedener Anwendungen können demselben Netzwerk angehören.  Weitere Informationen finden Sie unter [Netzwerke in Service Fabric Mesh-Anwendungen](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> Die aktuelle Vorschauversion unterstützt nur ein 1:1-Mapping zwischen Anwendungen und Netzwerken.

![Netzwerk und Gateway][Image2]

## <a name="gateways"></a>Gateways
Eine Gatewayressource verbindet zwei Netzwerke und leitet Datenverkehr weiter.  Ein Gateway ermöglicht Ihren Diensten die Kommunikation mit externen Clients und stellt eine Erfassung Ihrer Dienste bereit.  Ein Gateway kann auch verwendet werden, um die Mesh-Anwendung mit Ihrem eigenen, vorhandenen virtuellen Netzwerk zu verbinden. Weitere Informationen finden Sie unter [Netzwerke in Service Fabric Mesh-Anwendungen](service-fabric-mesh-networks-and-gateways.md).

![Netzwerk und Gateway][Image2]

## <a name="secrets"></a>Geheimnisse

Geheimnisressourcen können unabhängig von einer Anwendungs- oder Dienstressource bereitgestellt werden, die diese als Abhängigkeit verwenden kann. Sie werden verwendet, um Geheimnisse sicher an Ihre Anwendungen zu übermitteln. Mehrere Dienste aus verschiedenen Anwendungen können auf Werte desselben Geheimnisses verweisen.

## <a name="volumes"></a>Volumes

Container stellen häufig temporäre Datenträger zur Verfügung. Temporäre Datenträger sind jedoch flüchtig, sodass Sie einen neuen temporären Datenträger erhalten und die Informationen verlieren, wenn ein Container abstürzt. Außerdem ist es schwierig, Informationen auf temporären Datenträgern mit anderen Containern gemeinsam zu verwenden. Volumes sind Verzeichnisse, die in Ihre Containerinstanzen eingebunden werden, und mit denen Sie Zustände erhalten können. Volumes bieten Ihnen einen universellen Dateispeicher und ermöglichen das Lesen und Schreiben von Dateien über normale Datenträger-E/A-Datei-APIs. Die Volumeressource ist eine deklarative Methode, um zu beschreiben, wie ein Verzeichnis und der Sicherungsspeicher für dieses eingebunden wird (Azure Files Volume oder Service Fabric Reliable Volume).  Weitere Informationen finden Sie unter [Speicherzustand](service-fabric-mesh-storing-state.md#volumes).

![Die Abbildung zeigt den Dienst, der in einem Volume speichert, das sowohl in Service Fabric Reliable Volume und dann auf einem replizierten lokalen Datenträger als auch im Azure Files-Volume und im Netzwerkspeicher speichert.][Image3]

## <a name="programming-models"></a>Programmmodelle
Die Dienstressource erfordert nur ein ausgeführtes Containerimage, das in den Codepaketen referenziert wird, die der Ressource zugeordnete sind. Sie können jeden Code, in einer beliebigen Sprache, mit einem beliebigen Framework im Container ausführen, ohne die spezifischen APIs von Service Fabric Mesh zu kennen oder zu verwenden. 

Ihr Anwendungscode bleibt auch außerhalb von Service Fabric Mesh portabel, und Ihre Anwendungsbereitstellungen bleiben konsistent, unabhängig von der Sprache oder vom Framework, das zum Implementieren Ihrer Dienste verwendet wurde. Unabhängig davon, ob Ihre Anwendung ASP.NET Core, Go oder eine Reihe von Prozessen und Skripten ist, das Service Fabric Mesh-Ressourcenbereitstellungsmodell bleibt unverändert. 

## <a name="packaging-and-deployment"></a>Verpacken und Bereitstellung

Service Fabric Mesh-Anwendungen, die auf dem Ressourcenmodell basieren, werden als Docker-Container gepackt.  Service Fabric Mesh ist eine gemeinsame, mehrinstanzenfähigen Umgebung, und Container bieten Ihnen ein hohes Maß an Isolierung.  Diese Anwendungen werden mit einem JSON-Format oder einem YAML-Format (das dann in JSON konvertiert wird) beschrieben. Beim Bereitstellen einer Mesh-Anwendung in Azure Service Fabric Mesh ist der JSON-Code, der zum Beschreiben der Anwendung verwendet wird, eine Azure Resource Manager-Vorlage. Ressourcen werden Azure-Ressourcen zugeordnet.  Beim Bereitstellen einer Mesh-Anwendung in einem Service Fabric-Cluster (eigenständig oder in Azure gehostet) liegt der JSON-Code, mit dem die Anwendung beschrieben wird, in einem Format ähnlich einer Azure Resource Manager-Vorlage vor.  Nach der Bereitstellung können Mesh-Anwendungen über HTTP-Schnittstellen oder die Azure CLI verwaltet werden. 


## <a name="next-steps"></a>Nächste Schritte 
In der Übersicht erfahren Sie mehr über Service Fabric Mesh:
- [Übersicht über Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
