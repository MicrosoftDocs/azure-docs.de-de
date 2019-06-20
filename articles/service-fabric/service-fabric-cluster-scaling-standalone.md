---
title: Skalieren von eigenständigen Azure Service Fabric-Clustern | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie für eigenständige Service Fabric-Cluster das horizontale und zentrale Hoch- und Herunterskalieren durchführen.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 05049b9b08b4630c4299a6d3054c7815b082af52
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60516038"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Skalieren von eigenständigen Service Fabric-Clustern
Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein physischer oder virtueller Computer, der Teil eines Clusters ist, wird als Knoten bezeichnet. Cluster können Tausende von Knoten enthalten. Nach dem Erstellen eines Service Fabric-Clusters können Sie den Cluster horizontal (Änderung der Anzahl von Knoten) oder vertikal (Änderung der Ressourcen von Knoten) skalieren.  Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster.  Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

Gründe für das Skalieren des Clusters Anwendungsanforderungen ändern sich im Laufe der Zeit.  Unter Umständen müssen Sie die Clusterressourcen erhöhen, um auf eine höhere Anwendungsworkload oder vermehrten Netzwerkdatenverkehr zu reagieren, oder verringern, wenn die Nachfrage nachlässt.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Horizontales Herunter- und Hochskalieren
Hierbei wird die Anzahl von Knoten im Cluster geändert.  Nachdem die neuen Knoten in den Cluster eingebunden wurden, verschiebt der [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) Dienste auf diese Knoten, um die Last für die vorhandenen Knoten zu reduzieren.  Sie können die Anzahl von Knoten auch verringern, falls die Ressourcen des Clusters nicht effizient genutzt werden.  Wenn Knoten den Cluster verlassen, werden Dienste von diesen Knoten verschoben, und die Last für die verbleibenden Knoten erhöht sich.  Mit der Reduzierung der Anzahl von Knoten in einem Cluster, der in Azure ausgeführt wird, können Sie Kosten sparen. Der Grund ist, dass Sie für die Anzahl von VMs zahlen, die Sie nutzen, und nicht für die Workload auf diesen VMs.  

- Vorteile: Praktisch unbegrenzte Skalierungsmöglichkeiten.  Wenn Ihre Anwendung auf Skalierbarkeit ausgelegt ist, können Sie sie unbegrenzt erweitern, indem Sie weitere Knoten hinzufügen.  Die Tools in Cloudumgebungen machen das Hinzufügen und Entfernen von Knoten einfach, sodass Sie die Kapazität leicht anpassen können, und Sie zahlen nur für die genutzten Ressourcen.  
- Nachteile: Anwendungen müssen [für Skalierbarkeit konzipiert sein](service-fabric-concepts-scalability.md).  Für Anwendungsdatenbanken und die Persistenz ist ggf. zusätzlicher Architekturaufwand erforderlich, damit die Skalierung hierfür ebenfalls möglich ist.  [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) in zustandsbehafteten Service Fabric-Diensten erleichtern Ihnen die Skalierung Ihrer Anwendungsdaten aber deutlich.

Mit eigenständigen Clustern können Sie Service Fabric-Cluster lokal oder über den Cloudanbieter Ihrer Wahl bereitstellen.  Knotentypen bestehen je nach Bereitstellung aus physischen oder virtuellen Computern. Verglichen mit Clustern, die in Azure ausgeführt werden, ist der Prozess zum Skalieren eines eigenständigen Clusters etwas aufwändiger.  Sie müssen die Anzahl von Knoten im Cluster manuell ändern und dann ein Upgrade für die Clusterkonfiguration durchführen.

Durch das Entfernen von Knoten werden möglicherweise mehrere Upgrades initiiert. Einige Knoten sind mit dem Tag `IsSeedNode=”true”` gekennzeichnet und können durch Abfragen des Clustermanifests mit [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) ermittelt werden. Das Entfernen solcher Knoten dauert möglicherweise länger als bei anderen Knoten, da die Seed-Knoten in einem solchen Szenario verschoben werden müssen. Der Cluster muss mindestens drei primäre Knotentypen verwalten.

> [!WARNING]
> Die für den Cluster angegebene Knotenanzahl sollte mindestens der [Clustergröße der Zuverlässigkeitsstufe](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) entsprechen. Andernfalls besteht die Gefahr, dass die Replizierung von Service Fabric-Systemdiensten im Cluster beeinträchtigt und der Cluster destabilisiert oder sogar gelöscht wird.
>

Beachten Sie beim Skalieren eines eigenständigen Clusters die folgenden Richtlinien:
- Das Ersetzen primärer Knoten muss für jeden Knoten einzeln ausgeführt werden. Das Entfernen und Hinzufügen in Batches ist nicht möglich.
- Überprüfen Sie vor dem Entfernen eines Knotentyps, ob andere Knoten auf den Knotentyp verweisen. Entfernen Sie diese Knoten, bevor Sie den entsprechenden Knotentyp entfernen. Nachdem alle zugehörigen Knoten entfernt wurden, können Sie den NodeType aus der Clusterkonfiguration entfernen und über [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) ein Konfigurationsupgrade starten.

Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Knoten für einen eigenständigen Service Fabric-Cluster unter Windows Server](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Zentrales Hoch- und Herunterskalieren 
Hierbei werden die Ressourcen (CPU, Arbeitsspeicher oder Speicher) von Knoten im Cluster geändert.
- Vorteile: Die Software- und Anwendungsarchitektur bleibt gleich.
- Nachteile: Begrenzte Skalierung, da eine Beschränkung in Bezug auf die Erhöhung von Ressourcen auf einzelnen Knoten besteht. Ausfallzeiten, da physische oder virtuelle Computer offline geschaltet werden müssen, um Ressourcen hinzuzufügen oder zu entfernen.

## <a name="next-steps"></a>Nächste Schritte
* Machen Sie sich mit der [Skalierbarkeit von Anwendungen](service-fabric-concepts-scalability.md) vertraut.
* [Skalieren eines Service Fabric-Clusters](service-fabric-tutorial-scale-cluster.md) (horizontal hoch oder herunter)
* [Programmgesteuertes Skalieren eines Service Fabric-Clusters](service-fabric-cluster-programmatic-scaling.md) (per Azure Fluent-Compute-SDK)
* [Horizontales Herunter- oder Hochskalieren eines eigenständigen Clusters](service-fabric-cluster-windows-server-add-remove-nodes.md)

