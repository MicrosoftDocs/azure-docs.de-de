---
title: Azure Service Fabric-Ereignisliste | Microsoft-Dokumentation
description: Eine umfassende Liste mit Ereignissen, die von Azure Service Fabric bereitgestellt werden, um Sie bei der Clusterüberwachung zu unterstützen.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde0464985f756132c60453c4e79ffefd4a1dd2c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788596"
---
# <a name="list-of-service-fabric-events"></a>Liste mit Service Fabric-Ereignissen 

Service Fabric macht einen primären Satz von Clusterereignissen in Form von [Service Fabric-Ereignissen](service-fabric-diagnostics-events.md) verfügbar, um Sie über den Status Ihres Clusters zu informieren. Diese basieren auf Aktionen, die von Service Fabric für Ihre Knoten und Cluster ausgeführt werden, oder auf Verwaltungsentscheidungen eines Clusterbesitzers/-betreibers. Auf diese Ereignisse kann durch Konfigurieren auf verschiedene Art und Weise zugegriffen werden, einschließlich Konfigurieren von [Azure Monitor-Protokollen mit Ihrem Cluster](service-fabric-diagnostics-oms-setup.md) oder Abfragen von [EventStore](service-fabric-diagnostics-eventstore.md). Auf Windows-Computern sind diese Ereignisse in das „EventLog“ (Ereignisprotokoll) eingebunden, sodass Service Fabric-Ereignisse in der Ereignisanzeige angezeigt werden. 

Im Folgenden sind einige Merkmale dieser Ereignisse aufgeführt:
* Jedes Ereignis ist an eine bestimmte Entität im Cluster gebunden, z. B. Anwendung, Dienst, Knoten, Replikat.
* Jedes Ereignis verfügt über mehrere gemeinsame Felder: „EventInstanceId“ (Ereignis-Instanz-ID), „EventName“ (Ereignisname), und „Category“ (Kategorie).
* Jedes Ereignis verfügt über Felder, die das Ereignis an die Entität binden, der es zugeordnet ist. So besitzt beispielsweise das Ereignis „ApplicationCreated“ (Anwendung erstellt) Felder, die den Namen der erstellten Anwendung identifizieren.
* Ereignisse sind so strukturiert, dass sie für detaillierte Analysen in einer Reihe von Tools benutzt werden können. Zusätzlich werden relevante Details für ein Ereignis, im Gegensatz zu einer langen Zeichenfolge, als separate Eigenschaften definiert. 
* Ereignisse werden durch verschiedene Subsysteme in Service Fabric geschrieben und durch „Source(Task)“ (Quelle(Aufgabe)) identifiziert. Weitere Informationen zu diesen Subsystemen finden Sie unter [Service Fabric Architecture (Service Fabric-Architektur)](service-fabric-architecture.md) und unter [Service Fabric Technical Overview (Technische Übersicht über Service Fabric)](service-fabric-technical-overview.md).

Im Folgenden sind diese Service Fabric-Ereignisse sortiert nach Entität aufgelistet:

## <a name="cluster-events"></a>Clusterereignisse

**Ereignisse für Clusterupgrades**

Weitere Informationen zu Clusterupgrades finden Sie [hier](service-fabric-cluster-upgrade-windows-server.md).

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Upgrade | Ein Clusterupgrade wurde gestartet. | CM | Information |
| 29628 | ClusterUpgradeCompleted | Upgrade | Ein Clusterupgrade wurde abgeschlossen. | CM | Information | 
| 29629 | ClusterUpgradeRollbackStarted | Upgrade | Das Rollback eines Clusterupgrades wurde gestartet.  | CM | Warnung | 
| 29630 | ClusterUpgradeRollbackCompleted | Upgrade | Das Rollback eines Clusterupgrades wurde abgeschlossen. | CM | Warnung | 
| 29631 | ClusterUpgradeDomainCompleted | Upgrade | Der Upgradevorgang für eine Upgradedomäne wurde während eines Clusterupgrades beendet. | CM | Information | 

## <a name="node-events"></a>Knotenereignisse

