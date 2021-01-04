---
title: Azure Load Balancer Standard und Verfügbarkeitszonen
titleSuffix: Azure Load Balancer
description: In diesem Lernpfad beginnen Sie mit Azure Load Balancer Standard und Verfügbarkeitszonen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: fca6b40f4bd871041d14d119eb44e8366d8562ee
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700459"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard und Verfügbarkeitszonen

Azure Load Balancer Standard unterstützt Szenarien mit Verfügbarkeitszonen. Sie können Load Balancer Standard verwenden, um die Verfügbarkeit im gesamten Szenario zu erhöhen, indem Sie Ressourcen an Zonen ausrichten und die Verteilung zonenübergreifend ausführen. Verfügbarkeitszonen und Load Balancer Standard bilden in Kombination eine umfassende und flexible Sammlung von Funktionen, mit denen sich viele verschiedene Szenarien darstellen lassen.  Lesen Sie dieses Dokument, um diese [Konzepte](#concepts) und die [Entwurfsanleitung](#design) für das grundlegende Szenario zu verstehen.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Auf den Load Balancer angewendete Verfügbarkeitszonenkonzepte

Ein Load Balancer erbt die Zonenkonfiguration von seinen Komponenten: 

* Front-End
* Regeln
* Back-End-Pooldefinition

Im Kontext von Verfügbarkeitszonen werden das Verhalten und die Eigenschaften einer Load Balancer-Regel als „zonenredundant“ oder „zonal“ beschrieben.  Im Kontext des Load Balancers bezieht sich „zonenredundant“ immer auf **mehrere Zonen**, und „zonal“ bedeutet, dass der Dienst in einer **einzelnen Zone** isoliert wird. Ein Azure Load Balancer weist zwei Typen auf: öffentlich und intern. Beide Arten von Load Balancer unterstützen Zonenredundanz und zonale Bereitstellung.  Beide Load Balancer-Typen können den Datenverkehr nach Bedarf über Zonen hinweg weiterleiten.

## <a name="frontend"></a>Front-End

Ein Load Balancer-Front-End ist eine Front-End-IP-Konfiguration, die entweder auf eine öffentliche IP-Adresse oder auf eine private IP-Adresse innerhalb des Subnetzes eines virtuellen Netzwerks verweist. Es bildet den Lastenausgleichsendpunkt, in dem Ihr Dienst verfügbar gemacht wird.

Eine Load Balancer-Ressource kann gleichzeitig Regeln mit zonalen und zonenredundanten Front-Ends enthalten. Wenn eine öffentliche oder private IP-Adresse für eine Zone zugesichert wird, ist die Zonalität (oder deren Fehlen) nicht änderbar. Wenn Sie die Zonalität eines Front-Ends mit öffentlicher oder privater IP-Adresse ändern oder auslassen möchten, müssen Sie die IP-Adresse in der entsprechenden Zone neu erstellen. 

Verfügbarkeitszonen ändern nicht die Einschränkungen für mehrere Front-Ends. Weitere Informationen zu dieser Funktion finden Sie unter [mehrere Front-Ends für Load Balancer](load-balancer-multivip-overview.md).

### <a name="zone-redundant"></a>Zonenredundant 

In einer Region mit Verfügbarkeitszonen kann ein Front-End mit Load Balancer Standard zonenredundant sein. Mehrere Zonen können eingehenden oder ausgehenden Datenverkehr in einer Region bereitstellen. Dieser Datenverkehr wird mit einer einzelnen IP-Adresse bereitgestellt. DNS-Redundanzschemas sind nicht erforderlich. 

Eine einzelne Front-End-IP-Adresse übersteht einen Zonenfehler. Die Front-End-IP kann verwendet werden, um alle (nicht betroffenen) Back-End-Poolmitglieder unabhängig von der Zone zu erreichen. Der Datenpfad bleibt im Falle eines Ausfalls einzelner oder mehrerer Verfügbarkeitszonen verfügbar, solange noch eine einzelne fehlerfreie Zone in der Region vorhanden ist. 

Die IP-Adresse des Front-Ends wird gleichzeitig durch mehrere unabhängige Infrastrukturbereitstellungen in mehreren Verfügbarkeitszonen bereitgestellt. Alle Wiederholungen oder Wiederherstellungen sind in anderen Zonen erfolgreich, die nicht von dem Zonenausfall betroffen sind. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Abbildung: Zonenredundanter Load Balancer*

### <a name="zonal"></a>Zonal

Sie können festlegen, dass ein Front-End – ein so genanntes *zonales Front-End* – einer einzelnen Zone zugesichert wird.  Dieses Szenario bedeutet, dass alle eingehenden oder ausgehenden Datenströme von einer einzelnen Zone in einer Region bereitgestellt werden.  Ihr Front-End ist von der Integrität der Zone abhängig.  Der Datenpfad ist nicht betroffen von Fehlern in Zonen, in denen er nicht zugesichert wurde. Mit zonalen Front-Ends können Sie eine IP-Adresse pro Verfügbarkeitszone verfügbar machen.  

Darüber hinaus wird die Verwendung zonaler Front-Ends direkt für Endpunkte mit Lastenausgleich innerhalb der einzelnen Zonen unterstützt. Sie können diese Konfiguration verwenden, um pro Zone Lastenausgleichsendpunkte zur individuellen Überwachung jeder Zone verfügbar zu machen. Bei öffentlichen Endpunkten können Sie sie mit einem DNS-Lastenausgleichsprodukt wie [Traffic Manager](../traffic-manager/traffic-manager-overview.md) kombinieren und einen einzelnen DNS-Namen verwenden.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Abbildung: Zonales Lastenausgleichsmodul*

Wenn Sie diese Konzepte (zonenredundant und zonal für dasselbe Back-End) mischen möchten, lesen Sie den Artikel [Mehrere Front-Ends für Azure Load Balancer](load-balancer-multivip-overview.md).

Für ein öffentliches Load Balancer-Front-End fügen Sie der öffentlichen IP-Adresse einen **zones**-Parameter hinzu. Auf diese öffentliche IP-Adresse wird von der Front-End-IP-Konfiguration verwiesen, die von der jeweiligen Regel verwendet wird.

Für ein internes Load Balancer-Front-End fügen Sie der internen Load Balancer-Front-End-IP-Konfiguration einen **zones**-Parameter hinzu. Ein zonales Front-End sichert eine IP-Adresse in einem Subnetz für eine bestimmte Zone zu.

## <a name="backend"></a>Back-End

Azure Load Balancer arbeitet mit Instanzen von virtuellen Computern. Bei diesen Instanzen kann es sich um eigenständige Instanzen, um Verfügbarkeitsgruppen oder um VM-Skalierungsgruppen handeln. Jeder virtuelle Computer in einem einzelnen virtuellen Netzwerk kann Teil des Back-End-Pools in der Zone sein, die für den virtuellen Computer zugesichert ist.

Wenn Sie Ihr Front-End und Back-End mit einer einzelnen Zone ausrichten und zusichern möchten, platzieren Sie virtuelle Computer nur innerhalb der gleichen Zone im entsprechenden Back-End-Pool.

Wenn Sie virtuelle Computer über mehrere Zonen hinweg adressieren möchten, platzieren Sie virtuelle Computer aus mehreren Zonen im gleichen Back-End-Pool. 

Bei Verwendung von VM-Skalierungsgruppen platzieren Sie mindestens eine VM-Skalierungsgruppe im selben Back-End-Pool. Skalierungsgruppen können sich in einer Zone oder in mehreren Zonen befinden.

## <a name="outbound-connections"></a>Ausgehende Verbindungen

Zonenredundante und zonale Eigenschaften gelten für [ausgehende Verbindungen](load-balancer-outbound-connections.md). Eine zonenredundante öffentliche IP-Adresse für ausgehende Verbindungen wird von allen Zonen bedient. Eine zonale öffentliche IP-Adresse wird nur von der Zone bereitgestellt, in der sie zugesichert wurde. 

SNAT-Portzuordnungen für ausgehende Verbindungen überstehen Zonenausfälle, und Ihr Szenario bietet weiterhin ausgehende SNAT-Konnektivität, sofern es von dem Zonenausfall nicht betroffen ist. Bei Zonenfehlern sind hierzu möglicherweise Verbindungswiederherstellungen erforderlich, wenn Datenverkehr von einer betroffenen Zone bereitgestellt wurde. Datenströme in nicht betroffenen Zonen bleiben davon unberührt.

Für die SNAT-Portvorabzuordnung wird unabhängig von der Verwendung von Verfügbarkeitszonen der gleiche Algorithmus verwendet.

## <a name="health-probes"></a>Integritätstests

Ihre vorhandenen Integritätstestdefinitionen bleiben so, wie sie ohne Verfügbarkeitszonen sind. Wir haben allerdings das Integritätsmodell auf der Infrastrukturebene erweitert. 

Bei Verwendung zonenredundanter Front-Ends erweitert der Load Balancer seine interne Integritätsüberprüfung. Der Load Balancer prüft unabhängig die Verfügbarkeit eines virtuellen Computers aus jeder Zone und fährt ohne Benutzereingriff Pfade zonenübergreifend herunter, bei denen ein Fehler aufgetreten ist.  

Andere Zonen, die diesen virtuellen Computer erreichen können, bedienenden virtuellen Computer weiterhin von ihren jeweiligen Front-Ends aus. Während der Fehlerereignisse kann jede Zone unterschiedliche Verteilungen neuer Datenströme aufweisen, während die allgemeine Integrität Ihres Diensts geschützt wird.

## <a name="design-considerations"></a><a name="design"></a> Überlegungen zum Entwurf

Load Balancer ist im Kontext von Verfügbarkeitszonen flexibel. Sie können sich für eine zonenbasierte Ausrichtung entscheiden oder zonenredundant für jede Regel bleiben. Erhöhte Verfügbarkeit kann den Preis erhöhter Komplexität mit sich bringen. Verfügbarkeitsentwurf für optimale Leistung.

### <a name="zone-redundancy"></a>Zonenredundanz

Load Balancer macht es Ihnen leicht, eine einzelne IP-Adresse als zonenredundantes Front-End einzusetzen. Eine zonenredundante IP-Adresse kann eine zonale Ressource in einer beliebigen Zone bereitstellen.  Die IP-Adresse kann mindestens einen Zonenfehler überstehen, solange eine Zone in der Region fehlerfrei bleibt.  Stattdessen ist ein zonales Front-End eine Reduzierung des Diensts auf eine einzelne Zone und von der jeweiligen Zone abhängig.

Zonenredundanz impliziert weder einen störungsfreien Datenpfad noch eine störungsfreie Steuerebene: Es handelt sich um eine Datenebene. Zonenredundante Datenströme können alle Zonen verwenden, und die Datenströme eines Kunden verwenden alle fehlerfreien Zonen in einer Region. Bei einem Zonenfehler sind Datenströme, die fehlerfreie Zonen verwenden, nicht betroffen. 

Datenströme, die zum Zeitpunkt des Zonenausfalls eine Zone verwenden, können beeinträchtigt werden, aber Anwendungen können wiederhergestellt werden. Der Datenverkehr wird in den fehlerfreien Zonen innerhalb der Region nach nochmaliger Übertragung fortgesetzt, wenn Azure den Zonenausfall in den Griff bekommen hat.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Zonenübergreifende Grenzen

Sie müssen sich unbedingt darüber im Klaren sein, dass Sie jedes Mal, wenn ein Dienst Zonen überschreitet, nicht nur von einer, sondern potenziell von mehreren Zonen abhängig sind. Daraus ergibt sich, dass Ihr Dienst möglicherweise keine Verfügbarkeit über nicht zonale Bereitstellungen erlangt hat.

Vermeiden Sie unbeabsichtigte zonenübergreifende Abhängigkeiten, die die durch die Verwendung von Verfügbarkeitszonen erzielten Verfügbarkeitsverbesserungen zunichtemachen. Wenn die Anwendung mehrere wichtige Komponenten enthält, stellen Sie sicher, dass diese bei einem Zonenausfall verfügbar bleiben.

Eine einzelne wichtige Komponente für Ihre Anwendung kann die gesamte Anwendung beeinträchtigen, wenn sie nur in einer anderen Zone als der nicht beeinträchtigten Zone vorhanden ist. Berücksichtigen Sie die Zonenwiederherstellung und wie Ihre Anwendung auf diese reagiert. Sie müssen verstehen, wie sich Ihre Anwendung verhält, wenn Teile davon ausfallen. Machen Sie sich mit den wichtigen Aspekten vertraut, und verwenden Sie sie als Inspiration für Fragen, die aufkommen, während Sie über Ihr konkretes Szenario nachdenken.

- Wenn Ihre Anwendung über zwei Komponenten verfügt:

    * IP-Adresse
    * Virtueller Computer mit verwaltetem Datenträger

Diese Komponenten sind in Zone 1 und Zone 2 konfiguriert. Wenn Zone 1 ausfällt, ist Ihr Dienst nicht verfügbar. In zonalen Szenarien sollte keine zonenübergreifende Konfiguration verwendet werden – es sei denn, Sie sind sich bewusst, dass dadurch ein potenziell riskanter Fehlermodus entsteht. Dieses Szenario ist aus Flexibilitätsgründen zulässig.

- Wenn Ihre Anwendung über zwei Komponenten verfügt:

    * IP-Adresse
    * Virtueller Computer mit verwaltetem Datenträger

Diese Komponenten sind als zonenredundant und Zone 1 konfiguriert. Der Dienst bleibt bei einem Zonenausfall von Zone 2, Zone 3 oder beiden Zonen verfügbar, es sei denn, Zone 1 fällt aus. Allerdings verlieren Sie in gewissem Umfang die Integrität Ihres Diensts aus dem Auge, wenn Sie sich nur auf die Erreichbarkeit des Front-Ends konzentrieren.  Erwägen Sie die Entwicklung eines umfassenderen Integritäts- und Kapazitätsmodells.  Sie können zonenredundante und zonengebundene Konzepte zusammen verwenden, um Einblicke und Verwaltungsfähigkeit zu verbessern.

- Wenn Ihre Anwendung über zwei Komponenten verfügt:

    * Zonenredundantes Load Balancer-Front-End
    * Zonenübergreifende VM-Skalierungsgruppe in drei Zonen

Ihre Ressourcen in Zonen, die nicht von einem Fehler betroffen sind, sind verfügbar. Die Dienstkapazität kann während des Ausfalls beeinträchtigt werden. Aus Sicht der Infrastruktur kann Ihre Bereitstellung einen oder mehrere Zonenausfälle überstehen. In diesem Szenario ergeben sich folgende Fragen:

  - Verstehen Sie, wie Ihre Anwendung mit solchen Ausfällen und eingeschränkter Kapazität umgeht?
  - Müssen Sie Sicherheitsmaßnahmen in Ihren Dienst integrieren, um ggf. ein Failover zu einem Regionspaar zu erzwingen?
  - Wie überwachen, erkennen und mildern Sie ein solches Szenario? Sie können mit der Load Balancer Standard-Diagnose die Überwachung Ihrer Dienstleistung erweitern. Berücksichtigen Sie, was verfügbar ist, und welche Ergänzung erforderlich ist.

- In Zonen können Ausfälle leichter verständlich sein. Ein Zonenausfall unterscheidet sich nicht von anderen Ausfällen, wenn es um Timeouts, Wiederholungsversuche und Backoffalgorithmen geht. Azure Load Balancer stellt zonenredundante Pfade bereit. Der Load Balancer versucht schnell die Wiederherstellung auf Paketebene in Echtzeit. Beim Auftreten eines Fehlers können erneute Übertragungen oder Wiederherstellungen auftreten. Es ist wichtig zu verstehen, wie Ihre Anwendung mit Fehlern umgeht. Ihr Lastenausgleichsschema wird überdauern, aber Sie müssen die folgenden Fragen berücksichtigen:

  - Versteht Ihr Dienst den Ausfall einer Zone, und wie nehmen Sie die Wiederherstellung vor, wenn der Status verloren geht?
  - Kann Ihre Anwendung sicher konvergieren, wenn eine Zone zurückkehrt?

Informationen zur Verbesserung der Resilienz Ihrer Anwendung in Ausfallszenarien finden Sie unter [Cloudentwurfsmuster](/azure/architecture/patterns/).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Verfügbarkeitszonen](../availability-zones/az-overview.md)
- Weitere Informationen zum [Load Balancer Standard](./load-balancer-overview.md).
- Erfahren Sie mehr über den [Lastenausgleich für VMs innerhalb einer Zone mit einem Standard-Lastenausgleich und einem zonenredundanten Front-End](./quickstart-load-balancer-standard-public-cli.md).
- Erfahren Sie mehr über den [zonenübergreifenden Lastenausgleich für VMs mit einem Standard-Lastenausgleich und einem zonenredundanten Front-End](./quickstart-load-balancer-standard-public-cli.md).
- Machen Sie sich mit [Cloudentwurfsmustern](/azure/architecture/patterns/) vertraut, um die Resilienz Ihrer Anwendung in Ausfallszenarien zu verbessern.