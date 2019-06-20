---
title: Notfallwiederherstellung in Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric bietet Funktionalität für den Umgang mit allen Arten von Notfällen. In diesem Artikel werden die Arten von Notfällen, die auftreten können, und entsprechende Vorgehensweisen beschrieben.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7153a6ed4a91e59eea936f1e17d827a40bb99371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60948505"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Notfallwiederherstellung in Azure Service Fabric
Zur Gewährleistung von hoher Verfügbarkeit muss unter anderem sichergestellt werden, dass Dienste verschiedenste Arten von Ausfällen überstehen können. Dies ist besonders wichtig bei Ausfällen, die überraschend auftreten oder sich Ihrer Kontrolle entziehen. In diesem Artikel werden einige allgemeine Ausfälle beschrieben, die sich als äußerst problematisch erweisen können, wenn sie nicht angemessen im Modell berücksichtigt und behandelt werden. Darüber hinaus enthält der Artikel Informationen zu Abhilfen und Maßnahmen, die Sie ergreifen können, wenn dennoch ein Notfall eintritt. Dadurch soll das Risiko von Ausfallzeiten oder Datenverlusten im Falle von geplanten oder anderweitigen Ausfällen möglichst gering gehalten oder ganz beseitigt werden.

## <a name="avoiding-disaster"></a>Vermeiden von Notfällen
Das Hauptziel von Service Fabric besteht darin, Sie dabei zu unterstützen, Ihre Umgebung und Ihre Dienste so zu gestalten, dass sich allgemeine Ausfälle nicht zu einem Notfall entwickeln. 

Grundsätzlich kann zwischen zwei Arten von Notfall-/Ausfallszenarien unterschieden werden:

1. Hardware- und Softwarefehler
2. Vorgangsbedingte Fehler

### <a name="hardware-and-software-faults"></a>Hardware- und Softwarefehler
Hardware- und Softwarefehler sind unberechenbar. Sie lassen sich am einfachsten kompensieren, indem mehrere Kopien des Diensts über die Fehlergrenzen von Hardware oder Software hinweg ausgeführt werden. Wenn der Dienst beispielsweise nur auf einem bestimmten Computer ausgeführt wird, ist der Ausfall dieses Computers ein schwerwiegendes Problem für diesen Dienst. Dieses Problem lässt sich am einfachsten vermeiden, indem der Dienst auf mehreren Computern ausgeführt wird. Darüber hinaus muss mithilfe von Tests sichergestellt werden, dass der Ausfall eines der Computer die Dienstausführung nicht beeinträchtigt. Die Kapazitätsplanung stellt sicher, dass an anderer Stelle eine Ersatzinstanz erstellt werden kann und dass die Verringerung der Kapazität keine Überlastung der übrigen Dienste zur Folge hat. Das gleiche Muster kann auch zur Vermeidung aller anderen Arten von Ausfällen verwendet werden. Beispiel: Wenn Sie sich Sorgen um den Ausfall eines SANs machen, nutzen Sie mehrere SANs. Wenn Sie sich Sorgen um den Verlust eines Serverracks machen, nutzen Sie mehrere Serverracks. Wenn Sie sich Sorgen um den Verlust von Datencentern machen, empfiehlt es sich, den Dienst in mehreren Azure-Regionen oder Datencentern auszuführen. 

In diesem übergreifenden Modus können zwar immer noch bestimmte Arten von gleichzeitigen Ausfällen auftreten, einzelne und sogar mehrere Ausfälle eines bestimmten Typs (etwa der Ausfall eines einzelnen virtuellen Computers oder einer Netzwerkverbindung) werden jedoch automatisch behandelt und stellen daher keinen Notfall mehr dar. Service Fabric bietet viele Mechanismen zur Erweiterung des Clusters und kümmert sich um die Wiederherstellung ausgefallener Knoten und Dienste. Darüber hinaus ermöglicht Service Fabric das Ausführen mehrerer Instanzen Ihrer Dienste, um zu vermeiden, dass sich diese Arten von ungeplanten Ausfällen zu einem echten Notfall entwickeln.

