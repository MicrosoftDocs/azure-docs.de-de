---
title: Cluster-Beschreibung im Clusterressourcen-Manager | Microsoft-Dokumentation
description: Beschreibung eines Service Fabric-Clusters durch Angabe von Fehlerdomänen, Upgradedomänen, Knoteneigenschaften und Knotenkapazitäten für den Clusterressourcen-Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 082abd89cd84fc34180f333b54664d7dddfa0ccf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65561211"
---
# <a name="describing-a-service-fabric-cluster"></a>Beschreiben eines Service Fabric-Clusters
Der Service Fabric-Cluster-Ressourcen-Manager stellt verschiedene Methoden zum Beschreiben eines Clusters bereit. Zur Laufzeit nutzt der Clusterressourcen-Manager diese Informationen, um die Hochverfügbarkeit der Dienste sicherzustellen, die im Cluster ausgeführt werden. Beim Erzwingen dieser wichtigen Regeln wird auch versucht, den Ressourcenverbrauch innerhalb des Clusters zu optimieren.

## <a name="key-concepts"></a>Wichtige Begriffe
Der Clusterressourcen-Manager unterstützt verschiedene Funktionen, die einen Cluster beschreiben:

* Fehlerdomänen
* Upgradedomänen
* Knoteneigenschaften
* Knotenkapazitäten

## <a name="fault-domains"></a>Fehlerdomänen
Eine Fehlerdomäne ist ein beliebiger Bereich mit einem koordinierten Ausfall. Ein einzelner Computer ist eine Fehlerdomäne (da er aus vielen unterschiedlichen Gründen ausfallen kann, z.B. infolge eines Stromausfalls oder aufgrund von Laufwerkfehlern oder fehlerhafter NIC-Firmware). Computer, die mit dem gleichen Ethernet-Switch verbunden sind, befinden sich in derselben Fehlerdomäne – genau wie Computer, die sich an einem Standort befinden oder die eine Stromquelle gemeinsam nutzen. Da sich Hardwarefehler natürlicherweise überschneiden, sind Fehlerdomänen inhärent hierarchisch und werden in Service Fabric als URIs dargestellt.

Es ist wichtig, dass Fehlerdomänen richtig eingerichtet werden, da Service Fabric diese Informationen nutzt, um Dienste sicher zu platzieren. Für Service Fabric soll eine Platzierung von Diensten vermieden werden, bei der der Verlust einer Fehlerdomäne (aufgrund des Ausfalls einer Komponente) dazu führt, dass ein Dienst nicht mehr verfügbar ist. In der Azure-Umgebung nutzt Service Fabric die von der Umgebung bereitgestellten Fehlerdomäneninformationen, um die Knoten im Cluster in Ihrem Auftrag richtig zu konfigurieren. Für eine eigenständige Service Fabric-Konfiguration werden Fehlerdomänen zum Zeitpunkt der Einrichtung des Clusters definiert. 

