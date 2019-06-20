---
title: Azure Service Fabric-Clusterskalierung | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie für Azure Service Fabric-Cluster das horizontale und zentrale Hoch- und Herunterskalieren durchführen.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: aljo
ms.openlocfilehash: cb9cb3998ed8208ff7b19aee8a984e4c057408ae
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302262"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Skalieren von Azure Service Fabric-Clustern
Ein Service Fabric-Cluster enthält eine per Netzwerk verbundene Gruppe von virtuellen oder physischen Computern, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Ein physischer oder virtueller Computer, der Teil eines Clusters ist, wird als Knoten bezeichnet. Cluster können Tausende von Knoten enthalten. Nach dem Erstellen eines Service Fabric-Clusters können Sie den Cluster horizontal (Änderung der Anzahl von Knoten) oder vertikal (Änderung der Ressourcen von Knoten) skalieren.  Sie können die Skalierung für den Cluster jederzeit durchführen – auch bei Ausführung von Workloads im Cluster.  Wenn der Cluster skaliert wird, werden Ihre Anwendungen ebenfalls automatisch skaliert.

Gründe für das Skalieren des Clusters Anwendungsanforderungen ändern sich im Laufe der Zeit.  Unter Umständen müssen Sie die Clusterressourcen erhöhen, um auf eine höhere Anwendungsworkload oder vermehrten Netzwerkdatenverkehr zu reagieren, oder verringern, wenn die Nachfrage nachlässt.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Horizontales Herunter- und Hochskalieren
Hierbei wird die Anzahl von Knoten im Cluster geändert.  Nachdem die neuen Knoten in den Cluster eingebunden wurden, verschiebt der [Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md) Dienste auf diese Knoten, um die Last für die vorhandenen Knoten zu reduzieren.  Sie können die Anzahl von Knoten auch verringern, falls die Ressourcen des Clusters nicht effizient genutzt werden.  Wenn Knoten den Cluster verlassen, werden Dienste von diesen Knoten verschoben, und die Last für die verbleibenden Knoten erhöht sich.  Mit der Reduzierung der Anzahl von Knoten in einem Cluster, der in Azure ausgeführt wird, können Sie Kosten sparen. Der Grund ist, dass Sie für die Anzahl von VMs zahlen, die Sie nutzen, und nicht für die Workload auf diesen VMs.  

- Vorteile: Praktisch unbegrenzte Skalierungsmöglichkeiten.  Wenn Ihre Anwendung auf Skalierbarkeit ausgelegt ist, können Sie sie unbegrenzt erweitern, indem Sie weitere Knoten hinzufügen.  Die Tools in Cloudumgebungen machen das Hinzufügen und Entfernen von Knoten einfach, sodass Sie die Kapazität leicht anpassen können, und Sie zahlen nur für die genutzten Ressourcen.  
- Nachteile: Anwendungen müssen [für Skalierbarkeit konzipiert sein](service-fabric-concepts-scalability.md).  Für Anwendungsdatenbanken und die Persistenz ist ggf. zusätzlicher Architekturaufwand erforderlich, damit die Skalierung hierfür ebenfalls möglich ist.  [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) in zustandsbehafteten Service Fabric-Diensten erleichtern Ihnen die Skalierung Ihrer Anwendungsdaten aber deutlich.

VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Sammlung von virtuellen Computern bereitstellen und verwalten können. Jeder Knotentyp, der in einem Azure-Cluster definiert ist, wird [als separate Skalierungsgruppe eingerichtet](service-fabric-cluster-nodetypes.md). Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen. 