Manchmal ist es jedoch möglicherweise aus diversen Gründen nicht sinnvoll, eine Bereitstellung zu verwenden, die groß genug ist, um Ausfälle zu überbrücken. Beispielsweise kann die Menge an benötigten Hardwareressourcen so groß und das Risiko für einen Ausfall so gering sein, dass sich die Ausgaben für die Ressourcen nicht lohnen. Im Zusammenhang mit verteilten Anwendungen führen zusätzliche Kommunikations-Hops oder Zustandsreplikationen über geografische Entfernungen hinweg möglicherweise zu nicht akzeptablen Verzögerungen. Diese Grenze ist jeweils anwendungsspezifisch. Bei Softwarefehlern kann der Fehler auch auf den Dienst zurückzuführen sein, den Sie skalieren möchten. In diesem Fall lässt sich ein Notfall nicht durch mehrere Kopien verhindern, da die Fehlerbedingung in allen Instanzen vorhanden ist.

### <a name="operational-faults"></a>Vorgangsbedingte Fehler
Selbst wenn Ihr Dienst über den gesamten Globus verteilt und durch mehrere Redundanzen abgesichert ist, kann es noch zu einem Notfall kommen. So kann beispielsweise jemand versehentlich die Konfiguration des DNS-Namens für den Dienst ändern oder sogar den gesamten Dienst löschen. Angenommen, Sie verfügen über einen zustandsbehafteten Service Fabric-Dienst, und jemand löscht diesen Dienst versehentlich. Sofern keine andere risikomindernde Maßnahme ergriffen wurde, geht dadurch der Dienst mitsamt seinen Zustandsinformationen verloren. Diese Arten von vorgangsbedingten Notfällen („Hoppla“) erfordern andere risikomindernde Maßnahmen und Wiederherstellungsschritte als reguläre ungeplante Ausfälle. 

Sie lassen sich am besten wie folgt vermeiden:
1. Operativen Zugriff auf die Umgebung einschränken
2. Gefährliche Vorgänge streng überwachen
3. Automatisierung erzwingen, manuelle Änderungen oder Out-of-Band-Änderungen verhindern sowie bestimmte Umgebungsänderungen vor deren Anwendung überprüfen
4. Sicherstellen, dass destruktive Vorgänge vorläufig sind. Vorläufige Vorgänge werden nicht sofort wirksam oder können innerhalb eines bestimmten Zeitfensters rückgängig gemacht werden.

Service Fabric bietet einige Mechanismen zur Verhinderung vorgangsbedingter Fehler – unter anderem eine [rollenbasierte](service-fabric-cluster-security-roles.md) Zugriffssteuerung für Clustervorgänge. Die meisten dieser vorgangsbedingten Fehler lassen sich jedoch nur mit organisatorischen Maßnahmen und anderen Systemen vermeiden. Service Fabric bietet Mechanismen zur Bewältigung vorgangsbedingter Fehler. Hierzu zählen insbesondere die Sicherung und Wiederherstellung für zustandsbehaftete Dienste.

## <a name="managing-failures"></a>Bewältigen von Ausfällen
Das Ziel von Service Fabric ist fast immer die automatische Bewältigung von Ausfällen. Zur Bewältigung einiger Ausfallarten müssen Dienste jedoch über zusätzlichen Code verfügen. Andere Arten von Ausfällen sollten hingegen aus Sicherheitsgründen sowie zur Gewährleistung der Geschäftskontinuität _nicht_ automatisch behandelt werden. 

### <a name="handling-single-failures"></a>Behandeln einzelner Ausfälle
Einzelne Computer können aus verschiedensten Gründen ausfallen. Manche Ausfälle sind auf Hardwareprobleme wie etwa den Ausfall des Netzteils oder der Netzwerkhardware zurückzuführen. Andere sind softwarebedingt. Hierzu zählt etwa der Ausfall des Betriebssystems oder des eigentlichen Diensts. Service Fabric erkennt diese Arten von Ausfällen automatisch – einschließlich Fälle, in denen der Computer aufgrund von Netzwerkproblemen nicht mehr mit anderen Computern kommunizieren kann.