> [!WARNING]
> Es ist wichtig, dass die für Service Fabric angegebenen Informationen zur Fehlerdomäne korrekt sind. Angenommen, die Knoten Ihres Service Fabric-Clusters werden auf zehn virtuellen Computern (VMs) ausgeführt, die auf fünf physischen Hosts betrieben werden. In diesem Fall gibt es trotz der zehn VMs lediglich fünf verschiedene Fehlerdomänen (der obersten Ebene). Die gemeinsame Verwendung eines physischen Hosts bewirkt, dass VMs dieselbe Stamm-Fehlerdomäne gemeinsam nutzen, da die VMs bei einem Ausfall ihres physischen Hosts koordiniert ausfallen.  
>
> Service Fabric erwartet keine Änderung der Fehlerdomäne eines Knotens. Bei anderen Mechanismen zum Sicherstellen der Hochverfügbarkeit der VMs, z.B. [HA-VMs](https://technet.microsoft.com/library/cc967323.aspx), können Konflikte mit Service Fabric auftreten, da sie die transparente Migration der VMs von einem Host zu einem anderen nutzen. Von diesen Mechanismen wird der ausgeführte Code im virtuellen Computer weder neu konfiguriert noch benachrichtigt. Somit werden **nicht** als Umgebungen für den Betrieb von Service Fabric-Clusters unterstützt. Service Fabric sollte die einzige eingesetzte Hochverfügbarkeitstechnologie sein. Mechanismen wie die Livemigration von VMs, SANs usw. sind nicht erforderlich. Bei Verwendung mit Service Fabric _mindern_ diese Mechanismen die Anwendungsverfügbarkeit und -zuverlässigkeit, da sie eine zusätzliche Komplexität mit sich bringen, zentrale Fehlerquellen einführen und Zuverlässigkeits- und Verfügbarkeitsstrategien nutzen, die mit denen in Service Fabric in Konflikt stehen. 
>
>

In der Grafik unten sind alle Entitäten farbig markiert, die einen Beitrag zu Fehlerdomänen leisten, und alle unterschiedlichen Fehlerdomänen aufgeführt, die sich ergeben. Dieses Beispiel enthält Datencenter („DC“), Racks („R“) und Blades („B“). Wenn jedes Blatt also mehrere virtuelle Computer enthält, kann die Fehlerdomänenhierarchie eine weitere Ebene umfassen.

<center>

![Mithilfe von Fehlerdomänen strukturierte Knoten][Image1]
</center>

Zur Laufzeit berücksichtigt der Clusterressourcen-Manager von Service Fabric die Fehlerdomänen im Cluster und plant Layouts. Die zustandsbehafteten Replikate oder zustandslosen Instanzen für einen bestimmten Dienst werden so verteilt, dass sie sich in separaten Fehlerdomänen befinden. Das Verteilen des Diensts auf Fehlerdomänen stellt sicher, dass seine Verfügbarkeit nicht beeinträchtigt wird, wenn eine Fehlerdomäne auf einer beliebigen Ebene der Hierarchie ausfällt.

Für den Clusterressourcen-Manager von Service Fabric spielt es keine Rolle, wie viele Ebenen die Hierarchie der Fehlerdomänen umfasst. Es wird aber versucht sicherzustellen, dass sich der Verlust eines Teils der Hierarchie nicht auf die darin ausgeführten Dienste auswirkt. 

Es empfiehlt sich, dass alle Ebenen auf jeder Tiefe der Fehlerdomänenhierarchie die gleiche Anzahl von Knoten enthalten. Wenn die „Struktur“ der Fehlerdomänen in Ihrem Cluster nicht ausgeglichen ist, ist es für den Clusterressourcen-Manager schwieriger, die beste Zuordnung für die Dienste zu ermitteln. Fehlerdomänenlayouts mit ungleichmäßiger Verteilung bedeuten, dass sich der Ausfall bestimmter Domänen stärker auf die Verfügbarkeit von Diensten auswirkt als der anderer Domänen. So kommt es für den Clusterressourcen-Manager zu einem Konflikt zwischen zwei Zielen: Die Computer in der stark ausgelasteten Domäne sollen einerseits genutzt werden, indem Dienste darauf platziert werden. Andererseits sollen die Dienste so in anderen Domänen platziert werden, dass der Verlust einer Domäne nicht zu Problemen führt. 

Wie sehen nicht ausgeglichene Domänen aus? In der Abbildung unten sind zwei unterschiedliche Clusterlayouts veranschaulicht. Im ersten Beispiel werden die Knoten gleichmäßig auf die Fehlerdomänen verteilt. Im zweiten Beispiel verfügt eine Fehlerdomäne über mehr Knoten als die anderen Fehlerdomänen. 

<center>

![Zwei unterschiedliche Clusterlayouts][Image2]
</center>

In Azure wird die Wahl, welche Fehlerdomäne einen Knoten enthält, für Sie verwaltet. Je nach der Anzahl von Knoten, die Sie bereitstellen, kann es trotzdem dazu kommen, dass einige Fehlerdomänen mehr Knoten als andere enthalten. Angenommen, Sie verfügen über fünf Fehlerdomänen im Cluster, stellen für einen bestimmten Knotentyp jedoch sieben Knoten bereit. In diesem Fall verfügen die ersten beiden Fehlerdomänen über eine größere Anzahl von Knoten. Das Problem verschlimmert sich, wenn Sie damit fortfahren, mehr NodeTypes mit nur wenigen Instanzen bereitzustellen. Daher empfiehlt es sich, dass die Anzahl der Knoten in jedem Knotentyp einem Vielfachen der Anzahl der Fehlerdomänen entspricht.

## <a name="upgrade-domains"></a>Upgradedomänen
Upgradedomänen sind ein weiteres Feature, mit dem der Clusterressourcen-Manager von Service Fabric das Layout des Clusters nachvollziehen kann. Mit Upgradedomänen werden Knotensätze definiert, die gleichzeitig aktualisiert werden. Anhand von Upgradedomänen kann der Clusterressourcen-Manager Verwaltungsvorgänge wie Upgrades verstehen und steuern.

Upgradedomänen sind Fehlerdomänen nicht unähnlich, es gibt jedoch einige wichtige Unterschiede. Zunächst werden Fehlerdomänen durch Bereiche von koordinierten Hardwareausfällen definiert. Upgradedomänen hingegen werden per Richtlinie definiert. Sie können entscheiden, wie viele Upgradedomänen Sie verwenden möchten, und dieser Wert wird nicht von der Umgebung vorgegeben. Sie können über genauso viele Upgradedomänen wie Knoten verfügen. Ein weiterer Unterschied zwischen Fehlerdomänen und Upgradedomänen besteht darin, dass Upgradedomänen nicht hierarchisch angeordnet sind. Stattdessen ähneln sie mehr einem einfachen Tag. 

Das folgende Diagramm zeigt drei Upgradedomänen, die auf drei Fehlerdomänen verteilt sind. Außerdem ist eine mögliche Platzierung für drei unterschiedliche Replikate eines zustandsbehafteten Diensts dargestellt, die jeweils in unterschiedlichen Fehler- und Upgradedomänen angeordnet werden. Bei dieser Platzierung kann der Ausfall einer Fehlerdomäne verkraftet werden, während gerade ein Dienstupgrade durchgeführt wird, und dennoch ist noch eine Kopie des Codes und der Daten vorhanden.  

<center>

![Platzierung mit Fehler- und Upgradedomänen][Image3]
</center>

Die Verwendung einer größeren Zahl von Upgradedomänen hat Vor- und Nachteile. Mit einer größeren Anzahl von Upgradedomänen wird jeder Schritt des Upgrades präziser und wirkt sich daher auf eine kleinere Zahl von Knoten oder Diensten aus. Dies führt dazu, dass weniger Dienste auf einmal verschoben werden müssen, sodass das System geringeren Änderungen unterliegt. Dies bewirkt meist eine Verbesserung der Zuverlässigkeit, da ein geringerer Anteil des Diensts von Problemen betroffen ist, die während des Upgrades auftreten. Eine höhere Zahl von Upgradedomänen bedeutet auch, dass Sie einen geringeren verfügbaren Puffer auf anderen Knoten vorhalten müssen, um die Auswirkungen des Upgrades verarbeiten zu können. Wenn Sie beispielsweise über fünf Upgradedomänen verfügen, verarbeiten die Knoten jeder Domäne ca. 20% des Datenverkehrs. Wenn Sie diese Upgradedomäne für ein Upgrade außer Betrieb nehmen müssen, muss diese Last im Allgemeinen verlagert werden können. Da Sie über vier verbleibende Upgradedomänen verfügen, muss jede von ihnen Raum für ca. 5 % des Gesamtdatenverkehrs bieten. Eine höhere Anzahl von Upgradedomänen bedeutet, dass Sie einen geringeren Puffer auf den Knoten im Cluster benötigen. Angenommen, Sie verfügen stattdessen über zehn Upgradedomänen. In diesem Fall würde jede Upgradedomäne nur etwa 10 % des Gesamtdatenverkehrs verarbeiten. Wenn ein Upgrade schrittweise im Cluster ausgeführt wird, benötigt jede Domäne nur Platz für ca. 1,1 % des Gesamtdatenverkehrs. Eine größere Anzahl von Upgradedomänen ermöglicht Ihnen den Betrieb Ihrer Knoten mit höherer Auslastung, da weniger Kapazitätsreserven erforderlich sind. Dasselbe gilt für Fehlerdomänen.  

Der Nachteil bei der Verwendung von vielen Upgradedomänen ist, dass Upgrades länger dauern können. Service Fabric wartet nach Fertigstellung einer Upgradedomäne einen kurzen Zeitraum und führt Überprüfungen durch, bevor mit dem Upgrade der nächsten Domäne begonnen wird. Diese Verzögerungen ermöglichen es, etwaige durch das Upgrade bewirkte Probleme zu erkennen, bevor mit dem Upgradevorgang fortgefahren wird. Dieser Kompromiss ist akzeptabel, da dadurch verhindert wird, dass sich negative Änderungen jeweils auf einen zu großen Teil des Diensts auswirken.

Eine zu geringe Anzahl von Upgradedomänen birgt viele negative Nebeneffekte: Während jede einzelne Upgradedomäne außer Betrieb genommen und aktualisiert wird, steht ein großer Teil der Gesamtkapazität nicht zur Verfügung. Beispiel: Wenn Sie nur drei Upgradedomänen besitzen, nehmen Sie jeweils etwa ein Drittel der Dienst- oder Clustergesamtkapazität außer Betrieb. Es ist nicht wünschenswert, dass so ein großer Teil Ihres Diensts auf einmal nicht betriebsbereit ist, weil im restlichen Cluster genügend Kapazität für die Verarbeitung der Workload vorhanden sein muss. Eine Verwaltung eines solchen Puffers bedeutet, dass die betreffenden Knoten während des Normalbetriebs in geringerem Umfang genutzt werden können, als dies sonst der Fall wäre. Dadurch steigen die Kosten für die Ausführung des Diensts.

Es gibt keine tatsächliche Beschränkung bei der Gesamtzahl der Fehler- oder Upgradedomänen in einer Umgebung bzw. keine Einschränkungen in Bezug auf ihre Überlappung. Es gibt einige allgemeine Muster:

- 1:1-Zuordnung von Fehlerdomänen und Upgradedomänen
- Eine Upgradedomäne pro Knoten (physische oder virtuelle Betriebssysteminstanz)
- Ein „Stripeset“- oder „Matrix“-Modell, bei dem die Fehler- und Upgradedomänen eine Matrix bilden, in der die Computer normalerweise entlang der Diagonalen angeordnet sind

<center>

![Layouts von Fehler- und Upgradedomänen][Image4]
</center>

Es gibt keine allgemeingültige Empfehlung für die Auswahl des Layouts, da jedes Layout seine Vor- und Nachteile hat. Das 1FD:1U-Modell ist beispielsweise einfach einzurichten. Das Modell „Eine Upgradedomäne pro Knoten“ ist wahrscheinlich das gängigste Modell. Während eines Upgradevorgangs wird jeder Knoten unabhängig aktualisiert. Dies ähnelt früheren Situationen, in denen für kleine Gruppen von Computern manuell ein Upgrade ausgeführt wurde.

Das häufigste Modell ist die FD/UD-Matrix, bei der die FDs und UDs eine Tabelle bilden und die Knoten am Anfang der Diagonalen angeordnet sind. Dies ist das Modell, das in Service Fabric-Clustern in Azure standardmäßig verwendet wird. Bei Clustern mit vielen Knoten entspricht das Gesamtbild anschließend dem obigen dichten Matrixmuster.

> [!NOTE]
> In Azure gehostete Service Fabric-Cluster unterstützten keine Änderung der Standardstrategie. Nur eigenständige Cluster bieten diese Anpassung.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Einschränkungen und daraus resultierendes Verhalten von Fehler- und Upgradedomänen
### <a name="default-approach"></a>*Standardansatz*
Standardmäßig behält der Clusterressourcen-Manager die ausgewogene Verteilung der Dienste auf Fehler- und Upgradedomänen bei. Dies wird als [Einschränkung](service-fabric-cluster-resource-manager-management-integration.md) simuliert. In den Einschränkungen für Fehler- und Upgradedomänen heißt es: „Für jede Dienstpartition sollte der Unterschied zwischen der Anzahl von Dienstobjekten (zustandslose Dienstinstanzen oder zustandsbehaftete Dienstreplikate) zwischen zwei Domänen auf derselben Hierarchieebene nie mehr als 1 betragen.“ Nehmen wir an, dass diese Einschränkung eine „maximale Differenz“ garantiert. Die Fehler- und Upgradedomäneneinschränkung verhindert bestimmte Verschiebungen oder Anordnungen, die die oben genannte Regel verletzen.

Wir sehen uns nun ein Beispiel an. Angenommen, wir verwenden einen Cluster mit sechs Knoten, für die fünf Fehlerdomänen und fünf Upgradedomänen konfiguriert wurden.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfiguration 1*

Angenommen, wir erstellen nun einen Dienst mit einer TargetReplicaSetSize (bzw. für einen zustandslosen Dienst einem InstanceCount) von 5. Die Replikate (R) werden auf den Knoten N1-N5 gespeichert. N6 wird unabhängig von der Anzahl der erstellten Dienste tatsächlich niemals verwendet. Warum ist das so? Wir betrachten den Unterschied zwischen dem aktuellen Layout und dem Fall, in dem N6 gewählt wird.

Hier sehen Sie das Layout und die Gesamtzahl von Replikaten pro Fehler- und Upgradedomäne:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 1*


Dieses Layout ist in Bezug auf die Knoten pro Fehler- und Upgradedomäne ausgeglichen. Außerdem ist es in Bezug auf die Anzahl von Replikaten pro Fehler- und Upgradedomäne ausgeglichen. Jede Domäne verfügt über die gleiche Anzahl von Knoten und die gleiche Anzahl von Replikaten.

Nun sehen wir uns an, was passieren würde, wenn wir N6 anstelle von N2 verwenden würden. Wie wären die Replikate dann verteilt?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Layout 2*


Dieses Layout verstößt gegen die Definition der „maximalen Differenz“ für die Fehlerdomäneneinschränkung. FD0 hat zwei Replikate, während FD1 keine Replikate hat, sodass der Unterschied zwischen FD0 und FD1 insgesamt zwei beträgt, also die maximale Differenz überschreitet. Da die Einschränkung verletzt wird, lässt der Clusterressourcen-Manager diese Anordnung nicht zu. Wenn wir N2 und N6 (anstelle von N1 und N2) auswählen, erhalten wir Folgendes:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 3*


Dieses Layout ist in Bezug auf Fehlerdomänen ausgeglichen. Allerdings verletzt es jetzt die Upgradedomäneneinschränkung, da UD0 0 (null) Replikate aufweist, UD1 dagegen zwei. Daher ist dieses Layout ebenfalls ungültig und wird vom Clusterressourcen-Manager nicht ausgewählt.

Dieser Ansatz zur Verteilung zustandsbehafteter Replikate oder zustandsloser Instanzen bietet die bestmögliche Fehlertoleranz. Wenn eine Domäne außer Betrieb geht, ist die minimale Anzahl von Replikaten/Instanzen verloren. 

Andererseits kann dieser Ansatz zu streng sein und dem Cluster nicht die Nutzung aller Ressourcen erlauben. Bei bestimmten Clusterkonfigurationen können bestimmte Knoten nicht verwendet werden. Dies kann dazu führen, dass Service Fabric Ihre Dienste nicht platziert, was zu Warnmeldungen führt. Im vorherigen Beispiel können einige der Clusterknoten nicht verwendet werden (N6 im vorliegenden Beispiel). Auch wenn Sie diesem Cluster Knoten hinzufügen würden (N7 - N10), würden Replikate/Instanzen aufgrund der Fehler- und Upgradedomäneneinschränkungen nur auf N1 - N5 platziert werden. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfiguration 2*


### <a name="alternative-approach"></a>*Alternativer Ansatz*

Der Clusterressourcen-Manager unterstützt eine andere Version der Fehler- und Upgradedomäneneinschränkung, die die Platzierung ermöglicht und dabei ein Mindestmaß an Sicherheit garantiert. Die alternative Fehler- und Upgradedomäneneinschränkung kann wie folgt beschrieben werden: „Für eine bestimmte Dienstpartition sollte die domänenübergreifende Replikatverteilung sicherstellen, dass die Partition keinem Quorumsverlust unterliegt“. Nehmen wir an, dass diese Einschränkung eine „Quorumssicherheit“ garantiert. 

> [!NOTE]
>Für einen zustandsbehafteten Dienst definieren wir den *Quorumsverlust* in einer Situation, wo der überwiegende Teil der Partitionsreplikate zur gleichen Zeit außer Betrieb ist. Wenn z.B. TargetReplicaSetSize fünf beträgt, stellt ein Satz von drei Replikaten das Quorum dar. Wenn TargetReplicaSetSize sechs beträgt, sind entsprechend vier Replikate für das Quorum erforderlich. In beiden Fällen können nicht mehr als zwei Replikate zur gleichen Zeit außer Betrieb sein, damit die Partition noch ordnungsgemäß funktioniert. Für einen zustandslosen Dienst gibt es keinen *Quorumsverlust*, weil zustandslose Dienste selbst dann noch normal funktionieren, wenn der überwiegende Teil der Instanzen gleichzeitig außer Betrieb geht. Darum konzentrieren wir uns im weiteren Verlauf des Texts auf zustandsbehaftete Dienste.
>

Wir kehren zum vorherigen Beispiel zurück. Mit der „quorumsicheren“ Version der Einschränkung würden alle drei vorliegenden Layouts gültig sein. Dies liegt daran, dass die Partition selbst bei einem Ausfall von FD0 im zweiten Layout oder UD1 im dritten Layout noch über ein Quorum verfügen würde (der überwiegende Teil der Replikate wäre noch in Betrieb). Mit dieser Version der Einschränkung könnte N6 fast immer genutzt werden.

Der „quorumsichere“ Ansatz bietet mehr Flexibilität als der Ansatz mit „maximaler Differenz“, da es einfacher ist, Replikatverteilungen zu finden, die in fast allen Clustertopologien gültig sind. Allerdings kann dieser Ansatz nicht die besten Fehlertoleranzeigenschaften garantieren, da manche Ausfälle schlimmer sind als andere. Im schlimmsten Fall könnte der überwiegende Teil der Replikate mit dem Ausfall einer Domäne und eines zusätzlichen Replikats verlorengehen. Sie könnten z.B. anstatt mit 3 Ausfällen, die bei 5 Replikaten oder Instanzen für einen Quorumsverlust erforderlich sind, jetzt mit nur zwei Ausfällen den überwiegenden Teil verlieren. 

### <a name="adaptive-approach"></a>*Adaptiver Ansatz*
Da beide Ansätze Stärken und Schwächen aufweisen, haben wir einen adaptiven Ansatz eingeführt, der diese beiden Strategien kombiniert.

> [!NOTE]
> Dies ist das Standardverhalten ab Service Fabric Version 6.2. 
> 
> Der adaptive Ansatz nutzt in der Standardeinstellung die Logik der „maximalen Differenz“ und wechselt nur bei Bedarf zur „quorumsicheren“ Logik. Der Clusterressourcen-Manager ermittelt automatisch anhand der Konfiguration von Clustern und Diensten, welche Strategie erforderlich ist. Für einen bestimmten Dienst gilt: *Wenn TargetReplicaSetSize durch die Anzahl von Fehlerdomänen und die Anzahl von Upgradedomänen gleichmäßig teilbar ist **und** die Anzahl von Knoten höchstens dem Produkt aus der Anzahl von Fehlerdomänen * der Anzahl von Upgradedomänen entspricht, sollte der Clusterressourcen-Manager die „quorumbasierte“ Logik für diesen Dienst nutzen.* Bedenken Sie, dass der Clusterressourcen-Manager diesen Ansatz für zustandslose und zustandsbehaftete Dienste verwendet, obwohl der Quorumsverlust für zustandslose Dienste nicht relevant ist.

Wir kehren nun zum vorherigen Beispiel zurück und gehen davon aus, dass ein Cluster nun 8 Knoten besitzt (der Cluster ist immer noch mit fünf Fehlerdomänen und fünf Upgradedomänen konfiguriert und die TargetReplicaSetSize eines auf diesem Cluster gehosteten Diensts ist weiterhin fünf). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfiguration 3*

Da alle erforderlichen Bedingungen erfüllt sind, nutzt der Clusterressourcen-Manager die „quorumbasierte“ Logik bei der Verteilung des Diensts. Dies ermöglicht die Verwendung von N6 - N8. Eine mögliche Dienstverteilung könnte in diesem Fall wie folgt aussehen:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Layout 4*

Wenn die TargetReplicaSetSize Ihres Diensts (z.B.) auf vier reduziert wird, erkennt der Clusterressourcen-Manager diese Änderung und verwendet wieder die Logik der „maximalen Differenz“, weil TargetReplicaSetSize nicht mehr durch die Anzahl der FDs und UDs teilbar ist. In der Folge treten bestimmte Replikatverschiebungen auf, um die verbleibenden vier Replikate auf Knoten N1-N5 zu verteilen, sodass die Version der Domänenlogik der „maximalen Differenz“ von Fehler- und Upgradedomäne nicht verletzt wird. 

Wir werfen einen Blick zurück auf das vierte Layout und die TargetReplicaSetSize von fünf. Wenn N1 aus dem Cluster entfernt wird, ist die Anzahl der Upgradedomänen gleich vier. Erneut beginnt der Clusterressourcen-Manager mit der Verwendung der Logik der „maximalen Differenz“, da die TargetReplicaSetSize des Diensts nicht mehr gleichmäßig durch die Anzahl der UDs geteilt werden kann. Daher muss Replikat R1 beim erneuten Erstellen auf N4 untergebracht werden, sodass die Fehler- und Upgradedomäneneinschränkung nicht verletzt wird.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |– |– |– |– |– |– |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Layout 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurieren von Fehler- und Upgradedomänen
Das Definieren von Fehler- und Upgradedomänen erfolgt in unter Azure gehosteten Service Fabric-Bereitstellungen automatisch. Service Fabric wählt die Umgebungsinformationen von Azure und verwendet sie.

Wenn Sie Ihren eigenen Cluster erstellen (oder eine bestimmte Topologie ausführen möchten, die sich in der Entwicklung befindet), können Sie die Informationen für die Fehler- und Upgradedomänen selbst angeben. In diesem Beispiel definieren wir einen lokalen Entwicklungscluster mit neun Knoten, der drei „Datencenter“ (mit jeweils drei Racks) umfasst. Dieser Cluster verfügt auch über drei Upgradedomänen, die auf diese drei Datencenter verteilt sind. Unten findet sich ein Beispiel für die Konfiguration: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

über „ClusterConfig.json“ für eigenständige Bereitstellungen

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Beim Definieren von Clustern über Azure Resource Manager werden Fehlerdomänen und Upgradedomänen von Azure zugewiesen. Deshalb umfasst die Definition von Knotentypen und VM-Skalierungsgruppen in der Azure Resource Manager-Vorlagen keine Informationen zu Fehlerdomänen oder Upgradedomänen.
>

## <a name="node-properties-and-placement-constraints"></a>Knoteneigenschaften und Platzierungseinschränkungen
Manchmal (eigentlich in den meisten Fällen) möchten Sie sicherstellen, dass bestimmte Workloads nur auf bestimmten Knotentypen im Cluster ausgeführt werden. Für einige Workloads sind beispielsweise GPUs oder SSDs erforderlich, für andere hingegen nicht. Ein gutes Beispiel für die Ausrichtung von Hardware auf bestimmte Workloads ist nahezu jede vorstellbare N-Tier-Architektur. Bestimmte Computer fungieren als Front-End oder API-Seite der Anwendung und sind für die Clients oder das Internet verfügbar. Unterschiedliche Computer (häufig mit unterschiedlichen Hardwareressourcen) verarbeiten die Workload der Compute- oder Speicherebenen. Diese sind normalerweise _nicht_ direkt für Clients oder das Internet verfügbar. Service Fabric erwartet, dass Situationen eintreten, in denen bestimmte Workloads in bestimmten Hardwarekonfigurationen ausgeführt werden müssen. Beispiel:

* Eine vorhandene n-Ebenen-Anwendung wurde in eine Service Fabric-Umgebung verschoben.
* Eine Workload soll aus Leistungs-, Skalierbarkeits- oder Sicherheitsisolationsgründen auf bestimmter Hardware ausgeführt werden.
* Eine Workload sollte aufgrund von Richtlinien oder des Ressourcenverbrauchs von anderen Workloads isoliert werden.

Zur Unterstützung dieser Arten von Konfigurationen verfügt Service Fabric über ein erstklassiges Tagkonzept, bei dem die Tags auf Knoten angewendet werden können. Diese Tags werden als **Knoteneigenschaften** bezeichnet. **Platzierungseinschränkungen** sind die Anweisungen, die an einzelne Dienste angefügt sind und eine oder mehrere Knoteneigenschaften auswählen. Platzierungseinschränkungen definieren, an welcher Stelle Dienste ausgeführt werden sollen. Die Gruppe der Einschränkungen ist erweiterbar. Alle Schlüssel-Wert-Paare können funktionieren. 

<center>

![Clusterlayout bei verschiedenen Workloads][Image5]
</center>

### <a name="built-in-node-properties"></a>Integrierte Knoteneigenschaften
Service Fabric definiert einige Standardknoteneigenschaften, die automatisch verwendet werden können, ohne dass sie vom Benutzer definiert werden müssen. Für jeden Knoten sind die Standardeigenschaften **NodeType** und **NodeName** definieren. Sie können beispielsweise folgende Platzierungseinschränkung schreiben: `"(NodeType == NodeType03)"`. Es hat sich gezeigt, dass NodeType eine der am häufigsten verwendeten Eigenschaften ist. Sie ist hilfreich, da sie eindeutig dem Typ eines Computers entspricht. Jeder Computertyp wiederum entspricht einem Typ von Workload in einer herkömmlichen n-schichtigen Anwendung.

<center>

![Platzierungseinschränkungen und Knoteneigenschaften][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Syntax der Platzierungseinschränkungen und Knoteneigenschaften 
Der Wert in der Knoteneigenschaft kann als Zeichenfolge, boolescher Wert oder als Wert vom Typ „signed long“ angegeben werden. Die Anweisung im Dienst wird als Platzierungs*einschränkung* bezeichnet, weil damit eingeschränkt wird, wo der Dienst im Cluster ausgeführt werden kann. Bei der Einschränkung kann es sich um eine boolesche Anweisung handeln, die für die verschiedenen Knoteneigenschaften im Cluster ausgeführt wird. Die gültigen Selektoren in diesen booleschen Anweisungen lauten:

1) Bedingungsüberprüfungen zum Erstellen bestimmter Anweisungen

| Anweisung | Syntax |
| --- |:---:|
| "equal to" (ist gleich) | "==" |
| "not equal to" (ungleich) | "!=" |
| "greater than" (größer als) | ">" |
| "greater than or equal to" (größer als oder gleich) | ">=" |
| "less than" (kleiner als) | "<" |
| "less than or equal to" (kleiner als oder gleich) | "<=" |

2) Boolesche Anweisungen für Gruppierungen und logische Vorgänge