Beachten Sie beim Skalieren eines Azure-Clusters die folgenden Richtlinien:
- Primäre Knotentypen mit Ausführung von Produktionsworkloads sollten immer über mindestens fünf Knoten verfügen.
- Andere Knotentypen mit Ausführung von zustandsbehafteten Produktionsworkloads sollten immer über mindestens fünf Knoten verfügen.
- Andere Knotentypen mit Ausführung von zustandslosen Produktionsworkloads sollten immer über mindestens zwei Knoten verfügen.
- Jeder Knotentyp mit der [Dauerhaftigkeitsstufe](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) „Gold“ oder „Silber“ sollte immer über mindestens fünf Knoten verfügen.
- Entfernen Sie keine zufälligen VM-Instanzen/Knoten für einen Knotentyp, sondern verwenden Sie immer die Funktion zum zentralen Herunterskalieren für VM-Skalierungsgruppen. Das Löschen von zufälligen VM-Instanzen kann sich negativ auf die Fähigkeit des Systems zur Durchführung eines korrekten Lastenausgleichs auswirken.
- Wenn Sie Regeln für die automatische Skalierung verwenden, sollten Sie diese so festlegen, dass das horizontale Herunterskalieren (Entfernen von VM-Instanzen) jeweils nur für einen Knoten erfolgt. Es ist nicht sicher, mehrere Instanzen gleichzeitig herunterzuskalieren.

Da die Service Fabric-Knotentypen in Ihrem Cluster auf dem Back-End aus VM-Skalierungsgruppen bestehen, können Sie für jeden Knotentyp bzw. jede VM-Skalierungsgruppe [Regeln für die automatische oder manuelle Skalierung einrichten](service-fabric-cluster-scale-up-down.md).

### <a name="programmatic-scaling"></a>Programmgesteuerte Skalierung
In vielen Szenarien ist das [Skalieren eines Clusters mit einem manuellen Vorgang oder mit Regeln für die automatische Skalierung](service-fabric-cluster-scale-up-down.md) eine gute Lösung. Für anspruchsvollere Szenarien ist diese Vorgehensweise aber unter Umständen nicht geeignet. Mögliche Nachteile dieser Ansätze:

- Das manuelle Skalieren erfordert, dass Sie sich anmelden und Skalierungsvorgänge explizit anfordern. Wenn Skalierungsvorgänge häufig oder zu unvorhersehbaren Zeiten erforderlich sind, ist dieser Ansatz u.U. keine geeignete Lösung.
- Wenn Regeln für automatische Skalierung eine Instanz aus einer VM-Skalierungsgruppe entfernen, entfernen sie nicht automatisch Informationen dieses Knotens aus dem zugeordneten Service Fabric-Cluster, es sei denn, der Knotentyp weist die Dauerhaftigkeitsstufe „Gold“ oder „Silber“ auf. Da Regeln für automatische Skalierung auf Skalierungsgruppenebene gelten (anstatt auf Service Fabric-Ebene), können sie Service Fabric-Knoten entfernen, ohne diese ordnungsgemäß heruntergefahren. Diese grobe Knotenentfernung hinterlässt Service Fabric-Knoten nach Vorgängen zum horizontalen Herunterskalieren in verwaistem Zustand. Eine Person (oder ein Dienst) müsste den Zustand entfernter Knoten im Service Fabric-Cluster in regelmäßigen Abständen bereinigen.
- Ein Knotentyp mit der Dauerhaftigkeitsstufe „Gold“ oder „Silber“ bereinigt entfernte Knoten automatisch. Daher ist keine weitere Bereinigung erforderlich.
- Zwar werden [viele Metriken](../azure-monitor/platform/autoscale-common-metrics.md) von Regeln für automatische Skalierung unterstützt, es handelt sich jedoch immer noch um einen begrenzten Satz. Wenn Ihr Szenario die Skalierung basierend auf einer Metrik erfordert, die in diesem Satz nicht enthalten ist, sind Regeln für automatische Skalierung möglicherweise keine gute Wahl.

Der geeignete Ansatz für die Service Fabric-Skalierung hängt von Ihrem Szenario ab. Wenn selten skaliert wird, ist die Möglichkeit zum manuellen Hinzufügen oder Entfernen von Knoten wahrscheinlich ausreichend. Bei komplexeren Szenarien stellen Regeln für automatische Skalierung und SDKs, die eine programmgesteuerte Skalierung ermöglichen, leistungsfähige Alternativen dar.

