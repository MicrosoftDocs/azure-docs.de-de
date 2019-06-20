---
title: Prüfliste für die Produktionsbereitschaft von Azure Service Fabric | Microsoft-Dokumentation
description: Bereiten Sie Ihre Service Fabric-Anwendung und den Cluster anhand der folgenden bewährten Methoden auf die Produktion vor.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/05/2019
ms.author: aljo
ms.openlocfilehash: a75b02b8173507a28204a3ec2030ce7ed9838495
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729864"
---
# <a name="production-readiness-checklist"></a>Prüfliste für die Produktionsbereitschaft

Sind Ihre Anwendung und Ihr Cluster bereit für den Produktionsdatenverkehr? Das Ausführen und Testen Ihrer Anwendung und Ihres Clusters bedeutet nicht notwendigerweise, dass Produktionsbereitschaft vorliegt. Halten Sie die Ausführung Ihrer Anwendung und Ihres Clusters reibungslos in Gang, indem Sie die folgende Prüfliste durchgehen. Wir empfehlen dringend, alle diese Punkte abzuhaken. Selbstverständlich können Sie für einen bestimmten Punkt alternative Lösungen verwenden (z.B. eigene Diagnoseframeworks).


## <a name="prerequisites-for-production"></a>Voraussetzungen für die Produktion
1. Bewährte Methoden für Azure Service Fabric: [Anwendungsentwurf](./service-fabric-best-practices-applications.md), [Sicherheit](./service-fabric-best-practices-security.md), [Netzwerk](./service-fabric-best-practices-networking.md), [Kapazitätsplanung und Skalierung](./service-fabric-best-practices-capacity-scaling.md), [Infrastruktur als Code](./service-fabric-best-practices-infrastructure-as-code.md) und [Überwachung und Diagnose](./service-fabric-best-practices-monitoring.md). 
1. Implementieren der Reliable Actors-Sicherheitskonfiguration, wenn das Actors-Programmiermodell verwendet wird
1. Erstellen Sie für Cluster mit mehr als 20 Kernen oder 10 Knoten einen dedizierten primären Knotentyp für Systemdienste. Fügen Sie [Platzierungseinschränkungen](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) hinzu, um den primären Knotentyp für Systemdienste zu reservieren.
1. Verwenden Sie für den primären Knotentyp eine SKU des Typs D2v2 oder höher. Es wird empfohlen, eine SKU mit einer Festplattenkapazität von mindestens 50 GB auszuwählen.
1. Produktionscluster müssen [sicher](service-fabric-cluster-security.md) sein. Ein Beispiel für das Einrichten eines sicheren Clusters finden Sie in dieser [Clustervorlage](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Verwenden Sie allgemeine Namen für die Zertifikate, und vermeiden Sie selbstsignierte Zertifikate.
1. Fügen Sie [Ressourceneinschränkungen für Container und Dienste](service-fabric-resource-governance.md) hinzu, damit diese nicht mehr als 75 % der Knotenressourcen belegen. 
1. Machen Sie sich mit [Dauerhaftigkeitsstufen](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) vertraut, und legen Sie eine fest. Für Knotentypen, auf denen statusbehaftete Workloads ausgeführt werden, wird die Dauerhaftigkeitsstufe Silber oder höher empfohlen. Für den primären Knotentyp sollte die Dauerhaftigkeitsstufe auf Silber oder höher festgelegt sein.
1. Machen Sie sich mit [Zuverlässigkeitsstufen](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) vertraut, und legen Sie eine für den Knotentyp fest. Die Zuverlässigkeitsstufe Silver oder höher wird empfohlen.
1. Laden Sie Ihre Workloads, und unterziehen Sie sie Skalierungstests, um die [Kapazitätsanforderungen](service-fabric-cluster-capacity.md) für Ihren Cluster zu ermitteln. 
1. Ihre Dienste und Anwendungen werden überwacht und Anwendungsprotokolle mit Warnhinweisen erstellt und gespeichert. Beispiele finden Sie unter [Hinzufügen von Protokollierung zur Service Fabric-Anwendung](service-fabric-how-to-diagnostics-log.md) und [Überwachen von Containern mit Azure Monitor-Protokollen](service-fabric-diagnostics-oms-containers.md).
1. Der Cluster wird mit Warnungen überwacht (z. B. mit [Azure Monitor-Protokollen](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Die zugrunde liegende Infrastruktur von VM-Skalierungsgruppen wird mit Warnungen überwacht (z. B. mit [Azure Monitor-Protokollen](service-fabric-diagnostics-oms-agent.md)).
1. Der Cluster verfügt stets über [primäre und sekundäre Zertifikate](service-fabric-cluster-security-update-certs-azure.md) (damit Sie nicht ausgesperrt werden).
1. Für Entwicklung, Staging und Produktion werden getrennte Cluster verwaltet. 
1. [Anwendungsupgrades](service-fabric-application-upgrade.md) und [Clusterupgrades](service-fabric-tutorial-upgrade-cluster.md) werden zuerst in Entwicklungs- und Stagingclustern getestet und bereitgestellt. 
1. Deaktivieren Sie automatische Upgrades in Produktionsclustern, und aktivieren Sie sie für Entwicklungs- und Staging-Cluster (Rollback nach Bedarf). 
1. Richten Sie ein Recovery Point Objective (RPO) für Ihren Dienst und einen [Notfallwiederherstellungsprozess](service-fabric-disaster-recovery.md) ein, und testen Sie ihn.
1. Planen Sie die manuelle oder programmgesteuerte [Skalierung](service-fabric-cluster-scaling.md) Ihres Clusters.
1. Planen Sie das [Patchen](service-fabric-patch-orchestration-application.md) der Clusterknoten. 
1. Richten Sie eine CI/CD-Pipeline ein, sodass die letzten Änderungen fortlaufend getestet werden. Beispiel für die Verwendung von [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) oder [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Testen Sie Ihre Entwicklungs- und Stagingcluster unter Last mit dem [Fault Analysis Service](service-fabric-testability-overview.md), und induzieren Sie kontrolliertes [Chaos](service-fabric-controlled-chaos.md). 
1. Planen Sie die [Skalierung](service-fabric-concepts-scalability.md) Ihrer Anwendungen. 


Wenn Sie das Programmiermodell Service Fabric Reliable Services oder Reliable Actors verwenden, müssen die folgenden Punkte abgehakt werden:
1. Aktualisieren Sie Anwendungen während der lokalen Entwicklung, um zu überprüfen, ob Ihr Dienstcode das Annullierungstoken in der `RunAsync`-Methode beachtet und benutzerdefinierte Kommunikationslistener schließt.
1. Vermeiden Sie [häufige Fehlerquellen](service-fabric-work-with-reliable-collections.md) bei Verwendung zuverlässiger Sammlungen.
1. Überwachen Sie die .NET CLR-Speicherleistungszähler, wenn Sie Lasttests durchführen, und prüfen Sie auf hohe Raten von Garbage Collection oder Ausreißer bei der Zunahme der Heapgröße.
1. Halten Sie Offlinesicherungen von [Reliable Services und Reliable Actors](service-fabric-reliable-services-backup-restore.md) vor, und testen Sie den Wiederherstellungsprozess.
1. Die Anzahl der Instanzen Ihrer primären virtuellen NodeType-Computer sollte idealerweise gleich der Mindestzahl für Ihre Clusterzuverlässigkeitsebene sein. Unter den folgenden Bedingungen ist es zulässig, das Ebenenminimum zu überschreiten: vorübergehend bei vertikaler Skalierung Ihrer SKU für Ihre VM-Skalierungsgruppe des primären virtuellen NodeTypes-Computers.

## <a name="optional-best-practices"></a>Optionale bewährte Methoden

Die obigen Listen sind Voraussetzungen für die Inbetriebnahme in der Produktion, aber auch die folgenden Punkte sollten berücksichtigt werden:
1. Stellen Sie eine Verbindung mit dem [Service Fabric-Integritätsmodell](service-fabric-health-introduction.md) her, um die integrierte Integritätsbewertung und Berichterstellung zu erweitern.
1. Stellen Sie einen benutzerdefinierten Watchdog bereit, der Ihre Anwendung überwacht und [Last](service-fabric-cluster-resource-manager-metrics.md) für [Ressourcenausgleich](service-fabric-cluster-resource-manager-balancing.md) meldet. 


## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen eines Service Fabric-Windows-Clusters](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Bereitstellen eines Service Fabric-Linux-Clusters](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Weitere Informationen zum [Anwendungslebenszyklus](service-fabric-application-lifecycle.md) von Service Fabric