Die Ausführung einer einzelnen Dienstinstanz führt unabhängig von der Art des Diensts zu Ausfallzeiten für diesen Dienst, wenn bei dieser einzelnen Kopie des Codes aus irgendeinem Grund ein Fehler auftritt. 

Einen einzelnen Ausfall können Sie am einfachsten behandeln, indem Sie dafür sorgen, dass Ihre Dienste standardmäßig auf mehreren Knoten ausgeführt werden. Bei zustandslosen Diensten kann dies durch einen `InstanceCount`-Wert größer 1 erreicht werden. Bei zustandsbehafteten Diensten wird für `TargetReplicaSetSize` und `MinReplicaSetSize` mindestens der Wert 3 empfohlen. Durch die Ausführung mehrerer Kopien des Dienstcodes wird sichergestellt, dass der Dienst automatisch mit einem einzelnen Ausfall zurechtkommt. 

### <a name="handling-coordinated-failures"></a>Behandeln koordinierter Ausfälle
Koordinierte Ausfälle können in einem Cluster aufgrund von geplanten oder ungeplanten Infrastrukturausfällen und -änderungen sowie aufgrund von geplanten Softwareänderungen auftreten. Service Fabric modelliert Infrastrukturzonen, in denen koordinierte Ausfälle auftreten, als Fehlerdomänen. Bereiche, in denen koordinierte Softwareänderungen auftreten, werden als Upgradedomänen modelliert. Weitere Informationen zu Fehler- und Upgradedomänen finden Sie in [diesem Dokument](service-fabric-cluster-resource-manager-cluster-description.md) zur Clustertopologie und Definition.

Fehler- und Upgradedomänen werden von Service Fabric bei der Planung des Ausführungsorts Ihrer Dienste standardmäßig berücksichtigt. Service Fabric versucht standardmäßig, Ihre Dienste über mehrere Fehler- und Upgradedomänen hinweg auszuführen, um im Falle von geplanten oder ungeplanten Änderungen die Verfügbarkeit Ihrer Dienste zu gewährleisten. 

Ein Beispiel: Angenommen, aufgrund eines defekten Netzteils fallen mehrere Computer eines Racks gleichzeitig aus. Wenn mehrere Kopien des Diensts ausgeführt werden, ist der Verlust mehrerer Computer in der ausgefallenen Fehlerdomäne nur ein weiteres Beispiel für einen einzelnen Ausfall eines bestimmten Diensts. Aus diesem Grund ist die Verwaltung von Fehlerdomänen so wichtig, um die Hochverfügbarkeit Ihrer Dienste zu gewährleisten. Wenn Service Fabric in Azure ausgeführt wird, erfolgt die Verwaltung von Fehlerdomänen automatisch. In anderen Umgebungen ist das unter Umständen nicht der Fall. Wenn Sie Ihre eigenen Cluster lokal erstellen, achten Sie auf eine korrekte Zuordnung und Planung des Fehlerdomänenlayouts.

Upgradedomänen eignen sich für die Modellierung von Bereichen mit gleichzeitigen Softwareupgrades. Daher definieren Upgradedomänen häufig auch die Bereiche, in denen Software während geplanter Upgrades offline geschaltet wird. Upgrades für Service Fabric und Ihre Dienste folgen dem gleichen Modell. Weitere Informationen zu parallelen Upgrades, Upgradedomänen und dem Service Fabric-Integritätsmodell, mit dem unter anderem verhindert wird, dass unbeabsichtigte Änderungen den Cluster und den Dienst beeinträchtigen, finden Sie in den folgenden Dokumenten:

 - [Anwendungsupgrade](service-fabric-application-upgrade.md)
 - [Tutorial für das Upgraden von Service Fabric-Anwendungen mithilfe von Visual Studio](service-fabric-application-upgrade-tutorial.md)
 - [Einführung in die Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md)

Mithilfe der von [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)bereitgestellten Clusterzuweisung können Sie das Layout Ihres Clusters visualisieren:

<center>

