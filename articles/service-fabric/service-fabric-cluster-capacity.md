---
title: Planen der Service Fabric-Clusterkapazität | Microsoft Docs
description: Überlegungen zur Kapazitätsplanung für Service Fabric-Cluster. Knotentypen, Vorgänge, Dauerhaftigkeit und Zuverlässigkeitsstufen
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: bd76658c939496f27bf3751060c18d17968acd15
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386794"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Überlegungen zur Kapazitätsplanung für Service Fabric-Cluster
Die Kapazitätsplanung ist ein wichtiger Schritt bei jeder Produktionsbereitstellung. Nachfolgend sind einige Aspekte aufgeführt, die Sie dabei berücksichtigen müssen.

* Die Anzahl von Knotentypen, über die Ihr Cluster anfänglich verfügen muss
* Die Eigenschaften der einzelnen Knotentypen (Größe, primärer Knotentyp, Internetzugriff, Anzahl von VMs usw.)
* Die Zuverlässigkeits- und Dauerhaftigkeitsmerkmale des Clusters

> [!NOTE]
> Sie müssen mindestens alle Werte der Upgraderichtlinie **Unzulässig** während der Planung überprüfen. Damit stellen Sie sicher, dass Sie die Werte richtig festlegen und ein späteres Abbrechen Ihres Clusters aufgrund unveränderlicher Systemkonfigurationseinstellungen verhindern. 
> 

Im Folgenden gehen wir kurz auf diese Aspekte ein.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Die Anzahl von Knotentypen, über die Ihr Cluster anfänglich verfügen muss
Zuerst müssen Sie ermitteln, für welche Zwecke der von Ihnen erstellte Cluster verwendet werden soll.  Welche Arten von Anwendungen sollen in diesem Cluster bereitgestellt werden? Wenn Sie bezüglich des Verwendungszwecks des Clusters unsicher sind, ist es vermutlich noch zu früh für die Kapazitätsplanung.

Legen Sie die Anzahl von Knotentypen fest, über die Ihr Cluster anfänglich verfügen muss.  Jeder Knotentyp wird einer VM-Skalierungsgruppe zugeordnet. Jeden Knotentyp kann dann unabhängig zentral hoch- oder herunterskaliert werden, bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen können verschiedene Kapazitätsmetriken aufweisen. Wenn Sie die Anzahl von Knotentypen festlegen, sind also die folgenden Aspekte entscheidend:

* Weist Ihre Anwendung mehrere Dienste auf, und müssen einige dieser Dienste öffentlich sein oder über Internetzugriff verfügen? Typische Anwendungen umfassen einen Front-End-Gatewaydienst, der Eingaben von einem Client empfängt, und einen oder mehrere Back-End-Dienste, die mit den Front-End-Diensten kommunizieren. In diesem Fall verfügen Sie also über mindestens zwei Knotentypen.
* Haben Ihre Dienste (aus denen sich Ihre Anwendung zusammensetzt) unterschiedliche Infrastrukturanforderungen, z. B. höhere RAM-Anforderungen oder längere CPU-Zyklen? Nehmen wir z. B. an, dass die Anwendung, die bereitgestellt werden soll, einen Front-End-Dienst und einen Back-End-Dienst umfasst. Der Front-End-Dienst kann auf kleineren virtuellen Computern (VM-Größen wie D2) platziert werden, die über geöffnete Ports für das Internet verfügen.  Der rechenintensive Back-End-Dienst hingegen muss auf größeren virtuellen Computern (mit VM-Größen wie D4, D6, D15) platziert werden, die nicht vom Internet aus zugänglich sind.
  
  Auch wenn beide Dienste in diesem Beispiel auf einem Knotentyp verwendet werden können, wird empfohlen, sie in einem Cluster mit zwei Knotentypen zu platzieren.  Dadurch können für die Knotentypen unterschiedliche Eigenschaften (z.B. Internetkonnektivität oder VM-Größe) festgelegt werden. Außerdem kann die Anzahl von VMs individuell skaliert werden.  
* Da Sie nicht in die Zukunft blicken können, sollten Sie sich auf die Ihnen bekannten Fakten verlassen und die Anzahl von Knotentypen entsprechend festlegen, über die Ihre Anwendungen anfänglich verfügen müssen. Später können Knotentypen hinzugefügt oder entfernt werden. Ein Service Fabric-Cluster muss über mindestens einen Knotentyp verfügen.