| Anweisung | Syntax |
| --- |:---:|
| "and" (und) | "&&" |
| "or" (oder) | "&#124;&#124;" |
| "not" (nicht) | "!" |
| "group as single statement" (Als einzelne Anweisung gruppieren) | "()" |

Hier sind einige Beispiele für einfache Anweisungen für Einschränkungen angegeben.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Nur auf Knoten, bei denen die Anweisung der Platzierungseinschränkung insgesamt als „True“ ausgewertet wird, kann ein Dienst platziert werden. Knoten, für die keine Eigenschaft definiert ist, entsprechen keiner Platzierungseinschränkung mit dieser Eigenschaft.

Angenommen, die folgenden Knoteneigenschaften wurden für einen bestimmten Knotentyp definiert:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster. 

> [!NOTE]
> In der Azure Resource Manager-Vorlage ist der Knotentyp im Allgemeinen parametrisiert. Er wird im Allgemeinen als „[Parameter('vmNodeType1Name')]“ und nicht als „NodeType01“ angegeben.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Sie können Dienstplatzierungs*einschränkungen* für einen Dienst wie folgt erstellen:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Wenn alle Knoten des Typs „NodeType01“ gültig sind, können Sie diesen Knotentyp auch mit der Einschränkung „(NodeType == NodeType01)“ auswählen.