Azure-APIs sind vorhanden, sodass Anwendungen programmgesteuert mit den VM-Skalierungsgruppen und Service Fabric-Clustern arbeiten können. Wenn vorhandene Optionen zur automatischen Skalierung für Ihr Szenario nicht funktionieren, ermöglichen diese APIs das Implementieren einer benutzerdefinierten Skalierungslogik. 

Ein Verfahren zur Implementierung dieser selbst erstellen Funktionalität für automatische Skalierung ist das Hinzufügen eines neuen zustandslosen Diensts zur Service Fabric-Anwendung, um Skalierungsvorgänge zu verwalten. Das Erstellen eines eigenen Skalierungsdiensts bietet das höchste Maß an Kontrolle und Anpassbarkeit für das Skalierungsverhalten Ihrer Anwendung. Dies kann für Szenarien hilfreich sein, die eine genaue Kontrolle darüber erfordern, wann oder wie eine Anwendung horizontal herunter- oder hochskaliert wird. Diese Kontrolle bringt jedoch den Nachteil von komplexem Code mit sich. Für diesen Ansatz benötigen Sie anspruchsvollen eigenen Skalierungscode. Innerhalb der `RunAsync`-Methode des Dienstes kann ein Satz von Triggern ermitteln, ob eine Skalierung erforderlich ist (dabei werden auch Parameter wie z.B. die maximale Clustergröße überprüft und cooldown-Einstellungen skaliert).   