## <a name="the-properties-of-each-node-type"></a>Die Eigenschaften der einzelnen Knotentypen
Der **Knotentyp** kann als Äquivalent zu Rollen in Cloud Services betrachtet werden. Knotentypen definieren die Größe, die Anzahl und die Eigenschaften der virtuellen Computer. Jeder Knotentyp, der in einem Service Fabric-Cluster definiert ist, wird einer [VM-Skalierungsgruppe](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) zugeordnet.  
Jeder Knotentyp ist eine separate Skalierungsgruppe und kann einzeln zentral hoch- oder herunterskaliert werden. Bei jedem Typ können unterschiedliche Portgruppen geöffnet sein, und die Typen weisen verschiedene Kapazitätsmetriken auf. Weitere Informationen über die Beziehungen zwischen Knotentypen und VM-Skalierungsgruppen, über den Zugriff per RDP auf eine der Instanzen, über das Öffnen neuer Ports usw. finden Sie unter [Service Fabric-Clusterknotentypen](service-fabric-cluster-nodetypes.md).

Ein Service Fabric-Cluster kann mehrere Knotentypen enthalten. In diesem Fall besteht der Cluster aus einem primären Knotentyp und einem oder mehreren nicht primären Knotentypen.

Ein einzelner Knotentyp kann nicht zuverlässig auf mehr als 100 Knoten pro VM-Skalierungsgruppe für Service Fabric-Anwendungen skaliert werden. Um zuverlässig mehr als 100 Knoten zu erreichen, müssen Sie weitere VM-Skalierungsgruppen hinzufügen.

### <a name="primary-node-type"></a>Primärer Knotentyp

Die Service Fabric-Systemdienste (z.B. der Cluster-Manager-Dienst oder der Imagespeicherdienst) werden auf dem primären Knotentyp platziert. 

![Screenshot eines Clusters mit zwei Knotentypen][SystemServices]