**Ereignisse für den Knotenlebenszyklus** 

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | StateTransition | Die Deaktivierung eines Knotens wurde abgeschlossen. | FM | Information | 
| 18603 | NodeUp | StateTransition | Der Cluster hat erkannt, dass ein Knoten gestartet wurde. | FM | Information | 
| 18604 | NodeDown | StateTransition | Der Cluster hat erkannt, dass ein Knoten heruntergefahren wurde. Bei einem Neustart eines Knotens wird ein NodeDown-Ereignis gefolgt von einem NodeUp-Ereignis angezeigt. |  FM | Error | 
| 18605 | NodeAddedToCluster | StateTransition |  Dem Cluster wurde ein neuer Knoten hinzugefügt, und Service Fabric kann für diesen Knoten Anwendungen bereitstellen. | FM | Information | 
| 18606 | NodeRemovedFromCluster | StateTransition |  Ein Knoten wurde aus dem Cluster entfernt. Service Fabric stellt für diesen Knoten keine Anwendungen mehr bereit. | FM | Information | 
| 18607 | NodeDeactivateStarted | StateTransition |  Die Deaktivierung eines Knotens wurde gestartet. | FM | Information | 
| 25621 | NodeOpenSucceeded | StateTransition |  Ein Knoten wurde erfolgreich gestartet. | FabricNode | Information | 
| 25622 | NodeOpenFailed | StateTransition |  Ein Knoten konnte nicht gestartet und nicht mit dem Ring verknüpft werden. | FabricNode | Error | 
| 25624 | NodeClosed | StateTransition |  Ein Knoten wurde erfolgreich heruntergefahren. | FabricNode | Information | 
| 25626 | NodeAborted | StateTransition |  Ein Knoten wurde nicht ordnungsgemäß heruntergefahren. | FabricNode | Error | 

## <a name="application-events"></a>Anwendungsereignisse

**Ereignisse für den Anwendungslebenszyklus**

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | LifeCycle | Eine neue Anwendung wurde erstellt. | CM | Information | 
| 29625 | ApplicationDeleted | LifeCycle | Eine vorhandene Anwendung wurde gelöscht. | CM | Information | 
| 23083 | ApplicationProcessExited | LifeCycle | Ein Prozess in einer Anwendung wurde beendet. | Hosting | Information | 

**Ereignisse für Anwendungsupgrades**

Weitere Informationen zu Anwendungsupgrades finden Sie [hier](service-fabric-application-upgrade.md).

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Upgrade | Ein Anwendungsupgrade wurde gestartet. | CM | Information | 
| 29622 | ApplicationUpgradeStarted | Upgrade | Ein Anwendungsupgrade wurde abgeschlossen. | CM | Information | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgrade | Das Rollback eines Anwendungsupgrades wurde gestartet. |CM | Warnung | 
| 29624 | ApplicationUpgradeRollbackCompleted | Upgrade | Das Rollback eines Anwendungsupgrades wurde abgeschlossen. | CM | Warnung | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgrade | Der Upgradevorgang für eine Upgradedomäne wurde während eines Anwendungsupgrades beendet. | CM | Information | 

## <a name="service-events"></a>Dienstereignisse

**Ereignisse für den Dienstlebenszyklus**

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | LifeCycle | Ein neuer Dienst wurde erstellt. | FM | Information | 
| 18658 | ServiceDeleted | LifeCycle | Ein vorhandener Dienst wurde gelöscht. | FM | Information | 

## <a name="partition-events"></a>Partitionsereignisse

**Ereignisse für Partitionsverschiebungen**

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | LifeCycle | Die Neukonfiguration einer Partition wurde abgeschlossen. | RA | Information | 

## <a name="replica-events"></a>Replikatereignisse

**Replikatlebenszyklusereignisse**