Das Praktische an den Platzierungseinschränkungen eines Diensts ist, dass sie während der Laufzeit dynamisch aktualisiert werden können. Falls erforderlich, können Sie also einen Dienst im Cluster verschieben, Anforderungen hinzufügen und entfernen usw. Service Fabric stellt sicher, dass der Dienst einsatzbereit und verfügbar bleibt, selbst wenn diese Art von Änderungen ausgeführt wird.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Platzierungseinschränkungen werden für jede einzelne benannte Dienstinstanz angegeben. Updates ersetzen (überschreiben) immer die vorherige Angabe.

Die Clusterdefinition definiert die Eigenschaften für einen Knoten. Werden die Eigenschaften eines Knotens geändert, ist ein Upgrade der Clusterkonfiguration erforderlich. Beim Durchführen eines Upgrades für Knoteneigenschaften muss jeder betroffene Knoten neu gestartet werden, sodass seine neuen Eigenschaften gemeldet werden. Diese parallelen Upgrades werden von Service Fabric verwaltet.

## <a name="describing-and-managing-cluster-resources"></a>Beschreiben und Verwalten von Clusterressourcen
Eine der wichtigsten Aufgaben eines Orchestrators besteht in der Verwaltung des Ressourcenverbrauchs im Cluster. Das Verwalten von Clusterressourcen kann viele verschiedene Aspekte umfassen. Zunächst ist sicherstellen, dass die Computer nicht überlastet werden. Das heißt, es muss dafür gesorgt werden, dass auf Computern nicht mehr Dienste ausgeführt werden, als sie bewältigen können. Zweitens sind Lastenausgleich und Optimierung erforderlich. Diese sind unverzichtbar, damit Dienste effizient ausgeführt werden. Kosteneffiziente oder leistungsabhängige Dienstangebote lassen nicht zu, dass einige Knoten stark ausgelastet sind, während andere Knoten inaktiv sind. Knoten mit hoher Auslastung führen zu Ressourcenkonflikten und schlechter Leistung, und Knoten mit sehr geringer Auslastung bedeuten verschwendete Ressourcen und höhere Kosten. 