![Auf Fehlerdomänen verteilte Knoten in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Die Modellierung von Ausfallbereichen, parallele Upgrades, die Ausführung mehrerer Instanzen Ihres Dienstcodes und -zustands, Platzierungsregeln, die sicherstellen, dass Ihre Dienste über Fehler- und Upgradedomänen hinweg ausgeführt werden, sowie die integrierte Integritätsüberwachung sind nur **einige** der Features, mit denen Service Fabric dafür sorgt, dass sich normale Betriebsprobleme und Ausfälle nicht zu Notfällen entwickeln. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Behandeln gleichzeitiger Hardware- oder Softwareausfälle
Weiter oben haben wir uns mit einzelnen Ausfällen beschäftigt. Wie Sie sehen, können Sie diese sowohl bei zustandslosen als auch bei zustandsbehafteten Diensten recht einfach behandeln, indem Sie mehrere Kopien des Codes (und des Zustands) über Fehler- und Upgradedomänen hinweg ausführen. Es können allerdings auch gleichzeitig mehrere willkürliche Ausfälle auftreten. Hierbei ist die Gefahr eines echten Notfalls größer.


### <a name="random-failures-leading-to-service-failures"></a>Dienstausfälle aufgrund von willkürlichen Ausfällen
Angenommen, bei einem Dienst mit dem `InstanceCount`-Wert 5 fallen gleichzeitig mehrere Knoten aus, auf denen diese Instanzen ausgeführt wurden. Als Reaktion erstellt Service Fabric automatisch Ersatzinstanzen auf anderen Knoten. Dies wird so lange fortgesetzt, bis wieder die gewünschte Anzahl von Dienstinstanzen erreicht ist. Anderes Beispiel: Angenommen, Sie verfügen über einen zustandslosen Dienst mit dem `InstanceCount`-Wert -1. (Der Dienst wird also auf allen gültigen Knoten im Cluster ausgeführt.) Nehmen wir weiter an, dass einige dieser Instanzen ausfallen. In diesem Fall bemerkt Service Fabric, dass der Zustand des Diensts nicht dem gewünschten Zustand entspricht, und versucht, die Instanzen auf den Knoten zu erstellen, auf denen sie fehlen. 

Bei zustandsbehafteten Diensten hängt die Situation davon ab, ob der Dienst über einen persistenten Zustand verfügt. Entscheidend ist auch, über wie viele Replikate der Dienst verfügte und wie viele ausgefallen sind. Die Bestimmung, ob bei einem zustandsbehafteten Dienst ein Notfall vorliegt, und der Umgang mit der Situation umfassen drei Stufen:

1. Bestimmen, ob ein Quorumverlust vorliegt
   - Ein Quorumverlust liegt immer dann vor, wenn die Mehrheit der Replikate eines zustandsbehafteten Diensts zur gleichen Zeit ausgefallen ist (einschließlich des primären Replikats).
2. Bestimmen, ob es sich um einen dauerhaften Quorumverlust handelt
   - Die meisten Ausfälle sind vorübergehend. Prozesse, Knoten und virtuelle Computer werden neu gestartet, und Netzwerkpartitionen werden korrigiert. Manche Ausfälle sind jedoch dauerhaft. 
     - Bei Diensten ohne persistenten Zustand führt der Ausfall eines Quorums oder weiterer Replikate zu einem _sofortigen_ und dauerhaften Quorumverlust. Wenn Service Fabric einen Quorumverlust für einen nicht persistenten zustandsbehafteten Dienst feststellt, folgt sofort Schritt 3 – es wird also ein (potenzieller) Datenverlust deklariert. Service Fabric erkennt, dass das Warten auf die Wiederherstellung der Replikate keinen Sinn hätte, da die wiederhergestellten Replikate ohnehin leer wären.
     - Bei persistenten zustandsbehafteten Diensten führt der Ausfall eines Quorums oder weiterer Replikate dazu, dass Service Fabric auf die Wiederherstellung der Replikate wartet, um das Quorum wiederherzustellen. Dies führt zu einem Dienstausfall für alle _Schreibvorgänge_ in der betroffenen Partition (Replikatsatz) des Diensts. Lesevorgänge sind dagegen unter Umständen weiterhin möglich, allerdings mit geringerer Konsistenzgarantie. Service Fabric wartet standardmäßig unendlich lange auf die Quorumwiederherstellung, da auf den Quorumverlust ein (potenzieller) Datenverlust mit anderen Risiken folgt. Der Standardwert für `QuorumLossWaitDuration` kann zwar überschrieben werden, dies wird jedoch nicht empfohlen. Stattdessen empfiehlt es sich, alles für die Wiederherstellung der ausgefallenen Replikate zu tun. Dazu müssen die ausgefallenen Knoten wieder online geschaltet werden, damit sie die Laufwerke mit dem lokalen persistenten Zustand wieder einbinden können. Ist der Quorumverlust auf einen Prozessfehler zurückzuführen, versucht Service Fabric automatisch, die Prozesse neu zu erstellen und die Replikate darin neu zu starten. Ist dies nicht erfolgreich, meldet Service Fabric Integritätsfehler. Wenn sich diese beheben lassen, können die Replikate in der Regel wiederhergestellt werden. Manchmal ist das jedoch nicht möglich. Dies ist beispielsweise der Fall, wenn alle Laufwerke ausgefallen sind oder die Computer aus irgendeinem Grund physisch zerstört wurden. In einem solchen Fall haben wir es mit einem dauerhaften Quorumverlust zu tun. Wenn Service Fabric nicht länger auf die Wiederherstellung der ausgefallenen Replikate warten soll, muss ein Clusteradministrator ermitteln, welche Partitionen welcher Dienste betroffen sind, und die API `Repair-ServiceFabricPartition -PartitionId` oder `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` aufrufen.  Diese API ermöglicht die Angabe der ID für die Partition, die aus „QuorumLoss“ in den potenziellen Datenverlust überführt werden soll.

   > [!NOTE]
   > Aus Sicherheitsgründen darf diese API _ausschließlich_ gezielt für bestimmte Partitionen verwendet werden. 
   >

3. Ermitteln, ob tatsächlich Daten verloren gegangen sind, und Wiederherstellen von Sicherungen
   - Wenn Service Fabric die `OnDataLossAsync`-Methode aufruft, dann immer aufgrund eines _vermuteten_ Datenverlusts. Service Fabric stellt sicher, dass dieser Aufruf immer für das _beste_ verbleibende Replikat erfolgt. Dabei handelt es sich um das Replikat mit dem größten Fortschritt. Wir sprechen von _vermutetem_ Datenverlust, da es durchaus sein kann, dass das verbleibende Replikat über die gleichen Zustandsinformationen verfügt wie das primäre Replikat zum Zeitpunkt des Ausfalls. Da dieser Zustand jedoch nicht für einen Vergleich zur Verfügung steht, können Service Fabric und die Betreiber dies nicht mit Gewissheit sagen. An diesem Punkt steht für Service Fabric auch fest, dass die anderen Replikate nicht wiederhergestellt werden können. Diese Entscheidung ist gefallen, als beschlossen wurde, nicht weiter auf die Auflösung des Quorumverlusts zu warten. In den meisten Fällen empfiehlt es sich, dass der Dienst einfach einfriert und auf Eingriffe durch einen Administrator wartet. Wie läuft also eine typische Implementierung der Methode `OnDataLossAsync` ab?
   - Als Erstes wird protokolliert, dass `OnDataLossAsync` ausgelöst wurde, und die erforderlichen administrativen Warnungen werden ausgelöst.
   - An diesem Punkt wird die Ausführung in der Regel angehalten und auf weitere Entscheidungen und manuelle Aktionen gewartet. Der Grund: Gegebenenfalls verfügbare Sicherungen müssen unter Umständen erst vorbereitet werden. Wenn also beispielsweise zwei verschiedene Dienste Informationen koordinieren, müssen diese Sicherungen möglicherweise geändert werden, um sicherzustellen, dass die relevanten Informationen für die beiden Dienste bei der Wiederherstellung konsistent sind. 
   - Zudem sind häufig auch noch andere Telemetriedaten oder Ausgaben des Diensts vorhanden. Diese Metadaten können in anderen Diensten oder in Protokollen enthalten sein. Auf der Grundlage dieser Informationen lässt sich ermitteln, ob vom primären Replikat Aufrufe empfangen und verarbeitet wurden, die in der Sicherung nicht vorhanden waren oder nicht in diesem speziellen Replikat repliziert wurden. Diese Aufrufe müssen ggf. vor der Wiederherstellung wiederholt oder der Sicherung hinzugefügt werden.  
   - Der Zustand des verbleibenden Replikats wird mit dem Zustand ggf. verfügbarer Sicherungen verglichen. Bei Verwendung zuverlässiger Service Fabric-Auflistungen stehen hierfür entsprechende Tools und Prozesse zur Verfügung. Weitere Informationen finden Sie in [diesem Artikel](service-fabric-reliable-services-backup-restore.md). Auf diese Weise wird ermittelt, ob der Zustand innerhalb des Replikats ausreicht bzw. was in der Sicherung möglicherweise fehlt.
   - Nach Abschluss des Vergleichs (und ggf. der Wiederherstellung) gibt der Dienstcode „true“ zurück, sofern Zustandsänderungen vorgenommen wurden. Falls es sich bei dem Replikat um die beste verfügbare Kopie des Zustands handelt und keine Änderungen vorgenommen wurden, wird „false“ zurückgegeben. Bei „true“ sind _andere_ verbleibende Replikate nun möglicherweise nicht mehr mit diesem Replikat konsistent. Sie werden gelöscht und auf der Grundlage dieses Replikats neu erstellt. Bei „false“ wurde der Zustand nicht geändert, und die anderen Replikate können unverändert bleiben. 

Dienstautoren müssen unbedingt mögliche Datenverlust- und Ausfallszenarien üben, bevor Dienste in der Produktionsumgebung bereitgestellt werden. Zum Schutz vor Datenverlusten empfiehlt sich die regelmäßige [Sicherung des Zustands](service-fabric-reliable-services-backup-restore.md) sämtlicher zustandsbehafteter Dienste in einem geografisch redundanten Speicher. Vergewissern Sie sich zudem, dass Sie ihn wiederherstellen können. Da Sicherungen von vielen unterschiedlichen Diensten zu unterschiedlichen Zeiten erstellt werden, müssen Sie sich nach einer Wiederherstellung vergewissern, dass Ihre Dienste untereinander jeweils über eine konsistente Sicht verfügen. Stellen Sie sich beispielsweise eine Situation vor, in der ein Dienst eine Zahl generiert, speichert und dann an einen anderen Dienst sendet, der sie ebenfalls speichert. Nach einer Wiederherstellung stellen Sie unter Umständen fest, dass der zweite Dienst über die Zahl verfügt, der erste aber nicht, da der entsprechende Vorgang nicht in dessen Sicherung enthalten war.

Wenn Sie in einem Szenario mit Datenverlust feststellen, dass die verbleibenden Replikate nicht ausreichen und sich der Dienstzustand nicht anhand von Telemetriedaten oder Ausgaben rekonstruieren lässt, hängt Ihre bestmögliche Recovery Point Objective (RPO) von der Häufigkeit Ihrer Sicherungen ab. Service Fabric bietet viele Tools zum Testen verschiedener Ausfallszenarien – einschließlich dauerhafter Quorum- und Datenverluste, die die Wiederherstellung einer Sicherung erfordern. Diese Szenarien sind Teil der vom Fault Analysis Service verwalteten Testability-Tools von Service Fabric. Weitere Informationen zu diesen Tools und Mustern finden Sie [hier](service-fabric-testability-overview.md). 

> [!NOTE]
> Systemdienste können auch einen Quorumverlust erleiden, wobei die Auswirkung spezifisch für den jeweiligen Dienst ist. Beispielsweise wirkt sich der Quorumverlust im Naming Service auf die Namensauflösung aus. Im Failover-Manager-Dienst verhindert der Quorumverlust hingegen das Erstellen neuer Dienste sowie Failover. Die Service Fabric-Systemdienste folgen zwar dem gleichen Muster wie Ihre Dienste für die Zustandsverwaltung, wir raten aber dennoch davon ab, sie aus dem Quorumverlust in den potenziellen Datenverlust zu überführen. Stattdessen empfiehlt es sich, [Unterstützung anzufordern](service-fabric-support.md), um eine geeignete Lösung für Ihre individuelle Situation zu finden.  In der Regel ist es besser, zu warten, bis die ausgefallenen Replikate wiederhergestellt wurden.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Verfügbarkeit des Service Fabric-Clusters
Der Service Fabric-Cluster ist im Großen und Ganzen eine hochgradig verteilte Umgebung ohne Single Points of Failure. Der Ausfall eines Knotens führt nicht zu Problemen mit der Verfügbarkeit oder Zuverlässigkeit des Clusters. Das liegt in erster Linie daran, dass die Service Fabric-Systemdienste den zuvor angegebenen Richtlinien folgen: Sie werden standardmäßig immer mit mindestens drei Replikaten betrieben, und zustandslose Systemdienste werden auf allen Knoten ausgeführt. Die zugrunde liegenden Netzwerk- und Ausfallerkennungsebenen von Service Fabric sind vollständig verteilt. Die meisten Systemdienste können auf der Grundlage von Metadaten im Cluster neu erstellt werden oder ihren Zustand über andere Orte synchronisieren. Die Verfügbarkeit des Clusters kann beeinträchtigt werden, wenn bei Systemdiensten Quorumverluste auftreten, wie weiter oben beschrieben. In diesen Fällen können möglicherweise bestimmte Vorgänge für den Cluster (beispielsweise das Starten eines Upgrades oder das Bereitstellen neuer Dienste) nicht ausgeführt werden, obwohl der Cluster selbst noch aktiv ist. Bereits ausgeführte Dienste werden in diesem Fall weiter ausgeführt, sofern sie nicht in die Systemdienste schreiben müssen. Wenn also beispielsweise für den Failover-Manager ein Quorumverlust festgestellt wurde, werden alle Dienste weiter ausgeführt, fehlerhafte Dienste können jedoch nicht automatisch neu gestartet werden, da hierzu der Failover-Manager benötigt wird. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Ausfall eines Datencenters oder einer Azure-Region
In seltenen Fällen kann es vorkommen, dass ein physisches Rechenzentrum aufgrund eines Stromausfalls oder einer Unterbrechung der Netzwerkverbindung vorübergehend nicht verfügbar ist. In einem solchen Fall sind Ihre Service Fabric-Cluster und -Dienste im betroffenen Datencenter oder in der betroffenen Azure-Region nicht verfügbar. _Ihre Daten bleiben jedoch erhalten._ Für in Azure ausgeführte Cluster können Sie aktuelle Informationen zu Ausfällen auf der [Azure-Statusseite][azure-status-dashboard] nachlesen. Im äußerst unwahrscheinlichen Fall der vollständigen oder teilweisen Zerstörung eines physischen Rechenzentrums können sämtliche dort gehostete Service Fabric-Cluster sowie die darin enthaltenen Dienste verloren gehen. Dies schließt sämtliche Zustandsinformationen ein, die nicht außerhalb des Rechenzentrums oder der Region gesichert wurden.

Dem dauerhaften Ausfall eines einzelnen Datencenters oder einer einzelnen Region kann mit zwei unterschiedlichen Strategien begegnet werden: 

1. Betreiben Sie separate Service Fabric-Cluster in mehreren Regionen, und nutzen Sie einen Failover- und Failbackmechanismus zwischen diesen Umgebungen. Dieses Modell mit mehreren Clustern (aktiv/aktiv oder aktiv/passiv) ist mit zusätzlichem Verwaltungsaufwand verbunden und erfordert zusätzlichen Vorgangscode. Darüber hinaus müssen Sicherungen der Dienste in einem Datencenter oder in einer Region koordiniert werden, damit sie bei einem Ausfall in anderen Datencentern oder Regionen verfügbar sind. 
2. Betreiben Sie einen einzelnen Service Fabric-Cluster, der sich über mehrere Datencenter oder Regionen erstreckt. Für diese Strategie müssen mindestens drei Datencenter oder Regionen konfiguriert werden. Empfohlen werden fünf Regionen oder Datencenter. Hierzu wird eine komplexere Clustertopologie benötigt. Das Modell hat jedoch den Vorteil, dass der Ausfall eines Datencenters oder einer Region keinen Notfall mehr darstellt, sondern nur noch einen normalen Ausfall. Diese Ausfälle können von den Mechanismen bewältigt werden, die für Cluster innerhalb einer einzelnen Region zum Einsatz kommen. Dank Fehlerdomänen, Upgradedomänen und Platzierungsregeln von Service Fabric werden Arbeitslasten so verteilt, dass sie normale Ausfälle tolerieren. Weitere Informationen zu hilfreichen Richtlinien für den Betrieb von Diensten in dieser Art von Cluster finden Sie unter [Platzierungsrichtlinien für Service Fabric-Dienste](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-leading-to-cluster-failures"></a>Clusterausfälle aufgrund von willkürlichen Ausfällen
Bei Service Fabric kommt das Seedknoten-Konzept zur Anwendung. Hierbei handelt es sich um Knoten zur Aufrechterhaltung der Verfügbarkeit des zugrunde liegenden Clusters. Diese Knoten gewährleisten die Verfügbarkeit des Clusters durch die Einrichtung von Leases mit anderen Knoten. Darüber hinaus fungieren sie als entscheidendes Element bei bestimmten Arten von Netzwerkausfällen. Wenn zufällige Ausfälle einen Großteil der Startknoten im Cluster entfernen und diese Knoten nicht zurückgebracht werden, dann bricht Ihr Clusterverbundring zusammen, da Sie das Startknotenquorum verloren haben und der Cluster ausfällt. In Azure verwaltet der Service Fabric-Ressourcenanbieter Service Fabric-Clusterkonfigurationen, und der Anbieter verteilt Startknoten standardmäßig in Fehler- und Upgradedomänen mit Primärknotentyp. Ist der Primärknotentyp mit der Dauerhaftigkeit „Silber“ oder „Gold“ gekennzeichnet, wenn Sie einen Startknoten entfernen, indem Sie entweder Ihren Primärknotentyp horizontal herunterskalieren oder den Startknoten manuell entfernen, versucht der Cluster, einen anderen Nicht-Startknoten aus der für den Primärknotentyp verfügbaren Kapazität hochzustufen. Dies schlägt fehl, wenn weniger Kapazität verfügbar ist, als Ihre Clusterzuverlässigkeitsstufe für Ihren Primärknotentyp erfordert.

Sowohl in eigenständigen Service Fabric-Clustern als auch in Azure werden die Seeds vom primären Knotentyp ausgeführt. Beim Definieren eines primären Knotentyps verwendet Service Fabric automatisch die Anzahl bereitgestellter Knoten und erstellt bis zu neun Seedknoten und sieben Replikate der einzelnen Systemdienste. Wenn die Mehrzahl dieser Systemdienstreplikate überraschend gleichzeitig ausfällt, tritt für die Systemdienste wie weiter oben beschrieben ein Quorumverlust ein. Geht die Mehrzahl der Seedknoten verloren, wird der Cluster kurz darauf heruntergefahren.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie verschiedene Ausfälle mit dem [Testability-Framework](service-fabric-testability-overview.md)
- Lesen Sie weitere Artikel zu Wiederherstellung und hoher Verfügbarkeit. Microsoft hat sehr umfassende Anleitungen zu diesen Themen veröffentlicht. Während sich einige dieser Dokumente auf bestimmte Techniken für die Verwendung in anderen Produkten beziehen, enthalten viele allgemeine bewährte Methoden, die auch im Service Fabric-Kontext befolgt werden können:
  - [Checkliste für die Verfügbarkeit](../best-practices-availability-checklist.md)
  - [Ausführen von Notfallwiederherstellungsverfahren](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Notfallwiederherstellung und Hochverfügbarkeit für Azure-Anwendungen][dr-ha-guide]
- Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