| EventId | NAME | Category (Kategorie) | BESCHREIBUNG |Quelle (Aufgabe) | Level |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | LifeCycle | Ein zuverlässiges Wörterbuch wurde geöffnet. | DistributedDictionary | Information |
| 61702 | ReliableDictionaryClosed | LifeCycle | Ein zuverlässiges Wörterbuch wurde geschlossen. | DistributedDictionary | Information |
| 61703 | ReliableDictionaryCheckpointRecovered | LifeCycle | Ein zuverlässiges Wörterbuch hat seinen Prüfpunkt wiederhergestellt. | DistributedDictionary | Information |
| 61704 | ReliableDictionaryCheckpointFilesSent | LifeCycle | Das Replikat hat die Prüfpunktdateien des zuverlässigen Wörterbuchs gesendet. | DistributedDictionary | Information |
| 61705 | ReliableDictionaryCheckpointFilesReceived | LifeCycle | Das Replikat hat die Prüfpunktdateien des zuverlässigen Wörterbuchs empfangen. | DistributedDictionary | Information |
| 61963 | ReliableQueueOpened | LifeCycle | Eine zuverlässige Warteschlange wurde geöffnet. | DistributedQueue | Information |
| 61964 | ReliableQueueClosed | LifeCycle | Eine zuverlässige Warteschlange wurde geschlossen. | DistributedQueue | Information |
| 61965 | ReliableQueueCheckpointRecovered | LifeCycle | Eine zuverlässige Warteschlange hat ihren Prüfpunkt wiederhergestellt. | DistributedQueue | Information |
| 61966 | ReliableQueueCheckpointFilesSent | LifeCycle | Das Replikat hat die Prüfpunktdateien der zuverlässigen Warteschlange gesendet. | DistributedQueue | Information |
| 63647 | ReliableQueueCheckpointFilesReceived | LifeCycle | Das Replikat hat die Prüfpunktdateien der zuverlässigen Warteschlange empfangen. | DistributedQueue | Information |
| 63648 | ReliableConcurrentQueueOpened | LifeCycle | Eine zuverlässige parallele Warteschlange wurde geöffnet. | ReliableConcurrentQueue | Information |
| 63649 | ReliableConcurrentQueueClosed | LifeCycle | Eine zuverlässige parallele Warteschlange wurde geschlossen. | ReliableConcurrentQueue | Information |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | LifeCycle | Eine zuverlässige parallele Warteschlange hat ihren Prüfpunkt wiederhergestellt. | ReliableConcurrentQueue | Information |
| 61687 | TStoreError | Fehler | In der zuverlässigen Sammlung ist ein unerwarteter Fehler aufgetreten. | TStore | Error |
| 63831 | PrimaryFullCopyInitiated | LifeCycle | Das primäre Replikat hat eine vollständige Kopie initiiert. | TReplicator | Information |
| 63832 | PrimaryPartialCopyInitiated | LifeCycle | Das primäre Replikat hat eine Teilkopie initiiert. | TReplicator | Information |
| 16831 | BuildIdleReplicaStarted | LifeCycle | Das primäre Replikat hat mit dem Erstellen von Leerlaufreplikaten begonnen. | Replikation | Information |
| 16832 | BuildIdleReplicaCompleted | LifeCycle | Das primäre Replikat hat das Erstellen von Leerlaufreplikaten abgeschlossen. | Replikation | Information |
| 16833 | BuildIdleReplicaFailed | LifeCycle | Das primäre Replikat konnte keine Leerlaufreplikate erstellen. | Replikation | Warnung |
| 16834 | PrimaryReplicationQueueFull | Health | Die Replikationswarteschlange des primären Replikats ist voll. | Replikation | Warnung |
| 16835 | PrimaryReplicationQueueWarning | Health | Die Replikationswarteschlange des primären Replikats ist fast voll. | Replikation | Warnung |
| 16836 | PrimaryReplicationQueueWarningMitigated | Health | Die Auslastung der Replikationswarteschlange des primären Replikats ist im Normalbereich. | Replikation | Information |
| 16837 | SecondaryReplicationQueueFull | Health | Die Replikationswarteschlange des sekundären Replikats ist voll. | Replikation | Warnung |
| 16838 | SecondaryReplicationQueueWarning | Health | Die Replikationswarteschlange des sekundären Replikats ist fast voll. | Replikation | Warnung |
| 16839 | SecondaryReplicationQueueWarningMitigated | Health | Die Auslastung der Replikationswarteschlange des sekundären Replikats ist im Normalbereich. | Replikation | Information |
| 16840 | PrimaryFaultedSlowSecondary | Health | Das primäre Replikat verfügt über ein fehlerhaftes, langsames sekundäres Replikat. | Replikation | Warnung |
| 16841 | ReplicatorFaulted | Health | Das Replikat ist ausgefallen. | Replikation | Warnung |