Service Fabric stellt Ressourcen als `Metrics` (Metriken) dar. Bei Metriken handelt es sich um alle logischen oder physischen Ressourcen, die Sie für Service Fabric beschreiben möchten. Beispiele für Metriken: WorkQueueDepth oder MemoryInMb. Informationen zu den physischen Ressourcen, die von Service Fabric auf Knoten verwaltet werden können, finden Sie unter [Ressourcenkontrolle](service-fabric-resource-governance.md). Informationen zum Konfigurieren von benutzerdefinierten Metriken und zu deren Verwendung erhalten Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md)

Metriken unterscheiden sich von Platzierungseinschränkungen und Knoteneigenschaften. Knoteneigenschaften sind statische Deskriptoren der Knoten. Metriken beschreiben die Ressourcen, über die Knoten verfügen und die von Diensten genutzt werden, wenn sie auf einem Knoten ausgeführt werden. Eine Knoteneigenschaft könnte beispielsweise „HasSSD“ lauten und auf „true“ oder „false“ festgelegt sein. Die Menge des verfügbaren Speicherplatzes auf dieser SSD und die von Diensten genutzte Menge kann eine Metrik wie „DriveSpaceInMb“ sein. 

Es ist wichtig zu beachten, dass der Clusterressourcen-Manager von Service Fabric – genau wie bei Platzierungseinschränkungen und Knoteneigenschaften – nicht versteht, was die Namen der Metriken bedeuten. Bei Namen von Metriken handelt es sich lediglich um Zeichenfolgen. Es ist ratsam, Einheiten als Teil der von Ihnen erstellten Namen von Metriken zu deklarieren, falls die Gefahr von Mehrdeutigkeiten besteht.

