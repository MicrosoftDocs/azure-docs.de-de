---
title: Behandeln von Problemen anhand von Berichten zur Systemintegrität
description: Beschreibt die von Komponenten des Azure Fabric-Diensts versendeten Integritätsberichte und ihre Verwendung bei der Behandlung von Problemen mit Clustern oder Anwendungen.
author: georgewallace
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: gwallace
ms.openlocfilehash: 8e60ac5065c2f9543a641daf4f62299c00c61fc8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000656"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>Verwenden von Systemintegritätsberichten für die Problembehandlung
Azure Service Fabric-Komponenten erstellen direkt Integritätsberichte für alle Entitäten im Cluster. Im [Integritätsspeicher](service-fabric-health-introduction.md#health-store) werden Entitäten basierend auf den Systemberichten erstellt und gelöscht. Darüber hinaus werden sie in einer Hierarchie organisiert, in der Interaktionen zwischen den Entitäten erfasst werden.

> [!NOTE]
> Informationen zu integritätsbezogenen Konzepten finden Sie unter [Einführung in die Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md).
> 
> 

Die Systemintegritätsberichte sorgen für Transparenz in Bezug auf die Cluster- und Anwendungsfunktionen und weisen auf Probleme hin. Für Anwendungen und Dienste wird mit den Systemintegritätsberichten überprüft, ob Entitäten implementiert sind und sich aus Sicht von Service Fabric richtig verhalten. Die Berichte umfassen weder die Integritätsüberwachung der Geschäftslogik des Diensts noch die Erkennung von Prozessen, die nicht mehr reagieren. Benutzerdienste können die Integritätsdaten um spezielle Informationen zu ihrer Logik erweitern.

> [!NOTE]
> Die von Watchdogs des Benutzers gesendeten Integritätsberichte werden erst angezeigt, *nachdem* die Systemkomponenten eine Entität erstellt haben. Wenn eine Entität gelöscht wird, werden vom Integritätsspeicher automatisch alle dazugehörigen Integritätsberichte gelöscht. Dies gilt auch, wenn eine neue Instanz der Entität erstellt wird. Ein Beispiel hierfür ist, wenn eine neue zustandsbehaftete, persistente Dienstreplikatinstanz erstellt wird. Alle Berichte, die der alten Instanz zugeordnet sind, werden gelöscht und im Speicher bereinigt.
> 
> 

Die Systemkomponentenberichte werden über die Quelle identifiziert. Diese beginnt mit dem „**System.** “- . Watchdogs können nicht das gleiche Präfix für ihre Quellen verwenden, da Berichte mit ungültigen Parametern abgelehnt werden.

Nun sehen wir uns einige Systemberichte an, um zu verstehen, wodurch sie ausgelöst werden und wie mögliche Probleme behoben werden, die damit verbunden sind.

> [!NOTE]
> Service Fabric fügt weiterhin Berichte für relevante Bedingungen hinzu, die zu einer besseren Transparenz in Bezug auf die Vorgänge im Cluster und in den Anwendungen führen. Vorhandene Berichte können um zusätzliche Details erweitert werden, damit das Problem schneller behoben werden kann.
> 
> 

## <a name="cluster-system-health-reports"></a>Cluster-Systemintegritätsberichte
Die Entität für die Clusterintegrität wird im Integritätsspeicher automatisch erstellt. Wenn alles ordnungsgemäß funktioniert, liegt kein Systembericht vor.

### <a name="neighborhood-loss"></a>Verlust der Nachbarschaft
**System.Federation** meldet einen Fehler, wenn ein Verlust der „Nachbarschaft“ (Neighborhood) erkannt wird. Der Bericht stammt von einzelnen Knoten. Die Knoten-ID wird in den Eigenschaftsnamen eingefügt. Wenn eine Nachbarschaft im gesamten Service Fabric-Ring verloren geht, sind meist zwei Ereignisse zu erwarten, die beide Seiten des Berichts zur Lücke darstellen. Falls weitere Nachbarschaften verloren gehen, ist die Anzahl von Ereignissen höher.

Im Bericht wird das Global Lease-Timeout als Gültigkeitsdauer (Time to Live, TTL) angegeben. Der Bericht wird jeweils zur Hälfte der Gültigkeitsdauer erneut gesendet, solange die Bedingung aktiv ist. Wenn das Ereignis abläuft, wird es automatisch entfernt. Durch dieses Verhalten ist selbst bei einem Ausfall des berichtenden Knotens eine ordnungsgemäße Bereinigung des Integritätsspeichers gewährleistet.

* **SourceID**: System.Federation
* **Property:** Beginnt mit **Neighborhood** und enthält Knoteninformationen.
* **Nächste Schritte:** Untersuchen, warum es zu einem Verlust der Umgebung kommt. Überprüfen Sie beispielsweise die Kommunikation zwischen den Clusterknoten.

### <a name="rebuild"></a>Neu erstellen

Der Dienst Failover-Manager (FM) verwaltet Informationen zu den Clusterknoten. Im Falle eines Datenverlusts im FM sind die Informationen zu den Clusterknoten unter Umständen nicht auf dem neuesten Stand. In diesem Fall wird für das System eine Neuerstellung durchgeführt, und System.FM sammelt Daten von allen Knoten im Cluster, um den Zustand neu zu erstellen. Manchmal kann die Neuerstellung aufgrund von Netzwerk- oder Knotenproblemen hängen bleiben oder angehalten werden. Das gleiche Problem kann beim Dienst „Failover-Manager-Master“ (FMM) auftreten. Der FMM ist ein zustandsloser Systemdienst, der die Position aller FMs im Cluster nachverfolgt. Der Primärknoten eines FMM ist immer der Knoten, dessen ID am nächsten bei 0 liegt. Wird dieser Knoten verworfen, wird eine Neuerstellung ausgelöst.
Im Falle einer dieser Bedingungen weist **System.FM** oder **System.FMM** mithilfe eines Fehlerberichts darauf hin. Die Neuerstellung kann in einer von zwei Phasen hängen bleiben:

* **Warten auf die Übertragung:** FM/FMM wartet auf eine Antwort auf die Broadcastmeldung durch die anderen Knoten.

  * **Nächste Schritte:** Überprüfen Sie, ob ein Netzwerkverbindungsfehler zwischen Knoten aufgetreten ist.
* **Warten auf Knoten**: FM/FMM hat von den anderen Knoten bereits eine Antwort auf die Broadcastmeldung erhalten und wartet auf die Antwort von bestimmten Knoten. Im Integritätsbericht sind die Knoten aufgeführt, auf deren Antwort FM/FMM wartet.
   * **Nächste Schritte:** Überprüfen Sie die Netzwerkverbindung zwischen FM/FMM und den aufgeführten Knoten. Untersuchen Sie alle aufgeführten Knoten auf andere mögliche Probleme.

* **SourceID:** System.FM oder System.FMM
* **Property:** Neuerstellung.
* **Nächste Schritte:** Überprüfen Sie die Netzwerkverbindung zwischen den Knoten sowie den Zustand bestimmter Knoten, die in der Beschreibung des Integritätsberichts aufgeführt sind.

### <a name="seed-node-status"></a>Startknotenstatus
**System.FM** gibt eine Warnung auf Clusterebene aus, wenn einige Startknoten fehlerhaft sind. Startknoten sind Knoten zur Aufrechterhaltung der Verfügbarkeit des zugrunde liegenden Clusters. Diese Knoten gewährleisten die Verfügbarkeit des Clusters durch die Einrichtung von Leases mit anderen Knoten. Darüber hinaus fungieren sie als entscheidendes Element bei bestimmten Arten von Netzwerkausfällen. Falls ein Großteil der Startknoten im Cluster nicht mehr verfügbar ist und nicht wiederhergestellt wird, wird der Cluster automatisch heruntergefahren. 

Ein Startknoten ist fehlerhaft, wenn der Knotenstatus „Down“, „Removed“ oder „Unknown“ lautet.
Im Warnungsbericht für den Status des Startknotens sind alle fehlerhaften Startknoten mit detaillierten Informationen aufgeführt.

* **SourceID:** System.FM
* **Property:** SeedNodeStatus
* **Nächste Schritte:** Wenn diese Warnung im Cluster angezeigt wird, führen Sie die folgenden Anweisungen aus, um das Problem zu beheben: Für Cluster, auf denen Service Fabric-Version 6.5 oder höher ausgeführt wird: Wenn der Service Fabric-Cluster unter Azure ausgeführt wird, versucht Service Fabric, nachdem der Startknoten heruntergefahren ist, ihn automatisch in einen Nicht-Startknoten zu ändern. Stellen Sie dazu sicher, dass die Anzahl der Nicht-Startknoten im primären Knotentyp nicht kleiner ist als die Anzahl der Down-Startknoten. Fügen Sie zu diesem Zweck bei Bedarf weitere Knoten zum primären Knotentyp hinzu.
Je nach den Status des Clusters kann es einige Zeit dauern, das Problem zu beheben. Sobald das Problem behoben ist, wird der Warnungsbericht automatisch gelöscht.

Bei eigenständigen Service Fabric-Clustern müssen zum Löschen des Warnungsberichts alle Startknoten fehlerfrei sind. Je nachdem, warum die Startknoten fehlerhaft sind, müssen verschiedene Maßnahmen ergriffen werden: wenn der Startknoten den Status „Down“ hat, müssen Benutzer den Startknoten starten; wenn der Startknoten den Status „Removed“ oder „Unknown“ hat, muss dieser [aus dem Cluster entfernt werden](./service-fabric-cluster-windows-server-add-remove-nodes.md).
Der Warnungsbericht wird automatisch gelöscht, wenn alle Startknoten fehlerfrei sind.

Für Cluster, auf denen eine niedrigere Service Fabric-Version als 6.5 ausgeführt wird: In diesem Fall muss der Warnungsbericht manuell gelöscht werden. **Benutzer müssen sicherstellen, dass die Startknoten vor dem Löschen des Berichts fehlerfrei sind**: wenn der Startknoten den Status „Down“ hat, müssen Benutzer den Startknoten starten; wenn der Startknoten den Status „Removed“ oder „Unknown“ hat, muss dieser aus dem Cluster entfernt werden.
Wenn alle Startknoten fehlerfrei sind, verwenden Sie den folgenden Befehl aus Powershell, um [den Warnungsbericht zu löschen](/powershell/module/servicefabric/send-servicefabricclusterhealthreport):

```powershell
PS C:\> Send-ServiceFabricClusterHealthReport -SourceId "System.FM" -HealthProperty "SeedNodeStatus" -HealthState OK

## Node system health reports
System.FM, which represents the Failover Manager service, is the authority that manages information about cluster nodes. Each node should have one report from System.FM showing its state. The node entities are removed when the node state is removed. For more information, see [RemoveNodeStateAsync](/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync).

### Node up/down
System.FM reports as OK when the node joins the ring (it's up and running). It reports an error when the node departs the ring (it's down, either for upgrading or simply because it has failed). The health hierarchy built by the health store acts on deployed entities in correlation with System.FM node reports. It considers the node a virtual parent of all deployed entities. The deployed entities on that node are exposed through queries if the node is reported as up by System.FM, with the same instance as the instance associated with the entities. When System.FM reports that the node is down or restarted, as a new instance, the health store automatically cleans up the deployed entities that can exist only on the down node or on the previous instance of the node.

* **SourceId**: System.FM
* **Property**: State.
* **Next steps**: If the node is down for an upgrade, it should come back up after it's been upgraded. In this case, the health state should switch back to OK. If the node doesn't come back or it fails, the problem needs more investigation.

The following example shows the System.FM event with a health state of OK for node up:

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>Zertifikatablauf
**System.FabricNode** gibt eine Warnung aus, wenn vom Knoten verwendete Zertifikate kurz vor dem Ablauf stehen. Es gibt drei Zertifikate pro Knoten: **Certificate_cluster**, **Certificate_server** und **Certificate_default_client**. Wenn das Ablaufdatum noch mindestens zwei Wochen entfernt ist, lautet der Berichtsintegritätsstatus „OK“. Wenn der Ablauf in weniger als zwei Wochen erfolgt, wird als Berichtstyp eine Warnung erstellt. Die Gültigkeitsdauer (TTL) dieser Ereignisse ist unendlich. Sie werden entfernt, wenn ein Knoten den Cluster verlässt.

* **SourceID**: System.FabricNode
* **Property:** Beginnt mit **Certificate** und enthält weitere Informationen zum Zertifikattyp.
* **Nächste Schritte:** Aktualisieren Sie die Zertifikate, wenn sie in Kürze ablaufen.

### <a name="load-capacity-violation"></a>Verletzung der Ladekapazität
Der Service Fabric Load Balancer gibt eine Warnung aus, wenn eine Verletzung einer Knotenkapazität erkannt wird.

* **SourceID**: System.PLB
* **Property:** Beginnt mit **Capacity**.
* **Nächste Schritte:** Überprüfen Sie die bereitgestellten Metriken, und zeigen Sie die aktuelle Kapazität auf dem Knoten an.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>Nichtübereinstimmung der Knotenkapazität für Ressourcenkontrollmetriken
System.Hosting gibt eine Warnung aus, wenn definierte Knotenkapazitäten im Clustermanifest größer als die tatsächlichen Knotenkapazitäten für Ressourcenkontrollmetriken (Arbeitsspeicher und CPU-Kerne) sind. Ein Integritätsbericht wird angezeigt, wenn das erste Dienstpaket, für das die [Ressourcenkontrolle](service-fabric-resource-governance.md) verwendet wird, für einen angegebenen Knoten registriert wird.

* **SourceID**: System.Hosting
* **Property:** **ResourceGovernance**.
* **Nächste Schritte:** Dies kann problematisch sein, weil die Kontrolle von Dienstpaketen nicht wie erwartet erzwungen wird und die [Ressourcenkontrolle](service-fabric-resource-governance.md) nicht richtig funktioniert. Aktualisieren Sie das Clustermanifest mit den richtigen Knotenkapazitäten für diese Metriken, oder geben Sie sie nicht an, und lassen Sie verfügbare Ressourcen von Service Fabric automatisch erkennen.

## <a name="application-system-health-reports"></a>Systemintegritätsberichte für Anwendungen
System.CM steht für den Cluster-Manager-Dienst und ist die Autorität, die die Informationen zu einer Anwendung verwaltet.

### <a name="state"></a>State
System.CM gibt die Meldung „OK“ aus, wenn die Anwendung erstellt oder aktualisiert wurde. Der Integritätsspeicher wird informiert, wenn die Anwendung gelöscht wurde, damit sie aus dem Speicher entfernt werden kann.

* **SourceID**: System.CM
* **Property:** Status.
* **Nächste Schritte:** Wenn die Anwendung erstellt oder aktualisiert wurde, sollte sie den Cluster-Manager-Integritätsbericht enthalten. Überprüfen Sie andernfalls den Status der Anwendung, indem Sie eine Abfrage ausführen. Verwenden Sie beispielsweise das PowerShell-Cmdlet **Get-ServiceFabricApplication -ApplicationName** *applicationName*.

Das folgende Beispiel zeigt das Zustandsereignis für die Anwendung **fabric:/WordCount** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>Dienst-Systemintegritätsberichte
System.FMsteht für den Failover-Manager-Dienst und ist die Autorität, die die Informationen zu Diensten verwaltet.

### <a name="state"></a>State
System.FM gibt die Meldung „OK“ aus, wenn der Dienst erstellt wurde. Die Entität wird aus dem Integritätsspeicher gelöscht, wenn der Dienst gelöscht wurde.

* **SourceID**: System.FM
* **Property:** Status.

Das folgende Beispiel zeigt das Zustandsereignis für den Dienst **fabric:/WordCount/WordCountWebService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>Dienstkorrelationsfehler
**System.PLB** meldet einen Fehler, wenn erkannt wird, dass das Aktualisieren eines Diensts, der mit einem anderen Dienst korreliert werden soll, zu einer Affinitätskette führt. Der Bericht wird gelöscht, wenn erfolgreich ein Update durchgeführt wurde.

* **SourceID**: System.PLB
* **Property:** **ServiceDescription**.
* **Nächste Schritte:** Überprüfen der korrelierten Dienstbeschreibungen.

## <a name="partition-system-health-reports"></a>Systemintegritätsberichte für Partitionen
System.FM steht für den Failover-Manager-Dienst und ist die Autorität, die die Informationen zu den Dienstpartitionen verwaltet.

### <a name="state"></a>State
System.FM gibt die Meldung „OK“ aus, wenn die Partition erstellt wurde und fehlerfrei ist. Die Entität wird aus dem Integritätsspeicher gelöscht, wenn die Partition gelöscht wird.

Wenn die Replikatanzahl der Partition unterhalb des Mindestwerts liegt, wird ein Fehler gemeldet. Wenn der Mindestwert für die Replikatanzahl der Partition erfüllt wird, jedoch nicht die Zielreplikatanzahl, wird eine Warnung ausgegeben. Falls für die Partition ein Quorumverlust vorliegt, gibt System.FM einen Fehler aus.

Zu anderen relevanten Ereignissen zählen unter anderem Warnungen, wenn die Neukonfiguration länger als erwartet dauert und wenn die Erstellung länger als erwartet dauert. Die erwarteten Zeiträume für die Erstellung und Neukonfiguration sind basierend auf Dienstszenarien konfigurierbar. Wenn ein Dienst beispielsweise über Zustandsdaten im Terabytebereich verfügt (z.B. Azure SQL-Datenbank), dauert die Erstellung länger als bei einem Dienst mit einer geringeren Menge an Zustandsdaten.

* **SourceID**: System.FM
* **Property:** Status.
* **Nächste Schritte:** Wenn der Integritätsstatus nicht „OK“ lautet, ist es möglich, dass einige Replikate nicht richtig erstellt, geöffnet oder auf primäre oder sekundäre Elemente heraufgestuft wurden. 

Wenn in der Beschreibung ein Quorumsverlust angegeben ist, hilft es, den detaillierten Integritätsbericht auf inaktive Replikate zu überprüfen und diese wieder zu aktivieren, um so die Partition wieder online zu schalten.

Wenn in der Beschreibung angegeben ist, dass die [Neukonfiguration](service-fabric-concepts-reconfiguration.md) der Partition unterbrochen wurde, liefert der Integritätsbericht über das primäre Replikat zusätzliche Informationen.

Für andere System.FM-Integritätsberichte liegen Berichte über die Replikate, die Partition oder den Dienst aus anderen Systemkomponenten vor. 

In den folgenden Beispielen werden einige dieser Berichte beschrieben. 

Das folgende Beispiel zeigt eine fehlerfreie Partition:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

Das folgende Beispiel zeigt die Integrität einer Partition, bei der die Zielreplikatanzahl nicht erreicht wird. Als nächstes wird die Beschreibung der Partition abgerufen, die zeigt, wie sie konfiguriert ist: **MinReplicaSetSize** ist drei und **TargetReplicaSetSize** ist sieben. Geben Sie anschließend die Anzahl der Knoten im Cluster an, d.h. in diesem Fall „5“. In diesem Fall können zwei Replikate also nicht platziert werden, da die Zielanzahl von Replikaten höher ist als die Anzahl verfügbarer Knoten.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

Das folgende Beispiel zeigt die Integrität einer Partition, deren Neukonfiguration unterbrochen wurde, weil der Benutzer das Abbruchtoken in der **RunAsync**-Methode nicht berücksichtigt hat. Die Untersuchung des Integritätsberichts auf Replikate, die als primär (P) markiert sind, kann helfen, das Problem näher zu umreißen.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
Dieser Integritätsbericht zeigt den Zustand der Replikate der Partition, für die aktuell eine Neukonfiguration durchgeführt wird: 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

Für jedes Replikat enthält der Integritätsbericht Folgendes:
- Vorherige Konfigurationsrolle
- Aktuelle Konfigurationsrolle
- [Replikatzustand](service-fabric-concepts-replica-lifecycle.md)
- Knoten, auf dem das Replikat ausgeführt wird
- Replikat-ID

In einem wie in dem Beispiel gezeigten Fall sind weitere Untersuchungen erforderlich. Untersuchen Sie die Integrität von jedem einzelnen Replikat, und beginnen Sie dabei mit den Replikaten, die im vorherigen Beispiel mit `Primary` und `Secondary` (131482789658160654 und 131482789688598467) gekennzeichnet sind.

### <a name="replica-constraint-violation"></a>Verletzung der Replikateinschränkung
**System.PLB** gibt eine Warnung aus, wenn eine Verletzung der Replikateinschränkung erkannt wird und nicht alle Partitionsreplikate platziert werden können. Den Berichtsdetails kann entnommen werden, welche Einschränkungen und Eigenschaften die Replikatplatzierung verhindern.

* **SourceID**: System.PLB
* **Property:** Beginnt mit **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Systemintegritätsberichte für Replikate
**System.RA** steht für die Reconfiguration Agent-Komponente und ist die Autorität für den Replikatzustand.

### <a name="state"></a>State
System.RA gibt „OK“ aus, wenn das Replikat erstellt wurde.

* **SourceID**: System.RA
* **Property:** Status.

Das folgende Beispiel zeigt ein fehlerfreies Replikat:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
Mit dieser Eigenschaft werden Warnungen oder Fehler angegeben, wenn versucht wird, ein Replikat zu öffnen oder zu schließen oder die Rolle eines Replikats zu ändern. Weitere Informationen finden Sie unter [Replikatlebenszyklus](service-fabric-concepts-replica-lifecycle.md). Bei den Fehlern kann es sich um Ausnahmen handeln, die von den API-Aufrufen oder Abstürzen des Diensthostprozesses während dieser Zeit ausgelöst werden. Bei Fehlern aufgrund von API-Aufrufen aus dem C#-Code fügt Service Fabric die Ausnahme und Stapelüberwachung dem Integritätsbericht hinzu.

Diese Integritätswarnungen werden ausgelöst, nachdem die Aktion einige Male (Anzahl abhängig von der Richtlinie) lokal wiederholt wurde. Service Fabric wiederholt die Aktion bis zum maximalen Schwellenwert. Wenn der maximale Schwellenwert erreicht wird, wird unter Umständen versucht, dies zu korrigieren. Dieser Versuch kann dazu führen, dass diese Warnungen gelöscht werden, da die Aktion auf diesem Knoten abgebrochen wird. Beispiel: Wenn ein Replikat auf einem Knoten nicht geöffnet werden kann, löst Service Fabric eine Integritätswarnung aus. Wenn das Replikat auch weiterhin nicht geöffnet werden kann, führt Service Fabric eine Selbstreparatur aus. Bei dieser Aktion wird eventuell versucht, denselben Vorgang auf einem anderen Knoten auszuführen. Dieser Versuch führt dazu, dass die für dieses Replikat ausgelöste Warnung gelöscht wird. 

* **SourceID**: System.RA
* **Property:** **ReplicaOpenStatus**, **ReplicaCloseStatus** und **ReplicaChangeRoleStatus**.
* **Nächste Schritte:** Untersuchen Sie den Dienstcode oder die Absturzabbilder, um zu ermitteln, warum der Vorgang nicht durchgeführt werden kann.

Das folgende Beispiel zeigt die Integrität eines Replikats, das `TargetInvocationException` für seine open-Methode auslöst. Die Beschreibung enthält die Fehlerquelle (**IStatefulServiceReplica.Open**), den Ausnahmetyp (**TargetInvocationException**) und die Stapelüberwachung.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

Das folgende Beispiel zeigt ein Replikat, das beim Schließen ständig abstürzt:

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguration
Diese Eigenschaft wird verwendet, um anzugeben, wenn ein Replikat, das eine [Neukonfiguration](service-fabric-concepts-reconfiguration.md) durchführt, erkennt, dass die Neukonfiguration angehalten wurde oder hängen geblieben ist. Dieser Integritätsbericht enthält das Replikat, das aktuell eine primäre Rolle aufweist (außer im Fall einer Auslagerungsneukonfiguration primärer Replikate, bei dem er das Replikat enthalten kann, das von der primären Rolle auf die aktive sekundäre Rolle tiefer gestuft wird).

Die Neukonfiguration kann aus folgenden Gründen unterbrochen sein:

- Eine Aktion für das lokale Replikat (das gleiche Replikat, das auch die Neukonfiguration durchführt) wird nicht abgeschlossen. In diesem Fall kann die Untersuchung der Integritätsberichte für dieses Replikat aus anderen Komponenten (System.RAP oder System.RE) zusätzliche Informationen liefern.

- Eine Aktion wird für ein Remotereplikat nicht abgeschlossen. Replikate, für die Aktionen ausstehen, werden im Integritätsbericht aufgelistet. Diese Remotereplikate sollten in den Integritätsberichten weiter untersucht werden. Es können auch Kommunikationsprobleme zwischen dem Knoten und dem Remoteknoten vorliegen.

In seltenen Fällen ist die Neukonfiguration möglicherweise aufgrund von Kommunikations- oder anderen Problemen zwischen dem Knoten und dem Failover-Manager-Dienst unterbrochen.

* **SourceID**: System.RA
* **Property:** Neukonfiguration.
* **Nächste Schritte:** Untersuchen Sie abhängig von der Beschreibung im Integritätsbericht die lokalen oder Remotereplikate.

Das folgende Beispiel zeigt einen Integritätsbericht, bei dem eine Neukonfiguration für das lokale Replikat unterbrochen ist. In diesem Beispiel liegt dies daran, dass ein Dienst den Abbruchtoken nicht berücksichtigt.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

Das folgende Beispiel zeigt einen Integritätsbericht, bei dem eine Neukonfiguration unterbrochen wurde, da auf eine Antwort von zwei Remotereplikaten gewartet wird. In diesem Beispiel sind drei Replikate in der Partition, einschließlich des aktuellen primären Replikats, enthalten. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

Der folgende Integritätsbericht zeigt, dass die Neukonfiguration unterbrochen wurde und auf eine Antwort von zwei Replikaten wartet: 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

Für jedes Replikat sind die folgenden Informationen angegeben:
- Vorherige Konfigurationsrolle
- Aktuelle Konfigurationsrolle
- [Replikatzustand](service-fabric-concepts-replica-lifecycle.md)
- Knoten-ID
- Replikat-ID

So wird die Unterbrechung der Neukonfiguration aufgehoben
- Die **down**-Replikate müssen wieder aktiviert werden. 
- Die **inbuild**-Replikate müssen die Erstellung abschließen und in den Zustand „Bereit“ übergehen.

### <a name="slow-service-api-call"></a>Langsamer Dienst-API-Aufruf
**System.RAP** und **System.Replicator** geben eine Warnung aus, wenn ein Aufruf des Benutzerdienstcodes länger dauert als in der Konfiguration angegeben. Die Warnung wird gelöscht, wenn der Aufruf abgeschlossen ist.

* **SourceID**: System.RAP oder System.Replicator
* **Property:** Name der langsamen API. Die Beschreibung liefert weitere Details dazu, wie lange die API bereits aussteht.
* **Nächste Schritte:** Untersuchen Sie, warum der Aufruf länger dauert als erwartet.

Das folgende Beispiel zeigt das Integritätsereignis aus System.RAP für einen zuverlässigen Dienst, der das Abbruchtoken in **RunAsync** nicht berücksichtigt:

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

Die Eigenschaft und der Text geben an, welche API nicht ausgeführt wurde. Die nächsten auszuführenden Schritte hängen von den jeweiligen nicht ausgeführten APIs ab. Jede API in *IStatefulServiceReplica* oder *IStatelessServiceInstance* ist normalerweise ein Fehler im Dienstcode. Im folgenden Abschnitt wird beschrieben, wie diese in das [Reliable Services-Modell](service-fabric-reliable-services-lifecycle.md) übertragen werden:

- **IStatefulServiceReplica.Open**: Diese Warnung gibt an, dass ein Aufruf von `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`, oder sofern überschrieben, von `OnOpenAsync` unterbrochen ist.

- **IStatefulServiceReplica.Close** und **IStatefulServiceReplica.Abort**: Der häufigste Fall ist ein Dienst, der das in `RunAsync` übergebene Abbruchtoken nicht berücksichtigt. Es kann auch sein, dass `ICommunicationListener.CloseAsync` oder sofern überschrieben `OnCloseAsync` unterbrochen ist.

- **IStatefulServiceReplica.ChangeRole(S)** und **IStatefulServiceReplica.ChangeRole(N)** : Der häufigste Fall ist ein Dienst, der das in `RunAsync` übergebene Abbruchtoken nicht berücksichtigt. In diesem Szenario besteht die beste Lösung im Neustart des Replikats.

- **IStatefulServiceReplica.ChangeRole(P)** : Am häufigsten hat der Dienst keine Aufgabe von `RunAsync` zurückgegeben.

Andere API-Aufrufe, die unterbrochen werden können, befinden sich in der **IReplicator**-Schnittstelle. Beispiel:

- **IReplicator.CatchupReplicaSet:** Diese Warnung gibt eines von zwei Dingen an. Es sind nicht ausreichend aktive Replikate vorhanden. Dies kann durch Prüfen des Replikatzustands der Replikate in der Partition oder im System.FM-Integritätsbericht für eine unterbrochene Neukonfiguration ermittelt werden. oder die Replikate bestätigen Vorgänge nicht. Mit dem PowerShell-Cmdlet `Get-ServiceFabricDeployedReplicaDetail` kann der Zustand aller Replikate ermittelt werden. Das Problem liegt bei den Replikaten, deren `LastAppliedReplicationSequenceNumber`-Wert unter dem `CommittedSequenceNumber`-Wert des primären Replikats liegt.

- **IReplicator.BuildReplica(\<Remote ReplicaId>)** : Diese Warnung weist auf ein Problem im Buildprozess hin. Weitere Informationen finden Sie unter [Replikatlebenszyklus](service-fabric-concepts-replica-lifecycle.md). Dies ist möglicherweise auf eine Fehlkonfiguration der Replikationsadresse zurückzuführen. Entsprechende Informationen finden Sie unter [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md) und [Angeben von Ressourcen in einem Dienstmanifest](service-fabric-service-manifest-resources.md). Ursache kann auch ein Problem mit dem Remoteknoten sein.

### <a name="replicator-system-health-reports"></a>Systemintegritätsberichte für Replikatoren
**Replikationswarteschlange ist voll:** 
**System.Replicator** gibt eine Warnung aus, wenn die Replikationswarteschlange voll ist. Beim primären Replikat ist die Replikationswarteschlange in der Regel voll, weil sekundäre Replikate beim Bestätigen von Vorgängen sehr langsam sind. Beim sekundären Element geschieht dies gewöhnlich, wenn der Dienst langsam beim Anwenden der Vorgänge ist. Die Warnung wird gelöscht, wenn die Warteschlange nicht mehr voll ist.

* **SourceID**: System.Replicator
* **Property:** **PrimaryReplicationQueueStatus** oder **SecondaryReplicationQueueStatus**, je nach Replikatrolle.
* **Nächste Schritte:** Gilt der Bericht für das primäre Element, überprüfen Sie die Verbindung zwischen den Knoten im Cluster. Wenn alle Verbindungen fehlerfrei sind, kann mindestens ein langsames sekundäres Element mit hoher Datenträgerwartezeit für die Anwendung auf Vorgänge vorhanden sein. Falls sich der Bericht auf dem sekundären Replikat befindet, sollten Sie zuerst die Datenträgerauslastung und -leistung auf dem Knoten überprüfen. Überprüfen Sie anschließend die ausgehende Verbindung vom langsamen Knoten zum primären Replikat.

**RemoteReplicatorConnectionStatus:** 
**System.Replicator** für das primäre Replikat gibt eine Warnung aus, wenn bei der Verbindung mit einem sekundären Replikator (Remotereplikator) Fehler auftreten. Die Adresse des Remotereplikators wird in der Meldung des Berichts angezeigt. Dadurch kann einfacher erkannt werden, ob die falsche Konfiguration übergeben wurde oder ob Netzwerkfehler zwischen den Replikatoren auftreten.

* **SourceID**: System.Replicator
* **Property:** **RemoteReplicatorConnectionStatus**.
* **Nächste Schritte:** Überprüfen Sie die Fehlermeldung, und stellen Sie sicher, dass die Adresse des Remotereplikators richtig konfiguriert ist. Wenn der Remotereplikator beispielsweise mit der Überwachungsadresse „localhost“ beginnt, ist er von außen nicht erreichbar. Wenn die Adresse korrekt aussieht, sollten Sie die Verbindung zwischen dem primären Knoten und der Remoteadresse überprüfen, um mögliche Netzwerkfehler zu ermitteln.

### <a name="replication-queue-full"></a>Replikationswarteschlange ist voll
**System.Replicator** gibt eine Warnung aus, wenn die Replikationswarteschlange voll ist. Beim primären Replikat ist die Replikationswarteschlange in der Regel voll, weil sekundäre Replikate beim Bestätigen von Vorgängen sehr langsam sind. Beim sekundären Element geschieht dies gewöhnlich, wenn der Dienst langsam beim Anwenden der Vorgänge ist. Die Warnung wird gelöscht, wenn die Warteschlange nicht mehr voll ist.

* **SourceID**: System.Replicator
* **Property:** **PrimaryReplicationQueueStatus** oder **SecondaryReplicationQueueStatus**, je nach Replikatrolle.

### <a name="slow-naming-operations"></a>Langsame Naming-Vorgänge
**System.NamingService** liefert Informationen zur Integrität des entsprechenden primären Replikats, wenn ein Benennungsvorgang zu lange dauert. Beispiele für Naming-Vorgänge sind [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) und [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync). Weitere Methoden finden Sie unter „FabricClient“. Beispielsweise unter [Dienstverwaltungsmethoden](/dotnet/api/system.fabric.fabricclient.servicemanagementclient) oder [Eigenschaftsverwaltungsmethoden](/dotnet/api/system.fabric.fabricclient.propertymanagementclient).

> [!NOTE]
> Der Naming-Dienst löst die Dienstnamen in einen Speicherort im Cluster auf. Er ermöglicht es Benutzern, Dienstnamen und -eigenschaften zu verwalten. Hierbei handelt es sich um einen partitionierten, persistenten Service Fabric-Dienst. Eine der Partitionen stellt den *Autoritätsbesitzer* dar, der Metadaten zu allen Service Fabric-Namen und -Diensten enthält. Die Service Fabric-Namen werden verschiedenen Partitionen (so genannten *Namensbesitzer*-Partitionen) zugeordnet, um die Erweiterung des Diensts zu ermöglichen. Weitere Informationen finden Sie unter [Service Fabric-Architektur](service-fabric-architecture.md).
> 
> 

Wenn ein Benennungsvorgang unerwartet lang dauert, wird der Vorgang im primären Replikat der Benennungsdienstpartition, die den Vorgang abwickelt, mit einem Warnungsbericht gekennzeichnet. Ist der Vorgang erfolgreich, wird die Warnung gelöscht. Wird der Vorgang mit einem Fehler abgeschlossen, enthält der Integritätsbericht Einzelheiten zu dem Fehler.

* **SourceID**: System.NamingService
* **Property:** Beginnt mit dem Präfix „**Duration_** “ und identifiziert den langsamen Vorgang und den Service Fabric-Namen, auf den der Vorgang angewendet wird. Beispiel: Wenn die Diensterstellung für den Namen **fabric:/MyApp/MyService** zu lang dauert, lautet die Eigenschaft **Duration_AOCreateService.fabric:/MyApp/MyService**. „AO“ verweist auf die Rolle der Namenspartition für diesen Namen und Vorgang.
* **Nächste Schritte:** Überprüfen Sie, warum der Benennungsvorgang nicht erfolgreich ist. Bei jedem Vorgang können andere Ursachen vorliegen. So kann beispielsweise der Befehl zum Löschen des Diensts unterbrochen sein. Dies kann daran liegen, dass der Anwendungshost auf einem Knoten aufgrund eines Benutzerfehlers im Dienstcode immer wieder abstürzt.

Im Anschluss sehen Sie ein Beispiel für einen Diensterstellungsvorgang. Der Vorgang dauerte länger als in der Konfiguration festgelegt. „AO“ wiederholt den Vorgang und sendet Arbeit an „NO“. „NO“ hat den letzten Vorgang mit TIMEOUT abgeschlossen. In diesem Fall wird sowohl für die „AO“- als auch für die „NO“-Rolle das gleiche Replikat als primäres Replikat verwendet.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication-Systemintegritätsberichte
**System.Hosting** ist die Autorität für bereitgestellte Entitäten.

### <a name="activation"></a>Aktivierung
System.Hosting gibt die Meldung „OK“ aus, wenn eine Anwendung auf dem Knoten aktiviert wurde. Andernfalls wird ein Fehler gemeldet.

* **SourceID**: System.Hosting
* **Property:** **Activation**, einschließlich der Rolloutversion.
* **Nächste Schritte:** Falls die Anwendung fehlerhaft ist, untersuchen Sie, warum die Aktivierung nicht erfolgreich war.

Das folgende Beispiel zeigt eine erfolgreiche Aktivierung:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Download
System.Hosting meldet einen Fehler, wenn das Herunterladen des Anwendungspakets nicht erfolgreich war.

* **SourceID**: System.Hosting
* **Property:** **Download**, einschließlich der Rolloutversion.
* **Nächste Schritte:** Untersuchen Sie, warum das Herunterladen auf dem Knoten nicht erfolgreich war.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage-Systemintegritätsberichte
**System.Hosting** ist die Autorität für bereitgestellte Entitäten.

### <a name="service-package-activation"></a>Aktivierung des Dienstpakets
System.Hosting gibt die Meldung „OK“ aus, wenn die Aktivierung des Dienstpakets auf dem Knoten erfolgreich ist. Andernfalls wird ein Fehler gemeldet.

* **SourceID**: System.Hosting
* **Property:** Aktivierung.
* **Nächste Schritte:** Untersuchen Sie, warum die Aktivierung nicht erfolgreich war.

### <a name="code-package-activation"></a>Aktivierung des Codepakets
System.Hosting gibt für jedes Codepaket die Meldung „OK“ aus, wenn die Aktivierung erfolgreich ist. Wenn die Aktivierung nicht erfolgreich ist, wird gemäß Konfiguration eine Warnung ausgegeben. Wenn **CodePackage** nicht aktiviert werden kann oder mit einem Fehler beendet wird, der über den unter **CodePackageHealthErrorThreshold** konfigurierten Wert hinausgeht, meldet Hosting einen Fehler. Wenn ein Dienstpaket mehrere Codepakete enthält, wird jeweils ein eigener Aktivierungsbericht erstellt.

* **SourceID**: System.Hosting
* **Property:** Verwendet das Präfix **CodePackageActivation** und enthält den Namen des Codepakets sowie den Einstiegspunkt im Format *CodePackageActivation:CodePackageName:SetupEntryPoint/EntryPoint*. Beispiel: **CodePackageActivation:Code:SetupEntryPoint**.

### <a name="service-type-registration"></a>Diensttypregistrierung
System.Hosting gibt die Meldung „OK“ aus, wenn der Diensttyp erfolgreich registriert wurde. Wenn die Registrierung nicht in der vorgegebenen Zeit erfolgt ist (gemäß Konfiguration mit **ServiceTypeRegistrationTimeout**), wird ein Fehler gemeldet. Nach Abschluss der Laufzeit wird die Registrierung des Diensttyps beim Knoten aufgehoben, und das Hosting gibt eine Warnung aus.

* **SourceID**: System.Hosting
* **Property:** Verwendet das Präfix **ServiceTypeRegistration** und enthält den Namen des Diensttyps. Beispiel: **ServiceTypeRegistration:FileStoreServiceType**.

Das folgende Beispiel zeigt ein fehlerfreies bereitgestelltes Dienstpaket:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>Download
System.Hosting meldet einen Fehler, wenn das Herunterladen des Dienstpakets nicht erfolgreich war.

* **SourceID**: System.Hosting
* **Property:** **Download**, einschließlich der Rolloutversion.
* **Nächste Schritte:** Untersuchen Sie, warum das Herunterladen auf dem Knoten nicht erfolgreich war.

### <a name="upgrade-validation"></a>Upgradeüberprüfung
System.Hosting meldet einen Fehler, wenn die Überprüfung während des Upgrades nicht erfolgreich war oder wenn das Upgrade auf dem Knoten fehlerhaft ist.

* **SourceID**: System.Hosting
* **Property:** Verwendet das Präfix **FabricUpgradeValidation** und enthält die Upgradeversion.
* **Beschreibung**: Verweist auf den aufgetretenen Fehler.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>Nicht definierte Kapazität des Knotens für Ressourcenkontrollmetriken
System.Hosting gibt eine Warnung aus, wenn im Clustermanifest keine Knotenkapazitäten definiert sind und die Konfiguration für die automatische Erkennung deaktiviert ist. Service Fabric löst eine Integritätswarnung aus, wenn ein Dienstpaket, für das die [Ressourcenkontrolle](service-fabric-resource-governance.md) verwendet wird, für einen angegebenen Knoten registriert wird.

* **SourceID**: System.Hosting
* **Property:** **ResourceGovernance**.
* **Nächste Schritte:** Die bevorzugte Methode für die Umgehung dieses Problems besteht darin, das Clustermanifest so zu ändern, dass die automatische Erkennung der verfügbaren Ressourcen aktiviert wird. Eine andere Möglichkeit besteht im Aktualisieren des Clustermanifests mit richtig angegebenen Knotenkapazitäten für diese Metriken.

## <a name="next-steps"></a>Nächste Schritte
* [Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

* [Melden und Überprüfen der Dienstintegrität](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