## <a name="container-events"></a>Containerereignisse

**Ereignisse für den Containerlebenszyklus** 

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Ein Container wurde gestartet. | Hosting | Information | 1 |
| 23075 | ContainerDeactivated | Ein Container wurde beendet. | Hosting | Information | 1 |
| 23082 | ContainerExited | Ein Container wurde beendet. Überprüfen Sie das UnexpectedTermination-Flag. | Hosting | Information | 1 |

## <a name="health-reports"></a>Integritätsberichte

Das [Service Fabric-Integritätsmodell](service-fabric-health-introduction.md) bietet eine umfassende, flexible und erweiterbare Integritätsevaluierung und -berichterstellung. Ab der Service Fabric-Version 6.2 werden Integritätsdaten als Plattformereignisse geschrieben, um Verlaufsinformationen für das Integritätsereignis bereitzustellen. Um die Anzahl von Integritätsereignissen gering zu halten, schreiben wir nur Folgendes als Service Fabric-Ereignisse:

* Alle Integritätsberichte für `Error` oder `Warning`
* Integritätsberichte für `Ok` während Übergängen
* Wenn ein `Error`- oder `Warning`-Integritätsereignis abläuft. Damit kann bestimmt werden, wie lange eine Entität fehlerhaft war.

**Ereignisse für Clusterintegritätsberichte**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Ein Integritätsbericht für einen neuen Cluster ist verfügbar. | HM | Information | 1 |
| 54437 | ClusterHealthReportExpired | Ein Integritätsbericht für einen vorhandenen Cluster ist abgelaufen. | HM | Information | 1 |

**Ereignisse für Knotenintegritätsberichte**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Ein Integritätsbericht für einen neuen Knoten ist verfügbar. | HM | Information | 1 |
| 54432 | NodeHealthReportExpired | Ein Integritätsbericht für einen vorhandenen Knoten ist abgelaufen. | HM | Information | 1 |

**Ereignisse für Anwendungsintegritätsberichte**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Ein Integritätsbericht für eine neue Anwendung wurde erstellt. Dies gilt für nicht bereitgestellte Anwendungen. | HM | Information | 1 |
| 54426 | DeployedApplicationNewHealthReport | Ein Integritätsbericht für eine neue bereitgestellte Anwendung wurde erstellt. | HM | Information | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Ein Integritätsbericht für einen neuen bereitgestellten Dienst wurde erstellt. | HM | Information | 1 |
| 54434 | ApplicationHealthReportExpired | Ein Integritätsbericht für eine vorhandene Anwendung ist abgelaufen. | HM | Information | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Ein Integritätsbericht für eine vorhandene bereitgestellte Anwendung ist abgelaufen. | HM | Information | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Ein Integritätsbericht für einen vorhandenen bereitgestellten Dienst ist abgelaufen. | HM | Information | 1 |

**Ereignisse für Dienstintegritätsberichte**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Ein Integritätsbericht für einen neuen Dienst wurde erstellt. | HM | Information | 1 |
| 54433 | ServiceHealthReportExpired | Ein Integritätsbericht für einen vorhandenen Dienst ist abgelaufen. | HM | Information | 1 |

**Ereignisse für Partitionsintegritätsberichte**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Ein Integritätsbericht für eine neue Anwendung wurde erstellt. | HM | Information | 1 |
| 54431 | PartitionHealthReportExpired | Ein Integritätsbericht für eine vorhandene Partition ist abgelaufen. | HM | Information | 1 |

**Ereignisse für Replikatintegritätsberichte**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Ein Integritätsbericht für ein zustandsbehaftetes Replikat wurde erstellt. | HM | Information | 1 |
| 54430 | StatelessInstanceNewHealthReport | Ein Integritätsbericht für ein neues zustandsloses Replikat wurde erstellt. | HM | Information | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Ein Integritätsbericht für ein vorhandenes zustandsbehaftetes Replikat ist abgelaufen. | HM | Information | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Ein Integritätsbericht für eine vorhandene zustandslose Instanz ist abgelaufen. | HM | Information | 1 |

## <a name="chaos-testing-events"></a>Chaostestereignisse 