* Die **Mindestgröße von VMs** für den primären Knotentyp hängt von der gewählten **Dauerhaftigkeitsstufe** ab. Die Standarddauerhaftigkeitsstufe ist „Bronze“. Weitere Informationen finden Sie unter [Die Dauerhaftigkeitsmerkmale des Clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Die **Mindestanzahl von VMs** für den primären Knotentyp hängt von der gewählten **Zuverlässigkeitsstufe** ab. Die Standardzuverlässigkeitsstufe ist „Silber“. Weitere Informationen finden Sie unter [Die Zuverlässigkeitsmerkmale des Clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster).  

Der primäre Knotentyp wird anhand der Azure Resource Manager-Vorlage mit dem Attribut `isPrimary` unter der [Knotentypdefinition](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) konfiguriert.

### <a name="non-primary-node-type"></a>Nicht primärer Knotentyp

Cluster mit mehreren Knotentypen verfügen über einen primären Knotentyp. Die übrigen Knotentypen sind keine primären Knotentypen.

* Die **Mindestgröße von VMs** für nicht primäre Knotentypen hängt von der ausgewählten **Dauerhaftigkeitsstufe** ab. Die Standarddauerhaftigkeitsstufe ist „Bronze“. Weitere Informationen finden Sie unter [Die Dauerhaftigkeitsmerkmale des Clusters](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* Die **Mindestanzahl von VMs** für nicht primäre Knotentypen ist 1. Diese Anzahl sollte jedoch basierend auf der Anzahl von Replikaten der Anwendung/Dienste ausgewählt werden, die auf diesem Knotentyp ausgeführt werden soll bzw. sollen. Die Anzahl von VMs auf einem Knotentyp kann nach der Bereitstellung des Clusters erhöht werden.

## <a name="the-durability-characteristics-of-the-cluster"></a>Die Dauerhaftigkeitsmerkmale des Clusters
Über die Dauerhaftigkeitsstufe wird dem System angezeigt, über welche Berechtigungen Ihre VMs für die zugrunde liegende Azure-Infrastruktur verfügen. Auf dem primären Knotentyp kann Service Fabric mit dieser Berechtigung Infrastrukturanforderungen auf VM-Ebene anhalten (z. B. einen VM-Neustart, ein VM-Reimaging oder eine VM-Migration), die sich auf die Quorumanforderungen für die Systemdienste und Ihre zustandsbehafteten Dienste auswirken. Auf den nicht primären Knotentypen kann Service Fabric mit dieser Berechtigung Infrastrukturanforderungen auf VM-Ebene (z.B. einen VM-Neustart, ein VM-Reimaging und eine VM-Migration) anhalten, die sich auf die Quorumanforderungen für Ihre zustandsbehafteten Dienste auswirken.

| Dauerhaftigkeitsstufe  | Erforderliche Mindestanzahl von VMs | Unterstützte VM-SKUs                                                                  | Aktualisierungen, die Sie an Ihrer VM-Skalierungsgruppe vornehmen                               | Updates und Wartung, initiiert von Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Gold             | 5                              | Vollknoten-SKUs speziell für einen einzelnen Kunden (z.B. L32s, GS5, G5, DS15_v2, D15_v2) | Kann bis zur Genehmigung durch den Service Fabric-Cluster verzögert werden | Kann für 2 Stunden pro UD angehalten werden, damit zusätzliche Zeit für die Wiederherstellung von Replikaten nach früheren Fehlern verfügbar ist |
| Silber           | 5                              | VMs mit einem Kern oder mehr                                                        | Kann bis zur Genehmigung durch den Service Fabric-Cluster verzögert werden | Kann nicht für eine längere Zeit verzögert werden                                                    |
| Bronze           | 1                              | Alle                                                                                | Wird nicht durch den Service Fabric-Cluster verzögert           | Kann nicht für eine längere Zeit verzögert werden                                                    |

> [!WARNING]
> Knotentypen, die mit der Dauerhaftigkeitsstufe „Bronze“ ausgeführt werden, erhalten _keine Berechtigungen_. Das bedeutet, dass die Infrastrukturaufträge, die sich auf die zustandslosen Workloads auswirken, nicht angehalten oder verzögert werden. Dies kann sich auf Ihre Workloads auswirken. Verwenden Sie „Bronze“ nur für Knotentypen, die ausschließlich zustandslose Workloads ausführen. Für Produktionsworkloads wird die Ausführung unter „Silber“ oder höher empfohlen. 
> 
> Unabhängig von der Dauerhaftigkeitsstufe wird durch den Vorgang der [Aufhebung der Zuordnung](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) für die VM-Skalierungsgruppe der Cluster gelöscht.

**Vorteile der Verwendung der Dauerhaftigkeitsstufe „Silber“ oder „Gold“**
 
- Reduziert die Anzahl der erforderlichen Schritte in einem Vorgang zur horizontalen Herunterskalierung (d.h., die Knotendeaktivierung und der Befehl „Remove-ServiceFabricNodeState“ werden automatisch ausgeführt)
- Reduziert das Risiko eines Datenverlusts aufgrund eines vom Kunden initiierten direkten VM-SKU-Änderungsvorgangs oder von Azure-Infrastruktur-Vorgängen.

**Nachteile der Verwendung der Dauerhaftigkeitsstufe „Silber“ oder „Gold“**
 
- Für Bereitstellungen in Ihrer VM-Skalierungsgruppe (und anderen verwandten Azure-Ressourcen) kann eine Verzögerung bzw. ein Timeout auftreten, oder sie können durch Probleme in Ihrem Cluster oder auf der Infrastrukturebene vollständig blockiert werden. 
- Erhöht die Anzahl der [Lebenszyklusereignisse für Replikate](service-fabric-reliable-services-lifecycle.md) (z.B. Tausch des primären Replikats) aufgrund von automatischen Knotendeaktivierungen während Azure-Infrastrukturvorgängen.
- Stellt Knoten für bestimmte Zeiträume außer Dienst, während Updates der Azure-Plattformsoftware oder Aktivitäten zur Hardwarewartung durchgeführt werden. Es kann sein, dass Knoten während dieser Aktivitäten den Status „Wird deaktiviert“ oder „Deaktiviert“ aufweisen. Hierdurch reduziert sich die Kapazität Ihres Clusters vorübergehend, aber es sollten sich keine Auswirkungen auf die Verfügbarkeit Ihres Clusters oder Ihrer Anwendungen ergeben.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Empfehlungen für den Einsatz der Dauerhaftigkeitsstufen „Silber“ und „Gold“

Verwenden Sie die Silber- oder Gold-Dauerhaftigkeit für alle Knotentypen, die zustandsbehaftete Dienste hosten, für die Sie eine häufige horizontale Herunterskalierung (Reduzierung der VM-Instanzanzahl) erwarten und für die Sie bevorzugen, dass sich – zugunsten einer Vereinfachung dieser Herunterskalierungsvorgänge – Bereitstellungsvorgänge verzögern und die Kapazität reduziert wird. Die Szenarien mit horizontaler Hochskalierung (Hinzufügen von virtuellen Computerinstanzen) haben keinen Einfluss auf Ihre Wahl der Dauerhaftigkeitsstufe, dies ist nur bei horizontaler Hochskalierung der Fall.

### <a name="changing-durability-levels"></a>Ändern von Dauerhaftigkeitsstufen
- Knotentypen mit der Dauerhaftigkeitsstufe „Silber“ oder „Gold“ können nicht auf „Bronze“ herabgestuft werden.
- Das Upgrade von „Bronze“ auf „Silber“ oder „Gold“ kann einige Stunden dauern.
- Stellen Sie beim Ändern der Dauerhaftigkeitsstufe sicher, dass diese sowohl in der Service Fabric-Erweiterungskonfiguration der VMS-Skalierungsgruppenressource als auch in der Knotentypdefinition der Service Fabric-Clusterressource aktualisiert wird. Diese Werte müssen übereinstimmen.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Betriebsempfehlungen für den Knotentyp, dessen Dauerhaftigkeitsstufe Sie auf „Silber“ oder „Gold“ festgelegt haben.

- Halten Sie Ihren Cluster und Anwendungen jederzeit fehlerfrei, und stellen Sie sicher, dass Anwendungen rechtzeitig auf alle [Lebenszyklus-Dienstereignisse für Replikate](service-fabric-reliable-services-lifecycle.md) (z.B. Unterbrechung der Replikaterstellung) reagieren.
- Führen Sie sicherere Methoden für VM-SKU-Änderungen (Horizontales Hoch-/Herunterskalieren nach) ein: Das Ändern der VM-SKU einer VM-Skalierungsgruppe ist grundsätzlich ein unsicherer Vorgang und sollte daher vermieden werden. Mit dieser Vorgehensweise vermeiden Sie gängige Probleme.
    - **Für nicht primäre Knotentypen:** Es wird empfohlen, eine neue VM-Skalierungsgruppe zu erstellen, die Dienstplatzierungseinschränkungen so zu ändern, dass die neue VM-Skalierungsgruppe bzw. der neue Knotentyp enthalten ist, und dann die Instanzenanzahl der alten VM-Skalierungsgruppe auf null zu reduzieren (nacheinander für alle Knoten, um sicherzustellen, dass das Entfernen der Knoten die Zuverlässigkeit des Clusters nicht beeinträchtigt).
    - **Für den primären Knotentyp:** Eine Änderung der VM-SKU des primären Knotentyps wird nicht empfohlen. Das Ändern der SKU des primären Knotentyps wird nicht unterstützt. Ist Kapazität der Grund für die neue SKU, wird empfohlen, weitere Instanzen hinzuzufügen. Wenn dies nicht möglich ist, erstellen Sie einen neuen Cluster und führen eine [Wiederherstellung des Anwendungszustands](service-fabric-reliable-services-backup-restore.md) (falls zutreffend) vom alten Cluster durch. Sie müssen keine Systemdienstzustände wiederherstellen, denn diese werden neu erstellt, wenn Sie Ihre Anwendungen im neuen Cluster bereitstellen. Wenn Sie in Ihrem Cluster zustandslose Anwendungen ausführen, stellen Sie Ihre Anwendungen im neuen Cluster bereit.  Sie müssen nichts wiederherstellen. Wenn Sie einen nicht unterstützten Weg einschlagen und die VM-SKU ändern möchten, können Sie das Modell der VM-Skalierungsgruppe an die neue SKU anpassen. Wenn der Cluster nur einen Knotentyp enthält, sollten Sie sicherstellen, dass Ihre gesamten zustandsbehafteten Anwendungen rechtzeitig auf alle [Lebenszyklus-Dienstereignisse für Replikate](service-fabric-reliable-services-lifecycle.md) (z.B. Unterbrechung der Replikaterstellung) reagieren und dass die Dauer für die Neuerstellung des Dienstreplikats weniger als fünf Minuten beträgt (für die Dauerhaftigkeitsstufe „Silber“). 
    
- Verwalten Sie mindestens fünf Knoten für alle VM-Skalierungsgruppen, für die die Dauerhaftigkeitsstufen „Gold“ oder „Silber“ aktiviert wurden.
- Jede VM-Skalierungsgruppe mit der Dauerhaftigkeitsstufe „Silber“ oder „Gold“ muss einem eigenen Knotentyp im Service Fabric-Cluster zugeordnet werden. Das Zuordnen mehrerer VM-Skalierungsgruppen zu einem einzelnen Knotentyp verhindert die ordnungsgemäße Koordinierung zwischen dem Service Fabric-Cluster und der Azure-Infrastruktur.
- Löschen Sie keine zufälligen VM-Instanzen, sondern verwenden Sie immer die Funktion zum zentralen Herunterskalieren für VM-Skalierungsgruppen. Das Löschen von zufälligen VM-Instanzen kann zu Ungleichheiten in der auf UD und FD verteilten VM-Instanz führen. Eine solche Ungleichheit kann die Fähigkeit des Systems zum ordnungsgemäßen Lastenausgleich zwischen den Dienstinstanzen/Dienstreplikaten beeinträchtigen.
- Wenn Sie die automatische Skalierung verwenden, legen Sie die Regeln so fest, dass die horizontale Herunterskalierung (Entfernung von VM-Instanzen) jeweils nur für einen Knoten ausgeführt wird. Es ist nicht sicher, mehrere Instanzen gleichzeitig herunterzuskalieren.
- Beim Löschen oder Freigeben von virtuellen Computern auf dem primären Knotentyp sollten Sie niemals die Anzahl der zugeordneten virtuellen Computer unter die Anforderungen der Zuverlässigkeitsstufe reduzieren. Diese Vorgänge werden in einer Skalierungsgruppe mit der Dauerhaftigkeitsstufe „Silber“ oder „Gold“ auf unbestimmte Zeit blockiert.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Die Zuverlässigkeitsmerkmale des Clusters
Über die Zuverlässigkeitsstufe wird die Anzahl von Replikaten der Systemdienste festgelegt, die in diesem Cluster auf dem primären Knotentyp ausgeführt werden sollen. Je mehr Replikate vorhanden sind, desto größer ist die Zuverlässigkeit der Systemdienste in Ihrem Cluster.  

Für die Zuverlässigkeitsstufe können folgende Werte festgelegt werden:

* Platin: Systemdienste mit einer Replikatgruppen-Zielanzahl von 7 ausführen
* Gold: Systemdienste mit einer Replikatgruppen-Zielanzahl von 7 ausführen
* Silber: Systemdienste mit einer Replikatgruppen-Zielanzahl von 5 ausführen 
* Bronze: Systemdienste mit einer Replikatgruppen-Zielanzahl von 3 ausführen

> [!NOTE]
> Die gewählte Zuverlässigkeitsstufe bestimmt die Mindestanzahl von Knoten, über die Ihr primärer Knotentyp verfügen muss. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Empfehlungen für die Zuverlässigkeitsstufe

Wenn Sie den Cluster vergrößern oder verkleinern (die Summe der VM-Instanzen in allen Knotentypen), müssen Sie die Zuverlässigkeit des Clusters von einer Stufe auf eine andere aktualisieren. Durch diesen Vorgang werden die erforderlichen Clusterupgrades ausgelöst, um die Replikatgruppenanzahl der Systemdienste zu ändern. Warten Sie, bis das laufende Upgrade abgeschlossen ist, ehe Sie Änderungen am Cluster vornehmen, beispielsweise Knoten hinzufügen.  Sie können den Fortschritt des Upgrades im Service Fabric Explorer oder durch Ausführen von [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) verfolgen.

Dies ist die Empfehlung für die Auswahl der Zuverlässigkeitsstufe.  Die Anzahl von Seedknoten wird ebenfalls auf die Mindestanzahl von Knoten für eine Zuverlässigkeitsstufe festgelegt.  Bei einem Cluster mit Gold-Zuverlässigkeit sind beispielsweise sieben Seedknoten vorhanden.

| **Anzahl von Clusterknoten** | **Zuverlässigkeitsstufe** |
| --- | --- |
| 1 |Geben Sie den Parameter für die Zuverlässigkeitsstufe nicht an, er wird vom System berechnet |
| 3 |Bronze |
| 5 oder 6|Silber |
| 7 oder 8 |Gold |
| 9 und höher |Platin |

## <a name="primary-node-type---capacity-guidance"></a>Primärer Knotentyp: Kapazitätsleitfaden

Nutzen Sie diesen Leitfaden, um die Kapazität Ihres primären Knotentyps zu planen:

- **Anzahl von VM-Instanzen, um eine Produktionsworkload in Azure auszuführen:** Sie müssen die Mindestgröße 5 und die Zuverlässigkeitsstufe Silber für den primären Knotentyp angeben.  
- **Anzahl von VM-Instanzen, um eine Testworkload in Azure auszuführen:** Sie können die Mindestgröße 1 oder 3 für den primären Knotentyp angeben. Knotencluster 1 setzt eine spezifische Konfiguration voraus, daher wird bei diesem Cluster horizontales Skalieren nicht unterstützt. Knotencluster 1 besitzt keine Zuverlässigkeit. Daher müssen Sie in Ihrer Resource Manager-Vorlage diese Konfiguration entfernen bzw. nicht angeben (es reicht nicht, den Konfigurationswert nicht einzustellen). Wenn Sie über das Portal den Knotencluster 1 eingerichtet haben, wird die Konfiguration automatisch bearbeitet. Bei den Knotenclustern der Größe 1 und 3 wird die Ausführung von Produktionsworkloads nicht unterstützt. 
- **VM-SKU:** Der primäre Knotentyp dient zur Ausführung der Systemdienste, daher muss die ausgewählte VM-SKU die gesamte Spitzenlast verarbeiten können, die Sie für den Cluster planen. Eine Analogie zur Veranschaulichung: Stellen Sie sich den primären Knotentyp als Ihre Lunge vor, die Ihr Gehirn mit Sauerstoff versorgt. Wenn Ihr Gehirn nicht genügend Sauerstoff erhält, funktioniert Ihr Körper nicht richtig. 

Da die Kapazitätsanforderungen eines Clusters von der Workload abhängig sind, die Sie für die Ausführung im Cluster planen, können wir Ihnen keine detaillierten Anleitungen für Ihre spezifische Workload bereitstellen. Im Folgenden finden Sie eine allgemeine Übersicht, um Ihnen beim Einstieg zu helfen

Für Produktionsworkloads: 

- Es wird empfohlen, den primären NodeType exklusiv für Systemdienste zu reservieren und Platzierungseinschränkungen zu verwenden, um Ihre Anwendung auf den sekundären NodeTypes bereitzustellen.
- Die empfohlene VM-SKU ist D3 Standard oder D3_V2 Standard oder eine entsprechende SKU mit einer lokalen SSD-Kapazität von mindestens 14 GB.
- Die minimal unterstützte VM-SKU ist D1 Standard oder D1_V2 Standard oder eine entsprechende SKU mit einer lokalen SSD-Kapazität von mindestens 14 GB. 
- Ein lokaler SSD-Datenträger mit mindestens 14 GB ist erforderlich. Empfohlen werden mindestens 50 GB. Für Ihre Workloads, insbesondere bei der Ausführung von Windows-Containern, werden größere Datenträger benötigt. 
- VM-SKUs mit partiellem Kern wie A0 Standard werden für Produktionsworkloads nicht unterstützt.
- A1 Standard-SKUs werden aus Leistungsgründen für Produktionsworkloads nicht unterstützt.
- Virtuelle Computer mit niedriger Priorität werden nicht unterstützt.

> [!WARNING]
> Das Ändern der VM-SKU-Größe des primären Knotens in einem aktuell ausgeführten Cluster ist ein Skalierungsvorgang, der in der Dokumentation [Horizontales Hochskalieren von VM-Skalierungsgruppen](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentiert wird.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Nicht primärer Knotentyp: Kapazitätsleitfaden für zustandsbehaftete Workloads

Diese Anleitung gilt für zustandsbehaftete Workloads mit [zuverlässigen Sammlungen oder Reliable Actors](service-fabric-choose-framework.md) von Service Fabric, die Sie auf dem nicht primären Knotentyp ausführen.

**Anzahl von VM-Instanzen:** Es wird empfohlen, zustandsbehaftete Produktionsworkloads mit mindestens 5 Zielreplikaten auszuführen. Dies bedeutet, dass Sie im stabilen Zustand in jeder Fehlerdomäne und jeder Upgradedomäne ein Replikat (aus einer Replikatgruppe) erhalten. Das gesamte Konzept der Zuverlässigkeitsstufen für den primären Knotentyp ist eine Möglichkeit, diese Einstellung für Systemdienste anzugeben. Daher gilt die gleiche Überlegung auch für Ihre zustandsbehafteten Dienste.

Für Produktionsworkloads wird die Mindestgröße 5 für den primären Knotentyp empfohlen, wenn Sie zustandsbehaftete Workloads auf dem Knoten ausführen.

**VM-SKU:** Dies ist der Knotentyp, auf dem Ihre Anwendungsdienste ausgeführt werden. Daher muss die ausgewählte VM-SKU die Spitzenlast verarbeiten können, die Sie für jeden Knoten planen. Die Kapazitätsanforderungen des Knotentyps sind von der Workload abhängig, die Sie im Cluster ausführen möchten. Daher können wir keine detaillierten Anleitungen für Ihre spezifische Workload bereitstellen. Im Anschluss finden Sie eine allgemeine Übersicht, um Ihnen beim Einstieg zu helfen.

Für Produktionsworkloads 

- Die empfohlene VM-SKU ist D3 Standard oder D3_V2 Standard oder eine entsprechende SKU mit einer lokalen SSD-Kapazität von mindestens 14 GB.
- Die minimal unterstützte VM-SKU ist D1 Standard oder D1_V2 Standard oder eine entsprechende SKU mit einer lokalen SSD-Kapazität von mindestens 14 GB. 
- VM-SKUs mit partiellem Kern wie A0 Standard werden für Produktionsworkloads nicht unterstützt.
- A1 Standard-SKUs werden aus Leistungsgründen für Produktionsworkloads nicht unterstützt.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Nicht primärer Knotentyp: Kapazitätsleitfaden für zustandslose Workloads

Diese Anleitung gilt für zustandslose Workloads, die Sie auf dem nicht primären Knotentyp ausführen.

**Anzahl von VM-Instanzen:** Für zustandslose Produktionsworkloads wird die Mindestgröße 2 für nicht primäre Knotentypen unterstützt. So können Sie zwei zustandslose Instanzen Ihrer Anwendung ausführen und stellen sicher, dass Ihr Dienst auch beim Ausfall einer VM-Instanz weiter funktioniert. 

**VM-SKU:** Dies ist der Knotentyp, auf dem Ihre Anwendungsdienste ausgeführt werden. Daher muss die ausgewählte VM-SKU die Spitzenlast verarbeiten können, die Sie für jeden Knoten planen. Die Kapazitätsanforderungen des Knotentyps sind von der Workload abhängig, die Sie im Cluster ausführen möchten. Wir können keine detaillierten Anleitungen für Ihre spezifische Workload bereitstellen.  Im Anschluss finden Sie jedoch eine allgemeine Übersicht, um Ihnen beim Einstieg zu helfen.

Für Produktionsworkloads 

- Die empfohlene VM-SKU ist D3 Standard oder D3_V2 Standard oder eine entsprechende SKU. 
- Die minimal unterstützte VM-SKU ist D1 Standard oder D1_V2 Standard oder eine entsprechende SKU. 
- VM-SKUs mit partiellem Kern wie A0 Standard werden für Produktionsworkloads nicht unterstützt.
- A1 Standard-SKUs werden aus Leistungsgründen für Produktionsworkloads nicht unterstützt.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie die Kapazitätsplanung abgeschlossen haben und einen Cluster einrichten, sollten Sie folgende Artikel lesen:

* [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md)
* [Service Fabric-Clusterskalierung](service-fabric-cluster-scaling.md)
* [Planen der Notfallwiederherstellung](service-fabric-disaster-recovery.md)
* [Beziehung zwischen Knotentypen und der VM-Skalierungsgruppe](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
