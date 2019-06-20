---
title: Azure Service Fabric-Anwendungsmodell | Microsoft-Dokumentation
description: Informationen zum Modellieren und Beschreiben von Anwendungen und Diensten in Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60621414"
---
# <a name="model-an-application-in-service-fabric"></a>Modellieren von Anwendungen in Service Fabric
Dieser Artikel bietet eine Übersicht über das Azure Service Fabric-Anwendungsmodell und erläutert, wie eine Anwendung und ein Dienst über Manifestdateien definiert werden.

## <a name="understand-the-application-model"></a>Informationen zum Anwendungsmodell
Eine Anwendung ist eine Sammlung von einzelnen Diensten, die eine bzw. mehrere bestimmte Funktionen ausführen. Ein Dienst führt eine vollständige und eigenständige Funktion aus und kann unabhängig von anderen Diensten gestartet und ausgeführt werden.  Ein Dienst besteht aus Code, Konfiguration und Daten. Für jeden Dienst besteht der Code aus den ausführbaren Binärdateien, die Konfiguration umfasst Diensteinstellungen, die zur Laufzeit geladen werden können, und die Daten bestehen aus beliebigen statischen Daten, die vom Dienst verarbeitet werden. Jede Komponente in diesem hierarchischen Anwendungsmodell kann unabhängig mit einer Versionsangabe versehen und aktualisiert werden.

![Das Service Fabric-Anwendungsmodell][appmodel-diagram]

Ein Anwendungstyp ist eine Kategorisierung einer Anwendung und besteht aus einem Bündel von Diensttypen. Ein Diensttyp ist eine Kategorisierung eines Diensts. Bei der Kategorisierung können die Einstellungen und Konfigurationen variieren, die Kernfunktionen bleiben jedoch gleich. Die Instanzen eines Diensts sind die verschiedenen Dienstkonfigurationsvarianten desselben Diensttyps.  

Klassen (oder „Typen“) von Anwendungen und Diensten werden über XML-Dateien (Anwendungsmanifeste und Dienstmanifeste) beschrieben.  Die Manifeste beschreiben Anwendungen und Dienste und sind die Vorlagen, anhand derer Anwendungen aus dem Imagespeicher des Clusters instanziiert werden können.  Manifeste werden unter [Anwendungs- und Dienstmanifeste](service-fabric-application-and-service-manifests.md) ausführlich behandelt. Die Schemadefinition für die Dateien „ServiceManifest.xml“ und „ApplicationManifest.xml“ wird mit dem Service Fabric SDK und den Service Fabric-Tools unter *C:\Programme\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* installiert. Eine Dokumentation des XML-Schemas finden Sie unter [ServiceFabricServiceModel.xsd – Schemadokumentation](service-fabric-service-model-schema.md).

Die Codes für verschiedene Anwendungsinstanzen werden als separate Prozesse ausgeführt, selbst wenn sie im gleichen Service Fabric-Knoten gehostet werden. Darüber hinaus kann der Lebenszyklus jeder Anwendungsinstanz unabhängig verwaltet (z.B. Durchführen eines Upgrades). Die folgende schematische Darstellung zeigt die Gliederung von Anwendungstypen in Diensttypen, die wiederum aus Code, Konfiguration und Datenpaketen bestehen. Obwohl jeder Diensttyp normalerweise einige oder alle dieser Pakettypen enthält, werden nur die Code-/Konfigurations-/Datenpakete für `ServiceType4` angezeigt, um das Diagramm zu vereinfachen.

![Service Fabric-Anwendungstypen und Diensttypen][cluster-imagestore-apptypes]

Im Cluster können eine oder mehrere Instanzen eines Diensttyps aktiv sein. Zustandsbehaftete Dienstinstanzen oder Replikate erzielen beispielsweise eine hohe Zuverlässigkeit durch die Replikation des Zustands zwischen Replikaten, die sich im Cluster in verschiedenen Knoten befinden. Die Replikation gewährleistet im Wesentlichen die Redundanz für den Dienst, sodass dieser auch bei Ausfall eines Knotens in einem Cluster verfügbar ist. Bei einem [partitionierten Dienst](service-fabric-concepts-partitioning.md) unterteilt sich der Zustand (und die Zugriffsmuster auf diesen Zustand) noch weiter auf verschiedene Knoten im Cluster.

Die folgende schematische Darstellung zeigt die Beziehung zwischen Anwendungen und Dienstinstanzen, Partitionen und Replikaten.

![Partitionen und Replikate in einem Dienst][cluster-application-instances]

> [!TIP]
> Sie können das Layout von Anwendungen in einem Cluster mit dem Service Fabric Explorer-Tool anzeigen, das hier verfügbar ist: http://&lt;IhreClusterAdresse&gt;:19080/Explorer. Weitere Informationen finden Sie unter [Visualisieren Ihres Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Nächste Schritte
- Machen Sie sich mit der [Skalierbarkeit von Anwendungen](service-fabric-concepts-scalability.md) vertraut.
- Erfahren Sie mehr über den [Status](service-fabric-concepts-state.md), die [Partitionierung](service-fabric-concepts-partitioning.md) und die [Verfügbarkeit](service-fabric-availability-services.md) von Diensten.
- Lesen Sie, wie Anwendungen und Dienste in [Anwendungs- und Dienstmanifesten](service-fabric-application-and-service-manifests.md) definiert werden.
- [Anwendungshostingmodelle](service-fabric-hosting-model.md) beschreiben die Beziehung zwischen Replikaten (oder Instanzen) eines bereitgestellten Diensts und dem Diensthostprozess.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