## <a name="capacity"></a>Capacity
Wenn Sie den *Lastenausgleich*für alle Ressourcen deaktivieren, kann der Clusterressourcen-Manager von Service Fabric dennoch versuchen sicherzustellen, dass kein Knoten überlastet wird. Das Verwalten von Kapazitätsüberläufen ist möglich, es sei denn, der Cluster ist zu belegt und die Workload ist größer als jeder einzelne Knoten. Die Kapazität ist eine weitere *Einschränkung*, anhand der der Clusterressourcen-Manager ermittelt, wie viele Ressourcen auf einem Knoten verbraucht werden. Auch die verbleibende Kapazität wird für den gesamten Cluster nachverfolgt. Sowohl die Kapazität als auch der Verbrauch auf Dienstebene werden als Metrik ausgedrückt. Die Metrik kann beispielsweise „ClientConnections“ lauten, und ein Knoten kann für „ClientConnections“ eine Kapazität von 32768 aufweisen. Für andere Knoten können andere Grenzwerte gelten. Ein auf dem Knoten ausgeführter Dienst kann beispielsweise bestimmen, dass derzeit eine Kapazität von 32256 der Metrik „ClientConnections“ in Anspruch genommen wird.

Während der Laufzeit verfolgt der Clusterressourcen-Manager die verbleibende Kapazität im Cluster und auf den Knoten. Zum Verfolgen der Kapazität subtrahiert der Clusterressourcen-Manager den Verbrauch jedes Diensts von der Kapazität des Knotens, auf dem der betreffende Dienst ausgeführt wird. Mit diesen Informationen kann der Clusterressourcen-Manager von Service Fabric ermitteln, wo Replikate platziert oder wohin diese verschoben werden sollen, um die Kapazität der Knoten nicht zu überschreiten.