**Chaossitzungsereignisse**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Eine Chaostestsitzung wurde gestartet. | Prüfbarkeit | Information | 1 |
| 50023 | ChaosStopped | Eine Chaostestsitzung wurde beendet. | Prüfbarkeit | Information | 1 |

**Chaosknotenereignisse**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Der Neustart eines Knoten wurde als Teil einer Chaostestsitzung geplant. | Prüfbarkeit | Information | 1 |
| 50087 | ChaosNodeRestartCompleted | Der Neustart eines Knoten wurde als Teil einer Chaostestsitzung beendet. | Prüfbarkeit | Information | 1 |

**Chaosanwendungsereignisse**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Ein Neustart eines Codepakets wurde während einer Chaostestsitzung geplant. | Prüfbarkeit | Information | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Ein Neustart eines Codepakets wurde während einer Chaostestsitzung abgeschlossen. | Prüfbarkeit | Information | 1 |

**Chaospartitionsereignisse**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Die Verschiebung einer primären Partition wurde als Teil einer Chaostestsitzung geplant. | Prüfbarkeit | Information | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Die Verschiebung einer sekundären Partition wurde als Teil einer Chaostestsitzung geplant. | Prüfbarkeit | Information | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Die Tiefenanalyse der Verschiebung der primären Partition ist verfügbar. | Prüfbarkeit | Information | 1 |

**Chaosreplikatereignisse**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Ein Neustart eines Replikats wurde als Teil einer Chaostestsitzung geplant. | Prüfbarkeit | Information | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Die Entfernung eines Replikats wurde als Teil einer Chaostestsitzung geplant. | Prüfbarkeit | Information | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Die Entfernung eines Replikats wurde als Teil einer Chaostestsitzung abgeschlossen. | Prüfbarkeit | Information | 1 |

## <a name="other-events"></a>Sonstige Ereignisse

**Korrelationsereignisse**

| EventId | NAME | BESCHREIBUNG |Quelle (Aufgabe) | Level | Version |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Eine Korrelation wurde ermittelt. | Prüfbarkeit | Information | 1 |

## <a name="events-prior-to-version-62"></a>Ereignisse vor Version 6.2

Im Anschluss finden Sie eine umfassende Liste mit Ereignissen, die von Service Fabric vor Version 6.2 bereitgestellt wurden:

| EventId | NAME | Quelle (Aufgabe) | Ebene |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Information |
| 25621 | NodeOpenedSuccess | FabricNode | Information |
| 25622 | NodeOpenedFailed | FabricNode | Information |
| 25623 | NodeClosing | FabricNode | Information |
| 25624 | NodeClosed | FabricNode | Information |
| 25625 | NodeAborting | FabricNode | Information |
| 25626 | NodeAborted | FabricNode | Information |
| 29627 | ClusterUpgradeStart | CM | Information |
| 29628 | ClusterUpgradeComplete | CM | Information |
| 29629 | ClusterUpgradeRollback | CM | Information |
| 29630 | ClusterUpgradeRollbackComplete | CM | Information |
| 29631 | ClusterUpgradeDomainComplete | CM | Information |
| 23074 | ContainerActivated | Hosting | Information |
| 23075 | ContainerDeactivated | Hosting | Information |
| 29620 | ApplicationCreated | CM | Information |
| 29621 | ApplicationUpgradeStart | CM | Information |
| 29622 | ApplicationUpgradeComplete | CM | Information |
| 29623 | ApplicationUpgradeRollback | CM | Information |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Information |
| 29625 | ApplicationDeleted | CM | Information |
| 29626 | ApplicationUpgradeDomainComplete | CM | Information |
| 18566 | ServiceCreated | FM | Information |
| 18567 | ServiceDeleted | FM | Information |

## <a name="next-steps"></a>Nächste Schritte

* Verschaffen Sie sich einen Überblick über die [Diagnosen in Service Fabric](service-fabric-diagnostics-overview.md).
* Unter [Übersicht über den EventStore-Dienst in Service Fabric](service-fabric-diagnostics-eventstore.md) erfahren Sie mehr über den EventStore-Dienst.
* Ändern Ihrer [Azure-Diagnose](service-fabric-diagnostics-event-aggregation-wad.md)-Konfiguration zum Sammeln weiterer Protokolle
* [Einrichten von Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) zum Anzeigen der Betriebskanalprotokolle