Die API, die für Interaktionen mit der VM-Skalierungsgruppe (sowohl zum Überprüfen als auch zum Ändern der aktuellen Anzahl von VM-Instanzen) verwendet wird, ist die Fluent-Bibliothek [Azure.Management.Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Die Fluent-Compute-Bibliothek stellt eine benutzerfreundliche API für die Interaktion mit VM-Skalierungsgruppen bereit.  Verwenden Sie für die Interaktion mit dem Service Fabric-Cluster selbst [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Der Skalierungscode muss aber nicht als Dienst in dem Cluster ausgeführt werden, der skaliert werden soll. Sowohl `IAzure` als auch `FabricClient` können eine Remoteverbindung mit ihren jeweils zugeordneten Azure-Ressoucen herstellen, damit der Skalierungsdienst einfach eine Konsolenanwendung oder ein Windows-Dienst sein kann, die bzw. der außerhalb der Service Fabric-Anwendung ausgeführt werden kann.

Aufgrund dieser Einschränkungen ist es ggf. ratsam, [stärker angepasste Modelle für die automatische Skalierung zu implementieren](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Zentrales Hoch- und Herunterskalieren 
Hierbei werden die Ressourcen (CPU, Arbeitsspeicher oder Speicher) von Knoten im Cluster geändert.
- Vorteile: Die Software- und Anwendungsarchitektur bleibt gleich.
- Nachteile: Begrenzte Skalierung, da eine Beschränkung in Bezug auf die Erhöhung von Ressourcen auf einzelnen Knoten besteht. Ausfallzeiten, da physische oder virtuelle Computer offline geschaltet werden müssen, um Ressourcen hinzuzufügen oder zu entfernen.

VM-Skalierungsgruppen sind eine Azure-Computeressource, mit der Sie eine Sammlung von virtuellen Computern bereitstellen und verwalten können. Jeder Knotentyp, der in einem Azure-Cluster definiert ist, wird [als separate Skalierungsgruppe eingerichtet](service-fabric-cluster-nodetypes.md). Jeder Knotentyp kann dann separat verwaltet werden.  Das zentrale Hoch- oder Herunterskalieren eines Knotentyps umfasst das Ändern der SKU von VM-Instanzen in der Skalierungsgruppe. 

> [!WARNING]
> Wir empfehlen Ihnen, die VM-SKU einer Skalierungsgruppe bzw. eines Knotentyps nur dann zu ändern, wenn sie mit der [Dauerhaftigkeitsstufe „Silber“ oder höher](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) ausgeführt wird. Bei der Änderung der VM-SKU-Größe handelt es sich um einen für Daten schädlichen direkten Infrastrukturvorgang. Ohne eine Möglichkeit der Verzögerung oder Überwachung dieser Änderung ist es möglich, dass der Vorgang bei zustandsbehafteten Diensten zu Datenverlusten führt oder selbst bei zustandslosen Workloads unvorhergesehene Probleme auftreten. 
>

Beachten Sie beim Skalieren eines Azure-Clusters die folgende Richtlinie:
- Wenn Sie einen primären Knotentyp herunterskalieren, sollten Sie ihn nie weiter herunterskalieren, als für die [Zuverlässigkeitsstufe](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) zulässig ist.

Der Prozess zum zentralen Hoch- oder Herunterskalieren eines Knotentyps unterscheidet sich jeweils in Abhängigkeit davon, ob es sich um einen primären oder einen anderen Knotentyp handelt.

### <a name="scaling-non-primary-node-types"></a>Skalieren von nicht primären Knotentypen
Erstellen Sie einen neuen Knotentyp mit den erforderlichen Ressourcen.  Aktualisieren Sie die Platzierungseinschränkungen von ausgeführten Diensten, damit diese den neuen Knotentyp enthalten.  Reduzieren Sie die Instanzanzahl für den alten Knotentyp langsam (eine Instanz nach der anderen) bis auf null, damit die Zuverlässigkeit des Clusters nicht beeinträchtigt wird.  Dienste werden nach und nach zum neuen Knotentyp migriert, wenn der alte Knotentyp außer Betrieb genommen wird.

### <a name="scaling-the-primary-node-type"></a>Skalieren des primären Knotentyps
Wir empfehlen Ihnen, die VM-SKU des primären Knotentyps nicht zu ändern. Falls Sie mehr Clusterkapazität benötigen, raten wir Ihnen, weitere Instanzen hinzuzufügen. 

Sollte dies nicht möglich sein, können Sie einen neuen Cluster erstellen und eine [Wiederherstellung des Anwendungszustands](service-fabric-reliable-services-backup-restore.md) (falls zutreffend) vom alten Cluster durchführen. Sie müssen keine Systemdienstzustände wiederherstellen, denn diese werden neu erstellt, wenn Sie Ihre Anwendungen im neuen Cluster bereitstellen. Wenn Sie in Ihrem Cluster ausschließlich zustandslose Anwendungen ausführen, stellen Sie lediglich Ihre Anwendungen im neuen Cluster bereit und müssen nichts wiederherstellen. Wenn Sie einen nicht unterstützten Weg einschlagen und die VM-SKU ändern möchten, können Sie das Modell der VM-Skalierungsgruppe an die neue SKU anpassen. Wenn der Cluster nur einen Knotentyp enthält, sollten Sie sicherstellen, dass Ihre gesamten zustandsbehafteten Anwendungen rechtzeitig auf alle [Lebenszyklus-Dienstereignisse für Replikate](service-fabric-reliable-services-lifecycle.md) (z.B. Unterbrechung der Replikaterstellung) reagieren und dass die Dauer für die Neuerstellung des Dienstreplikats weniger als fünf Minuten beträgt (für die Dauerhaftigkeitsstufe „Silber“). 

## <a name="next-steps"></a>Nächste Schritte
* Machen Sie sich mit der [Skalierbarkeit von Anwendungen](service-fabric-concepts-scalability.md) vertraut.
* [Skalieren eines Service Fabric-Clusters](service-fabric-tutorial-scale-cluster.md) (horizontal hoch oder herunter)
* [Programmgesteuertes Skalieren eines Service Fabric-Clusters](service-fabric-cluster-programmatic-scaling.md) (per Azure Fluent-Compute-SDK)
* [Horizontales Herunter- oder Hochskalieren eines eigenständigen Clusters](service-fabric-cluster-windows-server-add-remove-nodes.md)