<center>

![Clusterknoten und -kapazität][Image7]
</center>

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Sie sehen die definierten Kapazitäten im Clustermanifest:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Im Allgemeinen ändert sich die Last eines Diensts dynamisch. Angenommen, die Last eines Replikats von „ClientConnections“ hat sich von 1024 in 2048 geändert, der Knoten, auf dem es ausgeführt wurde, verfügte jedoch nur über eine Restkapazität von 512 für diese Metrik. Die Platzierung dieses Replikats bzw. der Instanz ist ungültig, da auf dem Knoten nicht genügend Platz ist. Daher muss der Clusterressourcen-Manager eingreifen und für den Knoten wieder die Einhaltung der Kapazitätsgrenze sicherstellen. Er verringert eine Auslastung des Knotens, die seine Kapazität übersteigt, indem ein oder mehrere Replikate bzw. Instanzen von diesem Knoten auf andere Knoten verschoben werden. Beim Verschieben von Replikaten versucht der Clusterressouren-Manager, die Kosten dieser Verschiebungen möglichst gering zu halten. Die Kosten der Verschiebungen werden in [diesem Artikel](service-fabric-cluster-resource-manager-movement-cost.md) erörtert, und eine ausführlichere Beschreibung der Strategien und Regeln des Clusterressourcen-Managers für einen erneuten Ausgleich finden Sie [hier](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Clusterkapazität
Wie stellt der Clusterressourcen-Manager von Service Fabric sicher, dass der Cluster insgesamt nicht zu stark ausgelastet wird? Nun, bei einer dynamischen Last kann hier nicht allzu viel getan werden. Dienste können unabhängig von Aktionen, die vom Clusterressourcen-Manager durchgeführt werden, Lastspitzen aufweisen. Dies kann dazu führen, dass Ihr Cluster, der heute noch über ausreichend Spielraum verfügt, nicht mehr ausreicht, wenn Sie morgen großen Erfolg haben. Es sind jedoch einige Kontrollmechanismen eingebaut, mit denen Probleme verhindert werden sollen. Zunächst einmal können wir die Erstellung neuer Workloads verhindern, die eine Überlastung des Clusters verursachen.

Sie erstellen beispielsweise einen zustandslosen Dienst, und dieser bringt eine gewisse Last mit sich. Angenommen, für den Dienst ist die Metrik „DiskSpaceInMb“ wichtig. Wir nehmen weiterhin an, dass für jede Instanz des Diensts fünf Einheiten von „DiskSpaceInMb“ verbraucht werden. Sie möchten drei Instanzen des Diensts erstellen. Prima. Dies bedeutet, dass im Cluster 15 Einheiten von „DiskSpaceInMb“ vorhanden sein müssen, damit wir diese Dienstinstanzen überhaupt erstellen können. Der Clusterressourcen-Manager berechnet laufend die Kapazität und den Verbrauch jeder Metrik, sodass die verbleibende Kapazität im Cluster bestimmt werden kann. Falls kein ausreichender Speicherplatz vorhanden ist, lehnt der Clusterressourcen-Manager den Aufruf zum Erstellen des Diensts ab.

Da die Anforderung lediglich darin besteht, dass 15 Einheiten verfügbar sind, kann dieser Speicherplatz auf vielerlei Weise zugeordnet werden. Beispielweise kann eine verbleibende Einheit mit 15 unterschiedlichen Knoten verwendet werden, oder drei verbleibende Einheiten mit einer Kapazität von fünf unterschiedlichen Knoten. Wenn der Clusterressourcen-Manager die Anordnung so ändern kann, dass auf drei Knoten fünf Einheiten verfügbar sind, wird der Dienst platziert. Das Neuanordnen des Clusters ist im Allgemeinen möglich, es sei denn, der Cluster ist nahezu voll belegt oder die vorhandenen Dienste können aus bestimmten Gründen nicht konsolidiert werden.

## <a name="buffered-capacity"></a>Gepufferte Kapazität
Die gepufferte Kapazität ist eine weitere Funktion des Clusterressourcen-Managers. Sie ermöglicht die Reservierung eines bestimmten Teils der Knotengesamtkapazität. Mithilfe dieses Kapazitätspuffers werden lediglich Dienste während Upgrades und Knotenausfällen platziert. Die gepufferte Kapazität wird pro Metrik global für alle Knoten angegeben. Der Wert, den Sie für die reservierte Kapazität auswählen, ist eine Funktion aus der Anzahl von Fehler- und Upgradedomänen im Cluster. Eine höhere Zahl von Fehler- und Upgradedomänen bedeutet, dass Sie für Ihre gepufferte Kapazität einen niedrigeren Wert wählen können. Bei einer größeren Zahl von Domänen können Sie erwarten, dass geringere Mengen Ihres Clusters bei Upgrades und Ausfällen nicht verfügbar sind. Das Angeben der gepufferten Kapazität ist nur sinnvoll, wenn Sie auch die Knotenkapazität für eine Metrik angegeben haben.

Hier finden Sie ein Beispiel für die Angabe der gepufferten Kapazität:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Über „ClusterConfig.json“ für eigenständige Bereitstellungen bzw. über „Template.json“ für in Azure gehostete Cluster:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Die Erstellung neuer Dienste ist nicht erfolgreich, wenn der Cluster für eine Metrik keine gepufferte Kapazität mehr aufweist. Dadurch, dass zum Erhalt des Puffers verhindert wird, dass neue Dienste erstellt werden, wird sichergestellt, dass bei Upgrades oder Ausfällen die Kapazität von Knoten nicht überschritten wird. Die gepufferte Kapazität ist optional, aber sie wird für alle Cluster empfohlen, bei denen für eine Metrik eine Kapazität definiert wird.

Der Clusterressourcen-Manager macht diese Lastinformationen verfügbar. Für jede Metrik sind folgende Informationen vorhanden: 
  - die Einstellungen für die gepufferte Kapazität
  - die Gesamtkapazität
  - die aktuelle Nutzung
  - die Angabe, ob die jeweilige Metrik als ausgeglichen oder nicht ausgeglichen angesehen wird
  - Statistiken zur Standardabweichung
  - der Knoten mit der stärksten und der geringsten Auslastung  
  
Im Folgenden sehen Sie ein Beispiel für diese Ausgabe:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Nächste Schritte
* Informationen zur Architektur und zum Informationsfluss innerhalb des Cluster Resource Managers finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-architecture.md).
* Das Definieren von Defragmentierungsmetriken ist eine Möglichkeit, die Last auf Knoten zu konsolidieren, statt sie auszubreiten. Informationen zum Konfigurieren der Defragmentierung finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
